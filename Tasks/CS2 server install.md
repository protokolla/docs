## CS2 retakes server w/ Pterodactyl

Pterodactyl actually works for cs2 and is currently the best option. One should though remember to give enough disk space to the server as sources on the internet mention only 40 GB requirement when the files require at least 50 GB. This caused my installation to fail multiple times in the past. 

First of all you have created a new node and it has the correct ports allocated to it (`27015` and `27015`). New server creation happens from the admin panel (for us it is [here](https://host.protokolla.fi/admin/servers)).

After the server creation you should just wait (and maybe monitor download speeds from `btop`). When downloading has stopped (the server doesn't constantly go over 100 Mbps) you can start it from the Pterodactyl panel.

Hosting workshop maps happens by first starting the server to default map. Then execute the `host_workshop_map 3074758439` command to change the map to an [Only Up](https://steamcommunity.com/sharedfiles/filedetails/?id=3074758439) map. A good link about the topic can be found [here](https://steamcommunity.com/sharedfiles/filedetails/?id=3070689635).

Go to [Metamod:Source](https://www.metamodsource.net/downloads.php/?branch=master)s website and download the latest dev build. Extract the .tar.gz file with 7zip. Move the `addons`-folder to `/game/csgo` directory on the Pterodactyl container (over SFTP).

Edit the file `/game/csgo/gameinfo.gi` and search for word `Game_LowViolence`. Add the following include text below it if it doesn't exist:
```
Game	csgo/addons/metamod
```

Download the latest release from [CounterStrikeSharp](https://github.com/roflmuffin/CounterStrikeSharp/releases) (**SELECT WITH RUNTIME - THE PLUGIN WON'T WORK OTHERWISE**) and extract the zip file. Copy the files to the server on the same `/game/csgo` directory.

_This was basically quick see through of the tutorial [here](https://docs.cssharp.dev/docs/guides/getting-started.html)._

Download the latest [cs2-retakes](https://github.com/B3none/cs2-retakes) from their Github releases [page](https://github.com/B3none/cs2-retakes/releases/latest). Extract the zip file and copy the `RetakesPlugin` to `/game/csgo/addons/counterstrikesharp/plugins` directory on the Pterodactyl container.

> - Download the latest shared plugin and put it into your `addons/counterstrikesharp/shared` directory.

As the cs2-retakes plugin specifies you should use a custom weapon system (allocator), so your teammates can play with m4a4 (even though the m4a1-s is superior).

Download the zip from [here](https://github.com/yonilerner/cs2-retakes-allocator) and extract the files. Place the `RetakesAllocator` folder to `/game/csgo/addons/counterstrikesharp/plugins`. 

After that edit the `retakes_config.json` file in `/game/csgo/addons/counterstrikesharp/plugins/RetakesPlugin` to disable `EnableFallbackAllocation`. 

```json
"EnableFallbackAllocation": false,
```

`/game/csgo/cfg/cs2-retakes/retakes.cfg`
```sh
// allow buying anywhere so your teammates can buy the SUPERIOR m4a4
mp_buy_anywhere 1
mp_buytime 60000
mp_maxmoney 65535
mp_startmoney 65535
mp_afterroundmoney 65535
```

Server features:
- Gun based on the economy
- You can buy another gun
- There is awoopa queue (join it with `!awp`)
### Useful commands

**basic commands**

```sh
mp_autokick 0 # allow team to be funny

# Maps
host_workshop_map 3219506727 # (cs2 implementation of de_lake)
host_workshop_map 3436488774 # 1v1 Aim Map

# If the server is problematic with human joining ct side
mp_humanteam 1 # allows human on ct side
bot_kick # kick all bots (previously bot_kickall)
```

**cs2 retakes commands**

```sh
css_mapconfigs # list map configs
css_mapconfig de_dust2

# change map to mirage and load configs
map de_mirage
css_mapconfig de_mirage

map de_nuke
css_mapconfig de_nuke

map de_inferno
css_mapconfig de_inferno
```

### Issues

#### No knife and no autobuy

The retakesallocator plugin was problematic and printed a startup error to server log on the server boot. It might have had some corrupted files as the server had its disk space full for some time. The issue was fixed by reinstalling the plugin.

## Previously tried solutions - Pufferpanel

I encountered multiple issues with Pufferpanel. Thus I decided not to use it. The problem might have been that I used the beta release of version 3.0. 

Problems and their solutions:

CS2 server doesn't start and outputs:

Problem: `"./srcds_run: 32: ./srcds_run: pushd: not found`
Solution: `apt-get install lib32stdc++6`

Problem:
```sh
./srcds_run: 32: pushd: not found
./srcds_run: 35: popd: not found
```
Solution: Edit the `srcdsun_run` file. Change the beginning to `#!/bin/bash` from `#!/bin/sh`

Problem: something
Solution: Edit the server file `/var/lib/pufferpanel/servers/3e4f5f32`. Run command `mv ./bin/libgcc_s.so.1 ./bin/libgcc_s.so.1.old`.

Resources:
- https://www.opengamepanel.org/forum/viewthread.php?thread_id=6764
- https://docs.pufferpanel.com/en/3.x/games/srcds/dependencies.html (install these)
- https://askubuntu.com/questions/1467404/cs-go-server-error-on-ubuntu-22-04

## CS2 retakes server w/ Pterodactyl

### Background

I was looking for a solution to host a cs2 server for me and my friends. I tested couple solutions and decided to stick with Pterodactyl for the best support and scalability. I tried Docker and Pufferpanel before that.

In the process I learned couple things:
- Internet lies about the required disk space (it is at LEAST 50 GB and NOT 40 GB) => causes silent installation failure in Pterodactyl
- Pterodactyl doesn't have any good indicator about the installation process status (I decided to use btop and watch how much new network traffic was generated)

Server features after installation:
- cs2 retakes server w/ multiple maps
- Gun based on the economy
- You can buy another gun
- There is awoopa queue (join it with `!awp`)
### Installation

---

Go to [Metamod:Source](https://www.metamodsource.net/downloads.php/?branch=master)s website and download the latest dev build. Extract the .tar.gz file with 7zip. Move the `addons`-folder to `/game/csgo` directory on the Pterodactyl container (over SFTP).

Edit the file `/game/csgo/gameinfo.gi` and search for word `Game_LowViolence`. Add the following text below it if it doesn't exist:
```
Game	csgo/addons/metamod
```

Download the latest release from [CounterStrikeSharp](https://github.com/roflmuffin/CounterStrikeSharp/releases) (**SELECT WITH RUNTIME - THE PLUGIN WON'T WORK OTHERWISE**) and extract the zip file. Copy the files to the server on the same `/game/csgo` directory.

_This was basically quick see through of the tutorial [here](https://docs.cssharp.dev/docs/guides/getting-started.html)._

---

Adding the correct plugins can be quite a tedious work so we create a local copy of the desired outcome and just transfer it to the server.

Your folder structure should look as following. The part in paranthesis indicates whether you have to create the folder or download the file from Github. If there is a Github link, go to releases and download the zip with the folder name.

```
csgo (create)
└── addons (create)
    └── counterstrikesharp (create)
        ├── plugins (create)
        │   ├── InstadefusePlugin (github - https://github.com/B3none/cs2-instadefuse)
        │   ├── RetakesAllocator (github - https://github.com/yonilerner/cs2-retakes-allocator)
        │   └── RetakesPlugin (github - https://github.com/B3none/cs2-retakes)
        └── shared
            └── RetakesPluginShared (github - https://github.com/B3none/cs2-retakes)
```

Copy the above files to the `/game` directory on the Pterodactyl server.


After that edit the `/game/csgo/addons/counterstrikesharp/plugins/RetakesPlugin/retakes_config.json` file to disable `EnableFallbackAllocation`. 

```json
"EnableFallbackAllocation": false,
```

Modify file `/game/csgo/cfg/cs2-retakes/retakes.cfg` to include the following lines:
```sh
// allow buying anywhere so your teammates can buy the SUPERIOR m4a4
mp_buy_anywhere 1
mp_buytime 60000
mp_maxmoney 65535
mp_startmoney 65535
mp_afterroundmoney 65535
```
### Useful commands

**basic commands**

```sh
mp_autokick 0 # allow team to be funny

# Maps
host_workshop_map 3074758439 # Only Up
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

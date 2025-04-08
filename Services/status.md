View at [status](https://status.protokolla.fi/) .

We are using [Gatus](https://github.com/TwiN/gatus) as it supports yaml based configuration and the web UI allows viewing uptime over 24h unlike uptime kuma. The server should be standalone and completely separated from other infrastructure to avoid having status page downtime as other services are also down. 

The Gatus server thus also has its own reverse proxy (which is traefik). Traefik was chosen so that all the configuration can be done via docker labels and a handful of yaml files.
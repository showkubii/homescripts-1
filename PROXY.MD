# Caddy Proxy Server

## Caddy Plex Configuration

I use [Caddy](https://github.com/mholt/caddy) as a proxy server and if you want to use it with Plex, there is a very simple config below that I used. I have since removed this from my config to help simplify things.

My plex configuration in my CaddyFile as follows:

```bash
# Plex Server
plex.animosity.us {
gzip
timeouts 1h
log /opt/caddy/logs/plex.log
tls {
  dns cloudflare
}
proxy / 127.0.0.1:32400 {
 transparent
 websocket
 keepalive 12
 timeout 1h
    }
}
```

Plex also needs to have a few additional steps to be added so Caddy is used in all situations.

### Plex Configuration

Remote Access - Disable

```
Network - Custom server access URLs = https://<your-domain>:443,http://<your-domain>:80
```
Network - Secure connections = Preferred

<i>Note: You can force SSL by setting required and not adding the HTTP URL, however some players which do not support HTTPS (e.g: Roku, Playstations, some SmartTVs) will no longer function. I only use SSL is my config and as I do not open up HTTP traffic. </i>

### Stopping Local Access
By default, Plex regardless of what override URL you set will still connect locally to 32400. To remove this, I use the second option of adding the option to the Preferences.xml. You need to stop Plex and add the line in near the end of your Preferences.xml

allowLocalhostOnly="1" 

UFW or other firewall:
- Deny port 32400 externally (Plex still pings over 32400, some clients may use 32400 by mistake despite 443 and 80 being set).
# Caddy Proxy Server

## Google Authentication

For my build, I use this to get the plugins I use:
```bash
CADDY_TELEMETRY=on curl https://getcaddy.com | bash -s personal http.cache,http.cgi,http.jwt,http.login,http.realip,tls.dns.cloudflare
```
This allows me to use Google Authentication for my logins and CloudFlare for my automatic DNS updates for my hosts.

I use proxy entries for all my items behind and Google oAuth for my login validation:

```bash
# apps servers with auth
apps.host.com {
root /opt/www
browse
gzip
realip cloudflare
log /opt/caddy/logs/apps.log
tls {
	dns cloudflare
}
jwt {
    path /
    redirect /login?backTo={rewrite_uri}
    except /favicon.ico
    allow email your@email.us
    log-file /opt/caddy/logs/jwt.log
    log-level info
}

  login {
    redirect_check_referer false
    google client_id=yourcliendid.apps.googleusercontent.com,client_secret=yourclientsecret,scope=https://www.googleapis.com/auth/userinfo.email
    jwt_expiry 168h
    cookie_expiry 2400h
  }

proxy /sonarr 127.0.0.1:8989 {
  transparent
	}

proxy /radarr 127.0.0.1:7878 {
  transparent
	}

proxy /tautulli 127.0.0.1:8181 {
	transparent
	}

proxy /nzbget 127.0.0.1:9876 {
	transparent
	}

proxy /jackett 127.0.0.1:9117 {
	transparent
	}

proxy /grafana 127.0.0.1:3333 {
	without /grafana
	transparent
	}

proxy /netdata/ 192.168.1.30:19999 {
	without /netdata
	transparent
	}

proxy /router/ 192.168.1.1:19999 {
	without /router
	transparent
	}

proxy /jackett 127.0.0.1:9117 {
	transparent
	}

proxy /bazaar 127.0.0.1:6767 {
        transparent
        }


proxy /kitana 127.0.0.1:31337 {
        transparent
        }

proxy /qbit 127.0.0.1:8080 {
	transparent
	without /qbit
	}

}
```


## Caddy Plex Configuration

I use [Caddy](https://github.com/mholt/caddy) as a proxy server and if you want to use it with Plex, there is a very simple config below that I used. I have since removed this from my config to help simplify things.

My plex configuration in my CaddyFile as follows:

```bash
# Plex Server
plex.host.com {
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
## Websockets Video

Make sure you understand how MeshCentral works with your browser using chrome developer tools.

<div class="video-wrapper">
  <iframe width="320" height="180" src="https://www.youtube.com/embed/3vI4URd3VzU" frameborder="0" allowfullscreen></iframe>
</div>

## Enabling trace in your browser Dev Tools

`Trace=1` as a parameter in chrome dev tools for debugging


To log all database queries, change log_statement in /etc/postgresql/13/main/postgresql.conf

```
# CUSTOM
log_statement = 'all'           # none, ddl, mod, all
```

The stacktrace was logged to `stdout/journalctl`. Supposedly, you can enable debug logging for node modules by adding `DEBUG=<modulename>` to the environment. 

Adding this to `/etc/systemd/system/meshcentral.service` should do it but it didn't seem to do anything. 

I think that's because Mesh uses the trace logging in the browser instead of logging things in the server logs. 

```
Environment=DEBUG=mesh*
```

If you want to change node to meshcentral in journalctl, add this to /etc/systemd/system/meshcentral.service.
  
```
SyslogIdentifier=meshcentral
```

## Server: Logging it all

To log everything that's possible, prepare the log directory.

```
mkdir /meshcentral/meshcentral-logs/
chown tactical:tactical logs
ln -s ../meshcentral-logs/log.txt /meshcentral/meshcentral-data/log.txt
```

And then add this to your config.

```json
    "meshErrorLogPath": "/meshcentral/meshcentral-logs/",
    "authLog": "/meshcentral/meshcentral-logs/auth.log",
    "log": [
        "cookie",
        "dispatch",
        "main",
        "peer",
        "web",
        "webrequest",
        "relay",
        "webrelaydata",
        "webrelay",
        "mps",
        "mpscmd",
        "swarm",
        "swarmcmd",
        "agentupdate",
        "agent",
        "cert",
        "db",
        "email",
        "amt",
        "httpheaders",
        "websocket"
    ],
```

You'll then have 3 files:

```bash
-rw-rw-r-- 1 tactical tactical   2593 Feb  2 12:22 auth.log
-rw-r--r-- 1 tactical tactical 147593 Feb  2 12:31 log.txt
-rw-rw-r-- 1 tactical tactical    381 Feb  2 12:02 mesherrors.txt
```

`log.txt` will now log everything in the Trace tab

## Restricting server to specific IP(s)

When doing debugging on my development server, I use this line in the settings section to block all agent connections except the agent I want:

```
"agentAllowedIp": [ "192.168.2.147" ],
```

Of course, this is just for debugging.

## Finding system ID types

<https://serverurl/meshagents> aka trying figure out what this is

![ID](images/determine-id.png)

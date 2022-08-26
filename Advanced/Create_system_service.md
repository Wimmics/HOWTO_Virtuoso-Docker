### Virtuoso as a system service on Linux

Creating a service file in /etc/systemd/system/
Call it as you want NAME.service
Adapt the following content:

```
[Unit]
Description=Launch a Virtuoso server
After=apache2

[Service]
ExecStart=docker run --name docker-virtuoso --interactive --tty --env DBA_PASSWORD=<PASSWORD> --publish 1111:1111 --publish 8890:8890 --volume `pwd`:/database openlink/virtuoso-opensource-7:latest
ExecStop=docker stop docker-virtuoso
ExecStopPost=docker rm docker-virtuoso
Restart=always


[Install]
WantedBy=multi-user.target
```

Enable it :

`sudo systemctl enable NAME.service`

after it :

`sudo system start NAME.service`

`sudo system stop NAME.service`

`sudo system status NAME.service`

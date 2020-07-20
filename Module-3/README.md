# Logging drives

These are the pluggable framerwork fro accessing log data from services and containers in Docker. You can use syslog or json-file

# Override container logging driver
You can use log drive using flags --log-driver and --log-opt

Making ti permanent
go to /etc/docker/daemon.json and add following
```
{
	"log-driver":"json-file",
	"log-opts":{
		"max-size":"15m"
	}
}
```
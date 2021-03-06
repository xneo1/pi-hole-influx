# Pi-hole-Influx to Grafana

A simple daemonized script to report Pi-Hole stats to an InfluxDB, ready to be displayed via Grafana.

(Initially forked by [@Janw](https://github.com/janw/pi-hole-influx))

![Example Grafana Dashboard](.readme-assets/Dashboard.jpg)

## Requirements and Setup

As Pi-hole (as the name suggests) is built specifically with the Raspberry Pi in mind (and I run it on there as well), the following steps assume an instance of Pi-hole on Raspbian Strech Lite, with no additional modifications so far. Piholeinflux will be configured to run on the same Pi. 

First install the necessary packages via apt as Raspbian Lite does have neither git nor pip installed.

```bash
sudo apt update
sudo apt install git python-pip -y
```

Now clone the repo, install the Python dependencies, and make sure to copy and adjust the example configuration file to match your setup.

```bash
git clone https://github.com/xneo1/pi-hole-influx.git
cd ~/pi-hole-influx

# Install requirements via pip
pip install -r requirements.txt

#Setup credentials ,Pi hole ip/port and influxdb ip/port

sudo nano piholeinflux.pi
and put the following details:

HOSTNAME = "pihole" # Pi-hole hostname to report in InfluxDB for each measurement
PIHOLE_API = "http://XXX.XXX.XXX.XXX/admin/api.php"
INFLUXDB_SERVER = "XXX.XXX.XXX.XXX" # IP or hostname to InfluxDB server
INFLUXDB_PORT = 8086 # Port on InfluxDB server
INFLUXDB_USERNAME = "-----------"
INFLUXDB_PASSWORD = "-----------"
INFLUXDB_DATABASE = "-----------"

Before starting the daemon for the first time, symlink the systemd service into place, reload, and enable the service.

```bash
sudo systemctl link /home/pi/pi-hole-influx/piholeinflux.service

sudo systemctl --system daemon-reload
sudo systemctl enable piholeinflux.service
```

Now you're ready to start the daemon. Wait a few seconds to check its status.

```bash
sudo systemctl start piholeinflux.service
sudo systemctl status piholeinflux.service
```

The status should look as follows. Note the `Status:` line showing the last time, the daemon reported to InfluxDB:

![Active and Running Service](.readme-assets/activesvc.jpg)

## Set up a Grafana Dashboard 

The example dashboard seen [at the top](#pi-hole-influx) uses the collected data and displays it in concise and sensible graphs and single stats. The dashboard can be imported into your Grafana instance from the `dashboard.json` file included in the repo, or by using ID `6603` to [import it from Grafana's Dashboard Directory](https://grafana.com/dashboards/6603).


## Attributions

The script originally [created by Jon Hayward](https://fattylewis.com/Graphing-pi-hole-stats/), adapted to work with InfluxDB [by /u/tollsjo in December 2016](https://github.com/sco01/piholestatus), and [improved and extended by @johnappletree](https://github.com/johnappletree/piholestatus) and [@Janw](https://github.com/janw/pi-hole-influx)

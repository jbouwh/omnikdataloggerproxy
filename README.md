# OMNIKDATALOGGERPROXY
![omnikdatalogger](https://github.com/jbouwh/omnikdatalogger/workflows/omnikdatalogger/badge.svg)
![PyPI version](https://badge.fury.io/py/omnikdataloggerproxy.svg) 

The `omnikdataloggerproxy.py` script and supporting files can be used intercept your inverters data messages. See the comments in the example shell script enand the config.ini example about how to use them.
Good luck with them.
The output can be processed with [omnikdatalogger](https://github.com/jbouwh/omnikdatalogger) for output to pvoutput, mqtt, influxdb and integration with Home Assistant.

## Install using pip
`sudo pip3 install omnikdataloggerproxy`

The supporting files are installed at the folder */usr/local/share/omnikdataloggerproxy/*

### Command line
```
usage: omnikloggerproxy.py [-h] --serialnumber SERIALNUMBER [SERIALNUMBER ...]
                           [--settings FILE  Path to .yaml configuration file]
                           [--section  Section to .yaml configuration file to use. Defaults to the first section found.]
                           [--config FILE  Path to configuration file (ini) (DECREPATED!)]
                           [--loglevel LOGLEVEL]
                           [--listenaddress LISTENADDRESS]
                           [--listenport LISTENPORT]
                           [--omniklogger OMNIKLOGGER]
                           [--omnikloggerport OMNIKLOGGERPORT]
                           [--mqtt_host MQTT_HOST] [--mqtt_port MQTT_PORT]
                           [--mqtt_retain MQTT_RETAIN]
                           [--mqtt_discovery_prefix MQTT_DISCOVERY_PREFIX]
                           [--mqtt_client_name_prefix MQTT_CLIENT_NAME_PREFIX]
                           [--mqtt_username MQTT_USERNAME]
                           [--mqtt_password MQTT_PASSWORD]
                           [--mqtt_device_name MQTT_DEVICE_NAME]
                           [--mqtt_logger_sensor_name MQTT_LOGGER_SENSOR_NAME]
                           [--mqtt_tls MQTT_TLS]
                           [--mqtt_ca_certs MQTT_CA_CERTS]
                           [--mqtt_client_cert MQTT_CLIENT_CERT]
                           [--mqtt_client_key MQTT_CLIENT_KEY]
```
### Configuration file
The proxy parameters will fallback to the `config.yaml` in under key `omnikdatalogger->proxy`. Specify a configfile using the --config option.
This way it easier tot run omnikdatalogger proxy as a docker container.
> NOTE: The use of config.ini will is decrepated and config.yaml replaces config.ini.

```yaml
omnikdatalogger:
  proxy:
    serialnumber:
    - serial1232323
    ...
    ...
```

key | optional | type | default | description
-- | --| -- | -- | --
`serialnumber` | False | list | [] | List of serialnumbers of the inverters supported
`loglevel` | True | string | `INFO` | The basic loglevel [DEBUG, INFO, WARNING, ERROR, CRITICAL]
`listenaddress` | True | string | `127.0.0.1` | A local available address to listen to
`listenport` | True | int | `10004` | The local port to listen to
`omniklogger` | True | string | _None_ | Forward to an address omnik/SolarmanPV datalogger server listens to. Set this to `176.58.117.69` as final forwarder.
`listenport` | True | int | `10004` | The port the omnik/SolarmanPV datalogger server listens to.

You can use the mqtt based client of omnikdatalogger `localproxy.hassapi`. You need to specify `mqtt_host` to activate this feature and specify all needed attributes.

Config file settings will overrule the command line settings. The MQTT parameters will fallback to the settings in the section `output.mqtt:`. Specify a config file using the --settings (or --config) option.
For details see the [Omnik Data Logger README.md](https://github.com/jbouwh/omnikdatalogger#mqtt_proxy-plugin-for-the-localproxy-client-in-the-section-clientlocalproxymqtt_proxy-of-appsyaml-or-configini)

There are example config files included:
- [`config.yaml_example.txt`](https://github.com/jbouwh/omnikdataloggerproxy/blob/main/config.yaml_example.txt)
- [`config.ini_example.txt`](https://github.com/jbouwh/omnikdataloggerproxy/blob/main/config.ini_example.txt).

#### MQTT configuration
Omnikdatalogger proxy supports forwarding using MQTT. The Omnikdatalogger mqtt_proxy and hassapi plugin (localproxy client) can use the data published by Omnikdataloggerproxy. The MQTT can also be read from the config.yaml file under key `omnikdatalogger->proxy`.

```yaml
omnikdatalogger:
    ...
    ...
  mqtt:
    discovery_prefix: homeassistant
    ...
    ...    
```

| key                  | optional | type    | default                 | description                                                                                                                                                      |
| -------------------- | -------- | ------- | ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `discovery_prefix`   | True     | string  | _'homeassistant'_       | The mqtt plugin supports MQTT auto discovery with Home Assistant. The discovery_prefix configures the topic prefix Home Assistant listens to for auto discovery. |
| `device_name`        | True     | string  | _'Datalogger proxy'_    | Omnik data logger proxy only setting. Overrides the name of the datalogger.                                                                                      |
| `logger_sensor_name` | True     | string  | _'Datalogger'_          | Omnik data logger proxy only setting. Overrides the name of the datalogger datalogger sensor entity.                                                             |
| `append_plant_id`    | True     | bool    | _False_                 | When a device_name is specified the plant id can be added to the name te be able to identify the plant.                                                          |
| `host`               | True     | string  | `localhost`             | Hostname or fqdn of the MQTT server for publishing.                                                                                                              |
| `port`               | True     | integer | _1883_                  | MQTT port to be used.                                                                                                                                            |
| `retain`             | True     | bool    | _True_                  | Retains the data send to the MQTT service                                                                                                                        |
| `client_name_prefix` | True     | string  | _'ha-mqtt-omniklogger'_ | Defines a prefix that is used as client name. A 4 byte uuid is added to ensure an unique ID.                                                                     |
| `username`           | False    | string  | _(none)_                | The MQTT username used for authentication                                                                                                                        |
| `password`           | False    | string  | _(none)_                | The MQTT password used for authentication                                                                                                                        |
| `tls`                | True     | bool    | _False_                 | Secures the connection to the MQTT service, the MQTT server side needs a valid certificate                                                                       |
| `ca_certs`           | True     | string  | _(none)_                | File path to a file containing alternative CA's. If not configure the systems default CA is used                                                                 |
| `client_cert`        | True     | string  | _(none)_                | File path to a file containing a PEM encoded client certificate                                                                                                  |
| `client_key`         | True     | string  | _(none)_                | File path to a file containing a PEM encoded client private key                                                                                                  |


## Using Docker

### Run the docker container

The config files for omnikdataloggerproxy in the container will be will /config/config.yaml (settings), or if you would like to use the config.ini /config/config.ini. 

The following command will pull the `Docker` image, mount the `config.yaml` (in the root of the container) and create the `Docker` container.

```
$ docker run --name omnikdataloggerproxy -d -v ${PWD}/config.yaml:/config.yaml -p 10004:10004 --name omnikdataloggerproxy --restart unless-stopped jbouwh/omnikdataloggerproxy:latest
```

I also added a `docker-compose.yml` that can be used. Run it at the folder where your `config.yaml` file resites. It is still possible to use `config.ini` files as well. This option is decrepated now.

So, doing exactly the same ... but using `docker-compose`:

```
$ docker-compose -f /path/to/docker-compose.yml up -d
```


## Prearing to run omnikdataloggerproxy.py script (manual install)

* Make sure you have shell access (ssh or telnet).
* Install pip: `curl -k https://bootstrap.pypa.io/get-pip.py | python3` See: (https://stackoverflow.com/questions/47649902/installing-pip-on-a-dsm-synology)
* Install the paho mqtt client: `/volume1/@appstore/py3k/usr/local/bin/pip3 install paho-mqtt`. The path may be diferent. I used a Synology DS218 play

On upgrades it might be necessary to reinstall pip and paho-mqtt. Make sure you chack on this after an update for your Synology.

Now take the following steps:
* Place the omnikloggerproxy.py script, the bash script (omnikproxy_example_startup.sh) and `config.ini_example.txt` to a folder that will not be affected by upgrades. E.g. `/volume1/someshare/yourscriptfolder`.
* Rename `config.ini_example.txt` to `config.ini` and configure settings.
* check the commandline settings in the shell script.
* Try to execute the script to test if it is working. (You can use task plannel later to start the script at boot automatically as activated task)
* The lines to configure iptables should run as root. The omnikproxylogger script works at userlevel too.
* On your internet router/gateway, set up a static route for `176.58.117.69/32` to your synology.
* Configure MQTT to forward the data to be able to use the localproxy plugin with `hassapi` or `mqtt_proxy`.

You can forward the logger trafic to the omnik servers, but if you rerouted yhe traffic for 176.58.117.69 you need to forward to a linux server elswere in the internet.

## Running omnikdataloggerproxy as a service on a Debian based system

You can find the following sample service config at `/usr/local/share/omnikdataloggerproxy/omnikdatalogggerproxy.service` after installing `pip3 install omnikdataloggerproxy` as root.

```ini
[Unit]
Description=Omnik datalogger proxy
After=network.target
[Service]
Type=simple
ExecStart=/usr/local/bin/omnikloggerproxy.py --serialnumber NLDN123456789012 --listenaddress 0.0.0.0 --omniklogger 176.58.117.69 --omnikloggerport 10004
User=omnik
Group=users
Restart=on-failure
RestartSec=30s
[Install]
WantedBy=multi-user.target
```
The template service file shows a forwarding only setup.

To setup omnikdatalogger proxy as root do:
- Create a config folder and copy the sample service script:
  - `cd /etc/`
  - `mkdir omnikdataloggerproxy`
  - `cp /usr/local/share/omnikdataloggerproxy/omnikdatalogggerproxy.config .`
- Update `User` and *serialnumber* in the script `omnikdatalogggerproxy.config` using your favorite editor.
  - `nano omnikdatalogggerproxy.config`
- Link the script to systemd: to `ln -s /etc/omnikdataloggerproxy/omnikdatalogggerproxy.service /etc/systemd/system/omnikdatalogggerproxy.service`
- Enable the service: `systemctl enable omnikdatalogggerproxy`
- Start the service: `systemctl start omnikdatalogggerproxy`
- check if the service is running: `systemctl status omnikdatalogggerproxy`

After some time the logging should show something similar like this:
```
# systemctl status omnikdataloggerproxy
??? omnikdataloggerproxy.service - Omnik datalogger proxy
   Loaded: loaded (/etc/omnikdataloggerproxy/omnikdataloggerproxy.service; enabled; vendor preset: enabled)
   Active: active (running) since Sun 2020-06-21 13:33:21 CEST; 16min ago
 Main PID: 28182 (omnikloggerprox)
    Tasks: 4 (limit: 4915)
   Memory: 9.8M
   CGroup: /system.slice/omnikdataloggerproxy.service
           ??????28182 /usr/bin/python3 /usr/local/bin/omnikloggerproxy.py --serialnumber NLDN123456789012 --listenaddress 0.0.0.0 --omniklogger 176.58.117.69 --omnikloggerport 10004

Jun 21 13:33:22 alpha omnikloggerproxy.py[28182]: omnikloggerproxy: Forwarding succesful.
Jun 21 13:38:33 alpha omnikloggerproxy.py[28182]: omnikloggerproxy: Processing message for inverter 'NLDN123456789012'
Jun 21 13:38:33 alpha omnikloggerproxy.py[28182]: omnikloggerproxy: 2020-06-21 13:38:33.795477 Forwarding to omnik logger "176.58.117.69"
Jun 21 13:38:33 alpha omnikloggerproxy.py[28182]: omnikloggerproxy: Forwarding succesful.
Jun 21 13:43:38 alpha omnikloggerproxy.py[28182]: omnikloggerproxy: Processing message for inverter 'NLDN123456789012'
Jun 21 13:43:38 alpha omnikloggerproxy.py[28182]: omnikloggerproxy: 2020-06-21 13:43:38.937148 Forwarding to omnik logger "176.58.117.69"
Jun 21 13:43:38 alpha omnikloggerproxy.py[28182]: omnikloggerproxy: Forwarding succesful.
Jun 21 13:48:50 alpha omnikloggerproxy.py[28182]: omnikloggerproxy: Processing message for inverter 'NLDN123456789012'
Jun 21 13:48:50 alpha omnikloggerproxy.py[28182]: omnikloggerproxy: 2020-06-21 13:48:50.194599 Forwarding to omnik logger "176.58.117.69"
Jun 21 13:48:50 alpha omnikloggerproxy.py[28182]: omnikloggerproxy: Forwarding succesful.
```
The log shows when messages were forwarded.

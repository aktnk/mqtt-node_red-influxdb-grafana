# MQTT - Node-RED - InfluxDB - Grafana

Temperature and humidity sensor values installed in the room are received via MQTT, stored in InfluxDB using Node-RED, and monitored by Grafana.

## Prerequisites

From the MQTT Broker, which receives sensor values, to Grafana for monitoring, we use Docker containers to build the system.
Therefore, a docker environment must be installed and the docker compose command must be available.

This system has been tested in the following environments.
* Ubuntu 20.04LTS environment built on WSL2 of Windows 11 Pro
* Docker version 24.0.7
* Docker Compose version v2.21.0
* mosquitto-clients already installed in Ubuntu environment by `sudo apt install mosquitto-clients`

## Usage

1. Clone this repository

    ```
    $ git clone git@github.com:aktnk/mqtt-node_red-influxdb-grafana.git mqtt
    $ cd mqtt
    ```

1. Edit ".env" file

    Edit the .env file and fill in the InfluxDB and Grafana admin username, password, and InfluxDB database name.

1. Start each containers

    Start the container with the following command and check the message displayed.

    ```
    $ docker compose up
    ```

1. Stop and Restart each containers

    After confirming that each container is running without any errors, stop the container by `CTRL+C` and start it up again by `docker compose start`.

    ```
    Aborting on container exit...
    [+] Stopping 4/4
    ✔ Container node-red  Stopped                                                                                1.0s
    ✔ Container influxdb  Stopped                                                                                0.7s
    ✔ Container mqtt      Stopped                                                                                0.8s
    ✔ Container grafana   Stopped                                                                                1.1s
    canceled

    $ docker compose start
    [+] Running 4/4
    ✔ Container node-red  Started                                                                                1.0s
    ✔ Container influxdb  Started                                                                                0.7s
    ✔ Container mqtt      Started                                                                                1.0s
    ✔ Container grafana   Started                                                                                0.5s
    $
    ```

1. Create a database on InfluxDB

    Start the influx shell on the influxdb container and use the `CREATE DATABASE` command to create a database with the name specified in the environment variable "INFLUXDB_DATABASE" specified in the ".env" file.

    ```
    $ docker compose exec influxdb influx
    Connected to http://localhost:8086 version 1.8.10
    InfluxDB shell version: 1.8.10
    > CREATE DATABASE db0
    > exit
    $
    ```

1. Configurering Node-RED

    * Open Node-RED by accessing localhost:1880 in your browser.
    [Open the "Add Node" tab under "Palette Management" and add "node-red-contrib-influxdb".

    * Set up a flow in Node-RED to receive information from sensors via MQTT Broker and store it in InfluxDB.

1. Send test datas

    Use the mosquitto_pub command to manually send test data from the sensor.

    ```
    $ mosquitto_pub -t test/sensors -m '{"p":"room1","t":22.0,"h":54}'
    $ mosquitto_pub -t test/sensors -m '{"p":"room2","t":21.0,"h":50}'
    
    ```

1.  Grafana Sttings

    * Open Grafana by accessing localhost:3000 in your browser.
    Enter the administrator user name and password specified in the .env file, you will be prompted to change the password appropriately.
    * Add InfluxDB to Datasource.
    * Set up a Time series panel on the Dashboard.


## References

In constructing this environment, I referred to the following articles.

* [dockerhub - elipse-mosquitto](https://hub.docker.com/_/eclipse-mosquitto)
* [dockerhub - nodered/node-red](https://hub.docker.com/r/nodered/node-red)
* [node-red-contrib-inflxdb](https://flows.nodered.org/node/node-red-contrib-influxdb)
* [Get started with InfluxDB OSS](https://docs.influxdata.com/influxdb/v1/introduction/get-started/)
* [Get started with Grafana and InfluxDB](https://grafana.com/docs/grafana/latest/getting-started/get-started-grafana-influxdb/#get-started-with-grafana-and-influxdb)
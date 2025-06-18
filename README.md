# SmokePing Docker Setup Guide

This guide will help you install and run SmokePing locally using Docker and Docker Compose.

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/) installed on your machine
- [Docker Compose](https://docs.docker.com/compose/install/) installed

## 1. Create a Project Directory

Open a terminal and create a directory for your project:

```sh
mkdir smokeping
cd smokeping
```

## 2. Create a `docker-compose.yml` File

Create a file named `docker-compose.yml` in your project directory with the following content:

```yaml
version: '3.8'

services:
  smokeping:
    image: linuxserver/smokeping
    container_name: smokeping
    environment:
      - PUID=1000         # Change to your user id if needed
      - PGID=1000         # Change to your group id if needed
      - TZ=Europe/London  # Change to your timezone
    volumes:
      - ./config:/config
      - ./data:/data
    ports:
      - 8080:80           # Access SmokePing at http://localhost:8080
    restart: unless-stopped
```

**Note:**  

- You can change the `PUID`, `PGID`, and `TZ` values as needed.
- The `./config` and `./data` folders will be created automatically and used to persist your configuration and data.

## 3. Start SmokePing

Run the following commands in your project directory:

```sh
docker-compose pull
docker-compose up -d
```

This will download the latest SmokePing image and start the service in the background.

## 4. Access the SmokePing Web Interface

Open your browser and go to:

```browser
http://localhost:8080
```

You should see the SmokePing dashboard.

## 5. Stopping and Managing the Service

- To stop the service:  
  
  ```sh
  docker-compose down
  ```

- To view logs:  
  
  ```sh
  docker-compose logs -f
  ```

## 6. Customizing Configuration

- The first time you run SmokePing, configuration files will be created in the `./config` directory.
- Edit these files as needed to add targets or change settings.
- After making changes, restart the container:
  
  ```sh
  docker-compose restart
  ```

## 7. Configuring Ping Frequency (Database Config)

To adjust how often SmokePing sends pings (the probe frequency), you need to edit the `Database` section in the configuration file:

1. Open the `config/Database` file (created after the first run).

2. The file should look like this:

   ```sh
   *** Database ***
   step     = 300
   pings    = 20
   # ... other settings ...
   ```

   - `step` is the interval in seconds between each round of pings (default is 300 seconds = 5 minutes).
   - `pings` is the number of pings sent in each round.

3. To change the frequency, set `step` to your desired interval (e.g., `step = 60` for 1 minute).

4. Save the file and restart the container:

   ```sh
   docker-compose restart
   ```

**Note:** Lowering the `step` value increases the frequency of pings and may increase system and network load.

## 8. Adding a New Target for Probe

To monitor a new target (host or IP address) with SmokePing, follow these steps:

1. Open the `config/Targets` file in your `config` directory (created after the first run).

2. The file should look like this:

   ```sh
   + Targets
   menu = Top
   title = Network Latency Grapher

   ++ Example
   menu = Example
   title = Example Host
   host = example.com
   ```

3. To add a new target, copy the `++ Example` block and modify it. For example, to add a target for `google.com`:

   ```sh
   ++ Google
   menu = Google
   title = Google
   host = google.com
   ```

   - `++ Google` is the unique identifier for the target.
   - `menu` is the label shown in the web interface.
   - `title` is the title for the graph.
   - `host` is the hostname or IP address to probe.

4. Save the file and restart the container:

   ```sh
   docker-compose restart
   ```

After restarting, the new target will appear in the SmokePing web interface and monitoring will begin automatically.

---

**You're all set!**  
SmokePing should now be running locally in Docker. For more advanced configuration, refer to the [SmokePing documentation](https://oss.oetiker.ch/smokeping/doc/smokeping_config.en.html) or the [linuxserver/smokeping Docker image page](https://hub.docker.com/r/linuxserver/smokeping).

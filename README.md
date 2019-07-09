# Automata

[![Build Status](https://drone.rileyflynn.me/api/badges/MUNComputerScienceSociety/Automata/status.svg)](https://drone.rileyflynn.me/MUNComputerScienceSociety/Automata)
[![](https://images.microbadger.com/badges/image/muncs/automata.svg)](https://microbadger.com/images/muncs/automata "Get your own image badge on microbadger.com")

Discord bot handling the management of the MUN Computer Science Society Discord server.

## Running locally

1. Copy `.env.dist` to `.env`
2. Fill out the required information in the `.env`
   - At the moment, the only required environment variable required is `AUTOMATA_TOKEN`, which is a Discord token, which you can see how to get [here](https://discordpy.readthedocs.io/en/latest/discord.html)
3. Install the Loki logging driver
   - `docker plugin install grafana/loki-docker-driver:latest --alias loki --grant-all-permissions`
4. Start the containers by running `docker-compose up -d`

## Developing your own plugins

1. Create the folder `mounted_plugins` if it doesn't already exist.
   - `plugins` is baked into the image when it is built, so editing files there won't have an effect
2. Create a new plugin within the `mounted_plugins` folder
   1. Create a new [Jigsaw plugin manifest](https://jigsaw.readthedocs.io/en/latest/plugin.json.html)
      - You can use `plugins/PrivateChannels/plugin.json` as an example
   2. Create a new plugin
      - You can use `plugins/PrivateChannels/__init__.py` as an example
3. Start the bot using the instructions from [Running locally](#running-locally)

When you make changes to your plugins, restart the Automata container using `docker-compose restart automata`

## Developing the bot core and built-in plugins

1. Edit the `docker-compose.yml`, replacing the `image: muncs/automata` line for the automata container with `build: .`
2. Edit the bot core or the plugins as you wish
3. Start the container, forcing a rebuild of the image using `docker-compose up -d --build`

## Container responsibilities

Automata is comprised of a number of containers, each with distinct responsibilities. Their responsibilities are as follows:

| Container | Responsibilities |
| --- | --- |
| automata | The Discord bot itself. |
| mongo | A MongoDB server used to provide persistent data storage to the `automata` container. |
| prometheus | A Prometheus server used to collect statistics from the `automata` container. |
| grafana | A Grafana instance that collects data from `prometheus` and uses them to produce graphs. |
| cadvisor | Exports stats on running docker containers to `prometheus` |
| mongodbexporter | Exports stats from `mongo` to `prometheus` |

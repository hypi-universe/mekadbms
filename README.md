# Overview

MekaDB™ is Hypi's SQL data platform.
It allows you to create databases, schemas and tables like a traditional relational database.
On top of that, it gives you the ability to scale to hundreds of nodes so you can handle any scale your business runs at.

Some key points:

* Create SQL databases
* Create SQL schemas
* Create SQL tables
* Run modern, standards based SQL queries including ALL the JOIN types across tables
* Auto index - a MekaDB™ feature means you don't need to create custom indices and ALL your queries will run performantly regardless of how much data you have in the table. Just add more nodes to meet your scale.
* Built in ETL - No more external ETL tools needed, MekaDB™ comes with integration to over 300+ sources you can get data from and sync it into your tables e.g. external databases like Postgres, MySQL or APIs like Facebook ads or Google sheets can all be synced continuously or periodically with just a one off command to set up the sync. You can also sync data from MekaDB™ to some external destinations
* Binaries for all major OS and CPU architecture are supported
* Run with Docker or download the binary for your OS and run it directly
* Built with SaaS use cases in mind, you can create millions of databases and tables without any performance implication so feel free to create a database per SaaS tenant!
* Runs on your local computer or server (works independently or in Kubernetes)

# MekaDB Management Server

This public MekaDB distribution is designed to run MekaDB with one command.
Since MekaDB requires a storage provider e.g. Opensearch, this repo provides a binary which handles configuring both MekaDB and starting a storage provider.

This mode can be used in production environments which do not require distributed storage but is not recommended and rather is intended for developers to use to develop applications and integration tests against MekaDB before deploying to production.

For production, you should set `MEKADB_OPENSEARCH_HOST` which points to a production setup Opensearch cluster. 
This means you can have any number of replicas of MekaDB pointing to the same Opensearch cluster.

# Getting started

You can get MekaDB™ through your preferred channel.

## Docker

For Docker, you need a single command.

### On Linux and Mac

    docker run --privileged -it -p 2023 -p 2024 hypi/mekadbms

On Linux and Mac, because MekaDB starts and manages other containers for you dynamically, this is needed to provide it access to Docker running on your machine.

If you do not want to provide `--privileged` then you can set the environment variable `MEKADB_OPENSEARCH_HOST` to a URL where Opensearch is running and MekaDB will use it instead of starting one itself.

Note that without this, you will still be able to use pipes to sync data into and push data out of MekaDB but you need to use the MekaDB client libraries and write code to do it.
`sudo` is not needed unless your Docker requires it to run commands. MekaDB will get permission denied and you need to re-run the Docker command with `sudo`.

### On windows
    docker run -it -p 2023 -p 2024 hypi/mekadbms
No other option needed but you may need to run it in an Administrator Powershell instance

### Binary and latest release
You can download the latest binaries from our [latest release](https://github.com/hypi-universe/mekadbms/releases/latest).

# Environment variables

| Name                             	| Default                      	| Comment                                                                                                                                                                                                                                                                                                                                      	|
|----------------------------------	|------------------------------	|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| MEKADB_OPENSEARCH_HOST           	| https://localhost:9200       	| Points to an Opensearch cluster where your data will be stored                                                                                                                                                                                                                                                                               	|
| MEKADB_OPENSEARCH_USERNAME       	| admin                        	| The username for the Opensearch admin account                                                                                                                                                                                                                                                                                                	|
| MEKADB_OPENSEARCH_PASS           	| A4DS_a1KauwK!aIl             	| You should really set your own!                                                                                                                                                                                                                                                                                                              	|
| MEKADB_OPENSEARCH_VERIFY_SSL     	| false                        	| Set to true if your opensearch cluster has a valid SSL certificate                                                                                                                                                                                                                                                                           	|
| MEKADB_MAPPING_FILE              	| mapping.json                 	| Hypi's default mapping.json is built in                                                                                                                                                                                                                                                                                                      	|
| MEKADB_INDEX_NAME                	| data-0                       	| The Opensearch index where your data is stored                                                                                                                                                                                                                                                                                               	|
| MEKADB_AES_VERSION               	| 1                            	|                                                                                                                                                                                                                                                                                                                                              	|
| MEKADB_AES_PASSWORD              	| LasF_w4@43h$                 	| You should really set your own!                                                                                                                                                                                                                                                                                                              	|
| MEKADB_OPENSEARCH_IMAGE          	| opensearchproject/opensearch 	| Technically could possible be Elasticsearch but not officially supported                                                                                                                                                                                                                                                                     	|
| MEKADB_OPENSEARCH_TAG            	| 2.14.0                       	|                                                                                                                                                                                                                                                                                                                                              	|
| MEKADB_OPENSEARCH_ARC            	| linux/amd64                  	| We try to detect it but can be wrong so set if needed                                                                                                                                                                                                                                                                                        	|
| MEKADB_OPENSEARCH_CREDS          	|                              	| If you set the MEKADB_OPENSEARCH_IMAGE to a private registry, provide a  JSON object like `{"username":"user", "password":"pass","serveraddress":"private.host.com"}`  Other supported keys in the JSON are: * `auth` * `email` * `identitytoken` * `registrytoken`  Which ones you need depending on the auth method your registry supports 	|
| MEKADB_OPENSEARCH_HOST_INTERFACE 	| 0.0.0.0                      	| If you don't set `MEKADB_OPENSEARCH_HOST` MekaDB will start opensearch for you. It will bind Opensearch to this network interface.  For production/servers you may not want Opensearch listening to requests directly from the internet so change this to an internal interface.                                                             	|
| MEKADB_OPENSEARCH_HOST_PORT      	| 9200                         	| The port MekaDB will start Opensearch on if you do not set `MEKADB_OPENSEARCH_HOST`                                                                                                                                                                                                                                                          	|
|                                  	|                              	|                                                                                                                                                                                                                                                                                                                                              	|

# Docker
In order to perform various actions with Docker, we need to run in `privileged` mode on Linux/Mac.
This mode is not supported on Windows but is also not necessary.

# How to install

Installation method depends on your OS, CPU and preferred method. Below is an overview of various options and their current availability.


|                  |                                                                                                             |                                                                                                                 |                  |                                                                                                                  |                                                                                                                |
|------------------|-------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|------------------|------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------|
|                  | Windows                                                                                                     | Linux (Debian based/Ubuntu)                                                                                     | Linux RHEL based | Mac (x86/Intel)                                                                                                  | Mac (ARM64/M1-4)                                                                                               |
| Binary ARM64     |                                                                                                             | [mekadbms-linux-arm64](https://github.com/hypi-universe/mekadbms/releases/latest/download/mekadbms-linux-arm64) | [mekadbms-linux-arm64](https://github.com/hypi-universe/mekadbms/releases/latest/download/mekadbms-linux-arm64)                | -                                                                                                                | [mekadbms-mac-arm64](https://github.com/hypi-universe/mekadbms/releases/latest/download/mekadbms-darwin-arm64) |
| Binary AMD64     | [mekadbms-amd64.exe](https://github.com/hypi-universe/mekadbms/releases/latest/download/mekadbms-amd64.exe) | [mekadbms-linux-amd64](https://github.com/hypi-universe/mekadbms/releases/latest/download/mekadbms-linux-amd64) | [mekadbms-linux-amd64](https://github.com/hypi-universe/mekadbms/releases/latest/download/mekadbms-linux-amd64)                | [mekadbms-linux-amd64](https://github.com/hypi-universe/mekadbms/releases/latest/download/mekadbms-darwin-arm64) | [mekadbms-mac-amd64](https://github.com/hypi-universe/mekadbms/releases/latest/download/mekadbms-darwin-amd64) |
| Docker ARM64     | ✅                                                                                                           | ✅                                                                                                               | ✅                | ✅                                                                                                                | ✅                                                                                                              |
| Docker AMD64     | ✅                                                                                                           | ✅                                                                                                               | ✅                | ✅                                                                                                                | ✅                                                                                                              |
| .deb ARM64       | ‒                                                                                                           | 🚧                                                                                                              | ‒                | ‒                                                                                                                | ‒                                                                                                              |
| .deb AMD64       | ‒                                                                                                           | 🚧                                                                                                              | ‒                | ‒                                                                                                                | ‒                                                                                                              |
| .rpm ARM64       | ‒                                                                                                           | ‒                                                                                                               | 🚧               | ‒                                                                                                                | ‒                                                                                                              |
| .rpm AMD64       | ‒                                                                                                           | ‒                                                                                                               | 🚧               | ‒                                                                                                                | ‒                                                                                                              |
| .pkg ARM64       | ‒                                                                                                           | ‒                                                                                                               | ‒                | 🚧                                                                                                               | 🚧                                                                                                             |
| .pkg ARM64       | ‒                                                                                                           | ‒                                                                                                               | ‒                | 🚧                                                                                                               | 🚧                                                                                                             |
| Chocolatey ARM64 | 🚧                                                                                                          | ‒                                                                                                               | ‒                | ‒                                                                                                                | ‒                                                                                                              |
| Chocolatey AMD64 | 🚧                                                                                                          | ‒                                                                                                               | ‒                | ‒                                                                                                                | ‒                                                                                                              |
| Homebrew ARM64   | ‒                                                                                                           | ‒                                                                                                               | ‒                | 🚧                                                                                                               | 🚧                                                                                                             |
| Homebrew AMD64   | ‒                                                                                                           | ‒                                                                                                               | ‒                | 🚧                                                                                                               | 🚧                                                                                                             |
| MacPorts ARM64   | ‒                                                                                                           | ‒                                                                                                               | ‒                | 🚧                                                                                                               | 🚧                                                                                                             |
| MacPorts AMD64   | ‒                                                                                                           | ‒                                                                                                               | ‒                | 🚧                                                                                                               | 🚧                                                                                                             |

✅ = live/available now
🚧 = planned/in progress
‒  = not applicable to this OS

# License
MekaDB™ is available to use under the [Hypi Software License Agreement](https://github.com/hypi-universe/mekadbms/blob/main/LICENSE.md).  You can use it commercially with a free license :).

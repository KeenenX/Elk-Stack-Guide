# Full ELK Stack Guide for Beginners: (Elasticsearch, Logstash, Kibana)

[![Elastic Stack version](https://img.shields.io/badge/Elastic%20Stack-9.0.2-00bfb3?style=flat&logo=elastic-stack)](https://www.elastic.co/blog/category/releases)

Run the latest version of the [Elastic stack][elk-stack]

Based on the [official Docker images][elastic-docker] from Elastic:

* [Elasticsearch](https://github.com/elastic/elasticsearch/tree/main/distribution/docker)
* [Logstash](https://github.com/elastic/logstash/tree/main/docker)
* [Kibana](https://github.com/elastic/kibana/tree/main/src/dev/build/tasks/os_packages/docker_generator)


> [!IMPORTANT]
> [Platinum][subscriptions] features are enabled by default for a [trial][license-mngmt] duration of **30 days**. After
> this evaluation period, you will retain access to all the free features included in the Open Basic license seamlessly,
> without manual intervention required, and without losing any data. Refer to the [How to disable paid
> features](#how-to-disable-paid-features) section to opt out of this behaviour.

---



## Overview

The **ELK Stack** is a powerful set of tools—**Elasticsearch**, **Logstash**, and **Kibana**—used for searching, analyzing, and visualizing log data in real time. **Logstash** collects and processes incoming data from various sources, **Elasticsearch** stores and indexes that data for fast search, and **Kibana** provides a user-friendly dashboard to explore and visualize the results. Together, they help monitor systems, troubleshoot issues, and gain insights from large volumes of data.

---

## Contents

1. [Requirements](#requirements)
   * [Host setup](#host-setup)

1. [Installation](#installation)
   * [Bringing up the stack](#bringing-up-the-stack)
   * [Initial setup](#initial-setup)
     * [Setting up user authentication](#setting-up-user-authentication)
     * [Injecting data](#injecting-data)
   * [Cleanup](#cleanup)
   * [Version selection](#version-selection)
1. [Configuration](#configuration)
   * [How to configure Elasticsearch](#how-to-configure-elasticsearch)
   * [How to configure Kibana](#how-to-configure-kibana)
   * [How to configure Logstash](#how-to-configure-logstash)
   * [How to disable paid features](#how-to-disable-paid-features)
   * [How to scale out the Elasticsearch cluster](#how-to-scale-out-the-elasticsearch-cluster)
   * [How to re-execute the setup](#how-to-re-execute-the-setup)
   * [How to reset a password programmatically](#how-to-reset-a-password-programmatically)
1. [Extensibility](#extensibility)
   * [How to add plugins](#how-to-add-plugins)
   * [How to enable the provided extensions](#how-to-enable-the-provided-extensions)
1. [JVM tuning](#jvm-tuning)
   * [How to specify the amount of memory used by a service](#how-to-specify-the-amount-of-memory-used-by-a-service)
   * [How to enable a remote JMX connection to a service](#how-to-enable-a-remote-jmx-connection-to-a-service)
1. [Going further](#going-further)
   * [Plugins and integrations](#plugins-and-integrations)

## Requirements

### Host setup

* [Install Elasticsearch](https://github.com/elastic/elasticsearch/tree/main/distribution/docker)
* [Install Logstash](https://github.com/elastic/logstash/tree/main/docker)
* [Install Kibana](https://github.com/elastic/kibana/tree/main/src/dev/build/tasks/os_packages/docker_generator)

> [!NOTE]
> Any version of Elasticsearch below 7.0 (specifically Elasticsearch 6.x and earlier) requires you to manually install the JDK (Java Development Kit)
> because it does not come bundled with Java.

### Software Prerequisites
* **PowerShell / Command Prompt:**
     * For running `.bat` scripts and managing services.
* **JDK (Optional)**
     * Elasticsearch comes with a bundled JDK; no need to install unless custom configuration requires it.
* **7-Zip or WinRAR**
     * To extract `.zip` distributions of Elasticsearch, Kibana, and Logstash.
* **Text Editor (Notepad++ / VS Code)**
     * For editing `.yml` configuration files.
* **Browser (Chrome / Firefox)**
     * To access the Kibana dashboard (`http://localhost:5601`).

#### By default, the stack exposes the following ports:

* Elasticsearch: 9200 (HTTP), 9300 (Transport)
* Kibana: 5601
* Logstash (default): 5044 (or other custom Beats/inputs)


> [!WARNING]
> Elasticsearch's [bootstrap checks][bootstrap-checks] were purposely disabled to facilitate the setup of the Elastic
> stack in development environments. For production setups, we recommend users to set up their host according to the
> instructions from the Elasticsearch documentation: [Important System Configuration][es-sys-config].


## Installation

> [!IMPORTANT]
> Depending on the the configuration of your host/VM system you may come across errors throught the tutorial. If you come across any error messages throughtout the tutorial, copy the error message into chatGPT to troubleshoot the issue.

### Download Elasticsearch, Logstash, & Kibana
* [Install Elasticsearch](https://github.com/elastic/elasticsearch/tree/main/distribution/docker)
* [Install Logstash](https://github.com/elastic/logstash/tree/main/docker)
* [Install Kibana](https://github.com/elastic/kibana/tree/main/src/dev/build/tasks/os_packages/docker_generator)


> [!NOTE]
> Java JDK comes bundled with these downloads for versions 7.0+ 

### Extract Files

* Extract all 3 folders into a newly created folder called "Elk Stack" on your Local Disk C: drive

```sh
C:\ELK Stack
```

### Start Elastic Search

* Open Command Prompt (Run as Administator)

* **Navigate to the Elasticsearch** `bin` **folder:**
Open the folder where you extracted Elasticsearch, go into the `bin` directory, and copy its path from the address bar. Then, in Command Prompt, type `cd` followed by the path you copied. It should look something like this:
```sh
cd C:\ELK Stack\elasticsearch-9.0.2\bin
```
* Start ElasticSearch: Copy and paste this into CMD
```sh
elasticsearch.bat
```

Give Elasticsearch about a minute to initialize, then access the Elasticsearch web UI by opening <http://localhost:9200> in a web
browser and use the following (default) credentials to log in:

* user: *elastic*
* password: *changeme*

If it doesn’t work, reopen Command Prompt, navigate to the Elasticsearch `bin` directory, and run the following command:
```sh
elasticsearch-reset-password -u elastic
```
### Start Kibana

* Open Command Prompt (Run as Administator)

* **Navigate to the Kibana** `bin` **folder:**
Open the folder where you extracted Kibana, go into the `bin` directory, and copy its path from the address bar. Then, in Command Prompt, type `cd` followed by the path you copied. It should look something like this:
```sh
cd C:\ELK Stack\kibana-9.0.2\bin
```
* Start Kibana: Copy and paste this into CMD
```sh
Kibana.bat
```
Give Elasticsearch about a minute to initialize, then access the Elasticsearch web UI by opening <http://localhost:5601> in a web
browser and use the following (default) credentials to log in:

* user: *elastic*
* password: *changeme* **(Or use the reset password generated)**


## Start Logstash
1. Open VS Code
2. Create a file: *logstash.conf*
3. Save file into Logstash folder
4. Copy and Paste code below:
```sh
input {
    stdin {}
}

output {
    elasticsearch {
        hosts => ["localhost:9200"] 
        user => "elastic"
        password => "changeme"
        index => "indexforlogstash"
    }

}
```
> [!NOTE]
> Your user and password could be different, input the correct credentials



## Add Elasticsearch, Logstash, and Kibana into Windows Environmental Variables

Overview:

1. Right click Windows icon and open settings
2. Use search bar to find *"Advanced System Settings"*.
3. Select **Environment Varibles**
4. Under System Varibles, double click on **Path**, select **New**, then add all 3 Bin directories for Elasticsearch, Logstash, & Kibana:
```sh
C:\ELK Stack\elasticsearch-9.0.2\bin
```
```sh
C:\ELK Stack\kibana-9.0.2\bin
```
```sh
C:\ELK Stack\logstash-9.0.2\bin
```



> [!NOTE]
> Upon the initial startup, the `elastic`, `logstash_internal` and `kibana_system` Elasticsearch users are initialized
> with the values of the passwords defined in the [`.env`](.env) file (_"changeme"_ by default). The first one is the
> [built-in superuser][builtin-users], the other two are used by Kibana and Logstash respectively to communicate with
> Elasticsearch. This task is only performed during the _initial_ startup of the stack. To change users' passwords
> _after_ they have been initialized, please refer to the instructions in the next section.

### Initial setup

#### Setting up user authentication

> [!NOTE]
> Refer to [Security settings in Elasticsearch][es-security] to disable authentication.

> [!WARNING]
> Starting with Elastic v8.0.0, it is no longer possible to run Kibana using the bootstrapped privileged `elastic` user.

The _"changeme"_ password set by default for all aforementioned users is **unsecure**. For increased security, we will
reset the passwords of all aforementioned Elasticsearch users to random secrets.

1. Reset passwords for default users

    The commands below reset the passwords of the `elastic`, `logstash_internal` and `kibana_system` users. Take note
    of them.

    ```sh
    docker compose exec elasticsearch bin/elasticsearch-reset-password --batch --user elastic
    ```

    ```sh
    docker compose exec elasticsearch bin/elasticsearch-reset-password --batch --user logstash_internal
    ```

    ```sh
    docker compose exec elasticsearch bin/elasticsearch-reset-password --batch --user kibana_system
    ```

    If the need for it arises (e.g. if you want to [collect monitoring information][ls-monitoring] through Beats and
    other components), feel free to repeat this operation at any time for the rest of the [built-in
    users][builtin-users].

1. Replace usernames and passwords in configuration files

    Replace the password of the `elastic` user inside the `.env` file with the password generated in the previous step.
    Its value isn't used by any core component, but [extensions](#how-to-enable-the-provided-extensions) use it to
    connect to Elasticsearch.

    > [!NOTE]
    > In case you don't plan on using any of the provided [extensions](#how-to-enable-the-provided-extensions), or
    > prefer to create your own roles and users to authenticate these services, it is safe to remove the
    > `ELASTIC_PASSWORD` entry from the `.env` file altogether after the stack has been initialized.

    Replace the password of the `logstash_internal` user inside the `.env` file with the password generated in the
    previous step. Its value is referenced inside the Logstash pipeline file (`logstash/pipeline/logstash.conf`).

    Replace the password of the `kibana_system` user inside the `.env` file with the password generated in the previous
    step. Its value is referenced inside the Kibana configuration file (`kibana/config/kibana.yml`).

    See the [Configuration](#configuration) section below for more information about these configuration files.

1. Restart Logstash and Kibana to re-connect to Elasticsearch using the new passwords

    ```sh
    docker compose up -d logstash kibana
    ```

> [!NOTE]
> Learn more about the security of the Elastic stack at [Secure the Elastic Stack][sec-cluster].

#### Injecting data

Launch the Kibana web UI by opening <http://localhost:5601> in a web browser, and use the following credentials to log
in:

* user: *elastic*
* password: *\<your generated elastic password>*

Now that the stack is fully configured, you can go ahead and inject some log entries.

The shipped Logstash configuration allows you to send data over the TCP port 50000. For example, you can use one of the
following commands — depending on your installed version of `nc` (Netcat) — to ingest the content of the log file
`/path/to/logfile.log` in Elasticsearch, via Logstash:

```sh
# Execute `nc -h` to determine your `nc` version

cat /path/to/logfile.log | nc -q0 localhost 50000          # BSD
cat /path/to/logfile.log | nc -c localhost 50000           # GNU
cat /path/to/logfile.log | nc --send-only localhost 50000  # nmap
```

You can also load the sample data provided by your Kibana installation.

### Cleanup

Elasticsearch data is persisted inside a volume by default.

In order to entirely shutdown the stack and remove all persisted data, use the following Docker Compose command:

```sh
docker compose down -v
```

### Version selection

This repository stays aligned with the latest version of the Elastic stack. The `main` branch tracks the current major
version (9.x).

To use a different version of the core Elastic components, simply change the version number inside the [`.env`](.env)
file. If you are upgrading an existing stack, remember to rebuild all container images using the `docker compose build`
command.

> [!IMPORTANT]
> Always pay attention to the [official upgrade instructions][upgrade] for each individual component before performing a
> stack upgrade.

Older major versions are also supported on separate branches:

* [`release-8.x`](https://github.com/deviantony/docker-elk/tree/release-8.x): 8.x series
* [`release-7.x`](https://github.com/deviantony/docker-elk/tree/release-7.x): 7.x series (End-of-Life)
* [`release-6.x`](https://github.com/deviantony/docker-elk/tree/release-6.x): 6.x series (End-of-life)
* [`release-5.x`](https://github.com/deviantony/docker-elk/tree/release-5.x): 5.x series (End-of-life)

## Configuration

> [!IMPORTANT]
> Configuration is not dynamically reloaded, you will need to restart individual components after any configuration
> change.

### How to configure Elasticsearch

The Elasticsearch configuration is stored in [`elasticsearch/config/elasticsearch.yml`][config-es].

You can also specify the options you want to override by setting environment variables inside the Compose file:

```yml
elasticsearch:

  environment:
    network.host: _non_loopback_
    cluster.name: my-cluster
```

Please refer to the following documentation page for more details about how to configure Elasticsearch inside Docker
containers: [Install Elasticsearch with Docker][es-docker].

### How to configure Kibana

The Kibana default configuration is stored in [`kibana/config/kibana.yml`][config-kbn].

You can also specify the options you want to override by setting environment variables inside the Compose file:

```yml
kibana:

  environment:
    SERVER_NAME: kibana.example.org
```

Please refer to the following documentation page for more details about how to configure Kibana inside Docker
containers: [Install Kibana with Docker][kbn-docker].

### How to configure Logstash

The Logstash configuration is stored in [`logstash/config/logstash.yml`][config-ls].

You can also specify the options you want to override by setting environment variables inside the Compose file:

```yml
logstash:

  environment:
    LOG_LEVEL: debug
```

Please refer to the following documentation page for more details about how to configure Logstash inside Docker
containers: [Configuring Logstash for Docker][ls-docker].

### How to disable paid features

You can cancel an ongoing trial before its expiry date — and thus revert to a basic license — either from the [License
Management][license-mngmt] panel of Kibana, or using Elasticsearch's `start_basic` [Licensing API][license-apis]. Please
note that the second option is the only way to recover access to Kibana if the license isn't either switched to `basic`
or upgraded before the trial's expiry date.

Changing the license type by switching the value of Elasticsearch's `xpack.license.self_generated.type` setting from
`trial` to `basic` (see [License settings][license-settings]) will only work **if done prior to the initial setup.**
After a trial has been started, the loss of features from `trial` to `basic` _must_ be acknowledged using one of the two
methods described in the first paragraph.

### How to scale out the Elasticsearch cluster

Follow the instructions from the Wiki: [Scaling out Elasticsearch](https://github.com/deviantony/docker-elk/wiki/Elasticsearch-cluster)

### How to re-execute the setup

To run the setup container again and re-initialize all users for which a password was defined inside the `.env` file,
simply "up" the `setup` Compose service again:

```console
$ docker compose up setup
 ⠿ Container docker-elk-elasticsearch-1  Running
 ⠿ Container docker-elk-setup-1          Created
Attaching to docker-elk-setup-1
...
docker-elk-setup-1  | [+] User 'monitoring_internal'
docker-elk-setup-1  |    ⠿ User does not exist, creating
docker-elk-setup-1  | [+] User 'beats_system'
docker-elk-setup-1  |    ⠿ User exists, setting password
docker-elk-setup-1 exited with code 0
```

### How to reset a password programmatically

If for any reason your are unable to use Kibana to change the password of your users (including [built-in
users][builtin-users]), you can use the Elasticsearch API instead and achieve the same result.

In the example below, we reset the password of the `elastic` user (notice "/user/elastic" in the URL):

```sh
curl -XPOST -D- 'http://localhost:9200/_security/user/elastic/_password' \
    -H 'Content-Type: application/json' \
    -u elastic:<your current elastic password> \
    -d '{"password" : "<your new password>"}'
```

## Extensibility

### How to add plugins

To add plugins to any ELK component you have to:

1. Add a `RUN` statement to the corresponding `Dockerfile` (eg. `RUN logstash-plugin install logstash-filter-json`)
1. Add the associated plugin code configuration to the service configuration (eg. Logstash input/output)
1. Rebuild the images using the `docker compose build` command

### How to enable the provided extensions

A few extensions are available inside the [`extensions`](extensions) directory. These extensions provide features which
are not part of the standard Elastic stack, but can be used to enrich it with extra integrations.

The documentation for these extensions is provided inside each individual subdirectory, on a per-extension basis. Some
of them require manual changes to the default ELK configuration.

## JVM tuning

### How to specify the amount of memory used by a service

The startup scripts for Elasticsearch and Logstash can append extra JVM options from the value of an environment
variable, allowing the user to adjust the amount of memory that can be used by each component:

| Service       | Environment variable |
|---------------|----------------------|
| Elasticsearch | ES_JAVA_OPTS         |
| Logstash      | LS_JAVA_OPTS         |

To accommodate environments where memory is scarce (Docker Desktop for Mac has only 2 GB available by default), the Heap
Size allocation is capped by default in the `docker-compose.yml` file to 512 MB for Elasticsearch and 256 MB for
Logstash. If you want to override the default JVM configuration, edit the matching environment variable(s) in the
`docker-compose.yml` file.

For example, to increase the maximum JVM Heap Size for Logstash:

```yml
logstash:

  environment:
    LS_JAVA_OPTS: -Xms1g -Xmx1g
```

When these options are not set:

* Elasticsearch starts with a JVM Heap Size that is [determined automatically][es-heap].
* Logstash starts with a fixed JVM Heap Size of 1 GB.

### How to enable a remote JMX connection to a service

As for the Java Heap memory (see above), you can specify JVM options to enable JMX and map the JMX port on the Docker
host.

Update the `{ES,LS}_JAVA_OPTS` environment variable with the following content (I've mapped the JMX service on the port
18080, you can change that). Do not forget to update the `-Djava.rmi.server.hostname` option with the IP address of your
Docker host (replace **DOCKER_HOST_IP**):

```yml
logstash:

  environment:
    LS_JAVA_OPTS: -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.ssl=false -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.port=18080 -Dcom.sun.management.jmxremote.rmi.port=18080 -Djava.rmi.server.hostname=DOCKER_HOST_IP -Dcom.sun.management.jmxremote.local.only=false
```

## Going further

### Plugins and integrations

See the following Wiki pages:

* [External applications](https://github.com/deviantony/docker-elk/wiki/External-applications)
* [Popular integrations](https://github.com/deviantony/docker-elk/wiki/Popular-integrations)

[elk-stack]: https://www.elastic.co/elastic-stack/
[elastic-docker]: https://www.docker.elastic.co/
[subscriptions]: https://www.elastic.co/subscriptions
[es-security]: https://www.elastic.co/docs/reference/elasticsearch/configuration-reference/security-settings
[license-settings]: https://www.elastic.co/docs/reference/elasticsearch/configuration-reference/license-settings
[license-mngmt]: https://www.elastic.co/docs/deploy-manage/license/manage-your-license-in-self-managed-cluster
[license-apis]: https://www.elastic.co/docs/api/doc/elasticsearch/group/endpoint-license

[elastdocker]: https://github.com/sherifabdlnaby/elastdocker

[docker-install]: https://docs.docker.com/get-started/get-docker/
[compose-install]: https://docs.docker.com/compose/install/
[linux-postinstall]: https://docs.docker.com/engine/install/linux-postinstall/
[desktop-filesharing]: https://docs.docker.com/desktop/settings-and-maintenance/settings/#file-sharing

[bootstrap-checks]: https://www.elastic.co/docs/deploy-manage/deploy/self-managed/bootstrap-checks
[es-sys-config]: https://www.elastic.co/docs/deploy-manage/deploy/self-managed/important-system-configuration
[es-heap]: https://www.elastic.co/docs/deploy-manage/deploy/self-managed/important-settings-configuration#heap-size-settings

[builtin-users]: https://www.elastic.co/docs/deploy-manage/users-roles/cluster-or-deployment-auth/built-in-users
[ls-monitoring]: https://www.elastic.co/docs/reference/logstash/monitoring-with-metricbeat
[sec-cluster]: https://www.elastic.co/docs/deploy-manage/security#cluster-or-deployment-security-features

[config-es]: ./elasticsearch/config/elasticsearch.yml
[config-kbn]: ./kibana/config/kibana.yml
[config-ls]: ./logstash/config/logstash.yml

[es-docker]: https://www.elastic.co/docs/deploy-manage/deploy/self-managed/install-elasticsearch-with-docker
[kbn-docker]: https://www.elastic.co/docs/deploy-manage/deploy/self-managed/install-kibana-with-docker
[ls-docker]: https://www.elastic.co/docs/reference/logstash/docker-config

[upgrade]: https://www.elastic.co/docs/deploy-manage/upgrade/deployment-or-cluster/self-managed

<!-- markdownlint-configure-file
{
  "MD033": {
    "allowed_elements": [ "picture", "source", "img" ]
  }
}
-->

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
   * [Download Elasticsearch, Logstash, & Kibana](#download-elasticsearch-logstash-and-kibana)
   * [Extract Files](#extract-files)
   * [Start Elasticsearch](#start-elasticsearch)
   * [Start Kibana](#start-kibana)
   * [Start Logstash](#start-logstash)
   * [Add Elk Stack into Windows Environmental Variables](#add-elasticsearch-logstash-and-kibana-into-windows-environmental-variables)


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

### Download Elasticsearch, Logstash, and Kibana
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

### Start Elasticsearch

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

5. On VS Code, right-click the file, and copy the path
6. Open Command Prompt, type `cd`, then paste the logstash configuration path to change the directory
```sh
C:\ELK Stack\logstash-9.0.2\logstash.conf
```
7. Then paste code below:
```sh
logstash -f logstash.conf
```
8. Press enter to start logstash
> [!NOTE]
> Logstash installation instructions are also available on their website [here](elastic.co/downloads/logstash) 

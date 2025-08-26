<img src="https://github.com/orangecoding/fredy/blob/master/doc/logo.png" width="400">

![Test](https://github.com/orangecoding/fredy/actions/workflows/test.yml/badge.svg) [![Create and publish Docker image](https://github.com/orangecoding/fredy/actions/workflows/docker.yml/badge.svg)](https://github.com/orangecoding/fredy/actions/workflows/docker.yml) ![Check the sourcecode](https://github.com/orangecoding/fredy/actions/workflows/check_source.yml/badge.svg)

Searching an apartment in Germany can be a frustrating task. Not any longer though, as _Fredy_ will take over and will only notify you once new listings have been found that match your requirements.

_Fredy_ scrapes multiple services (Immonet, Immowelt etc.) and send new listings to you once they become available. The list of available services can easily be extended. For your convenience, _Fredy_ has a UI to help you configure your search jobs.

If _Fredy_ finds matching results, it will send them to you via Slack, Email, Telegram etc. (More adapters can be configured.) As _Fredy_ stores the listings it has found, new results will not be sent to you twice (and as a side-effect, _Fredy_ can show some statistics). Furthermore, _Fredy_ checks duplicates per scraping so that the same listings are not being sent twice or more when posted on various platforms (which happens more often than one might think).

# Sponsorship [![](https://img.shields.io/static/v1?label=Sponsor&message=%E2%9D%A4&logo=GitHub&color=%23fe8e86)](https://github.com/sponsors/orangecoding)

If you like my work, consider becoming a sponsor. I'm not expecting anybody to pay for _Fredy_ or any other Open Source Project I'm maintaining, however keep in mind, I'm doing all of this in my spare time :) Thanks.

[![JetBrains logo.](https://resources.jetbrains.com/storage/products/company/brand/logos/jetbrains.svg)](https://jb.gg/OpenSourceSupport)

_Fredy_ is supported by JetBrains under Open Source Support Program

## Demo

If you want to try out _Fredy_, you can access the demo version [here](https://fredy.orange-coding.net) 🤘

## Usage

- Make sure to use Node.js 20 or above
- Run the following commands:

```ssh
yarn
yarn run start:backend
yarn run start:frontend
```

_Fredy_ will start with the default port, set to `9998`. You can access _Fredy_ by opening your browser at `http://localhost:9998`. The default login is `admin`, both for username and password. You should change the password as soon as possible when you plan to run Fredy on a server.

<p align="center">
  <img alt="Job Configuration" src="https://github.com/orangecoding/fredy/blob/master/doc/screenshot1.png" width="30%">
&nbsp; &nbsp; &nbsp; &nbsp;
  <img alt="Job Analytics" src="https://github.com/orangecoding/fredy/blob/master/doc/screenshot_2.png" width="30%">
&nbsp; &nbsp; &nbsp; &nbsp;  
  <img alt="Job Overview" width="30%" src="https://github.com/orangecoding/fredy/blob/master/doc/screenshot_3.png">
</p>

## Understanding the fundamentals

There are 3 important parts in Fredy, that you need to understand to leverage the full power of _Fredy_.

#### Provider

_Fredy_ supports multiple services. Immonet, Immowelt and Ebay are just a few examples. Those services are called providers within _Fredy_. When creating a new job, you can choose one or more providers.
A provider contains the URL that points to the search results for the respective service. If you go to immonet.de and search for something, the displayed URL in the browser is what the provider needs to do its magic.
**It is important that you order the search results by date, so that _Fredy_ always picks the latest results first!**

#### Adapter

_Fredy_ supports multiple adapters, such as Slack, SendGrid, Telegram etc. A search job can have as many adapters as supported by _Fredy_. Each adapter needs different configuration values, which you have to provide when using them. An adapter dictates how the frontend renders by telling the frontend what information it needs in order to send listings to the user.

#### Jobs

A Job wraps adapters and providers. _Fredy_ runs the configured jobs in a specific interval (can be configured in `/conf/config.json`).

## Creating your first job

To create your first job, click on the button "Create New Job" on the job table. The job creation dialog should be self-explanatory, however there is one important thing.
When configuring providers, before copying the URL from your browser, make sure that you have sorted the results by date to make sure _Fredy_ always picks the latest results first.

## User management

As an administrator, you can create, edit and remove users from _Fredy_. Be careful, each job is connected to the user that has created the job. If you remove the user, their jobs will also be removed.

# Development

### Running Fredy in development mode

Start the backend with:

```shell
yarn run start:backend:dev
```

For the frontend, run:

```shell
yarn run start:frontend:dev
```

You should now be able to access _Fredy_ from your browser. Check your Terminal to see what port the frontend is running on.

### Running Tests

To run the tests, run

```shell
yarn run test
```

# Architecture

```mermaid
flowchart TD
 subgraph Jobs["Jobs"]
        A1["Job 1"]
        A2["Job 2"]
        A3["Job 3"]
  end
 subgraph Providers["Providers"]
        C1["Provider 1"]
        C2["Provider 2"]
        C3["Provider 3"]
  end
 subgraph NotificationAdapters["Notification Adapters"]
        F1["Notification Adapter 1"]
        F2["Notification Adapter 2"]
  end

    A1 --> B["FredyRuntime"]
    A2 --> B
    A3 --> B
    B --> C1 & C2 & C3
    C1 --> D["Similarity-Check"]
    C2 --> D
    C3 --> D
    D --> E{"Found<br>similarity?"}
    E -- No --> F1
    F1 --> F2

    style A1 fill:#fde9a0,stroke:#333333,color:#333333
    style A2 fill:#fde9a0,stroke:#333333,color:#333333
    style A3 fill:#fde9a0,stroke:#333333,color:#333333
    style C1 fill:#c4c9f1,stroke:#333333,color:#333333
    style C2 fill:#c4c9f1,stroke:#333333,color:#333333
    style C3 fill:#c4c9f1,stroke:#333333,color:#333333
    style F1 fill:#d2edba,stroke:#333333,color:#333333
    style F2 fill:#d2edba,stroke:#333333,color:#333333
    style B fill:#abd8f9,stroke:#333333,color:#333333
    style D fill:#fab4a8,stroke:#333333,color:#333333
    style E fill:#fffbb4,stroke:#333333,color:#333333
```

### Immoscout

Immoscout has implemented advanced bot detection. In order to work around this, we are using a reversed engineered version of their mobile api. See [Immoscout Reverse Engineering Documentation](https://github.com/orangecoding/fredy/blob/master/reverse-engineered-immoscout.md)

# Analytics

Fredy is completely free (and will always remain free). However, it would be a huge help if you’d allow me to collect some analytical data.
Before you freak out, let me explain...  
If you agree, Fredy will send a ping to my Mixpanel project each time it runs.  
The data includes: names of active adapters/providers, OS, architecture, Node version, and language. The information is entirely anonymous and helps me understand which adapters/providers are most frequently used.</p>
**Thanks**🤘

# Docker

Use the Dockerfile in this repository to build an image.

Example: `docker build -t fredy/fredy /path/to/your/Dockerfile`

Or use docker-compose:

Example `docker-compose build`

Or use the container that will be built automatically.

`docker pull ghcr.io/orangecoding/fredy:master`

## Create & run a container

Put your config.json into a path of your choice, such as `/path/to/your/conf/`.

Example: 
```
mkdir data
sudo docker create --name fredy -v /home/zee/fredy/conf:/conf -v /home/zee/fredy/data:/db -p 9998:9998 fredy/fredy
sudo systemctl start fredy
```

## Logs

You can browse the logs with `docker logs fredy -f`.

## jobs
You can export your jobs from the container with `docker cp fredy:/db/jobs.json ./jobs.json`. 

## Start docker as service

``` sh
# create a systemd service file
cat <<EOF | sudo tee /etc/systemd/system/fredy.service
[Unit]
Description=Fredy Service
After=docker.service
Requires=docker.service

[Service]
Restart=always
ExecStart=/usr/bin/docker start -a fredy
ExecStop=/usr/bin/docker stop -t 2 fredy

[Install]
WantedBy=multi-user.target
EOF

# enable the service
sudo systemctl enable fredy.service
```

### 👐 Contributing

Thanks to all the people who already contributed!

<a href="https://github.com/orangecoding/fredy/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=orangecoding/fredy" />
</a>

See [Contributing](https://github.com/orangecoding/fredy/blob/master/CONTRIBUTING.md)

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=orangecoding/fredy&type=Date)](https://www.star-history.com/#orangecoding/fredy&Date)

+++
title = "Connect Apache Superset© to MySql on Host"
date = "2020-11-22T22:21:46+02:00"
cover = ""
tags = ["python", "apache","superset", "analytics","data"]
keywords = ["python", "apache","superset", "analytics","data"]
description = ""
summary="👉 Starting out with Apache Superset? ♾
⚠️ These are the things I came accross, scratching my head for a while"
slug="connect-apache-superset-to-mysql-on-host"
+++


👉 Starting out with [Apache Superset](https://superset.apache.org/docs/installation/installing-superset-using-docker-compose)? ♾
⚠️ These are the things I came accross, scratching my head for a while

Off we go:

-   building *Superset* using the [github Superset repository](https://github.com/apache/incubator-superset) is easy, bút might not be the best approach if you're not actively going to develop new features for the platform itself

    -   **❌  reason being** that building the docker images requires a big amount of docker resources, especially to build the *Superset* image itself. It requires the [frontend to be built using Webpack](https://github.com/apache/incubator-superset/tree/master/superset-frontend) and it seems you either need to wait for an hour or so until the static resources can be provided **OR **you should assign at least 16GB of memory to Docker. Well, in my case, neither of those options seem to be the best way forward. (see [#11508](https://github.com/apache/incubator-superset/issues/11508) and [#9880](https://github.com/apache/incubator-superset/issues/9880))
    -   **✅  instead you can use** the community-built image through <https://github.com/amancevice/docker-superset> (image can be found on <https://hub.docker.com/r/amancevice/superset/>). 
        -   if you then follow [the examples found in the github repo](https://github.com/amancevice/docker-superset/tree/main/examples) you should be able to get *Superset* running in no-time!!  🚀 📈

-   after checking out the demo with provided datasets, graphs and dashboards, you'd probably want to start using your own dataset, right?

    -   so I wanted to connect *Superset*, which was running inside a docker container, directly to my application database (MariaDB) running on my host-machine. 
    -   when trying to setup a new datasource connection in *Superset* a database URI should be provided:
        -   ❌  *mysql://<user>:<password>@localhost:3306/<database_name>*: did not work, docker could not connect using the *unix_socket* 
        -   ❌  *mysql://<user>:<password>@127.0.0.1:3306/<database_name>*: did not work, ofcourse the database is not provided on that specific container
        -   ❌  *mysql://<user>:<password>@mysql_host:3306/<database_name>*: might have worked [using "extra_hosts" or a specific docker network in your docker-compose.yml](https://stackoverflow.com/questions/60355365/unable-to-connect-to-postgres-from-inside-docker-container)but this already seemed too much work to me and never felt like this was the out-of-the-box feature to connect to a service running on the host-machine..
        -   ✅  luckily after some time, [this was the answer I was looking for](https://stackoverflow.com/questions/24319662/from-inside-of-a-docker-container-how-do-i-connect-to-the-localhost-of-the-mach): **"Long live ~~the king~~... Stackoverflow, long live Stackoverflow!!"**
            -   **try this URI and it should work like a charm:** *mysql://<user>:<password>@host.docker.internal:3306/<database_name>* 🚀

Hope this might help!

G.
# Heroku Confluent Buildpack

Installs the [Confluent](https://confluent.io) distribution on
Heroku. While you won't be able to run ZooKeeper or Kafka on it, you
could in theory run Kafka Rest on it, which is the primary purpose of
this buildpack.

## Setup

```bash
$ heroku create -b https://github.com/heroku/heroku-confluent-buildpack.git
Creating vast-woodland-9430... done, stack is cedar-14
Buildpack set. Next release on vast-woodland-9430 will use https://github.com/heroku/heroku-confluent-buildpack.git.
https://vast-woodland-9430.herokuapp.com/ | https://git.heroku.com/vast-woodland-9430.git
Git remote heroku added
```

Now that you have an app, you can download and "install" a version
by setting up `CONFLUENT_VERSION=1.0.1`, and deploying an app that
makes use of confluent.

```bash
$ heroku config:set CONFLUENT_VERSION=1.0.1
Setting config vars and restarting vast-woodland-9430... done, v3
CONFLUENT_VERSION: 1.0.1
```

Setting `CONFLUENT_COMPONENT=(kafka-rest|schema-registry)` will also
create a `bin/run-confluent` script, which will perform the following
steps:

* If you have an executable at the root of your app named
  `properties-generate`, it will run it, with no arguments, and
  redirect STDOUT to confluent.properties.

* It will setup a trap to run `bin/$CONFLUENT_COMPONENT}-stop` on
  SIGINT, or SIGTERM.

* It will then substitute `%PORT%` for the value of the environment
  variable `$PORT` (this enables static configuration).

* It will then launch `bin/${CONFLUENT_COMPONENT}-start confluent.properties`

This provides ultimate flexibility in generating your properties file
from the heroku config, or through a static config file which is part
of your app.

## Your app's Procfile

```bash
$ cat Procfile
web: bin/run-confluent
```

# Install By Rpm

This article explains how to install the td-agent rpm package, the stable Fluentd distribution package maintained by [Treasure Data, Inc](http://www.treasuredata.com/).

## What is td-agent?

Fluentd is written in Ruby for flexibility, with performance sensitive parts written in C. However, casual users may have difficulty installing and operating a Ruby daemon.

That's why [Treasure Data, Inc](http://www.treasuredata.com/) is providing **the stable community distribution of Fluentd**, called `td-agent`. The differences between Fluentd and td-agent can be found [here](https://www.fluentd.org/faqs).

Currently, td-agent 2 rpm has two versions, td-agent 2.5 / td-agent 2.3. The different point is bundled ruby version. td-agent 2.5 or later uses ruby 2.5 and td-agent 2.3 or earlier uses ruby 2.1. ruby 2.1 is EOL so we recommend to use td-agent 2.5 for new deployment. td-agent 2.5 and td-agent 2.3 use fluentd v0.12 serise so the behaviour is same.

## Step 0: Before Installation

Please follow the [Preinstallation Guide](before-install.md) to configure your OS properly. This will prevent many unnecessary problems.

## Step 1: Install from rpm Repository

CentOS and RHEL 5, 6, 7 and Amazon Linux are currently supported.

Executing [install-redhat-td-agent2.sh](https://toolbelt.treasuredata.com/sh/install-redhat-td-agent2.sh) will automatically install td-agent on your machine. This shell script registers a new rpm repository at `/etc/yum.repos.d/td.repo` and installs the `td-agent` rpm package.

```text
# td-agent 2.5 or later. Only CentOS/RHEL 6 and 7 for now.
$ curl -L https://toolbelt.treasuredata.com/sh/install-redhat-td-agent2.5.sh | sh
# td-agent 2.3 or earlier
$ curl -L https://toolbelt.treasuredata.com/sh/install-redhat-td-agent2.sh | sh
```

It's HIGHLY recommended that you set up **ntpd** on the node to prevent invalid timestamps in your logs. Please check the [Preinstallation Guide](before-install.md). td-agent supports recent 2 Amazon Linux versions. It means if the latest version is 2016.09, we provides new packages for 2016.03 and 2016.09. No new packages for 2015.09 or earlier.

## Step2: Launch Daemon

The `/etc/init.d/td-agent` script is provided to start, stop, or restart the agent.

```text
$ sudo /etc/init.d/td-agent start
Starting td-agent: [  OK  ]
$ sudo /etc/init.d/td-agent status
td-agent (pid  21678) is running...
```

The following commands are supported:

```text
$ sudo /etc/init.d/td-agent start
$ sudo /etc/init.d/td-agent stop
$ sudo /etc/init.d/td-agent restart
$ sudo /etc/init.d/td-agent status
```

Please make sure **your configuration file** is located at `/etc/td-agent/td-agent.conf`.

## Step3: Post Sample Logs via HTTP

By default, `/etc/td-agent/td-agent.conf` is configured to take logs from HTTP and route them to stdout \(`/var/log/td-agent/td-agent.log`\). You can post sample log records using the curl command.

```text
$ curl -X POST -d 'json={"json":"message"}' http://localhost:8888/debug.test
```

## Next Steps

You're now ready to collect your real logs using Fluentd. Please see the following tutorials to learn how to collect your data from various data sources.

* Basic Configuration
  * [Config File](../configuration/config-file.md)
* Application Logs
  * [Ruby](ruby.md), [Java](java.md), [Python](python.md), [PHP](php.md),

    [Perl](perl.md), [Node.js](nodejs.md), [Scala](scala.md)
* Examples
  * [Store Apache Log into Amazon S3](apache-to-s3.md)
  * [Store Apache Log into MongoDB](apache-to-mongodb.md)
  * [Data Collection into HDFS](http-to-hdfs.md)

Please refer to the resources below for further steps.

* [ChangeLog of td-agent](https://docs.treasuredata.com/display/public/PD/The+td-agent+Change+Log)
* [Chef Cookbook](https://github.com/treasure-data/chef-td-agent/)

If this article is incorrect or outdated, or omits critical information, please [let us know](https://github.com/fluent/fluentd-docs-gitbook/issues?state=open). [Fluentd](http://www.fluentd.org/) is a open source project under [Cloud Native Computing Foundation \(CNCF\)](https://cncf.io/). All components are available under the Apache 2 License.


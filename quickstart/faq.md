# FAQ

## What version of Ruby does fluentd support?

Fluentd v0.12 works on 1.9.3 or later. Since v1.x, 2.1 or later.

## Known Issue

### I use Fluentd with Ruby 2.0 but Fluentd seems deadlocked. Why?

The rubygems of Ruby 2.0-p353 has a deadlock problem \([\#9224](https://bugs.ruby-lang.org/issues/9224)\). If you use Ruby 2.0-p353, upgrading Ruby to latest patch level or Ruby 2.1 resolve this problem.

Please make sure that you are using either RHEL 7.1 and ruby-2.0.0.598-25.el7\_1 package or alternatively you can choose more recent Ruby provided by Red Hat Software Collections \([https://access.redhat.com/documentation/en/red-hat-software-collections/](https://access.redhat.com/documentation/en/red-hat-software-collections/)\)

Unfortunately, Ruby 2.0 package of Ubuntu 14.04 use Ruby 2.0-p353. So don't use Fluentd with Ruby 2.0 package on this environment. You can install specified Ruby version using [rbenv](https://github.com/sstephenson/rbenv).

Using td-agent is another way to avoid this problem because td-agent includes own Ruby.

## Operations

### I have millisecond timestamp log but fluentd drops subsecond. Why?

In v0.12 or earlier, fluentd's event time is second unit. It means fluentd converts millisecond/nanosecond timestamp into second timestamp.

Since v1.x, event tims has nanosecond resolution, so fluentd can handle millisecond/nanosecond timestamp properly.

Visit [v1.x document](https://docs.fluentd.org/v1.0/articles/quickstart)

### I have a weird timestamp value, what happened?

The timestamps of Fluentd and its logger libraries depend on your system's clock. It's highly recommended that you set up NTP on your nodes so that your clocks remain synced with the correct clocks.

### I installed td-agent and want to add custom plugins. How do I do it?

td-agent has own Ruby so you should install gems into td-agent's Ruby, not system Ruby.

#### td-agent:

Please use `td-agent-gem` as shown below.

```text
$ /usr/sbin/td-agent-gem install <plugin name>
```

For example, issue the following command if you are adding `fluent-plugin-twitter`.

```text
$ /usr/sbin/td-agent-gem install fluent-plugin-twitter
```

Now you might be wondering, "Why do I need to specify the full path?" The reason is that td-agent does not modify any host environment variable, including `PATH`. If you want to make all td-agent/fluentd related programs available without writing "/usr/lib/..." every time, you can add

```text
export PATH=$PATH:/opt/td-agent/embedded/bin/
```

to your `~/.bash_profile`.

If you would like to find out more about plugin management, please take a look at the [Plugin Management](../deployment/plugin-management.md) article.

### I installed the plugin and it updates fluentd from v0.12 to v1.x. Why?

You installed v1.0 based plugin. See [Plugin Management](../deployment/plugin-management.md#plugin-version-management).

### How can I match \(send\) an event to multiple outputs?

You can use the `copy` [output plugin](faq.md) to send the same event to multiple output destinations.

### How can I use environment variables to configure parameters dynamically?

Use `"#{ENV['YOUR_ENV_VARIABLE']}"`. For example,

```text
some_field "#{ENV['FOO_HOME']}"
```

Note that it must be double quotes and not single quotes

### Fluentd raises an error for host:port. Why?

There are several reasons:

* If you get `Address already in use` error, other process has already

  used `host:port`. Check port conflict between processes / plugins.

* If you get `Permission denied` error, you try to use well-known

  port. Search `well-known port` for how to use well-known port. Use

  `capabilities` or something

If you get other errors, google it.

### I got "no patterns matched" in the log, why?

This means you emit the event but no `<match>` directive for emitted event. For example, if you emit the event with `foo.bar` tag, you need to define `<match>` for `foo.bar` tag like `<match foo.**>`.

See also: [Life of a Fluentd event](life-of-a-fluentd-event.md) or [Config File](../configuration/config-file.md)

### File buffer doesn't work properly, why?

`file` buffer has limitations. Check [`buf_file` article](https://docs.fluentd.org/v/0.12/buffer/file#limitation).

### I got enconding error inside plugin. How to fix it?

You may hit `"\xC3" from ASCII-8BIT to UTF-8"` like `UndefinedConversionError` in the plugin. This error happens when string encoding is set to `ASCII-8BIT` but actual content is `UTF-8`. Fluentd and almost plugins treat the logs as a `ASCII-8BIT` by default but some libraries assume the log encoding is `UTF-8`. This is why this error happens.

There are several approaches to avoid this problem.

* Set encoding correctly.
  * `tail` input has encoding related parameters to change the log

    encoding

  * Use `record_modifier` filter to change the encoding. See

    \[fluent-plugin-record-modifier

    README\]\([https://github.com/repeatedly/fluent-plugin-record-modifier\#char\_encoding](https://github.com/repeatedly/fluent-plugin-record-modifier#char_encoding)\)
* Use `yajl` instead of `json` when error happens inside

  `JSON.parse/JSON.dump`

## Plugin Development

### How do I develop a custom plugin?

Please refer to the [Plugin Development Guide](http://docs.fluentd.org/articles/plugin-development).

## HOWTOs

### How can I parse `<my complex text log>`?

If you are willing to write Regexp, [fluentd-ui's in\_tail editor](https://github.com/fluent/fluentd-docs-gitbook/tree/507e377b7e8e78a312dc49e76bd9a302c33fd058/articles/fluentd-ui/README.md#intail-setting) or [Fluentular](http://fluentular.herokuapp.com) is a great tool to verify your Regexps.

If you do NOT want to write any Regexp, look at [the Grok parser](https://github.com/kiyoto/fluent-plugin-grok-parser).

If this article is incorrect or outdated, or omits critical information, please [let us know](https://github.com/fluent/fluentd-docs-gitbook/issues?state=open). [Fluentd](http://www.fluentd.org/) is a open source project under [Cloud Native Computing Foundation \(CNCF\)](https://cncf.io/). All components are available under the Apache 2 License.


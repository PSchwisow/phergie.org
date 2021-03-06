---
layout: default
title: Users
generator: pagination
pagination:
    max_per_page: 3
use:
    - posts
---
## Installation

The recommended method of installation is [through composer](http://getcomposer.org). On your local filesystem, create a new directory. Within that directory, create a `composer.json` file containing the following:

```JSON
{
    "require": {
        "phergie/phergie-irc-bot-react": "~1"
    }
}
```

From this created directory, [install composer](https://getcomposer.org/download/) and run `php composer.phar install`. This will install Phergie and all needed core dependencies.

## Configuration

A Phergie installation needs a minimum of two things to be particularly useful: connections to servers and plugins to interact with those servers. Both of these are specified using a Phergie configuration file, which is simply a PHP file that returns an associative array. Here's [an example configuration file](https://github.com/phergie/phergie-irc-bot-react/blob/master/config.sample.php). Let's examine its parts.

### Connections

Connecting to an IRC server requires at minimum the hostname of the server and a nickname, username, and real name for the bot on that server. Connections are represented by instances of classes that implement [`ConnectionInterface`](https://github.com/phergie/phergie-irc-connection/blob/master/src/ConnectionInterface.php) such as [`Connection`](https://github.com/phergie/phergie-irc-connection/blob/master/src/Connection.php), which handily supports configuration via its constructor.

Within the array returned by the Phergie configuration file, there must be a `'connections'` key that references an array of one or more connection instances.

```php
use Phergie\Irc\Connection;
return array(
  'connections' => array(
    new Connection(array(
      'serverHostname' => 'irc.freenode.net',
      'username' => 'Elazar',
      'realname' => 'Matthew Turland',
      'nickname' => 'Phergie3'
    )),
    new Connection(array(
      'serverHostname' => 'irc.efnet.org',
      'username' => 'MTurland',
      'realname' => 'Matthew Turland',
      'nickname' => 'Matt'
    ))
  )
);
```

### Plugins

Plugins are PHP classes used to receive and/or send events between the bot and the servers it connects to. To use a plugin, it must be installed and the bot must be configured to use it. As with the bot itself, the recommended method of installing plugins is through composer.

One commonly used plugin is the [Pong plugin](https://github.com/phergie/phergie-irc-plugin-react-pong), which responds to server ping events used to ensure user connections are active. Let's walk through adding a plugin to a Phergie installation using the Pong plugin as an example.

To install the plugin, open the `composer.json` file created to install the bot and add a line to the `"require"` section so that the file looks like the example below, then run 
`php composer.phar update` from the created directory.

```JSON
{
    "require": {
        "phergie/phergie-irc-bot-react": "~1",
        "phergie/phergie-irc-plugin-react-pong": "~1"
    }
}
```

Now that the plugin is installed, it needs to be added to the Phergie configuration file. Within the array returned by that file, in addition to the `'connections'` key added in the last section, there can also be a `'plugins'` key that references an array of one or more plugin instances.

```php
return array(
  // ...
  'plugins' => array(
    new \Phergie\Irc\Plugin\React\Pong\Plugin
  )
);
```

Like the [`Connection`](https://github.com/phergie/phergie-irc-bot-react/blob/master/src/Connection.php) class, plugins typically accept configuration via constructors. However, the Pong plugin in particular needs no configuration, so it's simply instantiated within the plugins array.

For more plugins to install, check out the [[Plugins|Plugins]] wiki page.

## Usage

Now that connection information has been added to the configuration file, a plugin has been installed, and that plugin has been enabled in the configuration file, the bot is ready to be run. To do this, from the created directory, execute the bot runner with the path to the configuration file.

```
./vendor/bin/phergie path/to/config.php
```

If a configuration file path is not specified, the bot runner will check for a file called `config.php` in the current working directory by default.

## Logging

By default, Phergie uses [Monolog](http://github.com/Seldaek/monolog) for logging. More specifically, it uses a Monolog logger configured with a `DEBUG` log level that sends messages to `stderr`. This is the cause of the console output that results from running the bot, which emits all its IRC interactions as `DEBUG`-level messages.

Phergie supports use of any logging library that implements the [PSR-3 standard](http://www.php-fig.org/psr/psr-3/). To use a custom logger, within the array returned by the configuration file, add a `'logger'` key that references the logger instance to use it.

```php
use My\Logger;
return array(
  // ...
  'logger' => new My\Logger
);
```

## Troubleshooting

If you encounter an issue, check out the [#phergie channel](irc://irc.freenode.net/phergie) on the Freenode IRC network or [file an issue on Github](https://github.com/phergie/phergie-irc-bot-react/issues).
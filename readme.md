# RemoteRotator

[![Go Report Card](https://goreportcard.com/badge/github.com/dh1tw/remoteRotator)](https://goreportcard.com/report/github.com/dh1tw/remoteRotator)
[![Build Status](https://travis-ci.org/dh1tw/remoteRotator.svg?branch=master)](https://travis-ci.org/dh1tw/remoteRotator)
[![MIT licensed](https://img.shields.io/badge/license-MIT-blue.svg)](https://img.shields.io/badge/license-MIT-blue.svg) 
[![Coverage Status](https://coveralls.io/repos/github/dh1tw/remoteRotator/badge.svg?branch=master)](https://coveralls.io/github/dh1tw/remoteRotator?branch=master)
[![Downloads](https://img.shields.io/github/downloads/dh1tw/remoteRotator/total.svg?maxAge=1800)](https://github.com/dh1tw/remoteRotator/releases)

remoteRotator is a cross platform application which makes your antenna rotators
available on the network. remoteRotator implements a statemachine which will
query in a defineable interval the heading (azimuth and/or elevation) of the 
rotator. Updates are sent to all connected clients which can also execute 
commands at any time.

remoteRotator is written in the programing language [Go](https://golang.org).

**ADVICE**: This project is **under development**. The parameters and the ICD
are still **not stable** and subject to change until the first major version
has been reached.

## Supported Rotators

- [Yaesu GS232A](http://www.yaesu.com/indexVS.cfm?cmd=DisplayProducts&ProdCatID=104&encProdID=79A89CEC477AA3B819EE02831F3FD5B8)
- [EA4TX ARS (implements Yaesu GS232A)](http://ea4tx.com/en/)
- Dummy rotator

## Supported Transportation Protocols

- TCP
- HTTP
- Websockets

## License

remoteRotator is published under the permissive [MIT license](https://github.com/dh1tw/remoteRotator/blob/master/LICENSE).

## Download

You can download a tarball / zip archive with the compiled binary for MacOS
(AMD64), Linux (386/AMD64/ARM) and Windows (386/AMD64) from the
[releases](https://github.com/dh1tw/remoteRotator/releases) page. remoteRotator
is just a single executable.

## Dependencies

remoteRotator does not have any external dependencies.

## Getting started

Identify the serial port to which your rotator is connected. On Windows
this will be something like COMx (e.g. COM3), on Linux / MacOS it will be
a device in the `/dev/` folder (e.g. /dev/ttyACM0).

All parameters can be set either in a config file (see below) or through pflags.
To get a list of supported flags for the tcp server, execute:

```bash
$ remoteRotator server tcp --help
```

```
expose a rotator to the network

Usage:
  remoteRotator server tcp [flags]

Flags:
      --azimuth-max int        metadata: maximum azimuth (in deg) (default 450)
      --azimuth-min int        metadata: minimum azimuth (in deg)
      --azimuth-stop int       metadata: mechanical azimuth stop (in deg)
  -b, --baudrate int           baudrate (default 9600)
      --discovery-enabled      make rotator discoverable on the network (default true)
      --elevation-max int      metadata: maximum elevation (in deg) (default 180)
      --elevation-min int      metadata: minimum elevation (in deg)
      --has-azimuth            Indicate if the rotator supports Azimuth (default true)
      --has-elevation          Indicate if the rotator supports Elevation
  -h, --help                   help for tcp
      --http-enabled           enable HTTP Server (default true)
  -w, --http-host string       Host (use '0.0.0.0' to listen on all network adapters) (default "127.0.0.1")
  -k, --http-port int          Port for the HTTP access to the rotator (default 7070)
  -n, --name string            Name tag for the rotator (default "myRotator")
      --pollingrate duration   rotator polling rate (default 1s)
  -P, --portname string        portname / path to the rotator (e.g. COM1) (default "/dev/ttyACM0")
      --tcp-enabled            enable TCP Server (default true)
  -u, --tcp-host string        Host (use '0.0.0.0' to listen on all network adapters) (default "127.0.0.1")
  -p, --tcp-port int           TCP Port (default 7373)
  -t, --type string            Rotator type (supported: yaesu, dummy (default "yaesu")

Global Flags:
      --config string   config file (default is $HOME/.remoteRotator.yaml)
```

So in order to launch remoteRotator on Windows with a Yaesu rotator connected at
COM3 and having the server listening on the network port 5050, we would call:

```bash
$ remoteRotator server tcp -u "0.0.0.0" -p 5050 -P "COM3"
```

```
no config file found
Listening on 0.0.0.0:5050 for TCP connections
Listening on 0.0.0.0:7070 for HTTP connections

```

## Web Interface


![Alt text](https://i.imgur.com/X8zJwQO.png "remoteRotator WebUI")

Appart of connecting to the rotator through a TCP socket, you can also
access the rotator through a web Interface. You can specify the host and port
in the settings above, or deactivate the built-in webserver if you don't need it.

The red arrow indicates the heading of the rotator and the yellow arrow
indicates the preset value to which the rotator will turn to.

If you would like to access remoteRotator via Websockets, then go to the
url `http://<http-host>:<http-port>/ws`.

**Note:** Currently only horizontal (azimuth) rotators are supported.

## Config file

The repository contains an example configuration file. By convention it is called
`.remoteRotator.[yaml|toml|json]` and is located by default either in the
home directory or the directory where the remoteRotator executable is located.
The format of the file can either be in
[yaml](https://en.wikipedia.org/wiki/YAML),
[toml](https://github.com/toml-lang/toml), or
[json](https://en.wikipedia.org/wiki/JSON).

The first line after starting remoteRotator will indicate if / which config
file has been found.

Priority:

1. Pflags (e.g. -p 4040 -t dummy)
2. Values from config file
3. Default values

## Behaviour on Errors

If an error occures from which remoteRotator can not recover, the application
exits. This typically happens when the connection with the rotator has been
lost or if the rotator is not responding anymore.
It is recommended to execute remoteRotator as a service under the supervision
of a scheduler like [systemd](https://en.wikipedia.org/wiki/Systemd).

## Bug reports, Questions & Pull Requests

Please use the Github [Issue tracker](https://github.com/dh1tw/remoteRotator/issues)
to report bugs and ask questions! If you would like to contribute to remoteRotator,
[pull requests](https://help.github.com/articles/creating-a-pull-request/) are
welcome! However please consider to provide unit tests with the PR to verify
the proper behaviour.

If you file a bug report, please include always the version of remoteRotator
you are running:

``` bash
$ remoteRotator version
```

```
copyright Tobias Wellnitz, DH1TW, 2017
remoteRotator Version: 0.1.0, darwin/amd64, BuildDate: 2017-09-04T00:58:00+02:00, Commit: 338ff13
```

## Documentation

The auto generated documentation can be found at
[godoc.org](https://godoc.org/github.com/dh1tw/remoteRotator).

## How to build

In order to compile remoteRotator from the sources, you need to have
[Go](https://golang.org) installed and configured.

This his how to checkout and compile remoteRotator under Linux/MacOS:

```bash
$ go get -d github.com/dh1tw/remoteRotator
$ cd $GOPATH/src/github.com/remoteRotator
$ make install-deps
$ make generate
```

## How to execute the tests

All critial packages have their own set of unit tests. The tests can be
executed with the following commands:

```bash
$ cd $GOPATH/src/github.com/remoteRotator
$ go test -v -race ./...

```

The datarace detector might not be available on all platforms / operating
systems.
# tinyproxy [![Build Status](https://travis-ci.org/hfm/puppet-tinyproxy.svg?branch=master)](https://travis-ci.org/hfm/puppet-tinyproxy) [![Puppet Forge](https://img.shields.io/puppetforge/v/hfm/tinyproxy.svg?style=flat-square)](https://forge.puppet.com/hfm/tinyproxy)

#### Table of Contents

1. [Description](#description)
1. [Setup - The basics of getting started with tinyproxy](#setup)
    - [Setup requirements](#setup-requirements)
    - [Beginning with tinyproxy](#beginning-with-tinyproxy)
1. [Usage - Configuration options and additional functionality](#usage)
    - [Configuring tinyproxy](#configuring-tinyproxy)
    - [Configuring modules from Hiera](#configuring-modules-from-hiera)
1. [Reference - An under-the-hood peek at what the module is doing and how](#reference)
    - [Public Classes](#public-classes)
    - [Private Classes](#private-classes)
    - [Parameters](#parameters)
1. [Limitations - OS compatibility, etc.](#limitations)
1. [Development - Guide for contributing to the module](#development)
    - [Running tests](#running-tests)
    - [Testing quickstart](#testing-quickstart)

## Description

The tinyproxy module handles installing, configuring, and running [tinyproxy](http://tinyproxy.github.io).

## Setup

### Setup Requirements

The tinyproxy module requires the following puppet modules:

- [puppetlabs-stdlib](https://forge.puppet.com/puppetlabs/stdlib) (>= 4.0.0 < 5.0.0)
- [puppetlabs-apt](https://forge.puppet.com/puppetlabs/apt) (>= 2.0.0 < 3.0.0) (only Debian-based distributions)
- [stahnma-epel](https://forge.puppet.com/stahnma/epel) (>= 1.0.0 < 2.0.0) (only RedHat-based distributions)

Both puppetlabs-apt and stahnma-epel are soft dependencies. If you are installing on Debian or RedHat-based systems, you will need to configure appropriate versions of those modules.

### Beginning with tinyproxy

To install the tinyproxy with default parameters, declare the `tinyproxy` class.

```puppet
include ::tinyproxy
```

## Usage

### Configuring tinyproxy

```puppet
class { '::tinyproxy':
  port          => 8080,
  listen        => '0.0.0.0',
  allow         => '10.0.0.0/8',
  connect_ports => [],
}
```

### Configuring modules from Hiera

Listen 0.0.0.0:8080 and allow access from 10.0.0.0/8.

```yaml
tinyproxy::port: 8080
tinyproxy::listen: '0.0.0.0'
tinyproxy::allow: '10.0.0.0/8'
```

Configure connections.

```yaml
tinyproxy::max_clients: 200
tinyproxy::min_spare_servers: 20
tinyproxy::max_spare_servers: 20
tinyproxy::start_servers: 20
```

No ConnectPort means that all ports are allowed.

```yaml
tinyproxy::connect_ports: null
```

Add some headers.

```yaml
tinyproxy::add_headers:
  'X-My-Header': 'Powored by Tinyproxy'
  'Y-My-Header': 'Powored by Tinyproxy'
```

Turn off the normal proxy (only reverse proxy).

```yaml
tinyproxy::reverse_only: true
tinyproxy::reverse_paths:
  '/google/': 'http://www.google.com/'
  '/wired/': 'http://www.wired.com/'
```

## Reference

### Public Classes

- [`tinyproxy`](#tinyproxy): Installs, configures, and runs tinyproxy.

### Private Classes

- `tinyproxy::install`: Installs the tinyproxy package.
- `tinyproxy::config`: Configures tinyproxy.conf.
- `tinyproxy::service`: Manages service.

### Parameters

#### Class: `tinyproxy`

- `use_epel`: Whether epel repository should be installed. Type is Boolean. Default: true.
- `package_ensure`: What state the tinyproxy package should be in. Type is String. Default: 'installed'.
- `config_ensure`: Whether the tinyproxy.conf should exist. Type is Enum['file', 'absent']. Default: 'file'.
- `config_path`: The path to the file to manage. Type is String. Default: '/etc/tinyproxy/tinyproxy.conf' (RedHat) or '/etc/tinyproxy.conf' (Debian).
- `user`: The user to whom the file should belong. Type is String. Default: 'tinyproxy' or 'nogroup' (Debian).
- `gruop`: Which group should own the file.  Type is String. Default: 'tinyproxy' (RedHat) or 'nogroup' (Debian).
- `port`:Specify the port which tinyproxy will listen on. Type is Integer. Default: 8888.
- `listen`: Allow you to bind to an interface. Type is Optional[String]. Default: undef.
- `bind`: Specify which interface will be used for outgoing connections. Type is Optional[String]. Default: undef.
- `bind_same`: Whether tinyproxy will bind the outgoing connection to the ip address of the incoming connection. Type is Optional[Boolean]. Default: undef.
- `timeout`: Specify a timeout of a connection. Type is Integer. Default: 600.
- `error_files`: Specify the HTML file to send when a given HTTP error occurs. Type is Optional[Hash[Integer, String]]. Default: undef.
- `default_error_file`: Specify the default Error HTML file. Type is String. Default: '/usr/share/tinyproxy/default.html'.
- `stat_host`: Specify the stat host. Type is Optional[String]. Default: undef.
- `stat_file`: Specify the HTML file for the stat host. Type is String. Default: '/usr/share/tinyproxy/stats.html'.
- `log_file`: Specify the log file. Type is Optional[String]. Default: '/var/log/tinyproxy/tinyproxy.log'.
- `use_syslog`: Whether tinyproxy uses syslog instead of a log file. This parameter and `$log_file` are mutually exclusive. Type is Boolean. Default: false.
- `pid_file`: Specify the pid file. Type is String. Default: '/var/run/tinyproxy/tinyproxy.pid'.
- `use_xtinyproxy`: Whether tinyproxy include the 'X-Tinyproxy' header. Type is Boolean. Default: false.
- `default_upstreams`: Specify a set of rules for deciding whether the default upstream proxie servers are to be used. Type is Optional[Array[String]]. Default: undef.
- `upstreams`: Specify a set of rules for deciding whether upstream proxie servers are to be used. Type is Optional[Hash[String, String]]. Default: undef.
- `no_upstreams`: Specify no upstream proxy for internal websites and unqualified hosts. Type is Optional[Array[String]]. Default: undef.
- `max_clients`: Specify maximum number of clients can be connected at the same time. Type is Integer. Default: 100.
- `min_spare_servers`: Specify the lower limit for the number of spare servers which should be available. Type is Integer. Default: 5.
- `max_spare_servers`: Specify the upper limit for the number of spare servers which should be available. Type is Integer. Default: 20.
- `start_servers`: Specify the number of servers to start initially. Type is Integer. Default: 10.
- `max_requests_per_child`: Specify the number of connections a thread will handle before it's killed. Type is Integer. Default: 0.
- `allow`: Specify authorization control which clients are allowed to access Tinyproxy. Type is Optional[String]. Default: '127.0.0.1'.
- `deny`: Specify authorization control which clients are denied to access Tinyproxy. Type is Optional[String]. Default: undef.
- `add_headers`: Specify additional headers. Type is Optional[Hash[String, String]]. Default: undef.
- `via_proxy_name`: Specify the "Via" header. Type is String. Default: 'tinyproxy'.
- `disable_via_header`: Whether tinyproxy disables the "Via" header. Type is Boolean. Default: false.
- `filter`: Specify the location of the filter file. Type is Optional[String]. Default: false.
- `filter_urls`: Whether filter is based on urls rather than domains. Type is Optional[Boolean]. Default: undef.
- `filter_extended`: Whether filter uses POSIX Extended regexp. Type is Optional[Boolean]. Default: undef.
- `filter_case_sensitive`: Whether filter uses case sensitive regexp. Type is Optional[Boolean]. Default: undef.
- `filter_default_deny`: Whether filter changes the default policy of the filtering system. Type is Optional[Boolean]. Default: undef.
- `log_level`: Set the logging level. Type is Enum['Critical', 'Error', 'Warning', 'Notice', 'Connect', 'Info']. Default: 'Info'.
- `anonymous`: Specify anonymous keywords. Type is Optional[Array[String]]. Default: undef.
- `connect_ports`: Specify a list of ports allowed by tinyproxy when the CONNECT method is used. Type is Optional[Array[Integer]]. Default: [443, 563].
- `reverse_paths`: Specify a mapping from the local path to remote URL. Type is Optional[Hash[String, String]]. Default: undef.
- `reverse_only`: Whether the normal(forward) proxy is turned off. Type is Optional[Boolean]. Default: undef.
- `reverse_magic`: Whether tinyproxy uses a cookie to track reverse proxy mappings. Type is Optional[Boolean]. Default: undef.
- `reverse_baseurl`: Specify URL that's used to access this reverse proxy. Type is Optional[String]. Default: undef.
- `service_ensure`: Whether a service should be running. Type is Enum['running', 'stopped']. Default: 'running'.
- `service_enable`: Whether a service should be enabled to start at boot. Type is Boolean. Default: true.

## Limitations

This module has been tested on:

- RedHat Enterprise Linux 7
- CentOS 7
- Scientific Linux 7
- Debian 8
- Ubuntu 16.04

## Development

### Running tests

The tinyproxy module contains tests for both [rspec-puppet](http://rspec-puppet.com/) (unit tests) and [beaker-rspec](https://github.com/puppetlabs/beaker-rspec) (acceptance tests) to verify functionality. For detailed information on using these tools, please see their respective documentation.

#### Testing quickstart

- Unit tests:

```console
$ bundle install
$ bundle exec rake test
```

- Acceptance tests using docker:

```console
# List available beaker nodesets
$ bundle exec rake beaker_nodes
centos7
jessie
xenial

# Run beaker acceptance tests
$ BEAKER_set=centos7 bundle exec rake beaker
```

# cf-object-storage

[![Go Report Card](https://goreportcard.com/badge/github.com/ibmjstart/cf-object-storage)](https://goreportcard.com/report/github.com/ibmjstart/cf-object-storage)
[![standard-readme compliant](https://img.shields.io/badge/standard--readme-OK-green.svg)](https://github.com/RichardLitt/standard-readme)

> A CloudFoundry Plugin for interacting with OpenStack Object Storage

## Table of Contents

- [Background](#background)
- [Install](#install)
- [Usage](#usage)
- [Contribute](#contribute)
- [License](#license)

## Background

Static Large Objects (SLOs) and Dynamic Large Objects (DLOs) are incredibly useful aggregate file types available
in OpenStack Object Storage. However, manipulating them can be quite difficult. This Cloud Foundry CLI plugin is
designed to make using SLOs and DLOs much more accessible. 

This plugin makes heavy use of the [swiftlygo](https://github.com/ibmjstart/swiftlygo) library. Much more information 
on SLOs and DLOs can be found by reading that library's README.

Additionally, some basic object and container interactions are included as commands. This allows for working with
Object Storage from the command line without having to go through the long authentication process on your own.

## Install

**Dependenies:** This plugin requires the Cloud Foundry CLI version 6.21.0 or later. You can check your version with
`cf version`. Install and update instructions can be found [here](https://github.com/cloudfoundry/cli).

Since this plugin is not currently in an offical Cloud Foundry plugin repo, it will need to be downloaded and installed
manually. 

### Install From Binary (Recommended)

Download the binary for your machine from the [releases page](https://github.com/ibmjstart/cf-object-storage/releases)
and navigate to the downloaded binary from within your terminal. Then run the following `cf` command from the directory
the binary was downloaded to.

**Note:** If you are reinstalling, run `cf uninstall-plugin cf-object-storage` first to uninstall the outdated
version.

#### Mac & Linux
```
cf install-plugin cf-object-storage
```

If you get a permission error, ensure that the binary has execute permissions.
```
chmod +x cf-object-storage
```

#### Windows
```
cf install-plugin cf-object-storage.exe
```

### Install From Source

Installing this way requires Go. Binaries and install instructions can be found on their [official website](https://golang.org/).
To download this package, open your terminal and run
```
go get github.com/ibmjstart/cf-object-storage
```

#### Mac & Linux
Navigate to the project's root directory, which should be `$GOPATH/src/github.com/ibmjstart/cf-object-storage` with
most standard Go setups. The provided `reinstall.sh` script can then be ran to install the plugin.
```
cd $GOPATH/src/github.com/ibmjstart/cf-object-storage
./scripts/reinstall.sh
```

**Note:** `reinstall.sh` first attempts to uninstall the plugin, so you will get a failure message from the uninstall
command if the plugin is not already installed. However, as long as the following install succeeds all should work fine.

#### Windows
`reinstall.sh` is intended for use on Mac and Linux. To install on Windows, first navigate to the project's root 
directory, which should be `%GOPATH%\src\github.com\ibmjstart\cf-object-storage` with most standard Go setups. Then
build and install as shown.
```
cd %GOPATH%\src\github.com\ibmjstart\cf-object-storage
go build
cf install-plugin cf-object-storage.exe
```

## Usage

This plugin is invoked as follows:
`cf os SUBCOMMAND [ARGS...]`

Sixteen subcommands are included in this plugin, described below. More information can be found by using `cf os help` 
followed by any of the subcommands.

#### Subcommand List

Subcommand		|Usage															|Description
---		|---															|---
`auth` | `cf os auth service_name [-url] [-x]`										|Retrieve and store<sup>!</sup> a service's x-auth info
`containers` | `cf os containers service_name` | Show all containers in an Object Storage instance
`container` | `cf os container service_name container_name` | Show a given container's information
`create-container` | `cf os create-container service_name container_name [headers...] [-gr] [-rm-gr]` | Create a new container in an Object Storage instance
`update-container` | `cf os update-container service_name container_name headers... [-gr] [-rm-gr]` | Update an existing container's metadata
`rename-container` | `cf os rename-container service_name container_name new_container_name` | Rename an existing container<sup>!!</sup>
`delete-container` | `cf os delete-container service_name container_name [-f]` | Remove a container from an Object Storage instance
`objects` | `cf os objects service_name container_name` | Show all objects in a container
`object` | `cf os object service_name container_name object_name` | Show a given object's information
`put-object`    | `cf os put-object service_name container_name path_to_source [-n object_name]` | Upload a file to Object Storage
`get-object` | `cf os get-object service_name container_name object_name path_to_download` | Download an object from Object Storage
`rename-object` | `cf os rename-object service_name container_name object_name new_object_name` | Rename an object
`copy-object` | `cf os copy-object service_name container_name object_name new_container_name` | Copy an object from one container to another
`delete-object` | `cf os delete-object service_name container_name object_name [-l]` | Remove an object from a container
`create-dynamic-object`	| `cf os create-dynamic-object service_name dlo_container dlo_name [-c object_container] [-p dlo_prefix]`				|Create a DLO manifest in Object Storage
`put-large-object`	| `cf os put-large-object service_name slo_container slo_name source_file [-m] [-o output_file] [-s chunk_size] [-t num_threads]`	|Upload a file to Object Storage as an SLO

**<sup>!</sup>** `auth` checks if `HOME/.cf/os_creds.json` exists and contains the target service's x-auth token and 
storage url. If it does, these credentials are used to authenticate with Object Storage (which saves a few http requests).
Upon successful authentication, `auth` will save a service's x-auth info to the above location to speed up subsequent
commands.

**<sup>!!</sup>** `rename-container` should not be used (and will likely fail) on containers containing SLOs and DLOs. This is due to their strict naming conventions that expect certain containers to have certain names.

## Contribute

PRs accepted.

Small note: If editing the README, please conform to the [standard-readme](https://github.com/RichardLitt/standard-readme) specification.

## License
Apache 2.0
 © IBM jStart

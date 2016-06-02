# Vagrant PostGIS on Windows

[Vagrant](https://www.vagrantup.com/) configuration to provide users with
VM for hassle-free fun with [PostGIS](http://postgis.net)

Looking for Linux VM with PostGIS environment? Check https://github.com/mloskot/vagrant-postgis

## Features

* Microsoft Hyper-V Server 2012 R2
* PostgreSQL 9.5 (official binaries)
* PostgGIS 2.2.8 (official binaries)
* Pre-configured with
  * Port forwarding from host `6543` to guest `5432`.
  * PostgreSQL server with admin (`postgres`) and non-admin user (`vagrant`), password for both users is `vagrant`.
  * Sample PostgreSQL database `vagrant`  created, with PostGIS enabled.

## Requirements

* [VirtualBox](https://www.virtualbox.org/) installed.
* [Vagrant](https://www.vagrantup.com/downloads.html) installed.

## Installation

* `git clone` this repository or [download ZIP](https://github.com/mloskot/vagrant-postgis-windows/archive/master.zip).
* `cd vagrant-postgis-windows`
* Follow the [Usage](#usage) section.

## Usage
  
* `vagrant up` to create and boot the guest virtual machine.
First time run, this may take quite a while as the base box image is downloaded
and provisioned, packages installed.

* `vagrant halt` to shutdown the guest machine.

* `vagrant destroy` to wipe out the guest machine completely.
You can re-create it and start over with `vagrant up`.

* `psql -h localhost -p 6543 -d vagrant -U vagrant` to connect to database from host. Similarly, you can connect using [pgAdmin3](http://www.postgresql.org/ftp/pgadmin3/). 

## Credits

* Marc Abramowitz for his [Microsoft Hyper-V Server 2012 R2](https://github.com/msabramo/vagrant_hyperv_server_free) Vagrant image.

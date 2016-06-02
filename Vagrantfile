# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrant VM for PostGIS users

$script = <<'SCRIPT'
# Install PostgreSQL
$pgsqlPrefix = (Join-Path -Path $env:ProgramFiles -ChildPath 'PostgreSQL\9.5')
$pgsqlBin = (Join-Path -Path $pgsqlPrefix -ChildPath 'bin')
$pgsqlDatadir = (Join-Path -Path $pgsqlPrefix -ChildPath 'data')
$pgsqlConf = (Join-Path -Path $pgsqlDatadir -ChildPath 'postgresql.conf')
$pgsqlHba = (Join-Path -Path $pgsqlDatadir -ChildPath 'pg_hba.conf')
$psql = (Join-Path -Path $pgsqlBin -ChildPath 'psql.exe')
$createdb = (Join-Path -Path $pgsqlBin -ChildPath 'createdb.exe')
$dropdb = (Join-Path -Path $pgsqlBin -ChildPath 'dropdb.exe')
echo "Downloading PostgreSQL 9.5"
$pgsqlInstallExe = (Join-Path -Path $env:UserProfile -ChildPath 'postgresql-9.5.3-1-windows-x64.exe')
(New-Object Net.WebClient).DownloadFile('http://get.enterprisedb.com/postgresql/postgresql-9.5.3-1-windows-x64.exe', $pgsqlInstallExe)
echo "Downloading PostGIS 2.2"
$postgisInstallExe = (Join-Path -Path $env:UserProfile -ChildPath 'postgis-bundle-pg95x64-setup-2.2.2-2.exe')
(New-Object Net.WebClient).DownloadFile('http://download.osgeo.org/postgis/windows/pg95/postgis-bundle-pg95x64-setup-2.2.2-2.exe', $postgisInstallExe)
echo "Installing PostgreSQL 9.5"
cmd /c start /wait $pgsqlInstallExe --mode unattended --prefix $pgsqlPrefix --servicename PostgreSQL --superpassword vagrant
echo "Installing PostGIS 2.2"
cmd /c start /wait $postgisInstallExe /S /USERNAME=postgres /PASSWORD=vagrant /PORT=5432
# Post-installation
echo "PostgreSQL: Adding $pgsqlBin to PATH"
$reg = "Registry::HKLM\System\CurrentControlSet\Control\Session Manager\Environment"
$OldPath = (Get-ItemProperty -Path "$reg" -Name PATH).Path + ';' + $pgsqlBin
Set-ItemProperty -Path "$Reg" -Name PATH -Value $NewPath
echo "PostgreSQL: updating $pgsqlConf"
(Get-Content $pgsqlConf) | ForEach-Object {$_ -replace "#listen_address.*", "listen_addresses '*'"} | Set-Content $pgsqlConf
echo "PostgreSQL: updating $pgsqlHba"
Add-Content $pgsqlHba "`n# Accept all IPv4 connections - NOT FOR PRODUCTION`nhost    all         all         0.0.0.0/0             md5"
echo "Enabled acccepting all IPv4 connections"
echo "PostgreSQL: creating user vagrant/vagrant"
Start-Process $psql -Wait -ArgumentList '-c "CREATE ROLE vagrant WITH LOGIN SUPERUSER CREATEDB ENCRYPTED PASSWORD ''vagrant''"','postgresql://postgres:vagrant@localhost/postgres'
echo "PostgreSQL: creating vagrant database"
Start-Process $psql -Wait -ArgumentList '-c "CREATE DATABASE vagrant WITH OWNER vagrant"','postgresql://postgres:vagrant@localhost/postgres'
echo "PostgreSQL: installing PostGIS extension in vagrant"
Start-Process $psql -Wait -ArgumentList '-c "CREATE EXTENSION postgis;"','postgresql://postgres:vagrant@localhost/vagrant'
echo "PostgreSQL: restarting"
Restart-Service PostgreSQL
echo "PostgreSQL: DONE"
echo "PostgreSQL: to connect to the database server from your host,"
echo "            use the host IP and port 6543"
echo "Disabling Windows Firewall"
netsh advfirewall set allprofiles state off
echo "Guest IP address:"
netsh int ip show addresses
SCRIPT

Vagrant.configure(2) do |config|
  config.vm.box = "msabramo/HyperVServer2012"
  config.vm.provider "virtualbox" do |vb|
    vb.gui = true
    vb.memory = "4096"
    vb.cpus = 4
  end
  config.vm.network "private_network", type: "dhcp"
  config.vm.network "forwarded_port",  host: 6543, guest: 5432, auto_correct: true
  config.vm.provision "shell", inline: $script
end

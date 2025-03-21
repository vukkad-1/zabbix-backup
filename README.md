# Backup script for Zabbix configuration data (MySQL/PostgreSQL)

This is a MySQL/PostgreSQL database backup script for the [Zabbix](http://www.zabbix.com/) monitoring software from version 1.3.1 up to 6.2.

## Download

Download the latest (stable) release here:

https://github.com/npotorino/zabbix-backup/releases/latest

## More informations

Please see the [Project Wiki](https://github.com/npotorino/zabbix-backup/wiki).

## Examples

### Backup

Example: backup Zabbix 5.0.1 with PostgreSQL and TimeScaleDB

```
git clone https://github.com/npotorino/zabbix-backup
cd zabbix-backup
./zabbix-dump -t psql -H localhost -P 5432 -o /var/backup
```

### Restore

Example: restore Zabbix 5.0 with PostgreSQL and TimeScaleDB

```
systemctl stop zabbix-server.service
sudo -u postgres dropdb zabbix
sudo -u postgres createdb -O zabbix zabbix

echo "CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;" | sudo -u postgres psql zabbix
echo "SELECT timescaledb_pre_restore();" | sudo -u postgres psql zabbix

gunzip /var/backup/zabbix_cfg_localhost_20200730-1810_db-psql-5.0.1.sql.gz
sudo -u postgres psql zabbix < /var/backup/zabbix_cfg_localhost_20200730-1810_db-psql-5.0.1.sql

echo "SELECT timescaledb_post_restore();" | sudo -u postgres psql zabbix
systemctl restart postgresql-12.service
systemctl start zabbix-server.service
```

## Version history

**0.9.9 (2022-08-31)**
- ENH: zabbix dump add custom format (pg), stdout dump (@gullevek)
- ENH: minimal automated syntax check through Github Actions
- FIX: table schema for PostgreSQL (@jokay)
- FIX: retrieving DBPassword when it contains a "=" (@mathieumd)

**0.9.8 (2022-07-27)**
- ENH: Support for Zabbix 6.2

**0.9.7 (2022-05-10)**
- ENH: Support for Zabbix 6.0

**0.9.6 (2021-12-13)**
- FIX: fix item_rtdata retention (@kernbug)

**0.9.5 (2021-07-23)**
- ENH: Support for Zabbix 5.2 and 5.4
- ENH: Add column names to INSERT INTO .. VALUES and quote names as needed (@diffway)

**0.9.4 (2021-01-21)**
- ENH: Support for Zabbix 5.0
- FIX: Be more specific on detecting mysql socket

**0.9.3 (2020-01-17)**

- ENH: Check for unknown tables
- ENH: Speed up MySQL backup by not calling mysqldump for every single table anymore
- ENH: New option -S to specify PostgreSQL schema
- FIX: Stabilize and enhance PostgreSQL dump
- FIX: Skip IP reverse lookup for localhost, fix multiline dig answers

**0.9.2 (2020-01-16)**

- ENH: Support for Zabbix 4.4
- ENH: Fix (non-critical) shellcheck issues (Mario Trangoni)
- CHG: Fix and enhance helper script get-table-list.pl
- FIX: Escape special characters while reading password (ironbishop)
- FIX: Re-enable accidentally disabled cleanup of postgresql password file
- FIX: Insert hostname into backup file also if database resides on localhost

**0.9.1 (2019-03-21)**

- FIX: Correctly process hostname option -H (Tiago Cruz)

**0.9.0 (2019-03-14)**

- NEW: Support for PostgreSQL databases (Sergey Galkin)
- NEW: Option -P to specify database server port (Sergey Galkin)
- NEW: Support for socket connections to MySQL and PostgreSQL server (Greg Cockburn)
- NEW: Database connection parameters are read from Zabbix servern config by default (Greg Cockburn)
- ENH: Support for Zabbix 4.0 (Wesley Schaft)
- ENH: Options -h and --help to show help (hostname is now specified using -H)
- ENH: Options -Z to skip reading the Zabbix server config file
- CHG: Rename script to "zabbix-dump"
- FIX: Support for whitespaces in database parameters (Greg Cockburn)
- FIX: Support for backslashes in manually entered password (Greg Cockburn)

**0.8.2 (2016-09-08)**

- NEW: Option -x to use XZ instead of GZ for compression (Jonathan Wright)
- NEW: Option -0 for "no compression"
- FIX: Evil space was masking end of here-document (fixed in #8 by @msjmeyer)
- FIX: Prevent "Warning: Using a password on the command line interface can be insecure."

**0.8.1 (2016-07-11)**

- ENH: Added Zabbix 3.0.x tables to list (added & tested by Ruslan Ohitin)

**0.8.0 (2016-01-22)**

- FIX: Only invoke `dig` if available
- ENH: Option -c to use a MySQL config ("options") file (suggested by Daniel Schneller)
- ENH: Option -r to rotate backup files (Daniel Schneller)
- ENH: Add database version to filename if available
- ENH: Add quiet mode. IP reverse lookup optional (Daniel Schneller)
- ENH: Bash related fixes (Misu Moldovan)
- CHG: Default output directory is now $PWD instead of script dir

**0.7.1 (2015-01-27)**

- NEW: Parsing of commandline arguments implemented
- ENH: Try reverse lookup of IPs and include hostname/IP in filename
- REV: Stop if database password is wrong

**0.7.0 (2014-10-02)**

- ENH: Complete overhaul to make script work with lots of Zabbix versions

**0.6.0 (2014-09-15)**

- REV: Updated the table list for use with zabbix v2.2.3

**0.5.0 (2013-05-13)**

- NEW: Added table list comparison between database and script

**0.4.0 (2012-03-02)**

- REV: Incorporated mysqldump options (suggested by Jonathan Bayer)

**0.3.0 (2012-02-06)**

- ENH: Backup of Zabbix 1.9.x / 2.0.0, removed unnecessary use of
  variables (DATEBIN etc) for commands that use to be in $PATH

**0.2.0 (2011-11-05)**

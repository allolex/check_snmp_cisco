check_snmp_cisco
================


Summary
-------

  A Nagios plugin to check the state of Cisco devices via SNMP


Synopsis
--------

  `check_cisco_snmp -H ip -C community -I interface [-S state]`


Description
-----------

  This Nagios plugin will use SNMP to find out information about Cisco
  devices.

  State option are: 
    - up (-S up)
    - down (-S down)
    - dormant (-S dormant)

  State defaults to "up".


Dependencies
------------

  This plugin uses the CPAN modules Net::SNMP and Storable.

  Nagios isn't strictly required by this plugin; it can work as a stand-alone
  script.


What's New
----------

  - Added support for cache storage using Storable.
  - Reduced number of system calls
  - Optimised interface lookup
  - Changed usage and help messages to heredocs
  - Made help argument return help


Examples
--------

  1. To check whether Fa2/0/1 is up on your switch:

    `check_cisco_snmp -H 192.168.0.1 -C public -I Fa2/0/1`

    or

    `check_cisco_snmp -H 192.168.0.1 -C public -I FastEthernet2/0/1`

  2. To check whether FastEthernet0 is down on your router:

    `check_cisco_snmp -H 192.168.0.1 -C MyCommunity -I FastEthernet0 -S down`

  3. To check whether Backup RNIS BRIO:1 is dormant on your router:

    `check_cisco_snmp -H 192.168.0.1 -C MyCommunity -I BRI0:1 -S down`


Original Description by Martin
------------------------------

http://exchange.nagios.org/directory/Plugins/Hardware/Network-Gear/Cisco/check_snmp_cisco/details

SNMP check for cisco devices (routers/switches) returning state of different
interfaces.

The plugin is a enhanced and modified version of `check_cisco.pl` from R3dL!GhT.

19.05.2009 - New version. Corrects a minor bug. In case the cache file was
deleted a new one was not generated automatically. Fixed. Now - in case the
cached indexfile is wrong (for example you have reconfigured the cisco switch)
delete the file. The next time the script runs the cachefile will be
generated.

15.07.2008 - First release of the improved version.

The changes are mainly related on performance. The script was rewritten in
several parts and the code was cleaned up.

On bigger switches the plugin was too slow due to the fact that it did
multiple snmpwalks. On a switch with over 350 ports that caused a timeout in
Nagios.

The problem was solved by implementing a cache mechanism. This was done in the
following manner.

You need a directory for the cache files. It is best to do it with a tmpfs
because a tmpfs is opposite to a ramdisk swapable.

Sample entry from `/etc/fstab`:

  `tmpfs /var/nagios_plugin_cache tmpfs defaults 0 0`

Please change variable `$CachePath` for a different location

Unforunately I use some systemcalls for executing system commands. This is a
little bit dirty but every snmp call in this script was done be a system call
instead of using perl. A complete rewrite was too much work for the time I had
to solve my problems.

The function `snmpwalkgrep`, `snmpwalk` and some lines of code were kicked out
because they were no longer necessary

The script does a find in the cache directory for the cache file. If it is too
old (`-mtime`) or not existent it will be generated. OIDs, interface names and
descriptions are store in the cache That means the double walk will be done
only every 2 days instead of every run


Authors
-------

  - R3dL!GhT (original source)
  - Martin Fuerstenau <mf@maerber.de> (modifications)
  - Damon Davison <damon@allolex.net> (modifications)

# Lizardfs TM drivers for OpenNebula

Based on the original OpenNebula code, changes by Carlo Daffara (NodeWeaver Srl) 2017-2018

For information and requests: info@nodeweaver.eu

To contribute bug patches or new features, you can use the github Pull Request model.

Code and documentation are under the Apache License 2.0 like OpenNebula.

This is the same set of drivers used within our NodeWeaver platform, compatible with Lizardfs 3.x and OpenNebula 5.4.x
Prerequisites:
* lizardfs command executable by the user that launch the OpenNebula probes (usually oneadmin) and in the default path 
* the lizardfs datastore must be mounted and reachable from all the nodes where the TM drivers are in use, and with the same path

The TM drivers are derived from the latest 5.4.3 "shared" TM drivers, with all the copies and other operations modified to use the 
live snapshot feature of LizardFS.

## To install in OpenNebula:
Copy the drivers in /var/lib/one/remotes/tm/lizardfs

```
# fix ownership
# is you have changed the default user and group of OpenNebula, substitute oneadmin.onedadmin with <installationuser>.<installationgroup>
chown -R oneadmin.oneadmin /var/lib/one/remotes/tm/lizardfs
```

in the section
```
TM_MAD = [
    executable = "one_tm",
    arguments = "-t 15 -d dummy,lvm,shared,fs_lvm,qcow2,ssh,vmfs,ceph,dev,lizardfs"
]
```
(or the name you use for the lizardfs repo)

if you want to add Datastore support:
Create a lizardfs directory in the datastore directory, and add the files in the repo
https://github.com/cloudweavers/lizardfs-DM_MAD
In the one.conf file add "lizardfs" in the section
```
DATASTORE_MAD = [
    executable = "one_datastore",
    arguments  = "-t 15 -d dummy,fs,vmfs,lvm,ceph,dev,lizardfs"
]
```

and

```
TM_MAD_CONF = [
    NAME = "lizardfs",
    LN_TARGET = "NONE",
    CLONE_TARGET = "SYSTEM",
    SHARED = "YES",
    DS_MIGRATE = "YES",
    ALLOW_ORPHANS = "NO"
]
DS_MAD_CONF = [
    NAME = "lizardfs",
    REQUIRED_ATTRS = "",
    PERSISTENT_ONLY = "NO",
    MARKETPLACE_ACTIONS = "export"
]

```
(the TM_MAD_CONF and DS_MAD_CONF were graciously highlighted by GitHub user @ioiioi
restart opennebula:
```
service opennebula restart
service opennebula-sunstone restart
```
As oneadmin user sync the remote scripts:
```
su - oneadmin -c 'onehost sync --force'
```

Thanks to 



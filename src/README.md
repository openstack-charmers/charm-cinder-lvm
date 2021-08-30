# Overview

Cinder-lvm is a charm that works as a subordinate for the cinder charm, implementing the LVM backend.

# Usage

## Configuration

This section covers common and/or important configuration options. See file `config.yaml` for the full list of options, along with their descriptions and default values. See the [Juju documentation][juju-docs-config-apps] for details on configuring applications.

#### `alias`

An alias given to the storage pool. This alias will be used to derive the volume-group name and the backend name, and is used
to differentiate volume groups and backend names if multiple instances are deployed for the charm on the same host.
      .
When deploying one instance, this name does not matter and "default" is probably ok. But when deploying multiple instances,
a more meaningful name should be used (e.g: "pool1", "pool2", "fast", "slow", etc).

#### `allocation-type`

Either 'thin' or 'thick'; specifies the model when allocating logical volumes. Defaults to 'thick'.

### `block-device`

The block devices on which to create an LVM volume group. This is a mandatory option and leaving it blank will cause an error.
This can be a space-separated list of block devices to attempt to use in the cinder lvm volume group. Each block device detected
will be added to the available physical volumes in the volume group.

Formatting as a physical volume will fail if the device is already formatted and may potentially have data, unless the 'overwrite'
option is set to true.

May be set to the path and size of a local file (/path/to/file|$sizeG), which will be created and used as a
loopback device (should only be used for testing purposes). $sizeG defaults to 5G.

### `config-flags`

Comma-separated list of key=value config flags. These values will be added to standard options when injecting config into `cinder.conf`.

### `ephemeral-unmount`

Cloud instances provide ephemeral storage which is normally mounted on /mnt.

Providing this option will force an unmount of the ephemeral device so that it can be used as a Cinder storage device. This is useful for
testing purposes (cloud deployment is not a typical use case).

You need to pass the mount point to be unmounted, if blank (or invalid) it will be ignored.

### `overwrite`

If true, the charm will attempt to overwrite block devices containing previous filesystems or LVM, assuming it is not in use.

### `remove-missing`

If true, the charm will attempt to remove missing physical volumes from the volume group, if logical volumes are not allocated on top of them.

### `remove-missing-force`

If true, the charm will attempt to remove missing physical volumes from the volume group, even when logical volumes are allocated on top of them.
This option overrides 'remove-missing' when set.

### `unique-backend`

Normally all backends that this charm configure are going to register themselves with Cinder using the same backend name (the
name of the volume group), allowing Cinder to use them as a cluster and schedule volume creation to any of the nodes that pass
cinder filter selection. Set this to True to make each host register with a unique name (volume-group name + hostname)
so that each host can be individually addressable in cinder backend list.

## Deployment

Because this is a subordinate charm a relation will need to be added to the 'cinder' charm
application to have the charm deployed on a machine.

## Cinder-lvm as a backend for Cinder

This charm's primary use is as a backend for the cinder charm. To do so, add a relation betweeen both charms:

  juju add-relation cinder-lvm cinder

## Actions

This section lists Juju [actions][juju-docs-actions] supported by the charm. Actions allow specific operations to be performed on a per-unit basis. To display action descriptions run `juju actions --schema cinder-lvm`. If the charm is not deployed then see file `actions.yaml`.

* `pause`

Pause the services. If the deployment is clustered using the hacluster charm, the corresponding hacluster unit on the node must first be paused
as well (Not doing so may lead to an interruption of service).

* `resume`

Resume services. If the deployment is clustered using the hacluster charm, the corresponding hacluster unit on the node must be resumed as well.

* `restart-services`

Restart the services the charm manages.

# Documentation

The OpenStack Charms project maintains two documentation guides:

* [OpenStack Charm Guide][cg]: for project information, including development
  and support notes
* [OpenStack Charms Deployment Guide][cdg]: for charm usage information

# Bugs

Please report bugs on [Launchpad][lp-bugs-charm-cinder-lvm].

<!-- LINKS -->

[hacluster-charm]: https://jaas.ai/hacluster
[cg]: https://docs.openstack.org/charm-guide
[cdg]: https://docs.openstack.org/project-deploy-guide/charm-deployment-guide
[cdg-app-policy-overrides]: https://docs.openstack.org/project-deploy-guide/charm-deployment-guide/latest/app-policy-overrides.html
[lp-bugs-charm-cinder-lvm]: https://bugs.launchpad.net/charm-cinder-lvm/+filebug


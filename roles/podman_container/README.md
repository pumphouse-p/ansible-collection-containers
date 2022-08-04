# Ansible Role: `pumphouse_p.containers.podman_container`

## Description

Manages Podman containers.

## Requirements

* `community.containers`
* `podman`: see the [podman_tools](../podman_tools/README.md) role found in this collection.

## Role Variables

### Container Configuration

Podman containers are managed through the `podman_containers` variable. This variable should contain
a list of records, where each list is a map that supports the variables outlined in the table below.

|  Variable Name  |  Default  | Required  |       Type       |                                                                      Description                                                                      |
|:---------------:|:---------:|:---------:|:----------------:|:-----------------------------------------------------------------------------------------------------------------------------------------------------:|
|     `name `     |    ""     |    yes    |       str        |                                                              The name of the container.                                                               |
|     `image`     |    ""     | on create |       str        |                                                         Image to use to create the container.                                                         |
|     `state`     | `created` |    no     |       str        |                        Desired state of the container. Can be one of `started`, `present`, `absent`, `created`, or `stopped`.                         |
|    `volumes`    |    ""     |    no     |    list(str)     |                                   List of volumes to bind mount. Should be specified as `/host_dir:/container_dir`.                                   |
|   `networks`    |    ""     |    no     |    list(str)     |        Sets the network mode for the container. Can be one of `bridge`, `none`, `container`, `network-name`, `ns`, or (default) `slirp4netns`         |
|    `publish`    |    ""     |    no     |    list(str)     | Publish container port on host. Should be specified as `ip:hostport:containerport`, `ip::containerport`, `hostport:containerport`, or `containerport` |
|  `publish_all`  |  `false`  |    no     |       bool       |                                                Publish all exposed ports to random ports on the host.                                                 |
|      `env`      |    ""     |    no     |   map(str:str)   |                                                              Set environment variables.                                                               |
|   `env_host`    |    ""     |    no     |       bool       |                                                     Inherit environment variables from the host.                                                      |
|      `cmd`      |    ""     |    no     | str or list(str) |                                                    Override the command from the container image.                                                     |
|   `cmd_args`    |    ""     |    no     |    list(str)     |                                                   Command options to pass to the `podman` command.                                                    |
|     `debug`     |  `false`  |    no     |       bool       |                                                  Return additional information for troubleshooting.                                                   |
|    `detach`     |  `true`   |    no     |       bool       |                                                            Run container in detached mode.                                                            |
|  `interactive`  |  `false`  |    no     |       bool       |                                                          Run container in interactive mode.                                                           |
|      `dns`      |    ""     |    no     |    list(str)     |                                                        Custom nameservers to use in container.                                                        |
|  `dns_search`   |    ""     |    no     |       str        |                                                              Custom DNS search domains.                                                               |
|  `entrypoint`   |    ""     |    no     |       str        |                                                     Override the default entrypoint of the image.                                                     |
|   `etc_hosts`   |    ""     |    no     |  dict(str:str)   |                                                Dictionary containing host (key) to IP (value) mappings                                                |
| `force_restart` |  `false`  |    no     |       bool       |                                                              Force restart of container.                                                              |
|   `hostname`    |    ""     |    no     |       str        |                                                      Hostname value to set inside the container.                                                      |
|  `privileged`   |  `false`  |    no     |       bool       |                                                   Give extended system privileges to the container.                                                   |
|    `labels`     |    ""     |    no     |   map(str:str)   |                                                              Set metadata to container.                                                               |
|  `annotations`  |    ""     |    no     |   map(str:str)   |                                                             Set annotations on container.                                                             |
|      `rm`       |  `false`  |    no     |       bool       |                                                          Remove the container when it exits.                                                          |
|   `recreate`    |  `false`  |    no     |       bool       |                                                       Recreate container if it already exists.                                                        |
|    `systemd`    |    ""     |    no     |       map        |                                          Configuration (see below) for having container managed by systemd.                                           |

The `systemd` map supports the following variables.

|   Variable Name    |                     Default                     | Required | Type |                                                            Description                                                             |
|:------------------:|:-----------------------------------------------:|:--------:|:----:|:----------------------------------------------------------------------------------------------------------------------------------:|
|       `new`        |                     `false`                     |    no    | bool |                      Create containers and pods when the unit is started instead of expecting them to exist.                       |
|  `restart_policy`  |                  `on-failure`                   |    no    | str  | Restart policy for the unit. Can be one of `no`, `on-success`, `on-failure`, `on-abnormal`, `on-watchdog`, `on-abort`, or `always` |
|      `names`       |                     `true`                      |    no    | bool |                         Use names of the containers for the start, stop, and description of the unit file.                         |
| `container_prefix` |                   `container`                   |    no    | str  |                                                     Systemd unit name prefix.                                                      |
|       `path`       | `/home/{{ ansible_user }}/.config/systemd/user` |    no    | str  |                                      Path to a directory where unit files will be generated.                                       |
|     `enabled`      |                       ""                        |   yes    | bool |                                         Whether container should be started at boot time.                                          |
|      `state`       |                       ""                        |   yes    | str  |                  Set current state of container. Can be one of `started`, `stopped`, `restarted`, or `reloaded`.                   |

# Dependencies

None.

# Example Playbook

```yaml
- name: Ensure podman containers at desired state
  hosts: all
  become: true
  vars:
    podman_containers:
    - name: http
      image: docker.io/nginx:latest
      state: created
      systemd:
        enabled: true
        state: started

  roles:
     - role: pumphouse_p.containers.podman_container
```

# License

GPL

# Author Information

Devin Parrish [GitHub](https://github.com/pumphouse-p)

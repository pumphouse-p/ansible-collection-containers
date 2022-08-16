# Ansible Role: `pumphouse_p.containers.helm_install`

## Description

Installs a package to a Kubernetes cluster through `helm`.

## Requirements

* `kubernetes.core`

## Role Variables

### Repo Configuration

The following variables can be used to configure repository access to where charts reside.

|           Variable Name            | Default | Required | Type |                   Description                    |
|:----------------------------------:|:-------:|:--------:|:----:|:------------------------------------------------:|
|      `helm_install_repo_name`      |   ""    |   yes    | str  |        Name of repo where chart is found.        |
|      `helm_install_repo_url`       |   ""    |   yes    | str  |        URL of repo where chart is found.         |
|    `helm_install_repo_username`    |   ""    |    no    | str  | Chart repository username for basic auth repos.  |
|    `helm_install_repo_password`    |   ""    |    no    | str  | Chart repository password for basic auth repos.  |
| `helm_install_repo_validate_certs` | `false` |    no    | bool | Whether or not to validate API SSL certificates. |

The following variables can be used to specify the details of the chart to install.

|          Variable Name           | Default | Required |   Type    |                           Description                            |
|:--------------------------------:|:-------:|:--------:|:---------:|:----------------------------------------------------------------:|
|       `helm_install_name`        |   ""    |   yes    |    str    |              Name to assign to chart installation.               |
|     `helm_install_chart_ref`     |   ""    |   yes    |    str    |               Chart reference on chart repository.               |
|  `helm_install_chart_repo_url`   |   ""    |    no    |    str    |            Chart repository URL where chart resides.             |
|   `helm_install_chart_version`   |   ""    |    no    |    str    | Chart version to install. Defaults to latest when not specified. |
|   `helm_install_release_name`    |   ""    |    no    |    str    |                     Release name to install.                     |
| `helm_install_release_namespace` |   ""    |   yes    |    str    |    Kubernetes namespace where the chart should be installed.     |
|  `helm_install_release_values`   |   ""    |    no    |    map    |            Values to pass to chart for configuration.            |
| `helm_install_update_repo_cache` | `false` |    no    |   bool    |      Whether to run `helm repo update` before installation.      |
|   `helm_install_values_files`    |   ""    |    no    | list(str) |                  Value files to pass to chart.                   |
|      `helm_install_atomic`       | `false` |    no    |   bool    |      Whether the installation should be deleted on failure.      |
| `helm_install_create_namespace`  | `true`  |    no    |   bool    |     Whether the namespace should be created if not present.      |
|       `helm_install_force`       | `false` |    no    |   bool    |             Whether reinstallation should be forced.             |
|  `helm_install_validate_certs`   | `false` |    no    |   bool    |         Whether or not to validate API SSL certificates.         |
|       `helm_install_wait`        | `false` |    no    |   bool    |           Wait for all objects to reach a ready state.           |

# Dependencies

None.

# Example Playbook

```yaml
- name: Ensure Namespaces are at desired state
  hosts: localhost
  become: false
  gather_facts: false
  vars:
    helm_install_repo_name: bitnami
    helm_install_repo_url: https://charts.bitnami.com/bitnami
    helm_install_name: nginx-server
    helm_install_release_namespace: nginx-server
    helm_install_create_namespace: true
    helm_install_chart_ref: bitnami/nginx

  roles:
     - role: pumphouse_p.containers.helm_install
```

# License

GPL

# Author Information

Devin Parrish [GitHub](https://github.com/pumphouse-p)

# Ansible Role: `pumphouse_p.containers.helm_uninstall`

## Description

Uninstalls a package and removes a repo from a Kubernetes cluster through `helm`.

## Requirements

* `kubernetes.core`

## Role Variables

### Repo Configuration

The following variables can be used to configure repository access to where charts reside.

|        Variable Name         | Default | Required | Type |            Description             |
|:----------------------------:|:-------:|:--------:|:----:|:----------------------------------:|
|  `helm_uninstall_repo_name`  |   ""    |    no    | str  | Name of repo where chart is found. |
| `helm_uninstall_delete_repo` | `false` |    no    | bool |      Remove repo from client.      |

The following variables can be used to specify the details of the chart to uninstall.

|           Variable Name            | Default | Required | Type |                           Description                           |
|:----------------------------------:|:-------:|:--------:|:----:|:---------------------------------------------------------------:|
|       `helm_uninstall_name`        |   ""    |   yes    | str  |              Name of chart installation to remove.              |
| `helm_uninstall_release_namespace` |   ""    |   yes    | str  |          Namespace where chart being removed is found.          |
| `helm_uninstall_delete_namespace`  | `false` |    no    | bool |        Whether Namespace should be removed the cluster.         |
|       `helm_uninstall_wait`        | `false` |    no    | bool | Whether to wait for all objects to be removed from the cluster. |

# Dependencies

None.

# Example Playbook

```yaml
- name: Ensure cleanup of chart, namespace, and repo
  hosts: localhost
  become: false
  gather_facts: false
  vars:
    helm_uninstall_name: nginx-server
    helm_uninstall_release_namespace: nginx-server
    helm_uninstall_delete_namespace: true
    helm_uninstall_repo_name: bitnami
    helm_uninstall_delete_repo: true

  roles:
     - role: pumphouse_p.containers.helm_uninstall
```

# License

GPL

# Author Information

Devin Parrish [GitHub](https://github.com/pumphouse-p)

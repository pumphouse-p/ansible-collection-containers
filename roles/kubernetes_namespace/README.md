# Ansible Role: `pumphouse_p.containers.kubernetes_namespace`

## Description

Manages Kubernetes Namespaces.

## Requirements

* `kubernetes.core`

## Role Variables

### Namespace Configuration

Kubernetes namespaces containers are managed through two variables, `kubernetes_namespaces_present`
and `kubernetes_namespaces_absent`. These variables are lists that support the fields outlined in the
example below.

```yaml
kubernetes_namespaces_present:
  - name: app01
    labels:
      key1: value1
      key2: value2

kubernetes_namespaces_absent:
  - name: app02
```

# Dependencies

None.

# Example Playbook

```yaml
- name: Ensure Namespaces are at desired state
  hosts: localhost
  become: false
  gather_facts: false
  vars:
    kubernetes_namespaces_present:
      - name: app01
        labels:
          key1: value1
          key2: value2

    kubernetes_namespaces_absent:
      - name: app02

  roles:
     - role: pumphouse_p.containers.kubernetes_namespace
```

# License

GPL

# Author Information

Devin Parrish [GitHub](https://github.com/pumphouse-p)

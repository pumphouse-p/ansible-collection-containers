# README for `pumphouse_p.containers.k3s_setup`

## Description

An Ansible Role that installs and configures k3s.

## Requirements

* `ansible.builtin`
* `ansible.posix`

## Role Variables

Available variables are described in the table below.

| Variable Name |        Default        | Required | Type |                   Description                   |
|:-------------:|:---------------------:|:--------:|:----:|:-----------------------------------------------:|
| `systemd_dir` | `/etc/systemd/system` |    no    | str  | Path to where systemd stores it's configuration |


## Dependencies

None.

## Example Playbook

```yaml
---
- name: Reset k3s on systems
  hosts: k3s

  roles:
  - role: pumphouse_p.containers.k3s_reset
```

## License

GPL

## References

Adapted from the content found in the [k3s-ansible](github.com/k3s-io/k3s-ansible) repo.

## Author Information

Devin Parrish ([GitHub](https://github.com/pumphouse-p))
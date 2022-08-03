# Ansible Role: `pumphouse_p.containers.podman_tools`

Installs and configures `podman` tooling.

# Requirements

None.

# Role Variables

Available variables are listed here, along with the default values (see `defaults/main.yml`):

Registries to try, in order of precedence, when using unqualified image names.

```yaml
podman_tools_unqualified_search_registries:
  - "registry.fedoraproject.org"
  - "registry.access.redhat.com"
  - "docker.io"
  - "quay.io"
```

# Dependencies

None.

# Example Playbook

```yaml
- name: Ensure podman is installed and configured
  hosts: all
  become: true

  roles:
     - role: pumphouse_p.containers.podman_tools
```

# License

GPL

# Author Information

Devin Parrish [GitHub](https://github.com/pumphouse-p)

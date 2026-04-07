# Podman Quadlet Role

Ansible role to manage Podman Quadlet files for containers, networks, and volumes. Supports both system-wide and user-scoped quadlets.

## Requirements

- Podman 4.4+ (Quadlet support)
- systemd

## Role Variables

### Scope and Paths

```yaml
# Scope: 'system' or 'user'
podman_quadlet_scope: system

# User to run quadlets under (only applies when scope is 'user')
podman_quadlet_user: "{{ ansible_user_id }}"

# Quadlet directory paths (auto-selected based on scope)
podman_quadlet_system_path: /etc/containers/systemd
podman_quadlet_user_path: "~{{ podman_quadlet_user }}/.config/containers/systemd"
```

### Service Management

```yaml
# State: 'present' or 'absent'
podman_quadlet_state: present

# Whether to reload systemd daemon after changes
podman_quadlet_reload_daemon: true

# Whether to enable and start services
podman_quadlet_enable_services: true
podman_quadlet_start_services: true

# Enable linger for user scope (allows user services to run when user is not logged in)
podman_quadlet_enable_linger: true
```

> **Note:** When `podman_quadlet_scope: user` and `podman_quadlet_enable_linger: true`, the role automatically runs `loginctl enable-linger` for the specified user. This allows user systemd services to run even when the user is not logged in.

### Quadlet Definitions

```yaml
# Networks
podman_quadlet_networks:
  - name: mynetwork
    network_name: custom-network
    subnet: 10.89.0.0/24
    gateway: 10.89.0.1
    state: present

# Volumes
podman_quadlet_volumes:
  - name: myvolume
    volume_name: data-volume
    state: present

# Containers
podman_quadlet_containers:
  - name: nginx
    image: docker.io/nginx:latest
    publish_port:
      - "8080:80"
    volume:
      - "myvolume:/usr/share/nginx/html:Z"
    network: mynetwork
    state: present
```

## Container Options

```yaml
podman_quadlet_containers:
  - name: myapp
    image: docker.io/myapp:latest
    container_name: myapp-container  # Optional: custom container name
    exec: /usr/bin/myapp             # Optional: override entrypoint

    # Environment variables
    environment:
      KEY: value
      ANOTHER_KEY: another_value
    environment_file:               # Optional: load from file(s)
      - /path/to/env/file

    # Networking
    network:
      - mynetwork
      - host
    publish_port:
      - "8080:80"
      - "443:443"

    # Storage
    volume:
      - "myvolume:/data:Z"
      - "/host/path:/container/path:ro"

    # User/Group
    user: "1000"
    group: "1000"

    # Additional options
    pull: always                    # always, missing, never, newer
    auto_update: registry          # registry, local
    run_init: true
    timezone: local
    security_label_disable: false

    # Labels
    labels:
      app: myapp
      env: production

    # Advanced
    podman_args: --log-level=debug

    # Unit section
    unit:
      description: My Application
      after:
        - network-online.target
        - mynetwork-network.service
      requires:
        - myvolume-volume.service

    # Service section
    service:
      restart: always
      timeout_start_sec: 60
      timeout_stop_sec: 30

    # Install section
    install:
      wanted_by: multi-user.target
```

## Network Options

```yaml
podman_quadlet_networks:
  - name: mynetwork
    network_name: custom-network    # Optional: custom network name

    # IP Configuration
    subnet:
      - 10.89.0.0/24
      - fd00:dead:beef::/48        # IPv6 support
    gateway: 10.89.0.1
    ip_range: 10.89.0.0/25
    ipv6: true

    # DNS
    disable_dns: false
    dns:
      - 8.8.8.8
      - 1.1.1.1

    # Network settings
    driver: bridge
    interface_name: podman1
    internal: false
    ipam_driver: host-local

    # Labels and options
    labels:
      environment: production
    options:
      mtu: "9000"

    # Advanced
    podman_args: --ipv6

    # Unit section
    unit:
      description: My Custom Network
      after: network.target
```

## Volume Options

```yaml
podman_quadlet_volumes:
  - name: myvolume
    volume_name: custom-volume      # Optional: custom volume name

    # Permissions
    user: "1000"
    group: "1000"

    # Driver options
    device: /dev/sda1              # For device-based volumes
    type: tmpfs                    # Volume type

    # Labels and options
    labels:
      backup: daily
    options:
      size: 1G                     # For tmpfs

    # Advanced
    podman_args: --opt o=nodev

    # Unit section
    unit:
      description: My Data Volume
```

## Example Playbooks

### System-wide Quadlets

```yaml
---
- name: Deploy system-wide Podman quadlets
  hosts: servers
  become: true

  roles:
    - role: podman_quadlet
      vars:
        podman_quadlet_scope: system
        podman_quadlet_networks:
          - name: app-network
            subnet: 172.20.0.0/16
            gateway: 172.20.0.1

        podman_quadlet_volumes:
          - name: app-data
            labels:
              app: myapp

        podman_quadlet_containers:
          - name: nginx
            image: docker.io/nginx:latest
            network: app-network
            publish_port: "80:80"
            volume: "app-data:/usr/share/nginx/html:Z"
            unit:
              description: Nginx Web Server
              after: app-network-network.service
              requires: app-data-volume.service
```

### User-scoped Quadlets

```yaml
---
- name: Deploy user-scoped Podman quadlets
  hosts: workstations

  roles:
    - role: podman_quadlet
      vars:
        podman_quadlet_scope: user
        podman_quadlet_user: myuser

        podman_quadlet_containers:
          - name: personal-app
            image: docker.io/myapp:latest
            publish_port: "8080:8080"
            environment:
              APP_ENV: development
            install:
              wanted_by: default.target
```

### Mixed Configuration

```yaml
---
- name: Deploy multiple containers with dependencies
  hosts: servers
  become: true

  roles:
    - role: podman_quadlet
      vars:
        podman_quadlet_networks:
          - name: frontend
            subnet: 172.21.0.0/24
          - name: backend
            subnet: 172.22.0.0/24
            internal: true

        podman_quadlet_volumes:
          - name: postgres-data
          - name: redis-data

        podman_quadlet_containers:
          - name: postgres
            image: docker.io/postgres:16
            network: backend
            volume: "postgres-data:/var/lib/postgresql/data:Z"
            environment:
              POSTGRES_PASSWORD: secret
              POSTGRES_DB: myapp

          - name: redis
            image: docker.io/redis:7
            network: backend
            volume: "redis-data:/data:Z"

          - name: webapp
            image: docker.io/mywebapp:latest
            network:
              - frontend
              - backend
            publish_port: "443:443"
            environment_file: /etc/webapp/env
            unit:
              after:
                - postgres.service
                - redis.service
              requires:
                - postgres.service
                - redis.service
```

### Remove Quadlets

```yaml
---
- name: Remove Podman quadlets
  hosts: servers
  become: true

  roles:
    - role: podman_quadlet
      vars:
        podman_quadlet_state: absent
        podman_quadlet_containers:
          - name: old-app
        podman_quadlet_networks:
          - name: old-network
        podman_quadlet_volumes:
          - name: old-volume
```

## Service Names

Systemd service names are generated from the quadlet filename:
- Containers: `{name}.service`
- Networks: `{name}-network.service`
- Volumes: `{name}-volume.service`

Manage services manually:
```bash
# System-wide
systemctl status nginx
systemctl restart nginx

# User-scoped
systemctl --user status personal-app
systemctl --user restart personal-app
```

## User Lingering

For user-scoped quadlets, the role automatically enables lingering (when `podman_quadlet_enable_linger: true`) so that user systemd services can run even when the user is not logged in.

Check linger status:
```bash
# Check if linger is enabled for a user
loginctl show-user username --property=Linger

# List users with linger enabled
ls /var/lib/systemd/linger/
```

Manually manage linger:
```bash
# Enable linger for a user
sudo loginctl enable-linger username

# Disable linger for a user
sudo loginctl disable-linger username
```

Without linger enabled, user systemd services (including Podman containers) will stop when the user logs out.

## License

MIT

## Author

Devin

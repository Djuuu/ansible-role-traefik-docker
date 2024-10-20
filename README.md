Ansible Role: Traefik-docker
============================

Install Traefik Docker Compose project.

- https://traefik.io/traefik/
- https://doc.traefik.io/traefik/

Requirements
------------

Requires the following to be installed:
- docker
- docker compose

Role Variables
--------------

Common Docker projects variables:

```yaml
# Base directory for Docker projects
docker_projects_path: /var/www
```

Available role variables are listed below, along with default values (see `defaults/main.yml`):

```yaml
# Docker Compose project variables

traefik_project_name: traefik

# Optional IP subnets
traefik_compose_ipv4_subnet:
traefik_compose_ipv6_subnet:

# Top-level additional docker-compose options
traefik_compose_additional_options:         ""
# Main service additional docker-compose options (ex: cpu_shares, deploy, ...)
traefik_compose_service_additional_options: ""

traefik_traefik_router_service: api@internal
traefik_traefik_entrypoints: http,https
traefik_traefik_middlewares:
  - "internal-access@file"
  - "{{ project_name }}-cors@docker"


# Traefik project variables

traefik_version: 3.2

traefik_service_restart: always
traefik_network_mode:    host

traefik_logs_path: "{{ project_path }}/logs"

traefik_cert_base_src:   # ex: /etc/letsencrypt/live
traefik_cert_domains: [] # ex: [ my-domain.net, other-domain.net ]

# Label used by other services to indicate subdomain to use when relying on default routing
# see: *_traefik_subdomain vars usage
# see: djuuu.docker_tasks - tasks/init-compose-project-vars.yml
traefik_subdomain_label: "traefik.subdomain"

traefik_plugins: []

# Networks allowed through the 'internal-access@file' middleware (CIDR)
traefik_internal_networks: []
#  - { net: '192.168.1.0/24', comment: 'IPV4 local subnet' }
#  - { net: '2001:db8::/60',  comment: 'IPv6 example subnet' }

# Users allowed through the 'basic-auth@file' middleware (htpasswd strings)
traefik_basicauth_users: []
# - "example:$apr1$aLpmRU6d$ZrSfA1TGPFlpHZxGPB2re1"
```

Additional config files
-----------------------

Additional files located in `config/traefik/{{ inventory_hostname }}` will be copied in the project main directory.

This is especially useful to add dynamic configuration files.

Ex:
- config/traefik/
  - my-host/
    - dynamic-conf/
      - services/
        - some-service.yml
        - other-service.yml
      - middlewares/
        - custom-middleware.yml

Dependencies
------------

This role depends on :
- [djuuu.docker_project](https://github.com/Djuuu/ansible-role-docker-project)

Example Playbook
----------------

```yaml
- hosts: all
  gather_facts: false
  roles:
    - djuuu.traefik_docker
```

License
-------

Beerware License
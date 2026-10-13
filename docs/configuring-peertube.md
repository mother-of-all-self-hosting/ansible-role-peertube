<!--
SPDX-FileCopyrightText: 2020 Aaron Raimist
SPDX-FileCopyrightText: 2020 Chris van Dijk
SPDX-FileCopyrightText: 2020 Dominik Zajac
SPDX-FileCopyrightText: 2020 Mickaël Cornière
SPDX-FileCopyrightText: 2020-2024 MDAD project contributors
SPDX-FileCopyrightText: 2020-2024 Slavi Pantaleev
SPDX-FileCopyrightText: 2022 François Darveau
SPDX-FileCopyrightText: 2022 Julian Foad
SPDX-FileCopyrightText: 2022 Warren Bailey
SPDX-FileCopyrightText: 2023 Antonis Christofides
SPDX-FileCopyrightText: 2023 Felix Stupp
SPDX-FileCopyrightText: 2023 Julian-Samuel Gebühr
SPDX-FileCopyrightText: 2023 Pierre 'McFly' Marty
SPDX-FileCopyrightText: 2024 Thomas Miceli
SPDX-FileCopyrightText: 2024-2026 Suguru Hirahara

SPDX-License-Identifier: AGPL-3.0-or-later
-->

# Setting up PeerTube

This is an [Ansible](https://www.ansible.com/) role which installs [PeerTube](https://docs.peertube.dev/en/stable/) to run as a [Docker](https://www.docker.com/) container wrapped in a systemd service.

PeerTube is an open-source web application that provides [IP address management (IPAM)](https://en.wikipedia.org/wiki/IP_address_management) and [data center infrastructure management (DCIM)](https://en.wikipedia.org/wiki/Data_center_management#Data_center_infrastructure_management) functionality.

See the project's [documentation](https://docs.peertube.dev/en/stable/) to learn what PeerTube does and why it might be useful to you.

## Prerequisites

To run a PeerTube instance it is necessary to prepare a [Postgres](https://www.postgresql.org/) database server and [Redis](https://redis.io/) database for managing cache data.

If you are looking for Ansible roles for them, you can check out [ansible-role-postgres](https://github.com/mother-of-all-self-hosting/ansible-role-postgres) and [ansible-role-redis](https://github.com/mother-of-all-self-hosting/ansible-role-redis), both of which are maintained by the [Mother-of-All-Self-Hosting (MASH)](https://github.com/mother-of-all-self-hosting) team. The role for [Valkey](https://valkey.io/) ([ansible-role-valkey](https://github.com/mother-of-all-self-hosting/ansible-role-valkey)) is available as well.

## Adjusting the playbook configuration

To enable PeerTube with this role, add the following configuration to your `vars.yml` file.

**Note**: the path should be something like `inventory/host_vars/mash.example.com/vars.yml` if you use the [MASH Ansible playbook](https://github.com/mother-of-all-self-hosting/mash-playbook).

```yaml
########################################################################
#                                                                      #
# peertube                                                             #
#                                                                      #
########################################################################

peertube_enabled: true

########################################################################
#                                                                      #
# /peertube                                                            #
#                                                                      #
########################################################################
```

### Set the hostname

To enable the PeerTube instance you need to set the hostname as well. To do so, add the following configuration to your `vars.yml` file. Make sure to replace `example.com` with your own value.

```yaml
peertube_hostname: "example.com"
```

After adjusting the hostname, make sure to adjust your DNS records to point the domain to your server.

### Set a random string

You also need to set a random string to the variable as below by adding the following configuration to your `vars.yml` file. The value can be generated with `pwgen -s 64 1` or in another way.

```yaml
peertube_environment_variable_secret_key: YOUR_SECRET_KEY_HERE
```

### Set variables for the database server

To have the PeerTube instance connect to your Postgres server, add the following configuration to your `vars.yml` file.

```yaml
peertube_database_hostname: YOUR_POSTGRES_SERVER_HOSTNAME_HERE
peertube_database_port: 5432
peertube_database_username: YOUR_POSTGRES_SERVER_USERNAME_HERE
peertube_database_password: YOUR_POSTGRES_SERVER_PASSWORD_HERE
peertube_database_name: YOUR_POSTGRES_SERVER_DATABASE_NAME_HERE
```

Make sure to replace the placeholders with your own values.

### Configure a Redis database

It is necessary to set up a Redis database for the PeerTube instance. Valkey can also be used instead.

To enable the Redis database for PeerTube, add the following configuration to your `vars.yml` file:

```yaml
peertube_redis_hostname: YOUR_REDIS_SERVER_HOSTNAME_HERE
peertube_redis_port: 6379
peertube_redis_username: YOUR_REDIS_SERVER_USERNAME_HERE
peertube_redis_password: YOUR_REDIS_SERVER_PASSWORD_HERE
```

Make sure to replace the placeholders with your own values.

### Set administrator's account details

You also need to specify administrator's account details by adding the following configuration to your `vars.yml` file:

```yaml
peertube_environment_variable_superuser_name: ADMIN_USERNAME_HERE
peertube_environment_variable_superuser_email: ADMIN_EMAIL_ADDRESS_HERE
peertube_environment_variable_superuser_password: ADMIN_PASSWORD_HERE
```

Generating a strong password (e.g. `pwgen -s 64 1`) is recommended for `peertube_environment_variable_superuser_password`.

>[!NOTE]
> Subsequent changes to them will not affect the existing user.

### Extending the configuration

There are some additional things you may wish to configure about the service.

Take a look at:

- [`defaults/main.yml`](../defaults/main.yml) for some variables that you can customize via your `vars.yml` file. You can override settings (even those that don't have dedicated playbook variables) using the `peertube_environment_variables_additional_variables` variable

## Installing

After configuring the playbook, run the installation command of your playbook as below:

```sh
ansible-playbook -i inventory/hosts setup.yml --tags=setup-all,start
```

If you use the MASH playbook, the shortcut commands with the [`just` program](https://github.com/mother-of-all-self-hosting/mash-playbook/blob/main/docs/just.md) are also available: `just install-all` or `just setup-all`

## Usage

After running the command for installation, PeerTube becomes available at the specified hostname like `https://example.com`.

To get started, open the URL with a web browser to log in to the instance. You can log in with the username (not email address) and password specified with the `peertube_environment_variable_superuser_*` variables.

## Troubleshooting

### Check the service's logs

You can find the logs in [systemd-journald](https://www.freedesktop.org/software/systemd/man/systemd-journald.service.html) by logging in to the server with SSH and running `journalctl -fu peertube` (or how you/your playbook named the service, e.g. `mash-peertube`).

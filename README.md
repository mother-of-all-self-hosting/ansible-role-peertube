<!--
SPDX-FileCopyrightText: 2023 Slavi Pantaleev

SPDX-License-Identifier: AGPL-3.0-or-later
-->

# PeerTube Ansible role

This is an [Ansible](https://www.ansible.com/) role which installs [PeerTube](https://joinpeertube.org/) to run as a [Docker](https://www.docker.com/) container wrapped in a systemd service.

This role *implicitly* depends on:

- [`com.devture.ansible.role.playbook_help`](https://github.com/devture/com.devture.ansible.role.playbook_help)
- [`com.devture.ansible.role.systemd_docker_base`](https://github.com/devture/com.devture.ansible.role.systemd_docker_base)

Check [defaults/main.yml](defaults/main.yml) for the full list of supported options.

For an Ansible playbook which integrates this role and makes it easier to use, see the [mash-playbook](https://github.com/mother-of-all-self-hosting/mash-playbook).

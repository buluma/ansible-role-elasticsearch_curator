# [Ansible role elasticsearch_curator](#elasticsearch_curator)

Elasticsearch curator for Linux.

|GitHub|GitLab|Downloads|Version|Issues|Pull Requests|
|------|------|-------|-------|------|-------------|
|[![github](https://github.com/buluma/ansible-role-elasticsearch_curator/actions/workflows/molecule.yml/badge.svg)](https://github.com/buluma/ansible-role-elasticsearch_curator/actions/workflows/molecule.yml)|[![gitlab](https://gitlab.com/shadowwalker/ansible-role-elasticsearch_curator/badges/master/pipeline.svg)](https://gitlab.com/shadowwalker/ansible-role-elasticsearch_curator)|[![downloads](https://img.shields.io/ansible/role/d/4697)](https://galaxy.ansible.com/buluma/elasticsearch_curator)|[![Version](https://img.shields.io/github/release/buluma/ansible-role-elasticsearch_curator.svg)](https://github.com/buluma/ansible-role-elasticsearch_curator/releases/)|[![Issues](https://img.shields.io/github/issues/buluma/ansible-role-elasticsearch_curator.svg)](https://github.com/buluma/ansible-role-elasticsearch_curator/issues/)|[![PullRequests](https://img.shields.io/github/issues-pr-closed-raw/buluma/ansible-role-elasticsearch_curator.svg)](https://github.com/buluma/ansible-role-elasticsearch_curator/pulls/)|

## [Example Playbook](#example-playbook)

This example is taken from [`molecule/default/converge.yml`](https://github.com/buluma/ansible-role-elasticsearch_curator/blob/master/molecule/default/converge.yml) and is tested on each push, pull request and release.

```yaml
---
- name: Converge
  hosts: all
  become: true

  pre_tasks:
    - name: Install cron (RedHat).
      yum: name=cronie state=present
      when: ansible_os_family == 'RedHat'

    - name: Install cron (Debian).
      apt: name=cron state=present
      when: ansible_distribution == 'Debian'

    - name: Update apt cache.
      apt: update_cache=yes cache_valid_time=600
      when: ansible_distribution == 'Ubuntu'

    - name: Set pip package for newer distros.
      ansible.builtin.set_fact:
        elasticsearch_curator_pip_package: python3-pip
      when: >
        (ansible_distribution == 'Debian' and ansible_distribution_major_version == '10') or
        (ansible_distribution == 'Ubuntu' and ansible_distribution_major_version >= '18') or
        (ansible_os_family == 'RedHat' and ansible_distribution_major_version == '8')

  roles:
    - role: geerlingguy.repo-epel
      when: ansible_os_family == 'RedHat'
    - role: buluma.elasticsearch_curator
```

Also see a [full explanation and example](https://buluma.github.io/how-to-use-these-roles.html) on how to use these roles.

## [Role Variables](#role-variables)

The default values for the variables are set in [`defaults/main.yml`](https://github.com/buluma/ansible-role-elasticsearch_curator/blob/master/defaults/main.yml):

```yaml
---
elasticsearch_curator_version: ''

elasticsearch_curator_cron_jobs:
  - name: "Run elasticsearch curator actions."
    job: "/usr/local/bin/curator ~/.curator/action.yml"
    minute: 0
    hour: 1

elasticsearch_curator_config_directory: ~/.curator

elasticsearch_curator_hosts:
  - 'localhost:9200'
elasticsearch_curator_http_auth: ''
elasticsearch_curator_yaml: |
  ---
  client:
    hosts: {{ elasticsearch_curator_hosts | to_yaml }}
    url_prefix:
    use_ssl: False
    certificate:
    client_cert:
    client_key:
    ssl_no_validate: False
    http_auth: {{ elasticsearch_curator_http_auth }}
    timeout: 30
    master_only: False
  logging:
    loglevel: INFO
    logfile:
    logformat: default
    blacklist: ['elasticsearch', 'urllib3']

elasticsearch_curator_action_yaml: |
  ---
  actions:
    1:
      action: delete_indices
      options:
        ignore_empty_list: True
        disable_action: False
      filters:
      - filtertype: pattern
        kind: prefix
        value: logstash-
        exclude:
      - filtertype: age
        source: name
        direction: older
        timestring: '%Y.%m.%d'
        unit: days
        unit_count: 45
        exclude:

elasticsearch_curator_pip_package: 'python-pip'
```

## [Requirements](#requirements)

- pip packages listed in [requirements.txt](https://github.com/buluma/ansible-role-elasticsearch_curator/blob/master/requirements.txt).

## [State of used roles](#state-of-used-roles)

The following roles are used to prepare a system. You can prepare your system in another way.

| Requirement | GitHub | GitLab |
|-------------|--------|--------|
|[buluma.elasticsearch_curator](https://galaxy.ansible.com/buluma/elasticsearch_curator)|[![Build Status GitHub](https://github.com/buluma/ansible-role-elasticsearch_curator/workflows/Ansible%20Molecule/badge.svg)](https://github.com/buluma/ansible-role-elasticsearch_curator/actions)|[![Build Status GitLab](https://gitlab.com/shadowwalker/ansible-role-elasticsearch_curator/badges/master/pipeline.svg)](https://gitlab.com/shadowwalker/ansible-role-elasticsearch_curator)|
|[geerlingguy.repo-epel](https://galaxy.ansible.com/buluma/geerlingguy.repo-epel)|[![Build Status GitHub](https://github.com/buluma/geerlingguy.repo-epel/workflows/Ansible%20Molecule/badge.svg)](https://github.com/buluma/geerlingguy.repo-epel/actions)|[![Build Status GitLab](https://gitlab.com/shadowwalker/geerlingguy.repo-epel/badges/master/pipeline.svg)](https://gitlab.com/shadowwalker/geerlingguy.repo-epel)|

## [Context](#context)

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://buluma.github.io/) for further information.

Here is an overview of related roles:

![dependencies](https://raw.githubusercontent.com/buluma/ansible-role-elasticsearch_curator/png/requirements.png "Dependencies")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/buluma):

|container|tags|
|---------|----|
|[EL](https://hub.docker.com/repository/docker/buluma/enterpriselinux/general)|all|
|[Debian](https://hub.docker.com/repository/docker/buluma/debian/general)|all|
|[Ubuntu](https://hub.docker.com/repository/docker/buluma/ubuntu/general)|all|

The minimum version of Ansible required is 2.12, tests have been done to:

- The previous version.
- The current version.
- The development version.

If you find issues, please register them in [GitHub](https://github.com/buluma/ansible-role-elasticsearch_curator/issues)

## [Changelog](#changelog)

[Role History](https://github.com/buluma/ansible-role-elasticsearch_curator/blob/master/CHANGELOG.md)

## [License](#license)

[Apache-2.0](https://github.com/buluma/ansible-role-elasticsearch_curator/blob/master/LICENSE).

## [Author Information](#author-information)

[buluma](https://buluma.github.io/)

Please consider [sponsoring me](https://github.com/sponsors/buluma).

### [Special Thanks](#special-thanks)

Template inspired by [Robert de Bock](https://github.com/robertdebock)

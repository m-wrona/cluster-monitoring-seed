# Cluster monitoring

Sample seed for cluster's monitoring.

Cluster is built basing on:
- Log-stash: log aggregator
- Elastic-search: search engine 
- Kibana: analytics and visualization
- Docker: virtualization 
- Ansible: deployment & provisioning

# Infrastructure

Basic cluster monitoring configuration looks like following:

```preformated
    ┌──────────────────────────┐           ┌────────────────────────────┐
    │ monitoring (node 1)      │           │ monitoring   (node 2)      │
    └───────────────┬──────────┘           └──────┬─────────────────────┘
                    │                             │
                    │                             │
                    └────────────┐   ┌────────────┘
                                 │   │
                                 │   │
                          ┌──────┴───┴──────┐
                          │ LB (node 1,2)   │
                          └────────┬────────┘
                                   │
                                   │
                                in-data
```

Each node is composed of running monitoring services and LB.

Each service is run on separate Docker container.



# Provisioning

Install all depedendencies and prepare monitoring.

```shell
cd ansible
ansible-playbook -i {{env}} provisioning/site.yml
```

# Deployment

Deploy configuration and start monitoring services.  

```shell
cd ansible
ansible-playbook -i {{env}} deployment/site.yml
```

After deployment running instances can be checked using following command:

```shell 
ansible all -i {{env}} -a "docker ps"
```

Sample output of "docker ps":

```preformated
TODO
```


# Ansible - common commands

Some samples of handy commands to be familiar with. 

1) Ping

```shell
ansible all -i {{env}} -m ping
```

2) Run a command on all servers

```shell
ansible all -i {{env}} -a "whoami"
ansible all -i {{env}} -a "uptime"
ansible all -i {{env}} -a "date"
ansible all -i {{env}} -a "cat /etc/issue"
ansible all -i {{env}} -a "docker ps"
```

3) Show memory, cpu and other config options on all servers

```shell
ansible all -i {{env}} -m setup
ansible all -i {{env}} -m setup -a "filter=ansible_*_mb"
ansible all -i {{env}} -m setup -a "filter=ansible_processor*"
ansible all -i {{env}} -m setup -a "filter=ansible_all_ipv4_addresses"
ansible all -i {{env}} -m setup -a "filter=ansible_bios_*"
```

4) Run playbook

```shell
ansible-playbook -i {{env}} deployment/site.yml --ask-sudo-pass
ansible-playbook -i {{env}} deployment/site.yml --tags logs --ask-sudo-pass
```

5) Dry-run, i.e. only check and report what changes should be made without actually executing them

```shell
ansible-playbook -i {{env}} deployment/site.yml --tags "logs" --ask-sudo-pass --check
ansible-playbook -i {{env}} deployment/site.yml --tags "logs" --ask-sudo-pass --check --diff
```



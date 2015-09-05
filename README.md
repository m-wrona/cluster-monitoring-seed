# Cluster monitoring

Sample seed for cluster's monitoring.

Cluster is built basing on:
- Log-stash: log aggregator
- Elastic-search: search engine, load-balancer 
- Kibana: analytics and visualization
- Docker: virtualization 
- Ansible: deployment & provisioning

***Note:***

Monitoring is prepared to watch over sample [cluster-seed](https://github.com/m-wrona/cluster-seed). 

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

# Local development

To speed up local development & testing Vagrant configuration is provided.

In order to star virtual cluster locally just hit:

```shell
vagrant up
```

After that two nodes will be started:

- monitoring-node1: 10.10.2.30
- monitoring-node2: 10.10.2.31

Don't forget to provide your key later on while performing any activities on cluster, for instance:

```shell
cd ansible
# ansible with provided private key
ansible-playbook -i {{env}} provisioning/site.yml  --private-key=~/.vagrant.d/insecure_private_key
```

# Checking cluster state

After deployment running services on cluster can be checked using following command:

```shell 
ansible all -i {{env}} -a "docker ps "
```

For local cluster on Vagrant it looks like following:

```shell 
ansible all -i local -a "docker ps" --sudo --private-key=~/.vagrant.d/insecure_private_key
```

Sample output of "docker ps" for local cluster:

```preformated
TODO add traces here
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



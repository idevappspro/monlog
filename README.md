# Ansible playbook with roles to install Monitoring and Logging software

A set of Ansible roles to install and configure Prometheus and its exporters, as well as the EFK logging stack.

## Prometheus

- Creates the right user, group and directories with the right permissions and owners
- Installs Prometheus Server as a service
- Adds Configuration with a preset target for monitoring Prometheus itself (see <https://prometheus.io/docs/prometheus/latest/getting_started/#configuring-prometheus-to-monitor-itself>)
- Adds Systemd service file to run it
- Starts and restarts the service when the global configuration parameters are changed
  
**Note** The list of versions is available on the [github](https://github.com/prometheus/prometheus/releases) page

![Prometheus](./images/prom.png "Prometheus")

## Node Exporter
  
- Creates the right users and directories with the right permissions and owners
- Installs the Node Exporter
- Adds
  - Systemd service file to run it
  - A new host to the Prometheus configuration through <https://prometheus.io/docs/prometheus/latest/configuration/configuration/#static_config> (don't forget that the server with Prometheus may not be located locally, you need to be able to add the configuration to a remote server as well).
  - Runs the Node Exporter service and reloads the Prometheus service when adding a new host to the configuration
  
**Note**: The list of versions is available on the [github](https://github.com/prometheus/node_exporter/releases) page
  
Bear in mind that you shouldn't have any configuration conflicts when running the Node Exporter role and the Prometheus role, even though both roles make changes to the config.

![Node Exporter](./images/node.png "Node Exporter")

## EFK (Elasticsearch + Kibana)

- Creates the right users and directories with the right permissions and owners
- Installs the Elasticsearch and Kibana
- Add systemd service file to run it
- Runs the Elasticsearch and Kibana services

----

## Requiremetns

### 1. Install requirements

To install both roles and collections required at the same time with one command, run the following:

```bash
ansible-galaxy install -r requirements.yaml
```

### 2. Generate SSH key and copy to role files folder

**Note**
The key files present in the project are provided for introductory purposes. It is highly recommended to create personal keys on your own.

In a terminal window run the following commads:

```bash
cd ~
git pull https://github.com/idevappspro/monlog.git
cd ~/monlog/roles/provision_ssh_key
ssh-keygen -t rsa ssh-keygen -f ./ssh_auth
```

Follow the prompts to generate the key. A public and a private key are generated. The public key has .pub in the file name.

```bash
eval "$(ssh-agent -s)"
ssh-add ./ssh_auth
cd ~/monlog
```

### 3. Inventory

Define proper host and its IP address into the inventory file

```inventory.yaml```

----

## Run playbook

Execute the following command in you terminal from the machine with access to target hosts

```bash
ansible-playbook playbook.yaml
```

----

## Project file structure

```text
── ansible.cfg
├── images
│   ├── node.png
│   └── prom.png
├── inventory.yaml
├── playbook.yaml
├── README.md
├── requirements.yaml
└── roles
    ├── efk
    │   ├── defaults
    │   │   └── main.yml
    │   ├── files
    │   ├── handlers
    │   │   └── main.yml
    │   ├── meta
    │   │   └── main.yml
    │   ├── README.md
    │   ├── tasks
    │   │   └── main.yml
    │   ├── templates
    │   ├── tests
    │   │   ├── inventory
    │   │   └── test.yml
    │   └── vars
    │       └── main.yml
    ├── node_exporter
    │   ├── defaults
    │   │   └── main.yml
    │   ├── files
    │   │   └── node_exporter.service
    │   ├── handlers
    │   │   └── main.yml
    │   ├── meta
    │   │   └── main.yml
    │   ├── README.md
    │   ├── tasks
    │   │   └── main.yml
    │   ├── templates
    │   ├── tests
    │   │   ├── inventory
    │   │   └── test.yml
    │   └── vars
    │       └── main.yml
    ├── prometheus
    │   ├── defaults
    │   │   └── main.yml
    │   ├── files
    │   │   └── prometheus.service
    │   ├── handlers
    │   │   └── main.yml
    │   ├── meta
    │   │   └── main.yml
    │   ├── README.md
    │   ├── tasks
    │   │   └── main.yml
    │   ├── templates
    │   ├── tests
    │   │   ├── inventory
    │   │   └── test.yml
    │   └── vars
    │       └── main.yml
    └── provision_ssh_key
        ├── defaults
        │   └── main.yml
        ├── files
        │   ├── ssh_auth
        │   └── ssh_auth.pub
        ├── handlers
        │   └── main.yml
        ├── meta
        │   └── main.yml
        ├── README.md
        ├── tasks
        │   └── main.yml
        ├── templates
        ├── tests
        │   ├── inventory
        │   └── test.yml
        └── vars
            └── main.yml
```

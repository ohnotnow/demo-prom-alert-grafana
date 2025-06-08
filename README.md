# demo-prom-alert-grafana

A self-contained demonstration of an HA Prometheus monitoring and alerting stack with Grafana dashboards.  
It includes:
- Two Prometheus servers with shared alerting rules  
- A two-node Alertmanager cluster with email alert simulation via MailHog  
- Two Node Exporters to simulate server metrics  
- Grafana with pre-provisioned datasources and dashboards  

Repository: https://github.com/ohnotnow/demo-prom-alert-grafana  
License: MIT

## Table of Contents
1. [Prerequisites](#prerequisites)  
2. [Installation](#installation)  
3. [Running the Stack](#running-the-stack)  
4. [Accessing Services](#accessing-services)  
5. [Configuration](#configuration)  
6. [Contributing](#contributing)  
7. [License](#license)

## Prerequisites
- git  
- Docker Engine  
- Docker Compose  

(Ensure Docker and Docker Compose are installed and in your PATH.)

## Installation

### Clone the repository
```sh
git clone https://github.com/ohnotnow/demo-prom-alert-grafana.git
cd demo-prom-alert-grafana
```

### (Optional) Pull images in advance
```sh
docker compose pull
```

## Running the Stack

### macOS & Ubuntu
```sh
docker compose up -d
```

### Windows (PowerShell or CMD)
```powershell
cd demo-prom-alert-grafana
docker-compose up -d
```

To stop and remove containers, networks and volumes:
```sh
docker compose down
```

After the images have pulled and started (might take a few minutes) you can run :
```
docker stop node-exporter1
```
After a minute or so you should get a single (de-duplicated) alert in Mailhog.

## Accessing Services

| Service           | URL                         | Notes                                      |
|-------------------|-----------------------------|--------------------------------------------|
| Prometheus #1     | http://localhost:9090       | Primary Prometheus                         |
| Prometheus #2     | http://localhost:9091       | Secondary Prometheus                       |
| Alertmanager #1   | http://localhost:9093       | HTTP/API port                              |
| Alertmanager #2   | http://localhost:9095       | HTTP/API port                              |
| MailHog UI        | http://localhost:8026       | Captures alert emails                      |
| Grafana           | http://localhost:3000       | Default admin/admin (password: `admin`)    |

## Configuration

All configuration files live under the project:

- `prometheus/prometheus-1.yml` & `prometheus/prometheus-2.yml`: Prometheus server configs  
- `prometheus/alerts.rules.yml`: Reusable alerting rules  
- `alertmanager/am-1.yml` & `alertmanager/am-2.yml`: Alertmanager cluster configs  
- `grafana/provisioning`: Datasource and dashboard provisioning  

Adjust these files as needed before starting the stack.

## Additions for real-world implementations
You might want at least basic-auth in front of your node-exporters and/or prometheus instances.

Node exporter and prometheus both (somewhat experimentally) support an extra flag of `--web.config.file` which is a yaml config.  For example :

```yaml
# Usernames and passwords required to connect.
# Passwords are hashed with bcrypt: https://github.com/prometheus/exporter-toolkit/blob/master/docs/web-configuration.md#about-bcrypt.
basic_auth_users:
  alice: $2y$10$mDwo.lAisC94iLAyP81MCesa29IzH37oigHC/42V2pdJlUprsJPze
  bob: $2y$10$hLqFl9jSjoAAy95Z/zw8Ye8wkdMBM8c5Bn1ptYqP/AXyV0.oy0S8m
```

To make the alerts more reliable - you could set up an instance of an smtp-relay (eg, postfix) which has a main smtp relay and a fallback.  Then your alertmanager sends emails to the local postfix and lets it deal with fall-over smtp if your smtp server itself is offline.

There is a very basic example in `postfix/main.cf`.

## Contributing

1. Fork the repository  
2. Create a feature branch (`git checkout -b feature/YourFeature`)  
3. Commit your changes (`git commit -m "Add YourFeature"`)  
4. Push to the branch (`git push origin feature/YourFeature`)  
5. Open a pull request  

Please ensure all changes are documented and tested.

## License

This project is licensed under the MIT License.  
See [LICENSE](LICENSE) for details.

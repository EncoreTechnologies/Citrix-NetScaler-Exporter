# Prometheus exporter for Citrix NetScaler
This exporter collects statistics from Citrix NetScaler and makes them available for Prometheus to pull.  As the NetScaler is an appliance it's not recommended to run the exporter directly on it, so it will need to run elsewhere.

## NetScaler configuration
The exporter works with any local NetScaler user account which has permissions to run the ``stats`` command.  It would be preferable to configure a specific user for this which only has permissions to retrieve stats.

If you lean towards the NetScaler CLI, you want to do something like the following (obviously changing the username as you see fit).

````
# Create a new Command Policy which is only allowed to run the stat command
add system cmdPolicy stat ALLOW "(^stat.*|show ns license)"

# Create a new user.  Disabling externalAuth is important as if it is enabled a user created in AD (or other external source) with the same name could login
add system user stats "password" -externalAuth DISABLED # Change the password to reflect whatever complex password you want

# Bind the local user account to the new Command Policy
bind system user stats stat 100
````

## Usage
You can monitor multiple NetScaler instances by passing in the URL, username, and password as command line flags to the exporter.  If you're running multiple exporters on the same server, you'll also need to change the port that the exporter binds to.

| Flag      | Description                                                                                               | Default Value |
| --------- | --------------------------------------------------------------------------------------------------------- | ------------- |
| url       | Base URL of the NetScaler management interface.  Normally something like https://mynetscaler.internal.com | none          |
| username  | Username with which to connect to the NetScaler API                                                       | none          |
| password  | Password with which to connect to the NetScaler API                                                       | none          |
| bind_port | Port to bind the exporter endpoint to                                                                     | 9280          |


Run the exporter manually using the following command:

````
Citrix-NetScaler-Exporter.exe -url https://mynetscaler.internal.com -username stats -password "my really strong password"
````

This will run the exporter using the default bind port.  If you need to change the port, append the ``-bind_port`` flag to the command.

### Running as a service
Ideally you'll run the exporter as a service.  There are many ways to do that, so it's really up to you.  If you're running it on Windows I would recommend [NSSM](https://nssm.cc/).

## Exported metrics
### NetScaler

| Metric                                 | Metric Type | Unit    |
| -------------------------------------- | ----------- | ------- |
| CPU usage                              | Gauge       | Percent |
| Memory usage                           | Gauge       | Percent |
| Management CPU usage                   | Gauge       | Percent |
| Packet engine CPU usage                | Gauge       | Percent |
| /flash partition usage                 | Gauge       | Percent |
| /var partition usage                   | Gauge       | Percent |
| MB received per second                 | Gauge       | MB/s    |
| MB sent per second                     | Gauge       | MB/s    |
| HTTP requests per second               | Gauge       | None    |
| HTTP responses per second              | Gauge       | None    |
| Current client connections             | Gauge       | None    |
| Current established client connections | Gauge       | None    |
| Current server connections             | Gauge       | None    |
| Current established server connections | Gauge       | None    |

### Interfaces
For each interface, the following metrics are retrieved.

| Metric                                 | Metric Type | Unit    |
| -------------------------------------- | ----------- | ------- |
| Interface ID                           | N/A         | None    |
| Received bytes per second              | Gauge       | Bytes   |
| Transmitted bytes per second           | Gauge       | Bytes   |
| Received packets per second            | Gauge       | None    |
| Transmitted packets per second         | Gauge       | None    |
| Jumbo packets retrieved per second     | Gauge       | None    |
| Jumbo packets transmitted per second   | Gauge       | None    |
| Error packets received per second      | Gauge       | None    |
| Intrerface alias                       | N/A         | None    |

## Virtual Servers
For each virtual server, the following metrics are retrieved.

| Metric                     | Metric Type | Unit    |
| ---------------------------| ----------- | ------- |
| Name                       | N/A         | None    |
| Waiting requests           | Gauge       | None    |
| Health                     | Gauge       | Percent |
| Inactive services          | Gauge       | None    |
| Active services            | Gauge       | None    |
| Total hits                 | Counter     | None    |
| Hits rate                  | Gauge       | None    |
| Total requests             | Counter     | None    |
| Requests rate              | Gauge       | None    |
| Total responses            | Counter     | None    |
| Responses rate             | Gauge       | None    |
| Total request bytes        | Counter     | Bytes   |
| Request bytes rate         | Gauge       | Bytes/s |
| Total response bytes       | Counter     | Bytes   |
| Response bytes rate        | Gauge       | Bytes/s |
| Current client connections | Gauge       | None    |
| Current server connections | Gauge       | None    |

## Services
For each service, the following metrics are retrieved.

| Metric                         | Metric Type | Unit    |
| -------------------------------| ----------- | ------- |
| Name                           | N/A         | None    |
| Throughput                     | Counter     | MB      |
| Throughput rate                | Gauge       | MB/s    |
| Average time to first byte     | Gauge       | Seconds |
| State                          | Gauge       | None    |
| Total requests                 | Counter     | None    |
| Requests rate                  | Gauge       | None    |
| Total responses                | Counter     | None    |
| Responses rate                 | Gauge       | None    |
| Total request bytes            | Counter     | Bytes   |
| Request bytes rate             | Gauge       | Bytes/s |
| Total response bytes           | Counter     | Bytes   |
| Response bytes rate            | Gauge       | Bytes/s |
| Current client connections     | Gauge       | None    |
| Surge count                    | Gauge       | None    |
| Current server connections     | Gauge       | None    |
| Server established connections | Gauge       | None    |
| Current reuse pool             | Gauge       | None    |
| Max clients                    | Gauge       | None    |
| Current load                   | Gauge       | Percent |
| Service hits                   | Counter     | None    |
| Service hits rate              | Gauge       | None    |
| Active transactions            | Gauge       | None    |

## Downloading a release
https://gitlab.com/rokett/Citrix-NetScaler-Exporter/tags

## Building the executable
All dependencies are version controlled, so building the project is really easy.

1. Clone the repository locally.
2. From within the repository directory run ``go build``.
3. Hey presto, you have an executable.

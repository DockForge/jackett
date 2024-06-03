# [Jackett](https://github.com/DockForge/Jackett)


[Jackett](https://github.com/Jackett/Jackett) is a versatile and robust proxy server designed to streamline the process of querying torrent trackers. By translating queries from various applications such as Sonarr, SickRage, CouchPotato, and Mylar into tracker-site-specific HTTP queries, Jackett simplifies the complex interaction between these applications and numerous torrent sites.

### How It Works
When a supported application needs to perform a search or fetch recent uploads, it sends a query to Jackett. Jackett then translates this query into a format that is compatible with the target torrent site, sends the request, and processes the HTML response. After parsing the response, Jackett extracts the relevant data and sends it back to the requesting application in a standardized format. This seamless integration allows users to efficiently manage and automate their torrent-related tasks.

### Key Benefits
1. **Centralized Indexer Management**: Jackett maintains a single repository of indexer scraping and translation logic. This centralized approach reduces the complexity for other applications, as they no longer need to individually manage and update indexers.
2. **Broad Compatibility**: With support for a wide range of applications and torrent sites, Jackett offers extensive flexibility and ensures that users can find and download content from their preferred sources.
3. **Ease of Use**: By handling the intricacies of tracker-specific queries and responses, Jackett makes it easier for users to set up and maintain their media management workflows.

### Use Cases
- **Automated Media Management**: Applications like Sonarr and CouchPotato can automatically search for and download new episodes or movies as they become available on torrent sites.
- **Unified Search**: Users can perform searches across multiple torrent sites from a single interface, without needing to manually visit each site.
- **RSS Feed Emulation**: Jackett can fetch recent uploads from various trackers, emulating the functionality of RSS feeds for torrent sites that do not support them.

Jackett is an essential tool for anyone looking to streamline their interaction with torrent trackers and enhance the automation of their media management systems.


## Application Setup

### Accessing the Web Interface
Once you have Jackett up and running, you can access its web interface by navigating to `<your-ip>:9117` in your web browser. This interface is the central hub where you can configure various settings, manage tracker connections, and integrate Jackett with other applications.

### Configuring Trackers
In the web interface, you'll find options to add and configure different torrent trackers. Each tracker can be customized with specific settings to ensure optimal performance and compatibility with your desired torrent sites. Follow the prompts and guidelines within the interface to set up each tracker according to your preferences.

### Integrating with Other Applications
Jackett works seamlessly with a variety of popular applications such as Sonarr, SickRage, CouchPotato, and Mylar. To integrate Jackett with these applications:
1. **Navigate to the Configuration Section**: Within the web interface, go to the section dedicated to configuring connections with other apps.
2. **Generate API Keys**: Some applications may require an API key from Jackett. You can generate and manage these keys within the interface.
3. **Input Jackett Details in the Application**: In the settings of the application you wish to integrate with Jackett, input the necessary details such as the Jackett server address (`<your-ip>:9117`) and the API key if required.
4. **Test the Connection**: Ensure the integration is successful by testing the connection within the application.

### Additional Resources
For more detailed instructions and troubleshooting, please refer to the comprehensive documentation available on the [Jackett GitHub page](https://github.com/Jackett/Jackett).

By following these steps, you can effectively set up Jackett and integrate it with your media management applications, creating a powerful and automated system for handling your torrent downloads.

## Usage

To help you get started with creating a container from this image, you can use either Docker Compose (recommended) or the Docker CLI.

### Using Docker Compose (Recommended)
Docker Compose simplifies the management of multi-container Docker applications. For more detailed information, you can refer to the [official Docker Compose documentation](https://docs.docker.com/compose/).

#### Sample `docker-compose.yml` Configuration

```yaml
---
services:
  jackett:
    image: dublok/jackett:latest
    container_name: jackett
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
      - AUTO_UPDATE=true # optional
      - RUN_OPTS= # optional
    volumes:
      - /path/to/jackett/data:/config
      - /path/to/blackhole:/downloads
    ports:
      - 9117:9117
    restart: unless-stopped
```

### Using Docker CLI
If you prefer using the Docker command-line interface, you can create and run the Jackett container with the following command. For more details, visit the [Docker CLI reference](https://docs.docker.com/engine/reference/commandline/cli/).

#### Sample Docker CLI Command

```bash
docker run -d \
  --name=jackett \
  -e PUID=1000 \
  -e PGID=1000 \
  -e TZ=Etc/UTC \
  -e AUTO_UPDATE=true `# optional` \
  -e RUN_OPTS= `# optional` \
  -p 9117:9117 \
  -v /path/to/jackett/data:/config \
  -v /path/to/blackhole:/downloads \
  --restart unless-stopped \
  dublok/jackett:latest
```

## Parameters

Containers are configured using parameters passed at runtime, such as those shown above. These parameters are formatted as `<external>:<internal>`, representing the external host configuration and the internal container configuration respectively. For example, `-p 8080:80` maps port `80` inside the container to port `8080` on the host machine.

| Parameter | Function |
|:---------:|----------|
| `-p 9117` | WebUI |
| `-e PUID=1000` | UserID - see explanation below |
| `-e PGID=1000` | GroupID - see explanation below |
| `-e TZ=Etc/UTC` | Specify a timezone, see this [list](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones#List). |
| `-e AUTO_UPDATE=true` | Allow Jackett to update inside the container (recommended and enabled by default) |
| `-e RUN_OPTS=` | Optionally specify additional arguments to pass. |
| `-v /config` | Path where Jackett should store its config files. |
| `-v /downloads` | Path to the torrent blackhole directory. |

### Environment Variables from Files (Docker Secrets)
You can set any environment variable from a file by using the special prefix `FILE__`. This is particularly useful for managing sensitive information such as passwords.

#### Example

```bash
-e FILE__MYVAR=/run/secrets/mysecretvariable
```

This will set the environment variable `MYVAR` based on the contents of the `/run/secrets/mysecretvariable` file.

Here's an enhanced and more detailed version of the sections on Umask and User/Group Identifiers:

---

## Umask for Running Applications

For all our Docker images, you have the option to override the default umask settings for services started within the containers. This can be done using the optional `-e UMASK=022` setting.

### Understanding Umask
Umask, or user file creation mode mask, is a setting that determines the default permission set for newly created files and directories. It works by subtracting permissions from the default setting. Unlike `chmod`, which directly sets permissions, umask removes permissions based on its value. For a deeper understanding, please refer to the [umask Wikipedia article](https://en.wikipedia.org/wiki/Umask).

### Example
If you set `UMASK=022`, the resulting permissions for newly created files and directories will have read and write permissions for the owner, and read permissions for the group and others. The calculation works as follows:
- Default directory permissions: `777` (rwxrwxrwx)
- Umask value: `022` (-----w--w-)
- Resulting permissions: `755` (rwxr-xr-x)

By understanding and configuring umask properly, you can ensure that your files and directories have the desired permissions.

## User / Group Identifiers

When using volumes (`-v` flags) to mount directories from the host OS into the container, you might encounter permissions issues. This can happen because the container's internal user might not have the same permissions as the host user. To resolve this, you can specify the user `PUID` (Process User ID) and group `PGID` (Process Group ID) when running the container.

### How to Specify PUID and PGID
Ensure any volume directories on the host are owned by the same user and group that you specify in the container settings. This will prevent permissions issues and ensure seamless operation.

### Finding Your User and Group IDs
To find your user and group IDs, use the `id` command on your host system:

```bash
id your_user
```

#### Example Output
```text
uid=1000(your_user) gid=1000(your_user) groups=1000(your_user)
```
In this example, `PUID=1000` and `PGID=1000`.

### Setting PUID and PGID in Docker
When starting the container, include the following environment variables:

```yaml
environment:
  - PUID=1000
  - PGID=1000
```

Or, when using the Docker CLI:

```bash
docker run -d \
  --name=jackett \
  -e PUID=1000 \
  -e PGID=1000 \
  -v /path/to/jackett/data:/config \
  -v /path/to/blackhole:/downloads \
  dublok/jackett:latest
```

By ensuring that the volume directories on the host are owned by the specified user and group, permissions issues will be resolved, allowing the container to operate smoothly.

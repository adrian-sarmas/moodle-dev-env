# Moodle Docker Environment

This repository provides a complete Docker-based environment for local Moodle development, incorporating essential services such as a web server, database, PHP runtime, and email testing utility. It also includes HTTPS support for enhanced testing of secure features, enabled through the use of `mkcert` to generate trusted SSL certificates for localhost.

The setup includes:
- **Nginx** as the web server, configured to handle both HTTP and HTTPS requests.
- **PHP-FPM** with essential Moodle extensions for optimal performance.
- **MariaDB** as the database server, configured with typical Moodle settings.
- **Mailhog** for capturing outgoing emails, allowing easy testing of email functionality within Moodle.
- **mkcert** for generating locally trusted SSL certificates, enabling HTTPS support on localhost.

This setup is platform-independent and can be used on **macOS**, **Linux**, and **Windows** systems with minimal configuration changes. Once set up, you will have a complete Moodle environment accessible via both HTTP and HTTPS, suitable for development and testing.

## Prerequisites

*   Docker and Docker Compose
*   [`mkcert`](https://github.com/FiloSottile/mkcert) for generating trusted SSL certificates for local development

## Setup Instructions

### 1\. Clone the Repository

```bash
git clone git@github.com:adrian-sarmas/moodle-dev-env.git moodle-dev-env
cd moodle-dev-env
```

### 2\. Download Moodle

Place the Moodle source code in the `moodle` directory. You can download Moodle from moodle.org or use `git` to clone a specific version.

```bash
cd moodle
# Example to download a specific Moodle version (replace <VERSION> with the desired version)
wget https://download.moodle.org/stable<VERSION>/moodle-latest-<VERSION>.tgz
tar -xzvf moodle-latest-<VERSION>.tgz --strip 1
cd ..
```

### 3\. Generate SSL Certificates with `mkcert`

1.  **Install `mkcert`** if not already installed:
    
    *   macOS: `brew install mkcert && brew install nss` (for Firefox support)
    *   Linux and Windows: Follow instructions from the [mkcert GitHub page](https://github.com/FiloSottile/mkcert)
2.  **Generate SSL certificates**:

    ```bash
    mkcert -install
    mkcert -key-file ssl/localhost.key -cert-file ssl/localhost.crt localhost
    ```
    
    This will create two files:
    *   `ssl/localhost.key` - Private key
    *   `ssl/localhost.crt` - Certificate

### 4\. Update Nginx Configuration (Optional)

If you encounter port conflicts with other local servers, you can modify the exposed ports in `docker-compose.yml`. For example, change the HTTP port from `8045:80` to another unused port, like `8080:80`.

In `docker-compose.yml`:

```yaml
ports:
  - "8080:80"   # HTTP
  - "8443:443"  # HTTPS
```

And update `nginx.conf` to ensure the server listens on the correct ports if you made any changes.

### 5\. Update Moodle’s Configuration for HTTPS

In `moodle/config.php`, set the `wwwroot` to use HTTPS and specify the correct port if needed (default is `8443`):

```php
$CFG->wwwroot = 'https://localhost:8443';
```

### 6\. Start Docker Containers

Once everything is set up, you can start the Docker containers:

```bash
docker-compose up --build
```

This will spin up the following services:

*   **MariaDB**: MySQL-compatible database for Moodle
*   **PHP-FPM**: PHP environment for running Moodle
*   **Nginx**: Web server configured for both HTTP and HTTPS
*   **Mailhog**: Mail server for capturing outgoing emails (available at [http://localhost:8025](http://localhost:8025))

### 7\. Access Moodle

You can now access Moodle:

*   **HTTP**: [http://localhost:8045](http://localhost:8045) (or another port if modified)
*   **HTTPS**: https://localhost:8443 (or another port if modified)

### 8\. Additional Configuration

*   **Mailhog**: Access Mailhog for viewing email messages sent by Moodle at [http://localhost:8025](http://localhost:8025).
*   **Database**: The MariaDB database is accessible at `mariadb` with the credentials configured in `docker-compose.yml`.

- - -

## Troubleshooting

### 1\. HTTPS Certificate Warnings

If your browser shows warnings for the certificate, ensure that `mkcert`'s root CA is properly installed and trusted.

### 2\. Port Conflicts

If you encounter issues with ports, update the `ports` section in `docker-compose.yml` to use available ports on your system.

### License
This project is licensed under the MIT License. See the [`LICENSE`](https://github.com/adrian-sarmas/moodle-dev-env/blob/main/LICENSE) file for details.

- - -

## Notes

*   This environment is intended for **local development only** and should not be used in production.
*   Make sure to update any sensitive credentials before deploying in a non-local environment.
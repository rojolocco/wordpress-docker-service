# WordPress Deployment with Docker Compose

This project contains a `docker-compose.yml` file to deploy WordPress and MySQL in a production environment using Docker. The setup includes resource limits and uses Caddy as a reverse proxy.

## Requirements

- A VPS with Docker and Docker Compose installed.
- A domain name configured to point to your VPS IP (e.g., `urgenciaslean360.com`).
- Caddy installed and configured as the reverse proxy.
- `.env` file with the required environment variables.

## Configuration

### 1. `.env` File

Create a `.env` file in the same directory as the `docker-compose.yml` file with the following variables:

```env
MYSQL_ROOT_PASSWORD=your_root_password
MYSQL_DATABASE=wordpress
MYSQL_USER=wordpress
MYSQL_PASSWORD=your_wordpress_password
WORDPRESS_DB_HOST=db
```

### 2. Caddy Configuration

Ensure your Caddy configuration file includes an entry for the domain pointing to the WordPress service. Example:

```caddyfile
urgenciaslean360.com {
    reverse_proxy localhost:8081
    tls your-email@example.com
}
```

Reload Caddy after updating the configuration:

```bash
caddy reload
```

## Usage

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/your-repo.git
cd your-repo
```

### 2. Start the Services

Run the following command to start the WordPress and MySQL containers:

```bash
docker-compose up -d
```

### 3. Access the Application

- Visit `http://urgenciaslean360.com` to access the WordPress installation page.
- Complete the installation wizard to set up WordPress.

## Docker Compose Configuration

### Services

#### `db`

- **Image**: MySQL 8.0
- **Ports**: Not exposed publicly
- **Environment Variables**: Defined in `.env`
- **Volumes**: Data persistence for `/var/lib/mysql`

#### `wordpress`

- **Image**: WordPress (latest)
- **Ports**: Mapped `8081:80`
- **Environment Variables**: Defined in `.env`
- **Resource Limits**:
  - Memory: 512 MB
  - CPU: 50%
- **Volumes**: Data persistence for `/var/www/html`

### Networks

The services are connected to an external network called `caddy_network`, which must exist prior to deployment. Create it with:

```bash
docker network create caddy_network
```

## Healthcheck (Optional)

The `db` service includes an optional healthcheck:

```yaml
healthcheck:
  test: ["CMD-SHELL", "mysqladmin ping -h localhost -u root -p$MYSQL_ROOT_PASSWORD || exit 1"]
  interval: 30s
  timeout: 10s
  retries: 3
  start_period: 30s
```

To verify the health status:

```bash
docker inspect --format='{{json .State.Health}}' mysql
```

## Stopping the Services

To stop the containers:

```bash
docker-compose down
```

This will stop and remove the containers but preserve the data in the volumes.

## License

This project is licensed under the MIT License. See the `LICENSE` file for details.

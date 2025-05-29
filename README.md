# Cantaloupe Docker

A Docker setup for running [Cantaloupe IIIF Image Server](https://cantaloupe-project.github.io/) version 5.0.7.

## Quick Start

1. **Setup environment variables:**
   ```bash
   cp .env.example .env
   ```
   
2. **Edit `.env` file as needed:**
   - `IMAGES_FOLDER`: Path to your image directory (default: `./images`)
   - `JAVA_OPTS`: JVM options (default: `-Xmx2g`)

3. **Start the service:**
   ```bash
   docker compose up -d
   ```

4. **Access the service:**
   - IIIF Image API: http://localhost:8182/iiif/2/
   - Health check: http://localhost:8182/health

## Configuration

The Cantaloupe configuration is located in [`cantaloupe/cantaloupe.properties`](cantaloupe/cantaloupe.properties). Key settings include:

- **Source**: FilesystemSource with images served from `/images/`
- **IIIF API**: Endpoints 2.x and 3.x enabled
- **File format**: Configured for `.tif` files by default
- **Caching**: Logs and cache stored in mounted volumes

## Directory Structure

```
├── cantaloupe/              # Cantaloupe configuration and Dockerfile
│   ├── cantaloupe.properties
│   ├── delegates.rb
│   └── Dockerfile
├── images/                  # Your image files (mounted volume)
├── cantaloupe-cache/        # Cache directory (mounted volume)
├── cantaloupe-logs/         # Log files (mounted volume)
└── docker-compose.yml
```

## Example Usage

Place your TIFF images in the `images/` directory and access them via:
```
http://localhost:8182/iiif/2/{image-identifier}/info.json
http://localhost:8182/iiif/2/{image-identifier}/full/max/0/default.jpg
```

## Logs

Application logs are available in the `cantaloupe-logs/` directory:
- [`cantaloupe-logs/application.log`](cantaloupe-logs/application.log)
- [`cantaloupe-logs/access.log`](cantaloupe-logs/access.log)
- [`cantaloupe-logs/error.log`](cantaloupe-logs/error.log)

## Customization

To customize the Cantaloupe configuration:
1. Edit [`cantaloupe/cantaloupe.properties`](cantaloupe/cantaloupe.properties)
2. Modify [`cantaloupe/delegates.rb`](cantaloupe/delegates.rb) for advanced scripting
3. Rebuild the container: `docker compose build`
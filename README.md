# AWS Emulator Docker Setup

This repository provides a minimal Docker image for running the AWS emulator locally. The image is based on Amazon Linux 2023 and installs the latest LTS AWS emulator package available from AWS.

The emulator listens on port `9297` inside the container and is exposed through Docker as `localhost:9297`.

## Included in the image

- Amazon Linux 2023 Minimal
- AWS emulator LTS package
- Non-root runtime user
- Network binding on `0.0.0.0`
- Container port `9297`

The `main` branch is built and published automatically to GitHub Container Registry (GHCR).

## Run the published image

```bash
docker run --rm -p 9297:9297 ghcr.io/carlosolcina/aws-local-setup:latest
```

The emulator will then be available at:

```text
http://localhost:9297
```

Stop the container with `Ctrl+C`.

To run it in the background:

```bash
docker run --detach --name aws-emulator --publish 9297:9297 ghcr.io/carlosolcina/aws-local-setup:latest
```

Check the container status:

```bash
docker ps --filter "name=aws-emulator"
```

Stop and remove it:

```bash
docker stop aws-emulator
docker rm aws-emulator
```

## Build the image locally

From the repository root:

```bash
docker build --tag aws-local-emulator .
```

Run the local image:

```bash
docker run --rm -p 9297:9297 aws-local-emulator
```

## Use with the AWS CLI

Configure the AWS CLI to send requests to the local emulator endpoint:

```bash
export AWS_ACCESS_KEY_ID=test
export AWS_SECRET_ACCESS_KEY=test
export AWS_DEFAULT_REGION=us-east-1
```

On Windows PowerShell:

```powershell
$env:AWS_ACCESS_KEY_ID = "test"
$env:AWS_SECRET_ACCESS_KEY = "test"
$env:AWS_DEFAULT_REGION = "us-east-1"
```

Pass `--endpoint-url http://localhost:9297` to AWS CLI commands, for example:

```bash
aws --endpoint-url http://localhost:9297 sts get-caller-identity
```

The emulator does not require valid AWS credentials, but the AWS CLI may require placeholder values as shown above.

## Docker Compose

You can add the emulator to a `docker-compose.yml` file:

```yaml
services:
  aws-emulator:
    image: ghcr.io/carlosolcina/aws-local-setup:latest
    ports:
      - "9297:9297"
```

Start it with:

```bash
docker compose up
```
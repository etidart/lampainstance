This repository contains everything needed to deploy a private online cinema. It was created to work with my specific setup but can be adapted for use anywhere. I hope others find it useful!

**Note:** While everything can potentially be replaced with a single `docker-compose` file, I prefer manually created systems.

## System Components

The ready-made system should include the following elements:

- **Nginx with Cookie-Based Authorization**
  - **Static:**
    - Authorization page with the ability to assign a passkey cookie (not included).
    - [Lampa](https://github.com/yumata/lampa-source) (look for instructions below).
  - **Proxy:**
    - TorrServer (by default to `localhost:8090`).
    - TMDB (for `api.themoviedb.org` and `imagetmdb.com`).
- **TorrServer**: [TorrServer-slim](https://github.com/etidart/TorrServer-slim) which is listening on `localhost:8090` by default.
- **Optional:** Jackett server (not included in this repository due to the availability of many public servers).

## Instructions

### Lampa

1. In `lampa.patch`, replace `asd.ad` with your actual domain name and/or modify URLs as needed (jackett url):

```bash
sed -i 's/asd\.ad/your_actual_domain_name/g' lampa.patch
```

2. Build the lampa inside of a Docker container:

```bash
docker build --output=. .
```

3. This will create a `lampa.tar.gz` file in the current directory. Extract it to `/var/www/lampa` to match the Nginx configurations from this repository.

```bash
tar -xvf lampa.tar.gz -C /var/www/lampa
```

### Nginx

1. Copy the configuration files:

```bash
cp nginx.conf tokens.conf /etc/nginx/
cp asd.ad-site /etc/nginx/sites-available/your_actual_domain_name-site
ln -s ../sites-available/your_actual_domain_name-site /etc/nginx/sites-enabled/your_actual_domain_name-site
cp conf.d/* /etc/nginx/conf.d/
```

2. Review the configurations and modify them as needed. You will need to change server_name and specify ssl certs.

### TorrServer

1. Clone or download the TorrServer-slim repository:

```bash
git clone https://github.com/etidart/TorrServer-slim
cd TorrServer-slim/server
```

2. Build the TorrServer:

```bash
go build -o /usr/local/bin/torrserver cmd/main.go
```

3. Copy the service file:

```bash
cp ../torrserver.service /etc/systemd/system
```

4. Modify the service file and enable the service.

## Usage

Be sure that you have ability to add `passkey` cookie and specified allowed tokens in `/etc/nginx/tokens.conf` matching the scheme.

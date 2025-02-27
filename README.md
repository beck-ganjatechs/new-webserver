# New webserver

Scripts and config files to quickly start a new webserver that has:

- ufw
- letsenscrypt ssl cert
- Diffie-Hellman parameters
- nginx with ssl properly configured
- docker
- postgres via docker

Assumes you are logged in as root.

# nodejs

    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash
    export NVM_DIR="$HOME/.nvm"
    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
    [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
    nvm install 17.3.0

# UFW

    ufw default deny incoming
    ufw default allow outgoing
    ufw allow 22  # ssh
    ufw allow 443 # https
    ufw allow 80  # http
    # allow port from specific IP
    # ufw allow from 1.1.1.1 to any port 22
    # allow port from specific interface
    # ufw allow in on eth0 to any port 80
    ufw enable

# Nginx

    apt-get install nginx # todo: nginx official repo
    openssl dhparam -out /etc/nginx/dhparam.pem 2048 # Diffie-Hellman parameters
    cd /etc/nginx/conf.d
    wget https://raw.githubusercontent.com/askmike/new-webserver/master/site.conf
    # edit nginx conf with your site and api
    service nginx configtest
    service nginx restart

# Nginx /w certbot

    apt-get update
    apt-get install nginx certbot python-certbot-nginx
    openssl dhparam -out /etc/nginx/dhparam.pem 2048 # Diffie-Hellman parameters
    cd /etc/nginx/conf.d
    wget https://raw.githubusercontent.com/askmike/new-webserver/master/csite.conf
    # edit nginx conf with your domain
    service nginx configtest
    service nginx restart
    sudo certbot --nginx -d domain
    [email]
    a
    y
    2
    wget https://raw.githubusercontent.com/askmike/new-webserver/master/csite2.conf
    rm csite.conf
    mv csite2.conf site.conf
    # edit nginx conf with your site and api
    service nginx configtest
    service nginx restart
    crontab -e
    # add txt:
    0 12 * * * /usr/bin/certbot renew --quiet

# Build tools

    apt-get install python python3 make build-essential

# docker

    apt-get update
    apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    # apt-key fingerprint 0EBFCD88
    add-apt-repository    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
       $(lsb_release -cs) \
       stable"
    apt-get update
    apt-get install docker-ce docker-ce-cli containerd.io

## docker postgres

    cd ~
    mkdir postgresdata
    docker run --rm   --name pg-docker -e POSTGRES_PASSWORD=YOUR-PW -d -p 5432:5432 -v $HOME/postgresdata:/var/lib/postgresql/data  postgres
    docker ps

# Amazon EC2 instance Setup for http to https

## Instance Configuration
1. **Choose Ubuntu** (any size)
2. **Security Group**: Allow all traffic for SSH, HTTP, and HTTPS (also add inbound for port 8501 for all IP)
3. **Storage**: Any size (I use 40GB)

## Streamlit Setup

1. SSH into your instance and run the following commands:

    ```bash
    sudo apt-get update
    sudo apt upgrade -y
    ```

2. **Setup a basic Streamlit application on localhost:**

    ```bash
    python3 -m venv venv
    ```

    ⚠️ **Warning**: You may see the following error:

    ```
    The virtual environment was not created successfully because ensurepip is not
    available.  On Debian/Ubuntu systems, you need to install the python3-venv
    package using the following command.
    ```

    Follow the instructions and install the package using:

    ```bash
    sudo apt install python3.12-venv -y
    ```

3. **Continue installing the packages and create the `.env` file:**

    ```bash
    . venv/bin/activate
    pip install streamlit
    ```

4. **Create a simple Streamlit application:**

    ```bash
    echo -e 'import streamlit as st\n\nst.write("hello world")' > app.py
    ```

5. **Run the application:**

    ```bash
    streamlit run app.py
    ```

    Open the External URL in your browser. You should see "hello world".

## Setup HTTP Access

1. **Install nginx:**

    ```bash
    sudo apt-get install nginx -y
    ```

2. **Clear the current configuration of nginx:**

    ```bash
    sudo sh -c 'echo -n > /etc/nginx/sites-available/default'
    ```

3. **Open the nginx config file:**

    ```bash
    sudo vi /etc/nginx/sites-available/default
    ```

4. **Paste the following configuration:**

    ```nginx
    map $http_upgrade $connection_upgrade {
        default upgrade;
        '' close;
    }

    upstream backend {
        server 127.0.0.1:8501;
        keepalive 64;
    }

    server {
        listen 80;
        server_name _;
        location / {
            proxy_pass http://backend;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection $connection_upgrade;
            proxy_set_header Host $host;
            proxy_cache_bypass $http_upgrade;
        }
    }
    ```

5. **Restart your nginx server:**

    ```bash
    sudo service nginx restart
    ```

    You should now be able to view your external URL without the port number.

## Custom Domain on HTTP

1. **Sign up for an account on noip.com**

2. **Create a host name** (e.g., `yourdomain.noip.com`) and enter the EC2 External URL for the IPv4 address requested (e.g., `3.91.241.72`).

3. **Navigate to your new custom domain** to see your Streamlit application.

## HTTPS

1. **Install certbot:**

    ```bash
    sudo apt install certbot -y
    ```

2. **Stop Nginx so certbot can run:**

    ```bash
    sudo service nginx stop
    ```

3. **Create a certificate for your domain:**

    ```bash
    sudo certbot certonly --standalone -d domain name
    ```

4. **Start nginx again:**

    ```bash
    sudo service nginx start
    ```

5. **Clear the current configuration of nginx:**

    ```bash
    sudo sh -c 'echo -n > /etc/nginx/sites-available/default'
    ```

6. **Open the nginx config file:**

    ```bash
    sudo vi /etc/nginx/sites-available/default
    ```

7. **Paste the following configuration** (replace `domain name` with your custom domain):

    ```nginx
    map $http_upgrade $connection_upgrade {
        default upgrade;
        '' close;
    }

    upstream backend {
        server 127.0.0.1:8501;
        keepalive 64;
    }

    server {
        listen 80;
        server_name domain name;

        location / {
            return 301 https://$host$request_uri;
        }
    }

    server {
        listen 443 ssl;
        server_name domain name;

        ssl_certificate /etc/letsencrypt/live/domain name/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/domain name/privkey.pem;

        location / {
            proxy_pass http://backend;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection $connection_upgrade;
            proxy_set_header Host $host;
            proxy_cache_bypass $http_upgrade;
        }
    }
    ```

8. **Restart your nginx server:**

    ```bash
    sudo service nginx restart
    ```

    You should now be able to view your Streamlit application over HTTPS.

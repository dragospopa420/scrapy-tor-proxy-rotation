# Scrapy Tor Proxy Rotation
This module allows Scrapy to rotate Tor IPs.

## Install

Simple install, via **pip**:
```bash
pip install scrapy-tor-proxy-rotation
```

## Config Tor
To configure **Tor**. First, install :

```bash
sudo apt-get install tor
```

Stop its execution to make configurations:

```bash
sudo service tor stop
```

Open your configuration file as root, available in */etc/tor/torrc*, for example, using nano:

```bash
sudo nano /etc/tor/torrc
```
Place the lines below and save:

```
ControlPort 9051
CookieAuthentication 0
```

Restart Tor:

```bash
sudo service tor start
```


It is possible to verify the IP of your machine and compare it as Tor in the following way:
- To see your IP:
    ```bash
    curl http://icanhazip.com/
    ```
- To see the ip of Tor:
    ```bash
    torify curl http://icanhazip.com/   
    ```

For Scrapy it is necessary to use an intermediary, in this case or **[Privoxy](https://www.privoxy.org/)**. 

> Tor Default Proxy Server: 127.0.0.1:9050

## Install and Config **Privoxy**:
- Install: 
    ```bash
    sudo apt install privoxy
    ```
- Stop the service:
    ```bash
    sudo service privoxy stop
    ```
- Open the config file:
    ```bash
    sudo nano /etc/privoxy/config
    ```
- Add the following lines:
    ```bash
    forward-socks5t / 127.0.0.1:9050 .
    ``` 
- Start the service: 
    ```
    service privoxy start
    ```

Test: 
```bash
torify curl http://icanhazip.com/
```
```bash
curl -x 127.0.0.1:8118 http://icanhazip.com/
```

## Use

After performing these configurations, it is possible to integrate Tor with Scrapy.
- Configure the middleware in your settings file (**settings.py**):
    ```python
    DOWNLOADER_MIDDLEWARES = {
        ...,
        'scrapy.downloadermiddlewares.httpproxy.HttpProxyMiddleware': 110,
        'tor_ip_rotator.middlewares.TorProxyMiddleware': 100
    }
    ```
    
- Add those in your custom_settings in your spider or in (**settings.py**) if you want to use them on all spiders from the project:  
    ```python
    TOR_IPROTATOR_ENABLED = True
    TOR_IPROTATOR_CHANGE_AFTER = #número de requisições feitas em um mesmo endereço IP
    ```
By default, an IP can be reused after 10 other uses. This value can be altered by the variable TOR_IPROTATOR_ALLOW_REUSE_IP_AFTER, as below:

```python
TOR_IPROTATOR_ALLOW_REUSE_IP_AFTER = #
```

A large number can also make it slower to retrieve a new IP to use or find. If the value is 0, there will be no record of used IPs.

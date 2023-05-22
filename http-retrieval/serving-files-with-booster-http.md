---
description: Configuring booster-http to serve blocks and files
---

# Serving files with booster-http

With the release v1.7.0-rc1 of booster-http, Storage Providers can now serve blocks and files directly over the HTTP protocol. `booster-http` now implements a [IPFS HTTP gateway](https://docs.ipfs.tech/concepts/ipfs-gateway/#overview) with a [path resolution style](https://specs.ipfs.tech/http-gateways/path-gateway/). This will allow the clients to download individual [IPFS blocks](https://docs.ipfs.tech/how-to/work-with-blocks/), car files and request uploaded files directly from their browser.

SPs can take advantage of the ecosystem of tools to manage HTTP traffic, like load balancers and reverse proxies.

{% embed url="https://youtu.be/P8Yhy_naQlk" %}
Browsing deal data served via booster-http from web browser
{% endembed %}

{% hint style="danger" %}
Before proceeding any further, we request you to read [basics of HTTP retrieval configuration](./). This section is an extension of HTTP retrievals and deals with configuration specific to serving files and raw blocks.
{% endhint %}

## Configuring what to serve

The `booster-http` service can be started with specific type of content on IPFS gateway API

```
   --serve-pieces                                           enables serving raw pieces (default: true)
   --serve-blocks                                           serve blocks with the ipfs gateway API (default: true)
   --serve-cars                                             serve CAR files with the ipfs gateway API (default: true)
   --serve-files                                            serve original files (eg jpg, mov) with the ipfs gateway API (default: false)
   --api-filter-endpoint value                              the endpoint to use for fetching a remote retrieval configuration for bitswap requests
   --api-filter-auth value                                  value to pass in the authorization header when sending a request to the API filter endpoint (e.g. 'Basic ~base64 encoded user/pass~'
   --badbits-denylists value [ --badbits-denylists value ]  the endpoints for fetching one or more custom BadBits list instead of the default one at https://badbits.dwebops.pub/denylist.json (default: "https://badbits.dwebops.pub/denylist.json")
   --help, -h                                               show help
```

This allows SPs to run multiple `booster-http` instances, each serving specific type of content like car files only or raw blocks only.

## Enable serving files

In the curl request below we appended the query parameter format=raw to the URL to get the raw block data for the file.

```
$ curl --output /tmp/museum.jpg "http://localhost:7777/ipfs/bafybeidqindpi4ucx7kmrtnw3woc6jtl7bqvyiokrkpbbuy6gs6trn57tm/vincent/Vincent%20van%20Gogh_files/Caf%C3%A9tafel_met_absint_-_s0186V1962_-_Van_Gogh_Museum.jpg?format=raw"
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 11830  100 11830    0     0   140k      0 --:--:-- --:--:-- --:--:--  175k

$ open /tmp/museum.jpg
```

But, if we try to open the file directly in a web browser, with no extra query parameters, we get an error message:

<figure><img src="https://lh3.googleusercontent.com/Ztp7SEG5XUL-1j2hhgg_QBeJDWqcz_hFjYLE174f1T3HxrI3_O6yXci8cSsxkLQicszSqQs6pZbDbPXVCtHu-rHiAMEX9ObAUMLygp-8rDF97Fc_fxBbBpOw1upFvoB43kq6AdKqrJzFbJ0_JbZ_Rtk" alt=""><figcaption><p>Error when accessing IPLD block from web browser</p></figcaption></figure>

By default `booster-http` does not serve files in a format that can be read by a web browser. This is to protect Storage Providers from serving content that may be [flagged as illicit content](https://blog.nft.storage/posts/2022-04-29-gateways-and-gatekeepers).

To enable serving files to web browsers, we must pass `--serve-files=true` to `booster-http` on startup. Once, `booster-http` is restarted with `--serve-files=true,` we can open the file directly from a web browser:

<figure><img src="https://lh5.googleusercontent.com/4kDeP4XYol4NtcIDMYM_LSQaLBMI-_okjSS9cD4NbwfzN9BVBi7SG-R3TV5kbrc-oD4z0Q9M4lmlQDovrTzctgQ6hwL1-ax0g5rFsCfrnOj-Xj3aDZwPn8vIjUpEJngy7DFAoc83v29gp5o63WQREeI" alt=""><figcaption><p>Accessing files from Filecoin SP via web browser</p></figcaption></figure>

{% hint style="danger" %}
`booster-http` (and `booster-bitswap`) automatically filter out known flagged content using the denylist maintained at [https://badbits.dwebops.pub/denylist.json](https://badbits.dwebops.pub/denylist.json)
{% endhint %}

We can also browse all files in the CAR archive.

<figure><img src="https://lh4.googleusercontent.com/rsuZ86VU0fD_zWqvHYflkoa6hqEkFe3zJ1SHlB_X_iIsXRONFoldkH9cbOayAv2KFSY-6tw7MQO6ENC6lWIKVldBQYsPKWweQw845xXSvrqB-j-YWKFRHBlX9ak3CVjp5Is4MRHuNcva0LLnAg0UH6Q" alt=""><figcaption><p>Browsing files in a deal with a web browser</p></figcaption></figure>

## Protecting booster-http with NGINX

SPs must secure their `booster-http` before exposing it to the public. SPs can feel free to use any tool available to limit who can download files, the number of requests per second, and the download bandwidth each client can use per second.

Users can follow this example to use [NGNIX](https://nginx.org/en/docs/) reverse proxy to secure their `booster-http` instance. In this section we’ve just scratched the surface of the ways in which nginx can set access limits, rate limits and bandwidth limits. In particular it’s possible to add limits by request token, or using JWT tokens. The examples in this section are adapted from [Deploying NGINX as an API Gateway](http://nginx.com/blog/deploying-nginx-plus-as-an-api-gateway-part-1) which goes into more detail.

By default nginx puts configuration files into `/etc/nginx`

The default configuration file is `/etc/nginx/sites-available/default`

### Setup server block

Setup nginx server listen on port `7575` and forward requests to booster-http on port `7777`

The IPFS gateway serves files from `/ipfs`. So, we will add a server block for location /ipfs/

```
# ipfs gateway config
server {
        listen 7575 default_server;
        listen [::]:7575 default_server;

        location /ipfs/ {
                proxy_pass http://127.0.0.1:7777;
        }
}
```

### Limiting Access

Let’s limit access to the IPFS gateway using the standard `.htaccess` file. We need to set up an `.htaccess` file with a username and password. Create a user named _`alice`_

```
$ mkdir /etc/nginx/ipfs-gateway.conf.d

$ htpasswd -c /etc/nginx/ipfs-gateway.conf.d/.htpasswd alice
New password:
Re-type new password:
Adding password for user alice
```

Include the `.htaccess` file in the `/etc/nginx/sites-available/default`

```
 # ipfs gateway config
server {
        listen 7575 default_server;
        listen [::]:7575 default_server;

        location /ipfs/ {
        # htaccess authentication
                auth_basic "Restricted Server";
                auth_basic_user_file /etc/nginx/ipfs-gateway.conf.d/.htpasswd;
                proxy_pass http://127.0.0.1:7777;
        }
}
```

Now when we open any URL under the path `/ipfs` we will be presented with a Sign in dialog.

<figure><img src="../.gitbook/assets/secure_login.jpg" alt=""><figcaption><p>Login prompt when accessing booster-http url</p></figcaption></figure>

### Rate Limiting

To prevent users from making too many requests per second, we should add rate limits.

1. Create a file with the rate limiting configuration at `/etc/nginx/ipfs-gateway.conf.d/ipfs-gateway.conf`
2. Add a request zone limit to the file of 1 request per second, per client IP

```
limit_req_zone $binary_remote_addr zone=client_ip_10rs:1m rate=1r/s;
```

3. Include `ipfs-gateway.conf` in `/etc/nginx/sites-available/default` and set the response for too many requests to _HTTP response code 429_

```
include /etc/nginx/ipfs-gateway.conf.d/ipfs-gateway.conf;
server {
        listen 7575 default_server;
        listen [::]:7575 default_server;

        location /ipfs/ {
                # htaccess authentication
                auth_basic "Restricted Server";
                auth_basic_user_file /etc/nginx/ipfs-gateway.conf.d/.htpasswd;

                limit_req zone=client_ip_10rs;
                limit_req_status 429;
                proxy_pass http://127.0.0.1:7878;
        }
}

```

4. Click the refresh button in your browser on any path under /ipfs more than once per second you will see a 429 error page

<figure><img src="https://lh6.googleusercontent.com/d-wTXF49wBDA8q-RiTRmZ7tAW4ZnH8nNRvpMxilJwr9hgyhtRemO5cceBfveUqsDUYEA1fINOyEOrHKVX0eL4WS8U9FjoKzYNNjef6OZl_FlrlQsg-slRftPCClEJLq9MxvF7EIwQKgfhJRVKx2Fz1M" alt=""><figcaption><p>HTTP error 429</p></figcaption></figure>

### Bandwidth Limiting

It is also recommended to limit the amount of bandwidth that clients can take up when downloading data from `booster-http`. This ensures a fair bandwidth distribution to each client and prevents situations where one client ends up choking the `booster-http` instance.

1. Create a new .htaccess user called _`bob`_

```
$ htpasswd /etc/nginx/ipfs-gateway.conf.d/.htpasswd bob
```

2. Add a mapping from `.htaccess` username to bandwidth limit in `/etc/nginx/ipfs-gateway.conf.d/ipfs-gateway.conf`

```
map $remote_user $bandwidth_limit {
    default  1k;
    "alice"  10k;
    "bob"    512k;
}
```

3. Add the bandwidth limit to `/etc/nginx/sites-available/default`

```
include /etc/nginx/ipfs-gateway.conf.d/ipfs-gateway.conf;
server {
        listen 7575 default_server;
        listen [::]:7575 default_server;

        location /ipfs/ {
                # htaccess authentication
                auth_basic "Restricted Server";
                auth_basic_user_file /etc/nginx/ipfs-gateway.conf.d/.htpasswd;

                limit_rate $bandwidth_limit;

                limit_req zone=client_ip_10rs;
                limit_req_status 429;
                proxy_pass http://127.0.0.1:7878;
        }
}
```

4. To verify bandwidth limiting, use `curl` to download a file with user _`alice`_ and then _`bob`_\
   Note the difference in the Average Dload column (the average download speed).

<figure><img src="../.gitbook/assets/download_speed.jpg" alt=""><figcaption><p>Bandwidth limiting result</p></figcaption></figure>

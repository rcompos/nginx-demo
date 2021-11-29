# Nginx Demo

## Objective

* Leverages the AWS ecosystem
* Deploys an nginx server
* Configures the nginx server to serve a custom “hello world” page. You can think of this as the “app”.

## POC

The nginx service is available at this url.

[https://nginx-demo.3u6rc4jp6k002.us-west-2.cs.amazonlightsail.com/](https://nginx-demo.3u6rc4jp6k002.us-west-2.cs.amazonlightsail.com/)

## Nginx

Nginx is deployed as a Docker container using the default Dockerhub image.

A new image is created with a custom Nginx default.conf file for reverse-proxy server. The nginx server will proxy requests to an S3 bucket with web service.

```
    location / {
        # root   /usr/share/nginx/html;
        proxy_pass http://demo-omed.s3-website-us-west-2.amazonaws.com/;
        index  index.html index.htm;
    }
```

Dockerfile is as follows:

```
FROM nginx:latest
RUN rm /etc/nginx/conf.d/default.conf
COPY ./default.conf /etc/nginx/conf.d/default.conf
```

Build the container image as nginx.

```
docker build -t nginx .
```

[https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/)

## AWS LightSail

AWS LightSail was selected to host the nginx container.  The service can be created with AWS console or CLI.

The LightSail container service configuration is in the file _aws-lightsail-nginx-demo.json_.

Push the container image into LightSail registry.

```
aws lightsail push-container-image --region us-west-2 --service-name nginx-demo --label nginx --image nginx:latest
```

[https://aws.amazon.com/getting-started/hands-on/lightsail-containers/]()

## AWS S3

The web content is stored in an AWS S3 bucket. New content is delivered by pushing new files into S3.

```
aws s3api create-bucket --bucket demo-omed --region us-west-2
aws s3 cp index.html s3://demo-omed/index.html
aws s3 cp 50x.html s3://demo-omed/50x.html
Create read all bucket policy s3-bucket-policy-read-all.json
```

The S3 bucket is configured for static website hosting.

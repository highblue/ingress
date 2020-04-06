# Introduction 对于KIC中的描述的总结

主要的参考文件
References files
https://github.com/nginxinc/kubernetes-ingress/tree/master/examples/complete-example

Modification based on self-test purpose. You should consult that link above to go through all the steps before dive in.
1. Based on NGINX Plus. Remove the related nginx description.
2. You need to create the NGINX Plus ingress image.

Demo ENV
Docker Desktop with Kubernetes installed.
Don't forget to configure the preference in the Docker Desktop to enable

```
{
  "insecure-registries": [
    "nexus.local:8082",
    "nexus.local:8083"
  ],
  "debug": true,
  "experimental": true
}
```

## Quick steps summary

In this example we deploy the NGINX Plus Ingress controller, a simple web application and then configure load balancing for that application using the Ingress resource.

## Running the Example

## 1. Deploy the Ingress Controller

1. Follow the installation [instructions](https://docs.nginx.com/nginx-ingress-controller/installation/installation-with-manifests/) to deploy the Ingress controller.

2. Save the public IP address of the Ingress controller into a shell variable:
    ```
    $ IC_IP=XXX.YYY.ZZZ.III
    or
    $ IC_IP=127.0.0.1
    ```
    ⚠️ Because I test on my local machine, so I changed this to 127.0.0.1
3. Save the HTTPS port of the Ingress controller into a shell variable:
    ```
    $ IC_HTTPS_PORT=<port number>
    ```
    you will get this port number when you deploy the NodePort in previous steps.

## 2. Deploy the Cafe Application 部署Cafe应用

Create the coffee and the tea deployments and services:
```
$ kubectl create -f cafe.yaml
```

## 3. Configure Load Balancing

1. Create a secret with an SSL certificate and a key:
    ```
    $ kubectl create -f cafe-secret.yaml
    ```

2. Create an Ingress resource:
    ```
    $ kubectl create -f cafe-ingress.yaml
    ```

## 4. Test the Application 测试

1. To access the application, curl the coffee and the tea services. We'll use ```curl```'s --insecure option to turn off certificate verification of our self-signed
certificate and the --resolve option to set the Host header of a request with ```cafe.example.com```
    ⚠️ 这里做测试的时候，如果是localhost也会出问题。所以必须是127.0.0.1
    To get coffee:
    ```
    $ curl --resolve cafe.example.com:$IC_HTTPS_PORT:$IC_IP https://cafe.example.com:$IC_HTTPS_PORT/coffee --insecure
    Server address: 10.12.0.18:80
    Server name: coffee-7586895968-r26zn
    ...
    ```
    If your prefer tea:
    ```
    $ curl --resolve cafe.example.com:$IC_HTTPS_PORT:$IC_IP https://cafe.example.com:$IC_HTTPS_PORT/tea --insecure
    Server address: 10.12.0.19:80
    Server name: tea-7cd44fcb4d-xfw2x
    ...
    ```

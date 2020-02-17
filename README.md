# API Gateway Kong (DB less Mode) with UI (Konga)
This project aims to showcase the various capabilities of Kong API Gateway and Konga (Additional UI for Kong). This project uses Kong in DB Less mode using "kong.yml" file .

## Note: Kong with DB less mode is not recommended since it cannot store dynamic configurations which needs database like "oauth2.0" 


## Kong
Kong is a widely adopted, open source API Gateway, written in Lua. It runs on top of Nginx, leveraging the OpenResty framework and provides a simple RESTful API that can be used to provision your infrastructure in a dynamic way.
Kong is referred to as microservices API Gateway.


### Kong is available in both community and Enterprise Edition:

![alt text](https://github.com/dipsscor/API-Gateway-Kong/blob/master/screenshots/version_comparision.png)

## Konga
Konga is a fully featured open source, multi-user GUI, that makes the hard task of managing multiple Kong installations a breeze.
It can be integrated with some of the most popular databases out of the box and provides the visual tools you need to better understand and maintain your architecture.



![alt text](https://github.com/dipsscor/API-Gateway-Kong/blob/master/screenshots/architecture.png)


# Setup - Configurations (Docker)


## Kong and Konga with Docker Compose

This Docker Compose template provisions a Kong container in DB Less mode, plus a nginx load-balancer. After running the template, the `nginx-lb` load-balancer will be the entrypoint to Kong.

To run this template execute:

    ```shell
    $ docker-compose up
    ```

    

Kong will be available through the `nginx-lb` instance on port `8000`, and `8001`. You can customize the template with your own environment variables or datastore configuration.

Kong's documentation can be found at [https://docs.konghq.com/][kong-docs-url].

The docker-compose template for Kong and Konga are available at:

    https://gist.github.com/pantsel/73d949774bd8e917bfd3d9745d71febf





## Kong in DB Less mode
For Kong to run in DB-less mode there are 2 requirements:
    
          KONG_DATABASE: "off"
          KONG_DECLARATIVE_CONFIG: /kong.yml # Kong configuration YMl file.




# Feature - Configurations 

### Kong Admin API is available at : http://localhost:8001 , https://localhost:8444 
### Kong Gateway is avaliable at : http://localhost:8000, https://localhost:8443

### Konga UI is avaliable at : http://localhost:1337



## Stage 1. Konga setup

    1. Browse to http://localhost:1337
    2. Create a admin user with username , email and password.
    3. Login to Konga
    4. Point to Kong connection :
            - Name : Any Name (e.g Kong)
            - URL : http://kong:8001/   ( since using docker-compose the Kong service is exposed to other containers as  "Kong" hostname)


## Stage 2. User setup

    1. Create some users in the user sections of Konga
    

## Stage 3. features
All configurations are mentioned in Kong.yml file

### Services and Routes:

Service:

    services:
    - name: Testing-service
      url: https://jsonplaceholder.typicode.com/  # Upstream server URL
      path: /API-V1.0/

Route:

      routes:
      - name: posts
        paths:
        - /
        methods:
        - GET
        - POST
        - DELETE
        - PUT
        - PATCH


### Plugins:

Read about plugin configs from : http://konghq.com/plugins


### Testing Plugins:

Test the plugins as mentioned in : https://github.com/dipsscor/API-Gateway-Kong


# References

    [kong-site-url]: https://konghq.com/
    [kong-docs-url]: https://docs.konghq.com/
    [github-new-issue]: https://github.com/Kong/docker-kong/issues/new
    https://medium.com/@tselentispanagis/managing-microservices-and-apis-with-kong-and-konga-7d14568bb59d
    https://gist.github.com/pantsel/73d949774bd8e917bfd3d9745d71febf
    https://docs.konghq.com/hub/
    https://github.com/Kong/kong-oauth2-hello-world
    https://medium.com/@far3ns/kong-oauth-2-0-plugin-38faf938a468
    https://docs.konghq.com/hub/kong-inc/jwt/
    
    

# API Gateway Kong with UI (Konga)
This project aims to showcase the various capabilities of Kong API Gateway and Konga (Additional UI for Kong). This project uses Postgres as DB for Kong and Kong configuration stores. 

There is a DB Less KONG configuration as well in next project.

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

This Docker Compose template provisions a Kong container with a Postgres database, plus a nginx load-balancer. After running the template, the `nginx-lb` load-balancer will be the entrypoint to Kong.

To run this template execute:

    ```shell
    $ docker-compose up
    ```

To scale Kong (ie, to three instances) execute:

    ```shell
    $ docker-compose scale kong=3
    ```

Kong will be available through the `nginx-lb` instance on port `8000`, and `8001`. You can customize the template with your own environment variables or datastore configuration.

Kong's documentation can be found at [https://docs.konghq.com/][kong-docs-url].

The docker-compose template for Kong and Konga are available at:

    https://gist.github.com/pantsel/73d949774bd8e917bfd3d9745d71febf





## Kong and Konga without Docker-compose
Follow the instructions to provision Kong and Konga without docker-compose on:

    https://medium.com/@tselentispanagis/managing-microservices-and-apis-with-kong-and-konga-7d14568bb59d




# Feature - Configurations 

### Kong Admin API is available at : http://localhost:8001 
### Kong Gateway is avaliable at : http://localhost:8000

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
    

## Stage 3. Services and Routes creation

For this POC, we’re going to use the excelent online fake API for testing and prototyping provided by "jsonplaceholder.typicode.com"


    1. Go to the Services section and create a new Service "Testing-Service"
    
          Config:
          
            - Protocol: "https" 
            
            - Host: "jsonplaceholder.typicode.com" ## For this POC we are using "jsonplaceholder.typicode.com" which provides some rest APIs.
            
            - Path: /API-V1.0  ## Path that would be used to get the endpoints : http://localhost:8000/API-V1.0/<route -path>
            
Note: Here Upstream server means the host and endpoint of the actual service.

    2. Go to routes section and create routes for following endpoints
            /users
            /comments
            /posts
            
            
          Config:   
            - Name: Give the name of the route
            
            - methods: Mention the HTTP methods ( GET, POST , PUT , PATCH , DELETE) in caps and press return. 
            
            - protocols: Select the protocols (HTTP,HTTPS) and press return.

Save the configurations and test the services at with all HTTP methods:

    http://locahost:8000/API-V1.0/users
    http://locahost:8000/API-V1.0/comments
    http://locahost:8000/API-V1.0/posts

The snapshot for service configuration can be found in snapshots --> service_route_configuration folder.



## Stage 4. 
## Authentication - Basic

        1. Go the service created in Stage 3.
        2. Go to Plugins --> Add Plugin -->Authentication --> Basic Auth
        3. leave all fields and "add plugin"
        4. The Basic Auth plugin is created and applied to all "consumers" of the service with their usename/pwd.
        
        5. Go to "consumers" in left pane and create 2 consumers.
        
            - username: Give the username e.g. "mike , john" and submit
            - Create Basic credentials for the newly created consumers of the services
            - Credentials --> Basic Auth -->username: "mike/john"-->password--> "password"
            
        6. Go back to "services" section --> eligible consumers --> check both the consumers "mike /john" are added.
        
Test the services by passing the credentials {Username and Password} in the header of the requests.

### Postman Logs:
    Without Authentication:
    
            GET http://localhost:8000/API-V1.0/comments

            {"message":"Unauthorized"}
            
    With Username and password:
    
            GET /API-V1.0/comments HTTP/1.1
            Authorization: Basic bWlrZTpwYXNzd29yZA==
            
            HTTP/1.1 200 OK



## Authentication - Key Auth

        1. Go the service created in Stage 3.
        2. Go to Plugins --> Add Plugin -->Authentication --> Key Auth
        3. Fill the form
            - key names : "apikey" ## Enter any key name that would be used as key name when adding to header
            - Key in body : Incase you want to supply key in body , enable this field
        4. The Key Auth plugin is created and applied to all "consumers" of the service with their api keys.
        
        5. Go to "consumers" in left pane and create 2 consumers.
        
            - username: Give the username e.g. "mike , john" and submit
            - Create Key Auth credentials for the newly created consumers of the services
            - Credentials--> API keys -->leave blank for Kong to generate the key.
            
        6. Go back to "services" section --> eligible consumers --> check both the consumers "mike /john" are added.
        
        
Test the services by passing the API key credential {apikey:2uASxFLd6bMc2qEgshr7qzvqzXVAZQ1A} in the header of the requests.

### Postman logs:

    With incorrect keys:
    
            GET /API-V1.0/comments HTTP/1.1
            apikey: 1111

            {"message": "Invalid authentication credentials"}
            
    With correct keys:
    
            GET /API-V1.0/comments HTTP/1.1
            apikey: 2uASxFLd6bMc2qEgshr7qzvqzXVAZQ1A
            
            HTTP/1.1 200 OK


## Authentication - OAuth2.0

        1. Go the service created in Stage 3.
        2. Go to Plugins --> Add Plugin -->Authentication --> oauth2
        3. Fill the form
                - scopes : email , phone , address ## press return for each scope
                - mandatory scope: true
                - token expiration : Default value is 7200 milliseconds
                - enable authorization code : true
                
        4. Go to "consumers" in left pane and create 2 consumers.
        
            - username: Give the username e.g. "mike , john" and submit
            - Create oauth2 credentials for the newly created consumers of the services
            - Credentials--> oauth2 -->
            
                    - name : "test_oauth2_app" ## Name of the credential
                    
                    - redirect_uris : e.g. "http://konghq.com/" ## This the redirect URL where the authorization response will come back with credentials. Ideally this is the client application URL requesting for authorization.
                    
                    - client_id : blank to get generated.
                    - client_secret : blank to get generated.
            
        5. Go back to "services" section --> eligible consumers --> check both the consumers "mike /john" are added.



  Test the services by with the following process:
  

  
  #### 1. Authorization ( Get the authorization code):
  Authorize the calling application at the authorization server which is appended to the resource endpoint. The authorization should be done at SSL port which is 8443 for kong. 
  
  The Kong authorization endpoint is:
        
        -/oauth2/authorize
        
        -e.g. POST: https://<service-host>:<ssl-port>//<service-endpoint>/oauth2/authorize
  
        In this case:
        POST: https://localhost:8443/API-V1.0/comments/oauth2/authorize
        
  Send the rest of the data as form-data in the request body:
  
        - provision_key : The provision key generated on the oauth2 plugin added.
        - client_id : consumer oauth2 cred client ID
        - client_secret : consumer oauth2 cred client secret
        - redirect_uri : redirect URL where the authorization response will come back with credentials.
        - scope : comma separated scopes
        - authenticated_userid : consumer user id to be authenticated.
        - response_type : "code" ## since the authorization code will be send back.
        
        
    e.g.
        - provision_key : yWg5F8bXvgFiHR6OKqcqcEWDeNm6ODVM
        - client_id : WITJXt7wXf6AZ5UahwL9caFD3BVgBtl6
        - client_secret : tC3Ypj8aC33vz2sfwhfu8T9hz9hre5wE
        - redirect_uri : http://konghq.com/
        - scope : email
        - authenticated_userid : john
        - response_type : code
        
        
 Response: 
 
        {
            "redirect_uri": "http://konghq.com/?code=d84vNp5pxJbsv5lsevdA36KIR9x3ZyBi"
        }
        
        
   #### 2. Authentication ( Get the access token with the authorization code): 
   
   Get the access token by using the authorization code at the endpoint 
   
        - /oauth2/token
        -e.g. POST: https://<service-host>:<ssl-port>//<service-endpoint>/oauth2/token
  
        In this case:
        POST: https://localhost:8443/API-V1.0/comments/oauth2/token
        
Send the rest of the data as form-data in the request body:

        - grant_type:authorization_code
        - client_id:WITJXt7wXf6AZ5UahwL9caFD3BVgBtl6
        - client_secret:tC3Ypj8aC33vz2sfwhfu8T9hz9hre5wE
        - redirect_uri:http://konghq.com/
        - code:d84vNp5pxJbsv5lsevdA36KIR9x3ZyBi
 
 
 Response: 
 
        {
            "refresh_token": "kzrN5Nrd7vV5JLr6W35E7m5t0uefOMaB",
            "token_type": "bearer",
            "access_token": "Vw4cwDpE3AzbKbJzs7nI5JHqe5wqzJQU",
            "expires_in": 7200
        }
        
        
   #### 3. Access the resource with the token:
   
   Access the resource by sending the authorization token in the header:
   
        GET /API-V1.0/comments/ HTTP/1.1
        Authorization: Bearer Vw4cwDpE3AzbKbJzs7nI5JHqe5wqzJQU
        
        
        HTTP/1.1 200 OK



## Authentication - JWT

        1. Go the service created in Stage 3.
        2. Go to Plugins --> Add Plugin -->Authentication --> jwt
        3. Fill the form
                - uri param names : "jwt" and press return
                - secret is base64: true
                - header names: "authorization" and press return
                
        4. Go to "consumers" in left pane and create 2 consumers.
        
            - username: Give the username e.g. "mike , john" and submit
            - Create jwt credentials for the newly created consumers of the services
            - Credentials--> jwt --> Leave all fields blank and generate the jwt credentials.
            
                eg:

                        {
                          "rsa_public_key": null,
                          "created_at": 1581780408,
                          "consumer": {
                            "id": "6feca0d4-825d-465c-81a5-7ea3de0de2cd"
                          },
                          "id": "6ab1b2f7-6826-4a49-a9b7-26500f8cdadf",
                          "tags": null,
                          "key": "Qfzl2GC8l77osSnio8KuzKD7qbPnLzAu",
                          "secret": "QX17sjBTY50xUHvEFj5JR5BqaQiUvpS2",
                          "algorithm": "HS256"
                        }
            
        5. Go back to "services" section --> eligible consumers --> check both the consumers "mike /john" are added.
        
        6. Craft a JWT with a secret (HS256): 
        
                    - Use http://jwt.io --> jwt debugger -->decoded
                        - Header:

                                {
                                  "alg": "HS256",
                                  "typ": "JWT"
                                }                            
                        - Payload:
                                 {
                                    "iss": "Qfzl2GC8l77osSnio8KuzKD7qbPnLzAu" ## Key from the JWT Cred
                                 }  

                        - Verify Signature:

                                HMACSHA256(
                                  base64UrlEncode(header) + "." +
                                  base64UrlEncode(payload),

                                QX17sjBTY50xUHvEFj5JR5BqaQiUvpS2 ## your-256-bit-secret from the JWT Cred

                                ) secret base64 encoded (checked)
                    
                     - Get the encoded token:
                     "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJRZnpsMkdDOGw3N29zU25pbzhLdXpLRDdxYlBuTHpBdSJ9.Fp-VtgXTFOOWTAO2zlEN8rDv5V03nG-qMS3_lVSo6yw"

Test the services by passing the jwt key credential in the header of the requests.

                     
        GET /API-V1.0/comments/ HTTP/1.1
        Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJRZnpsMkdDOGw3N29zU25pbzhLdXpLRDdxYlBuTHpBdSJ9.Fp-VtgXTFOOWTAO2zlEN8rDv5V03nG-qMS3_lVSo6yw

        HTTP/1.1 200 OK                   




## Stage 5. 
## Security - ACL (Acess Control)
Apply security to previously created Authentication (Basic / key auth / oauth2 / Jwt / etc) plugins which were till not applied to all users. 
When ACL is applied only allowed conusmers will be able to access the services using their creds. ACL works on kong when consumer to assigned to groups and groups are either whitelisted or blacklisted.

        1. Go to consumers and assign them to each groups . e.g John --> API Developer, Mike --> API Manager
        2. Go to services --> Add plugin -- >Security ---> acl
        3. Fill the form :
        
                - Whilelist : "API Developer" ## write the consumer group and press return
                - blacklist : "<black list consumer group>"
        
        4. Check eligble consumers. The consumers belonging to the groups that are not whitelisted cannot access the service.
        
 Test the services by consumer credentials for NOT whitelisted consumers       
       
     Response:    
            {
                "message": "You cannot consume this service"
            }

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
    
    

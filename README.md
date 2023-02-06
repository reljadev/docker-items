# INTRODUCTION
This repo demonstrates basic usages of Angular framework, .Net 6 framework and Keycloak authentication & authorization technologies and how they are integrated
togheter in a simple SPA + REST API application.

# USAGE
## Getting started
To set up the application on your local machine, open your terminal, navigate to the directory containing compose.yml file and run the following command:
```
docker compose up
```
After a few minutes, the application should be fully started, and you can check it out at `http://localhost:4200`. However, if you do this, you'll notice that
nothing actually appears. Indeed, console throws an error. That's because SPA and keycloak aren't integrated yet.

## Keycloak server configuration
### Registering SPA with keycloak
In order to register your web application with your keycloak server, you have to open keycloak admin console. It is available at `http://localhost:8080/admin`.
Credentials for admin are `admin` for both username and password.
There is a default master realm, which keycloak server uses, and we shouldn't modify it. That's why we create our own realm, named `samplerealm`. Note that
it is important to name it `samplerealm` and not something else, because our SPA is configured to seek authentication within that realm.
In `client` section, create a new client with ID `sampleclient`, which is our web application, and select only `Standard Flow` for authentication.
Enabling our client app communication with the keycloak server is done by populating fields like so:
```
valid redirect URIs: http://localhost:4200/*
valid post logout redirect URIs: http://localhost:4200/*
web origins: http://localhost:4200
```
This essentially insures that only our application can request a token, and that user will, post login/logout, be redirected back to our app. Note that it is important
to be as specific as possible here, to protect against another application stealing our authentication token.  
We'll make our app a little bit more secure, by enabling PKCS flow, which guarantees that another app can't get an auth token even if it intercepts the response from
keycloak server. So, navigate to `client` section, `advanced` tab, locate `Proof Key for Code Exchange Challenge Method` and select `S256`.  
After this setup we can navigate back to SPA without a problem, however we can't yet log in because we haven't created our account.

### User registration
We can register new user in two ways, through keycloak admin console or SPA.
First way, is to navigate to `users` section in samplerealm and then create user with your desired info,
and assign him the password in credentials tab.
Second way is to enable user registration. To do this, navigate to `realm settings` section, and under login tab there is
a user registration toggle. Enable it. Now when you click login button in SPA, you'll find an option for registration as well.

## Populating the database
SPA is fully functional right now, but it doesn't have any items entries because our database is empty. In order to populate it,
open postman, navigate to collections tab, and make a new collection, named `Populate catalog database`. In it, make a new request
with the following: 
```
method: POST
url: http://localhost:5214/items
body:
{
  "name": "{{name}}",
  "description": "{{description}}",
  "price": {{price}}
}
```
Now click on the three dots next to the collection, and select `Run Collection` from the drop down menu. Select items-data file
and run the collection.

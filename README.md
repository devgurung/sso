The first thing that we need to do is to decide the SSO protocol that you want to use for the application. There are multiple protocols supported , one of the most common and recommended protocol is Open-ID connect which again supports various flows. 

Now that you are done with the first step, let's go ahead and register your application with the authentication provider
 
 
        There are totally five mandatory fields that need to be filled with the details.
        Asset Insight ID
        Client Name - a + Asset Insight ID + name of application or service to be integrated
        Redirect URIs - URLs to redirect the user to upon successful authentication. Minimum of 1 required
        Scopes -  scopes needed for authentication
        Grant Type -Grant types to allow for authentication 
        Note - Please tick the refresh token option in order to avoid this issue.

 

Now that our application is registered with the Authentication provider, we need to implement the "authorization code" flow so that the user can be authenticated by the SSO while accessing our application.

 
The below diagram nicely summarizes the authorization code flow that we have implemented for BermudaDIY.

![Image of Yaktocat](https://github.com/devgurung/sso/blob/master/pastedImage_2.png)
 

  

 In the above diagram, consider App XYZ as slack application and Service ABC Authorization Server as an Keycloak server

 

            Step 1.  slack application registered with the eSSO server using the Keycloak admin console.  User credentials are already stored with the Keycloak server.

 

            Step 2.  The first time the user tries to access the slack web page, the app makes an authorization request to Service's authorization endpoint with the following details 

 

https://{sso_server}/as/authorization.oauth2?
client_id=a29999-MyApp&
response_type=code&
scope=openid%20profile&
redirect_uri=https://slack.com/redirecturi

 
Note: redirect_uri that are being passed should exactly match with what you had given while registering the application with Keycloak server or else Keycloak server will throw "Invalid redirect_url" error.

             

               Step 3.  Once your app hits the authorization endpoint, the keycloak server returns an authorization page.

 

               Step 4.  App now displays the authorization page.

 

               Step 5.  The user now checks the requested permissions like reading the profile etc. and inputs the username and password and finally approves the authorization request.

 

               Step 6.   The auth server now issues a short-lived authorization code.

 

               Step 7.    In the next step, we have to send a POST request to the token endpoint URL with the auth code and secret id received in the previous step.

 

https://{sso_Server}/as/token.oauth2

                Step 8.   Finally, the auth server presents the access token and requests resources.  

 

                Step 9.   Now that we have access token with us, the same can be used to hit user info endpoint in order to get user details( email, first name, last name, address, etc)

 

https://{sso_server}/idp/userinfo.openid?access_token={access_token}


Note - All applications integrating with eSSO need to support HTTPS for the OAuth calls.

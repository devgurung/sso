The first thing that we need to do is to decide the SSO protocol that you want to use for the application. There are multiple protocols supported by eSSO, one of the most common and recommended protocol is Open-ID connect which again supports various flows. Since BermudaDIY is a web application that runs on a Web Server, we have selected "authorization_code" as the Oauth flow for user authentication.

Now that you are done with the first step, let's go ahead and register your application with the authentication provider which in this case is eSSO using the link.

 

 

    There are totally five mandatory fields that need to be filled with the details.
        Asset Insight ID
        Client Name - a + Asset Insight ID + name of application or service to be integrated
        Redirect URIs - URLs to redirect the user to upon successful authentication. Minimum of 1 required
        Scopes -  scopes needed for authentication
        Grant Type -Grant types to allow for authentication 
        Note - Please tick the refresh token option in order to avoid this issue.

 

 

    The sample request submitted for BermudaDIY is in the link.

 

 

    Once submitted, immediately go the "My Tasks" in service now and get the client id and secret from the private Data source tab.

 

 

    Now that our application is registered with the Authentication provider, we need to implement the "authorization code" flow so that the user can be authenticated by the eSSO while accessing our application.

 

 

    The below diagram nicely summarizes the authorization code flow that we have implemented for BermudaDIY.

 

  

 In the above diagram, consider App XYZ as BermudaDIY application and Service ABC Authorization Server as an eSSO server

 

            Step 1.  BermudaDIY application registered with the eSSO server using the ServiceNow request.  User credentials are already stored with the eSSO server.

 

            Step 2.  The first time the user tries to access the BermudaDIY web page, the app makes an authorization request to Service's authorization endpoint with the following details 

 

https://sso.thomsonreuters.com/as/authorization.oauth2?
client_id=a29999-MyApp&
response_type=code&
scope=openid%20profile&
redirect_uri=https://mycoolapp.thomsonreuters.com/redirecturi

 

      Note: redirect_uri that are being passed should exactly match with what you had given while registering the application with eSSO or else eSSO server will throw "Invalid redirect_url" error.

             

               Step 3.  Once your app hits the authorization endpoint, the eSSO server returns an authorization page.

 

               Step 4.  App now displays the authorization page.

 

               Step 5.  The user now checks the requested permissions like reading the profile etc. and inputs the username and password and finally approves the authorization request.

 

               Step 6.   The auth server now issues a short-lived authorization code.

 

               Step 7.    In the next step, we have to send a POST request to the token endpoint URL with the auth code and secret id received in the previous step.

 

https://sso.thomsonreuters.com/as/token.oauth2

                Step 8.   Finally, the auth server presents the access token and requests resources.  

 

                Step 9.   Now that we have access token with us, the same can be used to hit user info endpoint in order to get user details( email, first name, last name, address, etc)

 

https://sso.thomsonreuters.com/idp/userinfo.openid?access_token={access_token}

 

 

    https://github.com/tr/esso_node_sample  - This is the sample node application that has the full authorization code flow already implemented using the third party library.

 

 

 

Issues we faced and how it was solved

 

    "Invalid redirect_url" error while redirecting to eSSO  - This is the common error that you can see whenever the redirect URL that is getting passed to /authorization.oauth2 endpoint is different that you had given while registering the client using ServiceNow. You have to make sure that the redirect URL passed to the auth endpoint matches exactly with the URL that you had provided while registering the application. Follow the thread for more details.

 

    "Refresh token" field under grant type should be selected during client registration in order to skip approval screen which keeps popping up during each login. Follow the thread for more details.

 

    If you want to completely disable approval screen then please follow this thread.

 

    If the response from the token endpoint /token.oauth2 does not include the "access-control-allow-origin" header then please make sure that your application domain name is included in the CORS whitelist of eSSO.Please follow the thread for more details.

 

    In case you are planning to use "authorization code flow with PKCE" grant type then please follow this thread for more details.

 

    Since we used the "authorization code flow with PKCE" grant type in our application, we faced one major bottleneck where we had to migrate our application URL from HTTP to HTTPS in order to resolve it. if you are implementing the mentioned grant type in the client-side using JavaScript, and are using this API then it would work only for secure origins like localhost, https, etc, but not in HTTP.

 

 

Note - All applications integrating with eSSO need to support HTTPS for the OAuth calls.

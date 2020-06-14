# Slack integration with HyperSign SSO using SAML 2.0

In this blog we are going to see how we can Integrate Slack(Service Provider) with HyperSign SSO(Password-less Identity provider) using custom SAML 2.0 configuration.

Before we see what exactly we need to do to integrate Slack with HyperSign SSO, lets see what SAML2.O is all about.

**SAML2.0**
Security Assertion Markup Language (SAML) is an open standard that allows identity providers (in this case its [HyperSign](https://hypermine.in/hypersign/)) to pass authorization credentials to service providers (like Slack, office 365, salesforce etc), and with that ,using single Identity provider, a user can login to multiple websites without having to maintain multiple credentials.

So in our case when a user logs into Slack, it requests authorization from the HyperSign. HyperSign authenticates the user’s credentials by asking user to solve QR Code challenge using the HyperSign authenticator and then returns the authorization and user's attributes(like email address, first name, last name etc.) back to Slack, and the user is now able to use the Slack.All this is possible because both of those systems speaks the same language - SAML 2.0. 

As we can see, one of the main advantage of SAML adoption is that it allows us  to use software as a service (SaaS) solutions while maintaining a single secure federated identity management system.
Now in order for SAML authentication to work, HyperSign and Slack need to agree upon the exact configuration of SAML.

Let us now see how HyperSign and Slack needs to be configured for SAML authentication to work.

**HyperSign Configuration**
First you need to login to HyperSign Admin console.
 - Create a new client with the following settings 
	 - Set client ID to [https://slack.com](https://slack.com)
	 - Set the Client Protocol to SAML
	 - Set “Include AuthnStatement” to ON
	 - Set “Sign Documents” to ON
	 - Set “Sign Assertions” to ON
	 - Set “Signature Algorithm” to “RSA_SHA1”
	 - Set “SAML Signature Key Name” to “NONE”
	 - Set “Force POST Binding” to ON
	 - Set “Force Name ID Format” to ON
	 - Set “Name ID Format” to persistent
	 - Set “Valid Redirect UIs” to 2 values
		 - https://[your-slack-url]/
		 - https://[your-slack-url]/*
	 - Set “Assertion Consumer Service POST Binding URL” to “https://[your-slack-url]/sso/saml”
	 - Set “Logout Service POST Binding URL” to “https://[your-slack-url]/sso/saml/logout”
	 - The value of [your-slack-url] is the actual slack URL for your account, for example homelogin.slack.com
![Image of Yaktocat](https://github.com/devgurung/sso/blob/master/HyperSign.PNG)
 - Mapper configuration -Mappers are nothing but SAML Assertion.Its the XML document that the identity provider sends to the service provider that contains information like authentication, attribute(email , first name etc.), and authorization (roles etc.) decision.For Slack to work with HyperSign, we need to configure mapper as follows.
	 - Click the “Add Builtin” button .
	 - Select “X500 email”
	 - Set “SAML Attribute Name” to “User.Email”
   ![Image of Yaktocat](https://github.com/devgurung/sso/blob/master/Email_attribute.PNG)
	 - Next, Slack expects only one role in the response, so in order to make sure HyperSign sends only one role back to Slack, go to the client scope and select "Role_list" , then select "mappers" tab and then again select "role_list" and make sure that "Single Role Attribute" is set to ON.
   ![Image of Yaktocat](https://github.com/devgurung/sso/blob/master/single_role.PNG)
 - Next go to the installation tab and select the Format Option “SAML Metadata IDPSSODescriptor”. This XML document has valuable settings which we will need while configuring slack.
 ![Image of Yaktocat](https://github.com/devgurung/sso/blob/master/installtion.PNG)

**Slack Configuration**
Now that Configuration is all set from HyperSign side, lets go ahead and configure Slack.In order to perform the following operations, you need to be a slack admin.

 - Go to the Settings and Permissions 
 - Click on authentication tab and then go to SSO configuration.
 - Click on the custom SAML 2.0 options and perform the following operations.
	 - Set SAML 2.0 Endpoint (HTTP) to https://www.hsauth.hypermine.in/keycloak/auth/realms/master/protocol/saml. (This is available in the SAML metadata that we downloaded in the previous step)
	 - Set Identity Provider Issuer to https://www.hsauth.hypermine.in/keycloak/auth/realms/master.(This is available in the SAML metadata that we downloaded in the previous step)
	 - Set Public Certificate by reading x.509 Certificate from the downloaded SAML metadata.
	 - Under advanced setting select both "Responses Signed" and "Assertions Signed".
	 - Keep Settings as it is.
	 - Finally you can customize the sign in button using "Customize" option.
	 - Clicking on Save configuration button will take you to HyperSign login page to test the integration.
	 - Please make sure that you are logging into the HyperSign as a user with Email address attribute.
   ![Image of Yaktocat](https://github.com/devgurung/sso/blob/master/hyerpsign_login.PNG)







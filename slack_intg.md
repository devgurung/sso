# Slack integration with HyperSign SSO using SAML2.0

In this blog we are going to see how we can Integrate Slack(Service Provider) with HyperSign SSO(Password-less Identity provider) using custom SAML 2.0 configuration.

Before we see what exactly we need to do to integrate Slack with HyperSign SSO, lets see what SAML2.O is all about.

**SAML2.0**
Security Assertion Markup Language (SAML) is an open standard that allows identity providers (in this case its [HyperSign](https://hypermine.in/hypersign/)) to pass authorization credentials to service providers (like Slack, office 365, salesforce etc), and with that ,using single Identity provider, a user can login to multiple websites without having to maintain multiple credentials.

So in our case when a user logs into Slack, it requests authorization from the HyperSign. HyperSign authenticates the user’s credentials by asking user to solve QR Code challenge using the HyperSign authenticator and then returns the authorization and user's attributes(like email address, first name, last name etc.) back to Slack, and the user is now able to use the Slack.All this is possible because both of those systems speaks the same language - SAML 2.0. 

As we can see, one of the main advantage of SAML adoption is that it allows us  to use software as a service (SaaS) solutions while maintaining a single secure federated identity management system.
Now in order for SAML authentication to work, HyperSign and Slack need to agree upon the exact configuration of SAML.

Let us now see how HyperSign and Slack needs to be configured for SAML authentication to work.

**HyperSign Configuration**






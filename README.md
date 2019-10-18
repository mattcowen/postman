# postman
Hopefully useful postman collections

# Admin Consent

If you want to automate the creation of applications in Azure AD rather than using the portal then these scripts will show you how. If you have the following requirements:

1. Automate the creation of an application and assign users so only certain users in the tenant have access to the application.
2. Automate the consent of all users in the AAD tenant so they have access to the application and any applications the app makes requests to on their behalf. This is analagous to the "grant admin consent" button in the application blade in the portal.

These scripts have the following prerequisites.

1. A "provisioner" application registered in AAD that has the following permissions. It is this application's responsibility to create applications, assign users and grant consent. <b>It therefore has significant privileges and should be treated as an admin user.</b>

AppRoleAssignment.ReadWrite.All (type "Application")

Application.ReadWrite.All (type "Application")

Directory.ReadWrite.All (type "Application")

User.Read (the default type "Delegated")


The provisioner app will authN using client credentials to AAD. I have also used this application for user authN for script 4.3 but this is optional. This script is only used to get an object id of a user which you could get from the portal. If you want to sign in as a user in Postman then you need to add the redirect uri "https://app.getpostman.com/oauth2/callback" to an application registered in AAD.

2. An API application registered in AAD that I called Api1. This needs to have a scope, Api1-User-Access, defined under "Expose an api".

When we create an application registration, "new_web1", via these scripts, we will configure new_web1 to have permision to call the Api1. It is this api that we grant user consent for new_web1 to call on behalf of users. 

3. Import and update the environment variables in Postman

<b>azuread</b>: change this to your AAD tenant domain.

<b>SPID</b>: this is the Application ID for the provisioner app.

<b>SPSecret</b>: provisioner app secret

<b>user_objectid_assigned</b>: this is the object id of the user you want to assign to new_web1. 


For the user_object_assigned variable, you can set this manually via the environment variables but running script 4.3 will get an object id for a user that you authN as. You can configure Postman to sign in as a user by clicking Authorization tab, select OAuth2 in the dropdown and click "get new access token" and configure an app's details in the dialog (use any app you want but you need to set the redirect URI correctly as mentioned above). You can then click "request token" button, sign in, get a token and then click send on the request to graph. The Tests script should then update the variable. 



# Running the scripts

Run scripts 1 and 2 to set the tokenEndpoint variable. Script 3 will use Client Credentials to aquire an access token that will be persisted in the environment variables (see the Tests script).

Create an application using script 4.1 and a corresponding Service Principal for the app in 4.2. These scripts output newSpId and appRoleId to variables. You can now assign a user to the new app using 5.1 as long as user_objectid_assigned has been set. 

Finally, you can consent to the user access to API 1 in script 6. <b>Make sure you set resourceId to be the object id of the API's service principal.</b>

5.2 will show you the permissions granted to the app.


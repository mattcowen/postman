# postman
Hopefully useful postman collections

# Admin Consent

If you want to automate the creation of applications in Azure AD rather than using the portal then these scripts will show you how. If you have the following requirements:

1. Automate the creation of an application and assign users so only certain users in the tenant have access to the application.
2. Automate the consent of all users in the AAD tenant so they have access to the application and any applications the app makes requests to on their behalf. This is analagous to the "grant admin consent" button in the application blade in the portal.

These scripts have the following prerequisites.

1. A "provisioner" application registered in AAD that has the following permissions. It is this application's responsibility to create applications, assign users and grant consent. It therefore has significant privileges and should be treated as an admin user.

AppRoleAssignment.ReadWrite.All (type "Application")
Application.ReadWrite.All (type "Application")
Directory.ReadWrite.All (type "Application")
User.Read (the default type "Delegated")

The provisioner app will authN using client credentials to AAD. I have also used this application for user authN for script 4.3 but this is optional. This script is only used to get an object id of a user which you could get from the portal. If you want to sign in as a user in Postman then you need to add the redirect uri "https://app.getpostman.com/oauth2/callback" to the app.

2. An API application registered in AAD that I called Api1. 

When we create an application registration, "new_web1", via these scripts, we will configure new_web1 to have permision to call the Api1. It is this api that we grant user consent for new_web1 to call on behalf of users. 

3. Update the environment variables

azuread: change this to your AAD tenant domain.
SPID: this is the Application ID for the provisioner app.
SPSecret: provisioner app secret
user_objectid_assigned: this is the object id of the user you want to assign to new_web1. 

For the user_object_assigned variable, you can set this manually via the environment variables but running script 4.3 will get an object id for a user that you authN as. You can configure Postman to sign in as a user by clicking Authorization tab, select OAuth2 in the dropdown and click "get new access token" and configure the provisioner app details in the dialog (you need to set the redirect URI correctly as mentioned above). You can then click "request token" button, sign in, get a token and then click send on the request to graph. The Tests script should then update the variable. 




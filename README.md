# postman
Hopefully useful postman collections

# Admin Consent

If you want to automate the creation of applications in Azure AD rather than using the portal then this collection will show you how. If you have the following requirements:

1. Automate the creation of an application and assign users so only certain users in the tenant have access to the application. In the portal you would set "User Assignment Required" to yes under the Enterprise Application/Service Principal for the app.
2. Automate the consent of all users in the AAD tenant so they have access to the application and any applications the app makes requests to on their behalf. This is analagous to the "grant admin consent" button in the application blade in the portal.

These requests have the following prerequisites.

## 1. A "provisioner" application registered in AAD that has the following permissions. 

It is this application's responsibility to create applications, assign users and grant consent. <b>It therefore has significant privileges and should be treated as an admin user.</b>

#### AppRoleAssignment.ReadWrite.All (type "Application")
#### Application.ReadWrite.All (type "Application")
#### Directory.ReadWrite.All (type "Application")
#### User.Read (the default type "Delegated")


The provisioner app will authN using client credentials to AAD. I have also used this application for user authN for request 4.3 but this is optional. This request is only used to get a user's object id which you could get from the portal. If you want to sign in as a user in Postman then you need to add the redirect uri "https://app.getpostman.com/oauth2/callback" to an AAD application.

## 2. An API application registered in AAD that I called Api1. This needs to have a scope, Api1-User-Access, defined under "Expose an api".

When we create an application registration, "new_web1", we will configure new_web1 to have permision to call the Api1. It is this api that we grant user consent for new_web1 to call on behalf of users. 

## 3. Import the collection json to Postman

## 4. Import environment json and update the variables in Postman

<b>azuread</b>: change this to your AAD tenant domain.

<b>SPID</b>: this is the Application ID for the provisioner app.

<b>SPSecret</b>: provisioner app secret

<b>user_objectid_assigned</b>: this is the object id of the user you want to assign to new_web1. 


For the user_object_assigned variable, you can set this manually via the environment variables but running request 4.3 will get an object id for a user that you authN as. You can sign in as a user in Postman by clicking the Authorization tab, select OAuth2 in the dropdown, click "get new access token" and configure an app's details in the dialog (use any app you want but you need to set the redirect URI correctly as mentioned above). Click "request token" button, sign in, get a token and then click send to call Microsoft Graph. The Tests script should then update the variable for you. 



# Running the requests

Run requests 1 and 2 to set the tokenEndpoint variable automatically. Request 3 will use Client Credentials to acquire an access token that will be persisted in the environment variables (see the Tests script).

Create an application using request 4.1 and a corresponding Service Principal for the app in 4.2. These scripts will ensure User Assignment is required for the application. These requests output newSpId and appRoleId to variables so you can assign a user to the new app using 5.1 as long as user_objectid_assigned has been set. 

Finally, you can consent to the user access to "API 1" in request 6. <b>Make sure you set resourceId to be the object id of the "API 1's" service principal. Please note that I'm only consenting to the API. For users to be able to sign into our new web application I would need to consent to User.Read too. I'll update asap.</b>

5.2 will show you the permissions granted to the app.


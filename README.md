# Agent Identity Setup and Operations
This repository demonstrates the following activities related to **Microsoft Entra Agent Identity**.
## Activities Covered
1. Create an **Agent Identity Blueprint**
2. Create an **Agent Identity Blueprint Principal**
3. Add **Password Credential (Client Secret)** to the Agent Blueprint
4. Get **Access Token** from Agent Blueprint
5. **Expose API (scope)** for the Agent Blueprint
6. Create **Agent Identity**
7. Create **Agentic User**
8. Grent (consent) permissions for the Agentic user
9. Obtain a Federated Identity Credential (FIC) for the Agent Blueprint
10. Obtain a Federated Identity Credential (FIC) for the Agent Identity
11. Obtain an Agentic User Token for MS Graph using the agent_blueprint_fic-token and Agent_ID_fic_token
12. User the agent_user_token to call MS Graph/me endpoint
13. Get the groups the Agentic User (Digital colleague) is member of
14. Obtain the Agent Blueprint FIC Token (Autonomous Agent)
15. Obtain the Agent Identity MS Graph access token
---
# Environment Setup
## Required Tools
1. **Insomnia**  
   Download: https://insomnia.rest/download  
   *If your machine uses Windows OS, ensure Windows Defender allows external applications to open.*
2. **Microsoft Entra Admin Center**  
   Login to the Entra Admin Center.  
   You will need the following from this portal:
   - **Tenant ID**
   - **Sponsor ID**
---
# Setup Steps
## Create Environment variable mentioned below
1. Tenant ID - Go to Microsoft Entra Admin Center -> Overview Page -> Copy the Tenant ID
2. ACCESS_TOKEN - Microsoft Graph Explorer with Entra credentials -> Locate the Access Token on the screen -> Copy the Access Token
3. agent_sponsor_URI - Open Users (MS Entra Admin Center) -> Click on the Sponsor User (Can be any user who controls the Agent) -> Copy the Object ID of the user 
4. agent_blueprint_appId - Copy the App ID result from 01.01
5. client_secret - Copy the output "Secret Text" from 01.03
6. Agentaccess_token - Copy the result from 01.04
7. agent_user_upn - Create a User UPN with Text@tenantaddress. Example vij@M5633647.onmicrosoft.com
8. agent_user_mailNickName - You can create a Nickname for the Agent User Mail ID. Example vijAgent
9. GENERATED_GUID - randomly create a GUID from the internet
10. agent_identity_clientId - Execute 02.01 -> Entra Admin Center -> Agent ID (Preview) -> Click the Agent ID just created and copy the Client ID
11. agent_blueprint_ficToken - Execute 02.04 and copy the token from the output
12. agent_identity_ficToken - Execute 02.05 and copt the token from the output
13. agent_blueprint_appObjectId -Microsoft Entra Admin Center -> Agent ID (Preview) -> View All Agent Identities -> Agent Blueprints -> Click the Blueprint 
14. agent_user_accessToken -
15. token_url - https://login.microsoftonline.com/{{ _.Tenant_ID }}/oauth2/v2.0/token
---
Note - Remove directory.readwrite.all permission (if needed)
If the access token fails due to permission conflicts, remove the directory.readwrite.all permission.
reference documentation:  
[how to remove some of the permissions from graph explorer](https://learn.microsoft.com/en-us/answers/questions/1346583/how-to-remove-some-of-the-permissions-from-graph-e)

---
# Agent Identity End-to-End Lab Guide
## 01 Creating the Agent ID Blueprint
### 01.01 Create the Agent Identity Blueprint (application)
```bash
 curl --request POST \
  --url https://graph.microsoft.com/beta/applications/ \
  --header 'Authorization: Bearer {{ ACCESS_TOKEN }}' \
  --header 'Content-Type: application/json' \
  --header 'OData-Version: 4.0' \
  --header 'User-Agent: insomnia/12.4.0' \
  --data '{
  "@odata.type": "Microsoft.Graph.AgentIdentityBlueprint",
  "displayName": "[ai] Blueprint for Digital Worker 01",
  "sponsors@odata.bind": [
    "https://graph.microsoft.com/beta/users/{Agent_Sponsor_URI}"
  ]
}'
```
Expected result
```json
{
	"@odata.context": "https://graph.microsoft.com/beta/$metadata#applications/$entity",
	"@odata.type": "#microsoft.graph.agentIdentityBlueprint",
	"id": "70b20c91-fe87-46e7-8c92-24e65a0e199c",
	"deletedDateTime": null,
	"appId": "70b20c91-fe87-46e7-8c92-24e65a0e199c",
	"applicationTemplateId": null,
	"identifierUris": [],
	"createdByAppId": "de8bc8b5-d9f9-48b1-a8ad-b748da725064",
	"createdDateTime": "2026-03-12T20:37:33.8446502Z",
	"description": null,
	"disabledByMicrosoftStatus": null,
	"displayName": "[ai] Blueprint for Digital Worker 01",
	"isAuthorizationServiceEnabled": false,
	"isDeviceOnlyAuthSupported": null,
	"isDisabled": null,
	"isFallbackPublicClient": null,
	"isManagementRestricted": null,
	"groupMembershipClaims": null,
	"nativeAuthenticationApisEnabled": null,
	"notes": null,
	"oauth2RequirePostResponse": false,
	"orgRestrictions": [],
	"publisherDomain": "M365t09313528.onmicrosoft.com",
	"signInAudience": "AzureADMyOrg",
	"tags": [],
}
```
### 01.02 Create Agent Identity Blueprint Principal
```bash
curl --request POST \
  --url https://graph.microsoft.com/beta/serviceprincipals/graph.agentIdentityBlueprintPrincipal \
  --header 'Authorization: Bearer {{ ACCESS_TOKEN }} ' \
  --header 'Content-Type: application/json' \
  --header 'OData-Version: 4.0' \
  --header 'User-Agent: insomnia/12.4.0' \
  --data '{
  "appId": " Agent_blueprint_appId"
}'
```
Expected result
```json
{
	"error": {
		"code": "Request_MultipleObjectsWithSameKeyValue",
		"message": "The service principal cannot be created, updated, or restored because the service principal name c642e789-57df-465d-8407-aea848cc4057 is already in use.",
		"details": [
			{
				"code": "ObjectConflict",
				"message": "The service principal cannot be created, updated, or restored because the service principal name c642e789-57df-465d-8407-aea848cc4057 is already in use.",
				"target": "None",
				"blockedWord": "",
				"prefix": "",
				"suffix": ""
			}
		],
		"innerError": {
			"date": "2026-03-12T23:50:07",
			"request-id": "20879765-24ec-45ea-b420-e9e35cf61a50",
			"client-request-id": "20879765-24ec-45ea-b420-e9e35cf61a50"
		}
	}
}
```
### 01.03  Add Password credential (client secret) to the Agent Blueprint
```bash
curl --request POST \
  --url https://graph.microsoft.com/beta/applications/Agent_Blueprint_ObjectID/addPassword \
  --header 'Authorization: Bearer {{ ACCESS_TOKEN }}' \
  --header 'Content-Type: application/json' \
  --header 'User-Agent: insomnia/12.4.0' \
  --data '{
  "passwordCredential": {
    "displayName": "Dummy Secret",
    "endDateTime": "2026-08-05T23:59:59Z"
  }
}'
```
Expected Respone
```json
{
	"@odata.context": "https://graph.microsoft.com/beta/$metadata#microsoft.graph.passwordCredential",
	"customKeyIdentifier": null,
	"endDateTime": "2026-08-05T23:59:59Z",
	"keyId": "b9700ba4-663a-45af-81ab-963167973e1c",
	"startDateTime": "2026-03-12T23:17:19.5583321Z",
	"secretText": "Qr***",
	"hint": "Qr0",
	"displayName": "Dummy Secret"
}
```
### 01.04  Get Access Token from Agent Blueprint
```bash
curl --request POST \
  --url https://login.microsoftonline.com/TENANT_ID/oauth2/v2.0/token \
  --header 'Authorization: Bearer YOUR_BLUENPRINT_APP_ID' \
  --header 'Content-Type: application/x-www-form-urlencoded' \
  --header 'OData-Version: 4.0' \
  --header 'User-Agent: insomnia/12.4.0' \
  --cookie 'fpc=Ahf1c03AJX5Nnnw2O2K3O0wsIdGkAgAAAFJIReEOAAAA8yufIQEAAACRSEXhDgAAAA; x-ms-gateway-slice=estsfd; stsservicecookie=estsfd' \
  --data client_id=YOUR_BLUEPRINT_APP_ID \
  --data 'client_secret=YOUR_SECRET_FROM_01.03_OUTPUT' \
  --data scope=https://graph.microsoft.com/.default \
  --data grant_type=client_credentials
```
Expected response
```json
{
	"token_type": "Bearer",
	"expires_in": 3599,
	"ext_expires_in": 3599,
	"access_token": "ey***"
}
```
### 01.05  Exposing API (scope) for the Agent Blueprint
```bash
curl --request PATCH \
  --url https://graph.microsoft.com/beta/applications/{{ _.agent_blueprint_appObjectId }} \
  --header 'Authorization: Bearer {{ ACCESS_TOKEN }}' \
  --header 'Content-Type: application/json' \
  --header 'User-Agent: insomnia/12.4.0' \
  --data '{
  "identifierUris": [
    "api://{{ _.agent_blueprint_appId }}"
  ],
  "api": {
    "oauth2PermissionScopes": [
      {
        "adminConsentDescription": "Allow the application to access the agent on behalf of the signed-in user.",
        "adminConsentDisplayName": "Access agent",
        "id": "{{ GENERATED_GUID }}",
        "isEnabled": true,
        "type": "User",
        "value": "access_agent"
      }
    ]
  }
}'
```
## 02 Creating the Agent Identity
### 02.01  Creating Agent ID
```bash
curl --request POST \
  --url https://graph.microsoft.com/beta/serviceprincipals/Microsoft.Graph.AgentIdentity \
  --header 'Authorization: Bearer {{ ACCESS_TOKEN_FROM_OAUTH2_CLIENT_CREDENTIALS }}' \
  --header 'Content-Type: application/json' \
  --header 'OData-Version: 4.0' \
  --header 'User-Agent: insomnia/12.4.0' \
  --data '{
    "displayName": "[ai] Agent Identity for Digital Worker 01",
    "agentAppId": "{{ _.agent_blueprint_appId }}",
	  "sponsors@odata.bind": [
      "https://graph.microsoft.com/beta/users/{{ _.agent_sponsor_URI }}"
    ]
  }'
```
Expected Response
```json
{
	"@odata.context": "https://graph.microsoft.com/beta/$metadata#servicePrincipals/microsoft.graph.agentIdentity/$entity",
	"id": "6266698c-faf8-4806-a251-ec250a1ff466",
	"deletedDateTime": null,
	"accountEnabled": true,
	"alternativeNames": [],
	"createdByAppId": "c642e789-57df-465d-8407-aea848cc4057",
	"createdDateTime": null,
	"deviceManagementAppType": null,
	"appDescription": null,
	"appDisplayName": null,
	"appId": "6266698c-faf8-4806-a251-ec250a1ff466",
	"applicationTemplateId": null,
	"appOwnerOrganizationId": null,
	"appRoleAssignmentRequired": false,
	"assignmentRequiredForPrincipalTypes": null,
	"description": null,
	"disabledByMicrosoftStatus": null,
	"displayName": "[ai] Agent Identity for Digital Worker 01",
	....
}
```
### 02.02  Creating Agentic UserID
```bash
curl --request POST \
  --url https://graph.microsoft.com/beta/users \
  --header 'Authorization: Bearer {{ ACCESS_TOKEN_FROM_OAUTH2_CLIENT_CREDENTIALS }}' \
  --header 'Content-Type: application/json' \
  --header 'OData-Version: 4.0' \
  --header 'User-Agent: insomnia/12.4.0' \
  --data '{
    "@odata.type":"microsoft.graph.agentUser",
    "displayName": "[ai] Digital Worker 01 Agent User",
    "userPrincipalName": "AGENT_USER_UPN",
    "mailNickname": "AGENT_USER_NICKNAME",
    "accountEnabled": true,
    "identityParentId":"AGENT_IDENTITY_CLIENTID"
  }'
```
### 02.03  Grant (consent) permissions for the Agentic User
You need Tenant_ID, agent_identity_clientId to enable the permissions. We provide consent explicitly to the Agentic user. Add the below link in the browser and you are redirected to Entra to consent with the permissions. 
```bash
https://login.microsoftonline.com/{{ _.tenant_id }}/v2.0/adminconsent?client_id={{agent_identity_clientId}}&scope=User.Read+groupmember.read.all+Chat.ReadWrite+Calendars.ReadWrite+Mail.ReadWrite+Contacts.Read+People.Read&redirect_uri=https://entra.microsoft.com/TokenAuthorize&state=xyz123
```
### 02.04  Obtain a Federated Identity Credetial (FIC) for the Agent Blueprint
```bash
curl --request POST \
  --url _.token_url \
  --header 'Authorization: Basic {{ _.agent_blueprint_appId }}:{{ _.agent_blueprint_clientSecret }}' \
  --header 'Content-Type: application/x-www-form-urlencoded' \
  --header 'User-Agent: insomnia/12.4.0' \
  --data scope=api://AzureADTokenExchange/.default \
  --data grant_type=client_credentials \
  --data fmi_path={{ _.agent_identity_clientId }}
```
### 02.05  Obtain a Federated Identity Credetial (FIC) for the Agent Identity
```bash
curl --request POST \
  --url token_url \
  --header 'Content-Type: application/x-www-form-urlencoded' \
  --header 'User-Agent: insomnia/12.4.0' \
  --data client_id={{ _.agent_identity_clientId }} \
  --data scope=api://AzureADTokenExchange/.default \
  --data grant_type=client_credentials \
  --data client_assertion_type=urn:ietf:params:oauth:client-assertion-type:jwt-bearer \
  --data client_assertion= {{ _.agent_blueprint_ficToken }}
```
### 02.05  Obtain an Agentic User token for MS Graph using the agent_blueprint_fic-token and agent_id_fic-token
```bash
curl --request POST \
  --url token_url \
  --header 'Content-Type: multipart/form-data' \
  --header 'User-Agent: insomnia/12.4.0' \
  --form client_id=agent_identity_clientId \
  --form client_assertion_type=urn:ietf:params:oauth:client-assertion-type:jwt-bearer \
  --form client_assertion={{ _.agent_blueprint_ficToken }} \
  --form grant_type=user_fic \
  --form requested_token_use=on_behalf_of \
  --form scope=https://graph.microsoft.com/.default \
  --form username={{ _.agent_user_upn }} \
  --form user_federated_identity_credential= {{ _.agent_identity_ficToken }}
```
### 02.05  Use the agent-user-token to call MS Graph /me endpoint
```bash

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
## 1. Get ACCESS_TOKEN_FROM_OAUTH2_CLIENT_CREDENTIALS
1. Open **Microsoft Graph Explorer**.
2. Login using your **Microsoft Entra Admin Center credentials**.
3. Locate the **Access Token** option on the screen.
4. Copy the **Access Token** and use it where required in the API calls.
---
## 2. Get Agent Sponsor URI
1. Go to **Microsoft Entra Admin Center**.
2. Navigate to **Users**.
3. Click on the user who should act as the **Sponsor**.
4. Open the user profile.
5. Copy the **Object ID** of the user.
---
## 3. Remove Directory.ReadWrite.All Permission (If Needed)
If the access token fails due to permission conflicts, remove the **Directory.ReadWrite.All** permission.
Reference documentation:  
https://learn.microsoft.com/en-us/answers/questions/1346583/how-to-remove-some-of-the-permissions-from-graph-e
---
# Task 1: Create Agent Identity Blueprint

## Get Agent Blueprint App ID

1. Open **Microsoft Entra Admin Center**.
2. Navigate to **Agent ID (Preview)**.
3. Click **All Agent Identities**.
4. Select **View All Blueprints**.
5. Click the **Blueprint** you created.
6. Copy the **Application ID (App ID)** from the blueprint.

This App ID will be used in later steps.
This repository consists of the following activities 
1. How to create Agent Blueprint
2. How to create Agent Identity Blueprint Principal
3. Add password credential (client secret) to the Agent Blueprint
4. Get Access token from Agent Blueprint
5. Exposing API (scope) for Agent Blueprint
6. Create Agent identity
7. Creating Agentic User
Environment Setup
List of tools required
1. Insomnia - https://insomnia.rest/download (If your machine has windows OS, make sure that Windows defender allows the external apps to open)
2. Entra Admin center - You need to login to the Entra admin center. You need Tenant ID, Sponsor ID from this portal

Set up - 
1. How to get ACCESS_TOKEN_FROM_OAUTH2_CLIENT_CREDENTIALS - Open Microsoft graph explorer with Microsoft Entra Admin center credentials and look at the access token option on the screen. Copy the Access token and paste it where required.
2. Agent Sponsor URI - Got to Entra Admin center. Click on the user profile who should act as a sponsor. Click on the user profile, copy the Object ID
3. How to remove Directory ReadWrite access all permission for the access token to work - https://learn.microsoft.com/en-us/answers/questions/1346583/how-to-remove-some-of-the-permissions-from-graph-e
Task 1 - How to create Agent Identity Blueprint
4.Agent Blueprint App ID - Open Entra Admin Center, Agent ID Preview tab, All Agent Identities, Click View all Blueprints. Then Click the Blueprint you just created. It has App ID. Copy the App ID. 
---

## 01 Creating the Agent ID Blueprint

### 01.01 Create the Agent Identity Blueprint (application)
```bash
 curl --request POST \
  --url https://graph.microsoft.com/beta/applications/ \
  --header 'Authorization: Bearer {{ ACCESS_TOKEN_FROM_OAUTH2_CLIENT_CREDENTIALS }}' \
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
  --header 'Authorization: Bearer {{ ACCESS_TOKEN_FROM_OAUTH2_CLIENT_CREDENTIALS }} ' \
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
  --header 'Authorization: Bearer {{ ACCESS_TOKEN_FROM_OAUTH2_CLIENT_CREDENTIALS }}' \
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

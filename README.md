# Agent-ID

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
Task 1 - How to create Agent Identity Blueprint
---

## 01 Creating the Agent ID Blueprint

### 01.01 Create the Agent Identity Blueprint (application)
```bash
 curl --request POST \
  --url https://graph.microsoft.com/beta/applications/ \
  --header 'Authorization: {{ ACCESS_TOKEN_FROM_OAUTH2_CLIENT_CREDENTIALS }}' \
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

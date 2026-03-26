# Connecting a 3rd-Party Agent (Base44) to Microsoft Entra Agent Identity

**Platform:** Base44 (3rd-party agent platform) or any 3P platform  
**Identity Provider:** Microsoft Entra ID  

---

## Executive Summary

This document demonstrates how any **3rd-party agent (Base44 Agent in this session)** can authenticate using **Microsoft Entra Agent Identity**.

The integration is based entirely on:

- OAuth 2.0 `client_credentials`
- Federated Identity Credentials (FIC)
- JWT token exchange

This proves:

- Entra Agent Identity is **platform-agnostic**
- Any system capable of HTTP calls can integrate

---

## 1. Objective

Enable a **Base44 agent** to authenticate as a **Microsoft Entra Agent Identity** and obtain a valid Entra-issued JWT.

---

## 2. Pre-requisites

| Asset | Details |
|------|--------|
| Tenant ID | `98430660-2a7e-4e6b-b49c-800a8ba8b657` |
| Blueprint | Steps in AgentID+Blueprint.md |
| Agent Identity | Steps in AgentID+Blueprint.md |
| Base44 Agent | Created |
| FIC Tokens | Steps in AgentID+Blueprint.md |

---

## 3. Architecture (High-Level Flow)
Blueprint App (client_id + secret)
↓
Generate FIC Token (JWT)
↓
Pass FIC as client_assertion
↓
Exchange for Agent Identity Token
↓
Use token in 3rd-party agent (Base44)


---

## 4. Core Concept

The flow works because Entra supports:

- OAuth2 token exchange
- JWT bearer assertions
- Federated credentials

---

## 5. End-to-End Flow

### Step 1 — Generate FIC Token (Blueprint)
In this step, the **Blueprint application authenticates using its own credentials** and requests a **FIC token for a specific Agent Identity**.

```bash
POST https://login.microsoftonline.com/<TENANT_ID>/oauth2/v2.0/token
Authorization: Basic <BASE64(BLUEPRINT_CLIENT_ID:BLUEPRINT_CLIENT_SECRET)>
Content-Type: application/x-www-form-urlencoded
scope=api://AzureADTokenExchange/.default
grant_type=client_credentials
fmi_path=<AGENT_IDENTITY_APP_ID>
```
### Step 2 — Exchange FIC for Agent Identity Token
In this step, the **Agent Identity takes over**. The Blueprint already generated a **FIC token (JWT)** in Step 1. Now that token is used as proof to say: “I am allowed to act as this Agent Identity.”
```bash
POST https://login.microsoftonline.com/<TENANT_ID>/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded
client_id=<AGENT_IDENTITY_APP_ID>
grant_type=client_credentials
client_assertion=<FIC_TOKEN>
scope=https://graph.microsoft.com/.default
```

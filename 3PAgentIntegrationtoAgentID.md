# Connecting a 3rd-Party Agent (Base44) to Microsoft Entra Agent Identity

**Author:** Vijaya Nukala  
**Date:** March 26, 2026  
**Status:** Completed  
**Platform:** Base44 (3rd-party agent platform)  
**Identity Provider:** Microsoft Entra ID  

---

## Executive Summary

This document demonstrates how a **3rd-party agent (Base44)** can authenticate using **Microsoft Entra Agent Identity** without using any Microsoft SDK.

The integration is based entirely on:

- OAuth 2.0 `client_credentials`
- Federated Identity Credentials (FIC)
- JWT token exchange

This proves:

- Entra Agent Identity is **platform-agnostic**
- No SDK or native OIDC support is required
- Any system capable of HTTP calls can integrate

---

## 1. Objective

Enable a **Base44 agent** to authenticate as a **Microsoft Entra Agent Identity** and obtain a valid Entra-issued JWT.

---

## 2. Pre-requisites

| Asset | Details |
|------|--------|
| Tenant ID | `98430660-2a7e-4e6b-b49c-800a8ba8b657` |
| Blueprint | Blueprint for Digital Worker |
| Agent Identity | Already created |
| Base44 Agent | Created |
| FIC Tokens | Generated |
| Codebase | AgentID Demo (read-only) |

---

## 3. Architecture (High-Level Flow)

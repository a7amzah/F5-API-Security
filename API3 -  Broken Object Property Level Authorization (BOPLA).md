## Overview:
It’s when an API lets you see or change more data than you should be able to — either by accident or because the developer didn’t limit it properly

**This combines two old categories:**

- **Mass Assignment** — changing properties you shouldn’t be able to change (e.g., setting your account from “free” to “premium” in a hidden field).
- **Excessive Data Exposure** — the API sends too much data (extra fields with sensitive info) even if the UI only shows part of it.

Here, I'll simulate Excessive Data Exposure   

### Setup:
Testing with two hostnames 
	crapi.local |  crAPI Application protected by WAF
	10.1.10.104 | crAPI Application Without WAF

### Users:
legit-user@example.com/F5@Pass50

## Data Exposure To CrAPI without WAF
- Login in to crAPI with username and password and get access token
```zsh unfold ln:false unwrap title:"Command"
$ export token=$(curl -X POST http://crapi.local/identity/api/auth/login -d '{"email":"legit-user@example.com","password":"F5@Pass50"}' -H "Content-Type: application/json"  -s | jq -r '.token')
```

- Exposed Data
We discovered that any authenticated user can reveal the data of other users by reviewing their posts
```python unfold ln:false unwrap title:"code"
curl  http://10.1.10.104/community/api/v2/community/posts/urvi4epTExTgLepeBeeNR9 -H "Authorization: Bearer $token" -H "Content-Type: application/json"
```
<img width="2484" height="351" alt="image" src="https://github.com/user-attachments/assets/903afdb5-8d05-4446-aaa6-59a1c9487af1" />

email address and vehicle id for Adam are revealed.
## crAPI Data Masked With WAF
enforce WAF to mask `email addresses` and `vehicle id` in the response. 
<img width="2178" height="1168" alt="image" src="https://github.com/user-attachments/assets/0397b1dd-6c10-4f09-bf5e-048c510a66a8" />
<img width="852" height="139" alt="image" src="https://github.com/user-attachments/assets/0f387fc9-c5ea-44c4-9cff-324a8896af9f" />



- Accessing posts we see that emails and vehicle id are masked. 
```python unfold ln:false unwrap title:"code"
curl  http://crapi.local/community/api/v2/community/posts/urvi4epTExTgLepeBeeNR9 -H "Authorization: Bearer $token" -H "Content-Type: application/json"
```
<img width="2499" height="295" alt="image" src="https://github.com/user-attachments/assets/80ef4f40-574a-469f-95a0-26e93e787c33" />

same exact request but email address and vehicle id are masked.


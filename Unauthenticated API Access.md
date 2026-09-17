## Overview:
Discovered that orders endpoint "shop/order" accepts any traffic without authentication 
### Setup:
Testing with two hostnames 
	**crapi.local** |  crAPI Application protected by WAF
	**10.1.10.104** | crAPI Application Without WAF

### Users:
**Malicious User:** malicious@example.com/F5@Pass50 
## Without WAF/Access Profile
- any user can access the order of someone else, revealing order details, and user and payment information. 
```zsh unfold ln:false unwrap title:"Command"
curl http://10.1.10.104/workshop/api/shop/orders/5 -s | jq
```
<img width="1158" height="1238" alt="image" src="https://github.com/user-attachments/assets/07baeb08-3e75-4cb9-8514-72bf5951c616" />

## With WAF/Access Profile 
### Configure Access Profile (WAF don't require APM)
<img width="1430" height="1204" alt="image" src="https://github.com/user-attachments/assets/bbe8194d-64f8-452e-91e1-c3cd4a122aea" />

- Apply access profile to `GET /workshop/api/shop/orders/*` URI 
<img width="1429" height="848" alt="image" src="https://github.com/user-attachments/assets/501aa892-a7d2-41b0-ab83-8f33e4d0b619" />


### Access without Token
```zsh unfold ln:false unwrap title:"Without Token"
$ curl http://crapi.local/workshop/api/shop/orders/5 -s | jq
```
<img width="1156" height="210" alt="image" src="https://github.com/user-attachments/assets/7bd836ff-6857-4db5-8720-e0c77bd35c01" />
<img width="1156" height="210" alt="image" src="https://github.com/user-attachments/assets/b338f9a5-f10b-4498-a7f4-d049a9e04139" />

### With Expired Token
- Use an expired token 
<img width="2489" height="213" alt="image" src="https://github.com/user-attachments/assets/6e0a79cf-af90-4317-9f2a-b2b36c9b760f" />
looks like` old_token` expired two days ago
<img width="2318" height="731" alt="image" src="https://github.com/user-attachments/assets/9dc57db0-1390-429f-8d8e-2c72639b1904" />

```zsh unfold ln:false unwrap title:"With Old Token"
$ curl http://crapi.local/workshop/api/shop/orders/5 -H "Authorization: Bearer $old_token"
```

<img width="1677" height="228" alt="image" src="https://github.com/user-attachments/assets/84f7ae5c-c215-46f6-acc9-191e6c1228e3" />
<img width="1820" height="667" alt="image" src="https://github.com/user-attachments/assets/0fc5ab9d-6b69-4eb1-b5a6-726ea1cab8bb" />

#### Access with Valid New Token
```zsh unfold ln:false unwrap title:"Command"
export new_token=$(curl -X POST http://10.1.10.104/identity/api/auth/login -d '{"email":"malicious@example.com","password":"F5@Pass50"}' -H "Content-Type: application/json"  -s | jq -r '.token')
```
<img width="2479" height="224" alt="image" src="https://github.com/user-attachments/assets/21253530-daa8-4c23-93f3-17ece4f3a904" />
<img width="1765" height="727" alt="image" src="https://github.com/user-attachments/assets/4022f1f3-ca35-4af0-8700-f10c672a92fd" />

```zsh unfold ln:false unwrap title:"With New Token"
$ curl http://crapi.local/workshop/api/shop/orders/5 -H "Authorization: Bearer $new_token"
```
<img width="2485" height="249" alt="image" src="https://github.com/user-attachments/assets/e769ca0e-71df-4d71-83de-2a34a7ebcf02" />


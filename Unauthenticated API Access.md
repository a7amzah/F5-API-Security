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
![[Pasted image 20260906141514.png|592]]

## With WAF/Access Profile 
### Configure Access Profile (WAF don't require APM)
![[Pasted image 20260906141930.png|725]]

- Apply access profile to `GET /workshop/api/shop/orders/*` URI 
![[Pasted image 20260906141733.png|787]]

### Access without Token
```zsh unfold ln:false unwrap title:"Without Token"
$ curl http://crapi.local/workshop/api/shop/orders/5 -s | jq
```
![[Pasted image 20260906142048.png|759]]
![[Pasted image 20260906142540.png|764]]

### With Expired Token
- Use an expired token 
![[Pasted image 20260906143242.png]]
looks like` old_token` expired two days ago
![[Pasted image 20260906143223.png|991]]

```zsh unfold ln:false unwrap title:"With Old Token"
$ curl http://crapi.local/workshop/api/shop/orders/5 -H "Authorization: Bearer $old_token"
```

![[Pasted image 20260906143331.png|859]]
![[Pasted image 20260906143450.png|940]]

#### Access with Valid New Token
```zsh unfold ln:false unwrap title:"Command"
export new_token=$(curl -X POST http://10.1.10.104/identity/api/auth/login -d '{"email":"malicious@example.com","password":"F5@Pass50"}' -H "Content-Type: application/json"  -s | jq -r '.token')
```
![[Pasted image 20260906144921.png|1017]]
![[Pasted image 20260906145036.png|1024]]

```zsh unfold ln:false unwrap title:"With New Token"
$ curl http://crapi.local/workshop/api/shop/orders/5 -H "Authorization: Bearer $new_token"
```
![[Pasted image 20260906145155.png|969]]

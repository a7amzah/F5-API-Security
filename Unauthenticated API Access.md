**Fixing OWASP API #1 — Broken Object Level Authorization (BOLA)**
> BOLA happens when an app or API lets you access or change something you shouldn’t be allowed to — like seeing another user’s private data or performing actions on someone else’s account.

## Demo Overview:
Discovered that orders endpoint "shop/order" accepts any traffic without authentication 

### Setup:
Testing with two hostnames:

**crapi.local** |  crAPI Application protected by WAF

**10.1.10.104** | crAPI Application Without WAF

### Users:

**Malicious User:** malicious@example.com/F5@Pass50

## Without WAF/Access Profile
- any user can access the order of someone else, revealing order details, and user and payment information. 
```zsh unfold ln:false unwrap title:"Command"
curl http://10.1.10.104/workshop/api/shop/orders/5 -s | jq
```
<img width="1158" height="1238" alt="image" src="https://github.com/user-attachments/assets/27e38d2c-577f-41da-ba30-31a037d9f388" />


## With WAF/Access Profile 
### Configure Access Profile (WAF don't require APM)
<img width="1430" height="1204" alt="image" src="https://github.com/user-attachments/assets/dd8b624f-16bb-4888-92c3-7428742189fe" />

- Apply access profile to `GET /workshop/api/shop/orders/*` URI 
<img width="1429" height="848" alt="image" src="https://github.com/user-attachments/assets/93d9ba3a-bb3a-4257-bc45-7cde06af9854" />


### Access without Token
```zsh unfold ln:false unwrap title:"Without Token"
$ curl http://crapi.local/workshop/api/shop/orders/5 -s | jq
```
<img width="1156" height="210" alt="image" src="https://github.com/user-attachments/assets/773d2181-756a-4970-ae33-2987ede3ac7f" />
<img width="1856" height="555" alt="image" src="https://github.com/user-attachments/assets/1d3ae765-8a0e-471d-8321-53a9e3d6f36b" />


### With Expired Token
- Use an expired token 
<img width="2489" height="213" alt="image" src="https://github.com/user-attachments/assets/e42dfba0-adaa-42c7-9e13-30d87821cedb" />

looks like` old_token` expired two days ago
<img width="2318" height="731" alt="image" src="https://github.com/user-attachments/assets/b6313de1-8ee7-4304-a24a-fb86aa6eddf9" />

```zsh unfold ln:false unwrap title:"With Old Token"
$ curl http://crapi.local/workshop/api/shop/orders/5 -H "Authorization: Bearer $old_token"
```

<img width="1677" height="228" alt="image" src="https://github.com/user-attachments/assets/d6743064-d532-4fa6-bcdb-391b48bc5d74" />
<img width="1820" height="667" alt="image" src="https://github.com/user-attachments/assets/f2d48323-2a74-46ab-9a78-5d4ffde7213b" />


#### Access with Valid New Token
```zsh unfold ln:false unwrap title:"Command"
export new_token=$(curl -X POST http://10.1.10.104/identity/api/auth/login -d '{"email":"malicious@example.com","password":"F5@Pass50"}' -H "Content-Type: application/json"  -s | jq -r '.token')
```
<img width="2479" height="224" alt="image" src="https://github.com/user-attachments/assets/d64ff95b-a7c7-4fd1-ac88-df0231fd6cc0" />
<img width="1765" height="727" alt="image" src="https://github.com/user-attachments/assets/5b7a452b-7304-4086-961a-13ab19ff76dd" />


```zsh unfold ln:false unwrap title:"With New Token"
$ curl http://crapi.local/workshop/api/shop/orders/5 -H "Authorization: Bearer $new_token"
```
<img width="2485" height="249" alt="image" src="https://github.com/user-attachments/assets/f9f2dbf5-415c-412a-afa1-8d11918d788e" />


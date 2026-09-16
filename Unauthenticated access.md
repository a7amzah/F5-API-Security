## Overview:
orders endpoint "" accepts any traffic without authentication 

## Without WAF/Access Profile
user can access the order of another user, revealing order details, user, payment information. 
```zsh unfold ln:false unwrap title:"Command"
$ curl http://crapi.local/workshop/api/shop/orders/5 -s | jq
```
![[Pasted image 20260906141514.png|827]]

## With WAF/Access Profile 
![[Pasted image 20260906141930.png|832]]


![[Pasted image 20260906141733.png|931]]

```zsh unfold ln:false unwrap title:"Without Token"
$ curl http://crapi.local/workshop/api/shop/orders/5 -s | jq
```
![[Pasted image 20260906142048.png|759]]
![[Pasted image 20260906142540.png|825]]

### With Old Token

![[Pasted image 20260906143242.png]]

![[Pasted image 20260906143223.png|991]]

```zsh unfold ln:false unwrap title:"With Old Token"
$ curl http://crapi.local/workshop/api/shop/orders/5 -H "Authorization: Bearer $old_token"
```

![[Pasted image 20260906143331.png]]
![[Pasted image 20260906143450.png]]

### Access with Valid New Token
```zsh unfold ln:false unwrap title:"Command"
curl -X POST http://crapi.local/identity/api/auth/login -d '{"email":"user2@ku.sa","password":"H@mza@123"}' -H "Content-Type: application/json" -o toke_file.json -s > /dev/null
```
![[Pasted image 20260906144921.png|1017]]
![[Pasted image 20260906145036.png]]

```zsh unfold ln:false unwrap title:"With New Token"
$ curl http://crapi.local/workshop/api/shop/orders/5 -H "Authorization: Bearer $new_token"
```
![[Pasted image 20260906145155.png]]

![](../../../All%20Images/Pasted%20image%2020260916104107.png)
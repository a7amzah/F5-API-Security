
## Without WAF
```python unfold ln:false unwrap title:"code"
curl  http://10.1.10.104/community/api/v2/community/posts/urvi4epTExTgLepeBeeNR9 -H "Authorization: Bearer $token" -H "Content-Type: application/json"
```
![[Pasted image 20260902204420.png]]email address and vehicle id of different user are revealed.
## With WAF Protection
enforce WAF to mask email addresses and vehicle id in the response. 
![[Pasted image 20260902205324.png]]

![[Pasted image 20260902205347.png]]

```python unfold ln:false unwrap title:"code"
curl  http://crapi.local/community/api/v2/community/posts/urvi4epTExTgLepeBeeNR9 -H "Authorization: Bearer $token" -H "Content-Type: application/json"
```
![[Pasted image 20260902204725.png]]
same exact request but email address and vehicle id are masked.


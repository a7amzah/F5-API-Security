
##  Increase your balance
Go to the shop page and there is an initial available balance of **$100** and two items: ‘**Seat**’ and ‘**Wheel**’
![[Pasted image 20260830151437.png]]

make a purchase one seat of 10$ then the balance will be 90$

however we can increase the balance by returning the same order but modify the quantity and status
We can make a PUT request with a body parameter called ‘<font color="#ffc000">status=returned</font>’ from 
we also see a ‘<font color="#ffc000">quantity</font>’ parameter in the response.
![[Pasted image 20260830151327.png|867]]


below command is retuning order 8 with quantity 100
```zsh unfold ln:false unwrap title:"PUT"
curl -X PUT  http://10.1.10.104/workshop/api/shop/orders/8 -d '{"quantity":100,"status":"returned"}' -H "Authorization: Bearer $token" -H "Content-Type: application/json"
```

the request submitted with quantity 100 and the total return is 1000$
![[Pasted image 20260830152400.png]]

the balance increased.
![[Pasted image 20260830152248.png]]




## Overview
crAPI orders endpoint is accepting any origin.
According to the Same Origin Policy, a web page or a script running on a<font color="#ffc000"> web page can only access content (like <u>XMLHttpRequest</u>) from the same origin as the web page</font>, where “*origin*” is defined as the combination of the protocol (HTTP or HTTPS), the hostname, and the port number.
however by simulating from `app.external ` we can access `orders`  endpoint <font color="#ffc000">through Javascript using an XMLHttpRequest</font>. 
### Setup:
Testing with two hostnames 
	**crapi.local** |  crAPI Application protected by WAF
	**10.1.10.104** | crAPI Application Without WAF
	**app.external** | simulating a request from different origin through XMLHttpRequest
	![673](../../../All%20Images/Pasted%20image%2020260917155223.png)
## CORS without WAF
- crAPI "`shop/orders`" endpoint is vulnerable to CORS misconfiguration as the service is accepting any origin
```zsh unfold ln:false unwrap title:"Command"
curl  http://10.1.10.104/workshop/api/shop/orders/9 -H "Origin: null" --head
```
![688](../../../All%20Images/Pasted%20image%2020260917150312.png)
it simply means that any cross domains requests can be loaded in a browser
With `access-control-allow-origin: *` CORS header, we are simply disabling the inbuilt protections that come with **Same Origin Policy**, and the request was allowed by browser
- Simulating external application that call crapi orders endpoint from 
![787](../../../All%20Images/Pasted%20image%2020260917153335.png)

- Comparing with the landing URI `/` which doesn't include access control header
```zsh unfold ln:false unwrap title:"Command"
curl  http://10.1.10.104/ -H "Origin: null" --head
```
![507](../../../All%20Images/Pasted%20image%2020260917150417.png)
No access control header in the response.

and as expected the request was blocked by browser.
![780](../../../All%20Images/Pasted%20image%2020260917153007.png)
## Enforce Cross Origin from WAF
- Enforce Cross Domain at URI  `/workshop/api/shop/orders/{{id}}`
![[Pasted image 20260907165935.png|667]]
- enforce `illegal cross-origin request`
![[Pasted image 20260907165830.png|579]]

### Verification 
![[Pasted image 20260907170454.png|630]]
as this origin is not configured by WAF, so the request is blocked. 
![617](../../../All%20Images/Pasted%20image%2020260917154112.png)
confirm the request is blocked by browser as well.
![633](../../../All%20Images/Pasted%20image%2020260917154320.png)


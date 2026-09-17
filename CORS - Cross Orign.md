**Fixing API#8 - Miss configuration - CORS**
>> Security Misconfiguration occurs when API servers, systems, or components are not properly set up, hardened, or maintained — leaving gaps that attackers can exploit.

## Demo Overview
crAPI orders endpoint is accepting any origin.
According to the Same Origin Policy, a web page or a script running on a<font color="#ffc000"> web page can only access content (like <u>`XMLHttpRequest`</u>) from the same origin as the web page</font>, where “*origin*” is defined as the combination of the protocol (HTTP or HTTPS), the hostname, and the port number.
however by simulating from `app.external ` we can access `orders`  endpoint <font color="#ffc000">through Javascript using an XMLHttpRequest</font>. 
### Setup: 
**crapi.local** |  crAPI Application protected by WAF

**10.1.10.104** | crAPI Application Without WAF
	
**app.external** | simulating a request from different origin through XMLHttpRequest
	
<img width="1572" height="1198" alt="image" src="https://github.com/user-attachments/assets/76b7f853-e5df-4388-afc9-3d1e950e171d" />

## CORS without WAF
- crAPI "`shop/orders`" endpoint is vulnerable to CORS misconfiguration as the service is accepting any origin
```zsh unfold ln:false unwrap title:"Command"
curl  http://10.1.10.104/workshop/api/shop/orders/9 -H "Origin: null" --head
```
<img width="1247" height="425" alt="image" src="https://github.com/user-attachments/assets/31ad4d03-a4aa-4946-ac41-beff73086511" />

it simply means that any cross domains requests can be loaded in a browser
With `access-control-allow-origin: *` CORS header, we are simply disabling the inbuilt protections that come with **Same Origin Policy**, and the request was allowed by browser
- Simulating external application that call crapi orders endpoint from 
<img width="2306" height="1213" alt="image" src="https://github.com/user-attachments/assets/73b95e8b-9e10-4e4f-8e38-e9e691f19c0c" />


- Comparing with the landing URI `/` which doesn't include access control header
```zsh unfold ln:false unwrap title:"Command"
curl  http://10.1.10.104/ -H "Origin: null" --head
```
<img width="808" height="344" alt="image" src="https://github.com/user-attachments/assets/fe92b906-17dc-4c21-b524-9f6811990988" />
No access control header in the response.

and as expected the request was blocked by browser.
<img width="2297" height="1079" alt="image" src="https://github.com/user-attachments/assets/5172a225-780c-4a90-b028-b7bb595b4a5c" />

## Enforce Cross Origin from WAF
- Enforce Cross Domain at URI  `/workshop/api/shop/orders/{{id}}`
<img width="2047" height="1318" alt="image" src="https://github.com/user-attachments/assets/3af3a8bb-e226-4484-9efa-585bfbd208f3" />

- enforce `illegal cross-origin request`
<img width="1100" height="840" alt="image" src="https://github.com/user-attachments/assets/4820a754-7843-4f8c-a072-b2f4ad66cc44" />


### Verification 
<img width="1352" height="462" alt="image" src="https://github.com/user-attachments/assets/1568f03b-51e6-4936-90cc-9c8e8a943aed" />

as this origin is not configured by WAF, so the request is blocked. 
<img width="2309" height="1205" alt="image" src="https://github.com/user-attachments/assets/7f8aa4f7-730a-409f-b3bb-8d4f39587002" />

confirm the request is blocked by browser as well.
<img width="2046" height="1028" alt="image" src="https://github.com/user-attachments/assets/b2e3dedc-c0f4-4aae-bd01-8258106a7ded" />



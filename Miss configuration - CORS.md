
## Overview
crAPI "shop/orders" endpoint is vulnerable to CORS misconfiguration as the service is accepting any origin
![[Pasted image 20260907160213.png|906]]
it simply means that any cross domains requests can be loaded in a browser
With `access-control-allow-origin: *` CORS header, we are simply disabling the inbuilt protections that come with Same Origin Policy, and the request was allowed by browser
![[Pasted image 20260907165007.png]]

comparing to the landing URI which doesn't include access control header
![[Pasted image 20260907162517.png|641]]
so the request was blocked by browser.
![[Pasted image 20260907164653.png]]
## Enforce Cross Origin from WAF
![[Pasted image 20260907165935.png]]
![[Pasted image 20260907165830.png]]

### Verification 
![[Pasted image 20260907170454.png]]
as this origin is not configured by WAF, so the request is blocked. 
![[Pasted image 20260907165717.png]]
confirm the request is blocked by browser as well.

![[Pasted image 20260907170239.png]]


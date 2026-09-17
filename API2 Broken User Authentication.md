## Overview
Observing that the OTP endpoint `POST /identity/api/v2/auth/check-otp` is not protected by rate limiting.

### Setup:
Testing with two hostnames 
	crapi.local | Application protected by WAF
	10.1.10.104 | Accessing application direct 

### Users:
Malicious User:
	malicious@example.com/F5@Pass50 

from another enumeration we know user `adam007@example.com` exists but we don't know the password.

## Discovering Weak OTP Endpoint 
-  discover forgot password functionality  
![[Pasted image 20260914175945.png|834]]

- from previous enumeration we discovered a user `adam007@example.com` we'll try to get OTP for it
![[Pasted image 20260914180634.png|759]]

![[Pasted image 20260914180804.png|777]]

- discovering the OTP endpoint
so the endpoint for the password change is  `POST /identity/api/v3/auth/check-otp`
![[Pasted image 20260914180905.png|764]]


### Exploiting OTP v3  `POST /identity/api/v3/auth/check-otp`endpoint  
Using burp suite to bruteforce OTP by trying different combinations of 4 digits 
![[Pasted image 20260914185119.png|1008]]
![[Pasted image 20260914183117.png|1019]]

- after 7 attempts we have reached the limit of attempts
![[Pasted image 20260914183109.png|1019]]


### API version downgrade OTP v2
- it's looks like v3 is using rate limit protection, check if earlier versions are still accessible
![[Pasted image 20260914185025.png|977]]
- it looks like there's no rate limit in v2
![[Pasted image 20260914184944.png|946]]

- we received success response for one of the OTP **6346**
![[Pasted image 20260915135844.png|961]]

- Login to admam007 account with credential "**Ctest@1234**"  
![[Pasted image 20260915140022.png|981]]


## Configure Rate-limit from WAF 
- Create API Protection Profile, and upload the Swegger file
![[Pasted image 20260915154712.png|980]]

- Configure Rate Limit by 10 attempts per user
![[Pasted image 20260915144949.png|989]]
- Apply rate limit to OTP v2 endpoint 
![[Pasted image 20260915154731.png|908]]
![[Pasted image 20260915145037.png|913]]

- After 10 attempts receiving too many attempts from F5 API protection profile 
![[Pasted image 20260915143954.png|877]]![[Pasted image 20260915144425.png|909]]
![[Pasted image 20260915144848.png|905]]
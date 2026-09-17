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
<img width="1487" height="1169" alt="image" src="https://github.com/user-attachments/assets/ed37840d-af76-4024-bd4f-f67cfbaf4a7b" />


- from previous enumeration we discovered a user `adam007@example.com` we'll try to get OTP for it
<img width="1016" height="777" alt="image" src="https://github.com/user-attachments/assets/0788f5e8-99fa-4362-b726-e7120fe2d7b9" />

<img width="1082" height="1195" alt="image" src="https://github.com/user-attachments/assets/7bf45083-65ee-4a4e-b44b-d57bdd48eed8" />

- discovering the OTP endpoint
so the endpoint for the password change is  `POST /identity/api/v3/auth/check-otp`
<img width="1695" height="695" alt="image" src="https://github.com/user-attachments/assets/2b39a4cf-1d79-4910-aaf9-2104266c9ed6" />


### Exploiting OTP v3  `POST /identity/api/v3/auth/check-otp`endpoint  
Using burp suite to bruteforce OTP by trying different combinations of 4 digits 
<img width="2217" height="1138" alt="image" src="https://github.com/user-attachments/assets/9a84a3fd-af61-4add-a3c1-f26fc3dd92de" />
<img width="1653" height="1039" alt="image" src="https://github.com/user-attachments/assets/6b945687-c519-44ed-a56c-3703aeaef608" />

- after 7 attempts we have reached the limit of attempts
<img width="1604" height="1067" alt="image" src="https://github.com/user-attachments/assets/2851a5bf-d092-451f-84d9-e717ffc1deed" />


### API version downgrade OTP v2
- it's looks like v3 is using rate limit protection, check if earlier versions are still accessible
<img width="2222" height="1095" alt="image" src="https://github.com/user-attachments/assets/460c4381-b65b-4eb8-9562-cd9656a93d69" />

- it looks like there's no rate limit in v2
<img width="1827" height="970" alt="image" src="https://github.com/user-attachments/assets/f029be05-5e45-4d55-a3dd-15bc1ad2ec44" />

- we received success response for one of the OTP **6346**
<img width="1576" height="1163" alt="image" src="https://github.com/user-attachments/assets/0bc75599-b768-4ed8-9fcf-da217c4c1de1" />

- Login to admam007 account with credential "**Ctest@1234**"  
<img width="1925" height="1145" alt="image" src="https://github.com/user-attachments/assets/e41254b4-c462-4712-bf56-6e88ef41273c" />


## Configure Rate-limit from WAF 
- Create API Protection Profile, and upload the Swegger file
<img width="1859" height="1349" alt="image" src="https://github.com/user-attachments/assets/c2c35f56-d9b0-413f-a90f-b7c8f4b5472b" />


- Configure Rate Limit by 10 attempts per user
<img width="1839" height="1427" alt="image" src="https://github.com/user-attachments/assets/63029028-f66a-4d09-9b1e-f0893f5794b3" />

- Apply rate limit to OTP v2 endpoint 
<img width="1441" height="429" alt="image" src="https://github.com/user-attachments/assets/5b4c169c-a7ca-40ea-a4fc-df0f6b7ea884" />

<img width="2223" height="617" alt="image" src="https://github.com/user-attachments/assets/1a6136cf-67d1-4e0a-974f-59f48521d098" />


- After 10 attempts receiving too many attempts from F5 API protection profile 
<img width="1672" height="936" alt="image" src="https://github.com/user-attachments/assets/b76f94dd-9728-4716-b221-9bda140c6ce1" />

<img width="1058" height="771" alt="image" src="https://github.com/user-attachments/assets/d054849e-3569-42b5-94cb-3a878a79aa56" />

<img width="1047" height="754" alt="image" src="https://github.com/user-attachments/assets/fcb85370-bc45-4eae-860d-588a8fbe1ba4" />

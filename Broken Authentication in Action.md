## Overview

## OTP 
![[Pasted image 20260914175945.png]]

from analysis we found a user 'adam007@example.com' we'll try to get OTP for it
![[Pasted image 20260914180634.png]]

![[Pasted image 20260914180804.png]]

![[Pasted image 20260914180905.png]]

so the endpoint for the password change is  "POST /identity/api/v3/auth/check-otp"

use burpsuite to run different combinations of 4 digits 
![[Pasted image 20260914185119.png]]
![[Pasted image 20260914183117.png]]
![[Pasted image 20260914183109.png]]
after 7 attempts we have reached the limit of attempts

### API version downgrade 
it's looks like v3 is using rate limit protection, check if earlier versions are still accessible
![[Pasted image 20260914185025.png]]
it looks like there's no rate limit in v2
![[Pasted image 20260914184944.png]]
![[Pasted image 20260915135844.png]]

with password "Ctest@1234" i had access to Adam's account 
![[Pasted image 20260915140022.png]]


## Configure Rate-limit from WAF 
API Protection Profile
![[Pasted image 20260915154712.png]]

![[Pasted image 20260915144949.png]]
![[Pasted image 20260915154731.png]]
![[Pasted image 20260915145037.png]]


![[Pasted image 20260915143954.png]]![[Pasted image 20260915144425.png]]
![[Pasted image 20260915144848.png]]
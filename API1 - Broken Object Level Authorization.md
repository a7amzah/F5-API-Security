## Overview:
After Logging into the application 'crAPI' anyone can access the vehicle report of another user if they only have the Vehicle ID.

## Issue: Access Another Users Vehicle report
each customer can view his own vehicle report via  <font color="#ffc000">/vehicle-service-dashboard?VIN=8PK1LM7PB5YV9EEG0</font>
the problem if any other user changes VIN value to the value of another user he/she can access 
- Below VIN of user "Ahmed"
![[Pasted image 20260827152737.png|901]]
- from another account 'user' 
![[Pasted image 20260827152527.png|903]]

when user2 changed the **vin** to ahmed's vin, he can access his account.
![[Pasted image 20260827161400.png]]

## WAF Mitigation - Dynamic Parameter Tampering
- Reviewing the traffic, we see the parameter is set in the response of uri `/identity/api/v2/vehicle/vehicles`
![[Pasted image 20260827162053.png]]
2- Make sure JSON parameters are parsed for this uri 
![[Pasted image 20260827161719.png]]

3- Configure dynamic parameter 
![[Pasted image 20260827162312.png|840]]

![[Pasted image 20260827162331.png|802]]

![[Pasted image 20260827162416.png|695]]

### Validation
- access user2 vehicle portal
![[Pasted image 20260827162516.png]]

- change VIN to the other user's value 
![[Pasted image 20260827162734.png]]

![[Pasted image 20260827162947.png]]
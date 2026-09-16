## Overview:
After Logging into the application 'crAPI' anyone can access the vehicle report of another user if they only have the Vehicle ID.

### Setup:
Testing with two hostnames 
	crapi.local | Application protected by WAF
	10.1.10.104 | Accessing application direct 

### Users:
Legit User:
	legit-user@example.com/F5@Pass50
Malicious User:
	malicious@example.com/F5@Pass50 
## Issue: Access Another Users Vehicle report
each customer can view his/her own vehicle report via  `/vehicle-service-dashboard?VIN=<User Vehicle ID>
If another user "e.g Attacker" can alter VIN value to the value of another user to access his/her vehicle report.  
- Below VIN of user "Legit User"
![](../../../All%20Images/Pasted%20image%2020260916163847.png)
- from another account 'Malicious' who has no reports 
![1171](../../../All%20Images/Pasted%20image%2020260916164258.png)
	
Malicious user can access Legit user car's report by changing the **vin** to `1M742JK0ZJB9J639X`
![](../../../All%20Images/Pasted%20image%2020260916164442.png)

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
- change VIN to the other user's value 
![](../../../All%20Images/Pasted%20image%2020260916164611.png)
![916](../../../All%20Images/Pasted%20image%2020260916164809.png)

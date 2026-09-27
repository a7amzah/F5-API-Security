**Fixing/Mitigating OWASP API #1 — Broken Object Level Authorization (BOLA)**
> BOLA happens when an app or API lets you access or change something you shouldn’t be allowed to — like seeing another user’s private data or performing actions on someone else’s account.

## Demo Overview:
After Logging into the application `crAPI` anyone can access the vehicle report of another user if they only have the Vehicle ID.

### Setup:
Testing with two hostnames:

**crapi.local** >> Application protected by WAF

**10.1.10.104** >> Accessing application direct 

### Users:
**Legit User:**
legit-user@example.com/F5@Pass50
	
**Malicious User:**
malicious@example.com/F5@Pass50 

## Issue: Access Another Users Vehicle report
each customer can view his/her own vehicle report via  `/vehicle-service-dashboard?VIN='\<User Vehicle ID\>`

If another user "e.g Attacker" can alter VIN value to the value of another user to access his/her vehicle report.  
- Below VIN of user "Legit User"
<img width="2278" height="1219" alt="image" src="https://github.com/user-attachments/assets/afe11e83-ada3-46f2-8d38-e3f6a4a970eb" />

- from another account 'Malicious' who has no reports 
<img width="2287" height="778" alt="image" src="https://github.com/user-attachments/assets/350f8ec0-1b35-4d95-875b-dd63ba305be1" />

Malicious user can access Legit user car's report by changing the **vin** to `1M742JK0ZJB9J639X`
<img width="2102" height="1164" alt="image" src="https://github.com/user-attachments/assets/bdb8e80a-b609-42ee-836d-7a46a5068942" />


## WAF Mitigation - Dynamic Parameter Tampering
- Reviewing the traffic, we see the parameter is set in the response of uri `/identity/api/v2/vehicle/vehicles`
<img width="2165" height="1020" alt="image" src="https://github.com/user-attachments/assets/836ba99f-9b24-4512-b39b-0715a87af638" />

2- Make sure JSON parameters are parsed for this uri 
<img width="2062" height="1201" alt="image" src="https://github.com/user-attachments/assets/5fa6736a-19bd-4fac-adec-ccb1eec50c0c" />


3- Configure dynamic parameter
<img width="1030" height="681" alt="image" src="https://github.com/user-attachments/assets/889e74a7-ea1d-4f75-a620-5731aef2279d" />

<img width="1119" height="946" alt="image" src="https://github.com/user-attachments/assets/462adc57-4142-46a1-8d53-9916b5ee3e95" />

<img width="1189" height="780" alt="image" src="https://github.com/user-attachments/assets/d09aa229-d6e3-435f-80ae-b1cdedf7468c" />


### Validation
- access user2 vehicle portal
- change VIN to the other user's value 
<img width="1679" height="566" alt="image" src="https://github.com/user-attachments/assets/9e870f1c-453e-4877-afcb-92bed8528c2e" />
<img width="1363" height="806" alt="image" src="https://github.com/user-attachments/assets/8aa15dba-960b-4a30-9cc9-5d591eb97335" />



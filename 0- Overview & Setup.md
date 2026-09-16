## Overview
crAPI (Completely Ridiculous API) is an intentionally vulnerable API-based web app created by OWASP to practice attacking insecure APIs — think of it as<font color="#ffc000"> DVWA, but for modern apps</font>.
It contains real-world vulnerabilities aligned with the OWASP API Top 10, like:
	BOLA (IDOR)
	Broken Auth
	JWT tampering
	Information Disclosure
	Mass Assignment
	And more.

## Setup
- first change the web service template "/crAPI/deploy/helm/templates/web/ingress.yaml" to **NodePort**
<img width="1176" height="698" alt="image" src="https://github.com/user-attachments/assets/d20ac58f-9bb3-472b-bacb-1309adcbd2ba" />
- use helm to install crAPI components in crapi namespace 
```python unfold ln:false unwrap title:"code"
 helm install --namespace crapi crapi . --values values.yaml
```
![alt text](<Pasted image 20260825161907.png>)
![alt text](<Pasted image 20260825161907-1.png>)

## WAF Configuration 
Create WAF API security policy using swagger file `openapi-spec/crapi-openapi-spec.json`

URIs
![alt text](<Pasted image 20260825162342.png>)
Parameters
![alt text](<Pasted image 20260825162325.png>)


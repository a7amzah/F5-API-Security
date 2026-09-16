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
	![[Pasted image 20260825161206.png]]
- use helm to install crAPI components in crapi namespace 
```zsh unfold ln:false unwrap 
 helm install --namespace crapi crapi . --values values.yaml
```

![[Pasted image 20260825161907.png]]
![[Pasted image 20260825161956.png]]

## WAF Configuration 
Create WAF API security policy using swagger file `openapi-spec/crapi-openapi-spec.json`

URIs
![[Pasted image 20260825162342.png]]
Parameters
![[Pasted image 20260825162325.png]]



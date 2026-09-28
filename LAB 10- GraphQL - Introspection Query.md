## Demo Overview:
Using WAF to block GraphQL introspection queiries 

### Setup:
graphql endpoint 

**graphql.local/graphql** |  Generic University Application simulating without and With WAF
 
## Overview:
- Generally, there is a single endpoint that processes all requests, as in GraphQL APIs, where one endpoint manages numerous queries and mutations.
 
```zsh unfold ln:false unwrap title:"Command"
$ curl -s https://graphql.local/graphql --tls-max 1.2 -k    
```
<img width="1067" height="141" alt="image" src="https://github.com/user-attachments/assets/7118bbb7-e4ff-4875-88ac-0a432a94c773" />

An empty json result indicates that a GraphQL <font color="#ffc000">endpoint exists</font>.

## Introspection Without WAF
### Query the available schema
from the GraphQL endpoint, you can use the following query structure *{“query”: “{ \_\_schema { types { name } } }”}*
```zsh unfold ln:false unwrap title:"Command"
curl -s https://10.1.10.107/graphql --tls-max 1.2 -k -X POST -H "Content-Type: application/json" -d '{"query": "{ __schema { types { name } } }"}'  | jq '.data.__schema.types[].name'
```
<img width="2478" height="812" alt="image" src="https://github.com/user-attachments/assets/b4009283-820a-49f4-9011-dde41f7b30cb" />

#### 1- View the data inside the User schema
```zsh unfold ln:false unwrap title:"Command"
$ curl -s -X POST -H "Content-Type: application/json" -d '{"query": "{ __type(name: \"User\") { name fields { name type { name kind ofType { name kind ofType { name kind } } } } } }"}' https://10.1.10.107/graphql -k | jq '.data.__type.fields[].name'
```
<img width="2490" height="510" alt="image" src="https://github.com/user-attachments/assets/7bb17df7-2ddf-435e-bc15-7910126d4383" />

#### 2- view the Role schema
```zsh unfold ln:false unwrap title:"Command"
curl -X POST -H "Content-Type: application/json" -d '{"query": "{ __type(name: \"Role\") { name fields { name type { name kind ofType { name kind ofType { name kind } } } } } }"}' https://10.1.10.107/graphql -ks| jq '.data.__type.fields[].name'
```
<img width="2492" height="303" alt="image" src="https://github.com/user-attachments/assets/4a77a731-b322-4896-96e1-b87e53d51c40" />

#### 3- view the Grade schema
```zsh unfold ln:false unwrap title:"Command"
curl -X POST -H "Content-Type: application/json" -d '{"query": "{ __type(name: \"Grade\") { name fields { name type { name kind ofType { name kind ofType { name kind } } } } } }"}' https://10.1.10.107/graphql -ks| jq '.data.__type.fields[].name'
```
<img width="2489" height="361" alt="image" src="https://github.com/user-attachments/assets/1b8749ec-3cf7-4ecd-a5dc-bdd7c7944d9d" />

#### 4-  Query mutationType without WAF
We will use the *mutationType* within the *\_\_\_\_schema* query<font color="#ffc000"> to retrieve a list of mutations</font>.
```zsh unfold ln:false unwrap title:"Command"
curl -X POST -H "Content-Type: application/json" -d '{"query": "{ __schema { mutationType { name fields { name } } } }"}' https://10.1.10.107/graphql -ks | jq '.data.__schema.mutationType.fields[].name'
```
<img width="2491" height="218" alt="image" src="https://github.com/user-attachments/assets/18fef618-6d6c-456f-b5b7-5d66e9612a0d" />

Perfect! We’ve found the list of **mutations** that we can use to update data through GraphQL.

## Introspection With WAF
### WAF Configuration 
<img width="1171" height="836" alt="image" src="https://github.com/user-attachments/assets/fbdfdeff-9f7e-427c-8638-f2738652e0ca" />
<img width="2114" height="1097" alt="image" src="https://github.com/user-attachments/assets/2ecc26a5-e685-4fb7-8ec2-a07dc9c1390f" />

### Query the available schema
```zsh unfold ln:false unwrap title:"Command"
$  curl -s https://graphql.local/graphql --tls-max 1.2 -k -X POST -H "Content-Type: application/json" -d '{"query": "{ __schema { types { name } } }"}'
```
<img width="2461" height="443" alt="image" src="https://github.com/user-attachments/assets/276bc98d-a132-4e8e-8e9b-32fc08c71d95" />
<img width="2517" height="912" alt="image" src="https://github.com/user-attachments/assets/a2d7fdf2-cd1d-4d96-9d3b-619522112333" />

WAF GraphQL profile is blocking any introspection attempts.

#### 1- View the data inside the User schema
```zsh unfold ln:false unwrap title:"Command"
 curl -s -X POST -H "Content-Type: application/json" -d '{"query": "{ __type(name: \"User\") { name fields { name type { name kind ofType { name kind ofType { name kind } } } } } }"}' https://graphql.local/graphql -k | jq '.data.__type.fields[].name'
```
<img width="2485" height="418" alt="image" src="https://github.com/user-attachments/assets/a2dfadac-f929-4cbd-990c-a3321b6d2577" />
<img width="2483" height="875" alt="image" src="https://github.com/user-attachments/assets/d252c8d0-c2a5-44cd-87fa-173b18e176c0" />


#### 2- view the Role schema
```zsh unfold ln:false unwrap title:"Command"
curl -X POST -H "Content-Type: application/json" -d '{"query": "{ __type(name: \"Role\") { name fields { name type { name kind ofType { name kind ofType { name kind } } } } } }"}' https://graphql.local/graphql -ks| jq '.data.__type.fields[].name'
```
<img width="2498" height="345" alt="image" src="https://github.com/user-attachments/assets/81b00d2c-5558-4952-89ce-ee4e282bbcd0" />
<img width="1474" height="842" alt="image" src="https://github.com/user-attachments/assets/ef667859-b980-456b-87a4-dec452e3ddfc" />

#### 3- view the Grade schema
```zsh unfold ln:false unwrap title:"Command"
curl -X POST -H "Content-Type: application/json" -d '{"query": "{ __type(name: \"Grade\") { name fields { name type { name kind ofType { name kind ofType { name kind } } } } } }"}' https://graphql.local/graphql -ks
```
<img width="2496" height="339" alt="image" src="https://github.com/user-attachments/assets/101779cd-6202-4443-aa7f-7bf2bd20c81d" />
<img width="1473" height="837" alt="image" src="https://github.com/user-attachments/assets/cf9cca18-1b35-4c56-9f1a-8854b9249b34" />

#### 4- Query mutationType With WAF
We will use the *mutationType* within the *\_\_\_\_schema* query<font color="#ffc000"> to retrieve a list of mutations</font>.
```zsh unfold ln:false unwrap title:"Command"
curl -X POST -H "Content-Type: application/json" -d '{"query": "{ __schema { mutationType { name fields { name } } } }"}' https://gra/graphql -ks | jq '.data.__schema.mutationType.fields[].name'
```
<img width="2492" height="355" alt="image" src="https://github.com/user-attachments/assets/53bad39a-89e5-4b70-87f8-6f3766c4fc8f" />
<img width="1464" height="856" alt="image" src="https://github.com/user-attachments/assets/dd99e8b5-af88-4471-97cf-03005f21a55f" />





# JQ tips

## Disclaimer
There is no need for this nonsense. Refer to this awesome person: [Lzone jq cheatsheet](https://lzone.de/cheat-sheet/jq)

## Installation
`sudo apt install jq`

---
## Filtering (Grepping) for specific key values, then selecting a specific key afterwards
* Example given: Bloohound collector’s json data. Finding hosts with Unconstrained Delegation.
```bash
cat 20210401_computers.json|jq '.computers[] |select(.Properties.unconstraineddelegation ==true) | .Properties.name'
```
## Printing 2 or more values 
* Example: For priting the email and password/hashed_password from dehashed at the same time
```bash
cat dehashed.txt|jq '.entries[] | {email,password}'
cat evilginx_sessions.json|jq -r 'keys[] as $k | "\(.[$k] | .name)=\(.[$k] | .value)"' 
```

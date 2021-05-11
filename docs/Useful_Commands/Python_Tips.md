# Useful tips and tricks in Python

## TEMPLATE or Skeleton script with tips and references
* This can be found in [TEMPLATE.py](https://github.com/trojand/Script_Yard/blob/main/TEMPLATE.py) which is in the [Script_Yard](https://github.com/trojand/Script_Yard) repo

## Generate requirements.txt
* JCharisTech[^1]
``` bash
pip3 install pipreqs
pipreqs /path/to/project
# OR
pipreqs $(pwd)
# Manually check the requirements.txt also by installing pip3 install -r requirements.txt
```
[^1]: [JCharisTech](https://blog.jcharistech.com/2020/11/02/how-to-create-requirements-txt-file-in-python/)

## Global Variables
```python
# Below "import" <insert_package_names>
timeout = None

def imAFunction():
  if i > timeout:
    print("Greater than timeout")

# Inside function. In this example main()
global timeout 
timeout = args.timeout
```

## URL Encode Python String
```python
import urllib.parse
action = urllib.parse.quote_plus("GET_[TO]-THE")
location = urllib.parse.quote_plus("CHOPP@!")
url = "www.domain.com:2323/whatever?action=%s&location=%s" % (action,location)
```

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


## Update all pip packages
```bash
python3 -m pip install --upgrade pip
pip3 list --outdated --format=freeze | grep -v '^\-e' | cut -d = -f 1  | xargs -n1 pip3 install -U
```

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

## Accept custom certificate
* When using proxy (i.e. Burp), rather than using `verify=False` in the `request.get` or `request.post`, convert the Burp certificate cacert.der instead to cacert.pem then use in on python requests. [^2]
```python
# Converting cacert.der to cacert.pem
# openssl x509 -in cacert.der -inform DER -outform PEM -out cacert.pem
r = session.post(burp0_url, headers=burp0_headers, data=burp0_data,proxies=proxies,verify="cacert.pem")
```

[^1]: [JCharisTech](https://blog.jcharistech.com/2020/11/02/how-to-create-requirements-txt-file-in-python/)
[^2]: [Medium - Gitconnected](https://levelup.gitconnected.com/solve-the-dreadful-certificate-issues-in-python-requests-module-2020d922c72f)

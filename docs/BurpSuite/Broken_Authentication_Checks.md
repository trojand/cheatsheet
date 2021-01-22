# Broken Authentication Checks

## Requirements:

* Account / Authentication on the web application[^1]


## Instructions
1. Login to the application using credentials while being passed through Burp Suite (Intercept: Off)
    * Intercept: Off
    * Make sure target is in scope and is being recorded (History)
1. Browsing / Crawling
    * Browse to all pages
    * Try all functions
        * Change password
        * Add / Edit / Delete
            * accounts
            * data/entries
1. In **Target** -> **Sitemap, on the specific URL/host** -> "**Copy URLs in this host**"
1. In a text editor/grep, remove the base URL
    * i.e. `https://domain.com/some/function/page.aspx` -> `/some/function/page.aspx`
1. Logout from the web application (Destory the session / Expire)
1. On Burp's Intruder, Mode: Sniper
    1. Input the link as payload for the Intruder on Line 1
        * Examples:
             * `GET /some/function/page.aspx HTTP/1.1`
             * `POST /some/function/changepassword HTTP/1.1`
1. Monitor for the response code and size
1. For links / function with potential
    1. Retrieve the correct data(parameters etc.) and try again
    
    
[^1]: [websecgeeks](https://www.websecgeeks.com/2015/05/testing-of-broken-session-management.html)

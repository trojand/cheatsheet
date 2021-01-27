# Persistent Automated Collaborator

## Description

Used for continuous retrieval and saving of Burp Collaborator results.

Useful in DNS exfiltration [^1]


## Instructions

Note: Make sure to note and replace the "KEY"

1. In Burp go to `Project options -> Misc` and check `Poll over unencrypted HTTP`
1. Open Collaborator: `Burp menu -> Burp Collaborator client`
1. Run tshark:
    1. `#!bash sudo tshark -Y http -T fields -e http.request.method -e http.request.uri -e http.host -e http.request.uri`
1. ‘Poll’ interactions in the Collaborator client and observe following request in tshark:
    1. `GET polling.burpcollaborator.net /burpresults?biid=KEY`
1. Acquire one or more (depending on your needs) Collaborator’s hostnames (number to generate & 'copy to clipboard')
1. Now you can retreive (also after closing the Collaborator client) interactions with your Collaboarator’s hostnames by requesting:
    1. `#!bash curl http://polling.burpcollaborator.net/burpresults?biid=KEY`
    

## Filtered Command
```bash
curl http://polling.burpcollaborator.net/burpresults?biid=KEY | cut -d \" -f 24|cut -d . -f 1
```

## Loop Command
```bash
while true; do VALUE=$(curl -s -XGET "https://polling.burpcollaborator.net/burpresults?biid=KEY" | cut -d \" -f 24|cut -d . -f 1| tr -d {|tr -d \} ) && if [ -n "$VALUE" ]; then echo $VALUE >> ~/Results/BurpSuite/Collaborator.txt; fi && sleep 1; done
```

## Update (October 23, 2020)
Please also see [Collabfiltrator](https://github.com/0xC01DF00D/Collabfiltrator)


[^1]: [@mzet-](https://mzet-.github.io/2019/09/09/burp-suite-pro-rltandt-collaborator-presistence.html)
[^2]: [Same person's blog](https://0x00sec.org/t/achieving-persistent-access-to-burp-collaborator-sessions/14311)

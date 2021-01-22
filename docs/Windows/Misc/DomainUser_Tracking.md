# Track or Trace a domain user's host machine

## Description

* Used when Bloodhound does not give you the host where a user is logged-on or has session on
* Long term tracking of the host that a user is logged-in to
* Note: There are much better ways than this, you can use this when you don't have any more options
* Can be used for monitoring a target's behaviour/routine/tasks and accessing host machines of VIPs for PoC/screenshots (i.e. Executives)
* [netview.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/netview.py) (part of Impacket from @SecureAuthCorp)

## Tools/Instructions

* If `netview.py` does not work (somehow) running Sharphound with sufficient domain privileges usually does the job, but if not:
	* Crackmapexec:
	    ```bash
	    cme smb -d <domain> -u <Domain_Admin> -p <Password> --loggedon-users x.x.x.x/XX
	    cme smb --local-auth -u <Local_Admin> -p <Password> --loggedon-users x.x.x.x/XX
	    ```
	* Event Viewer:
	    * On the Domain Controller: `Windows -> Security`:
	        * Filter Event IDs:
	            * Windows 2008 R2 and above: 4624
	            * Windows 2003 and below: 540 or 528
	            
	            

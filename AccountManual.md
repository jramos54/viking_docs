Email: [joel.ramos@viking.com](mailto:joel.ramos@viking.com)  
User name: joel.ramos  

Mail WebApp: [https://outlook.office.com/](https://outlook.office.com/)
Teams WebAPP: [https://teams.microsoft.com/v2/](https://teams.microsoft.com/v2/) 
ITServiceCenter: [https://itservicecenter.viking.com/ ](https://itservicecenter.viking.com/)Github setup:
1. ~~Create GitHub account with @viking.com email.~~
2. If you already have an invitation for the viking organization in your email click “join organization” link in the email. If not, ask your PM to create a DO ticket in JIRA epic [https://vikingtravel.atlassian.net/browse/DO-2508](https://vikingtravel.atlassian.net/browse/DO-2508)  
3. ~~Enable 2FA. In case you face problems [here are the docs](https://docs.github.com/es/authentication/securing-your-account-with-two-factor-authentication-2fa/troubleshooting-two-factor-authentication-issues). Very likely it's related to clock synchronization due to different devices/timezones.~~
4. Join the Viking organization
5. Create SSH key in your notebook 
6. Add to GH (Github -> settings -> SSH and GPG keys / SSH keys / add key)
7. Connect with SSO (in Github -> settings -> SSH and GPG keys / SSH keys / Authentication keys / your key / Configure SSO (here select viking-cruises to allow the key to work for the organization related repos)
8. Download repos with SSH authentication. Follow instructions for installing/configuring
9. Once signed in, look for the Dedicated Power VDI (Must be WUS2).   Open and software will begin to download.   
10. List of initial software to expect: 
11. ~~Office~~
12. ~~Teams~~
13. ~~Visual Studio 2022~~
14. ~~Framework 4.8.1~~
15. ~~SSMS 22~~
16. ~~Visual Studio Code~~
17. ~~Postman~~
18. ~~Git~~
19. GitHub Desktop
20. Beyond Compare
21. Snagit
22. Notepad ++
If you require any other software, please inform your PM. 
NOTE: There are very strict security protocols in place for the VDI usage. DO NOT Install anything in the VDI unless specifically approved by the management team.
### Access to Environments: 
Most of the Web environments URLs will need you to be connected to the VPN using a US WEST gateway, follow the next steps:
1. If you see this screen, problem is the Gateway from the VPN
2. Make sure you have Global Protect in your VDI
3. If not, request Global Protect from Service Now. You will need ask your PM for e-mail Approvals from Viking’s Manager and upload the approval. 
4. Click in the Global Protect icon and select the Change Gateway button
5. Select US West and wait for 3 seconds. Now you should be able to connect to URLs.  
6. IF you see this screen, means you don’t have access to the Application itself, contact your Tech Manager or Tech Lead for access

https://vikingtravel.atlassian.net/browse/DO-3210
https://vikingtravel.atlassian.net/browse/DO-3209

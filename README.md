# self-signed-SSL
Deploy self signed SSL certificate in Rocky Linux.

Steps followed to Activate https service & deploy Self-Signed SSL certificate
  
  1.  Installed SSL package using command **dnf install mod_ssl**
  ![image](https://github.com/hoque-reza/self-signed-SSL/assets/144583935/9f64d5fa-74a6-4868-a6b4-71f031ec457d)
  
  2.  Checked the SSL port listening by using command **telnet 127.0.0.1 443**. Connection was refused. 
      Applied changes to Webserver by using command **systemctl restart httpd**. Then again checked the if SSL port was listening, this time it was connected.
      
  ![image](https://github.com/hoque-reza/self-signed-SSL/assets/144583935/22ea886b-bd9f-413b-bbdb-2350ed753ad8)

  4.  Allowed https service at firewall: **firewall-cmd --permanent --zone=public --add-service=https** 
      Then Reloaded the firewall to apply changes: **firewall-cmd --reload**
      
      ![image](https://github.com/hoque-reza/self-signed-SSL/assets/144583935/3da360af-ca7f-4475-bbec-e6b8bbd79f8b)

  5. Checked the status: **firewall-cmd --list-all**
     It was showing the https service as below in red mark
  ![image](https://github.com/hoque-reza/self-signed-SSL/assets/144583935/d62463c0-02ab-4965-ba5d-50bb9054a236)

  6. Hit the browser with https://your-public-IP-address (My case - https://142.3.24.31) and found below warning.
      ![image](https://github.com/hoque-reza/self-signed-SSL/assets/144583935/6feae8ac-20f7-43b3-8dfc-6ac88eda19a9)

  7. Clicked on the Continued to 142.3.24.31(unsafe) and window popped up as below. It proved https service was running.
      ![image](https://github.com/hoque-reza/self-signed-SSL/assets/144583935/8cf2b3a9-73d2-4f4e-8d75-3dadccbed5b9)

  8. Generated a 2048-bit RSA private key: **openssl req -new -sha256 -newkey rsa:2048 -nodes - keyout my.key -out my.csr**
      ![image](https://github.com/hoque-reza/self-signed-SSL/assets/144583935/08330ba5-430e-4535-b229-e9b427d5836c)

  9. Filled up the following info while generating the key: [Please fill up with your own details]
        a. Country Name (2 letter code) [XX]: CA
        b. State or Province Name (full name) []: Saskacthewan
        c. Locality Name (eg, city) [Default City]: Regina
        d. Organization Name (eg, company) [Default Company Ltd]: University of Regina
        e. Organizational Unit Name (eg, section) []: Engineering
        f. Common Name (eg, your name or your server's hostname) []: ed489-ense03
        g. Email Address []: mrh165@uregina.ca
     
  10. Signed the certificate: openssl x509 -req -days 365 -in my.csr -signkey my.key -out my.crt
     Moved my.key file to following directory: **mv my.key /etc/pki/tls/private/**
     Moved my.crt file to following directory: **mv my.crt /etc/pki/tls/certs/**
    ![image](https://github.com/hoque-reza/self-signed-SSL/assets/144583935/adc0d00e-5a5d-42dc-b79e-83583b488a9e)

  11. Went to: **cd /etc/httpd/conf.d/** and took backup of ssl.conf to root folder: **cp ssl.conf /root/**
      ![image](https://github.com/hoque-reza/self-signed-SSL/assets/144583935/3ea4ccba-e317-4822-8cc1-9ca678a7d31f)

  12. Edit the **ssl.conf** file as below: **emacs ssl.conf** [**emacs** is my linux text editor, you can use any text editor as you like (vi, nano etc.)]
      **/etc/pki/tls/private/localhost.crt → /etc/pki/tls/certs/my.crt**
      
      **/etc/pki/tls/private/localhost.key/ → etc/pki/tls/private/my.key**
      <img width="776" alt="image" src="https://github.com/hoque-reza/self-signed-SSL/assets/144583935/2abdd852-61bc-4c10-a2e1-aa517216000e">

  13. Restart the webserver: **systemctl restart httpd** and got error as below.
      ![image](https://github.com/hoque-reza/self-signed-SSL/assets/144583935/f2fb83a9-702c-47be-ae32-f2e7a038d737)

  14. Stop the selinux: **setenforce 0** and restarted the httpd service: **systemctl restart httpd**
      (listening on: port 443 which is the default https port proved https service was running fine)
      ![image](https://github.com/hoque-reza/self-signed-SSL/assets/144583935/ef31218c-2755-4201-b97b-5b0df7ecb654)

  15. Checked self-signed SSL certificate info: Matched with provided info during key generation and it proved installed SSL certificate was running.
      ![image](https://github.com/hoque-reza/self-signed-SSL/assets/144583935/e1aebdfb-8f6c-4f48-94a4-326a6580bc0b)


   



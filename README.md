## VaultWarden:

+ *Update server*
+ *Docker installation*
+ *SSL cetificate*
+ *Download and run VaultWarden*
+ *Update VaultWarden*

**1.) Update server:**
  ```
  sudo apt update && sudo apt upgrade
  ```


**2.) Docker installation:**
  - ***Install:***
    ```
    curl -fsSL https://get.docker.com -o get-docker.sh
    sh get-docker.sh
    ```
    - or
    ```
    curl -fsSL https://test.docker.com -o test-docker.sh
    sh test-docker.sh
    ```
  - ***Add user to Docker group:***
    ```
    sudo usermod -aG docker <username>
    ```
    - reboot server
    ```
    sudo reboot
    ```
    - after reboot, you'll be able to run docker command without sudo permission:
    ```
    docker run test-123
    docker -v
    ```


**3.) SSL certificate:**
  > [!NOTE]
  > Certificate is valid for 365 days, than just repeate these steps.
  - ***Create folder and cd into it:***
    ```
    mkdir certs
    cd certs/
    ```
  - ***Create private CA key and set pass phrase:***
    ```
    openssl genpkey -algorithm RSA -aes128 -out private-ca.key -outform PEM -pkeyopt rsa_keygen_bits:2048
    ```
  > [!NOTE]
  > Enter your pass phrase, type **.** to everything exept Common Name (type e.g. VaulWarden-cert), so you can recognize your certs 
  - ***Create CA Certificate:***
    ```
    openssl req -x509 -new -nodes -sha256 -days 365 -key private-ca.key -out vaultwarden-ca-cert.crt
    ```
  - ***Create VaultWarden key:***
    ```
    openssl genpkey -algorithm RSA -out vaultwarden.key -outform PEM -pkeyopt rsa_keygen_bits:2048
    ```
  > [!IMPORTANT]
  > Type **.** to everything exept Common Name, here you write your rpi's IP (e.g. 192.168.1.15)
  - ***Create vaultwarden certificate request file:***
    ```
    openssl req -new -key vaultwarden.key -out vaultwarden.csr
    ```
  - ***Create `vaultwarden.ext`***
    - create file:
    ```
    sudo nano vaultwarden.ext
    ```
    - put inside following content and change it to your setup:
    ```
    authorityKeyIdentifier=keyid,issuer
    basicConstraints=CA:TRUE
    keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
    extendedKeyUsage = serverAuth
    subjectAltName = @alt_names

    [alt_names]
    IP.1 = <your-ip>
    ```
    - and save the file (CTRL + X, and than Y)
  - ***Create VaultWarden certificate:***
    ```
    openssl x509 -req -in vaultwarden.csr -CA vaultwarden-ca-cert.crt -CAkey private-ca.key -CAcreateserial -out vaultwarden.crt -days 365 -sha256 -extfile vaultwarden.ext
    ```
  - ***Move Certificates to the system folder for Certificates:***
    ```
    sudo mv vaultwarden.crt vaultwarden.key /etc/ssl/certs
    ```
  - ***Use certificates:***
    - move your certificate `vaultwarden-ca-cert` out from server (e.g. FTP), and put it in the browser or in your mobile:
      - **[Chrome](https://docs.vmware.com/en/VMware-Adapter-for-SAP-Landscape-Management/2.1.0/Installation-and-Administration-Guide-for-VLA-Administrators/GUID-D60F08AD-6E54-4959-A272-458D08B8B038.html)**
      - **[FireFox](https://docs.vmware.com/en/VMware-Adapter-for-SAP-Landscape-Management/2.1.0/Installation-and-Administration-Guide-for-VLA-Administrators/GUID-0CED691F-79D3-43A4-B90D-CD97650C13A0.html)**
      - **[Opera](https://wiki.wmtransfer.com/projects/webmoney/wiki/Installing_root_certificate_in_Opera)**
      - **[Microsoft Edge](https://www.ibm.com/docs/en/devops-test-hub/10.5.3?topic=lists-importing-certificate-authority-into-microsoft-edge-browser)**
      - **[Safari](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/making-ssl-certificates-trusted-in-safari-1910.html)**
      - **[Android](https://support.securly.com/hc/en-us/articles/212869927-How-do-I-install-Securly-SSL-certificate-on-Android-device)**
      - **[IOS](https://support.apple.com/en-us/102390)**

    
**4.) Download and run VaultWarden:**
  - ***Pull lastest docker image:***
    ```
    docker pull vaultwarden/server:latest
    ```
  - ***Run VaultWarden:***
    ```
    docker run -d --name vaultwarden --restart unless-stopped -v /vw-data:/data -v /etc/ssl/certs:/ssl -e ROCKET_TLS='{certs="/ssl/vaultwarden.crt",key="/ssl/vaultwarden.key"}' -e ADMIN_TOKEN=<you-can-type-your-admin-token-here(any_long_password)> -p 8080:80 vaultwarden/server:latest
    ```
  - ***Download BitWarden app:***
    - **[Chrome](https://chrome.google.com/webstore/detail/bitwarden-free-password-m/nngceckbapebfimnlniiiahkandclblb/related?hl=en)**
    - **[FireFox](https://addons.mozilla.org/en-US/firefox/addon/bitwarden-password-manager/)**
    - **[Opera](https://addons.opera.com/en/extensions/details/bitwarden-free-password-manager/)**
    - **[Microsoft Edge](https://microsoftedge.microsoft.com/addons/detail/bitwarden-free-password/jbkfoedolllekgbhcbcoahefnbanhhlh?hl=en-GB)**
    - **[Safari](https://bitwarden.com/help/install-safari-app-extension/)**
    - **[Android](https://play.google.com/store/apps/details?id=com.x8bit.bitwarden&hl=en_US)**
    - **[IOS](https://apps.apple.com/us/app/bitwarden-password-manager/id1137397744)**


**5.) Update VaultWarden:**
  - ***Stop VaultWarden:***
    ```
    docker stop vaultwarden
    docker rm vaultwarden
    ```
  - ***Download lastest image:***
    ```
    docker pull vaultwarden/server:latest
    ```
  - ***Run VaultWarden:***
    ```
    docker run -d --name vaultwarden --restart unless-stopped -v /vw-data:/data -v /etc/ssl/certs:/ssl -e ROCKET_TLS='{certs="/ssl/vaultwarden.crt",key="/ssl/vaultwarden.key"}' -e ADMIN_TOKEN=<you-can-type-your-admin-token-here(any_long_password)> -p 8080:80 vaultwarden/server:latest
    ```


## Sources:
+ https://www.youtube.com/watch?v=eCJA1F72izc
+ https://github.com/dani-garcia/vaultwarden
+ https://github.com/dani-garcia/vaultwarden/wiki/Private-CA-and-self-signed-certs-that-work-with-Chrome

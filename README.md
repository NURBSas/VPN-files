# VPN-files
Viskas apie VPN servisą

![WG](https://images.sftcdn.net/images/t_app-icon-s/p/202bac9d-51e4-47a6-8a02-0f02f344c184/2940667803/wireguard-144361_O.png)

## Wireguard instaliacija (ubuntu server ir windows client)

Pradžioje užsiinstaliuojam ubuntu server:

     sudo apt update
     sudo apt install wireguard -y
     
Konfiguruojam serverį:

     sudo nano /etc/sysctl.conf

Atidarius failą patikrinam ar atidaryta eilutė:
(Ši komanda atsakynga už adresų peradresavimą)

     net.ipv4.ip_forward=1 
     net.ipv6.conf.all.forwarding=1 #(Jai norime naudoti IPv6 standartą)

Toliau patikrinam tinklo interfeiso pavadinimą:

     ip route list default

_default via XX.XX.XX.XX dev eth0 onlink_

**eth0** <-- mūsų tinklo plokštė per kurią ir eina visas srautas.

Toliau tikrinam FireWall'ą

     sudo ufw disable
     sudo ufw enable
     sudo ufw status

 Turim gauti atsakymą:

_51866/udp ALLOW Anywhere_

_OpenSSH ALLOW Anywhere_

Toliau paleidžiam patį WG servisą:

     sudo systemctl enable wg-quick@wg0.service
     sudo systemctl start wg-quick@wg0.service

Jai norime tik perkrauti tai:

     sudo systemctl restart wg-quick@wg0.service

Žiūrime į WG serviso būseną:

     sudo systemctl status wg-quick@wg0.service     
     
### Pagrindinės komandos konfiguruojant Wireguard serverį:

Komanda generuojanti WG privatų raktą:

     wg genkey

Komanda generuojanti viešajį raktą:

     echo XXprivatus-raktasXX | wg pubkey 

Komanda generuojanti patvirtinantijį _PresharedKey_

     wg genpsk

Komanda redaguojanti pagrindinį konfiguracinį failą (wg0 virtualaus interfeiso, pavadinimas gali būti ir kitoks):

     sudo nano /etc/wireguard/wg0.conf

Failo viduje turėtu būti tokia konfiguracija: 
_(pirma serverio duomenys poto klientų)_

     Address = 16.0.0.1/24
     SaveConfig = true
     ListenPort = 51266 #(Priklausomai koks atviras portas yra pas jus tokį ir įrašykite)
     PrivateKey = xxxxxxxxxxxxxxx #(privatus serverio raktas)
     PostUp = iptables -A FORWARD -i %i -j ACCEPT; iptables -t nat -A POSTROUTING -o ens3 -j MASQUERADE
     PostDown = iptables -D FORWARD -i %i -j ACCEPT; iptables -t nat -D POSTROUTING -o ens3 -j MASQUERADE

     [Peer]
     PublicKey = xxxxxxxx #(kliento viešas raktas)
     PresharedKey = xxxxxxxxxx #(bendras patikimumo raktas)
     AllowedIPs = 16.0.0.2/24

Kliento pusėje turėtų būti.

     [Interface]
     PrivateKey = MK8WzzosyUVFX01B89bMjGLiP9zjXKE/gjzse7oG2GI=
     Address = 16.0.0.2/32
     DNS = 8.8.8.8

     [Peer]
     PublicKey = xxxxxxxxx #(serverio viešas raktas)
     AllowedIPs = 0.0.0.0/0
     Endpoint = XX.XX.XX.XX:51266 #(Jūsų išorinis IP adresas)
     PersistentKeepalive = 20

Norint pridėti ar pakeisti kažką **wg0.conf** faile ir kad tai suveiktu reikia perkrauti patį servisą.

Serviso sustabdymo komanda:

     wg-quick down wg0

Pakoregavus **wg0.conf** failą reikia vėl paleisti servisą komanda:

     wg-quick up wg0

Po perkrovimo esamas **wg0.conf** pasikeičia tai yra perrašomas.

#### SVARBU!

Norint pridėti papildomą vartotoją reikia iš pirmo sustabdyti servisą **wg-quick down wg0** tada perrašyti **wg0.conf** failą _(pridėti vartotojo duomenis)_ 
ir tik po to startuoti servisą vėl komanda **wg-quick up wg0**

### Papildomai dar galima sukonfiguruoti NGINX serverį:

Atnaujinam ubuntu ir instaliuojame **Nginx** serverį ir jį paleidžiame:

     sudo apt update -y && sudo apt install -y nginx

     sudo systemctl start nginx

Toliau tikrinam konfiguracinį failą ir jį pakoreguojam:
Susirandam vietą su  _stream{_ ir įrašom sekančias eilutes:

     server {
     listen 80 udp;
     proxy_pass 127.0.0.1:51866;
     }

Tuomet paleidžiame komandą kuri paleidžia atgalinį proxy per 80 portą:
     
     sudo nginx -t

PAtikrinam ar WG serveris veikia tvarkingai ir kas vyksta.
Tam yra komanda

     wg

arba _wg show_


### Windows kliento instaliacija (paprastas AD/lokalus vartotojas)

Tesiame galimai iškilsiančių problemų sprendimus.
Windows klientas kuris neturi admin teisių gali susidurti su instaliacijos ir paleidimo problemomis.
Sprandžiasi jos taip. Vartotojo aplinkoje atidarome **CMD** administratoriaus vardu:

Registrai kuriuos reikia sukurti:

     reg add HKLM\Software\WireGuard /v LimitedOperatorUI /t REG_DWORD /d 1 /f

     reg add HKLM\Software\WireGuard /v DangerousScriptExecution /t REG_DWORD /d 1 /f

Norint užsinstaliuoti wireguard klientą.

Pasitikrinam kliento vardą **powershell'e** komanda: **whoami**

Reikia vartotoja pridėti prie _Administrators_ grupės:

     net localgroup administrators AzureAD\vardaspavarde /add
     
Vartotojo vardas gali būti ir toks kaip c:/Users/xxxx -->  net localgroup administrators /add "AzureAD\UserUpn"
     
     net localgroup "Network Configuration Operators" /add "AzureAD\vardas.pavarde@contoso.lt"

Pasitikrinam ar mūsų vartotojas atsirado ten kur reikia **CMD** komanda **compmgmt.msc**

Aarba naudojant **Win + X** ir pasirenkant **"Computer Managment"**

užinstaliuojam wireguard'ą ir tada

     net localgroup administrators AzureAD\vardaspavarde /delete



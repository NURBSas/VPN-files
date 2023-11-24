# VPN-files
Viskas apie VPN servisą

## Wireguard instaliacija (ubuntu server ir windows client)

Pradžioje užsiinstaliuojam ubuntu server:

     sudo apt update
     sudo apt install wireguard -y

Pagrindinės komandos konfiguruojant Wireguard serverį:

Komanda generuojanti WG privatų raktą:

     wg genkey

Komanda generuojanti viešajį raktą:

     echo XXprivatus-raktasXX | wg pubkey 

Komanda redaguojanti pagrindinį konfiguracinį failą (wg0 virtualaus interfeiso, pavadinimas gali būti ir kitoks):

     sudo nano /etc/wireguard/wg0.conf

Failo viduje turėtu būti tokia konfiguracija: 
(pirma serverio duomenys poto klientų)

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

Testuojam:


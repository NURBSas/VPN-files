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
     ListenPort = 51280 (Priklausomai koks atviras portas yra pas jus tokį ir įrašykite)                                                                                                                           
     PrivateKey = xxxxxxxxxxxxxxx
     PostUp = iptables -A FORWARD -i %i -j ACCEPT; iptables -t nat -A POSTROUTING -o ens3 -j MASQUERADE
     PostDown = iptables -D FORWARD -i %i -j ACCEPT; iptables -t nat -D POSTROUTING -o ens3 -j MASQUERADE

     [Peer]
     PublicKey = xxxxxxxx
     PresharedKey = xxxxxxxxxx
     AllowedIPs = 16.0.0.2/24


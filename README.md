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



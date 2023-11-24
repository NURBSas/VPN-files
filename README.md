# VPN-files
Viskas apie VPN servisą

## Wireguard instaliacija (ubuntu server ir windows client)

Pradžioje užsiinstaliuojam ubuntu server:

     sudo apt update
     sudo apt install wireguard -y

Pagrindinės komandos konfiguruojant Wireguard serverį:

     wg genkey <-- komanda generuojanti WG privatų raktą
     
     echo XXprivatus-raktasXX | wg pubkey <-- komanda generuojanti viešajį raktą



# VPN-files
Viskas apie VPN servisą

## Wireguard instaliacija (ubuntu server ir windows client)

sudo apt update
sudo apt install wireguard -y

wg genkey <-- komanda generuojanti WG privatų raktą
echo XXprivatus-raktasXX | wg pubkey <-- komanda generuojanti viešajį raktą



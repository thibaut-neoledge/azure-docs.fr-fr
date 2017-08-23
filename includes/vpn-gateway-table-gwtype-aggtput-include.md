Azure propose les références SKU de passerelle VPN suivantes :

|**Référence (SKU)**   | **S2S/VNet-to-VNet<br>Tunnels** | **P2S<br>Connexions** | **Agrégat<br>Référence de débit** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| Bande passante 30                         | Bande passante 128               | 650 Mbits/s                    |
|**VpnGw2**| Bande passante 30                         | Bande passante 128               | 1 Gbit/s                      |
|**VpnGw3**| Bande passante 30                         | Bande passante 128               | 1,25 Gbits/s                   |
|**De base** | Bande passante 10                         | Bande passante 128               | 100 Mbits/s                    | 
|          |                                 |                        |                             | 

- La référence de débit agrégée est basée sur les mesures de plusieurs tunnels agrégés via une passerelle unique. Le débit n’est pas garanti en raison de conditions de trafic Internet et des comportements de votre application.

- Pour des informations sur les prix, consultez la page [Tarification](https://azure.microsoft.com/pricing/details/vpn-gateway) .

- Vous trouverez des informations relatives au contrat de niveau de service (SLA) sur la page [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).
Il existe 3 SKU de passerelle VPN :

- De base
- Standard
- Hautes performances

Le tableau ci-dessous indique les types de passerelle et le débit total estimé. La tarification varie en fonction des différents SKU de passerelle. Pour plus d'informations sur la tarification, consultez la [tarification de passerelle VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/). Cette table s’applique aux modèles de déploiement classique et Resource Manager.


| | **Débit de passerelle VPN** | **Tunnels IPsec max de passerelle VPN** | **Débit de passerelle ExpressRoute** | **Passerelle VPN et ExpressRoute coexistants**|
|--- |----------------------------|-----------------------------------|-------------------------------------|-----------------------------------------|
| **Référence De base** | 100 Mbits/s | 10 | 500 Mbits/s | Non |
| **Référence Standard** | 100 Mbits/s | 10 | 1 000 Mbits/s | Oui |
| **Référence Hautes performances** | 200 Mbits/s | 30 | 2 000 Mbits/s | Oui |


**Remarque :** le débit de la passerelle VPN est une estimation calculée à partir de mesures entre réseaux virtuels dans la même région Azure. Il ne s’agit pas d’une garantie de débit pour des connexions entre différents locaux sur Internet, ce chiffre doit être considéré comme une mesure maximale potentielle.

<!---HONumber=AcomDC_0224_2016-->
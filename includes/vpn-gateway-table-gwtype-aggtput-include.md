| | **Débit de passerelle VPN (1)** | **Tunnels IPsec max de passerelle VPN (2)** | **Débit de passerelle ExpressRoute** | **Passerelle VPN et ExpressRoute coexistants**|
|--- |----------------------------|-----------------------------------|-------------------------------------|-----------------------------------------|
| **Référence De base** | 100 Mbits/s | 10 | 500 Mbits/s | Non |
| **Référence standard (3)** | 100 Mbits/s | 10 | 1 000 Mbits/s | Oui |
| **Référence Hautes performances (3)** | 200 Mbits/s | 30 | 2 000 Mbits/s | Oui |

- (1) Le débit de la passerelle VPN est une estimation calculée à partir de mesures entre réseaux virtuels dans la même région Azure. Ce débit n’est pas garanti pour les connexions intersites via Internet. Il s’agit de la mesure du débit maximal possible.
- (2) Le nombre de tunnels fait référence aux VPN basés sur un itinéraire. Un VPN basé sur des stratégies peut uniquement prendre en charge un seul tunnel VPN de site à site.
- (3) Les VPN basés sur des stratégies ne sont pas pris en charge sur cette référence SKU. Ils sont uniquement pris en charge sur la référence SKU de base.

<!---HONumber=AcomDC_0921_2016-->
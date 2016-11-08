|  | **Débit de passerelle VPN (1)** | **Tunnels IPsec max de passerelle VPN (2)** | **Débit de passerelle ExpressRoute** | **Passerelle VPN et ExpressRoute coexistants** |
| --- | --- | --- | --- | --- |
| **Référence SKU De base (3)** |100 Mbits/s |10 |500 Mbits/s |Non |
| **Référence SKU Standard (4)** |100 Mbits/s |10 |1 000 Mbits/s |Oui |
| **Référence SKU Hautes performances (4)** |200 Mbits/s |30 |2 000 Mbits/s |Oui |

* (1) Le débit de la passerelle VPN est une estimation calculée à partir de mesures entre réseaux virtuels dans la même région Azure. Ce débit n’est pas garanti pour les connexions intersites via Internet. Il s’agit de la mesure du débit maximal possible.
* (2) Le nombre de tunnels fait référence aux VPN basés sur un itinéraire. Un VPN basé sur des stratégies peut uniquement prendre en charge un seul tunnel VPN de site à site.
* (3) Le protocole BGP n’est pas pris en charge pour la référence SKU De base.
* (4) Les VPN basés sur des stratégies ne sont pas pris en charge pour cette référence SKU. Ils sont uniquement pris en charge sur la référence SKU De base.

<!--HONumber=Oct16_HO2-->



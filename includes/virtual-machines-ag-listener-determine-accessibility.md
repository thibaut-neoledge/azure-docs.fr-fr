Il est important de savoir qu'il existe deux manières de configurer un écouteur de groupe de disponibilité dans Azure. Ces méthodes diffèrent dans le type d'équilibrage de charge Azure que vous utilisez lorsque vous créez l'écouteur. Le tableau suivant décrit ces différences :

| Équilibrage de charge | Implémentation | Contexte d'utilisation : |
| ------------- | -------------- | ----------- |
| **Externe** | Utilise l'**adresse IP virtuelle publique** du service cloud qui héberge des machines virtuelles. | Vous devez accéder à l'écouteur en dehors du réseau virtuel, notamment depuis Internet. |
| **Interne** | Utilise l'**équilibrage de charge interne (ILB)** avec une adresse privée pour l'écouteur. | Vous accéder à l'écouteur uniquement dans le même réseau virtuel. Ceci inclut un réseau privé virtuel de site à site dans des scénarios hybrides. |

>[AZURE.IMPORTANT] Pour un écouteur utilisant l’adresse IP virtuelle publique du service cloud (équilibrage de charge externe), tant que le client, l’écouteur et les bases de données se trouvent dans la même région Azure, aucuns frais de sortie ne vous seront facturés. Sinon, les données retournées par l’écouteur sont considérées comme une sortie et facturées au taux de transfert de données normal.

L'équilibrage de charge peut être configuré uniquement dans des réseaux virtuels à portée régionale. Les réseaux virtuels existants qui ont été configurés pour un groupe d'affinités ne peuvent pas utiliser l'ILB. Pour plus d'informations, consultez la rubrique [Équilibreur de charge interne](../articles/load-balancer/load-balancer-internal-overview.md).


Il est important de savoir qu'il existe deux manières de configurer un écouteur de groupe de disponibilité dans Azure. Ces méthodes diffèrent dans le type d'équilibrage de charge Azure que vous utilisez lorsque vous créez l'écouteur. Le tableau suivant décrit ces différences :

| Équilibrage de charge | Implémentation | Contexte d'utilisation : |
| ------------- | -------------- | ----------- |
| **Externe** | Utilise l'**adresse IP virtuelle publique** du service cloud qui héberge des machines virtuelles. | Vous devez accéder à l'écouteur en dehors du réseau virtuel, notamment depuis Internet. |
| **Interne** | Utilise l'**équilibrage de charge interne (ILB)** avec une adresse privée pour l'écouteur. | Vous accéder à l'écouteur uniquement dans le même réseau virtuel. Ceci inclut un réseau privé virtuel de site à site dans des scénarios hybrides. |

>[AZURE.IMPORTANT]Pour un écouteur utilisant l'adresse IP virtuelle publique du service de cloud (équilibreur de charge externe), toutes les données renvoyées par l'écouteur sont considérées comme sortie et chargées à des taux de transfert de données normaux dans Azure. Ceci est vrai même si le client se trouve dans le même réseau virtuel et centre de données en tant qu'écouteur et bases de données. Mais ce n'est pas le cas avec un écouteur utilisant l'équilibrage de charge.

L'équilibrage de charge peut être configuré uniquement dans des réseaux virtuels à portée régionale. Les réseaux virtuels existants qui ont été configurés pour un groupe d'affinités ne peuvent pas utiliser l'ILB. Pour plus d'informations, consultez la rubrique [Équilibreur de charge interne](../articles/load-balancer/load-balancer-internal-overview.md).

<!---HONumber=August15_HO7-->
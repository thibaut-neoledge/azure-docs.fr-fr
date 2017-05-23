Il est important de savoir qu’il existe deux manières de configurer un écouteur de groupe de disponibilité dans Azure. Ces méthodes diffèrent dans le type d’équilibrage de charge Azure que vous utilisez lorsque vous créez l’écouteur. Le tableau suivant décrit ces différences :

| Type d’équilibrage de charge | Implémentation | Utilisez-la quand : |
| --- | --- | --- |
| **Externe** |Utilise *l’adresse IP virtuelle publique* du service cloud qui héberge les machines virtuelles. |Vous devez accéder à l’écouteur en dehors du réseau virtuel, notamment depuis Internet. |
| **Interne** |Utilise un *équilibrage de charge interne* avec une adresse privée pour l’écouteur. |Vous ne pouvez accéder à l’écouteur qu’à partir du même réseau virtuel. Cet accès inclut un réseau privé virtuel de site à site dans des scénarios hybrides. |

> [!IMPORTANT]
> Pour un écouteur qui utilise l’adresse IP virtuelle publique du service cloud (équilibrage de charge externe), tant que le client, l’écouteur et les bases de données se trouvent dans la même région Azure, aucuns frais de sortie ne vous seront facturés. Sinon, les données retournées par l’écouteur sont considérées comme une sortie et facturées au taux de transfert de données normal. 
> 
> 

Un équilibrage de charge interne ne peut être configuré que dans des réseaux virtuels à portée régionale. Les réseaux virtuels existants qui ont été configurés pour un groupe d’affinités ne peuvent pas utiliser un équilibrage de charge interne. Pour plus d’informations, voir [Présentation de l’équilibrage de charge interne](../articles/load-balancer/load-balancer-internal-overview.md).


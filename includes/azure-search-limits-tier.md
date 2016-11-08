| Ressource | Gratuit | De base (version préliminaire) | S1 | S2 |
| --- | --- | --- | --- | --- |
| Nombre maximal de services à chaque niveau par abonnement <sup>1</sup> |1 |12 |12 |1 |
| Mise à l’échelle maximale par niveau <sup>2</sup> |N/A |3 unités de recherche (3 réplicas et 1 partition) |36 unités de recherche |36 unités de recherche |

<sup>1</sup> Chaque service est approvisionné selon un niveau de tarification donné, une limite étant appliquée au nombre de services que vous pouvez approvisionner à chaque niveau au sein d’un même abonnement Azure. Les niveaux sont disponibles à un prix inférieur de 50 % du prix complet pendant la période de version préliminaire.

<sup>2</sup> Les limites d’augmentation de la taille des instances sont définies en termes d’unités de recherche par niveau. Les unités de recherche désignent l’unité facturable pour un **réplica** ou une **partition**. Vous avez besoin des deux pour les opérations de stockage, d’indexation et de requête. Consultez [Scale resource levels for query and index workloads](../articles/search/search-capacity-planning.md) (Mettre à l’échelle les niveaux de ressource pour les charges de travail de requêtes et d’index) pour obtenir des combinaisons valides de réplicas et de partitions restant sous la limite maximale de 3 ou 36 unités (pour les versions **De base** et **Standard**, respectivement). Étant donné que le niveau **Gratuit** repose sur des ressources partagées utilisées par plusieurs abonnés, l’augmentation de la taille des instances n’est pas proposée à ce niveau.

<!---HONumber=AcomDC_0601_2016-->
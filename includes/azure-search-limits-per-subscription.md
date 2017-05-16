Vous pouvez créer plusieurs services au sein d’un abonnement (chacun d’eux étant approvisionné à un niveau spécifique) en leur affectant uniquement la limite du nombre de services autorisé à chaque niveau. Ainsi, vous pouvez créer jusqu’à 12 services au niveau de base et 12 autres au niveau S1 au sein du même abonnement. Pour en savoir plus sur ces niveaux, voir [Choisir une référence (SKU) ou un niveau tarifaire pour Azure Search](../articles/search/search-sku-tier.md).

Les limites de service maximales peuvent être augmentées sur demande. S’il vous faut davantage de services dans le même abonnement, contactez le Support Azure.

| Ressource | Gratuit | De base | S1 | S2 | S3 | S3 HD <sup>1</sup> |
| --- | --- | --- | --- | --- | --- | --- |
| Nombre de services maximum |1 |12 |12 |6 |6 |6 |
| Mise à l’échelle maximale en unités de recherche <sup>2</sup> |N/A <sup>3</sup> |3 unités de recherche <sup>4</sup> |36 unités de recherche |36 unités de recherche |36 unités de recherche |36 unités de recherche |

<sup>1</sup> S3 HD ne prend pas en charge les [indexeurs](../articles/search/search-indexer-overview.md) pour l’instant. 

<sup>2</sup> Les unités de recherche sont des unités de facturation, allouées en tant que *réplicas* ou *partitions*. Vous avez besoin des deux types de ressource pour les opérations de stockage, d’indexation et d’interrogation. Pour plus d’informations sur le mode de calcul des unités de recherche, et pour découvrir un graphique des combinaisons valables qui restent inférieures aux limites maximales, consultez l’article [Mettre à l’échelle les niveaux de ressources pour interroger et indexer les charges de travail dans Azure Search](../articles/search/search-capacity-planning.md). 

<sup>3</sup> La gratuité repose sur des ressources partagées utilisées par plusieurs abonnés. Dans ce niveau, il n’existe aucune ressource dédiée à un abonné particulier. Pour cette raison, l’échelle maximale est signalée comme n’était pas applicable.

<sup>4</sup> Le niveau « De base » présente une partition fixe. À ce niveau, des unités de recherche supplémentaires sont utilisées pour allouer davantage de réplicas pour les charges de travail de requête accrues.


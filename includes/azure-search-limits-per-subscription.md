Vous pouvez créer plusieurs services au sein d’un abonnement (chacun d’eux étant approvisionné à un niveau spécifique) en leur affectant uniquement la limite du nombre de services autorisé à chaque niveau. Ainsi, vous pouvez créer jusqu’à 12 services au niveau de base et 12 autres au niveau S1 au sein du même abonnement. 

Les limites de service maximales peuvent être augmentées sur demande. S’il vous faut davantage de services dans le même abonnement, contactez le Support Azure.

Ressource|Gratuit|De base|S1|S2|S3 <br/>(Version préliminaire) <sup>1</sup>  |S3 HD <br/>(Version préliminaire) <sup>1</sup> 
---|---|---|---|----|---|----
Nombre de services maximum |1 |12 |12  |6 |6 |6 
Mise à l’échelle maximale en unités de recherche <sup>2</sup>|N/A <sup>3</sup>|3 unités de recherche <sup>4</sup> |36 unités de recherche|36 unités de recherche|36 unités de recherche|12 unités de recherche <sup>5</sup>

<sup>1</sup> Les niveaux de version préliminaire sont facturés au départ à 50 %. Ils atteignent leur prix complet lorsque le niveau est « Disponibilité générale ». Pendant la période de version préliminaire, il n’existe aucun contrat de niveau de service (SLA). Pour en savoir plus sur ces niveaux, voir [Choisir une référence (SKU) ou un niveau tarifaire pour Azure Search](../articles/search/search-sku-tier.md).

<sup>2</sup> Les unités de recherche sont des unités facturables par service, allouées en tant que *réplicas* ou *partitions*. Vous avez besoin des deux types de ressource pour les opérations de stockage, d’indexation et d’interrogation. Pour en savoir plus sur les combinaisons valables qui restent inférieures aux limites maximales, voir [Mettre à l’échelle les niveaux de ressources pour interroger et indexer les charges de travail dans Azure Search](../articles/search/search-capacity-planning.md). 

<sup>3</sup> La gratuité repose sur des ressources partagées utilisées par plusieurs abonnés. Dans ce niveau, il n’existe aucune ressource dédiée à un abonné particulier. Pour cette raison, l’échelle maximale est signalée comme n’était pas applicable.

<sup>4</sup> Le niveau « De base » présente une partition fixe. À ce niveau, des unités de recherche supplémentaires sont utilisées pour allouer davantage de réplicas pour les charges de travail de requête accrues.

<sup>5</sup> Le niveau « S3 HD » est basé sur le même matériel que S3, mais dans une configuration optimisée pour un grand nombre d’index plus petits. Comme le niveau « De base », il inclut 1 partition très volumineuse associée à des unités de recherche supplémentaires, qui peuvent être utilisées pour les réplicas.






<!--HONumber=Oct16_HO2-->



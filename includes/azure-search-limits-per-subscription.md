Vous pouvez créer plusieurs services au sein d’un abonnement, chacun d’eux configuré à un niveau spécifique, limités uniquement par le nombre de services autorisé à chaque niveau dans un seul abonnement Azure. Le nombre maximum de services par niveau est indiqué ci-dessous. Comme indiqué, vous pouvez créer jusqu’à 12 services au niveau de base et 12 autres au niveau S1 au sein du même abonnement.

Pour les autres niveaux, on compte un service par abonnement. Vous pouvez contacter le Support Azure si vous avez besoin de plusieurs niveaux S2, S3 ou S3 HD par abonnement.

Ressource|Gratuit|De base|S1|S2|S3 <sup>1</sup> <br/>(version préliminaire) |S3 HD <sup>1</sup> <br/>(version préliminaire) 
---|---|---|---|----|---|----
Nombre de services maximum |1 |12 |12 |1 |1 |1 
Mise à l’échelle maximale en unités de recherche<sup>2</sup>|N/A <sup>3</sup>|3 unités de recherche <sup>4</sup> |36 unités de recherche|36 unités de recherche|36 unités de recherche|12 unités de recherche <sup>5</sup>

<sup>1</sup> **Les niveaux de version préliminaire** sont facturés à un taux de lancement 50 % inférieur au prix total. Avant la disponibilité générale, des niveaux sont introduits comme une fonctionnalité de version préliminaire. Pendant la période de version préliminaire, il n’existe aucun contrat de niveau de service (SLA). Consultez la rubrique [Choose a SKU or tier for Azure Search](../articles/search/search-sku-tier.md) (Choisir un niveau ou une référence pour Azure Search).

<sup>2</sup> **Les unités de recherche (SU)** sont des unités facturables par service, allouées comme **réplica** ou comme **partition**. Vous avez besoin des deux types de ressource pour les opérations de stockage, d’indexation et d’interrogation. Consultez la rubrique [Scale resource levels for query and index workloads](../articles/search/search-capacity-planning.md) (Mettre à l’échelle les niveaux de ressources pour interroger et indexer les charges de travail) pour découvrir des combinaisons valides restant dans les limites maximales.

<sup>3</sup> **Gratuit** repose sur des ressources partagées utilisées par plusieurs abonnés. Dans ce niveau, il n’existe aucune ressource dédiée à un abonné particulier. Pour cette raison, l’extensibilité n’est pas prise en charge.

<sup>4</sup> **De base** a une partition fixe. Des unités de recherche sont utilisées pour allouer des réplicas pour la mise à l’échelle de charges de travail de requête.

<sup>5</sup> **S3 HD** est basé sur le même matériel que S3, mais dans une configuration optimisée pour un grand nombre d’index plus petits. Il dispose d’1 partition très volumineuse au lieu de 12 partitions plus petites, et il comporte un maximum de 12 réplicas, comme S3.

<!---HONumber=AcomDC_0608_2016-->
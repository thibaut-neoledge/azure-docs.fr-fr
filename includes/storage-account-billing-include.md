La facturation de l’utilisation d’Azure Storage est basée sur votre compte de stockage. Les coûts de stockage sont basés sur les facteurs suivants : la région/l’emplacement, le type de compte, la capacité de stockage, le schéma de réplication, les transactions de stockage et l’acheminement des données.

- La région se rapporte à la région géographique dans laquelle votre compte est basé.
- Le compte que vous utilisez peut être de deux types : compte de stockage à usage général ou compte de stockage d’objets blob. Avec un compte de stockage d’objets blob, le niveau d’accès détermine également le modèle de facturation du compte.
- La capacité de stockage fait référence à l’unité de compte de stockage que vous utilisez pour stocker des données.
- La réplication détermine le nombre de copies de vos données qui sont conservées simultanément et à quels emplacements.
- Les transactions font références à toutes les opérations de lecture et d'écriture vers Azure Storage.
- L'acheminement des données fait référence aux données transférées hors d'une région Azure. Lorsque les données de votre compte de stockage sont utilisées par une application qui n’est pas exécutée dans la même région, vous êtes facturé pour l’acheminement des données (pour les services Azure, vous pouvez grouper vos données et services dans les mêmes centres de données pour réduire ou éliminer les frais d'acheminement des données).

La page [Prix appliqués à Azure Storage](https://azure.microsoft.com/pricing/details/storage/) fournit des informations de tarification détaillées basées sur le type de compte, la capacité de stockage, la réplication et les transactions. La page [Détails de la tarification – Transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/) fournit des informations de tarification détaillées pour les acheminements de données. Vous pouvez utiliser le [Calcul des coûts Azure Storage](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) pour faciliter l'estimation des coûts.

<!---HONumber=AcomDC_0427_2016-->
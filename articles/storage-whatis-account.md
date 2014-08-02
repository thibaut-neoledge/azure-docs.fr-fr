<properties linkid="manage-services-what-is-a-storage-account" urlDisplayName="What is a Storage Account" pageTitle="What is a storage account? | Microsoft Azure" metaKeywords="" description="Learn about the different types of storage accounts available in Azure, and get definitions for key storage terms." metaCanonical="" services="storage" documentationCenter="" title="What is a Storage Account?" authors="tamram" solutions="" manager="mbaldwin" editor="cgronlun" />

Présentation des comptes de stockage
====================================

Azure Storage inclut trois services : le stockage d'objets blob, le stockage de table et le stockage de files d'attente. Ces services sont inclus dans chaque compte de stockage. Un compte de stockage vous fournit un espace de nom unique pour utiliser les objets blob, les files d'attente et les tables.

Les comptes de stockage créés le 8 juin 2012 (ou après cette date) peuvent contenir jusqu'à 200 To de données d'objets blob, de files d'attentes et de tables. Les comptes de stockage créés avant cette date ont une capacité totale de 100 To. Un seul abonnement vous permet de créer jusqu'à 20 comptes de stockage uniques. Pour plus d'informations sur les comptes de stockage, consultez la rubrique [Objectifs de performance et évolutivité du stockage Azure](http://msdn.microsoft.com/en-us/library/dn249410.aspx).

Toutes les informations sur votre compte de stockage, notamment sa date de création, sont disponibles sur le portail de gestion, sur la page **Tableau de bord** pour **Storage**.

Les coûts de stockage sont basés sur quatre facteurs : la capacité de stockage, le schéma de réplication, les transactions de stockage et l'acheminement des données. La capacité de stockage fait référence à l'unité de compte de stockage que vous utilisez pour stocker des données. Le coût d'un simple stockage de vos données est déterminé par la quantité de données que vous stockez et leur type de réplication. Les transactions font références à toutes les opérations de lecture et d'écriture vers Azure Storage. L'acheminement des données fait référence aux données transférées hors d'une région Azure. Lorsque les données de votre compte de stockage sont utilisées par une application qui n'est pas exécutée dans la même région, que cette application soit un service cloud ou un autre type d'application, vous êtes facturé pour l'acheminement des données (pour les services Azure, vous pouvez grouper vos données et services dans les mêmes centres de données pour réduire ou éliminer les frais d'acheminement des données).

La page [Tarification – Stockage](http://www.windowsazure.com/en-us/pricing/details/#storage) fournit des informations de tarification détaillées pour les capacités de stockage, la réplication et les transactions. La page [Détails de la tarification – Transferts de données](http://www.windowsazure.com/en-us/pricing/details/data-transfers/) fournit des informations de tarification détaillées pour les acheminements de données.

Concepts
--------

-   **Stockage géo-redondant (GRS)** : le stockage géo-redondant offre le plus haut niveau de durabilité de stockage en répliquant en toute transparence vos données vers un emplacement secondaire dans la même région. Ceci active le basculement en cas de panne sur l'emplacement principal. L'emplacement secondaire est situé à des centaines de kilomètres de l'emplacement principal. Le GRS est implémenté via une fonctionnalité nommée *géo-réplication*, qui est activée pour un compte de stockage par défaut, mais qui peut être désactivée si vous ne voulez pas l'utiliser (par exemple, si les stratégies de votre société l'interdisent). Pour plus d'informations, consultez le billet [Présentation de la géo-réplication pour Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx).

-   **Stockage redondant local (LRS)** : permet un stockage local à disponibilité élevée hautement durable dans un seul emplacement. Avec le LRS, les données du compte sont répliquées trois fois dans le même centre de données. Tous les stockages dans Azure sont redondants localement. Pour améliorer la durabilité, vous pouvez activer la géo-réplication. Vous pouvez bénéficier d'une réduction pour le stockage redondant local. Pour plus d'informations sur la tarification, consultez la page [Tarification](http://www.windowsazure.com/en-us/pricing/details/#storage).

-   **Groupe d'affinités** : il s'agit d'un regroupement géographique de vos déploiements de service cloud et de vos comptes de stockage dans Azure. Un groupe d'affinités peut améliorer les performances d'un service en situant les charges de travail d'un ordinateur dans le même centre de données ou près des utilisateurs cibles. De même, vous n'êtes pas facturé pour les acheminements des données lorsque les données de votre compte de stockage sont utilisées par un service exécuté dans le même groupe d'affinités.

-   **Points de terminaison de compte de stockage** : les *points de terminaison* d'un compte de stockage représentent le plus haut niveau de l'espace de noms pour l'accès aux objets blob, aux tables ou aux files d'attentes. Les points de terminaison par défaut d'un compte de stockage suivent ces formats :

    -   Service d'objets blob : http://*mystorageaccount*.blob.core.windows.net

    -   Service de tables : http://*mystorageaccount*.table.core.windows.net

    -   Service de files d'attente : http://*mystorageaccount*.queue.core.windows.net

-   **URL de compte de stockage** : l'URL permettant d'accéder à un objet dans un compte de stockage est constituée de l'ajout de l'emplacement de l'objet dans le compte de stockage au point de terminaison. Par exemple, une adresse d'objet blob peut avoir ce format : http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

-   **Clés d'accès de stockage** : lorsque vous créez un compte de stockage, Azure génère deux clés de d'accès de stockage 512 bits, qui sont utilisées pour l'authentification lors de l'accès au compte de stockage. En fournissant deux clés d'accès de stockage, Azure vous permet de régénérer les clés sans interrompre votre service de stockage ni l'accès à ce service.

-   **Métriques minimales et détaillées** : vous pouvez configurer des métriques minimales ou détaillées dans les paramètres de surveillance de votre compte de stockage. Les *métriques minimales* concernent les données telles que l'entrée/sortie, la disponibilité, la latence et les pourcentages de succès, qui sont agrégées pour les services d'objets blob, de tables et de files d'attente. Les *métriques détaillées* concernent les détails des opérations en plus de ceux des services agrégés pour les mêmes métriques. Les métriques détaillées permettent d'analyser plus précisément les problèmes survenant durant le fonctionnement d'une application. Pour obtenir une liste de toutes les métriques disponibles, consultez l'article[Schéma de table de métriques Storage Analytics](http://msdn.microsoft.com/en-us/library/windowsazure/hh343264.aspx). Pour plus d'informations sur la surveillance du stockage, consultez l'article [À propos des métriques Storage Analytics](http://msdn.microsoft.com/en-us/library/windowsazure/hh343258.aspx).

-   **Journalisation** : il s'agit une fonctionnalité configurable des comptes de stockage qui permet de créer des journaux contenant les demandes de lecture, d'écriture et de suppression des objets blob, des tables et des files d'attente. Vous pouvez configurer la journalisation dans le portail de gestion Azure, mais vous ne pouvez pas y afficher les journaux. Les journaux sont stockés et accessibles via le compte de stockage, dans le conteneur blobs. Pour plus d'informations, consultez l'article [Présentation de Storage Analytics](http://msdn.microsoft.com/en-us/library/windowsazure/hh343268.aspx).



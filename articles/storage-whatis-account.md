<properties urlDisplayName="What is a Storage Account" pageTitle="Pr&eacute;sentation des comptes de stockage | Microsoft&nbsp;Azure" metaKeywords="" description="En savoir plus sur les diff&eacute;rents types de compte de stockage disponibles dans&nbsp;Azure&nbsp;; d&eacute;finitions des principaux termes utilis&eacute;s pour le stockage." metaCanonical="" services="storage" documentationCenter="" title="Pr&eacute;sentation des comptes de stockage" authors="tamram" solutions="" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Présentation des comptes de stockage

Azure Storage inclut trois services : le stockage d'objets blob, le stockage de table et le stockage de files d'attente. Ces services sont inclus dans chaque compte de stockage. Un compte de stockage vous fournit un espace de nom unique pour utiliser les objets blob, les files d'attente et les tables.

Pour plus d'informations sur les limites des comptes de stockage, consultez la page [Objectifs d'évolutivité et de performances d'Azure Storage][Objectifs d'évolutivité et de performances d'Azure Storage].

Toutes les informations sur votre compte de stockage, notamment sa date de création, sont disponibles sur le portail de gestion, sur la page **Tableau de bord** pour **Storage**.

Les coûts de stockage sont basés sur quatre facteurs : la capacité de stockage, le schéma de réplication, les transactions de stockage et l'acheminement des données. La capacité de stockage fait référence à l'unité de compte de stockage que vous utilisez pour stocker des données. Le coût d'un simple stockage de vos données est déterminé par la quantité de données que vous stockez et leur type de réplication. Les transactions font références à toutes les opérations de lecture et d'écriture vers Azure Storage. L'acheminement des données fait référence aux données transférées hors d'une région Azure. Lorsque les données de votre compte de stockage sont utilisées par une application qui n'est pas exécutée dans la même région, que cette application soit un service cloud ou un autre type d'application, vous êtes facturé pour l'acheminement des données (pour les services Azure, vous pouvez grouper vos données et services dans les mêmes centres de données pour réduire ou éliminer les frais d'acheminement des données).

La page [Tarification – Stockage][Tarification – Stockage] fournit des informations de tarification détaillées pour les capacités de stockage, la réplication et les transactions. La page [Détails de la tarification – Transferts de données][Détails de la tarification – Transferts de données] fournit des informations de tarification détaillées pour les acheminements de données.

## Concepts liés aux comptes de stockage

### Options de réplication des comptes de stockage

[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

### Points de terminaison d'un compte de stockage

Les *points de terminaison* d'un compte de stockage représentent le plus haut niveau de l'espace de noms pour l'accès aux objets blob, aux tables, aux files d'attente ou aux fichiers. Les points de terminaison par défaut d'un compte de stockage suivent ces formats :

-   Service d'objets blob : http://*mystorageaccount*.blob.core.windows.net

-   Service de tables : http://*mystorageaccount*.table.core.windows.net

-   Service de files d'attente : http://*mystorageaccount*.queue.core.windows.net

-   Service de fichiers : http://*mystorageaccount*.file.core.windows.net

L'URL permettant d'accéder à un objet dans un compte de stockage est constituée de l'ajout de l'emplacement de l'objet dans le compte de stockage au point de terminaison. Par exemple, une adresse d'objet blob peut avoir ce format : http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

### Sécurité des comptes de stockage

Lorsque vous créez un compte de stockage, Azure génère deux clés d'accès de stockage de 512 bits, qui servent à l'authentification lors de l'accès au compte de stockage. En fournissant deux clés d'accès de stockage, Azure vous permet de régénérer les clés sans interrompre votre service de stockage ni l'accès à ce service.

Nous vous recommandons d'éviter de partager les clés d'accès à votre compte de stockage avec qui que ce soit. Si vous pensez que votre compte a été compromis, vous pouvez régénérer vos clés d'accès dans le portail. Sélectionnez votre compte de stockage, puis choisissez **Gérer les clés d'accès**.

Pour autoriser l'accès aux ressources de stockage sans donner vos clés d'accès, vous pouvez utiliser une *signature d'accès partagé*. Celle-ci donne accès à votre compte à une ressource pendant une durée que vous déterminez et avec les autorisations que vous spécifiez. Pour plus d'informations, consultez le [didacticiel sur les signatures d'accès partagé][didacticiel sur les signatures d'accès partagé].

### Mesures et journalisation d'un compte de stockage

-   **Métriques minimales et détaillées** : vous pouvez configurer des métriques minimales ou détaillées dans les paramètres de surveillance de votre compte de stockage. Les *métriques minimales* concernent les données telles que l'entrée/sortie, la disponibilité, la latence et les pourcentages de succès, qui sont agrégées pour les services d'objets blob, de tables et de files d'attente. Les *métriques détaillées* concernent les détails des opérations en plus de ceux des services agrégés pour les mêmes métriques. Les métriques détaillées permettent d'analyser plus précisément les problèmes survenant durant le fonctionnement d'une application. Pour obtenir une liste de toutes les métriques disponibles, consultez l'article [Schéma de table de métriques Storage Analytics][Schéma de table de métriques Storage Analytics]. Pour plus d'informations sur la surveillance du stockage, consultez l'article [À propos des métriques Storage Analytics][À propos des métriques Storage Analytics].

-   **Journalisation** : il s'agit une fonctionnalité configurable des comptes de stockage qui permet de créer des journaux contenant les demandes de lecture, d'écriture et de suppression des objets blob, des tables et des files d'attente. Vous pouvez configurer la journalisation dans le portail de gestion Azure, mais vous ne pouvez pas y afficher les journaux. Les journaux sont stockés et accessibles via le compte de stockage, dans le conteneur $logs. Pour plus d'informations, consultez l'article [Présentation de Storage Analytics][Présentation de Storage Analytics].

### Groupes d'affinités pour la colocation entre Azure Storage et d'autres services

Un *groupe d'affinités* est un regroupement géographique de vos services et machines virtuelles Azure avec votre compte de stockage Azure. Un groupe d'affinités peut améliorer les performances d'un service en situant les charges de travail d'un ordinateur dans le même centre de données ou près des utilisateurs cibles. De même, vous n'êtes pas facturé pour les acheminements lorsque les données d'un compte de stockage sont utilisées par un service faisant partie du même groupe d'affinités.

  [Objectifs d'évolutivité et de performances d'Azure Storage]: http://msdn.microsoft.com/fr-fr/library/dn249410.aspx
  [Tarification – Stockage]: http://www.windowsazure.com/fr-fr/pricing/details/#storage
  [Détails de la tarification – Transferts de données]: http://www.windowsazure.com/fr-fr/pricing/details/data-transfers/
  [didacticiel sur les signatures d'accès partagé]: ../storage-dotnet-shared-access-signature-part-1/
  [Schéma de table de métriques Storage Analytics]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh343264.aspx
  [À propos des métriques Storage Analytics]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh343258.aspx
  [Présentation de Storage Analytics]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh343268.aspx

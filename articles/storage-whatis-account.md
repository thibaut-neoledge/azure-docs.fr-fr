<properties linkid="manage-services-what-is-a-storage-account" urlDisplayName="What is a Storage Account" pageTitle="What is a storage account? | Microsoft Azure" metaKeywords="" description="Learn about the different types of storage accounts available in Azure, and get definitions for key storage terms." metaCanonical="" services="storage" documentationCenter="" title="What is a Storage Account?" authors="tamram" solutions="" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram"></tags>

# Présentation des comptes de stockage

Azure Storage inclut trois services : le stockage d'objets blob, le stockage de table et le stockage de files d'attente. Ces services sont inclus dans chaque compte de stockage. Un compte de stockage vous fournit un espace de nom unique pour utiliser les objets blob, les files d'attente et les tables.

For information about storage account limits, see [Azure Storage Scalability and Performance Targets][Azure Storage Scalability and Performance Targets].

Toutes les informations sur votre compte de stockage, notamment sa date de création, sont disponibles sur le portail de gestion, sur la page **Tableau de bord** pour **Storage**.

Les coûts de stockage sont basés sur quatre facteurs : la capacité de stockage, le schéma de réplication, les transactions de stockage et l'acheminement des données. La capacité de stockage fait référence à l'unité de compte de stockage que vous utilisez pour stocker des données. Le coût d'un simple stockage de vos données est déterminé par la quantité de données que vous stockez et leur type de réplication. Les transactions font références à toutes les opérations de lecture et d'écriture vers Azure Storage. L'acheminement des données fait référence aux données transférées hors d'une région Azure. Lorsque les données de votre compte de stockage sont utilisées par une application qui n'est pas exécutée dans la même région, que cette application soit un service cloud ou un autre type d'application, vous êtes facturé pour l'acheminement des données (pour les services Azure, vous pouvez grouper vos données et services dans les mêmes centres de données pour réduire ou éliminer les frais d'acheminement des données).

La page [Tarification – Stockage][Tarification – Stockage] fournit des informations de tarification détaillées pour les capacités de stockage, la réplication et les transactions. La page [Détails de la tarification – Transferts de données][Détails de la tarification – Transferts de données] fournit des informations de tarification détaillées pour les acheminements de données.

## Concepts liés aux comptes de stockage

### Storage account replication options

[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

### Storage account endpoints

The *endpoints* for a storage account represent the highest level of the namespace for accessing blobs, tables, queues, or files. Les points de terminaison par défaut d'un compte de stockage suivent ces formats :

-   Service d'objets blob : <http://*mystorageaccount>\*.blob.core.windows.net

-   Service de tables : <http://*mystorageaccount>\*.table.core.windows.net

-   Service de files d'attente : <http://*mystorageaccount>\*.queue.core.windows.net

-   File service: <http://*mystorageaccount>\*.file.core.windows.net

The URL for accessing an object in a storage account is built by appending the object's location in the storage account to the endpoint. Par exemple, une adresse d'objet blob peut avoir ce format : <http://*mystorageaccount>*.blob.core.windows.net/*mycontainer*/*myblob\*.

### Storage account security

Lorsque vous créez un compte de stockage, Azure génère deux clés d'accès de stockage de 512 bits, qui servent à l'authentification lors de l'accès au compte de stockage. En fournissant deux clés d'accès de stockage, Azure vous permet de régénérer les clés sans interrompre votre service de stockage ni l'accès à ce service.

We recommend that you avoid sharing your storage account access keys with anyone else. If you feel that your account has been compromised, you can regenerate your access keys from within the portal. Select your storage account and choose **Manage Access Keys**.

To permit access to storage resources without giving out your access keys, you can use a *shared access signature*. A shared access signature provides access to a resource in your account for an interval that you define and with the permissions that you specify. See the [shared access signature tutorial][shared access signature tutorial] for more information.

### Storage account metrics and logging

-   **Métriques minimales et détaillées** : vous pouvez configurer des métriques minimales ou détaillées dans les paramètres de surveillance de votre compte de stockage. Les *métriques minimales* concernent les données telles que l'entrée/sortie, la disponibilité, la latence et les pourcentages de succès, qui sont agrégées pour les services d'objets blob, de tables et de files d'attente. Les *métriques détaillées* concernent les détails des opérations en plus de ceux des services agrégés pour les mêmes métriques. Les métriques détaillées permettent d'analyser plus précisément les problèmes survenant durant le fonctionnement d'une application. Pour obtenir une liste de toutes les métriques disponibles, consultez l'article[Schéma de table de métriques Storage Analytics][Schéma de table de métriques Storage Analytics]. Pour plus d'informations sur la surveillance du stockage, consultez l'article [À propos des métriques Storage Analytics][À propos des métriques Storage Analytics].

-   **Journalisation** : il s'agit une fonctionnalité configurable des comptes de stockage qui permet de créer des journaux contenant les demandes de lecture, d'écriture et de suppression des objets blob, des tables et des files d'attente. Vous pouvez configurer la journalisation dans le portail de gestion Azure, mais vous ne pouvez pas y afficher les journaux. Les journaux sont stockés et accessibles via le compte de stockage, dans le conteneur $logs. Pour plus d'informations, consultez l'article [Présentation de Storage Analytics][Présentation de Storage Analytics].

### Affinity groups for co-locating Azure Storage and other services

An *affinity group* is a geographic grouping of your Azure services and VMs with your Azure storage account. Un groupe d'affinités peut améliorer les performances d'un service en situant les charges de travail d'un ordinateur dans le même centre de données ou près des utilisateurs cibles. Also, no billing charges are incurred for egress when data in a storage account is accessed from another service that is part of the same affinity group.

  [Azure Storage Scalability and Performance Targets]: http://msdn.microsoft.com/fr-fr/library/dn249410.aspx
  [Tarification – Stockage]: http://www.windowsazure.com/fr-fr/pricing/details/#storage
  [Détails de la tarification – Transferts de données]: http://www.windowsazure.com/fr-fr/pricing/details/data-transfers/
  [storage-replication-options]: ../includes/storage-replication-options.md
  [shared access signature tutorial]: ../storage-dotnet-shared-access-signature-part-1/
  [Schéma de table de métriques Storage Analytics]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh343264.aspx
  [À propos des métriques Storage Analytics]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh343258.aspx
  [Présentation de Storage Analytics]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh343268.aspx

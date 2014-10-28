<properties umbracoNaviHide="0" pageTitle="Storage Account Concepts | Azure" metaKeywords="Azure storage, storage service, service, storage account, account, create storage account, create account" description="Learn about storage account concepts." linkid="manage-windows-how-to-guide-storage-accounts" urlDisplayName="How to: storage accounts" headerExpose="" footerExpose="" disqusComments="1" title="Storage Account Concepts" services="storage" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="tamram"></tags>

# Concepts liés aux comptes de stockage

## Options de réplication des comptes de stockage

[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

## Points de terminaison des comptes de stockage

Les *points de terminaison* d'un compte de stockage représentent le plus haut niveau de l'espace de noms pour l'accès aux objets blob, aux tables, aux files d'attente ou aux fichiers. Les points de terminaison par défaut d'un compte de stockage suivent ces formats :

-   Service d'objets blob : http://*mystorageaccount\*.blob.core.windows.net

-   Service de tables : http://*mystorageaccount\*.table.core.windows.net

-   Service de files d'attente : http://*mystorageaccount\*.queue.core.windows.net

-   Service de fichiers : http://*mystorageaccount\*.file.core.windows.net

L'URL permettant d'accéder à un objet disponible dans un compte de stockage est constituée par l'ajout de l'emplacement de cet objet au point de terminaison. Par exemple, une adresse d'objet blob peut avoir ce format : http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob\*.

## Sécurité d'un compte de stockage

Lorsque vous créez un compte de stockage, Azure génère deux clés d'accès de stockage de 512 bits, qui servent à l'authentification lors de l'accès au compte de stockage. En fournissant deux clés d'accès de stockage, Azure vous permet de régénérer les clés sans interrompre votre service de stockage ni l'accès à ce service.

Nous vous déconseillons de partager les clés d'accès à votre compte de stockage avec un tiers. Si vous pensez que votre compte est compromis, vous pouvez régénérer vos clés d'accès à partir du portail. Sélectionnez votre compte de stockage et choisissez **Gérer les clés d'accès**.

Pour autoriser l'accès aux ressources de stockage sans révéler vos clés d'accès, vous pouvez utiliser une *signature d'accès partagé*. Cette dernière permet d'accéder à une ressource de votre compte pendant une période que vous définissez et avec les autorisations que vous spécifiez. Pour plus d'informations, reportez-vous au [Didacticiel sur la signature d'accès partagé][Didacticiel sur la signature d'accès partagé].

## Mesures et journalisation du compte de stockage

-   **Métriques minimales et détaillées** : vous pouvez configurer des métriques minimales ou détaillées dans les paramètres de surveillance de votre compte de stockage. Les *métriques minimales* concernent les données telles que l'entrée/sortie, la disponibilité, la latence et les pourcentages de succès, qui sont agrégées pour les services d'objets blob, de tables et de files d'attente. Les *métriques détaillées* concernent les détails des opérations en plus de ceux des services agrégés pour les mêmes métriques. Les métriques détaillées permettent d'analyser plus précisément les problèmes survenant durant le fonctionnement d'une application. Pour obtenir une liste de toutes les métriques disponibles, consultez l'article[Schéma de table de métriques Storage Analytics][Schéma de table de métriques Storage Analytics]. Pour plus d'informations sur la surveillance du stockage, consultez l'article [À propos des métriques Storage Analytics][À propos des métriques Storage Analytics].

-   **Journalisation** : il s'agit une fonctionnalité configurable des comptes de stockage qui permet de créer des journaux contenant les demandes de lecture, d'écriture et de suppression des objets blob, des tables et des files d'attente. Vous pouvez configurer la journalisation dans le portail de gestion Azure, mais vous ne pouvez pas y afficher les journaux. Les journaux sont stockés et accessibles via le compte de stockage, dans le conteneur $logs. Pour plus d'informations, consultez l'article [Présentation de Storage Analytics][Présentation de Storage Analytics].

## Groupes d'affinités pour la colocalisation d'Azure Storage et des autres services

Un *groupe d'affinités* est un regroupement géographique de vos services et machines virtuelles Azure avec votre compte de stockage Azure. Un groupe d'affinités peut améliorer les performances d'un service en situant les charges de travail d'un ordinateur dans le même centre de données ou près des utilisateurs cibles. De plus, l'accès aux données d'un compte de stockage depuis un autre service membre du même groupe d'affinités n'entraîne pas de frais de sortie.

  [storage-replication-options]: ../includes/storage-replication-options.md
  [Didacticiel sur la signature d'accès partagé]: ../storage-dotnet-shared-access-signature-part-1/
  [Schéma de table de métriques Storage Analytics]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh343264.aspx
  [À propos des métriques Storage Analytics]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh343258.aspx
  [Présentation de Storage Analytics]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh343268.aspx

<properties 
	pageTitle="Storage Analytics et facturation" 
	description="Découvrez comment tirer parti de Storage Analytics pour mieux comprendre votre facture de service de stockage" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>


# Storage Analytics et facturation

Cette rubrique décrit le coût de Storage Analytics et comment utiliser les données de métriques et de journalisation pour comprendre votre facture de services de stockage.


## Coût de Storage Analytics

Storage Analytics est activé par un propriétaire de compte de stockage ; il n'est pas activé par défaut. Toutes les données de métriques sont écrites par les services d'un compte de stockage. En conséquence, chaque opération d'écriture effectuée par Storage Analytics est facturable. En outre, la quantité de stockage utilisée par les données de métriques est également facturable.

Les actions suivantes effectuées par Storage Analytics sont facturables :

- Demandes de création d'objets blob pour la journalisation

- Demandes de création d'entités de table pour les métriques

Si vous avez configuré une stratégie de rétention des données, vous n'êtes pas facturé pour les transactions de suppression lorsque Storage Analytics supprime les anciennes données de journalisation et de métriques.  Toutefois, les transactions de suppression d'un client sont facturables. Pour plus d'informations sur les stratégies de rétention, consultez [Définition d'une stratégie de rétention des données Storage Analytics](https://msdn.microsoft.com/library/azure/hh343263.aspx).

## Présentation des demandes facturables

Chaque demande adressée à un service de stockage d'un compte est soit facturable, soit non facturable.  Storage Analytics enregistre chaque demande effectuée à un service, avec un message d'état qui indique comment la demande a été traitée. De même, Storage Analytics stocke des métriques pour un service et pour les opérations de l'API de ce service, y compris les pourcentages et le décompte de certains messages d'état. Conjointement, ces fonctionnalités peuvent vous aider à analyser vos demandes facturables, à améliorer votre application et à diagnostiquer les problèmes liés aux demandes à vos services. Pour plus d'informations sur la facturation, consultez [Présentation de la facturation du stockage Azure - bande passante, transactions et capacité](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

En examinant les données Storage Analytics, vous pouvez utiliser les tables de la rubrique [Opérations et messages d'état enregistrés Storage Analytics](https://msdn.microsoft.com/library/azure/hh343260.aspx) pour identifier les demandes facturables. Vous pouvez ensuite comparer vos données de journaux et de métriques aux messages d'état pour déterminer si vous avez été facturé pour une demande spécifique. Vous pouvez également utiliser les tables de la rubrique ci-dessus pour examiner la disponibilité d'un service de stockage ou d'une opération individuelle de l'API.

## Étapes suivantes
[Opérations et messages d'état enregistrés Storage Analytics](https://msdn.microsoft.com/library/azure/hh343260.aspx)

[À propos de la journalisation Storage Analytics](https://msdn.microsoft.com/library/azure/hh343262.aspx)

[À propos des métriques de Storage Analytics](../storage-about-analytics-metrics.md) 

<!--HONumber=47-->

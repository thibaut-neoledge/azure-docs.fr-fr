<properties 
	pageTitle="Storage Analytics" 
	description="Gestion de l'accès concurrentiel pour les services BLOB, de File d'attente, de Table et de Fichier" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/08/2014" 
	ms.author="tamram"/>

# Storage Analytics

Azure Storage Analytics effectue la journalisation et fournit des données de métriques pour un compte de stockage. Vous pouvez utiliser ces données pour suivre les demandes, analyser les tendances d'utilisation et diagnostiquer les problèmes liés à votre compte de stockage

Pour utiliser Storage Analytics, vous devez l'activer individuellement pour chaque service que vous souhaitez analyser. Vous pouvez l'activer à partir du [Portail de gestion Azure](https://manage.windowsazure.com/) ; pour plus d'informations, consultez [Surveillance d'un compte de stockage](http://www.azure.com/manage/services/storage/how-to-monitor-a-storage-account/). Vous pouvez également activer Storage Analytics par programmation via l'API REST ou la bibliothèque cliente. Utilisez les opérations [Get Blob Service Properties](https://msdn.microsoft.com/fr-fr/library/hh452239.aspx), [Get Queue Service Properties](https://msdn.microsoft.com/fr-fr/library/hh452243.aspx) et [Get Table Service Properties](https://msdn.microsoft.com/fr-fr/library/hh452238.aspx) pour activer Storage Analytics pour chaque service.

Les données agrégées sont stockées dans un objet blob connu (pour la journalisation) et dans des tables connues (pour les métriques), qui sont accessibles via les API du service BLOB et du service de Table.

Storage Analytics a une limite de 20 To pour la quantité de données stockées qui est indépendante de la limite totale pour votre compte de stockage. Pour plus d'informations sur les stratégies de facturation et de rétention de données, consultez [Storage Analytics et facturation](https://msdn.microsoft.com/library/hh360997.aspx). Pour plus d'informations sur les limites des comptes de stockage, consultez [Objectifs d'extensibilité et de performances d'Azure Storage](https://msdn.microsoft.com/library/dn249410.aspx).

Pour obtenir un guide détaillé concernant l'utilisation de Storage Analytics et d'autres outils permettant d'identifier, de diagnostiquer et de résoudre les problèmes d'Azure Storage, consultez la page [Surveillance, diagnostic et résolution des problèmes sur Microsoft Azure Storage](http://azure.microsoft.com/documentation/articles/storage-monitoring-diagnosing-troubleshooting/)



<!--HONumber=47-->
 
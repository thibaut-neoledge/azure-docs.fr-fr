<properties 
	pageTitle="Vue d'ensemble du Kit de développement logiciel de Windows Universal" 
	description="Vue d'ensemble du Kit de développement logiciel de Windows Universal pour Azure Mobile Engagement" 									
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/03/2016" 
	ms.author="piyushjo" />

#Vue d'ensemble du Kit de développement logiciel de Windows Universal pour Azure Engagement Mobile

Cliquez ici pour obtenir tous les détails de l'intégration d'Azure Mobile Engagement dans une application Windows Universal. Si vous souhaitez tout d'abord l'essayer, pensez à suivre notre [didacticiel de 15 minutes](mobile-engagement-windows-store-dotnet-get-started.md).

Cliquez pour voir le [contenu du Kit de développement logiciel (SDK)](mobile-engagement-windows-store-sdk-content.md)

##Procédures d'intégration

1. Commencez ici : [intégration de Mobile Engagement à votre application Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)

2. Pour les notifications : [intégration du module Couverture (Notifications) à votre application Windows Universal](mobile-engagement-windows-store-integrate-engagement-reach.md)

3. Implémentation du plan de balise : [utilisation de l'API avancée de balisage de Mobile Engagement dans votre application Windows Universal](mobile-engagement-windows-store-use-engagement-api.md)

##Notes de publication

###3\.4.0 (04/19/2016)

-   Améliorations de la superposition Reach
-   Ajout de l'API « TestLogLevel » aux journaux de console enable/disable/filter émis par le Kit de développement logiciel (SDK).
-   Correction de l’erreur où des notifications dans l’activité ciblant la première activité n’apparaissaient pas au démarrage de l'application.

Pour les versions antérieures, consultez les [notes de publication complètes](mobile-engagement-windows-store-release-notes.md)

##Procédures de mise à niveau

Si vous avez déjà intégré une version antérieure d'Engagement dans votre application, vous devez prendre en compte les points suivants lors de la mise à niveau du Kit de développement logiciel.

Si vous avez manqué plusieurs versions du Kit de développement logiciel (SDK), vous devrez peut-être effectuer plusieurs procédures. Consultez la version complète des [Procédures mises à jour](mobile-engagement-windows-store-upgrade-procedure.md) pour les découvrir. Par exemple, si vous migrez de la version 0.10.1 vers 0.11.0, vous devez tout d'abord suivre la procédure « Migration de 0.9.0 vers 0.10.1 », puis la procédure « Migration de 0.10.1 vers 0.11.0 ».

###Migration de 3.3.0 vers 3.4.0

####Journaux des tests

Les journaux de console produits par le Kit de développement logiciel (SDK) peuvent maintenant être activés/désactivés/filtrés. Pour personnaliser ce résultat, mettez à jour la propriété `EngagementAgent.Instance.TestLogEnabled` avec une des valeurs disponibles à partir de l'énumération `EngagementTestLogLevel`, par exemple :

			EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
			EngagementAgent.Instance.Init();

####Ressources

La superposition Reach a été améliorée. Elle fait partie des ressources du package NuGet du Kit de développement logiciel (SDK).

Lors de la mise à niveau vers la nouvelle version du Kit de développement logiciel (SDK), vous pouvez choisir de conserver ou non les fichiers existants dans le dossier de superposition de vos ressources :

* Si la superposition précédente vous convient ou que vous intégrez manuellement les éléments `WebView`, vous pouvez choisir de conserver vos fichiers existants car ils resteront opérationnels. 
* Si vous souhaitez effectuer la mise à jour vers la nouvelle superposition, remplacez simplement l'ensemble du dossier `overlay` contenant vos ressources par le nouveau dossier contenant le package du Kit de développement logiciel (applications UWP : après la mise à niveau, vous trouverez le nouveau dossier de superposition sous %USERPROFILE%\\.nuget\\packages\\MicrosoftAzure.MobileEngagement\\3.4.0\\content\\win81\\Resources).

> [AZURE.WARNING] L’utilisation de la nouvelle superposition remplacera toutes les personnalisations apportées à la version précédente.

### Mise à niveau à partir de versions antérieures

Consultez la rubrique [Procédures de mise à niveau](mobile-engagement-windows-store-upgrade-procedure.md)

<!---HONumber=AcomDC_0504_2016-->
<properties 
	pageTitle="Vue d’ensemble du Kit de développement logiciel Windows Phone Silverlight" 
	description="Vue d'ensemble du Kit de développement Silverlight de Windows Phone pour Azure Engagement Mobile" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede"
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/19/2016" 
	ms.author="piyushjo" />

#Vue d'ensemble du Kit de développement Silverlight de Windows Phone pour Azure Engagement Mobile

Cette introduction contient les informations nécessaires pour intégrer Azure Mobile Engagement à une application Windows Phone Silverlight. Si vous souhaitez tout d'abord l'essayer, pensez à suivre notre didacticiel de [15 minutes](mobile-engagement-windows-phone-get-started.md).

Cliquez pour voir le [contenu du Kit de développement logiciel (SDK)](mobile-engagement-windows-phone-sdk-content.md)

##Procédures d'intégration

1. Commencez ici : [Intégration de Mobile Engagement à votre application Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)

2. Pour les notifications : [Intégration de Reach (Notifications) à votre application Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement-reach.md)

3. Implémentation du plan de la balise : [Utilisation de l'API de marquage avancée Mobile Engagement dans votre application Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md)

##Notes de publication

###3\.3.0 (04/19/2016)
Partie du package NuGet *MicrosoftAzure.MobileEngagement* **v3.4.0**

-   Ajout de l'API « TestLogLevel » aux journaux de console enable/disable/filter émis par le Kit de développement logiciel (SDK).

Pour les versions antérieures, consultez les [notes de publication complètes](mobile-engagement-windows-phone-release-notes.md)

##Procédures de mise à niveau

Si vous avez déjà intégré une ancienne version de notre SDK à votre application, tenez compte des points suivants avant de procéder à la mise à niveau du SDK.

Vous devrez peut-être suivre quelques procédures si vous avez manqué plusieurs versions du kit SDK. Consultez l’ensemble des [procédures de mise à niveau](mobile-engagement-windows-phone-upgrade-procedure.md). Par exemple, si vous migrez de la version 0.10.1 vers 0.11.0, vous devez tout d'abord suivre la procédure « Migration de 0.9.0 vers 0.10.1 », puis la procédure « Migration de 0.10.1 vers 0.11.0 ».

###Migration de 2.0.0 vers 3.3.0

####Journaux des tests

Les journaux de console produits par le Kit de développement logiciel (SDK) peuvent maintenant être activés/désactivés/filtrés. Pour personnaliser ce résultat, mettez à jour la propriété `EngagementAgent.Instance.TestLogEnabled` avec une des valeurs disponibles à partir de l'énumération `EngagementTestLogLevel`, par exemple :

			EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
			EngagementAgent.Instance.Init();

### Mise à niveau à partir de versions antérieures

Consultez [Procédures de mise à niveau](mobile-engagement-windows-phone-upgrade-procedure.md)
 

<!---HONumber=AcomDC_0824_2016-->
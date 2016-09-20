<properties
	pageTitle="Azure App Service : Mise à l’échelle des applications d’App Service"
	description="Découvrez les tenants et les aboutissants de la mise à l’échelle d’application dans App Service."
	keywords="app service, azure app service, mise à l'échelle, évolutif, plan app service, coût d'app service"
	services="app-service"
	documentationCenter=""
	authors="btardif"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/15/2016"
	ms.author="byvinyal"/>

# Azure App Service : Mise à l’échelle des applications d’App Service

Les applications hébergées dans Azure App Service peuvent [être mises à l’échelle massivement](https://azure.microsoft.com/blog/canadian-broadcasting-corporation-radio-canada-leverage-azure-for-smooth-election-coverage/). Toutefois, la mise à l’échelle d’une application est un problème complexe pour lequel il n’existe pas de solution universelle. Pour mettre à l’échelle correctement votre application, il convient de prendre en compte 3 domaines clés :

1. Comprendre l’architecture de votre application et ses faiblesses.
	* Votre application est-elle avec état ? Sans état ?
	* Quels sont les composants de votre application ?
		* Où se trouvent les goulots d’étranglement dans l’application ?
	* Lors de l’application de la charge à votre application, qu’est-ce qui s’arrête en premier ?
2. Comprendre les exigences de performances et de charge attendues.
	* L’application doit-elle répondre à 1 000 ou à un million d’utilisateurs ?
	* Le trafic provient-il d’un seul emplacement géographique ou du monde entier ?
	* Existe-t-il des variations saisonnières ? Des pics de trafic ?
	* À quelle vitesse, l’application doit-elle répondre ? 1 seconde ? 1 milliseconde ?
3. Comprendre et exploiter correctement la plateforme d’hébergement de votre application.
	* Quelles fonctionnalités dois-je utiliser pour atteindre mes objectifs de mise à l’échelle ?

Cette section vous aidera à comprendre tous les facteurs et à concevoir une stratégie tirant parti des fonctionnalités d’App Service nécessaires pour atteindre vos objectifs d’évolutivité.

[AZURE.INCLUDE [app-service-blueprint-scaling-app-service-applications](../../includes/app-service-blueprint-scaling-app-service-applications.md)]

<!---HONumber=AcomDC_0907_2016-->
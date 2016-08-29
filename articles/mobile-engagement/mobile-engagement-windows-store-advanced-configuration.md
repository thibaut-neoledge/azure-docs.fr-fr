<properties
	pageTitle="Configuration avancée du Kit de développement logiciel (SDK) des applications Windows Universal pour Engagement"
	description="Options de configuration avancées pour Engagement avec des applications Windows Universal" 					
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/12/2016"
	ms.author="piyushjo;ricksal" />

# Configuration avancée du Kit de développement logiciel (SDK) des applications Windows Universal pour Engagement

> [AZURE.SELECTOR]
- [Windows universel](mobile-engagement-windows-store-advanced-configuration.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advan.mdced-configuration.md)

Cette procédure explique comment définir différentes options de configuration pour les applications Azure Mobile Engagement pour Android.

## Composants requis

[AZURE.INCLUDE [Conditions préalables](../../includes/mobile-engagement-windows-store-prereqs.md)]

##Configuration avancée

### Désactiver le signalement automatique des incidents

Vous pouvez désactiver la fonctionnalité de signalement automatique des incidents d'Engagement. Puis, lorsqu’une exception non gérée se produit, Engagement ne fait rien.

> [AZURE.WARNING] Si vous désactivez cette fonctionnalité et qu'un incident non géré se produit dans votre application, 'Engagement n'envoie pas l'incident **ET** qu'il ne ferme ni la session ni les tâches.

Pour désactiver le signalement automatique des incidents, personnalisez votre configuration en fonction de la façon dont vous l'avez déclarée :

#### Dans le fichier `EngagementConfiguration.xml`

Affectez au signalement des incidents la valeur `false` entre les balises `<reportCrash>` et `</reportCrash>`.

#### Dans l'objet `EngagementConfiguration` au moment l'exécution

Affectez au signalement des incidents la valeur false à l'aide de votre objet EngagementConfiguration.

		/* Engagement configuration. */
		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

		/* Disable Engagement crash reporting. */
		engagementConfiguration.Agent.ReportCrash = false;

### Désactiver les rapports en temps réel

Par défaut, le service Engagement génère des journaux en temps réel. Si votre application crée des journaux très fréquemment, il est préférable de les mettre en mémoire tampon et de les rassembler dans un rapport à intervalle régulier. On parle dans ce cas de « mode rafale ».

Pour cela, appelez la méthode :

		EngagementAgent.Instance.SetBurstThreshold(int everyMs);

L'argument est une valeur en **millisecondes**. Si vous souhaitez réactiver la génération de journaux en temps réel, appelez la méthode sans aucun paramètre ou avec la valeur 0.

Le mode rafale accroît légèrement l'autonomie de la batterie, mais il affecte aussi Engagement Monitor. En effet, la durée des sessions et des tâches est arrondie au seuil de rafale (les sessions et les tâches plus courtes que le seuil de rafale ne sont donc pas visibles). Nous vous recommandons d'utiliser un seuil de rafale inférieur à 30000 (30 s). Les journaux enregistrés sont limités à 300 entrées. Si l'envoi est trop long, vous risquez de perdre certains journaux.

> [AZURE.WARNING] Il n'est pas possible de configurer un seuil de rafale inférieur à une seconde. Sinon, le SDK affiche une trace avec l'erreur et rétablit automatiquement la valeur par défaut, c'est-à-dire zéro seconde. Le SDK génère alors les journaux en temps réel.

[here]: http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]: http://docs.nuget.org/docs/start-here/overview

<!---HONumber=AcomDC_0817_2016-->
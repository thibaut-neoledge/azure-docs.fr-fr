<properties 
	pageTitle="Vue d'ensemble du Kit de développement logiciel (SDK) Azure Engagement Mobile pour iOS" 
	description="Dernières mises à jour et procédures du Kit de développement logiciel (SDK) iOS pour Azure Mobile Engagement"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

# 2.0.0

Cliquez ici pour obtenir tous les détails de l'intégration d'Azure Mobile Engagement dans une application iOS. Si vous souhaitez tout d'abord l'essayer, veillez à suivre nos [didacticiel de 15 minutes](mobile-engagement-ios-get-started.md).

Cliquez pour voir le [contenu du Kit de développement logiciel](mobile-engagement-ios-sdk-content.md)

## Procédures d'intégration
1. Démarrez ici : [Intégration de Mobile Engagement dans votre application iOS](mobile-engagement-ios-integrate-engagement.md)

2. Pour les Notifications : [Comment intégrer Reach (Notifications) dans votre application iOS](mobile-engagement-ios-integrate-engagement-reach.md)

3. Implémentation du plan de balises : [Utilisation de l'API avancée de balisage de Mobile Engagement dans votre application iOS](mobile-engagement-ios-use-engagement-api.md)


## Notes de publication

### 2.0.0 (17/02/2015)

-   Version initiale d'Azure Mobile Engagement
-   Remplacement de la configuration de l'appId/sdkKey par une configuration de chaîne de connexion.
-   Suppression de l'API pour envoyer et recevoir des messages XMPP arbitraires d'entités XMPP arbitraires.
-   Suppression de l'API pour envoyer et recevoir des messages entre périphériques.
-   Améliorations de sécurité.
-   Suppression du suivi SmartAd.

Pour les versions antérieures, consultez le [les notes de publication complètes](mobile-engagement-ios-release-notes.md)

## Procédures de mise à niveau

Si vous avez déjà intégré une version antérieure d'Engagement dans votre application, vous devez prendre en compte les points suivants lors de la mise à niveau du Kit de développement logiciel.

Vous devrez peut-être suivre plusieurs procédures si vous avez manqué plusieurs versions du Kit de développement logiciel. Voir les [procédures de mise à niveau](mobile-engagement-ios-upgrade-procedure.md) complètes.

Pour chaque nouvelle version du Kit de développement logiciel, vous devez d'abord remplacer (supprimer et importer de nouveau dans xcode) les dossiers EngagementSDK et EngagementReach.

### De 1.16.0 à 2.0.0
La procédure qui suit décrit comment migrer une intégration du Kit de développement logiciel (SDK) du service Capptain offert par Capptain SAS dans une application reposant sur Azure Mobile Engagement. 

>[Azure.IMPORTANT] Capptain et Mobile Engagement ne sont pas les mêmes services, et la procédure décrite ci-dessous explique uniquement la migration de l'application cliente. La migration du Kit de développement logiciel dans l'application NE migre PAS vos données à partir des serveurs Capptain vers les serveurs de Mobile Engagement

Si vous effectuez une migration depuis une version antérieure, consultez le site web Capptain pour migrer vers la version 1.16 tout d'abord, puis appliquez la procédure suivante

#### Agent

La méthode `registerApp:`a été remplacée par la nouvelle méthode `init:`. Votre délégué d'application doit être mis à jour en conséquence et utiliser la chaîne de connexion :

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
			  [...]
			}

Le suivi SmartAd a été supprimé du Kit de développement logiciel ; vous devez uniquement supprimer toutes les instances de la classe `AETrackModule`

#### Modifications de nom de classe

Dans le cadre du repositionnement, quelques classes/noms de fichiers doivent être modifiés.

Toutes les classes avec le préfixe " CP " sont renommées avec le préfixe " AE ".

Exemple :

-   `CPModule.h`est renommé `AEModule.h`.

Toutes les classes avec le préfixe " Capptain " sont renommées avec le préfixe " Engagement ".

Exemples :

-   La classe `CapptainAgent` est renommée `EngagementAgent`.
-   La classe `CapptainTableViewController` est renommée `EngagementTableViewController`.
-   La classe `CapptainUtils` est renommée `EngagementUtils`.
-   La classe `CapptainViewController` est renommée `EngagementViewController`.

<!--HONumber=47-->

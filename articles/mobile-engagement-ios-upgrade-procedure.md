<properties 
	pageTitle="Azure Engagement Mobile iOS procédure de mise à niveau SDK" 
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

# Procédures de mise à niveau

Si vous avez déjà intégré une version antérieure d'Engagement dans votre application, vous devez prendre en compte les points suivants lors de la mise à niveau du Kit de développement logiciel.

Pour chaque nouvelle version du Kit de développement logiciel, vous devez d'abord remplacer (supprimer et importer de nouveau dans xcode) les dossiers EngagementSDK et EngagementReach.

## De 1.16.0 à 2.0.0
La procédure qui suit décrit comment migrer une intégration du Kit de développement logiciel du service Capptain offert par Capptain SAS dans une application reposant sur Azure Mobile Engagement. 

>[Azure.IMPORTANT] Capptain et Mobile Engagement ne sont pas les mêmes services, et la procédure décrite ci-dessous explique uniquement la migration de l'application cliente. La migration du Kit de développement logiciel dans l'application NE migre PAS vos données des serveurs Capptain vers les serveurs Mobile Engagement

Si vous effectuez une migration depuis une version antérieure, consultez le site Web Capptain pour migrer tout d'abord vers la version 1.16, puis appliquez la procédure suivante

### Agent

La méthode `registerApp:` a été remplacée par la nouvelle méthode `init:`. Votre délégué d'application doit être mis à jour en conséquence et utiliser la chaîne de connexion :

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
			  [...]
			}

Le suivi SmartAd a été supprimé du Kit de développement logiciel (SDK) ; vous devez uniquement supprimer toutes les instances de la classe `AETrackModule`

### Modifications de nom de classe

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

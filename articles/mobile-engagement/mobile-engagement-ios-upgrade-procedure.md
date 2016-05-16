<properties
	pageTitle="Azure Engagement Mobile iOS procédure de mise à niveau SDK"
	description="Dernières mises à jour et procédures du Kit de développement logiciel (SDK) iOS pour Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="MehrdadMzfr"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="MehrdadMzfr" />

#Procédures de mise à niveau

Si vous avez déjà intégré une version antérieure d'Engagement dans votre application, vous devez prendre en compte les points suivants lors de la mise à niveau du Kit de développement logiciel.

Pour chaque nouvelle version du Kit de développement logiciel, vous devez d'abord remplacer (supprimer et importer de nouveau dans xcode) les dossiers EngagementSDK et EngagementReach.

##Migration de 2.0.0 vers 3.0.0
Prise en charge d’iOS 4.X abandonnée. À partir de cette version, la cible de déploiement de votre application doit être au moins iOS 6.

Si vous utilisez Reach dans votre application, vous devez ajouter la valeur `remote-notification` au tableau `UIBackgroundModes` dans votre fichier Info.plist pour recevoir des notifications à distance.

La méthode `application:didReceiveRemoteNotification:` doit être remplacée par `application:didReceiveRemoteNotification:fetchCompletionHandler:` dans votre délégué d’application.

« AEPushDelegate.h » est une interface déconseillée et vous devez supprimer toutes les références. Cela inclut notamment la suppression `[[EngagementAgent shared] setPushDelegate:self]` et les méthodes de délégation depuis votre délégué d’application :

	-(void)willRetrieveLaunchMessage;
	-(void)didFailToRetrieveLaunchMessage;
	-(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

##De 1.16.0 à 2.0.0
La section qui suit décrit comment migrer une intégration du SDK à partir du service Capptain offert par Capptain SAS dans une application reposant sur Azure Mobile Engagement. Si vous effectuez une migration depuis une version antérieure, veuillez d’abord consulter le site web Capptain pour effectuer une migration vers la version 1.16, puis appliquer la procédure suivante.

>[AZURE.IMPORTANT] Capptain et Engagement Mobile ne sont pas les mêmes services et la procédure décrite ci-dessous explique uniquement comment migrer l'application cliente. La migration du SDK dans l'application ne migre PAS vos données des serveurs Capptain vers les serveurs Engagement Mobile.

### Agent

La méthode `registerApp:` a été remplacée par la nouvelle méthode `init:`. Votre délégué d'application doit être mis à jour en conséquence et utiliser la chaîne de connexion :

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
			  [...]
			}

Le suivi SmartAd a été supprimé du Kit de développement logiciel (SDK). Vous devez seulement supprimer toutes les instances de la classe `AETrackModule`

### Modifications de nom de classe

Dans le cadre du repositionnement, quelques classes/noms de fichiers doivent être modifiés.

Toutes les classes avec le préfixe « CP » sont renommées avec le préfixe « AE ».

Exemple :

-   `CPModule.h` est renommé `AEModule.h`.

Toutes les classes avec le préfixe « Capptain » sont renommées avec le préfixe « Engagement ».

Exemples :

-   La classe `CapptainAgent` est renommée `EngagementAgent`.
-   La classe `CapptainTableViewController` est renommée `EngagementTableViewController`.
-   La classe `CapptainUtils` est renommée `EngagementUtils`.
-   La classe `CapptainViewController` est renommée `EngagementViewController`.

<!---HONumber=AcomDC_0504_2016-->
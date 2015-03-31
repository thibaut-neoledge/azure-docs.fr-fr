<properties 
	pageTitle="Intégration du Kit de développement logiciel (SDK) Windows Phone pour Azure Mobile Engagement" 
	description="Dernières mises à jour et procédures relatives au Kit de développement logiciel (SDK) Windows Phone pour Azure Mobile Engagement" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

#Kit SDK Windows Phone pour Azure Mobile Engagement v2.0.0
Cette introduction contient toutes les informations nécessaires pour intégrer Azure Mobile Engagement à une application Windows Phone. Si vous souhaitez commencer par la pratique, suivez notre [didacticiel de 15 minutes](../mobile-engagement-windows-phone-get-started/).

Pour afficher le contenu du Kit SDK, cliquez [ici](../mobile-engagement-windows-phone-sdk-content/).

##Procédures d'intégration

1. Commencez ici : [Comment intégrer Mobile Engagement à votre application Windows Phone](../mobile-engagement-windows-phone-integrate-engagement/)

2. Pour Notifications : [Comment intégrer Reach (Notifications) à votre application Windows Phone](../mobile-engagement-windows-phone-integrate-engagement-reach/)

3. Implémentation du plan de balisage : [Comment utiliser l'API de balisage avancée Mobile Engagement dans votre application Windows Phone](../mobile-engagement-windows-phone-use-engagement-api/)

##Notes de publication

###2.0.0 (17/02/2015)
-   Version initiale d'Azure Mobile Engagement
-   La configuration d'une chaîne de connexion remplace désormais la configuration d'appId/sdkKey.
-   Améliorations de la sécurité.

Pour les versions antérieures, consultez la [version complète des notes de publication](../mobile-engagement-windows-phone-release-notes/).

##Procédures de mise à niveau

Si vous avez déjà intégré une ancienne version de notre Kit SDK à votre application, tenez compte des points suivants avant de procéder à la mise à niveau du Kit SDK.

Si vous avez omis plusieurs versions du Kit SDK, vous devrez peut-être effectuer des procédures supplémentaires. Consultez la [version complète des notes de publication](mobile-engagement-windows-phone-upgrade-procedure/). Par exemple, si vous migrez la version 0.10.1 vers la version 0.11.0, vous devez d'abord suivre la procédure " De 0.9.0 à 0.10.1 ", puis la procédure " De 0.10.1 à 0.11.0 ".

###De 1.1.1 à 2.0.0

La procédure suivante décrit comment migrer une intégration du SDK du service Capptain offert par Capptain SAS vers une application optimisée par Azure Mobile Engagement. 

>[Azure.IMPORTANT] Capptain et Mobile Engagement ne sont pas les mêmes services, et la procédure décrite ci-dessous traite uniquement de la migration de l'application cliente. Le fait de migrer le Kit SDK dans l'application NE migre PAS vos données des serveurs Capptain vers les serveurs Mobile Engagement.

Si vous effectuez la migration à partir d'une version antérieure, consultez le site web de Capptain pour réaliser d'abord la migration vers la version 1.1.1, puis appliquez la procédure suivante.

#### Package NuGet

Remplacez Capptain.WindowsPhone.nupkg par azuresdk-mobileengagement-windowsphone-VERSION.nupkg dans le dossier lib du package d'archive.

#### Application de Mobile Engagement

Le Kit SDK utilise le terme `Engagement`. Vous devez mettre à jour votre projet pour refléter ce changement.

Il est nécessaire de désinstaller le package NuGet Capptain actif. Sachez que toutes les modifications que vous avez apportées dans le dossier Capptain Resources seront supprimées. Si vous souhaitez conserver ces fichiers, faites-en une copie.

Ensuite, installez le nouveau package NuGet Microsoft Azure Engagement sur votre projet. Celui-ci est disponible sur le [site web de NuGet]. Cette action remplace tous les fichiers de ressources utilisés par Engagement et ajoute la nouvelle DLL Engagement aux références de votre projet.

Vous devez nettoyer les références de votre projet en supprimant les références aux DLL Capptain. Si vous n'effectuez pas cette opération, la version de Capptain génère un conflit qui entraîne des erreurs.

Si vous avez personnalisé les ressources Capptain, copiez le contenu de vos anciens fichiers et collez-le dans les nouveaux fichiers Engagement. Notez que les fichiers xaml et cs doivent être mis à jour.

Une fois ces étapes terminées, il vous suffit de remplacer les anciennes références à Capptain par les nouvelles références à Engagement.

Tous les espaces de noms Capptain doivent être mis à jour.

Avant la migration :

			using Capptain.Agent;
			using Capptain.Reach;

After migration:

			using Microsoft.Azure.Engagement;

All Capptain classes that contain "Capptain" should contain "Engagement".

Before migration:

			public sealed partial class MainPage : CapptainPage
			{
			  protected override string GetCapptainPageName()
			  {
			    return "Capptain Demo";
			  }
			  ...
			}

Après la migration :

			public sealed partial class MainPage : EngagementPage
			{
			  protected override string GetEngagementPageName()
			  {
			    return "Engagement Demo";
			  }
			  ...
			}

Pour les fichiers xaml, l'espace de noms et les attributs Capptain doivent également être modifiés.

Avant la migration :

			<capptain:CapptainPage
			...
			xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
			...
			</capptain:CapptainPage>

Après la migration :

			<engagement:EngagementPage
			...
			xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
			...
			</engagement:EngagementPage>

Notez que les autres ressources, comme les images Capptain, ont aussi été renommées afin d'utiliser " Engagement ".

#### ID d'application / clé SDK

Engagement utilise une chaîne de connexion. Il est donc inutile de spécifier un ID d'application et une clé SDK avec Mobile Engagement. Il suffit de spécifier une chaîne de connexion que vous pouvez configurer dans votre fichier EngagementConfiguration.

Vous pouvez définir la configuration d'Engagement dans le fichier `Resources\EngagementConfiguration.xml` de votre projet.

Modifiez ce fichier pour spécifier :

-   La chaîne de connexion de votre application entre les balises `<connectionString>` et `<\connectionString>`.

Si vous préférez la spécifier au moment de l'exécution, appelez la méthode suivante avant l'initialisation de l'agent Engagement :

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			
			/* Initialize Engagement angent with above configuration. */
			EngagementAgent.Instance.Init(engagementConfiguration);

La chaîne de connexion de votre application s'affiche dans le portail Azure.

#### Changement de nom des éléments

Tous les éléments nommés ...capptain... ont été renommés ...engagement... Il en va de même pour Capptain et Engagement.

Exemples d'éléments Capptain couramment utilisés :

> -   CapptainConfiguration, désormais nommé EngagementConfiguration
> -   CapptainAgent, désormais nommé EngagementAgent
> -   CapptainReach, désormais nommé EngagementReach
> -   CapptainHttpConfig, désormais nommé EngagementHttpConfig
> -   GetCapptainPageName, désormais nommé GetEngagementPageName

Notez que ce changement de nom affecte également les méthodes remplacées.

### Mise à niveau à partir de versions antérieures

Consultez [Procédures de mise à niveau](mobile-engagement-windows-phone-upgrades/)

<!--HONumber=47-->

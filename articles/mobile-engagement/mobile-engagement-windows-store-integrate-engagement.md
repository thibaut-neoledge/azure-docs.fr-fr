<properties 
	pageTitle="Intégration du Kit de développement logiciel du module Engagement des applications universelles Windows" 
	description="Intégration du module Azure Mobile Engagement avec des applications universelles Windows" 					
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
	ms.date="07/07/2015" 
	ms.author="piyushjo" />

#Intégration du Kit de développement logiciel du module Engagement des applications universelles Windows

> [AZURE.SELECTOR] 
- [Universal Windows](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

Cette procédure décrit comment activer les fonctions d'analyse et de surveillance d'Engagement dans votre application universelle Windows de la manière la plus simple possible.

Les étapes suivantes permettent d'activer la génération des journaux nécessaires pour calculer toutes les statistiques concernant les utilisateurs, les sessions, les activités, les incidents et les informations techniques. Pour calculer d'autres statistiques qui dépendent de l'application, comme les événements, les erreurs et les tâches, vous devez générer manuellement les journaux correspondants à l'aide de l'API Engagement. Consultez [Comment utiliser l'API de balisage avancée Mobile Engagement dans votre application universelle Windows](mobile-engagement-windows-store-use-engagement-api.md)

##Versions prises en charge

Le Kit de développement Engagement Mobile pour les applications universelles Windows peut uniquement être intégré aux applications Windows Runtime ciblant :

-   Windows 8
-   Windows 8.1
-   Windows Phone 8,1

> [AZURE.NOTE]Si vous ciblez Windows Phone 8.1 Silverlight, consultez la [procédure d’intégration Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md).


##Installation du Kit de développement d'applications universelles Mobile Engagement

Le Kit de développement Mobile Engagement pour l’application universelle Windows est disponible comme package Nuget appelé *MicrosoftAzure.MobileEngagement*. Vous pouvez l'installer à partir du gestionnaire de package Nuget Visual Studio.

##Ajouter les fonctionnalités

Pour fonctionner correctement, le SDK Engagement fait appel à certaines fonctionnalités du SDK Windows.

Ouvrez votre fichier `Package.appxmanifest` et vérifiez que les fonctionnalités suivantes sont déclarées :

-   `Internet (Client)`

##Initialiser le SDK Engagement

### Configuration d'Engagement

La configuration d'Engagement est centralisée dans le fichier `Resources\EngagementConfiguration.xml` de votre projet.

Modifiez ce fichier pour spécifier :

-   Votre chaîne de connexion d'application entre les balises `<connectionString>` et `<\connectionString>`.

Si vous souhaitez plutôt la spécifier au moment de l'exécution, vous pouvez appeler la méthode suivante avant l'initialisation de l'agent Engagement :
          
          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

        #if WINDOWS_PHONE_APP
          /* Connection string for my Windows Phone App. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        #else
          /* Connection string for my Windows Store App. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        #endif

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);

La chaîne de connexion de votre application est affichée sur le portail Azure.

> [AZURE.WARNING]Vous n'avez pas besoin d'utiliser le symbole de compilation conditionnelle `WINDOWS_PHONE_APP` pour définir une configuration différente sur des applications Windows Runtime autonomes, car vous disposez d'une seule plateforme.

### Initialisation d'Engagement

Quand vous créez un projet, un fichier `App.xaml.cs` est généré. Cette classe hérite de `Application` et contient de nombreuses méthodes importantes. Son rôle consiste également à initialiser le SDK Engagement.

Modifiez le fichier `App.xaml.cs` :

-   Ajoutez à vos instructions `using` :

		using Microsoft.Azure.Engagement;

-   Insérez `EngagementAgent.Instance.Init` dans la méthode `OnLaunched` :

		protected override void OnLaunched(LaunchActivatedEventArgs args)
		{
		  EngagementAgent.Instance.Init(args);
		
		  // or
		
		  EngagementAgent.Instance.Init(args, engagementConfiguration);
		}

-   Si votre application est lancée à partir d'un schéma personnalisé, d'une autre application ou de la ligne de commande, la méthode `OnActivated` est appelée. Vous devez également initier l'agent Engagement quand votre application est activée. Pour cela, remplacez la méthode `OnActivated` :

		protected override void OnActivated(IActivatedEventArgs args)
		{
		  EngagementAgent.Instance.Init(args);
		
		  // or
		
		  EngagementAgent.Instance.Init(args, engagementConfiguration);
		}

> [AZURE.IMPORTANT]Nous vous déconseillons fortement d'ajouter l'initialisation d'Engagement à un autre endroit de votre application.

##Génération de rapports de base

### Méthode recommandée : surchargez vos classes `Page`

Pour activer la génération de tous les journaux requis par Engagement pour calculer les statistiques concernant les utilisateurs, les sessions, les activités, les incidents et les informations techniques, vous pouvez simplement configurer toutes vos sous-classes `Page` de manière à ce qu'elles héritent des classes `EngagementPage`.

Voici un exemple qui montre comment procéder pour une page de votre application. Vous pouvez faire la même chose pour toutes les pages de votre application.

#### Fichier source C#

Modifiez le fichier `.xaml.cs` de votre page :

-   Ajoutez à vos instructions `using` :

		using Microsoft.Azure.Engagement;

-   Remplacez `Page` par `EngagementPage` :

**Sans Engagement :**
	
		namespace Example
		{
		  public sealed partial class ExamplePage : Page
		  {
		    [...]
		  }
		}

**Avec Engagement :**

		using Microsoft.Azure.Engagement;
		
		namespace Example
		{
		  public sealed partial class ExamplePage : EngagementPage 
		  {
		    [...]
		  }
		}

> [AZURE.IMPORTANT]Si votre page remplace la méthode `OnNavigatedTo`, veillez à appeler `base.OnNavigatedTo(e)`. Sinon, l'activité ne sera pas signalée (la `EngagementPage` appelle `StartActivity` à l'intérieur de sa méthode `OnNavigatedTo`).

#### Fichier XAML

Modifiez le fichier `.xaml` de votre page :

-   Ajoutez une déclaration d'espace de noms :

		xmlns:engagement="using:Microsoft.Azure.Engagement"

-   Remplacez `Page` par `engagement:EngagementPage` :

**Sans Engagement :**

		<Page>
		    <!-- layout -->
		    ...
		</Page>

**Avec Engagement :**

		<engagement:EngagementPage 
		    xmlns:engagement="using:Microsoft.Azure.Engagement">
		    <!-- layout -->
		    ...
		</engagement:EngagementPage >

#### Remplacer le comportement par défaut

Par défaut, le nom de classe de la page est signalé comme le nom de l'activité, sans informations supplémentaires. Si la classe utilise le suffixe « Page », Engagement le supprime également.

Si vous souhaitez remplacer le comportement par défaut associé au nom, ajoutez simplement ceci à votre code :

		// in the .xaml.cs file
		protected override string GetEngagementPageName()
		{
		  /* your code */
		  return "new name";
		}

Si vous souhaitez signaler des informations supplémentaires avec votre activité, vous pouvez ajouter ceci à votre code :

		// in the .xaml.cs file
		protected override Dictionary<object,object> GetEngagementPageExtra()
		{
		  /* your code */
		  return extra;
		}

Ces méthodes sont appelées depuis la méthode `OnNavigatedTo` de votre page.

### Autre méthode : appeler `StartActivity()` manuellement

Si vous ne pouvez pas ou ne souhaitez pas surcharger vos classes `Page`, vous pouvez démarrer vos activités en appelant directement les méthodes `EngagementAgent`.

Nous vous recommandons d'appeler `StartActivity` à l'intérieur de la méthode `OnNavigatedTo` de votre Page.

			protected override void OnNavigatedTo(NavigationEventArgs e)
			{
			  base.OnNavigatedTo(e);
			  EngagementAgent.Instance.StartActivity("MyPage");
			}

> [AZURE.IMPORTANT]Assurez-vous de terminer votre session correctement.
> 
> Le Kit de développement logiciel Windows Universal appelle automatiquement la méthode `EndActivity` quand l'application est fermée. Par conséquent, il est **FORTEMENT** recommandé d'appeler la méthode `StartActivity` chaque fois que l'activité de l'utilisateur change et de ne **JAMAIS** appeler la méthode `EndActivity`. Cette dernière envoie au serveur Engagement une notification indiquant que l’utilisateur actuel doit quitter l’application, ce qui aura un impact sur tous les journaux d’application.

##Génération de rapports avancés

Vous pouvez éventuellement signaler les événements, erreurs et tâches spécifiques à l'application. Pour cela, utilisez les autres méthodes disponibles dans la classe `EngagementAgent`. L'API Engagement permet d'utiliser toutes les fonctionnalités avancées d'Engagement.

Pour plus d'informations, consultez [Utilisation de l'API de marquage avancée Mobile Engagement dans votre application Windows Phone](../mobile-engagement-windows-store-use-engagement-api/).

##Configuration avancée

### Désactiver le signalement automatique des incidents

Vous pouvez désactiver la fonctionnalité de signalement automatique des incidents d'Engagement. Dans ce cas, si une exception non gérée se produit, Engagement ne fait rien.

> [AZURE.WARNING]Si vous désactivez cette fonctionnalité et qu'un incident non géré se produit dans votre application, sachez qu'Engagement n'envoie pas l'incident **ET** qu'il ne ferme ni la session ni les tâches.

Pour désactiver le signalement automatique des incidents, il vous suffit de personnaliser votre configuration en fonction de la façon dont vous l'avez déclarée :

#### Dans le fichier `EngagementConfiguration.xml`

Affectez au signalement des incidents la valeur `false` entre les balises `<reportCrash>` et `</reportCrash>`.

#### Dans l'objet `EngagementConfiguration` au moment l'exécution

Affectez au signalement des incidents la valeur false à l'aide de votre objet EngagementConfiguration.

		/* Engagement configuration. */
		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
		
		/* Disable Engagement crash reporting. */
		engagementConfiguration.Agent.ReportCrash = false;

### Mode rafale

Par défaut, le service Engagement génère des journaux en temps réel. Si votre application génère très fréquemment des journaux, il est préférable de les mettre en mémoire tampon et de les générer tous en même temps à intervalles réguliers (« mode rafale »).

Pour cela, appelez la méthode :

		EngagementAgent.Instance.SetBurstThreshold(int everyMs);

L'argument est une valeur en **millisecondes**. Si vous souhaitez réactiver la génération de journaux en temps réel, vous pouvez appeler à tout moment la méthode sans aucun paramètre ou avec la valeur 0.

Le mode rafale accroît légèrement l'autonomie de la batterie, mais il affecte aussi Engagement Monitor. En effet, la durée des sessions et des tâches est arrondie au seuil de rafale (les sessions et les tâches plus courtes que le seuil de rafale ne sont donc pas visibles). Il est recommandé d'utiliser un seuil de rafale inférieur à 30 000 (30 s). Vous devez savoir que les journaux enregistrés sont limités à 300 entrées. Si l'envoi est trop long, vous risquez de perdre certains journaux.

> [AZURE.WARNING]Vous ne pouvez pas configurer un seuil de rafale inférieur à 1 s. Sinon, le SDK affiche une trace avec l'erreur et rétablit automatiquement la valeur par défaut, c'est-à-dire 0 s. Le SDK génère alors les journaux en temps réel.

[here]: http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]: http://docs.nuget.org/docs/start-here/overview
 

<!---HONumber=August15_HO6-->
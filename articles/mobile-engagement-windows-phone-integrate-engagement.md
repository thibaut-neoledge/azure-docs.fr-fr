<properties 
	pageTitle="Intégration du Kit de développement logiciel (SDK) Azure Mobile Engagement dans Windows Phone" 
	description="Intégration d'Engagement dans Windows Phone" 					
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


<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-integrate-engagement/" title="Windows Store">Windows Store</a><a href="/documentation/articles/mobile-engagement-windows-phone-integrate-engagement/" title="Windows Phone" class="current">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-integrate-engagement/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-integrate-engagement/" title="Android" >Android</a></div>

# Intégration d'Engagement dans Windows Phone

Cette procédure décrit la méthode la plus simple pour activer l'analyse et la surveillance Engagement dans votre application Windows Phone.

Les étapes suivantes permettent d'activer la génération des journaux nécessaires pour calculer toutes les statistiques concernant les utilisateurs, les sessions, les activités, les incidents et les informations techniques. La génération des journaux nécessaires au calcul d'autres statistiques telles que les événements, les erreurs et les tâches doit être effectuée manuellement à l'aide de l'API Engagement (voir la section  [Utilisation avancée de l'API de marquage Mobile Engagement dans votre application Windows Phone](mobile-engagement-windows-phone-use-engagement-api.md)  ci-dessous) dans la mesure où ces statistiques dépendent de l'application.

## Versions prises en charge

Le Kit de développement logiciel Engagement pour Windows Phone peut uniquement être intégré dans les applications ciblant :

-   Windows Phone 8.0
-   Windows Phone 8.1 avec Silverlight

Le SDK Engagement pour Windows Phone est compatible avec les périphériques Windows Phone 8.0 et 8.1.

## Intégration du SDK Engagement dans votre projet Windows Phone

Le package NuGet pour Mobile Engagement n'est pas encore disponible en ligne. Vous devez donc télécharger notre archive du SDK et le décompresser. Ensuite dans Visual Studio, cliquez avec le bouton droit sur votre projet pour accéder à " Gérer les packages NuGet ", à Paramètres, puis ajoutez le dossier lib comme une nouvelle source du package.


> [AZURE.IMPORTANT] NuGet est le principal gestionnaire de package pour les projets .net. Il téléchargera, ajoutera et copiera tout ce dont vous avez besoin pour utiliser le SDK Engagement. Sur Windows Phone 8, Engagement utilise des bibliothèques tierces (Microsoft.Bcl.Build v1.0.10 et pack de portabilité Microsoft.Bcl v1.1.3) à des fins de portabilité interplateforme. NuGet vous demande automatiquement de les installer également. Vous trouverez plus d'informations sur le [site Web de NuGet](http://docs.nuget.org/docs/start-here/overview).

## Ajout de fonctionnalités

Le SDK Engagement a besoin de certaines fonctionnalités du SDK Windows Phone pour pouvoir fonctionner correctement.

Ouvrez votre fichier  `WMAppManifest.xml`  et assurez-vous que les fonctionnalités suivantes sont indiquées dans le volet `Capabilities`  :

-   `ID_CAP_NETWORKING`
-   `ID_CAP_IDENTITY_DEVICE`

## Initialisation du SDK Engagement

### Configuration d'Engagement

La configuration d'Engagement est centralisée dans le fichier  `Resources\EngagementConfiguration.xml` de votre projet.

Modifiez ce fichier pour spécifier :

-   La chaîne de connexion de votre application entre les balises de `<connectionString>` et  `<\connectionString>`.

Si vous souhaitez plutôt la spécifier lors de l'exécution, vous pouvez appeler la méthode suivante avant l'initialisation de l'agent Engagement :

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

			/* Initialize Engagement agent with above configuration. */
			EngagementAgent.Instance.Init(engagementConfiguration);

La chaîne de connexion pour votre application figure sur le portail Azure.

### Initialisation d'Engagement

Lorsque vous créez un nouveau projet, un fichier `App.xaml.cs`  est généré. Cette classe hérite de  `Application` et contient de nombreuses méthodes importantes. Elle servira également à initialiser le SDK Engagement.

Modifiez `App.xaml.cs` :

-   Ajoutez à vos instructions `using`  :

			using Microsoft.Azure.Engagement;

-   Insérez `EngagementAgent.Instance.Init` dans la méthode `Application_Launching`  :

			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			  EngagementAgent.Instance.Init();
			}

-   Insérez `EngagementAgent.Instance.OnActivated` dans la méthode `Application_Activated` :

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			   EngagementAgent.Instance.OnActivated(e);
			}

> [AZURE.WARNING] Nous vous déconseillons fortement d'ajouter l'initialisation d'Engagement à un autre endroit de votre application. Toutefois, sachez que la méthode `EngagementAgent.Instance.Init` s'exécute sur un thread dédié et non sur le thread d'interface utilisateur.

## Création de rapports de base

### Méthode recommandée : surchargement de vos classes `PhoneApplicationPage`

Pour activer la génération de tous les journaux requis par Engagement pour calculer les informations relatives aux utilisateurs, sessions, activités, incidents et statistiques techniques, vous pouvez simplement faire en sorte que toutes vos sous-classes `PhoneApplicationPage` héritent des classes `EngagementPage`.

Voici un exemple de procédure à suivre pour une page de votre application. Vous pouvez reproduire la procédure pour toutes les pages de votre application.

#### Fichier source C\#

Modifiez le fichier `.xaml.cs` de votre page :

-   Ajoutez à vos instructions `using` :

			using Microsoft.Azure.Engagement;

-   Remplacez `PhoneApplicationPage` par `EngagementPage` :

**Sans Engagement :**

			namespace Example
			{
			  public partial class ExamplePage : PhoneApplicationPage
			  {
			    [...]
			  }
			}

**Avec Engagement :**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			  public partial class ExamplePage : EngagementPage 
			  {
			    [...]
			  }
			}

> [AZURE.WARNING] Si votre page hérite de la méthode `OnNavigatedTo`, veillez à laisser l'appel `base.OnNavigatedTo(e)`. Sinon, l'activité ne sera pas signalée. En effet, l'appel`EngagementPage` invoque`StartActivity` à l'intérieur de la méthode `OnNavigatedTo`.

#### Fichier XAML

Modifiez le fichier `.xaml` de votre page :

-   Ajoutez à vos déclarations d'espaces de noms :

			xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

-   Remplacez `phone:PhoneApplicationPage` par `engagement:EngagementPage` :

**Sans Engagement :**

			<phone:PhoneApplicationPage>
			    <!-- layout -->
			</phone:PhoneApplicationPage>

**Avec Engagement :**

			<engagement:EngagementPage 
			    xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP">
			
			    <!-- layout -->
			</engagement:EngagementPage >

#### Remplacement du comportement par défaut

Par défaut, le nom de classe de la page est identique au nom de l'activité, sans ajout. Si la classe utilise le suffixe " Page ", Engagement le supprimera également.

Si vous souhaitez remplacer le comportement par défaut pour le nom, ajoutez simplement ceci à votre code :

			// in the .xaml.cs file
			protected override string GetEngagementPageName()
			{
			   /* your code */
			   return "new name";
			}

Si vous souhaitez signaler des informations supplémentaires à votre activité, vous pouvez ajouter la portion suivante à votre code :

			// in the .xaml.cs file
			protected override Dictionary<object,object> GetEngagementPageExtra()
			{
			   /* your code */
			   return extra;
			}

Vous trouverez davantage de détails sur les informations supplémentaires [sur cette page](../mobile-engagement-windows-phone-use-engagement-api/#extras-parameters).

Ces méthodes sont appelées à partir de la méthode  `OnNavigatedTo` de votre page.

### Méthode alternative : appel  `StartActivity()` manuel

Si vous ne pouvez pas ou ne voulez pas surcharger vos classes  `PhoneApplicationPage`, vous pouvez démarrer vos activités en appelant directement les méthodes  `EngagementAgent`.

Nous vous recommandons d'appeler  `StartActivity` à l'intérieur de la méthode  `OnNavigatedTo` de votre PhoneApplicationPage.

			protected override void OnNavigatedTo(NavigationEventArgs e)
			{
			   base.OnNavigatedTo(e);
			   EngagementAgent.Instance.StartActivity("MyPage");
			}

> [AZURE.IMPORTANT] Assurez-vous de terminer votre session correctement.
>
> Le SDK Windows Phone appelle automatiquement la méthode  `EndActivity` lors de la fermeture de l'application. Par conséquent, il est **FORTEMENT** recommandé d'appeler la méthode  `StartActivity` chaque fois que l'activité de l'utilisateur change et de ne **JAMAIS** appeler la méthode  `EndActivity`, étant donné que l'appel de cette méthode entraînerait l'arrêt forcé de l'application en cours.

## Création de rapports avancés

Vous pouvez choisir de signaler des événements, erreurs et tâches d'application spécifiques. Pour ce faire, utilisez les autres méthodes de la classe  `EngagementAgent`. L'API Engagement permet d'utiliser toutes les fonctionnalités avancées d'Engagement.

Pour plus d'informations, consultez [Utilisation de l'API de marquage avancée Mobile Engagement dans votre application Windows Phone](mobile-engagement-windows-phone-use-the-engagement-api.md).

## Configuration avancée

### Désactivation du signalement automatique d'incident

Vous pouvez désactiver la fonctionnalité de signalement automatique d'incident d'Engagement. Ainsi, lorsqu'une exception non prise en charge se produit, Engagement ne réagit pas.

> [AZURE.WARNING] Si vous envisagez de désactiver cette fonctionnalité, sachez que si un incident non pris en charge se produit dans votre application, Engagement n'enverra pas l'incident **ET** ne fermera ni la session ni les tâches.

Pour désactiver le signalement automatique d'incident, il suffit de personnaliser votre configuration en fonction de la façon dont vous l'avez déclaré :

#### À partir du fichier  `EngagementConfiguration.xml`

Définissez le signalement d'incident sur  `false` entre les balises `<reportCrash>` et `</reportCrash>`.

#### À partir de l'objet  `EngagementConfiguration` durant l'exécution

Définissez le signalement d'incident sur false à l'aide de l'objet EngagementConfiguration.

			/* Engagement configuration. */

			EngagementConfiguration engagementConfiguration = new EngagementConfiguration(); engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			/\* Disable Engagement crash reporting. \*/ engagementConfiguration.Agent.ReportCrash = false;

### Mode rafale [beta]

Par défaut, le service Engagement génère des journaux en temps réel. Si votre application génère des journaux très fréquemment, il est préférable de mettre les journaux en mémoire tampon et de générer tous les rapports en même temps à une fréquence définie (cela s'appelle le " mode rafale ").

Pour ce faire, appelez la méthode :

			EngagementAgent.Instance.SetBurstThreshold(int everyMs);

L'argument est une valeur en **millisecondes**. À tout moment, si vous souhaitez réactiver la journalisation en temps réel, il vous suffit d'appeler la méthode sans paramètre ou avec la valeur 0.

Le mode rafale permet d'augmenter légèrement l'autonomie de la batterie, mais a un impact sur l'analyse Engagement. En effet, toutes les durées de session et de tâches seront arrondies à la valeur de seuil de la rafale (par conséquent, les sessions et les tâches dont la durée est inférieure au seuil de rafale ne sont pas visibles). Il est recommandé d'utiliser un seuil de rafale inférieur à 30 000 (30 s).

> [AZURE.WARNING] Il n'est pas possible de configurer un seuil de rafale inférieur à une seconde. Si vous essayez de le faire, le SDK affichera une erreur et réinitialisera automatiquement la valeur par défaut, c'est-à-dire zéro seconde. Cela amènera le SDK à générer des journaux en temps réel.

<!--HONumber=47-->

<properties 
	pageTitle="Intégration du Kit de développement logiciel (SDK) Windows Phone pour Azure Mobile Engagement" 
	description="Comment utiliser l'API Engagement sur Windows Phone" 					
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
	ms.date="02/02/2015" 
	ms.author="kapiteir" />

#Comment utiliser l'API Engagement sur Windows Phone

Ce document vient compléter celui intitulé [Comment intégrer Mobile Engagement à votre application Windows Phone](../mobile-engagement-windows-phone-integrate-engagement/). Il fournit des informations détaillées sur l'utilisation de l'API Engagement pour signaler les statistiques de votre application.

Si vous souhaitez qu'Engagement signale uniquement les sessions, activités, incidents et informations techniques de votre application, la méthode la plus simple consiste à configurer toutes vos sous-classes `PhoneApplicationPage` de manière à ce qu'elles héritent de la classe  `EngagementPage`.

Si vous souhaitez aller plus loin, par exemple pour signaler les événements, erreurs et tâches spécifiques à l'application, ou si vous souhaitez signaler les activités de votre application d'une manière différente que celle implémentée dans les classes `EngagementPage`, vous devez utiliser l'API Engagement.

L'API Engagement est fournie par la classe `EngagementAgent`. Vous pouvez accéder à ces méthodes via `EngagementAgent.Instance`.

Même si le module de l'agent n'a pas été initialisé, chaque appel à l'API est différé et réexécuté une fois l'agent disponible.

##Concepts d'Engagement

Les parties suivantes décrivent plus en détail les concepts de Mobile Engagement pour la plateforme Windows Phone.

### `Session` et `Activity`

Une *activité* est généralement associée à une page de l'application, c'est-à-dire que l' *activité* démarre quand la page est affichée et qu'elle s'arrête quand la page est fermée. C'est le cas quand le SDK Engagement est intégré à l'aide de la classe `EngagementPage`.

Toutefois, vous pouvez aussi contrôler manuellement les *activités* à l'aide de l'API Engagement. Vous pouvez ainsi diviser une page donnée en plusieurs sous-parties pour obtenir davantage d'informations sur l'utilisation de cette page (par exemple pour déterminer la fréquence et la durée d'utilisation des boîtes de dialogue dans cette page).

##Signalement d'activités

### L'utilisateur démarre une nouvelle activité

#### Référence

			void StartActivity(string name, Dictionary<object, object> extras = null)

Vous devez appeler `StartActivity()` chaque fois que l'activité de l'utilisateur change. Le premier appel à cette fonction démarre une nouvelle session utilisateur.

> [AZURE.IMPORTANT] Le SDK Windows Phone appelle automatiquement la méthode EndActivity quand l'application est fermée. Par conséquent, il est FORTEMENT recommandé d'appeler la méthode StartActivity chaque fois que l'activité de l'utilisateur change et de ne JAMAIS appeler la méthode EndActivity, celle-ci forçant la fin de la session active.

#### Exemple

			EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### L'utilisateur termine son activité

#### Référence

			void EndActivity()

Ceci termine l'activité et la session. Appelez cette méthode uniquement si vous savez exactement ce que vous faites.

#### Exemple

			EngagementAgent.Instance.EndActivity();

##Signalement de tâches

### Démarrer une tâche

#### Référence

			void StartJob(string name, Dictionary<object, object> extras = null)

Vous pouvez utiliser une tâche pour faire le suivi de certaines opérations sur une période donnée.

#### Exemple

			// An upload begins...
			
			// Set the extras
			var extras = new Dictionary<object, object>();
			extras.Add("title", "avatar");
			extras.Add("type", "image");
			
			EngagementAgent.Instance.StartJob("uploadData", extras);

### Terminer une tâche

#### Référence

			void EndJob(string name)

Dès qu'une opération suivie par une tâche est terminée, vous devez appeler la méthode EndJob de cette tâche en fournissant le nom de la tâche.

#### Exemple

			// In the previous section, we started an upload tracking with a job
			// Then, the upload ends
			
			EngagementAgent.Instance.EndJob("uploadData");

##Signalement d'événements

Il existe trois types d'événements :

-   Événements autonomes
-   Événements de session
-   Événements de tâche

### Événements autonomes

#### Référence

			void SendEvent(string name, Dictionary<object, object> extras = null)

Des événements autonomes peuvent se produire en dehors du contexte d'une session.

#### Exemple

			EngagementAgent.Instance.SendEvent("event", extra);

### Événements de session

#### Référence

			void SendSessionEvent(string name, Dictionary<object, object> extras = null)

Les événements de session sont généralement utilisés pour signaler les actions effectuées par un utilisateur au cours de sa session.

#### Exemple

**Sans données :**

			EngagementAgent.Instance.SendSessionEvent("sessionEvent");
			
			// or
			
			EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**With data :**

			Dictionary<object, object> extras = new Dictionary<object,object>();
			extras.Add("name", "data");
			EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### Événements de tâche

#### Référence

			void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

Les événements de tâche sont généralement utilisés pour signaler les actions effectuées par un utilisateur au cours d'une tâche.

#### Exemple

			EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

##Signalement d'erreurs

Il existe trois types d'erreurs :

-   Erreurs autonomes
-   Erreurs de session
-   Erreurs de tâche

### Erreurs autonomes

#### Référence

			void SendError(string name, Dictionary<object, object> extras = null)

Contrairement aux erreurs de session, les erreurs autonomes peuvent se produire en dehors du contexte d'une session.

#### Exemple

			EngagementAgent.Instance.SendError("errorName", extras);

### Erreurs de session

#### Référence

			void SendSessionError(string name, Dictionary<object, object> extras = null)

Les erreurs de session sont généralement utilisées pour signaler les erreurs qui affectent l'utilisateur au cours de sa session.

#### Exemple

			EngagementAgent.Instance.SendSessionError("errorName", extra);

### Erreurs de tâche

#### Référence

			void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

Les erreurs peuvent être associées à une tâche en cours d'exécution à la place de la session utilisateur active.

#### Exemple

			EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

##Signalement d'incidents

L'agent propose deux méthodes pour gérer les incidents.

### Envoyer une exception

#### Référence

			void SendCrash(Exception e, bool terminateSession = false)

#### Exemple

Vous pouvez à tout moment envoyer une exception en appelant :

			EngagementAgent.Instance.SendCrash(aCatchedException);

Vous pouvez également utiliser un paramètre facultatif pour terminer la session Engagement en même temps que l'envoi de l'incident. Pour cela, appelez :

			EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

Si vous procédez ainsi, la session et les tâches sont fermées juste après l'envoi de l'incident.

### Envoyer une exception non gérée

#### Référence

			void SendCrash(ApplicationUnhandledExceptionEventArgs e)

Engagement offre aussi une méthode pour envoyer les exceptions non gérées. Elle s'avère particulièrement efficace quand vous l'utilisez dans le Gestionnaire d'événements UnhandledException Silverlight.

Une fois appelée, cette méthode termine **TOUJOURS** la session et les tâches Engagement.

#### Exemple

Vous pouvez l'utiliser pour implémenter votre propre gestionnaire UnhandledException (notamment si vous avez désactivé la fonctionnalité de signalement automatique des incidents d'Engagement). Par exemple, dans la méthode `Application_UnhandledException` du fichier `App.xaml.cs` :

			// In your App.xaml.cs file
			
			// Code to execute on Unhandled Exceptions
			private void Application_UnhandledException(object sender, ApplicationUnhandledExceptionEventArgs e)
			{
			  // your own code
			
			  EngagementAgent.Instance.SendCrash(e);
			}

##OnActivated

### Référence

			void OnActivated(ActivatedEventArgs e)

Quand l'utilisateur navigue vers l'avant, en dehors d'une application, le système d'exploitation tente de placer l'application dans un état dormant après le déclenchement de l'événement Deactivated. Ensuite, l'application est désactivée (" Tombstoning "). Au cours de ce processus, l'application est arrêtée, mais certaines données sur l'état de l'application et les pages individuelles au sein de l'application sont conservées.

Vous devez insérer `EngagementAgent.Instance.OnActivated(e)` dans la méthode `Application_Activated` à partir du fichier App.xaml.cs pour réinitialiser l'agent Engagement une fois l'application désactivée.

### Exemple

			// Inside your App.xaml.cs file
			
			// Code to execute when the application is activated (brought to foreground)
			// This code will not execute when the application is first launched
			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			  EngagementAgent.Instance.OnActivated(e);
			}

##ID de l'appareil

			String GetDeviceId()

Vous pouvez obtenir l'ID de l'appareil Engagement en appelant cette méthode.

##Paramètres extras

Vous pouvez attacher des données arbitraires à un événement, une erreur, une activité ou une tâche. Ces données peuvent être structurées à l'aide d'un dictionnaire. Les clés et les valeurs peuvent être de n'importe quel type.

Les données extras sont sérialisées. Par conséquent, pour insérer votre propre type dans extras, vous devez ajouter un contrat de données pour ce type.

### Exemple

Créons une classe " Person ".

			using System.Runtime.Serialization;
			
			namespace Engagement.Agent
			{
			  [DataContract]
			  public class Person
			  {
			    public Person(string name, int age)
			    {
			      Age = age;
			      Name = name;
			    }
			
			    // Properties
			
			    [DataMember]
			    public int Age
			    {
			      get;
			      set;
			    }
			
			    [DataMember]
			    public string Name
			    {
			      get;
			      set; 
			    }
			  }
			}

Ensuite, ajoutons une instance `Person` à un extra.

			Person person = new Person("Engagement Haddock", 51);
			var extras = new Dictionary<object, object>();
			extras.Add("people", person);
			
			EngagementAgent.Instance.SendEvent("Event", extras);

> [AZURE.WARNING] Si vous indiquez d'autres types d'objets, veillez à implémenter la méthode ToString() de chaque objet pour retourner une chaîne explicite.

### Limites

#### Clés

Chaque clé de l'objet doit correspondre à l'expression régulière suivante :

`^[a-zA-Z][a-zA-Z_0-9]*$`

Cela signifie que les clés doivent commencer par au moins une lettre, suivie de lettres, de chiffres ou de traits de soulignement (\_).

#### Taille

Les extras sont limités à **1 024** caractères par appel.

##Signalement d'informations sur l'application

### Référence

			void SendAppInfo(Dictionary<object, object> appInfos)

Vous pouvez signaler manuellement des informations de suivi (ou toute autre information spécifique à l'application) à l'aide de la fonction SendAppInfo().

Notez que ces informations peuvent être envoyées de façon incrémentielle (seule la dernière valeur d'une clé donnée est conservée pour un appareil donné). Comme pour les extras d'événement, utilisez Dictionary\<object, object\> pour attacher des informations.

### Exemple

			Dictionary<object, object> appInfo = new Dictionary<object, object>()
			{
			   {"subscription", "2013-12-07"},
			   {"premium", "true"}
			};
			
			EngagementAgent.Instance.SendAppInfo(appInfo);

### Limites

#### Clés

Chaque clé de l'objet doit correspondre à l'expression régulière suivante :

`^[a-zA-Z][a-zA-Z_0-9]*$`

Cela signifie que les clés doivent commencer par au moins une lettre, suivie de lettres, de chiffres ou de traits de soulignement (\_).

#### Taille

Les informations sur l'application sont limitées à **1 024** caractères par appel.

Dans l'exemple précédent, l'objet JSON envoyé au serveur comprend 44 caractères :

			{"subscription":"2013-12-07","premium":"true"}

<!--HONumber=47-->

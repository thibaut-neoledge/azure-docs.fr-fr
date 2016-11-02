<properties 
	pageTitle="Comment utiliser l'API Engagement sur Windows Universal" 
	description="Comment utiliser l'API Engagement sur Windows Universal"			
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
	ms.date="08/19/2016" 
	ms.author="piyushjo" />

#Comment utiliser l'API Engagement sur Windows Universal

Ce document est un complément du document [Comment intégrer Engagement sur Windows Universal](mobile-engagement-windows-store-integrate-engagement.md) : il explique en détail comment utiliser l'API Engagement pour créer des rapports sur les statistiques de vos applications.

N'oubliez pas que si vous souhaitez seulement qu'Engagement signale les sessions, les activités, les incidents et les informations techniques de votre application, la méthode la plus simple consiste à configurer toutes vos sous-classes `Page` de manière à ce qu’elles héritent de la classe `EngagementPage`.

Si vous souhaitez aller plus loin, par exemple si vous avez besoin de signaler des événements, des erreurs et des travaux spécifiques à l'application, ou si vous devez signaler les activités de votre application d'une manière différente de celle implémentée dans les classes `EngagementPage`, vous devez utiliser l'API Engagement.

L'API Engagement est fournie par la classe `EngagementAgent`. Vous pouvez accéder à ces méthodes par l'intermédiaire de `EngagementAgent.Instance`.

Même si le module de l'agent n'a pas été initialisé, chaque appel à l'API est différé et réexécuté une fois l'agent disponible.

##Concepts liés à Engagement

Les sections qui suivent affinent les [concepts Mobile Engagement](mobile-engagement-concepts.md) courants pour la plateforme Windows Universal.

### `Session` et `Activity`

Une *activité* est généralement associée à une page de l'application, c'est-à-dire que l'*activité* démarre lorsque la page est affichée et s'arrête lorsque la page est fermée : c'est le cas lorsque le Kit de développement logiciel Engagement est intégré à l'aide de la classe `EngagementPage`.

Mais les *activités* peuvent également être contrôlées manuellement à l'aide de l'API Engagement. Cela vous permet de diviser une page donnée en plusieurs sous-parties, afin d'obtenir davantage de détails sur l'utilisation de cette page (par exemple pour connaître la fréquence et la durée de l’utilisation des boîtes de dialogue à l'intérieur de cette page).

##Rapports d'activités

### L'utilisateur démarre une nouvelle activité

#### Référence

			void StartActivity(string name, Dictionary<object, object> extras = null)

Vous devez appeler `StartActivity()` chaque fois que l'activité utilisateur change. Le premier appel à cette fonction démarre une nouvelle session utilisateur.

> [AZURE.IMPORTANT] Le Kit de développement logiciel (SDK) appelle automatiquement la méthode EndActivity lorsque l'application est fermée. Par conséquent, il est FORTEMENT recommandé d'appeler la méthode StartActivity chaque fois que l'activité de l'utilisateur change et de ne JAMAIS appeler la méthode EndActivity, celle-ci forçant la fin de la session active.

#### Exemple

			EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### L'utilisateur met fin à l'activité en cours

#### Référence

			void EndActivity()

Cela met fin à l'activité et à la session. Vous ne devez pas appeler cette méthode, à moins de savoir exactement ce que vous faites.

#### Exemple

			EngagementAgent.Instance.EndActivity();

##Rapports de travaux

### Démarrer un travail

#### Référence

			void StartJob(string name, Dictionary<object, object> extras = null)

Vous pouvez utiliser le travail pour effectuer le suivi de certaines tâches sur une période donnée.

#### Exemple

			// An upload begins...
			
			// Set the extras
			var extras = new Dictionary<object, object>();
			extras.Add("title", "avatar");
			extras.Add("type", "image");
			
			EngagementAgent.Instance.StartJob("uploadData", extras);

### Mettre fin à un travail

#### Référence

			void EndJob(string name)

Dès qu'une tâche suivie par un travail est terminée, vous devez appeler la méthode EndJob pour ce travail, en fournissant le nom du travail.

#### Exemple

			// In the previous section, we started an upload tracking with a job
			// Then, the upload ends
			
			EngagementAgent.Instance.EndJob("uploadData");

##Rapports d'événements

Il existe trois types d'événements :

-   Événements autonomes
-   Événements de session
-   Événements de travail

### Événements autonomes

#### du SDK

			void SendEvent(string name, Dictionary<object, object> extras = null)

Les événements autonomes peuvent se produire en dehors du contexte d'une session.

#### Exemple

			EngagementAgent.Instance.SendEvent("event", extra);

### Événements de session

#### Référence

			void SendSessionEvent(string name, Dictionary<object, object> extras = null)

Les événements de session servent généralement à signaler les actions effectuées par un utilisateur lors de sa session.

#### Exemple

**Sans données :**

			EngagementAgent.Instance.SendSessionEvent("sessionEvent");
			
			// or
			
			EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**Avec données :**

			Dictionary<object, object> extras = new Dictionary<object,object>();
			extras.Add("name", "data");
			EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### Événements de travail

#### Référence

			void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

Les événements de travail servent généralement à signaler les actions effectuées par un utilisateur lors d'un travail.

#### Exemple

			EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

##Rapports d'erreurs

Il existe trois types d’erreurs :

-   Erreurs autonomes
-   Erreurs de session
-   Erreurs de travail

### Erreurs autonomes

#### Référence

			void SendError(string name, Dictionary<object, object> extras = null)

Contrairement aux erreurs de session, les erreurs autonomes peuvent se produire en dehors du contexte d'une session.

#### Exemple

			EngagementAgent.Instance.SendError("errorName", extras);

### Erreurs de session

#### Référence

			void SendSessionError(string name, Dictionary<object, object> extras = null)

Les erreurs de session servent généralement à signaler les erreurs affectant l'utilisateur lors de sa session.

#### Exemple

			EngagementAgent.Instance.SendSessionError("errorName", extra);

### Erreurs de travail

#### Référence

			void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

Les erreurs peuvent être associées à un travail en cours d'exécution plutôt qu'à la session utilisateur en cours.

#### Exemple

			EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

##Rapports d'incidents

L'agent fournit deux méthodes pour gérer les incidents.

### Envoyer une exception

#### Référence

			void SendCrash(Exception e, bool terminateSession = false)

#### Exemple

Vous pouvez envoyer une exception à tout moment en appelant :

			EngagementAgent.Instance.SendCrash(aCatchedException);

Vous pouvez également utiliser un paramètre facultatif pour mettre fin à la session Engagement en même temps que l'envoi de l'incident. Pour ce faire, appelez :

			EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

Si vous procédez ainsi, la session et les travaux sont fermés juste après l'envoi de l'incident.

### Envoyer une exception non gérée

#### Référence

			void SendCrash(Exception e)

Engagement fournit également une méthode pour envoyer des exceptions non gérées si vous avez **DÉSACTIVÉ** le signalement automatique d'**incident** Engagement. Ceci est particulièrement utile en cas d'utilisation dans le Gestionnaire d'événements UnhandledException de l'application.

Cette méthode met **TOUJOURS** fin aux travaux et à la session Engagement après avoir été appelée.

#### Exemple

Vous pouvez l'utiliser pour implémenter votre propre gestionnaire UnhandledExceptionEventArgs. Par exemple, ajoutez la méthode `Current_UnhandledException` du fichier `App.xaml.cs` :

			// In your App.xaml.cs file
			
			// Code to execute on Unhandled Exceptions
			void Current_UnhandledException(object sender, UnhandledExceptionEventArgs e)
			{
			   EngagementAgent.Instance.SendCrash(e.Exception,false);
			}

Dans App.xaml.cs dans « Public App(){} », ajoutez :

			Application.Current.UnhandledException += Current_UnhandledException;

##ID de périphérique

			String EngagementAgent.Instance.GetDeviceId()

Vous pouvez obtenir l'ID de périphérique Engagement en appelant cette méthode.

##Paramètres de suppléments

Des données arbitraires peuvent être associées à un événement, à une erreur, à une activité ou à un travail. Ces données peuvent être structurées à l'aide d'un dictionnaire. Les clés et les valeurs peuvent être de n'importe quel type.

Les données de suppléments sont sérialisées ; par conséquent, si vous souhaitez insérer votre propre type dans des suppléments, vous devez ajouter un contrat de données pour ce type.

### Exemple

Nous créons une classe nommée « Person ».

			using System.Runtime.Serialization;
			
			namespace Microsoft.Azure.Engagement
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

Ensuite, nous ajoutons une instance `Person` à un supplément.

			Person person = new Person("Engagement Haddock", 51);
			var extras = new Dictionary<object, object>();
			extras.Add("people", person);
			
			EngagementAgent.Instance.SendEvent("Event", extras);

> [AZURE.WARNING] Si vous placez d'autres types d'objets, assurez-vous que leur méthode ToString() est implémentée pour retourner une chaîne explicite.

### Limites

#### de clés symétriques

Chaque clé de l'objet doit correspondre à l'expression régulière suivante :

`^[a-zA-Z][a-zA-Z_0-9]*$`

Cela signifie que les clés doivent commencer par au moins une lettre, suivie de lettres, de chiffres ou de traits de soulignement (\_).

#### Taille

Les suppléments sont limités à **1 024** caractères par appel.

##Rapports d'informations sur l'application

### Référence

			void SendAppInfo(Dictionary<object, object> appInfos)

Vous pouvez signaler manuellement les informations de suivi (ou toute autre information spécifique à l'application) à l'aide de la fonction SendAppInfo().

Notez que ces données peuvent être envoyées de façon incrémentielle : seule la dernière valeur d'une clé donnée sera conservée pour un périphérique donné. Comme pour les suppléments d'événements, utilisez un Dictionary<object, object> pour joindre des données.

### Exemple

			Dictionary<object, object> appInfo = new Dictionary<object, object>()
			  {
			    {"birthdate", "1983-12-07"},
			    {"gender", "female"}
			  };
			
			EngagementAgent.Instance.SendAppInfo(appInfo);

### Limites

#### de clés symétriques

Chaque clé de l'objet doit correspondre à l'expression régulière suivante :

`^[a-zA-Z][a-zA-Z_0-9]*$`

Cela signifie que les clés doivent commencer par au moins une lettre, suivie de lettres, de chiffres ou de traits de soulignement (\_).

#### Taille

Les informations de l'application sont limitées à **1 024** caractères par appel.

Dans l'exemple précédent, le JSON envoyé au serveur fait 44 caractères :

			{"birthdate":"1983-12-07","gender":"female"}

##Journalisation
###Activation de la journalisation

Le Kit de développement logiciel (SDK) peut être configuré pour générer des journaux de tests dans la console IDE. Ces journaux ne sont pas activés par défaut. Pour personnaliser ce résultat, mettez à jour la propriété `EngagementAgent.Instance.TestLogEnabled` avec une des valeurs disponibles à partir de l'énumération `EngagementTestLogLevel`, par exemple :

			EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
			EngagementAgent.Instance.Init();
 

<!---HONumber=AcomDC_0824_2016-->
<properties 
	pageTitle="Présentation du Kit de développement logiciel (SDK) Azure WebJobs" 
	description="Présentation du Kit de développement logiciel WebJobs Azure Explique la nature du Kit de développement logiciel (SDK), les scénarios typiques pour lesquels il est utile et fournit des exemples de code." 
	services="app-service\web, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="tdykstra"/>

# Présentation du Kit de développement logiciel (SDK) Azure WebJobs

## <a id="overview"></a>Vue d’ensemble

Cet article explique la nature du Kit de développement logiciel (SDK) WebJobs, examine quelques scénarios courants pour lesquels il est utile, et présente son utilisation dans votre code.

[WebJobs](websites-webjobs-resources.md) est une fonctionnalité de Microsoft Azure App Service qui vous permet d’exécuter un programme ou un script dans un même contexte, en tant qu’application web. La fonction du Kit de développement logiciel (SDK) WebJobs est de simplifier l’écriture de code qui s’exécute en tant que tâche web et fonctionne avec des files d’attente, des objets blob et des tables Azure Storage, ainsi que des files d’attente Service Bus.

Ce Kit comprend les composants suivants :

* **Packages NuGet**. Les packages NuGet que vous ajoutez à un projet d'application console Visual Studio fournissent une infrastructure que votre code utilise pour fonctionner avec le service Azure Storage ou des files d'attente Service Bus.   
  
* **Tableau de bord**. Cette partie du Kit de développement logiciel (SDK) WebJobs est incluse dans Microsoft Azure App Service ; elle offre des fonctions évoluées de surveillance et de diagnostic pour les programmes qui utilisent les packages NuGet. Vous n’avez pas à écrire du code pour utiliser ces fonctions.

## <a id="scenarios"></a>Scénarios

Voici quelques scénarios typiques plus faciles à gérer avec le Kit de développement logiciel (SDK) Azure WebJobs :

* Traitement d'images ou autres tâches imposant une forte charge au processeur. Une caractéristique courante des sites web est le téléchargement d'images ou de vidéos. Vous voulez souvent manipuler le contenu après le téléchargement, mais vous ne voulez pas faire attendre l'utilisateur pendant ce temps.

* Traitement de files d'attente. Un serveur web frontal utilise souvent des files d'attente pour communiquer avec un service principal. Lorsque le site web doit effectuer un travail, il envoie un message à une file d'attente. Un service principal extrait les messages de la file d’attente et effectue le travail. Vous pouvez utiliser les files d’attente pour le traitement d’image. Par exemple, une fois que l’utilisateur a chargé un certain nombre de fichiers, placez les noms de fichiers dans un message en file d’attente, afin qu’ils soient récupérés par le serveur principal à des fins de traitement. Vous pouvez également utiliser des files d’attente pour améliorer la réactivité d’un site. Par exemple, au lieu d'écrire directement dans une base de données SQL, écrivez dans une file d'attente, indiquez à l'utilisateur que vous avez terminé et laissez le serveur principal gérer le travail sur les bases de données relationnelles ayant une latence élevée. Pour consulter un exemple de traitement de file d'attente avec le traitement d'images, consultez le [didacticiel Prise en mains du Kit de développement logiciel (SDK) WebJobs](websites-dotnet-webjobs-sdk-get-started.md).

* Agrégation RSS. Si vous avez un site qui maintient une liste de flux RSS, vous pouvez récupérer tous les articles des flux dans un traitement en arrière-plan.

* Maintenance de fichiers (par exemple, agrégation ou nettoyage de fichiers journaux). Vous voudrez peut-être que des fichiers journaux soient créés par plusieurs sites ou pour des périodes données que vous voulez associer pour les soumettre à des tâches d'analyse. Vous voudrez peut-être aussi planifier l'exécution hebdomadaire d'une tâche de nettoyage des fichiers journaux.

* Entrée dans des tables Azure. Vous aurez peut-être des fichiers et des objets blob stockés que vous voudrez analyser pour enregistrer les données dans des tables. La fonction d'entrée peut consister à écrire de nombreuses lignes (des millions dans certains cas) : le Kit de développement logiciel (SDK) WebJobs permet d'implémenter facilement cette fonctionnalité. Il permet également la surveillance en temps réel d'indicateurs de progression tels que le nombre de lignes écrites dans la table.

* Autres tâches longues que vous voulez exécuter dans un thread en arrière-plan (par exemple, [envoi de courriers électroniques](https://github.com/victorhurdugaci/AzureWebJobsSamples/tree/master/SendEmailOnFailure)).

## <a id="code"></a> Exemples de code

Le code de traitement des tâches typiques fonctionnant avec Azure Storage est simple. Dans une application console, vous écrivez des méthodes pour les tâches en arrière-plan que vous voulez exécuter et vous les décorez avec des attributs du Kit de développement logiciel (SDK) WebJobs. Votre méthode `Main` crée un objet `JobHost`, qui coordonne les appels aux méthodes que vous écrivez. L’infrastructure du Kit de développement logiciel (SDK) WebJobs sait quand appeler vos méthodes d’après les attributs associés que vous utilisez dans ces méthodes.

Voici un programme simple qui interroge une file d’attente et crée un objet blob pour chaque message de file d’attente reçu :

		public static void Main()
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}

		public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)
		{
		    writer.WriteLine(inputText);
		}

L’objet `JobHost` est un conteneur prévu pour un ensemble de fonctions en arrière-plan. L’objet `JobHost` surveille ces fonctions, recherche les événements qui les déclenchent et exécute les fonctions lorsque des événements de déclenchement se produisent. Vous appelez une méthode `JobHost` pour indiquer si vous voulez que le processus du conteneur s’exécute sur le thread actif ou dans un thread en arrière-plan. Dans cet exemple, la méthode `RunAndBlock` exécute le processus en permanence sur le thread actif.

Comme la méthode `ProcessQueueMessage` de cet exemple inclut un attribut `QueueTrigger`, le déclencheur de cette fonction est la réception d’un nouveau message en file d’attente. L’objet `JobHost` recherche les nouveaux messages dans la file d’attente spécifiée (« webjobsqueue », dans cet exemple). Lorsqu’il trouve un message, il appelle `ProcessQueueMessage`. L’attribut `QueueTrigger` indique également à l’infrastructure de lier le paramètre `inputText` à la valeur du message en file d’attente :

<pre class="prettyprint">public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")]] <mark>string inputText</mark>, 
    [Blob("containername/blobname")]TextWriter writer)</pre>

L’infrastructure lie également un objet `TextWriter` à un objet blob nommé « blobname » dans le conteneur « containername » :

<pre class="prettyprint">public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")]] string inputText, 
    <mark>[Blob("containername/blobname")]TextWriter writer</mark>)</pre>

La fonction utilise ensuite ces paramètres pour écrire la valeur du message en file d'attente dans l'objet blob :

		writer.WriteLine(inputText);

Les fonctionnalités de déclencheur et de classeur du Kit de développement logiciel (SDK) WebJobs simplifient considérablement le code que vous devez écrire avec des files d'attente Azure Storage et Service Bus. L'infrastructure du SDK écrit pour vous le code de bas niveau nécessaire à la gestion des files d'attente et au traitement des objets blob. Elle crée des files d'attente qui n'existent pas encore, ouvre une file d'attente, lit les messages en attente, supprime les messages en attente une fois qu'ils ont été traités, crée des conteneurs d'objets blob qui n'existent pas encore, écrit dans les objets blob, etc.

Le Kit de développement logiciel (SDK) WebJobs offre différentes manières d’utiliser Azure Storage. Par exemple, si le paramètre auquel vous associez l’attribut `QueueTrigger` est un tableau d’octets ou un type personnalisé, il est automatiquement désérialisé à partir de JSON. De plus, vous pouvez utiliser un attribut `BlobTrigger` pour déclencher un processus lorsqu’un nouvel objet blob est créé dans un compte Azure Storage. (Remarque : bien que l’élément `QueueTrigger` recherche les nouveaux messages de file d’attente en quelques secondes, `BlobTrigger` peut prendre jusqu’à 20 minutes pour détecter nouvel objet blob. `BlobTrigger` recherche les objets blob chaque fois que `JobHost` démarre, vérifiant ensuite périodiquement les journaux Azure Storage pour détecter de nouveaux objets blob.)

## <a id="workerrole"></a>Utilisation du Kit de développement logiciel (SDK) WebJobs en dehors de WebJobs

Un programme qui utilise le Kit de développement logiciel (SDK) WebJobs est une application console standard qui peut s’exécuter n’importe où, et pas nécessairement en tant que tâche web. Vous pouvez tester ce programme localement sur votre ordinateur de développement ; en production, vous pouvez l’exécuter dans un rôle de travail de service cloud ou un service Windows si vous préférez un de ces environnements.

Toutefois, le tableau de bord est uniquement disponible en tant qu’extension d’une application web Microsoft Azure App Service. Si vous voulez que l’exécution ait lieu en dehors de WebJobs, mais souhaitez pouvoir utiliser le tableau de bord, vous pouvez configurer une application web de manière à utiliser le compte de stockage auquel la chaîne de connexion du tableau de bord du Kit de développement logiciel (SDK) WebJobs fait référence ; le tableau de bord WebJobs de cette application affiche alors les données concernant l’exécution des fonctions de votre programme, qui s’exécute ailleurs. Vous pouvez accéder au tableau de bord via l’URL https://*{webappname}*.scm.azurewebsites.net/azurejobs/#/functions. Pour plus d’informations, consultez le billet de blog [Récupération d’un tableau de bord pour un développement local avec le Kit de développement logiciel (SDK) WebJobs](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx) (notez cependant que le billet de blog affiche un ancien nom de chaîne de connexion).

## <a id="nostorage"></a>Utilisation du Kit de développement logiciel (SDK) WebJobs pour l’appel d’une fonction

Le Kit de développement logiciel (SDK) WebJobs offre plusieurs avantages même si vous ne devez pas utiliser directement des files d’attente, des objets blob et des tables Azure Storage, et des files d’attente Service Bus :

* Vous pouvez appeler des fonctions depuis le tableau de bord.
* Vous pouvez réexécuter des fonctions depuis le tableau de bord.
* Vous pouvez afficher les journaux dans le tableau de bord, liés à une tâche web spécifique (journaux d’application, écrits via les éléments Console.Out, Console.Error, Trace, etc.) ou liés à l’appel de fonction spécifique à l’origine de leur génération (journaux écrits via un objet `TextWriter` que le Kit de développement logiciel (SDK) passe à la fonction, en tant que paramètre). 

* Pour en savoir plus, consultez les pages [Appel manuel d’une fonction](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual) et [Écriture de journaux](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)

## <a id="nextsteps"></a>Étapes suivantes

Pour en savoir plus sur le Kit de développement logiciel (SDK) WebJobs, consultez la rubrique [Tâches web Azure - Ressources recommandées](http://go.microsoft.com/fwlink/?linkid=390226).
 

<!---HONumber=July15_HO5-->
<properties pageTitle="Présentation du Kit de développement logiciel (SDK) WebJobs Azure" metaKeywords="Azure Azure WebJobs SDK, Azure storage, Azure queues, Azure tables" description="An introduction to the Azure WebJobs SDK. Explains what the SDK is, typical scenarios it is useful for, and code samples." metaCanonical="" services="web-sites,storage" documentationCenter=".NET" title="What is the Azure WebJobs SDK" authors="tdykstra" solutions="" manager="wpickett" editor="jimbe" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/12/2014" ms.author="tdykstra" />

# Présentation du Kit de développement logiciel (SDK) WebJobs Azure

Cet article explique la nature du Kit de développement logiciel (SDK) WebJobs, examine quelques scénarios courants pour lesquels il est utile, et présente son utilisation dans votre code.

## Sommaire

- [Vue d'ensemble](#overview)
- [Scénarios](#scenarios)
- [Exemples de code](#code)
- [Utilisation du Kit de développement logiciel (SDK) WebJobs en dehors de WebJobs](#workerrole)
- [Utilisation du Kit de développement logiciel (SDK) WebJobs pour appeler une fonction](#nostorage)
- [Étapes suivantes](#nextsteps)

## <a id="overview"></a>Vue d'ensemble

[WebJobs](/fr-fr/documentation/articles/web-sites-create-web-jobs/) constitue une fonctionnalité des sites web Azure qui permet d'exécuter un programme ou un script dans le même contexte qu'un site web. La fonction du Kit de développement logiciel (SDK) WebJobs est de simplifier l'écriture de code qui s'exécute en tant que tâche web et fonctionne avec des files d'attente, des objets blob et des tables Azure Storage, ainsi que des files d'attente Service Bus.

Ce Kit comprend les composants suivants :

* **Packages NuGet**. Les packages NuGet que vous ajoutez à un projet d'application console Visual Studio fournissent une infrastructure que votre code utilise pour fonctionner avec le service Azure Storage ou des files d'attente Service Bus.   
  
* **Tableau de bord**. Cette partie du Kit de développement logiciel (SDK) WebJobs est incluse dans les sites web Azure ; elle offre des fonctions évoluées de surveillance et de diagnostic pour les programmes qui utilisent les packages NuGet. Vous n'avez pas à écrire du code pour utiliser ces fonctions.

## <a id="scenarios"></a>Scénarios

Voici quelques scénarios typiques plus faciles à gérer avec le Kit de développement logiciel (SDK) Azure WebJobs :

* Traitement d'images ou autres tâches imposant une forte charge au processeur. Une caractéristique courante des sites web est le téléchargement d'images ou de vidéos. Vous voulez souvent manipuler le contenu après le téléchargement, mais vous ne voulez pas faire attendre l'utilisateur pendant ce temps.

* Traitement de files d'attente. Un serveur web frontal utilise souvent des files d'attente pour communiquer avec un service principal. Lorsque le site web doit effectuer un travail, il envoie un message à une file d'attente. Un service principal extrait les messages de la file d'attente et effectue le travail. Vous pouvez utiliser des files d'attente pour le traitement d'images : par exemple, lorsque l'utilisateur a téléchargé divers fichiers, placez leurs noms dans un message de file d'attente pour que le serveur principal les traite. Vous pouvez également utiliser des files d'attente pour améliorer la réactivité d'un site. Par exemple, au lieu d'écrire directement dans une base de données SQL, écrivez dans une file d'attente, indiquez à l'utilisateur que vous avez terminé et laissez le serveur principal gérer le travail sur les bases de données relationnelles ayant une latence élevée. Pour consulter un exemple de traitement de file d'attente avec le traitement d'images, consultez le [didacticiel Prise en main du Kit de développement logiciel (SDK) WebJobs](../websites-dotnet-webjobs-sdk-get-started/).

* Agrégation RSS. Si vous avez un site qui maintient une liste de flux RSS, vous pouvez récupérer tous les articles des flux dans un traitement en arrière-plan.

* Maintenance de fichiers (par exemple, agrégation ou nettoyage de fichiers journaux).  Vous voudrez peut-être que des fichiers journaux soient créés par plusieurs sites ou pour des périodes données que vous voulez associer pour les soumettre à des tâches d'analyse. Vous voudrez peut-être aussi planifier l'exécution hebdomadaire d'une tâche de nettoyage des fichiers journaux.

* Entrée dans des tables Azure. Vous aurez peut-être des fichiers et des objets blob stockés que vous voudrez analyser pour enregistrer les données dans des tables. La fonction d'entrée peut consister à écrire de nombreuses lignes (des millions dans certains cas) : le Kit de développement logiciel (SDK) WebJobs permet d'implémenter facilement cette fonctionnalité. Il permet également la surveillance en temps réel d'indicateurs de progression tels que le nombre de lignes écrites dans la table.

* Autres tâches longues que vous voulez exécuter dans un thread en arrière-plan (par exemple, [envoi de courriers électroniques](https://github.com/victorhurdugaci/AzureWebJobsSamples/tree/master/SendEmailOnFailure)). 

## <a id="code"></a> Exemples de code

Le code de traitement des tâches typiques fonctionnant avec Azure Storage est simple. Dans une application console, vous écrivez des méthodes pour les tâches en arrière-plan que vous voulez exécuter et vous les décorez avec des attributs du Kit de développement logiciel (SDK) WebJobs. Votre méthode `Main` crée un objet `JobHost` qui coordonne les appels aux méthodes que vous écrivez. L'infrastructure du SDK WebJobs sait quand appeler vos méthodes d'après les attributs associés que vous utilisez dans ces méthodes. 

Voici un programme simple qui interroge une file d'attente et crée un objet blob pour chaque message de file d'attente reçu :

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

L'objet `JobHost` est un conteneur d'un ensemble de fonctions en arrière-plan. L'objet `JobHost` surveille ces fonctions, recherche les événements qui les déclenchent et exécute les fonctions lorsque des événements de déclenchement se produisent. Vous appelez une méthode `JobHost` pour indiquer si vous voulez que le processus du conteneur s'exécute sur le thread actif ou dans un thread en arrière-plan. Dans l'exemple, la méthode `RunAndBlock` exécute le processus en permanence sur le thread actif.

Dans cet exemple, du fait que la méthode `ProcessQueueMessage` comporte l'attribut `QueueTrigger`, le déclencheur de cette fonction est la réception d'un nouveau message en file d'attente. L'objet `JobHost` recherche les nouveaux messages dans la file d'attente spécifiée (" webjobsqueue " dans cet exemple) ; lorsqu'un message est trouvé, il appelle `ProcessQueueMessage`. L'attribut `QueueTrigger` indique également à l'infrastructure de lier le paramètre `inputText` à la valeur du message en file d'attente : 

<pre class="prettyprint">public static void ProcessQueueMessage([QueueTrigger(&quot;webjobsqueue&quot;)]] <mark>string inputText</mark>, 
    [Blob("containername/blobname")]TextWriter writer)</pre>

L'infrastructure lie également l'objet `TextWriter` à un objet blob nommé " blobname " dans un conteneur nommé " containername " :

<pre class="prettyprint">public static void ProcessQueueMessage([QueueTrigger(&quot;webjobsqueue&quot;)]] string inputText, 
    <mark>[Blob("containername/blobname")]TextWriter writer</mark>)</pre>

La fonction utilise ensuite ces paramètres pour écrire la valeur du message en file d'attente dans l'objet blob :

		writer.WriteLine(inputText);

Les fonctionnalités de déclencheur et de classeur du Kit de développement logiciel (SDK) WebJobs simplifient considérablement le code que vous devez écrire avec des files d'attente Azure Storage et Service Bus. L'infrastructure du SDK écrit pour vous le code de bas niveau nécessaire à la gestion des files d'attente et au traitement des objets blob. Elle crée des files d'attente qui n'existent pas encore, ouvre une file d'attente, lit les messages en attente, supprime les messages en attente une fois qu'ils ont été traités, crée des conteneurs d'objets blob qui n'existent pas encore, écrit dans les objets blob, etc.

Le SDK WebJobs offre différentes manières d'utiliser   Azure Storage. Par exemple, si le paramètre que vous décorez avec l'attribut `QueueTrigger` est un tableau d'octets ou un type personnalisé, il est automatiquement désérialisé à partir de JSON. De plus, vous pouvez utiliser l'attribut `BlobTrigger` pour déclencher un processus lorsqu'un nouvel objet blob est créé dans un compte Azure Storage. (Notez que si l'attribut `QueueTrigger` trouve les nouveaux messages en file d'attente en quelques secondes, l'attribut `BlobTrigger` en revanche peut nécessiter jusqu'à 20 minutes pour détecter un nouvel objet blob. L'attribut `BlobTrigger` recherche des objets blob au démarrage de `JobHost`, puis vérifie régulièrement les journaux Azure Storage à la recherche de nouveaux objets blob.)

## <a id="workerrole"></a>Utilisation du Kit de développement logiciel (SDK) WebJobs en dehors de WebJobs

Un programme qui utilise le Kit de développement logiciel (SDK) WebJobs est une application console standard qui peut s'exécuter n'importe où, et pas nécessairement en tant que tâche web. Vous pouvez tester ce programme localement sur votre ordinateur de développement ; en production, vous pouvez l'exécuter dans un rôle de travail de service cloud ou un service Windows si vous préférez un de ces environnements. 

Cependant, le tableau de bord est disponible uniquement comme extension d'un site web Azure. Si vous voulez que l'exécution ait lieu en dehors d'une tâche web et toujours utiliser le tableau de bord, vous pouvez configurer un site web Azure pour utiliser le même compte de stockage auquel la chaîne de connexion du tableau de bord du Kit de développement logiciel (SDK) WebJobs fait référence ; le tableau de bord WebJobs de ce site affiche alors les données concernant l'exécution des fonctions de votre programme qui s'exécute ailleurs. Vous pouvez accéder au tableau de bord en utilisant l'URL https://*{websitename}*.scm.azurewebsites.net/azurejobs/#/functions. Pour plus d'informations, consultez le billet de blog [Récupération d'un tableau de bord pour un développement local avec le Kit de développement logiciel (SDK) WebJobs](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx) (notez cependant que le billet de blog affiche un ancien nom de chaîne de connexion). 

## <a id="nostorage"></a>Utilisation du Kit de développement logiciel (SDK) WebJobs pour appeler une fonction

Le Kit de développement logiciel (SDK) WebJobs offre plusieurs avantages même si vous ne devez pas utiliser directement des files d'attente, des objets blob et des tables Azure Storage, et des files d'attente Service Bus :

* Vous pouvez appeler des fonctions depuis le tableau de bord.
* Vous pouvez réexécuter des fonctions depuis le tableau de bord.
* Vous pouvez afficher des journaux dans le tableau de bord, liés à une tâche web donnée (journaux d'application) ou liés à l'appel d'une fonction particulière qui les a créés (journaux de paramètres `TextWriter`). 

* Pour en savoir plus, consultez les pages [Appel manuel d'une fonction](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#manual) et [Écriture de journaux](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#logs) 

## <a id="nextsteps"></a>Étapes suivantes

Pour en savoir plus sur le Kit de développement logiciel (SDK) WebJobs, consultez la rubrique [Tâches web Azure - Ressources recommandées](http://go.microsoft.com/fwlink/?linkid=390226).

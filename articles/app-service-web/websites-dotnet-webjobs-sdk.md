---
title: "Présentation du Kit de développement logiciel (SDK) Azure WebJobs"
description: "Présentation du Kit de développement logiciel WebJobs Azure Explique la nature du Kit de développement logiciel (SDK), les scénarios typiques pour lesquels il est utile et fournit des exemples de code."
services: app-service\web, storage
documentationcenter: .net
author: tdykstra
manager: wpickett
editor: jimbe
ms.assetid: 8281267b-572b-4b14-a328-6704493ea682
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2016
ms.author: tdykstra
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2d21cd34427921ad789b4c95212c07caddd5a966


---
# <a name="what-is-the-azure-webjobs-sdk"></a>Présentation du Kit de développement logiciel (SDK) Azure WebJobs
## <a name="a-idoverviewaoverview"></a><a id="overview"></a>Vue d’ensemble
Cet article explique la nature du Kit de développement logiciel (SDK) WebJobs, examine quelques scénarios courants pour lesquels il est utile, et présente son utilisation dans votre code.

[WebJobs](websites-webjobs-resources.md) est une fonctionnalité de Microsoft Azure App Service qui vous permet d’exécuter un programme ou un script dans un même contexte, en tant qu’application web, application API ou application mobile. L’objectif du [kit de développement logiciel (SDK) WebJobs](websites-webjobs-resources.md) consiste à simplifier le code que vous écrivez pour les tâches web courantes, telles que le traitement d’image, le traitement de la file d’attente, l’agrégation RSS, la maintenance des fichiers et l’envoi des messages électroniques. Le kit de développement logiciel (SDK) WebJobs dispose de fonctionnalités intégrées fonctionnant avec le stockage Azure et Service Bus et servant à planifier des tâches, à gérer des erreurs et à nombreux autres scénarios courants. En outre, il est conçu pour être extensible. Le [SDK WebJobs est open source](https://github.com/Azure/azure-webjobs-sdk/) et il existe un [référentiel open source pour les extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview).

Ce Kit comprend les composants suivants :

* **Packages NuGet**. Les packages NuGet que vous ajoutez à un projet d'application console Visual Studio fournissent une infrastructure que votre code utilise en décorant vos méthodes avec les attributs SDK de WebJobs.
* **Tableau de bord**. Cette partie du Kit de développement logiciel (SDK) WebJobs est incluse dans Microsoft Azure App Service ; elle offre des fonctions évoluées de surveillance et de diagnostic pour les programmes qui utilisent les packages NuGet. Vous n’avez pas à écrire du code pour utiliser ces fonctions.

## <a name="a-idscenariosascenarios"></a><a id="scenarios"></a>Scénarios
Voici quelques scénarios typiques plus faciles à gérer avec le Kit de développement logiciel (SDK) Azure WebJobs :

* Traitement d'images ou autres tâches imposant une forte charge au processeur. Une caractéristique courante des sites web est le téléchargement d'images ou de vidéos. Vous voulez souvent manipuler le contenu après le téléchargement, mais vous ne voulez pas faire attendre l'utilisateur pendant ce temps.
* Traitement de files d'attente. Un serveur web frontal utilise souvent des files d'attente pour communiquer avec un service principal. Lorsque le site web doit effectuer un travail, il envoie un message à une file d'attente. Un service principal extrait les messages de la file d’attente et effectue le travail. Vous pouvez utiliser les files d’attente pour le traitement d’image. Par exemple, une fois que l’utilisateur a chargé un certain nombre de fichiers, placez les noms de fichiers dans un message en file d’attente, afin qu’ils soient récupérés par le serveur principal à des fins de traitement. Vous pouvez également utiliser des files d’attente pour améliorer la réactivité d’un site. Par exemple, au lieu d'écrire directement dans une base de données SQL, écrivez dans une file d'attente, indiquez à l'utilisateur que vous avez terminé et laissez le serveur principal gérer le travail sur les bases de données relationnelles ayant une latence élevée. Pour consulter un exemple de traitement de file d’attente avec le traitement d’images, consultez le [didacticiel Prise en mains du Kit de développement logiciel (SDK) WebJobs](websites-dotnet-webjobs-sdk-get-started.md).
* Agrégation RSS. Si vous avez un site qui maintient une liste de flux RSS, vous pouvez récupérer tous les articles des flux dans un traitement en arrière-plan.
* Maintenance de fichiers (par exemple, agrégation ou nettoyage de fichiers journaux).  Vous voudrez peut-être que des fichiers journaux soient créés par plusieurs sites ou pour des périodes données que vous voulez associer pour les soumettre à des tâches d'analyse. Vous voudrez peut-être aussi planifier l'exécution hebdomadaire d'une tâche de nettoyage des fichiers journaux.
* Entrée dans des tables Azure. Vous aurez peut-être des fichiers et des objets blob stockés que vous voudrez analyser pour enregistrer les données dans des tables. La fonction d'entrée peut consister à écrire de nombreuses lignes (des millions dans certains cas) : le Kit de développement logiciel (SDK) WebJobs permet d'implémenter facilement cette fonctionnalité. Il permet également la surveillance en temps réel d'indicateurs de progression tels que le nombre de lignes écrites dans la table.
* Autres tâches longues que vous voulez exécuter dans un thread en arrière-plan (par exemple, [envoi de courriers électroniques](https://github.com/victorhurdugaci/AzureWebJobsSamples/tree/master/SendEmailOnFailure)). 
* Toutes les tâches que vous souhaitez exécuter sur un planning, par exemple une opération de sauvegarde exécutée toutes les nuits.

Dans un grand nombre de ces scénarios, vous souhaiterez mettre à l'échelle une application web de sorte à ce qu’elle puisse être exécutée sur plusieurs machines virtuelles, qui exécuteraient plusieurs WebJobs simultanément. Dans certains scénarios, cela pourrait entraîner le traitement multiple de mêmes données, mais ceci n'est pas un problème lorsque vous utilisez la file d'attente intégrée, blob et les déclencheurs de bus de service du Kit de développement logiciel (SDK) de WebJobs. Le Kit de développement logiciel (SDK) garantit que vos fonctions ne seront traitées qu'une seule fois pour chaque message ou objet blob.

Le SDK WebJobs facilite également la gestion des scénarios de traitement des erreurs courantes. Vous pouvez définir des alertes pour envoyer des notifications lorsqu’une fonction échoue, et vous pouvez définir des délais d'attente afin qu’une fonction soit automatiquement annulée si elle ne se termine pas dans un délai spécifié.

## <a name="a-idcodea-code-samples"></a><a id="code"></a> Exemples de code
Le code de traitement des tâches typiques fonctionnant avec Azure Storage est simple. Dans votre méthode `Main` de Console Application, vous créez un objet `JobHost` qui coordonne les appels aux méthodes que vous écrivez. L’infrastructure du Kit de développement logiciel (SDK) WebJobs sait quand appeler vos méthodes et quels valeurs de paramètre utiliser d’après les attributs associés que vous utilisez dans ces méthodes. Le SDK fournit des *déclencheurs* qui spécifient les conditions déclenchant l’appel de la fonction, et des *binders* qui spécifient comment obtenir des informations vers et depuis des paramètres de méthode.

Par exemple, l’attribut [QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md) déclenche l’appel d’une fonction lorsqu’une file d’attente reçoit un message ; si le message est enregistré au format JSON pour un tableau d’octets ou un type personnalisé, il est automatiquement désérialisé. L’attribut [BlobTrigger](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md) déclenche un processus lorsqu'un nouvel objet blob est créé dans un compte Azure Storage.

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

Comme la méthode `ProcessQueueMessage` de cet exemple inclut un attribut `QueueTrigger`, le déclencheur de cette fonction est la réception d’un nouveau message en file d’attente. L’objet `JobHost` recherche les nouveaux messages dans la file d’attente spécifiée (« webjobsqueue », dans cet exemple). Lorsqu’il trouve un message, il appelle `ProcessQueueMessage`. 

L’attribut `QueueTrigger` lie le paramètre `inputText` à la valeur du message de file d'attente. L’attribut `Blob` lie également un objet `TextWriter` à un objet blob nommé « blobname » dans le conteneur « containername ».  

        public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")]] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)

La fonction utilise ensuite ces paramètres pour écrire la valeur du message en file d'attente dans l'objet blob :

        writer.WriteLine(inputText);

Les fonctionnalités de déclencheur et de classeur du Kit de développement logiciel (SDK) WebJobs simplifient considérablement le code que vous devez écrire. L'infrastructure du SDK écrit pour vous le code de bas niveau nécessaire à la gestion des files d'attente et au traitement des objets blob. Par exemple, elle crée des files d'attente qui n'existent pas encore, ouvre une file d'attente, lit les messages en attente, supprime les messages en attente une fois qu'ils ont été traités, crée des conteneurs d'objets blob qui n'existent pas encore, écrit dans les objets blob, etc.

L’exemple de code suivant présente différents déclencheurs dans une tâche WebJob : `QueueTrigger`, `FileTrigger`, `WebHookTrigger`, et `ErrorTrigger`. 

```
    public class Functions
    {
        public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        TextWriter log)
        {
            log.WriteLine(message);
        }

        public static void ProcessFileAndUploadToBlob(
            [FileTrigger(@"import\{name}", "*.*", autoDelete: true)] Stream file,
            [Blob(@"processed/{name}", FileAccess.Write)] Stream output,
            string name,
            TextWriter log)
        {
            output = file;
            file.Close();
            log.WriteLine(string.Format("Processed input file '{0}'!", name));
        }

        [Singleton]
        public static void ProcessWebHookA([WebHookTrigger] string body, TextWriter log)
        {
            log.WriteLine(string.Format("WebHookA invoked! Body: {0}", body));
        }

        public static void ProcessGitHubWebHook([WebHookTrigger] string body, TextWriter log)
        {
            dynamic issueEvent = JObject.Parse(body);
            log.WriteLine(string.Format("GitHub WebHook invoked! ('{0}', '{1}')",
                issueEvent.issue.title, issueEvent.action));
        }

        public static void ErrorMonitor(
        [ErrorTrigger("00:01:00", 1)] TraceFilter filter, TextWriter log,
        [SendGrid(
            To = "admin@emailaddress.com",
            Subject = "Error!")]
         SendGridMessage message)
        {
            // log last 5 detailed errors to the Dashboard
            log.WriteLine(filter.GetDetailedMessage(5));
            message.Text = filter.GetDetailedMessage(1);
        }
    }
```

## <a name="a-idschedulea-scheduling"></a>Planification <a id="schedule"></a>
L’attribut `TimerTrigger` permet de déclencher des fonctions selon une planification. Vous pouvez planifier une tâche Web dans son ensemble via des fonctions individuelles Azure ou de la planification d'une tâche WebJob à l’aide du SDK WebJobs `TimerTrigger`. Voici un exemple de code.

```
public class Functions
{
    public static void ProcessTimer([TimerTrigger("*/15 * * * * *", RunOnStartup = true)]
    TimerInfo info, [Queue("queue")] out string message)
    {
        message = info.FormatNextOccurrences(1);
    }
}
```

Pour d’autres exemples de code, consultez [TimerSamples.cs](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/ExtensionsSample/Samples/TimerSamples.cs) dans le référentiel azure-webjobs-sdk-extensions sur GitHub.com.

## <a name="extensibility"></a>Extensibilité
Vous n'êtes pas limité aux fonctionnalités intégrées, en effet le SDK WebJobs vous permet d’écrire les binders et déclencheurs personnalisés.  Par exemple, vous pouvez écrire des déclencheurs pour les événements du cache et les planifications périodiques. Un [référentiel open source](https://github.com/Azure/azure-webjobs-sdk-extensions) contient un [guide détaillé sur l’extensibilité du SDK WebJobs](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) et l’exemple de code pour vous aider à commencer à écrire vos propres déclencheurs et binders.

## <a name="a-idworkerroleausing-the-webjobs-sdk-outside-of-webjobs"></a><a id="workerrole"></a>Utilisation du Kit de développement logiciel (SDK) WebJobs en dehors de WebJobs
Un programme qui utilise le Kit de développement logiciel (SDK) WebJobs est une application console standard qui peut s’exécuter n’importe où, et pas nécessairement en tant que tâche web. Vous pouvez tester ce programme localement sur votre ordinateur de développement ; en production, vous pouvez l’exécuter dans un rôle de travail de service cloud ou un service Windows si vous préférez un de ces environnements. 

Toutefois, le tableau de bord est uniquement disponible en tant qu’extension d’une application web Microsoft Azure App Service. Si vous voulez que l’exécution ait lieu en dehors de WebJobs, mais souhaitez pouvoir utiliser le tableau de bord, vous pouvez configurer une application web de manière à utiliser le compte de stockage auquel la chaîne de connexion du tableau de bord du Kit de développement logiciel (SDK) WebJobs fait référence ; le tableau de bord WebJobs de cette application affiche alors les données concernant l’exécution des fonctions de votre programme, qui s’exécute ailleurs. Vous pouvez accéder au tableau de bord en utilisant l’URL https://*{webappname}*.scm.azurewebsites.net/azurejobs/#/functions. Pour plus d’informations, consultez le billet de blog [Récupération d’un tableau de bord pour un développement local avec le Kit de développement logiciel (SDK) WebJobs](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx)(notez cependant que le billet de blog affiche un ancien nom de chaîne de connexion). 

## <a name="a-idnostorageadashboard-features"></a><a id="nostorage"></a>Fonctionnalités du tableau de bord
Le Kit de développement logiciel (SDK) WebJobs offre plusieurs avantages même si vous n’utilisez pas les déclencheurs ou binders du SDK WebJobs :

* Vous pouvez appeler des fonctions depuis le tableau de bord.
* Vous pouvez réexécuter des fonctions depuis le tableau de bord.
* Vous pouvez afficher les journaux dans le tableau de bord, liés à une tâche web spécifique (journaux d’application, écrits via les éléments Console.Out, Console.Error, Trace, etc.) ou liés à l’appel de fonction spécifique à l’origine de leur génération (journaux écrits via un objet `TextWriter` que le Kit de développement logiciel (SDK) passe à la fonction, en tant que paramètre). 

Pour en savoir plus, consultez les pages [Appel manuel d’une fonction](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual) et [Écriture de journaux](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs) 

## <a name="a-idnextstepsanext-steps"></a><a id="nextsteps"></a>Étapes suivantes
Pour en savoir plus sur le Kit de développement logiciel (SDK) WebJobs, consultez la rubrique [Tâches web Azure - Ressources recommandées](http://go.microsoft.com/fwlink/?linkid=390226).

Pour plus d'informations sur les dernières améliorations apportées au SDK WebJobs, consultez les [Notes de publication](https://github.com/Azure/azure-webjobs-sdk/wiki/Release-Notes).




<!--HONumber=Nov16_HO3-->



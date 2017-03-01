---
title: "Déclencheurs d’application API App Service | Microsoft Docs"
description: "Comment implémenter des déclencheurs dans une application API dans Azure App Service"
services: logic-apps
documentationcenter: .net
author: guangyang
manager: erikre
editor: jimbe
ms.assetid: 493c3703-786d-4434-9dca-8f77744b2f5d
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 08/25/2016
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: 015ca80c952110d3289888ed82d7a543be29950c
ms.openlocfilehash: cf020b0f5f14d73635cf44e0157b53b35eb00d60
ms.lasthandoff: 12/06/2016


---
# <a name="azure-app-service-api-app-triggers"></a>Déclencheurs des applications API Azure App Service
> [!NOTE]
> Cette version de l’article s’applique à la version du schéma 2014-12-01-preview API Apps.
>
>

## <a name="overview"></a>Vue d'ensemble
Cet article explique comment implémenter des déclencheurs d'application API et comment les utiliser à partir d'une application logique.

Tous les extraits de code de cette rubrique sont copiés depuis l’ [exemple de code de l’application API FileWatcher](http://go.microsoft.com/fwlink/?LinkId=534802).

Notez que vous devez télécharger le package NuGet suivant pour que le code de cet article puisse être généré et s'exécuter : [http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/).

## <a name="what-are-api-app-triggers"></a>Que sont les déclencheurs des applications API ?
C'est un scénario courant pour une application API que de déclencher un événement, afin que les clients de l'application API puissent effectuer les actions appropriées en réponse à l'événement. Le mécanisme basé sur l'API REST qui prend en charge ce scénario est appelé un déclencheur d'application API.

Par exemple, supposons que votre code client utilise l' [application API du connecteur Twitter](../connectors/connectors-create-api-twitter.md) et que votre code doit exécuter une action basée sur les nouveaux tweets contenant des mots spécifiques. Dans ce cas, vous pouvez définir un déclencheur d’émission ou de collecte pour répondre facilement à ce besoin.

## <a name="poll-trigger-versus-push-trigger"></a>Déclencheur d'interrogation et déclencheur d'émission
Deux types de déclencheurs sont actuellement pris en charge :

* Déclencheur d'interrogation : le client collecte auprès de l'application API les notifications de déclenchement d'un événement.
* Déclencheur d'émission : le client est notifié par l'application API quand un événement est déclenché.

### <a name="poll-trigger"></a>Déclencheur d'interrogation :
Un déclencheur d'interrogation est implémenté sous la forme d'une API REST normale et il attend que ses clients (comme une application Logic) l'interrogent pour obtenir une notification. Alors que le client est susceptible de gérer les états, le déclencheur d'interrogation lui-même est sans état.

Les informations suivantes concernant les paquets de demande et de réponse montrent certains aspects essentiels du contrat du déclencheur d'interrogation :

* Demande
  * Méthode HTTP : GET
  * Paramètres
    * triggerState : ce paramètre facultatif permet aux clients de spécifier leur état, afin que le déclencheur d'interrogation puisse décider correctement s'il faut ou non retourner la notification, en fonction de l'état spécifié.
    * Paramètres spécifiques de l'API
* Réponse
  * Code d'état **200** : la demande est valide et il existe une notification provenant du déclencheur. Le contenu de la notification sera le corps de la réponse. Un en-tête « Retry-After » dans la réponse indique que des données de notification supplémentaires doivent être récupérées avec un appel de demande suivant.
  * Code d'état **202** : la demande est valide, mais il n'existe pas de nouvelle notification provenant du déclencheur.
  * Code d'état **4xx** : la demande n'est pas valide. Le client ne doit pas recommencer la demande.
  * Code d'état **5xx** : la demande a entraîné une erreur de serveur interne et/ou un problème temporaire. Le client doit recommencer la demande.

L'extrait de code suivant est un exemple de la façon d'implémenter un déclencheur d'interrogation.

    // Implement a poll trigger.
    [HttpGet]
    [Route("api/files/poll/TouchedFiles")]
    public HttpResponseMessage TouchedFilesPollTrigger(
        // triggerState is a UTC timestamp
        string triggerState,
        // Additional parameters
        string searchPattern = "*")
    {
        // Check to see whether there is any file touched after the timestamp.
        var lastTriggerTimeUtc = DateTime.Parse(triggerState).ToUniversalTime();
        var touchedFiles = Directory.EnumerateFiles(rootPath, searchPattern, SearchOption.AllDirectories)
            .Select(f => FileInfoWrapper.FromFileInfo(new FileInfo(f)))
            .Where(fi => fi.LastAccessTimeUtc > lastTriggerTimeUtc);

        // If there are files touched after the timestamp, return their information.
        if (touchedFiles != null && touchedFiles.Count() != 0)
        {
            // Extension method provided by the AppService service SDK.
            return this.Request.EventTriggered(new { files = touchedFiles });
        }
        // If there are no files touched after the timestamp, tell the caller to poll again after 1 mintue.
        else
        {
            // Extension method provided by the AppService service SDK.
            return this.Request.EventWaitPoll(new TimeSpan(0, 1, 0));
        }
    }

Pour tester ce déclencheur d'interrogation, procédez comme suit :

1. Déployez l'application API avec un paramètre d'authentification **anonyme public**.
2. Appelez l'opération **touch** pour toucher un fichier. L'illustration suivante montre un exemple de demande via Postman.
   ![Appeler une opération Touch via Postman](./media/app-service-api-dotnet-triggers/calltouchfilefrompostman.PNG)
3. Appelez le déclencheur d'interrogation avec le paramètre **triggerState** défini avec un horodatage antérieur à l'étape 2. L'illustration suivante montre un exemple de demande via Postman.
   ![Appeler un déclencheur d'interrogation via Postman](./media/app-service-api-dotnet-triggers/callpolltriggerfrompostman.PNG)

### <a name="push-trigger"></a>Déclencheurs d'émission :
Un déclencheur d'émission est implémenté sous la forme d'une API REST normale, qui envoie des notifications aux clients qui se sont inscrits pour être notifiés quand des événements spécifiques se déclenchent.

Les informations suivantes concernant les paquets de demande et de réponse montrent certains aspects essentiels du contrat du déclencheur d'interrogation :

* Demande
  * Méthode HTTP : PUT
  * Paramètres
    * triggerId : obligatoire. Chaîne Opaque (comme un GUID) qui représente l'inscription d'un déclencheur d'émission.
    * callbackUrl : obligatoire. URL du rappel à appeler quand l'événement se déclenche. L'appel est un simple appel HTTP POST.
    * Paramètres spécifiques de l'API
* Réponse
  * Code d'état **200** : la demande d'inscription du client a réussi.
  * Code d'état **4xx** : la demande n'est pas valide. Le client ne doit pas recommencer la demande.
  * Code d'état **5xx** : la demande a entraîné une erreur de serveur interne et/ou un problème temporaire. Le client doit recommencer la demande.
* Rappel
  * Méthode HTTP : POST
  * Corps de la demande : contenu de la notification.

L'extrait de code suivant est un exemple de la façon d'implémenter un déclencheur d'émission.

    // Implement a push trigger.
    [HttpPut]
    [Route("api/files/push/TouchedFiles/{triggerId}")]
    public HttpResponseMessage TouchedFilesPushTrigger(
        // triggerId is an opaque string.
        string triggerId,
        // A helper class provided by the AppService service SDK.
        // Here it defines the input of the push trigger is a string and the output to the callback is a FileInfoWrapper object.
        [FromBody]TriggerInput<string, FileInfoWrapper> triggerInput)
    {
        // Register the trigger to some trigger store.
        triggerStore.RegisterTrigger(triggerId, rootPath, triggerInput);

        // Extension method provided by the AppService service SDK indicating the registration is completed.
        return this.Request.PushTriggerRegistered(triggerInput.GetCallback());
    }

    // A simple in-memory trigger store.
    public class InMemoryTriggerStore
    {
        private static InMemoryTriggerStore instance;

        private IDictionary<string, FileSystemWatcher> _store;

        private InMemoryTriggerStore()
        {
            _store = new Dictionary<string, FileSystemWatcher>();
        }

        public static InMemoryTriggerStore Instance
        {
            get
            {
                if (instance == null)
                {
                    instance = new InMemoryTriggerStore();
                }
                return instance;
            }
        }

        // Register a push trigger.
        public void RegisterTrigger(string triggerId, string rootPath,
            TriggerInput<string, FileInfoWrapper> triggerInput)
        {
            // Use FileSystemWatcher to listen to file change event.
            var filter = string.IsNullOrEmpty(triggerInput.inputs) ? "*" : triggerInput.inputs;
            var watcher = new FileSystemWatcher(rootPath, filter);
            watcher.IncludeSubdirectories = true;
            watcher.EnableRaisingEvents = true;
            watcher.NotifyFilter = NotifyFilters.LastAccess;

            // When some file is changed, fire the push trigger.
            watcher.Changed +=
                (sender, e) => watcher_Changed(sender, e,
                    Runtime.FromAppSettings(),
                    triggerInput.GetCallback());

            // Assoicate the FileSystemWatcher object with the triggerId.
            _store[triggerId] = watcher;

        }

        // Fire the assoicated push trigger when some file is changed.
        void watcher_Changed(object sender, FileSystemEventArgs e,
            // AppService runtime object needed to invoke the callback.
            Runtime runtime,
            // The callback to invoke.
            ClientTriggerCallback<FileInfoWrapper> callback)
        {
            // Helper method provided by AppService service SDK to invoke a push trigger callback.
            callback.InvokeAsync(runtime, FileInfoWrapper.FromFileInfo(new FileInfo(e.FullPath)));
        }
    }

Pour tester ce déclencheur d'interrogation, procédez comme suit :

1. Déployez l'application API avec un paramètre d'authentification **anonyme public**.
2. Accédez à [http://requestb.in/](http://requestb.in/) pour créer un élément RequestBin qui vous servira d'URL de rappel.
3. Appelez le déclencheur d’émission avec un GUID pour **triggerId** et l’URL de l’élément RequestBin pour **callbackUrl**.
   ![Appeler un déclencheur d'émission via Postman](./media/app-service-api-dotnet-triggers/callpushtriggerfrompostman.PNG)
4. Appelez l'opération **touch** pour toucher un fichier. L'illustration suivante montre un exemple de demande via Postman.
   ![Appeler une opération Touch via Postman](./media/app-service-api-dotnet-triggers/calltouchfilefrompostman.PNG)
5. Vérifiez l'élément RequestBin pour confirmer que le rappel du déclencheur d'émission est appelé avec une sortie de propriété.
   ![Appeler un déclencheur d’interrogation via Postman](./media/app-service-api-dotnet-triggers/pushtriggercallbackinrequestbin.PNG)

### <a name="describe-triggers-in-api-definition"></a>Décrire des déclencheurs dans une définition d'API
Après avoir implémenté les déclencheurs et déployé votre application API dans Azure, accédez au panneau **Définition d’API** dans le portail Azure en version préliminaire. Vous y voyez que les déclencheurs sont automatiquement reconnus dans l’interface utilisateur, qui repose sur la définition de l’API Swagger 2.0 de l’application API.

![Panneau Définition de l'API](./media/app-service-api-dotnet-triggers/apidefinitionblade.PNG)

Si vous cliquez sur le bouton **Télécharger Swagger** et que vous ouvrez le fichier JSON, vous verrez des résultats similaires à ceci :

    "/api/files/poll/TouchedFiles": {
      "get": {
        "operationId": "Files_TouchedFilesPollTrigger",
        ...
        "x-ms-scheduler-trigger": "poll"
      }
    },
    "/api/files/push/TouchedFiles/{triggerId}": {
      "put": {
        "operationId": "Files_TouchedFilesPushTrigger",
        ...
        "x-ms-scheduler-trigger": "push"
      }
    }

La propriété d'extension **x-ms-schedular-trigger** indique comment les déclencheurs sont décrits dans la définition de l'API, et elle est automatiquement ajoutée à la passerelle d'application API quand vous demandez la définition de l'API via la passerelle, si la demande satisfait à un des critères suivants. (Vous pouvez également ajouter cette propriété manuellement.)

* Déclencheur d'interrogation :
  * Si la méthode HTTP est **GET**.
  * Si la propriété **operationId** contient la chaîne **trigger**.
  * Si la propriété **parameters** inclut un paramètre avec une propriété **name** définie sur **triggerState**.
* Déclencheurs d'émission :
  * Si la méthode HTTP est **PUT**.
  * Si la propriété **operationId** contient la chaîne **trigger**.
  * Si la propriété **parameters** inclut un paramètre avec une propriété **name** définie sur **triggerId**.

## <a name="use-api-app-triggers-in-logic-apps"></a>Utiliser des déclencheurs d'application API dans les applications logiques
### <a name="list-and-configure-api-app-triggers-in-the-logic-apps-designer"></a>Répertorier et configurer des déclencheurs d'application API dans le concepteur d'applications logiques
Si vous créez une application logique dans le même groupe de ressources que l'application API, vous pourrez l'ajouter au canevas du concepteur simplement en cliquant dessus. Les images suivantes illustrent ce principe :

![Déclencheurs dans le concepteur d'applications logiques](./media/app-service-api-dotnet-triggers/triggersinlogicappdesigner.PNG)

![Configurer un déclencheur d'interrogation dans le concepteur d'applications logiques](./media/app-service-api-dotnet-triggers/configurepolltriggerinlogicappdesigner.PNG)

![Configurer un déclencheur d'émission dans le concepteur d'applications logiques](./media/app-service-api-dotnet-triggers/configurepushtriggerinlogicappdesigner.PNG)

## <a name="optimize-api-app-triggers-for-logic-apps"></a>Optimiser les déclencheurs d'application API pour les applications logiques
Après avoir ajouté des déclencheurs à une application API, vous pouvez faire un certain nombre de choses pour améliorer l'expérience de l'utilisation de l'application API dans une application logique.

Par exemple, le paramètre **triggerState** pour les déclencheurs d'interrogation doit être défini avec l'expression suivante dans l'application logique. Cette expression doit évaluer le dernier appel du déclencheur depuis l'application logique et retourner cette valeur.  

    @coalesce(triggers()?.outputs?.body?['triggerState'], '')

REMARQUE : pour une explication des fonctions utilisées dans l'expression ci-dessus, reportez-vous à la documentation sur le [Langage de définition des flux de travail des applications logiques](https://msdn.microsoft.com/library/azure/dn948512.aspx).

Les utilisateurs d'applications logiques devraient ainsi fournir l'expression ci-dessus pour le paramètre **triggerState** lors de l'utilisation du déclencheur. Il est possible de faire prédéfinir cette valeur par le concepteur d'applications logiques via la propriété d'extension **x-ms-scheduler-recommendation**.  La propriété d'extension **x-ms-visibility** peut être définie avec une valeur *internal* , de façon à ce que le paramètre lui-même ne soit pas affiché sur le concepteur.  L'extrait de code suivant montre ceci.

    "/api/Messages/poll": {
      "get": {
        "operationId": "Messages_NewMessageTrigger",
        "parameters": [
          {
            "name": "triggerState",
            "in": "query",
            "required": true,
            "x-ms-visibility": "internal",
            "x-ms-scheduler-recommendation": "@coalesce(triggers()?.outputs?.body?['triggerState'], '')",
            "type": "string"
          }
        ]
        ...
        "x-ms-scheduler-trigger": "poll"
      }
    }

Pour les déclencheurs d'émission, le paramètre **triggerId** doit identifier de façon univoque l'application logique. Une pratique recommandée consiste à définir cette propriété avec le nom du flux de travail en utilisant l'expression suivante :

    @workflow().name

À l’aide des propriétés d’extension **x-ms-scheduler-recommendation** et **x-ms-visibility** dans sa définition d’API, l’application API peut indiquer au concepteur d’applications logiques de définir automatiquement cette expression pour l’utilisateur.

        "parameters":[  
          {  
            "name":"triggerId",
            "in":"path",
            "required":true,
            "x-ms-visibility":"internal",
            "x-ms-scheduler-recommendation":"@workflow().name",
            "type":"string"
          },


### <a name="add-extension-properties-in-api-defintion"></a>Ajouter des propriétés d'extension dans la définition de l'API
Des informations de métadonnées supplémentaires, comme les propriétés d’extension **x-ms-scheduler-recommendation** et **x-ms-visibility**, peuvent être ajoutées dans la définition de l’API de deux façons : statique ou dynamique.

Pour les métadonnées statiques, vous pouvez modifier directement le fichier */metadata/apiDefinition.swagger.json* dans votre projet et y ajouter les propriétés manuellement.

Pour les applications API utilisant des métadonnées dynamiques, vous pouvez modifier le fichier SwaggerConfig.cs pour y ajouter un filtre d'opération qui peut ajouter ces extensions.

    GlobalConfiguration.Configuration
        .EnableSwagger(c =>
            {
                ...
                c.OperationFilter<TriggerStateFilter>();
                ...
            }


Voici un exemple de la façon dont cette classe peut être implémentée pour faciliter le scénario des métadonnées dynamiques.

    // Add extension properties on the triggerState parameter
    public class TriggerStateFilter : IOperationFilter
    {

        public void Apply(Operation operation, SchemaRegistry schemaRegistry, System.Web.Http.Description.ApiDescription apiDescription)
        {
            if (operation.operationId.IndexOf("Trigger", StringComparison.InvariantCultureIgnoreCase) >= 0)
            {
                // this is a possible trigger
                var triggerStateParam = operation.parameters.FirstOrDefault(x => x.name.Equals("triggerState"));
                if (triggerStateParam != null)
                {
                    if (triggerStateParam.vendorExtensions == null)
                    {
                        triggerStateParam.vendorExtensions = new Dictionary<string, object>();
                    }

                    // add 2 vendor extensions
                    // x-ms-visibility: set to 'internal' to signify this is an internal field
                    // x-ms-scheduler-recommendation: set to a value that logic app can use
                    triggerStateParam.vendorExtensions.Add("x-ms-visibility", "internal");
                    triggerStateParam.vendorExtensions.Add("x-ms-scheduler-recommendation",
                                                           "@coalesce(triggers()?.outputs?.body?['triggerState'], '')");
                }
            }
        }
    }


<properties pageTitle="How to use the API Inspector to trace calls in Azure API Management" metaKeywords="" description="Learn how to trace calls using the API Inspector in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How to use the API Inspector to trace calls in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Utilisation de l'inspecteur d'API pour le suivi des appels dans Gestion des API Azure

Gestion des API Azure (version préliminaire) fournit un outil Inspecteur d'API pour vous aider au débogage et à la résolution des problèmes de vos API. L'inspecteur d'API peut être utilisé par programme depuis vos applications ou directement depuis le portail des développeurs. Ce guide contient une procédure détaillée pour l'utilisation de l'inspecteur d'API.

## Dans cette rubrique

-   [Utilisation de l'inspecteur d'API pour suivre un appel][Utilisation de l'inspecteur d'API pour suivre un appel]
-   [Inspection du suivi][Inspection du suivi]
-   [Étapes suivantes][Étapes suivantes]

## <a name="trace-call"> </a>Utilisation de l'inspecteur d'API pour suivre un appel

Pour utiliser l'inspecteur d'API, ajoutez un en-tête de demande **ocp-apim-trace: true** à l'appel de l'opération, puis téléchargez et inspectez le suivi avec l'URL indiquée par l'en-tête de réponse **ocp-apim-trace-location**. Cette opération peut se faire par programme ou directement depuis le portail des développeurs.

Ce didacticiel vous montre comment utiliser l'inspecteur d'API pour suivre les opérations avec l'API Echo.

> Chaque instance du service Gestion des API est pré-configurée avec une API Echo qui peut être utilisée pour faire des expériences et en savoir plus sur la gestion des API. L'API Echo renvoie ce qui lui est fourni. Pour l'utiliser, vous pouvez appeler un verbe HTTP, et la valeur de retour sera simplement ce que vous avez envoyé.

Pour commencer, cliquez sur **Portail des développeurs** dans le portail Azure pour votre service Gestion des API. Vous accéderez au portail d'administration Gestion des API.

> Si vous n'avez pas encore créé une instance de service Gestion des API, consultez la page [Création d'une instance de service Gestion des API][Création d'une instance de service Gestion des API] dans le didacticiel [Prise en main de Gestion des API Azure][Prise en main de Gestion des API Azure].

![Portail des développeurs Gestion des API][Portail des développeurs Gestion des API]

Les opérations peuvent être directement appelées depuis le portail des développeurs, ce qui permet d'afficher et de tester les opérations d'une API. Dans cette étape du didacticiel, vous allez appeler la méthode **Get Resource** de l'**API Echo**.

Cliquez sur **API** dans le menu supérieur et sélectionnez **API Echo**.

![Echo API][Echo API]

> Si vous n'avez qu'une API configurée ou visible dans votre compte, cliquez sur des API pour accéder directement aux opérations associées.

Sélectionnez l'opération **Ressource GET** et cliquez sur **Ouvrir la console**.

![Open console][Open console]

Conservez les valeurs de paramètres par défaut et sélectionnez votre clé d'abonnement dans la liste déroulante **subscription-key**.

Tapez **ocp-apim-trace: true** dans la zone de texte **En-têtes de demande**, puis cliquez sur **HTTP Get**.

![HTTP Get][HTTP Get]

Les en-têtes de réponse contiennent un élément **ocp-apim-trace-location** avec une valeur similaire à celle de l'exemple qui suit.

    ocp-apim-trace-location : https://contosoltdxw7zagdfsprykd.blob.core.windows.net/apiinspectorcontainer/ZW3e23NsW4wQyS-SHjS0Og2-2?sv=2013-08-15&sr=b&sig=Mgx7cMHsLmVDv%2B%2BSzvg3JR8qGTHoOyIAV7xDsZbF7%2Bk%3D&se=2014-05-04T21%3A00%3A13Z&sp=r&verify_guid=a56a17d83de04fcb8b9766df38514742

Le suivi peut être téléchargé depuis l'emplacement spécifié et examiné, comme dans l'étape suivante.

## <a name="inspect-trace"> </a>Inspection du suivi

Pour examiner les valeurs du suivi, téléchargez le fichier de suivi à partir de l'URL **ocp-apim-trace-location**. Il s'agit d'un fichier texte au format JSON. Il se présente comme l'exemple ci-dessous.

    {
      "validityGuid":"a43a07a03de04fcb8b1425df38514742",
      "logEntries":[
        {
            "timestamp":"2014-05-03T21:00:13.2182473Z",
            "source":"Microsoft.WindowsAzure.ApiManagement.Proxy.Gateway.Handlers.DebugLoggingHandler",
            "data":{
            "originalRequest":{
                "method":"GET",
                "url":"https://contosoltd.current.int-azure-api.net/echo/resource?param1=sample&subscription-key=...",
                "headers":[
                {
                    "name":"ocp-apim-tracing",
                    "value":"true"
                },
                {
                    "name":"Host",
                    "value":"contosoltd.current.int-azure-api.net"
                }
                ]
            }
            }
        },
        {
          "timestamp":"2014-05-03T21:00:13.2182473Z",
          "source":"request handler",
          "data":{
            "configuration":{
              "api":{
                "from":"echo",
                "to":"http://echoapi.cloudapp.net/api"
              },
              "operation":{
                "method":"GET",
                "uriTemplate":"/resource"
              },
              "user":{
                "id":1,
                "groups":[
              
                ]
              },
              "product":{
                "id":1
              }
            }
          }
        },
        {
          "timestamp":"2014-05-03T21:00:13.2182473Z",
          "source":"backend call handler",
          "data":{
            "message":"Sending request to the service.",
            "request":{
              "method":"GET",
              "url":"http://echoapi.cloudapp.net/api/resource?param1=sample&subscription-key=...",
              "headers":[
                {
                  "name":"X-Forwarded-For",
                  "value":"138.91.78.77"
                },
                {
                  "name":"ocp-apim-tracing",
                  "value":"true"
                }
              ]
            }
          }
        },
        {
          "timestamp":"2014-05-03T21:00:13.2182473Z",
          "source":"backend call handler",
          "data":{
            "status":{
              "code":200,
              "reason":"OK"
            },
            "headers":[
              {
                "name":"Pragma",
                "value":"no-cache"
              },
              {
                "name":"Host",
                "value":"echoapi.cloudapp.net"
              },
              {
                "name":"X-Forwarded-For",
                "value":"138.91.78.77"
              },
              {
                "name":"ocp-apim-tracing",
                "value":"true"
              },
              {
                "name":"Content-Length",
                "value":"0"
              },
              {
                "name":"Cache-Control",
                "value":"no-cache"
              },
              {
                "name":"Expires",
                "value":"-1"
              },
              {
                "name":"Server",
                "value":"Microsoft-IIS/8.0"
              },
              {
                "name":"X-AspNet-Version",
                "value":"4.0.30319"
              },
              {
                "name":"X-Powered-By",
                "value":"Azure API Management - http://api.azure.com/,ASP.NET"
              },
              {
                "name":"Date",
                "value":"Sat, 03 May 2014 21:00:17 GMT"
              }
            ]
          }
        }
      ]
    }

## <a name="next-steps"> </a>Étapes suivantes

-   Consultez les autres rubriques du didacticiel [Prise en main de la configuration avancée des API][Prise en main de la configuration avancée des API].

  [Utilisation de l'inspecteur d'API pour suivre un appel]: #trace-call
  [Inspection du suivi]: #inspect-trace
  [Étapes suivantes]: #next-steps
  [Création d'une instance de service Gestion des API]: ../api-management-get-started/#create-service-instance
  [Prise en main de Gestion des API Azure]: ../api-management-get-started
  [Portail des développeurs Gestion des API]: ./media/api-management-howto-api-inspector/api-management-developer-portal-menu.png
  [Echo API]: ./media/api-management-howto-api-inspector/api-management-echo-api.png
  [Open console]: ./media/api-management-howto-api-inspector/api-management-open-console.png
  [HTTP Get]: ./media/api-management-howto-api-inspector/api-management-http-get.png
  [Prise en main de la configuration avancée des API]: ../api-management-get-started-advanced

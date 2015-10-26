<properties 
	pageTitle="Utilisation de l'inspecteur d'API pour le suivi des appels dans Gestion des API Azure" 
	description="Découvrez comment suivre les appels à l'aide de l'inspecteur d'API dans Gestion des API Azure." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/14/2015" 
	ms.author="sdanie"/>

# Utilisation de l'inspecteur d'API pour le suivi des appels dans Gestion des API Azure

Gestion des API Azure fournit un outil Inspecteur d’API pour vous aider au débogage et à la résolution des problèmes de vos API. L'inspecteur d'API peut être utilisé par programme et directement depuis le portail des développeurs.

En plus des opérations de suivi, l'inspecteur d’API assure également le suivi des évaluations d’[expression de stratégie](https://msdn.microsoft.com/library/azure/dn910913.aspx). Pour une démonstration, consultez l'épisode [Cloud Cover Episode 177: More API Management Features](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/), à partir de la 21e minute.

Ce guide contient une procédure détaillée pour l'utilisation de l'inspecteur d'API.

>[AZURE.NOTE]Le suivi de l’inspecteur d’API n’est généré et mis à disposition que pour les demandes contenant des clés d’abonnement qui appartiennent au compte [administrateur](api-management-howto-create-groups.md).

## <a name="trace-call"> </a>Utilisation de l’inspecteur d’API pour suivre un appel

Pour utiliser l’inspecteur d’API, ajoutez un en-tête de demande **ocp-apim-trace: true** à l’appel de l’opération, puis téléchargez et inspectez le suivi avec l’URL indiquée par l’en-tête de réponse **ocp-apim-trace-location**. Cette opération peut se faire par programme ou directement depuis le portail des développeurs.

Ce didacticiel montre comment utiliser l'inspecteur d'API pour suivre les opérations à l'aide de l'API Basic Calculator qui est configurée dans le didacticiel de prise en main [Gestion de votre première API](api-management-get-started.md). Si vous n'avez pas effectué ce didacticiel, prenez quelques minutes pour importer l'API Basic Calculator. Vous pouvez également utiliser une autre API, par exemple l'API Echo. Chaque instance du service Gestion des API est pré-configurée avec une API Echo qui peut être utilisée pour faire des expériences et en savoir plus sur la gestion des API. L'API Echo renvoie ce qui lui est fourni. Pour l'utiliser, vous pouvez appeler un verbe HTTP, et la valeur de retour sera simplement ce que vous avez envoyé.



Pour commencer, cliquez sur **Portail des développeurs** dans le portail Azure de votre service Gestion des API. Les opérations peuvent être appelées directement depuis le portail des développeurs, ce qui permet d'afficher et de tester les opérations d'une API.

>Si vous n'avez pas encore créé d’instance de service Gestion des API, consultez la page [Création d'une instance de service Gestion des API][] dans le didacticiel [Prise en main de Gestion des API Azure][].

![Portail des développeurs Gestion des API][api-management-developer-portal-menu]

Cliquez sur **API** dans le menu supérieur et sélectionnez **Basic Calculator**.

![API Echo][api-management-api]

Cliquez sur **Essayer** pour essayer l’opération **Ajouter deux entiers**.

![Essayer][api-management-open-console]

Conservez les valeurs de paramètres par défaut et sélectionnez la clé d'abonnement du produit à utiliser dans la liste déroulante **subscription-key**.

Par défaut, dans le portail des développeurs, l’en-tête **Ocp-Apim-Trace** est déjà défini sur **true**. Il indique si un suivi est généré.

![Envoyer][api-management-http-get]

Cliquez sur **Envoyer** pour appeler l'opération.

![Envoyer][api-management-send-results]

Les en-têtes de réponse contiennent un élément **ocp-apim-trace-location** avec une valeur similaire à celle de l'exemple qui suit.

	ocp-apim-trace-location : https://contosoltdxw7zagdfsprykd.blob.core.windows.net/apiinspectorcontainer/ZW3e23NsW4wQyS-SHjS0Og2-2?sv=2013-08-15&sr=b&sig=Mgx7cMHsLmVDv%2B%2BSzvg3JR8qGTHoOyIAV7xDsZbF7%2Bk%3D&se=2014-05-04T21%3A00%3A13Z&sp=r&verify_guid=a56a17d83de04fcb8b9766df38514742

Le suivi peut être téléchargé depuis l'emplacement spécifié et examiné, comme indiqué dans l'étape suivante.

## <a name="inspect-trace"> </a>Inspection du suivi

Pour examiner les valeurs du suivi, téléchargez le fichier de suivi à partir de l'URL **ocp-apim-trace-location**. Il s'agit d'un fichier texte au format JSON. Il se présente comme l'exemple ci-dessous.

	{
	    "traceId": "abcd8ea63d134c1fabe6371566c7cbea",
	    "traceEntries": {
	        "inbound": [
	            {
	                "source": "handler",
	                "timestamp": "2015-06-23T19:51:35.2998610Z",
	                "elapsed": "00:00:00.0725926",
	                "data": {
	                    "request": {
	                        "method": "GET",
	                        "url": "https://contoso5.azure-api.net/calc/add?a=51&b=49",
	                        "headers": [
	                            {
	                                "name": "Ocp-Apim-Subscription-Key",
	                                "value": "5d7c41af64a44a68a2ea46580d271a59"
	                            },
	                            {
	                                "name": "Connection",
	                                "value": "Keep-Alive"
	                            },
	                            {
	                                "name": "Host",
	                                "value": "contoso5.azure-api.net"
	                            }
	                        ]
	                    }
	                }
	            },
	            {
	                "source": "mapper",
	                "timestamp": "2015-06-23T19:51:35.2998610Z",
	                "elapsed": "00:00:00.0726213",
	                "data": {
	                    "configuration": {
	                        "api": {
	                            "from": "/calc",
	                            "to": {
	                                "scheme": "http",
	                                "host": "calcapi.cloudapp.net",
	                                "port": 80,
	                                "path": "/api",
	                                "queryString": "",
	                                "query": {},
	                                "isDefaultPort": true
	                            }
	                        },
	                        "operation": {
	                            "method": "GET",
	                            "uriTemplate": "/add?a={a}&b={b}"
	                        },
	                        "user": {
	                            "id": 1,
	                            "groups": [
	                                "Administrators",
	                                "Developers"
	                            ]
	                        },
	                        "product": {
	                            "id": 1
	                        }
	                    }
	                }
	            },
	            {
	                "source": "handler",
	                "timestamp": "2015-06-23T19:51:35.2998610Z",
	                "elapsed": "00:00:00.0727522",
	                "data": {
	                    "message": "Request is being forwarded to the backend service.",
	                    "request": {
	                        "method": "GET",
	                        "url": "http://calcapi.cloudapp.net/api/add?a=51&b=49",
	                        "headers": [
	                            {
	                                "name": "Ocp-Apim-Subscription-Key",
	                                "value": "5d7c41af64a44a68a2ea46580d271a59"
	                            },
	                            {
	                                "name": "X-Forwarded-For",
	                                "value": "33.52.215.35"
	                            }
	                        ]
	                    }
	                }
	            }
	        ],
	        "outbound": [
	            {
	                "source": "handler",
	                "timestamp": "2015-06-23T19:51:35.4256650Z",
	                "elapsed": "00:00:00.1960601",
	                "data": {
	                    "response": {
	                        "status": {
	                            "code": 200,
	                            "reason": "OK"
	                        },
	                        "headers": [
	                            {
	                                "name": "Pragma",
	                                "value": "no-cache"
	                            },
	                            {
	                                "name": "Content-Length",
	                                "value": "124"
	                            },
	                            {
	                                "name": "Cache-Control",
	                                "value": "no-cache"
	                            },
	                            {
	                                "name": "Content-Type",
	                                "value": "application/xml; charset=utf-8"
	                            },
	                            {
	                                "name": "Date",
	                                "value": "Tue, 23 Jun 2015 19:51:35 GMT"
	                            },
	                            {
	                                "name": "Expires",
	                                "value": "-1"
	                            },
	                            {
	                                "name": "Server",
	                                "value": "Microsoft-IIS/8.5"
	                            },
	                            {
	                                "name": "X-AspNet-Version",
	                                "value": "4.0.30319"
	                            },
	                            {
	                                "name": "X-Powered-By",
	                                "value": "ASP.NET"
	                            }
	                        ]
	                    }
	                }
	            },
	            {
	                "source": "handler",
	                "timestamp": "2015-06-23T19:51:35.4256650Z",
	                "elapsed": "00:00:00.1961112",
	                "data": {
	                    "message": "Response headers have been sent to the caller. Starting to stream the response body."
	                }
	            },
	            {
	                "source": "handler",
	                "timestamp": "2015-06-23T19:51:35.4256650Z",
	                "elapsed": "00:00:00.1963155",
	                "data": {
	                    "message": "Response body streaming to the caller is complete."
	                }
	            }
	        ]
	    }
	}

## <a name="next-steps"> </a>Étapes suivantes

-	Consultez les autres rubriques du didacticiel [Prise en main de la configuration avancée des API][].
-	Pour une démonstration du suivi des expressions de stratégie, regardez la vidéo [Cloud Cover Episode 177: More API Management Features](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/). Avancez à la 21e minute pour voir la démonstration.

>[AZURE.VIDEO episode-177-more-api-management-features-with-vlad-vinogradsky]

[Use API Inspector to trace a call]: #trace-call
[Inspect the trace]: #inspect-trace
[Next steps]: #next-steps

[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md

[Prise en main de Gestion des API Azure]: api-management-get-started.md
[Création d'une instance de service Gestion des API]: api-management-get-started.md#create-service-instance
[Prise en main de la configuration avancée des API]: api-management-get-started-advanced.md
[Management Portal]: https://manage.windowsazure.com/


[api-management-developer-portal-menu]: ./media/api-management-howto-api-inspector/api-management-developer-portal-menu.png
[api-management-api]: ./media/api-management-howto-api-inspector/api-management-api.png
[api-management-echo-api-get]: ./media/api-management-howto-api-inspector/api-management-echo-api-get.png
[api-management-developer-key]: ./media/api-management-howto-api-inspector/api-management-developer-key.png
[api-management-open-console]: ./media/api-management-howto-api-inspector/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-api-inspector/api-management-http-get.png
[api-management-send-results]: ./media/api-management-howto-api-inspector/api-management-send-results.png




 

<!---HONumber=Oct15_HO3-->
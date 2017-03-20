---
title: "Mises à jour de schéma du&1;er août&2015; (version préliminaire) - Azure Logic Apps | Microsoft Docs"
description: "Créer des définitions JSON pour Azure Logic Apps avec la version de schéma 2015-08-01 (version préliminaire)"
author: stepsic-microsoft-com
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 0d03a4d4-e8a8-4c81-aed5-bfd2a28c7f0c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 05/31/2016
ms.author: stepsic
translationtype: Human Translation
ms.sourcegitcommit: a3ae5ce7d16ac9ed3a06c313ff3c48e8cbf79c54
ms.openlocfilehash: f1bf19e6dfbcf187635730a53c93162244c17c6a
ms.lasthandoff: 03/01/2017


---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Mises à jour de schéma pour Azure Logic Apps - Version préliminaire du 1er août 2015

Cette nouvelle version de schéma et d’API intègre différentes améliorations clés destinées à accroître la fiabilité et la simplicité d’utilisation des applications logiques :

*    Le type d’action **APIApp** est remplacé par un nouveau type d’action [**APIConnection**](#api-connections).
*    **Repeat** est renommée [**Foreach**](#foreach).
*    L’application API de [**l’Écouteur HTTP**](#http-listener)n’est plus requise.
*    L’appel des flux de travail enfants utilise un [nouveau schéma](#child-workflows).

<a name="api-connections"></a>
## <a name="move-to-api-connections"></a>Déplacement vers les connexions d’API

La plus importante modification est que vous n’avez plus besoin de déployer des applications API dans votre abonnement Azure pour pouvoir utiliser des API. Voici les manières dont vous pouvez utiliser les API :

* API gérées
* Vos API web personnalisées

Chacune de ces méthodes fait l’objet d’un traitement légèrement différent, car leurs modèles de gestion et d’hébergement sont différents. Avec ce modèle, vous n’êtes plus limité aux ressources déployées dans votre groupe de ressources Azure. 

### <a name="managed-apis"></a>API gérées

Microsoft gère certaines API à votre place, comme Office 365, Salesforce, Twitter et FTP. Vous pouvez utiliser des API gérées (par exemple, Bing Translate), tandis que d’autres nécessitent une configuration. Cette configuration est appelée une *connexion*.

Par exemple, lorsque vous utilisez Office 365, vous devez créer une connexion qui contient votre jeton de connexion Office 365. Ce jeton est stocké et actualisé de manière sécurisée, afin que votre application logique puisse toujours appeler l’API Office 365. Sinon, si vous souhaitez vous connecter à votre serveur SQL ou FTP, il vous faut créer une connexion présentant la chaîne connection. 

Dans la définition, ces actions sont appelées `APIConnection`. Voici un exemple de connexion appelant l’API Office 365 pour envoyer un courrier électronique :

```
{
    "actions": {
        "Send_Email": {
            "type": "ApiConnection",
            "inputs": {
                "host": {
                    "api": {
                        "runtimeUrl": "https://msmanaged-na.azure-apim.net/apim/office365"
                    },
                    "connection": {
                        "name": "@parameters('$connections')['shared_office365']['connectionId']"
                    }
                },
                "method": "post",
                "body": {
                    "Subject": "Reminder",
                    "Body": "Don't forget!",
                    "To": "me@contoso.com"
                },
                "path": "/Mail"
            }
        }
    }
}
```

L’objet `host` est la portion des entrées spécifique aux connexions d’API et contient deux parties : `api` et `connection`.

L’ `api` présente l’URL d’exécution de l’emplacement d’hébergement de cette API gérée. Vous pouvez voir l’ensemble des API gérées disponibles en appelant `GET https://management.azure.com/subscriptions/{subid}/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview`.

Lorsque vous utilisez une, elle peut présenter ou non des *paramètres de connexion* définis. Si l’API n’en possède pas, aucune *connexion* n’est requise. Si l’API en possède, il vous faudra créer une connexion. La connexion créée porte le nom de votre choix. Vous référencez ensuite le nom de l’objet `connection` à l’intérieur de l’objet `host`. Pour créer une connexion dans un groupe de ressources, appelez :

```
PUT https://management.azure.com/subscriptions/{subid}/resourceGroups/{rgname}/providers/Microsoft.Web/connections/{name}?api-version=2015-08-01-preview
```

Avec le corps suivant :

```
{
  "properties": {
    "api": {
      "id": "/subscriptions/{subid}/providers/Microsoft.Web/managedApis/azureblob"
    },
    "parameterValues": {
        "accountName": "{The name of the storage account -- the set of parameters is different for each API}"
    }
  },
  "location": "{Logic app's location}"
}
```

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Déploiement d’API gérées dans un modèle Azure Resource Manager

Vous pouvez créer une application complète dans un modèle Azure Resource Manager, tant qu’elle ne nécessite aucune connexion interactive.
Si une connexion est requise, vous pouvez procéder à la configuration avec le modèle Azure Resource Manager, mais devrez néanmoins accéder au portail afin d’autoriser les connexions. 

```
    "resources": [{
        "apiVersion": "2015-08-01-preview",
        "name": "azureblob",
        "type": "Microsoft.Web/connections",
        "location": "[resourceGroup().location]",
        "properties": {
            "api": {
                "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
            },
            "parameterValues": {
                "accountName": "[parameters('storageAccountName')]",
                "accessKey": "[parameters('storageAccountKey')]"
            }
        }
    }, {
        "type": "Microsoft.Logic/workflows",
        "apiVersion": "2015-08-01-preview",
        "name": "[parameters('logicAppName')]",
        "location": "[resourceGroup().location]",
        "dependsOn": ["[resourceId('Microsoft.Web/connections', 'azureblob')]"
        ],
        "properties": {
            "sku": {
                "name": "[parameters('sku')]",
                "plan": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
                }
            },
            "definition": {
                "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
                "actions": {
                    "Create_file": {
                        "type": "apiconnection",
                        "inputs": {
                            "host": {
                                "api": {
                                    "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                                },
                                "connection": {
                                    "name": "@parameters('$connections')['azureblob']['connectionId']"
                                }
                            },
                            "method": "post",
                            "queries": {
                                "folderPath": "[concat('/', parameters('containerName'))]",
                                "name": "helloworld.txt"
                            },
                            "body": "@decodeDataUri('data:, Hello+world!')",
                            "path": "/datasets/default/files"
                        },
                        "conditions": []
                    }
                },
                "contentVersion": "1.0.0.0",
                "outputs": {},
                "parameters": {
                    "$connections": {
                        "defaultValue": {},
                        "type": "Object"
                    }
                },
                "triggers": {
                    "recurrence": {
                        "type": "Recurrence",
                        "recurrence": {
                            "frequency": "Day",
                            "interval": 1
                        }
                    }
                }
            },
            "parameters": {
                "$connections": {
                    "value": {
                        "azureblob": {
                            "connectionId": "[concat(resourceGroup().id,'/providers/Microsoft.Web/connections/azureblob')]",
                            "connectionName": "azureblob",
                            "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
                        }

                    }
                }
            }
        }
    }]
```

Vous pouvez constater dans cet exemple que les connexions sont des ressources hébergées dans votre groupe de ressources. Elles référencent les API gérées disponibles dans votre abonnement.

### <a name="your-custom-web-apis"></a>Vos API web personnalisées

Si vous utilisez vos propres API (non gérées par Microsoft), utilisez l’action **HTTP** intégrée pour les appeler. Pour une expérience optimale, vous avez tout intérêt à exposer un point de terminaison swagger pour votre API. Ce point de terminaison permet au concepteur d’application logique de réaliser le rendu des entrées et des sorties pour votre API. Sans point de terminaison swagger, le concepteur peut simplement représenter les entrées et les sorties en tant qu’objets JSON opaques.

Voici un exemple illustrant la nouvelle propriété `metadata.apiDefinitionUrl` :

```
{
   "actions": {
        "mycustomAPI": {
            "type": "http",
            "metadata": {
              "apiDefinitionUrl": "https://mysite.azurewebsites.net/api/apidef/"  
            },
            "inputs": {
                "uri": "https://mysite.azurewebsites.net/api/getsomedata",
                "method": "GET"
            }
        }
    }
}
```

Si vous hébergez votre API web sur Azure App Service, votre API web s’affiche automatiquement dans la liste des actions disponibles dans le concepteur. Si ce n’est pas le cas, vous devrez coller le contenu directement dans l’URL. Le point de terminaison Swagger doit être non authentifié pour être utilisé dans le concepteur des applications logiques ; vous pouvez cependant sécuriser l’API à l’aide des méthodes prises en charge dans Swagger.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Appel d’applications API déployées avec 2015-08-01-preview

Si vous avez déjà déployé une application API, vous pouvez l’appeler à l’aide de l’action **HTTP**.

Par exemple, si vous utilisez Dropbox pour répertorier les fichiers, vous pouvez disposer du contenu suivant dans votre définition de version de schéma **2014-12-01-preview** :

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
            "defaultValue": "eyJ0eX...wCn90",
            "type": "String",
            "metadata": {
                "token": {
                    "name": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
                }
            }
        }
    },
    "actions": {
        "dropboxconnector": {
            "type": "ApiApp",
            "inputs": {
                "apiVersion": "2015-01-14",
                "host": {
                    "id": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                    "gateway": "https://avdemo.azurewebsites.net"
                },
                "operation": "ListFiles",
                "parameters": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Vous pouvez générer l’action HTTP équivalente comme dans cet exemple (la section des paramètres de la définition de l’application demeure inchangée) :

```
{
    "actions": {
        "dropboxconnector": {
            "type": "Http",
            "metadata": {
              "apiDefinitionUrl": "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
            },
            "inputs": {
                "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
                "method": "POST",
                "body": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Présentation de ces propriétés une par une :

| Propriété d’action | Description |
| --- | --- |
| `type` |`Http` au lieu de `APIapp` |
| `metadata.apiDefinitionUrl` |Pour utiliser cette action dans le concepteur d’applications logiques, vous devez inclure le point de terminaison des métadonnées, créé depuis : `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` |Construit depuis : `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` |Toujours `POST` |
| `inputs.body` |Identique aux paramètres de l’application API |
| `inputs.authentication` |Identique à l’authentification de l’application API |

Cette approche doit fonctionner avec l’ensemble des actions d’application API. N’oubliez pas cependant que ces applications API antérieures ne sont plus prises en charge. Vous devez donc opter pour l’une des deux autres options précédentes, à savoir une API gérée ou l’hébergement de votre API web personnalisée.

<a name="foreach"></a>
## <a name="renamed-repeat-to-foreach"></a>« Repeat » renommée en « Foreach »

Les clients ayant utilisé la version de schéma précédente se sont beaucoup plaints du caractère déroutant de **Repeat** et n’ont pas vraiment compris que **Repeat** était réellement une boucle foreach. Par conséquent, nous avons renommé `repeat` en `foreach`. Par exemple, précédemment, vous auriez écrit :

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "repeat": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{repeatItem()}"
            }
        }
    }
}
```

Maintenant, vous devez écrire :

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "foreach": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{item()}"
            }
        }
    }
}
```

Auparavant, la fonction `@repeatItem()` était utilisée pour référencer l’élément sur lequel était actuellement exécutée une itération. Cette fonction est désormais simplifiée en `@item()`. 

### <a name="reference-outputs-from-foreach"></a>Référencement des sorties de 'foreach'

Dans un souci de simplification, les sorties des actions `foreach` ne sont pas encapsulées dans un objet appelé `repeatItems`. Tandis que les sorties de l’exemple `repeat` précédent étaient :

```
{
    "repeatItems": [
        {
            "name": "pingBing",
            "inputs": {
                "uri": "https://www.bing.com/search?q=0",
                "method": "GET"
            },
            "outputs": {
                "headers": { },
                "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
            }
            "status": "Succeeded"
        }
    ]
}
```

Les sorties sont maintenant :

```
[
    {
        "name": "pingBing",
        "inputs": {
            "uri": "https://www.bing.com/search?q=0",
            "method": "GET"
        },
        "outputs": {
            "headers": { },
            "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
        }
        "status": "Succeeded"
    }
]
```

Lorsque vous référenciez ces sorties, pour atteindre le corps de l’action vous deviez auparavant procéder ainsi :

```
{
    "actions": {
        "secondAction": {
            "type": "Http",
            "repeat": "@outputs('pingBing').repeatItems",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com",
                "body": "@repeatItem().outputs.body"
            }
        }
    }
}
```

Désormais le code présente cette apparence :

```
{
    "actions": {
        "secondAction": {
            "type": "Http",
            "foreach": "@outputs('pingBing')",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com",
                "body": "@item().outputs.body"
            }
        }
    }
}
```

Ces modifications entraînent la disparition des fonctions `@repeatItem()`, `@repeatBody()` et `@repeatOutputs()`.

<a name="http-listener"></a>
## <a name="native-http-listener"></a>Écouteur HTTP natif

Les fonctionnalités de l’écouteur HTTP sont désormais intégrées. Par conséquent, vous n’avez plus besoin de déployer une application API d’écouteur HTTP. Consultez [ici une explication détaillée de la configuration d’un point de terminaison d’application logique pouvant être appelé](../logic-apps/logic-apps-http-endpoint.md). 

Avec ces modifications, nous avons supprimé la fonction `@accessKeys()` et l’avons remplacée par la fonction `@listCallbackURL()` dédiée à la récupération du point de terminaison, si nécessaire. De plus, vous devez maintenant définir au moins un déclencheur dans votre application logique. Si vous souhaitez appliquer l’action `/run` au flux de travail, vous devez disposer d’un de ces déclencheurs : `manual`, `apiConnectionWebhook` ou `httpWebhook`.

<a name="child-workflows"></a>
## <a name="call-child-workflows"></a>Appel de flux de travail enfants

Auparavant, quiconque souhaitait appeler des flux de travail enfants devait y accéder, récupérer le jeton d’accès, puis coller ce dernier dans la définition de l’application logique destinée à effectuer l’appel. Avec le nouveau schéma, le moteur Logic Apps génère automatiquement une SAP lors de l’exécution pour le flux de travail enfant, ce qui évite d’avoir à coller une clé secrète dans la définition. Voici un exemple :

```
"mynestedwf": {
    "type": "workflow",
    "inputs": {
        "host": {
            "id": "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName": "myendpointtrigger"
        },
        "queries": {
            "extrafield": "specialValue"
        },
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        },
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "conditions": []
}
```

Autre amélioration, nous octroyons aux flux de travail enfants un accès complet à la requête entrante. Cela signifie que vous pouvez transmettre les paramètres dans la section *queries* et dans l’objet *headers*, et que vous pouvez définir intégralement le corps.

Enfin, des modifications doivent être apportées au flux de travail enfant. Auparavant, vous pouviez appeler directement un flux de travail enfant. Désormais, il vous faut définir un point de terminaison déclencheur dans le flux de travail pour prendre en charge l’appel du parent. Généralement, vous devrez ajouter un déclencheur de type `manual`, que vous utiliserez dans la définition du parent. Notez que la propriété `host` dispose d’un élément `triggerName`, dans la mesure où vous devez toujours spécifier le déclencheur invoqué.

## <a name="other-changes"></a>Autres modifications

### <a name="new-queries-property"></a>Nouvelle propriété « queries »

Tous les types d’action prennent désormais en charge une nouvelle entrée appelée `queries`. Cette entrée peut être un objet structuré, qui ne nécessite aucun assemblage manuel de chaîne.

### <a name="renamed-parse-function-to-json"></a>Fonction 'parse()' renommée 'json()'

Nous procédons actuellement à l’ajout d’autres types de contenu, c’est pourquoi nous avons renommé la fonction `parse()` en `json()`.

## <a name="coming-soon-enterprise-integration-apis"></a>Bientôt disponibles : API d’intégration d’entreprise

Nous ne disposons pas actuellement de versions gérées des API d’intégration d’entreprise, du type AS2. En attendant, vous pouvez utiliser vos API BizTalk existantes via l’action HTTP. Pour plus d’informations, consultez la rubrique dédiée à l’utilisation de vos applications API déjà déployées dans le [calendrier d’intégration](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). 


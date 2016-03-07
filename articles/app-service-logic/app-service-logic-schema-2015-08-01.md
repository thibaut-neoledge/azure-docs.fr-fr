<properties 
	pageTitle="Nouvelle version de schéma 2015-08-01-preview" 
	description="Découvrir comment écrire la définition JSON pour la dernière version des applications logiques" 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="stepsic"/>
	
# Nouvelle version de schéma 2015-08-01-preview

Le nouveau schéma et la récente version de l’API pour les applications logiques ont bénéficié d’un certain nombre d’améliorations destinées à accroître la fiabilité et la simplicité d’utilisation des applications logiques. Voici les 4 principales différences :

1. Le type d’action **APIApp** a été remplacé par un nouveau type d’action **APIConnection**.
2. **Repeat** a été renommée en **Foreach**.
3. L’application API de l’**Écouteur HTTP** n’est plus requise.
4. L’appel des flux de travail enfants utilise un nouveau schéma.

## 1\. Déplacement vers les connexions d’API

La modification la plus importante ? Pour utiliser les API, il n’est plus nécessaire de déployer des applications d’API dans votre abonnement Azure. Il existe 2 façons d’utiliser les API : * API gérées * Vos API web personnalisées

Chacune de ces méthodes fait l’objet d’un traitement légèrement différent, car leurs modèles de gestion et d’hébergement sont différents. Avec ce modèle, vous n’êtes plus limité aux ressources déployées dans votre groupe de ressources.

### API gérées

Diverses API sont gérées par Microsoft pour votre compte, comme Office 365, Salesforce, Twitter, FTP, etc... Certaines de ces API gérées peuvent être utilisées en l’état (par exemple, Bing Translate), tandis que d’autres nécessitent une configuration. Cette configuration est appelée une *connexion*.

Par exemple, lorsque vous utilisez Office 365, vous devez créer une connexion qui contient votre jeton de connexion Office 365. Ce jeton est stocké et actualisé de manière sécurisée, afin que votre application logique puisse toujours appeler l’API Office 365. Sinon, si vous souhaitez vous connecter à votre serveur SQL ou FTP, il vous faut créer une connexion présentant la chaîne connection.

Dans la définition, ces actions sont appelées `APIConnection`. Voici un exemple de connexion appelant l’API Office 365 pour envoyer un courrier électronique :

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

La portion des entrées spécifique aux connexions d’API est l’objet `host`. Elle comporte deux parties : `api` et `connection`.

L’`api` présente l’URL d’exécution de l’emplacement d’hébergement de cette API gérée. Pour afficher l’ensemble des API gérées disponibles, appelez `GET https://management.azure.com/subscriptions/{subid}/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview`.

Une API utilisée présente ou non des **paramètres de connexion** définis. Si elle n’en possède pas, aucune **connexion** n’est requise. Si elle en possède, il vous faudra créer une connexion. La connexion créée présente le nom choisi par vos soins, que vous référencez dans l’objet `connection`, dans l’objet `host`. Pour créer une connexion dans un groupe de ressources, appelez :

```
PUT https://management.azure.com/subscriptions/{subid}/resourceGroups/{rgname}/providers/Microsoft.Web/connections/{name}?api-version=2015-08-01-preview
```

Avec le corps suivant :


```
{
  "properties": {
    "api": {
      "id": "/subscriptions/{subid}/providers/Microsoft.Web/managedApis/azureblob"
    },
	"parameterValues" : {
		"accountName" : "{The name of the storage account -- the set of parameters is different for each API}"
	}
  },
  "location" : "{Logic app's location}"
}
```

### Déploiement d’API gérées dans un modèle Azure Resource Manager

Vous pouvez créer une application complète dans un modèle ARM, tant qu’elle ne nécessite aucune connexion interactive. Si elle nécessite une connexion, vous pouvez procéder à la configuration avec le modèle ARM, mais devrez néanmoins accéder au portail afin d’autoriser les connexions.

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
		"dependsOn": [
			"[resourceId('Microsoft.Web/connections', 'azureblob')]"
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
								"folderPath": "[concat('/',parameters('containerName'))]",
								"name": "helloworld.txt"
							},
							"body": "@decodeDataUri('data:,Hello+world!')",
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

Vous pouvez constater dans cet exemple que les connexions sont des ressources normales hébergées dans votre groupe de ressources. Elles référencent les API gérées disponibles dans votre abonnement.

### Vos API web personnalisées

Si vous utilisez vos propres API (c’est-à-dire non gérées par Microsoft), vous devez utiliser l’action **HTTP** intégrée pour les appeler. Pour profiter d’une expérience optimale, vous avez tout intérêt à exposer un point de terminaison swagger pour votre API. Ce faisant, le concepteur d’application logique pourra réaliser le rendu des entrées et des sorties pour votre API. Sans point de terminaison swagger, le concepteur pourra simplement représenter les entrées et les sorties en tant qu’objets JSON opaques.

Voici un exemple illustrant la nouvelle propriété `metadata.apiDefinitionUrl` : ```
{
   "actions": {
        "mycustomAPI": {
            "type": "http",
            "metadata" : {
              "apiDefinitionUrl" : "https://mysite.azurewebsites.net/api/apidef/"  
            },
            "inputs": {
                "uri": "https://mysite.azurewebsites.net/api/getsomedata",
                "method" : "GET"
            }
        }
    }
}
```

Si vous hébergez votre API web sur **App Service**, la liste des actions disponibles dans le concepteur s’affichera automatiquement. Si ce n’est pas le cas, vous devrez coller le contenu directement dans l’URL. Le point de terminaison swagger doit être non authentifié pour être utilisé dans le concepteur des applications logiques (vous pouvez cependant sécuriser l’API à l’aide des méthodes prises en charge dans le point de terminaison swagger).

### Utilisation de vos applications API déployées avec 2015-08-01-preview

Si vous avez déjà déployé une application API, vous pouvez l’appeler via l’action **HTTP**.

Par exemple, si vous utilisez Dropbox pour répertorier les fichiers, vous pouvez disposer du contenu suivant dans votre définition de version de schéma **2014-12-01-preview**: ```
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

Vous pouvez générer l’action HTTP équivalente comme ci-dessous (la section des paramètres de la définition de l’application demeure inchangée) :

```
{
    "actions": {
        "dropboxconnector": {
            "type": "Http",
            "metadata" : {
              "apiDefinitionUrl" : "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
            },
            "inputs": {
                "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
                "method" : "POST",
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

Présentation de ces propriétés une par une :

| Propriété d’action | Description |
| --------------- | -----------  |
| `type` | `Http` au lieu de `APIapp` |
| `metadata.apiDefinitionUrl` | Si vous souhaitez utiliser cette action dans le concepteur d’applications logiques, il vous faudra inclure le point de terminaison des métadonnées. La construction s’établit à partir du contenu suivant : `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | La construction s’établit à partir du contenu suivant : `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Toujours `POST` |
| `inputs.body` | Identique aux paramètres de l’application API | 
| `inputs.authentication` | Identique à l’authentification de l’application API |

Cette approche doit fonctionner avec l’ensemble des actions d’application API. Toutefois, gardez à l’esprit que ces anciennes applications API ne sont plus prises en charge, et que vous avez tout intérêt à adopter l’une des deux autres options ci-dessus (recourir à une API gérée ou héberger votre API web personnalisée).

## 2\. Repeat renommée en Foreach

Les clients ayant utilisé la version de schéma précédente se sont beaucoup plaints du caractère déroutant de **Repeat** et n’ont pas vraiment compris qu’il s’agissait réellement d’une boucle foreach. Par conséquent, nous l’avons renommée en **Foreach**. Par exemple :

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

Sera désormais rédigé de cette manière :

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

Auparavant, la fonction `@repeatItem()` était utilisée pour référencer l’élément sur lequel était actuellement exécutée une itération. Elle a été simplifiée en `@item()`.

### Référencement des sorties de la boucle Foreach
Afin de simplifier encore davantage le code, les sorties des actions **Foreach** ne seront pas encapsulées dans un objet appelé **repeatItems**. Concrètement, cela signifie que les sorties de la boucle repeat ci-dessous se présentaient ainsi :

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
                "body": "<!DOCTYPE html><html lang="en" xml:lang="en" xmlns="http://www.w3.org/1999/xhtml" xmlns:Web="http://schemas.live.com/Web/">...</html>"
            }
            "status": "Succeeded"
        }
    ]
}
```

Elles présenteront désormais cette apparence :

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
            "body": "<!DOCTYPE html><html lang="en" xml:lang="en" xmlns="http://www.w3.org/1999/xhtml" xmlns:Web="http://schemas.live.com/Web/">...</html>"
        }
        "status": "Succeeded"
    }
]
```

Lorsque vous référenciez ces sorties, pour atteindre le corps de l’action vous procédiez ainsi :

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "repeat" : "@outputs('pingBing').repeatItems",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@repeatItem().outputs.body"
            }
        }
    }
}
```

Désormais le code présente cette apparence :

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "foreach" : "@outputs('pingBing')",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@item().outputs.body"
            }
        }
    }
}
```

Ces modifications entraînent la disparition des fonctions `@repeatItem()`, `@repeatBody()` et `@repeatOutputs()`.

## 3\. Écouteur HTTP natif 
Les fonctionnalités de l’écouteur HTTP étant désormais intégrées, vous n’avez plus à déployer une application API d’écouteur HTTP. Découvrez [une explication détaillée de la configuration d’un point de terminaison d’application logique pouvant être appelé ici](app-service-logic-http-endpoint.md).

Avec ces modifications, la fonction `@accessKeys()`, supprimée, est remplacée par la fonction `@listCallbackURL()` dédiée à la récupération du point de terminaison (si nécessaire). En outre, vous devez maintenant définir au moins un déclencheur dans votre application logique. Si vous souhaitez appliquer l’action `/run` au flux de travail, vous devrez disposer d’un déclencheur `manual`, `apiConnectionWebhook` ou `httpWebhook`.

## 4\. Appel de flux de travail enfants

Auparavant, quiconque souhaitait appeler des flux de travail enfants devait y accéder, récupérer le jeton d’accès, puis coller ce dernier dans la définition de l’application logique destinée à effectuer l’appel. Avec la nouvelle version de schéma, le moteur des applications logiques génère automatiquement une SAP lors de l’exécution pour le flux de travail enfant, ce qui signifie qu’il n’est plus nécessaire de coller aucune clé secrète dans la définition. Voici un exemple :

```
"mynestedwf" : {
    "type" : "workflow",
    "inputs" : {
        "host" : {
            "id" : "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName" : "myendpointtrigger"
        },
        "queries" : {
            "extrafield" : "specialValue"
        },
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        },
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        }
    },
    "conditions" : []
}
```

Autre amélioration, nous octroierons aux flux de travail enfants un accès complet à la requête entrante. Cela signifie que vous pouvez transmettre les paramètres dans la section *queries* et dans l’objet *headers*, et que vous pouvez définir intégralement le corps.

Enfin, des modifications doivent être apportées au flux de travail enfant. Auparavant, vous pouviez appeler directement un flux de travail enfant. Désormais, il vous faut définir un point de terminaison déclencheur dans le flux de travail pour prendre en charge l’appel du parent. Généralement, il vous faudra ajouter un déclencheur de type **manual** que vous utiliserez dans la définition du parent. Notez que la propriété `host` dispose d’un élément `triggerName`, dans la mesure où vous devez toujours spécifier le déclencheur invoqué.

## Autres modifications

### Nouvelle propriété queries
Tous les types d’action prennent désormais en charge une nouvelle entrée appelée **queries**. Il peut s’agir d’un objet structuré, qui ne nécessite aucun assemblage manuel de chaîne.

### Fonction parse() renommée
Dans la mesure où nous ajouterons bientôt d’autres types de contenu, la fonction `parse()` a été renommée en `json()`.

## Bientôt disponibles : API d’intégration d’entreprise
À ce stade, nous ne disposons d’aucune version gérée disponible des API d’intégration d’entreprise (comme AS2). Elles le seront bientôt, tel qu’indiqué dans la [feuille de route](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). Entre-temps, vous pouvez utiliser vos API BizTalk actuellement déployées via l’action HTTP, tel qu’indiqué ci-dessus dans l’article « Utilisation de vos applications API déployées ».

<!---HONumber=AcomDC_0224_2016-->
<properties 
    pageTitle="Notifications de modifications DocumentDB à l’aide de Logic Apps | Microsoft Azure" 
    description="." 
    keywords="notification de modification"
    services="documentdb" 
    authors="hedidin" 
    manager="jhubbard" 
    editor="mimig" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="rest-api" 
    ms.topic="article" 
    ms.date="03/10/2016" 
    ms.author="b-hoedid"/>

# Notifications relatives aux ressources DocumentDB nouvelles ou modifiées à l’aide de Logic Apps

L’idée de cet article m’est venue à la lecture d’une question figurant sur l’un des forums de la communauté DocumentDB. La question était **DocumentDB prend-il en charge les notifications relatives aux ressources modifiées** ?

J’ai travaillé de nombreuses années sur BizTalk Server, et il s’agit d’un scénario très courant dans l’utilisation de l’[adaptateur métier WCF](https://msdn.microsoft.com/library/bb798128.aspx). J’ai donc décidé de voir si je pouvais dupliquer cette fonctionnalité dans DocumentDB pour les documents nouveaux et/ou modifiés.

Cet article fournit une vue d’ensemble des composants de la solution de notification des modifications, qui inclut un [déclencheur](documentdb-programming.md#trigger) et une [application logique](../app-service-logic/app-service-logic-what-are-logic-apps.md). Des extraits de code importants sont inclus et la solution complète est disponible sur [GitHub](https://github.com/HEDIDIN/DocDbNotifications).

## Cas d’utilisation

Voici le cas d’utilisation sur lequel porte cet article.

DocumentDB est le dépôt des documents FHIR (Fast Healthcare Interoperability Resources) HL7 (Health Level Seven International). Supposons que votre base de données DocumentDB associée à votre API et à une application logique constituent un serveur FHIR HL7. Un établissement de soins de santé stocke les données de la patientèle dans la base de données DocumentDB « Patients ». Il existe plusieurs collections dans la base de données des patients : Clinical, Identification, etc. Les informations sur les patients figurent sous Identification. Une collection est nommée « Patient ».

Le service de cardiologie effectue le suivi des données personnelles relatives à la santé et à l’exercice. La recherche des enregistrements nouveaux ou modifiés des patients prend du temps. Le service informatique a donc pour mission d’identifier un moyen qui permettrait la réception d’une notification relative aux enregistrements des patients nouveaux ou modifiés.

Le service informatique peut facilement s’acquitter de cette mission. Ses membres indiquent également qu’ils pourraient mettre à la disposition du service de cardiologie les documents dans le [Stockage d’objets blob Azure](https://azure.microsoft.com/services/storage/).

## Procédure développée par le service informatique

Pour créer cette application, le service informatique a décidé de commencer par la modéliser. Le modèle BPMN (Business Process Model and Notation) présente l’avantage d’être compréhensible à la fois par des personnes techniques et non techniques. L’intégralité de ce processus de notification est considéré comme un processus métier.

## Vue d’ensemble du processus de notification

1. Vous démarrez avec une application logique qui comporte un déclencheur de minuterie. Par défaut, le déclencheur s’exécute toutes les heures.
2. Ensuite, vous faites une requête HTTP POST à l’application logique.
3. L’application logique effectue tout le travail.

![Présentation](./media/documentdb-change-notification/high-level-view.png)

### Commençons par examiner ce que fait cette application logique.
Si vous examinez la figure suivante, il existe plusieurs étapes dans le flux de travail LogicApp.

![Processus de la logique principale](./media/documentdb-change-notification/main-logic-app-process.png)

La procédure comporte trois étapes :

1. Vous devez obtenir la date/heure UTC actuelles à partir d’une application API. La valeur par défaut est 1 heure avant.

2. La valeur date/heure UTC est convertie en un format d’horodatage Unix. Il s’agit du format par défaut des horodatages dans DocumentDB.

3. Vous publiez (via une requête POST) la valeur dans une application API, qui effectue une requête DocumentDB. La valeur est utilisée dans une requête.

    ```SQL
     	SELECT * FROM Patients p WHERE (p._ts >= @unixTimeStamp)
    ```

    > [AZURE.NOTE] \_ts représente les métadonnées d’horodatage pour toutes les ressources DocumentDB.

4. Si des documents sont identifiés, le corps de la réponse est envoyé à votre stockage d’objets blob Azure.

    > [AZURE.NOTE] Le stockage d’objets blob nécessite un compte Azure Storage. Vous devez approvisionner un compte de stockage d’objets blob Azure et ajouter un nouvel objet blob nommé « patients ». Pour plus d’informations, consultez les rubriques [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md) et [Prise en main du stockage d’objets blob Azure](../storage/storage-dotnet-how-to-use-blobs.md).

5. Enfin, l’envoi d’un courrier électronique indique au destinataire le nombre de documents trouvés. Si aucun document n’est identifié, le corps du message est « 0 document(s) trouvé(s) ».

Maintenant que vous avez une idée de ce que fait le flux de travail, examinons son implémentation.

### Commençons par l’application logique principale.

Si vous n’êtes pas familiarisé avec Logic Apps, vous le trouverez dans [Azure Marketplace](https://portal.azure.com/). Pour plus d’informations, consultez [Qu’est-ce qu’une application logique ?](../app-service-logic/app-service-logic-what-are-logic-apps.md)

Quand vous créez une application logique, vous êtes invité à **indiquer comment vous souhaitez démarrer**.

En cliquant à l’intérieur de la zone de texte, vous avez le choix entre plusieurs événements. Pour cette application logique, sélectionnez **Manuel - Quand une demande HTTP est reçue** comme indiqué ci-dessous.

![Démarrage](./media/documentdb-change-notification/starting-off.png)

### Mode Création de votre application logique terminée
Nous allons avancer et consulter le mode Création une fois l’application logique terminée, dont le nom est DocDB.

![Flux de travail d’application logique](./media/documentdb-change-notification/workflow-expanded.png)

Quand vous modifiez les actions dans le Concepteur d’applications logiques, vous pouvez choisir les **Résultats** de la requête HTTP ou de l’action précédente comme indiqué dans l’action sendMail ci-dessous.

![Choisir des sorties](./media/documentdb-change-notification/choose-outputs.png)

Avant chaque action dans votre flux de travail, vous pouvez prendre une décision : **Ajouter une action** ou **Ajouter une condition**, comme indiqué dans l’illustration suivante.

![Prendre une décision](./media/documentdb-change-notification/add-action-or-condition.png)

Si vous sélectionnez **Ajouter une condition**, vous pouvez entrer votre logique dans un formulaire, comme indiqué dans l’illustration suivante. Fondamentalement, il s’agit d’une règle d’entreprise. Si vous cliquez à l’intérieur d’un champ, vous pouvez sélectionner des paramètres de l’action précédente. Vous pouvez également entrer les valeurs directement.

![Ajouter une condition](./media/documentdb-change-notification/condition1.png)

> [AZURE.NOTE] Vous pouvez aussi tout entrer en mode Code.

Examinons l’application logique terminée en mode Code.

```JSON
   
   	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
    "actions": {
        "Conversion": {
            "conditions": [
                {
                    "dependsOn": "GetUtcDate"
                }
            ],
            "inputs": {
                "method": "post",
                "queries": {
                    "currentdateTime": "@{body('GetUtcDate')}"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "Createfile": {
            "conditions": [
                {
                    "expression": "@greater(length(body('GetDocuments')), 0)"
                },
                {
                    "dependsOn": "GetDocuments"
                }
            ],
            "inputs": {
                "body": "@body('GetDocuments')",
                "host": {
                    "api": {
                        "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                    },
                    "connection": {
                        "name": "@parameters('$connections')['azureblob']['connectionId']"
                    }
                },
                "method": "post",
                "path": "/datasets/default/files",
                "queries": {
                    "folderPath": "/patients",
                    "name": "Patient_@{guid()}.json"
                }
            },
            "type": "ApiConnection"
        },
        "GetDocuments": {
            "conditions": [
                {
                    "dependsOn": "Conversion"
                }
            ],
            "inputs": {
                "method": "post",
                "queries": {
                    "unixTimeStamp": "@body('Conversion')"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "GetUtcDate": {
            "conditions": [],
            "inputs": {
                "method": "get",
                "queries": {
                    "hoursBack": "@{int(triggerBody()['GetUtcDate_HoursBack'])}"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "sendMail": {
            "conditions": [
                {
                    "dependsOn": "GetDocuments"
                }
            ],
            "inputs": {
                "body": "api_user=@{triggerBody()['sendgridUsername']}&api_key=@{triggerBody()['sendgridPassword']}&from=@{parameters('fromAddress')}&to=@{triggerBody()['EmailTo']}&subject=@{triggerBody()['Subject']}&text=@{int(length(body('GetDocuments')))} Documents Found",
                "headers": {
                    "Content-type": "application/x-www-form-urlencoded"
                },
                "method": "POST",
                "uri": "https://api.sendgrid.com/api/mail.send.json"
            },
            "type": "Http"
        }
    },
    "contentVersion": "1.0.0.0",
    "outputs": {
        "Results": {
            "type": "String",
            "value": "@{int(length(body('GetDocuments')))} Records Found"
        }
    },
    "parameters": {
        "$connections": {
            "defaultValue": {},
            "type": "Object"
        },
        "fromAddress": {
            "defaultValue": "user@msn.com",
            "type": "String"
        },
        "toAddress": {
            "defaultValue": "XXXXX@XXXXXXX.net",
            "type": "String"
        }
    },
    "triggers": {
        "manual": {
            "inputs": {
                "schema": {
                    "properties": {},
                    "required": [],
                    "type": "object"
                }
            },
            "type": "Manual"
        }
	
```

Si vous n’êtes pas familiarisé avec les différentes sections du code, vous pouvez consulter la documentation [Langage de définition de workflow de l’application logique](http://aka.ms/logicappsdocs).

Pour ce flux de travail, vous utilisez un [déclencheur Webhook HTTP](https://sendgrid.com/blog/whats-webhook/). Si vous examinez le code ci-dessus, vous reconnaîtrez les paramètres de l’exemple suivant.

```C#

    =@{triggerBody()['Subject']}

```

Le paramètre `triggerBody()` représente les paramètres qui sont inclus dans le corps d’une requête POST REST de l’API REST de l’application logique. Le paramètre `()['Subject']` représente le champ. Tous ces paramètres constituent le corps au format JSON.

> [AZURE.NOTE] En utilisant un Webhook, vous disposez d’un accès complet à l’en-tête et au corps de la demande du déclencheur. Dans cette application, vous souhaitez le corps.

Comme mentionné précédemment, vous pouvez utiliser le concepteur pour affecter des paramètres ou le faire en mode Code. Si vous le faites en mode Code, vous définissez les propriétés qui nécessitent une valeur, comme indiqué dans l’exemple de code suivant.

```JSON

	"triggers": {
		"manual": {
		    "inputs": {
			"schema": {
			    "properties": {
			"Subject": {
			    "type" : "String"	

			}
			},
			    "required": [
			"Subject"
			     ],
			    "type": "object"
			}
		    },
		    "type": "Manual"
		}
	    }
```

Ce que vous faites consiste à créer un schéma JSON qui sera transmis à partir du corps de la requête HTTP POST. Pour activer votre déclencheur, vous avez besoin d’une URL de rappel. Vous allez apprendre à la générer plus tard dans ce didacticiel.

## Actions
Nous allons voir ce que fait chaque action dans notre application logique.

### GetUTCDate

**Mode Création**

![](./media/documentdb-change-notification/getutcdate.png)

**Mode Code**

```JSON

	"GetUtcDate": {
		    "conditions": [],
		    "inputs": {
			"method": "get",
			"queries": {
			    "hoursBack": "@{int(triggerBody()['GetUtcDate_HoursBack'])}"
			},
			"uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization"
		    },
		    "metadata": {
			"apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
		    },
		    "type": "Http"
		},

```

Cette action HTTP exécute une opération GET. Elle appelle la méthode GetUtcDate de l’application API. L’Uri utilise la propriété « GetUtcDate\_HoursBack » transmise dans le corps du déclencheur. La valeur « GetUtcDate\_HoursBack » est définie dans la première application logique. Vous en apprendrez plus sur l’application logique du déclencheur plus tard dans ce didacticiel.

Cette action appelle votre application API à renvoyer la valeur de chaîne de date UTC.

#### Opérations

**Requête**

```JSON

	{
	    "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization",
	    "method": "get",
	    "queries": {
		  "hoursBack": "24"
	    }
	}

```

**Réponse**

```JSON

	{
	    "statusCode": 200,
	    "headers": {
		  "pragma": "no-cache",
		  "cache-Control": "no-cache",
		  "date": "Fri, 26 Feb 2016 15:47:33 GMT",
		  "server": "Microsoft-IIS/8.0",
		  "x-AspNet-Version": "4.0.30319",
		  "x-Powered-By": "ASP.NET"
	    },
	    "body": "Fri, 15 Jan 2016 23:47:33 GMT"
	}

```

L’étape suivante consiste à convertir la valeur de date/heure UTC en horodatage Unix, qui est un type double .NET.

### Conversion

##### Mode Création

![Conversion](./media/documentdb-change-notification/conversion.png)

##### Mode Code

```JSON

	"Conversion": {
	    "conditions": [
		{
		    "dependsOn": "GetUtcDate"
		}
	    ],
	    "inputs": {
		"method": "post",
		"queries": {
		    "currentDateTime": "@{body('GetUtcDate')}"
		},
		"uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion"
	    },
	    "metadata": {
		"apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
	    },
	    "type": "Http"
	},

```

Dans cette étape, vous passez la valeur retournée par GetUTCDate. Il existe une condition dependsOn, ce qui signifie que l’action GetUTCDate doit se terminer correctement. Si ce n’est pas le cas, cette action est ignorée.

Cette action appelle votre application API pour gérer la conversion.

#### Opérations

##### Demande

```JSON

	{
	    "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion",
	    "method": "post",
	    "queries": {
		"currentDateTime": "Fri, 15 Jan 2016 23:47:33 GMT"
	    }
	}   
```

##### Response

```JSON

	{
	    "statusCode": 200,
	    "headers": {
		  "pragma": "no-cache",
		  "cache-Control": "no-cache",
		  "date": "Fri, 26 Feb 2016 15:47:33 GMT",
		  "server": "Microsoft-IIS/8.0",
		  "x-AspNet-Version": "4.0.30319",
		  "x-Powered-By": "ASP.NET"
	    },
	    "body": 1452901653
	}
```

Dans l’action suivante, vous allez exécuter une opération POST sur notre application API.

### GetDocuments 

##### Mode Création

![Obtenir des documents](./media/documentdb-change-notification/getdocuments.png)

##### Mode Code

```JSON

	"GetDocuments": {
	    "conditions": [
		{
		    "dependsOn": "Conversion"
		}
	    ],
	    "inputs": {
		"method": "post",
		"queries": {
		    "unixTimeStamp": "@{body('Conversion')}"
		},
		"uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient"
	    },
	    "metadata": {
		"apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
	    },
	    "type": "Http"
	},

```

Pour l’action GetDocuments, vous vous apprêtez à transmettre le corps de réponse de l’action Conversion. Il s’agit d’un paramètre dans l’Uri :

 
```C#

	unixTimeStamp=@{body('Conversion')}

```

L’action QueryDocuments effectue une opération HTTP POST sur l’application API.

La méthode appelée est **QueryForNewPatientDocuments**.

#### Opérations

##### Demande

```JSON

	{
	    "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient",
	    "method": "post",
	    "queries": {
		"unixTimeStamp": "1452901653"
	    }
	}
```

##### Response

```JSON

	{
	    "statusCode": 200,
	    "headers": {
		"pragma": "no-cache",
		"cache-Control": "no-cache",
		"date": "Fri, 26 Feb 2016 15:47:35 GMT",
		"server": "Microsoft-IIS/8.0",
		"x-AspNet-Version": "4.0.30319",
		"x-Powered-By": "ASP.NET"
	    },
	    "body": [
		{
		    "id": "xcda",
		    "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
		    "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
		    "_ts": 1454874620,
		    "_etag": ""00007d01-0000-0000-0000-56b79ffc0000"",
		    "resourceType": "Patient",
		    "text": {
			"status": "generated",
			"div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
		    },
		    "identifier": [
			{
			    "use": "usual",
			    "type": {
				"coding": [
				    {
					"system": "http://hl7.org/fhir/v2/0203",
					"code": "MR"
				    }
				]
			    },
			    "system": "urn:oid:2.16.840.1.113883.19.5",
			    "value": "12345"
			}
		    ],
		    "active": true,
		    "name": [
			{
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },

```

L’action suivante consiste à enregistrer les documents dans le [stockage d’objets blob Azure](https://azure.microsoft.com/services/storage/).

> [AZURE.NOTE] Le stockage d’objets blob nécessite un compte Azure Storage. Vous devez approvisionner un compte de stockage d’objets blob Azure et ajouter un nouvel objet blob nommé « patients ». Pour plus d’informations, consultez l’article [Prise en main du stockage d’objets blob Azure](../storage/storage-dotnet-how-to-use-blobs.md).

### Créer un fichier

##### Mode Création

![Créer un fichier](./media/documentdb-change-notification/createfile.png)

##### Mode Code

```JSON

	{
    "host": {
        "api": {
            "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
        },
        "connection": {
            "name": "subscriptions/fxxxxxc079-4e5d-b002-xxxxxxxxxx/resourceGroups/Api-Default-Central-US/providers/Microsoft.Web/connections/azureblob"
        }
    },
    "method": "post",
    "path": "/datasets/default/files",
    "queries": {
        "folderPath": "/patients",
        "name": "Patient_17513174-e61d-4b56-88cb-5cf383db4430.json"
    },
    "body": [
        {
            "id": "xcda",
            "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
            "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
            "_ts": 1454874620,
            "_etag": ""00007d01-0000-0000-0000-56b79ffc0000"",
            "resourceType": "Patient",
            "text": {
                "status": "generated",
                "div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
            },
            "identifier": [
                {
                    "use": "usual",
                    "type": {
                        "coding": [
                            {
                                "system": "http://hl7.org/fhir/v2/0203",
                                "code": "MR"
                            }
                        ]
                    },
                    "system": "urn:oid:2.16.840.1.113883.19.5",
                    "value": "12345"
                }
            ],
            "active": true,
            "name": [
                {
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },

```

Le code est généré à partir de l’action du concepteur. Vous n’êtes pas obligé de modifier le code.

Si vous n’êtes pas familiarisé avec l’utilisation de l’API d’objets blob Azure, consultez la page [Prise en main de l’API de stockage d’objets blob Azure](../connectors/create-api-azureblobstorage.md).

#### Opérations

##### Demande

```JSON

	"host": {
        "api": {
            "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
        },
        "connection": {
            "name": "subscriptions/fxxxxxc079-4e5d-b002-xxxxxxxxxx/resourceGroups/Api-Default-Central-US/providers/Microsoft.Web/connections/azureblob"
        }
    },
    "method": "post",
    "path": "/datasets/default/files",
    "queries": {
        "folderPath": "/patients",
        "name": "Patient_17513174-e61d-4b56-88cb-5cf383db4430.json"
    },
    "body": [
        {
            "id": "xcda",
            "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
            "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
            "_ts": 1454874620,
            "_etag": ""00007d01-0000-0000-0000-56b79ffc0000"",
            "resourceType": "Patient",
            "text": {
                "status": "generated",
                "div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
            },
            "identifier": [
                {
                    "use": "usual",
                    "type": {
                        "coding": [
                            {
                                "system": "http://hl7.org/fhir/v2/0203",
                                "code": "MR"
                            }
                        ]
                    },
                    "system": "urn:oid:2.16.840.1.113883.19.5",
                    "value": "12345"
                }
            ],
            "active": true,
            "name": [
                {
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },….


```

##### Response

```JSON

	{
	    "statusCode": 200,
	    "headers": {
		"pragma": "no-cache",
		"x-ms-request-id": "2b2f7c57-2623-4d71-8e53-45c26b30ea9d",
		"cache-Control": "no-cache",
		"date": "Fri, 26 Feb 2016 15:47:36 GMT",
		"set-Cookie": "ARRAffinity=29e552cea7db23196f7ffa644003eaaf39bc8eb6dd555511f669d13ab7424faf;Path=/;Domain=127.0.0.1",
		"server": "Microsoft-HTTPAPI/2.0",
		"x-AspNet-Version": "4.0.30319",
		"x-Powered-By": "ASP.NET"
	    },
	    "body": {
		"Id": "0B0nBzHyMV-_NRGRDcDNMSFAxWFE",
		"Name": "Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
		"DisplayName": "Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
		"Path": "/Patient/Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
		"LastModified": "2016-02-26T15:47:36.215Z",
		"Size": 65647,
		"MediaType": "application/octet-stream",
		"IsFolder": false,
		"ETag": ""c-g_a-1OtaH-kNQ4WBoXLp3Zv9s/MTQ1NjUwMTY1NjIxNQ"",
		"FileLocator": "0B0nBzHyMV-_NRGRDcDNMSFAxWFE"
	    }
	}
```

Votre dernière étape consiste à envoyer une notification par courrier électronique.

### SendEmail

##### Mode Création

![Envoyer un message électronique](./media/documentdb-change-notification/sendemail.png)

##### Mode Code

```JSON


	"sendMail": {
	    "conditions": [
		{
		    "dependsOn": "GetDocuments"
		}
	    ],
	    "inputs": {
		"body": "api_user=@{triggerBody()['sendgridUsername']}&api_key=@{triggerBody()['sendgridPassword']}&from=@{parameters('fromAddress')}&to=@{triggerBody()['EmailTo']}&subject=@{triggerBody()['Subject']}&text=@{int(length(body('GetDocuments')))} Documents Found",
		"headers": {
		    "Content-type": "application/x-www-form-urlencoded"
		},
		"method": "POST",
		"uri": "https://api.sendgrid.com/api/mail.send.json"
	    },
	    "type": "Http"
	}
```

Dans cette action, vous envoyez une notification par courrier électronique. Vous utilisez [SendGrid](https://sendgrid.com/marketing/sendgrid-services?cvosrc=PPC.Bing.sendgrib&cvo_cid=SendGrid%20-%20US%20-%20Brand%20-%20&mc=Paid%20Search&mcd=BingAds&keyword=sendgrib&network=o&matchtype=e&mobile=&content=&search=1&utm_source=bing&utm_medium=cpc&utm_term=%5Bsendgrib%5D&utm_content=%21acq%21v2%2134335083397-8303227637-1649139544&utm_campaign=SendGrid+-+US+-+Brand+-+%28English%29).

Le code a été généré à l’aide d’un modèle d’application logique et SendGrid qui se trouve dans le [dépôt Github 101-logic-app-sendgrid](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sendgrid).
 
L’opération HTTP est un POST.

Les paramètres d’autorisation figurent dans les propriétés du déclencheur.

```JSON

	},
		"sendgridPassword": {
			 "type": "SecureString"
		 },
		 "sendgridUsername": {
			"type": "String"
		 }

		In addition, other parameters are static values set in the Parameters section of the Logic App. These are:
		},
		"toAddress": {
		    "defaultValue": "XXXX@XXXX.com",
		    "type": "String"
		},
		"fromAddress": {
		    "defaultValue": "XXX@msn.com",
		    "type": "String"
		},
		"emailBody": {
		    "defaultValue": "@{string(concat(int(length(actions('QueryDocuments').outputs.body)) Records Found),'/n', actions('QueryDocuments').outputs.body)}",
		    "type": "String"
		},

```

Le paramètre emailBody concatène le nombre de documents renvoyés par la requête, qui peut être « 0 » ou plus, avec « enregistrement(s) trouvé(s) ». Le reste des paramètres sont définis dans les paramètres du déclencheur.

Cette action dépend de l’action **GetDocuments**.

#### Opérations

##### Demande
```JSON

	{
	    "uri": "https://api.sendgrid.com/api/mail.send.json",
	    "method": "POST",
	    "headers": {
		"Content-type": "application/x-www-form-urlencoded"
	    },
	    "body": "api_user=azureuser@azure.com&api_key=Biz@Talk&from=user@msn.com&to=XXXX@XXXX.com&subject=New Patients&text=37 Documents Found"
	}

```

##### Response

```JSON

	{
	    "statusCode": 200,
	    "headers": {
		"connection": "keep-alive",
		"x-Frame-Options": "DENY,DENY",
		"access-Control-Allow-Origin": "https://sendgrid.com",
		"date": "Fri, 26 Feb 2016 15:47:35 GMT",
		"server": "nginx"
	    },
	    "body": {
		"message": "success"
	    }
	}
```

Enfin, vous souhaitez être en mesure de voir les résultats de votre application logique sur le portail Azure. Pour ce faire, vous ajoutez un paramètre à la section des sorties.


```JSON

	"outputs": {
		"Results": {
		    "type": "String",
		    "value": "@{int(length(actions('QueryDocuments').outputs.body))} Records Found"
		}

```

Cela permet de renvoyer la même valeur que celle qui est envoyée dans le corps du message électronique. La figure suivante illustre l’exemple : « 29 enregistrement(s) trouvé(s) ».

![Résultats](./media/documentdb-change-notification/logic-app-run.png)

## Mesures
Vous pouvez configurer la surveillance de l’application logique principale dans le portail. Cela vous permet de visualiser l’événement d’exécution de la latence et d’autres événements, comme indiqué dans la figure suivante.

![](./media/documentdb-change-notification/metrics.png)

## Déclencheur DocDb

Cette application logique est le déclencheur qui démarre le flux de travail sur votre application logique principale.

La figure suivante illustre le mode Création.

![](./media/documentdb-change-notification/trigger-recurrence.png)

```JSON

	{
	    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
	    "actions": {
		"Http": {
		    "conditions": [],
		    "inputs": {
			"body": {
			    "EmailTo": "XXXXXX@XXXXX.net",
			    "GetUtcDate_HoursBack": "24",
			    "Subject": "New Patients",
			    "sendgridPassword": "********",
			    "sendgridUsername": "azureuser@azure.com"
			},
			"method": "POST",
			"uri": "https://prod-01.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=ObTlihr529ATIuvuG-dhxOgBL4JZjItrvPQ8PV6973c"
		    },
		    "type": "Http"
		}
	    },
	    "contentVersion": "1.0.0.0",
	    "outputs": {
		"Results": {
		    "type": "String",
		    "value": "@{body('Http')['status']}"
		}
	    },
	    "parameters": {},
	    "triggers": {
		"recurrence": {
		    "recurrence": {
			"frequency": "Hour",
			"interval": 24
		    },
		    "type": "Recurrence"
		}
	    }
	}

```

Le déclencheur est défini pour une périodicité de vingt-quatre heures. L’action est une requête HTTP POST qui utilise l’URL de rappel de l’application logique principale. Le corps contient les paramètres qui sont spécifiés dans le schéma JSON.

#### Opérations

##### Demande

```JSON

	{
	    "uri": "https://prod-01.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=ObTlihr529ATIuvuG-dhxOgBL4JZjItrvPQ8PV6973c",
	    "method": "POST",
	    "body": {
		"EmailTo": "XXXXXX@XXXXX.net",
		"GetUtcDate_HoursBack": "24",
		"Subject": "New Patients",
		"sendgridPassword": "********",
		"sendgridUsername": "azureuser@azure.com"
	    }
	}

```

##### Response

```JSON

	{
	    "statusCode": 202,
	    "headers": {
		"pragma": "no-cache",
		"x-ms-ratelimit-remaining-workflow-writes": "7486",
		"x-ms-ratelimit-burst-remaining-workflow-writes": "1248",
		"x-ms-request-id": "westus:2d440a39-8ba5-4a9c-92a6-f959b8d2357f",
		"cache-Control": "no-cache",
		"date": "Thu, 25 Feb 2016 21:01:06 GMT"
	    }
	}
```

Examinons maintenant l’application API.

## DocDBNotificationApi

Bien qu’il existe plusieurs opérations dans l’application, vous souhaitez uniquement en utiliser trois.

* GetUtcDate
* ConvertToTimeStamp
* QueryForNewPatientDocuments

### DocDBNotificationApi Operations
Commençons par examiner la documentation Swagger.

> [AZURE.NOTE] Pour vous permettre d’appeler les opérations en externe, vous devez ajouter une valeur d’origine CORS autorisée de « * » (sans guillemets) dans les paramètres de votre application API, comme illustré dans la figure suivante.

![Configuration de Cors](./media/documentdb-change-notification/cors.png)

#### GetUtcDate

![G](./media/documentdb-change-notification/getutcdateswagger.png)

#### ConvertToTimeStamp

![Obtenir la date UTC](./media/documentdb-change-notification/converion-swagger.png)

#### QueryForNewPatientDocuments

![Interroger](./media/documentdb-change-notification/patientswagger.png)

Examinons le code de cette opération.

#### GetUtcDate

```C#

    /// <summary>
	/// Gets the current UTC Date value
	/// </summary>
	/// <returns></returns>
	[H ttpGet]
	[Metadata("GetUtcDate", "Gets the current UTC Date value minus the Hours Back")]
	[SwaggerOperation("GetUtcDate")]
	[SwaggerResponse(HttpStatusCode.OK, type: typeof (string))]
	[SwaggerResponse(HttpStatusCode.InternalServerError, "Internal Server Operation Error")]
	public string GetUtcDate(
	   [Metadata("Hours Back", "How many hours back from the current Date Time")] int hoursBack)
	{


	    return DateTime.UtcNow.AddHours(-hoursBack).ToString("r");
	}
```

Cette opération renvoie simplement le retour de la date/heure UTC actuelle moins la valeur HoursBack.

#### ConvertToTimeStamp

``` C#

        /// <summary>
        ///     Converts DateTime to double
        /// </summary>
        /// <param name="currentdateTime"></param>
        /// <returns></returns>
        [Metadata("Converts Universal DateTime to number")]
        [SwaggerResponse(HttpStatusCode.OK, null, typeof (double))]
        [SwaggerResponse(HttpStatusCode.BadRequest, "DateTime is invalid")]
        [SwaggerResponse(HttpStatusCode.InternalServerError)]
        [SwaggerOperation(nameof(ConvertToTimestamp))]
        public double ConvertToTimestamp(
            [Metadata("currentdateTime", "DateTime value to convert")] string currentdateTime)
        {
            double result;

            try
            {
                var uncoded = HttpContext.Current.Server.UrlDecode(currentdateTime);

                var newDateTime = DateTime.Parse(uncoded);
                //create Timespan by subtracting the value provided from the Unix Epoch
                var span = newDateTime - new DateTime(1970, 1, 1, 0, 0, 0, 0).ToLocalTime();

                //return the total seconds (which is a UNIX timestamp)
                result = span.TotalSeconds;
            }
            catch (Exception e)
            {
                throw new Exception("unable to convert to Timestamp", e.InnerException);
            }

            return result;
        }

```

Cette opération convertit la réponse de l’opération GetUtcDate en valeur double.

#### QueryForNewPatientDocuments

```C#

	    /// <summary>
        ///     Query for new Patient Documents
        /// </summary>
        /// <param name="unixTimeStamp"></param>
        /// <returns>IList</returns>
        [Metadata("QueryForNewDocuments",
            "Query for new Documents where the Timestamp is greater than or equal to the DateTime value in the query parameters."
            )]
        [SwaggerOperation("QueryForNewDocuments")]
        [SwaggerResponse(HttpStatusCode.OK, type: typeof (Task<IList<Document>>))]
        [SwaggerResponse(HttpStatusCode.BadRequest, "The syntax of the SQL Statement is incorrect")]
        [SwaggerResponse(HttpStatusCode.NotFound, "No Documents were found")]
        [SwaggerResponse(HttpStatusCode.InternalServerError, "Internal Server Operation Error")]
        // ReSharper disable once ConsiderUsingAsyncSuffix
        public IList<Document> QueryForNewPatientDocuments(
            [Metadata("UnixTimeStamp", "The DateTime value used to search from")] double unixTimeStamp)
        {
            var context = new DocumentDbContext();
            var filterQuery = string.Format(InvariantCulture, "SELECT * FROM Patient p WHERE p._ts >=  {0}",
                unixTimeStamp);
            var options = new FeedOptions {MaxItemCount = -1};


            var collectionLink = UriFactory.CreateDocumentCollectionUri(DocumentDbContext.DatabaseId,
                DocumentDbContext.CollectionId);

            var response =
                context.Client.CreateDocumentQuery<Document>(collectionLink, filterQuery, options).AsEnumerable();

            return response.ToList();
	}

```

Cette opération utilise le [Kit de développement logiciel (SDK) DocumentDB .NET](documentdb-sdk-dotnet.md) pour créer une requête de document.

```C#
     CreateDocumentQuery<Document>(collectionLink, filterQuery, options).AsEnumerable();
```

La réponse de l’opération ConvertToTimeStamp (unixTimeStamp) est transmise. L’opération renvoie une liste de documents, `IList<Document>`.

Nous avons mentionné précédemment CallbackURL. Pour démarrer le flux de travail dans votre application logique principale, vous devez l’appeler à l’aide de CallbackURL.

## CallbackURL

Pour commencer, vous avez besoin de votre jeton Azure AD. Il peut être difficile d’obtenir ce jeton. Je recherche une méthode simple et Jeff Hollan, qui est un responsable de programme d’application logique Azure, a recommandé d’utiliser [armclient](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) dans PowerShell. Vous pouvez l’installer en suivant les instructions fournies.

Les opérations que vous souhaitez utiliser sont la connexion et l’appel de l’API ARM.
 
Connexion : vous utilisez les mêmes informations d’identification pour vous connecter au portail Azure.

L’opération d’appel de l’Api ARM consiste à générer votre CallBackURL.

Dans PowerShell, vous l’appelez comme suit :

```powershell

	ArmClient.exe post https://management.azure.com/subscriptions/[YOUR SUBSCRIPTION ID/resourcegroups/[YOUR RESOURCE GROUP]/providers/Microsoft.Logic/workflows/[YOUR LOGIC APP NAME/triggers/manual/listcallbackurl?api-version=2015-08-01-preview

```

Le résultat doit avoir l’aspect suivant :

```powershell

	https://prod-02.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-prevaiew&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=XXXXXXXXXXXXXXXXXXX

```

Vous pouvez utiliser un outil tel que [postman](http://www.getpostman.com/) pour tester votre application logique principale, comme indiqué dans l’illustration suivante.

![Postman](./media/documentdb-change-notification/newpostman.png)

Le tableau suivant répertorie les paramètres de déclencheur qui constituent le corps de l’application logique du déclencheur DocDB.

Paramètre | Description 
--- | --- 
GetUtcDate\_HoursBack | Permet de définir le nombre d’heures pour la date de début de recherche
sendgridUsername | Permet de définir le nombre d’heures pour la date de début de recherche
sendgridPassword | Nom d’utilisateur pour le courrier électronique SendGrid
EmailTo | Adresse de messagerie qui recevra la notification par courrier électronique
Objet | Objet du courrier électronique

## Affichage des données du patient dans le service BLOB Azure

Accédez à votre compte Azure Storage, puis sélectionnez les objets blob sous les services, comme indiqué dans l’illustration suivante.

![Compte de stockage](./media/documentdb-change-notification/docdbstorageaccount.png)

Vous pouvez afficher les informations de fichier d’objets blob Patient, comme indiqué ci-dessous.

![Service d'objets blob](./media/documentdb-change-notification/blobservice.png)


## Résumé

Dans cette procédure pas à pas, vous avez appris les éléments suivants :

* Il est possible d’implémenter les notifications dans DocumentDB.
* À l’aide de Logic Apps, vous pouvez automatiser le processus.
* À l’aide de Logic Apps, vous pouvez réduire le temps nécessaire pour fournir une application.
* À l’aide de HTTP, vous pouvez facilement utiliser une application API dans une application logique.
* Vous pouvez facilement créer un CallBackURL qui remplace l’écouteur HTTP.
* Vous pouvez facilement créer des flux de travail personnalisés avec le concepteur Logic Apps.

Le secret consiste à prévoir et à modéliser votre flux de travail.

## Étapes suivantes
Veuillez télécharger et utiliser le code de l’application logique fourni sur [Github](https://github.com/HEDIDIN/DocDbNotifications). Je vous invite à effectuer la génération à partir de l’application et à envoyer les modifications apportées au dépôt.

Pour en savoir plus sur DocumentDB, consultez le [parcours d’apprentissage](https://azure.microsoft.com/documentation/learning-paths/documentdb/) associé.

<!---HONumber=AcomDC_0316_2016-->
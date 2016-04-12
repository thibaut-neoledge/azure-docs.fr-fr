<properties 
	pageTitle="Créer des définitions d’application logique | Microsoft Azure" 
	description="Apprenez à écrire la définition JSON pour les applications logiques" 
	authors="stepsic-microsoft-com" 
	manager="erikre" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/16/2016"
	ms.author="stepsic"/>
	
# Créer des définitions d'application logique
Cette rubrique montre comment utiliser les définitions des [applications logiques App Services](app-service-logic-what-are-logic-apps.md), qui constituent un langage JSON déclaratif simple. Si nécessaire, commencez par consulter [comment créer une application logique](app-service-logic-create-a-logic-app.md). Vous pouvez également lire les [documents de référence complets du langage de définition sur MSDN](https://msdn.microsoft.com/library/azure/mt643789.aspx).

## Plusieurs étapes qui se répètent dans une liste

Il est courant d’avoir une étape qui obtient une liste d’éléments, puis une série de deux actions ou plus à effectuer pour chaque élément de la liste :

![Répétition sur des listes](./media/app-service-logic-author-definitions/newrepeatoverlists.png)

![Répétition sur des listes](./media/app-service-logic-author-definitions/newrepeatoverlists2.png)

![Répétition sur des listes](./media/app-service-logic-author-definitions/newrepeatoverlists3.png)

![Répétition sur des listes](./media/app-service-logic-author-definitions/newrepeatoverlists4.png)

 
Dans cet exemple, il existe 3 actions :

1. Obtenir une liste d'articles. Ceci renvoie un objet qui contient un tableau.

2. Une action qui accède à une propriété de lien sur chaque article, qui retourne l’emplacement réel de l’article.

3. Une action qui effectue une itération sur tous les résultats de la seconde action pour télécharger les articles réels.

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"getArticles": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
			}
		},
		"readLinks": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "@item().link"
			},
			"forEach": "@body('getArticles').responseData.feed.entries"
		},
		"downloadLinks": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "@item().outputs.headers.location"
			},
			"conditions": [{
				"expression": "@not(equals(actions('readLinks').status, 'Skipped'))"
			}],
			"forEach": "@actions('readLinks').outputs"
		}
	},
	"outputs": {}
}
```

Comme indiqué dans la section [Utiliser les fonctionnalités des applications logiques](app-service-logic-use-logic-app-features.md), vous effectuez une itération sur la première liste à l'aide de la propriété `forEach:` sur la deuxième action. Toutefois, pour la troisième action, vous devez sélectionner la propriété `@actions('readLinks').outputs`, étant donné que la seconde est exécutée pour chaque article.

Dans l’action, vous pouvez utiliser la fonction [`item()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#item). Dans cet exemple, je voulais obtenir l’en-tête `location`. J’ai donc utilisé la fonction `@item().outputs.headers` pour obtenir les sorties de l’exécution de l’action à partir de la seconde action sur laquelle nous effectuons une itération.

## Mappage d'éléments dans une liste à une autre configuration

Ensuite, supposons que nous voulions obtenir du contenu complètement différent selon une valeur d'une propriété. Nous pouvons créer un mappage des valeurs aux destinations en tant que paramètre :

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"specialCategories": {
			"defaultValue": ["science", "google", "microsoft", "robots", "NSA"],
			"type": "Array"
		},
		"destinationMap": {
			"defaultValue": {
				"science": "http://www.nasa.gov",
				"microsoft": "https://www.microsoft.com/fr-FR/default.aspx",
				"google": "https://www.google.com",
				"robots": "https://en.wikipedia.org/wiki/Robot",
				"NSA": "https://www.nsa.gov/"
			},
			"type": "Object"
		}
	},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"getArticles": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
			},
			"conditions": []
		},
		"getSpecialPage": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
			},
			"conditions": [{
				"expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)"
			}],
			"forEach": "@body('getArticles').responseData.feed.entries"
		}
	}
}
```

Dans ce cas, nous obtenons d'abord une liste d'articles, puis la deuxième étape recherche dans un mappage, en fonction de la catégorie définie en tant que paramètre, l'URL à partir de laquelle obtenir le contenu.

Il existe deux éléments auxquels il faut prêter attention : la fonction [`intersection()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection) est utilisée pour vérifier si la catégorie correspond à l'une des catégories connues définies. Ensuite, une fois que nous obtenons la catégorie, nous pouvons extraire l'élément de la carte à l'aide de crochets : `parameters[...]`.

## Associer/imbriquer les applications logiques lors de la répétition sur une liste

Souvent, il peut être plus facile de gérer vos applications logiques lorsqu'elles sont plus discrètes. Pour ce faire, vous pouvez factoriser votre logique dans plusieurs définitions et les appeler à partir de la même définition parent. Dans cet exemple, une application logique parent reçoit des commandes et une application logique enfant exécute des étapes pour chaque commande.

Dans l'application logique parent :

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"orders": {
			"defaultValue": [{
				"quantity": 10,
				"id": "myorder1"
			}, {
				"quantity": 200,
				"id": "specialOrder"
			}, {
				"quantity": 5,
				"id": "myOtherOrder"
			}],
			"type": "Array"
		}
	},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"iterateOverOrders": {
			"type": "Workflow",
			"inputs": {
				"uri": "https://westus.logic.azure.com/subscriptions/xxxxxx-xxxxx-xxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Logic/workflows/xxxxxxx",
				"apiVersion": "2015-02-01-preview",
				"trigger": {
					"name": "submitOrder",
					"outputs": {
						"body": "@item()"
					}
				},
				"authentication": {
					"type": "Basic",
					"username": "default",
					"password": "xxxxxxxxxxxxxx"
				}
			},
			"forEach": "@parameters('orders')"
		},
		"sendInvoices": {
			"type": "Http",
			"inputs": {
				"uri": "http://www.example.com/?invoiceID=@{item().outputs.run.outputs.deliverTime.value}",
				"method": "GET"
			},
			"forEach": "@outputs('iterateOverOrders')"
		}
	},
	"outputs": {}
}
```

Puis, dans l'application logique enfant, vous utiliserez la fonction [`triggerBody()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#triggerBody) pour obtenir les valeurs qui ont été transmises dans le workflow enfant. Vous remplirez ensuite les sorties avec les données que vous souhaitez renvoyer au flux parent.

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"calulatePrice": {
			"type": "Http",
			"inputs": {
				"method": "POST",
				"uri": "http://www.example.com/?action=calcPrice&id=@{triggerBody().id}&qty=@{triggerBody().quantity}"
			}
		},
		"calculateDeliveryTime": {
			"type": "Http",
			"inputs": {
				"method": "POST",
				"uri": "http://www.example.com/?action=calcTime&id=@{triggerBody().id}&qty=@{triggerBody().quantity}"
			}
		}
	},
	"outputs": {
		"deliverTime": {
			"type": "String",
			"value": "@outputs('calculateDeliveryTime').headers.etag"
		}
	}
}
```

Vous pouvez en savoir plus sur l'[action de type application logique sur MSDN](https://msdn.microsoft.com/library/azure/mt643939.aspx).

>[AZURE.NOTE]Le concepteur d'applications logiques ne prend pas en charge les actions de type application logique, donc vous devrez modifier la définition manuellement.


## Une étape de gestion des erreurs en cas de problème

En général, vous voulez être en mesure d’écrire une *étape de correction*, une logique qui s’exécute, si, **et seulement si**, un ou plusieurs de vos appels échouent. Dans cet exemple, nous obtenons des données à partir de différents emplacements mais, si l’appel échoue, je veux PUBLIER un message quelque part afin de pouvoir pister cet échec ultérieurement :

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"dataFeeds": {
			"defaultValue": ["https://www.microsoft.com/fr-FR/default.aspx", "https://gibberish.gibberish/"],
			"type": "Array"
		}
	},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"readData": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "@item()"
			},
			"forEach": "@parameters('dataFeeds')"
		},
		"postToErrorMessageQueue": {
			"type": "Http",
			"inputs": {
				"method": "POST",
				"uri": "http://www.example.com/?noteAnErrorFor=@{item().inputs.uri}"
			},
			"conditions": [{
				"expression": "@equals(actions('readData').status, 'Failed')"
			}, {
				"expression": "@equals(item().status, 'Failed')"
			}],
			"forEach": "@actions('readData').outputs"
		}
	},
	"outputs": {}
}
```

J'utilise deux conditions car dans la première étape, j'effectue la répétition sur une liste. Si n'avez qu'une seule action, vous n'avez besoin que d'une seule condition (la première). Notez également que vous pouvez utiliser les *entrées* de l’action qui a échoué dans l’étape de correction ; ici, je transmets l’URL ayant échoué à la deuxième étape :

![Correction](./media/app-service-logic-author-definitions/remediation.png)

Enfin, étant donné que vous avez désormais géré l'erreur, nous n'indiquons plus l'exécution en tant qu'**Échec**. Comme vous pouvez le voir, cette exécution est indiquée comme **Réussie** même si une étape a échoué, car j'ai écrit l'étape de façon à ce qu'elle gère cet échec.

## Deux étapes (ou plus) qui s’exécutent en parallèle

Pour exécuter plusieurs actions en parallèle, plutôt que dans une séquence, vous devez supprimer la condition `dependsOn` qui relie ces deux actions. Une fois que la dépendance est supprimée, les actions s'exécutent automatiquement en parallèle, sauf si elles ont besoin de données entre elles.

![Branches](./media/app-service-logic-author-definitions/branches.png)

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"dataFeeds": {
			"defaultValue": ["https://www.microsoft.com/fr-FR/default.aspx", "https://office.live.com/start/default.aspx"],
			"type": "Array"
		}
	},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"readData": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "@item()"
			},
			"forEach": "@parameters('dataFeeds')"
		},
		"branch1": {
			"type": "Http",
			"inputs": {
				"method": "POST",
				"uri": "http://www.example.com/?branch1Logic=@{item().inputs.uri}"
			},
			"forEach": "@actions('readData').outputs"
		},
		"branch2": {
			"type": "Http",
			"inputs": {
				"method": "POST",
				"uri": "http://www.example.com/?branch2Logic=@{item().inputs.uri}"
			},
			"forEach": "@actions('readData').outputs"
		}
	},
	"outputs": {}
}
```

Comme vous pouvez le voir dans l'exemple ci-dessus, branch1 et branch2 dépendent simplement du contenu de readData. Par conséquent, ces deux branches seront exécutées en parallèle :

![Parallèle](./media/app-service-logic-author-definitions/parallel.png)

Vous pouvez voir que l'horodatage pour les deux branches est identique.

## Joindre deux branches conditionnelles de logique

Vous pouvez combiner deux flux conditionnels de logique (qui ont peut-être été exécutés ou pas) à l'aide d'une seule action qui récupère les données des deux branches.

Votre stratégie pour cela varie selon que vous gérez un élément ou une collection d'éléments. Dans le cas d'un seul élément, vous devrez utiliser la fonction [`coalesce()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#coalesce) :

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"order": {
			"defaultValue": {
				"quantity": 10,
				"id": "myorder1"
			},
			"type": "Object"
		}
	},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"handleNormalOrders": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "http://www.example.com/?orderNormally=@{parameters('order').id}"
			},
			"conditions": [{
				"expression": "@lessOrEquals(parameters('order').quantity, 100)"
			}]
		},
		"handleSpecialOrders": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "http://www.example.com/?orderSpecially=@{parameters('order').id}"
			},
			"conditions": [{
				"expression": "@greater(parameters('order').quantity, 100)"
			}]
		},
		"submitInvoice": {
			"type": "Http",
			"inputs": {
				"method": "POST",
				"uri": "http://www.example.com/?invoice=@{coalesce(outputs('handleNormalOrders')?.headers?.etag,outputs('handleSpecialOrders')?.headers?.etag )}"
			},
			"conditions": [{
				"expression": "@or(equals(actions('handleNormalOrders').status, 'Succeeded'), equals(actions('handleSpecialOrders').status, 'Succeeded'))"
			}]
		}
	},
	"outputs": {}
}
```
 
Ou, si vos deux premières branches fonctionnent sur une liste de commandes, par exemple, vous devrez utiliser la fonction [`union()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#union) pour combiner les données des deux branches.

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"orders": {
			"defaultValue": [{
				"quantity": 10,
				"id": "myorder1"
			}, {
				"quantity": 200,
				"id": "specialOrder"
			}, {
				"quantity": 5,
				"id": "myOtherOrder"
			}],
			"type": "Array"
		}
	},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"handleNormalOrders": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "http://www.example.com/?orderNormally=@{item().id}"
			},
			"conditions": [{
				"expression": "@lessOrEquals(item().quantity, 100)"
			}],
			"forEach": "@parameters('orders')"
		},
		"handleSpecialOrders": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "http://www.example.com/?orderSpecially=@{item().id}"
			},
			"conditions": [{
				"expression": "@greater(item().quantity, 100)"
			}],
			"forEach": "@parameters('orders')"
		},
		"submitInvoice": {
			"type": "Http",
			"inputs": {
				"method": "POST",
				"uri": "http://www.example.com/?invoice=@{item().outputs.headers.etag}"
			},
			"conditions": [{
				"expression": "@equals(item().status, 'Succeeded')"
			}],
			"forEach": "@union(actions('handleNormalOrders').outputs, actions('handleSpecialOrders').outputs)"
		}
	},
	"outputs": {}
}
```
## Utilisation des chaînes

Il existe diverses fonctions qui peuvent être utilisées pour manipuler les chaînes. Prenons l'exemple où nous avons une chaîne à transmettre à un système, mais nous ne sommes pas certains que l'encodage de caractères sera correctement géré. Une option consiste à encoder cette chaîne en base64. Toutefois, pour éviter la séquence d'échappement dans une URL, nous allons remplacer plusieurs caractères.

Nous voulons également une sous-chaîne du nom de la commande, car les 5 premiers caractères ne sont pas utilisés.

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"order": {
			"defaultValue": {
				"quantity": 10,
				"id": "myorder1",
				"orderer": "NAME=Stèphén__Šīçiłianö"
			},
			"type": "Object"
		}
	},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"order": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').orderer,5,sub(length(parameters('order').orderer), 5) )),'+','-') ,'/' ,'_' )}"
			}
		}
	},
	"outputs": {}
}
```

De l'intérieur vers l'extérieur, voici ce que l'on a :

1. Obtenez la [`length()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#length) du nom de la commande, ceci renvoie le nombre total de caractères

2. Soustrayez 5 (car nous voulons une chaîne plus courte)

3. Prenez la [`substring()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#substring). Nous commençons à l'index `5` et suivons le reste de la chaîne.

4. Convertissez cette sous-chaîne en une chaîne [`base64()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#base64)

5. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) tous les caractères `+` par `-`

6. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) tous les caractères `/` par `_`

## Utilisation des dates

Les dates peuvent être utiles, particulièrement lorsque vous tentez d'extraire des données à partir d'une source de données qui ne prend pas naturellement en charge les **déclencheurs**. Vous pouvez également utiliser les dates pour déterminer combien de temps différentes étapes prennent.

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"order": {
			"defaultValue": {
				"quantity": 10,
				"id": "myorder1"
			},
			"type": "Object"
		}
	},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"order": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "http://www.example.com/?id=@{parameters('order').id}"
			}
		},
		"timingWarning": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
			},
			"conditions": [{
				"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))"
			}]
		}
	},
	"outputs": {}
}
```

Dans cet exemple, nous extrayons la `startTime` de l'étape précédente. Puis, nous sommes obtenons l'heure actuelle et soustrayons une seconde :[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds) (vous pouvez utiliser d'autres unités de temps, comme `minutes` ou `hours`). Enfin, nous pouvons comparer ces deux valeurs. Si la première est inférieure à la seconde, alors cela signifie que plus d'une seconde s'est écoulée depuis que la commande a été placée.

Notez également que nous pouvons utiliser des formateurs de chaîne pour formater les dates : dans la chaîne de requête j'utilise [`utcnow('r')`](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow) pour obtenir RFC1123. Le formatage des dates [est décrit sur MSDN](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow).

## Transmission de valeurs à l'exécution pour modifier le comportement

Imaginons que vous disposez de comportements différents que vous souhaitez exécuter en fonction d'une valeur que vous utilisez pour lancer votre application logique. Vous pouvez utiliser la fonction [`triggerOutputs()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#triggerOutputs) pour extraire ces valeurs de ce que vous avez transmis :

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"readData": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "@triggerOutputs().uriToGet"
			}
		},
		"extraStep": {
			"type": "Http",
			"inputs": {
				"method": "POST",
				"uri": "http://www.example.com/extraStep"
			},
			"conditions": [{
				"expression": "@triggerOutputs().doMoreLogic"
			}]
		}
	},
	"outputs": {}
}
```

Pour que cela fonctionne, vous devez transmettre les propriétés souhaitées (dans l'exemple ci-dessus `uriToGet` et `doMoreLogic`) lorsque vous démarrez l'exécution.

Avec la charge utile suivante. Notez que vous avez fourni l'application logique avec les valeurs à utiliser maintenant :

```
{
    "outputs": {
        "uriToGet" : "http://my.uri.I.want/",
        "doMoreLogic" : true
    }
}
``` 

Lors de l'exécution de cette application logique, elle appellera l'URI que j'ai transmise et exécutera cette étape supplémentaire, car j'ai transmis `true`. Si vous ne souhaitez varier les paramètres qu'au moment du déploiement (pas pour *chaque exécution*), alors vous devez utiliser les `parameters` comme ceux appelés ci-dessous.

## Utilisation de paramètres au moment du déploiement pour des environnements différents

Il est courant d'avoir un cycle de vie de déploiement où vous avez un environnement de développement, un environnement intermédiaire et un environnement de production. Dans tous ces environnements, vous pouvez vouloir la même définition, tout en utilisant des bases de données différentes, par exemple. De même, vous pouvez vouloir utiliser la même définition dans plusieurs régions pour une disponibilité élevée, tout en voulant que chaque instance de l'application logique communique avec la base de données de cette région.

Notez que ceci diffère de la prise de paramètres différents au moment de l'*exécution* ; pour cela, vous devez utiliser la fonction `trigger()` comme indiqué ci-dessus.

Vous pouvez commencer avec une définition très simple comme celle-ci :

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"connection": {
			"type": "string"
		}
	},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"readData": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "@parameters('connection')"
			}
		}
	},
	"outputs": {}
}
```

Ensuite, dans la requête `PUT` réelle pour l'application logique, vous pouvez fournir le paramètre `connection`. Comme il n'y a plus de valeur par défaut, ce paramètre est obligatoire dans la charge utile d'application logique :

```
{
    "properties": {
        "sku": {
            "name": "Premium",
            "plan": {
                "id": "/subscriptions/xxxxx/resourceGroups/xxxxxx/providers/Microsoft.Web/serverFarms/xxxxxx"
            }
        },
        "definition": {
          // Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

Dans chaque environnement, vous pouvez ensuite fournir une valeur différente pour le paramètre `connection`.

## Exécution d’une étape jusqu’à ce qu’une condition soit remplie

Dans le cas d’une API que vous appelez, vous pouvez souhaiter une réponse avant de continuer. Par exemple, imaginez que vous souhaitez attendre que quelqu’un télécharge un fichier dans un répertoire avant de traiter le fichier. Vous pouvez dans ce cas utiliser la syntaxe *do-until* :

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"http0": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "http://mydomain/listfiles"
			},
			"until": {
				"limit": {
					"timeout": "PT10M"
				},
				"conditions": [{
					"expression": "@greater(length(action().outputs.body),0)"
				}]
			}
		}
	},
	"outputs": {}
}
```

Consultez la [documentation sur l'API REST](https://msdn.microsoft.com/library/azure/mt643787.aspx) pour connaître toutes les options dont vous disposez pour créer et gérer des applications logiques.

<!---HONumber=AcomDC_0323_2016-->
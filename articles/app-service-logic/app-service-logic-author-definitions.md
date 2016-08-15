<properties 
	pageTitle="Créer des définitions d’application logique | Microsoft Azure" 
	description="Apprenez à écrire la définition JSON pour les applications logiques" 
	authors="jeffhollan" 
	manager="erikre" 
	editor="" 
	services="logic-apps" 
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/25/2016"
	ms.author="jehollan"/>
	
# Créer des définitions d'application logique
Cette rubrique montre comment utiliser les définitions [Azure Logic Apps](app-service-logic-what-are-logic-apps.md), qui constituent un langage JSON déclaratif simple. Si nécessaire, commencez par consulter [comment créer une application logique](app-service-logic-create-a-logic-app.md). Vous pouvez également lire les [documents de référence complets du langage de définition sur MSDN](http://aka.ms/logicappsdocs).

## Plusieurs étapes qui se répètent dans une liste

Vous pouvez utiliser le [type foreach](app-service-logic-loops-and-scopes.md) pour répéter des éléments sur un tableau pouvant comprendre 10 000 éléments et effectuer une action pour chacun d’eux.

## Une étape de gestion des erreurs en cas de problème

En général, vous voulez être en mesure d’écrire une *étape de correction*, une logique qui s’exécute, si, **et seulement si**, un ou plusieurs de vos appels échouent. Dans cet exemple, nous obtenons des données à partir de différents emplacements mais, si l’appel échoue, je veux PUBLIER un message quelque part afin de pouvoir pister cet échec ultérieurement :

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
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
				"uri": "http://myurl"
			}
		},
		"postToErrorMessageQueue": {
			"type": "ApiConnection",
			"inputs": "...",
			"runAfter": {
				"readData": ["Failed"]
			}
		}
	},
	"outputs": {}
}
```

Vous pouvez rendre utiliser la propriété `runAfter` pour indiquer que l’élément `postToErrorMessageQueue` doit s’exécuter uniquement lorsque `readData` est à l’état **d’échec**. Il peut s’agir également d’une liste de valeurs possibles, ce qui signifie que `runAfter` peut avoir la valeur `["Succeeded", "Failed"]`.

Enfin, étant donné que vous avez désormais géré l'erreur, nous n'indiquons plus l'exécution en tant qu'**Échec**. Comme vous pouvez le voir, cette exécution est indiquée comme **Réussie** même si une étape a échoué, car j'ai écrit l'étape de façon à ce qu'elle gère cet échec.

## Deux étapes (ou plus) qui s’exécutent en parallèle

Pour permettre l’exécution de plusieurs actions en parallèle, la propriété `runAfter` doit être équivalente à l’exécution.

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {},
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
				"uri": "http://myurl"
			}
		},
		"branch1": {
			"type": "Http",
			"inputs": "...",
			"runAfter": {
				"readData": ["Succeeded"]
			}
		},
		"branch2": {
			"type": "Http",
			"inputs": "...",
			"runAfter": {
				"readData": ["Succeeded"]
			}
		}
	},
	"outputs": {}
}
```

Comme vous pouvez le voir dans l’exemple ci-dessus, `branch1` et `branch2` sont définis pour s’exécuter après `readData`. Par conséquent, ces deux branches seront exécutées en parallèle :

![Parallèle](./media/app-service-logic-author-definitions/parallel.png)

Vous pouvez voir que l'horodatage pour les deux branches est identique.

## Joindre deux branches parallèles

Vous pouvez joindre deux actions qui ont été définies pour s’exécuter en parallèle en ajoutant des éléments à la propriété `runAfter` comme indiqué ci-dessus.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-04-01-preview/workflowdefinition.json#",
    "actions": {
        "readData": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        },
        "branch1": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "branch2": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "join": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "branch1": [
                    "Succeeded"
                ],
                "branch2": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        }
    },
    "contentVersion": "1.0.0.0",
    "outputs": {},
    "parameters": {},
    "triggers": {
        "manual": {
            "inputs": {
                "schema": {}
            },
            "kind": "Http",
            "type": "Request"
        }
    }
}
```

![Parallèle](./media/app-service-logic-author-definitions/join.png)

## Mappage d'éléments dans une liste à une autre configuration

Ensuite, supposons que nous voulions obtenir du contenu complètement différent selon une valeur d'une propriété. Nous pouvons créer un mappage des valeurs aux destinations en tant que paramètre :

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
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

Il existe deux éléments auxquels il faut prêter attention : la fonction [`intersection()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection) est utilisée pour vérifier si la catégorie correspond à l'une des catégories connues définies. Ensuite, une fois que nous obtenons la catégorie, nous pouvons extraire l'élément de la carte à l'aide de crochets : `parameters[...]`.

## Utilisation des chaînes

Il existe diverses fonctions qui peuvent être utilisées pour manipuler les chaînes. Prenons l'exemple où nous avons une chaîne à transmettre à un système, mais nous ne sommes pas certains que l'encodage de caractères sera correctement géré. Une option consiste à encoder cette chaîne en base64. Toutefois, pour éviter la séquence d'échappement dans une URL, nous allons remplacer plusieurs caractères.

Nous voulons également une sous-chaîne du nom de la commande, car les 5 premiers caractères ne sont pas utilisés.

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
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

De l'intérieur vers l'extérieur, voici ce que l'on a :

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
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
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
			"actions" {
				"type": "Http",
				"inputs": {
					"method": "GET",
					"uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
				},
				"runAfter": {}
			}
			"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))"
		}
	},
	"outputs": {}
}
```

Dans cet exemple, nous extrayons la `startTime` de l'étape précédente. Puis, nous sommes obtenons l'heure actuelle et soustrayons une seconde :[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds) (vous pouvez utiliser d'autres unités de temps, comme `minutes` ou `hours`). Enfin, nous pouvons comparer ces deux valeurs. Si la première est inférieure à la seconde, alors cela signifie que plus d'une seconde s'est écoulée depuis que la commande a été placée.

Notez également que nous pouvons utiliser des formateurs de chaîne pour formater les dates : dans la chaîne de requête j'utilise [`utcnow('r')`](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow) pour obtenir RFC1123. Le formatage des dates [est décrit sur MSDN](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow).

## Utilisation de paramètres au moment du déploiement pour des environnements différents

Il est courant d'avoir un cycle de vie de déploiement où vous avez un environnement de développement, un environnement intermédiaire et un environnement de production. Dans tous ces environnements, vous pouvez vouloir la même définition, tout en utilisant des bases de données différentes, par exemple. De même, vous pouvez vouloir utiliser la même définition dans plusieurs régions pour une disponibilité élevée, tout en voulant que chaque instance de l'application logique communique avec la base de données de cette région.

Notez que ceci diffère de la prise de paramètres différents au moment de l'*exécution* ; pour cela, vous devez utiliser la fonction `trigger()` comme indiqué ci-dessus.

Vous pouvez commencer avec une définition très simple comme celle-ci :

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"uri": {
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
				"uri": "@parameters('uri')"
			}
		}
	},
	"outputs": {}
}
```

Ensuite, dans la requête `PUT` réelle pour l'application logique, vous pouvez fournir le paramètre `uri`. Comme il n'y a plus de valeur par défaut, ce paramètre est obligatoire dans la charge utile d'application logique :

```
{
    "properties": {},
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

Consultez la [documentation sur l'API REST](https://msdn.microsoft.com/library/azure/mt643787.aspx) pour connaître toutes les options dont vous disposez pour créer et gérer des applications logiques.

<!---HONumber=AcomDC_0803_2016-->
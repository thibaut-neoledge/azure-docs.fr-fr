<properties
   pageTitle="Boucles, étendues et décomposition dans Logic Apps| Microsoft Azure"
   description="Concepts de boucle, d’étendue et de décomposition dans les applications logiques"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/14/2016"
   ms.author="jehollan"/>
   
  # Boucles, étendues et décomposition dans Logic Apps
  
  >[AZURE.NOTE] Cette version de l’article s’applique au schéma 2016-04-01-preview et schémas ultérieurs de Logic Apps. Les concepts sont similaires pour les schémas antérieurs, mais les étendues sont uniquement disponibles pour ce schéma et les schémas ultérieurs.
  
  ## Boucle ForEach et tableaux
  
  Logic Apps vous permet de parcourir un jeu de données au moyen d’une boucle et d’effectuer une action pour chaque élément. Cette opération est possible grâce à l’action `foreach`. Dans le concepteur, vous pouvez choisir d’ajouter une boucle foreach. Après avoir sélectionné le tableau à parcourir, vous pouvez commencer à ajouter des actions. Actuellement, vous êtes limité à une seule action par boucle foreach, mais cette restriction sera levée dans les semaines à venir. Une fois dans la boucle, vous pouvez commencer à spécifier ce qui doit se produire au niveau de chaque valeur du tableau.
  
  Si vous utilisez le mode code, vous pouvez spécifier une boucle foreach comme ci-dessous. Dans cet exemple, une boucle foreach envoie un e-mail à chaque adresse contenant « microsoft.com » :
  
  ```
  {
      "forEach_email": {
          "type": "foreach",
          "foreach": "@triggerBody()['emails']",
          "expression": "@contains(item(), 'microsoft.com')",
          "actions": {
              "send_email": {
                  "type": "ApiConnection",
                  "inputs": {
                    "body": {
                        "to": "@item()",
                        "from": "me@contoso.com",
                        "message": "Hello, thank you for ordering"
                    }
                    "host": {
                        "connection": {
                            "id": "@parameters('$connections')['office365']['connection']['id']"
                        }
                    }
                  }
              }
          }
      }
  }
  ```
  
  Une action `foreach` peut parcourir des tableaux comportant jusqu’à 5 000 lignes. Chaque itération pouvant s’exécuter en parallèle, vous pouvez être amené à ajouter des messages à une file d’attente si un contrôle de flux est nécessaire.
  
  ## Boucle Until
  
  Vous pouvez effectuer une action ou une série d’actions jusqu’à ce qu’une condition soit remplie. Le scénario le plus courant consiste à appeler un point de terminaison jusqu’à obtention de la réponse recherchée. Dans le concepteur, vous pouvez choisir d’ajouter une boucle until. Après avoir ajouté des actions à l’intérieur de la boucle, vous pouvez définir la condition de sortie et les limites de la boucle. Il existe un délai de 1 minute entre les cycles de la boucle.
  
  Si vous utilisez le mode code, vous pouvez spécifier une boucle until comme ci-dessous. Dans cet exemple, un point de terminaison HTTP est appelé jusqu’à ce que le corps de la réponse ait la valeur « Completed ». La procédure se termine quand l’une des conditions suivantes est remplie :
  
  * L’état de la réponse HTTP est « Completed ».
  * La procédure a duré 1 heure.
  * Elle a effectué 100 cycles de boucle.
  
  ```
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed'),
        "limit": {
            "count": 100,
            "timeout": "PT1H"
        },
        "actions": {
            "create_resource": {
                "type": "http",
                "inputs": {
                    "url": "http://provisionRseource.com",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                }
            }
        }
      }
  }
  ```
  
## SplitOn et décomposition

Parfois, un déclencheur peut recevoir un tableau d’éléments à décomposer et à partir de chacun desquels vous souhaitez démarrer un flux de travail. Vous pouvez effectuer ces opérations à l’aide de la commande `spliton`. Par défaut, si votre swagger de déclencheur spécifie une charge utile qui est un tableau, un `spliton` est ajouté et lance une exécution par élément. SplitOn peut uniquement être ajouté à un déclencheur. Ce paramétrage peut être configuré ou remplacé manuellement dans la définition en mode code. Actuellement, SplitOn peut décomposer des tableaux qui comportent jusqu’à 5 000 éléments. Vous ne pouvez pas avoir un `spliton` et également implémenter le modèle de réponse synchrone. Tout flux de travail appelé qui a une action `response` en plus de `spliton` s’exécute de façon asynchrone et envoie une réponse `202 Accepted` immédiate.

SplitOn peut être spécifié en mode code, comme dans l’exemple suivant. Dans cet exemple, un tableau d’éléments est reçu et chacune de ses lignes fait l’objet d’une décomposition.

```
{
    "myDebatchTrigger": {
        "type": "Http",
        "inputs": {
            "url": "http://getNewCustomers",
        },
        "recurrence": {
            "frequencey": "Second",
            "interval": 15
        },
        "spliton": "@triggerBody()['rows']"
    }
}
```

## Étendues

Vous pouvez grouper une série d’actions à l’aide d’une étendue. Cette opération est particulièrement utile pour implémenter la gestion des exceptions. Dans le concepteur, vous pouvez ajouter une nouvelle étendue et commencer à y ajouter des actions. Vous pouvez définir des étendues en mode code comme suit :


```
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```

<!---HONumber=AcomDC_0525_2016-->
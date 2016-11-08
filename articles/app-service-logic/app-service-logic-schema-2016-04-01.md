---
title: Nouvelle version de schéma 2016-06-01 | Microsoft Docs
description: Découvrir comment écrire la définition JSON pour la dernière version des applications logiques
author: jeffhollan
manager: dwrede
editor: ''
services: logic-apps
documentationcenter: ''

ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: jehollan

---
# Nouvelle version de schéma 2016-06-01
Le nouveau schéma et la récente version de l’API pour les applications logiques ont bénéficié d’un certain nombre d’améliorations destinées à accroître la fiabilité et la simplicité d’utilisation des applications logiques. Voici les 3 principales différences :

1. Ajout d’étendues, qui sont des actions qui contiennent un ensemble d’actions.
2. Les conditions et les boucles sont des actions de première classe
3. L’exécution plus détaillée des classements via la propriété `runAfter` (qui remplace `dependsOn`)

Pour plus d’informations sur la mise à niveau de vos applications logiques à partir du schéma 2015-08-01-preview vers le schéma 2016-06-01, [consultez la section relative à la mise à niveau ci-dessous](#upgrading-to-2016-06-01-schema).

## 1\. Étendues
L’un des plus grands changements dans ce schéma est l’ajout d’étendues et la possibilité d’imbriquer les actions entre elles. Cela est utile lors du regroupement d’un ensemble d’actions, ou lorsque vous avez besoin d’imbriquer les actions entre elles (par exemple une condition peut en contenir une autre). Vous trouverez plus d’informations sur la syntaxe étendue [ici](app-service-logic-loops-and-scopes.md), mais vous trouverez un exemple simple d’étendue ci-dessous :

```
{
    "actions": {
        "My_Scope": {
            "type": "scope",
            "actions": {                
                "Http": {
                    "inputs": {
                        "method": "GET",
                        "uri": "http://www.bing.com"
                    },
                    "runAfter": {},
                    "type": "Http"
                }
            }
        }
    }
}
```

## 2\. Modifications des conditions et boucles
Dans les versions précédentes du schéma, les conditions et les boucles étaient des paramètres associés à une seule action. Cette limitation a été levée dans ce schéma et, à présent, les conditions et les boucles s’affichent en tant que type d’action. [Cet article](app-service-logic-loops-and-scopes.md) propose plus d’informations et un exemple simple d’une action de condition est indiqué ci-dessous :

```
{
    "If_trigger_is_foo": {
        "type": "If",
        "expression": "@equals(triggerBody(), 'foo')",
        "runAfter": { },
        "actions": {
            "Http_2": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.bing.com"
                },
                "runAfter": {},
                "type": "Http"
            }
        },
        "else": 
        {
            "if_trigger_is_bar": "..."
        }      
    }
}
```

## 3\. Propriété de RunAfter
La nouvelle propriété `runAfter` remplace `dependsOn` afin de permettre une plus grande précision dans l’exécution de la commande. `dependsOn` était synonyme de « l’action a été exécutée et a réussi ». Peu importe le nombre de fois que vous avez besoin d’exécuter une action si l’action précédente a réussi, échoué ou été ignorée. `runAfter` permet cette souplesse. Il existe un objet qui spécifie tous les noms d’action exécutés et définit un tableau d’états acceptables pour le déclenchement. Par exemple, si vous souhaitez exécuter après la réussite de l’étape A et la réussite ou l’échec de l’étape B, vous devez construire la propriété `runAfter` suivante :

```
{
    "...",
    "runAfter": {
        "A": ["Succeeded"],
        "B": ["Succeeded", "Failed"]
    }
}
```

## Mise à niveau vers le schéma 2016-06-01
La mise à niveau vers le nouveau schéma 2016-06-01 se déroule en quelques étapes seulement. Des détails sur les modifications du schéma sont disponibles [dans cet article](app-service-logic-schema-2016-04-01.md). Le processus de mise à niveau implique l’exécution du script de mise à niveau, l’enregistrement en tant que nouvelle application logique et, éventuellement, le remplacement de l’ancienne application logique, si nécessaire.

1. Ouvrez votre application logique en cours.
2. Cliquez sur le bouton **Mise à jour de schéma** dans la barre d’outils
   
    ![][1]
   
    La définition mise à niveau sera retournée. Vous pouvez la copier et la coller dans une définition de ressources si vous en avez besoin, mais nous vous **recommandons vivement** d’utiliser le bouton **Enregistrer sous** pour vérifier que toutes les références de connexion sont valides dans l’application logique mise à niveau.
3. Cliquez sur le bouton **Enregistrer sous** dans la barre d’outils du Panneau de mise à niveau.
4. Entrez le nom et l’état logique de l’application et cliquez sur **Créer** pour déployer votre application logique mise à niveau.
5. Vérifiez que votre application logique mise à niveau fonctionne comme prévu.
   
   > [!NOTE]
   > Si vous utilisez un déclencheur manuel ou sur demande, l’URL de rappel aura changé dans votre nouvelle application logique. Utilisez la nouvelle URL pour vérifier son fonctionnement de bout en bout ; vous pouvez cloner votre application logique existante pour conserver les URL précédentes.
   > 
   > 
6. *Facultatif* Utilisez le bouton **Cloner** dans la barre d’outils (adjacente à l’icône **Mettre à jour le schéma** dans l’image ci-dessus) pour remplacer votre application logique précédente avec la nouvelle version du schéma. Cela est nécessaire uniquement si vous souhaitez conserver les mêmes ID de ressource ou URL du déclencheur de demande de votre application logique.

### Notes de l’outil de mise à niveau
#### Mappage de condition
L’outil s’efforce de regrouper les actions des branches true et false dans une étendue dans la définition mise à niveau. Le modèle de concepteur de `@equals(actions('a').status, 'Skipped')` doit notamment apparaître comme une action `else`. Toutefois, si l’outil détecte des modèles qu’il ne reconnaît pas, il va potentiellement créer des conditions séparées pour les branches true et false. Les actions peuvent être remappées après la mise à niveau si nécessaire.

#### ForEach avec condition
Le modèle précédent d’une boucle ForEach avec une condition par élément peut être répliqué dans le nouveau schéma avec l’action de filtre. Cette opération doit s’effectuer automatiquement lors de la mise à niveau. La condition devient une action de filtrage avant la boucle ForEach (pour retourner uniquement un tableau d’éléments correspondant à la condition), et ce tableau est transmis dans l’action ForEach. Vous pouvez afficher un exemple de ceci [dans cet article](app-service-logic-loops-and-scopes.md)

#### Balises de ressource
Les balises de ressources seront supprimées de la mise à niveau et vous devrez les définir à nouveau pour le flux de travail mis à niveau.

## Autres modifications
### Déclencheur manuel renommé déclencheur à la demande
Le type `manual` a été déconseillé et renommé `request` avec le genre `http`. Cela est plus cohérent avec le type de modèle de déclencheur utilisé pour la conception.

### Nouvelle action de « filtre »
Si vous travaillez avec un grand tableau et que vous avez besoin de filtrer sur un ensemble plus restreint d’éléments, vous pouvez utiliser le nouveau type de « filtre ». Il accepte un tableau et une condition et va évaluer la condition pour chaque élément et renvoyer un tableau d’éléments correspondant à la condition.

### Restrictions d’action ForEach et Until
Les boucles ForEach et Until sont limitées à une seule action.

### TrackedProperties sur les actions
Les actions peuvent désormais avoir une propriété supplémentaire (sœur de `runAfter` et de `type`) et appelée `trackedProperties`. Il s’agit d’un objet qui spécifie certaines entrées ou sorties d’action à inclure dans la télémétrie de diagnostic Azure qui est émise dans le cadre d’un workflow. Par exemple :

```
{                
    "Http": {
        "inputs": {
            "method": "GET",
            "uri": "http://www.bing.com"
        },
        "runAfter": {},
        "type": "Http",
        "trackedProperties": {
            "responseCode": "@action().outputs.statusCode",
            "uri": "@action().inputs.uri"
        }
    }
}
```

## Étapes suivantes
* [Utilisation de la définition de flux de travail d’application logique](app-service-logic-author-definitions.md)
* [Création d’un modèle de déploiement d’applications logiques](app-service-logic-create-deploy-template.md)

<!-- Image references -->
[1]: ./media/app-service-logic-schema-2016-04-01/upgradeButton.png

<!---HONumber=AcomDC_0803_2016-->
---
title: Azure Resource Manager Policy | Microsoft Docs
description: "Décrit comment utiliser Azure Resource Manager Policy pour éviter les violations au niveau de différentes étendues comme l’abonnement, les groupes de ressources ou les ressources individuelles."
services: azure-resource-manager
documentationcenter: na
author: ravbhatnagar
manager: timlt
editor: tysonn
ms.assetid: abde0f73-c0fe-4e6d-a1ee-32a6fce52a2d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/07/2016
ms.author: gauravbh;tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 223a890fd18405b2d1331e526403da89354a68f2
ms.openlocfilehash: 467e9f4f7372c619f41bb64445784485de18a863


---
# <a name="use-policy-to-manage-resources-and-control-access"></a>Utiliser le service Policy pour gérer les ressources et contrôler l’accès
Azure Resource Manager vous permet de contrôler l’accès par le biais de stratégies personnalisées. Avec les stratégies, vous pouvez empêcher les utilisateurs de votre organisation de rompre les conventions qui sont nécessaires pour gérer les ressources de votre organisation. 

Vous créez des définitions de stratégies qui décrivent les actions ou les ressources qui sont spécifiquement refusées. Vous affectez ces définitions de stratégies selon l'étendue souhaitée, au niveau de l'abonnement, du groupe de ressources ou d'une ressource individuelle. Toutes les ressources enfants héritent des stratégies. Ainsi, si une stratégie est appliquée à un groupe de ressources, elle est applicable à toutes les ressources appartenant à ce groupe de ressources.

Dans cet article, nous expliquons la structure de base du langage de définition de stratégies que vous pouvez utiliser pour créer des stratégies. Ensuite, nous décrivons comment vous pouvez appliquer ces stratégies à différentes étendues.

## <a name="how-is-it-different-from-rbac"></a>Quelle est la différence avec RBAC ?
Il existe quelques différences importantes entre la stratégie et le contrôle d'accès en fonction du rôle, mais la première chose à comprendre est que les stratégies et le contrôle d'accès en fonction du rôle (RBAC) fonctionnent ensemble. Pour utiliser des stratégies, vous devez vous authentifier au moyen de RBAC. Contrairement à RBAC, la stratégie est, par défaut, un système explicite d'autorisation et de refus. 

Le contrôle d’accès en fonction du rôle (RBAC) porte principalement sur les actions qu’un **utilisateur** peut effectuer dans différentes étendues. Par exemple, un utilisateur particulier est ajouté au rôle de collaborateur pour un groupe de ressources dans l'étendue de votre choix, ce qui permet à l'utilisateur d'apporter des modifications dans ce groupe de ressources. 

La stratégie porte principalement sur les actions des **ressources** dans différentes étendues. Par exemple, avec les stratégies, vous pouvez contrôler les types de ressources qui peuvent être mises en service ou restreindre les emplacements dans lesquels les ressources peuvent être mises en service.

## <a name="common-scenarios"></a>Scénarios courants
Un scénario courant consiste à rendre nécessaire l’utilisation de balises de service à des fins de facturation interne. Une organisation peut choisir de n’autoriser des opérations que si le centre de coût approprié est associé ; sinon, elle rejette la demande. Cette stratégie l’aide à facturer le centre de coût approprié pour les opérations effectuées.

Dans un autre scénario courant, l’organisation peut souhaiter contrôler les emplacements où les ressources sont créées. Ou bien elle peut vouloir contrôler l’accès aux ressources en autorisant l’approvisionnement de certains types de ressources uniquement.

De même, une organisation peut contrôler le catalogue de services ou appliquer les conventions d’affectation de noms souhaitées pour les ressources.

À l’aide de stratégies, ces scénarios peuvent être facilement mis en œuvre.

## <a name="policy-definition-structure"></a>Structure de la définition de stratégie
Une définition de stratégie est créée à l’aide de JSON. Elle se compose d’un ou plusieurs opérateurs logiques/conditions qui définissent les actions et d’un résultat qui indique ce qui se passe quand les conditions sont remplies. Le schéma est publié à l’adresse [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json). 

L’exemple suivant montre une stratégie que vous pouvez utiliser pour limiter les emplacements où les ressources sont déployées :

```json
{
  "properties": {
    "parameters": {
      "listOfAllowedLocations": {
        "type": "array",
        "metadata": {
          "description": "An array of permitted locations for resources.",
          "strongType": "location",
          "displayName": "List of locations"
        }
      }
    },
    "displayName": "Geo-compliance policy template",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('listOfAllowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

Essentiellement, une stratégie contient les sections suivantes :

**Paramètres :** valeurs spécifiées lorsque la stratégie est affectée.

**Opérateurs logiques/conditions :** ensemble de conditions qui peuvent être manipulées par le biais d’un ensemble d’opérateurs logiques.

**Effet :** résultat obtenu lorsque la condition est satisfaite ; refus ou audit. Un résultat d’audit émet un journal de service d’événement d’avertissement. Par exemple, un administrateur peut créer une stratégie qui provoque un événement d’audit si quelqu’un crée une machine virtuelle de grande taille. L’administrateur peut passer en revue les journaux ultérieurement.

    {
      "if" : {
          <condition> | <logical operator>
      },
      "then" : {
          "effect" : "deny | audit | append"
      }
    }

## <a name="policy-evaluation"></a>Évaluation de la stratégie
Les stratégies sont évaluées lors de la création des ressources. En cas de déploiement d’un modèle, les stratégies sont évaluées lors de la création de chaque ressource dans le modèle. 

> [!NOTE]
> Actuellement, la stratégie n'évalue pas les types de ressources qui ne prennent pas en charge les balises, le type et l'emplacement, par exemple, le type de ressource Microsoft.Resources/deployments. Cette prise en charge sera ajoutée prochainement. Pour éviter des problèmes de compatibilité descendante, vous devriez spécifier explicitement le type lors de la création de stratégies. Par exemple, une stratégie de balises sans spécification des types est appliquée à tous les types. Dans ce cas, le déploiement d’un modèle risque d’échouer s’il existe une ressource imbriquée ne prenant pas en charge les balises, et le type de ressource du déploiement sera ajouté à l’évaluation de la stratégie. 
> 
> 

## <a name="parameters"></a>Paramètres
À partir de l’API version 2016-12-01, vous pouvez utiliser des paramètres dans votre définition de stratégie. Les paramètres permettent de simplifier la gestion des stratégies en réduisant le nombre de définitions de stratégies. Vous fournissez des valeurs aux paramètres lorsque vous affectez la stratégie.

Vous déclarez des paramètres lorsque vous créez des définitions de stratégies.

    "parameters": {
      "listOfLocations": {
        "type": "array",
        "metadata": {
          "description": "An array of permitted locations for resources.",
          "displayName": "List Of Locations"
        }
      }
    }

Le type d’un paramètre peut être une chaîne ou un tableau. La propriété de métadonnées est utilisée pour des outils comme le Portail Azure pour afficher des informations conviviales. 

Dans la règle de stratégie, vous pouvez référencer les paramètres comme dans les modèles. Par exemple : 
        
    { 
        "field" : "location",
        "in" : "[parameters(listOfLocations)]"
    }

## <a name="logical-operators"></a>Opérateurs logiques
Les opérateurs logiques pris en charge avec la syntaxe sont les suivants :

| Nom de l’opérateur | Syntaxe |
|:--- |:--- |
| not |« not » : {&lt;condition ou opérateur &gt;} |
| and |« allOf » : [ {&lt;condition ou opérateur &gt;},{&lt;condition ou opérateur &gt;}] |
| ou |« anyOf » : [ {&lt;condition ou opérateur &gt;},{&lt;condition ou opérateur &gt;}] |

Resource Manager vous permet de spécifier une logique complexe dans votre stratégie via des opérateurs imbriqués. Par exemple, vous pouvez refuser la création de ressources à un emplacement particulier pour un type de ressource spécifié. Voici un exemple d’opérateurs imbriqués.

## <a name="conditions"></a>Conditions
Une condition évalue si un **champ** ou une **source** répond à certains critères. Les noms et la syntaxe des conditions prises en charge sont les suivants :

| Nom de la condition | Syntaxe |
|:--- |:--- |
| Égal à |« equals » : « &lt;valeur&gt; » |
| Comme |« like » : « &lt;valeur&gt; » |
| Contient |« contains » : « &lt;valeur&gt; » |
| Dans |« in » : [ « &lt;valeur1&gt; », « &lt;valeur2&gt; » ] |
| Contient clé |« containsKey » : « &lt;Nom de la clé&gt; » |
| Exists |« exists » : « &lt;bool&gt; » |

### <a name="fields"></a>Champs
Les conditions sont formées à partir de champs et de sources. Un champ représente des propriétés dans la charge utile de la requête de ressource qui est utilisée pour décrire l'état de la ressource. Une source représente les caractéristiques de la requête elle-même. 

Les sources et champs suivants sont pris en charge :

Champs : **name** (nom), **kind** (genre), **type**, **location** (emplacement), **tags** (balises), **tags.*** et **property alias** (alias de propriété). 

### <a name="property-aliases"></a>Alias de propriété
L’alias de propriété est un nom pouvant servir de définition de stratégie pour accéder aux propriétés propres au type de ressource, telles que les paramètres et les références (SKU). Il fonctionne sur toutes les versions d’API pour lesquelles la propriété existe. Vous pouvez récupérer les alias à l'aide de l'API REST (la prise en charge de Powershell sera ajoutée ultérieurement) :

    GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01

L'exemple suivant montre une définition d’alias : Comme vous pouvez le voir, un alias définit des chemins dans différentes versions d'API, même en cas de changement de nom de propriété. 

    "aliases": [
        {
          "name": "Microsoft.Storage/storageAccounts/sku.name",
          "paths": [
            {
              "path": "properties.accountType",
              "apiVersions": [
                "2015-06-15",
                "2015-05-01-preview"
              ]
            },
            {
              "path": "sku.name",
              "apiVersions": [
                "2016-01-01"
              ]
            }
          ]
        }
    ]

Actuellement, les alias pris en charge sont les suivants :

| Nom d'alias | Description |
| --- | --- |
| {resourceType}/sku.name |Les types de ressources pris en charge sont les suivants : Microsoft.Compute/virtualMachines,<br />Microsoft.Storage/storageAccounts,<br />Microsoft.Web/serverFarms,<br /> Microsoft.Scheduler/jobcollections,<br />Microsoft.DocumentDB/databaseAccounts,<br />Microsoft.Cache/Redis,<br />Microsoft.CDN/profiles |
| {resourceType}/sku.family |Le type de ressource pris en charge est Microsoft.Cache/Redis |
| {resourceType}/sku.capacity |Le type de ressource pris en charge est Microsoft.Cache/Redis |
| Microsoft.Compute/virtualMachines/imagePublisher | |
| Microsoft.Compute/virtualMachines/imageOffer | |
| Microsoft.Compute/virtualMachines/imageSku | |
| Microsoft.Compute/virtualMachines/imageVersion | |
| Microsoft.Cache/Redis/enableNonSslPort | |
| Microsoft.Cache/Redis/shardCount | |
| Microsoft.SQL/servers/version | |
| Microsoft.SQL/servers/databases/requestedServiceObjectiveId | |
| Microsoft.SQL/servers/databases/requestedServiceObjectiveName | |
| Microsoft.SQL/servers/databases/edition | |
| Microsoft.SQL/servers/databases/elasticPoolName | |
| Microsoft.SQL/servers/elasticPools/dtu | |
| Microsoft.SQL/servers/elasticPools/edition | |


## <a name="effect"></a>Résultat
La stratégie prend en charge trois types d’effet : **deny**, **audit** et **append**. 

* Deny génère un événement dans le journal d'audit et fait échouer la requête
* Audit génère un événement dans le journal d'audit mais ne fait pas échouer la requête
* Append ajoute l'ensemble des champs défini à la requête 

Pour **append**, vous devez fournir les détails suivants :

    "effect": "append",
    "details": [
      {
        "field": "field name",
        "value": "value of the field"
      }
    ]

La valeur peut être une chaîne ou un objet au format JSON. 


## <a name="policy-definition-examples"></a>Exemples de définition de stratégie
Maintenant, observons comment définir la stratégie pour obtenir les scénarios précédents.

### <a name="chargeback-require-departmental-tags"></a>Facturation interne : exiger l’utilisation de balises de service
La stratégie suivante refuse les demandes dépourvues de balise contenant la clé « costCenter ».

    {
      "if": {
        "not" : {
          "field" : "tags",
          "containsKey" : "costCenter"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

La stratégie suivante ajoute la balise costCenter, avec une valeur prédéfinie si aucune balise n’est présente. 

    {
      "if": {
        "field": "tags",
        "exists": "false"
      },
      "then": {
        "effect": "append",
        "details": [
          {
            "field": "tags",
            "value": {"costCenter":"myDepartment" }
          }
        ]
      }
    }

La stratégie suivante ajoute la balise costCenter, avec une valeur prédéfinie si la balise costCenter est absente, mais que d’autres balises sont présentes. 

    {
      "if": {
        "allOf": [
          {
            "field": "tags",
            "exists": "true"
          },
          {
            "field": "tags.costCenter",
            "exists": "false"
          }
        ]

      },
      "then": {
        "effect": "append",
        "details": [
          {
            "field": "tags.costCenter",
            "value": "myDepartment"
          }
        ]
      }
    }


### <a name="geo-compliance-ensure-resource-locations"></a>Conformité géographique : vérifier les emplacements des ressources
L’exemple suivant illustre une stratégie qui refuse les demandes où l’emplacement n’est pas l’Europe du Nord ou l’Europe de l’Ouest.

    {
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### <a name="service-curation-select-the-service-catalog"></a>Curation des services : sélectionner le catalogue de services
L’exemple suivant illustre une stratégie qui n’autorise que les actions sur les services de type Microsoft.Resources/\*, Microsoft.Compute/\*, Microsoft.Storage/\*, Microsoft.Network/\*. Le reste est refusé.

    {
      "if" : {
        "not" : {
          "anyOf" : [
            {
              "field" : "type",
              "like" : "Microsoft.Resources/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Compute/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Storage/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Network/*"
            }
          ]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### <a name="use-approved-skus"></a>Utilisation de références (SKU) approuvées
L’exemple suivant illustre l’utilisation d’alias de propriété pour restreindre les références (SKU). Dans l’exemple, seule l’utilisation de Standard_LRS et Standard_GRS est approuvée pour les comptes de stockage.

    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "allof": [
                {
                  "field": "Microsoft.Storage/storageAccounts/sku.name",
                  "in": ["Standard_LRS", "Standard_GRS"]
                }
              ]
            }
          }
        ]
      },
      "then": {
        "effect": "deny"
      }
    }


### <a name="naming-convention"></a>Conventions d’affectation de noms
L’exemple suivant illustre l’utilisation de caractères génériques, grâce à la condition « like ». La condition stipule que la demande est refusée si le nom ne correspond pas au modèle indiqué (namePrefix\*nameSuffix).

    {
      "if" : {
        "not" : {
          "field" : "name",
          "like" : "namePrefix*nameSuffix"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### <a name="tag-requirement-just-for-storage-resources"></a>Spécification de balise uniquement pour les ressources de stockage
L’exemple suivant montre comment imbriquer des opérateurs logiques pour requérir une balise d’application seulement pour les ressources de stockage.

    {
        "if": {
            "allOf": [
              {
                "not": {
                  "field": "tags",
                  "containsKey": "application"
                }
              },
              {
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
              }
            ]
        },
        "then": {
            "effect": "audit"
        }
    }

## <a name="create-and-assign-a-policy"></a>Création et attribution d’une stratégie
L’application d’une stratégie nécessite la création d’une définition de stratégie et son application à une étendue. 

### <a name="rest-api"></a>API REST
Vous pouvez créer une stratégie avec l’ [API REST pour les définitions de stratégies](https://docs.microsoft.com/rest/api/resources/policydefinitions). L’API REST vous permet de créer et de supprimer des définitions de stratégies, ainsi que d’obtenir des informations sur les définitions existantes.

Pour créer une stratégie, exécutez :

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

Pour la version de l’API, utilisez *2016-04-01* ou *2016-12-01*. Incluez un texte de demande semblable à l’exemple suivant :

    {
      "properties": {
        "parameters": {
          "listOfAllowedLocations": {
            "type": "array",
            "metadata": {
              "description": "An array of permitted locations for resources.",
              "strongType": "location",
              "displayName": "List Of Locations"
            }
          }
        },
        "displayName": "Geo-compliance policy template",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",
        "policyRule": {
          "if": {
            "not": {
              "field": "location",
              "in": "[parameters('listOfAllowedLocations')]"
            }
          },
          "then": {
            "effect": "deny"
          }
        }
      }
    }

Vous pouvez appliquer la définition de stratégie à l’étendue souhaitée via l’ [API REST pour les affectations de stratégies](https://docs.microsoft.com/rest/api/resources/policyassignments). L’API REST vous permet de créer et de supprimer des affectations de stratégies, ainsi que d’obtenir des informations sur les affectations existantes.

Pour créer une affectation de stratégie, exécutez :

    PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}

{policyAssignmentName} correspond au nom de l’affectation de stratégie. Pour la version de l’API, utilisez *2016-04-01* ou *2016-12-01* (pour les paramètres). 

Avec un corps de demande semblable à l’exemple suivant :

    {
      "properties":{
        "displayName":"West US only policy assignment on the subscription ",
        "description":"Resources can only be provisioned in West US regions",
        "parameters": {
             "listOfAllowedLocations": ["West US", "West US2"]
         },
        "policyDefinitionId":"/subscriptions/########/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
        "scope":"/subscriptions/########-####-####-####-############"
      },
    }

### <a name="powershell"></a>PowerShell
Vous pouvez créer une définition de stratégie à l’aide de l’applet de commande New-AzureRmPolicyDefinition. L’exemple suivant crée une stratégie permettant d’attribuer des ressources uniquement en Europe du Nord et en Europe de l’Ouest.

    $policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy '{    
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }'            

Le résultat de l’exécution est stocké dans l’objet $policy et peut être utilisé ultérieurement lors de l’affectation de la stratégie. Pour le paramètre de stratégie, vous pouvez également utiliser le chemin d’accès au fichier .json contenant la stratégie au lieu de spécifier la stratégie en ligne.

    New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain     regions" -Policy "path-to-policy-json-on-disk"

Vous pouvez appliquer la stratégie selon l’étendue de votre choix à l’aide de l’applet de commande New-AzureRmPolicyAssignment :

    New-AzureRmPolicyAssignment -Name regionPolicyAssignment -PolicyDefinition $policy -Scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Dans ce cas, $policy est l'objet de stratégie qui a été renvoyé suite à l'exécution de l'applet de commande New-AzureRmPolicyDefinition. L'étendue est ici le nom du groupe de ressources que vous spécifiez.

Pour supprimer une affectation de stratégie, utilisez :

    Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Vous pouvez obtenir, modifier ou supprimer des définitions de stratégie à l’aide des applets de commande Get-AzureRmPolicyDefinition, Set-AzureRmPolicyDefinition et Remove-AzureRmPolicyDefinition respectivement.

De même, vous pouvez obtenir, modifier ou supprimer les affectations de stratégies à l’aide des applets de commande Get-AzureRmPolicyAssignment, Set-AzureRmPolicyAssignment et Remove-AzureRmPolicyAssignment respectivement.

### <a name="azure-cli"></a>Interface de ligne de commande Azure
Vous pouvez créer une définition de stratégie à l’aide de l’interface de ligne de commande Azure avec la commande de définition de stratégie. L’exemple suivant crée une stratégie permettant d’attribuer des ressources uniquement en Europe du Nord et en Europe de l’Ouest.

    azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy-string '{    
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }'    


Il est possible de spécifier le chemin d’accès au fichier .json contenant la stratégie plutôt que la stratégie en ligne.

    azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy "path-to-policy-json-on-disk"

Vous pouvez appliquer la stratégie selon l’étendue de votre choix à l’aide de la commande d’affectation de stratégie :

    azure policy assignment create --name regionPolicyAssignment --policy-definition-id /subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/<policy-name> --scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

L'étendue est ici le nom du groupe de ressources que vous spécifiez. Si la valeur du paramètre policy-definition-id est inconnue, il est possible de l’obtenir grâce à l’interface de ligne de commande Azure : 

    azure policy definition show <policy-name>

Pour supprimer une affectation de stratégie, utilisez :

    azure policy assignment delete --name regionPolicyAssignment --scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Vous pouvez récupérer, modifier ou supprimer des définitions de stratégies par le biais des commandes d’affichage, de configuration et de suppression de définitions de stratégies respectivement.

De même, vous pouvez obtenir, modifier ou supprimer des affectations de stratégies par le biais des commandes d’affichage et de suppression des affectations de stratégies respectivement.

## <a name="policy-audit-events"></a>Événements d’audit de stratégie
Après avoir appliqué votre stratégie, vous commencez à voir des événements liés à la stratégie. Vous pouvez accéder au portail ou utiliser PowerShell ou l’interface CLI Azure pour obtenir ces données. 

### <a name="powershell"></a>PowerShell
Pour afficher tous les événements liés au résultat « refus », vous pouvez utiliser la commande PowerShell suivante :

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/deny/action"} 

Pour afficher tous les événements liés au résultat « audit », vous pouvez utiliser la commande suivante :

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/audit/action"} 

### <a name="azure-cli"></a>Interface de ligne de commande Azure
Pour afficher tous les événements d’un groupe de ressources liés au résultat « refus », vous pouvez utiliser la commande CLI suivante :

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.value == \"Microsoft.Authorization/policies/deny/action\")"

Pour afficher tous les événements liés au résultat « audit », vous pouvez utiliser la commande CLI suivante :

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.value == \"Microsoft.Authorization/policies/audit/action\")"

## <a name="view-a-policy"></a>Affichage d’une stratégie
Vous utilisez PowerShell, l’interface de ligne de commande Azure ou l’API REST pour afficher une stratégie. Vous devrez peut-être afficher une stratégie si un déploiement échoue et que vous souhaitez voir la règle qui a refusé le déploiement. Le message d’erreur inclut un ID pour la définition de stratégie.

### <a name="powershell"></a>PowerShell
Pour obtenir une stratégie, utilisez l’applet de commande suivante :

    (Get-AzureRmPolicyAssignment -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/{definition-name}").Properties.policyRule | ConvertTo-Json

Elle retourne le code JSON pour la définition de stratégie.

### <a name="azure-cli"></a>Interface de ligne de commande Azure
Pour obtenir une stratégie, utilisez la commande suivante :

    azure policy definition show {definition-name} --json

### <a name="rest-api"></a>API REST
Pour obtenir une stratégie, utilisez l’opération [Obtenir la définition de stratégie](https://docs.microsoft.com/rest/api/resources/policydefinitions#PolicyDefinitions_Get).

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](resource-manager-subscription-governance.md).




<!--HONumber=Dec16_HO2-->



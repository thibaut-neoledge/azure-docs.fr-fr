<properties
	pageTitle="Azure Resource Manager Policy | Microsoft Azure"
	description="Décrit comment utiliser Azure Resource Manager Policy pour éviter les violations au niveau de différentes étendues comme l’abonnement, les groupes de ressources ou les ressources individuelles."
	services="azure-resource-manager"
	documentationCenter="na"
	authors="ravbhatnagar"
	manager="ryjones"
	editor=""/>

<tags
	ms.service="azure-resource-manager"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="11/10/2015"
	ms.author="gauravbh;tomfitz"/>

# Utiliser le service Policy pour gérer les ressources et contrôler l’accès

Azure Resource Manager vous permet désormais de contrôler l’accès par le biais de stratégies personnalisées. Avec les stratégies, vous pouvez empêcher les utilisateurs de votre organisation de rompre les conventions qui sont nécessaires pour gérer les ressources de votre organisation.

Vous créez des définitions de stratégies qui décrivent les actions ou les ressources qui sont spécifiquement refusées. Vous affectez ces définitions de stratégies selon l'étendue souhaitée, au niveau de l'abonnement, du groupe de ressources ou d'une ressource individuelle.

Dans cet article, nous allons expliquer la structure de base du langage de définition de stratégies que vous pouvez utiliser pour créer des stratégies. Ensuite, nous décrirons comment vous pouvez appliquer ces stratégies au niveau de différentes étendues et, enfin, nous présenterons des exemples d'application par le biais de l'API REST.

La stratégie est actuellement disponible en version préliminaire.

## Quelle est la différence avec RBAC ?

Il existe quelques différences importantes entre la stratégie et le contrôle d'accès en fonction du rôle, mais la première chose à comprendre est que les stratégies et le contrôle d'accès en fonction du rôle (RBAC) fonctionnent ensemble. Pour pouvoir utiliser la stratégie, l'utilisateur doit être authentifié au moyen de RBAC. Contrairement à RBAC, la stratégie est, par défaut, un système explicite d'autorisation et de refus.

Le contrôle d’accès en fonction du rôle (RBAC) se concentre sur les actions qu’un **utilisateur** peut effectuer selon différentes étendues. Par exemple, un utilisateur particulier est ajouté au rôle de collaborateur pour un groupe de ressources dans l'étendue de votre choix, ce qui permet à l'utilisateur d'apporter des modifications dans ce groupe de ressources.

La stratégie se concentre sur les actions sur les **ressources** selon différentes étendues. Par exemple, avec les stratégies, vous pouvez contrôler les types de ressources qui peuvent être mises en service ou restreindre les emplacements dans lesquels les ressources peuvent être mises en service.

## Scénarios courants

Un scénario courant consiste à rendre nécessaire l’utilisation de balises de service à des fins de facturation interne. Une organisation peut choisir de n’autoriser des opérations que si le centre de coût approprié est associé ; sinon, elle rejette la demande. Cela l’aide à facturer le centre de coût approprié pour les opérations effectuées.

Dans un autre scénario courant, l’organisation peut souhaiter contrôler les emplacements où les ressources sont créées. Ou bien elle peut vouloir contrôler l’accès aux ressources en autorisant l’approvisionnement de certains types de ressources uniquement.

De même, une organisation peut contrôler le catalogue de services ou appliquer les conventions d’affectation de noms souhaitées pour les ressources.

À l’aide de stratégies, ces scénarios peuvent être facilement mis en œuvre comme décrit ci-après.

## Structure de la définition de stratégie

Une définition de stratégie est créée à l’aide de JSON. Elle se compose d’un ou plusieurs opérateurs logiques/conditions qui définissent les actions et d’un résultat qui indique ce qui se passe quand les conditions sont remplies.

Essentiellement, une stratégie contient les éléments suivants :

**Opérateurs logiques/conditions :** ensemble de conditions qui peuvent être manipulées par le biais d’un ensemble d’opérateurs logiques.

**Résultat :** résultat obtenu quand la condition est satisfaite (refus ou audit). Un résultat d’audit émet un journal de service d’événement d’avertissement. Par exemple, un administrateur peut créer une stratégie qui provoque un audit si quelqu’un crée une machine virtuelle de grande taille, puis passer en revue les journaux ultérieurement.

    {
      "if" : {
        <condition> | <logical operator>
      },
      "then" : {
        "effect" : "deny | audit"
      }
    }


## Opérateurs logiques

Les opérateurs logiques pris en charge avec la syntaxe sont répertoriés ci-après :

| Nom de l’opérateur | Syntaxe |
| :------------- | :------------- |
| Not | "not" : {&lt;condition ou opérateur &gt;} |
| Et | "allOf" : [ {&lt;condition1&gt;},{&lt;condition2&gt;}] |
| Ou | "anyOf" : [ {&lt;condition1&gt;},{&lt;condition2&gt;}] |

Les conditions imbriquées ne sont pas prises en charge.

## Conditions

Une condition évalue si un **champ** ou une **source** répond à certains critères. Les noms et la syntaxe des conditions prises en charge sont répertoriés ci-après :

| Nom de la condition | Syntaxe |
| :------------- | :------------- |
| Égal à | "equals" : "&lt;valeur&gt;" |
| Comme | "like" : "&lt;valeur&gt;" |
| Contient | "contains" : "&lt;valeur&gt;"|
| Dans | "in" : [ "&lt;valeur1&gt;","&lt;valeur2&gt;" ]|
| Contient clé | "containsKey" : "&lt;nom\_clé&gt;" |


## Champs et sources

Les conditions sont formées à partir de champs et de sources. Un champ représente des propriétés dans la charge utile de la requête de ressource. Une source représente les caractéristiques de la requête elle-même.

Les sources et champs suivants sont pris en charge :

Champs : **name**, **kind**, **type**, **location**, **tags**, **tags.***.

Sources : **action**.

Pour obtenir plus d'informations sur les actions, consultez [RBAC : rôles intégrés](active-directory/role-based-access-built-in-roles.md).

## Exemples de définition de stratégie

Voyons à présent comment nous pouvons définir la stratégie pour mettre en œuvre les scénarios évoqués plus haut.

### Facturation interne : rendre nécessaire l’utilisation de balises de service

La stratégie ci-dessous refuse toutes les demandes dépourvues de balise contenant la clé « costCenter ».

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


### Conformité géographique : vérifier les emplacements des ressources

L’exemple ci-dessous illustre une stratégie qui refuse toutes les demandes où l’emplacement n’est pas l’Europe du Nord ou l’Europe de l’Ouest.

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

### Curation des services : sélectionner le catalogue de services

L’exemple ci-dessous illustre l’utilisation de la source. Il indique que seules sont autorisées les actions sur les services de type Microsoft.Resources/*, Microsoft.Compute/*, Microsoft.Storage/* et Microsoft.Network/*. Toutes les autres sont refusées.

    {
      "if" : {
        "not" : {
          "anyOf" : [
            {
              "source" : "action",
              "like" : "Microsoft.Resources/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Compute/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Storage/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Network/*"
            }
          ]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### Conventions d’affectation de noms

L’exemple ci-dessous illustre l’utilisation de caractères génériques, grâce à la condition « like ». La condition stipule que la demande est refusée si le nom ne correspond pas au modèle indiqué (namePrefix*nameSuffix).

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

## Affectation de rôle

Les stratégies peuvent être appliquées au niveau de différentes étendues comme l’abonnement, les groupes de ressources et les ressources individuelles. Toutes les ressources enfants héritent des stratégies. Ainsi, si une stratégie est appliquée à un groupe de ressources, elle est applicable à toutes les ressources appartenant à ce groupe de ressources.

## Création d’une stratégie

Cette section fournit des détails sur la façon dont une stratégie peut être créée à l’aide de l’API REST.

### Créer la définition de stratégie avec l’API REST

Vous pouvez créer une stratégie avec l’[API REST pour les définitions de stratégies](https://msdn.microsoft.com/library/azure/mt588471.aspx). L’API REST vous permet de créer et de supprimer des définitions de stratégies, ainsi que d’obtenir des informations sur les définitions existantes.

Pour créer une stratégie, exécutez la commande suivante :

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

Avec un corps de demande semblable au suivant :

    {
      "properties":{
        "policyType":"Custom",
        "description":"Test Policy",
        "policyRule":{
          "if" : {
            "not" : {
              "field" : "tags",
              "containsKey" : "costCenter"
            }
          },
          "then" : {
            "effect" : "deny"
          }
        }
      },
      "id":"/subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
      "type":"Microsoft.Authorization/policyDefinitions",
      "name":"testdefinition"
    }


La définition de la stratégie peut s’inspirer de l’un des exemples montrés plus haut. Pour la version de l’API, utilisez *2015-10-01-preview*. Pour plus d’informations et des exemples, consultez [API REST pour les définitions de stratégies](https://msdn.microsoft.com/library/azure/mt588471.aspx).

### Création d'une définition de stratégie à l'aide de PowerShell

Vous pouvez créer une nouvelle définition de stratégie à l'aide de l'applet de commande New-AzureRmPolicyDefinition comme indiqué ci-dessous. Les exemples ci-dessous créent une stratégie permettant d'attribuer des ressources uniquement en Europe du Nord et en Europe de l'ouest.

    $policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation onlyin certain regions" -Policy '{	"if" : {
    	    			    "not" : {
    	      			    	"field" : "location",
    	      			    		"in" : ["northeurope" , "westeurope"]
    	    			    	}
    	    		          },
    	      		    		"then" : {
    	    			    		"effect" : "deny"
    	      			    		}
    	    		    	}'    		

Le résultat de l'exécution est stocké dans l'objet $policy, car il peut être utilisé ultérieurement lors de l'affectation de la stratégie. Pour le paramètre de stratégie, vous pouvez également utiliser le chemin d'accès au fichier .json contenant la stratégie au lieu de spécifier la stratégie en ligne, comme indiqué ci-dessous.

    New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain 	regions" -Policy "path-to-policy-json-on-disk"


## Application d’une stratégie

### Affectation de stratégie avec l’API REST

Vous pouvez appliquer la définition de stratégie en fonction l’étendue souhaitée par le biais de l'[API REST pour les affectations de stratégies](https://msdn.microsoft.com/library/azure/mt588466.aspx). L’API REST vous permet de créer et de supprimer des affectations de stratégies, ainsi que d’obtenir des informations sur les affectations existantes.

Pour créer une affectation de stratégie, exécutez la commande suivante :

    PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}

{policyAssignmentName} correspond au nom de l’affectation de stratégie. Pour la version de l'API, utilisez *2015-10-01-preview*.

Avec un corps de demande semblable au suivant :

    {
      "properties":{
        "displayName":"VM_Policy_Assignment",
        "policyDefinitionId":"/subscriptions/########/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
        "scope":"/subscriptions/########-####-####-####-############"
      },
      "id":"/subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyAssignments/VMPolicyAssignment",
      "type":"Microsoft.Authorization/policyAssignments",
      "name":"VMPolicyAssignment"
    }

Pour plus d'informations et des exemples, consultez [API REST pour l'affectation de stratégies](https://msdn.microsoft.com/library/azure/mt588466.aspx).

### Affectation de stratégies à l'aide de PowerShell

Vous pouvez appliquer la stratégie créée précédemment à l'aide de PowerShell selon l'étendue de votre choix à l'aide de l'applet de commande New-AzureRmPolicyAssignment, comme indiqué ci-dessous :

    New-AzureRmPolicyAssignment -Name regionPolicyAssignment -PolicyDefinition $policy -Scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>
        
Dans ce cas, $policy est l'objet de stratégie qui a été renvoyé suite à l'exécution de l'applet de commande New-AzureRmPolicyDefinition, comme indiqué ci-dessus. L'étendue est ici le nom du groupe de ressources que vous spécifiez.

Si vous souhaitez supprimer l'affectation de stratégie ci-dessus, procédez comme suit :

    Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Vous pouvez obtenir, modifier ou supprimer des définitions de stratégie à l'aide des applets de commande Get-AzureRmPolicyDefinition, Set-AzureRmPolicyDefinition et Remove-AzureRmPolicyDefinition respectivement.

De même, vous pouvez obtenir, modifier ou supprimer les affectations de stratégies à l'aide des applets de commande Get-AzureRmPolicyAssignment, Set-AzureRmPolicyAssignment et Remove-AzureRmPolicyAssignment respectivement.

<!---HONumber=Nov15_HO3-->
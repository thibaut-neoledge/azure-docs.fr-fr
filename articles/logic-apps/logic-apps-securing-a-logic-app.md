---
title: "Sécuriser l’accès à Azure Logic Apps | Microsoft Docs"
description: "Renforcez la sécurité pour protéger l’accès aux déclencheurs, aux entrées et sorties, aux paramètres d’action et aux services utilisés avec des workflows dans Azure Logic Apps."
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/22/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: d090ce5a912a2079d2e47d13caf60ca701f0e548
ms.openlocfilehash: 2cc83c6f10272139f148b450e3c1c8cc91fd68f9


---

# <a name="securing-a-logic-app"></a>Sécurisation d’une application logique

De nombreux outils sont disponibles pour vous aider à sécuriser votre application logique.

* Sécurisation de l’accès au déclencheur d’une application logique (Déclencheur de requête HTTP)
* Sécurisation de l’accès pour gérer, modifier ou lire une application logique
* Sécurisation de l’accès au contenu des entrées et sorties d’une exécution
* Sécurisation des paramètres ou des entrées dans des actions d’un flux de travail
* Sécurisation de l’accès aux services recevant des requêtes d’un flux de travail

## <a name="secure-access-to-trigger"></a>Sécuriser l’accès au déclencheur

Lorsque vous utilisez une application logique déclenchée par une requête HTTP ([Requête](../connectors/connectors-native-reqres.md) ou [Webhook](../connectors/connectors-native-webhook.md)), vous pouvez limiter l’accès de sorte que seuls les clients autorisés puissent déclencher l’application logique.  Toutes les requêtes dans une application logique sont chiffrées et sécurisées via SSL.

### <a name="shared-access-signature"></a>Signature d’accès partagé

Chaque point de terminaison de requête pour une application logique inclut une partie [Signature d’accès partagé](../storage/storage-dotnet-shared-access-signature-part-1.md) (SAP) dans l’URL.  Chaque URL contient un paramètre de requête `sp`, `sv` et `sig`.  Les autorisations sont spécifiées par `sp` et correspondent aux méthodes HTTP autorisées, `sv` est la version utilisée pour générer et `sig` est utilisé pour authentifier l’accès au déclencheur.  Il est généré à l’aide de l’algorithme SHA&256; avec une clé secrète sur tous les chemins d’accès à l’URL et les propriétés.  La clé secrète n’est jamais exposée et publiée, et est chiffrée et stockée dans l’application logique.  Votre application logique autorise uniquement les déclencheurs contenant une signature valide créée avec la clé secrète.

#### <a name="regenerate-access-keys"></a>Régénération de clés d'accès

Vous pouvez régénérer une nouvelle clé sécurisée à tout moment via l’API REST ou le portail Azure.  Toutes les URL actuelles, qui ont été générées précédemment à l’aide de l’ancienne clé, sont invalidées et ne sont plus autorisées pour déclencher l’application logique.

1. Dans le portail Azure, ouvrez l’application logique dont vous souhaitez régénérer une clé
1. Cliquez sur l’élément de menu **Clés d’accès** sous **Paramètres**
1. Choisissez la clé à régénérer et terminez le processus

Les URL que vous récupérez après la régénération sont signées avec la nouvelle clé d’accès.

#### <a name="creating-callback-urls-with-an-expiration-date"></a>Création d’URL de rappel avec une date d’expiration

Si vous partagez l’URL avec d’autres utilisateurs, vous pouvez générer des URL avec des clés et des dates d’expiration spécifiques si nécessaire.  Cela vous permet de déployer des clés de manière transparente, ou de garantir que l’accès pour déclencher une application est limité à un intervalle de temps donné.  Vous pouvez spécifier une date d’expiration pour une URL via l’[API REST de Logic Apps](https://docs.microsoft.com/rest/api/logic/workflowtriggers) comme suit :

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

Dans le corps, incluez la propriété `NotAfter` en tant que chaîne de date JSON, qui renvoie une URL de rappel qui n’est valide que jusqu’à ce que la date et l’heure `NotAfter`.

#### <a name="creating-urls-with-primary-or-secondary-secret-key"></a>Création d’URL avec une clé de secret principale ou secondaire

Lorsque vous générez ou répertoriez des URL de rappel pour des déclencheurs basés sur une requête, vous pouvez également spécifier la clé à utiliser pour signer l’URL.  Vous pouvez générer une URL signée par une clé spécifique via l’[API REST de Logic Apps](https://docs.microsoft.com/rest/api/logic/workflowtriggers) comme suit :

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

Dans le corps, incluez la propriété `KeyType` en tant que `Primary` ou `Secondary`.  Cela renvoie une URL signée par la clé sécurisée spécifiée.

### <a name="restrict-incoming-ip-addresses"></a>Limiter les adresses IP entrantes

En plus de la signature d’accès partagé, vous pouvez souhaiter limiter l’appel d’une application logique à partir de clients spécifiques uniquement.  Par exemple, si vous gérez votre point de terminaison via gestion des API Azure, vous pouvez limiter l’application logique à n’accepter la requête que lorsqu’elle provient de l’adresse IP d’instance de gestion des API.

Ce paramètre peut être configuré dans les paramètres d’application logique :

1. Dans le portail Azure, ouvrez l’application logique à laquelle vous souhaitez ajouter des restrictions d’adresse IP
1. Cliquez sur l’élément de menu **Configuration du contrôle d’accès** sous **Paramètres**
1. Spécifier la liste des plages d’adresses IP acceptées par le déclencheur

Une plage d’adresses IP valide se présente au format `192.168.1.1/255`.  Si vous souhaitez que l’application logique ne soit déclenchée que comme une application logique imbriquée, sélectionnez l’option **Autres applications logiques uniquement**.  Un tableau vide est écrit sur la ressource, ce qui signifie que seuls les appels du service lui-même (applications logiques parentes) entraînent un déclenchement.

> [!NOTE]
> Une application logique avec un déclencheur de requête peut toujours être exécutée via l’API REST / Gestion `/triggers/{triggerName}/run` , quelle que soit l’adresse IP.  Cela nécessite une authentification auprès de l’API REST Azure, et tous les événements apparaissent dans le journal d’audit Azure.  Définissez les stratégies de contrôle d’accès en conséquence.

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>Définition de plages d’adresses IP sur la définition de ressource

Si vous utilisez un [modèle de déploiement](logic-apps-create-deploy-template.md) pour automatiser vos déploiements, les paramètres de plage d’adresses IP peuvent être configurés sur le modèle de ressource.  

``` json
{
    "properties": {
        "definition": {
        },
        "parameters": {},
        "accessControl": {
            "triggers": {
                "allowedCallerIpAddresses": [
                    {
                        "addressRange": "192.168.12.0/23"
                    },
                    {
                        "addressRange": "2001:0db8::/64"
                    }
                ]
            }
        }
    },
    "type": "Microsoft.Logic/workflows"
}

```

### <a name="adding-azure-active-directory-oauth-or-other-security"></a>Ajout d’Azure Active Directory, d’OAuth ou d’une autre sécurité

Si vous souhaitez ajouter des protocoles d’autorisation supplémentaires sur une application logique, utilisez la [Gestion des API Azure](https://azure.microsoft.com/services/api-management/).  Ceci apporte une surveillance avancée, une sécurité, une stratégie et une documentation sur n’importe quel point de terminaison, permettant ainsi d’exposer une application logique sous la forme d’une API.  La Gestion des API Azure peut exposer un point de terminaison public ou privé de l’application logique, permettant ainsi de tirer profit d’Azure Active Directory, d’un certificat, d’OAuth ou d’autres normes de sécurité.  Lorsqu’une requête est reçue, la Gestion des API Azure la transmet à l’application logique (en effectuant à la volée les transformations ou les restrictions nécessaires).  Vous pouvez utiliser les paramètres de plage d’adresses IP entrantes sur l’application logique pour autoriser le déclenchement de l’application logique à partir de la Gestion des API uniquement.

## <a name="secure-access-to-manage-or-edit-a-logic-app"></a>Sécuriser l’accès pour gérer ou modifier une application logique

Vous pouvez limiter l’accès aux opérations de gestion sur une application logique de sorte que seuls des utilisateurs ou groupes spécifiques soient en mesure d’effectuer des opérations sur la ressource.  Les applications logiques utilisent la fonctionnalité [Contrôle d’accès basé sur les rôles (RBAC)](../active-directory/role-based-access-control-configure.md), et peuvent être personnalisées avec les mêmes outils.  Vous pouvez également affecter des rôles intégrés aux membres de votre abonnement :

* **Collaborateur d’application logique** : accorde un accès pour afficher, modifier et mettre à jour une application logique.  Il ne peut pas supprimer la ressource ou effectuer des opérations d’administration.
* **Opérateur d’application logique** : peut afficher l’application logique et l’historique d’exécution, et activer/désactiver.  Il ne peut pas modifier ou mettre à jour la définition.

Vous pouvez également utiliser le [Verrouillage de la ressource Azure](../azure-resource-manager/resource-group-lock-resources.md) pour empêcher la modification ou suppression d’une application logique.  Cela est utile pour éviter que des ressources de production ne soient modifiées ou supprimées.

## <a name="secure-access-to-contents-of-the-run-history"></a>Sécuriser l’accès au contenu de l’historique d’exécution

Vous pouvez limiter l’accès au contenu des entrées ou sorties d’exécutions précédentes à des plages d’adresses IP spécifiques.  

Toutes les données de l’exécution d’un flux de travail sont chiffrées pendant le transit et au repos.  Lorsque l’historique d’exécution est appelé, le service authentifie la requête et fournit des liens vers les entrées et sorties de la requête et de la réponse.  Ces liens peuvent être protégés afin que seules les requêtes d’affichage du contenu provenant d’une plage d’adresses IP désignée renvoient le contenu.  Cela permet d’obtenir un contrôle d’accès supplémentaire.  Vous pouvez également spécifier une adresse IP telle que `0.0.0.0` afin que personne ne puisse accéder aux entrées/sorties.  Seul un utilisateur disposant d’autorisations d’administrateur peut supprimer cette restriction, permettant ainsi d’obtenir un accès « juste-à-temps » au contenu du flux de travail.

Ce paramètre peut être configuré dans les paramètres de ressource du portail Azure :

1. Dans le portail Azure, ouvrez l’application logique à laquelle vous souhaitez ajouter des restrictions d’adresse IP
1. Cliquez sur l’élément de menu **Configuration du contrôle d’accès** sous **Paramètres**
1. Spécifier la liste des plages d’adresses IP pour accéder au contenu

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>Définition de plages d’adresses IP sur la définition de ressource

Si vous utilisez un [modèle de déploiement](logic-apps-create-deploy-template.md) pour automatiser vos déploiements, les paramètres de plage d’adresses IP peuvent être configurés sur le modèle de ressource.  

``` json
{
    "properties": {
        "definition": {
        },
        "parameters": {},
        "accessControl": {
            "contents": {
                "allowedCallerIpAddresses": [
                    {
                        "addressRange": "192.168.12.0/23"
                    },
                    {
                        "addressRange": "2001:0db8::/64"
                    }
                ]
            }
        }
    },
    "type": "Microsoft.Logic/workflows"
}
```

## <a name="secure-parameters-and-inputs-within-a-workflow"></a>Sécuriser les paramètres et les entrées dans un flux de travail

Vous souhaiterez peut-être paramétrer certains aspects d’une définition de flux de travail pour le déploiement dans divers environnements.  Certains de ces paramètres peuvent également être des paramètres sécurisés que vous ne souhaitez pas voir apparaître lors de la modification d’un flux de travail, comme un ID client et la clé secrète client pour l’[authentification Azure Active Directory](../connectors/connectors-native-http.md#authentication) d’une action HTTP.

### <a name="using-parameters-and-secure-parameters"></a>Utilisation des paramètres et des paramètres sécurisés

Le [langage de définition de flux de travail](http://aka.ms/logicappsdocs) fournit une opération `@parameters()` pour accéder à la valeur d’un paramètre de ressource lors de l’exécution.  Vous pouvez également [spécifier des paramètres dans le modèle de déploiement de ressource](../azure-resource-manager/resource-group-authoring-templates.md#parameters).  Si vous spécifiez le type de paramètre `securestring`, il n’est pas renvoyé avec la définition de la ressource, ce qui signifie qu’il n’est pas accessible en consultant la ressource après le déploiement.

> [!NOTE]
> Si votre paramètre est utilisé dans les en-têtes ou le corps d’une requête, il peut être visible en accédant à l’historique d’exécution et à la requête HTTP sortante.  Veillez à définir vos stratégies d’accès au contenu en conséquence.
> Les en-têtes d’autorisation ne sont jamais visibles via les entrées ou sorties. Si la clé secrète est utilisée, elle n’est donc pas récupérable.

#### <a name="resource-deployment-template-with-secrets"></a>Modèle de déploiement de ressource avec des clés secrètes

Voici un exemple de déploiement qui fait référence à un paramètre sécurisé de `secret` lors de l’exécution.  Dans un fichier de paramètres distinct, je peux spécifier la valeur d’environnement pour `secret` ou utiliser le [Coffre de clés Azure Resource Manager](../azure-resource-manager/resource-manager-keyvault-parameter.md) pour récupérer mes clés secrètes au moment du déploiement.

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "secretDeploymentParam": {
      "type": "securestring"
    }
  },
  "variables": {},
  "resources": [
    {
      "name": "secret-deploy",
      "type": "Microsoft.Logic/workflows",
      "location": "westus",
      "tags": {
        "displayName": "LogicApp"
      },
      "apiVersion": "2016-06-01",
      "properties": {
        "definition": {
          "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
          "actions": {
            "Call_External_API": {
              "type": "http",
              "inputs": {
                "headers": {
                  "Authorization": "@parameters('secret')"
                },
                "body": "This is the request"
              },
              "runAfter": {}
            }
          },
          "parameters": {
            "secret": {
              "type": "SecureString"
            }
          },
          "triggers": {
            "manual": {
              "type": "Request",
              "kind": "Http",
              "inputs": {
                "schema": {}
              }
            }
          },
          "contentVersion": "1.0.0.0",
          "outputs": {}
        },
        "parameters": {
          "secret": {
            "value": "[parameters('secretDeploymentParam')]"
          }
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="secure-access-to-services-receiving-requests-from-a-workflow"></a>Sécuriser l’accès aux services recevant des requêtes d’un flux de travail

De nombreuses méthodes sont possibles pour sécuriser un point de terminaison auquel l’application logique doit accéder.

### <a name="using-authentication-on-outbound-requests"></a>Utilisation de l’authentification sur les requêtes sortantes

Lorsque vous utilisez une action HTTP, HTTP + Swagger (API ouverte) ou Webhook, vous pouvez ajouter l’authentification à la requête envoyée.  Cela peut inclure l’authentification de base, l’authentification par certificat ou l’authentification Azure Active Directory.  Des détails sur la configuration cette authentification se fournis [dans cet article](../connectors/connectors-native-http.md#authentication).

### <a name="restricting-access-to-logic-app-ip-addresses"></a>Limitation de l’accès aux adresses IP d’application logique

Tous les appels d’applications logiques proviennent d’un ensemble spécifique d’adresses IP par région.  Vous pouvez ajouter un filtrage supplémentaire afin d’accepter ces adresses IP désignées uniquement.  Vous pouvez trouver une liste de ces adresses IP [dans cet article](logic-apps-limits-and-config.md#configuration).

### <a name="on-premises-connectivity"></a>Connectivité locale

Logic Apps permet l’intégration à un nombre de services afin de fournir une communication locale sécurisée et fiable.

#### <a name="on-premises-data-gateway"></a>Passerelle de données locale

La plupart des connecteurs d’applications logiques gérés fournissent une connectivité sécurisée aux systèmes locaux, notamment le système de fichiers, SQL, SharePoint, DB2, et bien d’autres encore.  La passerelle utilise des canaux chiffrés via Azure Service Bus pour relayer des données en local, et tout le trafic provient d’un trafic sortant sécurisé de l’agent de la passerelle.  Plus d’informations sur le fonctionnement de la passerelle sont fournies [dans cet article](logic-apps-gateway-install.md#how-the-gateway-works).

#### <a name="azure-api-management"></a>Gestion des API Azure

[Gestion des API Azure](https://azure.microsoft.com/services/api-management/) inclut des options de connectivité locale, notamment l’intégration VPN de site à site et ExpressRoute pour un proxy et une communication sécurisés vers les systèmes locaux.  Dans le concepteur d’application logique, vous pouvez sélectionner rapidement une API exposée à partir de la Gestion des API Azure dans un flux de travail, offrant ainsi un accès rapide aux systèmes locaux.

#### <a name="hybrid-connections-from-azure-app-services"></a>Connexions hybrides à partir des services d’application Azure

Vous pouvez utiliser la fonctionnalité de connexion hybride locale pour que l’API Azure et les applications web communiquent en local.  Des détails sur les connexions hybrides et leur configuration sont fournis [dans cet article](../app-service-web/web-sites-hybrid-connection-get-started.md).

## <a name="next-steps"></a>Étapes suivantes
[Créer un modèle de déploiement](logic-apps-create-deploy-template.md)  
[Gestion des exceptions](logic-apps-exception-handling.md)  
[Analyser vos applications logiques](logic-apps-monitor-your-logic-apps.md)  
[Diagnostic des échecs et problèmes d’applications logiques](logic-apps-diagnosing-failures.md)  



<!--HONumber=Jan17_HO4-->



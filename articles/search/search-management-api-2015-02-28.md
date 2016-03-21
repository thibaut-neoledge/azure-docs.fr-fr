<properties
	pageTitle="API REST de gestion Azure Search version 28/02/2015 | Microsoft Azure | Service de recherche cloud hébergé"
	description="API REST de gestion Azure Search : version 2015-02-28"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="02/04/2016"
	ms.author="heidist" />

# API de gestion : version 2015-02-28

Azure Search est un service de recherche cloud hébergé sur Microsoft Azure. Ce document décrit la version **2015-02-28* de l’API REST de gestion Azure Search. Elle a depuis été remplacée par une version plus récente. Pour obtenir la dernière version, consultez [API REST de gestion Azure Search (2015-08-19)](https://msdn.microsoft.com/library/dn832684.aspx) sur MSDN.

##Opérations de gestion de service

Avec l’API REST de gestion du service Azure Search, les administrateurs peuvent accéder par programme à la plupart des fonctionnalités disponibles sur le portail, et donc automatiser les opérations suivantes :

- création ou suppression d’un service Azure Search ;
- création, régénération ou récupération de `api-keys` afin d’automatiser les modifications de routine sur les clés d’administration utilisées pour authentifier les opérations menées sur les données de recherche ; 
- ajustement du service de recherche Azure Search par rapport aux nouvelles exigences en matière de stockage ou de volume des requêtes.

Pour administrer intégralement votre service par programme, vous avez besoin de deux API : l’API REST de gestion d’Azure Search et l’[API REST commune Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn790568.aspx). L’API Resource Manager est utilisée dans le cadre d’opérations générales qui sont pas liées à un service spécifique, comme l’interrogation des données d’abonnement ou encore le recensement des géolocalisations. Pour créer et gérer des services Azure Search au sein de votre abonnement, votre requête HTTP doit inclure le point de terminaison de Resource Manager, l’ID d’abonnement, le fournisseur (dans le cas présent, Azure Search) et l’opération spécifique du service Search.

La page [Prise en main de l’API REST de gestion Azure Search](http://go.microsoft.com/fwlink/p/?linkID=516968) détaille la procédure de l’exemple de code qui illustre les opérations de configuration de l’application et de gestion du service. L’exemple d’application envoie des demandes à l’API Azure Resource Manager ainsi qu’à l’API de gestion du service d’Azure Search, ce qui vous indique comment reconstituer une application cohésive à partir de deux API.

### Point de terminaison ###

Le point de terminaison des opérations d’administration du service est l’URL d’Azure Resource Manager, `https://management.azure.com`.

Tous les appels d’API de gestion doivent inclure l’ID d’abonnement et une version d’API.

### Versions ###

La version actuelle de l’API REST de gestion d’Azure Search est `api-version=2015-02-28`. La version précédente, `api-version=2014-07-31-Preview`, est déconseillée. Bien qu’elle continuera de fonctionner au cours des prochains mois, nous vous encourageons à passer à la nouvelle version dès que possible.

### Authentification et contrôle d’accès###

L’API REST de gestion d’Azure Search est une extension d’Azure Resource Manager et partage ses dépendances. Active Directory est donc une condition préalable à l’administration de service d’Azure Search. Toutes les demandes administratives provenant du code client doivent être authentifiées à l’aide d’Azure Active Directory avant d’être envoyées vers le gestionnaire des ressources.

Si votre code d’application gère *les opérations de gestion du service* ainsi que *les opérations sur les données* concernant les index ou les documents, vous utiliserez deux méthodes d’authentification pour chacune des API d’Azure Search :

- En raison de sa dépendance à Resource Manager, l’administration de service et de gestion de clés utilise Active Directory pour l’authentification.
- L’en-tête des demandes de données concernant le point de terminaison du service Azure Search, comme la création d’index ou la recherche de documents, comprend une `api-key`. Consultez [API REST de service Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour en savoir plus sur l’authentification des demandes de données.

L’exemple d’application décrit dans [Prise en main de l’API REST de gestion Azure Search](http://go.microsoft.com/fwlink/p/?linkID=516968) illustre les techniques d’authentification pour chaque type d’opération. Cet article explique comment configurer une application cliente pour utiliser Active Directory.

Le contrôle d’accès d’Azure Resource Manager repose sur les rôles intégrés de propriétaire, de contributeur et de lecteur. Par défaut, tous les administrateurs de service sont propriétaires. Pour en savoir plus, consultez [Contrôle d’accès basé sur les rôles dans le portail Azure Classic](../active-directory/role-based-access-control-configure.md).


### Résumé des API ##

Les opérations incluent les API suivantes :

- <a name="CreateService">Création du service Search</a>

    `PUT	https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28`

- <a name="GetService">Obtention du service Search</a>

    `GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28`

- <a name="ListService">Liste des services Search</a>

    `GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices?api-version=2015-02-28`

- <a name="DeleteService">Suppression d’un service Search</a>

    `DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28`

- <a name="UpdateService">Mise à jour d’un service Search</a>

    `PATCH https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28`

- <a name="ListAdminKey">Liste des clés d’administration</a>

    `POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listAdminKeys?api-version=2015-02-28`

- <a name="RegenAdminKey">Régénération d’une clé d’administration</a>

    `POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/regenerateAdminKey/[keyKind]?api-version=2015-02-28`

- <a name="CreateQueryKey">Création d’une clé de requête</a>

    `POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/createQueryKey/[name]?api-version=2015-02-28`

- <a name="ListQueryKey">Liste des clés de requête</a>

    `GET	https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listQueryKeys?api-version=2015-02-28`

- <a name="DeleteQueryKey">Suppression des clés de requête</a>

    `DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/deleteQueryKey/[key]?api-version=2015-02-28`

<a name="ServiceOps"></a>
##Opérations de service

Vous pouvez déployer ou annuler le déploiement de services Azure Search en émettant des requêtes HTTP sur votre abonnement Azure. Les scénarios induits par ces opérations incluent la création d’outils d’administration personnalisés ou la mise en place d’un environnement de production ou de développement de bout en bout (de la création d’un service jusqu’au remplissage d’un index). De même, les fournisseurs de solutions qui conçoivent et vendent des solutions cloud ont peut-être besoin d’une approche automatisée et reproductible permettant de déployer des services pour chaque nouveau client.

**Opérations sur un service**

Les options spécifiques de service incluent les API suivantes :

- <a name="CreateService">Création du service Search</a> ;
- <a name="GetService">Obtention du service Search</a> ;
- <a name="ListService">Liste des services Search</a> ;
- <a name="DeleteService">Suppression d’un service Search</a> ;
- <a name="UpdateService">Mise à jour d’un service Search</a>.


<a name="CreateService"></a>
###Création de service Search

L’opération **Création de service Search** déploie un nouveau service Search conforme aux paramètres spécifiés. Cette API peut également servir à mettre à jour une définition de service existante.

    PUT	https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

#### Paramètres de l’URI de requête

`subscriptionId` : requis. `subscriptionID` correspondant à l’utilisateur Azure. Vous pouvez obtenir cette valeur à partir de l’API Azure Resource Manager ou du portail.

`resourceGroupName` : requis. Nom du groupe de ressources dans l’abonnement de l’utilisateur. Vous pouvez obtenir cette valeur à partir de l’API Azure Resource Manager ou du portail.

`serviceName` : requis. Nom du service de recherche dans le groupe de ressources spécifié. Les noms de service doivent contenir uniquement des lettres minuscules, des chiffres ou des tirets, ne peuvent pas inclure de tiret dans les deux premiers ou les deux derniers caractères, ne peuvent pas comporter de tirets consécutifs et doivent être compris entre 2 et 15 caractères. Dans la mesure où tous les noms finissent par <name>.search.windows.net, les noms de service doivent être globalement uniques. Deux services d’un ou plusieurs abonnements ou groupes de ressources ne peuvent porter le même nom. Vous ne pouvez pas modifier le nom du service après sa création.

`api-version` : requis. Indique la version du protocole utilisée pour cette demande. La version actuelle est `2015-02-28`.


#### En-têtes de demande

`Content-Type` : requis. Définissez cet en-tête sur application/json.

`x-ms-client-request-id` : facultatif. Valeur GUID générée par le client, qui identifie cette demande. Si elle est spécifiée, cette valeur sera incluse dans le message de réponse afin de mapper la demande.


#### Corps de la requête

{ "location": "location of search service", "tags": { "key": "value", ... }, "properties": { "sku": { "name": "free | standard | standard2" }, "replicaCount": 1 | 2 | 3 | 4 | 5 | 6, "partitionCount": 1 | 2 | 3 | 4 | 6 | 12 } }

#### Paramètres du corps de la demande#

`location` : requis. L’une des zones géographiques Azure prises en charge et enregistrées (par exemple, ouest des États-Unis, est des États-Unis, Asie du Sud-Est, etc.). L’emplacement d’une ressource ne peut pas être modifié après la création de cette dernière.

`tags` : facultatif. Liste de paires clé/valeur décrivant la ressource. Ces balises peuvent servir à l’affichage et au regroupement de ressources parmi tous les groupes de ressources. Un maximum de 10 balises peut être fourni pour une ressource. Chaque balise doit disposer d’une clé de 128 caractères maximum et d’une valeur de 256 caractères maximum.

`sku` : requis. Les valeurs valides sont `free` et `standard`. `standard2` est également valide, mais ne peut être utilisée que lorsqu’elle est activée pour votre abonnement Azure par le support Microsoft. `free` déploie le service dans des clusters partagés. `standard` déploie le service dans des clusters dédiés. Vous ne pouvez créer qu’un seul service Search au niveau de tarification gratuit. Les services supplémentaires doivent être créés au niveau de tarification standard. Par défaut, la création d’un service s’accompagne d’une partition et d’un réplica. Les réplicas et les partitions supplémentaires sont facturés en termes d’unités de recherche. Pour plus d'informations, consultez [Limites et contraintes](search-limits-quotas-capacity.md). Vous ne pouvez pas modifier la `sku` après la création du service.

`replicaCount` : facultatif. 1 constitue la valeur par défaut. Les valeurs valides vont de 1 à 6. Valide uniquement lorsque la `sku` est `standard`.

`partitionCount` : facultatif. 1 constitue la valeur par défaut. Les valeurs valides incluent 1, 2, 3, 4, 6 et 12. Valide uniquement lorsque la `sku` est `standard`.


### Response

HTTP 200 (OK) est renvoyé lorsqu’une définition de service est mise à jour. Le message HTTP 201 (Created) est renvoyé lorsqu’un service est créé.


#### En-têtes de réponse

`Content-Type` : cet en-tête est toujours défini sur application/json.

`x-ms-request-id` : identificateur unique pour l’opération actuelle, généré par le service.


#### Corps de réponse 

Pour HTTP 200 et 201, le corps de la réponse contient la définition de service.
    
    { 
      "id": "/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]", 
    
      "name": "service name", 
      "location": "location of search service", 
    "type": " Microsoft.Search/searchServices", 
      "tags": {
        "key": "value",
        ...
      },  
    "properties": { 
    	"sku": { 
          "name": "free | standard | standard2" 
       		 }, 
        "replicaCount": 1 | 2 | 3 | 4 | 5 | 6, 
        "partitionCount": 1 | 2 | 3 | 4 | 6 | 12, 
        "status": "running | provisioning | deleting | degraded | disabled | error", 
        "statusDetails": "details of the status", 
        "provisioningState": "succeeded | provisioning | failed" 
      } 
    } 


#### Éléments du corps de la réponse

`id` : l’ID est l’URL (à l’exception du nom d’hôte/modèle) pour ce service Search.

`name` : nom du service Search.

`location` : l’une des zones géographiques Azure prises en charge et enregistrées (par exemple, ouest des États-Unis, est des États-Unis, Asie du Sud-Est, etc.).

`tags` : liste de paires clé/valeur décrivant la ressource utilisée pour l’affichage et le regroupement des ressources parmi les groupes de ressources.

`sku` : indique le niveau de tarification. Les valeurs valides incluent :

- `free` : cluster partagé ;
- `standard` : cluster dédié ;
- `standard2` : à utiliser uniquement sous la direction du support Microsoft. 

`replicaCount` : indique le nombre de réplicas du service. Les valeurs valides vont de 1 à 6.

`partitionCount` : indique le nombre de partitions du service. Les valeurs valides incluent 1, 2, 3, 4, 6 et 12.

`status` : état du service Search au moment de l’appel de l’opération. Les valeurs valides incluent :

- `running` : le service Search est créé.
- `provisioning` : le service Search est en cours de déploiement.
- `deleting` : le service Search est en cours de suppression.
- `degraded` : le service Search est détérioré. Cela peut se produire lorsque le cluster rencontre une erreur susceptible ou non d’empêcher le bon fonctionnement du service.
- `disabled` : le service Search est désactivé. Dans cet état, le service rejette toutes les demandes d’API.
- `error` : le service Search indique un état d’erreur. 

**Remarque** : si votre service indique un état `degraded`, `disabled` ou `error`, cela signifie que l’équipe Azure Search met tout en œuvre pour traiter le problème sous-jacent. Les services dédiés indiquant ces états peuvent faire l’objet d’une facturation en fonction du nombre d’unités de recherche déployées.

`statusDetails` : détails de l’état.

`provisioningState` : indique l’état actuel du déploiement du service. Les valeurs valides incluent :

- `succeeded` : le déploiement est effectué avec succès.
- `provisioning` : le service est en cours de déploiement.
- `failed` : le service n’a pas pu être déployé. 

Le déploiement désigne un état intermédiaire pendant lequel la capacité de service est établie. Une fois que la capacité est paramétrée, le statut de `provisioningState` indique la réussite ou l’échec. Les applications clientes peuvent interroger l’état de déploiement (fréquence d’interrogation recommandée comprise entre 30 secondes et une minute) par le biais de l’opération **Obtention du service Search** afin de déterminer quand une opération est effectuée. Si vous utilisez le service gratuit, la valeur apparaît généralement comme réussie directement dans l’appel destiné à la création le service. Cela est dû au fait que le service gratuit utilise une capacité déjà configurée.

<a name="GetService"></a>
### Obtention du service Search

L’opération **Obtention du service Search** renvoie les propriétés du service Search spécifié. Les clés d’administration ne sont pas renvoyées. Utilisez l’opération **Obtention des clés d’administration** pour récupérer les clés d’administration.

    GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

#### URI de demande

`subscriptionId` : requis. ID d’abonnement de l’utilisateur Azure. Vous pouvez obtenir cette valeur à partir de l’API Azure Resource Manager ou du portail.

`resourceGroupName` : requis. Nom du groupe de ressources dans l’abonnement de l’utilisateur. Vous pouvez obtenir cette valeur à partir de l’API Azure Resource Manager ou du portail.

`serviceName` : requis. Nom du service Search dans le groupe de ressources spécifié. Si vous ne connaissez pas le nom du service, vous pouvez obtenir une liste par le biais de l’opération Liste des services Search (API Azure Search).

`api-version` : requis. Indique la version du protocole utilisée pour cette demande. La version actuelle est `2015-02-28`.

#### En-têtes de demande

`x-ms-client-request-id` : facultatif. Valeur GUID générée par le client, qui identifie cette demande. Si elle est spécifiée, cette valeur sera incluse dans le message de réponse afin de mapper la demande.


#### Corps de la requête

Aucun.


#### Code du statut de réponse

HTTP 200 (OK) si l’opération réussit.


#### En-têtes de réponse

`Content-Type` : cet en-tête est toujours défini sur application/json.

`x-ms-request-id` : identificateur unique pour l’opération actuelle, généré par le service.

#### Corps de réponse

    {
      "id": "/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]",
    
      "name": "service name",
      "location": "location of search service",
    "type": " Microsoft.Search/searchServices",
      "tags": {
        "key": "value",
        ... 
      }, 
    "properties": {
    	"sku": {
          "name": "free | standard | standard2"
    	},
        "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
        "partitionCount": 1 | 2 | 3 | 4 | 6 | 12,
        "status": "running | provisioning | deleting | degraded | disabled | error",
        "statusDetails": "details of the status",
        "provisioningState": "succeeded | provisioning | failed"
      }
    }

#### Éléments du corps de la réponse

`id` : l’ID est l’URL (à l’exception du nom d’hôte/modèle) pour ce service Search.

`name` : nom du service Search.

`location` : emplacement de la ressource. Il s’agit de l’une des zones géographiques Azure prises en charge et enregistrées (par exemple, ouest des États-Unis, est des États-Unis, Asie du Sud-Est, etc.).

`tags` : les balises désignent une liste de paires clé/valeur décrivant la ressource. Ces balises peuvent servir à l’affichage et au regroupement de ressources parmi tous les groupes de ressources.

`sku` : indique le niveau de tarification. Les valeurs valides incluent :

- `free` : cluster partagé ;
- `standard` : cluster dédié ;
- `standard2` : à utiliser uniquement sous la direction du support Microsoft.

`replicaCount` : indique le nombre de réplicas du service. Les valeurs valides vont de 1 à 6.

`partitionCount` : indique le nombre de partitions du service. Les valeurs valides incluent 1, 2, 3, 4, 6 et 12.

`status` : état du service Search au moment de l’appel de l’opération. Les valeurs valides incluent :

- `running` : le service Search est créé.
- `provisioning` : le service Search est en cours de déploiement.
- `deleting` : le service Search est en cours de suppression.
- `degraded` : le service Search est détérioré. Cela peut se produire lorsque le cluster rencontre une erreur susceptible ou non d’empêcher le bon fonctionnement du service.
- `disabled` : le service Search est désactivé. Dans cet état, le service rejette toutes les demandes d’API.
- `error` : le service Search indique un état d’erreur. 
 
**Remarque** : si votre service indique un état `degraded`, `disabled` ou `error`, cela signifie que l’équipe Azure Search met tout en œuvre pour traiter le problème sous-jacent. Les services dédiés indiquant ces états peuvent faire l’objet d’une facturation en fonction du nombre d’unités de recherche déployées.
 
`statusDetails` : détails de l’état.

`provisioningState` : les valeurs valides incluent :

- `succeeded` : le déploiement a réussi.
- `provisioning` : le service est en cours de déploiement.
- `failed` : le déploiement a échoué.


<a name="ListService"></a>
### Liste des services Search

L’opération **Liste des services Search** renvoie la liste de tous les services Search dans l’abonnement d’un groupe de ressources spécifique. Cette opération renvoie les définitions de service, sans les clés API d’administration. Utilisez l’opération **Obtention des clés d’administration** pour récupérer les clés d’administration.

    GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices?api-version=2015-02-28
    
#### Paramètres de l’URI de requête

`subscriptionId` : requis. `subscriptionID` correspondant à l’utilisateur Azure. Vous pouvez obtenir cette valeur à partir de l’API Azure Resource Manager ou du portail.

`resourceGroupName` : requis. Nom du groupe de ressources dans l’abonnement de l’utilisateur. Vous pouvez obtenir cette valeur à partir de l’API Azure Resource Manager ou du portail.

`api-version` : requis. Indique la version du protocole utilisée pour cette demande. La version actuelle est `2015-02-28`.

#### En-têtes de demande

`x-ms-client-request-id` : facultatif. Valeur GUID générée par le client, qui identifie cette demande. Si elle est spécifiée, cette valeur sera incluse dans le message de réponse afin de mapper la demande.

####Corps de la requête

Aucun.

####Response

Le code d’état est HTTP 200  (OK) si l’opération réussit.

### En-têtes de réponse ###

`Content-Type` : cet en-tête est toujours défini sur application/json.

`x-ms-request-id` : identificateur unique pour l’opération actuelle, généré par le service.


####Corps de réponse 

Le corps de la réponse représente une liste de services, renvoyée sous la forme d’un tableau JSON, où chaque service suit le format défini dans l’opération **Obtention du service Search**.

Le champ `nextLink` est toujours Null, car la version actuelle ne prend pas en charge la pagination. Le renvoi d’une valeur vide permet de préserver la compatibilité future.

    {
      "value": [
     	{
          "id": "id of service 1",
          "name": "service name",
          "location": "location of search service 1",
    	"type": " Microsoft.Search/searchServices",
          "tags": {
            "key": "value",
            ...
          }, 
    	"properties": {
            "sku": {
              "name": "free | standard | standard2"
            },
            "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
            "partitionCount": 1 | 2 | 3 | 4 | 6 | 12,
            "status": "running | provisioning | deleting | degraded | disabled | error",
            "statusDetails": "details of the status",
            "provisioningState": "succeeded | provisioning | failed"
    	}
      },   
      {
          "id": "id of service 2",
          "name": "service name 2",
          "location": "location of search service 2",
   	 	"type": " Microsoft.Search/searchServices",
          "tags": {
            "key": "value",
            ...
          },
    	"properties": {
            "sku": { 
              "name": "free | standard | standard2"
            },
            "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
            "partitionCount": 1 | 2 | 3 | 4 | 6 | 12,
            "status": "running | provisioning | deleting | degraded | disabled | error",
            "statusDetails": "details of the status",
            "provisioningState": "succeeded | provisioning | failed"
    	}
      }
    ],
    "nextLink": null
    }


<a name="DeleteService"></a>
### Suppression du service

L’opération **Suppression du service** supprime les données liées à la recherche et au service Search, y compris tous les index et tous les documents.
    
    DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

**Remarque :** les administrateurs et les développeurs sauvegardent généralement les données d’application avant leur suppression sur un serveur de production. Azure Search ne propose aucune opération de sauvegarde. Si vous utilisez l’index comme espace de stockage principal pour votre application, vous devez utiliser une opération Search pour renvoyer toutes les données dans l’index, que vous pouvez stocker en externe.

###Paramètres de l’URI de requête###

`subscriptionId` : requis. ID d’abonnement de l’utilisateur Azure. Vous pouvez obtenir cette valeur à partir de l’API Azure Resource Manager ou du portail.

`resourceGroupName` : requis. Nom du groupe de ressources dans l’abonnement de l’utilisateur. Vous pouvez obtenir cette valeur à partir de l’API Azure Resource Manager ou du portail.

`serviceName` : requis. Nom du service de recherche dans le groupe de ressources spécifié. Si vous ne connaissez pas le nom du service, vous pouvez obtenir une liste par le biais de l’opération Liste des services Search (API Azure Search).

`api-version` : requis. Indique la version du protocole utilisée pour cette demande. La version actuelle est `2015-02-28`.

###En-têtes de demande###

`x-ms-client-request-id` : facultatif. Valeur GUID générée par le client, qui identifie cette demande. Si elle est spécifiée, cette valeur sera incluse dans le message de réponse afin de mapper la demande.

###Corps de la requête###

Aucun.

###Response###

Pour HTTP 200, le corps de la réponse est vide. Lorsque la ressource n’existe pas, HTTP 200 (OK) est la réponse appropriée.

Vous pouvez utiliser l’**API Obtention du service Search** pour interroger l’état du service de suppression. Nous vous recommandons des fréquences d’interrogation comprises entre 30 secondes et une minute.

###En-têtes de réponse###

`x-ms-request-id` : identificateur unique pour l’opération actuelle, généré par le service.

###Corps de réponse###

Aucun.

<a name="UpdateService"></a>
### Mise à jour du service ##

L’opération **Mise à jour du service** modifie la configuration du service Search. Les modifications valides comprennent la modification des balises, des partitions ou du nombre de réplicas, ce qui ajoute ou supprime des unités de recherche dans votre service en tant qu’événement facturable. Si vous essayez de réduire les partitions en dessous de la quantité requise pour stocker le corpus de recherche existant, cela entraînera une erreur et le blocage de l’opération. Les modifications apportées à la topologie du service peuvent prendre du temps. Il faut un certain temps pour déplacer les données et pour configurer ou détruire les clusters du centre de données.

Vous ne pouvez pas modifier le nom, l’emplacement ou la référence. Si vous modifiez l’une de ces propriétés, vous devez créer un service.

    PATCH https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

Vous pouvez également utiliser la méthode PUT.

    PUT https://management.azure.com/subscriptions/[subscriptionId]/resourcegroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

> [AZURE.NOTE] Si vous utilisez une commande PUT pour mettre à jour le service, vous devez recourir au corps de requête utilisé dans une requête [Créer un service](#CreateService).

###Paramètres de l’URI de requête###

`subscriptionId` : requis. `subscriptionID` correspondant à l’utilisateur Azure. Vous pouvez obtenir cette valeur à partir de l’API Azure Resource Manager ou du portail.

`resourceGroupName` : requis. Nom du groupe de ressources dans l’abonnement de l’utilisateur. Vous pouvez obtenir cette valeur à partir de l’API Azure Resource Manager ou du portail.

`serviceName` : requis. Nom du service de recherche dans le groupe de ressources spécifié. Si vous ne connaissez pas le nom du service, vous pouvez obtenir une liste par le biais de l’opération Liste des services Search (API Azure Search).

`api-version` : requis. Indique la version du protocole utilisée pour cette demande. La version actuelle est `2015-02-28`.

###En-têtes de demande###

`Content-Type` : requis. Définissez cet en-tête sur application/json.

`x-ms-client-request-id` : facultatif. Valeur GUID générée par le client, qui identifie cette demande. Si elle est spécifiée, cette valeur sera incluse dans le message de réponse afin de mapper la demande.

###Corps de la requête###

    {
      "tags": {
        "key": "value",
        ...
      }, 
    	"properties": {
        "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
        "partitionCount": 1 | 2 | 3 | 4 | 6 | 12
     	}
    }

###Paramètres du corps de la demande###

`tags` : facultatif. Liste de paires clé/valeur décrivant la ressource. Ces balises peuvent servir à l’affichage et au regroupement des ressources (parmi tous les groupes de ressources). Un maximum de 10 balises peut être fourni pour une ressource. Chaque balise doit disposer d’une clé de 128 caractères maximum et d’une valeur de 256 caractères maximum.

`replicaCount` : facultatif. 1 constitue la valeur par défaut. Les valeurs valides vont de 1 à 6. Valide uniquement lorsque la `sku` est `standard`.

`partitionCount` : facultatif. 1 constitue la valeur par défaut. Les valeurs valides incluent 1, 2, 3, 4, 6 et 12. Valide uniquement lorsque la `sku` est `standard`.

###Response###

HTTP 200 (OK) est renvoyé si l’opération a réussi. Vous pouvez utiliser l’**API Obtention du service Search** pour interroger l’état du service de mise à jour. Nous vous recommandons des fréquences d’interrogation comprises entre 30 secondes et une minute.


### En-têtes de réponse ###

`Content-Type` : cet en-tête est toujours défini sur application/json.

`x-ms-request-id` : identificateur unique pour l’opération actuelle, généré par le service.

### Corps de réponse ###

Le corps de la réponse contient la définition de service mise à jour. Pour obtenir un exemple, consultez **API Obtention du service Search**.


<a name="KeyOps"></a>
## Opérations sur les clés

L’authentification auprès d’un service Azure Search requiert deux informations : une URL de service Search et une clé API. Les clés API sont générées au moment de la création du service et peuvent être régénérées à la demande une fois le service déployé. Il existe deux types de clé API.

- clé d’administration, qui octroie un accès à toutes les opérations (2 par service maximum) ;
- clé de requête, qui authentifie les demandes de requête uniquement (50 par service maximum).

La gestion par programme des clés d’administration et de requête de votre service Azure Search permet de concevoir des outils personnalisés, en régénérant régulièrement les clés d’après une meilleure pratique de sécurité de routine, en régénérant les clés lorsqu’un employé quitte l’entreprise, en générant ou en acquérant des clés au moment du déploiement du service, effectué par programme ou par script.

Les clés de requête peuvent être acquises, créées et supprimées. Les opérations sur les clés d’administration se limitent à l’acquisition et à la régénération de valeurs de clés existantes. La suppression d’une clé d’administration peut bloquer définitivement le service et rendre l’opération indisponible.

Les clés désignent des chaînes composées d’une combinaison aléatoire de chiffres et de lettres en majuscules. Une clé API ne peut être utilisée qu’avec le service pour laquelle elle a été créée et elle peut changer régulièrement (si vous choisissez la stratégie de régénération de clé pour des raisons de sécurité).

Les clés API, et notamment les clés d’administration, sont des données sensibles. Toute personne faisant l’acquisition de votre clé d’administration a la possibilité de supprimer ou de lire les données de vos index.

**Opérations sur les clés**

Les opérations sur les clés incluent les API suivantes :

- <a name="ListAdminKey">Liste des clés d’administration</a> ;
- <a name="RegenAdminKey">Régénération d’une clé d’administration</a> ;
- <a name="CreateQueryKey">Création d’une clé de requête</a> ;
- <a name="ListQueryKey">Liste des clés de requête</a> ;
- <a name="DeleteQueryKey">Suppression des clés de requête</a>.


<a name="ListAdminKey"></a>
## Liste des clés d’administration ##

L’opération **Liste des clés d’administration** renvoie les clés d’administration principales et secondaires correspondant au service Search spécifié. La méthode POST permet de renvoyer les clés en lecture-écriture.

    POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listAdminKeys?api-version=2015-02-28

Les clés d’administration sont créées avec le service. Il existe toujours deux clés : une clé principale et une clé secondaire. Vous pouvez régénérer ces clés, mais vous ne pouvez pas les supprimer.

###Paramètres de l’URI de requête###

`subscriptionId` : requis. ID d’abonnement de l’utilisateur Azure. Vous pouvez obtenir cette valeur à partir de l’API Azure Resource Manager ou du portail.

`resourceGroupName` : requis. Nom du groupe de ressources dans l’abonnement de l’utilisateur. Vous pouvez obtenir cette valeur à partir de l’API Azure Resource Manager ou du portail.

`serviceName` : requis. Nom du service de recherche dans le groupe de ressources spécifié. Si vous ne connaissez pas le nom du service, vous pouvez obtenir une liste par le biais de l’opération Liste des services Search (API Azure Search).

`api-version` : requis. Indique la version du protocole utilisée pour cette demande. La version actuelle est `2015-02-28`.

`listAdminKeys` : requis. Cette action récupère les clés d’administration principales et secondaires du service Search.

###En-têtes de requête###

`x-ms-client-request-id` : facultatif. Valeur GUID générée par le client, qui identifie cette demande. Si elle est spécifiée, cette valeur sera incluse dans le message de réponse afin de mapper la demande.

###Corps de la requête###

Aucun.

###Response###

HTTP 200 (OK) est renvoyé si l’opération a réussi.

### En-têtes de réponse ###

`Content-Type` : cet en-tête est toujours défini sur application/json.

`x-ms-request-id` : identificateur unique pour l’opération actuelle, généré par le service.

###Corps de réponse###

    {
      "primaryKey": "api key",
      "secondaryKey": "api key"
    }
    

<a name="RegenAdminKey"></a>
## Régénération des clés d’administration ##

L’opération **Régénération des clés d’administration** supprime et régénère la clé principale ou la clé secondaire. Vous ne pouvez régénérer qu’une seule clé à la fois. Lors de la régénération des clés, pensez à la manière dont vous allez conserver l’accès au service. Une clé secondaire vous permet de disposer d’une clé même pendant la régénération de la clé principale. Chaque service dispose toujours de deux clés. Vous pouvez régénérer les clés, mais vous ne pouvez pas les supprimer ni exécuter un service sans elles.
 
    POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/regenerateAdminKey/[keyKind]?api-version=2015-02-28

###Paramètres de l’URI de requête###

`subscriptionId` : requis. ID d’abonnement de l’utilisateur Azure. Vous pouvez obtenir cette valeur à partir de l’API Azure Resource Manager ou du portail.

`resourceGroupName` : requis. Nom du groupe de ressources dans l’abonnement de l’utilisateur. Vous pouvez obtenir cette valeur à partir de l’API Azure Resource Manager ou du portail.

`serviceName` : requis. Nom du service de recherche dans le groupe de ressources spécifié. Si vous ne connaissez pas le nom du service, vous pouvez obtenir une liste par le biais de l’opération Liste des services Search (API Azure Search).

`api-version` : requis. Indique la version du protocole utilisée pour cette demande. La version actuelle est `2015-02-28`.
	
`regenerateKey` : requis. Cette action indique que la clé d’administration principale ou la clé d’administration secondaire sera régénérée.

`keyKind` : requis. Spécifie la clé à régénérer. Les valeurs valides incluent :

- `primary`
- `secondary`

###En-têtes de requête###

`Content-Type` : requis. Définissez cet en-tête sur application/json.

`x-ms-client-request-id` : facultatif. Valeur GUID générée par le client, qui identifie cette demande. Si elle est spécifiée, cette valeur sera incluse dans le message de réponse afin de mapper la demande.

###Corps de la requête###

Aucun.

###Response###

HTTP 200 (OK) est renvoyé si l’opération a réussi.

### En-têtes de réponse ###

`Content-Type` : cet en-tête est toujours défini sur application/json.

`x-ms-request-id` : identificateur unique pour l’opération actuelle, généré par le service.

###Corps de réponse###

    {
      "primaryKey": "api key",
      "secondaryKey": "api key"
    }
    
###Éléments du corps de la réponse###

`primaryKey` : clé d’administration principale, si elle a été régénérée.

`secondaryKey` : clé d’administration secondaire, si elle a été régénérée.



<a name="CreateQueryKey"></a>
## Création d’une clé de requête ##

L’opération **Création d’une clé de requête** génère une nouvelle clé de requête pour le service Search. Vous pouvez créer jusqu’à 50 clés de requête par service.

    POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/createQueryKey/[name]?api-version=2015-02-28

###Paramètres de l’URI de requête###

`subscriptionId` : requis. ID d’abonnement de l’utilisateur Azure. Vous pouvez obtenir cette valeur à partir de l’API Azure Resource Manager ou du portail.

`resourceGroupName` : requis. Nom du groupe de ressources dans l’abonnement de l’utilisateur. Vous pouvez obtenir cette valeur à partir de l’API Azure Resource Manager ou du portail.

`serviceName` : requis. Nom du service Search dans le groupe de ressources spécifié. Si vous ne connaissez pas le nom du service, vous pouvez obtenir une liste par le biais de l’opération Liste des services Search (API Azure Search).

`api-version` : requis. Indique la version du protocole utilisée pour cette demande. La version actuelle est `2015-02-28`.

`createQueryKey` : requis. Cette action crée une clé de requête pour le service Search.

`name` : requis. Nom de la nouvelle clé.

###En-têtes de requête###

`x-ms-client-request-id` : facultatif. Valeur GUID générée par le client, qui identifie cette demande. Si elle est spécifiée, cette valeur sera incluse dans le message de réponse afin de mapper la demande.

###Corps de la requête###

Aucun.

###Response###

Le code d’état de la réponse est HTTP 200 (OK) si l’opération réussit.

### En-têtes de réponse ###

`Content-Type` : cet en-tête est toujours défini sur application/json.

`x-ms-request-id` : identificateur unique pour l’opération actuelle, généré par le service.

###Corps de réponse###

    {
      "name": "name of key",
      "key": "api key"
    }


###Éléments du corps de la réponse###

`name` : nom de la clé de la requête.

`key` : valeur de la clé de la requête.

<a name="ListQueryKey"></a>
## Liste des clés de requête ##


L’opération **Liste des clés de requête** renvoie les clés de requête du service Search spécifié. Les clés de requête permettent d’envoyer des appels d’API de requête (en lecture seule) vers un service Search. Chaque service peut comporter jusqu’à 50 clés de requête.

    GET	https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listQueryKeys?api-version=2015-02-28

###Paramètres de l’URI de requête###

`subscriptionId` : requis. ID d’abonnement de l’utilisateur Azure. Vous pouvez obtenir cette valeur à partir de l’API Azure Resource Manager ou du portail.

`resourceGroupName` : requis. Nom du groupe de ressources dans l’abonnement de l’utilisateur. Vous pouvez obtenir cette valeur à partir de l’API Azure Resource Manager ou du portail.

`serviceName` : requis. Nom du service de recherche dans le groupe de ressources spécifié. Si vous ne connaissez pas le nom du service, vous pouvez obtenir une liste par le biais de l’opération Liste des services Search (API Azure Search).

`api-version` : requis. Indique la version du protocole utilisée pour cette demande. La version actuelle est `2015-02-28`.
	
`listQueryKeys` : requis. Cette action récupère les clés de requête du service Search.

###En-têtes de requête###

`x-ms-client-request-id` : facultatif. Valeur GUID générée par le client, qui identifie cette demande. Si elle est spécifiée, cette valeur sera incluse dans le message de réponse afin de mapper la demande.

###Corps de la requête###

Aucun.

###Response###

Le code d’état de la réponse est HTTP 200 (OK) si l’opération réussit.

### En-têtes de réponse ###

`Content-Type` : cet en-tête est toujours défini sur application/json.

`x-ms-request-id` : identificateur unique pour l’opération actuelle, généré par le service.

###Corps de réponse###

    {
      "value": [
    	{
          "name": "name of key 1",
          "key": "key 1"
      	},   
      	{   
          "name": "name of key 2",
          "key": "key 2"
      	}
      ],
    "nextLink": null
    }

###Éléments du corps de la réponse###

`name` : nom de la clé de la requête.

`key` : valeur de la clé de la requête.


<a name="DeleteQueryKey"></a>
## Suppression des clés de requête ##

L’opération **Suppression des clés de requête** supprime la clé de requête spécifiée. Les clés de requête sont facultatives et servent aux requêtes en lecture seule.

    DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/deleteQueryKey/[key]?api-version=2015-02-28

Contrairement aux clés d’administration, les clés de requête ne sont pas régénérées. Le processus de régénération d’une clé de requête consiste à supprimer la clé, puis à la recréer.

###Paramètres de l’URI de requête###

`subscriptionId` : requis. ID d’abonnement de l’utilisateur Azure. Vous pouvez obtenir cette valeur à partir de l’API Azure Resource Manager ou du portail.

`resourceGroupName` : requis. Nom du groupe de ressources dans l’abonnement de l’utilisateur. Vous pouvez obtenir cette valeur à partir de l’API Azure Resource Manager ou du portail.

`serviceName` : requis. Nom du service Search dans le groupe de ressources spécifié. Si vous ne connaissez pas le nom du service, vous pouvez obtenir une liste par le biais de l’opération Liste des services Search (API Azure Search).

`api-version` : requis. Indique la version du protocole utilisée pour cette demande. La version actuelle est `2015-02-28`.

`deleteQueryKey` : requis. Cette action supprime une clé de requête existante du service Search.

`key` : requis. Clé à supprimer.

###En-têtes de requête###

`x-ms-client-request-id` : facultatif. Valeur GUID générée par le client, qui identifie cette demande. Si elle est spécifiée, cette valeur sera incluse dans le message de réponse afin de mapper la demande.

###Corps de la requête###

Aucun.

###Response###

Le code d’état de réponse est HTTP 200  (OK) si l’opération réussit.

### En-têtes de réponse ###

`Content-Type` : cet en-tête est toujours défini sur application/json.

`x-ms-request-id` : identificateur unique pour l’opération actuelle, généré par le service.

###Corps de réponse###

Aucune.

<!---------HONumber=AcomDC_0309_2016-->
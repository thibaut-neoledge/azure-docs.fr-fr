---
title: Automatisation de DocumentDB - Azure CLI 2.0 | Microsoft Docs
description: "Utilisez Azure CLI 2.0 pour gérer les comptes de base de données DocumentDB. DocumentDB est une base de données NoSQL basée sur le cloud pour les données JSON."
services: documentdb
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 6158c27f-6b9a-404e-a234-b5d48c4a5b29
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/04/2017
ms.author: dimakwan
translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: 150e8f3e186683bce735d0952adb57544505d1e9
ms.lasthandoff: 04/06/2017


---
# <a name="automate-azure-documentdb-account-management-using-azure-cli-20"></a>Automatiser la gestion des comptes Azure DocumentDB à l’aide d’Azure CLI 2.0
> [!div class="op_single_selector"]
> * [Portail Azure](documentdb-create-account.md)
> * [Azure CLI 1.0](documentdb-automation-resource-manager-cli-nodejs.md)
> * [Azure CLI 2.0](documentdb-automation-resource-manager-cli.md)
> * [Azure PowerShell](documentdb-manage-account-with-powershell.md)

Le guide suivant décrit les commandes dédiées à l’automatisation de la gestion de vos comptes de base de données DocumentDB à l’aide des commandes de la version préliminaire DocumentDB disponibles dans Azure CLI 2.0. Il inclut également des commandes dédiées à la gestion des clés de comptes et des priorités de basculement dans des [comptes de base de données multi-régions][scaling-globally]. La mise à jour de votre compte de base de données vous permet de modifier les stratégies de cohérence et d’ajouter/de supprimer des régions. Pour une gestion multiplateforme de votre compte de base de données DocumentDB, vous pouvez utiliser [Azure Powershell](documentdb-manage-account-with-powershell.md), l’[API REST Resource Provider][rp-rest-api] ou le [portail Azure](documentdb-create-account.md).

## <a name="getting-started"></a>Prise en main

Suivez les instructions dans [Installer Azure CLI 2.0][install-az-cli2] afin de configurer votre environnement de développement avec Azure CLI 2.0.

Connectez-vous à votre compte Azure en exécutant la commande suivant et en suivant la procédure s’affichant à l’écran.

    az login

Si vous ne disposez pas encore de [groupe de ressources](../azure-resource-manager/resource-group-overview.md#resource-groups), créez-en un :

    az group create --name <resourcegroupname> --location <resourcegrouplocation>
    az group list

`<resourcegrouplocation>` doit être l’une des régions dans lesquelles DocumentDB est généralement disponible. La liste actuelle des régions est présentée sur la [page Régions Azure](https://azure.microsoft.com/regions/#services).

### <a name="notes"></a>Remarques

* Exécutez az documentdb -h pour récupérer une liste complète des commandes disponibles, ou consultez la [page de référence][az-documentdb-ref].
* Exécutez az documentdb <command> -h pour récupérer une liste des détails des paramètres obligatoires et facultatifs par commande.

## <a id="create-documentdb-account-cli"></a> Créer un compte de base de données DocumentDB

Cette commande vous permet de créer un compte de base de données DocumentDB. Configurez votre nouveau compte de base de données pour une seule ou [plusieurs régions][scaling-globally] avec une [stratégie de cohérence](documentdb-consistency-levels.md) déterminée. 

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the DocumentDB database account.
                                    Allowed values: BoundedStaleness, Eventual, Session, Strong.
    --ip-range-filter             : Firewall support. Specifies the set of IP addresses or IP
                                    address ranges in CIDR form to be included as the allowed list
                                    of client IPs for a given database account. IP addresses/ranges
                                    must be comma separated and must not contain any spaces.
    --kind                        : The type of DocumentDB database account to create.  Allowed
                                    values: GlobalDocumentDB, MongoDB, Parse.  Default:
                                    GlobalDocumentDB.
    --locations                   : Space separated locations in 'regionName=failoverPriority'
                                    format. E.g "East US"=0 "West US"=1. Failover priority values
                                    are 0 for write regions and greater than 0 for read regions. A
                                    failover priority value must be unique and less than the total
                                    number of regions. Default: single region account in the
                                    location of the specified resource group.
    --max-interval                : When used with Bounded Staleness consistency, this value
                                    represents the time amount of staleness (in seconds) tolerated.
                                    Accepted range for this value is 1 - 100.  Default: 5.
    --max-staleness-prefix        : When used with Bounded Staleness consistency, this value
                                    represents the number of stale requests tolerated. Accepted
                                    range for this value is 1 - 2,147,483,647.  Default: 100.
```

Exemples : 

    az documentdb create -g rg-test -n docdb-test
    az documentdb create -g rg-test -n docdb-test --kind MongoDB
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 "South Central US"=2
    az documentdb create -g rg-test -n docdb-test --ip-range-filter "13.91.6.132,13.91.6.1/24"
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 --default-consistency-level BoundedStaleness --max-interval 10 --max-staleness-prefix 200

### <a name="notes"></a>Remarques
* Les emplacements doivent correspondre à des régions dans lesquelles DocumentDB est généralement disponible. La liste actuelle des régions est présentée sur la [page Régions Azure](https://azure.microsoft.com/regions/#services).
* Pour activer l’accès au portail, incluez l’adresse IP du portail Azure pour votre région dans le filtre de plages d’adresses IP (ip-range-filter), comme spécifié dans [Configuration de la stratégie de contrôle d’accès IP](documentdb-firewall-support.md#configure-ip-policy).

## <a id="update-documentdb-account-cli"></a> Mettre à jour un compte de base de données DocumentDB

Cette commande vous permet de mettre à jour les propriétés de votre compte de base de données DocumentDB. Les informations mises à jour sont notamment la stratégie de cohérence et les emplacements hébergeant le compte de base de données.

> [!NOTE]
> Cette commande, qui vous permet d’ajouter ou de supprimer des régions, ne prend aucunement en charge la modification des priorités de basculement. Pour modifier les priorités de basculement, consultez la section [ci-dessous](#modify-failover-priority-powershell).

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the DocumentDB database account.
                                    Allowed values: BoundedStaleness, Eventual, Session, Strong.
    --ip-range-filter             : Firewall support. Specifies the set of IP addresses or IP address
                                    ranges in CIDR form to be included as the allowed list of client
                                    IPs for a given database account. IP addresses/ranges must be comma
                                    separated and must not contain any spaces.
    --locations                   : Space separated locations in 'regionName=failoverPriority' format.
                                    E.g "East US"=0. Failover priority values are 0 for write regions
                                    and greater than 0 for read regions. A failover priority value must
                                    be unique and less than the total number of regions.
    --max-interval                : When used with Bounded Staleness consistency, this value represents
                                    the time amount of staleness (in seconds) tolerated. Accepted range
                                    for this value is 1 - 100.
    --max-staleness-prefix        : When used with Bounded Staleness consistency, this value represents
                                    the number of stale requests tolerated. Accepted range for this
                                    value is 1 - 2,147,483,647.
```

Exemples : 

    az documentdb update -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 "South Central US"=2
    az documentdb update -g rg-test -n docdb-test --ip-range-filter "13.91.6.132,13.91.6.1/24"
    az documentdb update -g rg-test -n docdb-test --default-consistency-level BoundedStaleness --max-interval 10 --max-staleness-prefix 200

## <a id="add-remove-region-documentdb-account-cli"></a> Ajouter/Supprimer une région d’un compte de base de données DocumentDB

Pour ajouter ou supprimer des régions d’un compte existant de base de données DocumentDB, utilisez la commande [update](#update-documentdb-account-cli) avec l’indicateur `--locations`. L’exemple ci-dessous illustre la création d’un compte, ainsi que les opérations ultérieures d’ajout et de suppression de régions de ce compte.

Exemple :

    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1
    az documentdb update -g rg-test -n docdb-test --locations "East US"=0 "North Europe"=1 "South Central US"=2


## <a id="delete-documentdb-account-cli"></a> Supprimer un compte de base de données DocumentDB

Cette commande vous permet de supprimer un compte existant de base de données DocumentDB.

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Exemple :

    az documentdb delete -g rg-test -n docdb-test

## <a id="get-documentdb-properties-cli"></a> Obtenir des propriétés d’un compte de base de données DocumentDB

Cette commande vous permet d’obtenir les propriétés d’un compte existant de base de données DocumentDB.

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Exemple :

    az documentdb show -g rg-test -n docdb-test

## <a id="list-account-keys-cli"></a> Lister des clés de comptes

Lorsque vous créez un compte DocumentDB, le service génère deux clés d'accès maître qui peuvent être utilisées pour l'authentification lors de l'accès au compte DocumentDB. En fournissant deux clés d'accès, DocumentDB vous permet de régénérer les clés sans interruption de votre compte DocumentDB. Des clés en lecture seule pour l’authentification des opérations en lecture seule sont également disponibles. Il existe deux clés en lecture et écriture (primaire et secondaire) et deux clés en lecture seule (primaire et secondaire).

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Exemple :

    az documentdb list-keys -g rg-test -n docdb-test

## <a id="list-connection-strings-cli"></a> Lister les chaînes de connexion

Pour les comptes MongoDB, vous pouvez récupérer la chaîne de connexion pour connecter votre application MongoDB au compte de base de données à l’aide de la commande suivante.

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Exemple :

    az documentdb list-connection-strings -g rg-test -n docdb-test

## <a id="regenerate-account-key-cli"></a> Régénérer la clé de compte

Vous devez modifier périodiquement les clés d'accès à votre compte DocumentDB pour garantir la sécurité des connexions. Deux clés d'accès vous sont affectées afin de vous permettre de conserver des connexions au compte DocumentDB à l'aide d'une clé d'accès lorsque vous régénérez l'autre clé.

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --key-kind          [Required]: The access key to regenerate.  Allowed values: primary, primaryReadonly,
                                    secondary, secondaryReadonly.
```

Exemple :

    az documentdb regenerate-key -g rg-test -n docdb-test --key-kind secondary

## <a id="modify-failover-priority-cli"></a> Modifier la priorité de basculement d’un compte de base de données DocumentDB

Pour les comptes de base de données multi-régions, vous pouvez modifier la priorité de basculement des différentes régions dans lesquelles existe le compte de base de données DocumentDB. Pour plus d’informations sur le basculement dans votre compte de base de données DocumentDB, consultez la section [Distribution mondiale des données avec DocumentDB](documentdb-distribute-data-globally.md).

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --failover-policies [Required]: Space separated failover policies in 'regionName=failoverPriority' format.
                                    E.g "East US"=0 "West US"=1.
```

Exemple :

    az documentdb failover-priority-change "East US"=1 "West US"=0 "South Central US"=2

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous disposez d’un compte DocumentDB, l’étape suivante consiste à créer une base de données DocumentDB. Pour créer une base de données, utilisez au choix :

* Le portail Azure, tel que décrit dans la rubrique [Guide pratique pour créer une base de données et une collection DocumentDB à l’aide du portail Azure](documentdb-create-collection.md).
* les exemples .NET C# du projet [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) dans le dépôt [azure-documentdb-dotnet](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) sur GitHub ;
* les [kits de développement logiciel (SDK) de DocumentDB](documentdb-sdk-dotnet.md). DocumentDB possède les kits de développement logiciel (SDK) de .NET, Java, Python, Node.js et de l’API JavaScript.

Après avoir créé la base de données, vous devez lui [ajouter une ou plusieurs collections](documentdb-create-collection.md), puis [ajouter des documents](documentdb-view-json-document-explorer.md) aux collections.


Une fois que chaque collection contient des documents, vous pouvez utiliser [DocumentDB SQL](documentdb-sql-query.md) pour [exécuter des requêtes](documentdb-sql-query.md#ExecutingSqlQueries) sur vos documents à l’aide de [l’Explorateur de requête](documentdb-query-collections-query-explorer.md) du portail, de [l’API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou de l’un des [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).

Pour en savoir plus sur DocumentDB, explorez les ressources suivantes :

* [Parcours d’apprentissage de DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
* [Modèle de ressource et concepts de DocumentDB](documentdb-resources.md)


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[scaling-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet
[install-az-cli2]: https://docs.microsoft.com/en-us/cli/azure/install-az-cli2
[az-documentdb-ref]: https://docs.microsoft.com/en-us/cli/azure/documentdb
[az-documentdb-create-ref]: https://docs.microsoft.com/en-us/cli/azure/documentdb#create
[rp-rest-api]: https://docs.microsoft.com/en-us/rest/api/documentdbresourceprovider/


---
title: "Automatisation d’Azure Cosmos DB - Azure CLI 2.0 | Microsoft Docs"
description: "Azure CLI 2.0 permet de créer et gérer des comptes Azure Cosmos DB. Azure Cosmos DB est une base de données hautement disponible et distribuée dans le monde entier."
services: cosmosdb
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 6158c27f-6b9a-404e-a234-b5d48c4a5b29
ms.custom: quick start create
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/20/2017
ms.author: dimakwan
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6150914e623cb6c54f1257c772be62150ab81e17
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---
# <a name="create-an-azure-cosmos-db-account-using-the-azure-cli"></a>Créer un compte Azure Cosmos DB à l’aide de la CLI Azure

Le guide suivant décrit les commandes dédiées à l’automatisation de la gestion de vos comptes de base de données Azure Cosmos DB à l’aide des commandes de la version préliminaire disponibles dans Azure CLI 2.0. Il inclut également des commandes dédiées à la gestion des clés de comptes et des priorités de basculement dans des [comptes de base de données multi-régions][scaling-globally]. La mise à jour de votre compte de base de données vous permet de modifier les stratégies de cohérence et d’ajouter/de supprimer des régions. Pour une gestion multiplateforme de votre compte de base de données Azure Cosmos DB, vous pouvez utiliser [Azure PowerShell](documentdb-manage-account-with-powershell.md), l’[API REST Resource Provider][rp-rest-api] ou le [portail Azure](documentdb-create-account.md).

## <a name="getting-started"></a>Prise en main

Suivez les instructions dans [Installer Azure CLI 2.0][install-az-cli2] afin de configurer votre environnement de développement avec Azure CLI 2.0.

Connectez-vous à votre compte Azure en exécutant la commande suivant et en suivant la procédure s’affichant à l’écran.

```azurecli
az login
```

Si vous ne disposez pas encore de [groupe de ressources](../azure-resource-manager/resource-group-overview.md#resource-groups), créez-en un :

```azurecli
az group create --name <resourcegroupname> --location <resourcegrouplocation>
az group list
```

`<resourcegrouplocation>` doit être l’une des régions dans lesquelles Azure Cosmos DB est généralement disponible. La liste actuelle des régions est présentée sur la [page Régions Azure](https://azure.microsoft.com/regions/#services).

### <a name="notes"></a>Remarques

* Exécutez az documentdb -h pour récupérer une liste complète des commandes disponibles, ou consultez la [page de référence][az-documentdb-ref].
* Exécutez az documentdb &lt;command&gt; -h pour récupérer une liste des détails des paramètres obligatoires et facultatifs par commande.

## <a name="register-your-subscription-to-use-azure-cosmos-db"></a>Inscrire son abonnement pour utiliser Azure Cosmos DB

Cette commande inscrit votre abonnement pour une utilisation d’Azure Cosmos DB par le biais de la CLI.

```azurecli
az provider register -n Microsoft.DocumentDB 
```

## <a id="create-documentdb-account-cli"></a> Créer un compte de base de données Azure Cosmos DB

Cette commande vous permet de créer un compte de base de données Azure Cosmos DB. Configurez votre nouveau compte de base de données pour une seule ou [plusieurs régions][scaling-globally] avec une [stratégie de cohérence](documentdb-consistency-levels.md) déterminée.

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account. The account 
                                    name must be unique.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the Azure Cosmos DB database account.
                                    Allowed values: BoundedStaleness, Eventual, Session, Strong.
    --ip-range-filter             : Firewall support. Specifies the set of IP addresses or IP
                                    address ranges in CIDR form to be included as the allowed list
                                    of client IPs for a given database account. IP addresses/ranges
                                    must be comma separated and must not contain any spaces.
    --kind                        : The type of Azure Cosmos DB database account to create.  Allowed
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

```azurecli
az documentdb create -g <resourcegroupname> -n <uniquedocumentdbaccountname> --kind <typeofdatabaseaccount>
```

Exemples : 

    az documentdb create -g rg-test -n docdb-test
    az documentdb create -g rg-test -n docdb-test --kind MongoDB
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 "South Central US"=2
    az documentdb create -g rg-test -n docdb-test --ip-range-filter "13.91.6.132,13.91.6.1/24"
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 --default-consistency-level BoundedStaleness --max-interval 10 --max-staleness-prefix 200

### <a name="notes"></a>Remarques 
* Les emplacements doivent correspondre à des régions dans lesquelles Azure Cosmos DB est généralement disponible. La liste actuelle des régions est présentée sur la [page Régions Azure](https://azure.microsoft.com/regions/#services).
* Pour activer l’accès au portail, incluez l’adresse IP du portail Azure pour votre région dans le filtre de plages d’adresses IP (ip-range-filter), comme spécifié dans [Configuration de la stratégie de contrôle d’accès IP](documentdb-firewall-support.md#configure-ip-policy).

## <a id="update-documentdb-account-cli"></a> Mettre à jour un compte de base de données Azure Cosmos DB

Cette commande vous permet de mettre à jour les propriétés de votre compte de base de données Azure Cosmos DB. Les informations mises à jour sont notamment la stratégie de cohérence et les emplacements hébergeant le compte de base de données.

> [!NOTE]
> Cette commande, qui vous permet d’ajouter ou de supprimer des régions, ne prend aucunement en charge la modification des priorités de basculement. Pour modifier les priorités de basculement, consultez la section [ci-dessous](#modify-failover-priority-powershell).

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the Azure Cosmos DB database account.
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

## <a id="add-remove-region-documentdb-account-cli"></a> Ajouter/Supprimer une région dans un compte de base de données Azure Cosmos DB

Pour ajouter ou supprimer des régions dans un compte existant de base de données Azure Cosmos DB, utilisez la commande [update](#update-documentdb-account-cli) avec l’indicateur `--locations`. L’exemple ci-dessous illustre la création d’un compte, ainsi que les opérations ultérieures d’ajout et de suppression de régions de ce compte.

Exemple :

    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1
    az documentdb update -g rg-test -n docdb-test --locations "East US"=0 "North Europe"=1 "South Central US"=2


## <a id="delete-documentdb-account-cli"></a> Supprimer un compte de base de données Azure Cosmos DB

Cette commande vous permet de supprimer un compte existant de base de données Azure Cosmos DB.

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Exemple :

    az documentdb delete -g rg-test -n docdb-test

## <a id="get-documentdb-properties-cli"></a> Obtenir les propriétés d’un compte de base de données Azure Cosmos DB

Cette commande vous permet d’obtenir les propriétés d’un compte existant de base de données Azure Cosmos DB.

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Exemple :

    az documentdb show -g rg-test -n docdb-test

## <a id="list-account-keys-cli"></a> Lister des clés de comptes

Lorsque vous créez un compte Azure Cosmos DB, le service génère deux clés d’accès maître qui peuvent être utilisées pour l’authentification lors de l’accès au compte Azure Cosmos DB. En fournissant deux clés d’accès, Azure Cosmos DB vous permet de régénérer les clés sans interruption de votre compte Azure Cosmos DB. Des clés en lecture seule pour l’authentification des opérations en lecture seule sont également disponibles. Il existe deux clés en lecture et écriture (primaire et secondaire) et deux clés en lecture seule (primaire et secondaire).

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Exemple :

    az documentdb list-keys -g rg-test -n docdb-test

## <a id="list-connection-strings-cli"></a> Lister les chaînes de connexion

Pour les comptes MongoDB, vous pouvez récupérer la chaîne de connexion pour connecter votre application MongoDB au compte de base de données à l’aide de la commande suivante.

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Exemple :

    az documentdb list-connection-strings -g rg-test -n docdb-test

## <a id="regenerate-account-key-cli"></a> Régénérer la clé de compte

Vous devez modifier périodiquement les clés d’accès à votre compte Azure Cosmos DB pour garantir la sécurité des connexions. Deux clés d’accès vous sont affectées afin de vous permettre de conserver des connexions au compte Azure Cosmos DB à l’aide d’une clé d’accès lorsque vous régénérez l’autre clé.

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
    --key-kind          [Required]: The access key to regenerate.  Allowed values: primary, primaryReadonly,
                                    secondary, secondaryReadonly.
```

Exemple :

    az documentdb regenerate-key -g rg-test -n docdb-test --key-kind secondary

## <a id="modify-failover-priority-cli"></a> Modifier la priorité de basculement d’un compte de base de données Azure Cosmos DB

Pour les comptes de base de données multirégions, vous pouvez modifier la priorité de basculement des différentes régions dans lesquelles le compte de base de données Azure Cosmos DB existe. Pour plus d’informations sur le basculement dans votre compte de base de données Azure Cosmos DB, consultez la section [Azure Cosmos DB, un service de base de données mondialement distribué sur Azure](documentdb-distribute-data-globally.md).

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
    --failover-policies [Required]: Space separated failover policies in 'regionName=failoverPriority' format.
                                    E.g "East US"=0 "West US"=1.
```

Exemple :

    az documentdb failover-priority-change "East US"=1 "West US"=0 "South Central US"=2

## <a name="next-steps"></a>Étapes suivantes

* Pour vous connecter avec .NET, consultez la page [Se connecter et effectuer des requêtes avec .NET](../cosmos-db/create-documentdb-dotnet.md).
* Pour vous connecter avec .NET Core, consultez la page [Connect and query with .NET Core (Se connecter et effectuer des requêtes avec .NET Core)](../cosmos-db/create-documentdb-dotnet-core.md).
* Pour vous connecter avec Node.js, consultez [Connect and query with Node.js and a MongoDB app (Se connecter et exécuter des requêtes avec Node.js et une application MongoDB)](../cosmos-db/create-mongodb-nodejs.md).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[scaling-globally]: https://azure.microsoft.com/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet
[install-az-cli2]: https://docs.microsoft.com/cli/azure/install-az-cli2
[az-documentdb-ref]: https://docs.microsoft.com/cli/azure/documentdb
[az-documentdb-create-ref]: https://docs.microsoft.com/cli/azure/documentdb#create
[rp-rest-api]: https://docs.microsoft.com/rest/api/documentdbresourceprovider/


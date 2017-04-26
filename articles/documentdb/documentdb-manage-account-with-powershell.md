---
title: Automatisation Azure DocumentDB - Gestion avec Powershell | Microsoft Docs
description: "Utilisez Azure Powershell pour gérer vos comptes de base de données DocumentDB. DocumentDB est une base de données NoSQL basée sur le cloud pour les données JSON."
services: documentdb
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: dimakwan
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 1d7691fd5248691f42ad31224f2fff9f7f0d4b9e
ms.lasthandoff: 04/03/2017


---
# <a name="automate-azure-documentdb-account-management-using-azure-powershell"></a>Automatiser la gestion des comptes Azure DocumentDB à l’aide d’Azure Powershell
> [!div class="op_single_selector"]
> * [portail Azure](documentdb-create-account.md)
> * [Azure CLI 1.0](documentdb-automation-resource-manager-cli-nodejs.md)
> * [Azure CLI 2.0](documentdb-automation-resource-manager-cli.md)
> * [Azure PowerShell](documentdb-manage-account-with-powershell.md)

Le guide suivant décrit les commandes permettant d’automatiser la gestion de vos comptes de base de données DocumentDB à l’aide d’Azure Powershell. Il inclut également des commandes dédiées à la gestion des clés de comptes et des priorités de basculement dans des [comptes de base de données multi-régions][scaling-globally]. La mise à jour de votre compte de base de données vous permet de modifier les stratégies de cohérence et d’ajouter/de supprimer des régions. Pour une gestion multiplateforme de votre compte de base de données DocumentDB, vous pouvez utiliser l’interface [Azure CLI](documentdb-automation-resource-manager-cli.md), l’[API REST Resource Provider][rp-rest-api] ou le [portail Azure](documentdb-create-account.md).

## <a name="getting-started"></a>Mise en route

Suivez les instructions de la page [Installation et configuration d’Azure PowerShell][powershell-install-configure] pour procéder à l’installation et vous connecter à votre compte Azure Resource Manager dans Powershell.

### <a name="notes"></a>Remarques

* Si vous voulez exécuter les commandes suivantes sans demander la confirmation de l’utilisateur, ajoutez l’indicateur `-Force` à la commande.
* L’ensemble des commandes suivantes sont synchrones.

## <a id="create-documentdb-account-powershell"></a> Création d’un compte de base de données DocumentDB

Cette commande vous permet de créer un compte de base de données DocumentDB. Configurez votre nouveau compte de base de données pour une seule ou [plusieurs régions][scaling-globally] avec une [stratégie de cohérence](documentdb-consistency-levels.md) déterminée.

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $DocumentDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name>  -Location "<resource-group-location>" -Name <database-account-name> -PropertyObject $DocumentDBProperties
    
* `<write-region-location>` Le nom d’emplacement de la région d’écriture du compte de base de données. Cet emplacement doit présenter une valeur de priorité de basculement de 0. Il doit y avoir exactement une région d’écriture par compte de base de données.
* `<read-region-location>` Le nom d’emplacement de la région de lecture du compte de base de données. L’emplacement doit présenter une valeur de priorité de basculement supérieure à 0. Un compte de base de données peut présenter plusieurs régions de lecture.
* `<ip-range-filter>` Spécifie le jeu d’adresses IP ou des plages d’adresses IP au format CIDR pour les ajouter à la liste d’adresses IP clientes autorisées pour un compte de base de données particulier. Les plages/adresses IP doivent être séparées par des virgules et ne doivent pas contenir d’espaces. Pour plus d’informations, consultez [Prise en charge du pare-feu dans DocumentDB](documentdb-firewall-support.md).
* `<default-consistency-level>` Le niveau de cohérence par défaut du compte DocumentDB. Pour plus d’informations, consultez [Niveaux de cohérence dans DocumentDB](documentdb-consistency-levels.md).
* `<max-interval>` Utilisée avec la cohérence en fonction de l’obsolescence, cette valeur représente la durée (en secondes) d’obsolescence tolérée. Les valeurs acceptables sont comprises entre 1 et 100.
* `<max-staleness-prefix>` Utilisée avec la cohérence en fonction de l’obsolescence, cette valeur représente le nombre de requêtes d’obsolescence tolérées. Les valeurs acceptables sont 1-2, 147, 483 et 647.
* `<resource-group-name>` Le nom du [groupe de ressources Azure][azure-resource-groups] auquel appartient le nouveau compte de base de données DocumentDB.
* `<resource-group-location>` L’emplacement du groupe de ressources Azure auquel appartient le nouveau compte de base de données DocumentDB.
* `<database-account-name>` Le nom du compte de base de données DocumentDB à créer. Cette valeur accepte uniquement les lettres minuscules, les nombres, le caractère « - », et doit présenter entre 3 et 50 caractères.

Exemple : 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $DocumentDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Location "West US" -Name "docdb-test" -PropertyObject $DocumentDBProperties

### <a name="notes"></a>Remarques
* L’exemple précédent crée un compte de base de données avec deux régions. Il est également possible de créer un compte de base de données avec l’une des régions (désignée comme la région d’écriture, elle présente une valeur de priorité de basculement de 0) ou plus de deux régions. Pour plus d’informations, consultez la section des [comptes de base de données multi-régions][scaling-globally].
* Les emplacements doivent correspondre à des régions dans lesquelles DocumentDB est généralement disponible. La liste actuelle des régions est présentée sur la [page Régions Azure](https://azure.microsoft.com/regions/#services).

## <a id="update-documentdb-account-powershell"></a> Mise à jour d’un compte de base de données DocumentDB

Cette commande vous permet de mettre à jour les propriétés de votre compte de base de données DocumentDB. Les informations mises à jour sont notamment la stratégie de cohérence et les emplacements hébergeant le compte de base de données.

> [!NOTE]
> Cette commande, qui vous permet d’ajouter ou de supprimer des régions, ne prend aucunement en charge la modification des priorités de basculement. Pour modifier les priorités de basculement, consultez la section [ci-dessous](#modify-failover-priority-powershell).

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $DocumentDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name> -Name <database-account-name> -PropertyObject $DocumentDBProperties
    
* `<write-region-location>` Le nom d’emplacement de la région d’écriture du compte de base de données. Cet emplacement doit présenter une valeur de priorité de basculement de 0. Il doit y avoir exactement une région d’écriture par compte de base de données.
* `<read-region-location>` Le nom d’emplacement de la région de lecture du compte de base de données. L’emplacement doit présenter une valeur de priorité de basculement supérieure à 0. Un compte de base de données peut présenter plusieurs régions de lecture.
* `<default-consistency-level>` Le niveau de cohérence par défaut du compte DocumentDB. Pour plus d’informations, consultez [Niveaux de cohérence dans DocumentDB](documentdb-consistency-levels.md).
* `<ip-range-filter>` Spécifie le jeu d’adresses IP ou des plages d’adresses IP au format CIDR pour les ajouter à la liste d’adresses IP clientes autorisées pour un compte de base de données particulier. Les plages/adresses IP doivent être séparées par des virgules et ne doivent pas contenir d’espaces. Pour plus d’informations, consultez [Prise en charge du pare-feu dans DocumentDB](documentdb-firewall-support.md).
* `<max-interval>` Utilisée avec la cohérence en fonction de l’obsolescence, cette valeur représente la durée (en secondes) d’obsolescence tolérée. Les valeurs acceptables sont comprises entre 1 et 100.
* `<max-staleness-prefix>` Utilisée avec la cohérence en fonction de l’obsolescence, cette valeur représente le nombre de requêtes d’obsolescence tolérées. Les valeurs acceptables sont 1-2, 147, 483 et 647.
* `<resource-group-name>` Le nom du [groupe de ressources Azure][azure-resource-groups] auquel appartient le nouveau compte de base de données DocumentDB.
* `<resource-group-location>` L’emplacement du groupe de ressources Azure auquel appartient le nouveau compte de base de données DocumentDB.
* `<database-account-name>` Le nom du compte de base de données DocumentDB à mettre à jour.

Exemple : 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $DocumentDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -PropertyObject $DocumentDBProperties

## <a id="delete-documentdb-account-powershell"></a> Suppression d’un compte de base de données DocumentDB

Cette commande vous permet de supprimer un compte existant de base de données DocumentDB.

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"
    
* `<resource-group-name>` Le nom du [groupe de ressources Azure][azure-resource-groups] auquel appartient le nouveau compte de base de données DocumentDB.
* `<database-account-name>` Le nom du compte de base de données DocumentDB à supprimer.

Exemple :

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="get-documentdb-properties-powershell"></a> Obtention des propriétés d’un compte de base de données DocumentDB

Cette commande vous permet d’obtenir les propriétés d’un compte existant de base de données DocumentDB.

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` Le nom du [groupe de ressources Azure][azure-resource-groups] auquel appartient le nouveau compte de base de données DocumentDB.
* `<database-account-name>` Le nom du compte de base de données Document DB.

Exemple :

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="update-tags-powershell"></a> Mise à jour des balises d’un compte de base de données DocumentDB

L’exemple suivant vous explique comment définir les [balises des ressources Azure][azure-resource-tags] pour votre compte de base de données DocumentDB.

> [!NOTE]
> Cette commande peut être combinée aux commandes de création ou de mise à jour, via l’ajout de l’indicateur `-Tags` avec le paramètre correspondant.

Exemple :

    $tags = @{"dept" = "Finance”; environment = “Production”}
    Set-AzureRmResource -ResourceType “Microsoft.DocumentDB/databaseAccounts”  -ResourceGroupName "rg-test" -Name "docdb-test" -Tags $tags

## <a id="list-account-keys-powershell"></a> Liste des clés de comptes

Lorsque vous créez un compte DocumentDB, le service génère deux clés d'accès maître qui peuvent être utilisées pour l'authentification lors de l'accès au compte DocumentDB. En fournissant deux clés d'accès, DocumentDB vous permet de régénérer les clés sans interruption de votre compte DocumentDB. Des clés en lecture seule pour l’authentification des opérations en lecture seule sont également disponibles. Il existe deux clés en lecture et écriture (primaire et secondaire) et deux clés en lecture seule (primaire et secondaire).

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` Le nom du [groupe de ressources Azure][azure-resource-groups] auquel appartient le nouveau compte de base de données DocumentDB.
* `<database-account-name>` Le nom du compte de base de données Document DB.

Exemple :

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="list-connection-strings-powershell"></a> Répertorier les chaînes de connexion

Pour les comptes MongoDB, vous pouvez récupérer la chaîne de connexion pour connecter votre application MongoDB au compte de base de données à l’aide de la commande suivante.

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` Le nom du [groupe de ressources Azure][azure-resource-groups] auquel appartient le nouveau compte de base de données DocumentDB.
* `<database-account-name>` Le nom du compte de base de données Document DB.

Exemple :

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="regenerate-account-key-powershell"></a> Régénération de la clé de compte

Vous devez modifier périodiquement les clés d'accès à votre compte DocumentDB pour garantir la sécurité des connexions. Deux clés d'accès vous sont affectées afin de vous permettre de conserver des connexions au compte DocumentDB à l'aide d'une clé d'accès lorsque vous régénérez l'autre clé.

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"keyKind"="<key-kind>"}

* `<resource-group-name>` Le nom du [groupe de ressources Azure][azure-resource-groups] auquel appartient le nouveau compte de base de données DocumentDB.
* `<database-account-name>` Le nom du compte de base de données Document DB.
* `<key-kind>` L’un des quatre types de clés : ["Primary"|"Secondary"|"PrimaryReadonly"|"SecondaryReadonly"] que vous souhaitez régénérer.

Exemple :

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"keyKind"="Primary"}

## <a id="modify-failover-priority-powershell"></a> Modification de la priorité de basculement d’un compte de base de données DocumentDB

Pour les comptes de base de données multi-régions, vous pouvez modifier la priorité de basculement des différentes régions dans lesquelles existe le compte de base de données DocumentDB. Pour plus d’informations sur le basculement dans votre compte de base de données DocumentDB, consultez la section [Distribution mondiale des données avec DocumentDB][distribute-data-globally].

    $failoverPolicies = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0},@{"locationName"="<read-region-location>"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"failoverPolicies"=$failoverPolicies}

* `<write-region-location>` Le nom d’emplacement de la région d’écriture du compte de base de données. Cet emplacement doit présenter une valeur de priorité de basculement de 0. Il doit y avoir exactement une région d’écriture par compte de base de données.
* `<read-region-location>` Le nom d’emplacement de la région de lecture du compte de base de données. L’emplacement doit présenter une valeur de priorité de basculement supérieure à 0. Un compte de base de données peut présenter plusieurs régions de lecture.
* `<resource-group-name>` Le nom du [groupe de ressources Azure][azure-resource-groups] auquel appartient le nouveau compte de base de données DocumentDB.
* `<database-account-name>` Le nom du compte de base de données Document DB.

Exemple :

    $failoverPolicies = @(@{"locationName"="East US"; "failoverPriority"=0},@{"locationName"="West US"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"failoverPolicies"=$failoverPolicies}

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[powershell-install-configure]: https://docs.microsoft.com/en-us/azure/powershell-install-configure
[scaling-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet
[distribute-data-globally]: https://docs.microsoft.com/en-us/azure/documentdb/documentdb-distribute-data-globally
[azure-resource-groups]: https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/en-us/rest/api/documentdbresourceprovider/

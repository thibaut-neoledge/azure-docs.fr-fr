---
title: "Gestion des ressources avec l’interface CLI Azure | Microsoft Docs"
description: "Utiliser l’interface de ligne de commande Azure (CLI) pour gérer les groupes et les ressources Azure"
editor: 
manager: timlt
documentationcenter: 
author: tfitzmac
services: azure-resource-manager
ms.assetid: bb0af466-4f65-4559-ac3a-43985fa096ff
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 08/22/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e4d94d3f9736378d93e93be6645ed04ade763ca3
ms.openlocfilehash: 39ccb6e9b810d1c02a96b25946893ddd3e316463


---
# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>Utiliser l’interface de ligne de commande Azure pour gérer les ressources et les groupes de ressources Azure
> [!div class="op_single_selector"]
> * [Portail](resource-group-portal.md) 
> * [Interface de ligne de commande Azure](xplat-cli-azure-resource-manager.md)
> * [Azure PowerShell](powershell-azure-resource-manager.md)
> * [API REST](resource-manager-rest-api.md)
> 
> 

L’interface de ligne de commande Azure (CLI Azure) est l’un des nombreux outils que vous pouvez utiliser pour déployer et gérer des ressources avec Azure Resource Manager. Cet article présente des méthodes courantes pour gérer des ressources et groupes de ressources Azure en utilisant l’interface de ligne de commande Azure en mode Azure Resource Manager. Pour plus d’informations sur l’utilisation de l’interface de ligne de commande afin de déployer des ressources, voir [Déployer des ressources à l’aide de modèles Resource Manager et de l’interface de ligne de commande Azure](resource-group-template-deploy-cli.md). Pour plus d’informations sur les ressources Azure et Resource Manager, voir [Présentation d’Azure Resource Manager](resource-group-overview.md).

> [!NOTE]
> Pour gérer les ressources Azure avec l’interface de ligne de commande Azure, vous devez [installer l’interface de ligne de commande Azure](../xplat-cli-install.md) et vous [connecter à Azure](../xplat-cli-connect.md) en utilisant la commande `azure login`. Assurez-vous que l’interface de ligne de commande est en mode Resource Manager (exécutez `azure config mode arm`). Si ces opérations ont déjà été effectuées, vous pouvez dès à présent créer et gérer ces ressources.
> 
> 

## <a name="get-resource-groups-and-resources"></a>Obtenir des ressources et des groupes de ressources
### <a name="resource-groups"></a>Groupes de ressources
Pour obtenir une liste de tous les groupes de ressources dans votre abonnement et leurs emplacements, exécutez cette commande.

    azure group list


### <a name="resources"></a>Ressources
 Pour répertorier toutes les ressources d’un groupe, par exemple une ressource ayant pour nom *testRG*, utilisez la commande suivante.

    azure resource list testRG

Pour afficher une ressource individuelle dans le groupe, par exemple une machine virtuelle *MyUbuntuVM*, utilisez une commande du type de celle qui suit.

    azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"

Notez le paramètre **Microsoft.Compute/virtualMachines**. Il indique le type de la ressource sur laquelle vous demandez des informations.

> [!NOTE]
> Quand vous utilisez des commandes **azure resource** autres que la commande **list**, vous devez spécifier la version de l’API de la ressource avec laquelle vous travaillez au moyen du paramètre **-o**. Si vous ne connaissez pas la version de l’API à utiliser, consultez le fichier de modèle et recherchez le champ apiVersion de la ressource. Pour plus d’informations sur les versions d’API dans Resource Manager, voir [Fournisseurs, régions, schémas et versions d’API Resource Manager](resource-manager-supported-services.md).
> 
> 

Lorsque vous affichez des détails sur une ressource, il est souvent utile d'utiliser le paramètre `--json`. Il rend la sortie plus lisible, car certaines valeurs sont des structures imbriquées ou des ensembles de valeurs collectées. L’exemple suivant montre les résultats renvoyés par la commande **show** dans un document JSON.

    azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json

> [!NOTE]
> Si vous le voulez, vous pouvez enregistrer les données JSON dans un fichier au moyen du caractère&gt; pour diriger la sortie vers un fichier. Par exemple :
> 
> `azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json > myfile.json`
> 
> 

### <a name="tags"></a>Balises
[!INCLUDE [resource-manager-tag-resources-cli](../../includes/resource-manager-tag-resources-cli.md)]

## <a name="manage-resources"></a>Gestion des ressources
Pour ajouter une ressource telle qu’un compte de stockage à un groupe de ressources, exécutez une commande du type :

    azure resource create testRG MyStorageAccount "Microsoft.Storage/storageAccounts" "westus" -o "2015-06-15" -p "{\"accountType\": \"Standard_LRS\"}"

En plus de spécifier la version de l’API de la ressource avec le paramètre **-o**, utilisez le paramètre **-p** pour transmettre une chaîne au format JSON contenant toutes les propriétés requises ou supplémentaires.

Pour supprimer une ressource existante, par exemple une ressource de machine virtuelle, utilisez une commande comme dans l’exemple suivant.

    azure resource delete testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"

Pour déplacer des ressources existantes vers un autre groupe de ressources ou un autre abonnement, exécutez la commande **azure resource move** . L’exemple suivant montre comment déplacer un cache Redis vers un nouveau groupe de ressources. Dans le paramètre **-i** , spécifiez une liste séparée par des virgules des ID des ressources à déplacer.

    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"

## <a name="control-access-to-resources"></a>Contrôler l’accès aux ressources
Vous pouvez utiliser l’interface de ligne de commande Azure pour créer et gérer des stratégies et ainsi contrôler l’accès aux ressources Azure. Pour plus d’informations sur les définitions de stratégie et l’affectation de stratégies aux ressources, voir [Utiliser la stratégie pour gérer les ressources et contrôler l’accès](resource-manager-policy.md).

Par exemple, définissez la stratégie suivante pour refuser toutes les demandes dans lesquelles l’emplacement n’est pas Ouest des États-Unis ou Amérique du Nord, puis enregistrez-la dans le fichier de définition de stratégie policy.json :

    {
    "if" : {
        "not" : {
        "field" : "location",
        "in" : ["westus" ,  "northcentralus"]
        }
    },
    "then" : {
        "effect" : "deny"
    }
    }

Exécutez ensuite la commande **policy definition create** :

    azure policy definition create MyPolicy -p c:\temp\policy.json

Cette commande propose une sortie qui ressemble à ce qui suit.

    + Creating policy definition MyPolicy data:    PolicyName:             MyPolicy data:    PolicyDefinitionId:     /subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/MyPolicy

    data:    PolicyType:             Custom data:    DisplayName:            undefined data:    Description:            undefined data:    PolicyRule:             field=location, in=[westus, northcentralus], effect=deny

 Pour attribuer une stratégie correspondant à l’étendue souhaitée, utilisez la valeur **PolicyDefinitionId** renvoyée par la commande précédente. Dans l’exemple suivant, cette étendue est l’abonnement, mais il peut s’agir de ressources individuelles ou de groupes de ressources :

    azure policy assignment create MyPolicyAssignment -p /subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/MyPolicy -s /subscriptions/########-####-####-####-############/

Vous pouvez obtenir, modifier ou supprimer des définitions de stratégie à l’aide des commandes **policy definition show**, **policy definition set** et **policy definition delete**.

De même, vous pouvez obtenir, modifier ou supprimer des affectations de stratégie à l’aide des commandes **policy assignment show**, **policy assignment set** et **policy assignment delete**.

## <a name="export-a-resource-group-as-a-template"></a>Exporter un groupe de ressources en tant que modèle
Vous pouvez afficher le modèle Resource Manager pour un groupe de ressources existant. L’exportation du modèle offre deux avantages :

1. Vous pouvez facilement automatiser les prochains déploiements de la solution, car l’ensemble de l’infrastructure est défini dans le modèle.
2. Vous pouvez vous familiariser avec la syntaxe de modèle en regardant dans la JSON qui représente votre solution.

À l’aide de l’interface de ligne de commande Azure, vous pouvez exporter un modèle qui représente l’état actuel de votre groupe de ressources ou télécharger le modèle qui a été utilisé pour un déploiement spécifique.

* **L’exportation du modèle pour un groupe de ressources** est utile lorsque vous avez apporté des modifications à un groupe de ressources et que vous devez récupérer la représentation JSON de son état actuel. Toutefois, le modèle généré contient uniquement un nombre minimal de paramètres et aucune variable. La plupart des valeurs dans le modèle sont codées en dur. Avant de déployer le modèle généré, vous pouvez convertir plusieurs valeurs en paramètres afin de personnaliser le déploiement pour différents environnements.
  
    Pour exporter le modèle pour un groupe de ressources dans un répertoire local, exécutez la commande `azure group export` comme indiqué dans l’exemple suivant. (Indiquez un répertoire local adapté à l’environnement de votre système d’exploitation.)
  
        azure group export testRG ~/azure/templates/
* **Le téléchargement du modèle pour un déploiement spécifique** est utile lorsque vous avez besoin d’afficher le modèle réel qui a été utilisé pour déployer des ressources. Ce modèle comprend tous les paramètres et toutes les variables définis pour le déploiement d’origine. Toutefois, si un membre de votre organisation a apporté des modifications au groupe de ressources et que celles-ci ne sont pas définies dans le modèle, ce dernier ne représente pas l’état actuel du groupe de ressources.
  
    Pour télécharger le modèle utilisé pour un déploiement spécifique dans un répertoire local, exécutez la commande `azure group deployment template download`. Par exemple :
  
        azure group deployment template download TestRG testRGDeploy ~/azure/templates/downloads/

> [!NOTE]
> L’exportation de modèle est en version préliminaire. Elle n’est pas encore prise en charge par tous les types de ressources. Lorsque vous tentez d’exporter un modèle, une erreur indiquant que certaines ressources n’ont pas été exportées peut s’afficher. Le cas échéant, définissez ces ressources manuellement dans votre modèle après l’avoir téléchargé.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir des informations détaillées sur les opérations de déploiement et résoudre les erreurs de déploiement avec l’interface de ligne de commande Azure, voir [Afficher les opérations de déploiement avec l’interface CLI Azure](resource-manager-troubleshoot-deployments-cli.md).
* Si vous souhaitez utiliser l’interface de ligne de commande pour configurer une application ou un script afin d’accéder aux ressources, voir [Créer un principal du service pour accéder aux ressources à l’aide de l’interface de ligne de commande (CLI) Azure](resource-group-authenticate-service-principal-cli.md).
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](resource-manager-subscription-governance.md).




<!--HONumber=Dec16_HO2-->



---
title: "Déploiement Resource Manager et déploiement classique | Microsoft Docs"
description: "Décrit les différences entre le modèle de déploiement de Resource Manager et le modèle de déploiement classique (ou de gestion des services)."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 7ae0ffa3-c8da-4151-bdcc-8f4f69290fb4
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/17/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: c56a1dc92c27d55b90554e23dd9763ff98ce6cc7
ms.lasthandoff: 03/15/2017


---
# <a name="azure-resource-manager-vs-classic-deployment-understand-deployment-models-and-the-state-of-your-resources"></a>Déploiement Azure Resource Manager et déploiement classique : comprendre les modèles de déploiement et l’état de vos ressources
Dans cette rubrique, vous allez découvrir le modèle de déploiement Azure Resource Manager et le modèle de déploiement classique, l’état de vos ressources et la raison pour laquelle vos ressources ont été déployées avec l’un ou l’autre modèle de déploiement. Resource Manager et les modèles de déploiement classique représentent deux façons différentes de déployer et de gérer vos solutions Azure. Vous les utilisez avec deux ensembles différents d’API et les ressources déployées peuvent contenir des différences importantes. Les deux modèles ne sont pas entièrement compatibles entre eux. Cette rubrique décrit ces différences.

Pour simplifier le déploiement et la gestion des ressources, Microsoft recommande l’utilisation de Resource Manager pour les nouvelles ressources. Microsoft recommande, si possible, un nouveau déploiement des ressources existantes via Resource Manager.

Si vous n’êtes pas familier avec Resource Manager, nous vous recommandons de commencer par passer en revue la terminologie définie dans l’article [Vue d’ensemble d’Azure Resource Manager](resource-group-overview.md).

## <a name="history-of-the-deployment-models"></a>Historique des modèles de déploiement
À l’origine, Azure fournissait uniquement le modèle de déploiement classique. Dans ce modèle, chaque ressource existait indépendamment. Il n’existait aucun moyen de regrouper des ressources associées. Au lieu de cela, il fallait suivre manuellement les ressources qui composaient la solution ou l’application manuellement, et veiller à les gérer selon une approche coordonnée. Pour déployer une solution, il fallait soit créer chaque ressource individuellement via le portail classique, soit créer un script qui déployait toutes les ressources dans le bon ordre. Pour supprimer une solution, il fallait supprimer chaque ressource individuellement. Il était difficile d’appliquer et de mettre à jour des stratégies de contrôle d’accès pour des ressources liées. Enfin, il n’était pas possible d’appliquer des balises aux ressources afin de les étiqueter à l’aide de termes qui facilitent la surveillance des ressources et la gestion de la facturation.

En 2014, Azure a introduit le modèle de déploiement Resource Manager, et avec celui-ci le concept de groupe de ressources. Un groupe de ressources est un conteneur de ressources qui partagent un cycle de vie commun. Le modèle de déploiement Resource Manager présente plusieurs avantages :

* Vous pouvez déployer, gérer et surveiller tous les services de votre solution comme un groupe, plutôt que de gérer ces services individuellement.
* Vous pouvez déployer votre solution à plusieurs reprises tout au long de son cycle de vie et avoir ainsi l’assurance que vos ressources présentent un état cohérent lors de leur déploiement.
* Vous pouvez appliquer le contrôle d’accès à toutes les ressources de votre groupe de ressources, et ces stratégies sont appliquées automatiquement lorsque de nouvelles ressources sont ajoutées au groupe de ressources.
* Vous pouvez appliquer des balises aux ressources pour organiser logiquement toutes les ressources de votre abonnement.
* Vous pouvez utiliser JavaScript Objet Notation (JSON) pour définir l’infrastructure de votre solution. Le fichier JSON est connu sous le nom de modèle Resource Manager.
* Vous pouvez définir les dépendances entre les ressources afin de les déployer dans le bon ordre.

Quand Resource Manager a été ajouté, toutes les ressources ont été rétroactivement ajoutées aux groupes de ressources par défaut. Si vous créez une ressource via un déploiement classique maintenant, la ressource est automatiquement créée dans un groupe de ressources par défaut pour ce service, même si vous n’avez pas spécifié ce groupe de ressources au moment du déploiement. Toutefois, le fait d’exister simplement au sein d’un groupe de ressources ne signifie pas que la ressource a été convertie en modèle Resource Manager. Nous allons examiner la manière dont chaque service gère les deux modèles de déploiement dans la section qui suit. 

## <a name="understand-support-for-the-models"></a>Présentation de la prise en charge des modèles
Lorsque vous choisissez le modèle de déploiement à utiliser pour vos ressources, trois scénarios sont à prendre en considération :

1. Le service prend en charge Resource Manager et ne fournit qu’un seul type.
2. Le service prend en charge Resource Manager mais fournit deux types : un pour le modèle de déploiement Resource Manager et un pour le modèle classique. Ce scénario s’applique uniquement aux machines virtuelles, aux comptes de stockage et aux réseaux virtuels.
3. Le service ne prend pas en charge Resource Manager.

Pour découvrir si un service prend en charge Resource Manager, consultez la liste des [fournisseurs pris en charge pour Resource Manager](resource-manager-supported-services.md).

Si le service que vous souhaitez utiliser ne prend pas en charge Resource Manager, vous devez continuer à l’aide du déploiement classique.

Si le service prend en charge Resource Manager et **n’est pas** une machine virtuelle, un compte de stockage ou un réseau virtuel, vous pouvez utiliser le déploiement Resource Manager sans le moindre problème.

Pour les machines virtuelles, les comptes de stockage et les réseaux virtuels, si la ressource a été créée via un déploiement classique, vous devez continuer à l’utiliser via des opérations classiques. Si la machine virtuelle, le compte de stockage ou le réseau virtuel a été créé via un déploiement Resource Manager, vous devez continuer à utiliser des opérations de Resource Manager. Cette distinction peut être déroutante lorsque votre abonnement contient un mélange de ressources créées via un déploiement Resource Manager et un déploiement classique. Cette combinaison de ressources peut générer des résultats inattendus, car les ressources ne prennent pas en charge les mêmes opérations.

Dans certains cas, une commande Resource Manager peut récupérer des informations sur une ressource créée via un déploiement classique ou effectuer une tâche d’administration, telle que le déplacement d’une ressource classique vers un autre groupe de ressources, mais ces exemples ne doivent pas donner l’impression que le type prend en charge les opérations de Resource Manager. Par exemple, supposons que vous disposiez d’un groupe de ressources qui contient une machine virtuelle, créée via un déploiement classique. Si vous exécutez la commande PowerShell Resource Manager suivante :

```powershell
Get-AzureRmResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines
```

Elle renvoie la machine virtuelle :

```powershell
Name              : ExampleClassicVM
ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
ResourceName      : ExampleClassicVM
ResourceType      : Microsoft.ClassicCompute/virtualMachines
ResourceGroupName : ExampleGroup
Location          : westus
SubscriptionId    : {guid}
```

Toutefois, l’applet de commande Resource Manager **Get-AzureRmVM** renvoie uniquement les machines virtuelles déployées par le biais de Resource Manager. La commande suivante ne renvoie pas la machine virtuelle créée via un déploiement classique.

```powershell
Get-AzureRmVM -ResourceGroupName ExampleGroup
```

Seules les ressources créées via Resource Manager prennent en charge les balises. Vous ne pouvez pas appliquer des balises aux ressources classiques.

## <a name="resource-manager-characteristics"></a>Caractéristiques de Resource Manager
Pour vous aider à comprendre les deux modèles, examinons maintenant les caractéristiques des types Resource Manager :

* Créé via le [portail Azure](https://portal.azure.com/).
  
     ![portail Azure](./media/resource-manager-deployment-model/portal.png)
  
     Pour calculer, stocker et mettre en réseau des ressources, vous avez la possibilité d’utiliser le Gestionnaire des ressources ou le déploiement classique. Sélectionnez **Gestionnaire des ressources**.
  
     ![Déploiement Resource Manager](./media/resource-manager-deployment-model/select-resource-manager.png)
* Créé avec la version Resource Manager des applets de commande Azure PowerShell. Ces commandes présentent le format *Verb-AzureRmNoun*.

  ```powershell
  New-AzureRmResourceGroupDeployment
  ```

* Créé via [l’API REST Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) pour les opérations REST.
* Créé à l’aide de commandes de l’interface de ligne de commande Azure exécutées en mode **arm** .
  
  ```azurecli
  azure config mode arm
  azure group deployment create
  ```

* Le type de ressource n’inclut pas **(classique)** dans le nom. L’illustration suivante indique le type en tant que **compte de stockage**.
  
    ![application web](./media/resource-manager-deployment-model/resource-manager-type.png)

## <a name="classic-deployment-characteristics"></a>Caractéristiques du déploiement classique
Vous connaissez peut-être aussi le modèle de déploiement classique en tant que modèle de gestion des services.

Les ressources créées dans le modèle de déploiement classique partagent les caractéristiques suivantes :

* Créé via le [portail classique](https://manage.windowsazure.com)
  
     ![portail classique](./media/resource-manager-deployment-model/classic-portal.png)
  
     Ou créées via le portail Azure avec le déploiement **Classique** sélectionné (pour les catégories Calcul, Stockage et Mise en réseau).
  
     ![Déploiement classique](./media/resource-manager-deployment-model/select-classic.png)
* Créées via la version Gestion des services des applets de commande Azure PowerShell. Ces noms de commande présentent le format *Verb-AzureNoun*.

  ```powershell
  New-AzureVM
  ```

* Créées via [l’API REST Gestion des services](https://msdn.microsoft.com/library/azure/ee460799.aspx) pour les opérations REST.
* Créées à l’aide de commandes de l’interface de ligne de commande Azure exécutées en mode **asm** .

  ```azurecli
  azure config mode asm
  azure vm create
  ```
   
* Le type de ressource inclut **(classique)** dans le nom. L’illustration suivante indique le type en tant que **compte de stockage (classique)**.
  
    ![type classique](./media/resource-manager-deployment-model/classic-type.png)

Vous pouvez utiliser le portail Azure pour gérer les ressources créées par un déploiement classique.

## <a name="changes-for-compute-network-and-storage"></a>Modifications en termes de calcul, de réseau et de stockage
Le diagramme suivant affiche les ressources de calcul, de réseau et de stockage déployées via Resource Manager.

![Architecture Resource Manager](./media/resource-manager-deployment-model/arm_arch3.png)

Notez les relations suivantes entre les ressources :

* Toutes les ressources existent au sein d’un groupe de ressources.
* La machine virtuelle dépend d’un compte de stockage spécifique défini dans le fournisseur de ressources de stockage pour stocker ses disques dans le Stockage Blob (obligatoire).
* La machine virtuelle fait référence à une carte d’interface réseau spécifique définie dans le fournisseur de ressources réseau (obligatoire) et un groupe de disponibilité défini dans le fournisseur de ressources de calcul (facultatif).
* La carte d’interface réseau fait référence à l’adresse IP attribuée à la machine virtuelle (obligatoire), au sous-réseau du réseau virtuel de la machine virtuelle (obligatoire) et à un groupe de sécurité réseau (facultatif).
* Le sous-réseau présent au sein d’un réseau virtuel fait référence à un groupe de sécurité réseau (facultatif).
* L’instance d’équilibreur de charge fait référence au pool principal d’adresses IP qui comporte la carte d’interface réseau d’une machine virtuelle (facultatif) et fait référence à l’adresse IP publique ou privée d’un équilibreur de charge (facultative).

Voici les composants et leurs relations concernant le déploiement classique :

![architecture classique](./media/resource-manager-deployment-model/arm_arch1.png)

La solution classique pour l’hébergement d’une machine virtuelle comprend les éléments suivants :

* Un service cloud nécessaire qui agit en tant que conteneur pour héberger des machines virtuelles (calcul). Les machines virtuelles sont automatiquement fournies avec une carte d’interface réseau (NIC, Network Interface Card) et une adresse IP attribuée par Azure. En outre, le service cloud présente une instance d’équilibrage de charge externe, une adresse IP publique et des points de terminaison par défaut permettant un accès à distance et un trafic PowerShell distant pour les machines virtuelles Windows et un trafic Secure Shell (SSH) pour les machines virtuelles Linux.
* Un compte de stockage nécessaire qui stocke les disques durs virtuels d’une machine virtuelle, notamment le système d’exploitation, les disques de données temporaires et supplémentaires (stockage).
* Un réseau virtuel facultatif qui agit en tant que conteneur supplémentaire, dans lequel vous pouvez créer une structure de sous-réseaux et désigner le sous-réseau sur lequel se trouve la machine virtuelle (réseau).

Le tableau suivant décrit les modifications dans l’interaction des fournisseurs de ressources de calcul, de réseau et de stockage :

| Item | Classique | Gestionnaire de ressources |
| --- | --- | --- |
| Service cloud pour machines virtuelles |Le service cloud était un conteneur pour les machines virtuelles nécessitant la disponibilité de la plateforme et l’équilibrage de charge. |Le service cloud n’est plus un objet nécessaire à la création des machines virtuelles avec le nouveau modèle. |
| Virtual Network |Un réseau virtuel est facultatif pour la machine virtuelle. S’il est inclus, le réseau virtuel ne peut pas être déployé avec Resource Manager. |La machine virtuelle nécessite un réseau virtuel déployé avec Resource Manager. |
| Comptes de stockage |La machine virtuelle nécessite un compte de stockage qui stocke les disques durs virtuels pour le système d’exploitation, les disques de données temporaires et supplémentaires. |La machine virtuelle nécessite un compte de stockage pour stocker ses disques dans le Stockage Blob. |
| Groupes à haute disponibilité |La disponibilité de la plateforme était indiquée en configurant le même « AvailabilitySetName » sur les machines virtuelles. Le nombre maximal de domaines d’erreur était de 2. |Le groupe à haute disponibilité est une ressource exposée par le fournisseur Microsoft.Compute. Les machines virtuelles qui nécessitent une haute disponibilité doivent faire partie du groupe à haute disponibilité. Le nombre maximal de domaines d’erreur est maintenant de 3. |
| Groupe d'affinités |Les groupes d’affinités étaient nécessaires pour créer des réseaux virtuels. Toutefois, avec l’introduction des réseaux virtuels régionaux, ils ne sont plus nécessaires. |Pour simplifier, le concept de groupe d’affinités n’existe pas dans les API exposées par le Gestionnaire de ressources Azure. |
| Équilibrage de la charge. |La création d’un service cloud fournit un équilibrage de la charge implicite pour les machines virtuelles déployées. |L’équilibrage de la charge est une ressource exposée par le fournisseur Microsoft.Network. La principale interface réseau des machines virtuelles dont la charge doit être équilibrée doit faire référence à l’équilibrage de la charge. Ces éléments d’équilibrage de la charge peuvent être internes ou externes. Une instance d’équilibreur de charge fait référence au pool principal d’adresses IP qui comporte la carte d’interface réseau d’une machine virtuelle (facultatif) et fait référence à l’adresse IP publique ou privée d’un équilibreur de charge (facultative). [En savoir plus.](../virtual-network/resource-groups-networking.md) |
| Adresse IP virtuelle |Les services cloud obtiennent une adresse IP virtuelle par défaut lorsqu’une machine virtuelle est ajoutée à un service cloud. L’adresse IP virtuelle est l’adresse associée à l’équilibrage de charge implicite. |L’adresse IP est une ressource exposée par le fournisseur Microsoft.Network. L’adresse IP publique peut être statique (réservée) ou dynamique. Les adresses IP publiques dynamiques peuvent être attribuées à un équilibrage de charge. Les adresses IP publiques peuvent être sécurisées à l’aide de groupes de sécurité. |
| Adresses IP réservées |Vous pouvez réserver une adresse IP dans Azure et l’associer à un service cloud pour vous assurer que l’adresse IP est permanente. |L’adresse IP publique peut être créée en mode « Statique ». Elle offre les mêmes fonctionnalités qu’une « Adresse IP réservée ». Les adresses IP publiques statiques peuvent être attribuées dès maintenant à un équilibrage de la charge. |
| Adresse IP publique par machine virtuelle |Les adresses IP publiques peuvent également être associées directement à une machine virtuelle. |L’adresse IP est une ressource exposée par le fournisseur Microsoft.Network. L’adresse IP publique peut être statique (réservée) ou dynamique. Toutefois, seules les adresses IP dynamiques publiques peuvent être affectées à une interface réseau pour obtenir une adresse IP publique par machine virtuelle. |
| Points de terminaison |Les points de terminaison d’entrée doivent être configurés sur la machine virtuelle pour pouvoir ouvrir la connectivité pour certains ports. Un des modes les plus communs de connexion aux machines virtuelles se fait en définissant des points de terminaison d’entrée. |Les règles NAT entrantes peuvent être configurées sur l’équilibrage de la charge pour obtenir les mêmes fonctionnalités d’activation des points de terminaison sur certains ports spécifiques pour se connecter aux machines virtuelles. |
| Nom DNS |Les services cloud obtiennent un nom DNS global unique et implicite. Par exemple : `mycoffeeshop.cloudapp.net`. |Les noms DNS sont des paramètres facultatifs qui peuvent être spécifiés sur une ressource d’adresse IP publique. Le nom de domaine complet se présente au format suivant : `<domainlabel>.<region>.cloudapp.azure.com`. |
| Interfaces réseau |L’interface réseau principale et secondaire et ses propriétés ont été définies en tant que configuration du réseau d’une machine virtuelle. |L’interface réseau est une ressource exposée par le fournisseur Microsoft.Network. Le cycle de vie de l’interface réseau n’est pas lié à une machine virtuelle. L’interface réseau fait référence à l’adresse IP attribuée à la machine virtuelle (obligatoire), au sous-réseau du réseau virtuel de la machine virtuelle (obligatoire) et à un groupe de sécurité réseau (facultatif). |

Pour en savoir plus sur la connexion de réseaux virtuels à partir de modèles de déploiement différents, consultez [Connecter des réseaux virtuels utilisant des modèles de déploiement différents dans le portail](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="migrate-from-classic-to-resource-manager"></a>Migration à partir de déploiements classiques vers des déploiements Resource Manager
Si vous êtes prêt à migrer vos ressources d’un déploiement classique vers un déploiement Resource Manager, consultez :

1. [Étude technique approfondie de la migration prise en charge par la plateforme de ressources Classic vers Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
2. [Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
3. [Migration de ressources IaaS d’un environnement Classic vers Azure Resource Manager à l’aide d’Azure PowerShell](../virtual-machines/virtual-machines-windows-ps-migration-classic-resource-manager.md)
4. [Migration de ressources IaaS d’un environnement Classic vers Azure Resource Manager à l’aide de l’interface de ligne de commande Azure](../virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md)

## <a name="frequently-asked-questions"></a>Forum Aux Questions (FAQ)
**Puis-je créer une machine virtuelle à l’aide d’Azure Resource Manager et la déployer dans un réseau virtuel créé à l’aide d’un déploiement classique ?**

Ce n’est pas pris en charge. Vous ne pouvez pas utiliser Azure Resource Manager pour déployer une machine virtuelle dans un réseau virtuel créé à l’aide du modèle de déploiement classique.

**Puis-je créer une machine virtuelle à l’aide d’Azure Resource Manager à partir d’une image utilisateur créée à l’aide des API de gestion des services Azure ?**

Ce n’est pas pris en charge. Toutefois, vous pouvez copier les fichiers VHD (de disque dur virtuel) à partir d’un compte de stockage créé à l’aide des API de gestion des services et les ajouter dans un nouveau compte créé à l’aide d’Azure Resource Manager.

**Quel est l’impact sur le quota pour mon abonnement ?**

Les quotas pour les machines virtuelles, les réseaux virtuels et les comptes de stockage créés via Azure Resource Manager sont distincts des autres quotas. Chaque abonnement obtient des quotas pour créer les ressources avec les nouvelles API. Plus d’informations sur les quotas supplémentaires sont disponibles [ici](../azure-subscription-service-limits.md).

**Puis-je continuer à utiliser mes scripts automatisés pour approvisionner des machines virtuelles, des réseaux virtuels et des comptes de stockage avec les API Resource Manager ?**

Toutes les automatisations et tous les scripts que vous avez créés continueront à fonctionner pour les machines virtuelles et les réseaux virtuels existants créés en mode Gestion des services Azure. Cependant, les scripts doivent être mis à jour afin qu’ils utilisent le nouveau schéma pour créer les mêmes ressources via le mode Resource Manager.

**Où puis-je trouver des exemples de modèles Azure Resource Manager ?**

Vous trouverez un ensemble complet de modèles de démarrage sur [Modèles de démarrage rapide Azure Resource Manager](https://azure.microsoft.com/documentation/templates/).

## <a name="next-steps"></a>Étapes suivantes
* Pour connaître la procédure pas à pas de création d’un modèle qui définit une machine virtuelle, un compte de stockage et un réseau virtuel, consultez [Guide de création d’un modèle Resource Manager](resource-manager-template-walkthrough.md).
* Pour connaître les commandes permettant de déployer un modèle, consultez [Déploiement d’une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).



<properties
   pageTitle="Déploiement Resource Manager et déploiement classique | Microsoft Azure"
   description="Décrit les différences entre le modèle de déploiement de Resource Manager et le modèle de déploiement classique (ou de gestion des services)."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/27/2016"
   ms.author="tomfitz"/>

# Déploiement Azure Resource Manager et déploiement classique : comprendre les modèles de déploiement et l’état de vos ressources

Dans cette rubrique, vous allez découvrir le modèle de déploiement Azure Resource Manager et le modèle de déploiement classique, l’état de vos ressources et la raison pour laquelle vos ressources ont été déployées avec l’un ou l’autre modèle de déploiement. Le modèle de déploiement Resource Manager contient des différences importantes par rapport au modèle de déploiement classique, et les deux modèles ne sont pas entièrement compatibles entre eux. Pour simplifier le déploiement et la gestion des ressources, Microsoft recommande l’utilisation de Resource Manager pour les nouvelles ressources et, si possible, un nouveau déploiement des ressources existantes via Resource Manager.

Pour la plupart des ressources, vous pouvez passer à Resource Manager sans aucun problème. Cependant, certains fournisseurs de ressources offrent deux versions de la ressource (une pour le modèle classique et une pour Resource Manager) en raison des différences d’architecture entre les modèles. Les fournisseurs de ressources qui font la différence entre les deux modèles sont :

- **Calcul** : il prend en charge les instances de machines virtuelles et les groupes à haute disponibilité facultatifs.
- **Stockage** : il prend en charge les comptes de stockage nécessaires qui stockent les disques durs virtuels pour les machines virtuelles, notamment leur système d’exploitation et les disques de données supplémentaires.
- **Réseau** : il prend en charge les cartes réseau, adresses IP des machines virtuelles et sous-réseaux au sein de réseaux virtuels, ainsi que les équilibreurs de charge facultatifs, les adresses IP des équilibreurs de charge et les groupes de sécurité réseau.

Pour ces types de ressources, vous devez connaître la version que vous utilisez, car les opérations prises en charge diffèrent. Passons en revue les deux modèles pour comprendre lequel a été utilisé pour déployer vos ressources.

## Caractéristiques de Resource Manager

Les ressources créées par le biais de Resource Manager partagent les caractéristiques suivantes :

- Elles sont créées via l’une des méthodes suivantes :

  - Le [portail Azure](https://portal.azure.com/).

        ![Azure portal](./media/resource-manager-deployment-model/preview-portal.png)

        Pour calculer, stocker et mettre en réseau des ressources, vous avez la possibilité d'utiliser le Gestionnaire des ressources ou le déploiement classique.Sélectionnez **Gestionnaire des ressources**.

        ![Resource Manager deployment](./media/resource-manager-deployment-model/select-resource-manager.png)

  - Pour Azure PowerShell, utilisez la version Resource Manager des commandes. Ces commandes ont le format *Verb-AzureRmNoun*, comme illustré ci-dessous.

            Get-AzureRmResourceGroupDeployment

  - [API REST d’Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn790568.aspx) pour les opérations REST.
  - Commandes Azure CLI exécutées en mode **arm**.

            azure config mode arm

- Le type de ressource n’inclut pas **(classique)** dans le nom. L’illustration ci-dessous indique le type en tant que **compte de stockage**.

    ![application web](./media/resource-manager-deployment-model/resource-manager-type.png)

L’application figurant dans le diagramme qui suit montre que les ressources déployées par le biais du Gestionnaire de ressources appartiennent toutes à un seul groupe de ressources.

  ![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch3.png)

Il existe également des relations entre les ressources présentes au sein des fournisseurs de ressources :

- Une machine virtuelle dépend d’un compte de stockage spécifique défini dans le fournisseur de ressources de stockage pour stocker ses disques dans le stockage d’objets blob (obligatoire).
- Une machine virtuelle fait référence à une carte d’interface réseau spécifique définie dans le fournisseur de ressources réseau (obligatoire) et un groupe de haute disponibilité défini dans le fournisseur de ressources de calcul (facultatif).
- Une carte d’interface réseau fait référence à l’adresse IP attribuée à la machine virtuelle (obligatoire), au sous-réseau du réseau virtuel de la machine virtuelle (obligatoire) et à un groupe de sécurité réseau (facultatif).
- Un sous-réseau présent au sein d’un réseau virtuel fait référence à un groupe de sécurité réseau (facultatif).
- Une instance d’équilibreur de charge fait référence au pool principal d’adresses IP qui comporte la carte d’interface réseau d’une machine virtuelle (facultatif) et fait référence à l’adresse IP publique ou privée d’un équilibreur de charge (facultative).

## Caractéristiques du déploiement classique

Vous connaissez peut-être aussi le modèle de déploiement classique en tant que modèle de gestion des services.

Dans Azure Service Management, les ressources relatives au calcul, au stockage ou au réseau qui permettent d’héberger des machines virtuelles sont fournies par :

- Un service cloud nécessaire qui agit en tant que conteneur pour héberger des machines virtuelles (calcul). Les machines virtuelles sont automatiquement fournies avec une carte d’interface réseau (NIC, Network Interface Card) et une adresse IP attribuée par Azure. En outre, le service cloud présente une instance d’équilibrage de charge externe, une adresse IP publique et des points de terminaison par défaut permettant un accès à distance et un trafic PowerShell distant pour les machines virtuelles Windows et un trafic Secure Shell (SSH) pour les machines virtuelles Linux.
- Un compte de stockage nécessaire qui stocke les disques durs virtuels d’une machine virtuelle, notamment le système d’exploitation, les disques de données temporaires et supplémentaires (stockage).
- Un réseau virtuel facultatif qui agit en tant que conteneur supplémentaire, dans lequel vous pouvez créer une structure de sous-réseaux et désigner le sous-réseau sur lequel se trouve la machine virtuelle (réseau).

Les ressources créées dans le modèle de déploiement classique partagent les caractéristiques suivantes :

- Elles sont créées via l’une des méthodes suivantes :

  - [Portail classique](https://manage.windowsazure.com)

        ![Classic portal](./media/resource-manager-deployment-model/azure-portal.png)

        Vous pouvez également accéder au portail préliminaire et spécifier qu'il s'agit d'un déploiement classique (pour les catégories Calcul, Stockage et Mise en réseau)

        ![Classic deployment](./media/resource-manager-deployment-model/select-classic.png)

  - Pour Azure PowerShell, utilisez la version Service Management des commandes. Ces noms de commande ont le format *Verb-AzureNoun*, comme illustré ci-dessous.

            Get-AzureDeployment

  - [API REST de gestion des services](https://msdn.microsoft.com/library/azure/ee460799.aspx) pour les opérations REST.
  - Commandes de l’interface de ligne de commande Azure exécutées en mode **asm** ou par défaut.
- Le type de ressource inclut **(classique)** dans le nom. L’illustration ci-dessous indique le type en tant que **compte de stockage (classique)**.

    ![type classique](./media/resource-manager-deployment-model/classic-type.png)

Vous pouvez toujours utiliser le portail Azure pour gérer les ressources créées par un déploiement classique.

Voici les composants et leurs relations concernant Azure Service Management.

  ![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch1.png)

## Avantages de l’utilisation de Resource Manager et de groupes de ressources

Resource Manager a ajouté le concept de groupe de ressources. Chaque ressource que vous créez via Resource Manager existe dans un groupe de ressources. Le modèle de déploiement de Resource Manager présente plusieurs avantages :

- Vous pouvez déployer, gérer et surveiller tous les services de votre solution comme un groupe, plutôt que de gérer ces services individuellement.
- Vous pouvez déployer votre application à plusieurs reprises tout au long du cycle de vie de l’application et avoir ainsi l’assurance que vos ressources présentent un état cohérent lors de leur déploiement.
- Vous pouvez utiliser des modèles déclaratifs pour définir votre déploiement.
- Vous pouvez définir les dépendances entre les ressources afin de les déployer dans le bon ordre.
- Vous pouvez appliquer le contrôle d’accès à toutes les ressources dans votre groupe de ressources, car le contrôle d’accès en fonction du rôle (RBAC) est intégré en mode natif à la plateforme de gestion.
- Vous pouvez appliquer des balises aux ressources pour organiser logiquement toutes les ressources dans votre abonnement.


Avant Resource Manager, aucune ressource créée via un déploiement classique n’existait dans un groupe de ressources. Quand Resource Manager a été ajouté, toutes les ressources ont été rétroactivement ajoutées aux groupes de ressources par défaut. Si vous créez une ressource via un déploiement classique maintenant, la ressource est automatiquement créée dans un groupe de ressources par défaut pour ce service, même si vous n’avez pas spécifié ce groupe de ressources au moment du déploiement. Toutefois, le fait d’exister simplement au sein d’un groupe de ressources ne signifie pas que la ressource a été convertie en modèle Resource Manager. Pour les machines virtuelles, le stockage et les réseaux virtuels, si la ressource a été créée via un déploiement classique, vous devez continuer à l’utiliser via des opérations classiques.

Vous pouvez déplacer des ressources vers un groupe de ressources différent et ajouter de nouvelles ressources à un groupe de ressources existant. Par conséquent, votre groupe de ressources peut contenir une combinaison de ressources créées via Resource Manager et un déploiement classique. Cette combinaison de ressources peut générer des résultats inattendus, car les ressources ne prennent pas en charge les mêmes opérations.

À l’aide de modèles déclaratifs, vous pourrez simplifier vos scripts de déploiement. Au lieu de tenter de convertir des scripts existants de gestion des services en Resource Manager, envisagez de repenser votre stratégie de déploiement pour tirer parti de la définition de vos infrastructure et configuration dans le modèle.

## Utilisation de balises

Les balises vous permettent d’organiser logiquement vos ressources. Seules les ressources créées via Resource Manager prennent en charge les balises. Vous ne pouvez pas appliquer des balises aux ressources classiques.

Pour plus d’informations sur l’utilisation de balises dans Resource Manager, consultez [Organisation des ressources Azure à l’aide de balises](resource-group-using-tags.md).

## Opérations prises en charge pour les modèles de déploiement

Les ressources que vous avez créées dans le modèle de déploiement classique ne prennent pas en charge les opérations de Resource Manager. Dans certains cas, une commande Resource Manager peut récupérer des informations sur une ressource créée via un déploiement classique ou effectuer des tâches d’administration, telles que le déplacement d’une ressource classique vers un autre groupe de ressources, mais ces exemples ne doivent pas donner l’impression que le type prend en charge les opérations de Resource Manager. Par exemple, supposons que vous disposiez d’un groupe de ressources qui contient une machine virtuelle, créée via un déploiement classique. Si vous exécutez la commande PowerShell suivante :

    Get-AzureRmResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines

Elle renvoie la machine virtuelle :
    
    Name              : ExampleClassicVM
    ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
    ResourceName      : ExampleClassicVM
    ResourceType      : Microsoft.ClassicCompute/virtualMachines
    ResourceGroupName : ExampleGroup
    Location          : westus
    SubscriptionId    : {guid}

Toutefois, l’applet de commande **Get-AzureRmVM** renvoie uniquement les machines virtuelles déployées par le biais de Resource Manager. La commande suivante ne renvoie pas la machine virtuelle créée via un déploiement classique.

    Get-AzureRmVM -ResourceGroupName ExampleGroup

En général, vous ne devez pas vous attendre à ce que les ressources créées via un déploiement classique fonctionnent avec les commandes Resource Manager.

Quand vous utilisez des ressources créées avec Resource Manager, vous devez utiliser les opérations de Resource Manager et non revenir aux opérations de gestion des services.

## Considérations relatives aux machines virtuelles

Plusieurs points importants sont à prendre en compte lors de l’utilisation de machines virtuelles.

- Les machines virtuelles déployées avec le modèle de déploiement classique ne peuvent pas être incluses dans un réseau virtuel déployé avec Resource Manager.
- Les machines virtuelles déployées avec le modèle de déploiement de Resource Manager doivent être incluses dans un réseau virtuel.
- Les machines virtuelles déployées avec le modèle de déploiement classique ne doivent pas être incluses dans un réseau virtuel.

Si les temps d’arrêt ne pénalisent pas vos machines virtuelles, vous pouvez les faire passer du déploiement classique à Resource Manager à l’aide de [scripts PowerShell ASM2ARM](https://github.com/fullscale180/asm2arm).

Pour plus d’informations sur la transition des ressources de calcul, de réseau et de stockage, consultez [Fournisseurs de calcul, de réseau et de stockage Azure dans Azure Resource Manager](./virtual-machines/virtual-machines-windows-compare-deployment-models.md).

Pour en savoir plus sur la connexion de réseaux virtuels à partir de modèles de déploiement différents, consultez [Connexion de réseaux virtuels classiques aux nouveaux réseaux virtuels](./virtual-network/virtual-networks-arm-asm-s2s.md).

## Étapes suivantes

- Pour connaître la procédure pas à pas de création d’un modèle qui définit une machine virtuelle, un compte de stockage et un réseau virtuel, voir [Guide de création d’un modèle Resource Manager](resource-manager-template-walkthrough.md).
- Pour en savoir plus sur la structure des modèles Resource Manager, voir [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
- Pour connaître les commandes permettant de déployer un modèle, consultez [Déploiement d’une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0504_2016-->
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
   ms.date="07/29/2016"
   ms.author="tomfitz"/>

# Déploiement Azure Resource Manager et déploiement classique : comprendre les modèles de déploiement et l’état de vos ressources

Dans cette rubrique, vous allez découvrir le modèle de déploiement Azure Resource Manager et le modèle de déploiement classique, l’état de vos ressources et la raison pour laquelle vos ressources ont été déployées avec l’un ou l’autre modèle de déploiement. Le modèle de déploiement Resource Manager contient des différences importantes par rapport au modèle de déploiement classique, et les deux modèles ne sont pas entièrement compatibles entre eux. Pour simplifier le déploiement et la gestion des ressources, Microsoft recommande l’utilisation de Resource Manager pour les nouvelles ressources et, si possible, un nouveau déploiement des ressources existantes via Resource Manager.

Si vous n’êtes pas du tout familier avec Resource Manager, nous vous recommandons de commencer par passer en revue la terminologie définie dans l’article [résentation d’Azure Resource Manager](resource-group-overview.md).

## Historique des modèles de déploiement

À l’origine, Azure fournissait uniquement le modèle de déploiement classique. Dans ce modèle, chaque ressource existait indépendamment. Il n’existait aucun moyen de regrouper des ressources associées. Au lieu de cela, il fallait suivre manuellement les ressources qui composaient la solution ou l’application manuellement, et veiller à les gérer selon une approche coordonnée. Pour déployer une solution, il fallait soit créer chaque ressource individuellement via le portail classique, soit créer un script qui déployait toutes les ressources dans le bon ordre. Pour supprimer une solution, il fallait supprimer chaque ressource individuellement. Il était difficile d’appliquer et de mettre à jour des stratégies de contrôle d’accès pour des ressources liées. Enfin, il n’était pas possible d’appliquer des balises aux ressources afin de les étiqueter à l’aide de termes qui facilitent la surveillance des ressources et la gestion de la facturation.

En 2014, Azure a introduit le modèle de déploiement Resource Manager, et avec celui-ci le concept de groupe de ressources. Un groupe de ressources est un conteneur de ressources qui partagent un cycle de vie commun. Le modèle de déploiement Resource Manager présente plusieurs avantages :

- Vous pouvez déployer, gérer et surveiller tous les services de votre solution comme un groupe, plutôt que de gérer ces services individuellement.
- Vous pouvez déployer votre solution à plusieurs reprises tout au long de son cycle de vie et avoir ainsi l’assurance que vos ressources présentent un état cohérent lors de leur déploiement.
- Vous pouvez appliquer le contrôle d’accès à toutes les ressources de votre groupe de ressources, et ces stratégies sont appliquées automatiquement lorsque de nouvelles ressources sont ajoutées au groupe de ressources.
- Vous pouvez appliquer des balises aux ressources pour organiser logiquement toutes les ressources dans votre abonnement.
- Vous pouvez utiliser JavaScript Objet Notation (JSON) pour définir l’infrastructure de votre solution. Le fichier JSON est connu sous le nom de modèle Resource Manager.
- Vous pouvez définir les dépendances entre les ressources afin de les déployer dans le bon ordre.

Quand Resource Manager a été ajouté, toutes les ressources ont été rétroactivement ajoutées aux groupes de ressources par défaut. Si vous créez une ressource via un déploiement classique maintenant, la ressource est automatiquement créée dans un groupe de ressources par défaut pour ce service, même si vous n’avez pas spécifié ce groupe de ressources au moment du déploiement. Toutefois, le fait d’exister simplement au sein d’un groupe de ressources ne signifie pas que la ressource a été convertie en modèle Resource Manager. Nous allons examiner la manière dont chaque service gère les deux modèles de déploiement dans la section qui suit.

## Présentation de la prise en charge des modèles 

Lorsque vous choisissez le modèle de déploiement à utiliser pour vos ressources, trois scénarios sont à prendre en considération :

1. Le service prend en charge Resource Manager et ne fournit qu’un seul type.
2. Le service prend en charge Resource Manager mais fournit deux types : un pour le modèle de déploiement Resource Manager et un pour le modèle classique. Cela s’applique uniquement aux machines virtuelles, aux comptes de stockage et aux réseaux virtuels.
3. Le service ne prend pas en charge Resource Manager.

Pour découvrir si un service prend en charge Resource Manager, consultez la liste des [fournisseurs pris en charge pour Resource Manager](resource-manager-supported-services.md).

Si le service que vous souhaitez utiliser ne prend pas en charge Resource Manager, vous devez continuer à l’aide du déploiement classique.

Si le service prend en charge Resource Manager et **n’est pas** une machine virtuelle, un compte de stockage ou un réseau virtuel, vous pouvez utiliser le déploiement Resource Manager sans le moindre problème.

Pour les machines virtuelles, les comptes de stockage et les réseaux virtuels, si la ressource a été créée via un déploiement classique, vous devez continuer à l’utiliser via des opérations classiques. Si la machine virtuelle, le compte de stockage ou le réseau virtuel a été créé via un déploiement Resource Manager, vous devez continuer à utiliser des opérations de Resource Manager. Cette distinction peut être particulièrement déroutante lorsque votre abonnement contient un mélange de ressources créées via un déploiement Resource Manager et un déploiement classique. Cette combinaison de ressources peut générer des résultats inattendus, car les ressources ne prennent pas en charge les mêmes opérations.

Dans certains cas, une commande Resource Manager peut récupérer des informations sur une ressource créée via un déploiement classique ou effectuer une tâche d’administration, telle que le déplacement d’une ressource classique vers un autre groupe de ressources, mais ces exemples ne doivent pas donner l’impression que le type prend en charge les opérations de Resource Manager. Par exemple, supposons que vous disposiez d’un groupe de ressources qui contient une machine virtuelle, créée via un déploiement classique. Si vous exécutez la commande PowerShell Resource Manager suivante :

    Get-AzureRmResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines

Elle renvoie la machine virtuelle :
    
    Name              : ExampleClassicVM
    ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
    ResourceName      : ExampleClassicVM
    ResourceType      : Microsoft.ClassicCompute/virtualMachines
    ResourceGroupName : ExampleGroup
    Location          : westus
    SubscriptionId    : {guid}

Toutefois, l’applet de commande Resource Manager **Get-AzureRmVM** renvoie uniquement les machines virtuelles déployées par le biais de Resource Manager. La commande suivante ne renvoie pas la machine virtuelle créée via un déploiement classique.

    Get-AzureRmVM -ResourceGroupName ExampleGroup

Plusieurs autres points importants sont à prendre en compte lors de l’utilisation de machines virtuelles.

- Les machines virtuelles déployées avec le modèle de déploiement classique ne peuvent pas être incluses dans un réseau virtuel déployé avec Resource Manager.
- Les machines virtuelles déployées avec le modèle de déploiement de Resource Manager doivent être incluses dans un réseau virtuel.
- Les machines virtuelles déployées avec le modèle de déploiement classique ne doivent pas être incluses dans un réseau virtuel.

Pour en savoir plus sur la connexion de réseaux virtuels à partir de modèles de déploiement différents, consultez [Connexion de réseaux virtuels classiques aux nouveaux réseaux virtuels](./virtual-network/virtual-networks-arm-asm-s2s.md).

Seules les ressources créées via Resource Manager prennent en charge les balises. Vous ne pouvez pas appliquer des balises aux ressources classiques. Pour plus d’informations sur l’utilisation de balises dans Resource Manager, consultez [Organisation des ressources Azure à l’aide de balises](resource-group-using-tags.md).

## Caractéristiques de Resource Manager

Pour vous aider à comprendre les deux modèles, examinons maintenant les caractéristiques des types Resource Manager :

- Créé via le [portail Azure](https://portal.azure.com/).

     ![Portail Azure](./media/resource-manager-deployment-model/portal.png)

     Pour calculer, stocker et mettre en réseau des ressources, vous avez la possibilité d’utiliser le Gestionnaire des ressources ou le déploiement classique. Sélectionnez **Gestionnaire des ressources**.

     ![Déploiement Resource Manager](./media/resource-manager-deployment-model/select-resource-manager.png)

- Créé avec la version Resource Manager des applets de commande Azure PowerShell. Ces commandes présentent le format *Verb-AzureRmNoun*, comme illustré ci-dessous.

        New-AzureRmResourceGroupDeployment

- Créé via [l’API REST Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn790568.aspx) pour les opérations REST.

- Créé à l’aide de commandes de l’interface de ligne de commande Azure exécutées en mode **arm**.

        azure config mode arm
        azure group deployment create 

- Le type de ressource n’inclut pas **(classique)** dans le nom. L’illustration ci-dessous indique le type en tant que **compte de stockage**.

    ![application web](./media/resource-manager-deployment-model/resource-manager-type.png)

L’application figurant dans le diagramme qui suit montre que les ressources déployées par le biais du Gestionnaire de ressources appartiennent toutes à un seul groupe de ressources.

  ![Architecture Resource Manager](./media/virtual-machines-azure-resource-manager-architecture/arm_arch3.png)

Il existe également des relations entre les ressources présentes au sein des fournisseurs de ressources :

- Une machine virtuelle dépend d’un compte de stockage spécifique défini dans le fournisseur de ressources de stockage pour stocker ses disques dans le stockage d’objets blob (obligatoire).
- Une machine virtuelle fait référence à une carte d’interface réseau spécifique définie dans le fournisseur de ressources réseau (obligatoire) et un groupe de haute disponibilité défini dans le fournisseur de ressources de calcul (facultatif).
- Une carte d’interface réseau fait référence à l’adresse IP attribuée à la machine virtuelle (obligatoire), au sous-réseau du réseau virtuel de la machine virtuelle (obligatoire) et à un groupe de sécurité réseau (facultatif).
- Un sous-réseau présent au sein d’un réseau virtuel fait référence à un groupe de sécurité réseau (facultatif).
- Une instance d’équilibreur de charge fait référence au pool principal d’adresses IP qui comporte la carte d’interface réseau d’une machine virtuelle (facultatif) et fait référence à l’adresse IP publique ou privée d’un équilibreur de charge (facultative).

## Caractéristiques du déploiement classique

Vous connaissez peut-être aussi le modèle de déploiement classique en tant que modèle de gestion des services.

Les ressources créées dans le modèle de déploiement classique partagent les caractéristiques suivantes :

- Créées via le [portail classique](https://manage.windowsazure.com).

     ![Portail classique](./media/resource-manager-deployment-model/classic-portal.png)

     Ou créées via le portail Azure avec le déploiement **Classique** sélectionné (pour les catégories Calcul, Stockage et Mise en réseau).

     ![Déploiement classique](./media/resource-manager-deployment-model/select-classic.png)

- Créées via la version Gestion des services des applets de commande Azure PowerShell. Ces noms de commande présentent le format *Verb-AzureNoun*, comme illustré ci-dessous.

        New-AzureVM 

- Créées via [l’API REST Gestion des services](https://msdn.microsoft.com/library/azure/ee460799.aspx) pour les opérations REST.
- Créées à l’aide de commandes de l’interface de ligne de commande Azure exécutées en mode **asm**.

        azure config mode asm
        azure vm create 

- Le type de ressource inclut **(classique)** dans le nom. L’illustration ci-dessous indique le type en tant que **compte de stockage (classique)**.

    ![type classique](./media/resource-manager-deployment-model/classic-type.png)

Vous pouvez toujours utiliser le portail Azure pour gérer les ressources créées par un déploiement classique.

Dans Azure Service Management, les ressources relatives au calcul, au stockage ou au réseau qui permettent d’héberger des machines virtuelles sont fournies par :

- Un service cloud nécessaire qui agit en tant que conteneur pour héberger des machines virtuelles (calcul). Les machines virtuelles sont automatiquement fournies avec une carte d’interface réseau (NIC, Network Interface Card) et une adresse IP attribuée par Azure. En outre, le service cloud présente une instance d’équilibrage de charge externe, une adresse IP publique et des points de terminaison par défaut permettant un accès à distance et un trafic PowerShell distant pour les machines virtuelles Windows et un trafic Secure Shell (SSH) pour les machines virtuelles Linux.
- Un compte de stockage nécessaire qui stocke les disques durs virtuels d’une machine virtuelle, notamment le système d’exploitation, les disques de données temporaires et supplémentaires (stockage).
- Un réseau virtuel facultatif qui agit en tant que conteneur supplémentaire, dans lequel vous pouvez créer une structure de sous-réseaux et désigner le sous-réseau sur lequel se trouve la machine virtuelle (réseau).

Voici les composants et leurs relations concernant Azure Service Management.

  ![architecture classique](./media/virtual-machines-azure-resource-manager-architecture/arm_arch1.png)

## Migration de ressources classiques vers Resource Manager

Si vous êtes prêt à migrer vos ressources d’un déploiement classique vers un déploiement Resource Manager, consultez :

1. [Étude technique approfondie de la migration prise en charge par la plateforme de ressources Classic vers Azure Resource Manager](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
2. [Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
3. [Migration de ressources IaaS d’un environnement Classic vers Azure Resource Manager à l’aide d’Azure PowerShell](./virtual-machines/virtual-machines-windows-ps-migration-classic-resource-manager.md)
4. [Migration de ressources IaaS d’un environnement Classic vers Azure Resource Manager à l’aide de l’interface de ligne de commande Azure](./virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md)

## Étapes suivantes

- Pour connaître la procédure pas à pas de création d’un modèle qui définit une machine virtuelle, un compte de stockage et un réseau virtuel, consultez [Guide de création d’un modèle Resource Manager](resource-manager-template-walkthrough.md).
- Pour en savoir plus sur la structure des modèles Resource Manager, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
- Pour connaître les commandes permettant de déployer un modèle, consultez [Déploiement d’une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0803_2016-->
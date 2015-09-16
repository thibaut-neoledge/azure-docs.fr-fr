<properties
   pageTitle="Comprendre les différences entre les modèles de déploiement classique et de Resource Manager"
	description="Décrit les différences entre le modèle de déploiement de Resource Manager et le modèle de déploiement classique (ou de gestion des services)."
	services="azure-resource-manager"
	documentationCenter="na"
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
   ms.service="azure-resource-manager"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="08/20/2015"
	ms.author="tomfitz"/>

# Présentation du déploiement de Resource Manager et du déploiement classique

Le modèle de déploiement de Resource Manager fournit une nouvelle façon de déployer et gérer les services qui composent votre application. Ce nouveau modèle contient des différences importantes par rapport au modèle de déploiement classique, et les deux modèles ne sont pas entièrement compatibles entre eux. Pour simplifier le déploiement et la gestion des ressources, Microsoft recommande l’utilisation de Resource Manager pour les nouvelles ressources et, si possible, un nouveau déploiement des ressources existantes via Resource Manager.

Vous connaissez peut-être aussi le modèle de déploiement classique en tant que modèle de gestion des services.

Cette rubrique décrit les différences entre les deux modèles et certains des problèmes que vous pouvez rencontrer lors de la transition du modèle classique vers Resource Manager. Elle fournit une vue d’ensemble des modèles, mais ne couvre pas en détail les différences dans les services individuels. Pour plus d’informations sur la transition des ressources de calcul, de réseau et de stockage, consultez [Fournisseurs de calcul, de réseau et de stockage Azure dans Azure Resource Manager](./virtual-machines/virtual-machines-azurerm-versus-azuresm.md).

De nombreuses ressources fonctionnent sans problème dans le modèle classique et Resource Manager. Ces ressources prennent totalement en charge Resource Manager même si elles ont été créées dans le modèle classique. Vous pouvez passer à Resource Manager sans aucun problème ni effort supplémentaire.

Cependant, certains fournisseurs de ressources offrent deux versions de la ressource (une pour le modèle classique et une pour Resource Manager) en raison des différences d’architecture entre les modèles. Les fournisseurs de ressources qui font la différence entre les deux modèles sont :

- Calcul
- Stockage
- Réseau

Pour ces types de ressources, vous devez connaître la version que vous utilisez, car les opérations prises en charge diffèrent.

Pour comprendre les différences d’architecture entre les deux modèles, consultez [Architecture d’Azure Resource Manager](virtual-machines/virtual-machines-azure-resource-manager-architecture.md).

## Caractéristiques de Resource Manager

Les ressources créées par le biais de Resource Manager partagent les caractéristiques suivantes :

- Elles sont créées via l’une des méthodes suivantes :

  - [Portail en version préliminaire](https://portal.azure.com/).

        ![preview portal](./media/resource-manager-deployment-model/preview-portal.png)

        Pour calculer, stocker et mettre en réseau des ressources, vous avez la possibilité d'utiliser le Gestionnaire des ressources ou le déploiement classique. Sélectionnez **Gestionnaire des ressources**.

        ![Resource Manager deployment](./media/resource-manager-deployment-model/select-resource-manager.png)

  - Commandes PowerShell exécutées en mode **AzureResourceManager**.

            PS C:\> Switch-AzureMode -Name AzureResourceManager

  - [API REST d’Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn790568.aspx) pour les opérations REST.
  - Commandes Azure CLI exécutées en mode **arm**.

            azure config mode arm

- Le type de ressource n’inclut pas **(classique)** dans le nom. L’illustration ci-dessous indique le type en tant que **compte de stockage**.

    ![application web](./media/resource-manager-deployment-model/resource-manager-type.png)

## Caractéristiques du déploiement classique

Les ressources créées dans le modèle de déploiement classique partagent les caractéristiques suivantes :

- Elles sont créées via l’une des méthodes suivantes :

  - [Portail Azure](https://manage.windowsazure.com)

        ![Azure portal](./media/resource-manager-deployment-model/azure-portal.png)

        Vous pouvez également accéder au portail préliminaire et spécifier qu'il s'agit d'un déploiement **classique** (pour les catégories Calcul, Stockage et Mise en réseau).

        ![Classic deployment](./media/resource-manager-deployment-model/select-classic.png)

  - Commandes PowerShell exécutées en mode **AzureServiceManagement** (celui-ci étant le mode par défaut, si vous ne passez pas de façon spécifique à AzureResourceManager, vous êtes en mode AzureServiceManagement).

            PS C:\> Switch-AzureMode -Name AzureServiceManagement

  - [API REST de gestion des services](https://msdn.microsoft.com/library/azure/ee460799.aspx) pour les opérations REST.
  - Commandes Azure CLI exécutées en mode **asm** ou par défaut.
- Le type de ressource inclut **(classique)** dans le nom. L’illustration ci-dessous indique le type en tant que **compte de stockage (classique)**.

    ![type classique](./media/resource-manager-deployment-model/classic-type.png)

Vous pouvez toujours utiliser le portail en version préliminaire pour gérer les ressources qui ont été créées via un déploiement classique.

## Avantages de l’utilisation de Resource Manager et de groupes de ressources

Resource Manager a ajouté le concept de groupe de ressources. Chaque ressource que vous créez via Resource Manager existe dans un groupe de ressources. Le modèle de déploiement de Resource Manager présente plusieurs avantages :

- Vous pouvez déployer, gérer et surveiller tous les services de votre solution comme un groupe, plutôt que de gérer ces services individuellement.
- Vous pouvez déployer votre application à plusieurs reprises tout au long du cycle de vie de l’application et avoir ainsi l’assurance que vos ressources présentent un état cohérent lors de leur déploiement.
- Vous pouvez utiliser des modèles déclaratifs pour définir votre déploiement. 
- Vous pouvez définir les dépendances entre les ressources afin de les déployer dans le bon ordre.
- Vous pouvez appliquer le contrôle d’accès à tous les services dans votre groupe de ressources, car le contrôle d’accès en fonction du rôle (RBAC) est intégré en mode natif à la plateforme de gestion.
- Vous pouvez appliquer des balises aux ressources pour organiser logiquement toutes les ressources dans votre abonnement.


Avant Resource Manager, aucune ressource créée via un déploiement classique n’existait dans un groupe de ressources. Quand Resource Manager a été ajouté, toutes les ressources ont été rétroactivement ajoutées aux groupes de ressources par défaut. Si vous créez une ressource via un déploiement classique maintenant, la ressource est automatiquement créée dans un groupe de ressources vide même si vous n’avez pas spécifié ce groupe de ressources au moment du déploiement. Toutefois, le fait d’exister simplement au sein d’un groupe de ressources ne signifie pas que la ressource a été convertie en modèle Resource Manager. Si la ressource a été créée via un déploiement classique, vous devez continuer à l’utiliser via des opérations classiques.

Vous pouvez déplacer des ressources vers un groupe de ressources différent et ajouter de nouvelles ressources à un groupe de ressources existant. Par conséquent, votre groupe de ressources peut contenir une combinaison de ressources créées via Resource Manager et un déploiement classique. Cette combinaison de ressources peut générer des résultats inattendus, car les ressources ne prennent pas en charge les mêmes opérations.

À l’aide de modèles déclaratifs, vous pourrez simplifier vos scripts de déploiement. Au lieu de tenter de convertir des scripts existants de gestion des services en Resource Manager, envisagez de repenser votre stratégie de déploiement pour tirer parti de la définition de vos infrastructure et configuration dans le modèle.

## Utilisation de balises

Les balises vous permettent d’organiser logiquement vos ressources. Seules les ressources créées via Resource Manager prennent en charge les balises. Vous ne pouvez pas appliquer des balises aux ressources classiques.

Pour plus d’informations sur l’utilisation de balises dans Resource Manager, consultez [Organisation des ressources Azure à l’aide de balises](resource-group-using-tags.md).

## Opérations prises en charge pour les modèles de déploiement

Les ressources que vous avez créées dans le modèle de déploiement classique ne prennent pas en charge les opérations de Resource Manager. Dans certains cas, une commande Resource Manager peut récupérer des informations sur une ressource créée via un déploiement classique ou effectuer des tâches d’administration, telles que le déplacement d’une ressource classique vers un autre groupe de ressources, mais ces exemples ne doivent pas donner l’impression que le type prend en charge les opérations de Resource Manager. Par exemple, supposons que vous avez un groupe de ressources qui contient des machines virtuelles qui ont été créées avec Resource Manager et un déploiement classique. Si vous exécutez la commande PowerShell suivante, vous verrez toutes les machines virtuelles :

    PS C:\> Get-AzureResourceGroup -Name ExampleGroup
    ...
    Resources :
     Name                 Type                                          Location
     ================     ============================================  ========
     ExampleClassicVM     Microsoft.ClassicCompute/domainNames          eastus
     ExampleClassicVM     Microsoft.ClassicCompute/virtualMachines      eastus
     ExampleResourceVM    Microsoft.Compute/virtualMachines             eastus
    ...

Toutefois, si vous exécutez la commande Get-AzureVM, vous obtiendrez uniquement les machines virtuelles qui ont été créées avec Resource Manager.

    PS C:\> Get-AzureVM -ResourceGroupName ExampleGroup
    ...
    Id       : /subscriptions/xxxx/resourceGroups/ExampleGroup/providers/Microsoft.Compute/virtualMachines/ExampleResourceVM
    Name     : ExampleResourceVM
    ...

En général, vous ne devez pas vous attendre à ce que les ressources créées via un déploiement classique fonctionnent avec les commandes Resource Manager.

Quand vous utilisez des ressources créées avec Resource Manager, vous devez utiliser les opérations de Resource Manager et non revenir aux opérations de gestion des services.

## Considérations relatives aux machines virtuelles

Plusieurs points importants sont à prendre en compte lors de l’utilisation de machines virtuelles.

- Les machines virtuelles déployées avec le modèle de déploiement classique ne peuvent pas être incluses dans un réseau virtuel déployé avec Resource Manager.
- Les machines virtuelles déployées avec le modèle de déploiement de Resource Manager doivent être incluses dans un réseau virtuel.
- Les machines virtuelles déployées avec le modèle de déploiement classique ne doivent pas être incluses dans un réseau virtuel.

Pour obtenir une liste des commandes Azure CLI équivalentes lors de la transition d’un déploiement classique vers Resource Manager, consultez [Commandes équivalentes du Gestionnaire de ressources et de la gestion des services pour les opérations de machine virtuelle avec l’interface de ligne de commande Azure pour Mac, Linux et Windows](./virtual-machines/xplat-cli-azure-manage-vm-asm-arm.md).

Pour plus d’informations sur la transition des ressources de calcul, de réseau et de stockage, consultez [Fournisseurs de calcul, de réseau et de stockage Azure dans Azure Resource Manager](./virtual-machines/virtual-machines-azurerm-versus-azuresm.md).

Pour en savoir plus sur la connexion de réseaux virtuels à partir de modèles de déploiement différents, consultez [Connexion de réseaux virtuels classiques aux nouveaux réseaux virtuels](./virtual-network/virtual-networks-arm-asm-s2s.md).

## Étapes suivantes

- Pour en savoir plus sur la création de modèles de déploiement déclaratifs, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
- Pour connaître les commandes permettant de déployer un modèle, consultez [Déploiement d’une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).

<!----HONumber=August15_HO9-->
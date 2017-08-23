---
title: FAQ Azure DevTest Labs | Microsoft Docs
description: "Trouvez des réponses aux questions les plus fréquentes sur Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: afe83109-b89f-4f18-bddd-b8b4a30f11b4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: tarcher
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: e0adac5193ae412845c8413dfee6b5557096359a
ms.contentlocale: fr-fr
ms.lasthandoff: 05/25/2017


---
# <a name="azure-devtest-labs-faq"></a>FAQ d’Azure DevTest Labs
Cet article répond aux questions les plus fréquemment posées sur Azure DevTest Labs.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="general"></a>Généralités
* [Que dois-je faire si je n’ai pas trouvé de réponse à ma question ici ?](#what-if-my-question-isnt-answered-here)
* [Pourquoi dois-je utiliser Azure DevTest Labs ?](#why-should-i-use-azure-devtest-labs)
* [Que signifie « libre-service, sans problème » ?](#what-does-worry-free-self-service-mean)
* [Comment puis-je utiliser Azure DevTest Labs ?](#how-can-i-use-azure-devtest-labs)
* [Combien me coûte Azure DevTest Labs ?](#how-am-i-billed-for-azure-devtest-labs)

## <a name="security"></a>Sécurité
* [Quels sont les différents niveaux de sécurité dans Azure DevTest Labs ?](#what-are-the-different-security-levels-in-azure-devtest-labs)
* [Comment puis-je créer un rôle pour permettre aux utilisateurs d’effectuer une tâche spécifique ?](#how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task)

## <a name="cicd-integration--automation"></a>Automatisation et intégration CI/CD
* [Azure DevTest Labs est-il intégré à ma chaîne d’outils CI/CD ?](#does-azure-devtest-labs-integrate-with-my-cicd-toolchain)

## <a name="virtual-machines"></a>Machines virtuelles
* [Pourquoi ne puis-je pas voir certaines machines virtuelles dans le panneau Machines virtuelles Azure alors que je peux les voir dans Azure DevTest Labs ?](#why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs)
* [Quelle est la différence entre des images personnalisées et des formules ?](#what-is-the-difference-between-custom-images-and-formulas)
* [Comment puis-je créer plusieurs machines virtuelles à partir du même modèle en une seule fois ?](#how-do-i-create-multiple-vms-from-the-same-template-at-once)
* [Comment puis-je déplacer mes machines virtuelles Azure existantes dans mon laboratoire Azure DevTest Labs ?](#how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab)
* [Puis-je attacher plusieurs disques à mes machines virtuelles ?](#can-i-attach-multiple-disks-to-my-vms)
* [Si je souhaite utiliser une image de système d’exploitation Windows pour mes tests, dois-je acheter un abonnement MSDN ?](#if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription)
* [Comment puis-je automatiser le processus de téléchargement des fichiers de disque dur virtuel pour créer des images personnalisées ?](#how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images)
* [Comment puis-je automatiser le processus de suppression de toutes les machines virtuelles dans mon laboratoire ?](#how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab)

## <a name="artifacts"></a>Artefacts
* [Que sont les artefacts ?](#what-are-artifacts)

## <a name="lab-configuration"></a>Configuration du laboratoire
* [Comment puis-je créer un laboratoire à partir d’un modèle Azure Resource Manager ?](#how-do-i-create-a-lab-from-an-azure-resource-manager-template)
* [Pourquoi mes machines virtuelles sont-elles créées dans différents groupes de ressources avec des noms arbitraires ? Puis-je renommer ou modifier ces groupes de ressources ?](#why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups)
* [Combien de laboratoires puis-je créer sous le même abonnement ?](#how-many-labs-can-i-create-under-the-same-subscription)
* [Combien de machines virtuelles puis-je créer par laboratoire ?](#how-many-vms-can-i-create-per-lab)
* [Comment partager un lien direct vers mon laboratoire ?](#how-do-i-share-a-direct-link-to-my-lab)
* [Qu’est-ce qu’un compte Microsoft ?](#what-is-a-microsoft-account)

## <a name="troubleshooting"></a>Résolution de problèmes
* [Mon artefact a échoué lors de la création d’une machine virtuelle. Comment puis-je résoudre ce problème ?](#my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it)
* [Pourquoi mon réseau virtuel existant n’est pas enregistré correctement ?](#why-isnt-my-existing-virtual-network-saving-properly)
* [Pourquoi l’erreur signalant que la ressource parente est introuvable s’affiche-t-elle lors de l’approvisionnement à partir de PowerShell ?](#why-do-i-get-a-parent-resource-not-found-error-when-provisioning-a-vm-from-powershell)  
* [Où puis-je trouver plus d’informations d’erreur si le déploiement d’une machine virtuelle échoue ?](#where-can-i-find-more-error-information-if-a-vm-deployment-fails)  

### <a name="what-if-my-question-isnt-answered-here"></a>Que dois-je faire si je n’ai pas trouvé de réponse à ma question ici ?
Si votre question n’est pas répertoriée ici, faites-le-nous savoir pour que nous puissions vous aider à trouver une réponse.

* Publiez une question dans le [thread](#comments) à la fin de cette FAQ et collaborer avec l’équipe Azure Cache et d’autres membres de la communauté en cas de question sur cet article.
* Pour atteindre un public plus large, publiez une question sur le [Forum Azure DevTest Labs](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs)et collaborer avec l’équipe Azure DevTest Labs et d’autres membres de la Communauté.
* Pour effectuer une demande de fonctionnalité, envoyez vos demandes et idées à [Azure DevTest Labs User Voice](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="why-should-i-use-azure-devtest-labs"></a>Pourquoi dois-je utiliser Azure DevTest Labs ?
Azure DevTest Labs peut faire gagner du temps et de l’argent à votre équipe. Les développeurs peuvent créer leurs propres environnements à l’aide de plusieurs bases différentes et utiliser des artefacts pour déployer et configurer rapidement des applications. À l’aide de formules et d’images personnalisées, les machines virtuelles peuvent être enregistrées en tant que modèles et reproduites facilement. En outre, les laboratoires offrent plusieurs stratégies configurables qui permettent aux administrateurs de laboratoire de réduire le gaspillage et de gérer les environnements d’une équipe. Ces stratégies comprennent l’arrêt automatique, le seuil de coût, le nombre maximal de machines virtuelles par utilisateur et les tailles maximales de machine virtuelle. Pour plus de détails sur Azure DevTest Labs, lisez la [présentation](devtest-lab-overview.md) ou regardez la [vidéo d’introduction](/documentation/videos/videos/what-is-azure-devtest-labs).

### <a name="what-does-worry-free-self-service-mean"></a>Que signifie « libre-service, sans problème » ?
« Libre-service, sans problème » signifie que les développeurs et testeurs créent leurs propres environnements en fonction des besoins, et les administrateurs ont la garantie que Azure DevTest Labs leur permet de réduire le gaspillage et de contrôler les coûts. Les administrateurs peuvent spécifier quelles tailles de machine virtuelle sont autorisées, le nombre maximal de machines virtuelles et le moment où les machines virtuelles sont démarrées et arrêtées. Azure DevTest Labs facilite également la surveillance des coûts et la définition des alertes afin d’être informé sur l’utilisation des ressources dans le laboratoire.

### <a name="how-can-i-use-azure-devtest-labs"></a>Comment puis-je utiliser Azure DevTest Labs ?
Azure DevTest Labs vous sera utile dès que vous voudrez créer des environnements de test ou de développement, les reproduire et/ou les gérer rapidement avec des stratégies de réduction des coûts.

Voici quelques scénarios pour lesquels nos clients utilisent Azure DevTest Labs :

* Gestion des environnements de développement et de test dans un seul endroit, avec des stratégies pour réduire les coûts et des images personnalisées pour partager les builds au sein de l’équipe.
* Développement d’une application à l’aide d’images personnalisées pour enregistrer l’état du disque tout au long des étapes de développement.
* Suivi des coûts par rapport à la progression.
* Création d’environnements de test en série pour des tests d’assurance qualité.
* Utilisation d’artefacts et de formules pour configurer et reproduire facilement une application sur divers environnements.
* Distribution de machines virtuelles pour des hackathons (travail de test ou de développement collaboratif), puis les déprovisionner facilement lorsque l’événement se termine.

### <a name="how-am-i-billed-for-azure-devtest-labs"></a>Combien me coûte Azure DevTest Labs ?
Azure DevTest Labs est un service gratuit, ce qui signifie que la création de laboratoires et la configuration de stratégies, de modèles et d’artefacts ne coûtent rien. Vous payez uniquement pour les ressources Azure utilisées dans vos laboratoires, telles que les machines virtuelles, les comptes de stockage et les réseaux virtuels. Pour plus de détails sur le coût des ressources de laboratoire, consultez la page sur la [tarification Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/).

### <a name="what-are-the-different-security-levels-in-azure-devtest-labs"></a>Quels sont les différents niveaux de sécurité dans Azure DevTest Labs ?
L’accès à la sécurité est déterminé par le [contrôle d’accès en fonction du rôle (RBAC) d’Azure](../active-directory/role-based-access-built-in-roles.md). Pour comprendre comment l’accès est déterminé, vous devez saisir les différences entre une autorisation, un rôle et une étendue, comme défini par RBAC.

* **Autorisation** - Une autorisation est un accès défini pour une action spécifique. Par exemple, une autorisation peut être en lecture seule pour toutes les machines virtuelles.
* **Rôle** - un rôle est un jeu d’autorisations qui peuvent être regroupées et attribuées à un utilisateur. Par exemple, le rôle propriétaire de l’abonnement a accès à toutes les ressources au sein d’un abonnement.
* **Portée** - Une portée est un niveau dans la hiérarchie d’une ressource Azure. Par exemple, une portée peut être un groupe de ressources, un même laboratoire ou l’ensemble de l’abonnement.

Dans l’étendue d’Azure DevTest Labs, il existe deux types de rôles pour définir des autorisations utilisateur : propriétaire de laboratoire et utilisateur de laboratoire.

* **Propriétaire de laboratoire** - un propriétaire de laboratoire a accès à toutes les ressources du laboratoire. Ainsi, ils peuvent modifier les stratégies, lire et écrire sur toutes les machines virtuelles, modifier le réseau virtuel et ainsi de suite.
* **Utilisateur de laboratoire** : un utilisateur de laboratoire peut afficher toutes les ressources de laboratoire, telles que les machines virtuelles, les stratégies et les réseaux virtuels, mais il ne peut pas modifier les stratégies ou les machines virtuelles créées par d’autres utilisateurs. Il est également possible de créer des rôles personnalisés dans Azure DevTest Labs, et vous pouvez apprendre à le faire dans l’article [Accorder des autorisations à des utilisateurs sur des stratégies de laboratoire spécifiques](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Étant donné que les étendues sont hiérarchiques, lorsqu’un utilisateur dispose d’autorisations pour une certaine étendue, il reçoit automatiquement ces autorisations pour chaque niveau d’étendue inférieur englobé. Par exemple, si un utilisateur est affecté au rôle de propriétaire d’abonnement, il a accès à toutes les ressources dans un abonnement. Ces ressources incluent toutes les machines virtuelles, tous les réseaux virtuels et tous les laboratoires. Par conséquent, un propriétaire d’abonnement hérite automatiquement du rôle de propriétaire de laboratoire. Toutefois, l’inverse n’est pas vrai. Un propriétaire de laboratoire a accès à un laboratoire, qui est une étendue inférieure au niveau d’abonnement. Par conséquent, un propriétaire de laboratoire ne sera pas en mesure de voir les machines virtuelles ou réseaux virtuels ou toutes les ressources qui sont en dehors du laboratoire.

### <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task"></a>Comment puis-je créer un rôle pour permettre aux utilisateurs d’effectuer une tâche spécifique ?
Vous trouverez ici un article complet sur la façon de créer des rôles personnalisés et d’affecter des autorisations à ce rôle. Voici un exemple de script qui crée le rôle « Utilisateur avancé DevTest Labs », qui a l’autorisation de démarrer et d’arrêter toutes les machines virtuelles dans le laboratoire :

    $policyRoleDef = Get-AzureRmRoleDefinition "DevTest Labs User"
    $policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "DevTest Labs Advance User"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
    $policyRoleDef = New-AzureRmRoleDefinition -Role $policyRoleDef  

### <a name="does-azure-devtest-labs-integrate-with-my-cicd-toolchain"></a>Azure DevTest Labs est-il intégré à ma chaîne d’outils CI/CD ?
Si vous utilisez VSTS, il existe une [extension des tâches Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) qui vous permet d’automatiser votre pipeline de versions dans Azure DevTest Labs. Voici quelques-unes des utilisations de cette extension :

* Création et déploiement automatiques d’une machine virtuelle et sa configuration avec le dernier build à l’aide des tâches de copie de fichiers Azure ou PowerShell VSTS.
* Capture automatique de l’état d’une machine virtuelle après le test, afin de reproduire un bogue sur la même machine virtuelle pour un examen approfondi.
* Suppression de la machine virtuelle à la fin du pipeline de versions lorsqu’elle n’est plus nécessaire.

Les billets de blog suivants offrent des conseils et des informations sur l’utilisation de l’extension VSTS :

* [General availability of Azure DevTest Labs – VSTS extension (Disponibilité générale d’Azure DevTest Labs - extension VSTS)](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/)
* [Deploying a new VM in an existing AzureDevTestLab from VSTS (Déploiement d’une nouvelle machine virtuelle dans un AzureDevTestLab existant depuis VSTS)](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
* [Using VSTS Release Management for Continuous Deployments to AzureDevTestLabs (Utilisation de VSTS Release Management pour des déploiements en continu vers AzureDevTestLabs)](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

Pour d’autres chaînes d’outils CI/CD, tous les scénarios mentionnés ci-dessus qui peuvent être réalisés via l’extension de tâches VSTS peuvent également être réalisés via le déploiement de [modèles Azure Resource Manager](https://aka.ms/dtlquickstarttemplate) à l’aide des [applets de commande Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) et des [kits de développement logiciel (SDK) .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). De plus, vous pouvez utiliser les [API REST pour DevTest Labs](http://aka.ms/dtlrestapis) pour une intégration à votre chaîne d’outils.  

### <a name="why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs"></a>Pourquoi ne puis-je pas voir certaines machines virtuelles dans le panneau Machines virtuelles Azure alors que je peux les voir dans Azure DevTest Labs ?
Lorsqu’une machine virtuelle est créée dans Azure DevTest Labs, l’autorisation d’accéder à cette machine virtuelle est accordée. Vous pouvez l’afficher à la fois dans le panneau Laboratoires et dans le panneau **Machines virtuelles** . Les utilisateurs avec le rôle DevTest Labs peuvent voir toutes les machines virtuelles créées dans le laboratoire via le panneau **All Virtual Machines** (Toutes les machines virtuelles). Toutefois, les utilisateurs du rôle DevTest Labs ne reçoivent pas automatiquement l’accès en lecture aux ressources des machines virtuelles qu’ils ont créées. Par conséquent, ces machines virtuelles ne s’affichent pas dans le panneau **Machines virtuelles** .

### <a name="what-is-the-difference-between-custom-images-and-formulas"></a>Quelle est la différence entre des images personnalisées et des formules ?
Une image personnalisée est un disque dur virtuel, alors qu’une formule est une image que vous pouvez configurer avec des paramètres supplémentaires que vous pouvez enregistrer et reproduire. Une image personnalisée peut être préférable si vous souhaitez créer rapidement plusieurs environnements avec la même image de base, immuable. Une formule peut être préférable si vous souhaitez reproduire la configuration de votre machine virtuelle avec les tout derniers bits, un sous-réseau/réseau virtuel ou une taille spécifique. Pour plus de détails, consultez l’article [Comparaison entre les images personnalisées et les formules dans DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Comment puis-je créer plusieurs machines virtuelles à partir du même modèle en une seule fois ?
Vous pouvez utiliser [l’extension de tâches VSTS](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) ou [générer un modèle Azure Resource Manager](devtest-lab-add-vm.md#save-azure-resource-manager-template) lors de la création d’une machine virtuelle et [déployer le modèle Azure Resource Manager à partir de Windows PowerShell](../azure-resource-manager/resource-group-template-deploy.md).

### <a name="how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab"></a>Comment puis-je déplacer mes machines virtuelles Azure existantes dans mon laboratoire Azure DevTest Labs ?
Pour copier vos machines virtuelles existantes vers Azure DevTest Labs, procédez comme suit :

1. Copiez le fichier de disque dur virtuel de votre machine virtuelle existante à l’aide de ce [script Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1)
2. [Créez l’image personnalisée](devtest-lab-create-template.md) dans votre laboratoire Azure DevTest Labs.
3. Créez une machine virtuelle dans le laboratoire à partir de votre image personnalisée.

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>Puis-je attacher plusieurs disques à mes machines virtuelles ?
Vous pouvez attacher plusieurs disques aux machines virtuelles.  

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Si je souhaite utiliser une image de système d’exploitation Windows pour mon test, dois-je acheter un abonnement MSDN ?
Si vous avez besoin d’utiliser des images de système d’exploitation client Windows (Windows 7 ou version ultérieure) à des fins de développement ou de test dans Azure, alors oui, vous devez soit :

- [Achetez un abonnement MSDN](https://www.visualstudio.com/products/how-to-buy-vs).
- Si vous disposez d’un contrat Entreprise, créez un abonnement Azure avec [l’offre Entreprise Dev/Test](https://azure.microsoft.com/en-us/offers/ms-azr-0148p).

Pour plus d’informations sur les crédits Azure pour chaque offre de MSDN, consultez [Crédit Azure mensuel pour les abonnés Visual Studio](https://azure.microsoft.com/en-us/pricing/member-offers/msdn-benefits-details/).

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Comment puis-je automatiser le processus de téléchargement des fichiers de disque dur virtuel pour créer des images personnalisées ?
Nous avons deux options :

* [Azure AzCopy](../storage/storage-use-azcopy.md#blob-upload) peut être utilisé pour copier ou télécharger des fichiers de disque dur virtuel sur le compte de stockage associé au laboratoire.
* [L’Explorateur Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) est une application autonome qui s’exécute sur Windows, OSX et Linux.   

Pour trouver le compte de stockage de destination associé à votre laboratoire, suivez ces étapes :

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Sélectionnez **Groupes de ressources** à partir du panneau de gauche.
3. Recherchez et sélectionnez le groupe de ressources associé à votre laboratoire.
4. Dans le panneau **Vue d’ensemble** , sélectionnez l’un des comptes de stockage.
5. Sélectionnez **Objets Blob**.
6. Recherchez les téléchargements dans la liste. S’il n’y en a aucun, revenez à l’étape 4 et essayez un autre compte de stockage.
7. Utilisez **l’URL** en tant que destination dans votre commande AzCopy.

### <a name="how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Comment puis-je automatiser le processus de suppression de toutes les machines virtuelles dans mon laboratoire ?
Outre la suppression des machines virtuelles de votre laboratoire dans le portail Azure, vous pouvez supprimer toutes les machines virtuelles dans votre laboratoire à l’aide d’un script PowerShell. Dans l’exemple suivant, modifiez les valeurs des paramètres sous le commentaire **Valeurs à modifier**. Vous pouvez récupérer les valeurs `subscriptionId`, `labResourceGroup` et `labName` à partir du panneau de laboratoire dans le portail Azure.

    # Delete all the VMs in a lab

    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource group here>"
    $labName = "<Enter lab name here>"

    # Login to your Azure account
    Login-AzureRmAccount

    # Select the Azure subscription that contains the lab. This step is optional
    # if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Get the lab that contains the VMs to delete.
    $lab = Get-AzureRmResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

    # Get the VMs from that lab.
    $labVMs = Get-AzureRmResource | Where-Object {
              $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
              $_.ResourceName -like "$($lab.ResourceName)/*"}

    # Delete the VMs.
    foreach($labVM in $labVMs)
    {
        Remove-AzureRmResource -ResourceId $labVM.ResourceId -Force
    }




### <a name="what-are-artifacts"></a>Que sont les artefacts ?
Les artefacts sont des éléments personnalisables qui peuvent être utilisés pour déployer vos tout derniers bits ou vos outils de développement sur une machine virtuelle. Ils sont attachés à votre machine virtuelle lors de la création en quelques clics simples, et une fois que la machine virtuelle est configurée, les artefacts déploient et configurent votre machine virtuelle. Il existe divers artefacts préexistants dans notre [référentiel GitHub public](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), mais vous pouvez également [créer vos propres artefacts](devtest-lab-artifact-author.md) facilement.

### <a name="how-do-i-create-a-lab-from-an-azure-resource-manager-template"></a>Comment puis-je créer un laboratoire à partir d’un modèle Azure Resource Manager ?
Nous avons fourni un [référentiel Github de modèles Azure Resource Manager de laboratoire](https://aka.ms/dtlquickstarttemplate) que vous pouvez déployer tels quels ou modifier pour créer des modèles personnalisés pour vos laboratoires. Chacun de ces modèles a un lien sur lequel vous pouvez cliquer pour déployer le laboratoire tel quel sous votre propre abonnement Azure, ou vous pouvez personnaliser le modèle et le [déployer à l’aide de PowerShell ou de l’interface de ligne de commande (CLI) Azure](../azure-resource-manager/resource-group-template-deploy.md).

### <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups"></a>Pourquoi mes machines virtuelles sont-elles créées dans différents groupes de ressources avec des noms arbitraires ? Puis-je renommer ou modifier ces groupes de ressources ?
Les groupes de ressources sont créés de cette façon pour qu’Azure DevTest Labs puisse gérer les autorisations utilisateur et l’accès aux machines virtuelles. Même si vous pouvez déplacer la machine virtuelle vers un autre groupe de ressources avec le nom de votre choix, cela n’est pas recommandé. Nous travaillons sur l’amélioration de cette fonctionnalité pour une flexibilité accrue.   

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Combien de laboratoires puis-je créer sous le même abonnement ?
Il n’existe aucune limite spécifique concernant le nombre de laboratoires qui peuvent être créés par abonnement. Toutefois, les ressources utilisées sont limitées par abonnement. Vous pouvez consulter des articles pour en savoir plus sur les [quotas et les limites imposés pour les abonnements Azure](../azure-subscription-service-limits.md) et [comment augmenter ces limites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

### <a name="how-many-vms-can-i-create-per-lab"></a>Combien de machines virtuelles puis-je créer par laboratoire ?
Il n’existe aucune limite spécifique concernant le nombre de machines virtuelles qui peuvent être créées par laboratoire. Toutefois, les ressources utilisées sont limitées par abonnement (par exemple, cœurs de machines virtuelles, adresses IP publiques, etc.). Vous pouvez consulter des articles pour en savoir plus sur les [quotas et les limites imposés pour les abonnements Azure](../azure-subscription-service-limits.md) et [comment augmenter ces limites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Comment partager un lien direct vers mon laboratoire ?
Pour partager un lien direct à destination des utilisateurs de votre laboratoire, vous pouvez effectuer la procédure suivante :

1. Accédez au laboratoire dans le portail Azure.
2. Copiez l’URL du laboratoire dans votre navigateur et partagez-la avec les utilisateurs de votre laboratoire.

> [!NOTE]
> Si les utilisateurs de votre laboratoire sont des utilisateurs externes avec un [compte Microsoft](#what-is-a-microsoft-account) et qu’ils n’appartiennent pas au répertorie Active Directory de votre entreprise, il est possible qu’ils reçoivent une erreur lors de la navigation vers le lien fourni. Le cas échéant, indiquez-leur de cliquer sur leur nom dans le coin supérieur droit du portail Azure et de sélectionner le répertoire où se situe le laboratoire dans la section **Répertoire** du menu.
>
>

### <a name="what-is-a-microsoft-account"></a>Qu’est-ce qu’un compte Microsoft ?
Vous utilisez un compte Microsoft pour la plupart des opérations effectuées avec les services et les périphériques Microsoft. Il s’agit d’une adresse de messagerie et d’un mot de passe que vous utilisez pour vous connecter à Skype, Outlook.com, OneDrive, Windows Phone et Xbox LIVE. Cela signifie que vos fichiers, photos, contacts et paramètres vous suivent sur n’importe quel appareil.

> [!NOTE]
> Auparavant le compte Microsoft était appelé « Windows Live ID ».
>
>

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Mon artefact a échoué lors de la création d’une machine virtuelle. Comment puis-je résoudre ce problème ?
Reportez-vous au billet de blog [How to troubleshoot failing Artifacts in AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs), rédigé par un de nos MVP, pour savoir comment obtenir des journaux concernant votre artefact ayant échoué.

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Pourquoi mon réseau virtuel existant n’est pas enregistré correctement ?
Il se peut que votre nom de réseau virtuel contienne des points. Si tel est le cas, essayez de supprimer les points ou remplacez-les par des tirets, puis réessayez d’enregistrer le réseau virtuel.

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-provisioning-a-vm-from-powershell"></a>Pourquoi l’erreur signalant que la ressource parente est introuvable s’affiche t-elle lors de l’approvisionnement d’une machine virtuelle à partir de PowerShell ?
Lorsqu’une ressource est parent d’une autre ressource, la ressource parent doit exister avant de créer la ressource enfant. Si ce n’est pas cas, vous recevez une erreur **ParentResourceNotFound**. Si vous ne spécifiez pas de dépendance sur la ressource parente, la ressource enfant peut être déployée avant cette dernière.

Les machines virtuelles sont des ressources enfants se trouvant dans un laboratoire d’un groupe de ressources. Quand vous utilisez des modèles Azure Resource Manager pour effectuer un déploiement à l’aide de PowerShell, le nom du groupe de ressources fourni dans le script PowerShell doit être le nom du groupe de ressources du laboratoire. Pour plus d’informations, consultez la rubrique [Résolution des erreurs courantes dans des déploiements Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-common-deployment-errors#parentresourcenotfound).

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Où puis-je trouver plus d’informations d’erreur si le déploiement d’une machine virtuelle échoue ?
Les erreurs de déploiement de machine virtuelle sont capturées dans les journaux d’activité. Vous pouvez trouver les journaux d’activité des machines virtuelles du laboratoire via les **journaux d’audit** ou les **diagnostics de machine virtuelle** dans le menu de la ressource dans le panneau Machine virtuelle du laboratoire (le panneau s’affiche une fois que vous sélectionnez la machine virtuelle à partir de la liste **Mes machines virtuelles**).

Parfois, l’erreur de déploiement se produit avant le démarrage du déploiement de la machine virtuelle, par exemple lorsque la limite d’inscriptions pour une ressource créée avec la machine virtuelle est dépassée. Dans ce cas, les détails de l’erreur sont capturés dans le niveau de laboratoire **Journaux d’activité**, qui se trouve en bas des paramètres **Configuration et stratégies**. Pour plus d’informations sur l’utilisation des journaux d’activité dans Azure, consultez [Afficher les journaux d’activité pour auditer les actions sur les ressources](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-audit).


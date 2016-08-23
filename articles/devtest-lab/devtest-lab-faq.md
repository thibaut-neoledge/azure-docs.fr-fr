<properties
	pageTitle="FAQ DevTest Labs | Microsoft Azure"
	description="Trouvez des réponses aux questions les plus fréquentes sur DevTest Labs"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/05/2016"
	ms.author="tarcher"/>

# FAQ DevTest Labs

Cet article répond aux questions les plus fréquemment posées sur DevTest Labs.

## Généralités
- [Pourquoi dois-je utiliser DevTest Labs ?](#why-should-i-use-devtest-labs)
- [Que signifie « libre-service, sans problème » ?](#what-does-quotworry-free-self-servicequot-mean)
- [Comment puis-je utiliser DevTest Labs ?](#how-can-i-use-devtest-labs)
- [Comment serai-je facturé pour DevTest Labs ?](#how-will-i-be-charged-for-devtest-labs)
 
## Sécurité 
- [Quels sont les différents niveaux de sécurité dans DevTest Labs ?](#what-are-the-different-security-levels-in-devtest-labs)
- [Comment puis-je créer un rôle spécifique pour permettre aux utilisateurs d’effectuer une seule tâche ?](#how-do-i-create-a-specific-role-to-allow-users-to-perform-only-a-single-task)
 
## Automatisation et intégration CI/CD 
 
- [DevTest Labs est-il intégré à ma chaîne d’outils CI/CD ?](#does-devtest-labs-integrate-with-my-cicd-toolchain)
 
## Machines virtuelles 
 
- [Pourquoi ne puis-je pas voir certaines machines virtuelles dans le panneau Machines virtuelles Azure alors que je peux les voir dans DevTest Labs ?](#why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-devtest-labs)
- [Quelle est la différence entre des images personnalisées et des formules ?](#what-is-the-difference-between-custom-images-and-formulas)
- [Comment puis-je créer plusieurs machines virtuelles à partir du même modèle en une seule fois ?](#how-do-i-create-multiple-vms-from-the-same-template-at-once)
- [Comment puis-je déplacer mes machines virtuelles Azure existantes dans mon laboratoire DevTest Labs ?](#how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab)
- [Puis-je attacher plusieurs disques à mes machines virtuelles ?](#can-i-attach-multiple-disks-to-my-vms)
- [Comment puis-je automatiser le processus de téléchargement des fichiers de disque dur virtuel pour créer des images personnalisées ?](#how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images)
 
## Artefacts 
 
- [Que sont les artefacts ?](#what-are-artifacts)
 
## Configuration du laboratoire 
 
- [Comment puis-je créer un laboratoire à partir d’un modèle Azure Resource Manager ?](#how-do-i-create-a-lab-from-an-arm-template)
- [Pourquoi toutes mes machines virtuelles sont-elles créées dans différents groupes de ressources avec des noms arbitraires ? Puis-je renommer ou modifier ces groupes de ressources ?](#why-are-all-of-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups)
- [Combien de laboratoires puis-je créer sous le même abonnement ?](#how-many-labs-can-i-create-under-the-same-subscription)
- [Combien de machines virtuelles puis-je créer par laboratoire ?](#how-many-vms-can-i-create-per-lab)
 
## Résolution de problèmes 
 
- [Mon artefact a échoué lors de la création d’une machine virtuelle. Comment puis-je résoudre ce problème ?](#my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it)
- [Pourquoi mon réseau virtuel existant n’est pas enregistré correctement ?](#why-isnt-my-existing-virtual-network-saving-properly)

### Pourquoi dois-je utiliser DevTest Labs ? 
DevTest Labs peut faire gagner du temps et de l’argent à votre équipe. Les développeurs peuvent créer leurs propres environnements à l’aide d’un certain nombre de bases différentes et utiliser des artefacts pour déployer et configurer rapidement des applications. À l’aide de formules et d’images personnalisées, les machines virtuelles peuvent être enregistrées en tant que modèles et reproduites facilement. En outre, les laboratoires offrent plusieurs stratégies configurables (arrêt automatique, seuil de coût, machines virtuelles maximum par utilisateur et tailles de machine virtuelle maximum) qui permettent aux administrateurs de laboratoire de réduire le gaspillage et de gérer les environnements d’une équipe. Pour plus de détails sur DevTest Labs, lisez la [présentation](devtest-lab-overview.md) ou regardez la [vidéo d’introduction](/documentation/videos/videos/what-is-azure-devtest-labs).

### Que signifie « libre-service, sans problème » ?
« Libre-service, sans problème » signifie que les développeurs et testeurs peuvent créer leurs propres environnements en fonction des besoins, et les administrateurs ont la garantie que DevTest Labs leur permettra de réduire le gaspillage et de contrôler les coûts. Les administrateurs peuvent spécifier quelles tailles de machine virtuelle sont autorisées, le nombre maximal de machines virtuelles et le moment où les machines virtuelles sont démarrées et arrêtées. DevTest Labs facilite également la surveillance des coûts et la définition des alertes afin d’être informé sur l’utilisation des ressources dans le laboratoire.

### Comment puis-je utiliser DevTest Labs ? 
DevTest Labs vous sera utile dès que vous voudrez créer des environnements de test ou de développement, les reproduire et/ou les gérer rapidement avec des stratégies de réduction des coûts.

Voici quelques scénarios pour lesquels nos clients utilisent DevTest Labs :

- Gestion des environnements de développement et de test dans un seul endroit, avec des stratégies pour réduire les coûts et des images personnalisées pour partager les builds au sein de l’équipe.
- Développement d’une application à l’aide d’images personnalisées pour enregistrer l’état du disque tout au long des étapes de développement.
- Suivi des coûts par rapport à la progression.
- Création d’environnements de test en série pour des tests d’assurance qualité.
- Utilisation d’artefacts et de formules pour configurer et reproduire facilement une application sur divers environnements.
- Distribution de machines virtuelles pour des hackathons (travail de test ou de développement collaboratif), puis les déprovisionner facilement lorsque l’événement se termine.

### Comment serai-je facturé pour DevTest Labs ? 
DevTest Labs est un service gratuit, ce qui signifie que la création de laboratoires et la configuration de stratégies, de modèles et d’artefacts sont gratuits. Vous payez uniquement pour les ressources Azure utilisées dans vos laboratoires, telles que les machines virtuelles, les comptes de stockage et les réseaux virtuels. Pour plus de détails sur le coût des ressources de laboratoire, consultez la page sur la [tarification Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/).

### Quels sont les différents niveaux de sécurité dans DevTest Labs ?  
L’accès à la sécurité est déterminé par le [contrôle d’accès en fonction du rôle (RBAC) d’Azure](../active-directory/role-based-access-built-in-roles.md). Pour comprendre comment l’accès est déterminé, vous devez saisir les différences entre une autorisation, un rôle et une étendue, comme défini par RBAC. Une autorisation est un accès défini pour une action spécifique (p. ex. accès en lecture à toutes les machines virtuelles). Un rôle est un jeu d’autorisations qui peuvent être regroupées et affectées à un utilisateur (p. ex. « propriétaire d’abonnement » a accès à toutes les ressources au sein d’un abonnement). Une étendue est un niveau dans la hiérarchie des ressources Azure (p. ex. un groupe de ressources ou un simple laboratoire ou l’ensemble de l’abonnement).
 
Dans l’étendue de DevTest Labs, il existe deux types de rôles pour définir des autorisations utilisateur : propriétaire de laboratoire et utilisateur de laboratoire.

- Propriétaire de laboratoire : un propriétaire de laboratoire a accès à toutes les ressources dans le laboratoire. Par conséquent, ils peuvent modifier les stratégies, lire et écrire dans toutes les machines virtuelles, modifier le réseau virtuel, etc.
- Utilisateur de laboratoire : un utilisateur de laboratoire peut afficher toutes les ressources de laboratoire, telles que les machines virtuelles, les stratégies et les réseaux virtuels, mais il ne peut pas modifier les stratégies ou les machines virtuelles créées par d’autres utilisateurs. Il est également possible de créer des rôles personnalisés dans DevTest Labs, et vous pouvez apprendre à le faire dans l’article [Accorder des autorisations à des utilisateurs sur des stratégies de laboratoire spécifiques](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).
 
Étant donné que les étendues sont hiérarchiques, lorsqu’un utilisateur dispose d’autorisations pour une certaine étendue, il reçoit automatiquement ces autorisations pour chaque niveau d’étendue inférieur englobé. Par exemple, si un utilisateur est affecté au rôle de propriétaire d’abonnement, il a accès à toutes les ressources dans un abonnement, notamment à toutes les machines virtuelles, tous les réseaux virtuels et tous les laboratoires. Par conséquent, un propriétaire d’abonnement hérite automatiquement du rôle de propriétaire de laboratoire. Toutefois, l’inverse n’est pas vrai. Un propriétaire de laboratoire a accès à un laboratoire, qui est une étendue inférieure au niveau d’abonnement. Par conséquent, un propriétaire de laboratoire ne sera pas en mesure de voir les machines virtuelles ou réseaux virtuels ou toutes les ressources qui sont en dehors du laboratoire.

### Comment puis-je créer un rôle pour permettre aux utilisateurs d’effectuer une tâche spécifique ?
Vous trouverez ici un article complet sur la façon de créer des rôles personnalisés et d’affecter des autorisations à ce rôle. Voici un exemple de script qui crée le rôle « Utilisateur avancé DevTest Labs », qui a l’autorisation de démarrer et d’arrêter toutes les machines virtuelles dans le laboratoire :
 
	$policyRoleDef = (Get-AzureRmRoleDefinition "DevTest Labs User") 
	$policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*') 
	$policyRoleDef.Id = $null 
	$policyRoleDef.Name = "DevTest Labs Advance User" 
	$policyRoleDef.IsCustom = $true 
	$policyRoleDef.AssignableScopes.Clear() 
	$policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>") 
	$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action") 
	$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action") 
	$policyRoleDef = (New-AzureRmRoleDefinition -Role $policyRoleDef)  
 
### DevTest Labs est-il intégré à ma chaîne d’outils CI/CD ? 
Si vous utilisez VSTS, il existe une [extension des tâches Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) qui vous permet d’automatiser votre pipeline de versions dans DevTest Labs. Voici quelques-unes des utilisations de cette extension :

- Création et déploiement automatiques d’une machine virtuelle et sa configuration avec le dernier build à l’aide des tâches de copie de fichiers Azure ou PowerShell VSTS.
- Capture automatique de l’état d’une machine virtuelle après le test, afin de reproduire un bogue sur la même machine virtuelle pour un examen approfondi.
- Suppression de la machine virtuelle à la fin du pipeline de versions lorsqu’elle n’est plus nécessaire.

Les billets de blog suivants offrent des conseils et des informations sur l’utilisation de l’extension VSTS :
 
- [General availability of Azure DevTest Labs – VSTS extension](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/) (Disponibilité générale d’Azure DevTest Labs - extension VSTS)
- [Deploying a new VM in an existing AzureDevTestLab from VSTS](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS) (Déploiement d’une nouvelle machine virtuelle dans un AzureDevTestLab existant depuis VSTS)
- [Using VSTS Release Management for Continuous Deployments to AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs) (Utilisation de VSTS Release Management pour des déploiements en continu vers AzureDevTestLabs)
 
Pour d’autres chaînes d’outils CI/CD, tous les scénarios mentionnés ci-dessus qui peuvent être réalisés via l’extension de tâches VSTS peuvent également être réalisés via le déploiement de [modèles Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) à l’aide des [applets de commande Azure PowerShell](../resource-group-template-deploy.md) et des [kits de développement logiciel (SDK) .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). De plus, vous pouvez utiliser les [API REST pour DevTest Labs](http://aka.ms/dtlrestapis) pour une intégration à votre chaîne d’outils.

### Pourquoi ne puis-je pas voir certaines machines virtuelles dans le panneau Machines virtuelles Azure alors que je peux les voir dans DevTest Labs ?
Lorsqu’une machine virtuelle est créée dans DevTest Labs, une autorisation est accordée pour accéder à cette machine virtuelle, et vous serez en mesure de la voir dans le panneau des laboratoires et le panneau **Machines virtuelles**. Si vous êtes un utilisateur DevTest Labs, cela vous permet de voir toutes les machines virtuelles créées dans le laboratoire via le panneau **All Virtual Machines** (Toutes les machines virtuelles). Toutefois, en tant qu’utilisateur DevTest Labs, vous ne bénéficiez pas automatiquement d’un accès en lecture aux ressources de machine virtuelle que d’autres ont créées. Celles-ci ne s’afficheront donc pas dans le panneau **Machines Virtuelles**.

### Quelle est la différence entre des images personnalisées et des formules ? 
Une image personnalisée est un disque dur virtuel, alors qu’une formule est une image que vous pouvez configurer avec des paramètres supplémentaires que vous pouvez enregistrer et reproduire. Une image personnalisée peut être préférable si vous souhaitez créer rapidement plusieurs environnements avec la même image de base, immuable. Une formule peut être préférable si vous souhaitez reproduire la configuration de votre machine virtuelle avec les tout derniers bits, un sous-réseau/réseau virtuel ou une taille spécifique. Pour plus de détails, consultez l’article [Comparaison entre les images personnalisées et les formules dans DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).
 
### Comment puis-je créer plusieurs machines virtuelles à partir du même modèle en une seule fois ? 
Vous pouvez utiliser [l’extension de tâches VSTS](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) ou [générer un modèle Azure Resource Manager](devtest-lab-add-vm-with-artifacts.md#save-arm-template) lors de la création d’une machine virtuelle et [déployer le modèle Azure Resource Manager à partir de Windows PowerShell](../resource-group-template-deploy.md).
 
### Comment puis-je déplacer mes machines virtuelles Azure existantes dans mon laboratoire DevTest Labs ? 
Nous sommes en train de concevoir une solution pour déplacer directement des machines virtuelles vers DevTest Labs, mais actuellement vous pouvez copier vos machines virtuelles existantes dans DevTest Labs comme suit :

1. Copiez le fichier de disque dur virtuel de votre machine virtuelle existante à l’aide de ce [script Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1).
1. [Créez l’image personnalisée](devtest-lab-create-template.md) dans votre laboratoire DevTest Labs.
1. Créez une machine virtuelle dans le laboratoire à partir de votre image personnalisée.
 
### Puis-je attacher plusieurs disques à mes machines virtuelles ? 
Vous pouvez attacher plusieurs disques aux machines virtuelles.
 
### Comment puis-je automatiser le processus de téléchargement des fichiers de disque dur virtuel pour créer des images personnalisées ? 
Nous avons deux options :

- [Azure AzCopy](../storage/storage-use-azcopy.md#blob-upload) peut être utilisé pour copier ou télécharger des fichiers de disque dur virtuel sur le compte de stockage associé au laboratoire.
- [L’Explorateur Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) est une application autonome qui s’exécute sur Windows, OSX et Linux.
 
Pour trouver le compte de stockage de destination associé à votre laboratoire :

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Sélectionnez **Groupes de ressources** à partir du panneau de gauche.
1. Recherchez et sélectionnez le groupe de ressources associé à votre laboratoire.
1. Dans le panneau **Vue d’ensemble**, sélectionnez l’un des comptes de stockage.
1. Sélectionnez **Objets Blob**.
1. Recherchez les téléchargements dans la liste. S’il n’y en a aucun, revenez à l’étape 4 et essayez un autre compte de stockage.
1. Utilisez **l’URL** en tant que destination dans votre commande AzCopy.

### Que sont les artefacts ? 
Les artefacts sont des éléments personnalisables qui peuvent être utilisés pour déployer vos tout derniers bits ou vos outils de développement sur une machine virtuelle. Ils sont attachés à votre machine virtuelle lors de la création en quelques clics simples, et une fois que la machine virtuelle est configurée, les artefacts déploient et configurent votre machine virtuelle. Il existe un nombre d’artefacts préexistants dans notre [référentiel Github public](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), mais vous pouvez également [créer vos propres artefacts](devtest-lab-artifact-author.md) facilement.

### Comment puis-je créer un laboratoire à partir d’un modèle Azure Resource Manager ? 
Nous avons un [référentiel Github de modèles Azure Resource Manager de laboratoire](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates). Chacun de ces modèles a un lien sur lequel vous pouvez cliquer pour déployer le laboratoire DevTest Labs sous votre abonnement Azure.
 
### Pourquoi toutes mes machines virtuelles sont-elles créées dans différents groupes de ressources avec des noms arbitraires ? Puis-je renommer ou modifier ces groupes de ressources ? 
Les groupes de ressources sont créés de cette façon pour que DevTest Labs puisse gérer les autorisations utilisateur et l’accès aux machines virtuelles. Nous travaillons à l’amélioration de cette expérience afin d’offrir davantage de souplesse, mais vous pouvez renommer ces groupes de ressources comme nécessaire. Nous vous recommandons de ne pas déplacer les machines virtuelles dans différents groupes de ressources afin d’éviter de modifier par inadvertance des autorisations.
 
### Combien de laboratoires puis-je créer sous le même abonnement ? 
Il n’existe aucune limite spécifique concernant le nombre de laboratoires qui peuvent être créés par abonnement, mais les ressources utilisées sont limitées par abonnement. Vous pouvez consulter des articles pour en savoir plus sur les [quotas et les limites imposés pour les abonnements Azure](../azure-subscription-service-limits.md) et [comment augmenter ces limites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).
 
### Combien de machines virtuelles puis-je créer par laboratoire ? 
Il n’existe pas de limite spécifique concernant le nombre de machines virtuelles pouvant être créées par laboratoire, mais actuellement le laboratoire prend uniquement en charge 40 machines virtuelles s’exécutant en même temps dans le stockage standard et 25 machines virtuelles s’exécutant simultanément dans Premium Storage. Nous travaillons actuellement à l’augmentation de ces limites.
 
### Mon artefact a échoué lors de la création d’une machine virtuelle. Comment puis-je résoudre ce problème ? 
Reportez-vous à ce billet de blog [How to troubleshoot failing Artifacts in AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs) (Comment résoudre le problème des artefacts qui échouent dans AzureDevTestLabs) écrit par un de nos MVP, pour savoir comment obtenir des journaux concernant votre artefact ayant échoué.
 
### Pourquoi mon réseau virtuel existant n’est pas enregistré correctement ?  
Il se peut que votre nom de réseau virtuel contienne des points. Si tel est le cas, essayez de supprimer les points ou remplacez-les par des tirets, puis réessayez d’enregistrer le réseau virtuel.

<!---HONumber=AcomDC_0810_2016-->
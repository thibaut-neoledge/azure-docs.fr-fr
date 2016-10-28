<properties
   pageTitle="Résolution des erreurs courantes lors du déploiement Azure | Microsoft Azure"
   description="Décrit comment résoudre les erreurs courantes lors du déploiement de ressources sur Azure à l’aide d’Azure Resource Manager."
   services="azure-resource-manager"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"
   keywords="erreur de déploiement, déploiement Azure, déployer dans azure"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/14/2016"
   ms.author="tomfitz"/>

# Résolution des erreurs courantes dans des déploiements Azure avec Azure Resource Manager

Cette rubrique décrit comment résoudre certaines erreurs courantes liées au déploiement d’Azure que vous pouvez rencontrer. Si vous avez besoin de plus d’informations concernant l’échec de votre déploiement, consultez [Voir les opérations de déploiement](resource-manager-troubleshoot-deployments-portal.md), puis revenez à cet article pour résoudre l’erreur.

## Modèle ou ressource non valide

Lorsque vous déployez un modèle, vous pouvez recevoir :

    Code=InvalidTemplate 
    Message=Deployment template validation failed

Si vous recevez une erreur indiquant que le modèle ou une propriété de ressource n’est pas valide, cela signifie peut-être que votre modèle comporte une erreur de syntaxe. Cette erreur est facile à commettre car les expressions de modèle peuvent être complexes. Par exemple, l’affectation de nom suivante pour un compte de stockage contient un jeu de crochets, trois fonctions, trois jeux de parenthèses, un jeu de guillemets simples et une propriété :

    "name": "[concat('storage', uniqueString(resourceGroup().id))]",

Si vous ne fournissez pas toutes les syntaxes correspondantes, le modèle produit une valeur très différente de votre intention.

Lorsque vous recevez ce type d’erreur, examinez attentivement la syntaxe d’expression. Vous pouvez utiliser un éditeur JSON comme [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) ou [Visual Studio Code](resource-manager-vs-code.md), qui vous signale les erreurs de syntaxe.

## Longueurs de segments incorrectes

Une autre erreur de modèle non valide se produit lorsque le nom de la ressource n’est pas au format approprié.

    Code=InvalidTemplate
    Message=Deployment template validation failed: 'The template resource {resource-name}' 
    for type {resource-type} has incorrect segment lengths.

Une ressource au niveau racine doit avoir un segment de moins dans le nom que dans le type de ressource. Chaque segment se différencie par une barre oblique. Dans l’exemple suivant, le type comporte 2 segments et le nom 1 segment : il s’agit donc d’un **nom valide**.

    {
      "type": "Microsoft.Web/serverfarms",
      "name": "myHostingPlanName",

Mais l’exemple suivant n’est **pas un nom valide** car il possède le même nombre de segments que le type.

    {
      "type": "Microsoft.Web/serverfarms",
      "name": "appPlan/myHostingPlanName",

Pour les ressources enfants, le type et le nom doivent avoir le même nombre de segments. C’est logique car le nom complet et le type de l’enfant inclut le nom et le type du parent. Par conséquent, le nom complet a toujours un segment de moins que le type complet.

    "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "contosokeyvault",
            ...
            "resources": [
                {
                    "type": "secrets",
                    "name": "appPassword",

Obtenir des segments valides peut être particulièrement difficile si des types Resource Manager sont appliqués à plusieurs fournisseurs de ressources. Par exemple, l’installation d’un verrou de ressource sur un site web nécessite un type avec 4 segments. Par conséquent, le nom comporte 3 segments :

    {
        "type": "Microsoft.Web/sites/providers/locks",
        "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",

## Le nom de la ressource existe déjà ou est déjà utilisé par une autre ressource

Pour certaines ressources, notamment les comptes de stockage, les serveurs de base de données et les sites web, vous devez fournir un nom de ressource qui est unique dans l’ensemble de l’environnement Azure. Si vous ne fournissez pas un nom unique, une erreur de ce type peut s’afficher :

    Code=StorageAccountAlreadyTaken 
    Message=The storage account named mystorage is already taken.

Vous pouvez créer un nom unique en concaténant votre nommage avec le résultat de la fonction [uniqueString](resource-group-template-functions.md#uniquestring).

    "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]",
    "type": "Microsoft.Storage/storageAccounts",

## Impossible de trouver une ressource lors du déploiement

Resource Manager optimise le déploiement en créant des ressources en parallèle, quand cela est possible. Si une ressource doit être déployée après une autre ressource, vous devez utiliser l’élément **dependsOn** de votre modèle pour créer une dépendance avec l’autre ressource. Par exemple, quand vous déployez une application web, le plan App Service doit être présent. Si vous n’avez pas spécifié que l’application web est dépendante du plan App Service, Resource Manager crée les deux ressources en même temps. Vous recevez une erreur indiquant que la ressource du plan App Service est introuvable, car elle n’existe pas encore quand vous tentez de définir une propriété sur l’application web. Vous pouvez éviter cette erreur en définissant la dépendance dans l’application web.

    {
      "apiVersion": "2015-08-01",
      "type": "Microsoft.Web/sites",
      ...
      "dependsOn": [
        "[variables('hostingPlanName')]"
      ],
      ...
    }

## Could not find member ’copy’ on object (Impossible de trouver le membre « copy » sur l’objet)

Vous rencontrez cette erreur lorsque vous appliquez l’élément **copy** à une partie du modèle qui ne prend pas en charge cet élément. Vous pouvez uniquement appliquer l’élément copy à un type de ressource. Vous ne pouvez pas appliquer l’élément copy à une propriété au sein d’un type de ressource. Par exemple, vous appliquez copy à une machine virtuelle, mais ne pouvez pas l’appliquer aux disques du système d’exploitation pour une machine virtuelle. Dans certains cas, vous pouvez convertir une ressource enfant en ressource parent afin de créer une boucle de copie. Pour plus d’informations sur l’utilisation de copy, voir [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).

## Référence (SKU) non disponible

Lors du déploiement d’une ressource (généralement une machine virtuelle), vous pouvez recevoir le message et le code d’erreur suivants :

    Code: SkuNotAvailable
    Message: The requested tier for resource '<resource>' is currently not available in location '<location>' for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.

Vous recevez cette erreur lorsque la ressource de référence (SKU) que vous avez sélectionnée (par exemple, la taille de la machine virtuelle) n’est pas disponible pour l’emplacement que vous avez sélectionné. Deux options s’offrent à vous pour résoudre ce problème :

1.	Connectez-vous au portail et commencez à ajouter une nouvelle ressource via l’interface utilisateur. Lorsque vous définissez les valeurs, vous verrez les références (SKU) disponibles pour cette ressource.

    ![Références (SKU) disponibles](./media/resource-manager-common-deployment-errors/view-sku.png)

2.	Si vous ne parvenez pas à trouver une référence appropriée dans cette région ou une autre qui réponde aux besoins de votre entreprise, veuillez contacter le [support Azure](https://portal.azure.com/#create/Microsoft.Support).


## Aucun fournisseur inscrit trouvé

Lorsque vous déployez une ressource, vous pouvez recevoir le message et le code d’erreur suivants :

    Code: NoRegisteredProviderFound
    Message: No registered resource provider found for location '<location>' and API version '<api-version>' for type '<resource-type>'.

Cette erreur apparaît pour l’une des trois raisons suivantes :

1. L’emplacement n’est pas pris en charge pour le type de ressource.
2. La version de l’API n’est pas prise en charge pour le type de ressource.
3. Le fournisseur de ressources n’a pas été inscrit pour votre abonnement.

Le message d’erreur doit fournir des suggestions pour les emplacements et les versions d’API pris en charge. Vous pouvez changer votre modèle en l’une des valeurs suggérées. La plupart des fournisseurs sont inscrits automatiquement par le portail Azure ou l’interface de ligne de commande que vous utilisez, mais pas tous. Si vous n’avez pas déjà utilisé un fournisseur de ressources spécifique, vous devrez peut-être inscrire ce dernier. Pour en savoir plus sur les fournisseurs de ressources, utilisez les commandes suivantes.

### PowerShell

Pour afficher l’état de votre inscription, utilisez **Get-AzureRmResourceProvider**.

    Get-AzureRmResourceProvider -ListAvailable

Pour inscrire un fournisseur, utilisez **Register-AzureRmResourceProvider** et indiquez le nom du fournisseur de ressources que vous voulez inscrire.

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn

Pour obtenir les emplacements pris en charge d’un type particulier de ressource, utilisez la commande suivante :

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

Pour obtenir les versions d’API prises en charge d’un type particulier de ressource, utilisez la commande suivante :

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

### Interface de ligne de commande Azure

Pour voir si le fournisseur est inscrit, utilisez la commande `azure provider list`.

    azure provider list

Pour inscrire un fournisseur de ressources, utilisez la commande `azure provider register` et indiquez *l’espace de noms* à inscrire.

    azure provider register Microsoft.Cdn

Pour afficher les emplacements et les versions d’API pris en charge pour un fournisseur de ressources, utilisez la commande suivante :

    azure provider show -n Microsoft.Compute --json > compute.json

## Quota dépassé

Vous pouvez rencontrer des problèmes quand un déploiement dépasse un quota qui peut concerner entre autres les groupes de ressources, les abonnements ou les comptes. Par exemple, votre abonnement peut être configuré pour limiter le nombre de cœurs dans une région. Si vous tentez de déployer une machine virtuelle avec plus de cœurs que la quantité autorisée, vous recevez une erreur indiquant que le quota a été dépassé. Pour obtenir des informations complètes sur les quotas, consultez [Abonnement Azure et limites, quotas et contraintes du service](azure-subscription-service-limits.md).

Pour examiner les quotas de cœurs de votre abonnement, vous pouvez utiliser la commande `azure vm list-usage` dans l’interface de ligne de commande Azure. L’exemple suivant montre que le quota de cœurs d’un compte d’évaluation gratuit est de 4 :

    azure vm list-usage

Résultat :

    info:    Executing command vm list-usage
    Location: westus
    data:    Name   Unit   CurrentValue  Limit
    data:    -----  -----  ------------  -----
    data:    Cores  Count  0             4
    info:    vm list-usage command OK

Si vous essayez de déployer un modèle créant plus de 4 cœurs dans l’Ouest des États-Unis dans l’abonnement ci-dessus, vous obtenez une erreur de déploiement qui peut ressembler à ceci (dans le portail ou en examinant les journaux de déploiement) :

    statusCode:Conflict
    serviceRequestId:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    statusMessage:{"error":{"code":"OperationNotAllowed","message":"Operation results in exceeding quota limits of Core. Maximum allowed: 4, Current in use: 4, Additional requested: 2."}}

Dans PowerShell, vous pouvez également utiliser l’applet de commande **Get-AzureRmVMUsage**.

    Get-AzureRmVMUsage

Résultat :

    ...
    CurrentValue : 0
    Limit        : 4
    Name         : {
                     "value": "cores",
                     "localizedValue": "Total Regional Cores"
                   }
    Unit         : null
    ...

Dans ce cas, vous devez accéder au portail et signaler un problème de support afin d’augmenter votre quota pour la région vers laquelle vous souhaitez procéder au déploiement.

> [AZURE.NOTE] N’oubliez pas que pour les groupes de ressources, le quota est défini pour chaque région, pas pour tout l’abonnement. Si vous devez déployer 30 cœurs dans l’Ouest des États-Unis, vous devez demander 30 cœurs Resource Manager dans l’Ouest des États-Unis. Si vous devez déployer 30 cœurs dans l’une des régions auxquelles vous avez accès, vous devez demander 30 cœurs Resource Manager dans toutes les régions.


## Échec de l’autorisation

Vous pouvez recevoir une erreur lors du déploiement, car le compte ou le principal du service qui tente de déployer les ressources n’a pas accès à ces actions. Azure Active Directory permet à votre administrateur ou à vous-même de contrôler très précisément quelles identités peuvent accéder à quelles ressources. Par exemple, si votre compte est affecté au rôle de lecteur, il ne peut pas créer de ressources. Dans ce cas, vous devriez voir un message erreur indiquant que l’autorisation a échoué.

Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez la rubrique [Contrôle d’accès en fonction du rôle d’Azure](./active-directory/role-based-access-control-configure.md).

En plus du contrôle d’accès en fonction du rôle, vos actions de déploiement peuvent être limitées par les stratégies appliquées à l’abonnement. Par le biais de stratégies, l’administrateur peut appliquer les conventions sur toutes les ressources déployées dans l’abonnement. Par exemple, un administrateur peut exiger qu’une valeur de balise particulière soit fournie pour un type de ressource. Si vous n’avez pas rempli les critères de la stratégie, vous recevez une erreur pendant le déploiement. Pour plus d’informations sur les stratégies, consultez [Utiliser le service Policy pour gérer les ressources et contrôler l’accès](resource-manager-policy.md).

## Résolution des problèmes - Machines virtuelles

| Erreur | Articles |
| -------- | ----------- |
| Erreurs d’extension de script personnalisé | [Échecs d’extension de machine virtuelle Windows](./virtual-machines/virtual-machines-windows-extensions-troubleshoot.md)<br />ou<br />[Échecs d’extension de machine virtuelle Linux](./virtual-machines/virtual-machines-linux-extensions-troubleshoot.md) |
| Erreurs de configuration d’image de système d’exploitation | [Erreurs d’une nouvelle machine virtuelle Windows](./virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md)<br />ou<br />[Erreurs d’une nouvelle machine virtuelle Linux](./virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md) |
| Échecs d’allocation | [Échecs d’allocation de machine virtuelle Windows](./virtual-machines/virtual-machines-windows-allocation-failure.md)<br />ou<br />[Échecs d’allocation de machine virtuelle Linux](./virtual-machines/virtual-machines-linux-allocation-failure.md) |
| Erreurs de Secure Shell (SSH) lors de la tentative de connexion | [Connexions Secure Shell à la machine virtuelle Linux](./virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md) |
| Erreurs de connexion à l’application s’exécutant sur la machine virtuelle | [Application s’exécutant sur une machine virtuelle Windows](./virtual-machines/virtual-machines-windows-troubleshoot-app-connection.md)<br />ou<br />[Application s’exécutant sur une machine virtuelle Linux](./virtual-machines/virtual-machines-linux-troubleshoot-app-connection.md) |
| Erreurs de connexion Bureau à distance | [Connexions Bureau à distance avec une machine virtuelle Windows](./virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md) |
| Erreurs de connexion résolues par un nouveau déploiement | [Redéployer une machine virtuelle vers un nouveau nœud Azure](./virtual-machines/virtual-machines-windows-redeploy-to-new-node.md) |
| Erreurs de service cloud | [Problèmes de déploiement de service cloud](./cloud-services/cloud-services-troubleshoot-deployment-problems.md) |

## Résolution des problèmes d’autres services

Le tableau suivant ne constitue pas une liste exhaustive des rubriques de dépannage pour Azure. Au lieu de cela, il présente des problèmes liés au déploiement ou à la configuration des ressources. Si vous avez besoin d’aide pour résoudre les problèmes d’exécution d’une ressource, consultez la documentation du service Azure concerné.

| de diffusion en continu | Article |
| -------- | -------- |
| Automation | [Conseils de dépannage pour les erreurs courantes dans Azure Automation](./automation/automation-troubleshooting-automation-errors.md) |
| Azure Stack | [Dépannage de Microsoft Azure Stack](./azure-stack/azure-stack-troubleshooting.md) |
| Azure Stack | [Applications web et Azure Stack](./azure-stack/azure-stack-webapps-troubleshoot-known-issues.md) |
| Data Factory | [Résolution des problèmes liés à Data Factory](./data-factory/data-factory-troubleshoot.md) |
| Service Fabric | [Résoudre les problèmes courants rencontrés pendant le déploiement de services dans Azure Service Fabric](./service-fabric/service-fabric-diagnostics-troubleshoot-common-scenarios.md) |
| Site Recovery | [Surveiller et résoudre les problèmes de protection pour les machines virtuelles et les serveurs physiques](./site-recovery/site-recovery-monitoring-and-troubleshooting.md) |
| Storage | [Analyser, diagnostiquer et dépanner Microsoft Azure Storage](./storage/storage-monitoring-diagnosing-troubleshooting.md) |
| StorSimple | [Résolution des problèmes de déploiement d’un appareil StorSimple](./storsimple/storsimple-troubleshoot-deployment.md) |
| Base de données SQL | [Résoudre des problèmes de connexion à la base de données SQL Azure](./sql-database/sql-database-troubleshoot-common-connection-issues.md) |
| SQL Data Warehouse | [Résolution des problèmes d’Azure SQL Data Warehouse](./sql-data-warehouse/sql-data-warehouse-troubleshoot.md) |

## Déterminer quand un déploiement est prêt

Azure Resource Manager indique qu’un déploiement a réussi quand celui-ci retourne tous les fournisseurs avec succès. Toutefois, cela ne signifie pas nécessairement que votre groupe de ressources est « actif et prêt pour vos utilisateurs ». Par exemple, un déploiement peut nécessiter le téléchargement de mises à niveau, l’attente de ressources sans modèle, ou encore l’installation de scripts complexes ou d’une autre activité exécutable qu’Azure ne connaît pas, car il ne s’agit pas d’une activité suivie par un fournisseur. Dans ce cas, cela peut prendre un moment avant que vos ressources soient prêtes pour une utilisation concrète. Par conséquent, il est normal que l'état du déploiement indique la réussite un certain temps avant que votre déploiement puisse être utilisé.

Vous pouvez toutefois empêcher Azure de signaler la réussite d'un déploiement en créant un script personnalisé pour votre modèle personnalisé (à l'aide de l'extension [CustomScriptExtension](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/), par exemple) qui sait comment détecter la disponibilité pour tout le système par une surveillance de l'ensemble du déploiement et qui se termine correctement uniquement lorsque les utilisateurs peuvent interagir avec la totalité du déploiement. Si vous voulez vous assurer que votre extension est la dernière à s'exécuter, utilisez la propriété **dependsOn** dans votre modèle.

## Étapes suivantes

- Pour en savoir plus sur les actions d’audit, consultez [Opérations d’audit avec Resource Manager](resource-group-audit.md).
- Pour en savoir plus sur les actions visant à déterminer les erreurs au cours du déploiement, consultez [Voir les opérations de déploiement](resource-manager-troubleshoot-deployments-portal.md).

<!---HONumber=AcomDC_0720_2016-->
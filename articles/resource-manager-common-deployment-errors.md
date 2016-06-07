<properties
   pageTitle="Résolution des erreurs courantes lors du déploiement Azure | Microsoft Azure"
   description="Décrit comment résoudre les erreurs courantes lors du déploiement avec Azure Resource Manager."
   services="azure-resource-manager"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/18/2016"
   ms.author="tomfitz"/>

# Résoudre les erreurs courantes lors du déploiement de ressources sur Azure avec Azure Resource Manager

Cette rubrique décrit comment résoudre certaines des erreurs courantes que vous pouvez rencontrer lors du déploiement de ressources sur Azure. Elle part du principe qu’un message d’erreur s’est affiché. Pour plus d’informations sur la raison de l’échec de votre déploiement, consultez [Voir les opérations de déploiement](resource-manager-troubleshoot-deployments-portal.md).

## Modèle ou ressource non valide

Si vous recevez une erreur indiquant que le modèle ou une propriété de ressource n’est pas valide, cela signifie peut-être qu’il manque un caractère dans votre modèle. Cette erreur est courante lors de l’utilisation d’expressions de modèle, car l’expression est placée entre guillemets. Par conséquent, le JSON accepte la validation et votre éditeur est susceptible de ne pas détecter l’erreur. Par exemple, l’affectation de nom suivante pour un compte de stockage contient un jeu de crochets, trois fonctions, trois jeux de parenthèses, un jeu de guillemets simples et une propriété :

    "name": "[concat('storage', uniqueString(resourceGroup().id))]",

Si vous ne fournissez pas toutes les syntaxes correspondantes, le modèle produit une valeur très différente de votre intention.

Selon l’emplacement où le caractère manque dans le modèle, vous pouvez recevoir un message d’erreur indiquant que le modèle ou qu’une ressource n’est pas valide. L’erreur peut également indiquer que le processus de déploiement n’a pas été en mesure de traiter l’expression de langage de modèle. Lorsque vous recevez ce type d’erreur, examinez attentivement la syntaxe d’expression.

## Le nom de la ressource existe déjà

Pour certaines ressources, notamment les comptes de stockage, les serveurs de base de données et les sites web, vous devez fournir un nom de ressource qui est unique dans l'ensemble de l’environnement Azure. Vous pouvez créer un nom unique en concaténant votre convention d’affectation de noms avec le résultat de la fonction [uniqueString](./resource-group-template-functions/#uniquestring).
 
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

## Emplacement non disponible pour la ressource

Lorsque vous spécifiez l’emplacement d’une ressource, vous devez utiliser un des emplacements prenant en charge cette ressource. Avant d'entrer un emplacement pour une ressource, utilisez une des commandes suivantes pour vérifier que l'emplacement est compatible avec le type de ressource concerné.

### PowerShell

Utilisez **Get-AzureRmResourceProvider** pour obtenir les emplacements et les types pris en charge pour un fournisseur de ressources particulier.

    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web

Vous obtenez une liste des types de ressources pour ce fournisseur de ressources.

    ProviderNamespace RegistrationState ResourceTypes               Locations
    ----------------- ----------------- -------------               ---------
    Microsoft.Web     Registered        {sites/extensions}          {Brazil South, ...
    Microsoft.Web     Registered        {sites/slots/extensions}    {Brazil South, ...
    Microsoft.Web     Registered        {sites/instances}           {Brazil South, ...
    ...

Vous pouvez vous concentrer sur les emplacements d’un type particulier de ressource avec :

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

Les emplacements pris en charge sont retournés :

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia

### Interface de ligne de commande Azure

Pour l’interface de ligne de commande Azure, vous pouvez utiliser **azure location list**. Étant donné que la liste des emplacements peut être longue et qu'il existe de nombreux fournisseurs, vous pouvez utiliser des outils pour examiner les fournisseurs et les emplacements avant d'utiliser un emplacement qui n'est pas encore disponible. Le script suivant utilise **jq** pour détecter les emplacements dans lesquels le fournisseur de ressources pour les machines virtuelles Azure est disponible.

    azure location list --json | jq '.[] | select(.name == "Microsoft.Compute/virtualMachines")'
    
Les emplacements pris en charge sont retournés :
    
    {
      "name": "Microsoft.Compute/virtualMachines",
      "location": "East US,East US 2,West US,Central US,South Central US,North Europe,West Europe,East Asia,Southeast Asia,Japan East,Japan West"
    }

### API REST

Pour l'API REST, consultez [Obtention d'informations sur un fournisseur de ressources](https://msdn.microsoft.com/library/azure/dn790534.aspx).

## Quota dépassé

Vous pouvez rencontrer des problèmes quand un déploiement dépasse un quota qui peut concerner entre autres les groupes de ressources, les abonnements ou les comptes. Par exemple, votre abonnement peut être configuré pour limiter le nombre de cœurs dans une région. Si vous tentez de déployer une machine virtuelle avec plus de cœurs que la quantité autorisée, vous recevez une erreur indiquant que le quota a été dépassé. Pour obtenir des informations complètes sur les quotas, consultez [Abonnement Azure et limites, quotas et contraintes du service](azure-subscription-service-limits.md).

Pour examiner les quotas de cœurs de votre abonnement, vous pouvez utiliser la commande `azure vm list-usage` dans l’interface de ligne de commande Azure. L’exemple suivant montre que le quota de cœurs d’un compte d’évaluation gratuit est de 4 :

    azure vm list-usage
    
Résultat :
    
    info:    Executing command vm list-usage
    Location: westus
    data:    Name   Unit   CurrentValue  Limit
    data:    -----  -----  ------------  -----
    data:    Cores  Count  0             4
    info:    vm list-usage command OK

Si vous essayez de déployer un modèle créant plus de 4 cœurs dans l’Ouest des États-Unis dans l’abonnement ci-dessus, vous obtenez une erreur de déploiement qui peut ressembler à ceci (dans le portail ou en examinant les journaux de déploiement) :

    statusCode:Conflict
    serviceRequestId:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    statusMessage:{"error":{"code":"OperationNotAllowed","message":"Operation results in exceeding quota limits of Core. Maximum allowed: 4, Current in use: 4, Additional requested: 2."}}

Dans PowerShell, vous pouvez également utiliser l’applet de commande **Get-AzureRmVMUsage**.

    Get-AzureRmVMUsage
    
Résultat :

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

> [AZURE.NOTE] N’oubliez pas que pour les groupes de ressources, le quota est défini pour chaque région, pas pour tout l’abonnement. Si vous devez déployer 30 cœurs dans l’Ouest des États-Unis, vous devez demander 30 cœurs Resource Manager dans l’Ouest des États-Unis. Si vous devez déployer 30 cœurs dans l’une des régions auxquelles vous avez accès, vous devez demander 30 cœurs Resource Manager dans toutes les régions.


## Échec de l’autorisation

Vous pouvez recevoir une erreur lors du déploiement, car le compte ou le principal du service qui tente de déployer les ressources n’a pas accès à ces actions. Azure Active Directory permet à votre administrateur ou à vous-même de contrôler très précisément quelles identités peuvent accéder à quelles ressources. Par exemple, si votre compte est affecté au rôle de lecteur, il ne peut pas créer de ressources. Dans ce cas, vous devriez voir un message erreur indiquant que l’autorisation a échoué.

Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez [Contrôle d’accès en fonction du rôle d’Azure](./active-directory/role-based-access-control-configure.md).

En plus du contrôle d’accès en fonction du rôle, vos actions de déploiement peuvent être limitées par les stratégies appliquées à l’abonnement. Par le biais de stratégies, l’administrateur peut appliquer les conventions sur toutes les ressources déployées dans l’abonnement. Par exemple, un administrateur peut exiger qu’une valeur de balise particulière soit fournie pour un type de ressource. Si vous n’avez pas rempli les critères de la stratégie, vous recevez une erreur pendant le déploiement. Pour plus d’informations sur les stratégies, consultez [Utiliser le service Policy pour gérer les ressources et contrôler l’accès](resource-manager-policy.md).

## Vérifier l’inscription du fournisseur de ressources

Les ressources sont gérées par les fournisseurs de ressources, et un compte ou un abonnement doit être inscrit pour utiliser un fournisseur particulier. La plupart des fournisseurs sont inscrits automatiquement par le portail Azure ou l’interface de ligne de commande que vous utilisez, mais pas tous.

### PowerShell

Pour afficher l’état de votre inscription, utilisez **Get-AzureRmResourceProvider**.

    Get-AzureRmResourceProvider -ListAvailable

Tous les fournisseurs de ressources disponibles et l’état de votre inscription sont retournés :

    ProviderNamespace               RegistrationState ResourceTypes
    -----------------               ----------------- -------------
    Microsoft.ApiManagement         Unregistered      {service, validateServiceName, checkServiceNameAvailability}
    Microsoft.AppService            Registered        {apiapps, appIdentities, gateways, deploymenttemplates...}
    Microsoft.Batch                 Registered        {batchAccounts}

Pour inscrire un fournisseur, utilisez **Register-AzureRmResourceProvider** et indiquez le nom du fournisseur de ressources que vous voulez inscrire.

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn

Vous êtes invité à confirmer l’inscription et un état est retourné.

    Confirm
    Are you sure you want to register the provider 'Microsoft.Cdn'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

    ProviderNamespace RegistrationState ResourceTypes
    ----------------- ----------------- -------------
    Microsoft.Cdn     Registering       {profiles, profiles/endpoints,...

### Interface de ligne de commande Azure

Pour voir si le fournisseur est enregistré pour être utilisé à l'aide de l'interface de ligne de commande Azure, utilisez la commande `azure provider list` (l'exemple de sortie ci-dessous est tronqué).

    azure provider list
        
Tous les fournisseurs de ressources disponibles et l’état de votre inscription sont retournés :
        
    info:    Executing command provider list
    + Getting ARM registered providers
    data:    Namespace                        Registered
    data:    -------------------------------  -------------
    data:    Microsoft.Compute                Registered
    data:    Microsoft.Network                Registered  
    data:    Microsoft.Storage                Registered
    data:    microsoft.visualstudio           Registered
    ...
    info:    provider list command OK

Pour inscrire un fournisseur de ressources, utilisez la commande `azure provider register` et indiquez l’*espace de noms* à inscrire.

    azure provider register Microsoft.Cdn

### API REST

Pour afficher l'état de l'inscription, consultez [Obtention d'informations sur un fournisseur de ressources](https://msdn.microsoft.com/library/azure/dn790534.aspx).

Pour inscrire un fournisseur, consultez [Inscription d'un abonnement auprès d'un fournisseur de ressources](https://msdn.microsoft.com/library/azure/dn790548.aspx).

## Erreurs d’extension de script personnalisé

Si vous rencontrez une erreur au niveau d’une extension de script personnalisé lors du déploiement d’une machine virtuelle, consultez [Dépannage des échecs d’extension de machine virtuelle Windows dans Azure](./virtual-machines/virtual-machines-windows-extensions-troubleshoot.md) ou [Dépannage des échecs d’extension de machine virtuelle Azure Linux](./virtual-machines/virtual-machines-linux-extensions-troubleshoot.md).

## Échecs d’approvisionnement et d’allocation des machines virtuelles

Si vous avez rencontré une erreur d’allocation ou d’approvisionnement d’une image de système d’exploitation pendant le déploiement d’une machine virtuelle, consultez [Troubleshoot creating a new VM](./virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md) (Résoudre les problèmes de création d’une machine virtuelle) et [Résoudre des échecs d’allocation](./virtual-machines/virtual-machines-windows-allocation-failure.md).

## Déterminer quand un déploiement est prêt 

Azure Resource Manager indique qu’un déploiement a réussi quand celui-ci retourne tous les fournisseurs avec succès. Toutefois, cela ne signifie pas nécessairement que votre groupe de ressources est « actif et prêt pour vos utilisateurs ». Par exemple, un déploiement peut nécessiter le téléchargement de mises à niveau, l’attente de ressources sans modèle, ou encore l’installation de scripts complexes ou d’une autre activité exécutable qu’Azure ne connaît pas, car il ne s’agit pas d’une activité suivie par un fournisseur. Dans ce cas, cela peut prendre un moment avant que vos ressources soient prêtes pour une utilisation concrète. Par conséquent, il est normal que l'état du déploiement indique la réussite un certain temps avant que votre déploiement puisse être utilisé.

Vous pouvez toutefois empêcher Azure de signaler la réussite d'un déploiement en créant un script personnalisé pour votre modèle personnalisé (à l'aide de l'extension [CustomScriptExtension](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/), par exemple) qui sait comment détecter la disponibilité pour tout le système par une surveillance de l'ensemble du déploiement et qui se termine correctement uniquement lorsque les utilisateurs peuvent interagir avec la totalité du déploiement. Si vous voulez vous assurer que votre extension est la dernière à s'exécuter, utilisez la propriété **dependsOn** dans votre modèle.

## Étapes suivantes

- Pour en savoir plus sur les actions d’audit, consultez [Opérations d’audit avec Resource Manager](resource-group-audit.md).
- Pour en savoir plus sur les actions visant à déterminer les erreurs au cours du déploiement, consultez [Voir les opérations de déploiement](resource-manager-troubleshoot-deployments-portal.md).
- Pour résoudre les erreurs liées au protocole RDP (Remote Desktop Protocol) sur votre machine virtuelle Windows, consultez [Connexions Bureau à distance](./virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md).
- Pour résoudre les erreurs liées à Secure Shell sur votre machine virtuelle Linux, consultez [Résolution des problèmes des connexions SSH avec une machine virtuelle Azure Linux](./virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md).

<!---HONumber=AcomDC_0525_2016-->
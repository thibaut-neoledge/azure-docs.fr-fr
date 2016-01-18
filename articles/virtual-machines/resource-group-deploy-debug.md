<properties
   pageTitle="Résolution des problèmes liés aux déploiements de groupes de ressources | Microsoft Azure"
   description="Décrit les problèmes courants de déploiement de ressources créés à l'aide du modèle de déploiement Resource Manager et montre comment détecter et résoudre ces problèmes."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="01/06/2016"
   ms.author="tomfitz;rasquill"/>

# Résolution des problèmes liés aux déploiements de groupes de ressources dans Azure

Lorsque vous rencontrez un problème lors du déploiement, vous devez découvrir ce qui s’est produit. Resource Manager fournit deux méthodes vous permettant de découvrir ce qui s’est passé et pourquoi. Vous pouvez utiliser les commandes de déploiement afin de récupérer des informations concernant des déploiements spécifiques pour un groupe de ressources. Ou vous pouvez utiliser les journaux d'audit pour récupérer des informations sur toutes les opérations effectuées sur un groupe de ressources. Ces informations vous permettent de résoudre le problème et de reprendre les opérations dans votre solution.

Cette rubrique se concentre principalement sur l'utilisation de commandes de déploiement pour résoudre les problèmes de déploiement. Pour plus d'informations sur l’utilisation des journaux d'audit pour effectuer le suivi de toutes les opérations sur vos ressources, consultez [Opérations d’audit avec Resource Manager](../resource-group-audit.md).

Cette rubrique montre comment récupérer des informations de dépannage via PowerShell Azure, Azure CLI et API REST. Pour plus d’informations sur l’utilisation du portail en version préliminaire pour résoudre les problèmes de déploiement, consultez [Utilisation du portail Azure pour gérer vos ressources Azure](../azure-portal/resource-group-portal.md).

Les solutions aux erreurs courantes que rencontrent les utilisateurs sont également décrites dans cette rubrique.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique. Vous ne pouvez pas créer de groupes de ressources avec le modèle de déploiement classique.


## Résolution des problèmes avec PowerShell

[AZURE.INCLUDE [powershell-preview-inline-include](../../includes/powershell-preview-inline-include.md)]

Vous pouvez obtenir l'état global d'un déploiement à l'aide de la commande **AzureRmResourceGroupDeployment**. Dans l'exemple ci-dessous, le déploiement a échoué.

    PS C:\> Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment

    DeploymentName    : ExampleDeployment
    ResourceGroupName : ExampleGroup
    ProvisioningState : Failed
    Timestamp         : 8/27/2015 8:03:34 PM
    Mode              : Incremental
    TemplateLink      :
    Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    siteName         String                     ExampleSite
                    hostingPlanName  String                     ExamplePlan
                    siteLocation     String                     West US
                    sku              String                     Free
                    workerSize       String                     0

    Outputs           :

Chaque déploiement est généralement constitué de plusieurs opérations, chacune d’elles représentant une étape du processus de déploiement. Pour découvrir la cause du problème rencontré lors d’un déploiement, vous devez généralement afficher les détails concernant les opérations de déploiement. Vous pouvez afficher l'état des opérations avec **Get-AzureRmResourceGroupDeploymentOperation**.

    PS C:\> Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup
    Id                        OperationId          Properties         
    -----------               ----------           -------------
    /subscriptions/xxxxx...   347A111792B648D8     @{ProvisioningState=Failed; Timestam...
    /subscriptions/xxxxx...   699776735EFC3D15     @{ProvisioningState=Succeeded; Times...

Cette commande montre deux opérations dans le déploiement. Une opération affiche un état d'approvisionnement ayant échoué (Failed) et l'autre un état d’approvisionnement ayant réussi (Succeeded).

Vous pouvez récupérer le message d'état avec la commande suivante :

    PS C:\> (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties.StatusMessage

    Code       : Conflict
    Message    : Website with given name mysite already exists.
    Target     :
    Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
    Innererror :

## Résolution des problèmes avec Azure CLI

Vous pouvez obtenir l'état global d'un déploiement à l'aide de la commande **azure group deployment show**. Dans l'exemple ci-dessous, le déploiement a échoué.

    azure group deployment show ExampleGroup ExampleDeployment

    info:    Executing command group deployment show
    + Getting deployments
    data:    DeploymentName     : ExampleDeployment
    data:    ResourceGroupName  : ExampleGroup
    data:    ProvisioningState  : Failed
    data:    Timestamp          : 2015-08-27T20:03:34.9178576Z
    data:    Mode               : Incremental
    data:    Name             Type    Value
    data:    ---------------  ------  ------------
    data:    siteName         String  ExampleSite
    data:    hostingPlanName  String  ExamplePlan
    data:    siteLocation     String  West US
    data:    sku              String  Free
    data:    workerSize       String  0
    info:    group deployment show command OK


Vous trouverez plus d'informations sur l’origine de l’échec du déploiement dans les journaux d'audit. Pour afficher les journaux d'audit, exécutez la commande **azure group log show**. Vous pouvez utiliser l'option **--last-deployment** pour récupérer uniquement le journal du déploiement le plus récent.

    azure group log show ExampleGroup --last-deployment

La commande **azure group log show** risque de renvoyer un grand nombre d'informations. Pour le dépannage, vous devez généralement vous concentrer sur les opérations ayant échoué. Le script suivant utilise les options **--json** et **jq** pour rechercher les échecs de déploiement dans le journal. Pour en savoir plus sur les outils comme **jq**, consultez [Outils utiles pour interagir avec Azure](#useful-tools-to-interact-with-azure)

    azure group log show ExampleGroup --json | jq '.[] | select(.status.value == "Failed")'

    {
      "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/<guid>/",
        "iat": "1442510510",
        "nbf": "1442510510",
        "exp": "1442514410",
        "ver": "1.0",
        "http://schemas.microsoft.com/identity/claims/tenantid": "<guid>",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "<guid>",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress": "someone@example.com",
        "puid": "XXXXXXXXXXXXXXXX",
        "http://schemas.microsoft.com/identity/claims/identityprovider": "example.com",

        "altsecid": "1:example.com:XXXXXXXXXXX",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string>",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Tom",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "FitzMacken",
        "name": "Tom FitzMacken",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
        "groups": "<guid>",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "example.com#someone@example.com",
        "wids": "<guid>",
        "appid": "<guid>",
        "appidacr": "0",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "ipaddr": "000.000.000.000"
      },
      "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{"Code":"Conflict","Message":"Website with given name mysite already exists.","Target":null,"Details":[{"Message":"Website with given name
          mysite already exists."},{"Code":"Conflict"},{"ErrorEntity":{"Code":"Conflict","Message":"Website with given name mysite already exists.","ExtendedCode":
          "54001","MessageTemplate":"Website with given name {0} already exists.","Parameters":["mysite"],"InnerErrors":null}}],"Innererror":null}"
      },
    ...

Vous pouvez constater que la section **properties** inclut des informations concernant l'opération ayant échoué dans json.

Vous pouvez utiliser les options **--verbose** et **- vv** pour afficher plus d'informations des journaux. Utilisez l'option **--verbose** pour afficher les étapes effectuées par les opérations dans `stdout`. Pour obtenir un historique complet des demandes, utilisez l'option **- vv**. Souvent, ces messages fournissent des indices essentiels sur les causes d'erreurs.

## Résolution des problèmes avec API REST

L’API REST de Resource Manager fournit l'URI permettant de récupérer des informations sur un déploiement, les opérations de déploiement et les détails d’une opération particulière. Pour une description complète de ces commandes, consultez :

- [Obtenir des informations sur le déploiement d’un modèle](https://msdn.microsoft.com/library/azure/dn790565.aspx)
- [Liste de toutes les opérations de déploiement de modèle](https://msdn.microsoft.com/library/azure/dn790518.aspx)
- [Obtenir des informations sur l’opération de déploiement d’un modèle](https://msdn.microsoft.com/library/azure/dn790519.aspx)


## Actualisation des informations d'identification expirées

Votre déploiement échouera si vos informations d'identification Azure ont expiré ou si vous n'êtes pas connecté à votre compte Azure. Vos informations d'identification peuvent expirer si votre session reste ouverte trop longtemps. Vous pouvez actualiser vos informations d'identification avec les options suivantes :

- Pour PowerShell, utilisez l'applet de commande **AzureRmAccount de connexion** (ou **Add-AzureAccount** pour les versions de PowerShell antérieures à 1.0 en version préliminaire). Les informations d'identification que contient un fichier de paramètres de publication ne sont pas suffisantes pour les applets de commande du module AzureResourceManager.
- Pour l’interface de ligne de commande Azure, utilisez **azure login**. Pour obtenir de l'aide sur les erreurs d'authentification, assurez-vous que vous avez [correctement configuré l'interface de ligne de commande Azure](../xplat-cli-connect.md).

## Vérification du format des modèles et des paramètres

Si le format de fichier de modèle ou de paramètre n'est pas correct, le déploiement échoue. Avant d'exécuter un déploiement, vous pouvez tester la validité de votre modèle et vos paramètres.

### PowerShell

Pour PowerShell, utilisez **Test-AzureRmResourceGroupDeployment** (ou **Test-AzureResourceGroupTemplate** pour les versions de PowerShell antérieures à 1.0 en version préliminaire).

    PS C:\> Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile c:\Azure\Templates\azuredeploy.json -TemplateParameterFile c:\Azure\Templates\azuredeploy.parameters.json
    VERBOSE: 12:55:32 PM - Template is valid.

### Interface de ligne de commande Azure

Pour l’interface de ligne de commande Azure, utilisez **azure group template validate <resource group>**

L’exemple suivant montre comment valider un modèle et tous les paramètres requis. L’interface de ligne de commande Azure vous propose des valeurs de paramètres requises.

        azure group template validate \
        > --template-uri "https://contoso.com/templates/azuredeploy.json" \
        > resource-group
        info:    Executing command group template validate
        info:    Supply values for the following parameters
        adminUserName: UserName
        adminPassword: PassWord
        + Initializing template configurations and parameters
        + Validating the template
        info:    group template validate command OK

### API REST

Pour l'API REST, voir [Validation d'un déploiement de modèle](https://msdn.microsoft.com/library/azure/dn790547.aspx).

## Vérification des emplacements prenant en charge la ressource

Lorsque vous spécifiez l’emplacement d’une ressource, vous devez utiliser un des emplacements prenant en charge cette ressource. Avant d'entrer un emplacement pour une ressource, utilisez une des commandes suivantes pour vérifier que l'emplacement est compatible avec le type de ressource concerné.

### PowerShell

Pour les versions de PowerShell antérieures à 1.0 en version préliminaire, vous pouvez consulter la liste complète des ressources et des emplacements à l'aide de la commande **Get-AzureLocation**.

    PS C:\> Get-AzureLocation

    Name                                    Locations                               LocationsString
    ----                                    ---------                               ---------------
    ResourceGroup                           {East Asia, South East Asia, East US... East Asia, South East Asia, East US,...
    Microsoft.ApiManagement/service         {Central US, East US, East US 2, Nor... Central US, East US, East US 2, Nort...
    Microsoft.AppService/apiapps            {East US, West US, South Central US,... East US, West US, South Central US, ...
    ...

Vous pouvez spécifier un type de ressource particulier avec :

    PS C:\> Get-AzureLocation | Where-Object Name -eq "Microsoft.Compute/virtualMachines" | Format-Table Name, LocationsString -Wrap

    Name                                                        LocationsString
    ----                                                        ---------------
    Microsoft.Compute/virtualMachines                           East US, East US 2, West US, Central US, South Central US,
                                                                North Europe, West Europe, East Asia, Southeast Asia,
                                                                Japan East, Japan West

Pour PowerShell 1.0 en version préliminaire, utilisez **Get-AzureRmResourceProvider** pour obtenir les emplacements pris en charge.

    PS C:\> Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web

    ProviderNamespace RegistrationState ResourceTypes               Locations
    ----------------- ----------------- -------------               ---------
    Microsoft.Web     Registered        {sites/extensions}          {Brazil South, ...
    Microsoft.Web     Registered        {sites/slots/extensions}    {Brazil South, ...
    Microsoft.Web     Registered        {sites/instances}           {Brazil South, ...
    ...

Vous pouvez spécifier un type de ressource particulier avec :

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

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

Pour l’interface de ligne de commande Azure, vous pouvez utiliser **azure location list**. Étant donné que la liste des emplacements peut être longue, et qu'il existe de nombreux fournisseurs, vous pouvez utiliser des outils pour examiner les fournisseurs et les emplacements avant d'utiliser un emplacement qui n'est pas encore disponible. Le script suivant utilise **jq** pour détecter les emplacements dans lesquels le fournisseur de ressources pour les machines virtuelles Azure est disponible.

    azure location list --json | jq '.[] | select(.name == "Microsoft.Compute/virtualMachines")'
    {
      "name": "Microsoft.Compute/virtualMachines",
      "location": "East US,East US 2,West US,Central US,South Central US,North Europe,West Europe,East Asia,Southeast Asia,Japan East,Japan West"
    }

### API REST

Pour l'API REST, consultez [Obtention d'informations sur un fournisseur de ressources](https://msdn.microsoft.com/library/azure/dn790534.aspx).

## Création de noms de ressources uniques

Pour certaines ressources, notamment les comptes de stockage, les serveurs de base de données et les sites web, vous devez fournir un nom de ressource qui est unique dans l'ensemble de l’environnement Azure. Il n'existe actuellement aucun moyen de tester si un nom est unique. Nous suggérons d'utiliser une convention d'affectation de noms qui ne risque pas d'être utilisée par d'autres organisations.

## Problèmes liés à l’authentification, aux abonnements, aux rôles et aux quotas

Il est possible qu'un ou plusieurs problèmes empêchent un déploiement impliquant l'authentification, l'autorisation et Azure Active Directory. Quelle que soit la manière dont vous gérez vos groupes de ressources Azure, l'identité que vous utilisez pour vous connecter à votre compte Azure doit être un objet Azure Active Directory. Cette identité peut être un compte professionnel ou scolaire que vous avez créé ou qui vous a été attribué, ou vous pouvez créer un principal du service pour les applications.

Mais avec Azure Active Directory, vous ou votre administrateur pouvez contrôler très précisément quelles identités peuvent accéder à quelles ressources. Si vos déploiements échouent, examinez les demandes elles-mêmes pour rechercher des signes d’authentification ou des problèmes d’autorisation, et consultez également les journaux de déploiement de votre groupe de ressources. Il est possible que bien que vous possédiez des autorisations pour certaines ressources, vous n’en ayez pas pour d’autres ressources. À l'aide de l'interface de ligne de commande Azure, vous pouvez examiner les locataires et les utilisateurs Azure Active Directory à l'aide des commandes `azure ad`. (Pour obtenir la liste complète des commandes de l’interface de ligne de commande Azure, consultez [Utilisation de l’interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Manager](azure-cli-arm-commands.md).)

Il est possible que vous rencontriez également des problèmes lorsqu’un déploiement atteint un quota par défaut, ce qui peut être par groupe de ressources, par abonnement, par compte, et pour d’autres étendues également. Vérifiez, à votre convenance, que vous disposez des ressources disponibles pour effectuer correctement le déploiement. Pour obtenir des informations complètes sur les quotas, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md).

Pour examiner vos propres quotas de mémoire à tores magnétiques de votre abonnement, vous devez utiliser la commande `azure vm list-usage` dans l’interface de ligne de commande Azure et l'applet de commande **Get-AzureVMUsage** dans PowerShell. Ce qui suit présente la commande dans l’interface de ligne de commande Azure et indique que le quota de cœurs d’un compte d’évaluation gratuit est de 4 :

    azure vm list-usage
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

Dans ce cas, vous devez accéder au portail et signaler un problème de support afin d’augmenter votre quota pour la région vers laquelle vous souhaitez procéder au déploiement.

> [AZURE.NOTE]N’oubliez pas que pour les groupes de ressources, le quota est défini pour chaque région, pas pour tout l’abonnement. Si vous devez déployer 30 cœurs dans l’Ouest des États-Unis, vous devez demander 30 cœurs Resource Manager dans l’Ouest des États-Unis. Si vous devez déployer 30 cœurs dans l’une des régions auxquelles vous avez accès, vous devez demander 30 cœurs Resource Manager dans toutes les régions. <!-- --> Concrètement, vous pouvez par exemple vérifier les régions pour lesquelles vous devez demander le quota approprié à l’aide de la commande suivante, qui est dirigée vers **jq** pour l’analyse json. <!-- --> azure provider show Microsoft.Compute --json | jq ’.resourceTypes | select(.name == "virtualMachines") | { name,apiVersions, locations}’ { "name": "virtualMachines", "apiVersions": [ "2015-05-01-preview", "2014-12-01-preview" ], "locations": [ "East US", "West US", "West Europe", "East Asia", "Southeast Asia" ] }


## Vérification de l’inscription du fournisseur de ressources

Les ressources sont gérées par les fournisseurs de ressources, et un compte ou un abonnement peut être activé pour utiliser un fournisseur particulier. Si vous êtes configuré pour utiliser un fournisseur, il doit également être enregistré pour être utilisé. La plupart des fournisseurs sont enregistrés automatiquement par le portail Azure ou l’interface de ligne de commande que vous utilisez, mais pas tous.

### PowerShell

Pour obtenir une liste de fournisseurs de ressources et l'état de votre inscription, utilisez **Get-AzureProvider** pour les versions de PowerShell antérieures à 1.0 en version préliminaire.

    PS C:\> Get-AzureProvider

    ProviderNamespace                       RegistrationState                       ResourceTypes
    -----------------                       -----------------                       -------------
    Microsoft.AppService                    Registered                              {apiapps, appIdentities, gateways, d...
    Microsoft.Batch                         Registered                              {batchAccounts}
    microsoft.cache                         Registered                              {Redis, checkNameAvailability, opera...
    ...

Pour inscrire un fournisseur, utilisez **Register-AzureProvider**.

Pour PowerShell 1.0 en version préliminaire, utilisez **Get-AzureRmResourceProvider**.

    PS C:\> Get-AzureRmResourceProvider -ListAvailable

    ProviderNamespace               RegistrationState ResourceTypes
    -----------------               ----------------- -------------
    Microsoft.ApiManagement         Unregistered      {service, validateServiceName, checkServiceNameAvailability}
    Microsoft.AppService            Registered        {apiapps, appIdentities, gateways, deploymenttemplates...}
    Microsoft.Batch                 Registered        {batchAccounts}

Pour inscrire un fournisseur, utilisez **Register-AzureRmResourceProvider**.

### Interface de ligne de commande Azure

Pour voir si le fournisseur est enregistré pour être utilisé à l'aide de l'interface de ligne de commande Azure, utilisez la commande `azure provider list` (l'exemple de sortie ci-dessous est tronqué).

        azure provider list
        info:    Executing command provider list
        + Getting ARM registered providers
        data:    Namespace                        Registered
        data:    -------------------------------  -------------
        data:    Microsoft.Compute                Registered
        data:    Microsoft.Network                Registered  
        data:    Microsoft.Storage                Registered
        data:    microsoft.visualstudio           Registered
        data:    Microsoft.Authorization          Registered
        data:    Microsoft.Automation             NotRegistered
        data:    Microsoft.Backup                 NotRegistered
        data:    Microsoft.BizTalkServices        NotRegistered
        data:    Microsoft.Features               Registered
        data:    Microsoft.Search                 NotRegistered
        data:    Microsoft.ServiceBus             NotRegistered
        data:    Microsoft.Sql                    Registered
        info:    provider list command OK

Là encore, si vous souhaitez plus d’informations sur les fournisseurs, notamment sur leur disponibilité régionale, entrez `azure provider list --json`. Les éléments suivants sélectionnent uniquement le premier élément de la liste à afficher :

        azure provider list --json | jq '.[0]'
        {
          "resourceTypes": [
            {
              "apiVersions": [
                "2014-02-14"
              ],
              "locations": [
                "North Central US",
                "East US",
                "West US",
                "North Europe",
                "West Europe",
                "East Asia"
              ],
              "properties": {},
              "name": "service"
            }
          ],
          "id": "/subscriptions/<guid>/providers/Microsoft.ApiManagement",
          "namespace": "Microsoft.ApiManagement",
          "registrationState": "Registered"
        }

Si un fournisseur doit être enregistré, utilisez la commande `azure provider register <namespace>`, où la valeur *namespace* provient de la liste précédente.

### API REST

Pour afficher l'état de l'inscription, consultez [Obtention d'informations sur un fournisseur de ressources](https://msdn.microsoft.com/library/azure/dn790534.aspx).

Pour inscrire un fournisseur, consultez [Inscription d'un abonnement auprès d'un fournisseur de ressources](https://msdn.microsoft.com/library/azure/dn790548.aspx).


## Présentation des raisons d’un déploiement réussi pour des modèles personnalisés

Si vous utilisez des modèles que vous avez créés, il est important de comprendre que le système Azure Resource Manager signale la réussite d’un déploiement en cas de retour réussi de tous les fournisseurs par le déploiement. Cela signifie que tous vos éléments de modèle ont été déployés pour votre utilisation.

Toutefois, cela ne signifie pas nécessairement que votre groupe de ressources est « actif et prêt pour vos utilisateurs ». Par exemple, la plupart des déploiements demandent le téléchargement de certaines mises à niveau, l'attente pour d'autres, des ressources sans modèle, ou encore l'installation de scripts complexes ou d'une autre activité exécutable qu'Azure ne connaît pas, car il ne s'agit pas d'une activité suivie par un fournisseur. Dans ce cas, cela peut prendre un moment avant que vos ressources soient prêtes pour une utilisation concrète. Par conséquent, il est normal que l'état du déploiement indique la réussite un certain temps avant que votre déploiement puisse être utilisé.

Vous pouvez toutefois empêcher Azure de signaler la réussite d'un déploiement en créant un script personnalisé pour votre modèle personnalisé (à l'aide de l'extension [CustomScriptExtension](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/), par exemple) qui sait comment détecter la disponibilité pour tout le système par une surveillance de l'ensemble du déploiement et qui se termine correctement uniquement lorsque les utilisateurs peuvent interagir avec la totalité du déploiement. Si vous voulez vous assurer que votre extension est la dernière à s'exécuter, utilisez la propriété **dependsOn** dans votre modèle. Un exemple s’affiche lors de la [Création de modèles de déploiement](https://msdn.microsoft.com/library/azure/dn790564.aspx).

## Outils utiles pour interagir avec Azure
Lorsque vous travaillez avec vos ressources Azure à partir de la ligne de commande, vous collectez les outils qui vous aideront à exécuter votre tâche. Les modèles de groupe de ressources Azure sont des documents JSON, et l’API Azure Resource Manager accepte et retourne des données en JSON. Par conséquent, les outils d’analyse JSON sont l’un des premiers éléments que vous utiliserez pour parcourir des informations relatives à vos ressources, ainsi que pour concevoir des modèles et fichiers de paramètres de modèle ou interagir avec ces modèles et fichiers.

### Outils Mac, Linux et Windows
Si vous utilisez l'interface de ligne de commande Azure pour Mac, Linux et Windows, vous êtes probablement familiarisé avec les outils de téléchargement standard tels que **[curl](http://curl.haxx.se/)** et **[wget](https://www.gnu.org/software/wget/)**, ou **[Resty](https://github.com/beders/Resty)**, et les utilitaires JSON tels que **[jq](http://stedolan.github.io/jq/download/)**, **[jsawk](https://github.com/micha/jsawk)**, et les bibliothèques de langue qui traitent JSON. (Beaucoup de ces outils disposent également de ports pour Windows, tels que [wget](http://gnuwin32.sourceforge.net/packages/wget.htm) ; en fait, il existe plusieurs manières d’obtenir des outils Linux et d’autres outils logiciels open source fonctionnant également sur Windows.)

Cette rubrique inclut certaines commandes d'interface de ligne de commande Azure que vous pouvez utiliser avec **jq** pour obtenir précisément les informations souhaitées avec plus d'efficacité. Vous devez choisir l’ensemble d’outils que vous maîtrisez pour mieux comprendre l’utilisation des ressources Azure.

### PowerShell

PowerShell possède plusieurs commandes de base permettant d'effectuer les mêmes procédures.

- Utilisez l'applet de commande **[Invoke-WebRequest](https://technet.microsoft.com/library/hh849901%28v=wps.640%29)** pour télécharger des fichiers tels que des modèles de groupe de ressources ou des fichiers JSON de paramètres.
- Utilisez l'applet de commande **[ConvertFrom-Json](https://technet.microsoft.com/library/hh849898%28v=wps.640%29.aspx)** pour convertir une chaîne JSON en un objet personnalisé ([PSCustomObject](https://msdn.microsoft.com/library/windows/desktop/system.management.automation.pscustomobject%28v=vs.85%29.aspx)) qui possède une propriété pour chaque champ de la chaîne JSON.


## Étapes suivantes

Pour maîtriser la création de modèles, lisez le document [Création de modèles Azure Resource Manager](../resource-group-authoring-templates.md), et parcourez le [référentiel de modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates) pour obtenir des exemples pouvant être déployés. Un exemple de la propriété **dependsOn** est [Créer une machine virtuelle avec plusieurs cartes d'interface réseau et accessible par RDP](https://github.com/Azure/azure-quickstart-templates/tree/master/201-1-vm-loadbalancer-2-nics).

<!--Image references-->

<!--Reference style links - using these makes the source content way more readable than using inline links-->

<!---HONumber=AcomDC_0107_2016-->
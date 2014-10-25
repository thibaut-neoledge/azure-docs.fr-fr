<properties pageTitle="Using Windows PowerShell with Resource Manager" metaKeywords="ResourceManager, PowerShell, Azure PowerShell" description="Use Windows PowerShell to create a resource group" metaCanonical="" services="" documentationCenter="" title="Using Windows PowerShell with Resource Manager" authors="stevenka; juneb" solutions="" manager="stevenka" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="powershell" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="stevenka; juneb"></tags>

# Utilisation de Windows PowerShell avec le gestionnaire des ressources

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/powershell-azure-resource-manager.md" title="Windows PowerShell" class="current">Windows PowerShell</a><a href="/fr-fr/documentation/articles/xplat-cli-azure-resource-manager.md" title="Interface de ligne de commande interplateforme">Interface de ligne de commande interplateforme</a></div>

Le gestionnaire des ressources contribue à initier une réflexion entièrement nouvelle sur vos ressources Azure. Au lieu de créer et de gérer des ressources à un niveau individuel, commencez à imaginer un service complexe, tel qu'un blog, une galerie de photos, un portail SharePoint ou un wiki. Vous utilisez un modèle (modèle de ressource du service) pour créer un groupe de ressources avec les ressources dont vous avez besoin pour prendre en charge le service. Puis, vous gérez et déployez ce groupe de ressources comme une unité logique.

Ce didacticiel vous apprend à utiliser Windows PowerShell avec le gestionnaire des ressources de Microsoft Azure. Il vous guide dans le processus de création et de déploiement d'un groupe de ressources pour un site web (ou une application web) hébergé sur Azure avec une base de données SQL et l'ensemble des ressources nécessaires.

**Durée de réalisation estimée :** 15 minutes

## Conditions préalables

Pour utiliser Windows PowerShell avec le gestionnaire des ressources, vous devez disposer des composants suivants :

-   Windows PowerShell, version 3.0 ou 4.0. Pour rechercher la version de Windows PowerShell, tapez `$PSVersionTable` et vérifiez que la valeur de `PSVersion` est 3.0 ou 4.0. Pour installer une version compatible, consultez les pages [Windows Management Framework 3.0][] ou [Windows Management Framework 4.0][].

-   Azure PowerShell, version 0.8.0 ou ultérieure. Pour installer la dernière version et l'associer à votre abonnement Azure, accédez à la page [Installation et configuration d'Azure PowerShell][].

Ce didacticiel s'adresse aux utilisateurs novices de Windows PowerShell, mais il part du principe que vous comprenez les concepts fondamentaux (modules, cmdlets et sessions). Pour plus d'informations sur Windows PowerShell, consultez la page [Prise en main de Windows PowerShell][].

Pour accéder à l'aide détaillée de toute cmdlet présentée dans ce didacticiel, utilisez la cmdlet Get-Help.

    Get-Help <cmdlet-name> -Detailed

Par exemple, pour obtenir de l'aide sur la cmdlet Add-AzureAccount, tapez :

    Get-Help Add-AzureAccount -Detailed

## Dans ce didacticiel

-   [À propos des modules Azure Powershell][]
-   [Création d'un groupe de ressources][]
-   [Gestion d'un groupe de ressources][]
-   [Résolution des problèmes affectant un groupe de ressources][]
-   [Étapes suivantes][]

## <span id="about"></span></a>À propos des modules Azure PowerShell

Avec la version 0.8.0, l'installation d'Azure PowerShell inclut trois modules Windows PowerShell :

-   **Azure** : comprend les applets de commande traditionnelles de gestion de ressources spécifiques (comptes de stockage, sites web, bases de données, ordinateurs virtuels et services de média). Pour plus d'informations, consultez la page [Cmdlets de gestion des services Azure][].

-   **AzureResourceManager** : comprend les cmdlets de création, de gestion et de déploiement des ressources Azure pour un groupe de ressources. Pour plus d'informations, consultez la page [Cmdlets du gestionnaire des ressources Azure][].

-   **AzureProfile** : comprend les cmdlets communes aux deux modules, comme Add-AzureAccount, Get-AzureSubscription et Switch-AzureMode. Pour plus d’informations, consultez la page [Cmdlets de profil Azure][].

> [ WACOM.NOTE] Le module Azure Resource Manager est actuellement disponible dans une version préliminaire. Il peut ne pas fournir les mêmes fonctionnalités de gestion que le module Azure.

Les modules Azure et Azure Resource Manager ne sont pas conçus pour être utilisés dans la même session Windows PowerShell. Pour passer facilement de l'un à l'autre, nous avons ajouté une nouvelle cmdlet, **Switch-AzureMode**, au module Azure Profile.

Si vous utilisez Azure PowerShell, les applets de commande du module Azure sont importées par défaut. Pour passer au module Azure Resource Manager, utilisez la cmdlet Switch-AzureMode. Elle retire le module Azure de votre session et importe les modules Azure Resource Manager et Azure Profile.

Pour activer le module AzureResoureManager, tapez :

    PS C:\> Switch-AzureMode -Name AzureResourceManager

Pour revenir au module Azure, tapez :

    PS C:\> Switch-AzureMode -Name AzureServiceManagement

Par défaut, Switch-AzureMode affecte uniquement la session en cours. Pour rendre effectif ce basculement dans toutes les sessions Windows PowerShell, utilisez le paramètre **Global** de Switch-AzureMode.

Pour obtenir de l’aide sur la cmdlet Switch-AzureMode, tapez : `Get-Help Switch-AzureMode` ou consultez la page [Switch-AzureMode][].

Pour obtenir une liste des cmdlets dans le module AzureResourceManager avec un résumé de la rubrique d'aide, tapez :

    PS C:\> Get-Command -Module AzureResourceManager | Get-Help | Format-Table Name, Synopsis

    Name                                   Synopsis
    ----                                   --------
    Get-AzureLocation                      Gets the Azure data center locations and the resource types that they support
    Get-AzureResource                      Gets Azure resources
    Get-AzureResourceGroup                 Gets Azure resource groups
    Get-AzureResourceGroupDeployment       Gets the deployments in a resource group.
    Get-AzureResourceGroupGalleryTemplate  Gets resource group templates in the gallery
    Get-AzureResourceGroupLog              Gets the deployment log for a resource group
    New-AzureResource                      Creates a new resource in a resource group
    New-AzureResourceGroup                 Creates an Azure resource group and its resources
    New-AzureResourceGroupDeployment       Add an Azure deployment to a resource group.
    Remove-AzureResource                   Deletes a resource
    Remove-AzureResourceGroup              Deletes a resource group.
    Save-AzureResourceGroupGalleryTemplate Saves a gallery template to a JSON file
    Set-AzureResource                      Changes the properties of an Azure resource.
    Stop-AzureResourceGroupDeployment      Cancels a resource group deployment
    Test-AzureResourceGroupTemplate        Detects errors in a resource group template or template parameters

Pour obtenir une aide complète sur une cmdlet, entrez une commande avec la syntaxe suivante :

    Get-Help <cmdlet-name> -Full

Par exemple :

    Get-Help Get-AzureLocation -Full

# <span id="create"></span></a> Créer un groupe de ressources

Cette section du didacticiel vous guide tout au long du processus de création et de déploiement d'un groupe de ressources pour un site web avec une base de données SQL.

Vous n'avez pas besoin d'être un expert en matière d'Azure, de SQL, de sites web ou de gestion des ressources pour effectuer cette tâche. Les modèles fournissent un modèle de groupe de ressources avec l'ensemble des ressources dont vous pouvez avoir besoin. Et du fait que nous utilisons Windows PowerShell pour automatiser les tâches, vous pouvez utiliser ces processus dans le cadre d'un modèle pour le scripting de tâches à grande échelle.

## Étape 1 : activation d'Azure Resource Manager

1.  Lancez Windows PowerShell. Vous pouvez utiliser n'importe quel programme hôte de votre choix, tel que la console Windows PowerShell ou Windows PowerShell ISE.

2.  Utilisez la cmdlet **Switch-AzureMode** pour importer les cmdlets dans les modules AzureResourceManager et AzureProfile.

    `PS C:\>Switch-AzureMode AzureResourceManager`</code>

3.  Pour ajouter votre compte Azure à la session Windows PowerShell, utilisez la cmdlet **Add-AzureAccount**.

    `PS C:\> Add-AzureAccount`</code>

La cmdlet vous invite à entrer une adresse e-mail et un mot de passe. Elle télécharge ensuite vos paramètres de compte afin qu'ils soient reconnus par Windows PowerShell.

Un délai d'expiration est associé à ces paramètres ; il est donc nécessaire de les actualiser ponctuellement. Pour actualiser les paramètres de compte, réexécutez **Add-AzureAccount**.

> [ WACOM.NOTE] Le module AzureResourceManager nécessite Add-AzureAccount. L'utilisation d'un fichier de paramètres de publication est insuffisante.

## Étape 2 : choix d'un modèle de galerie

Il existe plusieurs façons de créer un groupe de ressources et les ressources correspondantes, mais la méthode la plus simple consiste à utiliser un modèle de groupe. Un *modèle de groupe de ressources* est une chaîne JSON qui définit les ressources d'un groupe de ressources. Cette chaîne inclut des espaces réservés appelés paramètres pour les valeurs définies par l'utilisateur, telles que les noms et les tailles.

Azure héberge une galerie de modèles de groupe de ressources. Vous pouvez aussi créer vos propres modèles, à partir de zéro ou en adaptant simplement des modèles existants de la galerie. Dans ce didacticiel, nous utilisons un modèle de la galerie.

Pour rechercher un modèle dans la galerie de modèles de groupe de ressources Azure, utilisez la cmdlet **Get-AzureResourceGroupGalleryTemplate**.

À l'invite Windows Powershell, tapez :

    PS C:\> Get-AzureResourceGroupGalleryTemplate

La cmdlet renvoie une liste de modèles de la galerie avec les propriétés Publisher et Identity. L'utilisation de la propriété **Identity** permet d'identifier le modèle dans les commandes.

    Publisher       Identity
    ---------       --------
    Ghost           Ghost.Ghost.0.1.0-preview1
    Joomla          Joomla.Joomla.0.1.0-preview1
    Microsoft       Microsoft.ASPNETEmptySite.0.1.0-preview1
    Microsoft       Microsoft.ASPNETstarterSite.0.1.0-preview1
    Microsoft       Microsoft.Bakery.0.1.0-preview1
    Microsoft       Microsoft.Boilerplate.0.1.0-preview1
    ...

CONSEIL : pour rappeler la dernière commande, appuyez sur la touche fléchée vers le haut.

Le modèle Microsoft.WebSiteSQLDatabase.0.1.0-preview1 constitue une approche intéressante. Pour obtenir plus d’informations sur un modèle de la galerie, utilisez le paramètre **Identity**. La valeur du paramètre Identity est l'identité du modèle.

    PS C:\> Get-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.1.0-preview1

La cmdlet renvoie un objet contenant des informations beaucoup plus détaillées (avec un descriptif, notamment) sur le modèle.

    <p>Windows Azure Websites offers secure and flexible development, 
    deployment and scaling options for any sized web application. Leverage 
    your existing tools to create and deploy applications without the hassle 
    of managing infrastructure.</p>

Ce modèle semble répondre à nos besoins. Enregistrons-le sur le disque et examinons-le plus en détail.

## Étape 3 : Examen du modèle

Enregistrons le modèle dans un fichier JSON sur le disque. Cette étape n'est pas obligatoire, mais elle simplifie la compréhension du modèle. Pour enregistrer le modèle, utilisez la cmdlet **Save-AzureResourceGroupGalleryTemplate**. Utilisez son paramètre **Identity** pour spécifier le modèle et le paramètre **Path** pour définir un chemin sur le disque.

Save-AzureResourceGroupGalleryTemplate enregistre le modèle et renvoie un nom de fichier du fichier de modèle JSON.

    PS C:\> Save-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 -Path D:\Azure\Templates

    Path
    ----
    D:\Azure\Templates\Microsoft.WebSite.0.1.0-preview1.json

Vous pouvez ouvrir le fichier de modèle avec un éditeur de texte tel que Notepad. Chaque modèle dispose d'une section **resources** et d'une section **parameters**.

La section **resources** du modèle répertorie les ressources que le modèle crée. Ce modèle crée un serveur de base de données SQL et une base de données SQL, une batterie de serveurs et un site web, ainsi que plusieurs paramètres de gestion.

La définition de chaque ressource inclut les propriétés correspondantes, telles que le nom, le type et l'emplacement, ainsi que les paramètres pour les valeurs définies par l'utilisateur. Par exemple, cette section du modèle définit la base de données SQL. Elle inclut les paramètres associés au nom de la base de données ([parameters('databaseName')]), l'emplacement du serveur de base de données [parameters('serverLocation')] et la propriété collation [parameters('collation')].

        {
          "name": "[parameters('databaseName')]",
          "type": "databases",
          "location": "[parameters('serverLocation')]",
          "apiVersion": "2.0",
          "dependsOn": [
            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
          ],
          "properties": {
            "edition": "Web",
            "collation": "[parameters('collation')]",
            "maxSizeBytes": "1073741824"
          }
        },

La section **parameters** du modèle est un ensemble de paramètres qui sont définis dans toutes les ressources. Elle inclut les propriétés databaseName, serverLocation et collation.

    "parameters": {
    ...    

    "serverLocation": {
      "type": "string"
    }, 
    ...

    "databaseName": {
      "type": "string"
    },
    "collation": {
      "type": "string",
      "defaultValue": "SQL_Latin1_General_CP1_CI_AS"
    }

Certains paramètres ont une valeur par défaut. Si vous utilisez le modèle, il n'est pas obligatoire de spécifier des valeurs pour ces paramètres. Si vous ne spécifiez pas de valeur, la valeur par défaut est utilisée.

    "collation": {
          "type": "string",
          "defaultValue": "SQL_Latin1_General_CP1_CI_AS"
        }

Pour les paramètres associées à une liste de valeurs, les valeurs valides sont énumérées avec le paramètre. Par exemple, le paramètre **sku** peut prendre les valeurs Gratuit, Partagé, De base ou Standard. Si aucune valeur n'est spécifiée pour ce paramètre **sku**, il utilise Gratuit, la valeur par défaut.

    "sku": {
      "type": "string",
      "allowedValues": [
        "Free",
        "Shared",
        "Basic",
        "Standard"
      ],
      "defaultValue": "Free"
    },

Notez que le paramètre **administratorLoginPassword** utilise une chaîne sécurisée, et non pas du texte brut. Si vous fournissez une valeur pour une chaîne sécurisée, celle-ci est masquée.

    "administratorLoginPassword": {
      "type": "securestring"
    },

Nous sommes presque prêts à utiliser le modèle, mais avant cela, il est nécessaire de trouver des emplacements pour chacune de ces ressources.

## Étape 4 : récupération des emplacements correspondant aux types de ressources

Dans la plupart des modèles, il vous est demandé de spécifier un emplacement pour chaque ressource d'un groupe de ressources. Chaque ressource est située dans un centre de données Azure, mais certains centres de données Azure ne prennent pas en charge tous les types de ressources.

Sélectionnez n'importe quel emplacement compatible. Les ressources d'un groupe de ressources ne doivent pas forcément se trouver dans le même emplacement, et il n'est pas nécessaire non plus qu'elles soient là où se trouve le groupe de ressources ou l'abonnement.

Pour obtenir les emplacements compatibles avec chaque type de ressource, utilisez la cmdlet **Get-AzureLocation**. Voici un extrait de la sortie. (Cette sortie peut être différente de la vôtre. Les détails sont susceptibles d'évoluer au fil du temps.)

    Name                                 Locations
    ----                                 ---------
    ResourceGroup                        East Asia, South East Asia, East US, West US, North Central US,
                                         South Central US, Central US, North Europe, West Europe

    Microsoft.Sql/servers/databases      Brazil South, Central US, East Asia, East US, East US 2, Japan
                                         East, Japan West, North Central US, North Europe, South Central US,
                                         Southeast Asia, West Europe, West US

    Microsoft.Web/serverFarms            Brazil South, East Asia, East US, Japan East, Japan West, North
                                         Central US, North Europe, West Europe, West US

    Microsoft.Web/sites                  Brazil South, East Asia, East US, Japan East, Japan West, North
                                         Central US, North Europe, West Europe, West US

Nous disposons maintenant des informations dont nous avons besoin pour créer le groupe de ressources.

## Étape 5 : création d'un groupe de ressources

Au cours de cette étape, nous allons utiliser le modèle de groupe de ressources pour créer le groupe de ressources. Pour référence, ouvrez le fichier JSON Microsoft.WebSiteSQLDatabase.0.1.0-preview1 et suivez la procédure.

Pour créer un groupe de ressources, utilisez la cmdlet **New-AzureResourceGroup**.

La commande utilise le paramètre **Name** pour attribuer un nom au groupe de ressources et le paramètre **Location** pour indiquer son emplacement. Utilisez la sortie de **Get-AzureLocation** pour sélectionner un emplacement pour le groupe de ressources. Elle utilise le paramètre **GalleryTemplateIdentity** pour spécifier le modèle de la galerie.

    PS C:\> New-AzureResourceGroup ` 
            -Name TestRG1 `
            -Location "East Asia" `
            -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 `
            ....

Dès que vous tapez le nom du modèle, New-AzureResourceGroup récupère le modèle, l'analyse et ajoute les paramètres du modèle à la commande de façon dynamique. Il est ainsi beaucoup plus facile de spécifier les valeurs des paramètres du modèle. En outre, si vous oubliez une valeur obligatoire, Windows PowerShell vous invite à spécifier cette valeur.

**Paramètres de modèle dynamiques**

Pour obtenir les paramètres, tapez un signe moins (-) pour indiquer un nom de paramètre, puis appuyez sur la touche de tabulation. Ou bien tapez les premières lettres d'un nom de paramètre tel que siteName, puis appuyez sur la touche de tabulation.

        PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
        -si<TAB>

Windows PowerShell complète le nom du paramètre. Pour parcourir les noms des paramètres, appuyez plusieurs fois sur la touche de tabulation.

        PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
        -siteName 

Entrez un nom pour le site web et recommencez à appuyer sur la touche de tabulation pour chacun des paramètres. Les paramètres dotés d'une valeur par défaut sont facultatifs. Pour accepter la valeur par défaut, omettez le paramètre de la commande.

Lorsqu'un paramètre du modèle est associé à une liste de valeurs, comme le paramètre sku dans ce modèle, appuyez sur la touche de tabulation pour parcourir les valeurs du paramètre.

        PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
        -siteName TestSite -sku <TAB>

        PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
        -siteName TestSite -sku Free<TAB>

        PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
        -siteName TestSite -sku Basic<TAB>

Voici un exemple de commande New-AzureResourceGroup qui indique uniquement les paramètres du modèle requis et le paramètre courant **Verbose**. Notez que **administratorLoginPassword** est omis. (Le guillemet simple ouvrant (\`) est le caractère de continuation de ligne dans Windows PowerShell.)

    PS C:\> New-AzureResourceGroup 
    -Name TestRG `
    -Location "East Asia" `
    -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 `
    -siteName TestSite `
    -hostingPlanName TestPlan `
    -siteLocation "North Europe" `
    -serverName testserver `
    -serverLocation "West US" `
    -administratorLogin Admin01 `
    -databaseName TestDB `
    -Verbose

Au moment d'entrer la commande, vous êtes invité à saisir le paramètre obligatoire manquant, **administratorLoginPassword**. Puis, lorsque vous tapez le mot de passe, la valeur de la chaîne sécurisée est masquée. Cette stratégie évite le risque de fournir un mot de passe en clair.

    cmdlet New-AzureResourceGroup at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    administratorLoginPassword: **********

**New-AzureResourcGroup** renvoie le groupe de ressources qu'il a créé et déployé. Voici le résultat de la commande, avec une sortie détaillée.

    VERBOSE: 3:47:30 PM - Create resource group 'TestRG' in location 'East Asia'
    VERBOSE: 3:47:30 PM - Template is valid.
    VERBOSE: 3:47:31 PM - Create template deployment 'Microsoft.WebSiteSQLDatabase.0.1.0-preview1'
    using template https://gallerystoreprodch.blob.core.windows.net/prod-microsoft-windowsazure-gallery/8D6B920B-10F4-4B5A-B3DA-9D398FBCF3EE.PUBLICGALLERYITEMS.MICROSOFT.WEBSITESQLDATABASE.0.1.0-PREVIEW1/DeploymentTemplates/Website_NewHostingPlan_SQL_NewDB-Default.json.
    VERBOSE: 3:47:43 PM - Resource Microsoft.Sql/servers 'testserver' provisioning status is succeeded
    VERBOSE: 3:47:43 PM - Resource Microsoft.Web/serverFarms 'TestPlan' provisioning status is
    succeeded
    VERBOSE: 3:47:47 PM - Resource Microsoft.Sql/servers/databases 'testserver/TestDB' provisioning status is succeeded
    VERBOSE: 3:47:47 PM - Resource microsoft.insights/autoscalesettings 'TestPlan-TestRG'
    provisioning status is succeeded
    VERBOSE: 3:47:47 PM - Resource Microsoft.Sql/servers/firewallrules
    'testserver/AllowAllWindowsAzureIps' provisioning status is succeeded
    VERBOSE: 3:47:50 PM - Resource Microsoft.Web/Sites 'TestSite' provisioning status is succeeded
    VERBOSE: 3:47:54 PM - Resource Microsoft.Web/Sites/config 'TestSite/web' provisioning status is succeeded
    VERBOSE: 3:47:54 PM - Resource microsoft.insights/alertrules 'ServerErrors-TestSite' provisioning
    status is succeeded
    VERBOSE: 3:47:57 PM - Resource microsoft.insights/components 'TestSite' provisioning status is succeeded


    ResourceGroupName : TestRG
    Location          : eastasia
    ProvisioningState : Succeeded
    Resources         :
                    Name                   Type                                  Location
                    =====================  ====================================  =========
                    ServerErrors-TestSite  microsoft.insights/alertrules         eastus
                    TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
                    TestSite               microsoft.insights/components         centralus
                    testserver             Microsoft.Sql/servers                 westus
                    TestDB                 Microsoft.Sql/servers/databases       westus
                    TestPlan               Microsoft.Web/serverFarms             westus
                    TestSite               Microsoft.Web/sites                   westus

En seulement quelques étapes, nous avons créé et déployé les ressources requises pour un site web complexe.
Le modèle de la galerie a fourni presque toutes les informations dont nous avions besoin pour effectuer cette tâche.

# <span id="manage"></span></a> Gestion d'un groupe de ressources

Après avoir créé un groupe de ressources, vous pouvez utiliser les cmdlets dans le module AzureResourceManager pour gérer le groupe de ressources, le modifier, y ajouter des ressources et le supprimer.

-   Pour obtenir les groupes de ressources dans votre abonnement, utilisez la cmdlet **Get-AzureResourceGroup** :

        PS C:>Get-AzureResourceGroup

        ResourceGroupName : TestRG
        Location          : eastasia
        ProvisioningState : Succeeded
        Resources         :
                        Name                   Type                                  Location
                        =====================  ====================================  =========
                        ServerErrors-TestSite  microsoft.insights/alertrules         eastus
                        TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
                        TestSite               microsoft.insights/components         centralus
                        testserver             Microsoft.Sql/servers                 westus
                        TestDB                 Microsoft.Sql/servers/databases       westus
                        TestPlan               Microsoft.Web/serverFarms             westus
                        TestSite               Microsoft.Web/sites                   westus

-   Pour obtenir les ressources dans le groupe de ressources, utilisez la cmdlet **GetAzureResource** et son paramètre ResourceGroupName. Sans paramètres, Get-AzureResource récupère l'ensemble des ressources de votre abonnement Azure.

        PS C:\> Get-AzureResource -ResourceGroupName TestRG

        Name                   ResourceType                          Location
        ----                   ------------                          --------
        ServerErrors-TestSite  microsoft.insights/alertrules         eastus
        TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
        TestSite               microsoft.insights/components         centralus
        testserver             Microsoft.Sql/servers                 westus
        TestDB                 Microsoft.Sql/servers/databases       westus
        TestPlan               Microsoft.Web/serverFarms             westus
        TestSite               Microsoft.Web/sites                   westus

-   Pour ajouter une ressource au groupe de ressources, utilisez la cmdlet **New-AzureResourceGroup**. Cette commande ajoute un nouveau site web au groupe de ressources TestRG. Cette commande est un peu plus complexe, car elle n'utilise pas de modèle.

        PS C:\>New-AzureResource -Name TestSite2 `
        -Location "North Europe" `
        -ResourceGroupName TestRG `
        -ResourceType "Microsoft.Web/sites" `
        -ApiVersion 2004-04-01 `
        -PropertyObject @{"name" = "TestSite2"; "siteMode"= "Limited"; "computeMode" = "Shared"}

-   Pour ajouter un nouveau déploiement basé sur un modèle au groupe de ressources, utilisez la commande **New-AzureResourceGroupDeployment**.

        PS C:\>New-AzureResourceGroupDeployment ` 
        -ResourceGroupName TestRG `
        -GalleryTemplateIdentity Microsoft.WebSite.0.1.0-preview1 `
        -siteName TestWeb2 `
        -hostingPlanName TestDeploy2 `
        -siteMode Limited `
        -computeMode Dedicated `
        -siteLocation "North Europe" `
        -subscriptionID "9b14a38b-4b93-4554-8bb0-3cefb47abcde" `
        -resourceGroup TestRG

-   Pour supprimer une ressource du groupe de ressources, utilisez la cmdlet **Remove-AzureResource**. Cette cmdlet supprime la ressource, mais pas le groupe de ressources.

    Cette commande supprime le site web TestSite2 du groupe de ressources TestRG.

        Remove-AzureResource -Name TestSite2 `
            -Location "North Europe" `
            -ResourceGroupName TestRG `
            -ResourceType "Microsoft.Web/sites" `
            -ApiVersion 2004-04-01

-   Pour supprimer un groupe de ressources, utilisez la cmdlet **Remove-AzureResourceGroup**. Cette cmdlet supprime le groupe de ressources et ses ressources.

        PS C:\ps-test> Remove-AzureResourceGroup -Name TestRG

        Confirm
        Are you sure you want to remove resource group 'TestRG'
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

# <span id="troubleshoot"></span></a> Résolution des problèmes affectant un groupe de ressources

En testant les cmdlets dans les modules AzureResourceManager, vous êtes susceptible de rencontrer des erreurs. Aidez-vous des conseils figurant dans cette section pour les résoudre.

## Prévention des erreurs

Le module AzureResourceManager inclut des cmdlets vous permettant d'éviter les erreurs.

-   **Get-AzureLocation** : cette cmdlet récupère les emplacements qui prennent en charge chaque type de ressource. Avant d'entrer un emplacement pour une ressource, utilisez cette cmdlet pour vérifier que l'emplacement est compatible avec le type de ressource concerné.

-   **Test-AzureResourceGroupTemplate** : testez votre modèle et le paramètre du modèle avant de les utiliser. Entrez un modèle personnalisé ou de la galerie et les valeurs de paramètres du modèle que vous prévoyez d'utiliser. Cette cmdlet teste si le modèle est intrinsèquement cohérent et si la valeur que vous avez définie correspond au modèle.

## Correction des erreurs

-   **Get-AzureResourceGroupLog** : cette cmdlet récupère les entrées dans le journal pour chaque déploiement du groupe de ressources. Si une erreur survient, commencez par examiner les journaux des déploiements.

-   **Sortie détaillée et débogage** : les cmdlets du module AzureResourceManager appellent les API REST qui se chargent d'agir. Pour voir les messages renvoyés par les API, définissez la variable sur Continue et ajoutez le paramètre courant Verbose dans vos commandes. Souvent, ces messages fournissent des indices essentiels sur les causes d'erreurs.

-   **Vos informations d'identification Azure n'ont pas été configurées ou ont expiré** : pour actualiser les informations d'identification dans votre session Windows PowerShell, utilisez la cmdlet Add-AzureAccount. Les informations d'identification que contient un fichier de paramètres de publication ne sont pas suffisantes pour les cmdlets du module AzureResourceManager.

# <span id="next"></span></a>Étapes suivantes

Pour en savoir plus sur l'utilisation de Windows PowerShell avec le gestionnaire des ressources, reportez-vous aux sources d'informations suivantes :

-   [Cmdlets Azure Resource Manager][] : découvrez comment utiliser les applets de commande dans le module AzureResourceManager.
-   [Utilisation des groupes de ressources pour gérer vos ressources Azure][] : découvrez comment créer et gérer des groupes de ressources dans le portail de gestion Azure.
-   [Utilisation de l'interface de ligne de commande interplateforme Azure avec le gestionnaire des ressources][] : découvrez comment créer et gérer des groupes de ressources avec des outils de commande en ligne compatibles avec de nombreuses plateformes de système d'exploitation.
-   [Blog Azure][] : découvrez les nouvelles fonctionnalités d'Azure.
-   [Blog Windows PowerShell][] : découvrez les nouvelles fonctionnalités de Windows PowerShell.
-   [Blog Hey, Scripting Guy!][] : bénéficiez des conseils et astuces de la communauté Windows PowerShell.

  [Windows PowerShell]: /fr-fr/documentation/articles/powershell-azure-resource-manager.md "Windows PowerShell"
  [Interface de ligne de commande interplateforme]: /fr-fr/documentation/articles/xplat-cli-azure-resource-manager.md "Interface de ligne de commande interplateforme"
  [Windows Management Framework 3.0]: http://www.microsoft.com/fr-fr/download/details.aspx?id=34595
  [Windows Management Framework 4.0]: http://www.microsoft.com/fr-fr/download/details.aspx?id=40855
  [Installation et configuration d'Azure PowerShell]: http://www.windowsazure.com/fr-fr/documentation/articles/install-configure-powershell/
  [Prise en main de Windows PowerShell]: http://technet.microsoft.com/fr-fr/library/hh857337.aspx
  [À propos des modules Azure Powershell]: #about
  [Création d'un groupe de ressources]: #create
  [Gestion d'un groupe de ressources]: #manage
  [Résolution des problèmes affectant un groupe de ressources]: #troubleshoot
  [Étapes suivantes]: #next
  [Cmdlets de gestion des services Azure]: http://msdn.microsoft.com/fr-fr/library/jj152841.aspx
  [Cmdlets du gestionnaire des ressources Azure]: http://go.microsoft.com/fwlink/?LinkID=394765
  [Cmdlets de profil Azure]: http://go.microsoft.com/fwlink/?LinkID=394766
  [Switch-AzureMode]: http://go.microsoft.com/fwlink/?LinkID=394398
  [Cmdlets Azure Resource Manager]: http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409
  [Utilisation des groupes de ressources pour gérer vos ressources Azure]: http://azure.microsoft.com/fr-fr/documentation/articles/azure-preview-portal-using-resource-groups
  [Utilisation de l'interface de ligne de commande interplateforme Azure avec le gestionnaire des ressources]: http://www.windowsazure.com/fr-fr/documentation/articles/xplat-cli-azure-resource-manager/
  [Blog Azure]: http://blogs.msdn.com/windowsazure
  [Blog Windows PowerShell]: http://blogs.msdn.com/powershell
  [Blog Hey, Scripting Guy!]: http://blogs.technet.com/b/heyscriptingguy/

<properties 
	pageTitle="Utilisation d’Azure PowerShell avec Azure Resource Manager" 
	description="Utilisez Azure PowerShell pour déployer plusieurs ressources sous la forme d’un groupe de ressources dans Azure." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/15/2015" 
	ms.author="tomfitz"/>

# Utilisation d’Azure PowerShell avec Azure Resource Manager

> [AZURE.SELECTOR]
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Azure CLI](xplat-cli-azure-resource-manager.md)

Azure Resource Manager contribue à initier une réflexion entièrement nouvelle concernant vos ressources Azure. Au lieu de créer et de gérer des ressources à un niveau individuel, commencez à imaginer un service complexe, tel qu'un blog, une galerie de photos, un portail SharePoint ou un wiki. Vous utilisez un modèle (modèle de ressource du service) pour créer un groupe de ressources avec les ressources dont vous avez besoin pour prendre en charge le service. Puis, vous gérez et déployez ce groupe de ressources comme une unité logique.

Ce didacticiel vous apprend à utiliser Azure PowerShell avec Azure Resource Manager pour Microsoft Azure. Il vous familiarise avec le processus de création et de déploiement d’un groupe de ressources pour une application web hébergée sur Azure avec une base de données SQL, ainsi qu’avec l’ensemble des ressources nécessaires.

## Composants requis

Pour suivre ce didacticiel, vous devez disposer d’Azure PowerShell version 0.8.0 ou ultérieure. Pour installer la dernière version et l’associer à votre abonnement Azure, consultez la page [Installation et configuration d’Azure PowerShell](powershell-install-configure.md).

Ce didacticiel s’adresse aux utilisateurs novices de PowerShell, mais il repose sur l’hypothèse que vous comprenez les concepts fondamentaux (modules, applets de commande et sessions). Pour plus d'informations sur Windows PowerShell, consultez la page [Prise en main de Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

Pour accéder à l'aide détaillée de toute applet de commande présentée dans ce didacticiel, utilisez l'applet de commande Get-Help.

	Get-Help <cmdlet-name> -Detailed

Par exemple, pour obtenir de l'aide sur la cmdlet Add-AzureAccount, tapez :

	Get-Help Add-AzureAccount -Detailed

## À propos des modules Azure PowerShell
À partir de la version 0.8.0, l’installation d’Azure PowerShell inclut plusieurs modules PowerShell. Vous devez explicitement choisir d’utiliser les commandes disponibles dans le module Azure ou celles du module Azure Resource Manager. Pour passer facilement de l'un à l'autre, nous avons ajouté une nouvelle cmdlet, **Switch-AzureMode**, au module Azure Profile.

Si vous utilisez Azure PowerShell, les applets de commande du module Azure sont importées par défaut. Pour passer au module Azure Resource Manager, utilisez la cmdlet Switch-AzureMode. Elle retire le module Azure de votre session et importe les modules Azure Resource Manager et Azure Profile.

Pour activer le module AzureResoureManager, tapez :

    PS C:\> Switch-AzureMode -Name AzureResourceManager

Pour revenir au module Azure, tapez :

    PS C:\> Switch-AzureMode -Name AzureServiceManagement

Par défaut, Switch-AzureMode affecte uniquement la session en cours. Pour rendre effectif ce basculement dans toutes les sessions PowerShell, utilisez le paramètre **Global** de Switch-AzureMode.

Pour obtenir de l’aide sur l’applet de commande Switch-AzureMode, tapez : `Get-Help Switch-AzureMode` ou consultez [Switch-AzureMode](http://go.microsoft.com/fwlink/?LinkID=394398).
  
Pour obtenir une liste des cmdlets dans le module AzureResourceManager avec un résumé de la rubrique d'aide, tapez :

    PS C:\> Get-Command -Module AzureResourceManager | Get-Help | Format-Table Name, Synopsis

Le résultat ressemble à l’extrait qui suit :

	Name                                   Synopsis
	----                                   --------
	Add-AlertRule                          Adds or updates an alert rule of either metric, event, o...
	Add-AzureAccount                       Adds the Azure account to Windows PowerShell
	Add-AzureEnvironment                   Creates an Azure environment
	Add-AzureKeyVaultKey                   Creates a key in a vault or imports a key into a vault.
        ...

Pour obtenir une aide complète sur une cmdlet, entrez une commande avec la syntaxe suivante :

	Get-Help <cmdlet-name> -Full

Par exemple,

	Get-Help Get-AzureLocation -Full

Pour découvrir l’ensemble des commandes Azure Resource Manager, voir [Applets de commande Azure Resource Manager](http://go.microsoft.com/fwlink/?LinkID=394765).
  
## Créer un groupe de ressources

Cette section du didacticiel vous accompagne tout au long du processus de création et de déploiement d’un groupe de ressources pour une application web avec une base de données SQL.

Il n’est pas nécessaire d’être un expert en matière d’Azure, de SQL, d’applications web ou de gestion des ressources pour effectuer cette tâche. Les modèles fournissent un modèle de groupe de ressources avec l'ensemble des ressources dont vous pouvez avoir besoin. Et du fait que nous utilisons Windows PowerShell pour automatiser les tâches, vous pouvez utiliser ces processus dans le cadre d'un modèle pour le scripting de tâches à grande échelle.

### Étape 1 : basculer vers Azure Resource Manager 
1. Démarrez PowerShell. Vous pouvez utiliser le programme hôte de votre choix, comme la console Azure PowerShell ou Windows PowerShell ISE.

2. Utilisez la cmdlet **Switch-AzureMode** pour importer les cmdlets dans les modules AzureResourceManager et AzureProfile.

        PS C:\> Switch-AzureMode AzureResourceManager

3. Pour ajouter votre compte Azure à la session Windows PowerShell, utilisez la cmdlet **Add-AzureAccount**.

        PS C:\> Add-AzureAccount

Les applets de commande vous invitent à entrer les informations d’identification de connexion pour votre compte Azure. Une fois que vous êtes connecté, l’applet de commande télécharge vos paramètres de compte pour qu’ils soient reconnus par Windows PowerShell.

Un délai d'expiration est associé à ces paramètres ; il est donc nécessaire de les actualiser ponctuellement. Pour actualiser les paramètres de compte, réexécutez **Add-AzureAccount**.

>[AZURE.NOTE]Le module AzureResourceManager nécessite Add-AzureAccount. L'utilisation d'un fichier de paramètres de publication est insuffisante.

### Étape 2 : sélectionner un modèle de galerie

Il existe plusieurs façons de créer un groupe de ressources et les ressources correspondantes, mais la méthode la plus simple consiste à utiliser un modèle de groupe. Un *modèle de groupe de ressources* est une chaîne JSON qui définit les ressources d'un groupe de ressources. Cette chaîne inclut des espaces réservés appelés paramètres pour les valeurs définies par l'utilisateur, telles que les noms et les tailles.

Azure héberge une galerie de modèles de groupe de ressources. Vous pouvez aussi créer vos propres modèles, à partir de zéro ou en adaptant simplement des modèles existants de la galerie. Dans ce didacticiel, nous utilisons un modèle de la galerie.

Pour visualiser tous les modèles de la galerie de modèles de groupe de ressources Azure, utilisez l’applet de commande **Get-AzureResourceGroupGalleryTemplate** ; toutefois, cette commande renvoie un grande nombre de modèles. Pour obtenir un nombre de modèles plus facile à gérer, spécifiez un paramètre d’éditeur.

À l’invite PowerShell, tapez :
    
    PS C:\> Get-AzureResourceGroupGalleryTemplate -Publisher Microsoft

L’applet de commande renvoie une liste de modèles de la galerie présentant Microsoft comme éditeur. L'utilisation de la propriété **Identity** permet d'identifier le modèle dans les commandes.

Le modèle Microsoft.WebSiteSQLDatabase.0.2.6-preview constitue une approche intéressante. Lorsque vous exécutez la commande, la version du modèle peut être légèrement différente, car une nouvelle version a été publiée. Utilisez la dernière version du modèle. Pour obtenir plus d’informations sur un modèle de la galerie, utilisez le paramètre **Identity**. La valeur du paramètre Identity est l'identité du modèle.

    PS C:\> Get-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.2.6-preview

L’applet de commande renvoie un objet contenant des informations beaucoup plus détaillées sur le modèle, notamment un résumé et une description.

Ce modèle semble répondre à nos besoins. Enregistrons-le sur le disque et examinons-le plus en détail.

### Étape 3 : examiner le modèle

Enregistrons le modèle dans un fichier JSON sur le disque. Cette étape n'est pas obligatoire, mais elle simplifie la compréhension du modèle. Pour enregistrer le modèle, utilisez la cmdlet **Save-AzureResourceGroupGalleryTemplate**. Utilisez son paramètre **Identity** pour spécifier le modèle et le paramètre **Path** pour définir un chemin sur le disque.

Save-AzureResourceGroupGalleryTemplate enregistre le modèle et renvoie un nom de fichier du fichier de modèle JSON.

	PS C:\> Save-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.2.6-preview -Path C:\Azure\Templates\New_WebSite_And_Database.json

	Path
	----
	C:\Azure\Templates\New_WebSite_And_Database.json


Vous pouvez ouvrir le fichier de modèle avec un éditeur de texte tel que Notepad. Chaque modèle dispose d’une section **parameters** et d’une section **resources**.

La section **parameters** du modèle est un ensemble de paramètres qui sont définis dans toutes les ressources. Elle inclut les valeurs de propriété que vous pouvez spécifier lorsque vous configurez votre groupe de ressources.

    "parameters": {
      "siteName": {
        "type": "string"
      },
      "hostingPlanName": {
        "type": "string"
      },
      "siteLocation": {
        "type": "string"
      },
      ...
    }

Certains paramètres ont une valeur par défaut. Si vous utilisez le modèle, il n'est pas obligatoire de spécifier des valeurs pour ces paramètres. Si vous ne spécifiez pas de valeur, la valeur par défaut est utilisée.

    "collation": {
      "type": "string",
      "defaultValue": "SQL_Latin1_General_CP1_CI_AS"
    },

Pour les paramètres associés à une liste de valeurs, les valeurs valides sont énumérées avec le paramètre. Par exemple, le paramètre **sku** peut prendre les valeurs Gratuit, Partagé, De base ou Standard. Si aucune valeur n'est spécifiée pour ce paramètre **sku**, il utilise Gratuit, la valeur par défaut.

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
          "edition": "[parameters('edition')]",
          "collation": "[parameters('collation')]",
          "maxSizeBytes": "[parameters('maxSizeBytes')]",
          "requestedServiceObjectiveId": "[parameters('requestedServiceObjectiveId')]"
        }
    },


Nous sommes presque prêts à utiliser le modèle, mais avant cela, il est nécessaire de trouver des emplacements pour chacune de ces ressources.

### Étape 4 : récupérer les emplacements correspondant aux types de ressource

Dans la plupart des modèles, il vous est demandé de spécifier un emplacement pour chaque ressource d'un groupe de ressources. Chaque ressource est située dans un centre de données Azure, mais certains centres de données Azure ne prennent pas en charge tous les types de ressources.

Sélectionnez n'importe quel emplacement compatible. Vous n’avez pas besoin de créer toutes les ressources d’un groupe de ressources au même emplacement ; toutefois, dans la mesure du possible, créez les ressources au même endroit pour optimiser les performances. Vous devez notamment vous assurer que votre base de données se trouve au même emplacement que l’application qui y accède.

Pour obtenir les emplacements compatibles avec chaque type de ressource, utilisez la cmdlet **Get-AzureLocation**. Pour limiter votre sortie à un type de ressource spécifique, tel que ResourceGroup, utilisez :

    Get-AzureLocation | Where-Object Name -eq "ResourceGroup" | Format-Table Name, LocationsString -Wrap

La sortie ressemblera à ceci :

    Name                                 LocationsString
    ----                                 ---------------
    ResourceGroup                        East Asia, South East Asia, East US, West US, North
                                         Central US, South Central US, Central US, North Europe,
                                         West Europe

Nous disposons maintenant des informations dont nous avons besoin pour créer le groupe de ressources.

### Étape 5 : créer un groupe de ressources
 
Au cours de cette étape, nous allons utiliser le modèle de groupe de ressources pour créer le groupe de ressources. Pour référence, ouvrez le fichier New_WebSite_And_Database.json sur le disque et suivez la procédure. Le fichier de modèle peut être très utile pour déterminer les valeurs de paramètre à transmettre, comme la valeur ApiVersion correcte pour une ressource.

Pour créer un groupe de ressources, utilisez la cmdlet **New-AzureResourceGroup**.

La commande utilise le paramètre **Name** pour attribuer un nom au groupe de ressources et le paramètre **Location** pour indiquer son emplacement. Utilisez la sortie de **Get-AzureLocation** pour sélectionner un emplacement pour le groupe de ressources. Elle utilise le paramètre **GalleryTemplateIdentity** pour spécifier le modèle de la galerie.

	PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview
            ....

Dès que vous tapez le nom du modèle, New-AzureResourceGroup récupère le modèle, l'analyse et ajoute les paramètres du modèle à la commande de façon dynamique. Il est ainsi beaucoup plus facile de spécifier les valeurs des paramètres du modèle. En outre, si vous oubliez une valeur obligatoire, Windows PowerShell vous invite à spécifier cette valeur.

**Paramètres de modèle dynamiques**

Pour obtenir les paramètres, tapez un signe moins (-) pour indiquer un nom de paramètre, puis appuyez sur la touche de tabulation. Ou bien tapez les premières lettres d'un nom de paramètre tel que siteName, puis appuyez sur la touche de tabulation.

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -si<TAB>

PowerShell complète le nom du paramètre. Pour parcourir les noms des paramètres, appuyez plusieurs fois sur la touche de tabulation.

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName 

Entrez un nom pour le site web et recommencez à appuyer sur la touche de tabulation pour chacun des paramètres. Les paramètres dotés d'une valeur par défaut sont facultatifs. Pour accepter la valeur par défaut, omettez le paramètre de la commande.

Lorsqu'un paramètre du modèle est associé à une liste de valeurs, comme le paramètre sku dans ce modèle, appuyez sur la touche de tabulation pour parcourir les valeurs du paramètre.

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku <TAB>

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku Basic<TAB>

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku Free<TAB>

Voici un exemple de commande New-AzureResourceGroup qui indique uniquement les paramètres du modèle requis et le paramètre courant **Verbose**. Notez que **administratorLoginPassword** est omis.

	PS C:\> New-AzureResourceGroup -Name TestRG -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -hostingPlanName TestPlan -siteLocation "East Asia" -serverName testserver -serverLocation "East Asia" -administratorLogin Admin01 -databaseName TestDB -Verbose

Au moment d'entrer la commande, vous êtes invité à saisir le paramètre obligatoire manquant, **administratorLoginPassword**. Puis, lorsque vous tapez le mot de passe, la valeur de la chaîne sécurisée est masquée. Cette stratégie évite le risque de fournir un mot de passe en clair.

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	administratorLoginPassword: **********

**New-AzureResourceGroup** renvoie le groupe de ressources qu’il a créé et déployé.

Il nous a suffi de quelques étapes seulement pour créer et déployer les ressources requises pour un site web complexe. Le modèle de la galerie a fourni la quasi-totalité des informations dont nous avions besoin pour effectuer cette tâche. Et, avantage non négligeable : la tâche est facilement automatisée.

## Obtenir des informations sur vos groupes de ressources

Après avoir créé un groupe de ressources, vous pouvez utiliser les applets de commande du module AzureResourceManager pour gérer vos groupes de ressources.

- Pour obtenir tous les groupes de ressources de votre abonnement, utilisez l’applet de commande **Get-AzureResourceGroup** :

		PS C:\>Get-AzureResourceGroup

		ResourceGroupName : TestRG
		Location          : eastasia
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
		
		...

- Pour obtenir les ressources du groupe de ressources, utilisez l’applet de commande **Get-AzureResource** et son paramètre ResourceGroupName. Sans paramètres, Get-AzureResource récupère l'ensemble des ressources de votre abonnement Azure.

		PS C:\> Get-AzureResource -ResourceGroupName TestRG
		
		ResourceGroupName : TestRG
		Location          : eastasia
		ProvisioningState : Succeeded
		Tags              :
		
		Resources         :
				Name                   Type                          Location
				----                   ------------                  --------
				ServerErrors-TestSite  microsoft.insights/alertrules         eastasia
	        	TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
	        	TestSite               microsoft.insights/components         centralus
	         	testserver             Microsoft.Sql/servers                 eastasia
	        	TestDB                 Microsoft.Sql/servers/databases       eastasia
	        	TestPlan               Microsoft.Web/serverFarms             eastasia
	        	TestSite               Microsoft.Web/sites                   eastasia
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG

## Ajouter une ressource à un groupe de ressources

- Pour ajouter une ressource au groupe de ressources, utilisez la cmdlet **New-AzureResourceGroup**. Cette commande ajoute un nouveau site web au groupe de ressources TestRG. Cette commande est un peu plus complexe, car elle n'utilise pas de modèle. 

        PS C:\>New-AzureResource -Name TestSite2 -Location "North Europe" -ResourceGroupName TestRG -ResourceType "Microsoft.Web/sites" -ApiVersion 2014-06-01 -PropertyObject @{"name" = "TestSite2"; "siteMode"= "Limited"; "computeMode" = "Shared"}

- Pour ajouter un nouveau déploiement basé sur un modèle au groupe de ressources, utilisez la commande **New-AzureResourceGroupDeployment**.

		PS C:\>New-AzureResourceGroupDeployment ` 
		-ResourceGroupName TestRG `
		-GalleryTemplateIdentity Microsoft.WebSite.0.2.6-preview `
		-siteName TestWeb2 `
		-hostingPlanName TestDeploy2 `
		-siteLocation "North Europe" 

## Déplacer une ressource

Vous pouvez déplacer des ressources existantes vers un nouveau groupe de ressources. Pour obtenir des exemples, consultez la page [Déplacement de ressources vers un nouveau groupe de ressources ou un abonnement](resource-group-move-resources.md).

## Supprimer un groupe de ressources

- Pour supprimer une ressource du groupe de ressources, utilisez la cmdlet **Remove-AzureResource**. Cette cmdlet supprime la ressource, mais pas le groupe de ressources.

	Cette commande supprime le site web TestSite2 du groupe de ressources TestRG.

		Remove-AzureResource -Name TestSite2 -ResourceGroupName TestRG -ResourceType "Microsoft.Web/sites" -ApiVersion 2014-06-01

- Pour supprimer un groupe de ressources, utilisez la cmdlet **Remove-AzureResourceGroup**. Cette cmdlet supprime le groupe de ressources et ses ressources.

		PS C:\ps-test> Remove-AzureResourceGroup -Name TestRG
		
		Confirm
		Are you sure you want to remove resource group 'TestRG'
		[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## Résolution des problèmes affectant un groupe de ressources
En testant les cmdlets dans les modules AzureResourceManager, vous êtes susceptible de rencontrer des erreurs. Aidez-vous des conseils figurant dans cette section pour les résoudre.

### Prévention des erreurs

Le module AzureResourceManager inclut des cmdlets vous permettant d'éviter les erreurs.


- **Get-AzureLocation** : cette applet de commande obtient les emplacements qui prennent en charge chaque type de ressource. Avant d'entrer un emplacement pour une ressource, utilisez cette applet de commande pour vérifier que l'emplacement est compatible avec le type de ressource concerné.


- **Test-AzureResourceGroupTemplate** : testez votre modèle et paramètre de modèle avant de les utiliser. Entrez un modèle personnalisé ou de la galerie et les valeurs de paramètres du modèle que vous prévoyez d'utiliser. Cette cmdlet teste si le modèle est intrinsèquement cohérent et si la valeur que vous avez définie correspond au modèle.



### Correction des erreurs

- **Get-AzureResourceGroupLog** : cette applet de commande récupère les entrées du journal pour chaque déploiement du groupe de ressources. Si une erreur survient, commencez par examiner les journaux des déploiements. 

- **Verbose et Debug** : les applets de commande du module AzureResourceManager appellent les API REST qui effectuent la tâche effective. Pour voir les messages renvoyés par les API, définissez la variable $DebugPreference sur Continue et ajoutez le paramètre courant Verbose dans vos commandes. Souvent, ces messages fournissent des indices essentiels sur les causes d'erreurs.

- **Vos informations d'identification Azure n'ont pas été configurées ou ont expiré** : pour actualiser les informations d'identification dans votre session Windows PowerShell, utilisez l'applet de commande Add-AzureAccount. Les informations d'identification que contient un fichier de paramètres de publication ne sont pas suffisantes pour les cmdlets du module AzureResourceManager.


## Étapes suivantes
Mise en route

- [Présentation du gestionnaire des ressources Azure](./resource-group-overview.md)
- [Utilisation de l’interface de ligne de commande Azure pour Mac, Linux et Windows avec le Gestionnaire des ressources Azure](./xplat-cli-azure-resource-manager.md)
- [Utilisation du portail Azure pour gérer vos ressources Azure](./resource-group-portal.md)

Création et déploiement d’applications

- [Création de modèles Azure Resource Manager](./resource-group-authoring-templates.md)
- [Déploiement d’une application avec un modèle Azure Resource Manager](./resource-group-template-deploy.md)
- [Déployer une application complexe de manière prévisible dans Microsoft Azure](app-service-web/app-service-deploy-complex-application-predictably.md)
- [Résolution des problèmes liés aux déploiements de groupes de ressources dans Azure](./resource-group-deploy-debug.md)
- [Fonctions des modèles Azure Resource Manager](./resource-group-template-functions.md)
- [Opérations de modèle avancées](./resource-group-advanced-template.md)

Organisation des ressources

- [Organisation des ressources Azure à l’aide de balises](./resource-group-using-tags.md)

Gestion et audit de l’accès

- [Gestion et audit d’accès aux ressources](./resource-group-rbac.md)
- [Authentification d’un principal du service à l’aide d’Azure Resource Manager](./resource-group-authenticate-service-principal.md)
- [Création d'un nouveau principal du service Azure à l’aide du portail Azure classique](./resource-group-create-service-principal-portal.md)

<!---HONumber=July15_HO4-->
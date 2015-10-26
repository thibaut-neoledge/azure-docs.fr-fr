<properties
   pageTitle="Environnements de développement et de test | Microsoft Azure"
   description="Apprenez à utiliser les modèles Azure Resource Manager pour créer et supprimer rapidement et avec cohérence des environnements de développement et de test."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="jimdial"
   manager="carolz"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/08/2015"
   ms.author="jdial"/>

# Environnements de développement et de test dans Microsoft Azure

Les applications personnalisées sont généralement déployées dans plusieurs environnements de développement et de test avant le déploiement en production. Lors de la création d’environnements en local, des ressources informatiques sont acquises ou allouées pour chaque environnement et pour chaque application. Les environnements incluent souvent plusieurs machines physiques ou virtuelles avec des configurations spécifiques qui sont déployées manuellement ou avec des scripts d’automatisation complexes. Souvent, les déploiements nécessitent plusieurs heures et entraînent des configurations incohérentes à travers les environnements.

## Scénario ##

Lorsque vous configurez des environnements de développement et de test dans Microsoft Azure, vous payez uniquement les ressources que vous utilisez. Cet article explique comment vous pouvez créer, gérer et supprimer, rapidement et de manière cohérente, des environnements de développement et de test à l’aide de modèles et de fichiers de paramètres Azure Resource Manager, comme illustré ci-dessous.

![Scénario](./media/solution-dev-test-environments-preview-portal/scenario.png)

Trois environnements de développement et de test sont présentés ci-dessus. Chacun dispose d’une application web et d’une base de données SQL. Les noms de l’application et de la base de données de chaque environnement sont différents. Cet article explique comment utiliser un modèle pour déployer la même ressource à travers des environnements et comment utiliser des fichiers de paramètres uniques pour spécifier différentes configurations pour les ressources à travers les environnements.

Si vous n’êtes pas familiarisé avec les concepts d’Azure Resource Manager, nous vous recommandons de lire l’article [Présentation d’Azure Resource Manager](resource-group-overview.md) avant de lire cet article.

En premier lieu, vous voudrez peut-être passer en revue les étapes de cet article sans toutefois lire les articles référencés, afin d’acquérir rapidement une expérience d’utilisation des modèles Azure Resource Manager. Vous vous poserez certainement de nombreuses questions au fil de la découverte de ces étapes. Lorsque vous aurez parcouru les étapes une fois, vous serez en mesure d’obtenir des réponses à la plupart de vos questions en étudiant plus en détail les étapes et en lisant les articles référencés.

## Planification de l’utilisation des ressources Azure
Une fois que vous disposez d’une conception de haut niveau pour votre application, vous pouvez définir les éléments suivants :

- Les ressources Azure que votre application inclura. Vous pouvez générer votre application et la déployer comme une application web Azure avec une base de données SQL Azure. Vous pouvez générer votre application dans des machines virtuelles à l’aide de PHP et de MySQL ou d’IIS et de SQL Server ou d’autres composants. L’article [Comparaison entre Azure App Service, Cloud Services et les machines virtuelles](app-service-web/choose-web-site-cloud-service-vm.md) vous permet de décider quelles ressources Azure vous souhaitez utiliser pour votre application.
- Les exigences de niveaux de service, telles que la disponibilité, la sécurité et l’échelle auxquelles votre application répondra.

## Utilisation de modèles Azure Resource Manager
Un modèle Azure Resource Manager définit toutes les ressources Azure que votre application utilise. Plusieurs modèles existent déjà. Vous pouvez les déployer directement dans le portail Azure en version préliminaire ou les télécharger, les modifier et les enregistrer dans un système de contrôle de code source avec le code de votre application. Il y a de grandes chances qu’un modèle existant incluant les ressources que vous souhaitez utiliser avec votre application soit disponible. Vous trouverez une liste des modèles disponibles dans le référentiel GitHub [Modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates/).

Dans la liste, vous verrez un dossier [201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database). Étant donné que de nombreuses applications personnalisées incluent une application web et une base de données SQL, ce modèle est utilisé comme exemple dans le reste de cet article, afin de vous aider à comprendre comment utiliser les modèles. Expliquer tous les éléments relatifs à la création et à la configuration par ce modèle dépasse la portée de cet article, mais si vous envisagez d’utiliser ce modèle pour créer des environnements réels dans votre organisation, vous bénéficierez d’une compréhension complète en lisant l’article [Mettre en service une application web avec une base de données SQL](app-service-web/app-service-web-arm-with-sql-database-provision.md).

> [AZURE.NOTE]Vous pouvez déployer le modèle dans Azure directement en cliquant sur le bouton Déployer dans Azure dans l’article [Mettre en service une application web avec une base de données SQL](http://azure.microsoft.com/documentation/templates/201-web-app-sql-database/). Il peut se révéler utile d’en savoir plus sur les modèles, mais cela ne vous permettra pas de modifier, de créer une version et d’enregistrer votre modèle et vos valeurs de paramètres avec le code de votre application. Les étapes de cet article expliquent comment vous pouvez enregistrer et créer une version de votre modèle et de vos valeurs de paramètres avec le code de votre application.

  **Étape 1 :** Affichez le contenu du fichier [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy.json) dans le dossier 201-web-app-sql-database. Il s’agit du fichier de modèle Azure Resource Manager. Dans le mode d’affichage, cliquez sur le bouton [Brut](https://github.com/Azure/azure-quickstart-templates/raw/master/201-web-app-sql-database/azuredeploy.json). Avec la souris, sélectionnez tout le contenu de ce fichier et enregistrez-le sur votre ordinateur dans un fichier nommé TestApp1-Template.json.

Dans le fichier de modèle, vous verrez une section « resources » qui définit les ressources Azure créées par ce modèle. Ce modèle crée, entre autres types de ressources, les ressources de l’[application web Azure](app-service-web/app-service-web-overview.md) et de la [base de données SQL Azure](sql-database/sql-database-technical-overview.md).

Vous verrez également une section « parameters » qui définit les paramètres selon lesquels chaque ressource peut être configurée. Certains des paramètres spécifiés dans le modèle ont des propriétés defaultValue, tandis que d’autres en sont dépourvus. Lorsque vous déployez des ressources Azure avec un modèle, vous devez fournir des valeurs pour tous les paramètres qui n’ont pas de propriétés defaultValue. Si vous ne fournissez pas de valeurs pour les paramètres avec des propriétés defaultValue, la valeur spécifiée pour le paramètre defaultValue dans le modèle est utilisée.

Un modèle définit quelles ressources Azure sont créées et selon quels paramètres chaque ressource peut être configurée. En général, vous voudrez créer les mêmes ressources dans chaque environnement de développement et de test. Pour en savoir plus sur les modèles et sur la manière de concevoir le vôtre, lisez l’article [Meilleures pratiques relatives à la conception des modèles Azure Resource Manager](best-practices-resource-manager-design-templates.md).

## Création de fichiers de paramètres

Vous voudrez probablement créer les mêmes ressources Azure dans chaque environnement, mais vous pouvez souhaiter une configuration des ressources différente dans chaque environnement. C’est là qu’interviennent les fichiers de paramètres.

  **Étape 2 :** Affichez le contenu du fichier [azuredeploy-parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy-parameters.json) dans le dossier 201-web-app-sql-database. Il s’agit du fichier de paramètres pour le fichier de modèle que vous avez enregistré à l’étape 1. Dans le mode d’affichage, cliquez sur le bouton [Brut](https://github.com/Azure/azure-quickstart-templates/raw/master/201-web-app-sql-database/azuredeploy-parameters.json). Avec la souris, sélectionnez tout le contenu de ce fichier et enregistrez-le dans trois fichiers distincts sur votre ordinateur avec les noms suivants :

- TestApp1-Parameters-Development.json
- TestApp1-Parameters-Test.json
- TestApp1-Parameters-Pre-Production.json

  **Étape 3 :** À l’aide de n’importe quel éditeur de texte ou JSON, modifiez les fichiers de paramètres d’environnement de développement et de test créés à l’étape 2, en remplaçant les valeurs existantes dans les fichiers par les valeurs ci-dessous.

  --TestApp1-Parameters-Development.json--

| Paramètre | Description |
|---|---|
| **siteName** | TestApp1Dev |
| **hostingPlanName** | TestApp1PlanDev |
| **siteLocation** | Centre des États-Unis |
| **serverName** | testapp1dev |
| **serverLocation** | Centre des États-Unis |
| **administratorLogin** | testapp1Admin |
| **administratorLoginPassword** | #testapp1XYZ |
| **databaseName** | testapp1dev |

--TestApp1-Parameters-Test.json--

| Paramètre | Description |
|---|---|
| **siteName** | TestApp1Test |
| **hostingPlanName** | TestApp1PlanTest |
| **siteLocation** | Centre des États-Unis |
| **serverName** | testapp1test |
| **serverLocation** | Centre des États-Unis |
| **administratorLogin** | testapp1Admin |
| **administratorLoginPassword** | #testapp1XYZ |
| **databaseName** | testapp1test |

À l’étape 6, ces fichiers de paramètres serviront à créer des configurations uniques pour les ressources de l’application web Azure et de la base de données SQL Azure dans les environnements de développement et de test.

 **Étape 4 :** Modifiez le fichier de paramètres TestApp1-Parameters-Pre-Production.json que vous avez créé à l’étape 2. Remplacez tout le contenu du fichier par les éléments suivants :

	{
	  "$schema" : "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
	  "contentVersion" : "1.0.0.0",
	  "parameters" : {
	    "administratorLogin" : {
	      "value" : "testApp1Admin"
	    },
	    "administratorLoginPassword" : {
	      "value" : "testApp1XP49"
	    },
	    "databaseName" : {
	      "value" : "testapp1preprod"
	    },
	    "hostingPlanName" : {
	      "value" : "TestApp1PlanPreProd"
	    },
	    "serverLocation" : {
	      "value" : "Central US"
	    },
	    "serverName" : {
	      "value" : "testapp1preprod"
	    },
	    "siteLocation" : {
	      "value" : "Central US"
	    },
	    "siteName" : {
	      "value" : "TestApp1PreProd"
	    },
	    "sku" : {
	      "value" : "Standard"
	    },
		"requestedServiceObjectiveName" : {
		  "value" : "S1"
	    }
	  }
	}

Dans le fichier de paramètres de préproduction ci-dessus, les paramètres **sku** et **requestedServiceObjectiveName** ont été *ajoutés*, alors qu’ils n’ont pas été ajoutés dans les fichiers de paramètres de développement et de test. Raison : il s’agit des valeurs par défaut spécifiées pour ces paramètres dans le modèle. Dans les environnements de développement et de test, les valeurs par défaut sont utilisées, mais dans l’environnement de préproduction, des valeurs personnalisées sont utilisées pour ces paramètres.

Des valeurs personnalisées sont utilisées pour ces paramètres dans l’environnement de préproduction afin de tester des valeurs pour ces paramètres que vous préférerez peut-être pour votre environnement de production. Ces paramètres sont tous liés aux [plans d’hébergement de l’application web](http://azure.microsoft.com/pricing/details/app-service/) Azure ou au paramètre **sku** et à la [base de données SQL](http://azure.microsoft.com/pricing/details/sql-database/) Azure ou au paramètre **requestedServiceObjectiveName** qui sont utilisés par l’application. Les différents noms d’objectif de service et références SKU ont des coûts et des fonctionnalités distincts et prennent en charge différentes mesures de niveau de service.

Le tableau ci-dessous répertorie les valeurs par défaut pour ces paramètres, spécifiées dans le modèle, et les valeurs qui sont utilisées à la place des valeurs par défaut dans le fichier de paramètres de préproduction.

| Paramètre | Valeur par défaut | Valeur du fichier de paramètres |
|---|---|---|
| **sku** | Gratuit | Standard |
| **requestedServiceObjectiveName** | S0 | S1 |

## Création d’environnements
Toutes les ressources Azure doivent être créées dans un [groupe de ressources Azure](azure-portal/resource-group-portal.md). Les groupes de ressources vous permettent de regrouper des ressources Azure afin de pouvoir les gérer de façon collective. Des [autorisations](azure-portal/resource-group-rbac.md) peuvent être affectées aux groupes de ressources. Ainsi, des personnes spécifiques au sein de votre organisation peuvent les créer, les modifier, les supprimer ou les afficher, de même que les ressources qu’ils contiennent. Les informations d’alerte et de facturation pour les ressources dans le groupe de ressources sont disponibles dans le [portail Azure en version préliminaire](https://portal.azure.com). Des groupes de ressources sont créés dans un [emplacement](http://azure.microsoft.com/regions/) d’Azure. Dans cet article, toutes les ressources sont créées dans le Centre des États-Unis. Lorsque vous commencez à créer des environnements réels, vous choisissez l’emplacement qui répond le mieux à vos besoins.

  **Étape 5 :** Créez des groupes de ressources pour les environnements de développement et de test à l’aide de l’une des méthodes ci-dessous. Les deux méthodes permettent d’obtenir exactement le même résultat.

  **Méthode 1 :** Interface de ligne de commande Azure

  Vérifiez que l’interface de ligne de commande est [installée](xplat-cli-install.md) sur un ordinateur Windows, OS X ou Linux et que vous avez [connecté](xplat-cli-connect.md) votre ID d’organisation à votre abonnement Azure. À partir de la ligne de commande de l’interface de ligne de commande, tapez la commande suivante pour créer le groupe de ressources pour l’environnement de développement.

	azure group create "TestApp1-Development" "Central US"

  Si elle réussit, la commande retourne les éléments suivants :

	info:    Executing command group create
	+ Getting resource group TestApp1-Development
	+ Creating resource group TestApp1-Development
	info:    Created resource group TestApp1-Development
	data:    Id:                  /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development
	data:    Name:                TestApp1-Development
	data:    Location:            centralus
	data:    Provisioning State:  Succeeded
	data:    Tags: null
	data:
	info:    group create command OK

  Pour créer le groupe de ressources pour l’environnement de test, tapez la commande suivante :

	azure group create "TestApp1-Test" "Central US"

  Pour créer le groupe de ressources pour l’environnement de préproduction, tapez la commande suivante :

	azure group create "TestApp1-Pre-Production" "Central US"

  **Méthode 2 :** PowerShell

  Vérifiez que PowerShell est installé sur un ordinateur Windows et connecté à votre abonnement, comme expliqué dans l’article [Installation et configuration d’Azure PowerShell](powershell-install-configure.md). À partir d’une invite de commande PowerShell, tapez la commande suivante pour créer le groupe de ressources pour l’environnement de développement. Si vous utilisez Azure PowerShell 1.0 en version préliminaire, la commande est **New-AzureRmResourceGroup** comme indiqué ci-dessous. Si vous utilisez une version d'Azure PowerShell antérieure à la version préliminaire 1.0, la commande est **New-AzureResourceGroup**.

	New-AzureRmResourceGroup -Name TestApp1-Development -Location "Central US"

  Si elle réussit, la commande retourne les éléments suivants :

	WARNING: The output object of this cmdlet will be modified in a future release.


	ResourceGroupName : TestApp1-Development
	Location          : centralus
	ProvisioningState : Succeeded
	Tags              : 
	Permissions       : 
	                    Actions  NotActions
	                    =======  ==========
	                    *                  
	                    
	ResourceId        : /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development

  Pour créer le groupe de ressources pour l’environnement de test, tapez la commande suivante :

	New-AzureRmResourceGroup -Name TestApp1-Test -Location "Central US"

  Pour créer le groupe de ressources pour l’environnement de préproduction, tapez la commande suivante :

	New-AzureRmResourceGroup -Name TestApp1-Pre-Production -Location "Central US"

 **Étape 6 :** Déployez des ressources Azure dans les groupes de ressources pour chaque environnement à l'aide du fichier de modèle pour l'application et des fichiers de paramètres pour chaque environnement en utilisant l'une des deux méthodes ci-dessous. Les deux méthodes permettent d’obtenir exactement le même résultat.

  **Méthode 1 :** Interface de ligne de commande Azure

  À partir de la ligne de commande de l’interface de ligne de commande, tapez la commande ci-dessous pour déployer des ressources dans le groupe de ressources créé pour l’environnement de développement, en remplaçant [chemin d’accès] par le chemin d’accès aux fichiers que vous avez enregistrés dans les étapes précédentes.

	azure group deployment create -g TestApp1-Development -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Development.json 

  Si elle réussit, la commande retourne les éléments suivants :

	info:    Executing command group deployment create
	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "Deployment1"
	+ Waiting for deployment to complete
	data:    DeploymentName     : Deployment1
	data:    ResourceGroupName  : TestApp1-Development
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : XXXX-XX-XXT20:20:23.5202316Z
	data:    Mode               : Incremental
	data:    Name                           Type          Value
	data:    -----------------------------  ------------  ----------------------------
	data:    siteName                       String        TestApp1Dev
	data:    hostingPlanName                String        TestApp1PlanDev
	data:    siteLocation                   String        Central US
	data:    sku                            String        Free
	data:    workerSize                     String        0
	data:    serverName                     String        testapp1dev
	data:    serverLocation                 String        Central US
	data:    administratorLogin             String        testapp1Admin
	data:    administratorLoginPassword     SecureString  undefined
	data:    databaseName                   String        testapp1dev
	data:    collation                      String        SQL_Latin1_General_CP1_CI_AS
	data:    edition                        String        Standard
	data:    maxSizeBytes                   String        1073741824
	data:    requestedServiceObjectiveName  String        S0
	info:    group deployment create command OKx

  Si la commande échoue, résolvez tous les messages d’erreur et réessayez. Les problèmes courants sont l’utilisation de valeurs de paramètres qui ne respectent pas les contraintes d’affectation de noms des ressources Azure. D'autres conseils de dépannage sont disponibles dans l'article [Résolution des problèmes liés aux déploiements de groupes de ressources dans Azure](virtual-machines/resource-group-deploy-debug.md).

  À partir de la ligne de commande de l’interface de ligne de commande, tapez la commande ci-dessous pour déployer des ressources dans le groupe de ressources créé pour l’environnement de test, en remplaçant [chemin d’accès] par le chemin d’accès aux fichiers que vous avez enregistrés dans les étapes précédentes.

	azure group deployment create -g TestApp1-Test -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Test.json

  À partir de la ligne de commande de l’interface de ligne de commande, tapez la commande ci-dessous pour déployer des ressources dans le groupe de ressources créé pour l’environnement de préproduction, en remplaçant [chemin d’accès] par le chemin d’accès aux fichiers que vous avez enregistrés dans les étapes précédentes.

	azure group deployment create -g TestApp1-Pre-Production -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Pre-Production.json
  
  **Méthode 2 :** PowerShell

  À partir d’une invite de commande PowerShell, tapez la commande ci-dessous pour déployer des ressources dans le groupe de ressources créé pour l’environnement de développement, en remplaçant [chemin d’accès] par le chemin d’accès aux fichiers que vous avez enregistrés dans les étapes précédentes. Si vous utilisez Azure PowerShell 1.0 en version préliminaire, la commande est **New-AzureRmResourceGroupDeployment** comme indiqué ci-dessous. Si vous utilisez une version d'Azure PowerShell antérieure à la version préliminaire 1.0, la commande est **New-AzureResourceGroupDeployment**.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Development -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Development.json -Name Deployment1 

  Si elle réussit, la commande retourne les éléments suivants :

	DeploymentName    : Deployment1
	ResourceGroupName : TestApp1-Development
	ProvisioningState : Succeeded
	Timestamp         : XX/XX/XXXX 2:44:48 PM
	Mode              : Incremental
	TemplateLink      : 
	Parameters        : 
	                    Name             Type                       Value     
	                    ===============  =========================  ==========
	                    siteName         String                     TestApp1Dev
	                    hostingPlanName  String                     TestApp1PlanDev
	                    siteLocation     String                     Central US
	                    sku              String                     Free      
	                    workerSize       String                     0         
	                    serverName       String                     testapp1dev
	                    serverLocation   String                     Central US
	                    administratorLogin  String                     testapp1Admin
	                    administratorLoginPassword  SecureString                         
	                    databaseName     String                     testapp1dev
	                    collation        String                     SQL_Latin1_General_CP1_CI_AS
	                    edition          String                     Standard  
	                    maxSizeBytes     String                     1073741824
	                    requestedServiceObjectiveName  String                     S0        
	                    
	Outputs           :

  Si la commande échoue, résolvez tous les messages d’erreur et réessayez. Les problèmes courants sont l’utilisation de valeurs de paramètres qui ne respectent pas les contraintes d’affectation de noms des ressources Azure. D'autres conseils de dépannage sont disponibles dans l'article [Résolution des problèmes liés aux déploiements de groupes de ressources dans Azure](virtual-machines/resource-group-deploy-debug.md).

  À partir d’une invite de commande PowerShell, tapez la commande ci-dessous pour déployer des ressources dans le groupe de ressources créé pour l’environnement de test, en remplaçant [chemin d’accès] par le chemin d’accès aux fichiers que vous avez enregistrés dans les étapes précédentes.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Test -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Test.json -Name Deployment1

  À partir d’une invite de commande PowerShell, tapez la commande ci-dessous pour déployer des ressources dans le groupe de ressources créé pour l’environnement de préproduction, en remplaçant [chemin d’accès] par le chemin d’accès aux fichiers que vous avez enregistrés dans les étapes précédentes.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Pre-Production -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Pre-Production.json -Name Deployment1

Les fichiers de modèles et de paramètres peuvent être créés, dotés d’une version et gérés avec le code de votre application dans un système de contrôle de code source. Vous pouvez également enregistrer les commandes ci-dessus dans des fichiers de script et les enregistrer avec votre code.

## Gestion d’environnements
Au cours du développement, la configuration des ressources Azure dans les différents environnements peut être modifiée de manière incohérente, intentionnellement ou accidentellement. Cela peut entraîner des dépannages et des résolutions de problèmes inutiles pendant le cycle de développement des applications.

  **Étape 7 :** Modifiez les environnements. Ouvrez le [portail Azure en version préliminaire](https://portal.azure.com) et connectez-vous avec le même compte que celui utilisé pour effectuer les étapes ci-dessus. Comme le montre l’image ci-dessous, cliquez sur Parcourir tout --> Groupes de ressources (vous devrez peut-être faire défiler le volet Parcourir pour voir Groupes de ressources). Vous verrez les trois groupes de ressources que vous avez créés en utilisant l’une des méthodes des étapes précédentes. Cliquez sur le groupe de ressources TestApp1-Development comme indiqué ci-dessous.

  ![Portail](./media/solution-dev-test-environments-preview-portal/portal1.png)

  Après avoir cliqué sur le groupe de ressources TestApp1-Development, le volet qui répertorie les ressources créées par le modèle s’affiche dans le déploiement du groupe de ressources que vous avez effectué à l’étape précédente. Supprimez la ressource de l’application web TestApp1Dev en cliquant sur TestApp1Dev --> Supprimer, comme indiqué ci-dessous.

  ![Portail](./media/solution-dev-test-environments-preview-portal/portal2.png)

  Cliquez sur Oui lorsque le portail vous demande si vous voulez vraiment supprimer la ressource. Le contenu du groupe de ressources est maintenant différent de ce qu’il devrait être. Vous pouvez poursuivre vos expériences en supprimant plusieurs ressources de plusieurs groupes de ressources, voire en modifiant les paramètres de configuration de certaines ressources.

> [AZURE.NOTE]Au lieu d'utiliser le portail Azure en version préliminaire pour supprimer une ressource d'un groupe de ressources, vous pouvez utiliser la commande PowerShell [Remove-AzureResource](https://msdn.microsoft.com/library/azure/dn757676.aspx) ou la commande « azure resource delete » à partir de l'interface de ligne de commande pour effectuer la même tâche.

  **Étape 8 :** Redéployez les environnements dans les groupes de ressources à l'aide des commandes utilisées à l'étape 6, mais remplacez « Deployment1 » par « Deployment2 ». Comme indiqué dans la section Résumé de l’image ci-dessous, vous verrez que toutes les ressources du modèle existent à nouveau dans le groupe de ressources TestApp1-Development. L’un des avantages du déploiement de vos environnements avec les modèles Azure Resource Manager est que vous pouvez facilement effectuer de nouveaux déploiements vers un état connu à tout moment.

  ![Portail](./media/solution-dev-test-environments-preview-portal/portal3.png)

  Si vous cliquez sur le texte sous « Dernier déploiement » dans l’image, vous verrez un volet qui affiche l’historique de déploiement du groupe de ressources. Étant donné que vous avez utilisé les noms « Deployment1 » pour le premier déploiement et « Deployment2 » pour le deuxième déploiement, vous aurez deux entrées. La sélection d’un déploiement affiche un volet qui présente les résultats pour chaque déploiement.

## Suppression d’environnements
Une fois que vous aurez terminé avec un environnement, vous souhaiterez le supprimer afin d’éviter des frais d’utilisation de ressources Azure dont vous ne vous servez plus. La suppression d’environnements est encore plus facile que leur création. Dans les étapes précédentes, des groupes de ressources Azure individuels ont été créés pour chaque environnement. Lorsque vous supprimez un groupe de ressources, toutes les ressources qu’il contient sont également supprimées. Ainsi, les méthodes ci-dessous permettent de supprimer les environnements (groupes de ressources), ainsi que toutes les ressources Azure qu’ils contiennent et que vous avez déployées précédemment.

  **Étape 9 :** Supprimez les environnements à l'aide de l'une des méthodes ci-dessous. Les deux méthodes permettent d’obtenir exactement le même résultat.

  **Méthode 1 : Interface de ligne de commande Azure**

  À partir d’une invite de l’interface de ligne de commande, tapez les éléments suivants :

	azure group delete "TestApp1-Development"

  La commande retournera les éléments suivants si vous tapez « y » lorsque vous y êtes invité :

	info:    Executing command group delete
	Delete resource group TestApp1-Development? [y/n] y
	+ Deleting resource group TestApp1-Development
	info:    group delete command OK

  À partir d’une invite de l’interface de ligne de commande, tapez les éléments suivants pour supprimer les environnements restants :

	azure group delete "TestApp1-Test"
	azure group delete "TestApp1-Pre-Production"
  
  **Méthode 2 :** PowerShell

  Si vous utilisez Azure PowerShell 1.0 en version préliminaire, la commande pour supprimer les groupe de ressources est **Remove-AzureRmResourceGroup** comme indiqué ci-dessous. Si vous utilisez une version d'Azure PowerShell antérieure à la version préliminaire 1.0, la commande est **Remove-AzureResourceGroup**. À partir d’une invite PowerShell, tapez les éléments suivants :

	Remove-AzureRmResourceGroup -Name TestApp1-Development

  La commande retournera les éléments suivants si vous tapez « y » lorsque vous y êtes invité :

	Confirm
	Are you sure you want to remove resource group 'TestApp1-Development'
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

  À partir d’une invite PowerShell, tapez les éléments suivants pour supprimer les environnements restants :

	Remove-AzureRmResourceGroup -Name TestApp1-Test
	Remove-AzureRmResourceGroup -Name TestApp1-Pre-Production

Quelle que soit la méthode que vous utilisez, une fois que les commandes terminent l’exécution, les groupes de ressources et toutes les ressources qu’ils contiennent n’existent plus et vous n’êtes plus facturé pour les ressources.

Pour réduire les dépenses liées à l'utilisation des ressources Azure pendant le développement des applications, vous pouvez utiliser [Azure Automation](automation/automation-intro.md) pour planifier des tâches qui :

- arrêtent les machines virtuelles à la fin de chaque journée et les redémarrent au début de chaque journée.
- suppriment des environnements entiers à la fin de chaque journée et les recréent au début de chaque journée.
 
Maintenant que vous avez pu constater comme il est facile de créer, de gérer et de supprimer les environnements de développement et de test, vous pouvez en savoir plus sur ce que vous venez de faire en étudiant plus en détail les étapes ci-dessus et en lisant les références contenues dans cet article.

## Étapes suivantes

- [Déléguer le contrôle administratif](role-based-access-control-configure.md) à différentes ressources dans chaque environnement en affectant des groupes ou des utilisateurs Microsoft Azure AD à des rôles spécifiques qui ont la possibilité d'exécuter un sous-ensemble d'opérations sur des ressources Azure.
- [Affecter des balises](resource-group-using-tags.md) aux groupes de ressources pour chaque environnement et/ou aux ressources individuelles. Vous pouvez ajouter une balise « Environment » à vos groupes de ressources et définir sa valeur de sorte qu’elle corresponde aux noms de votre environnement. Les balises peuvent être particulièrement utiles si vous devez organiser les ressources à des fins de facturation ou de gestion.
- Surveiller les alertes et la facturation pour les ressources du groupe de ressources dans le [portail Azure en version préliminaire](https://portal.azure.com).

## Ressources supplémentaires

- [Créer et déployer des modèles Azure Resource Manager dans Visual Studio](http://msdn.microsoft.com/library/azure/Dn872471.aspx) avec le Kit de développement logiciel (SDK) Azure 2.6 installé.
- Créer votre application à l'aide de [Visual Studio Enterprise](https://www.visualstudio.com/products/visual-studio-enterprise-vs), [Visual Studio Code](http://www.visualstudio.com/products/code-vs) ou [Web Matrix](http://www.microsoft.com/web/webmatrix/).
- [Déployer une application web](app-service-web/web-sites-deploy.md) dans les environnements que vous avez créés.
- Utiliser [Visual Studio Release Management](http://msdn.microsoft.com/Library/vs/alm/Release/overview) pour créer des pipelines de déploiement gérés et continus pour des publications rapides, faciles et fréquentes.
- Demander une invitation pour la version préliminaire d'[Azure Dev/Test Lab](http://azure.microsoft.com/campaigns/devtest-lab/). Cela vous permet de gérer les environnements de laboratoire de développement et de test à l’aide de modèles, ainsi que de configurer des quotas et des stratégies pour l’utilisation au sein de votre organisation.

<!---HONumber=Oct15_HO3-->
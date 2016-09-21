<properties
   pageTitle="Environnements de développement et de test | Microsoft Azure"
   description="Apprenez à utiliser les modèles Azure Resource Manager pour créer et supprimer rapidement et avec cohérence des environnements de développement et de test."
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
   ms.date="01/22/2016"
   ms.author="tomfitz"/>

# Environnements de développement et de test dans Microsoft Azure

Les applications personnalisées sont généralement déployées dans plusieurs environnements de développement et de test avant le déploiement en production. Lors de la création d’environnements en local, des ressources informatiques sont acquises ou allouées pour chaque environnement et pour chaque application. Les environnements incluent souvent plusieurs machines physiques ou virtuelles avec des configurations spécifiques qui sont déployées manuellement ou avec des scripts d’automatisation complexes. Souvent, les déploiements nécessitent plusieurs heures et entraînent des configurations incohérentes à travers les environnements.

## Scénario ##

Lorsque vous configurez des environnements de développement et de test dans Microsoft Azure, vous payez uniquement les ressources que vous utilisez. Cet article explique comment vous pouvez créer, gérer et supprimer, rapidement et de manière cohérente, des environnements de développement et de test à l’aide de modèles et de fichiers de paramètres Azure Resource Manager, comme illustré ci-dessous.

![Scénario](./media/solution-dev-test-environments/scenario.png)

Trois environnements de développement et de test sont présentés ci-dessus. Chacun dispose d’une application web et de ressources de base de données SQL spécifiées dans un fichier de modèle. Les noms de l’application et la base de données de chaque environnement sont différents et sont spécifiés dans les fichiers de paramètres uniques pour chaque environnement.

Si vous n’êtes pas familiarisé avec les concepts d’Azure Resource Manager, nous vous recommandons de lire l’article [Présentation d’Azure Resource Manager](resource-group-overview.md) avant de lire cet article.

En premier lieu, vous voudrez peut-être passer en revue les étapes de cet article sans toutefois lire les articles référencés, afin d’acquérir rapidement une expérience d’utilisation des modèles Azure Resource Manager. Lorsque vous aurez parcouru les étapes une fois, vous serez en mesure d’obtenir des réponses à la plupart des questions générées par la première expérience en étudiant de plus près les différentes opérations et en lisant les articles référencés.

## Planification de l’utilisation des ressources Azure
Une fois que vous disposez d’une conception de haut niveau pour votre application, vous pouvez définir les éléments suivants :

- Les ressources Azure que votre application inclura. Vous pouvez générer votre application et la déployer comme une application web Azure avec une base de données SQL Azure. Vous pouvez générer votre application dans des machines virtuelles à l’aide de PHP et de MySQL ou d’IIS et de SQL Server ou d’autres composants. L’article [Comparaison entre Azure App Service, Cloud Services et les machines virtuelles](app-service-web/choose-web-site-cloud-service-vm.md) vous permet de décider quelles ressources Azure vous souhaitez utiliser pour votre application.
- Les exigences de niveaux de service, telles que la disponibilité, la sécurité et l’échelle auxquelles votre application répondra.

## Télécharger un modèle existant
Un modèle Azure Resource Manager définit toutes les ressources Azure que votre application utilise. Plusieurs modèles existent déjà. Vous pouvez les déployer ou les télécharger directement dans le portail Azure, les modifier et les enregistrer dans un système de contrôle de code source avec le code de votre application. Exécutez les étapes ci-dessous pour télécharger un modèle existant.

1. Parcourez les modèles existants dans le référentiel GitHub [Modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates/). Dans la liste, vous verrez un dossier [201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database). Étant donné que de nombreuses applications personnalisées incluent une application web et une base de données SQL, ce modèle est utilisé comme exemple dans le reste de cet article, afin de vous aider à comprendre comment utiliser les modèles. Expliquer tous les éléments relatifs à la création et à la configuration par ce modèle dépasse la portée de cet article, mais si vous envisagez d’utiliser ce modèle pour créer des environnements réels dans votre organisation, vous bénéficierez d’une compréhension complète en lisant l’article [Mettre en service une application web avec une base de données SQL](app-service-web/app-service-web-arm-with-sql-database-provision.md). Remarque : cet article a été rédigé pour la version de décembre 2015 du modèle [201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database). Les liens ci-dessous qui renvoient au modèle et aux fichiers de paramètre se rapportent à cette version du modèle.
2. Cliquez sur le fichier [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.json) dans le dossier 201-web-app-sql-database pour afficher son contenu. Il s’agit du fichier de modèle Azure Resource Manager.
3. Dans le mode d’affichage, cliquez sur le bouton [Brut](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.json).
4. Avec la souris, sélectionnez le contenu de ce fichier et enregistrez-le sur votre ordinateur dans un fichier nommé « TestApp1-Template.json ».
5. Examinez le contenu du modèle et notez les points suivants :
 - Section **Ressources** : cette section définit les types de ressources Azure créées par ce modèle. Ce modèle crée, entre autres types de ressources, les ressources de l’[application web Azure](app-service-web/app-service-web-overview.md) et de la [base de données SQL Azure](sql-database/sql-database-technical-overview.md). Si vous préférez exécuter et gérer les serveurs web et SQL sur des machines virtuelles, vous pouvez utiliser les modèles « [iis-2vm-sql-1vm](https://github.com/Azure/azure-quickstart-templates/tree/master/iis-2vm-sql-1vm) » ou « [lamp-app](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app) », mais les instructions de cet article sont basées sur le modèle [201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database).
 - Section **Paramètres** : cette section définit les paramètres avec lesquels chaque ressource peut être configurée. Certains des paramètres spécifiés dans le modèle ont des propriétés defaultValue, tandis que d’autres en sont dépourvus. Lorsque vous déployez des ressources Azure avec un modèle, vous devez fournir des valeurs pour tous les paramètres dont les valeurs de propriétés defaultValue ne sont pas spécifiées. Si vous ne fournissez pas de valeurs pour les paramètres avec des propriétés defaultValue, la valeur spécifiée pour le paramètre defaultValue dans le modèle est utilisée.

Un modèle définit quelles ressources Azure sont créées et selon quels paramètres chaque ressource peut être configurée. Pour en savoir plus sur les modèles et sur la manière de concevoir le vôtre, lisez l’article [Meilleures pratiques relatives à la conception des modèles Azure Resource Manager](best-practices-resource-manager-design-templates.md).

## Télécharger et personnaliser un fichier de paramètre existant

Vous voulez probablement créer les *mêmes* ressources Azure dans chaque environnement, mais que chaque configuration des ressources soit *différente* en fonction de l’environnement. C’est là qu’interviennent les fichiers de paramètres. Créer des fichiers de paramètres contenant des valeurs uniques dans chaque environnement en exécutant les opérations ci-dessous.

1. Affichez les contenus du fichier [azuredeploy-parameters.json](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.parameters.json) dans le dossier 201-web-app-sql-database. Il s’agit du fichier de paramètres pour le fichier de modèle que vous avez enregistré à l’étape précédente.
2. Dans le mode d’affichage, cliquez sur le bouton [Brut](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.parameters.json).
3. Avec la souris, sélectionnez le contenu de ce fichier et enregistrez-le dans trois fichiers distincts sur votre ordinateur avec les noms suivants :
 - TestApp1-Parameters-Development.json
 - TestApp1-Parameters-Test.json
 - TestApp1-Parameters-Pre-Production.json

3. Avec un éditeur de texte ou JSON, modifiez le fichier de paramètres d’environnement de développement que vous avez créé à l’étape 3, en remplaçant les valeurs répertoriées à droite des valeurs de paramètre dans le fichier par les *valeurs* figurant à droite des **paramètres** ci-dessous :
 - **siteName** : *TestApp1DevApp*
 - **hostingPlanName** : *TestApp1DevPlan*
 - **siteLocation** : *Centre des États-Unis*
 - **serverName** : *testapp1devsrv*
 - **serverLocation** : *Centre des États-Unis*
 - **administratorLogin** : *testapp1Admin*
 - **administratorLoginPassword** : *remplacer par votre mot de passe*
 - **databaseName** : *testapp1devdb*

4. Avec un éditeur de texte ou JSON, modifiez le fichier de paramètres d’environnement de test créé à l’étape 3, en remplaçant les valeurs répertoriées à droite des valeurs de paramètre dans le fichier par les *valeurs* figurant à droite des **paramètres** ci-dessous :
 - **siteName** : *TestApp1TestApp*
 - **hostingPlanName** : *TestApp1TestPla*n
 - **siteLocation** : *Centre des États-Unis*
 - **serverName** : *testapp1testsrv*
 - **serverLocation** : *Centre des États-Unis*
 - **administratorLogin** : *testapp1Admin*
 - **administratorLoginPassword** : *remplacer par votre mot de passe*
 - **databaseName** : *testapp1testdb*

5. Avec un éditeur de texte ou JSON, modifiez le fichier de paramètres de pré-production que vous avez créé à l’étape 3. Remplacez l’ensemble du contenu du fichier par les éléments suivants :

	    {
    	  "$schema" : "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    	  "contentVersion" : "1.0.0.0",
    	  "parameters" : {
    	"administratorLogin" : {
    	  "value" : "testApp1Admin"
    	},
    	"administratorLoginPassword" : {
    	  "value" : "replace with your password"
    	},
    	"databaseName" : {
    	  "value" : "testapp1preproddb"
    	},
    	"hostingPlanName" : {
    	  "value" : "TestApp1PreProdPlan"
    	},
    	"serverLocation" : {
    	  "value" : "Central US"
    	},
    	"serverName" : {
    	  "value" : "testapp1preprodsrv"
    	},
    	"siteLocation" : {
    	  "value" : "Central US"
    	},
    	"siteName" : {
    	  "value" : "TestApp1PreProdApp"
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

Des valeurs personnalisées sont utilisées pour ces paramètres dans l’environnement de pré-production afin de tester des valeurs correspondant aux paramètres que vous préférerez peut-être pour votre environnement de production pour les tests. Ces paramètres sont tous liés aux [plans d’hébergement de l’application web](https://azure.microsoft.com/pricing/details/app-service/) Azure ou au paramètre **sku** et à la [base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/) Azure ou au paramètre **requestedServiceObjectiveName** qui sont utilisés par l’application. Les différents noms d’objectif de service et références SKU ont des coûts et des fonctionnalités distincts et prennent en charge différentes mesures de niveau de service.

Le tableau ci-dessous répertorie les valeurs par défaut pour ces paramètres, spécifiées dans le modèle, et les valeurs qui sont utilisées à la place des valeurs par défaut dans le fichier de paramètres de préproduction.

| Paramètre | Valeur par défaut | Valeur du fichier de paramètres |
|---|---|---|
| **sku** | Gratuit | Standard |
| **requestedServiceObjectiveName** | S0 | S1 |

## Création d’environnements
Toutes les ressources Azure doivent être créées dans un [groupe de ressources Azure](resource-group-overview.md). Les groupes de ressources vous permettent de regrouper des ressources Azure afin de pouvoir les gérer de façon collective. Des [autorisations](./active-directory/role-based-access-built-in-roles.md) peuvent être affectées aux groupes de ressources. Ainsi, des personnes spécifiques au sein de votre organisation peuvent les créer, les modifier, les supprimer ou les afficher, de même que les ressources qu’ils contiennent. Les informations d’alerte et de facturation pour les ressources dans le groupe de ressources sont disponibles dans le [portail Azure](https://portal.azure.com). Des groupes de ressources sont créés dans une [région](https://azure.microsoft.com/regions/) Azure. Dans cet article, toutes les ressources sont créées dans la région du Centre des États-Unis. Lorsque vous commencez à créer des environnements réels, vous choisissez la région qui répond le mieux à vos besoins.

Créez des groupes de ressources pour chaque environnement en utilisant l’une des méthodes ci-dessous. Les deux méthodes permettent d’obtenir le même résultat.

###Interface de ligne de commande Microsoft Azure (CLI)

Vérifiez que l’interface de ligne de commande est bien [installée](xplat-cli-install.md) sur un ordinateur Windows, OS X ou Linux et que votre[compte Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md) (également appelé compte professionnel ou scolaire) est bien [connecté](xplat-cli-connect.md) à votre abonnement Azure. À partir de la ligne de commande de l’interface de ligne de commande, tapez la commande suivante pour créer le groupe de ressources pour l’environnement de développement.

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

###PowerShell

Vérifiez qu’Azure PowerShell 1.01 ou version ultérieure est installé sur un ordinateur Windows et que votre [compte Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md) (également appelé compte professionnel ou scolaire) est bien associé à votre abonnement Azure.comme indiqué dans l’article [Installation et configuration d’Azure PowerShell](powershell-install-configure.md). À partir d’une invite de commande PowerShell, tapez la commande suivante pour créer le groupe de ressources pour l’environnement de développement.

	New-AzureRmResourceGroup -Name TestApp1-Development -Location "Central US"

Si elle réussit, la commande retourne les éléments suivants :

	ResourceGroupName : TestApp1-Development
	Location          : centralus
	ProvisioningState : Succeeded
	Tags              :
	ResourceId        : /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development

Pour créer le groupe de ressources pour l’environnement de test, tapez la commande suivante :

	New-AzureRmResourceGroup -Name TestApp1-Test -Location "Central US"

Pour créer le groupe de ressources pour l’environnement de préproduction, tapez la commande suivante :

	New-AzureRmResourceGroup -Name TestApp1-Pre-Production -Location "Central US"

###Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte [Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md) (également appelé compte professionnel ou scolaire). Cliquez sur Nouveau --> Gestion --> Groupe de ressources et saisissez « TestApp1-Development » dans la zone de nom de groupe de ressources, sélectionnez votre abonnement, puis sélectionnez « Centre des États-Unis » dans la zone de groupe de ressources comme indiqué dans l’illustration ci-dessous. ![Portail](./media/solution-dev-test-environments/rgcreate.png)
2. Cliquez sur le bouton Créer pour créer le groupe de ressources.
3. Cliquez sur Parcourir, faites défiler la liste jusqu’à Groupes de ressources et cliquez sur Groupes de ressources, comme indiqué ci-dessous. ![Portail](./media/solution-dev-test-environments/rgbrowse.png)
4. Après avoir cliqué sur les groupes de ressources, le panneau Groupes de ressources contenant le nouveau groupe de ressources s’affiche. ![Portail](./media/solution-dev-test-environments/rgview.png)
5. Créez les groupes de ressources TestApp1-Test et TestApp1-Pré-Production comme vous avez créé le groupe de ressources de TestApp1-Développement ci-dessus.

##Déployer des ressources vers des environnements

Déployez des ressources Azure dans les groupes de ressources pour chaque environnement en utilisant le fichier modèle de solution et les fichiers de paramètre pour chaque environnement à l’aide d’une des méthodes ci-dessous. Les deux méthodes permettent d’obtenir le même résultat.

###Interface de ligne de commande Microsoft Azure (CLI)

À partir de la ligne de commande de l’interface de ligne de commande, tapez la commande ci-dessous pour déployer des ressources dans le groupe de ressources créé pour l’environnement de développement, en remplaçant [chemin d’accès] par le chemin d’accès aux fichiers que vous avez enregistrés dans les étapes précédentes.

	azure group deployment create -g TestApp1-Development -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Development.json 

Après avoir affiché un message « Waiting for deployment to complete » pendant plusieurs minutes, la commande, si elle réussit, retourne les éléments suivants :

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
	data:    siteName                       String        TestApp1DevApp
	data:    hostingPlanName                String        TestApp1DevPlan
	data:    siteLocation                   String        Central US
	data:    sku                            String        Free
	data:    workerSize                     String        0
	data:    serverName                     String        testapp1devsrv
	data:    serverLocation                 String        Central US
	data:    administratorLogin             String        testapp1Admin
	data:    administratorLoginPassword     SecureString  undefined
	data:    databaseName                   String        testapp1devdb
	data:    collation                      String        SQL_Latin1_General_CP1_CI_AS
	data:    edition                        String        Standard
	data:    maxSizeBytes                   String        1073741824
	data:    requestedServiceObjectiveName  String        S0
	info:    group deployment create command OKx

Si la commande échoue, résolvez tous les messages d’erreur et réessayez. Les problèmes courants sont l’utilisation de valeurs de paramètres qui ne respectent pas les contraintes d’affectation de noms des ressources Azure. D'autres conseils de dépannage sont disponibles dans l'article [Résolution des problèmes liés aux déploiements de groupes de ressources dans Azure](./resource-manager-troubleshoot-deployments-cli.md).

À partir de la ligne de commande de l’interface de ligne de commande, tapez la commande ci-dessous pour déployer des ressources dans le groupe de ressources créé pour l’environnement de test, en remplaçant [chemin d’accès] par le chemin d’accès aux fichiers que vous avez enregistrés dans les étapes précédentes.

	azure group deployment create -g TestApp1-Test -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Test.json

À partir de la ligne de commande de l’interface de ligne de commande, tapez la commande ci-dessous pour déployer des ressources dans le groupe de ressources créé pour l’environnement de préproduction, en remplaçant [chemin d’accès] par le chemin d’accès aux fichiers que vous avez enregistrés dans les étapes précédentes.

	azure group deployment create -g TestApp1-Pre-Production -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Pre-Production.json
  
###PowerShell

À partir d’une invite de commande Azure PowerShell (version 1.01 ou ultérieure), tapez la commande ci-dessous pour déployer des ressources dans le groupe de ressources créé pour l’environnement de développement, en remplaçant [chemin d’accès] par le chemin d’accès aux fichiers que vous avez enregistrés dans les étapes précédentes.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Development -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Development.json -Name Deployment1 

Une fois que vous voyez un curseur clignotant pendant quelques minutes, la commande, si elle réussit, retourne les éléments suivants :

	DeploymentName    : Deployment1
	ResourceGroupName : TestApp1-Development
	ProvisioningState : Succeeded
	Timestamp         : XX/XX/XXXX 2:44:48 PM
	Mode              : Incremental
	TemplateLink      : 
	Parameters        : 
	                    Name             Type                       Value     
	                    ===============  =========================  ==========
	                    siteName         String                     TestApp1DevApp
	                    hostingPlanName  String                     TestApp1DevPlan
	                    siteLocation     String                     Central US
	                    sku              String                     Free      
	                    workerSize       String                     0         
	                    serverName       String                     testapp1devsrv
	                    serverLocation   String                     Central US
	                    administratorLogin  String                     testapp1Admin
	                    administratorLoginPassword  SecureString                         
	                    databaseName     String                     testapp1devdb
	                    collation        String                     SQL_Latin1_General_CP1_CI_AS
	                    edition          String                     Standard  
	                    maxSizeBytes     String                     1073741824
	                    requestedServiceObjectiveName  String                     S0        
	                    
	Outputs           :

  Si la commande échoue, résolvez tous les messages d’erreur et réessayez. Les problèmes courants sont l’utilisation de valeurs de paramètres qui ne respectent pas les contraintes d’affectation de noms des ressources Azure. D'autres conseils de dépannage sont disponibles dans l'article [Résolution des problèmes liés aux déploiements de groupes de ressources dans Azure](./resource-manager-troubleshoot-deployments-powershell.md).

  À partir d’une invite de commande PowerShell, tapez la commande ci-dessous pour déployer des ressources dans le groupe de ressources créé pour l’environnement de test, en remplaçant [chemin d’accès] par le chemin d’accès aux fichiers que vous avez enregistrés dans les étapes précédentes.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Test -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Test.json -Name Deployment1

  À partir d’une invite de commande PowerShell, tapez la commande ci-dessous pour déployer des ressources dans le groupe de ressources créé pour l’environnement de préproduction, en remplaçant [chemin d’accès] par le chemin d’accès aux fichiers que vous avez enregistrés dans les étapes précédentes.

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Pre-Production -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Pre-Production.json -Name Deployment1

Les fichiers de modèles et de paramètres peuvent être créés, dotés d’une version et gérés avec le code de votre application dans un système de contrôle de code source. Vous pouvez également enregistrer les commandes ci-dessus dans des fichiers de script et les enregistrer avec votre code.

> [AZURE.NOTE] Vous pouvez déployer le modèle dans Azure directement en cliquant sur le bouton Déployer dans Azure dans l’article [Mettre en service une application web avec une base de données SQL](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/). Il peut se révéler utile d’en savoir plus sur les modèles, mais cela ne vous permettra pas de modifier, de créer une version et d’enregistrer votre modèle et vos valeurs de paramètres avec le code de votre application, et cet article n’aborde pas d’autres détails de cette méthode.

## Gestion d’environnements
Au cours du développement, la configuration des ressources Azure dans les différents environnements peut être modifiée de manière incohérente, intentionnellement ou accidentellement. Cela peut entraîner des dépannages et des résolutions de problèmes inutiles pendant le cycle de développement des applications.

1. Modifiez les environnements en ouvrant le [portail Azure](https://portal.azure.com).
2. Connectez-vous avec le même compte que celui que vous avez utilisé pour effectuer les étapes ci-dessus.
3. Comme le montre l’image ci-dessous, cliquez sur Parcourir --> Groupes de ressources (vous devrez peut-être faire défiler les Groupes de ressources). ![Portail](./media/solution-dev-test-environments/rgbrowse.png)
4. Une fois que vous avez cliqué sur les groupes de ressources dans l’image ci-dessus, le panneau Groupes de ressources et les trois groupes de ressources que vous avez créés à l’étape précédente s’affichent, comme indiqué dans l’illustration ci-dessous. Cliquez sur le groupe de ressources TestApp1-Development. Le panneau répertoriant les ressources créées par le modèle lors du déploiement du groupe de ressources TestApp1-Development que vous avez effectué à l’étape précédente s’affiche. Supprimez la ressource d’application Web TestApp1DevApp en cliquant sur TestApp1DevApp dans le panneau du groupe de ressource TestApp1-Development, puis cliquez sur Supprimer dans le panneau application Web de TestApp1DevApp. ![Portail](./media/solution-dev-test-environments/portal2.png)
5. Cliquez sur Oui lorsque le portail vous demande si vous voulez vraiment supprimer la ressource. La fermeture, puis la réouverture du panneau du groupe de ressources TestApp1-Development permettent de l’afficher sans l’application Web que vous venez de supprimer. Le contenu du groupe de ressources est maintenant différent de ce qu’il devrait être. Vous pouvez poursuivre vos expériences en supprimant plusieurs ressources de plusieurs groupes de ressources, voire en modifiant les paramètres de configuration de certaines ressources. Au lieu d’utiliser le portail Azure pour supprimer une ressource d’un groupe de ressources, vous pouvez utiliser la commande PowerShell [Remove-AzureResource](https://msdn.microsoft.com/library/azure/dn757676.aspx) ou la commande « azure resource delete » à partir de l’interface de ligne de commande pour effectuer la même tâche.
6. Pour replacer toutes les ressources et la configuration supposées être dans les groupes de ressources dans l’état dans lequel elles devraient être, redéployez les environnements vers les groupes de ressources en utilisant les commandes que vous avez utilisées dans la section [Déployer des ressources dans les environnements](#deploy-resources-to-environments), mais remplacez « Deployment1 » par « Deployment2 ».
7.  Comme indiqué à la section Résumé du panneau TestApp1-Development dans l’image affichée à l’étape 4, vous verrez que l’application Web que vous avez supprimée dans le portail à l’étape précédente existe encore, au même titre que les autres ressources que vous avez supprimées. Si vous avez modifié la configuration de toutes les ressources, vous pouvez également vérifier qu’elles ont été ramenées aux valeurs des fichiers de paramètres également. L’un des avantages du déploiement de vos environnements avec les modèles Azure Resource Manager est que vous pouvez facilement effectuer de nouveaux déploiements vers un état connu à tout moment.
8. Si vous cliquez sur le texte sous « Dernier déploiement » dans l’image ci-dessous, vous verrez un volet qui affiche l’historique de déploiement du groupe de ressources. Étant donné que vous avez utilisé les noms « Deployment1 » pour le premier déploiement et « Deployment2 » pour le deuxième déploiement, vous aurez deux entrées. La sélection d’un déploiement affiche un panneau qui présente les résultats pour chaque déploiement. ![Portail](./media/solution-dev-test-environments/portal3.png)



## Suppression d’environnements
Une fois que vous aurez fini d’utiliser un environnement, vous souhaiterez le supprimer afin d’éviter des frais d’utilisation de ressources Azure dont vous ne vous servez plus. La suppression d’environnements est encore plus facile que leur création. Au cours des étapes précédentes, des groupes de ressources Azure ont été créés pour chaque environnement, puis des ressources ont été déployées dans les groupes de ressources.

Supprimez les environnements à l’aide de l’une des méthodes ci-dessous. Les deux méthodes permettent d’obtenir le même résultat.

### Interface de ligne de commande Azure

À partir d’une invite de l’interface de ligne de commande, tapez les éléments suivants :

	azure group delete "TestApp1-Development"

Lorsque vous y êtes invité, entrez y et appuyez sur Entrée pour supprimer l’environnement de développement et toutes ses ressources. Après quelques minutes, la commande retourne les éléments suivants :

	info:    group delete command OK

À partir d’une invite de l’interface de ligne de commande, tapez les éléments suivants pour supprimer les environnements restants :

	azure group delete "TestApp1-Test"
	azure group delete "TestApp1-Pre-Production"
  
### PowerShell

À partir d’une invite de commandes Azure PowerShell (version 1.01 ou une version ultérieure), saisissez la commande suivante pour supprimer le groupe de ressources et tout son contenu.

	Remove-AzureRmResourceGroup -Name TestApp1-Development

Lorsque vous y êtes invité, si vous êtes sûr de vouloir supprimer le groupe de ressources, saisissez Y, puis tapez sur la touche Entrée.

Tapez les éléments suivants pour supprimer les environnements restants :

	Remove-AzureRmResourceGroup -Name TestApp1-Test
	Remove-AzureRmResourceGroup -Name TestApp1-Pre-Production

### Portail Azure

1. Dans le portail Azure, accédez aux groupes de ressources comme vous l’avez fait lors des étapes précédentes.
2. Sélectionnez le groupe de ressources dTestApp1-Development, puis cliquez sur Supprimer dans le panneau du groupe de ressources TestApp1-Development. Un nouveau panneau s’affiche. Saisissez le nom de groupe de ressources, puis cliquez sur le bouton Supprimer. ![Portail](./media/solution-dev-test-environments/rgdelete.png)
3. Supprimez les groupes de ressources TestApp1-Test et TestApp1-Pre-Production de la même façon que vous avez supprimé le groupe de ressources de TestApp1-Développement.

Quelle que soit la méthode que vous utilisez, les groupes de ressources et toutes les ressources qu’ils contiennent n’existent plus et vous n’êtes plus facturé pour les ressources.

Pour réduire les dépenses liées à l'utilisation des ressources Azure pendant le développement des applications, vous pouvez utiliser [Azure Automation](automation/automation-intro.md) pour planifier des tâches qui :

- arrêtent les machines virtuelles à la fin de chaque journée et les redémarrent au début de chaque journée.
- suppriment des environnements entiers à la fin de chaque journée et les recréent au début de chaque journée.
 
Maintenant que vous avez pu constater comme il est facile de créer, de gérer et de supprimer les environnements de développement et de test, vous pouvez en savoir plus sur ce que vous venez de faire en étudiant plus en détail les étapes ci-dessus et en lisant les références contenues dans cet article.

## Étapes suivantes

- [Déléguer le contrôle administratif](./active-directory/role-based-access-control-configure.md) à différentes ressources dans chaque environnement en affectant des groupes ou des utilisateurs Microsoft Azure AD à des rôles spécifiques qui ont la possibilité d'exécuter un sous-ensemble d'opérations sur des ressources Azure.
- [Affecter des balises](resource-group-using-tags.md) aux groupes de ressources pour chaque environnement et/ou aux ressources individuelles. Vous pouvez ajouter une balise « Environment » à vos groupes de ressources et définir sa valeur de sorte qu’elle corresponde aux noms de votre environnement. Les balises peuvent être particulièrement utiles si vous devez organiser les ressources à des fins de facturation ou de gestion.
- Surveiller les alertes et la facturation pour les ressources du groupe de ressources dans le [portail Azure](https://portal.azure.com).

<!---HONumber=AcomDC_0907_2016-->
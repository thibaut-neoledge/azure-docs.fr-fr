<properties
	pageTitle="Automatisation DocumentDB - Resource Manager - CLI | Microsoft Azure"
	description="Utilisez les modèles ou l’interface de ligne de commande Azure Resource Manager pour déployer un compte de base de données DocumentDB. DocumentDB est une base de données NoSQL basée sur le cloud pour les données JSON."
	services="documentdb"
	authors="mimig1"
	manager="jhubbard"
	editor=""
    tags="azure-resource-manager"
	documentationCenter=""/>


<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="mimig"/>

# Automatiser la création du compte de base de données DocumentDB à l’aide de modèles Azure Resource Manager et de l’interface de ligne de commande Azure

> [AZURE.SELECTOR]
- [Azure Portal](documentdb-create-account.md)
- [Azure CLI and ARM](documentdb-automation-resource-manager-cli.md)

Cet article vous montre comment créer un compte DocumentDB à l’aide de modèles Azure Resource Manager ou de l’interface de ligne de commande Azure (CLI).

- [Créer un compte DocumentDB à l’aide de l’interface de ligne de commande (CLI)](#quick-create-documentdb-account)
- [Créer un compte DocumentDB à l’aide d’un modèle ARM](#deploy-documentdb-from-a-template)

Les comptes de base de données DocumentDB sont actuellement la seule ressource DocumentDB qui peut être créée à l’aide de modèles ARM et de l’interface CLI Azure.

## Préparation

Avant de pouvoir utiliser l'interface de ligne de commande Azure avec des groupes de ressources Azure, vous devez avoir la version adéquate de l'interface de ligne de commande Azure, ainsi qu'un compte Azure. Si vous ne disposez pas de l’interface de ligne de commande Azure, [installez-la](../xplat-cli-install.md).

### Mettre à jour votre version de l’interface de ligne de commande Azure

À l’invite de commandes, tapez `azure --version` pour savoir si vous avez déjà installé la version 0.9.11 ou une version ultérieure.

	azure --version
    0.9.11 (node: 0.12.7)

Si vous disposez d’une version antérieure à la version 0.9.11, vous devez [installer l’interface de ligne de commande Azure](../xplat-cli-install.md) ou la mettre à jour en utilisant l’un des programmes d’installation natifs. Vous pouvez également utiliser **npm** en tapant `npm update -g azure-cli` pour la mettre à jour ou `npm install -g azure-cli` pour l’installer.

### Configurer votre compte et votre abonnement Microsoft Azure

Si vous ne possédez pas déjà un abonnement Azure, mais que vous avez un abonnement Visual Studio, vous pouvez activer vos [avantages abonnés Visual Studio](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Ou vous pouvez vous inscrire à un [essai gratuit](http://azure.microsoft.com/pricing/free-trial/).

Vous devez disposer d’un compte professionnel ou scolaire, ou bien d’une identité du compte Microsoft, pour utiliser des modèles de gestion des ressources Azure. Si vous avez l’un de ces comptes, tapez la commande suivante.

	azure login

Ce qui génère la sortie suivante :

    info:    Executing command login
    |info:    To sign in, use a web browser to open the page https://aka.ms/devicelogin. 
    Enter the code E1A2B3C4D to authenticate. If you're signing in as an Azure
    AD application, use the --username and --password parameters.

> [AZURE.NOTE]Si vous n’avez pas de compte Azure, un message d’erreur s’affiche, indiquant que vous avez besoin d’un autre type de compte. Pour en créer un à partir de votre compte Azure actuel, consultez [Création d’une identité professionnelle ou scolaire dans Azure Active Directory](../resource-group-create-work-id-from-personal.md).

Ouvrez [https://aka.ms/devicelogin](https://aka.ms/devicelogin) dans un navigateur, puis entrez le code fourni dans la sortie de commande.

![Capture d’écran présentant l’écran de connexion d’appareil de l’interface de ligne de commande Microsoft Azure](media/documentdb-automation-resource-manager-cli/azure-cli-login-code.png)

Une fois que vous avez entré le code, sélectionnez l’identité que vous voulez utiliser dans le navigateur, puis indiquez votre nom d’utilisateur et votre mot de passe, si nécessaire.

![Capture d’écran montrant où sélectionner le compte d’identité Microsoft associé à l’abonnement que vous voulez utiliser](media/documentdb-automation-resource-manager-cli/identity-cli-login.png)

L’écran de confirmation suivant s’affiche lorsque vous êtes connecté avec succès. Vous pouvez alors fermer la fenêtre du navigateur.

![Capture d’écran présentant la confirmation de la connexion à l’interface de ligne de commande interplateforme Microsoft Azure](media/documentdb-automation-resource-manager-cli/login-confirmation.png)

L’interface de commande fournit également la sortie suivante.

    -info:    Added subscription Visual Studio Ultimate with MSDN
	+
	info:    login command OK

Outre la méthode de connexion interactive décrite ici, d’autres méthodes de connexion à l’interface de ligne de commande Azure (Azure CLI) sont disponibles. Pour plus d’informations sur les autres méthodes et obtenir des informations sur la gestion de plusieurs inscriptions, consultez [Se connecter à un abonnement Azure à partir de l’interface de ligne de commande Azure (Azure CLI)](../xplat-cli-connect.md).

### Passer en mode de groupe de ressources d’interface de ligne de commande Microsoft Azure

Par défaut, l’interface de ligne de commande Azure démarre en mode Gestion des services (mode **asm**). Tapez la commande suivante pour basculer en mode de groupe de ressources.

	azure config mode arm

Ce qui fournit la sortie suivante :

	info:    New mode is arm

Vous pouvez revenir à la configuration des commandes par défaut en tapant `azure config mode asm`.

## <a id="quick-create-documentdb-account"></a>Tâche : Créer un compte DocumentDB à l’aide de l’interface de ligne de commande (CLI)

Utilisez les instructions de cette section pour créer un compte DocumentDB avec l’interface de ligne de commande Azure.

### Étape 1 : créer ou récupérer votre groupe de ressources

Pour créer un compte DocumentDB, vous avez tout d’abord besoin d’un groupe de ressources. Si vous connaissez déjà le nom du groupe de ressources que vous voulez utiliser, passez à l’[étape 2](#create-documentdb-account-cli).

Pour passer en revue la liste de tous vos groupes de ressources actuels, exécutez la commande suivante et notez le nom du groupe de ressources que vous voulez utiliser :

    azure group list

Pour créer un groupe de ressources, exécutez la commande suivante, et spécifiez le nom du nouveau groupe de ressources à créer, ainsi que la région dans laquelle créer le groupe de ressources :

	azure group create <resourcegroupname> <location>

Par exemple :

	azure group create new_res_group westus

Ce qui génère la sortie suivante :

    info:    Executing command group create
    + Getting resource group new_res_group
    + Creating resource group new_res_group
    info:    Created resource group new_res_group
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group
    data:    Name:                new_res_group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

Si vous rencontrez des erreurs, consultez la section [Dépannage](#troubleshooting).

### <a id="create-documentdb-account-cli"></a> Étape 2 : créer un compte DocumentDB à l’aide de l’interface de ligne de commande

Créez un compte DocumentDB dans le groupe de ressources nouveau ou existant en entrant la commande suivante à l’invite de commandes :

> [AZURE.TIP]Si vous exécutez cette commande dans Azure PowerShell ou Windows PowerShell, une erreur relative à un jeton inattendu s’affiche. Exécutez plutôt cette commande à l’invite de commandes Windows.

    azure resource create -g <resourceGroupName> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o "2015-04-08" -l <databaseaccountlocation> -p "{"databaseAccountOfferType":"Standard"}" 

Par exemple :

    azure resource create -g new_res_group -n samplecliacct -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08  -l westus -p "{"databaseAccountOfferType":"Standard"}"

Ce qui génère la sortie suivante car votre nouveau compte est configuré :

    info:    Executing command resource create
    + Getting resource samplecliacct
    + Creating resource samplecliacct
    info:    Resource samplecliacct is updated
    data:
    data:    Id:        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group/providers/Microsoft.DocumentDB/databaseAccounts/samplecliacct
    data:    Name:      samplecliacct
    data:    Type:      Microsoft.DocumentDB/databaseAccounts
    data:    Parent:
    data:    Location:  West US
    data:    Tags:
    data:
    info:    resource create command OK

Si vous rencontrez des erreurs, consultez la section [Dépannage](#troubleshooting).

Une fois que la commande a renvoyé un résultat, le compte est dans l’état **Création** pendant quelques minutes, avant de passer à l’état **En ligne** dans lequel il est prêt à être utilisé. Vous pouvez vérifier l’état du compte sur le panneau **Comptes DocumentDB** du [portail Azure](https://portal.azure.com).

## <a id="deploy-documentdb-from-a-template"></a>Tâche : créer un compte DocumentDB à l’aide d’un modèle ARM

Utilisez les instructions de cette section pour créer un compte DocumentDB à l’aide d’un modèle ARM (Azure Resource Manager) et un fichier de paramètres facultatifs, tous deux étant des fichiers JSON. L’utilisation d’un modèle vous permet de décrire exactement ce que vous le voulez et de le répéter sans erreurs.

### Présentation des modèles ARM et des groupes de ressources

La plupart des applications sont basées sur une combinaison de différents types de ressources (par exemple, un ou plusieurs comptes DocumentDB, des comptes de stockage, un réseau virtuel ou un réseau de distribution de contenu). L’API de gestion des services Azure par défaut et le portail Azure présentaient ces éléments en suivant une approche au cas par cas, ce qui nécessitait un déploiement et une gestion propres à chaque service (ou l’obtention d’outils supplémentaires pour le faire) et interdisait l’usage d’une seule unité logique de déploiement.

Les *modèles Azure Resource Manager* permettent de regrouper le déploiement et la gestion de ces différentes ressources en une seule unité logique de déploiement de manière déclarative. Au lieu de devoir utiliser une suite de commandes pour indiquer à Azure les éléments à déployer les uns après les autres, il vous suffit de décrire l’intégralité de votre déploiement dans un fichier JSON, comprenant toutes vos ressources, ainsi que les configurations et les paramètres de déploiement leur étant associés, puis d’indiquer à Azure de déployer ces ressources en tant que groupe.

Pour plus d’informations sur les groupes de ressources Azure et ce qu’ils peuvent faire pour vous, consultez [Présentation d’Azure Resource Manager](../resource-group-overview.md). Si vous êtes intéressé par la création de modèles, consultez [Création de modèles Azure Resource Manager](../resource-group-authoring-templates.md).

### Étape 1 : créer un fichier de modèle et de paramètres

Créez un fichier de modèle local avec le contenu suivant. Nommez le fichier azuredeploy.json.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            }
        },
        "variables": { },
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "name": "[parameters('databaseAccountName')]",
                    "databaseAccountOfferType": "Standard"
                }
            }
        ]
    }

Ce modèle ne nécessite qu’un seul paramètre : le nom du compte de base de données à créer. L’emplacement du nouveau compte de base de données est défini sur le même emplacement que le groupe de ressources.

Étant donné que le modèle ne prend qu’un seul paramètre, vous pouvez entrer la valeur dans la ligne de commande ou créer un fichier de paramètres pour spécifier la valeur.

Pour créer un fichier de paramètres, copiez le contenu suivant dans un nouveau fichier et nommez ce fichier azuredeploy.parameters.json. Si vous prévoyez de spécifier le nom du compte de base de données à l’invite de commandes, vous pouvez continuer sans créer ce fichier.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            }
        }
    }

Dans le fichier azuredeploy.parameters.json, mettez à jour la valeur « samplearmacct » en la remplaçant par le nom de base de données à utiliser, puis enregistrez le fichier.

> [AZURE.TIP]Les noms de comptes de base de données ne peuvent contenir que des minuscules, des chiffres et le caractère « - », et doivent comporter entre 3 et 50 caractères.

### Étape 2 : créer ou récupérer votre groupe de ressources

Pour créer un compte DocumentDB, vous avez tout d’abord besoin d’un groupe de ressources. Si vous connaissez déjà le nom du groupe de ressources que vous voulez utiliser, passez à l’[étape 3](#create-account-from-template).

Pour passer en revue la liste de tous vos groupes de ressources actuels, exécutez la commande suivante et notez le nom du groupe de ressources que vous voulez utiliser :

    azure group list

Pour créer un groupe de ressources, exécutez la commande suivante, et spécifiez le nom du nouveau groupe de ressources à créer, ainsi que la région dans laquelle créer le groupe de ressources :

	azure group create <resourcegroupname> <location>

Par exemple :

	azure group create new_res_group westus

Ce qui génère la sortie suivante :

    info:    Executing command group create
    + Getting resource group new_res_group
    + Creating resource group new_res_group
    info:    Created resource group new_res_group
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group
    data:    Name:                new_res_group
    data:    Location:            West US
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

Si vous rencontrez des erreurs, consultez la section [Dépannage](#troubleshooting).

### <a id="create-account-from-template"></a>Étape 3 : créer un compte DocumentDB à l’aide d’un modèle ARM

Pour créer un compte DocumentDB dans votre groupe de ressources, exécutez la commande suivante et indiquez le chemin du fichier de modèle, le chemin du fichier de paramètres ou la valeur du paramètre, le nom du groupe de ressources dans lequel effectuer le déploiement et un nom du déploiement (-n est facultatif).

Pour utiliser un fichier de paramètres :

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

Par exemple :

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

OU, pour spécifier le paramètre de nom de compte de base de données sans un fichier de paramètres et être plutôt invité à entrer la valeur, exécutez la commande suivante :

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

Voici un exemple (qui affiche l’invite et une entrée pour un compte de base de données nommé new\_db\_acct) :

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: newarmacct

Comme le compte est configuré, vous recevez les informations suivantes :

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "azuredeploy"
    + Waiting for deployment to complete
    data:    DeploymentName     : azuredeploy
    data:    ResourceGroupName  : new_res_group
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-11-30T18:50:23.6300288Z
    data:    Mode               : Incremental
    data:    Name                 Type    Value
    data:    -------------------  ------  ------------------
    data:    databaseAccountName  String  newarmacct
    data:    location             String  West US
    info:    group deployment create command OK

Si vous rencontrez des erreurs, consultez la section [Dépannage](#troubleshooting).

Une fois que la commande a renvoyé un résultat, le compte est dans l’état **Création** pendant quelques minutes, avant de passer à l’état **En ligne** dans lequel il est prêt à être utilisé. Vous pouvez vérifier l’état du compte sur le panneau **Comptes DocumentDB** du [portail Azure](https://portal.azure.com).

## Résolution de problèmes

Si vous recevez des erreurs telles que `Deployment provisioning state was not successful` lors de la création de votre groupe de ressources ou de votre compte de base de données, utilisez la commande suivante pour afficher le journal du groupe de ressources.

    azure group log show <resourcegroupname> --last-deployment

Par exemple :

    azure group log show new_res_group --last-deployment

Pour plus d’informations, consultez [Résolution des problèmes liés aux déploiements de groupes de ressources dans Azure](../resource-group-deploy-debug.md).

N’oubliez pas que les noms de comptes de base de données ne peuvent contenir que des minuscules, des chiffres et le caractère « - », et doivent comporter entre 3 et 50 caractères.

Les informations relatives aux erreurs sont également disponibles dans le portail Azure, comme illustré dans la capture d’écran suivante. Pour accéder aux informations relatives aux erreurs : cliquez sur Groupes de ressources dans la barre de lancement, sélectionnez le groupe de ressources concerné par l’erreur, puis, dans la zone Éléments principaux du panneau Groupe de ressources, cliquez sur la date du dernier déploiement. Ensuite, dans le panneau Historique du déploiement, sélectionnez le déploiement ayant échoué, puis, dans le panneau Déploiement, cliquez sur le détail de l’opération marqué d’un point d’exclamation rouge. Le message d’état correspondant au déploiement ayant échoué s’affiche dans le panneau Détails de l’opération.

![Capture d’écran du portail Azure montrant comment accéder au message d’erreur de déploiement](media/documentdb-automation-resource-manager-cli/portal-troubleshooting-deploy.png)

## Étapes suivantes

Maintenant que vous disposez d’un compte DocumentDB, l’étape suivante consiste à créer une base de données DocumentDB. Pour créer une base de données, utilisez au choix :

- le portail Azure, tel que décrit dans la rubrique [Création d’une base de données DocumentDB avec le portail Azure](documentdb-create-database.md) ;
- les exemples .NET C# du projet [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) dans le dépôt [azure-documentdb-dotnet](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) sur GitHub ;
- les [kits de développement logiciel (SDK) de DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB possède les kits de développement logiciel (SDK) de .NET, Java, Python, Node.js et de l’API JavaScript. 

Après avoir créé la base de données, vous devez lui [ajouter une ou plusieurs collections](documentdb-create-collection.md), puis [ajouter des documents](documentdb-view-json-document-explorer.md) aux collections.

Une fois que chaque collection contient des documents, vous pouvez utiliser [DocumentDB SQL](documentdb-sql-query.md) pour [exécuter des requêtes](documentdb-sql-query.md#executing-queries) sur vos documents à l’aide de l’[Explorateur de requête](documentdb-query-collections-query-explorer.md) dans la version préliminaire du portail, de l’[API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou de l’un des [kits de développement logiciel](https://msdn.microsoft.com/library/azure/dn781482.aspx).

Pour en savoir plus sur DocumentDB, explorez les ressources suivantes :

-	[Parcours d’apprentissage de DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-	[Modèle de ressource et concepts de DocumentDB](documentdb-resources.md)

Pour connaître les modèles supplémentaires utilisables, consultez [Modèles de démarrage rapide Microsoft Azure](http://azure.microsoft.com/documentation/templates/).

<!---HONumber=AcomDC_1203_2015-->
<properties
	pageTitle="Personnaliser des clusters HDInsight à l'aide d’actions de script | Microsoft Azure"
	description="Découvrez comment personnaliser des clusters HDInsight à l'aide d'une action de script."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/02/2015"
	ms.author="larryfr"/>

# Personnalisation des clusters HDInsight à l'aide d'une action de script (Linux)

HDInsight fournit une option de configuration intitulée **Action de script**, qui appelle des scripts personnalisés qui définissent la personnalisation à effectuer sur le cluster pendant le processus de création. Ces scripts permettent d’installer des logiciels supplémentaires sur un cluster ou de modifier la configuration des applications d’un cluster.

> [AZURE.NOTE]Les informations présentes dans cet article sont spécifiques aux clusters HDInsight sous Linux. Pour obtenir une version de cet article spécifique aux clusters basés sur Windows, consultez [Personnaliser des clusters HDInsight à l’aide de l’action de script (Windows)](hdinsight-hadoop-customize-cluster.md).

## Action de script dans le processus de création de cluster

L’action de script est utilisée uniquement pendant la création d’un cluster. Le diagramme suivant illustre le moment de l’exécution de l’action de script pendant le processus de création :

![Personnalisation du cluster HDInsight et procédure de création d’un cluster][img-hdi-cluster-states]

Le script est exécuté pendant la configuration de HDInsight. À ce stade, le script est exécuté en parallèle sur tous les nœuds spécifiés dans le cluster, et s’exécute avec des privilèges d’administrateur complets sur les nœuds.

> [AZURE.NOTE]Étant donné que vous disposez de privilèges racine sur les nœuds pendant l’exécution du script, vous pouvez effectuer des opérations comme arrêter et démarrer des services, y compris des services liés à Hadoop. Si vous arrêtez les services, vous devez vous assurer que le service Ambari et d’autres services liés à Hadoop sont en cours d’exécution avant la fin de l’exécution du script. Ces services sont requis pour établir correctement l'intégrité et l'état du cluster pendant sa création.

Chaque cluster peut accepter plusieurs actions de script qui sont appelées dans l’ordre spécifié. Un script peut être exécuté sur le nœud principal, les nœuds worker ou les deux.

> [AZURE.IMPORTANT]Les actions de script doivent se terminer dans les 15 minutes, ou elles expirent.

## Exemple de script d’action de script

Les scripts d'action de script peuvent être utilisés à partir de la version préliminaire du portail Azure, d'Azure PowerShell ou du Kit de développement logiciel (SDK) .NET HDInsight. Cet article explique comment utiliser l'Action de Script à partir du portail. Pour savoir comment utiliser PowerShell et le Kit de développement .NETpour utiliser l'action de script, regardez les exemples répertoriés dans le tableau ci-dessous.

HDInsight propose plusieurs scripts pour installer les composants suivants sur des clusters HDInsight :

Nom | Script
----- | -----
**Installez Hue**. | https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/install-hue-uber-v01.sh. Consultez [Installer et utiliser Hue sur les clusters HDInsight](hdinsight-hadoop-hue-linux.md).
**Installation de Spark** | https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv02/spark-installer-v02.sh. Consultez [Installer et utiliser Spark sur les clusters HDInsight](hdinsight-hadoop-spark-install-linux.md).
**Installation de R** | https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh. Consultez [Installer et utiliser R sur les clusters HDInsight](hdinsight-hadoop-r-scripts-linux.md).
**Installation de Solr** | https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh. Consultez [Installer et utiliser Solr sur les clusters HDInsight](hdinsight-hadoop-solr-install-linux.md).
**Installation de Giraph** | https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh. Consultez [Installer et utiliser Giraph sur les clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md).

## Utilisez une Action de script à partir du portail Azure en version préliminaire

1. Démarrez la création d'un cluster comme décrit dans [Création de clusters Hadoop dans HDInsight](hdinsight-provision-clusters.md#portal).

2. Dans __Configuration facultative__, sur le panneau **Actions de script**, cliquez sur **ajouter l’action de script** pour fournir des informations sur l’action de script, comme illustré ci-dessous :

	![Utilisation d’une action de script pour personnaliser un cluster](./media/hdinsight-hadoop-customize-cluster-linux/HDI.CreateCluster.8.png "Utilisation d’une action de script pour personnaliser un cluster")

	| Propriété | Valeur |
	| -------- | ----- |
	| Nom | Indiquez un nom pour l'action de script. |
	| URI du script | Spécifiez l'URI du script appelé pour personnaliser le cluster. |
	| Head/Worker | Spécifiez les nœuds (**Head** ou **Worker** ou **ZooKeeper**) sur lesquels le script de personnalisation est exécuté. |
	| Paramètres | Spécifiez les paramètres, si le script le demande. |

	Appuyez sur ENTRÉE pour ajouter plusieurs actions de script et installer plusieurs composants sur le cluster.

3. Cliquez sur **Sélectionner** pour enregistrer la configuration d’action de script et poursuivre la création du cluster.

## Utilisez une action de Script à partir de modèles Azure Resource Manager

Dans cette section, nous utilisons des modèles Azure Resource Manager (ARM) pour créer un cluster HDInsight et une action de script pour installer des composants personnalisés (R, dans cet exemple) sur le cluster. Cette section fournit un exemple de modèle ARM pour créer un cluster utilisant l’action de script.

### Avant de commencer

* Pour plus d’informations sur la configuration d’un poste de travail pour exécuter des applets de commande HDInsight Powershell, consultez la rubrique [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).
* Pour obtenir des instructions sur la façon de créer des modèles ARM, consultez [modèles de programmation Azure Resource Manager](resource-group-authoring-templates.md).
* Si vous n’avez pas déjà utilisé Azure PowerShell avec Resource Manager, consultez [Utilisation d’Azure PowerShell avec Azure Resource Manager](powershell-azure-resource-manager).

### Création de clusters à l'aide d'une action de script

1. Copiez le modèle suivant vers un emplacement sur votre ordinateur. Ce modèle installe R sur le nœud principal, ainsi que des nœuds de travail dans le cluster. Vous pouvez également vérifier si le modèle JSON est valide. Collez le contenu dans votre modèle de [JSONLint](http://jsonlint.com/), un outil de validateur JSON en ligne.

			{
		    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
		    "contentVersion": "1.0.0.0",
		    "parameters": {
		        "clusterLocation": {
		            "type": "string",
		            "defaultValue": "West US",
		            "allowedValues": [ "West US" ]
		        },
		        "clusterName": {
		            "type": "string"
		        },
		        "clusterUserName": {
		            "type": "string",
		            "defaultValue": "admin"
		        },
		        "clusterUserPassword": {
		            "type": "securestring"
		        },
		        "sshUserName": {
		            "type": "string",
		            "defaultValue": "hdiuser"
		        },
		        "sshPassword": {
		            "type": "securestring"
		        },
		        "clusterStorageAccountName": {
		            "type": "string"
		        },
		        "clusterStorageAccountResourceGroup": {
		            "type": "string"
		        },
		        "clusterStorageType": {
		            "type": "string",
		            "defaultValue": "Standard_LRS",
		            "allowedValues": [
		                "Standard_LRS",
		                "Standard_GRS",
		                "Standard_ZRS"
		            ]
		        },
		        "clusterStorageAccountContainer": {
		            "type": "string"
		        },
		        "clusterHeadNodeCount": {
		            "type": "int",
		            "defaultValue": 1
		        },
		        "clusterWorkerNodeCount": {
		            "type": "int",
		            "defaultValue": 2
		        }
		    },
		    "variables": {
		    },
		    "resources": [
		        {
		            "name": "[parameters('clusterStorageAccountName')]",
		            "type": "Microsoft.Storage/storageAccounts",
		            "location": "[parameters('clusterLocation')]",
		            "apiVersion": "2015-05-01-preview",
		            "dependsOn": [ ],
		            "tags": { },
		            "properties": {
		                "accountType": "[parameters('clusterStorageType')]"
		            }
		        },
		        {
		            "name": "[parameters('clusterName')]",
		            "type": "Microsoft.HDInsight/clusters",
		            "location": "[parameters('clusterLocation')]",
		            "apiVersion": "2015-03-01-preview",
		            "dependsOn": [
		                "[concat('Microsoft.Storage/storageAccounts/', parameters('clusterStorageAccountName'))]"
		            ],
		            "tags": { },
		            "properties": {
		                "clusterVersion": "3.2",
		                "osType": "Linux",
		                "clusterDefinition": {
		                    "kind": "hadoop",
		                    "configurations": {
		                        "gateway": {
		                            "restAuthCredential.isEnabled": true,
		                            "restAuthCredential.username": "[parameters('clusterUserName')]",
		                            "restAuthCredential.password": "[parameters('clusterUserPassword')]"
		                        }
		                    }
		                },
		                "storageProfile": {
		                    "storageaccounts": [
		                        {
		                            "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
		                            "isDefault": true,
		                            "container": "[parameters('clusterStorageAccountContainer')]",
		                            "key": "[listKeys(resourceId(parameters('clusterStorageAccountResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).key1]"
		                        }
		                    ]
		                },
		                "computeProfile": {
		                    "roles": [
		                        {
		                            "name": "headnode",
		                            "targetInstanceCount": "[parameters('clusterHeadNodeCount')]",
		                            "hardwareProfile": {
		                                "vmSize": "Large"
		                            },
		                            "osProfile": {
		                                "linuxOperatingSystemProfile": {
		                                    "username": "[parameters('sshUserName')]",
		                                    "password": "[parameters('sshPassword')]"
		                                }
		                            },
		                            "scriptActions": [
		                                {
		                                    "name": "installR",
		                                    "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
		                                    "parameters": ""
		                                }
		                            ]
		                        },
		                        {
		                            "name": "workernode",
		                            "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
		                            "hardwareProfile": {
		                                "vmSize": "Large"
		                            },
		                            "osProfile": {
		                                "linuxOperatingSystemProfile": {
		                                    "username": "[parameters('sshUserName')]",
		                                    "password": "[parameters('sshPassword')]"
		                                }
		                            },
		                            "scriptActions": [
		                                {
		                                    "name": "installR",
		                                    "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
		                                    "parameters": ""
		                                }
		                            ]
		                        }
		                    ]
		                }
		            }
		        }
		    ],
		    "outputs": {
		        "cluster":{
		            "type" : "object",
		            "value" : "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
		        }
		    }
		}

2. Démarrer Azure PowerShell et se connecter à votre compte Azure. Une fois que vous avez entré vos informations d'identification, la commande retourne les informations relatives à votre compte.

		Add-AzureAccount

		Id                             Type       ...
		--                             ----
		someone@example.com            User       ...

3. Si vous avez plusieurs abonnements, fournissez l'ID d'abonnement que vous souhaitez utiliser pour le déploiement.

		Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

4. Basculez sur le module Azure Resource Manager.

		Switch-AzureMode AzureResourceManager

5. Si vous n'avez pas de groupe de ressources, créez-en un. Indiquez le nom du groupe de ressources et l'emplacement dont vous avez besoin pour votre solution. Un résumé du nouveau groupe de ressources est retourné.

		New-AzureResourceGroup -Name myresourcegroup -Location "West US"

		ResourceGroupName : myresourcegroup
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		            		Actions  NotActions
		            		=======  ==========
		            		*
		ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup


6. Pour créer un déploiement pour votre groupe de ressources, exécutez la commande **New-AzureResourceGroupDeployment** et indiquez les paramètres nécessaires. Les paramètres incluent un nom pour votre déploiement, le nom de votre groupe de ressources, le chemin d’accès ou l’URL du modèle que vous avez créé. Si votre modèle requiert n’importe quel paramètre, vous devez également transférer les paramètres. Dans ce cas, l’action de script pour installer R sur le cluster ne nécessite pas de paramètres.


		New-AzureResourceGroupDeployment -Name mydeployment -ResourceGroupName myresourcegroup -TemplateFile <PathOrLinkToTemplate>


	Vous serez invité à fournir des valeurs pour les paramètres définis dans le modèle.

7. Une fois le groupe de ressources déployé, un résumé du déploiement apparaît.

		  DeploymentName    : mydeployment
		  ResourceGroupName : myresourcegroup
		  ProvisioningState : Succeeded
		  Timestamp         : 8/17/2015 7:00:27 PM
		  Mode              : Incremental
		  ...

8. Si votre déploiement échoue, vous pouvez utiliser les applets de commande suivants pour obtenir des informations sur les échecs.

		Get-AzureResourceGroupLog -ResourceGroup myresourcegroup -Status Failed

	Pour obtenir des informations détaillées sur les échecs de déploiement, utilisez l’applet de commande suivant.

		Get-AzureResourceGroupLog -ResourceGroup myresourcegroup -Status Failed -DetailedOutput

## Utilisez une Action de Script PowerShell Azure

Dans cette section, nous allons utiliser l’applet de commande **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** pour appeler des scripts avec une action de script pour personnaliser un cluster. Avant de poursuivre, assurez-vous que vous avez installé et configuré Azure PowerShell. Pour plus d'informations sur la configuration d'un poste de travail pour exécuter des applets de commande HDInsight PowerShell, consultez la rubrique [Installation et configuration d'Azure PowerShell](../powershell-install-configure.md).

Procédez comme suit :

1. Ouvrez la console Azure PowerShell et déclarez les variables suivantes :

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
		$version = "<HDInsightClusterVersion>"          # HDInsight version, for example "3.1"

2. Spécifiez les valeurs de configuration telles que les nœuds du cluster et le stockage par défaut à utiliser.

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName

3. Utilisez l’applet de commande **Add-AzureHDInsightScriptAction** pour appeler le script. L’exemple suivant utilise le script pour installer R sur le cluster :

		# INVOKE THE SCRIPT USING THE SCRIPT ACTION
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install R"  -ClusterRoleCollection HeadNode,WorkerNode,ZookeeperNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh


	L’applet de commande **Add-AzureHDInsightScriptAction** reçoit les paramètres suivants :

	| Paramètre | Définition |
	| --------- | ---------- |
	| Configuration | Objet de configuration auquel des informations d'action de script sont ajoutées. |
	| Nom | Nom de l'action de script. |
	| ClusterRoleCollection | Spécifie les nœuds sur lesquels le script de personnalisation est exécuté. Les valeurs correctes sont **HeadNode** (pour une installation sur le nœud principal), le **WorkerNode** (pour une installation sur l’ensemble des nœuds de données) ou **ZookeeperNode** (pour installer le nœud zookeeper. Vous pouvez utiliser l’une ou l’autre de ces valeurs, ou les deux. |
	| Paramètres | Paramètres requis par le script. |
	| Uri | Spécifie l’URI du script qui est exécuté. |

4. Enfin, créez le cluster :

		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version

Lorsque vous y êtes invité, entrez les informations d'identification du cluster. La création du cluster peut prendre plusieurs minutes.

## Utilisez une action de Script dans le Kit de développement .NET HDInsight

Le Kit de développement logiciel (SDK) .NET HDInsight fournit des bibliothèques clientes qui facilitent l’utilisation d’HDInsight à partir d’une application .NET. Les étapes suivantes montrent comment utiliser un script pour personnaliser un cluster à partir du Kit de développement .NET HDInsight.

> [AZURE.IMPORTANT]Vous devez créer un certificat auto-signé, l’installer sur votre station de travail et le télécharger dans votre abonnement Azure. Pour obtenir des instructions, consultez la page [Création d'un certificat auto-signé](http://go.microsoft.com/fwlink/?LinkId=511138).


### Créer un projet Visual Studio


1. Créez une application console C# dans Visual Studio.
2. À partir de la **console du gestionnaire de package** Nuget, exécutez les commandes suivantes :

		Install-Package Microsoft.Azure.Common.Authentication -pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre

	Ces commandes ajoutent des bibliothèques .NET et leurs références nécessaires au projet Visual Studio en cours.

3. Ouvrez le fichier **Program.cs** et ajoutez les instructions using suivantes :

		using System;
		using System.Security;
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;

4. Remplacez le code dans la classe en procédant comme suit :

        private static HDInsightManagementClient _hdiManagementClient;

        private static Guid SubscriptionId = new Guid("<AZURE SUBSCRIPTION ID>");
        private const string ResourceGroupName = "<AZURE RESOURCEGROUP NAME>";

        private const string NewClusterName = "<HDINSIGHT CLUSTER NAME>";
        private const int NewClusterNumNodes = <NUMBER OF NODES>;
        private const string NewClusterLocation = "<LOCATION>";  // Must match the Azure Storage account location
        private const string NewClusterVersion = "3.2";
        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
        private const OSType NewClusterOSType = OSType.Windows;

        private const string ExistingStorageName = "<STORAGE ACCOUNT NAME>.blob.core.windows.net";
        private const string ExistingStorageKey = "<STORAGE ACCOUNT KEY>";
        private const string ExistingContainer = "<DEFAULT CONTAINER NAME>"; 

        private const string NewClusterUsername = "admin";
        private const string NewClusterPassword = "<HTTP USER PASSWORD>";

        private const string NewClusterSshUserName = "sshuser";
        private const string NewClusterSshPublicKey = @"---- BEGIN SSH2 PUBLIC KEY ----
			Comment: ""rsa-key-20150731""
			AAAAB3NzaC1yc2EAAAABJQAAAQEA4QiCRLqT7fnmUA5OhYWZNlZo6lLaY1c+IRsp
			gmPCsJVGQLu6O1wqcxRqiKk7keYq8bP5s30v6bIljsLZYTnyReNUa5LtFw7eauGr
			yVt3Pve6ejfWELhbVpi0iq8uJNFA9VvRkz8IP1JmjC5jsdnJhzQZtgkIrdn3w0e6
			WVfu15kKyY8YAiynVbdV51EB0SZaSLdMZkZQ81xi4DDtCZD7qvdtWEFwLa+EHdkd
			pzO36Mtev5XvseLQqzXzZ6aVBdlXoppGHXkoGHAMNOtEWRXpAUtEccjpATsaZhQR
			zZdZlzHduhM10ofS4YOYBADt9JohporbQVHM5w6qUhIgyiPo7w==
			---- END SSH2 PUBLIC KEY ----"; //replace the public key with your own

        private static void Main(string[] args)
        {
            var tokenCreds = GetTokenCloudCredentials();
            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);

            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);

            CreateCluster();
        }

        public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
        {
            var authFactory = new AuthenticationFactory();

            var account = new AzureAccount { Type = AzureAccount.AccountType.User };

            if (username != null && password != null)
                account.Id = username;

            var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

            var accessToken =
                authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
                    .AccessToken;

            return new TokenCloudCredentials(accessToken);
        }

        public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
        {
            return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
        }


        private static void CreateCluster()
        {
            var parameters = new ClusterCreateParameters
            {
                ClusterSizeInNodes = NewClusterNumNodes,
                Location = NewClusterLocation,
                ClusterType = NewClusterType,
                OSType = NewClusterOSType,
                Version = NewClusterVersion,

                DefaultStorageAccountName = ExistingStorageName,
                DefaultStorageAccountKey = ExistingStorageKey,
                DefaultStorageContainer = ExistingContainer,

                UserName = NewClusterUsername,
                Password = NewClusterPassword,
                SshUserName = NewClusterSshUserName,
        		SshPublicKey = NewClusterSshPublicKey
            };

            ScriptAction rScriptAction = new ScriptAction("Install R",
                new Uri("https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode,new System.Collections.Generic.List<ScriptAction> { rScriptAction});
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { rScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
        }
		
6. Remplacez les valeurs de membre de classe.

7. Appuyez sur **F5** pour exécuter l'application. Une fenêtre de console doit s'ouvrir et afficher l'état de l'application. Vous êtes invité à saisir les informations d’identification de votre compte Azure. La création d'un cluster HDInsight peut prendre plusieurs minutes.


## Prise en charge des logiciels open source utilisés sur les clusters HDInsight

Le service Microsoft Azure HDInsight est une plateforme flexible qui vous permet de créer des applications de données volumineuses (Big Data) dans le cloud à l’aide d’un écosystème de technologies open source articulées autour de Hadoop. Microsoft Azure fournit un niveau de support général pour les technologies open source, comme indiqué dans la section **Portée du support** du [site web FAQ du support Azure](http://azure.microsoft.com/support/faq/). Le service HDInsight fournit un niveau de support supplémentaire pour certains composants, comme indiqué ci-dessous.

Deux types de composant open source sont disponibles dans le service HDInsight :

- **Composants intégrés** : ces composants sont préinstallés sur les clusters HDInsight et fournissent la fonctionnalité principale du cluster. Par exemple, YARN ResourceManager, le langage de requête Hive (HiveQL) et la bibliothèque Mahout appartiennent à cette catégorie. Une liste complète des composants de cluster est disponible sur la page [Nouveautés des versions de cluster Hadoop fournies par HDInsight](hdinsight-component-versioning.md).

- **Composants personnalisés** : en tant qu’utilisateur du cluster, vous pouvez installer ou utiliser, dans votre charge de travail, tout composant qui est disponible dans la communauté ou que vous avez créé.

> [AZURE.WARNING]Les composants fournis avec le cluster HDInsight bénéficient d’une prise en charge totale, et le support Microsoft vous aidera à identifier et à résoudre les problèmes liés à ces composants.
>
> Les composants personnalisés bénéficient d'un support commercialement raisonnable pour vous aider à résoudre le problème. Cela signifie SOIT que le problème pourra être résolu, SOIT que vous serez invité à affecter les ressources disponibles pour les technologies Open Source. Vous pouvez, par exemple, utiliser de nombreux sites de communauté, comme le [forum MSDN sur HDInsight](https://social.msdn.microsoft.com/Forums/azure/fr-FR/home?forum=hdinsight) ou [http://stackoverflow.com](http://stackoverflow.com). En outre, les projets Apache ont des sites de projet sur [http://apache.org](http://apache.org) ; par exemple, [Hadoop](http://hadoop.apache.org/) ou [Spark](http://spark.apache.org/).

Le service HDInsight fournit plusieurs méthodes d’utilisation de ces composants personnalisés. Quel que soit le mode d’utilisation ou d’installation du composant sur le cluster, le même niveau de support s’applique. Vous trouverez, ci-dessous, la liste des méthodes les plus courantes d’utilisation des composants personnalisés sur des clusters HDInsight.

1. Envoi de travaux : il est possible d’envoyer des travaux Hadoop ou d’autres types de travaux au cluster qui exécute ou utilise des composants personnalisés.

2. Personnalisation de cluster : lors de la création du cluster, vous pouvez spécifier des paramètres supplémentaires et des composants personnalisés qui seront installés sur les nœuds de cluster.

3. Exemples : pour des composants personnalisés fréquemment utilisés, il arrive que Microsoft et d’autres éditeurs proposent des exemples illustrant leur utilisation sur les clusters HDInsight. Ces exemples sont fournis sans support.

## Résolution de problèmes

Vous pouvez utiliser l’interface utilisateur web de Ambari pour afficher les informations consignées par des scripts lors de la création du cluster.

1. Dans votre navigateur, accédez à https://CLUSTERNAME.azurehdinsight.net. Remplacez CLUSTERNAME par le nom de votre cluster HDInsight.

	Lorsque vous y êtes invité, saisissez le nom de compte (admin) et le mot de passe correspondant au cluster. Vous devrez peut-être saisir de nouveau les informations d’identification d’administrateur dans un formulaire web.

2. Dans la barre située en haut de la page, sélectionnez l’entrée __ops__. Cette opération permet d’afficher une liste des opérations en cours et précédentes effectuées sur le cluster via Ambari.

	![Barre de l’interface utilisateur web Ambari avec ops sélectionné](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Recherchez les entrées comportant __run\_customscriptaction__ dans la colonne __Opérations__. Ceux-ci sont créés lors de l’exécution des actions de Script.

	![Capture d’écran des opérations](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

	Sélectionnez cette entrée et explorez les liens pour afficher les sorties STDOUT et STDERR générées au moment où le script a été exécuté sur le cluster.

## Étapes suivantes

Consultez la rubrique suivante pour obtenir des informations et des exemples sur la création et l’utilisation de scripts afin de personnaliser un cluster :

- [Développer des scripts d’action de script pour HDInsight](hdinsight-hadoop-script-actions-linux.md)
- [Installer et utiliser Spark sur les clusters HDInsight](hdinsight-hadoop-spark-install-linux.md)
- [Installer et utiliser R sur les clusters HDInsight](hdinsight-hadoop-r-scripts-linux.md)
- [Installer et utiliser Solr sur les clusters HDInsight](hdinsight-hadoop-solr-install-linux.md)
- [Installer et utiliser Giraph sur les clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md)



[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Procédure de création d’un cluster"

<!---HONumber=Oct15_HO3-->
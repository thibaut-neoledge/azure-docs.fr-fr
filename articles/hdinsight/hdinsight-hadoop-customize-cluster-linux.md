<properties
	pageTitle="Personnaliser des clusters HDInsight à l'aide d’actions de script | Microsoft Azure"
	description="Découvrez comment ajouter des composants personnalisés à des clusters HDInsight basés sur Linux à l’aide des actions de script. Les actions de script sont des scripts Bash qui s'exécutent pendant la création du cluster et qui peuvent être utilisés pour personnaliser la configuration du cluster ou pour ajouter d'autres services et utilitaires comme Hue, Solr ou R."
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
	ms.date="02/16/2016"
	ms.author="larryfr"/>

# Personnalisation de clusters HDInsight basés sur Linux à l'aide d'une action de script

HDInsight fournit une option de configuration intitulée **Action de script**, qui appelle des scripts personnalisés qui définissent la personnalisation à effectuer sur le cluster pendant le processus de création. Ces scripts permettent d’installer des logiciels supplémentaires sur un cluster ou de modifier la configuration des applications d’un cluster.

> [AZURE.NOTE] Les informations présentes dans cet article sont spécifiques aux clusters HDInsight sous Linux. Pour obtenir une version de cet article spécifique aux clusters basés sur Windows, consultez [Personnaliser des clusters HDInsight à l’aide de l’action de script (Windows)](hdinsight-hadoop-customize-cluster.md).

## Action de script dans le processus de création de cluster

L’action de script est utilisée uniquement pendant la création d’un cluster. Le diagramme suivant illustre le moment de l’exécution de l’action de script pendant le processus de création :

![Personnalisation du cluster HDInsight et procédure de création d’un cluster][img-hdi-cluster-states]

Le script est exécuté pendant la configuration de HDInsight. À ce stade, le script est exécuté en parallèle sur tous les nœuds spécifiés dans le cluster, et s’exécute avec des privilèges d’administrateur complets sur les nœuds.

> [AZURE.NOTE] Étant donné que vous disposez de privilèges racine sur les nœuds pendant l’exécution du script, vous pouvez effectuer des opérations comme arrêter et démarrer des services, y compris des services liés à Hadoop. Si vous arrêtez les services, vous devez vous assurer que le service Ambari et d’autres services liés à Hadoop sont en cours d’exécution avant la fin de l’exécution du script. Ces services sont requis pour établir correctement l'intégrité et l'état du cluster pendant sa création.

Chaque cluster peut accepter plusieurs actions de script qui sont appelées dans l’ordre spécifié. Un script peut être exécuté sur le nœud principal, les nœuds worker ou les deux.

> [AZURE.IMPORTANT] Les actions de script doivent se terminer dans les 60 minutes, faute de quoi elles expirent. Lors de l’approvisionnement du nœud, le script est exécuté en même temps que les autres processus d'installation et de configuration. En raison de cette concurrence pour les ressources, par exemple au niveau du temps processeur ou de la bande passante, l’exécution du script risque de prendre plus de temps que dans votre environnement de développement.
> 
> Pour réduire le temps nécessaire à l’exécution du script, évitez les tâches comme le téléchargement et la compilation d’applications à partir de la source. Au lieu de cela, précompilez l'application et stockez le fichier binaire dans le stockage d’objets blob Azure afin de pouvoir le télécharger rapidement vers le cluster.


## Exemple de script d’action de script

Les scripts d’action de script peuvent être utilisés à partir du portail Azure, d’Azure PowerShell ou du Kit de développement logiciel (SDK) .NET HDInsight. Cet article explique comment utiliser l'Action de Script à partir du portail. Pour savoir comment utiliser PowerShell et le Kit de développement .NETpour utiliser l'action de script, regardez les exemples répertoriés dans le tableau ci-dessous.

HDInsight propose plusieurs scripts pour installer les composants suivants sur des clusters HDInsight :

Nom | Script
----- | -----
**Installez Hue.** | https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/install-hue-uber-v01.sh. Consultez [Installer et utiliser Hue sur les clusters HDInsight](hdinsight-hadoop-hue-linux.md).
**Installation de Spark** | https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv02/spark-installer-v02.sh. Consultez [Installer et utiliser Spark sur les clusters HDInsight](hdinsight-hadoop-spark-install-linux.md).
**Installation de R** | https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh. Consultez [Installer et utiliser R sur les clusters HDInsight](hdinsight-hadoop-r-scripts-linux.md).
**Installation de Solr** | https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh. Consultez [Installer et utiliser Solr sur les clusters HDInsight](hdinsight-hadoop-solr-install-linux.md).
**Installation de Giraph** | https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh. Consultez [Installer et utiliser Giraph sur les clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md).
| **Précharger les bibliothèques Hive** | https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh. Consultez [Ajouter les bibliothèques Hive sur des clusters HDInsight](hdinsight-hadoop-add-hive-libraries.md) |

## Utilisez une Action de script à partir du portail Azure

1. Démarrez la création d'un cluster comme décrit dans [Création de clusters Hadoop dans HDInsight](hdinsight-provision-clusters.md#portal).

2. Dans __Configuration facultative__, sur le panneau **Actions de script**, cliquez sur **ajouter l’action de script** pour fournir des informations sur l’action de script, comme illustré ci-dessous :

	![Utilisation d’une action de script pour personnaliser un cluster](./media/hdinsight-hadoop-customize-cluster-linux/HDI.CreateCluster.8.png)

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
* Pour obtenir des instructions sur la création de modèles ARM, consultez [Création de modèles Azure Resource Manager](../resource-group-authoring-templates.md).
* Si vous n’avez pas déjà utilisé Azure PowerShell avec Resource Manager, consultez [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md).

### Création de clusters à l'aide d'une action de script

1. Copiez le modèle suivant vers un emplacement sur votre ordinateur. Ce modèle installe R sur le nœud principal, ainsi que des nœuds de travail dans le cluster. Vous pouvez également vérifier si le modèle JSON est valide. Collez le contenu de votre modèle dans [JSONLint](http://jsonlint.com/), un outil de validation JSON en ligne.

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
		            "defaultValue": "username"
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

		Add-AzureRmAccount

		Id                             Type       ...
		--                             ----
		someone@example.com            User       ...

3. Si vous avez plusieurs abonnements, fournissez l’ID d’abonnement que vous souhaitez utiliser pour le déploiement.

		Select-AzureRmSubscription -SubscriptionID <YourSubscriptionId>

    > [AZURE.NOTE] Vous pouvez utiliser `Get-AzureRmSubscription` pour obtenir la liste de tous les abonnements associés à votre compte, y compris l'ID d'abonnement de chacun d'eux.

5. Si vous n’avez pas de groupe de ressources, créez-en un. Indiquez le nom du groupe de ressources et l'emplacement dont vous avez besoin pour votre solution. Un résumé du nouveau groupe de ressources est retourné.

		New-AzureRmResourceGroup -Name myresourcegroup -Location "West US"

		ResourceGroupName : myresourcegroup
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		            		Actions  NotActions
		            		=======  ==========
		            		*
		ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup


6. Pour créer un déploiement pour votre groupe de ressources, exécutez la commande **New-AzureRmResourceGroupDeployment** et indiquez les paramètres nécessaires. Les paramètres incluent un nom pour votre déploiement, le nom de votre groupe de ressources, le chemin d’accès ou l’URL du modèle que vous avez créé. Si votre modèle requiert n’importe quel paramètre, vous devez également transférer les paramètres. Dans ce cas, l’action de script pour installer R sur le cluster ne nécessite pas de paramètres.


		New-AzureRmResourceGroupDeployment -Name mydeployment -ResourceGroupName myresourcegroup -TemplateFile <PathOrLinkToTemplate>


	Vous serez invité à fournir des valeurs pour les paramètres définis dans le modèle.

7. Une fois le groupe de ressources déployé, un résumé du déploiement apparaît.

		  DeploymentName    : mydeployment
		  ResourceGroupName : myresourcegroup
		  ProvisioningState : Succeeded
		  Timestamp         : 8/17/2015 7:00:27 PM
		  Mode              : Incremental
		  ...

8. Si votre déploiement échoue, vous pouvez utiliser les applets de commande suivants pour obtenir des informations sur les échecs.

		Get-AzureRmResourceGroupDeployment -ResourceGroupName myresourcegroup -ProvisioningState Failed

## Utilisez une Action de Script PowerShell Azure

Dans cette section, nous allons utiliser l'applet de commande [Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) pour appeler des scripts avec une action de script pour personnaliser un cluster. Avant de poursuivre, assurez-vous que vous avez installé et configuré Azure PowerShell. Pour plus d'informations sur la configuration d'un poste de travail pour exécuter des applets de commande HDInsight PowerShell, consultez la rubrique [Installation et configuration d'Azure PowerShell](../powershell-install-configure.md).

Procédez comme suit :

1. Ouvrez la console Azure PowerShell et déclarez les variables suivantes :

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionId = "<SubscriptionId>"		# ID of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
        $resourceGroupName = "<ResourceGroupName>"      # The resource group that the HDInsight cluster will be created in

2. Spécifiez les valeurs de configuration telles que les nœuds du cluster et le stockage par défaut à utiliser.

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureRmSubscription -SubscriptionId $subscriptionId
		$config = New-AzureRmHDInsightClusterConfig
		$config.DefaultStorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccountKey=$storageAccountKey

3. Utilisez l'applet de commande **Add-AzureRmHDInsightScriptAction** pour appeler le script. L’exemple suivant utilise un script qui installe R sur le cluster :

		# INVOKE THE SCRIPT USING THE SCRIPT ACTION FOR HEADNODE AND WORKERNODE
		$config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install R"  -NodeType HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
        $config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install R"  -NodeType WorkerNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh

	L'applet de commande **Add-AzureRmHDInsightScriptAction** accepte les paramètres suivants :

	| Paramètre | Définition |
	| --------- | ---------- |
	| Configuration | Objet de configuration auquel des informations d'action de script sont ajoutées. |
	| Nom | Nom de l'action de script. |
	| NodeType | Spécifie le nœud sur lequel le script de personnalisation est exécuté. Les valeurs correctes sont **HeadNode** (pour une installation sur le nœud principal), **WorkerNode** (pour une installation sur l'ensemble des nœuds de données) ou **ZookeeperNode** (pour une installation sur le nœud zookeeper). |
	| Paramètres | Paramètres requis par le script. |
	| Uri | Spécifie l’URI du script qui est exécuté. |

4. Définir l'utilisateur admin/HTTPS pour le cluster :

        $httpCreds = get-credential
        
    Lorsque vous y êtes invité, entrez « admin » comme nom et saisissez un mot de passe.

5. Définir les informations d'identification SSH :

        $sshCreds = get-credential
    
    Lorsque vous y êtes invité, entrez le nom d'utilisateur et le mot de passe SSH. Si vous souhaitez sécuriser le compte SSH avec un certificat au lieu d'un mot de passe, utilisez un mot de passe vide et définissez `$sshPublicKey` sur le contenu de la clé publique du certificat à utiliser. Par exemple :
    
        $sshPublicKey = Get-Content .\path\to\public.key -Raw
    
4. Enfin, créez le cluster :
        
        New-AzureRmHDInsightCluster -config $config -clustername $clusterName -DefaultStorageContainer $containerName -Location $location -ResourceGroupName $resourceGroupName -ClusterSizeInNodes $clusterNodes -HttpCredential $httpCreds -SshCredential $sshCreds -OSType Linux
    
    Si vous utilisez une clé publique pour sécuriser votre compte SSH, vous devez également spécifier `-SshPublicKey $sshPublicKey` en tant que paramètre.

La création du cluster peut prendre plusieurs minutes.

## Utilisez une action de Script dans le Kit de développement .NET HDInsight

Le Kit de développement logiciel (SDK) .NET HDInsight fournit des bibliothèques clientes qui facilitent l’utilisation d’HDInsight à partir d’une application .NET. Pour obtenir un exemple de code, consultez [Créer des clusters basés sur Linux dans HDInsight à l’aide du Kit de développement logiciel (SDK) .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).


## Résolution de problèmes

Vous pouvez utiliser l’interface utilisateur web de Ambari pour afficher les informations consignées par des scripts lors de la création du cluster. Toutefois, si la création du cluster a échoué en raison d'une erreur dans le script, les journaux sont également disponibles dans le compte de stockage par défaut associé au cluster. Cette section fournit des informations sur la façon de récupérer les journaux à l'aide de ces deux options.

### À l'aide de l'interface utilisateur Web d’Ambari

1. Dans votre navigateur, accédez à https://CLUSTERNAME.azurehdinsight.net. Remplacez CLUSTERNAME par le nom de votre cluster HDInsight.

	Lorsque vous y êtes invité, saisissez le nom de compte (admin) et le mot de passe correspondant au cluster. Vous devrez peut-être saisir de nouveau les informations d’identification d’administrateur dans un formulaire web.

2. Dans la barre située en haut de la page, sélectionnez l’entrée __ops__. Cette opération permet d’afficher une liste des opérations en cours et précédentes effectuées sur le cluster via Ambari.

	![Barre de l’interface utilisateur web Ambari avec ops sélectionné](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Recherchez les entrées comportant __run\_customscriptaction__ dans la colonne __Operations__. Ceux-ci sont créés lors de l’exécution des actions de Script.

	![Capture d’écran des opérations](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

	Sélectionnez cette entrée et explorez les liens pour afficher les sorties STDOUT et STDERR générées au moment où le script a été exécuté sur le cluster.

### Accès aux journaux à partir du compte de stockage par défaut

Si la création du cluster a échoué en raison d'une erreur dans l'action de script, les journaux des actions de script restent accessibles directement à partir du compte de stockage par défaut associé au cluster.

* Les journaux de stockage sont disponibles dans `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`. 

	![Capture d’écran des opérations](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

	Ici, les journaux sont classés séparément selon les nœuds headnode, workdernode et zookeeper. Voici quelques exemples : * **Nœud principal** - `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net` * **Nœud de travail** - `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net` * **Nœud Zookeeper** - `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Toutes les valeurs stdout et stderr de l'hôte correspondant sont téléchargées vers le compte de stockage. Il existe un fichier **output-*.txt** et un fichier **errors-*.txt** pour chaque action de script. Le fichier output-*.txt contient des informations sur l'URI du script que vous avez exécuté sur l'ordinateur hôte. Par exemple :

		'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Vous pouvez créer plusieurs fois un cluster d'action de script portant le même nom. Dans ce cas, vous pouvez différencier les journaux correspondants selon le nom de dossier DATE. Par exemple, la structure de dossiers d’un cluster (mycluster) créé à différentes dates sera :
	* `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04`
	* `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Si vous créez un cluster d'action de script avec le même nom le même jour, vous pouvez utiliser le préfixe unique pour identifier les fichiers journaux correspondants.

* Si vous créez un cluster à la fin de la journée, il est possible que les fichiers journaux s'étendent sur deux jours. Dans ce cas, vous verrez deux dossiers de dates différents pour le même cluster.

* Le téléchargement des fichiers journaux vers le conteneur par défaut peut prendre jusqu'à 5 minutes, en particulier pour les clusters de grande taille. Par conséquent, si vous souhaitez accéder aux journaux, vous ne devez pas immédiatement supprimer le cluster en cas d'échec d'une action de script.


## Prise en charge des logiciels open source utilisés sur les clusters HDInsight

Le service Microsoft Azure HDInsight est une plateforme flexible qui vous permet de créer des applications de données volumineuses (Big Data) dans le cloud à l’aide d’un écosystème de technologies open source articulées autour de Hadoop. Microsoft Azure fournit un niveau de support général pour les technologies open source, comme indiqué dans la section **Portée du support** du site web [FAQ du support Azure](https://azure.microsoft.com/support/faq/). Le service HDInsight fournit un niveau de support supplémentaire pour certains composants, comme indiqué ci-dessous.

Deux types de composant open source sont disponibles dans le service HDInsight :

- **Composants intégrés** : ces composants sont préinstallés sur les clusters HDInsight et fournissent la fonctionnalité principale du cluster. Par exemple, YARN ResourceManager, le langage de requête Hive (HiveQL) et la bibliothèque Mahout appartiennent à cette catégorie. Une liste complète des composants de cluster est disponible sur la page [Nouveautés des versions de cluster Hadoop fournies par HDInsight](hdinsight-component-versioning.md).

- **Composants personnalisés** : en tant qu’utilisateur du cluster, vous pouvez installer ou utiliser, dans votre charge de travail, tout composant qui est disponible dans la communauté ou que vous avez créé.

> [AZURE.WARNING] Les composants fournis avec le cluster HDInsight bénéficient d’une prise en charge totale, et le support Microsoft vous aidera à identifier et à résoudre les problèmes liés à ces composants.
>
> Les composants personnalisés bénéficient d'un support commercialement raisonnable pour vous aider à résoudre le problème. Cela signifie SOIT que le problème pourra être résolu, SOIT que vous serez invité à affecter les ressources disponibles pour les technologies Open Source. Vous pouvez, par exemple, utiliser de nombreux sites de communauté, comme le [forum MSDN sur HDInsight](https://social.msdn.microsoft.com/Forums/azure/fr-FR/home?forum=hdinsight) ou [http://stackoverflow.com](http://stackoverflow.com). En outre, les projets Apache ont des sites de projet sur [http://apache.org](http://apache.org). Par exemple, [Hadoop](http://hadoop.apache.org/) ou [Spark](http://spark.apache.org/).

Le service HDInsight fournit plusieurs méthodes d’utilisation de ces composants personnalisés. Quel que soit le mode d’utilisation ou d’installation du composant sur le cluster, le même niveau de support s’applique. Vous trouverez, ci-dessous, la liste des méthodes les plus courantes d’utilisation des composants personnalisés sur des clusters HDInsight.

1. Envoi de travaux : il est possible d’envoyer des travaux Hadoop ou d’autres types de travaux au cluster qui exécute ou utilise des composants personnalisés.

2. Personnalisation de cluster : lors de la création du cluster, vous pouvez spécifier des paramètres supplémentaires et des composants personnalisés qui seront installés sur les nœuds de cluster.

3. Exemples : pour des composants personnalisés fréquemment utilisés, il arrive que Microsoft et d’autres éditeurs proposent des exemples illustrant leur utilisation sur les clusters HDInsight. Ces exemples sont fournis sans support.

## Étapes suivantes

Consultez la rubrique suivante pour obtenir des informations et des exemples sur la création et l’utilisation de scripts afin de personnaliser un cluster :

- [Développer des scripts d’action de script pour HDInsight](hdinsight-hadoop-script-actions-linux.md)
- [Installer et utiliser Spark sur les clusters HDInsight](hdinsight-hadoop-spark-install-linux.md)
- [Installer et utiliser R sur les clusters HDInsight](hdinsight-hadoop-r-scripts-linux.md)
- [Installer et utiliser Solr sur les clusters HDInsight](hdinsight-hadoop-solr-install-linux.md)
- [Installer et utiliser Giraph sur les clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md)



[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Procédure de création d’un cluster"

<!---HONumber=AcomDC_0224_2016-->
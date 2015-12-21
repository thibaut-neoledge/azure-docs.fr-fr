<properties
   	pageTitle="Créer des clusters Hadoop, HBase ou Storm sur Linux dans HDInsight à l’aide de cURL et de l’API REST Azure | Microsoft Azure"
   	description="Apprenez à créer des clusters HDInsight sous Linux à l’aide de cURL, de modèles Azure Resource Manager et de l’API REST Azure. Vous pouvez spécifier le type de cluster (Hadoop, HBase ou Storm) ou utiliser des scripts pour installer des composants personnalisés."
   	services="hdinsight"
   	documentationCenter=""
   	authors="Blackmist"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="12/04/2015"
   	ms.author="larryfr"/>

#Créer des clusters Linux dans HDInsight à l’aide de cURL et de l’API REST Azure

[AZURE.INCLUDE [sélecteur](../../includes/hdinsight-create-linux-cluster-selector.md)]

L’API REST Azure vous permet d’effectuer des opérations de gestion sur les services hébergés sur la plateforme Azure, y compris la création de nouvelles ressources, telles que des clusters Linux dans HDInsight. Ce document explique comment créer des modèles Azure Resource Manager pour configurer un cluster HDInsight et le stockage associé, puis comment utiliser cURL pour déployer un modèle vers l’API REST Azure et créer un cluster HDInsight.

> [AZURE.IMPORTANT]Les étapes de ce document utilisent le nombre par défaut de nœuds worker (4) pour un cluster HDInsight. Si vous envisagez d’utiliser plus de 32 nœuds worker lors de la création du cluster ou en faisant évoluer le cluster après sa création, vous devez sélectionner une taille de nœud principal avec au moins 8 cœurs et 14 Go de RAM.
>
> Pour plus d’informations sur les tailles de nœud et les coûts associés, consultez [Tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

##Configuration requise

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __Interface de ligne de commande Azure__. Cette interface est utilisée pour créer un principal du service, qui sert ensuite à générer des jetons d’authentification pour les demandes envoyées à l’API REST Azure.

    Pour plus d’informations sur l’installation de la CLI, consultez [Installer la CLI Azure](../xplat-cli-install.md).

- __cURL__. Cet utilitaire est disponible via votre système de gestion des packages, ou il peut être téléchargé à partir de [http://curl.haxx.se/](http://curl.haxx.se/).

    > [AZURE.NOTE]Si vous utilisez PowerShell pour exécuter les commandes décrites dans ce document, vous devez d’abord supprimer l’alias `curl` qu’il crée par défaut. Cet alias utilise l’applet de commande PowerShell Invoke-WebRequest au lieu de cURL quand vous exécutez la commande `curl` à partir d’une invite de commandes PowerShell. Il génère des erreurs pour la plupart des commandes utilisées dans ce document.
    > 
    > Pour supprimer cet alias, exécutez la commande suivante à partir de l’invite PowerShell :
    >
    > ```Remove-item alias:curl`
    >
    > Après avoir supprimé l’alias, vous pouvez normalement utiliser la version de cURL que vous avez installée sur votre système.

##Créer un modèle

Les modèles Azure Resource Management sont des documents JSON qui décrivent un __groupe de ressources__ et toutes les ressources qu’il contient (par exemple, HDInsight). Cette approche basée sur un modèle vous permet de définir toutes les ressources dont vous avez besoin pour HDInsight dans un seul modèle et de gérer les modifications apportées au groupe dans son ensemble par le biais de __déploiements__ qui appliquent les modifications au groupe.

Les modèles sont généralement constitués de deux parties : le modèle lui-même et un fichier de paramètres que vous renseignez avec les valeurs spécifiques de votre configuration (par exemple, le nom du cluster, le nom d’administrateur et le mot de passe). Si vous utilisez directement l’API REST, vous devez combiner ces deux parties en un seul fichier. Ce document JSON a le format suivant :

    {
        "properties": {
            "template": {
                contents of template file
            },
            "mode": "deploymentmode",
            "Parameters": {
                contents of the parameters element from parameters file
            }
        }
    }

Par exemple, le code ci-dessous combine le contenu des fichiers de modèle et de paramètres disponibles sur [https://github.com/Azure/azure-quickstart-templates/tree/master/hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/hdinsight-linux-ssh-password). Ce code crée un cluster Linux qui utilise un mot de passe pour sécuriser le compte d’utilisateur SSH.

    {
        "properties": {
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {
                    "location": {
                        "type": "string",
                        "allowedValues": ["Central US",
                        "East Asia",
                        "East US",
                        "Japan East",
                        "Japan West",
                        "North Europe",
                        "South Central US",
                        "Southeast Asia",
                        "West Europe",
                        "West US"],
                        "metadata": {
                            "description": "The location where all azure resources will be deployed."
                        }
                    },
                    "clusterType": {
                        "type": "string",
                        "allowedValues": ["hadoop",
                        "hbase",
                        "storm",
                        "spark"],
                        "metadata": {
                            "description": "The type of the HDInsight cluster to create."
                        }
                    },
                    "clusterName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the HDInsight cluster to create."
                        }
                    },
                    "clusterLoginUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                        }
                    },
                    "clusterLoginPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "sshUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to remotely access the cluster."
                        }
                    },
                    "sshPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "clusterStorageAccountName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the storage account to be created and be used as the cluster's storage."
                        }
                    },
                    "clusterWorkerNodeCount": {
                        "type": "int",
                        "defaultValue": 4,
                        "metadata": {
                            "description": "The number of nodes in the HDInsight cluster."
                        }
                    }
                },
                "variables": {
                    "defaultApiVersion": "2015-05-01-preview",
                    "clusterApiVersion": "2015-03-01-preview"
                },
                "resources": [{
                    "name": "[parameters('clusterStorageAccountName')]",
                    "type": "Microsoft.Storage/storageAccounts",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('defaultApiVersion')]",
                    "dependsOn": [],
                    "tags": {
                        
                    },
                    "properties": {
                        "accountType": "Standard_LRS"
                    }
                },
                {
                    "name": "[parameters('clusterName')]",
                    "type": "Microsoft.HDInsight/clusters",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('clusterApiVersion')]",
                    "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                    "tags": {
                        
                    },
                    "properties": {
                        "clusterVersion": "3.2",
                        "osType": "Linux",
                        "clusterDefinition": {
                            "kind": "[parameters('clusterType')]",
                            "configurations": {
                                "gateway": {
                                    "restAuthCredential.isEnabled": true,
                                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                                }
                            }
                        },
                        "storageProfile": {
                            "storageaccounts": [{
                                "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                                "isDefault": true,
                                "container": "[parameters('clusterName')]",
                                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                            }]
                        },
                        "computeProfile": {
                            "roles": [{
                                "name": "headnode",
                                "targetInstanceCount": "2",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            },
                            {
                                "name": "workernode",
                                "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            }]
                        }
                    }
                }],
                "outputs": {
                    "cluster": {
                        "type": "object",
                        "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                    }
                }
            },
            "mode": "incremental",
            "Parameters": {
                "location": {
                    "value": "North Europe"
                },
                "clusterName": {
                    "value": "newclustername"
                },
                "clusterType": {
                    "value": "hadoop"
                },
                "clusterStorageAccountName": {
                    "value": "newstoragename"
                },
                "clusterLoginUserName": {
                    "value": "admin"
                },
                "clusterLoginPassword": {
                    "value": "changeme"
                },
                "sshUserName": {
                    "value": "sshuser"
                },
                "sshPassword": {
                    "value": "changeme"
                }
            }
        }
    }

Cet exemple est utilisé dans les étapes de ce document. Vous devez remplacer les espaces réservés _value_ dans la section __Parameters__ à la fin du document par les valeurs que vous souhaitez utiliser pour votre cluster.

##Vous connecter à votre abonnement Azure

Suivez les étapes décrites dans [Se connecter à un abonnement Azure à partir de l’interface de ligne de commande Azure (Azure CLI)](../xplat-cli-connect.md) et connectez-vous à votre abonnement à l’aide de la méthode __login__.

##Créer un principal du service

> [AZURE.IMPORTANT]Si vous suivez les étapes décrites dans le lien ci-dessous, vous devez apporter la modification suivante :
> 
> * Dans les étapes qui vous demandent d’utiliser une valeur __reader__, utilisez une valeur __owner__ à la place. Cela crée un principal du service qui peut apporter des modifications aux services associés à votre abonnement, ce qui est nécessaire pour créer un cluster HDInsight.
>
> Vous devez également enregistrer les informations suivantes utilisées dans ce processus :
> 
> * L’ID d’abonnement reçu lors de l’utilisation de `azure account list`
> * L’ID de locataire reçu lors de l’utilisation de `azure account list`
> * L’ID d’application renvoyé lors de la création du principal du service
> * Le mot de passe du principal du service utilisé lors de la création de ce dernier

Suivez les étapes décrites dans la section _Authentifier le principal du service avec un mot de passe - Azure CLI_ dans le document [Authentification d’un principal du service à l’aide d’Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authenticate-service-principal/#authenticate-service-principal-with-password---azure-cli). Cette procédure crée un principal du service qui peut être utilisé pour authentifier la demande de création de cluster.

##Obtenir un jeton d’authentification

Utilisez le code suivant pour obtenir un nouveau jeton d’Azure. Remplacez __TENANTID__, __APPLICATIONID__ et __PASSWORD__ par les informations ayant été enregistrées lors de la création d’un principal du service :

    curl -X "POST" "https://login.microsoftonline.com/TENANTID/oauth2/token" \
    -H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
    -H "Content-Type: application/x-www-form-urlencoded" \
    --data-urlencode "client_id=APPLICATIONID" \
    --data-urlencode "grant_type=client_credentials" \
    --data-urlencode "client_secret=PASSWORD" \
    --data-urlencode "resource=https://management.azure.com/"

Si cette demande est acceptée, vous recevez une réponse 200 qui contient un document JSON.

> [AZURE.IMPORTANT]Le document JSON renvoyé par cette demande comporte un élément nommé __access\_token__. La valeur de cet élément est le jeton d’accès à utiliser pour authentifier les demandes effectuées dans les sections suivantes de ce document.

##Créer un groupe de ressources

Procédez comme suit pour créer un groupe de ressources. Vous devez créer le groupe avant de créer les ressources, telles que le cluster HDInsight.

* Remplacez __SUBSCRIPTIONID__ par l’ID d’abonnement reçu lors de la création du principal du service.
* Remplacez __ACCESSTOKEN__ par le jeton d’accès reçu à l’étape précédente.
* Remplacez __DATACENTERLOCATION__ par le centre de données dans lequel vous voulez créer le groupe de ressources et les ressources. Par exemple, « South Central US ». 
* Remplacez __GROUPNAME__ par le nom souhaité pour ce groupe :

    curl -X "PUT" "https://management.azure.com/subscriptions/SUBSCRIPTIONID/resourcegroups/GROUPNAME?api-version=2015-01-01" \\ -H "Authorization: Bearer ACCESSTOKEN" \\ -H "Content-Type: application/json" \\ -d $'{ "location": "DATACENTERLOCATION" }’

Si cette demande est acceptée, vous recevez une réponse 200 qui contient un document JSON renfermant des informations sur le groupe. L’élément `"provisioningState"` affichera la valeur `"Succeeded"`.

##Créer un déploiement

Procédez comme suit pour déployer la configuration du cluster (modèle et paramètres) dans le groupe de ressources.

* Remplacez __SUBSCRIPTIONID__ et __ACCESSTOKEN__ par les valeurs utilisées précédemment. 
* Remplacez __GROUPNAME__ par le nom du groupe de ressources créé dans la section précédente.
* Remplacez __DEPLOYMENTNAME__ par le nom souhaité pour ce déploiement.

    curl -X "PUT" "https://management.azure.com/subscriptions/SUBSCRIPTIONID/resourcegroups/GROUPNAME/providers/microsoft.resources/deployments/DEPLOYMENTNAME?api-version=2015-01-01" \\ -H "Authorization: Bearer ACCESSTOKEN" \\ -H "Content-Type: application/json" \\ -d "{set your body string to the template and parameters}"

> [AZURE.NOTE]Si vous avez enregistré le document JSON contenant le modèle et les paramètres dans un fichier, vous pouvez utiliser le code suivant à la place de « -d "{ template and parameters}" » :
>
> ```--data-binary "@/path/to/file.json"```

Si cette demande est acceptée, vous recevez une réponse 200 qui contient un document JSON renfermant des informations sur le déploiement.

> [AZURE.IMPORTANT]Notez que la demande de déploiement a été envoyée, mais que le déploiement n’est pas terminé à ce stade. Le processus de déploiement prend généralement 15 minutes environ.

##Vérifier l’état d’un déploiement

Pour vérifier l’état du déploiement, procédez comme suit :

* Remplacez __SUBSCRIPTIONID__ et __ACCESSTOKEN__ par les valeurs utilisées précédemment. 
* Remplacez __GROUPNAME__ par le nom du groupe de ressources créé dans la section précédente.

    curl -X "GET" "https://management.azure.com/subscriptions/SUBSCRIPTIONID/resourcegroups/GROUPNAME/providers/microsoft.resources/deployments/DEPLOYMENTNAME?api-version=2015-01-01" \\ -H "Authorization: Bearer ACCESSTOKEN" \\ -H "Content-Type: application/json"

Ceci renvoie un document JSON renfermant des informations sur le déploiement. L’élément `"provisioningState"` affiche l’état du déploiement. La valeur `"Succeeded"` indique que le déploiement a été correctement effectué. À ce stade, votre cluster est normalement prêt à être utilisé.

##Étapes suivantes

Vous avez créé un cluster HDInsight. Pour apprendre à l’utiliser, consultez les rubriques ci-dessous.

###Clusters Hadoop

* [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md)
* [Utilisation de MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

###Clusters HBase

* [Prise en main de HBase sur HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Développement d’applications Java pour HBase sur HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###Clusters Storm

* [Développement de topologies Java pour Storm sur HDInsight](hdinsight-storm-develop-java-topology.md)
* [Utilisation de composants Python dans Storm sur HDInsight](hdinsight-storm-develop-python-topology.md)
* [Déploiement et analyse des topologies avec Storm sur HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=AcomDC_1210_2015-->
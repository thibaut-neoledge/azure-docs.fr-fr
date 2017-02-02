---
title: "Créer des clusters Hadoop, HBase ou Storm sur Linux dans HDInsight à l’aide de cURL et de l’API REST Azure | Microsoft Docs"
description: "Apprenez à créer des clusters HDInsight sous Linux à l’aide de cURL, de modèles Azure Resource Manager et de l’API REST Azure. Vous pouvez spécifier le type de cluster (Hadoop, HBase ou Storm) ou utiliser des scripts pour installer des composants personnalisés."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 98be5893-2c6f-4dfa-95ec-d4d8b5b7dcb5
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 8056e7ece1942c9090a7c36447a96829febaf1a4
ms.openlocfilehash: 491f8540e8e53f366327ed80caff2e1e360132fc


---
# <a name="create-linux-based-clusters-in-hdinsight-using-curl-and-the-azure-rest-api"></a>Créer des clusters Linux dans HDInsight à l’aide de cURL et de l’API REST Azure

[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

L’API REST Azure vous permet d’effectuer des opérations de gestion sur les services hébergés sur la plateforme Azure, y compris la création de nouvelles ressources, telles que des clusters Linux dans HDInsight. Ce document explique comment créer des modèles Azure Resource Manager pour configurer un cluster HDInsight et le stockage associé, puis comment utiliser cURL pour déployer un modèle vers l’API REST Azure et créer un cluster HDInsight.

> [!IMPORTANT]
> Les étapes de ce document utilisent le nombre par défaut de nœuds worker (4) pour un cluster HDInsight. Si vous envisagez d’utiliser plus de 32 nœuds worker lors de la création du cluster ou en faisant évoluer le cluster après sa création, vous devez sélectionner une taille de nœud principal avec au moins 8 cœurs et 14 Go de RAM.
>
> Pour plus d’informations sur les tailles de nœud et les coûts associés, consultez [Tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).


## <a name="prerequisites"></a>Conditions préalables

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Azure CLI 2.0** (version préliminaire). Cette interface est utilisée pour créer un principal du service, qui sert ensuite à générer des jetons d’authentification pour les demandes envoyées à l’API REST Azure. Pour plus d’informations sur la version préliminaire d’Azure CLI 2.0, consultez [Prise en main d’Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

* **cURL**. Cet utilitaire est disponible via votre système de gestion des packages, ou il peut être téléchargé à partir de [http://curl.haxx.se/](http://curl.haxx.se/).

  > [!NOTE]
  > Si vous utilisez PowerShell pour exécuter les commandes décrites dans ce document, vous devez d’abord supprimer l’alias `curl` qu’il crée par défaut. Cet alias utilise l’applet de commande PowerShell Invoke-WebRequest au lieu de cURL quand vous exécutez la commande `curl` à partir d’une invite de commandes PowerShell. Il génère des erreurs pour la plupart des commandes utilisées dans ce document.
  >
  > Pour supprimer cet alias, exécutez la commande suivante à partir de l’invite PowerShell :
  >
  > `Remove-item alias:curl`
  >
  > Après avoir supprimé l’alias, vous pouvez normalement utiliser la version de cURL que vous avez installée sur votre système.

### <a name="access-control-requirements"></a>Exigences de contrôle d’accès
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-template"></a>Créer un modèle

Les modèles Azure Resource Manager sont des documents JSON qui décrivent un **groupe de ressources** et toutes les ressources qu’il contient (par exemple, HDInsight). Cette approche basée sur un modèle vous permet de définir toutes les ressources dont vous avez besoin pour HDInsight dans un seul modèle et de gérer les modifications apportées au groupe dans son ensemble par le biais de **déploiements** qui appliquent les modifications au groupe.

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

Par exemple, le code ci-dessous combine le contenu des fichiers de modèle et de paramètres disponibles sur [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password). Ce code crée un cluster Linux qui utilise un mot de passe pour sécuriser le compte d’utilisateur SSH.

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

Cet exemple est utilisé dans les étapes de ce document. Vous devez remplacer les espaces réservés *value* dans la section **Parameters** à la fin du document par les valeurs que vous souhaitez utiliser pour votre cluster.

## <a name="login-to-your-azure-subscription"></a>Connexion à votre abonnement Azure

Suivez la procédure décrite dans la [prise en main d’Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) et connectez-vous à votre abonnement en utilisant la commande `az login`.

## <a name="create-a-service-principal"></a>Créer un principal du service

> [!NOTE]
> Ces étapes sont une version abrégée des informations fournies dans la section *Créer un principal du service avec un mot de passe - Azure CLI* dans le document [Créer un principal du service pour accéder aux ressources à l’aide de l’interface de ligne de commande (CLI) Azure](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md#create-service-principal-with-password) . Ces étapes créent un principal du service qui peut être utilisé pour authentifier les demandes API REST servant à créer des ressources Azure, telles qu’un cluster HDInsight.

1. À partir d’une ligne de commande, utilisez la commande suivante pour répertorier vos abonnements Azure.

         az account list --query '[].{Subscription_ID:id,Tenant_ID:tenantId,Name:name}'  --output table

    Dans la liste, sélectionnez l’abonnement que vous souhaitez utiliser et notez les colonnes **Subscription_ID** et __Tenant_ID__. Enregistrez ces valeurs.

2. Utilisez les commandes suivantes pour créer une application dans Azure Active Directory.

        az ad app create --display-name "exampleapp" --homepage "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your password> --query 'appId'

    Remplacez les valeurs de `--display-name`, `--homepage` et `--identifier-uris` par vos propres valeurs. Fournissez un mot de passe pour la nouvelle entrée Active Directory.

   > [!NOTE]
   > Étant donné que vous créez cette application pour l’authentification par le biais d’un principal du service, les valeurs `--home-page` et `--identifier-uris` ne doivent pas faire référence à une page web réelle hébergée sur Internet, mais simplement être des URI uniques.

   La valeur retournée par cette commande est __l’ID de l’application__ pour la nouvelle application. Enregistrez cette valeur.

3. Utilisez la commande suivante pour créer un principal du service à l’aide de **l’ID de l’application**.

        az ad sp create --id <App ID> --query 'objectId'

     La valeur retournée par cette commande est __l’ID d’objet__. Enregistrez cette valeur.

4. Affectez le rôle **Owner** (Propriétaire) au principal du service à l’aide de la valeur **Object ID** (ID d’objet). Vous devez également utiliser **l’ID d’abonnement** obtenu précédemment.

        az role assignment create --assignee <Object ID> --role Owner --scope /subscriptions/<Subscription ID>/

## <a name="get-an-authentication-token"></a>Obtenir un jeton d’authentification

Utilisez la commande suivante pour récupérer un jeton d’authentification :

    curl -X "POST" "https://login.microsoftonline.com/TenantID/oauth2/token" \
    -H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
    -H "Content-Type: application/x-www-form-urlencoded" \
    --data-urlencode "client_id=AppID" \
    --data-urlencode "grant_type=client_credentials" \
    --data-urlencode "client_secret=password" \
    --data-urlencode "resource=https://management.azure.com/"

    Replace **TenantID**, **AppID**, and **password** with the values obtained or used previously.

    If this request is successful, you will receive a 200 series response and the response body will contain a JSON document.

    The JSON document returned by this request will contain an element named **access_token**; the value of this element is the access token you must use to authentication the requests used in the next sections of this document.

        {
            "token_type":"Bearer",
            "expires_in":"3599",
            "expires_on":"1463409994",
            "not_before":"1463406094",
            "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
        }

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Procédez comme suit pour créer un groupe de ressources. Vous devez créer le groupe avant de créer les ressources, telles que le cluster HDInsight.

* Remplacez **SubscriptionID** par l’ID d’abonnement reçu au moment de la création du principal du service.
* Remplacez **AccessToken** par le jeton d’accès reçu à l’étape précédente.
* Remplacez **DataCenterLocation** par le centre de données dans lequel vous voulez créer le groupe de ressources et les ressources. Par exemple, « South Central US ».
* Remplacez **ResourceGroupName** par le nom que vous souhaitez utiliser pour ce groupe :

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName?api-version=2015-01-01" \
    -H "Authorization: Bearer AccessToken" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DataCenterLocation"
}'
```

Si cette demande est acceptée, vous recevez une réponse 200 qui contient un document JSON renfermant des informations sur le groupe. L’élément `"provisioningState"` affichera la valeur `"Succeeded"`.

## <a name="create-a-deployment"></a>Créer un déploiement

Procédez comme suit pour déployer la configuration du cluster (modèle et paramètres) dans le groupe de ressources.

* Remplacez **SubscriptionID** et **AccessToken** par les valeurs utilisées précédemment.
* Remplacez **ResourceGroupName** par le nom du groupe de ressources créé dans la section précédente.
* Remplacez **DeploymentName** par le nom souhaité pour ce déploiement.

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [!NOTE]
> Si vous avez enregistré le document JSON contenant le modèle et les paramètres dans un fichier, vous pouvez utiliser le code suivant à la place de `-d "{ template and parameters}"` :
>
> `--data-binary "@/path/to/file.json"`

Si cette demande est acceptée, vous recevez une réponse 200 qui contient un document JSON renfermant des informations sur le déploiement.

> [!IMPORTANT]
> Notez que la demande de déploiement a été envoyée, mais que le déploiement n’est pas terminé à ce stade. Le processus de déploiement prend généralement 15 minutes environ.

## <a name="check-the-status-of-a-deployment"></a>Vérifier l’état d’un déploiement

Pour vérifier l’état du déploiement, procédez comme suit :

* Remplacez **SubscriptionID** et **AccessToken** par les valeurs utilisées précédemment.
* Remplacez **ResourceGroupName** par le nom du groupe de ressources créé dans la section précédente.

```
curl -X "GET" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json"
```

Ceci renvoie un document JSON renfermant des informations sur le déploiement. L’élément `"provisioningState"` affiche l’état du déploiement. La valeur `"Succeeded"` indique que le déploiement a été correctement effectué. À ce stade, votre cluster est normalement prêt à être utilisé.

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé un cluster HDInsight. Pour apprendre à l’utiliser, consultez les rubriques ci-dessous.

### <a name="hadoop-clusters"></a>Clusters Hadoop

* [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md)
* [Utilisation de MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Clusters HBase

* [Prise en main de HBase sur HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Développement d’applications Java pour HBase sur HDInsight](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clusters Storm

* [Développement de topologies Java pour Storm sur HDInsight](hdinsight-storm-develop-java-topology.md)
* [Utilisation de composants Python dans Storm sur HDInsight](hdinsight-storm-develop-python-topology.md)
* [Déploiement et analyse des topologies avec Storm sur HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)



<!--HONumber=Dec16_HO3-->



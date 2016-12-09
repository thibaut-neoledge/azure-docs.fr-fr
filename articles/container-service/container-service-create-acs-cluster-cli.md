---
title: "Déployer un cluster Azure Container Service avec CLI | Microsoft Docs"
description: "Déployer un cluster Azure Container Service à l’aide de la version préliminaire d’Azure CLI 2.0"
services: container-service
documentationcenter: 
author: sauryadas
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2016
ms.author: saudas
translationtype: Human Translation
ms.sourcegitcommit: 855f0fe77bd55f6ec0dacad4bc28603ac1c6979c
ms.openlocfilehash: c4a513686433e802f27f78de60e8b7fca21b4634


---
# <a name="using-the-azure-cli-20-preview-to-create-an-azure-container-service-cluster"></a>Utiliser la version préliminaire d’Azure CLI 2.0 pour créer un cluster Azure Container Service

Pour créer un cluster Azure Container Service, vous avez besoin des éléments suivants :
* un compte Azure ([obtenir une version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/))
* La version [Azure CLI 2.0 (version préliminaire)](https://github.com/Azure/azure-cli#installation) installée
* une connexion à votre compte Azure (voir ci-dessous)

## <a name="log-in-to-your-account"></a>Se connecter à son compte
```azurecli
az login 
```
Accédez à ce [lien](https://login.microsoftonline.com/common/oauth2/deviceauth) pour vous authentifier à l’aide du code de l’appareil fourni dans l’interface de ligne de commande.

![saisie de la commande](media/container-service-create-acs-cluster-cli/login.png)

![iPhone](media/container-service-create-acs-cluster-cli/login-browser.png)


## <a name="create-a-resource-group"></a>Créer un groupe de ressources
```azurecli
az resource group create -n acsrg1 -l "westus"
```

![Image création du groupe de ressources](media/container-service-create-acs-cluster-cli/rg-create.png)

## <a name="list-of-available-azure-container-service-cli-commands"></a>Liste des commandes CLI Azure Container Service disponibles

```azurecli
az acs -h
```

![utilisation des commandes ACS](media/container-service-create-acs-cluster-cli/acs-command-usage-help.png)

## <a name="create-an-azure-container-service-cluster"></a>Créer un cluster Azure Container Service

*création d’un cluster ACS dans CLI*

```azurecli
az acs create -h
```
Le nom du service de conteneur, le groupe de ressources créé à l’étape précédente et un nom DNS unique sont obligatoires. Les autres paramètres sont définis sur les valeurs par défaut (voir l’écran suivant avec l’instantané d’aide ci-dessous), sauf s’ils sont remplacés via leurs commutateurs respectifs.
![Image aide pour la création du cluster ACS](media/container-service-create-acs-cluster-cli/acs-command-usage-help.png)

*Création rapide du cluster ACS à l’aide des valeurs par défaut. Si vous n’avez pas de clé SSH, utilisez la seconde commande. Cette seconde commande de création qui intègre le commutateur --generate-ssh-keys créera automatiquement une clé pour vous*

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789
```

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789 --generate-ssh-keys
```

*Assurez-vous que le préfixe dns (commutateur -d) est unique. Si vous obtenez une erreur, veuillez réessayer avec une chaîne unique.*

Après avoir saisi la commande précédente, attendez environ 10 minutes que le cluster soit créé.

![Image création du cluster ACS](media/container-service-create-acs-cluster-cli/cluster-create.png)

## <a name="list-acs-clusters"></a>Répertorier les clusters ACS 

### <a name="under-a-subscription"></a>Dans un abonnement

```azurecli
az acs list --output table
```

### <a name="in-a-specific-resource-group"></a>Dans un groupe de ressources spécifique

```azurecli
az acs list -g acsrg1 --output table
```

![Image liste des clusters ACS](media/container-service-create-acs-cluster-cli/acs-list.png)


## <a name="display-details-of-a-container-service-cluster"></a>Afficher les détails d’un cluster Container Service

```azurecli
az acs show -g acsrg1 -n acs-cluster --output list
```

![Image liste des clusters ACS](media/container-service-create-acs-cluster-cli/acs-show.png)


## <a name="scale-the-acs-cluster"></a>Mettre à l’échelle le cluster ACS
*Vous pouvez effectuer une mise à l’échelle verticale ou horizontale. Le paramètre new-agent-count indique le nouveau nombre d’agents dans le cluster ACS.*

```azurecli
az acs scale -g acsrg1 -n acs-cluster --new-agent-count 4
```

![Image mise à l’échelle du cluster ACS](media/container-service-create-acs-cluster-cli/acs-scale.png)

## <a name="delete-a-container-service-cluster"></a>Supprimer un cluster Container Service
```
az acs delete -g acsrg1 -n acs-cluster 
```
*Notez que cette commande de suppression ne supprime pas toutes les ressources (réseau et stockage) créées lors de la création du service de conteneur. Pour supprimer toutes les ressources, nous vous recommandons de ne créer qu’un seul cluster ACS par groupe de ressources et de supprimer ensuite directement le groupe de ressources lorsque vous n’avez plus besoin du cluster ACS afin de vous assurer que toutes les ressources associées sont supprimées et qu’elles n’occasionnent plus aucun frais.*



<!--HONumber=Nov16_HO3-->



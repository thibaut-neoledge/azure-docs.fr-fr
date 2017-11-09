---
title: "Déployer OpenShift Origin dans Azure | Microsoft Docs"
description: "Déployez OpenShift Origin dans Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 1a40c4cc064b32aced7e976f40f6ed6a57e62204
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2017
---
# <a name="deploy-openshift-origin-in-azure"></a>Déployer OpenShift Origin dans Azure

Il existe plusieurs façons de déployer OpenShift Origin dans Azure. Vous pouvez déployer manuellement tous les composants d’infrastructure Azure nécessaires, puis suivre la [documentation](https://docs.openshift.org/3.6/welcome/index.html) d’OpenShift Origin.
Vous pouvez également utiliser un modèle Resource Manager existant qui simplifie le déploiement du cluster OpenShift Origin. Un tel modèle se trouve [ici](https://github.com/Microsoft/openshift-origin).

## <a name="deploy-using-the-openshift-origin-template"></a>Déployer à l’aide du modèle OpenShift Origin

Utilisez la valeur de `appId` du principal de service créé précédemment pour le paramètre `aadClientId`.

L’exemple suivant crée un fichier de paramètres nommé **azuredeploy.parameters.json** avec toutes les entrées requises.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

### <a name="deploy-using-azure-cli"></a>Déployer à l’aide d’Azure CLI


> [!NOTE] 
> La commande suivante requiert Azure CLI 2.0.8 ou version ultérieure. Pour vérifier la version de l’interface Azure CLI, exécutez la commande `az --version`. Pour mettre à jour l’interface, consultez [Installer Azure CLI 2.0]( /cli/azure/install-azure-cli).

L’exemple suivant déploie le cluster OpenShift et toutes les ressources associées dans un groupe de ressources nommé myResourceGroup avec le nom de déploiement myOpenShiftCluster. Le modèle est référencé directement à partir du dépôt GitHub, et un fichier de paramètres locaux nommé **azuredeploy.parameters.json** est utilisé.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

La durée du déploiement varie en fonction du nombre total de nœuds déployés, avec un minimum de 25 minutes. L’URL de la console OpenShift et le nom DNS du maître OpenShift est affiché sur le terminal à l’issue du déploiement.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="connect-to-the-openshift-cluster"></a>Se connecter au cluster OpenShift

Une fois le déploiement terminé, connectez-vous à la console OpenShift dans un navigateur à l’aide de la valeur de `OpenShift Console Uri`. Vous avez également la possibilité de vous connecter à l’OpenShift master à l’aide de la commande suivante :

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete](/cli/azure/group#delete) pour supprimer le groupe de ressources, le cluster OpenShift et toutes les ressources associées.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

- [Tâches post-déploiement](./openshift-post-deployment.md)
- [Résolution des problèmes relatifs au déploiement d’OpenShift](./openshift-troubleshooting.md)
- [Prise en main d’OpenShift Origin](https://docs.openshift.org/latest/getting_started/index.html)

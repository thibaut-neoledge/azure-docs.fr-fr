---
title: Utiliser Azure Files avec AKS | Microsoft Docs
description: Utiliser des disques Azure avec AKS
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 6e88c590e11aa8d2f4ae17e8b5e164483f0a6820
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2017
---
# <a name="using-azure-files-with-kubernetes"></a>Utilisation d’Azure Files avec Kubernetes

Les applications basées sur des conteneurs doivent souvent consulter et conserver des données dans un volume de données externe. Azure Files peut être utilisé en tant que banque de données externe. Cet article explique comment utiliser Azure Files comme volume Kubernetes dans Azure Container Service.

Pour plus d’informations sur les volumes Kubernetes, consultez [Volumes Kubernetes][kubernetes-volumes].

## <a name="creating-a-file-share"></a>Création d'un partage de fichiers

Un partage Azure File existant peut être utilisé avec Azure Container Service. Si vous devez en créer un, utilisez l’ensemble de commandes suivant.

Créez un groupe de ressources pour le partage Azure File à l’aide de la commande [az group create][az-group-create]. Le groupe de ressources du compte de stockage doit se trouver au même endroit que le cluster Kubernetes.

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Utilisez la commande [az storage account create][az-storage-create] pour créer un compte de stockage Azure. Le nom du compte de stockage doit être unique. Mettez à jour la valeur de l’argument `--name` avec une valeur unique.

```azurecli-interactive
az storage account create --name mystorageaccount --resource-group myResourceGroup --sku Standard_LRS
```

Utilisez la commande [az storage account keys list ][az-storage-key-list] pour récupérer la clé de stockage. Mettez à jour la valeur de l’argument `--account-name` avec le nom de compte de stockage unique.

Notez l’une des valeurs de clé, car vous en aurez besoin dans les étapes suivantes.

```azurecli-interactive
az storage account keys list --account-name mystorageaccount --resource-group myResourceGroup --output table
```

Utilisez la commande [az storage share create][az-storage-share-create] pour créer le partage Azure Files. Mettez à jour la valeur `--account-key` avec la valeur collectée à l’étape précédente.

```azurecli-interactive
az storage share create --name myfileshare --account-name mystorageaccount --account-key <key>
```

## <a name="create-kubernetes-secret"></a>Créer une clé secrète Kubernetes

Kubernetes a besoin d’informations d’identification pour accéder au partage de fichiers. La clé et le nom du compte de stockage Azure ne sont pas stockés avec chaque pod. Ils sont stockés une seule fois dans une [clé secrète Kubernetes][kubernetes-secret] et référencés par chaque volume Azure Files. 

Les valeurs contenues dans un manifeste de clé secrète Kubernetes doivent être encodées en Base64. Utilisez les commandes suivantes pour retourner des valeurs encodées.

Pour commencer, encodez le nom du compte de stockage. Remplacez `storage-account` par le nom de votre compte de stockage Azure.

```azurecli-interactive
echo -n <storage-account> | base64
```

Vous avez ensuite besoin de la clé d’accès au compte de stockage. Exécutez la commande suivante pour retourner la clé encodée. Remplacez `storage-key` avec la clé collectée à l’étape précédente.

```azurecli-interactive
echo -n <storage-key> | base64
```

Créez un fichier nommé `azure-secret.yml` et copiez-y le YAML suivant. Mettez à jour les valeurs `azurestorageaccountname` et `azurestorageaccountkey` avec les valeurs encodées en Base64 récupérées à l’étape précédente.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: azure-secret
type: Opaque
data:
  azurestorageaccountname: <base64_encoded_storage_account_name>
  azurestorageaccountkey: <base64_encoded_storage_account_key>
```

Utilisez la commande [kubectl apply][kubectl-apply] pour créer la clé secrète.

```azurecli-interactive
kubectl apply -f azure-secret.yml
```

## <a name="mount-file-share-as-volume"></a>Monter le partage de fichiers en tant que volume

Vous pouvez monter votre partage Azure Files dans votre pod en configurant le volume dans ses spécifications. Créez un fichier nommé `azure-files-pod.yml` avec le contenu suivant. Mettez à jour la valeur `share-name` avec le nom donné au partage Azure Files.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-files-pod
spec:
 containers:
  - image: kubernetes/pause
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: <share-name>
      readOnly: false
```

Utilisez kubectl pour créer un pod.

```azurecli-interactive
kubectl apply -f azure-files-pod.yml
```

Vous disposez maintenant d’un conteneur en cours d’exécution avec le partage Azure Files monté dans le répertoire `/mnt/azure`. Vous pouvez voir le volume monté en inspectant votre pod via `kubectl describe pod azure-files-pod`.

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur les volumes Kubernetes utilisant Azure Files.

> [!div class="nextstepaction"]
> [Plug-in Kubernetes pour Azure Files](https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md)

<!-- LINKS -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
[kubectl-apply]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#apply
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[az-group-create]: /cli/azure/group#az_group_create
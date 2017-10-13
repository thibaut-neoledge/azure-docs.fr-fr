---
title: "Chiffrer les disques sur une machine virtuelle Linux avec Azure CLI 1.0 | Microsoft Docs"
description: "Comment chiffrer des disques sur une machine virtuelle Linux avec Azure CLI 1.0 et le modèle de déploiement Resource Manager"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/06/2017
ms.author: iainfou
ms.openlocfilehash: b436f2d43c41000f4385889edb3fa3983d4a8c66
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="encrypt-disks-on-a-linux-vm-using-the-azure-cli-10"></a>Chiffrer des disques sur une machine virtuelle Linux avec Azure CLI 1.0
Pour renforcer la sécurité et la conformité de la machine virtuelle , les disques virtuels dans Azure peuvent être chiffrés au repos. Les disques sont chiffrés à l’aide de clés de chiffrement sécurisées dans un coffre de clés Azure. Vous contrôlez ces clés de chiffrement et pouvez effectuer un audit de leur utilisation. Cet article explique comment chiffrer des disques virtuels sur une machine virtuelle Linux avec Azure CLI 1.0 et le modèle de déploiement Resource Manager.

## <a name="cli-versions-to-complete-the-task"></a>Versions de l’interface de ligne de commande permettant d’effectuer la tâche
Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

- [Azure CLI 1.0](#quick-commands) : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager (cet article)
- [Azure CLI 2.0](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) : notre interface Azure CLI nouvelle génération pour le modèle de déploiement Resource Manager

## <a name="quick-commands"></a>Commandes rapides
Si vous avez besoin d’accomplir rapidement cette tâche, la section suivante décrit les commandes de base pour chiffrer des disques virtuels sur votre machine virtuelle. Pour obtenir plus d’informations et davantage de contexte pour chaque étape, lisez la suite de ce document, [à partir de cette section](#overview-of-disk-encryption).

La [dernière version d’Azure CLI 1.0](../../xplat-cli-install.md) doit être installée et connectée ainsi, selon le mode Resource Manager :

```azurecli
azure config mode arm
```

Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Exemples de noms de paramètre : `myResourceGroup`, `myKeyVault` et `myVM`.

Tout d’abord, activez le fournisseur Azure Key Vault au sein de votre abonnement Azure puis créez un groupe de ressources. L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `WestUS` :

```azurecli
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

Créez un coffre de clés Azure. L’exemple qui suit permet de créer un coffre de clés nommé `myKeyVault` :

```azurecli
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Créez une clé de chiffrement dans le coffre de clés et activez-la pour le chiffrement du disque. L’exemple qui suit permet de créer une clé nommée `myKey` :

```azurecli
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```

Créez un point de terminaison à l’aide d’Azure Active Directory pour gérer l’authentification et l’échange de clés de chiffrement à partir du coffre de clés. Les valeurs `--home-page` et `--identifier-uris` peuvent ne pas représenter des adresses routables réelles. Pour un niveau de sécurité maximal, vous devez utiliser des clés secrètes de client au lieu de mots de passe. Actuellement, l’interface CLI Azure ne permet pas de générer des clés secrètes de client. Les clés secrètes de client peuvent uniquement être générées dans le portail Azure. L’exemple suivant crée un point de terminaison Azure Active Directory nommé `myAADApp` et utilise un mot de passe `myPassword`. Spécifiez votre propre mot de passe comme suit :

```azurecli
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Notez la valeur `applicationId` indiquée dans la sortie de la commande précédente. Cet ID d’application est utilisé dans les étapes suivantes :

```azurecli
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```

Ajoutez un disque de données à une machine Virtuelle existante. L’exemple suivant ajoute un disque de données à une machine virtuelle nommée `myVM`:

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Examinez les détails de votre coffre de clés et la clé que vous avez créée. Vous avez besoin de l’ID du coffre de clés, de l’URI et de l’URL de la clé à la dernière étape. L’exemple suivant passe détaille un coffre de clés nommé `myKeyVault` et une clé nommée `myKey` :

```azurecli
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Chiffrez vos disques comme suit, en entrant vos propres noms de paramètre :

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

L’interface CLI Azure ne fournit pas le détail des erreurs au cours du processus de chiffrement. Pour obtenir plus d’informations de dépannage, consultez `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log`. Comme la commande précédente comporte plusieurs variables et que vous risquez de ne pas pouvoir identifier avec précision l’origine de l’échec du processus, voici un exemple de commande complet :

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

Pour terminer, réexaminez l’état de chiffrement pour confirmer que vos disques virtuels ont bien été chiffrés. L’exemple suivant vérifie l’état d’une machine virtuelle nommée `myVM` dans le groupe de ressources `myResourceGroup` :

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```

## <a name="overview-of-disk-encryption"></a>Présentation du chiffrement de disque
Les disques virtuels sur des machines virtuelles Linux sont chiffrés au repos à l’aide de la commande [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). Le chiffrement de disques virtuels dans Azure n’entraîne aucun frais. Les clés de chiffrement sont stockées dans le coffre de clés Azure à l’aide d’une protection logicielle, mais vous pouvez importer ou générer vos clés dans des modules de sécurité matériels (HSM) certifiés conformes aux normes FIPS 140-2 de niveau 2. Vous gardez le contrôle de ces clés de chiffrement et pouvez effectuer un audit de leur utilisation. Ces clés de chiffrement servent à chiffrer et à déchiffrer les disques virtuels connectés à votre machine virtuelle. Un point de terminaison Azure Active Directory fournit un mécanisme sécurisé pour l’émission de ces clés de chiffrement lors de la mise sous tension et hors tension des machines virtuelles.

Le processus de chiffrement d’une machine virtuelle est le suivant :

1. Créez une clé de chiffrement dans un coffre de clés Azure.
2. Configurez la clé de chiffrement afin de la rendre utilisable pour le chiffrement des disques.
3. Pour lire la clé de chiffrement à partir du coffre de clés Azure, créez un point de terminaison à l’aide d’Azure Active Directory avec les autorisations appropriées.
4. Exécutez la commande pour chiffrer vos disques virtuels, en spécifiant le point de terminaison Azure Active Directory et la clé de chiffrement appropriée à utiliser.
5. Le point de terminaison Azure Active Directory demande la clé de chiffrement requise au coffre de clés Azure.
6. Les disques virtuels sont chiffrés à l’aide de la clé de chiffrement fournie.

## <a name="supporting-services-and-encryption-process"></a>Services pris en charge et processus de chiffrement
Le chiffrement de disque s’appuie sur les composants supplémentaires suivants :

* **Coffre de clés Azure** - permet de sauvegarder les clés de chiffrement et les clés secrètes utilisées pour le processus de chiffrement/déchiffrement de disque.
  * Le cas échéant, vous pouvez utiliser un coffre de clés Azure existant. Vous n’êtes pas obligé de dédier un coffre de clés au chiffrement des disques.
  * Pour séparer les limites administratives et la visibilité de la clé, vous pouvez créer un coffre de clés dédié.
* **Azure Active Directory** - gère l’échange sécurisé des clés de chiffrement requises et l’authentification des actions demandées.
  * Vous pouvez généralement utiliser une instance Azure Active Directory existante pour héberger votre application.
  * L’application est plus un point de terminaison permettant aux services de coffre de clés et de machine virtuelle de demander et d’obtenir les clés de chiffrement appropriées. Vous ne développez pas de réelle application qui s’intègre à Azure Active Directory.

## <a name="requirements-and-limitations"></a>Spécifications et limitations
Configuration requise et scénarios pris en charge pour le chiffrement de disque :

* Les versions serveur Linux suivantes : Ubuntu, CentOS, SUSE, SUSE Linux Enterprise Server (SLES) et Red Hat Enterprise Linux.
* Toutes les ressources (par exemple, le coffre de clés, le compte de stockage, la machine virtuelle, etc.) doivent appartenir à la même région et au même abonnement Azure.
* Machines virtuelles standard des séries A, D, DS, G et GS.

Le chiffrement de disque n’est actuellement pas pris en charge dans les scénarios suivants :

* Machines virtuelles de niveau de base.
* Machines virtuelles créées à l’aide du modèle de déploiement Classic.
* Désactivation du chiffrement de disque du système d’exploitation sur les machines virtuelles Linux.
* Mise à jour des clés de chiffrement sur une machine virtuelle Linux déjà chiffrée.

## <a name="create-the-azure-key-vault-and-keys"></a>Créer le coffre de clés Azure et les clés
Pour effectuer les étapes restantes de ce guide, la [dernière version d’Azure CLI 1.0](../../xplat-cli-install.md) doit être installée et connectée ainsi, selon le mode Resource Manager :

```azurecli
azure config mode arm
```

Dans les exemples de commandes, remplacez tous les exemples de paramètres par vos propres noms, emplacement et valeurs de clés. Les exemples suivants utilisent une convention de type `myResourceGroup`, `myKeyVault`, `myAADApp`, etc.

La première étape consiste à créer un coffre de clés Azure pour y stocker les clés de chiffrement. Un coffre de clés Azure peut stocker des clés, des clés secrètes ou des mots de passe vous permettant de les implémenter en toute sécurité dans vos applications et services. Pour le chiffrement de disque virtuel, vous utilisez le coffre de clés afin de stocker une clé de chiffrement pour chiffrer ou déchiffrer vos disques virtuels.

Activez le fournisseur Azure Key Vault au sein de votre abonnement Azure puis créez un groupe de ressources. L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `WestUS` :

```azurecli
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

Le coffre de clés Azure contenant les clés de chiffrement et les ressources de calcul associées tels que le stockage et la machine virtuelle elle-même doit résider dans la même région. L’exemple qui suit permet de créer un coffre de clés Azure nommé `myKeyVault` :

```azurecli
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Vous pouvez stocker des clés de chiffrement à l’aide d’une protection logicielle ou HSM (modèle de sécurité matériel). L’utilisation d’une protection HSM nécessite un coffre de clés Premium. Contrairement à l’utilisation d’un coffre de clés standard qui stocke les clés protégées par logiciel, la création d’un coffre de clés Premium entraîne des frais. Pour créer un coffre de clés Premium, ajoutez `--sku Premium` à la commande de l’étape précédente. L’exemple suivant utilise des clés protégées par logiciel puisque nous avons créé un coffre de clés standard.

Pour les deux modèles de protection, la plateforme Azure doit être autorisée à demander les clés de chiffrement lorsque la machine virtuelle démarre pour déchiffrer les disques virtuels. Créez une clé de chiffrement dans votre coffre de clés, puis activez-la afin de l’utiliser pour le chiffrement de disque virtuel. L’exemple suivant crée une clé nommée `myKey` puis l’active pour le chiffrement de disque :

```azurecli
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```


## <a name="create-the-azure-active-directory-application"></a>Création de l’application Azure Active Directory
Lorsque des disques virtuels sont chiffrés ou déchiffrés, vous utilisez un point de terminaison pour gérer l’authentification et l’échange de clés de chiffrement à partir du coffre de clés. Ce point de terminaison, une application Azure Active Directory, permet à la plateforme Azure de demander les clés de chiffrement appropriées pour le compte de la machine virtuelle. Une instance Azure Active Directory par défaut est disponible dans votre abonnement, bien que de nombreuses organisations utilisent des répertoires Azure Active Directory dédiés.

Comme vous ne créez pas une application Azure Active Directory complète, les paramètres `--home-page` et `--identifier-uris` de l’exemple suivant n’ont pas besoin de représenter des adresses routables réelles. L’exemple suivant utilise également un mot de passe au lieu de générer des clés à partir du portail Azure. Pour le moment, la génération de clés ne peut pas être effectuée à partir de l’interface CLI Azure.

Créez votre application Azure Active Directory. L’exemple suivant crée une application nommée `myAADApp` et utilise un mot de passe `myPassword`. Spécifiez votre propre mot de passe comme suit :

```azurecli
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Notez la valeur `applicationId` retournée dans le résultat de la commande précédente. Cet ID d’application est utilisé dans les étapes restantes. Créez ensuite un nom de principal de service (SPN) afin que l’application soit accessible au sein de votre environnement. Pour chiffrer ou déchiffrer correctement des disques virtuels, des autorisations d’accès à la clé de chiffrement stockée dans le coffre de clés doivent être définies afin que l’application Azure Active Directory puisse lire les clés.

Créez le SPN et définissez les autorisations appropriées comme suit :

```azurecli
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```


## <a name="add-a-virtual-disk-and-review-encryption-status"></a>Ajouter un disque virtuel et vérifier l’état de chiffrement
Pour chiffrer réellement certains disques virtuels, ajoutons un disque à une machine virtuelle existante. Ajoutez un disque de données de 5 Go à une machine virtuelle existante comme suit :

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Actuellement, les disques virtuels ne sont pas chiffrés. Vérifiez l’état de chiffrement de votre machine virtuelle comme suit :

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="encrypt-virtual-disks"></a>Chiffrer des disques virtuels
Pour chiffrer maintenant les disques virtuels, vous allez réunir tous les composants précédents :

1. Spécifiez l’application et le mot de passe Azure Active Directory.
2. Spécifiez le coffre de clés qui stockera les métadonnées de vos disques chiffrés.
3. Spécifiez les clés de chiffrement à utiliser pour le chiffrement et le déchiffrement réels.
4. Indiquez si vous souhaitez chiffrer le disque du système d’exploitation, les disques de données ou l’ensemble.

Examinons en détail votre coffre de clés Azure et la clé que vous avez créée car vous aurez besoin de l’ID du coffre de clé, de l’URI et de l’URL de la clé à la dernière étape :

```azurecli
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Chiffrez vos disques virtuels à l’aide du résultat des commandes `azure keyvault show` et `azure keyvault key show` comme suit :

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

La commande précédente comportant plusieurs variables, l’exemple suivant sert de commande de référence :

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

L’interface CLI Azure ne fournit pas le détail des erreurs au cours du processus de chiffrement. Pour plus d’informations de dépannage, consultez `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log` sur la machine virtuelle que vous chiffrez.

Pour terminer, réexaminez l’état de chiffrement pour confirmer que vos disques virtuels ont bien été chiffrés :

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="add-additional-data-disks"></a>Ajouter des disques de données supplémentaires
Une fois que vous avez chiffré vos disques de données, vous pouvez ajouter ultérieurement d’autres disques virtuels à votre machine virtuelle et également les chiffrer. Lorsque vous exécutez la commande `azure vm enable-disk-encryption`, incrémentez la version de la séquence à l’aide du paramètre `--sequence-version`. Ce paramètre de version de séquence permet d’effectuer des opérations répétées sur la même machine virtuelle.

Par exemple, ajoutons un second disque virtuel à votre machine virtuelle comme suit :

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Réexécutez la commande pour chiffrer les disques virtuels, cette fois en ajoutant le paramètre `--sequence-version` et en incrémentant la valeur de notre première exécution comme suit :

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
  --sequence-version 2
```


## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur la gestion du coffre de clés Azure, y compris la suppression des clés de chiffrement et des coffres, consultez [Gérer le coffre de clés à l’aide de l’interface de ligne de commande](../../key-vault/key-vault-manage-with-cli2.md).
* Pour plus d’informations sur le chiffrement de disque, notamment la préparation d’une machine virtuelle personnalisée chiffrée à télécharger vers Azure, consultez [Chiffrement de disque Azure](../../security/azure-security-disk-encryption.md).

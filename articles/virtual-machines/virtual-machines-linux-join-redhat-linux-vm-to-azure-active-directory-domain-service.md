---
title: "Joindre une machine virtuelle Red Hat Linux à un service de domaine Azure Active Directory | Microsoft Docs"
description: "Découvrez comment joindre une machine virtuelle Red Hat Enterprise Linux 7 à un service de domaine Azure Active Directory."
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 847a5391cadb00c68e5ee5e12066fe910fd8a259
ms.openlocfilehash: 017c17dc15596ce05400500d709287b7ce52611e


---

# <a name="join-a-redhat-linux-vm-to-an-azure-active-directory-domain-service"></a>Joindre une machine virtuelle Red Hat Linux à un service de domaine Azure Active Directory

Cet article explique comment joindre une machine virtuelle Red Hat Enterprise Linux (RHEL) 7 à un domaine géré par Azure Active Directory Domain Services (AADDS).  Les conditions requises sont :

- [un compte Azure](https://azure.microsoft.com/pricing/free-trial/)

- [des fichiers de clés SSH publiques et privées](virtual-machines-linux-mac-create-ssh-keys.md)

- [un contrôleur de domaine Azure Active Directory Domain Services DC](../active-directory-domain-services/active-directory-ds-getting-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Commandes rapides

_Remplacez les exemples par vos propres paramètres._

### <a name="switch-the-azure-cli-to-classic-deployment-mode"></a>Basculer l’interface de ligne de commande Azure en mode de déploiement classique

```azurecli
azure config mode asm
```

### <a name="search-for-a-rhel-version-and-image"></a>Rechercher une version et une image de RHEL

```azurecli
azure vm image list | grep "Red Hat"
```

### <a name="create-a-redhat-linux-vm"></a>Créer une machine virtuelle Red Hat Linux

```azurecli
azure vm create myVM \
-o a879bbefc56a43abb0ce65052aac09f3__RHEL_7_2_Standard_Azure_RHUI-20161026220742 \
-g ahmet \
-p myPassword \
-e 22 \
-t "~/.ssh/id_rsa.pub" \
-z "Small" \
-l "West US"
```

### <a name="ssh-to-the-vm"></a>Utiliser une clé SSH sur la machine virtuelle

```bash
ssh -i ~/.ssh/id_rsa ahmet@myVM
```

### <a name="update-yum-packages"></a>Mettre à jour des packages YUM

```bash
sudo yum update
```

### <a name="install-packages-needed"></a>Installer les packages requis

```bash
sudo yum -y install realmd sssd krb5-workstation krb5-libs
```

Maintenant que les packages requis sont installés sur la machine virtuelle Linux, la tâche suivante consiste à joindre cette machine virtuelle au domaine géré.

### <a name="discover-the-aad-domain-services-managed-domain"></a>Découvrir le domaine géré par AAD Domain Services

```bash
sudo realm discover mydomain.com
```

### <a name="initialize-kerberos"></a>Initialiser Kerberos

Vérifiez que vous spécifiez un utilisateur appartenant au groupe « AAD DC Administrators ». Seuls ces utilisateurs peuvent joindre des ordinateurs au domaine géré.

```bash
kinit ahmet@mydomain.com
```

### <a name="join-the-machine-to-the-domain"></a>Joindre la machine au domaine

```bash
sudo realm join --verbose mydomain.com -U 'ahmet@mydomain.com'
```

### <a name="verify-the-machine-is-joined-to-the-domain"></a>Vérifier que la machine est jointe au domaine

```bash
ssh -l ahmet@mydomain.com mydomain.cloudapp.net
```

## <a name="next-steps"></a>Étapes suivantes

* [Infrastructure RHUI (Red Hat Update Infrastructure) pour machines virtuelles Red Hat Enterprise Linux à la demande dans Azure](virtual-machines-linux-update-infrastructure-redhat.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Configuration de Key Vault pour des machines virtuelles dans Azure Resource Manager](virtual-machines-linux-key-vault-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Déploiement et gestion de machines virtuelles à l’aide des modèles Azure Resource Manager et de l’interface de ligne de commande Azure](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Dec16_HO2-->



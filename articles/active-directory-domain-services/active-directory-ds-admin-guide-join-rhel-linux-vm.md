---
title: "Version préliminaire des services de domaine Azure Active Directory : associer une machine virtuelle RHEL à un domaine géré | Microsoft Docs"
description: Joindre une machine virtuelle Linux Red Hat Enterprise aux services de domaine Azure Active Directory
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 87291c47-1280-43f8-8fb2-da1bd61a4942
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: d36b4c1391dad88603ad823055e11c0a4a329c3c
ms.openlocfilehash: 89e97cb903e04efa77cfc6b02484d28a9ccfe6fb
ms.lasthandoff: 01/13/2017


---
# <a name="join-a-red-hat-enterprise-linux-7-virtual-machine-to-a-managed-domain"></a>Joindre une machine virtuelle Red Hat Enterprise Linux 7 à un domaine géré
Cet article indique comment joindre une machine virtuelle Red Hat Enterprise Linux (RHEL) 7 à un domaine géré par les services de domaine Azure Active Directory.

## <a name="provision-a-red-hat-enterprise-linux-virtual-machine"></a>Configurer une machine virtuelle Red Hat Enterprise Linux
Pour approvisionner une machine virtuelle RHEL 7 à l’aide du portail Azure, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

    ![Tableau de bord du portail Azure](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-dashboard.png)
2. Dans le volet gauche, cliquez sur **Nouveau** et tapez **Red Hat** dans le volet de recherche, comme illustré dans la capture d’écran suivante. Les entrées relatives à Red Hat Enterprise Linux s’affichent dans les résultats de recherche. Cliquez sur **Red Hat Enterprise Linux 7.2**.

    ![Sélectionner RHEL dans les résultats](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-find-rhel-image.png)
3. Dans le volet **Tous les fichiers** , l’image relative à Red Hat Enterprise Linux 7.2 doit apparaître dans les résultats de la recherche. Cliquez sur **Red Hat Enterprise Linux 7.2** pour afficher plus d’informations sur l’image de machine virtuelle.

    ![Sélectionner RHEL dans les résultats](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-select-rhel-image.png)
4. Le volet **Red Hat Enterprise Linux 7.2** doit afficher plus d’informations sur l’image de machine virtuelle. Dans la liste déroulante **Sélectionner un modèle de déploiement**, choisissez **Classique**. Ensuite, cliquez sur le bouton **Créer** .

    ![Afficher les détails d’une image](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-clicked.png)
5. Sur la page **Concepts de base** de l’Assistant **Créer une machine virtuelle**, entrez le **nom d’hôte** de la nouvelle machine virtuelle. Spécifiez également un nom d’utilisateur d’administrateur local dans le champ **Nom d’utilisateur** et indiquez une valeur dans le champ **Mot de passe**. Vous pouvez aussi choisir d’utiliser une clé SSH pour authentifier l’utilisateur d’administrateur local. Sélectionnez également un **niveau tarifaire** pour la machine virtuelle.

    ![Créer une machine virtuelle - Page Concepts de base](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-basic-details.png)
6. Sur la page **Taille** de l’Assistant **Créer une machine virtuelle**, sélectionnez la taille de la machine virtuelle.

    ![Créer une machine virtuelle - Sélection de la taille](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-select-vm-size.png)

7. Sur la page **Paramètres** de l’Assistant **Créer une machine virtuelle**, sélectionnez le compte de stockage de la machine virtuelle. Cliquez sur **Réseau virtuel** pour sélectionner le réseau virtuel sur lequel la machine virtuelle Linux doit être déployée. Dans le panneau **Réseau virtuel**, sélectionnez le réseau virtuel dans lequel les services de domaine Azure AD sont disponibles. Pour les besoins de cet exemple, nous allons sélectionner le réseau virtuel « MyPreviewVNet ».

    ![Créer une machine virtuelle - Sélection du réseau virtuel](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-select-vnet.png)
8. Sur la page **Résumé** de l’Assistant **Créer une machine virtuelle**, passez les informations en revue et cliquez sur le bouton **OK**.

    ![Créer une machine virtuelle - Réseau virtuel sélectionné](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-vnet-selected.png)
9. Le déploiement de la nouvelle machine virtuelle basée sur l’image de RHEL 7.2 doit commencer.

    ![Créer une machine virtuelle - Déploiement démarré](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployment-started.png)
10. Au bout de quelques minutes, la machine virtuelle est déployée et prête à être utilisée.

    ![Créer une machine virtuelle - Déployée](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployed.png)

## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Connexion à distance à la machine virtuelle Linux qui vient d’être configurée
La machine virtuelle RHEL 7.2 a été configurée dans Azure. La tâche suivante consiste à se connecter à distance à cette machine virtuelle.

**connecter à la machine virtuelle RHEL 7.2** , suivez les instructions de l’article [Connexion à une machine virtuelle exécutant Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Les étapes restantes indiquées partent du principe que vous utilisez le client SSH PuTTY pour vous connecter à la machine virtuelle RHEL. Pour voir en savoir plus, consultez la page [Téléchargement PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Ouvrez le programme PuTTY.
2. Saisissez une valeur dans le champ **Nom de l’hôte** de la machine virtuelle RHEL. Dans cet exemple, notre machine virtuelle présente le nom d’hôte « contoso-rhel.cloudapp .net ». Si vous n’êtes pas sûr du nom d’hôte de votre machine virtuelle, reportez-vous au tableau de bord de la machine virtuelle sur le portail Azure.

    ![Connexion PuTTY](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-connect.png)
3. Connectez-vous à la machine virtuelle en utilisant les informations d’identification de l’administrateur local que vous avez spécifiées lors de la création de la machine virtuelle. Dans cet exemple, nous avons utilisé le compte d’administrateur local appelé « mahesh ».

    ![Connexion PuTTY](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-login.png)

## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Installer les packages requis sur la machine virtuelle Linux
Lorsque vous êtes connecté à la machine virtuelle, la tâche suivante consiste à installer les packages requis pour la jonction de domaine sur la machine virtuelle. Procédez comme suit :

1. **Installer realmd :** le package realmd est utilisé pour la jonction de domaine. Sur votre terminal PuTTY, saisissez la commande suivante :

    sudo yum install realmd

    ![Installation de realmd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-realmd.png)

    Après quelques minutes, le package realmd doit être installé sur la machine virtuelle.

    ![Package realmd installé](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-installed.png)
2. **Installer sssd :** le package realmd s’appuie sur sssd pour effectuer des jonctions aux domaines. Sur votre terminal PuTTY, saisissez la commande suivante :

    sudo yum install sssd

    ![Installation de sssd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-sssd.png)

    Après quelques minutes, le package sssd doit être installé sur la machine virtuelle.

    ![Package realmd installé](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-sssd-installed.png)
3. **Installer Kerberos :** dans votre terminal PuTTY, saisissez la commande suivante :

    sudo yum install krb5-workstation krb5-libs

    ![Installation de Kerberos](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-kerberos.png)

    Après quelques minutes, le package realmd doit être installé sur la machine virtuelle.

    ![Kerberos installé](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kerberos-installed.png)

## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Joindre la machine virtuelle Linux au domaine géré
Maintenant que les packages requis sont installés sur la machine virtuelle Linux, la tâche suivante consiste à joindre cette machine virtuelle au domaine géré.

1. Découvrez le domaine géré par les services de domaine Azure AD. Sur votre terminal PuTTY, saisissez la commande suivante :

    sudo realm discover CONTOSO100.COM

    ![realm discover](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-discover.png)

    Si la commande **realm discover** ne parvient pas à découvrir votre domaine géré, vérifiez que ce dernier est accessible depuis la machine virtuelle (exécutez une commande ping). Assurez-vous également que la machine virtuelle a bien été déployée dans le réseau virtuel au sein duquel le domaine géré est disponible.
2. Initialisez Kerberos. Sur votre terminal PuTTY, saisissez la commande suivante. Vérifiez que vous spécifiez un utilisateur appartenant au groupe « AAD DC Administrators ». Seuls ces utilisateurs peuvent joindre des ordinateurs au domaine géré.

    kinit bob@CONTOSO100.COM

    ![kinit ](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kinit.png)

    Veillez à spécifier le nom de domaine en majuscules. Dans le cas contraire, kinit échoue.
3. Joignez l’ordinateur au domaine. Sur votre terminal PuTTY, saisissez la commande suivante. Spécifiez le même utilisateur qu’à l’étape précédente (« kinit »).

    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'

    ![Jonction de domaine](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-join.png)

Vous devez voir apparaître un message signalant que l’ordinateur a bien été joint au domaine géré : « La machine a bien été inscrite dans le domaine ».

## <a name="verify-domain-join"></a>Vérifier la jonction de domaine
Vous pouvez vérifier rapidement si l’ordinateur a bien été joint au domaine géré. Connectez-vous à la machine virtuelle RHEL qui vient d’être jointe au domaine via SSH et un compte d’utilisateur de domaine, puis vérifiez que le compte d’utilisateur est correctement résolu.

1. Sur votre terminal PuTTY, saisissez la commande suivante pour vous connecter à la machine virtuelle RHEL qui vient d’être jointe au domaine, à l’aide de SSH. Utilisez un compte de domaine appartenant au domaine géré (par exemple, 'bob@CONTOSO100.COM' dans ce cas).

    ssh -l bob@CONTOSO100.COM contoso-rhel.cloudapp.net
2. Sur votre terminal PuTTY, tapez la commande suivante pour voir si le répertoire de base a été initialisé correctement.

    pwd
3. Sur votre terminal PuTTY, tapez la commande suivante pour voir si les membres du groupe ont été correctement résolus.

    id

Vous trouverez ci-dessous un exemple de sortie de ces commandes :

![Vérifier la jonction de domaine](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-verify-domain-join.png)

## <a name="troubleshooting-domain-join"></a>Résolution des problèmes de jonction de domaine
Reportez-vous à l’article relatif à la [résolution des problèmes de jonction de domaine](active-directory-ds-admin-guide-join-windows-vm.md#troubleshooting-domain-join) .

## <a name="related-content"></a>Contenu connexe
* [Services de domaine Azure AD : guide de prise en main](active-directory-ds-getting-started.md)
* [Joindre une machine virtuelle Windows Server à un domaine géré par les services de domaine Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Connexion à une machine virtuelle exécutant Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Installing Kerberos (Installation de Kerberos)](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7 - Windows Integration Guide (Red Hat Enterprise Linux 7 - Guide d’intégration à Windows)](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)


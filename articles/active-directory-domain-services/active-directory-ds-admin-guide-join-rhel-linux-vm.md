<properties
	pageTitle="Version préliminaire des services de domaine Azure Active Directory : guide d’administration | Microsoft Azure"
	description="Joindre une machine virtuelle Linux Red Hat Enterprise aux services de domaine Azure Active Directory"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="maheshu"/>

# Joindre une machine virtuelle Red Hat Enterprise Linux 7 à un domaine géré
Cet article indique comment joindre une machine virtuelle Red Hat Enterprise Linux (RHEL) 7 à un domaine géré par les services de domaine Azure Active Directory.

## Configurer une machine virtuelle Red Hat Enterprise Linux
Procédez comme suit pour configurer une machine virtuelle RHEL 7 à l’aide du portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

    ![Tableau de bord du portail Azure](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-dashboard.png)

2. Dans le volet de gauche, cliquez sur **Nouveau** et saisissez **Red Hat** dans la barre de recherche, comme illustré dans la capture d’écran ci-dessous. Vous devez voir apparaître les entrées relatives à Red Hat Enterprise Linux dans les résultats de la recherche. Cliquez sur **Red Hat Enterprise Linux 7.2**.

    ![Sélectionner RHEL dans les résultats](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-find-rhel-image.png)

3. Dans le volet **Tous les fichiers**, l’image relative à Red Hat Enterprise Linux 7.2 doit apparaître dans les résultats de la recherche. Cliquez sur **Red Hat Enterprise Linux 7.2** pour afficher plus d’informations sur l’image de machine virtuelle.

    ![Sélectionner RHEL dans les résultats](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-select-rhel-image.png)

4. Le volet **Red Hat Enterprise Linux 7.2** doit afficher plus d’informations sur l’image de machine virtuelle. Dans la liste déroulante **Sélectionner un modèle de déploiement**, choisissez **Classique**. Ensuite, cliquez sur le bouton **Créer**.

    ![Afficher les détails d’une image](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-clicked.png)

5. Dans le volet **Créer une machine virtuelle**, saisissez une valeur dans le champ **Nom de l’hôte** de la machine virtuelle. Spécifiez également un nom d’utilisateur d’administrateur local dans le champ **Nom d’utilisateur** et indiquez une valeur dans le champ **Mot de passe**. Vous pouvez aussi choisir d’utiliser une clé SSH pour authentifier l’administrateur local. Sélectionnez également un **niveau tarifaire** pour la machine virtuelle.

    ![Créer une machine virtuelle - Informations de base](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-basic-details.png)

6. Cliquez sur **Configuration facultative**. Cela doit ouvrir un volet intitulé **Configuration facultative**. Dans le volet **Configuration facultative**, cliquez sur **Réseau** (comme indiqué dans la capture d’écran ci-dessous).

    ![Créer une machine virtuelle - Configuration du réseau virtuel](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-configure-vnet.png)

7. Cela doit ouvrir un volet intitulé **Réseau**. Dans le volet **Réseau**, cliquez sur **Réseau virtuel** afin de sélectionner le réseau virtuel sur lequel la machine virtuelle Linux doit être déployée. Cela doit ouvrir le volet **Réseau virtuel**. Dans le volet **Réseau virtuel**, choisissez l’option **Utiliser un réseau virtuel existant**. Ensuite, sélectionnez le réseau virtuel dans lequel les services de domaine Azure Active Directory sont disponibles. Pour les besoins de cet exemple, nous allons sélectionner le réseau virtuel « MyPreviewVNet ».

    ![Créer une machine virtuelle - Sélection du réseau virtuel](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-select-vnet.png)

8. Sur le volet **Configuration facultative**, cliquez sur le bouton **OK**.

    ![Créer une machine virtuelle - Réseau virtuel sélectionné](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-vnet-selected.png)

9. Vous êtes maintenant prêt à chiffrer la machine virtuelle. Sur le volet **Créer une machine virtuelle**, cliquez sur le bouton **Créer**.

    ![Créer une machine virtuelle - Prête](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm.png)

10. Le déploiement de la nouvelle machine virtuelle basée sur l’image de RHEL 7.2 doit commencer.

  ![Créer une machine virtuelle - Déploiement démarré](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployment-started.png)

11. Au bout de quelques minutes, la machine virtuelle est déployée et prête à être utilisée.

  ![Créer une machine virtuelle - Déployée](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployed.png)



## Connexion à distance à la machine virtuelle Linux qui vient d’être configurée
La machine virtuelle RHEL 7.2 a été configurée dans Azure. La tâche suivante consiste à se connecter à distance à cette machine virtuelle.

Pour vous **connecter à la machine virtuelle RHEL 7.2**, suivez les instructions de l’article [Connexion à une machine virtuelle exécutant Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md).

Les étapes restantes indiquées ci-dessous partent du principe que vous utilisez le client SSH PuTTY pour vous connecter à la machine virtuelle RHEL. Pour voir en savoir plus, consultez la page [Téléchargement PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Ouvrez le programme PuTTY.

2. Saisissez une valeur dans le champ **Nom de l’hôte** de la machine virtuelle RHEL. Dans cet exemple, notre machine virtuelle présente le nom d’hôte « contoso-rhel.cloudapp .net ». Si vous n’êtes pas sûr du nom d’hôte de votre machine virtuelle, reportez-vous au tableau de bord de la machine virtuelle sur le portail Azure.

    ![Connexion PuTTY](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-connect.png)

3. Connectez-vous à la machine virtuelle en utilisant les informations d’identification de l’administrateur local que vous avez spécifiées lors de la création de la machine virtuelle. Dans cet exemple, nous avons utilisé le compte d’administrateur local appelé « mahesh ».

    ![Connexion PuTTY](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-login.png)


## Installer les packages requis sur la machine virtuelle Linux
Lorsque vous êtes connecté à la machine virtuelle, la tâche suivante consiste à installer les packages requis pour la jonction de domaine sur la machine virtuelle. Procédez comme suit :

1. **Installer realmd :** le package realmd est utilisé pour la jonction de domaine. Sur votre terminal PuTTY, saisissez la commande suivante.

    sudo yum install realmd

    ![Installation de realmd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-realmd.png)

    Après quelques minutes, le package realmd doit être installé sur la machine virtuelle.

    ![Package realmd installé](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-installed.png)

3. **Installer sssd :** le package realmd s’appuie sur sssd pour effectuer les opérations de jonction de domaine. Sur votre terminal PuTTY, saisissez la commande suivante.

    sudo yum install sssd

	![Installation de sssd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-sssd.png)

    Après quelques minutes, le package sssd doit être installé sur la machine virtuelle.

    ![Package realmd installé](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-sssd-installed.png)

4. **Installer Kerberos :** dans votre terminal PuTTY, saisissez la commande suivante :

    sudo yum install krb5-workstation krb5-libs

	![Installation de Kerberos](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-kerberos.png)

	Après quelques minutes, le package realmd doit être installé sur la machine virtuelle.

	![Kerberos installé](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kerberos-installed.png)


## Joindre la machine virtuelle Linux au domaine géré
Maintenant que les packages requis sont installés sur la machine virtuelle Linux, la tâche suivante consiste à joindre cette machine virtuelle au domaine géré.

1. Découvrez le domaine géré par les services de domaine Azure AD. Sur votre terminal PuTTY, saisissez la commande suivante.

    sudo realm discover CONTOSO100.COM

	![Commande realm discover](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-discover.png)

	Si la commande **realm discover** ne parvient pas à découvrir votre domaine géré, vérifiez que ce dernier est accessible depuis la machine virtuelle (exécutez une commande ping). Assurez-vous également que la machine virtuelle a bien été déployée dans le réseau virtuel au sein duquel le domaine géré est disponible.

2. Initialisez Kerberos. Sur votre terminal PuTTY, saisissez la commande suivante. Vérifiez que vous spécifiez un utilisateur appartenant au groupe « AAD DC Administrators ». Seuls ces utilisateurs peuvent joindre des ordinateurs au domaine géré.

    kinit bob@CONTOSO100.COM

    ![Kinit](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kinit.png)

    Veillez à spécifier le nom de domaine en majuscules. Dans le cas contraire, kinit échoue.

3. Joignez l’ordinateur au domaine. Sur votre terminal PuTTY, saisissez la commande suivante. Veillez à spécifier le même utilisateur que celui qui est indiqué à l’étape précédente (« kinit »).

    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'

	![Jonction de domaine](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-join.png)

Vous devez voir apparaître un message signalant que l’ordinateur a bien été joint au domaine géré : « La machine a bien été inscrite dans le domaine ».


## Vérifier la jonction de domaine
Vous pouvez vérifier rapidement si l’ordinateur a bien été joint au domaine géré. Pour cela, connectez-vous à la machine virtuelle RHEL qui vient d’être jointe au domaine, via ssh, et vérifiez que le compte d’utilisateur est correctement résolu.

1. Sur votre terminal PuTTY, saisissez la commande suivante pour vous connecter à la machine virtuelle RHEL qui vient d’être jointe au domaine, à l’aide de SSH. Utilisez un compte de domaine appartenant au compte géré (par exemple : bob@CONTOSO100.COM).

    ssh -l bob@CONTOSO100.COM contoso-rhel.cloudapp.net

2. Sur votre terminal PuTTY, saisissez la commande suivante pour voir si le répertoire de base de l’utilisateur a été initialisé correctement.

	pwd

3. Sur votre terminal PuTTY, saisissez la commande suivante pour voir si les membres du groupe de l’utilisateur ont été correctement résolus.

    id

Vous trouverez ci-dessous un exemple de sortie de ces commandes.

![Vérifier la jonction de domaine](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-verify-domain-join.png)


## Résolution des problèmes de jonction de domaine
Reportez-vous à l’article relatif à la [résolution des problèmes de jonction de domaine](active-directory-ds-admin-guide-join-windows-vm.md#troubleshooting-domain-join).


## Plus d’informations
- [Connexion à une machine virtuelle exécutant Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md)
- [Installing Kerberos (Installation de Kerberos)](https://access.redhat.com/documentation/fr-FR/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
- [Red Hat Enterprise Linux 7 - Windows Integration Guide (Red Hat Enterprise Linux 7 - Guide d’intégration à Windows)](https://access.redhat.com/documentation/fr-FR/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)

<!---HONumber=AcomDC_0824_2016-->
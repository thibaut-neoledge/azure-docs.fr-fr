<properties
   pageTitle="Création et téléchargement d’une image de machine virtuelle FreeBSD | Microsoft Azure"
   description="Découvrez comment créer et télécharger un disque dur virtuel (VHD) contenant le système d'exploitation FreeBSD pour créer une machine virtuelle Azure."
   services="virtual-machines"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="01/12/2016"
   ms.author="kyliel"/>

# Création et téléchargement d’un disque dur virtuel FreeBSD dans Azure

Cet article vous montre comment créer et télécharger un disque dur virtuel (VHD) qui contient le système d’exploitation FreeBSD, ce qui vous permet de l’utiliser comme votre image personnelle pour créer des machines virtuelles dans Microsoft Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager


##Composants requis##
Cet article part du principe que vous disposez des éléments suivants :

- **Un abonnement Azure** : si vous n'en possédez pas, vous pouvez créer un compte en quelques minutes. Si vous avez un abonnement MSDN, consultez la page [Avantage Azure pour les abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Dans le cas contraire, consultez [créer un compte d'essai gratuit](https://azure.microsoft.com/pricing/free-trial/).  

- **Outils Microsoft Azure PowerShell** : le module Microsoft Azure PowerShell est installé et configuré de façon à utiliser votre abonnement. Pour télécharger le module, consultez la page [Téléchargements Azure](https://azure.microsoft.com/downloads/). Un didacticiel sur l'installation et la configuration du module est disponible ici. Vous utiliserez l’applet de commande [Téléchargements Azure](https://azure.microsoft.com/downloads/) pour télécharger le disque dur virtuel.

- **Un système d’exploitation FreeBSD installé dans un fichier .vhd** : vous avez installé un système d’exploitation FreeBSD pris en charge dans un disque dur virtuel. Il existe de nombreux outils de création de fichiers .vhd. Par exemple, vous pouvez utiliser une solution de virtualisation telle que Hyper-V pour créer le fichier .vhd et installer le système d'exploitation. Pour obtenir des instructions, consultez la page [Installation du rôle Hyper-V et configuration d'une machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] Azure ne prend pas en charge le nouveau format VHDX. Vous pouvez convertir le disque au format VHD à l’aide du Gestionnaire Hyper-V ou de l’applet de commande [convert-vhd](https://technet.microsoft.com/library/hh848454.aspx).

Cette tâche comprend les cinq étapes suivantes.

## Étape 1 : préparation de l'image pour le téléchargement ##

Concernant l’installation de FreeBSD sur Hyper-v, vous trouverez un didacticiel [ici](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx).

Depuis la machine virtuelle sur laquelle le système d’exploitation FreeBSD a été installé, effectuez les procédures suivantes :

1. **Activation de DHCP**

		# echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
		# service netif restart

2. **Activation de SSH**

    SSH est activé par défaut après l’installation à partir du disque. Dans le cas contraire ou si vous utilisez directement le disque dur virtuel FreeBSD, tapez :

		# echo 'sshd_enable="YES"' >> /etc/rc.conf
		# ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key
		# ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
		# service sshd restart

3. **Configuration d’une console série**

		# echo 'console="comconsole vidconsole"' >> /boot/loader.conf
		# echo 'comconsole_speed="115200"' >> /boot/loader.conf

4. **Installation de sudo**

    Le compte racine est désactivé dans Azure ; vous devez donc utiliser sudo à partir d'un utilisateur sans privilège pour exécuter des commandes avec des privilèges élevés.

		# pkg install sudo

5. Configuration requise pour l'Agent Azure

    5\.1 **Installer python**

		# pkg install python27
		# ln -s /usr/local/bin/python2.7 /usr/bin/python

    5\.2 **Installer wget**

		# pkg install wget

6. **Installation de l’Agent Azure**

    La dernière version de l’agent Azure se trouve toujours sur [github](https://github.com/Azure/WALinuxAgent/releases). La version 2.0.10 et les versions ultérieures sont officiellement prises en charge par FreeBSD 10 (et versions ultérieures). La dernière version de l’Agent Azure pour FreeBSD est la version 2.0.16.

		# wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.10/waagent --no-check-certificate
		# mv waagent /usr/sbin
		# chmod 755 /usr/sbin/waagent
		# /usr/sbin/waagent -install

    **Important** : après l’installation, vérifiez que le logiciel est en cours d’exécution.

		# service –e | grep waagent
		/etc/rc.d/waagent
		# cat /var/log/waagent.log

    Vous pouvez à présent **arrêter** votre machine virtuelle. Vous pouvez également exécuter l'étape 7 avant d'arrêter la machine, mais elle est facultative.

7. Le désapprovisionnement est facultatif. Il consiste à nettoyer le système afin de le rendre prêt pour un réapprovisionnement.

    La commande ci-dessous supprime également le dernier compte d'utilisateur approvisionné et les données associées.

		# waagent –deprovision+user

## Étape 2 : création d'un compte de stockage dans Azure ##

Pour télécharger vers Azure un fichier .vhd permettant de créer une machine virtuelle, vous avez besoin d’un compte de stockage Azure. Pour créer un tel compte, vous pouvez utiliser l’édition classique du portail Azure.

1. Connectez-vous au [Portail Azure Classic](https://manage.windowsazure.com).

2. Dans la barre de commandes, cliquez sur **New**.

3. Cliquez sur **Services de données** > **Stockage** > **Création rapide**.

	![Créer rapidement un compte de stockage](./media/virtual-machines-freebsd-create-upload-vhd/Storage-quick-create.png)

4. Remplissez les champs comme suit :

	- Sous **URL**, entrez un nom de sous-domaine à utiliser dans l'URL du compte de stockage. L'entrée peut être composée de 3 à 24 lettres minuscules et chiffres. Ce nom devient le nom d'hôte contenu dans l'URL utilisée pour adresser les ressources d'objets blob, de files d'attente et de tables pour l'abonnement.

	- Sélectionnez **l'emplacement ou le groupe d'affinités** pour le compte de stockage. Un groupe d’affinités vous permet de mettre vos services cloud et de stockage sur le cloud dans le même centre de données.

	- Indiquez si vous souhaitez utiliser la **géo-réplication** pour le compte de stockage. La géo-réplication est activée par défaut. Cette option permet une réplication gratuite de vos données vers un emplacement secondaire, pour que votre stockage puisse basculer vers cet emplacement en cas de panne sur l’emplacement principal. L'emplacement secondaire est attribué automatiquement. Vous ne pouvez pas le modifier. Si vous avez besoin de disposer d’un contrôle accru sur l’emplacement de votre stockage reposant sur le cloud du fait d’exigences juridiques ou de la stratégie de l'organisation, vous pouvez désactiver la géo-réplication. Cependant, sachez que si vous réactivez la géo-localisation ultérieurement, la réplication de vos données vers un emplacement secondaire sera facturée au tarif d'un transfert unique. Vous pouvez bénéficier d'une réduction pour les services de stockage sans géo-réplication. Vous trouverez plus d'informations sur la gestion de la géo-réplication des comptes de stockage ici : [Création, gestion ou suppression d’un compte de stockage](../storage-create-storage-account/#replication-options).

	![Entrer les détails du compte de stockage](./media/virtual-machines-freebsd-create-upload-vhd/Storage-create-account.png)


5. Cliquez sur **Create Storage Account**. Le compte apparaît à présent sous **Stockage**.

	![Compte de stockage correctement créé](./media/virtual-machines-freebsd-create-upload-vhd/Storagenewaccount.png)

6. Puis, créez un conteneur pour les disques durs virtuels téléchargés. Cliquez sur le Nom du compte de stockage, puis sur **Conteneurs**.

	![Détails du compte de stockage](./media/virtual-machines-freebsd-create-upload-vhd/storageaccount_detail.png)

7. Cliquez sur **Création d’un conteneur**.

	![Détails du compte de stockage](./media/virtual-machines-freebsd-create-upload-vhd/storageaccount_container.png)

8. Saisissez un **Nom** pour votre conteneur et sélectionnez la stratégie **Accès**.

	![Nom du conteneur](./media/virtual-machines-freebsd-create-upload-vhd/storageaccount_containervalues.png)

    > [AZURE.NOTE] Par défaut, le conteneur est privé et seul le propriétaire du compte peut y accéder. Pour autoriser l'accès public en lecture aux objets blob du conteneur, mais pas à ses propriétés et ses métadonnées, utilisez l'option « Objet blob public ». Pour autoriser un accès public total en lecture pour le conteneur et les objets blob, utilisez l'option « Conteneur public ».

## Étape 3 : préparation de la connexion à Microsoft Azure ##

Avant de pouvoir télécharger un fichier .vhd, vous devez établir une connexion sécurisée entre votre ordinateur et votre abonnement Azure. Pour ce faire, vous pouvez utiliser la méthode Microsoft Azure Active Directory ou la méthode par certificat.

###Utilisation de la méthode Microsoft Azure AD

1. Ouvrez la console Azure PowerShell.

2. Tapez la commande suivante : `Add-AzureAccount`

	Cette commande ouvre une fenêtre d'authentification, pour que vous puissiez vous authentifier avec votre compte professionnel ou scolaire.

	![Fenêtre PowerShell](./media/virtual-machines-freebsd-create-upload-vhd/add_azureaccount.png)

3. Azure authentifie et enregistre les informations d’identification, puis ferme la fenêtre.

###Utilisation de la méthode par certificat

1. Ouvrez la console Azure PowerShell.

2. Entrez : `Get-AzurePublishSettingsFile`.

3. Une fenêtre de navigateur apparaît et vous invite à télécharger un fichier .publishsettings. Il contient des informations et un certificat pour votre abonnement Microsoft Azure.

	![Page de téléchargement du navigateur](./media/virtual-machines-freebsd-create-upload-vhd/Browser_download_GetPublishSettingsFile.png)

3. Enregistrez le fichier .publishsettings.

4. Entrez : `Import-AzurePublishSettingsFile <PathToFile>`

	Où `<PathToFile>` est le chemin d'accès complet au fichier .publishsettings.

   Pour plus d'informations, consultez la page [Prise en main des cmdlets Microsoft Azure](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx)

   Pour plus d’information sur l’installation et la configuration de PowerShell, consultez [Installation et configuration de Microsoft Azure PowerShell](../install-configure-powershell.md).

## Étape 4 : téléchargement du fichier .vhd ##

Lorsque vous téléchargez le fichier .vhd, vous pouvez le placer n'importe où dans votre stockage d'objets blob. Dans les exemples de commandes suivants, **BlobStorageURL** correspond à l'URL du compte de stockage que vous avez créé lors de l'étape 2 ; **YourImagesFolder** est le conteneur du stockage d'objets blob où vous voulez stocker vos images. **VHDName** est l’étiquette affichée dans le portail Azure Classic pour identifier le disque dur virtuel. **PathToVHDFile** est le chemin d'accès complet et le nom du fichier .vhd.


1. Depuis la fenêtre Azure PowerShell utilisée lors de l’étape précédente, tapez :

		Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## Étape 5 : Création d’une machine virtuelle avec le disque dur virtuel téléchargé ##
Après avoir téléchargé le fichier .vhd, vous pouvez l'ajouter en tant qu'image à la liste des images personnalisées associée à votre abonnement et créer une machine virtuelle avec cette image personnalisée.

1. Depuis la fenêtre Azure PowerShell utilisée lors de l’étape précédente, tapez :

		Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

    **Important** : utilisez Linux comme type de système d’exploitation pour le moment, car la version actuelle de Microsoft Azure PowerShell accepte uniquement le paramètre « Linux » ou « Windows ».

2. À l’issue de ces étapes, la nouvelle image apparaît sous l’onglet **Images** du portail Azure Classic.

    ![ajouter une image](./media/virtual-machines-freebsd-create-upload-vhd/addfreebsdimage.png)

3. Créez une machine virtuelle à partir de la galerie. Cette nouvelle image est maintenant disponible sous **Mes Images**. Sélectionnez la nouvelle image, puis suivez les invites pour configurer un nom d'hôte, le mot de passe/la clé SSH, etc.

	![image personnalisée](./media/virtual-machines-freebsd-create-upload-vhd/createfreebsdimageinazure.png)

4. Une fois l'approvisionnement terminé, vous verrez votre machine virtuelle FreeBSD exécutée dans Azure.

	![image FreeBSD dans Microsoft Azure](./media/virtual-machines-freebsd-create-upload-vhd/freebsdimageinazure.png)

<!---HONumber=AcomDC_0128_2016-->
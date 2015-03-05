<properties 
   pageTitle="Création et téléchargement d'un disque dur virtuel FreeBSD dans Azure" 
   description="Apprenez à créer et à télécharger un disque dur virtuel (VHD) Azure contenant le système d'exploitation Linux." 
   services="virtual-machines" 
   documentationCenter="" 
   authors="KylieLiang" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services" 
   ms.date="02/05/2015"
   ms.author="kyliel"/>

# Création et téléchargement d'un disque dur virtuel FreeBSD dans Azure 

Cet article vous montre comment créer et télécharger un disque dur virtuel (VHD) qui contient le système d'exploitation FreeBSD, ce qui vous permet de l'utiliser comme votre image personnelle pour créer des machines virtuelles dans Azure. 

## Configuration requise##
Cet article part du principe que vous disposez des éléments suivants :

- **Un abonnement Azure** : si vous n'en possédez pas, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Création d'un compte Azure](http://azure.microsoft.com/documentation/articles/php-create-account/). 

- **Outils Azure PowerShell** : le module Microsoft Azure PowerShell est installé et configuré de façon à utiliser votre abonnement. Pour télécharger le module, consultez la page [Téléchargements Azure](http://azure.microsoft.com/downloads/). Un didacticiel sur l'installation et la configuration du module est disponible ici. Vous utiliserez la cmdlet [Téléchargements Azure](http://azure.microsoft.com/downloads/) pour télécharger le disque dur virtuel.

- **Système d'exploitation FreeBSD installé dans un fichier .vhd**  - Vous avez installé un système d'exploitation Linux pris en charge sur un disque dur virtuel. Il existe de nombreux outils de création de fichiers .vhd. Par exemple, vous pouvez utiliser une solution de virtualisation telle que Hyper-V pour créer le fichier .vhd et installer le système d'exploitation. Pour obtenir des instructions, consultez la page [Installation du rôle Hyper-V et configuration d'une machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx). 

> [AZURE.NOTE] Azure ne prend pas en charge le nouveau format VHDX. Vous pouvez convertir le disque au format VHD à l'aide du Gestionnaire Hyper-V ou l'applet de commande [convert-vhd](https://technet.microsoft.com/fr-fr/library/hh848454.aspx).

Cette tâche comprend les cinq étapes suivantes.

## Étape 1 : préparation de l'image pour le téléchargement ##

Concernant l'installation de FreeBSD sur Hyper-v, vous trouverez un didacticiel [ici](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx).

Depuis la machine virtuelle sur laquelle le système d'exploitation FreeBSD a été installé, effectuez les procédures suivantes :

1. **Activation de DHCP**

		# echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
		# service netif restart

2. **Activation de SSH**

    SSH est activé par défaut après l'installation à partir du disque. Dans le cas contraire ou si vous utilisez FreeBSD VHD directement, tapez :

		# echo 'sshd_enable="YES"' >> /etc/rc.conf 
		# ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key 
		# ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key 
		# service sshd restart

3. **Configuration d'une console série**

		# echo 'console="comconsole vidconsole"' >> /boot/loader.conf
		# echo 'comconsole_speed="115200"' >> /boot/loader.conf

4. **Installation de sudo**

    Le compte racine est désactivé dans Azure ; vous devez donc utiliser sudo à partir d'un utilisateur sans privilège pour exécuter des commandes avec des privilèges élevés.

		# pkg install sudo

5. Configuration requise pour l'Agent Azure

    5.1 **Installer python**

		# pkg install python27 py27-asn1
		# ln -s /usr/local/bin/python2.7 /usr/bin/python

    5.2 **Installer wget**

		# pkg install wget 

6. **Installation de l'Agent Azure**

    La dernière version de l'Agent Azure se trouve toujours sur [github](https://github.com/Azure/WALinuxAgent/releases). La version 2.0.10 et les versions ultérieures sont officiellement prises en charge par FreeBSD 10 (et versions ultérieures).

		# wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.10/waagent --no-check-certificate
		# mv waagent /usr/sbin
		# chmod 755 /usr/sbin/waagent
		# /usr/sbin/waagent -install

    **Important** : Après l'installation, vérifiez que le logiciel est en cours d'exécution.

		# service -e | grep waagent
		/etc/rc.d/waagent
		# cat /var/log/waagent.log

    Vous pouvez à présent **arrêter** votre machine virtuelle. Vous pouvez également exécuter l'étape 7 avant d'arrêter la machine, mais elle est facultative.

7. Le désapprovisionnement est facultatif. Il consiste à nettoyer le système afin de le rendre prêt pour un réapprovisionnement.

    La commande ci-dessous supprime également le dernier compte d'utilisateur approvisionné et les données associées.

		# waagent -deprovision+user

## Étape 2 : création d'un compte de stockage dans Azure ##

Pour télécharger vers Azure un fichier .vhd permettant de créer une machine virtuelle, vous avez besoin d'un compte de stockage Azure. Pour créer un tel compte, vous pouvez utiliser le portail de gestion Azure.

1. Connectez-vous au portail de gestion Azure.

2. Dans la barre de commandes, cliquez sur **Nouveau**.

3. Cliquez sur **Services de données**, **Stockage**, puis**Création rapide**.

	![Quick create a storage account](./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png)

4. Remplissez les champs comme suit :
	
	- Sous **URL**, tapez un nom de sous-domaine à utiliser dans l'URL pour le compte de stockage. L'entrée peut être composée de 3 à 24 lettres minuscules et chiffres. Ce nom devient le nom d'hôte dans l'URL qui est utilisée pour adresser les ressources d'objet Blob, file d'attente ou Table pour l'abonnement.
			
	- Sélectionnez **l'emplacement ou le groupe d'affinités** pour le compte de stockage. Un groupe d'affinités vous permet de mettre vos services cloud et de stockage sur le cloud dans le même centre de données.
		 
	- Indiquez si vous souhaitez utiliser la **géo-réplication** pour le compte de stockage. La géo-réplication est activée par défaut. Cette option permet une réplication gratuite de vos données vers un emplacement secondaire, pour que votre stockage puisse basculer vers cet emplacement en cas de panne sur l'emplacement principal. L'emplacement secondaire est attribué automatiquement. Vous ne pouvez pas le modifier. Si vous avez besoin de disposer d'un contrôle accru sur l'emplacement de votre stockage reposant sur le cloud du fait d'exigences juridiques ou de la stratégie de l'organisation, vous pouvez désactiver la géo-réplication. Cependant, sachez que si vous réactivez la géo-localisation ultérieurement, la réplication de vos données vers un emplacement secondaire sera facturée au tarif d'un transfert unique. Vous pouvez bénéficier d'une réduction pour les services de stockage sans géo-réplication. Vous trouverez plus d'informations sur la gestion de la géo-réplication des comptes de stockage ici : [Création, gestion ou suppression d'un compte de stockage](../storage-create-storage-account/#replication-options).

	![Enter storage account details](./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png)


5. Cliquez sur **Créer un compte de stockage**. Le compte apparaît à présent sous **Stockage**.

	![Storage account successfully created](./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png)

6. Puis, créez un conteneur pour les disques durs virtuels téléchargés. Cliquez sur le Nom du compte de stockage, puis sur **Conteneurs**.

	![Storage account detail](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_detail.png)

7. Cliquez sur **Créer un conteneur**.

	![Storage account detail](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_container.png)

8. Tapez un **Nom** pour votre conteneur et sélectionnez la **Stratégie d'accès**.

	![Container name](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_containervalues.png)

    > [AZURE.NOTE] Par défaut, le conteneur est privé et seul le propriétaire du compte peut y accéder. Pour autoriser l'accès public en lecture aux objets blob du conteneur, mais pas à ses propriétés ni à ses métadonnées, utilisez l'option " Objet blob public ". Pour autoriser un accès public total en lecture pour le conteneur et les objets blob, utilisez l'option " Conteneur public ".

## Étape 3 : Préparation de la connexion à Microsoft Azure ##

Avant de pouvoir télécharger un fichier .vhd, vous devez établir une connexion sécurisée entre votre ordinateur et votre abonnement Azure. Pour ce faire, vous pouvez utiliser la méthode Microsoft Azure Active Directory ou la méthode par certificat.

<h3>Utilisation de la méthode Microsoft Azure AD</h3>

1. Ouvrez la console Azure PowerShell.

2. Tapez la commande suivante :  
	`Add-AzureAccount`
	
	Cette commande ouvre une fenêtre d'authentification, pour que vous puissiez vous authentifier avec votre compte professionnel ou scolaire.

	![PowerShell Window](./media/virtual-machines-create-upload-vhd-windows-server/add_azureaccount.png)

3. Azure authentifie et enregistre les informations d'identification, puis ferme la fenêtre.

<h3>Utilisation de la méthode par certificat</h3> 

1. Ouvrez la console Azure PowerShell. 

2. Tapez : 
	`Get-AzurePublishSettingsFile`.

3. Une fenêtre de navigateur apparaît et vous invite à télécharger un fichier .publishsettings. Il contient des informations et un certificat pour votre abonnement Microsoft Azure.

	![Browser download page](./media/virtual-machines-create-upload-vhd-windows-server/Browser_download_GetPublishSettingsFile.png)

3. Enregistrez le fichier .publishsettings. 

4. Tapez : 
	`Import-AzurePublishSettingsFile <PathToFile>`

	Où `<PathToFile>` est le chemin d'accès complet au fichier .publishsettings. 

   Pour plus d'informations, consultez la page [Prise en main des cmdlets Microsoft Azure](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx) 
	
   Pour plus d'information sur l'installation et la configuration de PowerShell, consultez la page [Installation et configuration de Microsoft Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/) 

## Étape 4 : téléchargement du fichier .vhd ##

Lorsque vous téléchargez le fichier .vhd, vous pouvez le placer n'importe où dans votre stockage d'objets blob. Dans les exemples de commande suivants, **BlobStorageURL** correspond à l'URL du compte de stockage que vous avez créé lors de l'étape 2 et **YourImagesFolder** est le conteneur du stockage d'objets blob où vous souhaitez stocker vos images. **VHDName** est l'étiquette qui apparaît dans le portail de gestion pour identifier le disque dur virtuel. **PathToVHDFile** est le chemin d'accès complet et le nom du fichier .vhd. 


1. Depuis la fenêtre Azure PowerShell utilisée lors de l'étape précédente, tapez :

		Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>		

## Étape 5 : création d'une machine virtuelle avec le disque dur virtuel téléchargé ##
Après avoir téléchargé le fichier .vhd, vous pouvez l'ajouter en tant qu'image à la liste des images personnalisées associée à votre abonnement et créer une machine virtuelle avec cette image personnalisée.

1. Depuis la fenêtre Azure PowerShell utilisée lors de l'étape précédente, tapez :

		Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

    **Important** : Utilisez Linux comme type de système d'exploitation pour le moment, car la version actuelle d'Azure PowerShell accepte uniquement le paramètre " Linux " ou " Windows ".

2. À l'issue de ces étapes, la nouvelle image apparaît sous l'onglet **Images** du portail de gestion Azure.  

    ![add image](./media/virtual-machines-freebsd-create-upload-vhd/addfreebsdimage.png)

3. Créez une machine virtuelle à partir de la galerie. Cette nouvelle image est maintenant disponible sous **Mes Images**. Sélectionnez la nouvelle image, puis suivez les invites pour configurer un nom d'hôte, le mot de passe/la clé SSH, etc. 

	![custom image](./media/virtual-machines-freebsd-create-upload-vhd/createfreebsdimageinazure.png)

4. Une fois l'approvisionnement terminé, vous verrez votre machine virtuelle FreeBSD exécutée dans Azure. 

	![freebsd image in azure](./media/virtual-machines-freebsd-create-upload-vhd/freebsdimageinazure.png)

<!--HONumber=45--> 

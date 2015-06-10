<properties 
	pageTitle="Création et téléchargement d’un disque dur virtuel Windows Server dans Azure" 
	description="Apprenez à créer et à télécharger un disque dur virtuel (VHD) contenant le système d’exploitation Windows Server dans Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="kathydav"/>


# Création et téléchargement d’un disque dur virtuel Windows Server dans Azure#

Cette article vous montre comment télécharger un disque dur virtuel avec un système d'exploitation pour que vous puissiez l'utiliser comme image de base afin de créer des machines virtuelles. Pour plus d'informations sur les disques et les images dans Azure, consultez la page [À propos des disques et des images dans Azure](http://msdn.microsoft.com/library/azure/jj672979.aspx).

> [AZURE.NOTE]Lorsque vous créez une machine virtuelle reposant sur une image, vous pouvez personnaliser les paramètres du système d’exploitation selon les applications que vous prévoyez d'exécuter sur la machine virtuelle. Cette configuration est enregistrée pour cette machine virtuelle et n’a pas d’incidence sur l’image.

## Composants requis##

Cet article part du principe que vous disposez des éléments suivants :

1. **Un abonnement Azure** : si vous n’en avez pas, vous pouvez [ouvrir un compte Azure gratuitement](/pricing/free-trial/?WT.mc_id=A261C142F) : vous obtenez alors des crédits dont vous pouvez vous servir pour tester les services Azure payants, et même lorsqu'ils sont épuisés, vous pouvez conserver le compte et utiliser les services Azure gratuits, notamment Sites Web. Votre carte de crédit ne sera pas débitée tant que vous n'aurez pas explicitement modifié vos paramètres pour demander à l'être. Vous pouvez également [activer les avantages de l'abonnement MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) : votre abonnement MSDN vous donne droit chaque mois à des crédits dont vous pouvez vous servir pour les services Azure payants.   

2. **Microsoft Azure PowerShell** : le module Microsoft Azure PowerShell est installé et configuré de façon à utiliser votre abonnement. Pour télécharger le module, consultez la page [Téléchargements Microsoft Azure](http://azure.microsoft.com/downloads/). Un didacticiel sur l'installation et la configuration du module est disponible [ici](powershell-install-configure.md). Utilisez l’applet de commande [Add-AzureVHD](http://msdn.microsoft.com/library/azure/dn495173.aspx) pour télécharger le fichier VHD.

3. **Un système d'exploitation Windows stocké dans un fichier .vhd** : vous avez installé un système d'exploitation Windows Server pris en charge sur un disque dur virtuel. Plusieurs outils permettent de créer des fichiers .vhd. Vous pouvez utiliser des solutions de virtualisation comme Hyper-V pour créer la machine virtuelle et installer le système d’exploitation. Pour obtenir des instructions, consultez la page [Installation du rôle Hyper-V et configuration d'une machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE]Microsoft Azure ne prend pas en charge le format VHDX. Vous pouvez convertir le disque au format VHD à l'aide de Hyper-V Manager ou de la [cmdlet Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx). Vous trouverez un didacticiel à ce sujet [ici](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).
 
 Voici les versions de Windows Server prises en charge : <P> <TABLE BORDER="1" WIDTH="600"> <TR BGCOLOR="#E9E7E7"> <TH>SE</TH> <TH>SKU</TH> <TH>Service Pack</TH> <TH>Architecture</TH> </TR> <TR> <TD>Windows Server 2012 R2</TD> <TD>Toutes les éditions</TD> <TD>N/A</TD> <TD>x64</TD> </TR> <TR> <TD>Windows Server 2012</TD> <TD>Toutes les éditions</TD> <TD>N/A</TD> <TD>x64</TD> </TR> <TR> <TD>Windows Server 2008 R2</TD> <TD>Toutes les éditions</TD> <TD>SP1</TD> <TD>x64</TD> </TR> </TABLE> </P>


Cette procédure comprend les étapes suivantes :

- [Étape 1 : préparation de l'image pour le téléchargement][]
- [Étape 2 : création d'un compte de stockage dans Azure][]
- [Étape 3 : préparation de la connexion à Azure][]
- [Étape 4 : téléchargement du fichier .vhd][]

## Étape 1 : préparation de l'image pour le téléchargement ##

Avant de pouvoir télécharger l’image sur Microsoft Azure, vous devez la généraliser à l’aide de la commande Sysprep. Pour en savoir plus sur l’utilisation de Sysprep, consultez la page [Introduction à l’utilisation de Sysprep](http://technet.microsoft.com/library/bb457073.aspx).

Depuis la machine virtuelle sur laquelle le système d’exploitation a été installé, effectuez la procédure suivante :

1. Connectez-vous au système d'exploitation.

2. Ouvrez une fenêtre d'invite de commandes en tant qu'administrateur. Remplacez le répertoire par **%windir%\system32\sysprep**, puis exécutez `sysprep.exe`.

	![Ouvrir une fenêtre d'invite de commandes](./media/virtual-machines-create-upload-vhd-windows-server/sysprep_commandprompt.png)

3.	La boîte de dialogue **Outil de préparation système** apparaît.

	![Démarrer Sysprep](./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png)

4.  Dans **Outil de préparation système**, sélectionnez **Enter System Out-of-Box Experience (OOBE)** et vérifiez que la case à cocher Generalize est activée.

5.  Dans **Shutdown Options**, sélectionnez **Shutdown**.

6.  Cliquez sur **OK**.


## Étape 2 : création d'un compte de stockage dans Azure ##

Pour télécharger vers Azure un fichier .vhd permettant de créer une machine virtuelle, vous avez besoin d’un compte de stockage Azure. Pour créer un tel compte, vous pouvez utiliser le portail de gestion Azure.

1. Connectez-vous au portail de gestion Azure.

2. Dans la barre de commandes, cliquez sur **New**.

3. Cliquez sur **Services de données** > **Stockage** > **Création rapide**.

	![Créer rapidement un compte de stockage](./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png)

4. Remplissez les champs comme suit :
	
	- Sous **URL**, entrez un nom de sous-domaine à utiliser dans l'URL du compte de stockage. L'entrée peut être composée de 3 à 24 lettres minuscules et chiffres. Ce nom devient le nom d'hôte contenu dans l'URL utilisée pour adresser les ressources d'objets blob, de files d'attente et de tables pour l'abonnement.
			
	- Sélectionnez **l'emplacement ou le groupe d'affinités** pour le compte de stockage. Un groupe d’affinités vous permet de mettre vos services cloud et de stockage sur le cloud dans le même centre de données.
		 
	- Indiquez si vous souhaitez utiliser la **géo-réplication** pour le compte de stockage. La géo-réplication est activée par défaut. Cette option permet une réplication gratuite de vos données vers un emplacement secondaire, pour que votre stockage puisse basculer vers cet emplacement en cas de panne sur l’emplacement principal. L'emplacement secondaire est attribué automatiquement. Vous ne pouvez pas le modifier. Si vous avez besoin de disposer d’un contrôle accru sur l’emplacement de votre stockage reposant sur le cloud du fait d’exigences juridiques ou de la stratégie de l'organisation, vous pouvez désactiver la géo-réplication. Cependant, sachez que si vous réactivez la géo-localisation ultérieurement, la réplication de vos données vers un emplacement secondaire sera facturée au tarif d'un transfert unique. Vous pouvez bénéficier d'une réduction pour les services de stockage sans géo-réplication. Vous trouverez plus d'informations sur la gestion de la géo-réplication des comptes de stockage ici : [Création, gestion ou suppression d’un compte de stockage](../storage-create-storage-account/#replication-options).

	![Entrer les détails du compte de stockage](./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png)


5. Cliquez sur **Create Storage Account**. Le compte apparaît à présent sous **Stockage**.

	![Compte de stockage correctement créé](./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png)

6. Puis, créez un conteneur pour les disques durs virtuels téléchargés. Cliquez sur le Nom du compte de stockage, puis sur **Conteneurs**.

	![Détails du compte de stockage](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_detail.png)

7. Cliquez sur **Création d'un conteneur**.

	![Détails du compte de stockage](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_container.png)

8. Saisissez un **Nom** pour votre conteneur et sélectionnez la stratégie **Accès**.

	![Nom du conteneur](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_containervalues.png)

	> [AZURE.NOTE]Par défaut, le conteneur est privé et seul le propriétaire du compte peut y accéder. Pour autoriser l'accès public en lecture aux objets blob du conteneur, mais pas à ses propriétés et ses métadonnées, utilisez l'option « Objet blob public ». Pour autoriser un accès public total en lecture pour le conteneur et les objets blob, utilisez l'option « Conteneur public ».

## Étape 3 : préparation de la connexion à Microsoft Azure ##

Avant de pouvoir télécharger un fichier .vhd, vous devez établir une connexion sécurisée entre votre ordinateur et votre abonnement Azure. Pour ce faire, vous pouvez utiliser la méthode Microsoft Azure Active Directory ou la méthode par certificat.

> [AZURE.TIP]Pour commencer à utiliser Microsoft Azure PowerShell, consultez la page [Installation et configuration de Microsoft Azure PowerShell](install-configure-powershell.md). Pour plus d’informations, consultez la page [Prise en main des cmdlets Microsoft Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx).

### Utilisation de la méthode Microsoft Azure AD

1. Ouvrez la console Azure PowerShell.

2. Entrez : `Add-AzureAccount`
	
	Cette commande ouvre une fenêtre d'authentification, pour que vous puissiez vous authentifier avec votre compte professionnel ou scolaire.

	![Fenêtre PowerShell](./media/virtual-machines-create-upload-vhd-windows-server/add_azureaccount.png)

3. Azure authentifie et enregistre les informations d’identification, puis ferme la fenêtre.

### Utilisation de la méthode par certificat 

1. Ouvrez la console Azure PowerShell. 

2.	Entrez : `Get-AzurePublishSettingsFile`.

3. Une fenêtre de navigateur apparaît et vous invite à télécharger un fichier .publishsettings. Il contient des informations et un certificat pour votre abonnement Microsoft Azure.

	![Page de téléchargement du navigateur](./media/virtual-machines-create-upload-vhd-windows-server/Browser_download_GetPublishSettingsFile.png)

3. Enregistrez le fichier .publishsettings.

4. Entrez : `Import-AzurePublishSettingsFile <PathToFile>`

	Où `<PathToFile>` est le chemin d'accès complet au fichier .publishsettings.

## Étape 4 : téléchargement du fichier .vhd

Lorsque vous téléchargez le fichier .vhd, vous pouvez le placer n'importe où dans votre stockage d'objets blob. Dans les exemples de commandes suivants, **BlobStorageURL** correspond à l'URL du compte de stockage que vous avez créé lors de l'étape 2 ; **YourImagesFolder** est le conteneur du stockage d'objets blob où vous voulez stocker vos images. **VHDName** est l'étiquette affichée dans le portail de gestion pour identifier le disque dur virtuel. **PathToVHDFile** est le chemin d'accès complet et le nom du fichier .vhd.


1. Depuis la fenêtre Azure PowerShell utilisée lors de l'étape précédente, tapez :

	`Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>`
	
	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/powershell_upload_vhd.png)

	Pour plus d'informations sur la cmdlet Add-AzureVhd, consultez [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx).

## Étape 5 : ajout de l'image à votre liste d'images personnalisées ##

Une fois le fichier .vhd téléchargé, ajoutez-le comme image dans la liste des images personnalisées associée à votre abonnement.

1. Dans le portail de gestion, sous **All Items**, cliquez sur **Machines virtuelles**.

2. Sous Machines virtuelles, cliquez sur **Images**.

3. Puis, cliquez sur **Create an Image**.

	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image.png)

4. Effectuez les actions suivantes dans **Create an image from a VHD** :
 	

	- Indiquez le **nom**
	- Indiquez la **description**
	- Pour indiquer l’**URL de votre disque dur virtuel**, cliquez sur le bouton de dossier pour ouvrir la fenêtre ci-dessous :
 
	![Sélection du disque dur virtuel](./media/virtual-machines-create-upload-vhd-windows-server/Select_VHD.png)

	- Sélectionnez le compte de stockage de votre disque dur virtuel et cliquez sur **Ouvrir**. Cela vous ramène à la fenêtre **Create an image from a VHD**.
	- Après être revenu à la fenêtre **Create an image from a VHD**, sélectionnez la famille du système d'exploitation.
	- Activez la case à cocher **I have run Sysprep on the virtual machine associated with this VHD** pour confirmer la généralisation du système d'exploitation lors de l'étape 1, puis cliquez sur **OK**. 

	![Ajouter une image](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image_From_VHD.png)

5. **FACULTATIF :** vous pouvez utiliser l’applet de commande Add-AzureVMImage plutôt que le portail de gestion afin d’ajouter votre disque dur virtuel sous forme d’image. Dans la console Azure PowerShell, tapez :

	`Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>`
	
	![PowerShell Add-AzureVMImage](./media/virtual-machines-create-upload-vhd-windows-server/add_azureimage_powershell.png)

6. À l'issue de ces étapes, la nouvelle image apparaîtra dans l'onglet **Images**.


	![image personnalisée](./media/virtual-machines-create-upload-vhd-windows-server/vm_custom_image.png)

	Cette nouvelle image est maintenant disponible dans **Mes Images** lorsque vous créez une machine virtuelle. Pour obtenir des instructions, consultez la page [Création d’une machine virtuelle personnalisée exécutant Windows](virtual-machines-windows-create-custom.md).

	![créer un disque dur dur virtuel à partir d'une image personnalisée](./media/virtual-machines-create-upload-vhd-windows-server/create_vm_custom_image.png)

	> [AZURE.TIP]Si vous recevez une erreur lorsque vous tentez de créer une machine virtuelle, avec ce message, "The VHD https://XXXXX.. has an unsupported virtual size of YYYY bytes. The size must be a whole number (in MBs)" [« Le disque dur virtuel https://XXXXX... a une taille virtuelle non supportée de YYYY octets. La taille doit être un chiffre rond (en Mo) »], cela signifie que votre disque dur virtuel n’a pas un nombre rond en Mo et doit être à taille fixe. Essayez d’utiliser la cmdlet PowerShell Add-AzureVMImage plutôt que le portail de gestion pour ajouter l’image (voir l’étape 5 ci-dessus). Les cmdlets Azure garantissent que le disque dur virtuel répond aux exigences d’Azure.
	
## Étapes suivantes ##
 

Maintenant que vous avez créé une machine virtuelle, tentez de créer une machine virtuelle SQL Server. Pour plus d'informations, consultez [Configuration d'une machine virtuelle SQL Server sur Microsoft Azure](virtual-machines-provision-sql-server.md).

[Étape 1 : préparation de l'image pour le téléchargement]: #prepimage
[Étape 2 : création d'un compte de stockage dans Azure]: #createstorage
[Étape 3 : préparation de la connexion à Azure]: #prepAzure
[Étape 4 : téléchargement du fichier .vhd]: #upload

<!---HONumber=58-->
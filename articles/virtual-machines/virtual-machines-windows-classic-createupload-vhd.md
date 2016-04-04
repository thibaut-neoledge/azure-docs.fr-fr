<properties
	pageTitle="Créer et télécharger un disque dur virtuel Windows Server à l’aide de PowerShell | Microsoft Azure"
	description="Découvrez comment créer et télécharger un disque dur virtuel (VHD) basé sur Windows Server à l’aide du modèle de déploiement classique et d’Azure Powershell."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="cynthn"/>

# Création et téléchargement d’un disque dur virtuel Windows Server dans Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager


Cet article vous montre comment télécharger un disque dur virtuel avec un système d’exploitation pour que vous puissiez l’utiliser comme image de base afin de créer des machines virtuelles. Pour en savoir plus sur les disques et les disques durs virtuels dans Microsoft Azure, consultez la rubrique [À propos des disques et VHD pour machines virtuelles](virtual-machines-linux-about-disks-vhds.md).



## Composants requis

Cet article suppose que vous disposez de :

1. **Un abonnement Azure** : si vous n’en avez pas, vous pouvez [ouvrir un compte Azure gratuitement](/pricing/free-trial/?WT.mc_id=A261C142F) : vous obtenez alors des crédits dont vous pouvez vous servir pour tester les services Azure payants, et même lorsqu’ils sont épuisés, vous pouvez conserver le compte et utiliser les services Azure gratuits, notamment Sites Web. Votre carte de crédit ne sera pas débitée tant que vous n’aurez pas explicitement modifié vos paramètres et demandé à l’être. Vous pouvez également [activer les avantages de l'abonnement MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) : votre abonnement MSDN vous donne droit chaque mois à des crédits dont vous pouvez vous servir pour les services Azure payants.

2. **Microsoft Azure PowerShell** : le module Microsoft Azure PowerShell est installé et configuré de façon à utiliser votre abonnement. Pour télécharger le module, consultez la page [Téléchargements Microsoft Azure](https://azure.microsoft.com/downloads/). Un didacticiel sur l’installation et la configuration du module est disponible [ici](../powershell-install-configure.md). Utilisez l’applet de commande [Add-AzureVHD](http://msdn.microsoft.com/library/azure/dn495173.aspx) pour télécharger le fichier VHD.

3. **Système d’exploitation Windows pris en charge stocké dans un fichier .vhd et associé à une machine virtuelle** ; il existe plusieurs outils pour créer des fichiers .vhd. Vous pouvez utiliser des solutions de virtualisation comme Hyper-V pour créer une machine virtuelle et installer le système d’exploitation. Pour obtenir des instructions, voir [Installation du rôle Hyper-V et configuration d’une machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx). Pour plus d’informations sur les systèmes d’exploitation, voir [Prise en charge du logiciel serveur Microsoft pour les machines virtuelles Microsoft Azure](http://go.microsoft.com/fwlink/p/?LinkId=393550).

> [AZURE.IMPORTANT] Microsoft Azure ne prend pas en charge le format VHDX. Vous pouvez convertir le disque au format VHD à l’aide de Hyper-V Manager ou de la [cmdlet Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx). Pour plus de détail, voir [ce billet de blog](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).

## Étape 1 : Préparer le disque dur virtuel 

Avant de télécharger le disque dur virtuel vers Azure, vous devez le généraliser à l’aide de l’outil Sysprep. Cette opération prépare le disque dur virtuel à utiliser en tant qu’image. Pour plus d’informations sur Sysprep, voir [Introduction à l’utilisation de Sysprep](http://technet.microsoft.com/library/bb457073.aspx).

Depuis la machine virtuelle sur laquelle le système d’exploitation a été installé, effectuez la procédure suivante :

1. Connectez-vous au système d’exploitation.

2. Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur. Remplacez le répertoire par **%windir%\\system32\\sysprep**, puis exécutez `sysprep.exe`.

	![Ouvrir une fenêtre d’invite de commandes](./media/virtual-machines-windows-classic-createupload-vhd/sysprep_commandprompt.png)

3.	La boîte de dialogue **Outil de préparation système** apparaît.

	![Démarrer Sysprep](./media/virtual-machines-windows-classic-createupload-vhd/sysprepgeneral.png)

4.  Dans **Outil de préparation du système**, sélectionnez **Entrer en mode OOBE (Out-of-Box Experience)** et vérifiez que la case à cocher **Généraliser** est activée.

5.  Dans **Shutdown Options**, sélectionnez **Shutdown**.

6.  Cliquez sur **OK**.

## Étape 2 : Créer ou obtenir des informations à partir de votre compte de stockage Azure

Vous avez besoin d’un compte de stockage dans Azure afin d’avoir un emplacement pour télécharger le fichier .vhd. Cette étape vous montre comment créer un compte, ou obtenir les informations dont vous avez besoin d’un compte existant.

### Option 1 : Création d’un compte de stockage

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com).

2. Dans la barre de commandes, cliquez sur **New**.

3. Cliquez sur **Services de données** > **Stockage** > **Création rapide**.

	![Créer rapidement un compte de stockage](./media/virtual-machines-windows-classic-createupload-vhd/Storage-quick-create.png)

4. Remplissez les champs comme suit :

 - Sous **URL**, entrez un nom de sous-domaine à utiliser dans l'URL du compte de stockage. L’entrée peut être composée de 3 à 24 lettres minuscules et chiffres. Ce nom devient le nom d’hôte contenu dans l’URL utilisée pour accéder à des ressources d’objet blob, de files d’attente et de tables pour l’abonnement.
 - Sélectionnez **l’emplacement ou le groupe d’affinités** pour le compte de stockage. Un groupe d’affinités vous permet de mettre vos services cloud et de stockage sur le cloud dans le même centre de données.
 - Indiquez si vous souhaitez utiliser la **géo-réplication** pour le compte de stockage. La géo-réplication est activée par défaut. Cette option permet une réplication gratuite de vos données vers un emplacement secondaire, pour que votre stockage puisse basculer vers cet emplacement en cas de panne sur l’emplacement principal. L'emplacement secondaire est attribué automatiquement. Vous ne pouvez pas le modifier. Si vous avez besoin de disposer d’un contrôle accru sur l’emplacement de votre stockage reposant sur le cloud du fait d’exigences juridiques ou de la stratégie de l’organisation, vous pouvez désactiver la géo-réplication. Cependant, sachez que si vous réactivez la géo-réplication par la suite, la réplication de vos données vers un emplacement secondaire sera facturée au tarif d’un transfert unique. Vous pouvez bénéficier d’une réduction pour les services de stockage sans géo-réplication. Pour plus de détails, consultez [Création, gestion ou suppression d’un compte de stockage](../storage-create-storage-account/#replication-options).

      ![Entrer les détails du compte de stockage](./media/virtual-machines-windows-classic-createupload-vhd/Storage-create-account.png)

5. Cliquez sur **Create Storage Account**. Le compte apparaît à présent sous **Stockage**.

	![Compte de stockage correctement créé](./media/virtual-machines-windows-classic-createupload-vhd/Storagenewaccount.png)

6. Puis, créez un conteneur pour les disques durs virtuels téléchargés. Cliquez sur le Nom du compte de stockage, puis sur **Conteneurs**.

	![Détails du compte de stockage](./media/virtual-machines-windows-classic-createupload-vhd/storageaccount_detail.png)

7. Cliquez sur **Création d’un conteneur**.

	![Détails du compte de stockage](./media/virtual-machines-windows-classic-createupload-vhd/storageaccount_container.png)

8. Saisissez un **Nom** pour votre conteneur et sélectionnez la stratégie **Accès**.

	![Nom du conteneur](./media/virtual-machines-windows-classic-createupload-vhd/storageaccount_containervalues.png)

	> [AZURE.NOTE] Par défaut, le conteneur est privé et seul le propriétaire du compte peut y accéder. Pour autoriser l’accès public en lecture aux objets blob du conteneur, mais pas à ses propriétés et ses métadonnées, utilisez l’option **Objet BLOB public**. Pour autoriser un accès public total en lecture au conteneur et aux objets blob, utilisez l’option **Conteneur public**.

### Option 2 : Obtention des informations du compte de stockage

1.	Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com).

2.	Dans le volet de navigation, cliquez sur **Stockage**.

3.	Cliquez sur le nom du compte de stockage pour ouvrir le tableau de bord, puis cliquez sur **Tableau de bord**.

4.	Dans le tableau de bord, sous **Services**, placer le curseur sur l’URL des objets Blob, cliquez sur l’icône du Presse-papier pour copier l’URL, puis collez et enregistrez-la. Vous allez l’utiliser au moment de générer la commande permettant de télécharger le disque dur virtuel.

## Étape 3 : Connectez-vous à votre abonnement depuis Azure PowerShell

Avant de pouvoir télécharger un fichier .vhd, vous devez établir une connexion sécurisée entre votre ordinateur et votre abonnement Azure. Pour ce faire, vous pouvez utiliser la méthode Microsoft Azure Active Directory ou la méthode par certificat.

> [AZURE.TIP] Pour commencer à utiliser Microsoft Azure PowerShell, voir la page [Installation et configuration de Microsoft Azure PowerShell](../powershell-install-configure.md). Pour des informations, voir la page [Prise en main des applets de commande Microsoft Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx).

### Option 1 : Utilisation de Microsoft Azure AD

1. Ouvrez la console Azure PowerShell.

2. Entrez : `Add-AzureAccount`

3.	Dans les fenêtres de connexion, saisissez le nom d’utilisateur et un mot de passe de votre compte professionnel ou d’établissement scolaire.

4. Azure authentifie et enregistre les informations d’identification, puis ferme la fenêtre.

### Option 2 : Utiliser un certificat

1. Ouvrez la console Azure PowerShell.

2.	Entrez : `Get-AzurePublishSettingsFile`.

3. Une fenêtre de navigateur apparaît et vous invite à télécharger un fichier .publishsettings. Il contient des informations et un certificat pour votre abonnement Microsoft Azure.

	![Page de téléchargement du navigateur](./media/virtual-machines-windows-classic-createupload-vhd/Browser_download_GetPublishSettingsFile.png)

3. Enregistrez le fichier .publishsettings.

4. Entrez : `Import-AzurePublishSettingsFile <PathToFile>`

	Où `<PathToFile>` est le chemin d'accès complet au fichier .publishsettings.

## Étape 4 : téléchargement du fichier .vhd

Lorsque vous téléchargez le fichier .vhd, vous pouvez le placer n’importe où dans votre stockage d’objets blob.

1. Depuis la fenêtre Azure PowerShell utilisée lors de l’étape précédente, tapez une commande similaire à ce qui suit :

	`Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>`

	Où :
	- **BlobStorageURL** correspond à l’URL du compte de stockage.
	- **YourImagesFolder** est le conteneur au sein du stockage d’objets blob dans lequel vous souhaitez stocker vos images.
	- **VHDName** est le nom que le portail Azure Classic affiche pour identifier le disque dur virtuel.
	- **PathToVHDFile** est le chemin d’accès complet et le nom du fichier .vhd.

	![PowerShell Add-AzureVHD](./media/virtual-machines-windows-classic-createupload-vhd/powershell_upload_vhd.png)

Pour plus d’informations sur la cmdlet Add-AzureVhd, voir [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx).

## Étape 5 : Ajout de l’image à votre liste d’images personnalisées

> [AZURE.TIP] Pour utiliser Azure PowerShell au lieu du portail Azure Classic pour ajouter l’image, utilisez l’applet de commande **Add-AzureVMImage**. Par exemple :

>	`Add-AzureVMImage -ImageName <ImageName> -MediaLocation <VHDLocation> -OS <OSType>`

1. Dans le portail Azure Classic, sous **Tous les éléments**, cliquez sur **Machines virtuelles**.

2. Sous Machines virtuelles, cliquez sur **Images**.

3. Cliquez sur **Créer une image**.

	![PowerShell Add-AzureVHD](./media/virtual-machines-windows-classic-createupload-vhd/Create_Image.png)

4. Cela vous ramène à la fenêtre **Créer une image depuis un disque dur virtuel** :

	- Spécifiez le **nom**.

	- Spécifiez la **description**.

	- Sous **URL VHD**, cliquez sur le bouton de dossier pour ouvrir la fenêtre **Parcourir le stockage cloud**. Recherchez le fichier .vhd, puis cliquez sur **Ouvrir**.

    ![Sélection du disque dur virtuel](./media/virtual-machines-windows-classic-createupload-vhd/Select_VHD.png)

5.	Après être revenu dans la fenêtre **Créer une image depuis un disque dur virtuel**, sous **Famille de système d’exploitation**, sélectionnez votre système d’exploitation. Activez la case à cocher **J’ai exécuté Sysprep sur la machine virtuelle associée à ce disque dur virtuel** pour confirmer la généralisation du système d’exploitation, puis cliquez sur **OK**.

    ![Ajouter une image](./media/virtual-machines-windows-classic-createupload-vhd/Create_Image_From_VHD.png)

6. À l’issue de ces étapes, la nouvelle image apparaîtra dans l’onglet **Images**.

	![image personnalisée](./media/virtual-machines-windows-classic-createupload-vhd/vm_custom_image.png)

	Cette nouvelle image est maintenant disponible dans **Mes Images** lorsque vous créez une machine virtuelle. Pour obtenir des instructions, consultez [Création d’une machine virtuelle personnalisée](virtual-machines-windows-classic-createportal.md).

	![créer un disque dur virtuel à partir d’une image personnalisée](./media/virtual-machines-windows-classic-createupload-vhd/create_vm_custom_image.png)

	> [AZURE.TIP] Si vous recevez une erreur lorsque vous tentez de créer une machine virtuelle, avec ce message, "The VHD https://XXXXX.. has an unsupported virtual size of YYYY bytes. The size must be a whole number (in MBs)" [« Le disque dur virtuel https://XXXXX... a une taille virtuelle non supportée de YYYY octets. La taille doit être un chiffre rond (en Mo) »], cela signifie que votre disque dur virtuel n’a pas un nombre rond en Mo et doit être à taille fixe. Essayez d’utiliser l’applet de commande PowerShell **Add-AzureVMImage** plutôt que le portail Azure Classic pour ajouter l’image (voir l’étape 5 ci-dessus). Les cmdlets Azure garantissent que le disque dur virtuel répond aux exigences d’Azure.



[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Create a storage account in Azure]: #createstorage
[Step 3: Prepare the connection to Azure]: #prepAzure
[Step 4: Upload the .vhd file]: #upload

<!---HONumber=AcomDC_0323_2016-->
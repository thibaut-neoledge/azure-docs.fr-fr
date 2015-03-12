<properties 
	pageTitle="Création et téléchargement d'un disque dur virtuel Linux dans Azure" 
	description="Apprenez à créer et à télécharger un disque dur virtuel (VHD) Azure contenant le système d'exploitation Linux." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/13/2015" 
	ms.author="kathydav, szarkos"/>

# Création et téléchargement d'un disque dur virtuel contenant le système d'exploitation Linux

Cet article vous montre comment créer et télécharger un disque dur virtuel pour que vous puissiez l'utiliser comme image personnelle pour créer des machines virtuelles dans Azure. Vous apprendrez comment préparer le système d'exploitation pour créer plusieurs machines virtuelles basées sur cette image.  

> [AZURE.NOTE] Vous n'avez besoin d'aucune expérience préalable avec les machines virtuelles Azure pour suivre les étapes de cet article. Vous devez cependant posséder un compte Azure. Vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Création d'un compte Azure](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/). 

Une machine virtuelle Azure exécute le système d'exploitation basé sur l'image que vous avez choisie lors de sa création. Vos images sont conservées au format VHD, dans des fichiers .vhd au sein de votre compte de stockage. Pour plus d'informations sur les disques et les images dans Azure, consultez la page [Gestion des disques et des images](http://msdn.microsoft.com/library/windowsazure/jj672979.aspx).

Lors de la création de la machine virtuelle, vous pouvez personnaliser certains paramètres du système d'exploitation pour qu'ils correspondent à l'application que vous souhaitez exécuter. Pour obtenir des instructions, consultez le guide [Création d'une machine virtuelle personnalisée](/fr-fr/manage/windows/how-to-guides/custom-create-a-vm/).

**Important** : L'accord de niveau de service de la plateforme Azure s'applique aux machines virtuelles exécutant le système d'exploitation Linux uniquement lorsqu'une des distributions reconnues est utilisée avec les détails de configuration définis dans [cet article](http://support.microsoft.com/kb/2805216). Toutes les distributions Linux fournies dans la galerie d'images Azure sont des distributions reconnues répondant à la configuration requise.


##Configuration requise##
Cet article part du principe que vous disposez des éléments suivants :

- **Un certificat de gestion** : vous avez créé un certificat de gestion pour l'abonnement pour lequel vous souhaitez télécharger un disque dur virtuel, puis exporté ce certificat vers un fichier .cer. Pour plus d'informations sur la création de certificats, consultez la page [Création d'un certificat de gestion pour Azure](http://msdn.microsoft.com/library/windowsazure/gg551722.aspx). 

- **Un système d'exploitation Linux installé dans un fichier .vhd** : vous avez installé un système d'exploitation Linux pris en charge dans un disque dur virtuel. Il existe de nombreux outils de création de fichiers .vhd. Par exemple, vous pouvez utiliser une solution de virtualisation telle que Hyper-V pour créer le fichier .vhd et installer le système d'exploitation. Pour obtenir des instructions, consultez la page [Installation du rôle Hyper-V et configuration d'une machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx). 

	**Important** : Azure ne prend pas en charge le nouveau format VHDX. Vous pouvez convertir le disque au format VHD à l'aide de Hyper-V Manager ou de la cmdlet convert-vhd.

	Pour accéder à la liste des distributions approuvées, consultez la page [Linux dans les distributions approuvées par Azure](../linux-endorsed-distributions). Vous pouvez également consulter la section située à la fin de cet article : [Informations pour les distributions non approuvées](../virtual-machines-linux-create-upload-vhd-generic).

- **Outil en ligne de commande Linux Azure** : si vous utilisez un système d'exploitation Linux pour créer votre image, utilisez les [outils en ligne de commande Azure pour Linux et Mac](http://go.microsoft.com/fwlink/?LinkID=253691&clcid=0x409) pour charger le fichier VHD.

- **Outils Azure Powershell** : la cmdlet `Add-AzureVhd` est également utilisable pour télécharger le fichier VHD. Visitez la page [Téléchargements Azure](http://azure.microsoft.com/downloads/) pour télécharger les cmdlets Azure Powershell. Pour les informations de référence, consultez la page [Add-AzureVhd](http://msdn.microsoft.com/library/windowsazure/dn495173.aspx).


Cette procédure comprend les étapes suivantes :

- [Étape 1 : préparation de l'image pour le téléchargement] []
- [Étape 2 : création d'un compte de stockage dans Azure] []
- [Étape 3 : préparation de la connexion à Azure] []
- [Étape 4 : téléchargement de l'image sur Azure] []

## <a id="prepimage"> </a>Étape 1 : préparation de l'image pour le téléchargement ##

Microsoft Azure prend en charge diverses distributions de Linux (voir [Distributions Linux approuvées](../linux-endorsed-distributions)). Les articles suivants vous montrent comment préparer les diverses distributions de Linux prises en charge dans Azure :

- **[Distributions basées sur CentOS](../virtual-machines-linux-create-upload-vhd-centos)**
- **[Oracle Linux](../virtual-machines-linux-create-upload-vhd-oracle)**
- **[SLES et openSUSE](../virtual-machines-linux-create-upload-vhd-suse)**
- **[Ubuntu](../virtual-machines-linux-create-upload-vhd-ubuntu)**
- **[Autres - Distributions non approuvées](../virtual-machines-linux-create-upload-vhd-generic)**

Consultez également les **[Notes d'installation Linux](../virtual-machines-linux-create-upload-vhd-generic/#linuxinstall)** pour obtenir des conseils supplémentaires sur la préparation d'images Linux pour Azure.

En suivant toutes les étapes indiquées dans ces guides, vous devez disposer d'un fichier VHD prêt pour le téléchargement dans Azure.


## <a id="createstorage"> </a>Étape 2 : création d'un compte de stockage dans Azure ##

Un compte de stockage représente le plus haut niveau d'espace de noms pour l'accès aux services de stockage et est associé avec votre abonnement Azure. Pour télécharger vers Azure un fichier .vhd permettant de créer une machine virtuelle, vous avez besoin d'un compte de stockage Azure. Vous pouvez créer un compte de stockage à partir du portail de gestion Azure..

1. Connectez-vous au portail de gestion Azure.

2. Dans la barre de commandes, cliquez sur **Nouveau**.

	![Create storage account](./media/virtual-machines-linux-create-upload-vhd/create.png)

3. Cliquez sur **Compte de stockage**, puis sur **Création rapide**.

	![Quick create a storage account](./media/virtual-machines-linux-create-upload-vhd/storage-quick-create.png)

4. Remplissez les champs comme suit :

	![Enter storage account details](./media/virtual-machines-linux-create-upload-vhd/storage-create-account.png)

- Sous **URL**, entrez un nom de sous-domaine à utiliser dans l'URL du compte de stockage. L'entrée peut être composée de 3 à 24 lettres minuscules et chiffres. Ce nom devient le nom d'hôte contenu dans l'URL utilisée pour adresser les ressources d'objets blob, de files d'attente et de tables pour l'abonnement.
	
- Sélectionnez l'emplacement ou le groupe d'affinités pour le compte de stockage. En indiquant un groupe d'affinités, vous pouvez localiser vos services cloud dans le même centre de données, avec votre stockage.
 
- Indiquez si vous souhaitez utiliser la géo-réplication pour le compte de stockage. La géo-réplication est activée par défaut. Cette option permet une réplication gratuite de vos données vers un emplacement secondaire, pour que votre stockage puisse basculer vers l'emplacement secondaire en cas de panne sur l'emplacement principal. L'emplacement secondaire est attribué automatiquement. Vous ne pouvez pas le modifier. Si des obligations légales ou des stratégies organisationnelles requièrent un meilleur contrôle de l'emplacement de votre stockage sur le cloud, vous pouvez désactiver la géo-réplication. Cependant, sachez que si vous réactivez la géo-localisation ultérieurement, la réplication de vos données vers un emplacement secondaire sera facturée au tarif d'un transfert unique. Vous pouvez bénéficier d'une réduction pour les services de stockage sans géo-réplication.

5. Cliquez sur **Créer un compte de stockage**.

	Le compte apparaît à présent sous **Comptes de stockage**.

	![Storage account successfully created](./media/virtual-machines-linux-create-upload-vhd/Storagenewaccount.png)


## <a id="connect"> </a>Étape 3 : préparation de la connexion à Azure ##

Avant de pouvoir télécharger un fichier .vhd, vous devez établir une connexion sécurisée entre votre ordinateur et votre abonnement Azure. 

1. Ouvrez une fenêtre Azure PowerShell.

2. Tapez : 

	`Get-AzurePublishSettingsFile`

	Cette commande ouvre une fenêtre de navigateur et télécharge automatiquement un fichier .publishsettings contenant des informations et un certificat pour votre abonnement Azure. 

3. Enregistrez le fichier .publishsettings. 

4. Tapez :

	`Import-AzurePublishSettingsFile <PathToFile>`

	Où `<PathToFile>` correspond au chemin d'accès complet vers le fichier .publishsettings. 

	Pour plus d'informations, consultez la page [Prise en main des cmdlets Azure](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx) 


## <a id="upload"> </a>Étape 4 : téléchargement de l'image sur Azure ##

Lorsque vous téléchargez le fichier .vhd, vous pouvez le placer n'importe où dans votre stockage d'objets blob. Dans les exemples de commandes suivants, **BlobStorageURL** correspond à l'URL du compte de stockage que vous avez créé lors de l'étape 2 et **YourImagesFolder** est le conteneur du stockage d'objets blob où vous voulez stocker vos images. **VHDName** est l'étiquette affichée dans le portail de gestion pour identifier le disque dur virtuel. **PathToVHDFile** est le chemin d'accès complet et le nom du fichier .vhd. 

Effectuez l'une des actions suivantes :

- Depuis la fenêtre Azure PowerShell utilisée lors de l'étape précédente, tapez :

		`Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`

	Pour plus d'informations, consultez la page [Add-AzureVhd](http://msdn.microsoft.com/library/windowsazure/dn205185.aspx).

- Utilisez l'outil en commande de ligne Linux pour télécharger l'image. La commande suivante permet de télécharger une image :

		# azure vm image create <image-name> --location <location-of-the-data-center> --os Linux <source-path-to the vhd>



[Étape 1 : préparation de l'image pour le téléchargement]: #prepimage
[Étape 2 : création d'un compte de stockage dans Azure]: #createstorage
[Étape 3 : préparation de la connexion à Azure]: #connect
[Étape 4 : téléchargement de l'image sur Azure]: #upload



<!--HONumber=42-->

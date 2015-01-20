<properties urlDisplayName="Upload a VHD" pageTitle="Création et téléchargement d'un disque dur virtuel Windows Server dans Azure" metaKeywords="disque dur virtuel Azure, téléchargement de disque dur virtuel" description="Apprenez à créer et à télécharger un disque dur virtuel (VHD) contenant le système d'exploitation Windows Server dans Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Create and upload a Windows Server VHD to Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="09/23/2014" ms.author="kathydav" />


#Création et téléchargement d'un disque dur virtuel Windows Server dans Azure#

Cette article vous montre comment télécharger un disque dur virtuel avec un système d'exploitation pour que vous puissiez l'utiliser comme image de base afin de créer des machines virtuelles. Pour plus d'informations sur les disques et les images dans Microsoft Azure, consultez la page [À propos des disques et des images dans Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj672979.aspx).

**Remarque** : Lorsque vous créez une machine virtuelle, vous pouvez personnaliser les paramètres de son système d'exploitation pour faciliter l'exécution de votre application. La configuration que vous définissez pour une machine virtuelle est stockée sur un disque. Pour obtenir des instructions, consultez le guide [Création d'une machine virtuelle personnalisée](http://www.windowsazure.com/fr-fr/documentation/articles/virtual-machines-windows-tutorial/).

##Conditions préalables##
Cet article part du principe que vous disposez des éléments suivants :

**Un abonnement Azure** : si vous n'en possédez pas, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Création d'un compte Azure](http://www.windowsazure.com/fr-fr/develop/php/tutorials/create-a-windows-azure-account/).  

**Microsoft Azure PowerShell** : le module Microsoft Azure PowerShell est installé. Pour télécharger le module, consultez la page [Téléchargements Microsoft Azure](http://www.windowsazure.com/fr-fr/downloads/). Vous trouverez [ici](http://www.windowsazure.com/fr-fr/documentation/articles/install-configure-powershell/) un didacticiel expliquant comment installer et configurer PowerShell avec votre abonnement Azure.

- L'applet de commande [Add-AzureVHD](http://msdn.microsoft.com/fr-fr/library/windowsazure/dn205185.aspx), qui fait partie du module Microsoft Azure PowerShell. Vous utiliserez cette cmdlet pour télécharger le disque dur virtuel.

**Un système d'exploitation Windows stocké dans un fichier .vhd** : vous avez installé un système d'exploitation Windows Server pris en charge sur un disque dur virtuel. Plusieurs outils permettent de créer des fichiers .vhd. Vous pouvez utiliser des solutions de virtualisation comme Hyper-V pour créer le fichier .vhd et installer le système d'exploitation. Pour obtenir des instructions, consultez la page [Installation du rôle Hyper-V et configuration d'une machine virtuelle](http://technet.microsoft.com/fr-fr/library/hh846766.aspx).

**Important** : Microsoft Azure ne prend pas en charge le format VHDX. Vous pouvez convertir le disque au format VHD à l'aide de Hyper-V Manager ou de l'[applet de commande Convert-VHD](http://technet.microsoft.com/fr-fr/library/hh848454.aspx). Vous trouverez un didacticiel à ce sujet [ici](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).
 
**Support de système d'exploitation Windows Server.** Cette tâche nécessite un fichier .iso qui contient le système d'exploitation Windows Server. Les versions suivantes de Windows Server sont prises en charge :
<P>
  <TABLE BORDER="1" WIDTH="600">
  <TR BGCOLOR="#E9E7E7">
    <TH>SE</TH>
    <TH>SKU</TH>
    <TH>Service Pack</TH>
    <TH>Architecture</TH>
  </TR>
  <TR>
    <TD>Windows Server 2012 R2</TD>
    <TD>Toutes les éditions</TD>
    <TD>N/A</TD>
    <TD>x64</TD>
  </TR>
  <TR>
    <TD>Windows Server 2012</TD>
    <TD>Toutes les éditions</TD>
    <TD>N/A</TD>
    <TD>x64</TD>
  </TR>
  <TR>
    <TD>Windows Server 2008 R2</TD>
    <TD>Toutes les éditions</TD>
    <TD>SP1</TD>
    <TD>x64</TD>
  </TR>
  </TABLE>
</P>


Cette procédure comprend les étapes suivantes :

- [Étape 1 : préparation de l'image pour le téléchargement] []
- [Étape 2 : création d'un compte de stockage dans Azure] []
- [Étape 3 : préparation de la connexion à Azure] []
- [Étape 4 : téléchargement du fichier .vhd] []

## <a id="prepimage"> </a>Étape 1 : préparation de l'image pour le chargement ##


Avant de pouvoir télécharger l'image sur Azure, elle doit être généralisée en utilisant la commande Sysprep. Pour plus d'informations sur l'utilisation de Sysprep, consultez la page [Utilisation de Sysprep : introduction](http://technet.microsoft.com/fr-fr/library/bb457073.aspx).

Dans la machine virtuelle que vous venez de créer, suivez la procédure ci-dessous :

1. Connectez-vous au système d'exploitation.

2. Ouvrez une fenêtre d'invite de commandes en tant qu'administrateur. Basculez vers le répertoire **%windir%\system32\sysprep**, puis exécutez `sysprep.exe`.

	![Open Command Prompt window](./media/virtual-machines-create-upload-vhd-windows-server/sysprep_commandprompt.png)

3.	La boîte de dialogue **Outil de préparation système** apparaît.

	![Start Sysprep](./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png)

4.  Dans **Outil de préparation système**, sélectionnez **Entrer en mode OOBE (Out-of-Box Experience)** et vérifiez que la case Généraliser est cochée.

5.  Dans **Shutdown Options**, sélectionnez **Shutdown**.

6.  Cliquez sur **OK**. 




## <a id="createstorage"> </a>Étape 2 : création d'un compte de stockage dans Azure ##

Un compte de stockage représente le plus haut niveau d'espace de noms pour l'accès aux services de stockage et est associé avec votre abonnement Azure. Pour télécharger vers Azure un fichier .vhd permettant de créer une machine virtuelle, vous avez besoin d'un compte de stockage Azure. Pour créer un tel compte, vous pouvez utiliser le portail de gestion Azure.

1. Connectez-vous au portail de gestion Azure.

2. Dans la barre de commandes, cliquez sur **Nouveau**.

3. Cliquez sur **Compte de stockage**, puis sur **Création rapide**.

	![Quick create a storage account](./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png)

4. Remplissez les champs comme suit :

	
	
- Sous **URL**, entrez un nom de sous-domaine à utiliser dans l'URL du compte de stockage. L'entrée peut être composée de 3 à 24 lettres minuscules et chiffres. Ce nom devient le nom d'hôte contenu dans l'URL utilisée pour adresser les ressources d'objets blob, de files d'attente et de tables pour l'abonnement.
			
- Sélectionnez l'**emplacement ou le groupe d'affinités** pour le compte de stockage. En indiquant un groupe d'affinités, vous pouvez localiser vos services cloud dans le même centre de données, avec votre stockage.
		 
- Indiquez si vous souhaitez utiliser la **géo-réplication** pour le compte de stockage. La géo-réplication est activée par défaut. Cette option permet une réplication gratuite de vos données vers un emplacement secondaire, pour que votre stockage puisse basculer vers l'emplacement secondaire en cas de panne sur l'emplacement principal. L'emplacement secondaire est attribué automatiquement. Vous ne pouvez pas le modifier. Si des obligations légales ou des stratégies organisationnelles requièrent un meilleur contrôle de l'emplacement de votre stockage sur le cloud, vous pouvez désactiver la géo-réplication. Cependant, sachez que si vous réactivez la géo-localisation ultérieurement, la réplication de vos données vers un emplacement secondaire sera facturée au tarif d'un transfert unique. Vous pouvez bénéficier d'une réduction pour les services de stockage sans géo-réplication. Vous trouverez plus d'informations sur la gestion de la géo-réplication des comptes de stockage ici : [Création, gestion ou suppression d'un compte de stockage](../storage-create-storage-account/#replication-options).

	![Enter storage account details](./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png)

5. Cliquez sur **Créer un Compte de stockage**.

	Le compte apparaît à présent sous **Comptes de stockage**.

	![Storage account successfully created](./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png)

6. Puis, créez un conteneur pour les disques durs virtuels téléchargés. Cliquez sur le **Nom du compte de stockage**, plus sur **Conteneurs**.

	![Storage account detail](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_detail.png)

7. Cliquez sur **Créer un conteneur**.

	![Storage account detail](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_container.png)

8. Tapez un **Nom** pour votre conteneur et sélectionnez la **Stratégie d'accès**.

	![Container name](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_containervalues.png)

	> [WACOM.NOTE] Par défaut, le conteneur est privé et seul le propriétaire du compte peut y accéder. Pour autoriser l'accès public en lecture aux objets blob du conteneur, mais pas à ses propriétés et ses métadonnées, utilisez l'option " Objet blob public ". Pour autoriser un accès public total en lecture pour le conteneur et les objets blob, utilisez l'option " Conteneur public ".

## <a id="PrepAzure"> </a>Étape 3 : préparation de la connexion à Microsoft Azure ##

Avant de pouvoir télécharger un fichier .vhd, vous devez établir une connexion sécurisée entre votre ordinateur et votre abonnement Microsoft Azure. Pour ce faire, vous pouvez utiliser la méthode Microsoft Azure Active Directory ou la méthode par certificat.

<h3>Utilisation de la méthode Microsoft Azure AD</h3>

1. Ouvrez la console Microsoft Azure PowerShell, comme indiqué dans la rubrique [Installation de Microsoft Azure PowerShell](#Install).

2. Tapez la commande suivante :  
	`Add-AzureAccount`
	
	Cette commande ouvre une fenêtre d'authentification, pour que vous puissiez vous authentifier avec votre compte professionnel ou scolaire.

	![PowerShell Window](./media/virtual-machines-create-upload-vhd-windows-server/add_azureaccount.png)

3. Microsoft Azure authentifie et enregistre les informations d'identification, puis ferme la fenêtre.

<h3>Utilisation de la méthode par certificat</h3> 

1. Ouvrez une fenêtre Microsoft Azure PowerShell. 

2.	Type : 
	`Get-AzurePublishSettingsFile`.


3. Une fenêtre de navigateur apparaît et vous invite à télécharger un fichier .publishsettings. Il contient des informations et un certificat pour votre abonnement Microsoft Azure.

	![Browser download page](./media/virtual-machines-create-upload-vhd-windows-server/Browser_download_GetPublishSettingsFile.png)

3. Enregistrez le fichier .publishsettings. 

4. Type : 
	`Import-AzurePublishSettingsFile <PathToFile>`

	Où <PathToFile> correspond au chemin d'accès complet au fichier .publishsettings. 


	Pour plus d'informations, consultez la page [Prise en main des applets de commande Microsoft Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj554332.aspx) 
	
	Pour plus d'information sur l'installation et la configuration de PowerShell, consultez la page [Installation et configuration de Microsoft Azure PowerShell](http://www.windowsazure.com/fr-fr/documentation/articles/install-configure-powershell/) 


## <a id="upload"> </a>Étape 4 : téléchargement du fichier .vhd ##

Lorsque vous téléchargez le fichier .vhd, vous pouvez le placer n'importe où dans votre stockage d'objets blob. Dans les exemples de commandes suivants, **BlobStorageURL** correspond à l'URL du compte de stockage que vous avez créé lors de l'étape 2 et **YourImagesFolder** est le conteneur du stockage d'objets blob où vous voulez stocker vos images. **VHDName** est l'étiquette affichée dans le portail de gestion pour identifier le disque dur virtuel. **PathToVHDFile** est le chemin d'accès complet et le nom du fichier .vhd. 


1. Depuis la fenêtre Microsoft Azure PowerShell utilisée lors de l'étape précédente, tapez :

	`Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>`
	
	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/powershell_upload_vhd.png)

	Pour plus d'informations sur l'applet de commande Add-AzureVhd, consultez [Add-AzureVhd](http://msdn.microsoft.com/fr-fr/library/dn495173.aspx).

##Ajout de l'image à votre liste d'images personnalisées ##
Une fois le fichier .vhd téléchargé, ajoutez-le comme image dans la liste des images personnalisées associée à votre abonnement.

1. Dans le portail de gestion, sous **Tous les éléments**, cliquez sur **Machines virtuelles**.

2. Sous Machines virtuelles, cliquez sur **Images**.

3. Ensuite, cliquez sur **Créer une image**.

	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image.png)

4. Dans **Créer une image depuis un disque dur virtuel**, procédez comme suit :
 	
	- Indiquez le **nom**
	- Indiquez la **description**
	- Pour indiquer l'**URL de votre disque dur virtuel**, cliquez sur le bouton de dossier pour ouvrir la boîte de dialogue ci-dessous
 
	![Select VHD](./media/virtual-machines-create-upload-vhd-windows-server/Select_VHD.png)
	- Sélectionnez le compte de stockage de votre disque dur virtuel et cliquez sur **Ouvrir**. Cela vous ramène à la fenêtre **Créer une image depuis un disque dur virtuel**.
	- Après être revenu à la fenêtre **Créer une image depuis un disque dur virtuel**, sélectionnez la famille du système d'exploitation.
	- Cochez la case **J'ai exécuté Sysprep sur la machine virtuelle associée à ce disque dur virtuel** pour confirmer la généralisation du système d'exploitation lors de l'étape 1, puis cliquez sur **OK**. 

	![Add Image](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image_From_VHD.png)

5. **FACULTATIF :** vous pouvez également utiliser l'applet de commande Add-AzureVMImage d'Azure PowerShell pour ajouter votre disque dur virtuel en tant qu'image.

	Dans la fenêtre Microsoft Azure PowerShell, saisissez :

	`Add-AzureVMImage -ImageName <nom_de_votre_image> -MediaLocation <emplacement_de_votre_disque_dur_virtuel> -OS <Type_de_système_d'exploitation_sur_le_disque_dur_virtuel>`
	
	![PowerShell Add-AzureVMImage](./media/virtual-machines-create-upload-vhd-windows-server/add_azureimage_powershell.png)

6. À l'issue de ces étapes, la nouvelle image apparaît sous l'onglet **Images**. 


	![custom image](./media/virtual-machines-create-upload-vhd-windows-server/vm_custom_image.png)

	Vous pouvez à présent utiliser cette image au cours de la création d'une nouvelle machine virtuelle. Sélectionnez **Mes Images** pour afficher la nouvelle image. Pour obtenir des instructions, consultez le didacticiel [Création d'une machine virtuelle exécutant Windows Server](http://www.windowsazure.com/fr-fr/documentation/articles/virtual-machines-windows-tutorial/).

	![create VM from custom image](./media/virtual-machines-create-upload-vhd-windows-server/create_vm_custom_image.png)

## Étapes suivantes##
 

Maintenant que vous avez créé une machine virtuelle, tentez de créer une machine virtuelle SQL Server. Pour plus d'informations, consultez la page [Approvisionnement d'une machine virtuelle SQL Server sur Microsoft Azure](http://www.windowsazure.com/fr-fr/documentation/articles/virtual-machines-provision-sql-server/). 

[Étape 1 : préparation de l'image pour le téléchargement]: #prepimage
[Étape 2 : création d'un compte de stockage dans Azure]: #createstorage
[Étape 3 : préparation de la connexion à Azure]: #prepAzure
[Étape 4 : téléchargement du fichier .vhd]: #upload

<!--HONumber=35.2-->

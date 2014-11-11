<properties linkid="manage-windows-common-task-upload-vhd" urlDisplayName="Upload a VHD" pageTitle="Create and upload a Windows Server VHD to Azure" metaKeywords="Azure VHD, uploading VHD" description="Learn to create and upload a virtual hard disk (VHD) in Azure that has the Windows Server operating system." metaCanonical="" services="virtual-machines" documentationCenter="" title="Create and upload a Windows Server VHD to Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="09/23/2014" ms.author="kathydav" />

# Création et téléchargement d'un disque dur virtuel contenant le système d'exploitation Windows Server dans Azure

Cette article vous montre comment télécharger un disque dur virtuel avec un système d'exploitation pour que vous puissiez l'utiliser comme image de base afin de créer des machines virtuelles. Pour plus d'informations sur les disques et les images dans Azure, consultez la page [À propos des disques et des images dans Azure][À propos des disques et des images dans Azure].

**Remarque** : Lorsque vous créez une machine virtuelle, vous pouvez personnaliser les paramètres de son système d'exploitation pour faciliter l'exécution de votre application. La configuration que vous définissez pour une machine virtuelle est stockée sur un disque. Pour obtenir des instructions, consultez le guide [Création d'une machine virtuelle personnalisée][Création d'une machine virtuelle personnalisée].

## Configuration requise

Cet article part du principe que vous disposez des éléments suivants :

**Un abonnement Azure** - Si vous n'en possédez pas, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d’informations, consultez la page [Création d’un compte Azure][Création d’un compte Azure].

**Microsoft Azure PowerShell** - Le module Microsoft Azure PowerShell est installé. Pour télécharger le module, consultez la page [Téléchargements Microsoft Azure][Téléchargements Microsoft Azure]. Vous trouverez [ici][ici] un didacticiel expliquant comment installer et configurer PowerShell avec votre abonnement Azure.

-   La cmdlet [Add-AzureVHD][Add-AzureVHD], qui fait partie du module Microsoft Azure PowerShell. Vous utiliserez cette cmdlet pour télécharger le disque dur virtuel.

**Un système d'exploitation Windows stocké dans un fichier .vhd** : vous avez installé un système d'exploitation Windows Server pris en charge sur un disque dur virtuel. Plusieurs outils permettent de créer des fichiers .vhd. Vous pouvez utiliser des solutions de virtualisation comme Hyper-V pour créer le fichier .vhd et installer le système d'exploitation. Pour obtenir des instructions, consultez la page [Installation du rôle Hyper-V et configuration d'une machine virtuelle][Installation du rôle Hyper-V et configuration d'une machine virtuelle].

**Important** : Microsoft Azure ne prend pas en charge le format VHDX. Vous pouvez convertir le disque au format VHD à l'aide de Hyper-V Manager ou de la [cmdlet Convert-VHD][cmdlet Convert-VHD]. Vous trouverez un didacticiel à ce sujet [ici][1].

**Un support contenant le système d'exploitation Windows Server** : cette tâche requiert un fichier .iso contenant le système d'exploitation Windows Server. Voici les versions de Windows Server prises en charge :

<table border="1" width="600">

<tr bgcolor="#E9E7E7">

<th>
SE

</th>

<th>
SKU

</th>

<th>
Service Pack

</th>

<th>
Architecture

</th>

</tr>

<tr>

<td>
Windows Server 2012 R2

</td>

<td>
Toutes les éditions

</td>

<td>
N/A

</td>

<td>
x64

</td>

</tr>

<tr>

<td>
Windows Server 2012

</td>

<td>
Toutes les éditions

</td>

<td>
N/A

</td>

<td>
x64

</td>

</tr>

<tr>

<td>
Windows Server 2008 R2

</td>

<td>
Toutes les éditions

</td>

<td>
SP1

</td>

<td>
x64

</td>

</tr>

</table>

Cette procédure comprend les étapes suivantes :

-   [Étape 1 : préparation de l'image pour le téléchargement][Étape 1 : préparation de l'image pour le téléchargement]
-   [Étape 2 : création d'un compte de stockage dans Azure][Étape 2 : création d'un compte de stockage dans Azure]
-   [Étape 3 : préparation de la connexion à Azure][Étape 3 : préparation de la connexion à Azure]
-   [Étape 4 : téléchargement du fichier .vhd][Étape 4 : téléchargement du fichier .vhd]

## <span id="prepimage"></span> </a>Étape 1 : préparation de l'image pour le téléchargement

Avant de pouvoir télécharger l'image sur Azure, elle doit être généralisée en utilisant la commande Sysprep. Pour plus d'informations sur l'utilisation de Sysprep, consultez la page [Utilisation de Sysprep : introduction][Utilisation de Sysprep : introduction].

Dans la machine virtuelle que vous venez de créer, suivez la procédure ci-dessous :

1.  Connectez-vous au système d'exploitation.

2.  Ouvrez une fenêtre d'invite de commandes en tant qu'administrateur. Placez-vous dans le répertoire **%windir%\\system32\\sysprep**, puis exécutez `sysprep.exe`.

    ![Ouvrir une fenêtre d'invite de commandes][Ouvrir une fenêtre d'invite de commandes]

3.  La boîte de dialogue **Outil de préparation système** apparaît.

    ![Démarrer Sysprep][Démarrer Sysprep]

4.  Dans **Outil de préparation système**, sélectionnez **Enter System Out-of-Box Experience (OOBE)** et vérifiez que la case à cocher Generalize est activée.

5.  Dans **Shutdown Options**, sélectionnez **Shutdown**.

6.  Cliquez sur **OK**.

## <span id="createstorage"></span> </a>Étape 2 : création d'un compte de stockage dans Azure

Un compte de stockage représente le plus haut niveau d'espace de noms pour l'accès aux services de stockage et est associé avec votre abonnement Azure. Pour télécharger vers Azure un fichier .vhd permettant de créer une machine virtuelle, vous avez besoin d'un compte de stockage Azure. Pour créer un tel compte, vous pouvez utiliser le portail de gestion Azure.

1.  Connectez-vous au portail de gestion Azure.

2.  Dans la barre de commandes, cliquez sur **New**.

3.  Cliquez sur **Storage Account**, puis sur **Quick Create**.

    ![Créer rapidement un compte de stockage][Créer rapidement un compte de stockage]

4.  Remplissez les champs comme suit :

-   Sous **URL**, entrez un nom de sous-domaine à utiliser dans l'URL du compte de stockage. L'entrée peut être composée de 3 à 24 lettres minuscules et chiffres. Ce nom devient le nom d'hôte contenu dans l'URL utilisée pour adresser les ressources d'objets blob, de files d'attente et de tables pour l'abonnement.

-   Sélectionnez **l'emplacement ou le groupe d'affinités** pour le compte de stockage. En indiquant un groupe d'affinités, vous pouvez localiser vos services cloud dans le même centre de données, avec votre stockage.

-   Indiquez si vous souhaitez utiliser la **géo-réplication** pour le compte de stockage. La géo-réplication est activée par défaut. Cette option permet une réplication gratuite de vos données vers un emplacement secondaire, pour que votre stockage puisse basculer vers l'emplacement secondaire en cas de panne sur l'emplacement principal. L'emplacement secondaire est attribué automatiquement. Vous ne pouvez pas le modifier. Si des obligations légales ou des stratégies organisationnelles requièrent un meilleur contrôle de l'emplacement de votre stockage sur le cloud, vous pouvez désactiver la géo-réplication. Cependant, sachez que si vous réactivez la géo-localisation ultérieurement, la réplication de vos données vers un emplacement secondaire sera facturée au tarif d'un transfert unique. Vous pouvez bénéficier d'une réduction pour les services de stockage sans géo-réplication. Vous trouverez plus d'informations sur la gestion de la géo-réplication des comptes de stockage ici : [Gestion des comptes de stockage][Gestion des comptes de stockage].

    ![Entrer les détails du compte de stockage][Entrer les détails du compte de stockage]

1.  Cliquez sur **Create Storage Account**.

    Le compte apparaît à présent sous **Comptes de stockage**.

    ![Compte de stockage correctement créé][Compte de stockage correctement créé]

2.  Puis, créez un conteneur pour les disques durs virtuels téléchargés. Cliquez sur le **Nom du compte de stockage** plus cliquez sur **Conteneurs**.

    ![Détails du compte de stockage][Détails du compte de stockage]

3.  Cliquez sur **Création d'un conteneur**.

    ![Détails du compte de stockage][2]

4.  Saisissez un **Nom** pour votre conteneur et sélectionnez la **Stratégie d'accès**.

    ![Nom du conteneur][Nom du conteneur]

    > [WACOM.NOTE] Par défaut, le conteneur est privé et seul le propriétaire du compte peut y accéder. Pour autoriser l'accès public en lecture aux objets blob du conteneur, mais pas à ses propriétés et ses métadonnées, utilisez l'option « Objet blob public ». Pour autoriser un accès public total en lecture pour le conteneur et les objets blob, utilisez l'option « Conteneur public ».

## <span id="PrepAzure"></span> </a>Étape 3 : Préparation de la connexion à Microsoft Azure

Avant de pouvoir télécharger un fichier .vhd, vous devez établir une connexion sécurisée entre votre ordinateur et votre abonnement Microsoft Azure. Pour ce faire, vous pouvez utiliser la méthode Microsoft Azure Active Directory ou la méthode par certificat.

### Utilisation de la méthode Microsoft Azure AD

1.  Ouvrez la console Microsoft Azure PowerShell, comme indiqué dans la rubrique [Installation de Microsoft Azure PowerShell][Installation de Microsoft Azure PowerShell].

2.  Tapez la commande suivante :
    `Add-AzureAccount`

    Cette commande ouvre une fenêtre d'authentification, pour que vous puissiez vous authentifier avec votre compte professionnel ou scolaire.

    ![Fenêtre PowerShell][Fenêtre PowerShell]

3.  Microsoft Azure authentifie et enregistre les informations d’identification, puis ferme la fenêtre.

### Utilisation de la méthode par certificat

1.  Ouvrez une fenêtre Microsoft Azure PowerShell.

2.  Saisissez :
    `Get-AzurePublishSettingsFile`.

3.  Une fenêtre de navigateur apparaît et vous invite à télécharger un fichier .publishsettings. Il contient des informations et un certificat pour votre abonnement Microsoft Azure.

    ![Page de téléchargement du navigateur][Page de téléchargement du navigateur]

4.  Enregistrez le fichier .publishsettings.

5.  Saisissez :
    `Import-AzurePublishSettingsFile <PathToFile>`

    Où `<PathToFile>` correspond au chemin d'accès complet vers le fichier .publishsettings.

    Pour plus d'informations, consultez la page [Prise en main des cmdlets Microsoft Azure][Prise en main des cmdlets Microsoft Azure]

    Pour plus d'information sur l'installation et la configuration de PowerShell, consultez [Installation et configuration de Microsoft Azure PowerShell][ici]

## <span id="upload"></span> </a>Étape 4 : téléchargement du fichier .vhd

Lorsque vous téléchargez le fichier .vhd, vous pouvez le placer n'importe où dans votre stockage d'objets blob. Dans les exemples de commandes suivants, **BlobStorageURL** correspond à l'URL du compte de stockage que vous avez créé lors de l'étape 2 ; **YourImagesFolder** est le conteneur du stockage d'objets blob où vous voulez stocker vos images. **VHDName** est l'étiquette affichée dans le portail de gestion pour identifier le disque dur virtuel. **PathToVHDFile** est le chemin d'accès complet et le nom du fichier .vhd.

1.  Depuis la fenêtre Microsoft Azure PowerShell utilisée lors de l'étape précédente, tapez :

    `Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>`

    ![PowerShell Add-AzureVHD][PowerShell Add-AzureVHD]

    Pour plus d'informations sur la cmdlet Add-AzureVhd, consultez [Add-AzureVhd][Add-AzureVhd].

## Ajout de l'image à votre liste d'images personnalisées

Une fois le fichier .vhd téléchargé, ajoutez-le comme image dans la liste des images personnalisées associée à votre abonnement.

1.  Dans le portail de gestion, sous **All Items**, cliquez sur **Machines virtuelles**.

2.  Sous Machines virtuelles, cliquez sur **Images**.

3.  Puis, cliquez sur **Create an Image**.

    ![PowerShell Add-AzureVHD][3]

4.  Effectuez les actions suivantes dans **Create an image from a VHD** :

    -   Indiquez le **nom**
    -   Indiquez la **description**
    -   Pour indiquer l'**URL de votre disque dur virtuel**, cliquez sur le bouton dossier pour ouvrir la boîte de dialogue ci-dessous

    ![Sélection du disque dur virtuel][Sélection du disque dur virtuel]

    -   Sélectionnez le compte de stockage de votre disque dur virtuel et cliquez sur **Ouvrir**. Cela vous ramène à la fenêtre **Create an image from a VHD**.
    -   Après être revenu à la fenêtre **Create an image from a VHD**, sélectionnez la famille du système d'exploitation.
    -   Activez la case à cocher **I have run Sysprep on the virtual machine associated with this VHD** pour confirmer la généralisation du système d'exploitation lors de l'étape 1, puis cliquez sur **OK**.

    ![Ajouter une image][Ajouter une image]

5.  **FACULTATIF :** Vous pouvez également utiliser la cmdlet Add-AzureVMImage d'Azure PowerShell pour ajouter votre disque dur virtuel sous forme d'image.

    Dans la fenêtre Microsoft Azure PowerShell, saisissez :

    `Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>`

    ![PowerShell Add-AzureVMImage][PowerShell Add-AzureVMImage]

6.  À l'issue de ces étapes, la nouvelle image apparaîtra dans l'onglet **Images**.

    ![image personnalisée][image personnalisée]

    Vous pouvez à présent utiliser cette image au cours de la création d'une nouvelle machine virtuelle. Sélectionnez **Mes Images** pour afficher la nouvelle image. Pour obtenir des instructions, consultez le didacticiel [Création d'une machine virtuelle exécutant Windows Server][Création d'une machine virtuelle personnalisée].

    ![créer un disque dur dur virtuel à partir d'une image personnalisée][créer un disque dur dur virtuel à partir d'une image personnalisée]

## Étapes suivantes

Maintenant que vous avez créé une machine virtuelle, tentez de créer une machine virtuelle SQL Server. Pour plus d'informations, consultez [Configuration d'une machine virtuelle SQL Server sur Microsoft Azure][Configuration d'une machine virtuelle SQL Server sur Microsoft Azure].

  [À propos des disques et des images dans Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj672979.aspx
  [Création d'une machine virtuelle personnalisée]: http://www.windowsazure.com/fr-fr/documentation/articles/virtual-machines-windows-tutorial/
  [Création d’un compte Azure]: http://www.windowsazure.com/fr-fr/develop/php/tutorials/create-a-windows-azure-account/
  [Téléchargements Microsoft Azure]: http://www.windowsazure.com/fr-fr/downloads/
  [ici]: http://www.windowsazure.com/fr-fr/documentation/articles/install-configure-powershell/
  [Add-AzureVHD]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn205185.aspx
  [Installation du rôle Hyper-V et configuration d'une machine virtuelle]: http://technet.microsoft.com/fr-fr/library/hh846766.aspx
  [cmdlet Convert-VHD]: http://technet.microsoft.com/fr-fr/library/hh848454.aspx
  [1]: http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx
  [Étape 1 : préparation de l'image pour le téléchargement]: #prepimage
  [Étape 2 : création d'un compte de stockage dans Azure]: #createstorage
  [Étape 3 : préparation de la connexion à Azure]: #prepAzure
  [Étape 4 : téléchargement du fichier .vhd]: #upload
  [Utilisation de Sysprep : introduction]: http://technet.microsoft.com/fr-fr/library/bb457073.aspx
  [Ouvrir une fenêtre d'invite de commandes]: ./media/virtual-machines-create-upload-vhd-windows-server/sysprep_commandprompt.png
  [Démarrer Sysprep]: ./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png
  [Créer rapidement un compte de stockage]: ./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png
  [Gestion des comptes de stockage]: http://www.windowsazure.com/fr-fr/documentation/articles/storage-manage-storage-account/
  [Entrer les détails du compte de stockage]: ./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png
  [Compte de stockage correctement créé]: ./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png
  [Détails du compte de stockage]: ./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_detail.png
  [2]: ./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_container.png
  [Nom du conteneur]: ./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_containervalues.png
  [Installation de Microsoft Azure PowerShell]: #Install
  [Fenêtre PowerShell]: ./media/virtual-machines-create-upload-vhd-windows-server/add_azureaccount.png
  [Page de téléchargement du navigateur]: ./media/virtual-machines-create-upload-vhd-windows-server/Browser_download_GetPublishSettingsFile.png
  [Prise en main des cmdlets Microsoft Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj554332.aspx
  [PowerShell Add-AzureVHD]: ./media/virtual-machines-create-upload-vhd-windows-server/powershell_upload_vhd.png
  [Add-AzureVhd]: http://msdn.microsoft.com/fr-fr/library/dn495173.aspx
  [3]: ./media/virtual-machines-create-upload-vhd-windows-server/Create_Image.png
  [Sélection du disque dur virtuel]: ./media/virtual-machines-create-upload-vhd-windows-server/Select_VHD.png
  [Ajouter une image]: ./media/virtual-machines-create-upload-vhd-windows-server/Create_Image_From_VHD.png
  [PowerShell Add-AzureVMImage]: ./media/virtual-machines-create-upload-vhd-windows-server/add_azureimage_powershell.png
  [image personnalisée]: ./media/virtual-machines-create-upload-vhd-windows-server/vm_custom_image.png
  [créer un disque dur dur virtuel à partir d'une image personnalisée]: ./media/virtual-machines-create-upload-vhd-windows-server/create_vm_custom_image.png
  [Configuration d'une machine virtuelle SQL Server sur Microsoft Azure]: http://www.windowsazure.com/fr-fr/documentation/articles/virtual-machines-provision-sql-server/

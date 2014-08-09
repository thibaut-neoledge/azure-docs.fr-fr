<properties linkid="manage-windows-common-task-upload-vhd" urlDisplayName="Upload a VHD" pageTitle="Create and upload a Windows Server VHD to Azure" metaKeywords="Azure VHD, uploading VHD" description="Learn how to create and upload a virtual hard disk (VHD) in Azure that has the Windows Server operating system." metaCanonical="" services="virtual-machines" documentationCenter="" title="Creating and Uploading a Virtual Hard Disk that Contains the Windows Server Operating System" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

Création et téléchargement d'un disque dur virtuel contenant le système d'exploitation Windows Server
=====================================================================================================

Une machine virtuelle Azure exécute le système d'exploitation que vous avez choisi lors de sa création. Azure stocke le système d'exploitation d'une machine virtuelle dans un disque dur virtuel au format VHD (fichier .vhd). Le disque dur virtuel d'un système d'exploitation préparé pour la duplication est nommé « une image ». Cet article vous explique comment créer votre propre image en téléchargeant un fichier .vhd avec un système d'exploitation que vous avez installé et généralisé. Pour plus d'informations sur les disques et les images dans Azure, consultez la page [Gestion des disques et des images](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj672979.aspx).

**Remarque** : Lorsque vous créez une machine virtuelle, vous pouvez personnaliser les paramètres de son système d'exploitation pour faciliter l'exécution de votre application. La configuration que vous définissez pour une machine virtuelle est stockée sur un disque. Pour obtenir des instructions, consultez le guide [Création d'une machine virtuelle personnalisée](/en-us/manage/windows/how-to-guides/custom-create-a-vm/).

Conditions préalables
---------------------

Cet article part du principe que vous disposez des éléments suivants :

**Un certificat de gestion** : vous avez créé un certificat de gestion pour l'abonnement pour lequel vous souhaitez télécharger un disque dur virtuel, puis exporté ce certificat vers un fichier .cer. Pour plus d'informations sur la création de certificats, consultez la page [Création d'un certificat de gestion pour Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/gg551722.aspx).

**Un système d'exploitation Windows stocké dans un fichier .vhd** : vous avez installé un système d'exploitation Windows Server pris en charge sur un disque dur virtuel. Plusieurs outils permettent de créer des fichiers .vhd. Vous pouvez utiliser des solutions de virtualisation comme Hyper-V pour créer le fichier .vhd et installer le système d'exploitation. Pour obtenir des instructions, consultez la page [Installation du rôle Hyper-V et configuration d'une machine virtuelle](http://technet.microsoft.com/en-us/library/hh846766.aspx).

**Important** : Azure ne prend pas en charge le nouveau format VHDX. Vous pouvez convertir le disque au format VHD à l'aide de Hyper-V Manager ou de la cmdlet convert-vhd.

-   **Un support contenant le système d'exploitation Windows Server** : cette tâche requiert un fichier .iso contenant le système d'exploitation Windows Server. Voici les versions de Windows Server prises en charge :

	<TABLE BORDER="1" WIDTH="600">
	<TR BGCOLOR="#E9E7E7">
		<TH>OS</TH>
		<TH>SKU</TH>
		<TH>Service Pack</TH>
		<TH>Architecture</TH>
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

-   La cmdlet [Add-AzureVHD](http://msdn.microsoft.com/fr-fr/library/windowsazure/dn205185.aspx), qui fait partie du module Azure PowerShell. Pour télécharger le module, consultez la page [Téléchargements Azure](/en-us/develop/downloads/).

Cette procédure comprend les étapes suivantes :

-   [Étape 1 : préparation de l'image pour le téléchargement](#prepimage)
-   [Étape 2 : création d'un compte de stockage dans Azure](#createstorage)
-   [Étape 3 : préparation de la connexion à Azure](#prepAzure)
-   [Étape 4 : téléchargement du fichier .vhd](#upload)

Étape 1 : préparation de l'image pour le téléchargement
-------------------------------------------------------

Avant de pouvoir télécharger l'image sur Azure, elle doit être généralisée en utilisant la commande Sysprep. Pour plus d'informations sur l'utilisation de Sysprep, consultez la page [Introduction à l'utilisation de Sysprep](http://technet.microsoft.com/en-us/library/bb457073.aspx).

Dans la machine virtuelle que vous venez de créer, suivez la procédure ci-dessous :

1.  Connectez-vous au système d'exploitation.

2.  Ouvrez une fenêtre d'invite de commandes en tant qu'administrateur. Remplacez le répertoire par **%windir%\\system32\\sysprep**, puis exécutez `sysprep.exe`.

    ![Ouvrir une fenêtre d'invite de commandes](./media/virtual-machines-create-upload-vhd-windows-server/sysprepcommand.png)

3.  La boîte de dialogue **Outil de préparation système** apparaît.

    ![Démarrer Sysprep](./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png)

4.  Dans **Action de nettoyage du système**, sélectionnez **Enter System Out-of-Box Experience (OOBE)** et vérifiez que la case à cocher **Generalize** est activée.

5.  Dans **Shutdown Options**, sélectionnez **Shutdown**.

6.  Cliquez sur **OK**.

Étape 2 : création d'un compte de stockage dans Azure
-----------------------------------------------------

Un compte de stockage représente le plus haut niveau d'espace de noms pour l'accès aux services de stockage et est associé avec votre abonnement Azure. Pour télécharger vers Azure un fichier .vhd permettant de créer une machine virtuelle, vous avez besoin d'un compte de stockage Azure. Pour créer un tel compte, vous pouvez utiliser le portail de gestion Azure.

1.  Connectez-vous au portail de gestion Azure.

2.  Dans la barre de commandes, cliquez sur **New**.

3.  Cliquez sur **Storage Account**, puis sur **Quick Create**.

    ![Créer rapidement un compte de stockage](./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png)

4.  Remplissez les champs comme suit :

    ![Entrer les détails du compte de stockage](./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png)

-   Sous **URL**, entrez un nom de sous-domaine à utiliser dans l'URL du compte de stockage. L’entrée peut être composée de 3 à 24 lettres minuscules et chiffres. Ce nom devient le nom d'hôte contenu dans l'URL utilisée pour adresser les ressources d'objets blob, de files d'attente et de tables pour l'abonnement.

-   Sélectionnez l'emplacement ou le groupe d'affinités pour le compte de stockage. En indiquant un groupe d'affinités, vous pouvez localiser vos services cloud dans le même centre de données, avec votre stockage.

-   Indiquez si vous souhaitez utiliser la géo-réplication pour le compte de stockage. La géo-réplication est activée par défaut. Cette option permet une réplication gratuite de vos données vers un emplacement secondaire, pour que votre stockage puisse basculer vers l'emplacement secondaire en cas de panne sur l'emplacement principal. L'emplacement secondaire est attribué automatiquement. Vous ne pouvez pas le modifier. Si des obligations légales ou des stratégies organisationnelles requièrent un meilleur contrôle de l'emplacement de votre stockage sur le cloud, vous pouvez désactiver la géo-réplication. Cependant, sachez que si vous réactivez la géo-localisation ultérieurement, la réplication de vos données vers un emplacement secondaire sera facturée au tarif d'un transfert unique. Vous pouvez bénéficier d'une réduction pour les services de stockage sans géo-réplication.

5.	Cliquez sur **Create Storage Account**.

    Le compte apparaît à présent sous **Comptes de stockage**.

	![Compte de stockage correctement créé](./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png)

Étape 3 : préparation de la connexion à Azure
---------------------------------------------

Avant de pouvoir télécharger un fichier .vhd, vous devez établir une connexion sécurisée entre votre ordinateur et votre abonnement Azure.

1.  Ouvrez une fenêtre Azure PowerShell.

2.  Tapez :

    `Get-AzurePublishSettingsFile`

    Cette commande ouvre une fenêtre de navigateur et télécharge automatiquement un fichier .publishsettings contenant des informations et un certificat pour votre abonnement Azure.

3.  Enregistrez le fichier .publishsettings.

4.  Tapez :

    `Import-AzurePublishSettingsFile <PathToFile>`

    Où `<PathToFile>` correspond au chemin d'accès complet vers le fichier .publishsettings.

    Pour plus d'informations, consultez la page [Prise en main des cmdlets Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj554332.aspx)

Étape 4 : téléchargement du fichier .vhd
----------------------------------------

Lorsque vous téléchargez le fichier .vhd, vous pouvez le placer n'importe où dans votre stockage d'objets blob. Dans les exemples de commandes suivants, **BlobStorageURL** correspond à l'URL du compte de stockage que vous avez créé lors de l'étape 2 ; **YourImagesFolder** est le conteneur du stockage d'objets blob où vous voulez stocker vos images. **VHDName** est l'étiquette affichée dans le portail de gestion pour identifier le disque dur virtuel. **PathToVHDFile** est le chemin d'accès complet et le nom du fichier .vhd.

1.  Depuis la fenêtre Azure PowerShell utilisée lors de l'étape précédente, tapez :

    `Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`

    Pour plus d'informations, consultez la page [Add-AzureVhd](http://msdn.microsoft.com/fr-fr/library/windowsazure/dn205185.aspx).

Ajout de l'image à votre liste d'images personnalisées
------------------------------------------------------

Une fois le fichier .vhd téléchargé, ajoutez-le comme image dans la liste des images personnalisées associée à votre abonnement.

1.  Dans le portail de gestion, sous **All Items**, cliquez sur **Machines virtuelles**.

2.  Sous Machines virtuelles, cliquez sur **Images**, puis sur **Create**.

3.  Dans **Create an image from a VHD**, indiquez un nom et l'URL du fichier .vhd téléchargé.

4.  Activez la case à cocher **I have run Sysprep on the virtual machine associated with this VHD** pour confirmer la généralisation du système d'exploitation lors de l'étape 2, puis cliquez sur **OK**.

Étapes suivantes
----------------

Une fois l'image disponible dans votre liste, vous pouvez l'utiliser pour créer des machines virtuelles. Pour obtenir des instructions, consultez le didacticiel [Création d'une machine virtuelle exécutant Windows Server](../virtual-machines-windows-tutorial/).


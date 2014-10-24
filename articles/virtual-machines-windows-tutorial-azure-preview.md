<properties linkid="virtual-machines-windows-tutorial-azure-preview" urlDisplayName="Create a virtual machine in the Preview Portal" pageTitle="Create a virtual machine running Windows Server in the Azure Preview Portal" metaKeywords="Azure image gallery vm" description="Learn how to create an Azure virtual machine (VM) running Windows Server, using the VM Gallery in the Azure Preview Portal" metaCanonical="" services="virtual-machines" documentationCenter="" title="" authors="danlep,kathydav" solutions="" manager="dongill" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="danlep,kathydav"></tags>

# Création d'une machine virtuelle exécutant Windows Server dans la version préliminaire du portail Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/virtual-machines-windows-tutorial/" title="Portail Azure">Portail Azure</a><a href="/fr-fr/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Version pr&eacute;liminaire du portail Azure" class="current">Version pr&eacute;liminaire du portail Azure</a></div>

Ce didacticiel montre à quel point il est facile de créer une machine virtuelle Azure exécutant Windows Server à partir de la galerie de machines virtuelles de la version préliminaire du portail Azure. La galerie propose de nombreuses images, notamment des systèmes d'exploitation Windows et Linux, ainsi que des images d'applications.

> [WACOM.NOTE] Vous n'avez besoin d'aucune expérience préalable avec les machines virtuelles Azure pour pouvoir suivre ce didacticiel. Vous devez cependant posséder un compte Azure. Vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, consultez la page [Création d’un compte Azure][Création d’un compte Azure].

Ce didacticiel vous montre comment effectuer les opérations suivantes :

-   [Création de la machine virtuelle][Création de la machine virtuelle]
-   [Connexion à la machine virtuelle une fois celle-ci créée][Connexion à la machine virtuelle une fois celle-ci créée]

Pour en savoir plus, consultez la page [Machines virtuelles][Machines virtuelles].

## <span id="createvirtualmachine"></span> </a>Création de la machine virtuelle

Cette section vous montre comment utiliser la version préliminaire du portail pour créer une machine virtuelle exécutant Windows Server. Vous pouvez utiliser les paramètres par défaut d'Azure pour la majorité de la configuration, et créer la machine virtuelle en quelques minutes.

1.  Connectez-vous à la [version préliminaire du portail Azure][version préliminaire du portail Azure]. Consultez l'offre de [version d'évaluation gratuite][version d'évaluation gratuite] si vous n'avez pas encore d'abonnement.

2.  Dans le menu Hub, cliquez sur **Nouveau**.

    ![Select New from the Command Bar][Select New from the Command Bar]

3.  Sous **Nouveau**, cliquez sur **Tout**, puis sous **Galerie**, cliquez sur **Machines virtuelles**. Cliquez sur **Windows Server 2012 R2 Datacenter**. Ici, cliquez sur **Créer**.

    ![Select a VM image from the Gallery][Select a VM image from the Gallery]

4.  Dans le volet **Créer une machine virtuelle**, remplissez le **nom d'hôte** que vous souhaitez pour la machine virtuelle, le **nom d'utilisateur** administratif et un **mot de passe** fort.

    ![Configure host name and log on credentials][Configure host name and log on credentials]

    > [WACOM.NOTE] **Nom d'utilisateur** fait référence au compte administratif que vous utilisez pour gérer le serveur. Créez un mot de passe unique pour ce compte et gardez-le en mémoire. **Vous aurez besoin du nom d'utilisateur et du mot de passe pour vous connecter à la machine virtuelle**.

5.  Pour utiliser les paramètres par défaut pour les options de machine virtuelle restantes et commencer à créer la machine virtuelle, cliquez sur **Créer**. Si vous le souhaitez, avant de cliquer sur **Créer**, vous pouvez explorer les paramètres de **configuration facultative**. Vous pouvez par exemple configurer des diagnostics facultatifs sur la machine virtuelle pour obtenir ultérieurement de nombreuses statistiques sur votre machine virtuelle. Cliquez sur **Configuration facultative**, **Diagnostics** et définissez le **Statut** sur **Activé**.

    ![Turn on VM diagnostics][Turn on VM diagnostics]

    > [WACOM.NOTE] Si vous activez les diagnostics Azure, Azure stocke les données de diagnostic dans un compte de stockage Azure, ce qui entraîne des frais de stockage supplémentaires.

6.  Lorsqu'Azure crée la machine virtuelle, vous pouvez suivre la progression dans **Notifications**, dans le menu Hub. Une fois la machine virtuelle créée par Azure, vous pourrez la voir dans le tableau d'accueil.

    ![VM appears on the Startboard][VM appears on the Startboard]

## <span id="logon"></span> </a>Connexion à la machine virtuelle une fois celle-ci créée

Cette section explique comment se connecter à la machine virtuelle de manière à pouvoir gérer ses paramètres et les applications qui seront exécutées dessus.

> [WACOM.NOTE] Pour connaître les conditions requises et obtenir des astuces concernant le dépannage, consultez la page [Se connecter à une machine virtuelle Azure avec RDP ou SSH][Se connecter à une machine virtuelle Azure avec RDP ou SSH].

1.  Si ce n'est pas déjà fait, connectez-vous à la [version préliminaire du portail Azure][version préliminaire du portail Azure].

2.  Cliquez sur votre machine virtuelle dans le tableau d'accueil. Si vous devez la rechercher, cliquez sur **Parcourir**, puis sur **Machines virtuelles**.

    ![Browse to find the VM][Browse to find the VM]

3.  Dans le volet de la machine virtuelle, cliquez sur **Connecter** en haut.

    ![Se connecter à la machine virtuelle][Se connecter à la machine virtuelle]

4.  Cliquez sur **Ouvrir** pour utiliser le fichier de protocole RDP (Remote Desktop Protocol) qui a été créé automatiquement pour la machine virtuelle.

5.  Cliquez sur **Connect** pour continuer le processus de connexion.

    ![Poursuivre la connexion][Poursuivre la connexion]

6.  Tapez le nom d'utilisateur et le mot de passe du compte d'administration sur la machine virtuelle, puis cliquez sur **OK**.

7.  Cliquez sur **Yes** pour vérifier l'identité de la machine virtuelle.

    ![Vérifier l'identité de la machine][Vérifier l'identité de la machine]

    Vous pouvez désormais utiliser la machine virtuelle tout comme vous le feriez avec un serveur.

## Étapes suivantes

Pour en savoir plus sur la configuration de machines virtuelles Windows sous Azure, consultez les articles suivants :

[Connexion des machines virtuelles dans un service cloud][Connexion des machines virtuelles dans un service cloud]

[Création et téléchargement de votre disque dur virtuel contenant le système d'exploitation Windows Server][Création et téléchargement de votre disque dur virtuel contenant le système d'exploitation Windows Server]

[Association de disques de données avec une machine virtuelle][Association de disques de données avec une machine virtuelle]

[Gestion de la disponibilité des machines virtuelles][Gestion de la disponibilité des machines virtuelles]

  [Portail Azure]: /fr-fr/documentation/articles/virtual-machines-windows-tutorial/ "Portail Azure"
  [Version préliminaire du portail Azure]: /fr-fr/documentation/articles/virtual-machines-windows-tutorial-azure-preview/ "Version préliminaire du portail Azure"
  [Création d’un compte Azure]: http://www.windowsazure.com/en-us/develop/php/tutorials/create-a-windows-azure-account/
  [Création de la machine virtuelle]: #createvirtualmachine
  [Connexion à la machine virtuelle une fois celle-ci créée]: #logon
  [Machines virtuelles]: http://go.microsoft.com/fwlink/p/?LinkID=271224
  [version préliminaire du portail Azure]: https://portal.azure.com
  [version d'évaluation gratuite]: http://www.windowsazure.com/en-us/pricing/free-trial/
  [Select New from the Command Bar]: ./media/virtual-machines-windows-tutorial-azure-preview/new_button_preview_portal.png
  [Select a VM image from the Gallery]: ./media/virtual-machines-windows-tutorial-azure-preview/image_gallery_preview_portal.png
  [Configure host name and log on credentials]: ./media/virtual-machines-windows-tutorial-azure-preview/create_vm_preview_portal.png
  [Turn on VM diagnostics]: ./media/virtual-machines-windows-tutorial-azure-preview/vm_diagnostics_status_preview_portal.png
  [VM appears on the Startboard]: ./media/virtual-machines-windows-tutorial-azure-preview/vm_startboard_preview_portal.png
  [Se connecter à une machine virtuelle Azure avec RDP ou SSH]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [Browse to find the VM]: ./media/virtual-machines-windows-tutorial-azure-preview/browse_vm_preview_portal.png
  [Se connecter à la machine virtuelle]: ./media/virtual-machines-windows-tutorial-azure-preview/connect_vm_preview_portal.png
  [Poursuivre la connexion]: ./media/virtual-machines-log-on-windows-server/connectpublisher.png
  [Vérifier l'identité de la machine]: ./media/virtual-machines-log-on-windows-server/connectverify.png
  [Connexion des machines virtuelles dans un service cloud]: http://www.windowsazure.com/fr-fr/documentation/articles/cloud-services-connect-virtual-machine/
  [Création et téléchargement de votre disque dur virtuel contenant le système d'exploitation Windows Server]: http://www.windowsazure.com/fr-fr/documentation/articles/virtual-machines-create-upload-vhd-windows-server/
  [Association de disques de données avec une machine virtuelle]: http://www.windowsazure.com/fr-fr/documentation/articles/storage-windows-attach-disk/
  [Gestion de la disponibilité des machines virtuelles]: http://www.windowsazure.com/fr-fr/documentation/articles/manage-availability-virtual-machines/

<properties 
	pageTitle="Création d'une machine virtuelle exécutant Windows dans le portail Azure Preview" 
	description="Apprenez à créer une machine virtuelle Azure exécutant Windows à partir d'Azure Marketplace de la version préliminaire du portail Azure" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="dlepow" 
	manager="timlt"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/13/2015" 
	ms.author="danlep,kathydav,rasquill"/>

<!---Le flux de travail peut prêter à confusion... vous devez sélectionner la taille correcte avec l'option **Parcourir tous les niveaux de tarification**, puis cliquer sur Sélectionner dans ce volet et le volet **Niveaux de prix recommandés** pour appliquer. Mais même après cette opération, il n'a pas semblé choisir le compte de stockage Premium... il s'affichait sous la forme " GRS standard " lorsque j'ai inspecté le type de compte de stockage qui serait utilisé pour la machine virtuelle. 
-->

<!-- Preview portal screenshots getting a little out of date. Please refresh at next update.
-->

# Création d'une machine virtuelle dans le portail Azure en version préliminaire#

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/virtual-machines-windows-tutorial/" title="Azure Portal">Portail Azure</a><a href="/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Azure Preview Portal" class="current">Portail Azure en version préliminaire</a></div>

Ce didacticiel montre à quel point il est facile de créer une machine virtuelle Azure exécutant Windows en utilisant à titre d'exemple une image Windows Server à partir d'Azure Marketplace de la version préliminaire du portail Azure. Marketplace propose de nombreuses images, notamment des systèmes d'exploitation Windows et Linux, ainsi que des images d'applications. 

> [AZURE.NOTE] Vous n'avez besoin d'aucune expérience préalable avec les machines virtuelles Azure pour pouvoir suivre ce didacticiel. Vous devez cependant posséder un compte Azure. Vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Création d'un compte Azure](http://www.windowsazure.com/develop/php/tutorials/create-a-windows-azure-account/). 

Ce didacticiel vous montre comment effectuer les opérations suivantes :

- [Création de la machine virtuelle](#createvirtualmachine)
- [Connexion à la machine virtuelle une fois celle-ci créée](#logon)

Pour en savoir plus, consultez la page [Machines virtuelles](http://go.microsoft.com/fwlink/p/?LinkID=271224)


## <a id="createvirtualmachine"> </a>Création de la machine virtuelle##

Cette section explique comment utiliser le portail Preview pour créer une machine virtuelle en utilisant Windows Server 2012 R2 Datacenter à titre d'exemple. Vous pouvez utiliser les paramètres par défaut d'Azure pour la majorité de la configuration, et créer la machine virtuelle en quelques minutes.

> [AZURE.NOTE] Les images disponibles dépendent de votre abonnement. Ce didacticiel utilise une image Windows Server, mais un abonnement MSDN peut offrir d'autres images, y compris des images de Bureau. 
 

1. Connectez-vous au [portail Azure en version préliminaire](https://portal.azure.com). Consultez l'offre d'[évaluation gratuite](http://www.windowsazure.com/pricing/free-trial/) si vous ne possédez pas encore d'abonnement.

2. Dans le menu Hub, cliquez sur **Nouveau**.

	![Select New from the Command Bar](./media/virtual-machines-windows-tutorial-azure-preview/new_button_preview_portal.png)

3. Dans le panneau **Nouveau**, cliquez sur **Tout**, sur **Machines virtuelles**, sur **Windows Server 2012 R2 Datacenter**, puis sur **Créer**.

	![Select a VM image from the Gallery](./media/virtual-machines-windows-tutorial-azure-preview/image_gallery_preview_portal.png)
	
4. Dans le panneau **Créer une machine virtuelle**, remplissez le **Nom d'hôte** que vous souhaitez pour la machine virtuelle, le **Nom d'utilisateur administratif** et un **Mot de passe fort**.  

	>[AZURE.NOTE] **Nom d'utilisateur** fait référence au compte administratif que vous utilisez pour gérer le serveur. Créez un mot de passe unique pour ce compte et gardez-le en mémoire. **Vous aurez besoin du nom d'utilisateur et du mot de passe pour vous connecter à la machine virtuelle**.
	

	![Configure host name and log on credentials](./media/virtual-machines-windows-tutorial-azure-preview/create_vm_name_pwd_preview_portal.png)
	
	
5. Passez en revue les paramètres par défaut, tels que le **Niveau de tarification**, la **Configuration facultative** et l'**Emplacement**. Ces choix affectent la taille de la machine virtuelle et des options de réseau telles que l'appartenance au domaine. Par exemple, pour essayer le stockage Premium sur une machine virtuelle, vous devez choisir une région et une taille qui le prend en charge. 

	>[AZURE.NOTE] Le stockage Premium est en phase Preview et disponible pour les machines virtuelles de la série DS dans certaines régions. Pour plus d'informations, consultez [stockage Premium : Stockage hautes performances pour les charges de travail des machines virtuelles Azure](http://azure.microsoft.com/documentation/articles/storage-premium-storage-preview-portal/).

	![Configure host name and log on credentials](./media/virtual-machines-windows-tutorial-azure-preview/create_vm_preview_portal.png)
	
6. Quand vous avez terminé de passer en revue ou de mettre à jour les paramètres, cliquez sur **Créer**.	

7. Pendant qu'Azure crée la machine virtuelle, vous pouvez suivre la progression dans **Notifications**, dans le menu Hub. Une fois la machine virtuelle créée par Azure, vous pourrez la voir dans le tableau d'accueil.

	![VM appears on the Startboard](./media/virtual-machines-windows-tutorial-azure-preview/vm_startboard_preview_portal.png)

## <a id="logon"> </a>Connexion à la machine virtuelle une fois celle-ci créée ##

Cette section explique comment se connecter à la machine virtuelle de manière à pouvoir gérer ses paramètres et les applications qui seront exécutées dessus.

>[AZURE.NOTE] Pour obtenir les conditions préalables et des conseils sur le dépannage, consultez [Connexion d'une machine virtuelle Azure à l'aide de RDP or SSH](http://go.microsoft.com/fwlink/p/?LinkId=398294).

1. Si ce n'est pas déjà fait, connectez-vous au [portail Azure en version préliminaire ](https://portal.azure.com).

2. Cliquez sur votre machine virtuelle dans le tableau d'accueil. Si vous devez la rechercher, cliquez sur **Parcourir**, puis sur **Machines virtuelles**. 

	![Browse to find the VM](./media/virtual-machines-windows-tutorial-azure-preview/browse_vm_preview_portal.png)

3. Sélectionnez ensuite votre machine virtuelle à partir de la liste. 

	![Select your VM name from the list](./media/virtual-machines-windows-tutorial-azure-preview/vm_select_preview_portal.png)

4. Dans le panneau de la machine virtuelle, cliquez sur **Connecter** en haut.

	![Log on to the virtual machine](./media/virtual-machines-windows-tutorial-azure-preview/connect_vm_preview_portal.png)

5. Cliquez sur **Ouvrir** pour utiliser le fichier de protocole RDP (Remote Desktop Protocol) qui a été créé automatiquement pour la machine virtuelle.
	
6. Cliquez sur **Connecter** pour continuer le processus de connexion.

	![Continue with connecting](./media/virtual-machines-log-on-windows-server/connectpublisher.png)

7. Tapez le nom d'utilisateur et le mot de passe du compte d'administration sur la machine virtuelle, puis cliquez sur **OK**.
	
8. Cliquez sur **Oui** pour vérifier l'identité de la machine virtuelle.

	![Verify the identity of the machine](./media/virtual-machines-log-on-windows-server/connectverify.png)

	Vous pouvez désormais utiliser la machine virtuelle tout comme vous le feriez avec un serveur.

## Étapes suivantes 

Pour en savoir plus sur la configuration de machines virtuelles Windows sous Azure, consultez les articles suivants :

[Connexion des machines virtuelles dans un service cloud](http://www.windowsazure.com/documentation/articles/cloud-services-connect-virtual-machine/)

[Création et téléchargement de votre disque dur virtuel contenant le système d'exploitation Windows Server](http://www.windowsazure.com/documentation/articles/virtual-machines-create-upload-vhd-windows-server/)

[Association de disques de données avec une machine virtuelle](http://www.windowsazure.com/documentation/articles/storage-windows-attach-disk/)

[Gestion de la disponibilité des machines virtuelles](http://www.windowsazure.com/documentation/articles/manage-availability-virtual-machines/)

[À propos des paramètres de configuration de machine virtuelle Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx)

[Création de la machine virtuelle]: #custommachine
[Connexion à la machine virtuelle une fois celle-ci créée]: #logon

<!--HONumber=47-->

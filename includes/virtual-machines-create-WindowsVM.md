1. Connectez-vous à la version classique du [portail Azure](http://manage.windowsazure.com). Consultez l'offre d'[évaluation gratuite](http://azure.microsoft.com/pricing/free-trial/) si vous ne possédez pas encore d'abonnement.

2. Dans la barre de commandes en bas de la fenêtre, cliquez sur **Nouveau**.

3. Sous **Calculer**, cliquez sur **Machine virtuelle**, puis sur **À partir de la galerie**.

	![Navigate to From Gallery in the Command Bar](./media/virtual-machines-create-WindowsVM/fromgallery.png)
	
4. Le premier écran vous propose de **Choisir une image** pour votre machine virtuelle à partir de l'une des listes de la galerie d'images. \(Les images disponibles dépendent de l’abonnement que vous utilisez.\) Cliquez sur la flèche pour continuer.

	![Choose an image](./media/virtual-machines-create-WindowsVM/chooseimage.png)

5. Le deuxième écran vous permet de choisir un nom d'ordinateur, la taille, un nom d'utilisateur et un mot de passe d'administration. Si vous voulez seulement tester Azure Virtual Machines, remplissez les champs comme démontré dans l'image ci-dessous. Sinon, choisissez le niveau et la taille requis pour exécuter votre application ou charge de travail. Voici quelques détails pour vous aider à les remplir :
	
	- **Nouveau nom d'utilisateur** fait référence au compte administratif que vous utilisez pour gérer le serveur. Créez un mot de passe unique pour ce compte et gardez-le en mémoire. **Vous aurez besoin du nom d'utilisateur et du mot de passe pour vous connecter à la machine virtuelle**. 

	- La taille d'une machine virtuelle affecte son coût d'utilisation, ainsi que les options de configuration comme le nombre de disques de données que vous pouvez joindre. Pour plus d'informations, consultez la page [Tailles de machines virtuelles et services cloud pour Windows Azure](http://go.microsoft.com/fwlink/p/?LinkId=466520).

	![Configure the properties of the virtual machine](./media/virtual-machines-create-WindowsVM/vmconfiguration.png)



6. Le troisième écran vous permet de configurer les ressources pour la mise en réseau, le stockage et la disponibilité. Voici quelques détails pour vous aider à les remplir : 
	

	- The **Cloud Service DNS Name** is the global DNS name that becomes part of the URI that's used to contact the virtual machine. You'll need to come up with your own cloud service name because it must be unique in Azure. Cloud services are important for scenarios using [multiple virtual machines](../articles/cloud-services-connect-virtual-machine.md).
 
	- For **Region/Affinity Group/Virtual Network**, use a region that's appropriate to your location. You can also choose to specify a virtual network instead.
 
	>[AZURE.NOTE] If you want a virtual machine to use a virtual network, you **must** specify the virtual network when you create the virtual machine. You can't join the virtual machine to a virtual network after you create the VM. For more information, see [Azure Virtual Network Overview](http://go.microsoft.com/fwlink/p/?LinkID=294063).

	- For details about configuring endpoints, see [How to Set Up Endpoints to a Virtual Machine](../articles/virtual-machines-set-up-endpoints-classic-portal.md).

	![Configure the connected resources of the virtual machine](./media/virtual-machines-create-WindowsVM/resourceconfiguration.png)

7. Le quatrième écran de configuration vous permet de configurer l'agent de machine virtuelle et certaines des extensions disponibles. Cliquez sur la coche pour créer la machine virtuelle.


	![Configure VM Agent and extensions for the virtual machine](./media/virtual-machines-create-WindowsVM/agent-and-extensions.png)

	>[AZURE.NOTE] The VM agent provides the environment for you to install extensions that can help you interact with or manage the virtual machine. For details, see [Using Extensions](http://go.microsoft.com/FWLink/p/?LinkID=390493).  
    
8. Une fois que vous avez créé la machine virtuelle, la version classique du portail la répertorie sous **Machines virtuelles**. Le service cloud et le compte de stockage correspondants sont également créés et répertoriés dans ces sections. La machine virtuelle et le service cloud sont démarrés automatiquement et leur statut est répertorié comme **En cours d'exécution**. 

	![Configure VM Agent and the endpoints of the virtual machine](./media/virtual-machines-create-WindowsVM/vmcreated.png) <!--HONumber=52-->

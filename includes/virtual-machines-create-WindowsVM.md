1. Connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com). Consultez l'offre d'[évaluation gratuite](http://www.windowsazure.com/fr-fr/pricing/free-trial/) si vous ne possédez pas encore d'abonnement.

2. Dans la barre de commandes en bas de la fenêtre, cliquez sur **Nouveau**.

3. Sous **Calculer**, cliquez sur **Machine virtuelle**, puis sur **À partir de la galerie**.

	![Navigate to From Gallery in the Command Bar](./media/virtual-machines-create-WindowsVM/fromgallery.png)
	
4. Le premier écran vous propose de **Choisir une image** pour votre machine virtuelle à partir de l'une des listes de la galerie d'images. (Les images disponibles dépendent de l'abonnement que vous utilisez.) Cliquez sur la flèche pour continuer.

	![Choose an image](./media/virtual-machines-create-WindowsVM/chooseimage.png)

5. Le deuxième écran vous permet de choisir un nom d'ordinateur, la taille, un nom d'utilisateur et un mot de passe d'administration. Si vous voulez seulement tester Azure Virtual Machines, remplissez les champs comme démontré dans l'image ci-dessous. Sinon, choisissez le niveau et la taille requis pour exécuter votre application ou charge de travail. Voici quelques détails pour vous aider à les remplir : 
	
	- **Nouveau nom d'utilisateur** fait référence au compte administratif que vous utilisez pour gérer le serveur. Créez un mot de passe unique pour ce compte et gardez-le en mémoire. **Vous aurez besoin du nom d'utilisateur et du mot de passe pour vous connecter à la machine virtuelle**. 

	- La taille d'une machine virtuelle affecte son coût d'utilisation, ainsi que les options de configuration comme le nombre de disques de données que vous pouvez joindre. Pour plus d'informations, consultez la page [Tailles de machines virtuelles et services cloud pour Windows Azure](http://go.microsoft.com/fwlink/p/?LinkId=466520).

	![Configure the properties of the virtual machine](./media/virtual-machines-create-WindowsVM/vmconfiguration.png)



6. Le troisième écran vous permet de configurer les ressources pour la mise en réseau, le stockage et la disponibilité. Voici quelques détails pour vous aider à les remplir : 
	

	- Le **Nom du cloud Service DNS** est le nom DNS global qui devient un élément de l'URI utilisé pour contacter la machine virtuelle. Vous devrez créer votre propre nom de service cloud, car il doit être unique dans Azure. Les services cloud sont importants pour les scénarios utilisant [plusieurs machines virtuelles](http://www.windowsazure.com/fr-fr/documentation/articles/cloud-services-connect-virtual-machine/).
 
	- Pour l'option **Région/Groupe d'affinités/Réseau virtuel**, utilisez une région correspondant au lieu où vous êtes. Vous pouvez également choisir de spécifier un réseau virtuel à la place.
 
	>[AZURE.NOTE] Si vous voulez qu'une machine virtuelle utilise un réseau virtuel, lorsque vous la créez, vous **devez** indiquer le réseau virtuel lors de la création de la machine. Vous ne pouvez pas joindre la machine virtuelle à un réseau virtuel après avoir créé celle-ci. Pour plus d'informations, consultez la page [Vue d'ensemble d'Azure Virtual Network](http://go.microsoft.com/fwlink/p/?LinkID=294063).

	- Pour obtenir des détails sur la configuration de points de terminaison, consultez la page [Configuration des points de terminaison sur une machine virtuelle](http://www.windowsazure.com/fr-fr/documentation/articles/virtual-machines-set-up-endpoints/).

	![Configure the connected resources of the virtual machine](./media/virtual-machines-create-WindowsVM/resourceconfiguration.png)

7. Le quatrième écran de configuration vous permet de configurer l'agent de machine virtuelle et certaines des extensions disponibles. Cliquez sur la coche pour créer la machine virtuelle.


	![Configure VM Agent and extensions for the virtual machine](./media/virtual-machines-create-WindowsVM/agent-and-extensions.png)

	>[AZURE.NOTE] L'agent de machine virtuelle fournit l'environnement dans lequel vous installez les extensions qui permettent d'interagir avec la machine virtuelle ou de la gérer. Pour plus d'informations, consultez la page [Utilisation d'extensions](http://go.microsoft.com/FWLink/p/?LinkID=390493).  
    
8. Une fois que vous avez créé la machine virtuelle, le portail de gestion la répertorie sous **Machines virtuelles**. Le service cloud et le compte de stockage correspondants sont également créés et répertoriés dans ces sections. La machine virtuelle et le service cloud sont démarrés automatiquement et le portail de gestion définit leur statut sur **En cours d'exécution**. 

	![Configure VM Agent and the endpoints of the virtual machine](./media/virtual-machines-create-WindowsVM/vmcreated.png)

<!--HONumber=42-->

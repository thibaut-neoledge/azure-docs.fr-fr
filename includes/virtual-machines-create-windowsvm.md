1. Connectez-vous au [portail Azure](http://manage.windowsazure.com). Consultez l'offre d'[évaluation gratuite](http://azure.microsoft.com/pricing/free-trial/) si vous ne possédez pas encore d'abonnement.

2. Dans la barre de commandes en bas de la fenêtre, cliquez sur **Nouveau**.

3. Sous **Calculer**, cliquez sur **Machine virtuelle**, puis sur **À partir de la galerie**.

	![Accéder à À partir de la galerie dans la barre de commandes](./media/virtual-machines-create-WindowsVM/fromgallery.png)

4. Le premier écran vous propose de **Choisir une image** pour votre machine virtuelle dans la liste d’images disponibles. (Les images disponibles dépendent de l’abonnement que vous utilisez.)

5. Le deuxième écran vous permet de choisir un nom d’ordinateur, la taille, un nom d’utilisateur et un mot de passe d’administration. Utilisez le niveau et la taille requis pour exécuter votre application ou charge de travail. Voici quelques conseils :

	- **Nouveau nom d’utilisateur** fait référence au compte administratif que vous utilisez pour gérer le serveur. Créez un mot de passe unique pour ce compte et gardez-le en mémoire. **Vous aurez besoin du nom d'utilisateur et du mot de passe pour vous connecter à la machine virtuelle**.

	- La taille d'une machine virtuelle affecte son coût d'utilisation, ainsi que les options de configuration comme le nombre de disques de données que vous pouvez joindre. Pour en savoir plus, consultez la rubrique [Tailles de machines virtuelles](../articles/virtual-machines-size-specs.md).

6. Le troisième écran vous permet de configurer les ressources pour la mise en réseau, le stockage et la disponibilité. Voici quelques conseils :

	- Le **Nom du cloud Service DNS** est le nom DNS global qui devient un élément de l'URI utilisé pour contacter la machine virtuelle. Vous devrez créer votre propre nom de service cloud, car il doit être unique dans Azure. Les services cloud sont importants pour les scénarios utilisant [plusieurs machines virtuelles](../articles/cloud-services-connect-virtual-machine.md).

	- Pour **Région/Groupe d'affinités/Réseau virtuel**, utilisez une région qui correspond au lieu où vous êtes. Vous pouvez également choisir de spécifier un réseau virtuel à la place.

	>[AZURE.NOTE]Si vous voulez qu’une machine virtuelle utilise un réseau virtuel, lorsque vous la créez, vous **devez** indiquer le réseau virtuel. Vous ne pouvez pas joindre la machine virtuelle à un réseau virtuel après avoir créé celle-ci. Pour plus d'informations, consultez la page [Vue d'ensemble d'Azure Virtual Network](virtual-networks-overview.md)
	>
	> Pour obtenir des détails sur la configuration de points de terminaison, consultez la page [Configuration des points de terminaison sur une machine virtuelle](../articles/virtual-machines-set-up-endpoints.md).

7. Le quatrième écran de configuration vous permet d’installer l’agent de machine virtuelle et de configurer certaines des extensions disponibles.

	>[AZURE.NOTE]L’agent de machine virtuelle fournit l’environnement dans lequel vous installez les extensions qui permettent d’interagir avec la machine virtuelle ou de la gérer. Pour en savoir plus, consultez la page [À propos de l'agent et des extensions de machine virtuelle](virtual-machines-extensions-agent-about.md).

8. Une fois la machine virtuelle créée, le portail la répertorie sous **Machines virtuelles**. Le service cloud et le compte de stockage correspondants sont également créés et répertoriés dans ces sections. La machine virtuelle et le service cloud sont démarrés automatiquement et leur statut est répertorié comme **En cours d'exécution**.

	![Configurer l’agent de machine virtuelle et les points de terminaison de la machine virtuelle](./media/virtual-machines-create-WindowsVM/vmcreated.png)

<!---HONumber=Oct15_HO3-->
<properties
	pageTitle="Création d’une machine virtuelle pour un projet web avec Visual Studio"
	description="Création d’une machine virtuelle pour un site web"
	services="virtual-machines"
	documentationCenter=""
	authors="kempb"
	manager="douge"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/19/2015" 
	ms.author="kempb"/>

# Création d’une machine virtuelle pour un site web avec Visual Studio

Lorsque vous créez un projet web pour un site web Azure, vous pouvez approvisionner une machine virtuelle dans Azure. Vous pouvez ensuite la configurer avec des logiciels supplémentaires ou l'utiliser à des fins de diagnostic ou de débogage.

Pour créer une machine virtuelle lorsque vous créez un site web, suivez les étapes suivantes :

1. Dans Visual Studio, choisissez **Fichier**, **Nouveau projet**, puis **Web** et enfin **Application Web ASP.NET**.
2. Dans la boîte de dialogue **Nouveau projet ASP.NET**, sélectionnez le type d'application web souhaité. Dans la section Azure de la boîte de dialogue (en bas à droite), veillez à ce que la case **Héberger dans le cloud** soit cochée (elle est appelée **Créer des ressources distantes** dans certaines installations).

	![][0]

3. Choisissez **Machine virtuelle**, puis cliquez sur le bouton **OK**.
4. Si vous y êtes invité, connectez-vous à Azure. La boîte de dialogue Créer l’ordinateur virtuel s’affiche.

	![][2]

5. Dans la zone du nom DNS, saisissez le nom de la machine virtuelle. Le nom DNS doit être unique dans Azure. Si le nom saisi n'est pas disponible, un point d'exclamation rouge apparaît.
6. Dans la liste d'images, choisissez l'image du système d'exploitation que vous souhaitez sur la machine virtuelle. Vous pouvez choisir n'importe quelle image standard ou votre propre image téléchargée vers Azure.
7. Ne cochez pas la case **Activer IIS et Web Deploy**, sauf si vous prévoyez d'installer un serveur web différent. Vous ne pourrez pas effectuer de publication à partir de Visual Studio si vous désactivez Web Deploy. Vous pouvez ajouter IIS et Web Deploy à n'importe quelle image Windows Server, y compris à vos images personnalisées.
8. Dans la liste **Taille**, sélectionnez la taille de la machine virtuelle.
9. Spécifiez les identifiants de connexion de cette machine virtuelle. Notez-les, car vous en aurez besoin pour accéder à la machine avec le Bureau à distance.
10. Dans la liste **Emplacement**, choisissez la région, le réseau virtuel ou le groupe d'affinités qui hébergera la machine virtuelle. Il est possible d'utiliser des groupes d'affinités pour s'assurer que les ressources Azure présentant un trafic réseau important sont regroupées dans le même centre de données. Il est également possible d'utiliser des régions pour spécifier l'emplacement exact du centre de données.
11. Choisissez **OK** pour démarrer la création de la machine virtuelle. Vous pouvez suivre la progression dans la fenêtre **Sortie **. 
	![][3]

12. Lorsque la machine virtuelle est approvisionnée, des scripts de publication sont créés dans un nœud **PublishScripts** de votre solution. Le script de publication exécute et approvisionne une machine virtuelle dans Azure. La fenêtre **Sortie** affiche le statut. Le script effectue les opérations ci-après pour configurer la machine virtuelle :

	* Création de la machine virtuelle si elle n’existe pas déjà.
	* Crée un compte de stockage avec un nom qui commence par `devtest`, mais uniquement s’il n’existe pas déjà un compte de stockage de ce type dans la région spécifiée.
	* Création d’un service cloud comme conteneur pour la machine virtuelle et création d’un rôle web pour le site web.
	* Configuration de Web Deploy sur la machine virtuelle.
	* Configuration d’IIS et d’ASP.NET sur la machine virtuelle.

	![][4]

<br/> 13. (Facultatif) Dans l'**Explorateur de serveurs**, développez le nœud **Machines virtuelles**. Choisissez le nœud de la machine virtuelle que vous avez créée, puis sélectionnez **Se connecter avec le Bureau à distance** pour vous connecter à la machine virtuelle.

# Étapes suivantes

Si vous voulez personnaliser les scripts de publication créés, consultez des informations détaillées [ici](http://msdn.microsoft.com/library/dn642480.aspx).

[0]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_NewProject.PNG
[1]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_SignIn.PNG
[2]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_CreateVM.PNG
[3]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_Provisioning.png
[4]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_SolutionExplorer.png

<!---HONumber=58-->
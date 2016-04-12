

Quand vous créez un projet d’application web pour Azure, vous pouvez approvisionner une machine virtuelle dans Azure. Vous pouvez ensuite la configurer avec des logiciels supplémentaires ou l'utiliser à des fins de diagnostic ou de débogage.

Pour créer une machine virtuelle quand vous créez une application web, procédez comme suit :

1. Dans Visual Studio, cliquez sur **Fichier** > **Nouveau** > **Projet** > **Web**, puis choisissez **Application Web ASP.NET** (sous les nœuds **Visual C#** ou **Visual Basic**).
2. Dans la boîte de dialogue **Nouveau projet ASP.NET**, sélectionnez le type d'application web souhaité. Dans la section Azure de la boîte de dialogue (en bas à droite), veillez à ce que la case **Héberger dans le cloud** soit cochée (elle est appelée **Créer des ressources distantes** dans certaines installations).

	![][0]

3. Pour cet exemple, dans la liste déroulante sous Microsoft Azure, choisissez **Machine virtuelle (v1)**, puis cliquez sur le bouton **OK**.
4. Connectez-vous à Azure si vous y êtes invité. La boîte de dialogue **Créer une machine virtuelle** s’affiche.

	![][2]

5. Dans la zone **Nom DNS**, saisissez le nom de la machine virtuelle. Le nom DNS doit être unique dans Azure. Si le nom saisi n'est pas disponible, un point d'exclamation rouge apparaît.
6. Dans la liste **Images**, choisissez l'image de machine virtuelle sur laquelle vous souhaitez baser la machine virtuelle. Vous pouvez choisir n'importe quelle image de machine virtuelle Azure standard ou votre propre image téléchargée dans Azure.
7. Ne cochez pas la case **Activer IIS et Web Deploy**, sauf si vous prévoyez d'installer un serveur web différent. Vous ne pourrez pas effectuer de publication à partir de Visual Studio si vous désactivez Web Deploy. Vous pouvez ajouter IIS et Web Deploy à n'importe quelle image Windows Server, y compris à vos images personnalisées.
8. Dans la liste **Taille**, sélectionnez la taille de la machine virtuelle.
9. Spécifiez les identifiants de connexion de cette machine virtuelle. Notez-les, car vous en aurez besoin pour accéder à la machine avec le Bureau à distance.
10. Dans la liste **Emplacement**, choisissez la région dans laquelle héberger la machine virtuelle.
11. Cliquez sur le bouton **OK** pour commencer la création de la machine virtuelle. Vous pouvez suivre la progression de l'opération dans la fenêtre **Sortie**.

	![][3]

12. Quand la machine virtuelle est approvisionnée, les scripts publiés sont créés dans un nœud **PublishScripts** de votre solution. Le script publié exécute et provisionne une machine virtuelle dans Azure. La fenêtre **Sortie** affiche le statut. Le script effectue les opérations ci-après pour configurer la machine virtuelle :

	* Création de la machine virtuelle si elle n’existe pas déjà.
	* Crée un compte de stockage avec un nom qui commence par `devtest`, mais uniquement s’il n’existe pas déjà un compte de stockage de ce type dans la région spécifiée.
	* Création d’un service cloud comme conteneur pour la machine virtuelle et création d’un rôle web pour l’application web.
	* Configuration de Web Deploy sur la machine virtuelle.
	* Configuration d’IIS et d’ASP.NET sur la machine virtuelle.

	![][4]

13. (Facultatif) Vous pouvez vous connecter à la nouvelle machine virtuelle. Dans l’**Explorateur de serveurs**, développez le nœud **Machines virtuelles**. Choisissez le nœud de la machine virtuelle que vous avez créée, puis dans le menu contextuel, sélectionnez **Se connecter avec le Bureau à distance**. Vous pouvez également choisir dans **Cloud Explorer** l’option **Ouvrir dans le portail** dans le menu contextuel et y établir une connexion à la machine virtuelle.

 ![][5]


## Étapes suivantes

Si vous souhaitez personnaliser les scripts publiés que vous avez créés, vous trouverez des informations plus détaillées à la rubrique [Utilisation des scripts Windows PowerShell pour la publication dans des environnements de développement et de test](http://msdn.microsoft.com/library/dn642480.aspx).

[0]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_NewProject.PNG
[1]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_SignIn.PNG
[2]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_CreateVM.PNG
[3]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_Provisioning.png
[4]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_SolutionExplorer.png
[5]: ./media/virtual-machines-common-classic-web-app-visual-studio/VS_Create_VM_Connect.png

<!---HONumber=AcomDC_0323_2016-->
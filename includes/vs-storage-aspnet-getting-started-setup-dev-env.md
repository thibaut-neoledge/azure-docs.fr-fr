## <a name="set-up-the-development-environment"></a>Configuration de l’environnement de développement

Cette section vous guide lors de la configuration d’un environnement de déploiement. Cela inclut la création d’une application MVC ASP.NET, l’ajout d’une connexion Services connectés et d’un contrôleur et la spécification des directives requises portant sur les espaces de noms.

### <a name="create-an-aspnet-mvc-app-project"></a>Création d’un projet d’application MVC ASP.NET

1. Ouvrez Visual Studio.

1. Sélectionnez **Fichier -> Nouveau -> Projet** dans le menu principal.

1. Dans la boîte de dialogue **Nouveau projet**, spécifiez les options requises, comme indiqué dans la figure suivante :

    ![Création d’un projet ASP.NET](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. Sélectionnez **OK**.

1. Dans la boîte de dialogue **Nouveau projet ASP.NET**, spécifiez les options requises, comme indiqué dans la figure suivante :

    ![Spécification de MVC](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

1. Sélectionnez **OK**.

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Utiliser la fonctionnalité Services connectés pour se connecter à un compte de stockage Azure

1. Dans l’**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le projet, puis sélectionnez **Ajouter -> Service connecté** dans le menu contextuel.

1. Dans la boîte de dialogue **Ajouter un service connecté**, choisissez **Stockage Azure**, puis cliquez sur le bouton **Configurer**.

    ![Boîte de dialogue Ajouter un service connecté](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. Dans la boîte de dialogue **Stockage Azure**, sélectionnez le compte de stockage Azure que vous souhaitez utiliser, puis cliquez sur **Ajouter**.

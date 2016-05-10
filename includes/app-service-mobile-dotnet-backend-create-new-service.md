1. Connectez-vous au [Portail Azure].

2. Cliquez sur **+NOUVEAU** > **Web + Mobile** > **Application mobile**, puis fournissez un nom pour votre backend d’application mobile.

3. Pour **Groupe de ressources**, sélectionnez un groupe de ressources existant ou créez-en un nouveau (en utilisant le même nom que votre application.)
 
4. Pour **Plan App Service**, sélectionnez un plan App Service existant ou [créez un nouveau plan](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan). Cliquez sur **Create**. Cette action crée le serveur principal Mobile App. Les paramètres du plan App Service déterminent [l’emplacement, les fonctionnalités, les coûts et les ressources de calcul](https://azure.microsoft.com/pricing/details/app-service/) associés à votre application.
	
6. Dans le panneau **Paramètres** du nouveau serveur principal Mobile, cliquez sur **Démarrage rapide** > Votre plateforme d’application cliente > **Se connecter à une base de données**.

	![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)

7. Dans le panneau **Ajouter une connexion de données**, cliquez sur **Base de données SQL** > **Créer une base de données**, tapez le **nom** de la base de données, choisissez un niveau de tarification, puis cliquez sur **Serveur**. Vous pouvez réutiliser cette nouvelle base de données. Si vous disposez déjà d’une base de données dans le même emplacement, vous pouvez choisir à la place **Utiliser une base de données existante**. Il est déconseillé d’utiliser une base de données dans un autre emplacement en raison de la latence plus importante et des frais de bande passante supplémentaires.
 
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)

8. Dans le panneau **Nouveau serveur**, tapez un nom unique dans le champ **Nom du serveur**, fournissez des identifiants de connexion et un mot de passe, cochez la case **Autoriser des services Azure à accéder au serveur**, puis cliquez sur **OK**. Cette action crée la base de données.

9. Dans le panneau **Ajouter une connexion de données**, cliquez sur **Chaîne de connexion**, tapez les valeurs de connexion et de mot de passe pour votre base de données, puis cliquez sur **OK**. Patientez quelques minutes jusqu’au déploiement de la base de données, puis continuez.

<!-- URLs. -->
[Portail Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0504_2016-->
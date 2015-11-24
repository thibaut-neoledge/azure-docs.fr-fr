

Suivez ces étapes pour créer un serveur principal d’application mobile.

1. Connectez-vous au [portail Azure].

2. Dans le coin supérieur gauche de la fenêtre, cliquez sur le bouton **+ NOUVEAU** > **Web + Mobile** > **Application mobile**, puis indiquez un nom pour le serveur principal de votre application mobile.

3. Dans la zone **Groupe de ressources**, sélectionnez un groupe de ressources existant. Si vous n’avez pas de groupe de ressources, entrez le même nom que celui de votre application.
 
	À ce stade, le plan App Service par défaut est sélectionné, dans le niveau Gratuit. Les paramètres du plan App Service déterminent l’emplacement, les fonctionnalités, les coûts et les ressources de calcul associés à votre application. Sélectionnez un autre plan App Service ou créez-en un. Pour plus d’informations sur les plans App Service et leur création, voir [Présentation détaillée des plans Azure App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

4. Utilisez le plan App Service par défaut, sélectionnez un autre plan ou [créez un plan](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan), puis cliquez sur **Créer**.
	
	Cette action crée le serveur principal Mobile App. Ultérieurement, vous allez déployer votre projet de serveur sur ce serveur principal. La configuration d’un serveur principal Mobile App peut prendre plusieurs minutes. Le panneau **Paramètres** pour le serveur principal Mobile App s’affiche lorsque vous avez terminé. Avant de pouvoir utiliser le serveur principal Mobile App, vous devez également définir une connexion vers un magasin de données.

    > [AZURE.NOTE]Dans ce didacticiel, vous créez une instance et un serveur de base de données SQL. Vous pouvez réutiliser cette nouvelle base de données et la gérer comme une autre instance de base de données SQL. Si vous avez déjà une base de données dans le même emplacement que le nouveau serveur principal d’application mobile, vous pouvez choisir **Utiliser une base de données** et sélectionner la base de données. Il est déconseillé d’utiliser une base de données dans un autre emplacement en raison de latences plus importantes et de frais de bande passante supplémentaires. D’autres options de stockage de données sont disponibles.

6. Dans le panneau **Paramètres** du serveur principal Mobile App, faites défiler jusqu’à Mobile et cliquez sur **Mise en route ** > Votre plateforme d’application cliente > **Se connecter à une base de données**.

	![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)

7. Dans le panneau **Ajouter une connexion de données**, cliquez sur **Base de données SQL** > **Créer une base de données**, tapez le **nom** de la base de données, choisissez un niveau de tarification, puis cliquez sur **Serveur**.
 
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)

8. Dans le panneau **Nouveau serveur**, tapez un nom unique dans le champ **Nom du serveur**, indiquez une **connexion d’administrateur de serveur** et un **mot de passe** sécurisés, assurez-vous que la case **Autoriser des services Azure à accéder au serveur** est cochée, puis cliquez sur **OK** deux fois. Cette action crée la base de données et le serveur.

10. Dans le panneau **Ajouter une connexion de données**, cliquez sur **Chaîne de connexion**, tapez les valeurs de connexion et de mot de passe pour votre base de données, puis cliquez sur **OK** deux fois.

	La création de la base de données prend quelques minutes. Utilisez la zone **Notifications** pour surveiller la progression du déploiement. Attendez la fin du déploiement avant de continuer.

Vous avez maintenant configuré un serveur principal d’application mobile Azure qui peut être utilisé par vos applications clientes mobiles. Vous allez ensuite télécharger un projet de serveur pour un serveur principal « todo list » simple et le publier dans Azure.

<!-- URLs. -->
[portail Azure]: https://portal.azure.com/

<!---HONumber=Nov15_HO4-->
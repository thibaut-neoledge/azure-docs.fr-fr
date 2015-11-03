

Suivez ces étapes pour créer un serveur principal d’application mobile.

1. Connectez-vous au [portail Azure].

2. Dans le coin supérieur gauche de la fenêtre, cliquez sur le bouton **+ NOUVEAU** > **Web + Mobile** > **Application mobile**, puis indiquez un nom pour le serveur principal de votre application mobile.

3. Dans la zone **Groupe de ressources**, entrez le même nom que votre application.

4. Le plan App Service par défaut est sélectionné. Pour modifier votre plan de service d’applications, vous pouvez cliquer sur le plan de service d’applications > **+ Créer nouveau**. Indiquez le nom du nouveau plan App Service et sélectionnez un emplacement approprié. Cliquez sur Niveau de tarification et sélectionnez un niveau de tarification approprié pour le service. Sélectionnez **Afficher tout** pour afficher davantage d’options de tarification, telles que **Gratuit** et **Partagé**. Une fois que vous avez sélectionné le niveau de tarification, cliquez sur le bouton **Sélectionner**. Dans le panneau **Plan de service d’applications**, cliquez sur **OK**.

5. Cliquez sur **Create**. Un serveur principal d’application mobile est créé à l’emplacement où vous allez déployer votre projet de serveur ultérieurement. La configuration d’un serveur principal d’application mobile peut prendre quelques minutes. Une fois le serveur principal d’application mobile configuré, le portail ouvre le panneau **Paramètres** pour le serveur principal d’application mobile. Dans l’étape suivante, vous allez créer une base de données SQL.

    > [AZURE.NOTE]Dans ce didacticiel, vous créez une instance et un serveur de base de données SQL. Vous pouvez réutiliser cette nouvelle base de données et la gérer comme une autre instance de base de données SQL. Si vous avez déjà une base de données dans le même emplacement que le nouveau serveur principal d’application mobile, vous pouvez choisir **Utiliser une base de données** et sélectionner la base de données. Il est déconseillé d’utiliser une base de données dans un autre emplacement en raison de latences plus importantes et de frais de bande passante supplémentaires.

6. Dans le nouveau serveur principal d’application mobile, cliquez sur **Paramètres** > **Application mobile** > **Données** > **+Ajouter**.

7. Dans le panneau **Ajouter une connexion de données**, cliquez sur **Base de donnée SQL - Configurer les paramètres requis** > **Créer une base de données**. Entrez le nom de la nouvelle base de données dans le champ **Nom**.

8. Cliquez sur **Serveur**. Dans le panneau **Nouveau serveur**, entrez un nom de serveur unique dans le champ **Nom du serveur**, et indiquez un **nom de connexion d’administration serveur** et un **mot de passe** appropriés. Vérifiez que l’option **Autoriser les services Azure à accéder au serveur** est sélectionnée. Cliquez sur **OK**.

    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)

9. Dans le panneau **Nouvelle base de données**, cliquez sur **OK**.

10. Dans le panneau **Ajouter une connexion de données**, sélectionnez **Chaîne de connexion**, entrez le nom de connexion et le mot de passe que vous avez indiqués lors de la création de la base de données. Si vous utilisez une base de données existante, indiquez les informations d’identification de connexion à celle-ci. Cliquez ensuite sur **OK**.

11. Dans le panneau **Ajouter une connexion de données**, cliquez sur **OK** pour créer la base de données.

La création de la base de données prend quelques minutes. Utilisez la zone **Notifications** pour surveiller la progression du déploiement. Attendez la fin du déploiement avant de continuer.

Vous avez maintenant configuré un serveur principal d’application mobile Azure qui peut être utilisé par vos applications clientes mobiles. Vous allez ensuite télécharger un projet de serveur pour un serveur principal « todo list » simple et le publier dans Azure.

<!-- URLs. -->
[portail Azure]: https://portal.azure.com/

<!---HONumber=Nov15_HO1-->
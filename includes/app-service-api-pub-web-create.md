1. Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le projet (pas la solution), puis cliquez sur **Publier**. 

	![Option de menu Publier le projet](./media/app-service-api-pub-web-create/20-publish-gesture-v3.png)

2. Cliquez sur l’onglet **Profil** et sur **Microsoft Azure API Apps (version préliminaire)**.

	![Boîte de dialogue Publier le site web](./media/app-service-api-pub-web-create/21-select-api-apps-for-deployment-v2.png)

3. Cliquez sur **Nouveau** pour configurer une nouvelle application API dans votre abonnement Azure.

	![Boîte de dialogue de sélection des services API existants](./media/app-service-api-pub-web-create/23-publish-to-apiapps-v3.png)

4. Dans la boîte de dialogue **Créer une application API**, entrez les informations suivantes :

	- Pour **Nom de l’application API**, entrez le nom que vous utilisez pour ce didacticiel. 
	- Si vous avez plusieurs abonnements Azure, sélectionnez celui que vous souhaitez utiliser.
	- Sous **Plan App Service**, sélectionnez un des plans App Service existants ou **Créer un plan App Service** et entrez le nom d’un nouveau plan. 
	- Sous **Groupe de ressources**, sélectionnez un des groupes de ressources existants ou **Créer un groupe de ressources** et entrez un nom. 
	- Sous **Niveau d’accès**, sélectionnez **Accessible à tout le monde**. Vous pouvez restreindre l’accès ultérieurement via le portail Azure en version préliminaire.
	- Dans **Région**, sélectionnez une région proche de vous.  

	![Boîte de dialogue de configuration de l’application web Microsoft Azure](./media/app-service-api-pub-web-create/24-new-api-app-dialog-v3.png)

5. Cliquez sur **OK** pour créer l’application API dans votre abonnement.

	Comme ce processus peut prendre quelques minutes, Visual Studio affiche une boîte de dialogue de confirmation.

	![Message de confirmation de la création du service de l’API](./media/app-service-api-pub-web-create/25-api-provisioning-started-v3.png)

6. Cliquez sur **OK** dans la boîte de dialogue de confirmation.
 
	Le processus de configuration crée le groupe de ressources et l'application API dans votre abonnement Azure. Visual Studio affiche la progression dans la fenêtre **Activité d’Azure App Service**.

	![Notification d’état via la fenêtre Activité d’Azure App Service](./media/app-service-api-pub-web-create/26-provisioning-success-v3.png)

<!---HONumber=July15_HO3-->
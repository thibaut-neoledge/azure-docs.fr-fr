7. Cliquez avec le bouton droit sur l’application API dans l’**Explorateur de solutions** et sélectionnez **Publier** pour ouvrir la boîte de dialogue de publication. Le profil de publication que vous avez créé précédemment doit être présélectionné. 

9. Cliquez sur **Publier** pour commencer le processus de déploiement.

	![Déploiement de l’application API](./media/app-service-api-pub-web-deploy/26-5-deployment-success-v3.png)

	La fenêtre **Activité d’Azure App Service** affiche la progression du déploiement.

	![Notification d’état de la fenêtre Activité d’Azure App Service](./media/app-service-api-pub-web-deploy/26-5-deployment-success-v4.png)

	Pendant ce processus de déploiement, Visual Studio redémarre automatiquement la *passerelle*. La passerelle est une application web qui gère les fonctions d’administration pour toutes les applications d’API d’un groupe de ressources, et elle doit être redémarrée pour reconnaître les modifications effectuées dans la définition d’une application API ou dans un fichier *apiapp.json*.
 
	Si vous utilisez une autre méthode pour déployer une application API, ou si Visual Studio ne parvient pas à redémarrer la passerelle, vous devrez peut-être redémarrer la passerelle manuellement. Les étapes suivantes expliquent comment procéder.

1. Dans votre navigateur, accédez au [portail Azure en version préliminaire](https://portal.azure.com).

2. Accédez au panneau **Application API** pour l’application API que vous avez déployée.

	Pour plus d’informations sur le panneau **Application API** et pour le localiser, voir [Gérer les applications API](../articles/app-service-api/app-service-api-manage-in-portal.md).

4. Cliquez sur le lien **Passerelle**.

3. Dans le panneau **Passerelle**, cliquez sur **Redémarrer**.

	![](./media/app-service-api-pub-web-deploy/restartgateway.png)

<!---HONumber=July15_HO3-->
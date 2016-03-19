4. Dans une autre fenêtre de navigateur ou dans un autre onglet, accédez au [portail Azure en version préliminaire](https://portal.azure.com).

3. Accédez au panneau **Application API** de votre connecteur Dropbox. (Si vous êtes toujours dans le panneau **Groupe de ressources**, cliquez simplement sur le connecteur Dropbox dans le diagramme.)

4. Cliquez sur **Paramètres**, et dans le panneau **Paramètres**, cliquez sur **Authentification**.

	![Cliquez sur Paramètres](./media/app-service-api-exchange-dropbox-settings/clicksettings.png)

	![Cliquez sur Authentification](./media/app-service-api-exchange-dropbox-settings/clickauth.png)

5. Dans le panneau d’authentification, entrez l’ID client et la clé secrète client du site Dropbox, puis cliquez sur **Enregistrer**.

	![Entrez les paramètres et cliquez sur Enregistrer](./media/app-service-api-exchange-dropbox-settings/authblade.png)

3. Copiez l’**URI de redirection** (la zone grise au-dessus de la clé secrète client et de l’ID client) et ajoutez cette valeur à la page que vous avez laissée ouverte à l’étape précédente.

	L’URI de redirection suit ce modèle :

		[gatewayurl]/api/consent/redirect/[connectorname]

	Par exemple :

		https://dropboxrgaeb4ae60b7.azurewebsites.net/api/consent/redirect/DropboxConnector

	![Obtenir un URI de redirection](./media/app-service-api-exchange-dropbox-settings/redirecturi.png)

	![Créer une application Dropbox](./media/app-service-api-exchange-dropbox-settings/dbappsettings2.png)

<!---HONumber=Oct15_HO3-->
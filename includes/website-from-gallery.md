Azure Marketplace met à votre disposition une large gamme d’applications web populaires, développées par Microsoft, par des sociétés tierces ou par des initiatives de logiciel open source. Les applications web créées à partir d’Azure Marketplace ne nécessitent pas l’installation de logiciels autres que le navigateur utilisé pour se connecter au [portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

- création d’une application web via Azure Marketplace ;

- déploiement de l’application web via le portail Azure.
 
Vous allez configurer un blog WordPress avec un modèle par défaut. L’illustration suivante présente l’application terminée :


![Blog WordPress][13]

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Créer une application web dans le portail

1. Connectez-vous au portail Azure.

2. Ouvrez Azure Marketplace en cliquant sur l’icône **Marketplace** :

    ![Icône Marketplace][marketplace]

    Ou en cliquant sur l’icône **Nouveau** dans l’angle supérieur droit du tableau de bord, puis en sélectionnant**Marketplace** au bas de la liste.
	
    ![Création][5]
	
3. Sélectionnez **Web + mobile**. Recherchez **WordPress** et cliquez sur l’icône **WordPress**.

	![WordPress dans la liste][7]
	
5. Après avoir lu la description de l’application WordPress, sélectionnez **Créer**.

6. Cliquez sur **Application web** et indiquez les valeurs requises pour configurer votre application web.
	
    ![Configurer une application][8]

7. Cliquez sur **Base de données** et indiquez les valeurs requises pour configurer votre application MySQL.

    ![Configurer la base de données][database]

8. Fournissez un nom pour le nouveau groupe de ressources.

    ![Définir un groupe de ressources][groupname]

8. Si nécessaire, cliquez sur **ABONNEMENT** et spécifiez l’abonnement à utiliser.

7. Lorsque vous avez terminé la définition de l’application web, cliquez sur **Créer** et attendez que l’application web soit créée.

   Une fois l’application web créée, vous voyez apparaître le groupe de ressources contenant l’application web et la base de données.

   ![Afficher le groupe][resourcegroup]

## Lancer et gérer l’application web WordPress
	
1. Cliquez sur votre nouvelle application web pour afficher les détails la concernant.

    ![lancer le tableau de bord][10]

2. Dans la page **Essentials**, cliquez sur **Parcourir** ou sur le lien situé sous **URL** pour ouvrir la page d’accueil de l’application web.

    ![URL du site][browse]

3. Si vous n’avez pas installé WordPress, entrez les informations de configuration appropriées requises par ce logiciel, puis cliquez sur **Installer WordPress** pour finaliser la configuration et ouvrir la page de connexion à l’application web.

4. Cliquez sur **Connexion** et entrez vos informations d’identification.

5. Vous disposez d’une nouvelle application web WordPress, qui ressemble à l’application web indiquée ci-dessous.

	![votre site WordPress][13]






[5]: ./media/website-from-gallery/start-marketplace.png
[6]: ./media/website-from-gallery/wordpressgallery-02.png
[7]: ./media/website-from-gallery/search-web-app.png
[8]: ./media/website-from-gallery/set-web-app.png
[9]: ./media/website-from-gallery/wordpressgallery-05.png
[10]: ./media/website-from-gallery/select-web.png
[13]: ./media/website-from-gallery/wordpressgallery-09.png
[webapps]: ./media/website-from-gallery/selectwebapps.png
[database]: ./media/website-from-gallery/set-db.png
[resourcegroup]: ./media/website-from-gallery/show-rg.png
[browse]: ./media/website-from-gallery/browse-web.png
[marketplace]: ./media/website-from-gallery/marketplace-icon.png
[groupname]: ./media/website-from-gallery/set-rg.png

<!---HONumber=July15_HO3-->
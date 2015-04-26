

La procédure suivante permet de créer un service mobile dans Azure et d'ajouter du code à votre projet pour connecter votre application à ce nouveau service. Visual Studio 2013 se connecte à Azure à votre place pour créer le nouveau service mobile en utilisant les informations d'identification que vous lui fournissez. Lors de la création d'un service mobile, vous devez spécifier une base de données SQL Azure qui est utilisée par celui-ci pour stocker les données d'application. 


1. Dans Visual Studio 2013, ouvrez l'Explorateur de solutions, cliquez avec le bouton droit sur le projet d'application Windows Store, cliquez sur **Ajouter**, puis sur **Service connecté...**.  

2. Dans la boîte de dialogue Gestionnaire des services, cliquez sur **Créer un service...**, puis sélectionnez **&lt;Gérer...&gt;** sous **Abonnement** dans la boîte de dialogue Créer un service mobile.  

	![create service manage subscriptions](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png)

3. Dans Gérer les abonnements Microsoft Azure, cliquez sur **Se connecter...** pour vous connecter à votre compte Azure (si besoin est), sélectionnez un abonnement disponible, puis cliquez sur **Fermer**.

	Si votre abonnement dispose déjà d'un ou plusieurs services mobiles, leur nom s'affiche. 

5. De retour dans la boîte de dialogue **Créer un service mobile**, sélectionnez votre **Abonnement**, le **Javascript** principal dans **Exécution**, ainsi qu'une **Région** pour votre service mobile, puis tapez un **Nom** pour votre service mobile.

	>[AZURE.NOTE] Les noms des services mobiles doivent être uniques. Une croix rouge s'affiche en regard du **Nom** lorsqu'il n'est pas disponible. 

6. Dans **Base de données**, sélectionnez **&lt;Créer une base de données SQL gratuite&gt;**, indiquez le **Nom d'utilisateur**, le **Mot de passe serveur** et la **Confirmation du mot de passe serveur**, puis cliquez sur **Créer**.

  	![create new mobile service in VS 2013](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013-2.png)


	> [AZURE.NOTE]
	> Dans ce didacticiel, vous créez une instance et un serveur de base de données SQL gratuits. Vous pouvez réutiliser cette nouvelle base de données et la gérer comme une autre instance de base de données SQL. Vous ne pouvez disposer que d'une seule instance de base de données gratuite. Si vous disposez déjà d'une base de données dans la même région que le nouveau service mobile, vous pouvez alors choisir la base de données existante. Lorsque vous choisissez une base de données existante, veillez à fournir les informations d'identification correctes. Sinon, le service mobile est créé dans un état défectueux.

7. Une fois le service mobile créé, sélectionnez-le à partir de la liste du Gestionnaire de services, puis cliquez sur **OK**.

	Une fois que l'Assistant a terminé, les packages NuGet sont installés, une référence à la bibliothèque cliente Mobile Services est ajoutée au projet et son code source est mis à jour.



<!--HONumber=42-->

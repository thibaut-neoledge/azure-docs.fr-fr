1.	Connectez-vous au [portail Microsoft Azure en version préliminaire](https://portal.azure.com/) en ligne.
2.	Dans la barre de lancement, cliquez sur **Nouveau**, sélectionnez **Données + stockage**, puis cliquez sur **DocumentDB**. 

	![Capture du portail Azure][1]   

	<!-- Alternatively, from the Startboard, you can browse the Azure Marketplace, select **Data + storage**, choose **DocumentDB**, and then click **Create**.  -->
	
	<!-- ![Screen shot of the Azure preview portal, showing the Marketplace blade with the DocumentDB tile highlighted, and the DocumentDB blade with the Create database button highlighted][2]    -->
   

3. Dans le panneau **Nouveau compte DocumentDB**, indiquez la configuration souhaitée pour le compte DocumentDB. 
 
	![Capture d’écran du panneau Nouveau DocumentDB][3] 


	- Dans la zone **ID**, entrez un nom pour identifier le compte DocumentDB. Lorsque l’**ID** est validé, une coche verte s’affiche dans la case **ID**. La valeur pour **ID** devient le nom d’hôte dans l’URI. Cet **ID** ne peut contenir que des minuscules, des chiffres, le caractère « - » et doit compter entre 3 et 50 caractères. Notez que *documents.azure.com* sera ajouté au nom du point de terminaison de votre choix. Celui-ci deviendra le point de terminaison de votre compte DocumentDB.

	- L’objectif **Niveau de compte** est verrouillé car DocumentDB prend en charge un niveau de compte standard unique. Pour plus d’informations, consultez [Tarification de DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409).

	- Dans **Groupe de ressources**, sélectionnez ou créez un groupe de ressources pour votre compte DocumentDB. Par défaut, un nouveau groupe de ressources est créé. Cependant, vous pouvez choisir de sélectionner un groupe de ressources existant auquel ajouter votre compte DocumentDB. Pour plus d’informations, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](resource-group-portal.md).

	- Dans **Abonnement**, sélectionnez l'abonnement Azure à utiliser avec le compte DocumentDB. Si votre compte ne comporte qu’un seul abonnement, ce compte sera sélectionné par défaut.
 
	- Utilisez **Emplacement** pour indiquer l’emplacement géographique de l’hébergement de votre compte DocumentDB.

4.	Une fois les options du nouveau compte DocumentDB configurées, cliquez sur **Créer**. La création du compte DocumentDB peut prendre plusieurs minutes. Pour vérifier l’état, vous pouvez suivre l’avancement sur le Tableau d’accueil.  
	![Capture d’écran de la vignette Création dans le Tableau d’accueil - créateur de base de données en ligne][4]  
  
	Vous pouvez aussi surveiller l’avancement depuis le hub de notifications.  

	![Création rapide de bases de données - capture d’écran du concentrateur de Notifications, indiquant que le compte DocumentDB est créé - notification de créateur de base de données en ligne][5]  

	![Capture d’écran du hub de notifications montrant le compte DocumentDB créé avec succès et déployé vers un groupe de ressources][6]

5.	Une fois le compte DocumentDB créé, il est prêt à être utilisé avec les paramètres par défaut dans le portail en ligne. Notez que la cohérence par défaut du compte DocumentDB est définie sur **Par session**. Vous pouvez ajuster le paramètre de cohérence par défaut en cliquant sur la vignette **Cohérence par défaut** dans le panneau **Compte DocumentDB**.

    ![Capture d’écran du panneau Groupe de ressources][7]  

<!--Image references-->
[1]: media/documentdb-create-dbaccount/ca1.png
[2]: media/documentdb-create-dbaccount/ca2.png
[3]: media/documentdb-create-dbaccount/ca3.png
[4]: media/documentdb-create-dbaccount/ca4.png
[5]: media/documentdb-create-dbaccount/ca5.png
[6]: media/documentdb-create-dbaccount/ca6.png
[7]: media/documentdb-create-dbaccount/ca7.png

[How to: Create a DocumentDB account]: #Howto
[Next steps]: #NextSteps
[documentdb-manage]: ../articles/documentdb/documentdb-manage.md

<!----HONumber=August15_HO6-->
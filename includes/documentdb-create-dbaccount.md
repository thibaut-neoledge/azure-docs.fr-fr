1.	Connectez-vous au [portail Azure Preview](https://portal.azure.com/).
2.	Dans la barre de lancement, cliquez sur **Nouveau**, sélectionnez **Données + stockage**, puis cliquez sur **DocumentDB**. 

	![Capture d'écran du portail Azure en version préliminaire : mise en surbrillance du bouton **Nouveau**, de **Données + stockage** dans le panneau Créer et de **DocumentDB** dans le panneau Données + stockage][1]   

	Depuis le Tableau d'accueil, vous pouvez également parcourir Azure Marketplace, sélectionner la catégorie **Données + analyse**, choisir **DocumentDB**, puis cliquer sur **Créer**.  
	
	![Capture d'écran du portail Azure en version préliminaire : affichage du panneau Marketplace avec la vignette DocumentDB mise en surbrillance, et du panneau DocumentDB avec le bouton Créer mis en surbrillance][2]   
   

3. Dans le panneau **Nouveau DocumentDB (Preview)**, indiquez la configuration souhaitée pour le compte DocumentDB. 
 
	![Capture d'écran du panneau Nouveau DocumentDB (aperçu)][3] 


	- Dans la zone **Id**, entrez un nom pour identifier le compte DocumentDB. Cette valeur devient le nom d'hôte dans l'URI. Ce nom ne peut contenir que des minuscules, des chiffres, le caractère " - " et doit compter entre 3 et 50 caractères. Notez que documents.azure.com sera ajouté au nom du point de terminaison de votre choix. Celui-ci deviendra le point de terminaison de votre compte DocumentDB.

	- La thématique **Niveau de tarification** est verrouillée, car la version préliminaire de DocumentDB prend en charge un seul niveau de tarification standard. Pour plus d'informations, consultez [Tarification de DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409).

	- La thématique **Configuration facultative** permet d'indiquer la capacité initiale allouée à votre compte DocumentDB.  DocumentDB exploite les unités de capacité pour vous permettre de faire évoluer votre compte DocumentDB. Chaque unité de capacité inclut le stockage et le débit réservés à la base de données.  Par défaut, 1 unité de capacité est approvisionnée.  Vous pouvez régler à tout moment le nombre d'unités de capacité disponibles pour votre compte DocumentDB via la [version préliminaire du portail de gestion](https://portal.azure.com/#gallery/Microsoft.DocumentDB). Pour plus d'informations sur la capacité et le débit du compte DocumentDB, consultez l'article sur la [gestion de la capacité et des performances de DocumentDB][documentdb-manage].

	- Dans **Groupe de ressources**, sélectionnez ou créez un groupe de ressources pour votre compte DocumentDB.  Par défaut, un nouveau groupe de ressources est créé.  Cependant, vous pouvez choisir de sélectionner un groupe de ressources existant auquel ajouter votre compte DocumentDB. Pour plus d'informations, voir [Utilisation des groupes de ressources pour gérer vos ressources Azure](azure-preview-portal-using-resource-groups.md).

	- Dans **Abonnement**, sélectionnez l'abonnement Azure à utiliser avec le compte DocumentDB. Si votre compte ne comporte qu'un seul abonnement, ce compte sera sélectionné automatiquement.*
 
	- Utilisez **Emplacement** pour indiquer l'emplacement géographique où votre compte DocumentDB sera hébergé.   

3.	Une fois les options du nouveau compte DocumentDB configurées, cliquez sur **Créer**.  La création du compte DocumentDB peut prendre plusieurs minutes.  Pour vérifier l'état, vous pouvez suivre l'avancement sur le Tableau d'accueil.  
	![Capture d'écran de la vignette Création dans le Tableau d'accueil][4]  
  
	Vous pouvez aussi surveiller l'avancement depuis le hub de notifications.  

	![Capture d'écran du hub de notifications : le compte DocumentDB est en cours de création][5]  

	![Capture d'écran du hub de notifications : le compte DocumentDB a été créé et déployé vers un groupe de ressources][6]

4.	Une fois le compte DocumentDB créé, il est prêt à être utilisé avec les paramètres par défaut.

	*Remarque : la cohérence du compte DocumentDB sera définie par défaut avec la valeur Session.  Vous pouvez régler le paramètre de cohérence par défaut via la [version préliminaire du portail de gestion](https://portal.azure.com/#gallery/Microsoft.DocumentDB).*  
	![Capture d'écran du panneau Groupe de ressources][7]  


<!--Image references-->
[1]: ./media/documentdb-create-dbaccount/ca1.png
[2]: ./media/documentdb-create-dbaccount/ca2.png
[3]: ./media/documentdb-create-dbaccount/ca3.png
[4]: ./media/documentdb-create-dbaccount/ca4.png
[5]: ./media/documentdb-create-dbaccount/ca5.png
[6]: ./media/documentdb-create-dbaccount/ca6.png
[7]: ./media/documentdb-create-dbaccount/ca7.png

[Création d'un compte DocumentDB]: #Howto
[Étapes suivantes]: #NextSteps
[documentdb-manage]:../articles/documentdb-manage.md

<!--HONumber=49-->
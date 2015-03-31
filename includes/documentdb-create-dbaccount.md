1.	Connectez-vous au [portail Azure Preview](https://portal.azure.com/).
2.	Dans la barre de lancement, cliquez sur **Nouveau**, sélectionnez **Données + stockage**, puis cliquez sur **DocumentDB**. 

	![Screen shot of the Azure Preview portal, highlighting the **New** button, **Data + storage** in the Create blade, and **DocumentDB** in the Data + storage blade][1]   

	Depuis le Tableau d'accueil, vous pouvez également parcourir Azure Marketplace, sélectionner la catégorie **Données + analyse**, choisir **DocumentDB**, puis cliquer sur **Créer**.  
	
	![Screen shot of the Azure Preview portal, showing the Marketplace blade with the DocumentDB tile highlighted, and the DocumentDB blade with the Create button highlighted][2]   
   

3. Dans le panneau **Nouveau DocumentDB (Preview)**, indiquez la configuration souhaitée pour le compte DocumentDB. 
 
	![Screen shot of the New DocumentDB (Preview) blade][3] 


	- Dans la zone **Id**, entrez un nom pour identifier le compte DocumentDB. Cette valeur devient le nom d'hôte dans l'URI. Ce nom ne peut contenir que des minuscules, des chiffres, le caractère " - " et doit compter entre 3 et 50 caractères. Notez que documents.azure.com sera ajouté au nom du point de terminaison de votre choix. Celui-ci deviendra le point de terminaison de votre compte DocumentDB.

	- La thématique **Niveau de tarification** est verrouillée, car la version préliminaire de DocumentDB prend en charge un seul niveau de tarification standard. Pour plus d'informations, consultez [Tarification de DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409).

	- La thématique **Configuration facultative** permet d'indiquer la capacité initiale allouée à votre compte DocumentDB.  DocumentDB exploite les unités de capacité pour vous permettre de faire évoluer votre compte DocumentDB. Chaque unité de capacité inclut le stockage et le débit réservés à la base de données.  Par défaut, 1 unité de capacité est approvisionnée.  Vous pouvez régler à tout moment le nombre d'unités de capacité disponibles pour votre compte DocumentDB via la [version préliminaire du portail de gestion](https://portal.azure.com/#gallery/Microsoft.DocumentDB). Pour plus d'informations sur la capacité et le débit du compte DocumentDB, consultez l'article sur la [gestion de la capacité et des performances de DocumentDB][documentdb-manage].

	- Dans **Groupe de ressources**, sélectionnez ou créez un groupe de ressources pour votre compte DocumentDB.  Par défaut, un nouveau groupe de ressources est créé.  Cependant, vous pouvez choisir de sélectionner un groupe de ressources existant auquel ajouter votre compte DocumentDB. Pour plus d'informations, consultez la rubrique [Utilisation des groupes de ressources pour gérer vos ressources Azure](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups/).

	- Dans **Abonnement**, sélectionnez l'abonnement Azure à utiliser avec le compte DocumentDB. Si votre compte ne comporte qu'un seul abonnement, ce compte sera sélectionné automatiquement.*
 
	- Utilisez **Emplacement** pour indiquer l'emplacement géographique où votre compte DocumentDB sera hébergé.   

3.	Une fois les options du nouveau compte DocumentDB configurées, cliquez sur **Créer**.  La création du compte DocumentDB peut prendre plusieurs minutes.  Pour vérifier l'état, vous pouvez suivre l'avancement sur le Tableau d'accueil.  
	![Screen shot of the Creating tile on the Startboard][4]  
  
	Vous pouvez aussi surveiller l'avancement depuis le hub de notifications.  

	![Screen shot of the Notifications hub, showing that the DocumentDB account is being created][5]  

	![Screen shot of the Notifications hub, showing that the DocumentDB account was created successfully and deplyed to a resource group][6]

4.	Une fois le compte DocumentDB créé, il est prêt à être utilisé avec les paramètres par défaut.

	*Notez que la cohérence par défaut du compte DocumentDB sera configurée sur Session.  Vous pouvez régler le paramètre de cohérence par défaut via le [portail de gestion Preview](https://portal.azure.com/#gallery/Microsoft.DocumentDB).*  
	![Screen shot of the Resource Group blade][7]  


<!--Image references-->
[1]: ./media/documentdb-create-dbaccount/ca1.png
[2]: ./media/documentdb-create-dbaccount/ca2.png
[3]: ./media/documentdb-create-dbaccount/ca3.png
[4]: ./media/documentdb-create-dbaccount/ca4.png
[5]: ./media/documentdb-create-dbaccount/ca5.png
[6]: ./media/documentdb-create-dbaccount/ca6.png
[7]: ./media/documentdb-create-dbaccount/ca7.png

[Procédure : création d'un compte DocumentDB]: #Howto
[Étapes suivantes]: #NextSteps
[documentdb-manage]:../documentdb-manage/
<!--HONumber=47-->

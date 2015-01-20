<properties title="How to Create a DocumentDB Account" pageTitle="Créer un compte de base de données | Azure" description="Découvrez comment créer un compte de base de données de documents NoSQL DocumentDB et choisir des paramètres de compte sur la version préliminaire du portail Azure."  metaKeywords="NoSQL, DocumentDB, database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="mimig" manager="jhubbard" editor="monicar" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/09/2014" ms.author="mimig" />

#Créer un compte de base de données
Pour utiliser Microsoft Azure DocumentDB, vous devez créer un compte DocumentDB.  Cette rubrique décrit la procédure à suivre pour créer un compte DocumentDB sur la version préliminaire du portail de gestion Azure.  


1.	Connectez-vous à la [version préliminaire du portail de gestion](https://portal.azure.com/#gallery/Microsoft.DocumentDB).
2.	Cliquez sur Nouveau -> Compte DocumentDB.  
	![][1]  

	Depuis le Tableau d'accueil, vous pouvez également parcourir Azure Marketplace, sélectionner la catégorie " Données + analyse ", choisir **DocumentDB**, puis cliquer sur **Créer**.  

	![][2]   

3. Dans le panneau **Nouveau DocumentDB (version préliminaire)**, indiquez la configuration souhaitée pour le compte DocumentDB. 
 
	![][3] 


	- Dans la zone **Id**, entrez un nom pour identifier le compte DocumentDB. Cette valeur devient le nom d'hôte dans l'URI. Ce nom ne peut contenir que des minuscules, des chiffres, le caractère " - " et doit compter entre 3 et 50 caractères. Notez que documents.azure.com sera ajouté au nom du point de terminaison de votre choix. Celui-ci deviendra le point de terminaison de votre compte DocumentDB.

	- La thématique **Niveau de tarification** est verrouillée, car la version préliminaire de DocumentDB prend en charge un seul niveau de tarification standard. Pour plus d'informations, consultez [Tarification de DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409).

	- La thématique **Configuration facultative** permet d'indiquer la capacité initiale allouée à votre compte DocumentDB.  DocumentDB exploite les unités de capacité pour vous permettre de faire évoluer votre compte DocumentDB. Chaque unité de capacité inclut le stockage et le débit réservés à la base de données.  Par défaut, 1 unité de capacité est approvisionnée.  Vous pouvez régler à tout moment le nombre d'unités de capacité disponibles pour votre compte DocumentDB via la [version préliminaire du portail de gestion](https://portal.azure.com/#gallery/Microsoft.DocumentDB). Pour plus d'informations sur la capacité et le débit du compte DocumentDB, consultez l'article sur la [gestion de la capacité et des performances de DocumentDB][documentdb-manage].

	- Dans **Groupe de ressources**, sélectionnez ou créez un groupe de ressources pour votre compte DocumentDB.  Par défaut, un nouveau groupe de ressources est créé.  Cependant, vous pouvez choisir de sélectionner un groupe de ressources existant auquel ajouter votre compte DocumentDB. Pour plus d'informations, consultez la rubrique [Utilisation des groupes de ressources pour gérer vos ressources Azure](http://azure.microsoft.com/fr-fr/documentation/articles/azure-preview-portal-using-resource-groups/).

	- Dans **Abonnement**, sélectionnez l'abonnement Azure à utiliser avec le compte DocumentDB. Si votre compte ne comporte qu'un seul abonnement, ce compte sera sélectionné automatiquement.*
 
	- Utilisez **Emplacement** pour indiquer l'emplacement géographique où votre compte DocumentDB sera hébergé.   

3.	Une fois les options du nouveau compte DocumentDB configurées, cliquez sur **Créer**.  La création du compte DocumentDB peut prendre plusieurs minutes.  Pour vérifier l'état, vous pouvez suivre l'avancement sur le Tableau d'accueil.  
	![][4]  
  
	Vous pouvez aussi surveiller l'avancement depuis le hub de notifications.  

	![][5]  

	![][6]

4.	Une fois le compte DocumentDB créé, il est prêt à être utilisé avec les paramètres par défaut.

	*Remarque : la cohérence du compte DocumentDB sera définie par défaut avec la valeur Session.  Vous pouvez régler le paramètre de cohérence par défaut via la [version préliminaire du portail de gestion](https://portal.azure.com/#gallery/Microsoft.DocumentDB).*  
	![][7]  

5.	Vous pouvez également accéder à vos comptes DocumentDB existants à partir du panneau **Parcourir**.  
	![][8]

##<a id="NextSteps"></a>Étapes suivantes
Pour la prise en main d'Azure DocumentDB, explorez les ressources suivantes :

-	[Modèle de ressource et concepts de DocumentDB](/documentation/articles/documentdb-resources/)
-	[Interagir avec les ressources DocumentDB](/documentation/articles/documentdb-interactions-with-resources/)
-	[Comment créer un compte DocumentDB](/documentation/articles/documentdb-create-account/)
-	[Prendre en main un compte DocumentDB](/documentation/articles/documentdb-get-started/)

Pour en savoir plus sur DocumentDB, consultez la documentation Azure DocumentDB à l'adresse [azure.com](http://go.microsoft.com/fwlink/p/?LinkID=402319)

[Procédure : création d'un compte DocumentDB]: #Howto
[Étapes suivantes]: #NextSteps
[documentdb-manage]:../documentdb-manage/

<!--Image references-->
[1]: ./media/documentdb-create-account/ca1.png
[2]: ./media/documentdb-create-account/ca2.png
[3]: ./media/documentdb-create-account/ca3.png
[4]: ./media/documentdb-create-account/ca4.png
[5]: ./media/documentdb-create-account/ca5.png
[6]: ./media/documentdb-create-account/ca6.png
[7]: ./media/documentdb-create-account/ca7.png
[8]: ./media/documentdb-create-account/ca8.png

<!--HONumber=35.2-->

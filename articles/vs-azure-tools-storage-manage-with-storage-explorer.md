<properties
	pageTitle="Gérer les ressources de stockage Azure avec l’Explorateur de stockage (version préliminaire) | Microsoft Azure"
	description="Décrit comment utiliser l’Explorateur de stockage Microsoft Azure (version préliminaire) pour créer et gérer des ressources de stockage Azure."
	services="visual-studio-online"
	documentationCenter="na"
	authors="kempb"
	manager="douge"
	editor="tlee" />

 <tags
	ms.service="visual-studio-online"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="11/17/2015"
	ms.author="kempb" />

# Gérer les ressources de stockage Azure avec l’Explorateur de stockage (version préliminaire)

L’Explorateur de stockage Microsoft Azure (version préliminaire) est un outil autonome qui vous permet de gérer facilement vos comptes de stockage Azure. Il est utile dans les situations où vous souhaitez gérer rapidement le stockage en dehors du portail Azure, par exemple lorsque vous développez des applications dans Visual Studio. Cette version préliminaire vous permet de travailler facilement avec Blob Storage. Vous pouvez créer et supprimer des conteneurs, charger, télécharger et supprimer des objets blob et effectuer des recherches dans tous vos conteneurs et objets blob. Les fonctionnalités avancées permettent aux développeurs et aux opérations de travailler avec des clés et des stratégies SAP. Les développeurs Windows peuvent également utiliser l’émulateur de stockage Azure pour tester leur code à l’aide du compte de stockage de développement local.

Pour afficher ou gérer les ressources de stockage dans l’Explorateur de stockage, vous devez être en mesure d’accéder à un compte de stockage Azure dans votre abonnement ou à un compte de stockage externe. Si vous ne possédez pas de compte de stockage, vous pouvez en créer un en quelques minutes. Si vous avez un abonnement MSDN, consultez [Crédit Azure mensuel pour les abonnés Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Dans le cas contraire, consultez [Version d’évaluation d’un mois gratuite](https://azure.microsoft.com/pricing/free-trial/).

## Gérer les abonnements et les comptes Azure

Pour afficher vos ressources de stockage Azure dans l’Explorateur de stockage, vous devez vous connecter à un compte Azure avec un ou plusieurs abonnements actifs. Si vous avez plusieurs comptes Azure, vous pouvez les ajouter à l’Explorateur de stockage, puis sélectionner les abonnements que vous souhaitez inclure dans l’affichage des ressources de l’Explorateur de stockage. Si vous n’avez jamais utilisé Azure ou que vous n’avez pas ajouté les comptes nécessaires à Visual Studio, vous serez invité à vous connecter à un compte Azure.

### Ajouter un compte Azure à l’Explorateur de stockage

1.	Cliquez sur l’icône **Paramètres** (engrenage) dans la barre d’outils de l’Explorateur de stockage.
1.	Sélectionnez le lien **Ajouter un compte**. Connectez-vous au compte Azure dont vous souhaitez parcourir les ressources de stockage. Le compte que vous venez d'ajouter doit être sélectionné dans la liste déroulante du sélecteur de compte. Tous les abonnements de ce compte apparaissent sous l’entrée du compte.

	![][0]

1.	Cochez les cases des abonnements de compte que vous souhaitez parcourir, puis sélectionnez le bouton **Appliquer**.

	![][1]

	Les ressources de stockage Azure des abonnements sélectionnés apparaissent dans l’Explorateur de stockage.

### Attacher un stockage externe

1. Obtenez le nom du compte et la clé du compte de stockage que vous souhaitez attacher.
	1.	Dans le portail Azure en version préliminaire, choisissez le compte de stockage que vous souhaitez attacher.
	1.	Dans la section **Gérer** du volet **Paramètres** du portail Azure en version préliminaire, choisissez le bouton **Clés**.
	1.	Copiez les valeurs **Nom du compte de stockage** et **Clé d’accès primaire**.

		![][2]

1.	Dans le menu contextuel du nœud **Comptes de stockage** dans l’Explorateur de stockage, choisissez la commande **Attacher un stockage externe**.

	![][3]

1. Entrez le nom du compte de stockage dans la zone **Nom de compte** et la clé d’accès primaire dans la zone **Clé de compte**. Sélectionnez le bouton **Ok** pour continuer.

	![][4]

	Le stockage externe s’affiche dans l’Explorateur de stockage.

	![][5]

1. Pour supprimer le stockage externe, dans le menu contextuel du stockage externe, choisissez la commande Détacher.

	![][6]

## Afficher et parcourir les ressources de stockage

Pour accéder à une ressource de stockage Azure et afficher ses informations dans l’Explorateur de stockage, développez le type de stockage, puis choisissez la ressource. Des informations sur la ressource sélectionnée s’affichent dans les onglets **Actions** et **Propriétés** au bas de l’Explorateur de stockage.

![][7]

-	L’onglet **Actions** indique les actions possibles dans l’Explorateur de stockage pour la ressource de stockage sélectionnée, par exemple l’ouverture, la copie ou la suppression. Les actions apparaissent également dans le menu contextuel de la ressource.

-	L’onglet **Propriétés** affiche les propriétés de la ressource de stockage, notamment son type, ses paramètres régionaux, son groupe de ressources associé et son URL.

Tous les comptes de stockage possèdent l’action **Ouvrir dans le portail**. Lorsque vous sélectionnez cette action, l’Explorateur de stockage affiche le compte de stockage sélectionné dans le portail Azure en version préliminaire.

Des actions et des valeurs de propriétés supplémentaires s’affichent en fonction de la ressource sélectionnée. Par exemple, les nœuds conteneurs d’objets blob, files d’attente et tables possèdent tous une action **Créer**. Les éléments individuels (par exemple, les conteneurs d’objets blob) possèdent des actions telles que **Ouvrir**, **Supprimer** et **Obtenir une signature d’accès partagé**. Une action d’ouverture de l’éditeur d’objets blob apparaît lorsque vous choisissez un objet blob de compte de stockage.

## Effectuer des recherches dans les comptes de stockage et les conteneurs d’objets blob

Pour trouver des comptes de stockage et des conteneurs d’objets blob portant un nom spécifique dans les abonnements de votre compte Azure, entrez le nom dans la zone de **recherche** de l’Explorateur de stockage.

![][8]

Lorsque vous entrez des caractères dans la zone de **recherche**, seuls les comptes de stockage ou les conteneurs d’objets blob dont les noms correspondent à ces caractères s’affichent dans l’arborescence de ressources. Pour effacer la recherche, choisissez le bouton **x** dans la zone de **recherche**.

## Modifier les comptes de stockage

Pour ajouter du contenu à un compte de stockage ou le modifier, choisissez la commande **Ouvrir l’éditeur** pour ce type de stockage. Vous pouvez choisir des actions dans le menu contextuel de l’élément sélectionné, ou dans l’onglet **Actions** en bas de l’Explorateur de stockage.

![][9]

Vous pouvez créer ou supprimer des conteneurs d’objets blob, des files d’attente et des tables. Vous pouvez également modifier des objets blob dans l’Explorateur de stockage en choisissant l’action **Ouvrir l’éditeur de conteneurs d’objets blob**.

### Modifier un conteneur d’objets blob

1.	Choisissez l’action **Ouvrir l’éditeur de conteneurs d’objets blob**. L’éditeur de conteneurs d’objets blob s’affiche dans le volet droit.

	![][10]

1.	Choisissez le bouton **Charger**, puis la commande **Charger des fichiers**.

	![][11]

	Si les fichiers à charger sont dans le même dossier, vous pouvez choisir la commande Charger un dossier à la place.

1. Dans la boîte de dialogue **Charger des fichiers**, sélectionnez le bouton des points de suspension (**…**) sur le côté droit de la zone Fichiers pour sélectionner les fichiers que vous souhaitez charger. Ensuite, choisissez le type d’objet blob sous la forme duquel vous souhaitez les charger (bloc, page ou ajout). Si vous le souhaitez, vous pouvez choisir de charger les fichiers dans un dossier du conteneur d’objets blob. Entrez le nom du dossier dans la zone **Charger vers le dossier (facultatif)**. Si le dossier n’existe pas, il est créé.

	![][12]

	Dans la capture d’écran suivante, trois fichiers image ont été chargés vers un nouveau dossier appelé **Mes nouveaux fichiers** dans le conteneur d’objets blob **Images**.

	![][13]

	Les boutons de la barre d’outils de l’éditeur d’objets blob vous permettent de sélectionner, de télécharger, d’ouvrir, de copier et de supprimer les fichiers, et bien plus encore. Le volet **Activité** en bas de la boîte de dialogue indique si votre opération a réussi et vous permet de supprimer uniquement les activités réussies de l’affichage, ou d’effacer l’intégralité du volet. Choisissez l’icône **+** en regard des fichiers chargés pour afficher une liste détaillée des fichiers chargés.

## Créer une signature d’accès partagé (SAP)

Pour certaines opérations, une SAP peut être nécessaire pour accéder à une ressource de stockage. Vous pouvez en créer une à l’aide de l’Explorateur de stockage.

1.	Sélectionnez l’élément pour lequel vous souhaitez créer une SAP, puis choisissez la commande **Obtenir une signature d’accès partagé** dans le volet **Actions** ou dans le menu contextuel de l’élément.

	![][14]

1.	Dans la boîte de dialogue **Signature d’accès partagé**, sélectionnez la stratégie, les dates de début et d’expiration et le fuseau horaire. En outre, cochez les cases correspondant aux niveaux d’accès de votre choix pour la ressource, par exemple, lecture seule, lecture-écriture, etc. Lorsque vous avez terminé, choisissez le bouton **Créer** pour créer la SAP.

	![][15]

1.	La boîte de dialogue **Signature d’accès partagé** répertorie les conteneurs, ainsi que les URL et les chaînes de requête que vous pouvez utiliser pour accéder à la ressource de stockage. Choisissez le bouton **Copier** pour copier les chaînes.

	![][16]

## Gérer la SAP et les autorisations

Pour contrôler l’accès aux conteneurs d’objets blob, vous pouvez choisir les commandes **Gérer la liste de contrôle d’accès** et **Définir le niveau d’accès public**.

-	La commande Gérer la liste de contrôle d’accès vous permet d’ajouter, de modifier et de supprimer des stratégies d’accès (si les utilisateurs peuvent lire, écrire et ainsi de suite) sur le conteneur d’objets blob sélectionné.
-	La commande Définir le niveau d’accès public vous permet de déterminer l’accès à la ressource dont bénéficient les utilisateurs publics.  

-

1.	Sélectionnez le conteneur d’objets blob, puis la commande **Gérer la liste de contrôle d’accès** dans le menu contextuel ou dans le volet **Actions**.

	![][17]

1.	Dans la boîte de dialogue **Liste de contrôle d’accès**, sélectionnez le bouton **Ajouter** pour ajouter des stratégies d’accès. Choisissez une stratégie d’accès, puis sélectionnez les autorisations pour celle-ci. Lorsque vous avez terminé, cliquez sur le bouton **Enregistrer**.

	![][18]

1.	Pour définir un niveau d’accès pour un conteneur d’objets blob, sélectionnez-le dans l’Explorateur de stockage, puis choisissez la commande **Définir le niveau d’accès public** dans le menu contextuel ou dans le volet **Actions**.

	![][19]

1.	Dans la boîte de dialogue **Définir le niveau d’accès public du conteneur**, sélectionnez la case d’option pour le niveau d’accès que vous souhaitez accorder aux utilisateurs publics, puis cliquez sur le bouton **Appliquer**.

	![][20]

## Étapes suivantes
En savoir plus sur les fonctionnalités dans les services Azure Storage en lisant les articles de la page [Introduction à Microsoft Azure Storage](storage-introduction.md).

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AddAccount1c.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AddAccount2c.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External1c.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External2c.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External3c.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External4c.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/External5c.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Navigatec.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Searchc.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit1c.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit2c.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit3c.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit4c.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Edit5c.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SAS1c.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SAS2c.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SAS3c.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageSAS1c.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageSAS2c.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageSAS3c.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageSAS4c.png

<!---HONumber=AcomDC_1217_2015-->
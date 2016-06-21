<properties
	pageTitle="Gérer les ressources Azure Blob Storage avec l’Explorateur de stockage (version préliminaire) | Microsoft Azure"
	description="Gérer les conteneurs d’objets blob et les blobs Azure avec l’Explorateur de stockage (version préliminaire)"
	services="visual-studio-online"
	documentationCenter="na"
	authors="TomArcher"
	manager="douge"
	editor="" />

 <tags
	ms.service="visual-studio-online"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="06/05/2016"
	ms.author="tarcher" />

# Gérer les ressources Azure Blob Storage avec l’Explorateur de stockage (version préliminaire)

## Vue d'ensemble

[Azure Blob Storage](./storage/storage-dotnet-how-to-use-blobs.md) est un service permettant de stocker de gros volumes de données non structurées, telles que du texte ou des données binaires, accessibles depuis n’importe où dans le monde via HTTP ou HTTPS. Vous pouvez utiliser le stockage d'objets blob pour exposer les données publiquement dans le monde ou pour le stockage privé de données d'applications. Dans cet article, vous allez apprendre à utiliser l’Explorateur de stockage (version préliminaire) pour utiliser des conteneurs d’objets blob et des objets blob.

## Composants requis

Pour pouvoir suivre les étapes de cet article, vous devrez :

- [Télécharger et installer l’Explorateur de stockage (version préliminaire)](http://www.storageexplorer.com)
- [Vous connecter à un service ou un compte de stockage Azure](./vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## Création d’un conteneur d’objets blob

Tous les objets blob doivent résider dans un conteneur d’objets blob, c’est-à-dire un simple regroupement logique d’objets blob. Un compte peut contenir un nombre illimité de conteneurs, et chaque conteneur peut stocker un nombre illimité d’objets blob.

Les étapes suivantes expliquent comment créer un conteneur d’objets blob dans l’Explorateur de stockage (version préliminaire).

1.	Ouvrez l’Explorateur de stockage (version préliminaire).
1.	Dans le volet gauche, développez le compte de stockage dans lequel vous souhaitez créer le conteneur d’objets blob.
1.	Cliquez avec le bouton droit sur **Conteneurs d’objets Blob** puis, dans le menu contextuel, sélectionnez **Créer un conteneur d’objets blob**.

	![Création de conteneurs d’objets blob - Menu contextuel][0]

1.	Une zone de texte apparaît sous le dossier **Conteneurs d’objets blob**. Entrez le nom de votre conteneur d’objets blob. Consultez la section relative aux [règles d’affectation des noms de conteneurs](./storage/storage-dotnet-how-to-use-blobs.md#create-a-container) pour obtenir la liste des règles et restrictions applicables aux noms de conteneurs d’objets blob.

	![Création de conteneurs d’objets blob - Zone de texte][1]

1.	Appuyez sur **Entrée** pour créer le conteneur d’objets blob, ou sur **ÉCHAP** pour annuler. Une fois le conteneur créé, il apparaît sous le dossier **Conteneurs d’objets blob** correspondant au compte de stockage sélectionné.

	![Conteneur d’objets blob créé][2]

## Affichage du contenu d’un conteneur d’objets blob

Les conteneurs d’objets blob contiennent des objets blob et des dossiers (qui peuvent eux-mêmes contenir des objets blob).

Les étapes suivantes expliquent comment afficher un conteneur d’objets blob dans l’Explorateur de stockage (version préliminaire) :

1.	Ouvrez l’Explorateur de stockage (version préliminaire).
1.	Dans le volet gauche, développez le compte de stockage contenant le conteneur d’objets blob que vous souhaitez afficher.
1.	Développez le dossier **Conteneurs d’objets blob** du compte de stockage.
1.	Cliquez avec le bouton droit sur le conteneur d’objets blob que vous souhaitez afficher puis, dans le menu contextuel, sélectionnez **Ouvrir l’éditeur de conteneur d’objets blob**. Vous pouvez également double-cliquer sur le conteneur d’objets blob que vous souhaitez afficher.

	![Ouvrir l’éditeur de conteneurs d’objets blob - Menu contextuel][19]

1.	Le volet principal affiche le contenu du conteneur d’objets blob.

	![Éditeur de conteneurs d’objets blob][3]

## Suppression d'un conteneur d'objets blob

Vous pouvez facilement créer et supprimer des conteneurs d’objets blob selon vos besoins (pour savoir comment supprimer des objets blob, reportez-vous à la section [Gestion des objets blob dans un conteneur d’objets blob](./#managing-blobs-in-a-blob-container)).

Les étapes suivantes expliquent comment supprimer un conteneur d’objets blob dans l’Explorateur de stockage (version préliminaire) :

1.	Ouvrez l’Explorateur de stockage (version préliminaire).
1.	Dans le volet gauche, développez le compte de stockage contenant le conteneur d’objets blob que vous souhaitez afficher.
1.	Développez le dossier **Conteneurs d’objets blob** du compte de stockage.
1.	Cliquez avec le bouton droit sur le conteneur d’objets blob que vous souhaitez supprimer puis, dans le menu contextuel, sélectionnez **Supprimer**. Vous pouvez également appuyer sur **Supprimer** pour supprimer le conteneur d’objets blob actuellement sélectionné.

	![Supprimer un conteneur d’objets blob - Menu contextuel][4]

1.	Cliquez sur **Oui** dans la boîte de dialogue de confirmation.

	![Supprimer un conteneur d’objets blob - Confirmation][5]

## Copie d’un conteneur d’objets blob

L’Explorateur de stockage (version préliminaire) vous permet de copier un conteneur d’objets blob dans le Presse-papiers, puis de coller ce conteneur d’objets blob dans un autre compte de stockage (pour savoir comment copier des objets blob, reportez-vous à la section [Gestion des objets blob dans un conteneur d’objets blob](./#managing-blobs-in-a-blob-container)).

Les étapes suivantes expliquent comment copier un conteneur d’objets blob d’un compte de stockage à un autre.

1.	Ouvrez l’Explorateur de stockage (version préliminaire).
1.	Dans le volet gauche, développez le compte de stockage contenant le conteneur d’objets blob que vous souhaitez copier.
1.	Développez le dossier **Conteneurs d’objets blob** du compte de stockage.
1.	Cliquez avec le bouton droit sur le conteneur d’objets blob que vous souhaitez afficher puis, dans le menu contextuel, sélectionnez **Copy Blob Container** (Copier le conteneur d’objets blob).

	![Copier un conteneur d’objets blob - Menu contextuel][6]

1.	Cliquez avec le bouton droit sur le compte de stockage cible dans lequel vous souhaitez coller le conteneur d’objets blob puis, dans le menu contextuel, sélectionnez **Paste Blob Container** (Coller le conteneur d’objets blob).

	![Coller un conteneur d’objets blob - Menu contextuel][7]

## Obtenir la signature d’accès partagé pour un conteneur d’objets blob

Une [signature d’accès partagé (SAP)](./storage/storage-dotnet-shared-access-signature-part-1.md) fournit un accès délégué aux ressources de votre compte de stockage. Cela vous permet d’octroyer à un client des autorisations d’accès limité à des objets de votre compte de stockage pendant une période donnée et avec un ensemble défini d’autorisations, sans partager les clés d’accès de votre compte.

Les étapes suivantes expliquent comment créer une signature d’accès partagé pour un conteneur d’objets blob :

1.	Ouvrez l’Explorateur de stockage (version préliminaire).
1.	Dans le volet gauche, développez le compte de stockage contenant le conteneur d’objets blob pour lequel vous souhaitez obtenir une SAP.
1.	Développez le dossier **Conteneurs d’objets blob** du compte de stockage.
1.	Cliquez avec le bouton droit sur le conteneur d’objets blob souhaité puis, dans le menu contextuel, sélectionnez **Get Shared Access Signature** (Obtenir une signature d’accès partagé).

	![Obtenir une signature d’accès partagé - Menu contextuel][8]

1.	Dans la boîte de dialogue **Signature d’accès partagé**, spécifiez la stratégie, les dates de début et d’expiration, le fuseau horaire et les niveaux d’accès de la ressource.

	![Obtenir une signature d’accès partagé - Options][9]

1.	Une fois les options SAP spécifiées, sélectionnez **Créer**.

1.	Vous accédez alors à une deuxième boîte de dialogue **Signature d’accès partagé** dans laquelle vous pouvez visualiser le conteneur d’objets blob, ainsi que les URL et les chaînes de requête que vous pouvez utiliser pour accéder à la ressource de stockage. Sélectionnez **Copier** en regard de l’URL que vous souhaitez copier dans le Presse-papiers.

	![Copier les URL de SAP][10]

1.	Lorsque vous avez terminé, sélectionnez **Fermer**.

## Gestion des stratégies d’accès d’un conteneur d’objets blob

Les étapes suivantes montrent comment gérer (ajouter et supprimer) les stratégies d’accès d’un conteneur d’objet :

1.	Ouvrez l’Explorateur de stockage (version préliminaire).
1.	Dans le volet gauche, développez le compte de stockage contenant le conteneur d’objets blob pour lequel vous souhaitez gérer les stratégies d’accès.
1.	Développez le dossier **Conteneurs d’objets blob** du compte de stockage.
1.	Sélectionnez le conteneur d’objets blob souhaité puis, dans le menu contextuel, sélectionnez **Manage Access Policies** (Gérer les stratégies d’accès).

	![Gérer les stratégies d’accès - Menu contextuel][11]

1.	La boîte de dialogue **Stratégies d’accès** répertorie les stratégies d’accès déjà créées pour le conteneur d’objets blob sélectionné.

	![Options de stratégie d’accès][12]

1.	Suivez ces étapes en fonction de la tâche de gestion des stratégies d’accès :

	- **Ajouter une nouvelle stratégie d’accès** : sélectionnez **Ajouter**. Une fois la stratégie générée, la boîte de dialogue **Stratégies d’accès** affiche la stratégie d’accès que vous venez d’ajouter (avec les paramètres par défaut).
	- **Modifier une stratégie d’accès** : apportez les modifications souhaitées, puis cliquez sur **Enregistrer**.
	- **Supprimer une stratégie d’accès** : sélectionnez **Supprimer** en regard de la stratégie d’accès à supprimer.

## Définir le niveau d’accès public pour un conteneur d’objets blob

Par défaut, chaque conteneur d’objets blob est défini sur « No public access » (Aucun accès public).

Les étapes suivantes expliquent comment spécifier un niveau d’accès public pour un conteneur d’objets blob.

1.	Ouvrez l’Explorateur de stockage (version préliminaire).
1.	Dans le volet gauche, développez le compte de stockage contenant le conteneur d’objets blob pour lequel vous souhaitez gérer les stratégies d’accès.
1.	Développez le dossier **Conteneurs d’objets blob** du compte de stockage.
1.	Sélectionnez le conteneur d’objets blob souhaité puis, dans le menu contextuel, sélectionnez **Set Public Access Level** (Définir le niveau d’accès public).

	![Définir le niveau d’accès public - Menu contextuel][13]

1.	Dans la boîte de dialogue **Set Container Public Access Level** (Définir le niveau d’accès public du conteneur), spécifiez le niveau d’accès souhaité.

	![Définir le niveau d’accès public - Options][14]

1.	Sélectionnez **Appliquer**.

## Gestion des objets blob dans un conteneur d’objets blob

Une fois que vous avez créé un conteneur d’objets blob, vous pouvez effectuer de nombreuses tâches, par exemple charger un objet blob dans ce conteneur, télécharger un objet blob sur votre ordinateur local, ouvrir un objet blob sur votre ordinateur local, etc.

Les étapes suivantes expliquent comment gérer les objets blob (et les dossiers) dans un conteneur d’objets blob.

1.	Ouvrez l’Explorateur de stockage (version préliminaire).
1.	Dans le volet gauche, développez le compte de stockage contenant le conteneur d’objets blob que vous souhaitez gérer.
1.	Développez le dossier **Conteneurs d’objets blob** du compte de stockage.
1.	Double-cliquez sur le conteneur d’objets blob que vous souhaitez afficher.
1.	Le volet principal affiche le contenu du conteneur d’objets blob.

	![Afficher le conteneur d’objets blob][3]

1.	Le volet principal affiche le contenu du conteneur d’objets blob.

1.	Suivez ces étapes en fonction de la tâche que vous souhaitez effectuer :

	- **Télécharger des fichiers vers un conteneur d’objets blob**

		1.	Dans la barre d’outils du volet principal, sélectionnez **Télécharger**, puis **Télécharger des fichiers** dans le menu contextuel.

			![Télécharger des fichiers - Menu][15]

		1.	Dans la boîte de dialogue **Télécharger des fichiers**, sélectionnez le bouton des points de suspension (**…**) situé sur le côté droit de la zone **Fichiers** pour sélectionner les fichiers que vous souhaitez charger.

			![Télécharger des fichiers - Options][16]

		1.	Spécifiez le **Type d’objet blob**. L’article [Prise en main du stockage d’objets blob Azure à l’aide de .NET](./storage/storage-dotnet-how-to-use-blobs.md#blob-service-concepts) explique les différences entre les divers types d’objets blob.

		1.	Si vous le souhaitez, spécifiez un dossier cible dans lequel charger les fichiers sélectionnés. Si le dossier cible n’existe pas, il sera créé.

		1.	Sélectionnez **Télécharger**.

	- **Télécharger un dossier dans un conteneur d’objets blob**

		1.	Dans la barre d’outils du volet principal, sélectionnez **Télécharger**, puis **Upload Folder** (Télécharger un dossier) dans le menu contextuel.

			![Télécharger un dossier - Menu][17]

		1.	Dans la boîte de dialogue **Upload Folder** (Télécharger un dossier), sélectionnez le bouton des points de suspension (**…**) situé sur le côté droit de la zone **Dossier** pour sélectionner le dossier que vous souhaitez charger.

			![Télécharger un dossier - Options][18]

		1.	Spécifiez le **Type d’objet blob**. L’article [Prise en main du stockage d’objets blob Azure à l’aide de .NET](./storage/storage-dotnet-how-to-use-blobs.md#blob-service-concepts) explique les différences entre les divers types d’objets blob.

		1.	Si vous le souhaitez, spécifiez un dossier cible dans lequel charger le contenu du dossier sélectionné. Si le dossier cible n’existe pas, il sera créé.

		1.	Sélectionnez **Télécharger**.

	- **Télécharger un objet blob sur votre ordinateur local**

		1.	Sélectionnez l’objet blob que vous souhaitez télécharger.

		1.	Dans la barre d’outils du volet principal, sélectionnez **Télécharger**.

		1.	Dans la boîte de dialogue **Specify where to save the downloaded blob** (Indiquer où enregistrer l’objet blob téléchargé), spécifiez l’emplacement dans lequel vous souhaitez enregistrer l’objet blob téléchargé ainsi que le nom que vous souhaitez lui donner.

		1.	Sélectionnez **Enregistrer**.

	- **Ouvrir un objet blob sur votre ordinateur local**

		1.	Sélectionnez l’objet blob que vous souhaitez ouvrir.

		1.	Dans la barre d’outils du volet principal, sélectionnez **Ouvrir**.

		1.	L’objet blob est téléchargé et ouvert à l’aide de l’application associée au type de fichier sous-jacent de l’objet blob.

	- **Copier un objet blob dans le Presse-papiers**

		1.	Sélectionnez l’objet blob que vous souhaitez copier.

		1.	Dans la barre d’outils du volet principal, sélectionnez **Copier**.

		1.	Dans le volet de gauche, accédez à un autre conteneur d’objets blob et double-cliquez dessus pour l’afficher dans le volet principal.

		1.	Dans la barre d’outils du volet principal, sélectionnez **Coller** pour créer une copie de l’objet blob.

	- **Supprimer un blob.**

		1.	Sélectionnez l’objet blob que vous souhaitez supprimer.

		1.	Dans la barre d’outils du volet principal, sélectionnez **Supprimer**.

		1.	Cliquez sur **Oui** dans la boîte de dialogue de confirmation.

## Étapes suivantes

- Consultez les [dernières notes de publication et vidéos de l’Explorateur de stockage (version préliminaire)](http://www.storageexplorer.com).
- Découvrez comment [créer des applications à l’aide d'objets blob, de tables, de files d’attente et de fichiers Azure](https://azure.microsoft.com/documentation/services/storage/).

[0]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-create-context-menu.png
[1]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create.png
[2]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create-done.png
[3]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-editor.png
[4]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-context-menu.png
[5]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-confirmation.png
[6]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-copy-context-menu.png
[7]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-paste-context-menu.png
[8]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-context-menu.png
[9]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-options.png
[10]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-urls.png
[11]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-context-menu.png
[12]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-options.png
[13]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-context-menu.png
[14]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-options.png
[15]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-menu.png
[16]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-options.png
[17]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-menu.png
[18]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-options.png
[19]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-open-editor-context-menu.png

<!---HONumber=AcomDC_0608_2016-->
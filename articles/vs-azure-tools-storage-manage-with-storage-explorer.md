<properties
	pageTitle="Prise en main de l’explorateur de stockage (version préliminaire) | Microsoft Azure"
	description="Gérer les ressources de stockage Azure avec l’Explorateur de stockage (version préliminaire)"
	services="visual-studio-online"
	documentationCenter="na"
	authors="TomArcher"
	manager="douge"
	editor="" />

 <tags
	ms.service="visual-studio-online"
	ms.devlang="multiple"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="06/05/2016"
	ms.author="tarcher" />

# Prise en main de l’explorateur de stockage (version préliminaire)

## Vue d'ensemble 

L’explorateur de stockage Microsoft Azure (version préliminaire) est une application autonome qui vous permet d’utiliser facilement les données Azure Storage sur Windows, OSX et Linux. Dans cet article, vous découvrirez les différentes façons de vous connecter à vos comptes de stockage Azure et de les gérer.

## Configuration requise

- [Télécharger et installer l’explorateur de stockage (version préliminaire)](http://go.microsoft.com/fwlink/?LinkId=708343)

## Connexion à un service ou un compte de stockage

L’explorateur de stockage (version préliminaire) offre de nombreuses façons de se connecter à des comptes de stockage. Il vous permet notamment de vous connecter aux comptes de stockage associés à vos abonnements Azure, de vous connecter à des services et comptes de stockage partagés à partir d’autres abonnements Azure et même de vous connecter au stockage local et de le gérer à l’aide de l’émulateur de stockage Azure :

- [Connexion à un abonnement Azure](#connect-to-an-azure-subscription) : gérez les ressources de stockage appartenant à votre abonnement Azure.
- [Connexion au stockage local ](#connect-to-local-storage) : gérez le stockage local à l’aide de l’émulateur de stockage Azure. 
- [Attachement à un stockage externe](#attach-or-detach-an-external-storage-account) : gérez les ressources de stockage appartenant à un autre abonnement Azure à l’aide du nom et de la clé du compte de stockage.
- [Attachement d’un compte à l’aide d’une SAP](#attach-account-using-sas) : gérez les ressources de stockage appartenant à un autre abonnement Azure à l’aide d’une SAP.
- [Attachement d’un service à l’aide d’une SAP](#attach-service-using-sas) : gérez un service de stockage spécifique (conteneur d’objets blob, file d’attente ou table) appartenant à un autre abonnement Azure à l’aide d’une SAP.

## Connexion à un abonnement Azure

> [AZURE.NOTE] Si vous ne possédez pas de compte Azure, vous pouvez [vous inscrire à un essai gratuit](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [activer les avantages de votre abonnement Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

1. Démarrez l’explorateur de stockage (version préliminaire). 

1. Si vous exécutez l’explorateur de stockage (version préliminaire) pour la première fois ou si vous l’avez déjà exécuté mais ne vous êtes pas connecté à un compte Azure, une barre d’informations vous permettra de vous connecter à un compte Azure.

	![][0]
	
1. Sélectionnez **Connexion à Microsoft Azure** et suivez les boîtes de dialogue pour vous connecter avec un compte Microsoft associé à un ou plusieurs abonnements Azure actifs.

Une fois que vous être connecté à un compte Microsoft, le volet gauche de l’explorateur de stockage (version préliminaire) est renseigné avec tous les comptes de stockage associés à l’ensemble des abonnements Azure associés au compte Microsoft.

### Filtrer les abonnements Azure

L’explorateur de stockage (version préliminaire) vous permet de filtrer les abonnements Azure associés à votre ou vos comptes Microsoft connectés, dont les comptes de stockage seront répertoriés dans le volet gauche.

1. Sélectionnez l’icône **Paramètres** (engrenage).

	![][1]

1. 	Une liste déroulante contenant tous les comptes Microsoft auxquels vous vous êtes connecté est disponible en haut du volet gauche.

	![][3]
	 
1.	Sélectionnez la flèche vers le bas en regard de la liste déroulante pour afficher tous les comptes Microsoft connectés, ainsi qu’un lien pour ajouter (se connecter à) d’autres comptes Microsoft.

	![][4]

1.	Dans la liste déroulante, sélectionnez le compte Microsoft de votre choix.

1. 	Le volet gauche affiche tous les abonnements Azure associés au compte Microsoft sélectionné. La case à cocher à gauche de chaque abonnement Azure vous permet de spécifier si vous souhaitez ou non utiliser l’explorateur de stockage (version préliminaire) pour répertorier tous les comptes de stockage associés à ces abonnements Azure. La case à cocher **Tous les abonnements** permet de sélectionner ou désélectionner l’ensemble des abonnements Azure répertoriés.

	![][2]

1.	Lorsque vous avez sélectionné les abonnements Azure que vous souhaitez gérer, sélectionnez **Appliquer**. Le volet gauche est mis à jour pour afficher la liste complète des comptes de stockage pour chaque abonnement Azure sélectionné correspondant au compte Microsoft actuel.

### Ajout d’autres comptes Microsoft

Les étapes suivantes vous permettent de vous connecter à d’autres comptes Microsoft pour afficher les abonnements et comptes de stockage Azure de chaque compte.

1.	Sélectionnez l’icône **Paramètres** (engrenage).

	![][1]

1. 	Une liste déroulante contenant tous les comptes Microsoft actuellement connectés est disponible en haut du volet gauche.

	![][3]
	 
1.	Sélectionnez la flèche vers le bas en regard de la liste déroulante pour afficher tous les comptes Microsoft connectés, ainsi qu’un lien pour ajouter (se connecter à) d’autres comptes Microsoft.

	![][4]

1.	Sélectionnez **Ajouter un compte** et suivez les boîtes de dialogue pour vous connecter à un compte associé à un ou plusieurs abonnements Azure actifs.

1.	Sélectionnez les cases à cocher correspondant aux abonnements Azure que vous souhaitez parcourir.

	![][2]

1.	Sélectionnez **Appliquer**.

### Basculer entre des comptes Microsoft

Même si vous pouvez vous connecter à plusieurs comptes Microsoft, le volet gauche affiche uniquement les comptes de stockage associés aux abonnements d’un seul compte Microsoft (le compte actuel). Si vous vous connectez à plusieurs comptes Microsoft, vous pouvez basculer entre les comptes comme décrit ci-après :

1.	Sélectionnez l’icône **Paramètres** (engrenage).

	![][1]

1. 	Une liste déroulante contenant tous les comptes Microsoft actuellement connectés est disponible en haut du volet gauche.

	![][3]
	 
1.	Sélectionnez la flèche vers le bas en regard de la liste déroulante pour afficher tous les comptes Microsoft connectés, ainsi qu’un lien pour ajouter (se connecter à) d’autres comptes Microsoft.

	![][4]

1.	Sélectionnez le compte Microsoft de votre choix.

1.	Sélectionnez les cases à cocher correspondant aux abonnements Azure que vous souhaitez parcourir.

	![][2]

1.	Sélectionnez **Appliquer**.
  
## Connexion au stockage local

L’explorateur de stockage (version préliminaire) vous permet de travailler sur le stockage local à l’aide de l’émulateur de stockage Azure. Vous pouvez ainsi écrire du code pour le stockage et le tester sans nécessairement disposer d’un compte de stockage déployé sur Azure (étant donné que le compte de stockage est émulé par l’émulateur de stockage Azure).

>[AZURE.NOTE] Actuellement, l’émulateur de stockage Azure est pris en charge uniquement pour Windows.

1. Démarrez l’explorateur de stockage (version préliminaire). 

1. Dans le volet gauche, développez le nœud **(Développement)**.

	![][21]

1. Si vous n’avez pas encore installé l’émulateur de stockage Azure, vous êtes invité à le faire par le biais d’une barre d’informations. Si la barre d’informations s’affiche, sélectionnez **Télécharger la dernière version** et installez l’émulateur.

	![][22]

1. Une fois que l’émulateur est installé, vous pouvez créer et utiliser des tables, des files d’attente et objets blob locaux. Pour apprendre à utiliser chaque type de compte de stockage, sélectionnez le lien approprié ci-dessous :

	- [Manage Azure blob storage resources](./vs-azure-tools-storage-explorer-blobs.md) (Gérer les ressources Azure Blob Storage)
	- Manage Azure queue storage resources (Gérer les ressources Azure Queue Storage) - *Bientôt disponible*
	- Manage Azure table storage resources (Gérer les ressources Azure Table Storage) - *Bientôt disponible*

## Attacher ou détacher un compte de stockage externe

L’explorateur de stockage (version préliminaire) permet l’attachement aux comptes de stockage externes, pour un partage simplifié des comptes de stockage. Cette section explique la procédure d’attachement à des comptes de stockage externes (et la procédure de détachement).

### Obtention des informations d’identification du compte de stockage

Afin de partager un compte de stockage externe, le propriétaire du compte doit d’abord obtenir les informations d’identification du compte (nom et clé du compte), puis les partager avec les personnes souhaitant effectuer un attachement à ce compte (externe). Vous pouvez obtenir les informations d’identification du compte de stockage par le biais du Portail Azure en suivant ces étapes :

1.	Connectez-vous au [portail Azure](https://portal.azure.com).
1.	Sélectionnez **Parcourir**.
1.	Sélectionnez **Comptes de stockage**.
1.	Dans le panneau **Comptes de stockage**, sélectionnez le compte de stockage souhaité.
1.	Dans le panneau **Paramètres** du compte de stockage sélectionné, sélectionnez **Clés d’accès**.

	![][5]
	
1.	Dans le panneau **Clés d’accès**, copiez les valeurs **NOM DU COMPTE DE STOCKAGE** et **CLÉ 1** à utiliser pour l’attachement au compte de stockage.

	![][6]

### Attachement à un compte de stockage externe

1.	Dans l’explorateur de stockage (version préliminaire), cliquez avec le bouton droit sur **Comptes de stockage** puis, dans le menu contextuel, sélectionnez **Attacher un stockage externe**.

	![][7]
	
1.	La section *Obtention des informations d’identification du compte de stockage* explique comment obtenir les valeurs de nom et de clé 1 du compte de stockage. Ces valeurs seront utilisées dans cette étape. Dans la boîte de dialogue **Attacher un stockage externe**, entrez le nom du compte de stockage dans la zone **Nom du compte** et la valeur de clé 1 dans la zone **Clé du compte**. Cliquez sur **OK** quand vous avez terminé.

	![][8]

	Une fois attaché, le compte de stockage externe s’affiche avec le texte **(Externe)** ajouté au nom.

	![][9]

### Détachement d’un compte de stockage externe

1. 	Cliquez avec le bouton droit sur le compte de stockage externe que vous souhaitez détacher puis, dans le menu contextuel, sélectionnez **Détacher**.

	![][10]

1.	Lorsque le message de confirmation s’affiche, sélectionnez **Oui** pour confirmer le détachement du compte de stockage externe.

	![][12]

## Attachement d’un compte à l’aide d’une SAP

Une SAP (signature d’accès partagé) permet à l’administrateur d’un abonnement Azure d’accorder un accès temporaire à un compte de stockage sans avoir à fournir les informations d’identification de l’abonnement Azure.

Pour illustrer cela, supposons que l’utilisateur A est l’administrateur d’un abonnement Azure et qu’il souhaite autoriser l’utilisateur B à accéder à un compte de stockage pour une durée limitée, avec certaines autorisations :

1. L’utilisateur A génère une SAP (composée de la chaîne de connexion du compte de stockage) pour une période spécifique, avec les autorisations souhaitées.
1. L’utilisateur A partage la SAP avec la personne souhaitant accéder au compte de stockage, en l’occurrence l’utilisateur B.  
1. L’utilisateur B utilise l’explorateur de stockage (version préliminaire) pour effectuer l’attachement au compte appartenant à l’utilisateur A à l’aide de la SAP fournie. 

### Obtention d’une SAP pour le compte à partager

1.	Ouvrez l’explorateur de stockage (version préliminaire).
1.	Dans le volet gauche, cliquez avec le bouton droit sur le compte de stockage que vous souhaitez partager puis, dans le menu contextuel, sélectionnez **Get Shared Access Signature** (Obtenir une signature d’accès partagé).

	![][13]

1. Dans la boîte de dialogue **Shared Access Signature** (Signature d’accès partagé), spécifiez la période et les autorisations souhaitées pour le compte, puis sélectionnez **Créer**.

	![][14]
 
1. Une seconde boîte de dialogue **Signature d’accès partagé** affiche la SAP. Sélectionnez **Copier** en regard de la **Chaîne de connexion** pour la copier dans le Presse-papiers. Sélectionnez **Fermer** pour fermer la boîte de dialogue.

### Attachement au compte partagé à l’aide de la SAP

1.	Ouvrez l’explorateur de stockage (version préliminaire).
1.	Dans le volet gauche, cliquez avec le bouton droit sur **Comptes de stockage** puis, dans le menu contextuel, sélectionnez **Attach account using SAS** (Attacher le compte à l’aide d’une SAP). ![][15]

1. Dans la boîte de dialogue **Attach Account using SAS** (Attacher le compte à l’aide d’une SAP) :

	- **Nom du compte** : entrez le nom que vous souhaitez associer à ce compte. **REMARQUE :** le nom du compte ne doit pas nécessairement correspondre au nom du compte de stockage d’origine pour lequel la SAP a été générée. 
 	- **Chaîne de connexion** : collez la chaîne de connexion que vous avez copiée précédemment.
 	- Cliquez sur **OK** quand vous avez terminé.
	
	![][16]

Une fois attaché, le compte de stockage s’affiche avec le texte (SAP) ajouté au nom fourni.

![][17]

## Attachement d’un service à l’aide d’une SAP

La section [Attachement d’un compte à l’aide d’une SAP](#attach-account-using-sas) illustre la façon dont l’administrateur d’un abonnement Azure peut accorder un accès temporaire à un compte de stockage en générant (et en partageant) une SAP pour le compte de stockage. De la même façon, une SAP peut être générée pour un service spécifique (conteneur d’objets blob, file d’attente ou table) dans un compte de stockage.

### Génération d’une SAP pour le service à partager

Dans ce contexte, un service peut être un conteneur d’objets blob, une file d’attente ou une table. Les sections suivantes expliquent comment générer la SAP pour le service répertorié :

- [Obtenir la signature d’accès partagé pour un conteneur d’objets blob](./vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
- Get the SAS for a queue (Obtenir la signature d’accès partagé pour une file d’attente) - *Bientôt disponible*
- Get the SAS for a table (Obtenir la signature d’accès partagé pour une table) - *Bientôt disponible*

### Attachement au service de compte partagé à l’aide de la SAP

1.	Ouvrez l’explorateur de stockage (version préliminaire).
1.	Dans le volet gauche, cliquez avec le bouton droit sur **Comptes de stockage** puis, dans le menu contextuel, sélectionnez **Attach service using SAS** (Attacher le service à l’aide d’une SAP). ![][18]

1. Dans la boîte de dialogue **Attach Account using SAS** (Attacher le compte à l’aide d’une SAP), collez l’URI de la SAP que vous avez copié précédemment, puis sélectionnez **OK**.

	![][19]

Une fois attaché, le service nouvellement attaché s’affiche sous le nœud **(Service SAS)** (SAP de service).

![][20]

## Recherche de comptes de stockage

Si vous avez une longue liste de comptes de stockage, la zone de recherche située en haut du volet gauche offre un moyen rapide de rechercher un compte spécifique.

Lorsque vous tapez dans la zone de recherche, le volet gauche affiche uniquement les comptes de stockage qui correspondent à la valeur en cours de saisie. La capture d’écran suivante montre un exemple dans lequel je recherche tous les comptes de stockage dont le nom contient le texte « tarcher ».

![][11]
	
Pour effacer la recherche, sélectionnez le bouton **x** dans la zone de recherche.

## Étapes suivantes
- [Gérer les ressources Azure Blob Storage avec l’explorateur de stockage (version préliminaire)](./vs-azure-tools-storage-explorer-blobs.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-gear.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/filter-subscriptions.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/filter-accounts.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/accounts-drop-down.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-context-menu.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-dlg.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-context-menu.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-dlg.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png

<!---HONumber=AcomDC_0608_2016-->
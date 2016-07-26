<properties
	pageTitle="Prise en main de l’explorateur de stockage (version préliminaire) | Microsoft Azure"
	description="Gérer les ressources de stockage Azure avec l’Explorateur de stockage (version préliminaire)"
	services="visual-studio-online"
	documentationCenter="na"
	authors="TomArcher"
	manager="douge"
	editor="" />

 <tags
	ms.service="storage"
	ms.devlang="multiple"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="07/14/2016"
	ms.author="tarcher" />

# Prise en main de l’explorateur de stockage (version préliminaire)

## Vue d'ensemble 

L’explorateur de stockage Microsoft Azure (version préliminaire) est une application autonome qui vous permet d’utiliser facilement les données Azure Storage sur Windows, OSX et Linux. Dans cet article, vous découvrirez les différentes façons de vous connecter à vos comptes de stockage Azure et de les gérer.

## Configuration requise

- [Télécharger et installer l’explorateur de stockage (version préliminaire)](http://www.storageexplorer.com)

## Connexion à un service ou un compte de stockage

L’explorateur de stockage (version préliminaire) offre de nombreuses façons de se connecter à des comptes de stockage. Il vous permet notamment de vous connecter aux comptes de stockage associés à vos abonnements Azure, de vous connecter à des services et comptes de stockage partagés à partir d’autres abonnements Azure et même de vous connecter au stockage local et de le gérer à l’aide de l’émulateur de stockage Azure :

- [Connexion à un abonnement Azure](#connect-to-an-azure-subscription) : gérez les ressources de stockage appartenant à votre abonnement Azure.
- [Utilisation du stockage de développement local](#work-with-local-development-storage) : gérez le stockage local à l’aide de l’émulateur de stockage Azure.
- [Attachement à un stockage externe](#attach-or-detach-an-external-storage-account) : gérez les ressources de stockage appartenant à un autre abonnement Azure à l’aide du nom et de la clé du compte de stockage.
- [Attachement d’un compte de stockage à l’aide d’une SAP](#attach-storage-account-using-sas) : gérez les ressources de stockage appartenant à un autre abonnement Azure à l’aide d’une SAP.
- [Attachement d’un service à l’aide d’une SAP](#attach-service-using-sas) : gérez un service de stockage spécifique (conteneur d’objets blob, file d’attente ou table) appartenant à un autre abonnement Azure à l’aide d’une SAP.

## Connexion à un abonnement Azure

> [AZURE.NOTE] Si vous ne possédez pas de compte Azure, vous pouvez [vous inscrire à un essai gratuit](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [activer les avantages de votre abonnement Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

1. Dans l’Explorateur de stockage (version préliminaire), sélectionnez les **paramètres de compte Azure**.

	![][0]

1. Le volet gauche affiche maintenant tous les comptes Microsoft auxquels vous vous êtes connecté. Pour vous connecter à un autre compte, sélectionnez **Ajouter un compte** et suivez les boîtes de dialogue pour vous connecter avec un compte Microsoft associé à un ou plusieurs abonnements Azure actifs.

	![][1]

1. Une fois que vous êtes connecté avec un compte Microsoft, le volet gauche indique les abonnements Azure associés à ce compte. Sélectionnez les abonnements Azure que vous souhaitez utiliser, puis sélectionnez **Appliquer**. (La case à cocher **Tous les abonnements** permet de sélectionner ou de désélectionner l’ensemble des abonnements Azure répertoriés.)

	![][3]

1. Le volet gauche affiche désormais tous les abonnements de compte associés aux abonnements Azure sélectionnés.

	![][4]

## Utilisation du stockage de développement local

L’explorateur de stockage (version préliminaire) vous permet de travailler sur le stockage local à l’aide de l’émulateur de stockage Azure. Vous pouvez ainsi écrire du code pour le stockage et le tester sans nécessairement disposer d’un compte de stockage déployé sur Azure (étant donné que le compte de stockage est émulé par l’émulateur de stockage Azure).

>[AZURE.NOTE] Actuellement, l’émulateur de stockage Azure est pris en charge uniquement pour Windows.

1. Dans le volet gauche de l’Explorateur de stockage (version préliminaire), développez le nœud **(Local et attaché** > **Comptes de stockage** > **(Développement)**.

	![][21]

1. Si vous n’avez pas encore installé l’émulateur de stockage Azure, vous êtes invité à le faire par le biais d’une barre d’informations. Si la barre d’informations s’affiche, sélectionnez **Télécharger la dernière version** et installez l’émulateur.

	![][22]

1. Une fois que l’émulateur est installé, vous pouvez créer et utiliser des tables, des files d’attente et objets blob locaux. Pour apprendre à utiliser chaque type de compte de stockage, sélectionnez le lien approprié ci-dessous :

	- [Manage Azure blob storage resources (Gérer les ressources Azure Blob Storage)](./vs-azure-tools-storage-explorer-blobs.md)
	- Manage Azure file share storage resources (Gérer les ressources de stockage de partage de fichiers Azure) - *Bientôt disponible*
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
Pour attacher à un compte de stockage externe, vous aurez besoin du nom et de la clé du compte. La section *Obtention des informations d’identification du compte de stockage* explique comment obtenir ces valeurs à partir du portail Azure. Toutefois, notez que dans le portail, la clé du compte est appelée « clé 1», donc lorsque l’Explorateur de stockage (version préliminaire) demande une clé de compte, vous devrez entrer (ou coller) la valeur « clé 1 ».
 
1.	Dans l’Explorateur de stockage (version préliminaire), sélectionnez **Se connecter à Azure Storage**.

	![][23]

1.	Dans la boîte de dialogue **Se connecter à Azure Storage**, spécifiez la clé de compte (valeur « clé 1 » du portail Azure), puis sélectionnez **Suivant**.

	![][24]

1.	Dans la boîte de dialogue **Attacher un stockage externe**, entrez le nom du compte de stockage dans la zone **Nom du compte**, spécifiez tout autre paramètre souhaité, puis sélectionnez **Suivant** lorsque vous avez terminé.

	![][8]

1.	Vérifiez les informations de la boîte de dialogue **Résumé de la connexion**. Si vous souhaitez modifier quoi que ce soit, sélectionnez **Précédent** et saisissez de nouveau les paramètres souhaités. Une fois que vous avez terminé, sélectionnez **Se connecter**.

1.	Une fois connecté, le compte de stockage externe s’affiche avec le texte **(Externe)** ajouté au nom.

	![][9]

### Détachement d’un compte de stockage externe

1. 	Cliquez avec le bouton droit sur le compte de stockage externe que vous souhaitez détacher puis, dans le menu contextuel, sélectionnez **Détacher**.

	![][10]

1.	Lorsque le message de confirmation s’affiche, sélectionnez **Oui** pour confirmer le détachement du compte de stockage externe.

## Attachement d’un compte de stockage à l’aide d’une SAP

Une [SAP (signature d’accès partagé)](storage/storage-dotnet-shared-access-signature-part-1.md) permet à l’administrateur d’un abonnement Azure d’accorder un accès temporaire à un compte de stockage sans avoir à fournir les informations d’identification de l’abonnement Azure.

Pour illustrer cela, supposons que l’utilisateur A est l’administrateur d’un abonnement Azure et qu’il souhaite autoriser l’utilisateur B à accéder à un compte de stockage pour une durée limitée, avec certaines autorisations :

1. L’utilisateur A génère une SAP (composée de la chaîne de connexion du compte de stockage) pour une période spécifique, avec les autorisations souhaitées.
1. L’utilisateur A partage la SAP avec la personne souhaitant accéder au compte de stockage, en l’occurrence l’utilisateur B.
1. L’utilisateur B utilise l’explorateur de stockage (version préliminaire) pour effectuer l’attachement au compte appartenant à l’utilisateur A à l’aide de la SAP fournie.

### Obtention d’une SAP pour le compte à partager

1.	Dans l’Explorateur de stockage (version préliminaire), cliquez avec le bouton droit sur le compte de stockage que vous souhaitez partager puis, dans le menu contextuel, sélectionnez **Obtenir une signature d’accès partagé**.

	![][13]

1. Dans la boîte de dialogue **Signature d’accès partagé**, spécifiez la période et les autorisations souhaitées pour le compte, puis sélectionnez **Créer**.

	![][14]
 
1. Une seconde boîte de dialogue **Signature d’accès partagé** affiche la SAP. Sélectionnez **Copier** en regard de la **Chaîne de connexion** pour la copier dans le Presse-papiers. Sélectionnez **Fermer** pour fermer la boîte de dialogue.

### Attachement au compte partagé à l’aide de la SAP

1.	Dans l’Explorateur de stockage (version préliminaire), sélectionnez **Se connecter à Azure Storage**.

	![][23]

1.	Dans la boîte de dialogue **Se connecter à Azure Storage**, spécifiez la chaîne de connexion, puis sélectionnez **Suivant**.

	![][24]

1.	Vérifiez les informations de la boîte de dialogue **Résumé de la connexion**. Si vous souhaitez modifier quoi que ce soit, sélectionnez **Précédent** et saisissez de nouveau les paramètres souhaités. Une fois que vous avez terminé, sélectionnez **Se connecter**.

1.	Une fois attaché, le compte de stockage s’affiche avec le texte (SAP) ajouté au nom fourni.

	![][17]

## Attachement d’un service à l’aide d’une SAP

La section [Attachement d’un compte de stockage à l’aide d’une SAP](#attach-storage-account-using-sas) illustre la façon dont l’administrateur d’un abonnement Azure peut accorder un accès temporaire à un compte de stockage en générant (et en partageant) une SAP pour le compte de stockage. De la même façon, une SAP peut être générée pour un service spécifique (conteneur d’objets blob, file d’attente ou table) dans un compte de stockage.

### Génération d’une SAP pour le service à partager

Dans ce contexte, un service peut être un conteneur d’objets blob, une file d’attente ou une table. Les sections suivantes expliquent comment générer la SAP pour le service répertorié :

- [Obtenir la signature d’accès partagé pour un conteneur d’objets blob](./vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
- Get the SAS for a file share (Obtenir la signature d’accès partagé pour un partage de fichiers) - *Bientôt disponible*
- Get the SAS for a queue (Obtenir la signature d’accès partagé pour une file d’attente) - *Bientôt disponible*
- Get the SAS for a table (Obtenir la signature d’accès partagé pour une table) - *Bientôt disponible*

### Attachement au service de compte partagé à l’aide de la SAP

1.	Dans l’Explorateur de stockage (version préliminaire), sélectionnez **Se connecter à Azure Storage**.

	![][23]

1.	Dans la boîte de dialogue **Se connecter à Azure Storage**, spécifiez l’URI de signature d’accès partagé, puis sélectionnez **Suivant**.

	![][24]

1.	Vérifiez les informations de la boîte de dialogue **Résumé de la connexion**. Si vous souhaitez modifier quoi que ce soit, sélectionnez **Précédent** et saisissez de nouveau les paramètres souhaités. Une fois que vous avez terminé, sélectionnez **Se connecter**.

1.	Une fois attaché, le service nouvellement attaché s’affiche sous le nœud **(Service SAS)** (SAP de service).

	![][20]

## Recherche de comptes de stockage

Si vous avez une longue liste de comptes de stockage, la zone de recherche située en haut du volet gauche offre un moyen rapide de rechercher un compte spécifique.

Lorsque vous tapez dans la zone de recherche, le volet gauche affiche uniquement les comptes de stockage qui correspondent à la valeur en cours de saisie. La capture d’écran suivante montre un exemple dans lequel je recherche tous les comptes de stockage dont le nom contient le texte « tarcher ».

![][11]
	
Pour effacer la recherche, sélectionnez le bouton **x** dans la zone de recherche.

## Étapes suivantes
- [Gérer les ressources Azure Blob Storage avec l’explorateur de stockage (version préliminaire)](./vs-azure-tools-storage-explorer-blobs.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-icon.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-account-link.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/subscriptions-list.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-accounts-list.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-icon.png
[24]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-next.png

<!---HONumber=AcomDC_0720_2016-->
---
title: Liste des comptes Azure Storage
description: "Gérez les paramètres de votre compte de stockage à l&quot;aide du kit de ressources Azure pour Eclipse"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: bbacfcd8-dbf5-4265-a930-59f508de5325
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 11/01/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 91490e33ab2d524da90ae75bd71abe8005e45c54


---
# <a name="azure-storage-account-list"></a>Liste des comptes Azure Storage
Les comptes de stockage Azure permettent l'utilisation d'emplacements de téléchargement pour votre JDK, serveur d'applications et composants arbitraires, ainsi que pour stocker l'état lors de l'utilisation de la mise en cache. Eclipse conserve une liste des comptes de stockage connus qui sont disponibles pour vos projets dans votre espace de travail Eclipse. Pour ouvrir la boîte de dialogue **Comptes de stockage**, qui permet de gérer cette liste, dans Eclipse, cliquez sur **Fenêtre**, sur **Préférences**, développez **Azure**, puis cliquez sur **Comptes de stockage**.

La boîte de dialogue **Comptes de stockage** est illustrée ci-dessous.

![][ic719496]

Vous pouvez également ouvrir cette boîte de dialogue à partir du lien **Comptes** dans les boîtes de dialogue qui utilisent des comptes de stockage, comme ci-dessous :

* Onglet **JDK** de la boîte de dialogue **Configuration du serveur**.
* Onglet **Serveur** de la boîte de dialogue **Configuration du serveur**.
* Boîte de dialogue **Ajouter un composant** .
* Boîte de dialogue des propriétés de **mise en cache** .

## <a name="to-import-your-storage-accounts-using-a-publish-settings-file"></a>Pour importer vos comptes de stockage à l'aide d'un fichier de paramètres de publication
1. Dans la boîte de dialogue **Comptes de stockage**, cliquez sur **Importer à partir du fichier PUBLISH-SETTINGS**.
2. (Ignorez cette étape si vous avez déjà enregistré un fichier de paramètres de publication sur votre ordinateur local). Dans la boîte de dialogue **Importer les informations d’abonnement**, cliquez sur **Télécharger le fichier PUBLISH-SETTINGS**. Si vous n’êtes pas encore connecté à votre compte Azure, vous y êtes maintenant invité. Vous serez ensuite invité à enregistrer un fichier de paramètres de publication Azure. (Vous pouvez ignorer les instructions affichées sur les pages d'ouverture de session ; elles sont fournies par le portail Azure et sont destinées aux utilisateurs de Visual Studio.) Enregistrez-le sur votre ordinateur local.
3. Toujours dans la boîte de dialogue **Importer les informations d’abonnement**, cliquez sur le bouton **Parcourir**, sélectionnez le fichier de paramètres que vous avez enregistré localement, puis cliquez sur **Ouvrir**.
4. Cliquez sur **OK** pour fermer la boîte de dialogue **Importer les informations d’abonnement**.

## <a name="to-create-a-new-storage-account"></a>Pour créer un compte de stockage
1. Dans la boîte de dialogue **Comptes de stockage**, cliquez sur **Ajouter**.
2. Dans la boîte de dialogue **Ajouter un compte de stockage**, cliquez sur **Nouveau**.
3. Dans la boîte de dialogue **Nouveau compte de stockage** , entrez des valeurs pour les éléments suivants :
   * Nom du compte de stockage.
   * Emplacement du compte de stockage.
   * Description du compte de stockage.
   * Abonnement auquel appartient le compte de stockage.
4. Cliquez sur **OK** pour fermer la boîte de dialogue **Nouveau compte de stockage**.

La création de votre compte de stockage peut prendre plusieurs minutes. Après sa création, cliquez sur **OK** pour fermer la boîte de dialogue **Ajouter un compte de stockage** : votre compte de stockage sera ajouté à la liste des comptes de stockage disponibles.

## <a name="to-add-an-existing-storage-account-to-the-list"></a>Pour ajouter un compte de stockage existant à la liste
1. Si vous ne disposez pas déjà d'un compte de stockage Azure, créez-en un en suivant les étapes indiquées dans la section **Pour créer un compte de stockage** ci-dessus. (Vous pouvez aussi créer un compte de stockage dans le [portail de gestion Azure][portail de gestion Azure].)
2. Dans la boîte de dialogue **Comptes de stockage**, cliquez sur **Ajouter**.
3. Dans la boîte de dialogue **Ajouter un compte de stockage**, entrez des valeurs pour le **nom** et la **clé d’accès**. La clé d'accès et le nom de compte doivent être ceux d'un compte de stockage Azure existant. Utilisez la section **Stockage** du [portail de gestion Azure][portail de gestion Azure] pour afficher les noms et les clés de votre compte de stockage. La boîte de dialogue **Ajouter un compte de stockage** doit avoir l'aspect suivant.
   
    ![][ic719497]
4. Cliquez sur **OK** pour fermer la boîte de dialogue **Ajouter un compte de stockage**.

## <a name="to-modify-a-storage-account-to-use-a-new-access-key"></a>Pour modifier un compte de stockage pour utiliser une nouvelle clé d'accès
1. Dans la boîte de dialogue **Comptes de stockage**, cliquez sur le compte de stockage que vous souhaitez modifier, puis cliquez sur **Modifier**.
2. Dans la boîte de dialogue **Modifier la clé d’accès du compte de stockage**, modifiez la valeur **Clé d’accès**.
3. Cliquez sur **OK** pour fermer la boîte de dialogue **Modifier la clé d’accès du compte de stockage**.

## <a name="to-remove-a-storage-account-from-the-list-maintained-in-eclipse"></a>Pour supprimer un compte de stockage dans la liste d'Eclipse
1. Dans la boîte de dialogue **Comptes de stockage**, cliquez sur le compte de stockage que vous souhaitez modifier, puis cliquez sur **Supprimer**.
2. Cliquez sur **OK** lorsque vous êtes invité à supprimer le compte de stockage.

> [!NOTE]
> La suppression du compte de stockage via la boîte de dialogue **Comptes de stockage** supprime uniquement celui-ci de la liste des comptes de stockage dans Eclipse. Elle ne supprime pas le compte de stockage de votre abonnement Azure. En outre, le compte de stockage peut réapparaître dans votre liste si Eclipse recharge les détails de votre abonnement.
> 
> 

## <a name="see-also"></a>Voir aussi
[Kit de ressources Azure pour Eclipse][Kit de ressources Azure pour Eclipse]

[Installation du kit de ressources Azure pour Eclipse][Installation du kit de ressources Azure pour Eclipse] 

[Création d'une application Hello World pour Azure dans Eclipse][Création d'une application Hello World pour Azure dans Eclipse]

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre de développement Java pour Azure][Centre de développement Java pour Azure].

<!-- URL List -->

[Centre de développement Java pour Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Kit de ressources Azure pour Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[portail de gestion Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Création d'une application Hello World pour Azure dans Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installation du kit de ressources Azure pour Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Nouveautés du kit de ressources Azure pour Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic719496]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719496.png
[ic719497]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719497.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn205108.aspx -->



<!--HONumber=Nov16_HO3-->



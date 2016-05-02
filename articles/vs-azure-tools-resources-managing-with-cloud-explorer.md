<properties 
   pageTitle="Gestion des ressources Azure avec Cloud Explorer | Microsoft Azure"
   description="Découvrez comment utiliser Cloud Explorer pour rechercher et gérer des ressources Azure dans Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="04/18/2016"
   ms.author="tarcher" />

# Gestion des ressources Azure avec Cloud Explorer

##Vue d’ensemble

Cloud Explorer est conçu pour vous permettre de parcourir et de gérer vos ressources Azure dans l'IDE Visual Studio plus facilement et plus rapidement. Vous pouvez par exemple l’utiliser pour ouvrir une application web dans le portail Azure ou dans un navigateur, lui attacher un débogueur ou afficher les propriétés d'un conteneur d'objets blob et l’ouvrir dans l'éditeur de conteneur d'objets Blob.

Cloud Explorer repose sur la pile Azure Resource Manager, tout comme le portail Microsoft Azure en version préliminaire. Il comprend des ressources, notamment les groupes de ressources Azure, et des services Azure, notamment Logic Apps et API Apps, et il prend en charge le [contrôle d'accès en fonction du rôle](../role-based-access-control-configure/) (RBAC). Pour voir les ressources Azure ajoutées ou modifiées, sélectionnez le bouton **Actualiser** dans la barre d'outils Cloud Explorer.

Cloud Explorer est installé au sein de Visual Studio Tools pour le kit de développement logiciel (SDK) Azure 2.7.

## Composants requis

- Visual Studio 2015 RTM.

- Visual Studio Tools pour le kit de développement logiciel (SDK) Azure.
- Vous devez disposer également d’un compte Azure et être connecté pour afficher les ressources Azure dans Cloud Explorer. Si vous n'en possédez pas, vous pouvez créer un compte en quelques minutes. Si vous avez un abonnement MSDN, consultez la page [Avantage Azure pour les abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Dans le cas contraire, consultez [créer un compte d'évaluation gratuit](https://azure.microsoft.com/pricing/free-trial/).

- Si Cloud Explorer n'apparaît pas, vous pouvez l'afficher en sélectionnant **Affichage**, **Autres fenêtres,** **Cloud Explorer** dans la barre de menus.

## Gérer les abonnements et les comptes Azure

Pour afficher vos ressources Azure dans Cloud Explorer, vous devez vous connecter à un compte Azure avec un ou plusieurs abonnements actifs. Si vous avez plus d'un compte Azure, vous pouvez les ajouter à Cloud Explorer, puis sélectionner les abonnements que vous souhaitez inclure dans l'affichage des ressources de Cloud Explorer.

Si vous n'avez jamais utilisé Azure ou que vous n'avez pas ajouté les comptes nécessaires à Visual Studio, vous serez invité à le faire.

## Pour ajouter des comptes Azure à Cloud Explorer

1. Cliquez sur l'icône Paramètres dans la barre d'outils Cloud Explorer.

1. Sélectionnez le lien **Ajouter un compte**. Connectez-vous au compte Azure dont vous souhaitez parcourir les ressources. Le compte que vous venez d'ajouter doit être sélectionné dans la liste déroulante du sélecteur de compte. Les abonnements de ce compte apparaissent sous l'entrée du compte.

    ![Ajout d’abonnements Azure](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819514.png)

    ![Choix d’abonnements Azure](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819515.png)

1. Cochez les cases des abonnements de compte que vous souhaitez parcourir, puis sélectionnez le bouton **Appliquer**.

    Les ressources Azure des abonnements sélectionnés apparaissent dans Cloud Explorer.

## Pour supprimer un compte Azure

1. Sélectionnez **Fichier**, **Paramètres du compte** dans la barre de menus.

1. Dans la section **Tous les comptes** de la boîte de dialogue **Paramètres du compte**, sélectionnez la commande **Supprimer** en regard du compte que vous souhaitez supprimer. Cette commande supprime uniquement le compte de Visual Studio, elle n'affecte pas le compte Azure lui-même.

## Afficher les types ou les groupes de ressources

Pour afficher vos ressources Azure, vous pouvez choisir l’affichage **Types de ressources** ou **Groupes de ressources**.

![Liste déroulante d’affichage des ressources](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819516.png)

- L’affichage **Types de ressources**, qui est également l’affichage courant utilisé sur le portail Azure, affiche vos ressources Azure classées par type, par exemple applications web, comptes de stockage et machines virtuelles. Cette opération est semblable à l’affichage des ressources Azure dans l'Explorateur de serveurs.

- L’affichage Groupes de ressources organise les ressources Azure en fonction des groupes de ressources Azure auxquels elles sont associées.

 
	Un groupe de ressources est un regroupement de ressources Azure, généralement utilisé par une application spécifique. Pour en savoir plus sur les groupes de ressources Azure, consultez [Présentation d’Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-overview/).

## Afficher et parcourir des ressources

Pour accéder à une ressource Azure et afficher ses informations dans Cloud Explorer, développez le type de l'élément ou le groupe de ressources associé, puis sélectionnez la ressource. Lorsque vous sélectionnez une ressource, les informations apparaissent dans les deux onglets en bas de Cloud Explorer.

![Choisir un affichage des ressources](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819517.png)

- L’onglet **Actions** affiche les actions possibles dans Cloud Explorer pour la ressource sélectionnée. Vous pouvez également voir les actions disponibles dans le menu contextuel de la ressource.

- L’onglet **Propriétés** affiche les propriétés de la ressource, notamment son type, ses paramètres régionaux et le groupe de ressources auquel elle est associée.

Chaque ressource dispose de l'action **Ouvrir dans le portail**. Lorsque vous sélectionnez cette action, Cloud Explorer affiche la ressource sélectionnée dans le portail Azure. Cette fonctionnalité est particulièrement pratique pour parcourir des ressources profondément imbriquées.

Des actions et des valeurs de propriétés supplémentaires peuvent également s’afficher en fonction de la ressource Azure. Par exemple, les applications web et les applications logiques ont également les actions **Ouvrir dans un navigateur** et **Attacher le débogueur** en plus de l’action **Ouvrir dans le portail**. Les actions d’ouverture des éditeurs apparaissent lorsque vous sélectionnez un objet Blob, File d'attente ou Table de compte de stockage. Les applications Azure ont des propriétés **URL** et **État**, tandis que les ressources de stockage ont des propriétés clé et chaîne de connexion.

## Rechercher des ressources

Pour trouver des ressources portant un nom spécifique dans les abonnements de votre compte Azure, entrez le nom dans la zone de recherche de Cloud Explorer.

![Trouver des ressources dans Cloud Explorer](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC820394.png)

Lorsque vous entrez des caractères dans la zone de recherche, seules les ressources qui correspondent à ces caractères apparaissent dans l'arborescence de ressources.

<!---HONumber=AcomDC_0420_2016-->
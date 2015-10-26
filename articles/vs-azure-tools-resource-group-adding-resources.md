<properties 
   pageTitle="Ajout de ressources à un groupe de ressources Azure"
   description="Apprenez à ajouter des ressources à un groupe de ressources Azure à l'aide de Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags 
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/13/2015"
   ms.author="kempb" />

# Ajout de ressources à un groupe de ressources Azure

Si vous avez besoin d'ajouter des ressources à un groupe de ressources, vous pouvez le faire dans la fenêtre Structure JSON de Visual Studio.

## Ajout de ressources à un groupe de ressources

### Pour ajouter des ressources à un groupe de ressources

1. Dans l'Explorateur de solutions, sélectionnez le fichier JSON pour le groupe de ressources Azure. Le fichier JSON s’affiche dans l'éditeur et la fenêtre **Structure JSON** s’affiche également en regard de l'éditeur. Si vous fermez la fenêtre Structure JSON, elle ne s’ouvrira à nouveau automatiquement qu’après que vous aurez sélectionné la commande Afficher la structure dans le menu contextuel d’un fichier de modèle JSON (pas un fichier de paramètres).

    ![Fichier JSON pour groupe de ressources Azure](./media/vs-azure-tools-resource-group-adding-resources/arm-json-file.png)

1. Dans la fenêtre **Structure JSON**, sélectionnez le nœud **Ressources**, puis la commande **Ajouter une nouvelle ressource** dans le menu contextuel, ou cliquez sur le bouton **Ajouter une ressource** en haut de la fenêtre Structure JSON.

    ![Ajout d'une ressource à un groupe de ressources](./media/vs-azure-tools-resource-group-adding-resources/arm-add-resource.png)

1. Dans la liste des ressources dans la boîte de dialogue **Ajouter une ressource**, sélectionnez la ressource que vous souhaitez ajouter, fournissez l’information requise par la ressource, puis cliquez sur le bouton **Ajouter**. Par exemple, si vous ajoutez un compte de stockage, vous devez fournir un nom de base pour les paramètres qui seront créés pour vous.
 
    ![Boîte de dialoguer Ajouter une ressource](./media/vs-azure-tools-resource-group-adding-resources/arm-add-resource-dialog.png)

    La ressource est ajoutée aux listes de ressources dans la fenêtre **Structure JSON** et le nouveau JSON représentant la ressource s’affiche dans le fichier. Des paramètres et/ou des variables liés peuvent également être ajoutés.


    ![Ressource ajoutée au fichier JSON](./media/vs-azure-tools-resource-group-adding-resources/arm-add-resource-json.png)

1. Déployer la nouvelle ressource dans le groupe de ressources Azure. Dans le menu contextuel du projet de groupe de ressources dans l'Explorateur de solutions, sélectionnez **déployer**, puis choisissez le nom du groupe de ressources.

    ![Groupe de ressources Azure déployé](./media/vs-azure-tools-resource-group-adding-resources/deploy-arm-resource-group.png)

    La boîte de dialogue **Déployer vers le groupe de ressources** s'affiche.


1. Cliquez sur le bouton **Déployer**.

1. Si vous devez spécifier des paramètres, la boîte de dialogue **Modifier les paramètres** s'affiche. Entrez les valeurs requises, puis cliquez sur le bouton **Enregistrer**. La nouvelle ressource est déployée dans votre groupe de ressources Azure.

## Voir aussi

[Création et déploiement de projets de déploiement de groupe de ressources Azure](http://go.microsoft.com/fwlink/p/?LinkID=623073)

[Cmdlets du gestionnaire de ressources Azure](https://msdn.microsoft.com/library/dn654592.aspx)

[Utilisation de Windows PowerShell avec le gestionnaire de ressources Azure](../powershell-azure-resource-manager/)

[Vidéo Channel 9 : gestionnaire de ressources Azure](http://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B224#fbid=)

<!---HONumber=Oct15_HO3-->
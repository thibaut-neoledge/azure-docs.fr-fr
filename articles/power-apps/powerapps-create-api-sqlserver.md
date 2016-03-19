<properties
	pageTitle="Ajout de l’API SQL Server à PowerApps Enterprise | Microsoft Azure"
	description="Création ou configuration d’une API SQL Server dans l’environnement App Service de votre organisation et ajout d’une connexion aux données locales"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="linhtranms"
	manager="dwrede"
	editor=""/>


<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>


# Créer une API SQL Server dans l’environnement App Service de votre organisation

## Créer l’API dans le portail Azure

1. Dans le [Portail Azure](https://portal.azure.com/), connectez-vous avec votre compte professionnel. Par exemple, connectez-vous à l’aide *VotreNomUtilisateur*@*VotreSociété*.com. Lorsque vous procédez ainsi, vous êtes automatiquement connecté à l’abonnement de votre société. 
2. Dans la barre des tâches, sélectionnez **Parcourir** : ![][14]  
3. Dans la liste, vous pouvez faire défiler pour sélectionner PowerApps ou taper *powerapps* : ![][15]  
4. Dans **PowerApps**, sélectionnez **Gérer les API**.
5. Dans **Gérer les API**, sélectionnez **Ajouter** pour ajouter la nouvelle API.
6. Entrez un **Nom** descriptif pour votre API. Par exemple, vous ajoutez l'API SQL Server pour la démonstration, vous pouvez la nommer *SQLServerDemo*.  	
7. Dans **Source**, sélectionnez **API disponibles** pour sélectionner les API prédéfinies, puis sélectionnez **SQL Server**. 
8. Sélectionnez **OK** pour achever la procédure.

Quand vous avez terminé, une nouvelle API SQL Server est ajoutée à votre environnement App Service.

## Configurer la connectivité vers SQL Server en local

Vous pouvez vous connecter à SQL Server en local. Pour établir cette connectivité hybride, vous pouvez tirer parti des solutions de mise en réseau hybrides existantes dans Azure, notamment :

- [ExpressRoute](../expressroute-introduction.md)
- [VPN de site à site](../vpn-gateway-create-site-to-site-rm-powershell.md)
- [Connectivité de point à site](../vpn-gateway-point-to-site-create.md)  

	> [AZURE.NOTE]Chaque environnement App Service est associé à un réseau virtuel. Vous pouvez établir cette connectivité réseau à ce réseau virtuel.  
- [Hybrid connections](../web-sites-hybrid-connection-get-started.md)  

	> [AZURE.NOTE]Toutes les API inscrites dans votre environnement App Service sont associées à une application web. Vous pouvez établir des connexions hybrides à partir de cette application web comme à partir de n'importe quelle autre application web.
	
L’exemple suivant montre comment créer une connexion hybride :

1. Sélectionnez l'API SQL Server que vous venez de créer puis le groupe de ressources. Dans cet exemple, sélectionnez l'API appelée *sqlconnectordemo*, puis sélectionnez le groupe de ressources *DedicatedAses* : ![Groupe de ressources](./media/powerapps-create-api-sqlserver/sqlapi.png)

2.  Sélectionnez la mosaïque **Ressources**, puis l'application web portant le même nom que votre API SQL Server. Dans cet exemple, sélectionnez *sqlconnectordemo* : ![Application web Sql](./media/powerapps-create-api-sqlserver/sqlwebapp.png)

3.  Dans **Paramètres**, sélectionnez **Mise en réseau**. Sélectionnez **Configurer les points de terminaison de connexion hybride**, puis suivez [ces instructions](../web-sites-hybrid-connection-get-started.md) pour créer la connexion hybride : ![Mise en réseau](./media/powerapps-create-api-sqlserver/network.png)

Une fois votre connexion hybride créée et connectée, vous avez activé la connexion à votre serveur local. Ensuite, créez la connexion à vos données et accordez l’accès aux utilisateurs accès : ![Connexion hybride](./media/powerapps-create-api-sqlserver/hybridconn.png)

## Création de la connexion pour l'API SQL Server

1. Dans le portail Azure, ouvrez PowerApps, puis sélectionnez **Gérer les API**. Une liste des API configurées s'affiche : ![](./media/powerapps-create-api-sqlserver/apilist.png)

2. Sélectionnez l’API souhaitée. Dans cet exemple, sélectionnez **SQLServerDemo**, puis **Connexions**.

3. Dans Connexions, sélectionnez **Ajouter une connexion** : ![](./media/powerapps-create-api-sqlserver/addconnection.png)

4. Nommez la connexion et saisissez la chaîne de connexion. Pour saisir la chaîne de connexion, vous devez connaître certaines propriétés spécifiques concernant le service auquel vous vous connectez. Par exemple, si vous vous connectez à un système SQL Server local, vous devez connaître le nom d'utilisateur, le mot de passe et d'autres propriétés requises pour établir la connexion.

5. Sélectionnez **Ajouter** pour enregistrer vos modifications.

## Résumé et étapes suivantes
Dans cette rubrique, vous avez ajouté l'API SQL Server pour vous connecter à SQL Server en local. Ensuite, donnez accès à l’API pour les utilisateur afin qu’ils puissent l’ajouter à leurs applications :

[Ajouter une connexion et attribution d’accès aux utilisateurs](powerapps-manage-api-connection-user-access.md)


[14]: ./media/powerapps-create-api-sqlserver/browseall.png
[15]: ./media/powerapps-create-api-sqlserver/allresources.png

<!---HONumber=AcomDC_1203_2015-->
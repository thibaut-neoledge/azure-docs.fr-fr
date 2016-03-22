<properties
	pageTitle="Ajout ou création d’une API et attribution d’autorisations aux utilisateurs dans PowerApps | Microsoft Azure"
	description="Ajouter, créer et configurer une API, une connexion ou un profil de connexion nouveaux et donner des autorisations et droits liés à l’accès de l'utilisateur au portail Azure"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/02/2016"
   ms.author="litran"/>


# Ajout d’une nouvelle API, ajout d’une connexion et attribution d’un accès aux utilisateurs

Des API existent dans un [Environnement App Service](powerapps-get-started-azure-portal.md). Des API peuvent être créées à partir des API disponibles pour PowerApps, d’applications API hébergées dans votre Environnement App Service, de Swagger 2.0. Il existe de nombreuses API prédéfinies qui peuvent facilement être ajoutées à votre PowerApps. Vous pouvez également télécharger vos propres API au format JSON ou Swagger 2.0.

Cette rubrique :

- Répertorie les étapes d'ajout d'une API PowerApps et autorise les utilisateurs de votre société à utiliser l'API, notamment en modifiant ses propriétés.
- Répertorie les étapes pour ajouter une connexion à votre API et autorise les utilisateurs de votre société à utiliser la connexion.


#### Conditions préalables

- Activez [PowerApps dans votre abonnement Azure](powerapps-get-started-azure-portal.md).
- Créez un [Environnement App Service](powerapps-get-started-azure-portal.md).
- Créez une API en utilisant l'une des méthodes suivantes :  
	- Créez une [API gérée par Microsoft ou gérée par le service informatique](powerapps-register-from-available-apis.md).
	- Créez une API hébergée dans votre [Environnement App Service](powerapps-register-api-hosted-in-app-service.md).
	- Créez à l'aide d’une [Définition d'API Swagger 2.0](powerapps-register-existing-api-from-api-definition.md).


## Permettez aux utilisateurs d’accéder à l’API
Maintenant que l'API est créée et ajoutée à votre environnement App Service, il est temps d’autoriser les utilisateurs de votre entreprise à l'utiliser.

1. Dans PowerApps, sélectionnez **Gérer les API**, puis sélectionnez votre API. Par exemple, si vous avez créé une API *MS Power BI*, sélectionnez-la pour ouvrir son panneau. Sélectionnez **Accès utilisateur à l’API** : ![][1]  

2. Sélectionnez **Ajouter** pour ajouter des utilisateurs, puis sélectionnez les droits. Lorsque vous avez terminé, sélectionnez **Ajouter** pour enregistrer vos modifications. Le nombre d’utilisateurs ou de groupes augmente dans la fenêtre **Accès utilisateur à l’API**.


## Ajout d’une nouvelle connexion à votre API
L'étape suivante consiste à créer la « connexion » à votre API, une sorte de chaîne de connexion. Cela permet à l'API de se connecter à votre système « principal ». Pour la version préliminaire publique PowerApps Enterprise, seule la connexion SQL Server peut être ajoutée et configurée. D’autres seront ajoutées.

- [Création de la connexion SQL Server](powerapps-create-api-sqlserver.md)

## Attribution aux utilisateurs d’un accès au moment de l’exécution de la connexion
Autorisez maintenant les utilisateurs de votre société à utiliser la connexion.

1. Ouvrez votre API, sélectionnez **Connexions**, puis sélectionnez votre connexion spécifique. Cette opération ouvre un nouveau panneau qui affiche le nom de votre connexion en haut. 
2. Dans ce nouveau panneau, sélectionnez **Accès utilisateur à la connexion**. Dans l'exemple suivant, la connexion **Tunnel hybride** est sélectionnée. Le nouveau panneau s'ouvre et c'est ici que vous sélectionnez **Accès utilisateur à la connexion** : ![][2]
  
3. Dans **Accès utilisateur à la connexion**, sélectionnez **Ajouter**, puis sélectionnez l'autorisation que vous souhaitez accorder : ![][3]
  
4. Ajoutez un utilisateur ou un groupe. Sélectionnez **Ajouter** pour enregistrer vos modifications.

Maintenant que les utilisateurs disposent des autorisations d’accès à l'API et à sa connexion, ils peuvent ajouter ces API à leurs applications créées dans PowerApps. Plus précisément :

- Les utilisateurs peuvent voir l’API répertoriée sous **Connexions disponibles** dans PowerApps.
- Les utilisateurs peuvent voir la connexion sous **Mes connexions** dans PowerApps.


## Suppression d’une API
Vous pouvez également supprimer une API ajoutée précédemment. Dans PowerApps, sélectionnez **Gérer les API**, puis **Supprimer**: ![][4]


## Résumé et étapes suivantes
Dans cette rubrique, vous avez :

- Ajouté une API et donné à des utilisateurs au sein de votre société le droit de l’utiliser. Vous pouvez également utiliser ces étapes pour gérer l'accès au moment de l'exécution. Par exemple, si un UtilisateurA quitte votre société, vous pouvez utiliser le portail Azure pour supprimer facilement les autorisations de cet utilisateur. Le scénario est le même si un UtilisateurB rejoint votre entreprise.
- Ajouté une connexion (similaire à une chaîne de connexion). Cette étape permet à l'API hébergée dans Azure de se connecter à votre système, par exemple un serveur SQL Server local. Vous avez également autorisé des utilisateurs de votre société à utiliser la connexion. 
- Vous avez travaillé avec différents panneaux, en fonction de la tâche. Pour ajouter une connexion, vous ouvrez l'API et utilisez son panneau. Pour accorder un accès à l’utilisateur, vous ouvrez la connexion ou l'API, selon l’élément auquel vous donnez accès. 
- Vous pouvez également supprimer toutes les API que vous créez dans votre environnement App Service.

Vous pouvez ensuite [gérer et surveiller votre PowerApps](powerapps-manage-monitor-usage.md).

[1]: ./media/powerapps-manage-api-connection-user-access/apiuseraccess.png
[2]: ./media/powerapps-manage-api-connection-user-access/connectionuseraccess.png
[3]: ./media/powerapps-manage-api-connection-user-access/selectpermission.png
[4]: ./media/powerapps-manage-api-connection-user-access/deleteapi.png

<!---------HONumber=AcomDC_0309_2016-->
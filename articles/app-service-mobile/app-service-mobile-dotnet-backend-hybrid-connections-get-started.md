<properties
	pageTitle="Connexion de votre application mobile Azure à un serveur SQL Server local au moyen de connexions hybrides | Microsoft Azure"
	description="Découvrez comment vous connecter à un serveur SQL Server local à partir d'une application mobile App Service au moyen de connexions hybrides"
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="get-started-article"
	ms.date="10/23/2015"
	ms.author="glenga"/>


# Connexion à un serveur SQL Server local à partir d'applications mobiles au moyen de connexions hybrides

Lorsque votre entreprise effectue la transition vers le cloud, il est possible que vous ne soyez pas en mesure de migrer toutes vos ressources Azure immédiatement. Les connexions hybrides permettent à la fonctionnalité Applications mobiles dans Azure App Service de se connecter en toute sécurité à vos ressources locales. De cette façon, vos données locales sont accessibles aux clients mobiles à l'aide d'Azure. Les ressources prises en charge incluent toute ressource s'exécutant sur un port TCP statique, y compris Microsoft SQL Server, MySQL, les API web HTTP et la plupart des services web personnalisés. Les connexions hybrides utilisent l'autorisation avec signature d'accès partagé (SAS) pour sécuriser les connexions de votre service mobile et du Gestionnaire de connexion hybride local à la connexion hybride. Pour plus d'informations, consultez [Aperçu des connexions hybrides](../integration-hybrid-connection-overview.md).

Au cours de ce didacticiel, vous apprendrez à modifier un serveur principal .NET d'application mobile afin d'utiliser une base de données SQL Server locale au lieu de la base de données SQL Azure par défaut configurée avec votre service.

[AZURE.INCLUDE [app-service-mobile-to-web-and-api](../../includes/app-service-mobile-to-web-and-api.md)]

## Composants requis ##

Ce didacticiel requiert les éléments suivants :

- **Un serveur principal d’application mobile existant** <br/>Suivez le [didacticiel de démarrage rapide](app-service-mobile-windows-store-dotnet-get-started.md) pour créer et télécharger un serveur principal .NET d’application mobile à partir du [portail Azure].

[AZURE.INCLUDE [hybrid-connections-prerequisites](../../includes/hybrid-connections-prerequisites.md)]

## Installation de SQL Server Express, activation de TCP/IP et création d'une base de données SQL Server en local

[AZURE.INCLUDE [hybrid-connections-create-on-premises-database](../../includes/hybrid-connections-create-on-premises-database.md)]

## Création d'une connexion hybride

Vous devez créer une connexion hybride et un service BizTalk pour la partie du code du serveur principal d'application mobile, qui est une application Web.

1. Dans le [portail Azure], accédez à l’application mobile et cliquez sur **Mise en réseau** dans les paramètres.

	![Accédez à l'application Web](./media/app-service-mobile-dotnet-backend-hybrid-connections-get-started/mobile-app-link-to-web-app-backend.png)

	

2. Cliquez sur **Configurer les points de terminaison de connexion hybride** dans la section **Connexions hybrides**.

	![Hybrid connections](./media/app-service-mobile-dotnet-backend-hybrid-connections-get-started/start-hybrid-connection.png)

2. Dans le panneau Connexions hybrides, cliquez sur **Ajouter** puis sur **Nouvelle connexion hybride**.

3. Dans le **panneau Créer une connexion hybride**, indiquez un **Nom** et un **Nom d'hôte** pour la connexion hybride et définissez le **Port** sur `1433`.

	![Create a hybrid connection](./media/app-service-mobile-dotnet-backend-hybrid-connections-get-started/create-hybrid-connection.png)

4. Cliquez sur **Service Biz Talk** et entrez un nom pour le service BizTalk et cliquez deux fois sur **OK**.

	Ce didacticiel utilise **mobile1**. Vous aurez besoin d’un nom unique pour votre nouveau service BizTalk.

	Une fois le processus terminé, la zone **Notifications** affiche la mention **SUCCÈS** en vert clignotant et le panneau **Connexion hybride** affiche la nouvelle connexion hybride dans l’état **Non connecté**.

	![One hybrid connection created](./media/app-service-mobile-dotnet-backend-hybrid-connections-get-started/hybrid-connection-created.png)

À ce stade, vous avez terminé une partie importante de l'infrastructure de connexion hybride cloud. Vous allez ensuite créer un élément local.

## Installation du Gestionnaire de connexions hybrides local pour finaliser la connexion

[AZURE.INCLUDE [app-service-hybrid-connections-manager-install](../../includes/app-service-hybrid-connections-manager-install.md)]

## Configuration du projet principal d'application mobile pour la connexion à la base de données SQL Server

Dans cette étape, vous définissez une chaîne de connexion pour la base de données locale et vous modifiez le serveur principal de l'application mobile pour utiliser cette connexion.

1. Dans Visual Studio 2013, ouvrez le projet qui définit votre application mobile principale.

	Pour apprendre à télécharger votre projet principal .NET, consultez le [didacticiel de démarrage rapide](app-service-mobile-windows-store-dotnet-get-started.md).

2. Dans l'Explorateur de solutions, ouvrez le fichier Web.config, recherchez la section **connectionStrings**, ajoutez une entrée SqlClient comme suit, pointant vers la base de données SQL Server locale :

	    <add name="OnPremisesDBConnection"
         connectionString="Data Source=OnPremisesServer,1433;
         Initial Catalog=OnPremisesDB;
         User ID=HybridConnectionLogin;
         Password=<**secure_password**>;
         MultipleActiveResultSets=True"
         providerName="System.Data.SqlClient" />

	N’oubliez pas de remplacer `<**secure_password**>` dans cette chaîne par le mot de passe que vous avez créé pour *HybridConnectionLogin*.

3. Cliquez sur **Enregistrer** dans Visual Studio pour enregistrer le fichier Web.config.

	> [AZURE.NOTE]Ce paramètre de connexion est utilisé lors de l'exécution sur l'ordinateur local. Lors de l'exécution dans Azure, ce paramètre est remplacé par le paramètre de connexion défini dans le portail.

4. Développez le dossier **Modèles** et ouvrez le fichier de modèle de données, se terminant par *Context.cs*.

6. Modifiez le constructeur d'instance **DbContext** pour transmettre la valeur `OnPremisesDBConnection` au constructeur **DbContext** de base, similaire à l'extrait de code suivant :

        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base("OnPremisesDBConnection")
            {
            }
        }

	Le service utilise désormais la nouvelle connexion à la base de données SQL Server.

## Mise à jour d'Azure pour utiliser la chaîne de connexion locale

À présent, vous devez ajouter un paramètre d'application pour cette nouvelle chaîne de connexion afin qu'elle puisse être utilisée à partir d'Azure.

1. Dans le [portail Azure] dans le code principal d’application web de votre application mobile, cliquez sur **Tous les paramètres**, puis sur **Paramètres de l’application**.

3. Dans le panneau **Paramètres d'application web**, faites défiler jusqu'à **Chaînes de connexion** et ajoutez une chaîne de connexion **SQL Server** nommée `OnPremisesDBConnection` avec une valeur telle que `Server=OnPremisesServer,1433;Database=OnPremisesDB;User ID=HybridConnectionsLogin;Password=<**secure_password**>`.

	Remplacez `<**secure_password**>` par le mot de passe sécurisé de votre base de données locale.

	![Chaîne de connexion pour la base de données locale](./media/app-service-mobile-dotnet-backend-hybrid-connections-get-started/set-sql-server-database-connection.png)

2. Appuyez sur **Enregistrer** pour enregistrer la connexion hybride et la chaîne de connexion que vous venez de créer.

## Publication et test du serveur principal d'application mobile dans Azure

Enfin, vous devez publier le serveur principal d'application mobile sur Azure et vérifier qu'il utilise la connexion hybride pour stocker les données dans la base de données locale.

3. Dans Visual Studio, cliquez sur le projet avec le bouton droit, cliquez sur **Publier**, puis dans **Publier le site Web** cliquez sur **Sites Web Microsoft Azure**.

	Au lieu d'utiliser Visual Studio, vous pouvez également [utiliser Git pour publier votre serveur principal](mobile-services-dotnet-backend-store-code-source-control.md).

2. Connectez-vous avec les informations d'identification Azure et sélectionnez votre service sous **Sélectionner les sites Web existants**.

	Visual Studio télécharge vos paramètres de publication depuis Azure.

3. Enfin, cliquez sur **Publier**.

	Une fois la publication terminée, le service redémarre et la page de démarrage principale s'affiche.

4. À l’aide d’une application cliente connectée à votre application mobile, appelez des opérations permettant de générer des modifications dans la base de données.

5. Dans SQL Server Management Studio, connectez-vous à votre instance SQL Server, ouvrez l'Explorateur d'objets, développez la base de données **OnPremisesDB** et développez les **Tables**.

6. Cliquez avec le bouton droit sur la table **hybridService1.TodoItems** et choisissez **Sélectionner les 1 000 premières lignes** pour afficher les résultats.

	Notez que les modifications générées dans votre application client sont enregistrées par le serveur principal de votre application mobile dans votre base de données locale à l'aide de la connexion hybride.

## Voir aussi ##

+ [Site web des connexions hybrides](../../services/biztalk-services/)
+ [Aperçu des connexions hybrides](../integration-hybrid-connection-overview.md)
+ [BizTalk Services : Onglets Tableau de bord, Surveiller, Mettre à l’échelle, Configurer et Connexion hybride](../biztalk-dashboard-monitor-scale-tabs.md)
+ [Modifications des modèles de données pour un service mobile principal .NET](../mobile-services-dotnet-backend-how-to-use-code-first-migrations.md)

<!-- IMAGES -->


<!-- Links -->
[portail Azure]: https://portal.azure.com/
[Azure classic portal]: http://go.microsoft.com/fwlink/p/?linkid=213885
[Get started with Mobile Services]: ../mobile-services-windows-store-dotnet-get-started.md

<!---HONumber=AcomDC_1203_2015-->
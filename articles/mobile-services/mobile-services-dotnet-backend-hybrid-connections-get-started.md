<properties
	pageTitle="Connexion à un serveur SQL Server local à partir d’un service mobile de backend .NET au moyen de connexions hybrides | Azure Mobile Services"
	description="Découvrez comment vous connecter à un serveur SQL Server local à partir d’un service mobile de backend .NET au moyen de connexions Azure hybrides."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="03/05/2016"
	ms.author="glenga"/>


# Connexion à une instance SQL Server locale à partir d’Azure Mobile Services au moyen de connexions hybrides

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Pour la version Mobile Apps équivalente de cette rubrique, consultez [Accéder à des ressources locales à l’aide de connexions hybrides dans Azure App Service](../app-service-web/web-sites-hybrid-connection-get-started.md).

Lorsque votre entreprise effectue la transition vers le cloud, il est possible que vous ne soyez pas en mesure de migrer toutes vos ressources Azure immédiatement. Les connexions hybrides permettent aux Azure Mobile Services de se connecter en toute sécurité à vos ressources locales. De cette façon, vos données locales sont accessibles aux clients mobiles à l'aide d'Azure. Les ressources prises en charge incluent toute ressource s'exécutant sur un port TCP statique, y compris Microsoft SQL Server, MySQL, les API web HTTP et la plupart des services web personnalisés. Les connexions hybrides utilisent l'autorisation avec signature d'accès partagé (SAS) pour sécuriser les connexions de votre service mobile et du Gestionnaire de connexion hybride local à la connexion hybride. Pour plus d'informations, consultez [Aperçu des connexions hybrides](../biztalk-services/integration-hybrid-connection-overview.md).

Au cours de ce didacticiel, vous allez apprendre à modifier un service mobile principal .NET afin d'utiliser une base de données SQL Server locale au lieu de la base de données SQL Azure par défaut approvisionnée avec votre service. Les connexions hybrides sont également prises en charge pour un service mobile principal JavaScript, comme le décrit [cet article](http://blogs.msdn.com/b/azuremobile/archive/2014/05/12/connecting-to-an-external-database-with-node-js-backend-in-azure-mobile-services.aspx).

##Conditions préalables##

Ce didacticiel requiert les éléments suivants :

- **Un service mobile principal .NET existant** <br/>Suivez le didacticiel [Prise en main de Mobile Services] pour créer et télécharger un nouveau service mobile principal .NET à partir du [portail Azure Classic].

[AZURE.INCLUDE [hybrid-connections-prerequisites](../../includes/hybrid-connections-prerequisites.md)]

## Installation de SQL Server Express, activation de TCP/IP et création d'une base de données SQL Server en local

[AZURE.INCLUDE [hybrid-connections-create-on-premises-database](../../includes/hybrid-connections-create-on-premises-database.md)]

## Création d'une connexion hybride

[AZURE.INCLUDE [hybrid-connections-create-new](../../includes/hybrid-connections-create-new.md)]

## Installation du Gestionnaire de connexions hybrides local pour terminer la connexion

[AZURE.INCLUDE [hybrid-connections-install-connection-manager](../../includes/hybrid-connections-install-connection-manager.md)]

## Configuration du projet de service mobile pour la connexion à la base de données SQL Server

Dans cette étape, vous définissez une chaîne de connexion pour la base de données locale et vous modifiez le serveur mobile pour utiliser cette connexion.

1. Dans Visual Studio 2013, ouvrez le projet qui définit votre service mobile basé principal .NET.

	Pour apprendre à télécharger votre projet de backend .NET, consultez la page [Prise en main de Mobile Services](mobile-services-dotnet-backend-windows-store-dotnet-get-started.md) .

2. Dans l'Explorateur de solutions, ouvrez le fichier Web.config, recherchez la section **connectionStrings**, ajoutez une entrée SqlClient comme suit, pointant vers la base de données SQL Server locale :

	    <add name="OnPremisesDBConnection"
         connectionString="Data Source=OnPremisesServer,1433;
         Initial Catalog=OnPremisesDB;
         User ID=HybridConnectionLogin;
         Password=<**secure_password**>;
         MultipleActiveResultSets=True"
         providerName="System.Data.SqlClient" />

	N'oubliez pas de remplacer `<**secure_password**>` dans cette chaîne par le mot de passe que vous avez créé pour *HbyridConnectionLogin*.

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

##Test de la connexion à la base de données localement

Avant la publication dans Azure et l'utilisation de la connexion hybride, il est judicieux de vous assurer que la connexion à la base de données fonctionne lorsqu'elle est exécutée localement. De cette façon, vous pouvez plus facilement diagnostiquer et corriger les problèmes de connexion avant de republier et de commencer à utiliser la connexion hybride.

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]

## Mise à jour d'Azure pour utiliser la chaîne de connexion locale

À présent que vous avez vérifié la connexion à la base de données, vous devez ajouter un paramètre d'application pour cette nouvelle chaîne de connexion afin qu'elle puisse être utilisée à partir d'Azure et publier le service mobile sur Azure.

1. Dans le [portail Azure Classic], accédez à votre service mobile.

1. Cliquez sur l'onglet **Configurer** et recherchez la section **Chaînes de connexion**.

	![Chaîne de connexion pour la base de données locale](./media/mobile-services-dotnet-backend-hybrid-connections-get-started/11.png)

2. Ajoutez une nouvelle chaîne de connexion **SQL Server** nommée `OnPremisesDBConnection` avec une valeur comme la suivante :

		Server=OnPremisesServer,1433;Database=OnPremisesDB;User ID=HybridConnectionsLogin;Password=<**secure_password**>


	Remplacez `<**secure_password**>` par le mot de passe sécurisé de *HybridConnectionLogin*.

2. Appuyez sur **Enregistrer** pour enregistrer la connexion hybride et la chaîne de connexion que vous venez de créer.

3. À l'aide de Visual Studio, publiez votre projet de service mobile mis à jour sur Azure.

	La page de démarrage du service s'affiche.

4. À l'aide du bouton **Essayez maintenant** sur la page de démarrage comme avant, ou à l'aide d'une application cliente connectée à votre service mobile, appelez des opérations qui génèrent des modifications dans la base de données.

	>[AZURE.NOTE]Lorsque vous utilisez le bouton **Essayez maintenant** pour ouvrir les pages API d'aide, n'oubliez pas de fournir votre clé d'application comme mot de passe (avec un nom d'utilisateur vide).

4. Dans SQL Server Management Studio, connectez-vous à votre instance SQL Server, ouvrez l'Explorateur d'objets, développez la base de données **OnPremisesDB** et développez les **Tables**.

5. Cliquez avec le bouton droit sur la table **hybridService1.TodoItems** et choisissez **Sélectionner les 1 000 premières lignes** pour afficher les résultats.

	Notez que les modifications générées dans votre application ont été enregistrées par le service mobile dans votre base de données locale à l'aide de la connexion hybride.

##Voir aussi##

+ [Site web des connexions hybrides](https://azure.microsoft.com/services/biztalk-services/)
+ [Aperçu des connexions hybrides](../biztalk-services/integration-hybrid-connection-overview.md)
+ [BizTalk Services : Onglets Tableau de bord, Surveiller, Mettre à l’échelle, Configurer et Connexion hybride](../biztalk-services/biztalk-dashboard-monitor-scale-tabs.md)
+ [Modifications des modèles de données pour un service mobile principal .NET](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md)

<!-- IMAGES -->


<!-- Links -->
[portail Azure Classic]: http://manage.windowsazure.com
[Prise en main de Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md

<!---HONumber=AcomDC_0504_2016-->
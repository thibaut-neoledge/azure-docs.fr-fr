<properties 
	pageTitle="Connexion à un serveur SQL Server local à partir d’une application API dans Azure App Service au moyen de connexions hybrides" 
	description="Créer une application API sur Microsoft Azure et la connecter à une base de données SQL Server locale"
	services="app-service\api" 
	documentationCenter="" 
	authors="TomArcher" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/29/2015" 
	ms.author="tarcher"/>

# Connexion à un serveur SQL Server local à partir d’une application API dans Azure App Service au moyen de connexions hybrides

Les connexions hybrides permettent de connecter des applications API [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) à des ressources locales qui utilisent un port TCP statique. Les ressources prises en charge incluent Microsoft SQL Server, MySQL, les API web HTTP, les services mobiles et la plupart des services web personnalisés.

Dans ce didacticiel, vous allez apprendre à créer une application API App Service dans la [ version préliminaire d’Azure](http://go.microsoft.com/fwlink/?LinkId=529715) qui se connecte à une base de données SQL Server locale à l’aide de la nouvelle fonctionnalité de connexion hybride. Ce didacticiel part du principe que vous n’avez jamais utilisé Azure ni SQL Server.

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Composants requis

Pour réaliser ce didacticiel, vous avez besoin des produits suivants. Tous sont disponibles gratuitement, de sorte que vous pouvez commencer à développer des solutions Azure gratuitement.

- **Abonnement Azure** : pour un abonnement gratuit, consultez la page [Version d'évaluation gratuite d'Azure](/pricing/free-trial/). 

- **Visual Studio** - Pour télécharger une version d’évaluation gratuite de Visual Studio 2013 ou Visual Studio 2015, consultez [Téléchargements de Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs). Installez-en une des deux avant de continuer. (Les captures d’écran de ce didacticiel ont été effectuées à l’aide de Visual Studio 2013)

- **SQL Server 2014 Express with Tools** - Téléchargez gratuitement Microsoft SQL Server Express sur la [page des bases de données Microsoft Web Platform](https://www.microsoft.com/fr-fr/download/details.aspx?id=42299). Plus loin dans ce didacticiel, vous verrez comment [installer SQL Server](#InstallSQLDB) pour vous assurer qu’il est correctement configuré.

- **SQL Server Management Studio Express** : cet outil est inclus dans le téléchargement de SQL Server 2014 Express with Tools mentionné plus haut, mais si vous avez besoin de l'installer séparément, vous pouvez le télécharger et l'installer à partir de la [page de téléchargement de SQL Server Express](https://www.microsoft.com/fr-fr/download/details.aspx?id=42299).

Ce didacticiel part du principe que vous possédez un abonnement Azure, que vous avez installé Visual Studio 2013 et que vous avez installé ou activé .NET Framework 3.5. Il explique comment installer SQL Server 2014 Express dans une configuration adaptée à la fonctionnalité Connexions hybrides d'Azure (une instance par défaut avec un port IP statique). Avant de commencer ce didacticiel, téléchargez (sans l’installer) SQL Server 2014 Express with Tools depuis l’emplacement mentionné ci-dessus si vous n’avez pas installé SQL Server.

### Remarques
Pour utiliser une base de données SQL Server ou SQL Server Express locale avec une connexion hybride, TCP/IP doit être activé sur un port statique. Les instances par défaut dans SQL Server utilisent le port statique 1433, mais pas les instances nommées.

L'ordinateur sur lequel vous installez l'agent Gestionnaire de connexion hybride local :

- doit disposer d'une connectivité à Azure via :

> <table border="1">
    <tr>
       <th><strong>Port</strong></th>
        <th>Pourquoi</th>
    </tr>
    <tr>
        <td>80</td>
        <td><strong>Obligatoire</strong> pour le port HTTP pour la validation du certificat et éventuellement la connectivité des données.</td>
    </tr>
    <tr>
        <td>443</td>
        <td><strong>Facultatif</strong> pour la connectivité des données. Si la connectivité sortante à&#160;443 n'est pas disponible, le port TCP&#160;80 est utilisé.</td>
    </tr>
	<tr>
        <td>5671 et 9352</td>
        <td><strong>Recommandé</strong> mais facultatif pour la connectivité des données. Notez que ce mode entraîne habituellement un débit plus élevé. Si la connectivité sortante à ces ports n'est pas disponible, le port TCP&#160;443 est utilisé.</td>
	</tr>
</table>

- doit être capable d'accéder au *nom\_d'hôte*:*numéro\_de\_port* de votre ressource locale. 

Les étapes décrites dans cet article partent du principe que vous utilisez le navigateur à partir de l'ordinateur qui hébergera l'agent de connexion hybride local.

Si vous avez déjà installé SQL Server dans une configuration et dans un environnement qui répondent aux conditions décrites ci-dessus, vous pouvez passer directement à [Création d'une base de données SQL Server en local](#CreateSQLDB).

<a name="InstallSQL"></a>
## Installation de SQL Server Express, activation de TCP/IP et création d'une base de données SQL Server en local

Cette section explique comment installer SQL Server Express, activer TCP/IP et créer une base de données afin que votre application API fonctionne avec le [portail Azure en version préliminaire](https://portal.azure.com).

<a name="InstallSQLDB"></a>
### Installation de SQL Server Express

1. Pour installer SQL Server Express, téléchargez le fichier **SQLEXPRWT\_x64\_ENU.exe** (version 64 bits) ou **SQLEXPR\_x86\_ENU.exe** (version 32 bits) et extrayez-le dans le dossier souhaité. 

2. Une fois que vous avez extrait les fichiers d’installation de SQL Server Express, exécutez **setup.exe**.

3. Quand le **Centre d’installation SQL Server** s’affiche, choisissez **Nouvelle installation autonome SQL Server ou ajout de fonctionnalités à une installation existante**.

	![Centre d’installation SQL Server](./media/app-service-api-hybrid-on-premises-sql-server/sql-server-installation-center.png)

4. Suivez les instructions, en acceptant les options et les paramètres par défaut, jusqu'à l'affichage de la page **Configuration de l'instance**.
	
5. Dans la page **Configuration de l’instance**, choisissez **Instance par défaut**, puis cliquez sur **Suivant**.
	
	![Configuration de l’instance](./media/app-service-api-hybrid-on-premises-sql-server/instance-configuration.png)
	
	L’instance par défaut de SQL Server écoute les demandes des clients SQL Server sur le port statique 1433. C’est une exigence de la fonctionnalité Connexions hybrides. Les instances nommées utilisent des ports dynamiques et UDP, qui ne sont pas pris en charge par Connexions hybrides.
	
6. Acceptez les valeurs par défaut dans la page **Configuration du serveur**, puis cliquez sur **Suivant**.
	
7. Sur la page **Configuration du moteur de base de données**, sous **Mode d'authentification**, choisissez **Mode mixte (authentification SQL Server et authentification Windows)** et fournissez un mot de passe.
	
	![Configuration du moteur de base de données](./media/app-service-api-hybrid-on-premises-sql-server/database-engine-configuration.png)
	
	Dans ce didacticiel, vous allez utiliser l'authentification SQL Server. N'oubliez pas votre mot de passe, car vous en aurez besoin ultérieurement.
	
8. Parcourez les autres étapes de l’Assistant (en acceptant les valeurs par défaut au fur et à mesure) pour terminer l’installation.

9. Fermez la boîte de dialogue **Centre d’installation SQL Server**.

### Activation de TCP/IP
Pour activer TCP/IP, vous allez utiliser le Gestionnaire de configuration SQL Server, qui a été installé en même temps que SQL Server Express. Suivez la procédure décrite à la page [Activation du protocole réseau TCP/IP pour SQL Server](http://technet.microsoft.com/library/hh231672%28v=sql.110%29.aspx) avant de continuer.

<a name="CreateSQLDB"></a>
### Création d'une base de données SQL Server locale

1. Dans **SQL Server Management Studio**, connectez-vous à l’instance SQL Server que vous venez d’installer. Dans la zone **Type de serveur**, choisissez **Moteur de base de données**. Dans la zone **Nom du serveur**, vous pouvez utiliser **localhost** ou le nom de l'ordinateur dont vous vous servez. Choisissez **Authentification SQL Server**, puis ouvrez une session avec le nom d’utilisateur `sa` et le mot de passe que vous avez créé précédemment.

	![Se connecter au serveur](./media/app-service-api-hybrid-on-premises-sql-server/connect-to-server.png)
	
	Si la boîte de dialogue **Se connecter au serveur** ne s’affiche pas automatiquement, accédez à l’**Explorateur d’objets** dans le volet gauche, cliquez sur **Se connecter**, puis sur **Moteur de base de données**.
	
2. Pour créer une base de données avec SQL Server Management Studio, cliquez avec le bouton droit sur **Bases de données** dans l'Explorateur d'objets, puis cliquez sur **Nouvelle base de données**.
	
	![Menu Créer une base de données](./media/app-service-api-hybrid-on-premises-sql-server/new-database-menu.png)
	
3. Dans la boîte de dialogue **Nouvelle base de données**, entrez `LocalDatabase` comme nom de base de données, puis cliquez sur **OK**.
	
	![Création d’une base de données](./media/app-service-api-hybrid-on-premises-sql-server/new-database.png)
	
### Créer et remplir une table SQL Server

1. Dans l’**Explorateur d’objets** **SQL Server Management Studio**, développez l’entrée `LocalDatabase`.

	![Base de données étendue](./media/app-service-api-hybrid-on-premises-sql-server/local-database-expanded.png)

2. Cliquez avec le bouton droit sur **Tables** et sélectionnez l’option **Table...** dans le menu contextuel.

	![Menu Nouvelle table](./media/app-service-api-hybrid-on-premises-sql-server/new-table-menu.png)

3. Quand la grille du Concepteur de tables s’affiche, entrez les informations de colonne comme indiqué dans l’illustration suivante.

	![Nouvelles colonnes de table](./media/app-service-api-hybrid-on-premises-sql-server/table-def.png)

4. Appuyez sur **&lt;Ctrl>S** pour enregistrer la définition de la nouvelle table. Vous serez invité à entrer un nom de table. Entrez `Speakers` et appuyez sur **OK**.

	![Enregistrer la nouvelle table](./media/app-service-api-hybrid-on-premises-sql-server/save-new-table.png)

5. Dans l’**Explorateur d’objets**, cliquez avec le bouton droit sur la table `Speakers` nouvellement créée et sélectionnez **Modifier les 200 premières lignes** dans le menu contextuel.

	![Modifier les 200 lignes du haut](./media/app-service-api-hybrid-on-premises-sql-server/edit-top-200-rows.png)

6. Quand la grille s’affiche pour entrer/modifier les données de la table, entrez quelques données de test comme indiqué dans l’illustration suivante.

	![Données de test](./media/app-service-api-hybrid-on-premises-sql-server/speakers-data.png)

## Créer et déployer l’application API de démonstration sur Azure 

Cette section vous guide dans le processus de création de l’application API de démonstration.

1. Ouvrez Visual Studio 2013 et sélectionnez **Fichier > Nouveau > Projet**. 

2. Sélectionnez le modèle **Visual C# > Web > Application web ASP.NET**, désactivez l’option **Ajouter Application Insights à votre projet**, nommez le projet *SpeakersList*, puis cliquez sur **OK**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-project.png)

3. Dans la boîte de dialogue **Nouveau projet ASP.NET**, sélectionnez le modèle de projet **Application API Azure**, puis cliquez sur **OK**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-project-api-app.png)

4. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier **Modèles**, puis sélectionnez l’option de menu contextuel **Ajouter > Classe...**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-model-menu.png)

5. Nommez le nouveau fichier *Speaker.cs*, puis cliquez sur **Ajouter**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-model-class.png)

6. Remplacez tout le contenu du fichier `Speaker.cs` par le code suivant.

		namespace SpeakersList.Models
		{
			public class Speaker
			{
				public int Id { get; set; }
				public string Name { get; set; }
				public string EmailAddress { get; set; }
			}
		}

7. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier **Contrôleurs**, puis sélectionnez l’option de menu contextuel **Ajouter > Contrôleur...**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-controller.png)

8. Dans la boîte de dialogue **Ajouter une génération de modèles automatique**, sélectionnez l’option **Contrôleur d’API web 2 - Vide** et cliquez sur **Ajouter**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/add-scaffold.png)

9. Nommez le contrôleur **SpeakersController** et cliquez sur **Ajouter**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/add-controller-name.png)

10. Remplacez le code dans le fichier `SpeakersController.cs` par le code ci-dessous. Assurez-vous de spécifier vos propres valeurs pour les espaces réservés <nom\_serveur> et <mot\_de\_passe> dans la `connectionString`. La valeur <nom\_serveur> est le nom de l’ordinateur sur lequel se trouve SQL Server, et la valeur <mot\_de\_passe> est celle que vous avez définie quand vous avez installé et configuré SQL Server.

	> [AZURE.NOTE]L’extrait de code suivant contient des informations de mot de passe. Ceci permet de simplifier la démonstration. Dans un environnement de production réel, ne stockez pas vos informations d’identification dans le code. Reportez-vous plutôt aux [meilleures pratiques pour le déploiement de mots de passe et d’autres données sensibles dans ASP.NET et Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Web.Http;
		using SpeakersList.Models;
		using System.Data.Sql;
		using System.Data.SqlClient;
		
		namespace SpeakersList.Controllers
		{
		    public class SpeakersController : ApiController
		    {
		        [HttpGet]
		        public IEnumerable<Speaker> Get()
		        {
					// Instantiate List object that will be populated and returned
					// from this method.
		            List<Speaker> speakers = new List<Speaker>();
	
					// Build database connection string.
		            string connectionString = "Server=<serverName>;" +
		                                      "Initial Catalog=LocalDatabase;" +
		                                      "User Id=sa;Password=<password>;" +
		                                      "Asynchronous Processing=true;";
		
					// Build query string to select all three columns from the Speakers table.
		            string queryString = "SELECT Id, EmailAddress, Name FROM dbo.Speakers;";

					// Instantiate the SqlConnection object using the connection string.
		            using (SqlConnection connection = new SqlConnection(connectionString))
		            {
						// Instantiate the SqlCommand object using the connection and query strings.
		                SqlCommand command = new SqlCommand(queryString, connection);

						// Open the connection to the database.
		                connection.Open();

						// Execute the command and return a SqlDataReader object
						// that can be iterated.
		                SqlDataReader reader = command.ExecuteReader();
		                try
		                {
							// Read a row from the SqlDataReader object
		                    while (reader.Read())
		                    {
								// For each row, use the returned data to instantiate
								// and add to the List a new Speaker object.
		                        speakers.Add(new Speaker 
		                            { 
		                                Id = Convert.ToInt32(reader[0]), 
		                                EmailAddress = reader[1].ToString(), 
		                                Name = reader[2].ToString() 
		                            }
		                        );
		                    }
		                }
		                finally
		                {
							// Close the SqlDataReader object.
		                    reader.Close();
		                }
		            }
					// Return the List of Speaker objects.
		            return speakers;
		        }
		    }
		}

### Activer l’interface utilisateur Swagger

L’activation de l’interface utilisateur Swagger vous permet de tester facilement votre application API sans avoir à écrire de code client pour l’appeler.

1. Ouvrez le fichier *App\_Start/SwaggerConfig.cs* et recherchez **EnableSwaggerUI** :

	![](./media/app-service-api-hybrid-on-premises-sql-server/swagger-ui-disabled.png)

2. Annulez les marques de commentaire sur les lignes de code suivantes :

	        })
	    .EnableSwaggerUi(c =>
	        {

3. Quand vous avez terminé, vérifiez que le fichier ressemble à l’image suivante.

	![](./media/app-service-api-hybrid-on-premises-sql-server/swagger-ui-enabled.png)

### Tester l’application API

1. Pour afficher la page de test d’API, exécutez l’application localement via **<Ctrl>F5**. Vous devez voir une erreur similaire à l’image suivante.

	![](./media/app-service-api-hybrid-on-premises-sql-server/error-forbidden.png)

2. Dans la barre d’adresse du navigateur, ajoutez `/swagger` à la fin de l’URL et cliquez sur **<Entrée>**. Ceci affichera l’interface utilisateur Swagger que vous avez activée dans la section précédente.

	![](./media/app-service-api-hybrid-on-premises-sql-server/swagger-ui.png)

3. Cliquez sur la section **Speakers** pour la développer.

4. Cliquez sur **GET /api/speakers** pour développer cette section.

5. Cliquez sur **Essayer** pour afficher les données que vous avez entrées précédemment dans la base de données.

	![](./media/app-service-api-hybrid-on-premises-sql-server/try-it-out.png)

### Déploiement de l’application API

Maintenant que vous avez testé l’application localement, il est temps de la déployer sur Azure.

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet (pas la solution), puis cliquez sur **Publier**. 

	![Option de menu Publier le projet](./media/app-service-api-hybrid-on-premises-sql-server/publish-menu.png)

2. Cliquez sur l’onglet **Profil** et sur **Microsoft Azure API Apps (version préliminaire)**.

	![Option de menu Publier le projet](./media/app-service-api-hybrid-on-premises-sql-server/publish-web.png)

3. Cliquez sur **Nouveau** pour configurer une nouvelle application API dans votre abonnement Azure.

	![Boîte de dialogue de sélection des services API existants](./media/app-service-api-hybrid-on-premises-sql-server/publish-to-existing-api-app.png)

4. Dans la boîte de dialogue **Créer une application API**, entrez les informations suivantes :

	- Sous **Nom de l’application API**, entrez un nom pour l’application. 
	- Si vous avez plusieurs abonnements Azure, sélectionnez celui que vous souhaitez utiliser.
	- Sous **Plan App Service**, sélectionnez un des plans App Service existants ou **Créer un plan App Service** et entrez le nom d’un nouveau plan. 
	- Sous **Groupe de ressources**, sélectionnez un des groupes de ressources existants ou **Créer un groupe de ressources** et entrez un nom. Le nom doit être unique ; envisagez d'utiliser le nom de l'application en tant que préfixe et d'ajouter des informations personnelles telles que votre ID Microsoft (sans le signe @).  
	- Sous **Niveau d’accès**, sélectionnez **Accessible à tout le monde**. Cette option rendra votre API complètement publique, ce qui convient pour ce didacticiel. Vous pouvez restreindre l’accès ultérieurement via le [portail Azure en version préliminaire](https://portal.azure.com).
	- Sélectionnez une région.

	Cliquez sur **OK** pour créer l’application API dans votre abonnement.

	![Boîte de dialogue de configuration de l’application web Microsoft Azure](./media/app-service-api-hybrid-on-premises-sql-server/publish-new-api-app.png)

5. Comme le processus peut prendre quelques minutes, Visual Studio affiche une boîte de dialogue vous informant que le processus a commencé. Cliquez sur **OK** dans la boîte de dialogue de confirmation.

	![Message de confirmation de la création du service de l’API](./media/app-service-api-hybrid-on-premises-sql-server/create-api-app-confirmation.png)

6. Pendant que le processus d’approvisionnement crée le groupe de ressources et l’application API dans votre abonnement Azure, Visual Studio affiche la progression dans la fenêtre **Activité d’Azure App Service**.

	![Notification d’état via la fenêtre Activité d’Azure App Service](./media/app-service-api-hybrid-on-premises-sql-server/app-service-activity.png)

7. Une fois que l’application API est configurée, cliquez avec le bouton droit sur le projet dans l’**Explorateur de solutions** et sélectionnez **Publier** pour rouvrir la boîte de dialogue de publication. Le profil de publication créé à l'étape précédente doit être sélectionné au préalable. Cliquez sur **Publier** pour commencer le processus de déploiement.

	![Déploiement de l’application API](./media/app-service-api-hybrid-on-premises-sql-server/publish2.png)

La fenêtre **Activité d’Azure App Service** affiche la progression du déploiement et indique quand le processus de publication est terminé.

## Création d'une connexion hybride et d'un service BizTalk ##

1. Dans votre navigateur, accédez au [portail Azure en version préliminaire](https://portal.azure.com). 

2. Cliquez sur l’option **Parcourir tout** sur la gauche.

3. Dans le panneau **Parcourir**, sélectionnez **API Apps**.

4. Dans le panneau **API Apps**, localisez votre application API et cliquez dessus.

5. Dans le panneau API Apps, cliquez sur la valeur sous **Hôte d’application API**.
 
	![Panneau Application API](./media/app-service-api-hybrid-on-premises-sql-server/api-app-blade-api-app-host.png)

6. Quand le panneau **Hôte d’application API** s’affiche, faites défiler l’écran vers le bas jusqu’à la section **Mise en réseau** et cliquez sur **Connexions hybrides**.
	
	![Hybrid connections](./media/app-service-api-hybrid-on-premises-sql-server/api-app-host-blade-hybrid-connections.png)
	
7. Dans le panneau **Connexions hybrides**, cliquez sur **Ajouter** > **Nouvelle connexion hybride**.
	
8. Dans le volet **Créer une connexion hybride** :
	- Sous **Nom**, entrez un nom pour la connexion.
	- Sous **Nom d'hôte**, entrez le nom de votre ordinateur hôte SQL Server.
	- Sous **Port**, entrez `1433` (le port par défaut pour SQL Server).
	- Cliquez sur **Service Biz Talk** et entrez le nom du service BizTalk.
	
	![Create a hybrid connection](./media/app-service-api-hybrid-on-premises-sql-server/create-biztalk-service.png)
		
9. Cliquez sur **OK** deux fois.

	Quand le processus est terminé, la zone **Notifications** affiche la mention **SUCCÈS** en vert clignotant et le panneau **Connexions hybrides** affiche la nouvelle connexion hybride dans l’état **Non connecté**.
	
	![Connexion hybride créée](./media/app-service-api-hybrid-on-premises-sql-server/hybrid-not-connected-yet.png)
	
À ce stade, vous avez terminé une partie importante de l'infrastructure de connexion hybride cloud. Vous allez ensuite créer un élément local.

<a name="InstallHCM"></a>
## Installation du Gestionnaire de connexions hybrides local pour terminer la connexion

[AZURE.INCLUDE [app-service-hybrid-connections-manager-install](../../includes/app-service-hybrid-connections-manager-install.md)]

Maintenant que l’infrastructure de connexion hybride est terminée, il est temps de tester l’application.

<a name="CreateASPNET"></a>
## Tester l’application API terminée sur Azure

1. Dans le portail Azure en version préliminaire, revenez au panneau Hôte d’application API et cliquez sur la valeur sous **URL**.
	
2. Une fois que la page de l’hôte d’application API s’affiche dans votre navigateur, ajoutez `/swagger` à l’URL dans la barre d’adresse du navigateur et cliquez sur **<Entrée>**.
	
3. Cliquez sur la section **Speakers** pour la développer.

4. Cliquez sur **GET /api/speakers** pour développer cette section.

5. Cliquez sur **Essayer** pour afficher les données que vous avez entrées précédemment dans la base de données.

	![](./media/app-service-api-hybrid-on-premises-sql-server/try-it-out-azure.png)
	
**Félicitations !** Vous avez créé une application API qui s’exécute sur Azure et utilise une connexion hybride pour se connecter à une base de données SQL Server locale.

## Voir aussi
[Aperçu des connexions hybrides](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist présente les connexions hybrides (vidéo Channel 9)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Aperçu des connexions hybrides](/services/biztalk-services/)

[BizTalk Services : Onglets Tableau de bord, Surveiller, Mettre à l’échelle, Configurer et Connexion hybride](../biztalk-dashboard-monitor-scale-tabs/)

[Création d’un cloud hybride réel avec la portabilité transparente des applications (vidéo Channel 9)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Connexion à un serveur SQL Server local à partir d’un service mobile Azure au moyen de connexions hybrides](../mobile-services-dotnet-backend-hybrid-connections-get-started.md)

[Connexion à une instance SQL Server locale à partir d’Azure Mobile Services au moyen de connexions hybrides (vidéo Channel 9)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

[Vue d’ensemble d’ASP.NET Identity](http://www.asp.net/identity)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

<!---HONumber=August15_HO6-->
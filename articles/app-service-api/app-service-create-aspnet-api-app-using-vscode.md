<properties
   pageTitle="Créer une application API ASP.NET 5 dans Visual Studio Code"
   description="Ce didacticiel vous indique comment créer une application API ASP.NET 5 via Visual Studio Code."
   services="app-service\api"
   documentationCenter=".net"
   authors="erikre"
   manager="wpickett"
   editor="jimbe"/>

<tags
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="erikre"/>

# Créer une application API ASP.NET 5 dans Visual Studio Code

> [AZURE.SELECTOR]
- [Visual Studio 2015 or 2013](app-service-dotnet-create-api-app.md)
- [Visual Studio Code](app-service-create-aspnet-api-app-using-vscode.md)

## Vue d'ensemble

Ce didacticiel vous indique comment créer une application API ASP.NET 5 via [Visual Studio Code](http://code.visualstudio.com//Docs/whyvscode). ASP.NET 5 est une nouvelle conception importante de la fonctionnalité ASP.NET. ASP.NET 5 est une nouvelle infrastructure open source et interplateforme qui vous permet de créer des applications web modernes basées sur le Cloud, à l’aide de .NET. Pour en savoir plus, voir [Présentation de l’infrastructure ASP.NET 5](http://docs.asp.net/en/latest/conceptual-overview/aspnet.html). Pour en savoir plus sur les applications API, voir [Que sont les applications API ?](app-service-api-apps-why-best-platform.md)

> [AZURE.NOTE]Pour effectuer ce didacticiel, vous avez besoin d’un compte Microsoft Azure. Si vous n’avez pas de compte, vous pouvez [vous inscrire pour accéder à la version d’essai gratuit](/pricing/free-trial/) ou [activer les avantages dont vous bénéficiez en tant qu’abonné MSDN](/pricing/member-offers/msdn-benefits-details/). Vous pouvez également essayer gratuitement les [exemples d’applications App Service](http://tryappservice.azure.com).

## Composants requis  

* Installez et configurez [Visual Studio Code](http://code.visualstudio.com/Docs/setup).
* Installez [Node.js](http://nodejs.org/download/) <br> [Node.js](http://nodejs.org/) est une plateforme permettant de créer des applications de serveur rapides et évolutives, à l’aide de JavaScript. « Node » est le runtime (nœud) et [npm](http://www.npmjs.com/), le gestionnaire de package pour les modules Node. Vous utiliserez « npm » pour structurer une application API ASP.NET 5 dans ce didacticiel.

## Installer ASP.NET 5 et DNX
ASP.NET 5/DNX est une pile .NET lean vous permettant de créer des applications web et cloud modernes capables de s’exécuter sur OS X, Linux et Windows. Elle a été construite intégralement pour fournir une infrastructure de développement optimisée pour les applications qui sont déployées sur le cloud ou qui sont exécutées en local. Elle inclut des composants modulaires associés à des frais généraux réduits. Ainsi, vous bénéficiez d’une certaine flexibilité lors de la création de vos solutions.

> [AZURE.NOTE]ASP.NET 5 et DNX (environnement d’exécution .NET) sur OS X et Linux sont disponibles en version bêta/d’évaluation.

Ce didacticiel est conçu pour vous aider à créer des applications à l’aide des dernières versions de développement de l’infrastructure ASP.NET 5 et de DNX. Si vous souhaitez une expérience officielle, plus stable, accédez à la page [http://www.asp.net/vnext](http://www.asp.net/vnext). Les instructions suivantes portent spécifiquement sur Windows. Pour consulter des instructions plus détaillées sur l’installation de cette fonction sur des systèmes OS X, Linux et Windows, voir [Installation d’ASP.NET 5 et de DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx).

1. Pour installer le gestionnaire de version .NET (DNVM) dans Windows, ouvrez une invite de commandes et exécutez la commande suivante :

	<pre class="prettyprint">
@powershell -NoProfile -ExecutionPolicy unrestricted -Command "&amp;{$Branch='dev';iex ((new-object net.webclient).DownloadString('https://raw.githubusercontent.com/aspnet/Home/dev/dnvminstall.ps1'))}"
</pre>Cette action charge le script du gestionnaire de version .NET et l’associe à votre profil utilisateur.

2. Vous devrez peut-être vous déconnecter après avoir saisi la commande ci-dessus pour que la modification apportée à la variable d’environnement PATH soit appliquée.
3. Vérifiez l’emplacement de DNVM en exécutant la commande suivante dans la fenêtre Commande : 

	<pre class="prettyprint">
where dnvm
</pre>La fenêtre Commande affiche un chemin d’accès semblable à celui-ci :

	![Emplacement du gestionnaire de version .NET](./media/app-service-create-aspnet-api-app-using-vscode/00-where-dnvm.png)

4. Maintenant que le gestionnaire de version .NET est installé, vous devez l’utiliser pour charger DNX afin d’exécuter vos applications. Exécutez la commande suivante depuis la fenêtre Commande :

	<pre class="prettyprint">
dnvm upgrade
</pre>

5. Vérifiez votre gestionnaire de version .NET et affichez le runtime actif en saisissant la commande suivante dans la fenêtre Commande :

	<pre class="prettyprint">
dnvm list
</pre>La fenêtre Commande affiche les détails du runtime actif :

	![Emplacement du gestionnaire de version .NET](./media/app-service-create-aspnet-api-app-using-vscode/00b-dnvm-list.png)

Pour accéder à des instructions plus détaillées sur l’installation de cette fonction sur des systèmes OS X, Linux et Windows, voir [Installation d’ASP.NET 5 et de DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx).

## Créer l’application API 

Cette section vous indique comment structurer une nouvelle application API ASP.NET. Vous allez utiliser Node Package Manager (NPM) pour installer [Yeoman](http://yeoman.io/), [Grunt](http://gruntjs.com/) et [Bower](http://bower.io/).

1. Après avoir installé Node.js et Visual Studio Code, ouvrez une invite de commandes avec des droits d’administrateur et accédez à l’emplacement auquel installer tous vos projets ASP.NET utilisant VSCode.
2. Pour installer Yeoman et les outils de prise en charge, saisissez la commande suivante dans la fenêtre Commandes :

	<pre class="prettyprint">
npm install -g yo grunt-cli generator-aspnet bower
</pre>

3. Saisissez la commande suivante dans la fenêtre Commandes pour créer le dossier du projet et structurer l’application :

	<pre class="prettyprint">
yo aspnet
</pre>

4. Suivez les instructions fournies par le générateur en faisant défiler la zone et en sélectionnant le type d’**application API web**.

	![Yeoman : générateur ASP.NET 5](./media/app-service-create-aspnet-api-app-using-vscode/01-yo-aspnet.png)

5. Définissez le nom de votre nouvelle application API ASP.NET sur **ContactsList**. Ce nom sera utilisé dans le code fourni à une étape ultérieure de ce didacticiel. <br> Yeoman crée un dossier nommé **ContactsList**, ainsi que les fichiers requis par votre nouvelle application.
6. Ouvrez **Visual Studio Code**.<br> Vous pouvez ouvrir VSCode à partir de la fenêtre Commandes, en saisissant **code .**.
7. Dans le menu **Fichier**, sélectionnez **Ouvrir un dossier** et choisissez l’emplacement de votre application API ASP.NET.

	![Boîte de dialogue Sélectionner un dossier](./media/app-service-create-aspnet-api-app-using-vscode/02-open-folder.png)

	VSCode charge votre projet et l’affiche dans la fenêtre **Explorer**.

	![VSCode affichant le projet Contact](./media/app-service-create-aspnet-api-app-using-vscode/03-vscode-contacts-project.png)

8. Dans **VSCode**, accédez au menu **Afficher** et sélectionnez **Palette de commandes**.
9. Dans la zone **Palette de commandes**, saisissez les commandes suivantes :

	<pre class="prettyprint">
dnx:dnu restore - (ContactsList)
</pre>Lorsque vous commencez la saisie, vous voyez apparaître la ligne de commande complète dans la liste.

	![Commande « Restore »](./media/app-service-create-aspnet-api-app-using-vscode/04-dnu-restore.png)

	La commande « Restore » installe les packages NuGet requis pour exécuter l’application. Une fenêtre Commandes affiche la mention **Restauration terminée** lorsque l’opération est terminée.

## Modifier l’application API

Vous allez modifier l’application **ContactsList** en ajoutant une classe **Contact** et une classe **ContactsController**.

1. Placez le curseur sur le nom du projet **ContactsList** et ajoutez un nouveau dossier appelé *Models*, en cliquant sur l’icône Nouveau dossier.

	![Nouveau dossier « Models »](./media/app-service-create-aspnet-api-app-using-vscode/07-new-folder.png)

2. Cliquez avec le bouton droit de la souris sur le dossier **Models** afin d’ajouter un nouveau fichier de classe appelé *Contact.cs* en utilisant le code suivant :

	<pre class="prettyprint">
namespace ContactsList.Models
{
    public class Contact
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string EmailAddress { get; set; }
    }
}
</pre>

3. Cliquez avec le bouton droit de la souris sur le dossier **Controllers** et ajoutez un fichier *ContactsController.cs*, de façon qu’il apparaisse comme suit :

	<pre class="prettyprint">
using System.Collections.Generic;
using Microsoft.AspNet.Mvc;
using ContactsList.Models;

namespace ContactsList.Controllers
{
    [Route("api/[controller]")]
    public class ContactsController : Controller
    {
        // GET: api/Contacts
        [HttpGet]
        public IEnumerable&lt;Contact> Get()
        {
            return new Contact[]{
                new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
                new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
                new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"}
            };
        }
    }
}
</pre>

4. Assurez-vous que tous vos fichiers sont enregistrés en cliquant sur **Fichier** > **Enregistrer tout**.
5. Dans la zone **Palette de commandes**, saisissez la commande suivante pour exécuter l’application en local :

	<pre class="prettyprint">
dnx: kestrel - (ContactsList, Microsoft.AspNet.Hosting --server Kestrel --server.urls http://localhost:5001
</pre>La fenêtre Commandes affiche la mention *Démarré*. Si la fenêtre Commandes n’affiche pas la mention *Démarré*, vérifiez l’angle inférieur gauche de la fenêtre VSCode et recherchez les erreurs repérées dans votre projet.

5. Ouvrez un navigateur et accédez à l’URL suivante :

	****http://localhost:5001/api/Contacts**

	Vous pouvez ensuite afficher le contenu de l’élément *Contacts.json*. Lorsque vous affichez ce fichier, vous voyez apparaître le contenu suivant :

	![Contenu de JSON renvoyé](./media/app-service-create-aspnet-api-app-using-vscode/08-contacts-json.png)

## Modifier les métadonnées de l’application API
Les métadonnées qui permettent le déploiement d’un projet d’API ASP.NET en tant qu’application API se trouvent dans un fichier *apiapp.json*, situé à la racine du projet.

1. Dans VSCode, cliquez sur le dossier *wwwroot* avec le bouton droit de la souris et sélectionnez l’option **Nouveau fichier**.
2. Donnez au nouveau fichier le nom *apiapp.json*.<br> Assurez-vous que le fichier *apiapp.json* se trouve dans le dossier *wwwroot*.
3. Ajoutez le code suivant au fichier *apiapp.json* :

	<pre class="prettyprint">
{
    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
    "id": "ContactsList",
    "namespace": "microsoft.com",
    "gateway": "2015-01-14",
    "version": "1.0.0",
    "title": "ContactsList",
    "summary": "",
    "author": "",
    "endpoints": null
}
</pre>

Dans le fichier *apiapp.json*, vous pouvez spécifier un point de terminaison pour le JSON de définition de l’API Swagger. Cependant, pour les besoins de ce didacticiel, vous utiliserez un fichier de définition d’API statique. Pour obtenir un exemple qui utilise la génération de Swagger dynamique, voir [Configurer un projet d’API web comme application API](app-service-dotnet-create-api-app-visual-studio.md).

## Ajouter une définition d’API Swagger statique
Pour fournir un fichier de définition d’API Swagger 2.0 statique, vous devez créer un dossier à la racine web et placer le fichier de définition d’API à l’intérieur de ce dernier.

1. Dans VSCode, créez un dossier nommé *metadata* dans le dossier *wwwroot*.
2. Cliquez sur le nouveau dossier *metadata* avec le bouton droit de la souris et ajoutez un nouveau fichier appelé *apiDefinition.swagger.json*. 
3. Ajoutez la syntaxe JSON suivante dans le nouveau fichier :

	<pre class="prettyprint">
{
  "swagger": "2.0",
  "info": {
    "version": "v1",
    "title": "ContactsList"
  },
  "host": "REMPLACEZ CETTE VALEUR PAR L’URL DE VOTRE HÔTE",
  "schemes": [
    "https"
  ],
  "paths": {
    "/api/Contacts": {
      "get": {
        "tags": [
          "Contacts"
        ],
        "operationId": "Contacts_Get",
        "consumes": [],
        "produces": [
          "application/json",
          "text/json",
          "application/xml",
          "text/xml"
        ],
        "responses": {
          "200": {
            "description": "OK",
            "schema": {
              "type": "array",
              "items": {
                "$ref": "#/definitions/Contact"
              }
            }
          }
        },
        "deprecated": false
      },
      "post": {
        "tags": [
          "Contacts"
        ],
        "operationId": "Contacts_Post",
        "consumes": [
          "application/json",
          "text/json",
          "application/xml",
          "text/xml",
          "application/x-www-form-urlencoded"
        ],
        "produces": [
          "application/json",
          "text/json",
          "application/xml",
          "text/xml"
        ],
        "parameters": [
          {
            "name": "contact",
            "in": "body",
            "required": true,
            "schema": {
              "$ref": "#/definitions/Contact"
            }
          }
        ],
        "responses": {
          "200": {
            "description": "OK",
            "schema": {
              "$ref": "#/definitions/Object"
            }
          }
        },
        "deprecated": false
      }
    }
  },
  "definitions": {
    "Contact": {
      "type": "object",
      "properties": {
        "Id": {
          "format": "int32",
          "type": "integer"
        },
        "Name": {
          "type": "string"
        },
        "EmailAddress": {
          "type": "string"
        }
      }
    },
    "Object": {
      "type": "object",
      "properties": {}
    }
  }
}
</pre>

À une étape ultérieure de ce didacticiel, vous remplacerez la chaîne d’espace réservé relative à l’URL de l’hôte (ci-dessus) par l’URL de votre hôte Microsoft Azure, que vous créerez et copierez ultérieurement.

## Créer une application API dans la version préliminaire du portail Azure

> [AZURE.NOTE]Pour effectuer ce didacticiel, vous avez besoin d’un compte Microsoft Azure. Si vous n’avez pas de compte, vous pouvez [vous inscrire pour accéder à la version d’essai gratuit](/pricing/free-trial/) ou [activer les avantages dont vous bénéficiez en tant qu’abonné MSDN](/pricing/member-offers/msdn-benefits-details/). Vous pouvez également essayer gratuitement les [exemples d’applications App Service](http://tryappservice.azure.com).

1. Connectez-vous à la [version préliminaire du portail Azure](https://portal.azure.com).

2. Cliquez sur l’option **NOUVEAU** située dans la partie supérieure gauche du portail.

3. Cliquez sur **Web + Mobile > Application API**.

	![Nouvelle application API de Microsoft Azure](./media/app-service-create-aspnet-api-app-using-vscode/09-azure-newapiapp.png)

4. Saisissez une valeur pour le champ **Nom**, par exemple ContactsList.

5. Sélectionnez un plan App Service ou créez-en un. Si vous créez un plan, sélectionnez le niveau de tarification, l’emplacement et d’autres options.

	![Panneau Nouvelle application API de Microsoft Azure](./media/app-service-create-aspnet-api-app-using-vscode/10-azure-newappblade.png)

6. Cliquez sur **Create**.

	![Panneau Application API](./media/app-service-create-aspnet-api-app-using-vscode/11-azure-apiappblade.png)

	Si vous n’avez pas désélectionné l’option **Épingler au tableau d’accueil** lors de la création de l’application, vous pouvez facilement localiser cette dernière en cliquant sur **Accueil** ou **Parcourir**. Si vous avez désélectionné cette option, cliquez sur l’option **Notifications** figurant sur la gauche pour afficher l’état de la création de l’application API, puis cliquez sur la notification pour accéder au panneau de la nouvelle application API.

7. Cliquez sur **Paramètres > Paramètres de l'application**.

8. Définissez le niveau d'accès à **Public (anonyme)**.

9. Cliquez sur **Enregistrer**.

	![Paramètres de l’application dans Microsoft Azure](./media/app-service-create-aspnet-api-app-using-vscode/12-azure-appsettings.png)

## Activer la publication Git à partir de la nouvelle application API

Git est un système de contrôle de version distribué vous permettant de déployer votre site web Azure. Vous allez stocker le code que vous écrivez pour votre application API dans un référentiel Git local, et vous allez déployer votre code dans Azure par transmission de type push vers un référentiel distant. Cette méthode de déploiement est une fonctionnalité des applications web App Service que vous pouvez utiliser dans une application API, car les applications API sont basées sur des applications web : une application API dans Azure App Service est une application web avec des fonctionnalités supplémentaires pour l'hébergement de services web.

Dans le portail, vous gérez les fonctionnalités spécifiques aux applications API dans le panneau **Application API**, et vous gérez les fonctionnalités qui sont partagées avec des applications web dans le panneau **Hôte d'application API**. Dans cette section, vous accédez au panneau **Hôte d'application API** pour configurer la fonctionnalité de déploiement Git.

1. Dans le volet Application API, cliquez sur **Hôte d'application API**.

	![Hôte de l’application API dans Microsoft Azure](./media/app-service-create-aspnet-api-app-using-vscode/13-azure-apiapphost.png)

2. Recherchez la section **Déploiement** du panneau **Application API** et cliquez sur **Configurer le déploiement continu**. Il peut être nécessaire de faire défiler l'écran vers le bas pour visualiser cette partie du panneau.

	![Hôte de l’application API dans Microsoft Azure](./media/app-service-create-aspnet-api-app-using-vscode/14-azure-deployment.png)

3. Cliquez sur **Choisir la source > Référentiel Git local**.

5. Cliquez sur **OK**.

	![Référentiel Git local dans Microsoft Azure](./media/app-service-create-aspnet-api-app-using-vscode/15-azure-localrepository.png)

6. Si vous n'avez pas précédemment configuré les informations d'identification de déploiement pour la publication d'une application API ou d'une autre application App Service, configurez-les maintenant :

	* Cliquez sur **Définir les informations d'identification de déploiement**.

	* Créez un nom d'utilisateur et un mot de passe. Vous aurez besoin de ce mot de passe lorsque vous configurerez Git.

	* Cliquez sur **Save**.

	![Informations d’identification sur le déploiement dans Microsoft Azure](./media/app-service-create-aspnet-api-app-using-vscode/16-azure-credentials.png)

7. Dans le panneau **Hôte d'application API**, cliquez sur **Paramètres > Propriétés**. L'URL du référentiel Git distant vers lequel vous allez déployer se trouve sous « URL GIT ».

8. Copiez l’**URL GIT**, que vous utiliserez ultérieurement dans le didacticiel.

	![URL Git de Microsoft Azure](./media/app-service-create-aspnet-api-app-using-vscode/17-azure-giturl.png)

9. Ensuite, accédez au panneau **APPLICATION API** et copiez l’**URL** que vous utiliserez pour mettre à jour la valeur « host » dans le fichier *apiDefinition.swagger.json*.

	![URL Microsoft Azure](./media/app-service-create-aspnet-api-app-using-vscode/18-azure-url.png)

10. Dans VSCode, ouvrez le fichier *apiDefinition.swagger.json* et remplacez la valeur « REMPLACEZ CETTE VALEUR PAR L’URL DE VOTRE HÔTE » par l’**URL** que vous avez copiée à l’étape précédente.
11. Supprimez les caractères « https:// » du début de la valeur d’hôte.
12. Enregistrez les modifications que vous avez apportées au fichier *apiDefinition.swagger.json*.

## Publier une application API dans Microsoft Azure App Service

Dans cette section, vous créez un référentiel Git local et vous effectuez une transmission de type push depuis ce référentiel vers Azure pour déployer votre exemple d'application dans l'application API qui s'exécute dans Azure App Service.

1. Dans VS Code, sélectionnez l’option Git dans la barre de navigation de gauche.
2. Si la fonction Git n’est pas encore installée, procédez à son installation en choisissant l’un des liens fournis ([Chocolatey](https://chocolatey.org/packages/git) ou [git-scm.com](http://git-scm.com/downloads)). Si vous n’êtes pas familiarisé avec Git, choisissez **git-scm.com** et sélectionnez l’option vous permettant d’utiliser cette fonction avec GitBash, à partir de l’invite de commandes Windows. 
3. Une fois que vous avez installé Git, redémarrez VSCode et sélectionnez l’option Git dans la barre de gauche.
4. Dans VSCode, sélectionnez **Initialiser le référentiel Git** pour vous assurer que votre espace de travail est sous le contrôle du code source Git. 

	![Initialiser Git](./media/app-service-create-aspnet-api-app-using-vscode/19-initgit.png)

5. 
6. Ajoutez un message de validation, puis cochez la case **Valider tout**.

	![Option Valider tout de Git](./media/app-service-create-aspnet-api-app-using-vscode/20-git-commit.png)

6. Recherchez et ouvrez **GitBash**. Vous pouvez également utiliser l’invite de commandes Windows pour effectuer cette opération.
7. Dans **GitBash**, remplacez les dossiers par celui de votre projet VSCode. Exemple :

	<pre class="prettyprint">
cd c:\VSCodeProjects\ContactsList
</pre>

7. Créez une référence distante pour l’envoi des mises à jour vers l’application web (hôte de l’application API) que vous avez créée précédemment, en utilisant l’URL Git copiée précédemment (qui se termine par « .git ») :

	<pre class="prettyprint">
git remote add azure [URL de votre référentiel distant]
</pre>

8. Envoyez vos modifications dans Azure en entrant la commande suivante :

	<pre class="prettyprint">
git push azure master
</pre>Vous êtes invité à entrer le mot de passe que vous avez créé précédemment. **Remarque : votre mot de passe ne sera pas visible.**

	La sortie de cette commande se termine par un message indiquant que le déploiement a réussi :

	<pre class="prettyprint">
remote: Deployment successful.
To https://user@testsite.scm.azurewebsites.net/testsite.git
[new branch]      master -> master
</pre>

> [AZURE.NOTE]Si vous apportez des modifications à votre application, vous pouvez la republier en cochant la case **Valider tout** dans VS Code, puis en saisissant la commande **git push azure master** dans **GitBash**.

## Afficher la définition d'API dans le portail Azure en version préliminaire
Maintenant que vous avez déployé une API dans votre application API, vous pouvez voir la définition de l'API dans le portail Azure en version préliminaire. Vous commencez par redémarrer la passerelle, ce qui permet à Microsoft Azure de reconnaître que la définition d’API d’une application API a été modifiée. La passerelle est une application web qui gère l'administration et les autorisations des API pour les applications API d'un groupe de ressources.

1. Dans le portail Microsoft Azure en version préliminaire, accédez au panneau **APPLICATION API** de l’application API que vous avez créée précédemment, puis cliquez sur le lien **Passerelle**.
2. Dans le panneau **PASSERELLE**, cliquez sur **Redémarrer**. Vous pouvez maintenant fermer ce panneau.
3. Dans le panneau **APPLICATION API**, cliquez sur **Redémarrer**. 
4. Dans le panneau **APPLICATION API**, cliquez sur **Définition d’API**.<br> Le panneau Définition d’API affiche deux méthodes. Si vous ne voyez pas apparaître les méthodes GET et POST immédiatement, attendez quelques secondes, afin que Microsoft Azure puisse actualiser l’application. Ensuite, dans le panneau **APPLICATION API**, cliquez sur **Définition d’API**.

## Exécuter l’application dans Microsoft Azure
Dans le portail Microsoft Azure en version préliminaire, accédez au panneau **HÔTE D’APPLICATION API** de votre application API, puis cliquez sur **Parcourir**. Ensuite, ajoutez la chaîne **api/Contacts** à la fin de votre URL pour afficher les détails du contact.


## Conclusion
Ce didacticiel vous a montré comment créer une application API dans Visual Studio Code. Pour en savoir plus sur ce logiciel, voir [Visual Studio Code](https://code.visualstudio.com/Docs/). Pour en savoir plus sur les applications API, voir [Que sont les applications API ?](app-service-api-apps-why-best-platform.md).
 

<!---HONumber=August15_HO8-->
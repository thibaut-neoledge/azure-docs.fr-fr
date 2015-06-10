<properties
	pageTitle="Prise en main d’Azure AD iOS | Microsoft Azure"
	description="Création d’une application iOS qui s’intègre avec Azure AD pour la connexion et appelle des API protégées par Azure AD en utilisant OAuth."
	services="active-directory"
	documentationCenter="ios"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="brandwe"/>

# Intégration d’Azure AD dans une application iOS

[AZURE.INCLUDE [active-directory-devguide](../includes/active-directory-devguide.md)]

Si vous développez une application de bureau, Azure AD facilite l’authentification de vos utilisateurs avec leurs comptes Active Directory. Il permet également à votre application d’utiliser en toute sécurité une API web protégée par Azure AD, telle que l’API Office 365 ou Azure.

Pour les clients iOS qui doivent accéder à des ressources protégées, Azure AD fournit la bibliothèque d’authentification Active Directory (ADAL). Le seul objectif de cette bibliothèque ADAL est de faciliter l’obtention des jetons d’accès pour votre application. Pour illustrer sa facilité d’utilisation, nous allons créer une application de liste des tâches Objective C qui effectue les actions suivantes :

-	obtention de jetons d’accès pour appeler l’API Azure AD Graph à l’aide du [protocole d’authentification OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx) ;
-	recherche, dans un répertoire, d’utilisateurs correspondant à un alias donné ;
-	déconnexion des utilisateurs.

Pour générer l’application fonctionnelle complète, vous devez :

2. inscrire votre application auprès d’Azure AD ;
3. installer et configurer la bibliothèque ADAL ;
5. utiliser la bibliothèque ADAL pour obtenir des jetons à partir d’Azure AD.

Pour commencer, téléchargez [la structure de l’application](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) ou [l’exemple terminé](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip). Vous avez également besoin d’un client Azure AD dans lequel vous pouvez créer des utilisateurs et inscrire une application. Si vous ne disposez pas encore d’un client, [découvrez comment en obtenir un](active-directory-howto-tenant.md).

## Étape 1 : Téléchargement et exécution de l’exemple de serveur TODO d’API REST Node.js ou .Net

Cet exemple est écrit spécifiquement pour fonctionner avec notre exemple existant pour la création d’une API REST TODO avec un seul client pour Microsoft Azure Active Directory. Il s’agit d’un composant requis pour le démarrage rapide.

Pour plus d’informations sur la configuration associée, visitez notre exemple existant ici :

* [Exemple de service d’API REST Microsoft Azure Active Directory pour Node.js](active-directory-devquickstarts-webapi-nodejs.md)

## Étape 2 : Inscription de votre API web auprès de votre client Microsoft Azure AD

**Que faire ?**

*Microsoft Active Directory prend en charge l’ajout de deux types d’application. Les API web qui offrent des services aux utilisateurs, ainsi que les applications (soit sur Internet soit sur une application exécutée sur un appareil) qui y accèdent. Lors de cette étape, vous enregistrez l’API web que vous exécutez localement pour tester cet exemple. Normalement, cette API web est un service REST qui offre des fonctionnalités auxquelles une application va accéder. Microsoft Azure Active Directory peut protéger n’importe quel point de terminaison.*

*Ici, nous supposons que vous inscrivez l’API REST TODO mentionnée ci-dessus, mais cela fonctionne aussi pour les API web Azure Active Directory à protéger.*

Étapes d’inscription d’une API web avec Microsoft Azure AD

1. Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com).
2. Cliquez sur Active Directory dans la partie de gauche.
3. Cliquez sur le répertoire client dans lequel vous souhaitez enregistrer l'exemple d'application.
4. Cliquez sur l’onglet Applications.
5. Dans le tiroir, cliquez sur Ajouter.
6. Cliquez sur Ajouter une application développée par mon organisation.
7. Entrez un nom convivial pour l’application, par exemple « TodoListService », sélectionnez Application web et/ou API web, puis cliquez sur Suivant.
8. Pour l’URL de connexion, entrez l’URL de base pour l’exemple, qui est par défaut `https://localhost:8080`.
9. Pour l’URI d’ID d’application, saisissez `https://<your_tenant_name>/TodoListService`, en remplaçant `<your_tenant_name>` par le nom de votre client Azure AD. Cliquez sur OK pour terminer l’inscription.
10. Toujours dans le portail Azure, cliquez sur l’onglet Configurer de votre application.
11. **Recherchez la valeur d’ID client et prenez-en note**, car vous en aurez besoin plus tard lors de la configuration de votre application.

## Étape 3 : Inscription de l’exemple d’application cliente native iOS

Vous devez d’abord inscrire votre application web. Ensuite, vous devez aussi indiquer à Azure Active Directory l’existence de votre application. Cela permet à votre application de communiquer avec l’API web qui vient d’être inscrite.

**Que faire ?**

*Comme indiqué plus haut, Microsoft Active Directory prend en charge l’ajout de deux types d’application. Les API web qui offrent des services aux utilisateurs, ainsi que les applications (soit sur Internet soit sur une application exécutée sur un appareil) qui y accèdent. Dans cette étape, vous inscrivez l’application dans cet exemple. Vous devez procéder ainsi pour que cette application soit en mesure de demander l’accès à l’API web que vous venez d’inscrire. Azure Active Directory ne permet pas à votre application de demander une connexion, sauf si celle-ci est inscrite. Cela fait partie de la sécurité du modèle.*

*Ici, nous supposons que vous inscrivez l’exemple d’application mentionné ci-dessus, mais cela fonctionne pour n’importe quelle application que vous développez.*

**Pourquoi incorporer à la fois une application et une API web dans un seul client ?**

*Comme vous l’avez peut-être deviné, vous pouvez générer une application qui accède à une API externe inscrite dans Azure Active Directory à partir d’un autre client. Si vous procédez ainsi, vos clients sont invités à donner leur autorisation pour l’utilisation de l’API dans l’application. L’avantage, c’est que c’est la bibliothèque d’authentification Active Directory pour iOS qui s’occupe de cette autorisation pour vous. À mesure que nous aborderons des fonctionnalités plus avancées, vous verrez qu’il s’agit d’une partie importante du travail nécessaire pour accéder à la suite d’API Microsoft Azure et Office, ainsi qu’à tout autre fournisseur de services. Pour l’instant, comme vous avez inscrit votre API web et l’application sous le même client, vous ne voyez aucune invite d’autorisation. Cela est généralement le cas si vous développez une application uniquement pour une utilisation par votre entreprise.*

1. Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com).
2. Cliquez sur Active Directory dans la partie de gauche.
3. Cliquez sur le répertoire client dans lequel vous souhaitez enregistrer l'exemple d'application.
4. Cliquez sur l’onglet Applications.
5. Dans le tiroir, cliquez sur Ajouter.
6. Cliquez sur Ajouter une application développée par mon organisation.
7. Entrez un nom convivial pour l’application, par exemple « TodoListClient-iOS », sélectionnez Application cliente native, puis cliquez sur Suivant.
8. Pour l’URI de redirection, entrez `http://TodoListClient`. Cliquez sur Terminer.
9. Cliquez sur l’onglet Configurer de l’application.
10. Recherchez la valeur d’ID client et prenez-en note, car vous en aurez besoin plus tard lors de la configuration de votre application.
11. Dans Autorisations pour d’autres applications, cliquez sur Ajouter une application. Sélectionnez Autre dans la liste déroulante Afficher et cliquez sur la coche située en haut. Recherchez l’API TodoListService, cliquez dessus, puis cliquez sur la coche située en bas pour ajouter l’application. Sélectionnez Accéder à TodoListService à partir de la liste déroulante Autorisations déléguées, puis enregistrez la configuration.


## Étape 4 : Téléchargement du code de l’exemple de client natif iOS

* `$ git clone git@github.com:AzureADSamples/NativeClient-iOS.git`

## Étape 5 : Téléchargement de la bibliothèque ADAL pour iOS et ajout de celle-ci à votre espace de travail XCode

#### Téléchargement de la bibliothèque ADAL pour iOS

* `git clone git@github.com:MSOpenTech/azure-activedirectory-library-for-ios.git`

#### Importez la bibliothèque dans votre espace de travail.

Dans XCode, cliquez avec le bouton droit sur votre répertoire de projet et sélectionnez « Ajouter des fichiers l’exemple iOS »...

Lorsque vous y êtes invité, sélectionnez le répertoire où vous avez cloné ADAL pour iOS

#### Ajoutez la bibliothèque ADAL iOS à vos bibliothèques et infrastructures liées.

Cliquez sur le bouton Ajouter sous « Infrastructures et bibliothèques liées » et ajoutez le fichier de bibliothèque à partir des infrastructures importées.

Générez le projet pour vous assurer que tout se compile correctement.


## Étape 6 : Configuration du fichier settings.plist avec les informations de l’API web

Sous « Fichiers de prise en charge » se trouve un fichier settings.plist. Il contient les informations suivantes :

```XML
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>authority</key>
	<string>https://login.windows.net/common/oauth2/token</string>
	<key>clientId</key>
	<string>xxxxxxx-xxxxxx-xxxxxxx-xxxxxxx</string>
	<key>resourceString</key>
	<string>https://localhost/todolistservice</string>
	<key>redirectUri</key>
	<string>http://demo_todolist_app</string>
	<key>userId</key>
	<string>user@domain.com</string>
	<key>taskWebAPI</key>
	<string>https://localhost/api/todolist/</string>
</dict>
</plist>
```

Remplacez les informations du fichier plist par les paramètres de l’API web.

##### REMARQUE

Les valeurs par défaut en cours sont configurées pour fonctionner avec notre [Exemple de service d’API REST Microsoft Azure Active Directory pour Node.js](https://github.com/AzureADSamples/WebAPI-Nodejs). Vous devez toutefois spécifier l’ID client de votre API web. Si vous exécutez votre propre API, vous devez mettre à jour les points de terminaison en fonction des besoins.

## Étape 7 : Génération et exécution de l’application

Vous devez être en mesure de vous connecter au point de terminaison d’API REST et être invité à entrer les informations d’identification de votre compte Azure Active Directory.

Pour obtenir des ressources supplémentaires, consultez :- [AzureADSamples sur GitHub >](https://github.com/AzureAdSamples) - Documentation Azure AD sur [Azure.com >](http://azure.microsoft.com/documentation/services/active-directory/)

<!---HONumber=58-->
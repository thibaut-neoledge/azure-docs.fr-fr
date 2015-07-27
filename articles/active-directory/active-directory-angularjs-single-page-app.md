<properties
	pageTitle="Création d’une application à page unique AngularJS avec Azure AD"
	description="Illustre l'utilisation d'Active Directory Authentication Library (ADAL) pour Javascript pour sécuriser une application à page unique AngularJS, implémentée avec un serveur principal d'API web ASP.NET, qui appelle une autre API web ASP.NET à l'aide de CORS."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="terrylan"
	editor="LisaToft"/>

<tags
	ms.service="active-directory"
	ms.devlang="javascript"
	ms.topic="article" 
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="05/11/2015"
	ms.author="justinha"/>


# Création d’une application à page unique AngularJS avec Azure AD

Ce didacticiel illustre l'utilisation d'Active Directory Authentication Library (ADAL) pour Javascript pour sécuriser une application à page unique AngularJS, implémentée avec un serveur principal d'API web ASP.NET, qui appelle une autre API web ASP.NET à l'aide de CORS. Pour voir l’exemple de code de ce didacticiel, consultez la page [AzureADSamples/SinglePageApp-AngularJS-DotNet](https://github.com/AzureADSamples/SinglePageApp-AngularJS-DotNet) sur github.

La bibliothèque ADAL pour Javascript est une bibliothèque open source. Pour les options de distribution, le code source et les contributions, consultez la page du [référentiel JS ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-js).

Pour plus d’informations sur le fonctionnement des protocoles dans ce scénario et d’autres scénarios, consultez la page [Scénarios d’authentification pour Azure AD](http://go.microsoft.com/fwlink/?LinkId=394414).

## Exécution de cet exemple

La mise en route est aisée. Pour exécuter cet exemple, vous avez besoin des éléments suivants :

- Visual Studio 2013
- Une connexion Internet
- Un abonnement à Azure (un [essai gratuit](https://account.windowsazure.com/organization) suffit)

Chaque abonnement Azure possède un client Azure Active Directory (Azure AD) associé. Toutes les fonctionnalités d'Azure AD utilisées par cet exemple sont disponibles gratuitement.

## Clonage ou téléchargement de ce référentiel

À partir de l’interpréteur de commandes ou de la ligne de commande : `git clone https://github.com/AzureADSamples/SinglePageApp-WebAPI-AngularJS-DotNet.git`

## Enregistrement du service API To Go avec votre client Azure Active Directory

1. Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com).
2. Cliquez sur **Active Directory** dans la partie gauche.
3. Cliquez sur le répertoire où vous souhaitez enregistrer l'exemple d'application.
4. Cliquez sur l’onglet **Applications**.
5. Dans le tiroir, cliquez sur **Ajouter**.
6. Cliquez sur **Ajouter une application développée par mon organisation**.
7. Entrez un nom convivial pour l’application, par exemple « API To Go », sélectionnez **Application Web** et/ou **API Web**, puis cliquez sur **Suivant**.
8. Pour l’URL d’authentification, entrez l’URL de base pour l’exemple, qui est par défaut `https://localhost:44327/`.
9. Comme URI ID d’application, saisissez `https://<your_directory_name>/ToGoAPI`, en remplaçant `<your_directory_name>` par le nom de votre annuaire Azure AD. Enregistrez la configuration.

Vous avez terminé. Avant de passer à l'étape suivante, vous devez rechercher l'URI ID d'application de votre API.

1. Toujours dans le portail Azure, cliquez sur l’onglet **Configurer** de votre application.
2. Recherchez la valeur de l'URI ID d'application et copiez-la dans le Presse-papiers.

## Configuration de l'API To Go pour utiliser votre client Azure Active Directory

1. Ouvrez la solution dans Visual Studio 2013.
2. Dans le projet ToGoAPI, ouvrez le fichier `web.config`.
3. Recherchez la clé d’application `ida:Tenant` et remplacez la valeur par le nom de votre client Azure AD.
4. Recherchez la clé d’application `ida:Audience` et remplacez la valeur par l’URI ID d’application que vous avez copié à partir du portail Azure.
5. Également dans le projet ToGoAPI, ouvrez le fichier `Controllers/ToGoListController.cs`. Dans l’attribut `[EnableCors...]`, indiquez l’emplacement du client d’application à page unique To Do. Par défaut, il s’agit de `https://localhost:44326`. Veillez à omettre la barre oblique de fin.
5. Dans le projet d’application à page unique To Do, ouvrez le fichier `App/Scripts/App.js` et recherchez la déclaration de l’objet `endpoints`.
6. Entrez un mappage de l'emplacement du point de terminaison de l'API To Go à son identificateur de ressource ou à l'URI ID d'application. Le nom de la propriété `endpoints` de l’objet doit être l’emplacement de l’API To Go. Par défaut, il s’agit de `https://localhost:44327/`. La valeur de cette propriété doit être l’URI ID d’application que vous avez copié depuis le portail, par exemple `https://<your_tenant_name>/ToGoAPI`.
8. Ne vous inquiétez pas pour l'instant des autres valeurs de configuration de ce fichier, nous y reviendrons dans une seconde.
9. Également dans le projet d’application à page unique To Do, ouvrez le fichier `App/Scripts/toGoListSvc.js`. Remplacez la valeur de la variable `apiEndpoint` par l’emplacement de votre API To Go. La valeur par défaut est `https://localhost:44327/`.

## Enregistrement de l'application à page unique (SPA) To Do avec votre client Azure Active Directory

1. Reconnectez-vous au [portail de gestion Azure](https://manage.windowsazure.com).
2. Cliquez sur **Active Directory** dans la partie gauche.
3. Cliquez sur le client où vous souhaitez enregistrer l’exemple d’application.
4. Cliquez sur l’onglet **Applications**.
5. Dans le tiroir, cliquez sur **Ajouter**.
6. Cliquez sur **Ajouter une application développée par mon organisation**.
7. Entrez un nom convivial pour l’application, par exemple « SPA To Do », sélectionnez **Application Web et/ou API Web**, puis cliquez sur **Suivant**.
8. Pour l’URL d’authentification, entrez l’URL de base pour l’exemple, qui est par défaut `https://localhost:44326/`.
9. Comme URI ID d’application, saisissez `https://<your_directory_name>/ToDoSPA`, en remplaçant `<your_directory_name>` par le nom de votre annuaire Azure AD.
10. Dans la section **Autorisations pour d’autres applications**, cliquez sur **Ajouter une application**. Sélectionnez **Autre** dans la liste déroulante **Afficher** et cliquez sur la coche supérieure. Recherchez et cliquez sur l'API To Go, puis cliquez sur la coche inférieure pour ajouter l'application. Sélectionnez **Accéder à l’API To Go** à partir de la liste déroulante **Autorisations déléguées**, puis enregistrez la configuration.

Vous avez terminé. Avant de passer à l'étape suivante, vous devez rechercher l'ID client de votre application.

1. Toujours dans le portail Azure, cliquez sur l’onglet **Configurer** de votre application.
2. Recherchez la valeur de l'ID client et copiez-la dans le Presse-papiers.


## Activation de l'allocation implicite OAuth2 pour votre application

Par défaut, les applications approvisionnées dans Azure AD ne sont pas activées pour utiliser l'allocation implicite OAuth2. Pour exécuter cet exemple, vous devez l'indiquer explicitement.

1. En raison des étapes précédentes, votre navigateur doit toujours être sur le portail de gestion Azure et afficher en particulier l’onglet **Configurer** de l’entrée de votre application.
2. À l’aide du bouton **Gérer le manifeste** du tiroir, téléchargez le fichier manifeste de l’application et enregistrez-le sur le disque.
3. Ouvrez le fichier manifeste avec un éditeur de texte. Recherchez la propriété `oauth2AllowImplicitFlow`. Vous constaterez qu’elle est définie sur `false`. Remplacez-la par `true` et enregistrez le fichier.
4. À l’aide du bouton **Gérer le manifeste**, téléchargez le fichier manifeste mis à jour. Enregistrez la configuration de l'application.

## Configuration de l'application SPA To Do pour utiliser votre client Azure Active Directory

1. Ouvrez la solution dans Visual Studio 2013.
2. Dans le projet d’application à page unique To Do, ouvrez le fichier `web.config`.
3. Recherchez la clé d’application `ida:Tenant` et remplacez la valeur par le nom de votre annuaire Azure AD.
4. Recherchez la clé d’application `ida:Audience` et remplacez la valeur par l’ID client provenant du portail Azure.
5. Également dans le projet d’application à page unique To Do, ouvrez à nouveau le fichier `App/Scripts/App.js` et recherchez la ligne `adalAuthenticationServiceProvider.init(`.
6. Remplacez la valeur de `tenant` par le nom de votre annuaire Azure AD.
7. Remplacez la valeur de `clientId` par l’ID client provenant du portail Azure.

## Exécution de l'exemple

Nettoyez la solution, régénérez-la et exécutez-la.

Vous pouvez déclencher le processus de connexion en cliquant sur le lien de connexion dans le coin supérieur droit, ou en cliquant directement sur les onglets **Liste To Do** ou **Liste To Go**. Explorez l'exemple en vous connectant, en ajoutant des éléments à la liste To Do, en supprimant le compte d'utilisateur et en redémarrant. Ajoutez des emplacements à la liste To Go, en effectuant des opérations CRUD sur l'API To Go à l'aide de CORS.

## Déploiement de cet exemple dans Azure

Pour déployer les projets SPA To Do et API To Go vers des sites web Azure, vous créez deux sites web, publiez chaque projet sur un site web et mettez à jour les deux projets pour référencer les nouveaux emplacements au lieu d'IIS Express.

### Création du site web Azure du projet API To Go

1. Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com).
2. Cliquez sur **Sites Web** dans la partie gauche.
3. Cliquez sur **Nouveau** dans le coin inférieur gauche, sélectionnez **Calcul** > **Site Web** > **Création personnalisée**, sélectionnez le plan d’hébergement et la région et nommez votre site web, par exemple togo-contoso.azurewebsites.net. Sélectionnez une base de données à utiliser ou créez-en une nouvelle. Cliquez sur **Créer un site Web**.
4. Une fois le site web créé, cliquez dessus pour le gérer. Pour cet ensemble d'étapes, téléchargez le fichier .publishsettings et enregistrez-le. D'autres mécanismes de déploiement, par exemple à partir du contrôle de code source, peuvent également être utilisés. Pour plus d’informations sur l’utilisation d’un fichier .publishsettings, consultez la rubrique [Procédure : Connexion à votre abonnement](../install-configure-powershell.md#Connect).

### Création du site web Azure du projet SPA To Do

1. Accédez au [portail de gestion Azure](https://manage.windowsazure.com).
2. Cliquez sur **Sites Web** dans la partie gauche.
3. Cliquez sur **Nouveau** dans le coin inférieur gauche, sélectionnez **Calcul** > **Site Web** > **Création personnalisée**, sélectionnez le plan d’hébergement et la région et nommez votre site web, par exemple todo-contoso.azurewebsites.net. Sélectionnez une base de données à utiliser ; la même base de données que l'API To Go fera l'affaire. Cliquez sur **Créer un site Web**.
4. Une fois le site web créé, cliquez dessus pour le gérer. Une fois encore, téléchargez le fichier .publishsettings pour ce site et enregistrez-le.

### Mise à jour des deux projets pour utiliser des sites web Azure

1. Dans Visual Studio, accédez au projet SPA To Do.
2. Deux modifications sont nécessaires. Dans `App\Scripts\app.js`, remplacez le nom de la propriété de l’objet `endpoints` par le nouvel emplacement de votre API To Go, par exemple `https://togo-contoso.azurewebsites.net/`. Dans `App\Scripts\toGoListSvc.js`, remplacez la variable `apiEndpoint` par la même valeur.
3. Dans le projet API To Go, une seule modification est nécessaire. Dans `Controllers\ToGoListController.cs`, mettez à jour l’attribut `[EnableCors...]` en fonction du nouvel emplacement du projet d’application à page unique To Do, par exemple `https://todo-contoso.azurewebsites.net`. Une fois encore, assurez-vous d'omettre la barre oblique de fin.

### Publication du projet API To Go sur des sites web Azure

1. Basculez vers Visual Studio et accédez au projet API To Go. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**. Cliquez sur **Importer** et importez le profil de publication de l’API To Go que vous avez téléchargé.
6. Sous l’onglet **Connexion**, mettez à jour l’URL de destination afin qu’elle soit au format https, par exemple https://togo-constoso.azurewebsites.net. Cliquez sur **Next**.
7. Sous l’onglet **Paramètres**, vérifiez que la case **Activer l’authentification d’organisation** n’est PAS cochée. Cliquez sur **Publier**.
8. Visual Studio publie le projet et ouvre automatiquement un navigateur vers l’URL du projet. Si la page web par défaut du projet s’affiche, la publication a réussi.

### Publication du projet SPA To Do sur des sites web Azure

1. Basculez vers Visual Studio et accédez au projet SPA To Do. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**. Cliquez sur **Importer** et importez le fichier .publishsettings du projet que vous avez téléchargé.
6. Sous l’onglet **Connexion**, mettez à jour l’URL de destination afin qu’elle soit au format https, par exemple https://todo-contoso.azurewebsites.net. Cliquez sur **Next**.
7. Sous l’onglet **Paramètres**, vérifiez que la case **Activer l’authentification d’organisation** n’est PAS cochée. Cliquez sur **Publier**.
8. Visual Studio publie le projet et ouvre automatiquement un navigateur vers l’URL du projet. Si la page web par défaut du projet s’affiche, la publication a réussi.

### Mise à jour de la configuration du projet SPA To Do dans le client Azure Active Directory

1. Accédez au [portail de gestion Azure](https://manage.windowsazure.com).
2. Dans la partie gauche, cliquez sur **Active Directory** et sélectionnez votre client.
3. Sous l’onglet **Applications**, sélectionnez l’application **SPA To Do**.
4. Sous l’onglet **Configurer**, mettez à jour les champs **URL de connexion** et **URL de réponse** conformément à l’adresse de votre application à page unique, par exemple https://todo-contoso.azurewebsites.net. Enregistrez la configuration.

## À propos du code

Les fichiers de clé qui contiennent la logique d'authentification sont les suivants :

- **App.js** : injecte la dépendance du module ADAL, fournit les valeurs de configuration de l’application utilisées par la bibliothèque ADAL pour le pilotage des interactions de protocole avec Azure AD et indique les itinéraires qui ne doivent pas être accessibles sans authentification préalable.

- **index.html** : contient une référence à adal.js.

- **HomeController.js** : montre comment tirer parti des méthodes login() et logOut() dans la bibliothèque ADAL.

- **UserDataController.js** : montre comment extraire des informations utilisateur de l’id_token mis en cache.

Un grand merci à @matvelloso pour l'aide apportée à la création de ce didacticiel.


## Étapes suivantes

Voici quelques ressources supplémentaires pour vous aider à utiliser Azure AD pour ajouter l'authentification et l'autorisation à vos applications web et API web :

+ [Exemples de code Azure Active Directory](https://msdn.microsoft.com/library/azure/dn646737.aspx)
+ [Scénarios d’authentification pour Azure AD](https://msdn.microsoft.com/library/azure/dn499820.aspx)
 

<!---HONumber=July15_HO3-->
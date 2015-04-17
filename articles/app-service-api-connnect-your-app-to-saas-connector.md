<properties 
	pageTitle="Connecter votre application à un connecteur SaaS existant" 
	description="Cet article explique comment se connecter à un connecteur SaaS existant" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="VinayaReddy" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/20/2015" 
	ms.author="vinayr;tdykstra"/>

# Connecter votre application à un connecteur SaaS existant

## Vue d'ensemble

Ce didacticiel vous montre comment utiliser un connecteur SaaS qui se trouve dans Azure Marketplace. 

Vous allez effectuer les étapes suivantes :
- configurer le connecteur Dropbox ;
- configurer l'application API Dropbox ;
- configurer la passerelle ;
- acquérir et stocker le jeton dans le magasin de jetons ; 
- appeler l'API Dropbox et vérifier que l'accès authentifié fonctionne.

## Configurer le connecteur Dropbox

1. Accédez à la page d'accueil du portail en version préliminaire et cliquez sur Marketplace.

	![](./media/app-service-api-connect-your-app-to-saas-connector/01-Marketplace.png)

2. Recherchez Dropbox dans la galerie Marketplace.

	![](./media/app-service-api-connect-your-app-to-saas-connector/02-Marketplace-search.png)
 
3. Cliquez sur l'icône Dropbox pour configurer le connecteur Dropbox. Cliquez sur le bouton Créer pour configurer le connecteur Dropbox. Veillez à renseigner le nom et les valeurs voulues pour tous les champs avant de cliquer sur le bouton Créer. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/03-Dropbox-Connector-Blade.png) 

4. Examinez le panneau du groupe de ressources dans le [portail en version préliminaire] (https://portal.azure.com/). Vous voyez le connecteur Dropbox  et la passerelle. Quand vous avez configuré une application API, vous l'avez configurée pour faire partie d'un groupe de ressources. Quand le portail a créé le groupe de ressources pour vous, il a également créé une passerelle. Une passerelle est une application web spéciale qui gère toutes les demandes destinées aux applications API dans le groupe de ressources.

	Vous pouvez ajouter des applications web et d'autres applications API au même groupe de ressources, et chaque application API dans le groupe de ressources peut avoir l'un des trois paramètres d'accessibilité suivants :

	* Public (anonyme) : tout le monde peut appeler l'application API en dehors du groupe de ressources sans être connecté.
	* Public (authentifié) : seuls les utilisateurs authentifiés sont autorisés à appeler l'application API en dehors du groupe de ressources.
	* Interne : seules d'autres applications API ou web dans le même groupe de ressources sont autorisées à appeler l'application API.

## Configurer l'application API Dropbox

Commencez par configurer Dropbox pour accepter uniquement les demandes authentifiées.  Vous devez définir le paramètre d'accessibilité **Public (authentifié)** et configurer la passerelle pour exiger l'authentification auprès d'un fournisseur comme Azure Active Directory, Google ou Facebook.

1.	Dans le [portail en version préliminaire](https://portal.azure.com/) Azure, cliquez sur **Parcourir > Applications API**, puis sur le nom de l'application API à protéger.

	![](./media/app-service-api-connect-your-app-to-saas-connector/04-Browse-API-Apps.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/05-Dropbox-Connector.png) 
 
2.	Dans le panneau Application API, cliquez sur Paramètres, puis sur Paramètres de l'application.
 
	![](./media/app-service-api-connect-your-app-to-saas-connector/06-Dropbox-connector-properties.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/07-dropbox-settings-dialog.png) 

3.	Dans le panneau **Paramètres de l'application**, remplacez le **Niveau d'accès** par **Public (authentifié)**. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/08-public-auth-setting-blade.png) 

	Vous avez maintenant protégé le connecteur Dropbox contre tout accès non authentifié. Il est maintenant temps de configurer l'authentification Dropbox. Renseignez les détails *ID client* et "Question secrète du client* dans l'interface utilisateur affichée ci-dessous (vous pouvez connecter *ID client* et "Question secrète du client* à partir du [compte de développeur Dropbox](https://www.dropbox.com/developers/apps)).

	![](./media/app-service-api-connect-your-app-to-saas-connector/09-Dropbox-authentication-settings.png) 

Vous devez ensuite configurer la passerelle pour spécifier le fournisseur d'authentification à utiliser.

## Configurer la passerelle

1. Revenez au panneau Application API Dropbox, puis cliquez sur le lien vers la passerelle.
 
	![](./media/app-service-api-connect-your-app-to-saas-connector/10-dropbox-connector-gateway.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/11-gateway-all-properties.png)

	![](./media/app-service-api-connect-your-app-to-saas-connector/12-gateway-property-settings-blade.png) 

2. Choisissez le fournisseur d'identité à utiliser et suivez les étapes de l'article correspondant pour configurer votre application API avec ce fournisseur. Ces articles ont été écrits pour des applications mobiles, mais les procédures sont les mêmes pour les applications API. Certaines des procédures vous demandent d'utiliser à la fois le [portail de gestion](https://manage.windowsazure.com/) et le [portail en version préliminaire](https://portal.azure.com/).
  
	- [Compte Microsoft](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-microsoft-authentication-preview)
	- [Connexion Facebook](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-facebook-authentication-preview)
	- [Connexion Twitter](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-twitter-authentication-preview)
	- [Connexion Google](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-google-authentication-preview)
	- [Azure Active Directory](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-active-directory-authentication-preview)

	Consultez l'article " Protéger une application API : ajouter une authentification de fournisseur de réseau social ou Azure Active Directory " pour obtenir des instructions détaillées sur la procédure de configuration. 

## Acquérir et stocker le jeton OAuth dans le magasin de jetons

1.	Dans le navigateur, accédez à l'URL de connexion : 

	Vous pouvez récupérer l'URL à partir du panneau de configuration Active Directory de la passerelle.
	![](./media/app-service-api-connect-your-app-to-saas-connector/14-01-gateway-login-URL-portal.png) 
	
	Votre url se présente au format suivant...
	http://[nomgrouperessources]gateway.azurewebsites.net/login/[nomfournisseur]

	Par exemple, si vous avez nommé votre groupe de ressources MyResource et configuré la passerelle pour l'authentification Azure Active Directory, l'URL serait la suivante :

		http://Myresourcegateway.azurewebsites.net/login/aad
	
	Veillez à démarrer les Outils de développement avant d'entrer l'URL dans le navigateur et appuyez sur Entrée. Nous devons recevoir le message de connexion terminée. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/14-02-gateway-login-URL-Browser.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/14-03-gateway-login-confirmation.png) 

	Récupérez le jeton d'authentification zumo et la valeur sous l'onglet Réseau (Chrome).
	![](./media/app-service-api-connect-your-app-to-saas-connector/14-04-Acquire-Zumo-Auth-Token.png) 

	Affichez l'extension Postman dans Chrome. Entrez l'URL de consentement de la passerelle ainsi que le jeton d'authentification Zumo (en-tête) et la valeur, puis soumettez une demande POST. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/15-login-to-the-connector.png) 
 
	Récupérez l'URL de redirection qui est retournée de la demande POST et vérifiez qu'elle fonctionne. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/16-redirect-url-validate.png)


	Si l'URL de redirection fonctionne, cela signifie que le jeton est valide. Tous les appels passés au connecteur SaaS utiliseront le jeton établi et vérifié. Ce jeton doit être inclus avec toutes les demandes provenant de sources externes qui passent par la passerelle. Quand vous accédez à une API avec un navigateur, le navigateur stocke généralement le jeton dans un cookie et l'envoie avec tous les appels ultérieurs à l'API.

Le même flux est applicable pour tous les connecteurs SaaS comme Safesforce, Facebook, etc. 

## Appeler l'API Dropbox et vérifier que l'accès authentifié fonctionne

1. Revenez à la liste d'applications API et sélectionnez le connecteur Dropbox. 

1. Notez l'URL en haut.

2. Cliquez sur le connecteur Dropbox (comme indiqué dans l'image) pour voir toutes les API prises en charge.

	![](./media/app-service-api-connect-your-app-to-saas-connector/13-dropbox-api-app-operations.png) 

	Vous pouvez voir quelles opérations sont prises en charge dans l'application API dans le panneau Définition d'API. Vous pouvez télécharger swagger que vous pouvez utiliser dans Visual Studio pour générer des clients fortement typés. Vous pouvez également télécharger swaddle qui peut être utilisé dans Siena et Power App Studio. 

2. Dans une fenêtre de navigateur, tapez l'URL que vous avez copiée à partir du portail et ajoutez n'importe quelle API prise en charge pour accéder aux fichiers ou autres détails de votre compte Dropbox. 

	Format : `<URL>`/Operation

	Par exemple :

		https://dropboxconnector7b47555bd6784237ad3e7736da769ffc.azurewebsites.net/folder/photos
   
	Comme l'authentification correcte a déjà été établie, l'appel ci-dessus réussit et affiche les détails du dossier de photos dans le navigateur. 
	![](./media/app-service-api-connect-your-app-to-saas-connector/17-call-dropbox-method-from-browser.png) 

<!--todo<Copier l'image du navigateur>-->

## Étapes suivantes

Vous avez vu comment protéger une application API et configurer la passerelle pour accéder au connecteur SaaS à l'aide du magasin de jetons. Pour plus d'informations, consultez [Que sont les applications API ?](app-service-api-apps-why-best-platform.md) 

<!--HONumber=49-->
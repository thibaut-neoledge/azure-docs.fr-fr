<properties 
	pageTitle="Protéger une application API Azure" 
	description="Découvrez comment protéger une application API Azure à l'aide de Visual Studio." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tdykstra"/>

# Protéger une application API : ajouter une authentification de fournisseur de réseau social ou Azure Active Directory

## Vue d'ensemble

Dans le didacticiel [Déployer une application API](app-service-dotnet-deploy-api-app.md), vous avez déployé une application API avec le niveau d'accès **Accessible à tout le monde**. Ce didacticiel explique comment protéger une application API afin que seuls les utilisateurs authentifiés puissent y accéder.

Vous allez effectuer les étapes suivantes :

- appeler l'application API pour vérifier qu'elle fonctionne ;
- appliquer les règles d'authentification à l'application API ;
- appeler une nouvelle fois l'application API pour vérifier qu'elle rejette les demandes non authentifiées ;
- établir une connexion au fournisseur configuré ;
- appeler une nouvelle fois l'application API pour vérifier que l'accès authentifié fonctionne.

## Conditions préalables

Ce didacticiel fonctionne avec l'application API que vous avez créée dans [Créer une application API](app-service-dotnet-create-api-app.md) et déployée dans [Déployer une application API](app-service-dotnet-deploy-api-app.md).

## Utiliser le navigateur pour appeler l'application API 

Le moyen le plus simple de vérifier que votre application API est publiquement accessible consiste à l'appeler à partir d'un navigateur.

1. Dans votre navigateur, accédez au [portail Azure].

3. Dans la page d'accueil, cliquez sur **Parcourir > Applications API**, puis sur le nom de l'application API à protéger.

	![Browse](./media/app-service-api-dotnet-add-authentication/browse.png)

	![Select API app](./media/app-service-api-dotnet-add-authentication/select.png)

3. Dans le panneau **Application API**, cliquez sur l'**URL** pour ouvrir une fenêtre de navigateur qui appelle votre application API.

	![API App blade](./media/app-service-api-dotnet-add-authentication/chooseapiappurl.png)

2. Ajoutez `/api/contacts/get/` à l'URL dans la barre d'adresse du navigateur.

	Par exemple, si l'URL de l'application API est :

    	https://microsoft-apiappeeb5bdsasd744e188be7fa26f239bd4b.azurewebsites.net/

	L'URL complète serait :

    	https://microsoft-apiappeeb5bdsasd744e188be7fa26f239bd4b.azurewebsites.net/api/contacts/get/

	Des navigateurs différents gèrent les appels d'API différemment. L'image montre un appel réussi à partir d'un navigateur Chrome.

	![Chrome Get response](./media/app-service-api-dotnet-add-authentication/chromeget.png)

2. Enregistrez l'URL que vous avez utilisée ; vous l'utiliserez de nouveau plus tard dans le didacticiel.

## Protéger l'application API

Quand vous avez déployé votre application API, vous l'avez déployée dans un groupe de ressources. Vous pouvez ajouter des applications web et d'autres applications API au même groupe de ressources, et chaque application API dans le groupe de ressources peut avoir l'un des trois paramètres d'accessibilité suivants :
<!--todo: diagramme indiquant différents paramètres d'accessibilité-->

- **Public (anonyme)** : tout le monde peut appeler l'application API en dehors du groupe de ressources sans être connecté.
- **Public (authentifié)** : seuls les utilisateurs authentifiés sont autorisés à appeler l'application API en dehors du groupe de ressources.
- **Interne** : seules d'autres applications API ou web dans le même groupe de ressources sont autorisées à appeler l'application API.

Quand Visual Studio a créé le groupe de ressources pour vous, il a également créé une *passerelle*.  Une passerelle est une application web spéciale qui gère toutes les demandes destinées aux applications API dans le groupe de ressources.

Quand vous accédez au panneau du groupe de ressources dans le [portail Azure], vous pouvez voir l'application API et la passerelle dans le diagramme.

![Resource group diagram](./media/app-service-api-dotnet-add-authentication/rgdiagram.png)

Pour configurer votre application API pour accepter uniquement les demandes authentifiées, vous devez définir le paramètre d'accessibilité **Public (authentifié)** et configurer la passerelle pour exiger l'authentification auprès d'un fournisseur comme Azure Active Directory, Google ou Facebook.

1. Revenez au panneau **Application API** pour l'application API à protéger.

2. Dans le panneau **Application API**, cliquez sur **Paramètres**, puis sur **Paramètres de l'application**.

	![Click Settings](./media/app-service-api-dotnet-add-authentication/clicksettings.png)

	![Click Application settings](./media/app-service-api-dotnet-add-authentication/clickbasicsettings.png)

3. Dans le panneau **Paramètres de l'application**, remplacez le **Niveau d'accès** par **Public (authentifié)**, puis cliquez sur **Enregistrer**.

	![Click Basic settings](./media/app-service-api-dotnet-add-authentication/setpublicauth.png)

	Vous avez maintenant protégé l'application API contre tout accès non authentifié. Vous devez ensuite configurer la passerelle pour spécifier le fournisseur d'authentification à utiliser.

4. Faites défiler l'écran vers la gauche pour revenir au panneau **Application API**, puis cliquez sur le lien vers la passerelle.

	![Click gateway](./media/app-service-api-dotnet-add-authentication/gateway.png)

7. Dans le panneau **Passerelle**, cliquez sur **Paramètres**, puis sur **Identité**.

	![Click Settings](./media/app-service-api-dotnet-add-authentication/clicksettingsingateway.png)

	![Click Identity](./media/app-service-api-dotnet-add-authentication/clickidentity.png)

	Dans le panneau **Identité**, vous pouvez accéder à différents panneaux pour configurer l'authentification avec Azure Active Directory et plusieurs autres fournisseurs.

	![Identity blade](./media/app-service-api-dotnet-add-authentication/identityblade.png)
  
3. Choisissez le fournisseur d'identité à utiliser et suivez les étapes de l'article correspondant pour configurer votre application API avec ce fournisseur. Ces articles ont été écrits pour des applications mobiles, mais les procédures sont les mêmes pour les applications API. Certaines des procédures vous demandent d'utiliser l'[ancien portail]. 

 - [Compte Microsoft](app-service-mobile-how-to-configure-microsoft-authentication-preview.md)
 - [Connexion Facebook](app-service-mobile-how-to-configure-facebook-authentication-preview.md)
 - [Connexion Twitter](app-service-mobile-how-to-configure-twitter-authentication-preview.md)
 - [Connexion Google](app-service-mobile-how-to-configure-google-authentication-preview.md)
 - [Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication-preview.md)

À titre d'exemple, les captures d'écran suivantes montrent ce que vous devriez voir dans les pages de l'[ancien portail] et les panneaux du [portail Azure] une fois que vous avez configuré l'authentification Azure Active Directory.

Dans le portail Azure, le panneau **Azure Active Directory** a un **ID client** qui provient de l'application que vous avez créée sous l'onglet Azure Active Directory de l'ancien portail et **Clients autorisés** contient votre client Azure Active Directory (par exemple, " contoso.onmicrosoft.com ").

![Azure Active Directory blade](./media/app-service-api-dotnet-add-authentication/tdinaadblade.png)

Dans l'ancien portail, l'onglet **Configurer** pour l'application créée sous l'onglet **Azure Active Directory** contient **URL de connexion**, **URI ID d'application** et **URL de réponse** qui proviennent du panneau **Azure Active Directory** du portail Azure.

![Old portal AAD](./media/app-service-api-dotnet-add-authentication/oldportal1.png)

![Old portal AAD](./media/app-service-api-dotnet-add-authentication/oldportal2.png)

![Old portal AAD](./media/app-service-api-dotnet-add-authentication/oldportal3.png)

![Old portal AAD](./media/app-service-api-dotnet-add-authentication/oldportal4.png)

(L'URL de réponse dans l'image montre deux fois la même URL, une fois avec `http:` et une fois avec `https:`.)

## Vérifier que l'authentification fonctionne 

1. Ouvrez une fenêtre de navigateur et, dans la barre d'adresse, entrez l'URL qui appelle la méthode Get de votre application API, comme vous l'avez fait précédemment.

	Cette fois, la tentative d'accès à l'application API génère un message d'erreur.

	![Chrome Get response fail](./media/app-service-api-dotnet-add-authentication/chromegetfail.png)

2. Dans le navigateur, accédez à l'URL de connexion : 

    	http://[resourcegroupname]gateway.azurewebsites.net/login/[providername]

	Par exemple, si vous avez nommé votre groupe de ressources myfirstrg et configuré la passerelle pour l'authentification Azure Active Directory, l'URL serait la suivante :

    	http://myfirstrggateway.azurewebsites.net/login/aad

	Notez que, contrairement à l'URL précédente, celle-ci n'inclut pas le nom de votre application API :  la passerelle sert à vous authentifier et non à authentifier l'application API.  La passerelle gère l'authentification de toutes les applications API dans le groupe de ressources.

	(Si vous avez un problème de connexion, essayez d'ouvrir une fenêtre privée ou incognito.)

3. Entrez vos informations d'identification quand le navigateur affiche une page de connexion. 
 
	Si vous avez configuré la connexion Azure Active Directory, employez l'un des utilisateurs répertoriés sous l'onglet **Utilisateurs** pour l'application que vous avez créée sous l'onglet Azure Active Directory de l'[ancien portail], comme admin@contoso.onmicrosoft.com.

	![AAD users](./media/app-service-api-dotnet-add-authentication/aadusers.png)

	![Login page](./media/app-service-api-dotnet-add-authentication/ffsignin.png)

4. Quand le message " Connexion terminée " s'affiche, entrez à nouveau l'URL à la méthode Get de votre application API.

	Cette fois, l'appel réussit, car vous êtes authentifié. La passerelle reconnaît que vous êtes un utilisateur authentifié et transmet votre demande à votre application API.

	![Login completed](./media/app-service-api-dotnet-add-authentication/logincomplete.png)

	![Chrome Get response](./media/app-service-api-dotnet-add-authentication/chromeget.png)
	<!--todo:à remplacer par l'image affichant des noms fictifs-->

## Utiliser Postman pour envoyer une demande Post

Quand vous vous connectez à la passerelle, la passerelle renvoie un jeton d'authentification.  Ce jeton doit être inclus avec toutes les demandes provenant de sources externes qui passent par la passerelle. Quand vous accédez à une API avec un navigateur, le navigateur stocke généralement le jeton dans un cookie et l'envoie avec tous les appels ultérieurs à l'API.

Afin de voir ce qui se passe en arrière-plan, vous utilisez dans cette section un outil de navigateur pour créer et soumettre une demande Post, vous obtenez le jeton d'autorisation du cookie et l'incluez dans un en-tête HTTP.

Ces instructions indiquent comment utiliser l'outil Postman dans le navigateur Chrome, mais vous pouvez effectuer la même opération avec n'importe quel outil client REST et outil de développement de navigateur.

1. Dans une fenêtre de navigateur Chrome, effectuez les étapes indiquées dans la section précédente pour vous authentifier et ouvrez les Outils de développement (F12).

	![Go to Resources tab](./media/app-service-api-dotnet-add-authentication/resources.png)

3. Sous l'onglet **Ressources** des Outils de développement Chrome, recherchez les cookies pour votre passerelle, puis cliquez trois fois sur la valeur du cookie **x-zumo-auth** pour le sélectionner en entier.

	**Remarque :** assurez-vous d'obtenir l'intégralité de la valeur du cookie. Si vous double-cliquez, vous n'en obtiendrez que la première partie.

5. Cliquez avec le bouton droit sur la **Valeur** du cookie **x-zumo-auth**, puis cliquez sur **Copier**.

	![Copy auth token](./media/app-service-api-dotnet-add-authentication/copyzumotoken.png)

4. Installez l'extension Postman dans votre navigateur Chrome si ce n'est pas déjà fait.

6. Ouvrez l'extension Postman.

7. Dans le champ URL de la demande, entrez l'URL à la méthode Get de votre application API que vous avez utilisée précédemment, mais sans get/ à la fin.
 
		http://[apiappurl]/api/contacts
    
8. Cliquez sur **En-têtes**, puis ajoutez un en-tête *x-zumo-auth*. Collez la valeur du jeton du Presse-papiers dans le champ **Valeur**.

9. Ajoutez un en-tête *Content-Type* avec la valeur *application/json*.

10. Cliquez sur **form-data**, puis ajoutez une clé *contact* avec la valeur suivante :

		{   "Id": 0,   "Name": "Li Yan",   "EmailAddress": "yan@contoso.com" }

11. Cliquez sur Envoyer.

	L'application API retourne une réponse *201 Créé*.

	![Add headers and body](./media/app-service-api-dotnet-add-authentication/addcontact.png)

12. Pour vérifier que cette demande ne fonctionnerait pas sans le jeton d'authentification, supprimez l'en-tête d'authentification et cliquez à nouveau sur Envoyer.

	Vous obtenez une réponse *403 Interdit*.

	![403 Forbidden response](./media/app-service-api-dotnet-add-authentication/403forbidden.png)

## Étapes suivantes

Vous avez vu comment protéger une application API Azure en exigeant une authentification de fournisseur de réseau social ou Azure Active Directory. Pour plus d'informations, consultez [Que sont les applications API ?](app-service-api-apps-why-best-platform.md) 

[ancien portail]: https://manage.windowsazure.com/
[portail Azure]: https://portal.azure.com/


<!--HONumber=49-->
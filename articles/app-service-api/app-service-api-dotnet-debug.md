<properties 
	pageTitle="Déboguer une application API dans Azure App Service" 
	description="Découvrez comment déboguer une application API dans Azure App Service avec Visual Studio." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# Déboguer une application API dans Azure App Service

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

## Vue d'ensemble

Dans ce didacticiel, vous déboguez le code de l’API web ASP.NET configuré pour une exécution au sein d’une [application API](app-service-api-apps-why-best-platform.md) dans [Azure App Service](../app-service/app-service-value-prop-what-is.md). Vous déboguez le code lors de son exécution locale et lors de son exécution à distance dans Azure.

Le didacticiel fonctionne avec l’application API que vous avez [créée](app-service-dotnet-create-api-app.md) et [déployée](app-service-dotnet-deploy-api-app.md) dans les didacticiels précédents de cette série.

## Déboguer une application API à distance 

Pour activer le débogage à distance, vous devez déployer une version de débogage dans Azure.

1. Dans l’**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit de la souris sur le projet que vous avez déployé dans le cadre du [didacticiel précédent](app-service-dotnet-deploy-api-app.md), puis sélectionnez **Publier**.

2. Dans la boîte de dialogue **Publier le site Web**, sélectionnez l’onglet **Paramètres** et sélectionnez la configuration de version **Debug**.

4. Cliquez sur **Publier**.

	![Publier le projet](./media/app-service-api-dotnet-debug/rd-debug-publish.png)

	Une fenêtre de navigateur s'ouvre sur l'URL de base de votre application API.

4. Dans la barre d’adresse du navigateur, ajoutez swagger/ à la fin de l’URL et appuyez sur Entrée.

	Cette étape suppose que vous avez activé l’interface utilisateur Swagger comme indiqué dans le didacticiel [Créer](app-service-dotnet-create-api-app.md).

	![Interface utilisateur Swagger](./media/app-service-api-dotnet-debug/rd-swagger-ui.png)

5. Revenez à Visual Studio, puis cliquez sur **Affichage > Explorateur de serveurs**.

6. Dans l’**Explorateur de serveurs**, développez le nœud **Azure > App Service**.

7. Recherchez le groupe de ressources que vous avez créé ou sélectionné quand vous avez déployé votre application API.

8. Dans ce groupe de ressources, cliquez avec le bouton droit sur le nœud de votre application API et sélectionnez **Attacher le débogueur**.

	![Attachement du débogueur](./media/app-service-api-dotnet-debug/rd-attach-debugger.png)

	Le débogueur distant tente de se connecter. Dans certains cas, il est possible que vous deviez cliquer à nouveau sur **Attacher le débogueur** pour établir une connexion ; en cas d’échec, essayez à nouveau.

	![Attachement du débogueur](./media/app-service-api-dotnet-debug/rd-attaching.png)

9. Une fois la connexion établie, ouvrez le fichier **ContactsController.cs** dans le projet d’application API, puis ajoutez un point d’arrêt dans la méthode `Get`.

	![Application des points d’arrêt au contrôleur](./media/app-service-api-dotnet-debug/rd-breakpoints.png)

10. Revenez à la session de navigateur, cliquez sur le terme **Get** pour afficher le schéma de l’objet *Contact*, puis cliquez sur **Faire un essai**. Visual Studio arrête l'exécution du programme au point d'arrêt et vous pouvez déboguer la logique de votre contrôleur.

	![Faites un essai](./media/app-service-api-dotnet-debug/rd-try-it-out.png)

## Déboguer une application API en local 

Il peut arriver lorsque vous souhaitiez déboguer votre application API localement ; par exemple, si des allers-retours vers le serveur Azure ralentissent le débogage. Cette section indique comment déboguer votre application API en local, en utilisant l’interface utilisateur Swagger en tant que client de test.

2. Dans votre navigateur, accédez au [portail Azure en version préliminaire](https://portal.azure.com/). 

3. Cliquez sur le bouton **Parcourir** dans la barre latérale et sélectionnez **API Apps**.

	![Parcourir les options sur le portail Azure](./media/app-service-api-dotnet-debug/ld-browse.png)

4. Sélectionnez l’application API que vous avez créée dans la liste des applications API de votre abonnement.

	![Liste d’applications API](./media/app-service-api-dotnet-debug/ld-api-app-list.png)

5. Dans le volet de l’application API, cliquez sur **Hôte d’application API**.

	![Hôte d’application API](./media/app-service-api-dotnet-debug/ld-api-app-blade-api-app-host.png)

6. Dans le panneau de l’hôte d’application API, cliquez sur **Tous les paramètres**.

	![Hôte d’application API - Tous les paramètres](./media/app-service-api-dotnet-debug/ld-api-app-host-all-settings.png)

7. Dans le panneau **Paramètres**, cliquez sur **Paramètres de l’application**.

	![Hôte d’application API - Paramètres d’application](./media/app-service-api-dotnet-debug/ld-application-settings.png)

8. Faites défiler le contenu du panneau **Paramètres de l’application web** jusqu’à la section **Paramètres de l’application**.

	![Hôte d’application API - Paramètres d’application pour le débogage local](./media/app-service-api-dotnet-debug/ld-app-settings-for-local-debugging.png)

1. Dans Visual Studio, ouvrez le fichier *web.config* du projet d’application API.

9. Dans **Paramètres de l’application**, ajoutez chacune des clés et des valeurs suivantes dans la section **appSettings** du fichier *web.config*.
	- **EMA\_MicroserviceId**
	- **EMA\_Secret**
	- **EMA\_RuntimeUrl**

	Lorsque vous avez terminé, la section **appSettings** de votre fichier *web.config* doit ressembler à la capture d’écran suivante.

	![Hôte d’application API - Paramètres d’application pour le débogage local](./media/app-service-api-dotnet-debug/ld-debug-settings.png)

	**Remarque :** les valeurs *EMA\_* que vous avez ajoutées à votre fichier *web.config* dans cette section contiennent des données sensibles sur les autorisations. Par conséquent, il est recommandé d'éviter d'enregistrer ces valeurs dans un référentiel de contrôle de code source public. Pour en savoir plus, consultez la section [Meilleures pratiques portant sur le déploiement de mots de passe et autres données sensibles dans ASP.NET et Azure App Service](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

10. Placez un point d’arrêt dans le code du contrôleur de votre application API dans la méthode `Get`.

11. Appuyez sur F5 pour démarrer une session de débogage Visual Studio.
 
13.  Si le niveau d’accès de l’application API est défini sur **Public (anonyme)**, vous pouvez utiliser la page de l’interface utilisateur Swagger à tester.

	* Lorsque le navigateur charge la page, vous voyez une page d’erreur HTTP 403. Dans la barre d’adresse du navigateur, ajoutez */swagger* à la fin de l’URL et appuyez sur Entrée.

	* Une fois l’interface utilisateur Swagger chargée, cliquez sur le terme **Get** pour afficher le schéma de l’objet Contact, puis cliquez sur **Faire un essai**.

		Visual Studio arrête l'exécution du programme à votre point d'arrêt et vous pouvez déboguer la logique de votre contrôleur.

14.	Si le niveau d’accès de l’application API a la valeur **Public (authentifié)**, vous avez besoin de vous authentifier et d’utiliser un outil de navigation en suivant les procédures indiquées dans [Protéger une application API](app-service-api-dotnet-add-authentication.md#use-postman-to-send-a-post-request) pour une demande POST, comme suit :

	* Accédez à l’URL de connexion à la passerelle et entrez les informations d’identification pour vous connecter.
	* Récupérez la valeur du jeton Zumo à partir du cookie x-zumo-auth.
	* Ajoutez un en-tête x-zumo-auth à votre demande et définissez sa valeur par rapport à la valeur du cookie x-zumo-auth.
	* Envoyez la demande.

	**Remarque :** en mode local, Azure ne peut pas contrôler l’accès à l’application API pour vous assurer que seuls les utilisateurs authentifiés peuvent exécuter ses méthodes. Sur Azure, tout le trafic destiné à l’application API est acheminé via la passerelle, et la passerelle ne transmet pas les demandes non authentifiées. Il n’y a pas de redirection en mode local, ce qui signifie que les demandes non authentifiées peuvent accéder à l’application API. L’importance de l’authentification telle qu’elle est décrite ci-dessus vient du fait que vous pouvez exécuter correctement le code lié à l’authentification dans l’application API, comme le code qui récupère des informations sur l’utilisateur connecté. Pour plus d’informations sur la manière dont la passerelle gère l’authentification pour les applications API, consultez la page [Authentification pour les applications API et les applications mobiles](../app-service/app-service-authentication-overview.md#azure-app-service-gateway).

## Étapes suivantes

Dans ce didacticiel, vous avez vu comment déboguer des applications API.

Pour plus d’informations sur la résolution des problèmes, consultez la section [Dépanner une application web dans Azure App Service à l’aide de Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md). Les applications API sont des applications web qui présentent des fonctionnalités supplémentaires pour l’hébergement des services web. Vous pouvez donc utiliser les mêmes outils de débogage et de résolution des problèmes pour les applications API que ceux que vous utilisez pour les applications web.

<!---HONumber=AcomDC_0128_2016-->
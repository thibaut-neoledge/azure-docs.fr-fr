<properties
	pageTitle="Didacticiel : Créer une API personnalisée à l’aide d’une application web ASP.Net dans PowerApps et les flux logiques | Microsoft Azure"
	description="Didacticiel Application web ASP.Net pour créer une API personnalisée dans PowerApps et les flux logiques"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="sunaysv"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/12/2016"
   ms.author="mandia"/>

# Didacticiel : Créer une API web AAD protégée personnalisée pour PowerApps et les flux logiques

Ce didacticiel vous montre comment créer une API web ASP.Net, l’héberger sur Azure Webapps, activer l’authentification AAD (via l’authentification simple), puis inscrire la nouvelle API web dans PowerApps et des flux logiques.

>[AZURE.IMPORTANT] Cette rubrique a été déplacée vers powerapps.microsoft.com dans [Didacticiel : Créer une API web AAD protégée personnalisée pour PowerApps et les flux](https://powerapps.microsoft.com/tutorials/customapi-web-api-tutorial/). Accédez à PowerApps pour la dernière version. Ce lien Azure est archivé.

## Ce dont vous avez besoin pour commencer

* Un abonnement Azure
* Un compte PowerApps
* Visual Studio 2013 ou une version ultérieure

## Étape 1 : Créer une API web et la déployer sur Azure
1. Ouvrez Visual Studio et créez une application web ASP.NET C# : ![](./media/powerapps-web-api-tutorial/newwebapp.png "Nouvelle application web")

2. Dans l’écran suivant, sélectionnez le modèle d’API web, puis sélectionnez **Aucune authentification** : ![](./media/powerapps-web-api-tutorial/noauth.png "Aucune autorisation")

	>[AZURE.IMPORTANT] Assurez-vous de définir l’authentification sur « Aucune authentification ».

3. Une fois que vous avez créé le projet, vous devez créer l’API web pour vos ressources. Dans ce didacticiel, nous ne voyons pas les étapes permettant de créer une API web.

4. Ensuite, créez un fichier Swagger pour l’API web. Pour cela, ouvrez simplement la __console du Gestionnaire de package__ et installez __Swashbuckle__ : ![](./media/powerapps-web-api-tutorial/swashbuckle-console.png "Console Swashbuckle")

5. Après avoir installé et activé Swashbuckle, accédez aux points de terminaison des documents et de l’interface utilisateur de Swagger, respectivement : **<your-root-url>/swagger/docs/v1**

 	**<your-root-url>/swagger**

6. Quand vous êtes prêt, publiez votre API web sur Azure. Pour la publier dans Visual Studio, accédez à **BUILD**, puis sélectionnez **PUBLIER**.

7. Récupérez le fichier Swagger (.json) créé dans ***https://\<azure-webapp-url>/swagger/docs/v1***.

	> [AZURE.IMPORTANT] Un document Swagger n’est pas valide s’il contient une valeur operationid(s) en double. Si vous utilisez l’exemple de modèle C#, « Values\_Get » est spécifié deux fois pour operation-id. Remplacez l’une des instances par « Value\_Get ».


Vous pouvez télécharger le fichier Swagger utilisé dans ce didacticiel [ici][6]. Avant de l’utiliser, vous devez remplacer ou supprimer les commentaires existants. Les commentaires commencent par `//`.

## Étape 2 : Configurer l’authentification AAD

Ce didacticiel part du principe que vous savez créer une application AAD dans Azure. Pour en savoir plus sur la création d’une application AAD, consultez le [didacticiel d’Azure Resource Manager](powerapps-azure-resource-manager-tutorial.md). Nous avons besoin de deux applications AAD pour ce didacticiel.

1. La première application AAD sert à sécuriser l’API web. Nommez-la **webAPI**.
2. La deuxième application AAD permet de sécuriser l’inscription de l’API personnalisée et d’obtenir l’accès délégué à l’API web protégée par la première application. Nommez cette application **webAPI-customAPI**.
3. Configurez **webAPI** comme indiqué ci-dessous :

  1. URL de connexion : ***https://login.windows.net***
  2. URI ID d’application : ***https://\<your-root-url>*** (correspond en général à l’URL de votre site web déployé sur Azure)
  3. URL de réponse : ***https://\<your-root-url>/.auth/login/aad/callback***
  
	>[AZURE.IMPORTANT] Notez l’ID client de cette application, car vous en aurez besoin plus tard.

4. Configurez **webAPI-customAPI** comme indiqué ci-dessous :
  
  1. URL de connexion : **https://login.windows.net**
  2. URI ID d’application : ***n’importe quelle URL unique***
  3. URL de réponse : ***https://msmanaged-na.consent.azure-apim.net/redirect***
  4. Ajoutez les autorisations nécessaires pour avoir l’accès délégué à l’API web.
  5. Notez l’ID client de cette application, car vous en aurez également besoin plus tard.
  6. Générez une clé et conservez-la en lieu sûr, car vous en aurez besoin plus tard.

>[AZURE.IMPORTANT] Les deux applications doivent être dans le même répertoire.

## Étape 3 : Configurer une authentification simple pour votre application web

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à l’application web que vous avez déployée à l’**étape 1** de cette rubrique.
2. Dans **Paramètres**, sélectionnez **Authentification/Autorisation**.
3. Activez **Authentification App Service**, puis sélectionnez **Azure Active Directory**. Dans le panneau suivant, sélectionnez **Express**.
4. Cliquez sur **Sélectionner une application AD existante**, puis sélectionnez la première application AAD que vous avez créée à l’étape 2. Dans notre exemple, sélectionnez **webAPI**.

Votre application web doit normalement être configurée avec l’authentification AAD.

## Étape 4 : Configurer l’API personnalisée 

1. Nous devons légèrement modifier notre fichier Swagger pour ajouter l’objet `securityDefintions` et spécifier l’authentification AAD utilisée pour l’application web. Pour cela, ajoutez les lignes de code suivantes :

	```javascript
  "host": "<your-root-url>",
  "schemes": [
    "https"						//Change scheme to https 
  ],
  "securityDefinitions": {
    "AAD": {
      "type": "oauth2",
      "flow": "implicit",
      "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
      "scopes": []
    }
  },
	```

2. Accédez au [portail web][1] PowerApps et ajoutez une API personnalisée. Pour en savoir plus sur ces étapes, consultez [Utiliser des API personnalisées dans les flux logiques et PowerApps](powerapps-register-custom-api.md).

3. Une fois que vous avez chargé votre fichier Swagger, l’Assistant détecte automatiquement que vous utilisez l’authentification AAD pour votre API web.

4. Configurez l’authentification AAD pour votre API personnalisée :

  1. ID client : ***ID client de l’application webAPI-CustomAPI*** noté au point 4.5 de l’**étape 2** (plus haut dans cette rubrique)
  2. Clé secrète : ***Clé générée par webAPI-CustomAPI*** notée au point 4.6 de l’**étape 2** (plus haut dans cette rubrique)
  3. URL de connexion : ***https://login.windows.net***
  4. URI de ressource : ***ID client de l’application webAPI*** noté au point 3.4 de l’**étape 2** (plus haut dans cette rubrique)

5. Sélectionnez **Créer**, puis essayez de créer une connexion sur l’API personnalisée. Si tout est correctement configuré, vous devez être en mesure de vous connecter.

Pour en savoir plus sur la création d’applications PowerApps et de flux logiques, consultez les rubriques suivantes :

- [Connect to Office 365, Twitter, and Microsoft Translator (Se connecter à Office 365, Twitter et Microsoft Translator)][5]
- [Afficher les données d’Office 365][4]
- [Créer une application à partir de données][3]
- [Prise en main des flux logiques][2]

Envoyez vos questions ou commentaires à l’adresse [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com).

<!--Reference links in article-->
[1]: https://web.powerapps.com
[2]: https://powerapps.microsoft.com/tutorials/get-started-logic-flow/
[3]: https://powerapps.microsoft.com/tutorials/get-started-create-from-data/
[4]: https://powerapps.microsoft.com/tutorials/show-office-data/
[5]: https://powerapps.microsoft.com/tutorials/powerapps-api-functions/
[6]: http://pwrappssamples.blob.core.windows.net/samples/webAPI.json

<!---HONumber=AcomDC_0713_2016-->
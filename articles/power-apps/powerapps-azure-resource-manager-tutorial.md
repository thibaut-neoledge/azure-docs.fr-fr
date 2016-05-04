<properties
	pageTitle="Didacticiel : Créer une API personnalisée à l'aide d’Azure Resource Manager dans PowerApps et les flux logiques | Microsoft Azure"
	description="Didacticiel Azure Resource Manager pour créer une API personnalisée dans PowerApps et les flux logiques"
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
   ms.date="04/12/2016"
   ms.author="mandia"/>


# Didacticiel : Créer une API ARM AAD protégée personnalisée pour PowerApps et des flux logiques 

Ce didacticiel vous guide tout au long des étapes nécessaires à l'inscription d'un fichier Swagger qui décrit l'[API ARM][6], puis vous explique comment vous connecter à l'API personnalisée dans PowerApps.

## Ce dont vous avez besoin pour commencer

- Un abonnement Azure
- Un compte PowerApps

## Une authentification active dans Azure Active Directory

Nous devons d'abord créer une application Azure Active Directory (AAD) qui effectue l'authentification lors de l'appel du point de terminaison API ARM.

1. Pour créer une application AAD, connectez-vous à votre [abonnement Azure][7], puis accédez à **Active Directory** : ![](./media/powerapps-azure-resource-manager-tutorial/azureaad.png "Azure AAD")  

2. Sur cette page, sélectionnez le répertoire dans lequel vous souhaitez créer votre application AAD. Sélectionnez le répertoire, l'onglet **Applications**, puis **Ajouter** : ![](./media/powerapps-azure-resource-manager-tutorial/azureapplication.png "Application Azure")

3. Entrez le nom de votre application, sélectionnez **Application web et/ou API web**, puis **Suivant** : ![](./media/powerapps-azure-resource-manager-tutorial/newapplication.png "Nouvelle application")

4. Dans **URL de connexion**, entrez : **http://login.windows.net**. Dans **URI ID d'application**, entrez un URI unique. Sélectionnez **Terminé** : ![](./media/powerapps-azure-resource-manager-tutorial/newapplication2.png "Nouvelle seconde application")

5. Une fois l’application AAD créée, accédez à l’onglet **Configurer**. Dans cet onglet, nous configurons les autorisations de l'application.

6. Sous **Autorisations pour d'autres applications**, sélectionnez **Ajouter une application**, puis entrez les autorisations suivantes, comme indiqué : ![](./media/powerapps-azure-resource-manager-tutorial/permissions.png "Autorisations")

	Assurez-vous de déléguer les autorisations nécessaires pour votre application : ![](./media/powerapps-azure-resource-manager-tutorial/permissions2.png "Autorisations de délégué")

7. Sous **Clés**, sélectionnez une durée. **Copiez et enregistrez la clé dans un emplacement sûr** ; nous aurons besoin de cette clé plus tard. Prenez également note de l’__ID client__ : ![](./media/powerapps-azure-resource-manager-tutorial/configurekeys.png "Configurer les clés")

8. Sous **Authentification unique**, entrez l'URL suivante dans __reply-URL__ : https://msmanaged-na.consent.azure-apim.net/redirect : ![](./media/powerapps-azure-resource-manager-tutorial/redirecturl.png "URL de redirection")

9. **Enregistrez** vos modifications. **Copiez et enregistrez la clé dans un emplacement sûr**.

## Ajouter la connexion dans PowerApps ou des flux logiques

Maintenant que l'application AAD est configurée, nous allons ajouter l'API personnalisée.

1. Ouvrez le [portail web PowerApps][1], accédez à l’onglet **Connexions** puis sélectionnez __Ajouter une connexion__ dans le coin supérieur droit : ![](./media/powerapps-azure-resource-manager-tutorial/createnewconnection.png "Création d'API personnalisée")  

2. Sélectionnez __Ajouter une API personnalisée__ : ![](./media/powerapps-azure-resource-manager-tutorial/connecttocustomapi.png "Création d'API personnalisée")

3. Chargez le fichier Swagger ARM, disponible en [téléchargement][8] \: ![](./media/powerapps-azure-resource-manager-tutorial/createcustom.png "Création d'API personnalisée")

4. Sur l'écran suivant, étant donné que notre fichier Swagger est configuré pour utiliser l'authentification AAD, nous devons entrer l'ID de client AAD, la clé secrète du client (la **clé** que vous avez stockée dans un emplacement sûr) et d'autres paramètres : ![](./media/powerapps-azure-resource-manager-tutorial/oauthsettings.png "Paramètres OAuth")

5. Si tout est correctement configuré, vous pouvez utiliser l'API personnalisée ARM en créant une connexion, puis en la référençant lors de la création de votre PowerApp ou flux logique : ![](./media/powerapps-azure-resource-manager-tutorial/createdcustomapi.png "CustomAPI créée")

Vous pouvez accéder de la même façon aux données exposées à l'aide des API RESTful et authentifiées à l'aide d'AAD OAuth2.

Pour plus d’informations sur la création de PowerApps et de flux logiques, consultez les rubriques suivantes :

- [Se connecter à Office 365, Twitter et Microsoft Translator][5]
- [Afficher les données d'Office 365 ][4]
- [Créer une application à partir de données][3]
- [Prise en main des flux logiques][2]

Envoyez vos questions ou commentaires sur les API personnalisées à l’adresse [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com).


<!--Reference links in article-->
[1]: https://web.powerapps.com
[2]: https://powerapps.microsoft.com/tutorials/get-started-logic-flow/
[3]: https://powerapps.microsoft.com/tutorials/get-started-create-from-data/
[4]: https://powerapps.microsoft.com/tutorials/show-office-data/
[5]: https://powerapps.microsoft.com/tutorials/powerapps-api-functions/
[6]: https://msdn.microsoft.com/library/azure/dn790568.aspx
[7]: https://manage.windowsazure.com
[8]: http://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json

<!---HONumber=AcomDC_0420_2016-->
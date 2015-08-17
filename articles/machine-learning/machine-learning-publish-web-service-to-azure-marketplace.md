<properties 
	pageTitle="Publication du service web Machine Learning sur Azure Marketplace | Microsoft Azure" 
	description="Publication du service web Azure Machine Learning sur Azure Marketplace" 
	services="machine-learning" 
	documentationCenter="" 
	authors="LuisCabrer" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/12/2015" 
	ms.author="luisca"/>

# Publication du service Web Azure Machine Learning sur Azure Marketplace 

Azure Marketplace offre la possibilité de publier des services web Azure Machine Learning sous forme de services payants ou gratuits que des clients externes pourront consommer. Cet article propose une présentation du processus, avec des liens vers les consignes pour vous aider à démarrer. À l’aide de ce processus, vous pouvez mettre vos services Web à disposition d’autres développeurs, qui pourront les consommer dans leurs applications.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Présentation du processus de publication 

Les étapes suivantes vous permettront de publier un service Web Azure Machine Learning sur Azure Marketplace :

1. Créez et publiez un service Request-Response (Request-Response Service, service de requête-réponse) Machine Learning
2. Déployez le service en production et obtenez les informations du point de terminaison OData et de la clé d’API.
3. Utilisez l’URL du service Web à publier sur [Azure Marketplace (Data Market)](https://publish.windowsazure.com/workspace/) 
4. Une fois soumise, votre offre est évaluée et doit être approuvée avant que vos clients puissent commencer à l'acheter. Le processus de publication peut prendre quelques jours ouvrés. 

## Procédure
###Étape 1 : créez et publiez un RRS (Request-Response Service, service de requête-réponse) Machine Learning###
 Si vous ne l’avez pas encore fait, consultez ce [guide](machine-learning-walkthrough-5-publish-web-service.md).

###Étape 2 : déployez le service en production et obtenez les informations de point de terminaison OData et de la clé d’API.###
1. Depuis le [portail de gestion Azure](http://manage.windowsazure.com), sélectionnez l’option **MACHINE LEARNING** (apprentissage automatique) dans la barre de navigation de gauche, puis sélectionnez votre espace de travail. 

2. Cliquez sur l’onglet **WEB SERVICES** (services Web) et sélectionnez le service Web que vous souhaitez publier sur le marché.

	![Azure Marketplace][workspace]

3. Sélectionnez le point de terminaison que vous souhaitez voir consommé par le marché. Si vous n’avez créé aucun point de terminaison supplémentaire, vous pouvez sélectionner le point de terminaison **Default** (par défaut).

4. Une fois que vous avez cliqué sur le point de terminaison, vous voyez la **API KEY** (clé API). Vous aurez besoin de cette information par la suite, lors de l’étape 3. Faites-en une copie.

	![Azure Marketplace][apikey]

5. Cliquez sur la méthode **REQUEST/RESPONSE** (requête/réponse). Pour l’instant, nous ne prenons pas en charge la publication de services d’exécution par lots sur le marketplace. Vous serez dirigé sur la page d’aide API pour la méthode Request/Response.

6. Copiez l’**OData Endpoint Address** (l’adresse du point de terminaison OData), vous aurez besoin de ces informations ultérieurement, lors de l’étape 3.

	![Azure Marketplace][odata]




déployez le service en production.



###Étape 3 : utilisez l’URL du service Web à publier sur Azure Marketplace (DataMarket)###

1.  Accédez à [Azure Marketplace (Data Market)](http://datamarket.azure.com/home) 
2.  Cliquez sur le lien **Publish** (Publier) en haut de la page. Vous serez dirigé vers le [portail de publication Microsoft Azure](https://publish.windowsazure.com)
3.  Cliquez sur la section **publishers** (éditeurs) pour vous inscrire en tant qu’éditeur.
4.	Lors de la création d'une offre, sélectionnez **Services de données**, puis cliquez sur **Créer un nouveau service de données**. 
 
	![Azure Marketplace][image1]

	<br />


5.	Sous **Plans**, indiquez les informations relatives à votre offre, y compris un plan de tarification. Vous pouvez choisir de proposer un service payant ou gratuit. Pour être payé, vous devez indiquer des informations de paiement, notamment vos renseignements bancaires et fiscaux.

6.	Sous **Marketing**, vous devez fournir des informations relatives à votre offre, comme son titre et sa description.

7.	Sous **Tarification**, vous pouvez fixer le prix de vos plans pour des pays donnés, ou laisser le système décider lui-même du prix de votre offre.

8. Sur l’onglet **Service de données**, cliquez sur **Service Web** pour la **Source de données**.

	![Azure Marketplace][image2]

9.	Obtenez l’URL du service Web et la clé d’API à partir du portail de gestion Azure, tel qu’expliqué dans l’étape 2 ci-dessus.

10.	Dans la boîte de dialogue de configuration du service de données Marketplace, collez l’adresse du point de terminaison OData dans la zone de texte **URL du service**.

11. Pour l’**authentification**, choisissez **En-tête** comme **Schéma d’authentification**.

	- Saisissez « Autorisation » comme **nom d’en-tête**.
	- Pour la **valeur d’en-tête**, entrez « Titulaire » (sans les guillemets), appuyez sur la barre d’**espace** et collez la clé d’API.
	- Cochez la case **Ce service est OData**.
	- Cliquez sur **Tester la connexion** pour tester la connexion.

12.	Sous **Catégories**, assurez-vous que l’option **Apprentissage automatique** est sélectionnée.

13. Lorsque vous avez terminé la saisie de toutes les métadonnées concernant votre offre, cliquez sur **Publier**, puis **Push dans l’environnement intermédiaire**. À ce stade, vous recevrez une notification pour chaque problème restant à corriger.

14. Après avoir vérifié l’achèvement de tous les problèmes en suspens, cliquez sur **Demander l’approbation pour pousser vers la Production**. Le processus de publication peut prendre quelques jours ouvrés.


[image1]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
[workspace]: ./media/machine-learning-publish-web-service-to-azure-marketplace/selectworkspace.png
[apikey]: ./media/machine-learning-publish-web-service-to-azure-marketplace/apikey.png
[odata]: ./media/machine-learning-publish-web-service-to-azure-marketplace/odata.png
 

<!---HONumber=August15_HO6-->
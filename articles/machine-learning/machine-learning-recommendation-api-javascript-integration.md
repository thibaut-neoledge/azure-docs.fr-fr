<properties 
	pageTitle="Machine Learning Recommendations : intégration à l’aide de JavaScript | Microsoft Azure" 
	description="Azure Machine Learning Recommendations - Intégration à l’aide de JavaScript – documentation" 
	services="machine-learning" 
	documentationCenter="" 
	authors="LuisCabrer" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/08/2016" 
	ms.author="luisca"/>

# Azure Machine Learning Recommendations - Intégration à l’aide de JavaScript

>[AZURE.NOTE] Vous devez commencer à utiliser le Service cognitif de l’API Recommandations au lieu de cette version. Le Service cognitif de l’API Recommandations remplacera ce service et toutes les nouvelles fonctionnalités y seront développées. Il propose de nouvelles fonctionnalités telles que la prise en charge du traitement par lot, un meilleur Explorateur d’API, une surface d’API plus propre, une expérience d’inscription/de facturation plus cohérente, etc. En savoir plus sur la [migration vers le nouveau Service cognitif](http://aka.ms/recomigrate)


Ce document décrit comment intégrer votre site à l’aide de JavaScript. JavaScript vous permet d’envoyer des événements d’acquisition de données et d’utiliser les recommandations après la génération d’un modèle de recommandation. Toutes les opérations effectuées via JS peuvent également être effectuées côté serveur.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##1\. Présentation générale
L’intégration de votre site avec Azure ML Recommandations se compose de 2 phases :

1.	Envoi d’événements dans Azure ML Recommendations. Cela permet de générer un modèle de recommandation.
2.	Utilisation des recommandations. Une fois le modèle créé, vous pouvez utiliser les recommandations. (Ce document n’explique pas comment générer un modèle, lisez le guide de démarrage rapide pour obtenir plus d’informations à ce sujet).


<ins>Phase I</ins>

Dans la première phase, vous insérez dans vos pages html une petite bibliothèque JavaScript qui permet à la page d’envoyer des événements se produisant sur une page html vers les serveurs Azure ML Recommandations (via Data Market) :

![Drawing1][1]

<ins>Phase II</ins>

Pendant la deuxième phase, lorsque vous souhaitez afficher les recommandations sur la page, sélectionnez une des options suivantes :

1\. Votre serveur (dans la phase de rendu des pages) appelle le serveur Azure ML Recommandations (via Data Market) pour obtenir des recommandations. Les résultats incluent une liste des ID d’articles. Votre serveur a besoin d’enrichir les résultats avec les métadonnées des articles (par exemple des images, une description) et d’envoyer la page créée dans le navigateur.

![Drawing2][2]

2\. L’autre option consiste à utiliser le petit fichier JavaScript de la première étape pour obtenir une liste simple d’articles recommandés. Les données reçues ici sont moins conséquentes qu’avec la première option.

![Drawing3][3]

##2\. Composants requis

1. Créer un nouveau modèle à l’aide des API. Consultez le guide de démarrage rapide pour plus d’informations.
2. Encoder votre &lt;dataMarketUser&gt;:&lt;dataMarketKey&gt; avec base64. (Cela est utilisé pour l’authentification de base afin d’autoriser le code JS à appeler les API).


##3\. Envoyer des événements d’acquisition de données à l’aide de JavaScript
Les étapes suivantes facilitent l’envoi d’événements :

1.	Incluez la bibliothèque JQuery dans votre code. Vous pouvez la télécharger à partir de nuget sur l’URL suivante.

		http://www.nuget.org/packages/jQuery/1.8.2
2.	Incluez la bibliothèque JavaScript Recommandations depuis l’URL suivante : http://aka.ms/RecoJSLib1

3.	Initialisez la bibliothèque Azure ML Recommandations avec les paramètres appropriés.

		<script>
			AzureMLRecommendationsStart("<base64encoding of username:key>",
			"<model_id>");
		</script>

4.	Envoyez l’événement approprié. Consultez la section détaillée ci-dessous sur tous les types d’événements (exemple d’événement clic)

		<script>
			if (typeof AzureMLRecommendationsEvent=="undefined") { 		
        	        	AzureMLRecommendationsEvent = [];
	                }
			AzureMLRecommendationsEvent.push({ event: "click", item: "18321116" });
		</script>


###3\.1. Limitations et prise en charge du navigateur
Il s’agit d’une implémentation de référence, fournie en l’état. Elle prend normalement en charge les principaux navigateurs.

###3\.2. Type d’événements
Il existe cinq types d’événements pris en charge par la bibliothèque : clic, clic de recommandation, ajouter au panier, supprimer du panier et achat. Il existe un événement supplémentaire, utilisé pour définir le contexte utilisateur, appelé connexion.

####3\.2.1. Événement clic
Cet événement doit être utilisé chaque fois qu’un utilisateur clique sur un article. Généralement lorsque l’utilisateur clique sur un article, une nouvelle page s’ouvre avec les détails de l’article ; cet événement doit être déclenché dans cette page.

Paramètres :
- event (chaîne, obligatoire) – “click”
- item (chaîne, obligatoire) – identificateur unique de l'élément
- itemName (chaîne, facultatif) – nom de l'élément
- itemDescription (chaîne, facultatif) – description de l'élément
- itemCategory (chaîne, facultatif) – catégorie de l'élément
		
		<script>
			if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({ event: "click", item: "3111718" });
		</script>

Ou avec des données facultatives :

		<script>
			if (typeof AzureMLRecommendationsEvent === "undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({event: "click", item: "3111718", itemName: "Plane", itemDescription: "It is a big plane", itemCategory: "Aviation"});
		</script>


####3\.2.2. Événement clic de recommandation
Cet événement doit être utilisé chaque fois qu’un utilisateur clique sur un article recommandé reçu à partir d’Azure ML Recommandations. Généralement lorsque l’utilisateur clique sur un article, une nouvelle page s’ouvre avec les détails de l’article ; cet événement doit être déclenché dans cette page.

Paramètres :
- event (chaîne, obligatoire) – “recommendationclick”
- item (chaîne, obligatoire) – identificateur unique de l'élément
- itemName (chaîne, facultatif) – nom de l'élément
- itemDescription (chaîne, facultatif) – description de l'élément
- itemCategory (chaîne, facultatif) – catégorie de l'élément
- seeds (tableau de chaînes, facultatif) – valeurs initiales qui ont généré la requête de recommandation.
- recoList (tableau de chaînes, facultatif) – résultat de la demande de recommandation qui a généré l'élément cliqué.
		
		<script>
			if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({event: "recommendationclick", item: "18899918" });
		</script>

Ou avec des données facultatives :

		<script>
			if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({ event: eventName, item: "198", itemName: "Plane2", itemDescription: "It is a big plane2", itemCategory: "Default2", seeds: ["Seed1", "Seed2"], recoList: ["199", "198", "197"] 				});
		</script>


####3\.2.3. Événement ajouter au panier
Cet événement doit être utilisé lorsque l’utilisateur ajoute un article au panier. Paramètres :
* event (chaîne, obligatoire) – “addshopcart”
* item (chaîne, obligatoire) – identificateur unique de l'élément
* itemName (chaîne, facultatif) – nom de l'élément
* itemDescription (chaîne, facultatif) – description de l'élément
* itemCategory (chaîne, facultatif) – catégorie de l'élément
		
		<script>
			if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({event: "addshopcart", item: "13221118" });
		</script>

####3\.2.4. Événement supprimer du panier
Cet événement doit être utilisé lorsque l’utilisateur supprime un article du panier.

Paramètres :
* event (chaîne, obligatoire) – “removeshopcart”
* item (chaîne, obligatoire) – identificateur unique de l'élément
* itemName (chaîne, facultatif) – nom de l'élément
* itemDescription (chaîne, facultatif) – description de l'élément
* itemCategory (chaîne, facultatif) – catégorie de l'élément
		
		<script>
			if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({ event: "removeshopcart", item: "111118" });
		</script>

####3\.2.5. Événement d’achat
Cet événement doit être utilisé lorsque l’utilisateur achète son panier.

Paramètres :
* event (chaîne) – “purchase”
* items ( Purchased ) – Tableau contenant une entrée pour chaque article acheté.<br><br> Format d’achat :
	* item (chaîne) – identificateur unique de l'élément.
	* count (entier ou chaîne) – nombre d'articles achetés.
	* price (flottant ou chaîne) – champ facultatif – prix de l'élément.

L’exemple suivant présente l’achat de 3 articles (33, 34, 35), dont deux avec tous les champs renseignés (article, quantité, prix) et l’autre (article 34) sans prix.

		<script>
			if ( typeof AzureMLRecommendationsEvent == "undefined"){ AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({ event: "purchase", items: [{ item: "33", count: "1", price: "10" }, { item: "34", count: "2" }, { item: "35", count: "1", price: "210" }] });
		</script>

####3\.2.6. Événement de connexion utilisateur
La bibliothèque d’événements ML Azure Recommandations crée et utilise un cookie afin d’identifier les événements provenant du même navigateur. Afin d’améliorer les résultats du modèle, Azure ML Recommandations permet de définir une identification unique de l’utilisateur qui remplace l’utilisation de cookies.

Cet événement doit être utilisé après la connexion utilisateur à votre site.

Paramètres :
* event (chaîne) – “userlogin”
* user (string) – identification unique de l’utilisateur. 

		<script>
			if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({event: "userlogin", user: “ABCD10AA” });
		</script>

##4\. Utiliser les recommandations via JavaScript
Le code qui utilise les recommandations est déclenché par un événement JavaScript de la page Web du client. La réponse de recommandation inclut les ID des articles recommandés, leurs noms et leurs évaluations. Il est préférable d’utiliser cette option uniquement pour afficher les articles recommandés sous forme de liste : les opérations de gestion plus complexes (par exemple l’ajout de métadonnées de l’article) doivent être effectuées sur l’intégration du côté serveur.

###4\.1 Utilisation des recommandations
Pour utiliser des recommandations, vous devez inclure les bibliothèques JavaScript requises sur votre page et appeler AzureMLRecommendationsStart. Consultez la section 2.

Pour utiliser des recommandations pour un ou plusieurs articles, vous devez appeler une méthode nommée : AzureMLRecommendationsGetI2IRecommendation.

Paramètres :
* articles (table de chaînes) – un ou plusieurs articles pour lesquels vous souhaitez obtenir des recommandations. Si vous consommez une build Fbt, vous ne pouvez définir qu'un seul élément ici.
* numberOfResults (entier) – nombre de résultats requis.
* includeMetadata (booléen, facultatif) – si « true », indique que le champ de métadonnées doit être rempli dans le résultat.
* Fonction de traitement – fonction qui gérera les recommandations renvoyées. Les données sont retournées sous forme de tableau de :
	* Item – ID unique de l’élément
	* name – nom de l'élément (s’il existe dans le catalogue)
	* rating – évaluation de la recommandation
	* métadonnées – chaîne qui représente les métadonnées de l'élément

Exemple : le code suivant demande 8 recommandations pour l’article « 64f6eb0d-947a-4c18-a16c-888da9e228ba » (et en ne spécifiant pas includeMetadata, il indique implicitement qu’aucune métadonnée n’est requise). Il concatène ensuite les résultats dans une mémoire tampon.

		<script>
 			var reco = AzureMLRecommendationsGetI2IRecommendation(["64f6eb0d-947a-4c18-a16c-888da9e228ba"], 8, false, function (reco) {
 				var buff = "";
 				for (var ii = 0; ii < reco.length; ii++) {
   					buff += reco[ii].item + "," + reco[ii].name + "," + reco[ii].rating + "\n";
 				}
 				alert(buff);
			});
		</script>


[1]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing1.png
[2]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing2.png
[3]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing3.png
 

<!----HONumber=AcomDC_0914_2016-->
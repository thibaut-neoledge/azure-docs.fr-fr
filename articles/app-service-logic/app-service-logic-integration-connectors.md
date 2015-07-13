<properties 
	pageTitle="Applications API d'intégration BizTalk dans Microsoft Azure App Service | Azure" 
	description="Découvrez comment créer et configurer les applications API d'intégration BizTalk ; architecture microservices" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="mandia"/>


# Applications API d'intégration BizTalk dans Microsoft Azure App Service
Microsoft Azure App Service (ou App Service) comprend plusieurs applications API BizTalk qui sont essentielles pour les environnements d'intégration. Ces applications API sont basées sur des concepts et des outils utilisés dans BizTalk Server, mais elles sont désormais disponibles dans le cadre d'Azure App Service.

Les applications API d'intégration BizTalk constituent l'une des catégories d'applications API. Grâce à ces applications API BizTalk, vous pouvez facilement ajouter des règles d'entreprise, transformer et valider des messages XML, ou encore encoder des fichiers plats et des données JSON, comme vous le feriez sur un serveur local avec BizTalk Server.

Ces applications API d'intégration offrent des fonctionnalités d'« Action ». Une Action est un résultat (par exemple, après la réception d'un message XML, valider le message XML par rapport à un schéma XML).


## Qu'est-ce qu'une application API d'intégration ?
Une application API BizTalk d'intégration est une application API existante et préconfigurée qui peut effectuer un traitement des données et générer une sortie. Par exemple, certaines applications API permettent à différents formats de fichiers d'opérer ensemble et certaines appliquent une logique métier ou une logique d'application. Les applications API d'intégration sont les suivantes :

Applications API | Description
--- | ---
<ul><li>Application API X 12</li><li>Connecteur AS2</li><li>Application API EDIFACT</li><li>Application API de gestion des partenaires commerciaux</li> | Ces applications API fournissent des capacités B2B (entreprise à entreprise). La rubrique [Connecteurs B2B](app-service-logic-b2b-connectors.md) fournit plus de détails sur ces applications API.
Encodeur de fichier plat BizTalk | Une application API d’**Action**. Autorise l'interopérabilité entre des données de fichiers plats (comme Excel, csv) et des données XML. Peut convertir une instance de fichier plat au format XML et vice versa.
Encodeur JSON BizTalk | Une application API d’**Action**. Autorise l'interopérabilité entre des données JSON et XML. Peut convertir une instance JSON au format XML et vice versa.
Règles BizTalk | Une application API d’**Action**. Vous pouvez implémenter et appliquer une logique métier ou des « règles » pour contrôler une opération commerciale. Les règles sont dynamiques et peuvent changer à tout moment. Les règles BizTalk permettent aux utilisateurs d'activer une logique métier sans écrire de code et peuvent être facilement mises à jour sans affecter les applications.
Transformation BizTalk | Une application API d’**Action**. Convertit des données d'un format vers un autre. Vous pouvez utiliser différentes fonctions intégrées pour manipuler des chaînes, effectuer des opérations sur des expressions arithmétiques, mettre en forme la date et l'heure, et ainsi de suite. 
Validateur XML BizTalk | Une application API d’**Action**. Valide des données XML par rapport à des schémas XML prédéfinis. Vous pouvez utiliser des schémas basés sur des instances de fichiers plats, des instances JSON ou des connecteurs existants. 
Extracteur XPath BizTalk | Une application API d’**Action**. Recherchez et extrayez des données à partir de contenu XML en fonction d'une expression XPath spécifique.
Wait | Différer l'exécution pendant un laps de temps que vous spécifiez ou jusqu'à une heure spécifique. En cas d'ajout à une application logique, vous pouvez l'utiliser pour différer l'exécution de l'application entière.


	> [AZURE.NOTE] If the input xml has a simple node with an attribute (like "<authorid= ”1”>abc</author>"), then the JSON output of the library is { “author”: { “@id” : “1”, “#text”: “abc”}}. To handle the “Id” attribute, a new “#text” key is added for the text content of the node. To handle this kind of node, add a constant key. This is by design in the Newtonsoft.Json library. When you insert this into SQL, use “JSONOutput.Author.#text”; do not use “JsonOutput.Author”.

Avec ces applications API, vous pouvez effectuer différentes tâches de messagerie ou de données. Par exemple, avec l'application API de règles BizTalk, vous pouvez recevoir une commande et appliquer une remise quand une quantité spécifique est commandée. Vous pouvez aussi facturer un taux de taxe spécifique en fonction du code postal.

Vous pouvez créer autant d'applications API que vous le souhaitez, en toute simplicité. Vous pouvez aussi réutiliser les applications API dans plusieurs scénarios ou flux de travail.

Par exemple, supposez que vous avez une stratégie d'entreprise qui applique une remise de 10 % quand un client commande 100 articles. Dans votre application, vous pouvez ajouter l'application API de règles BizTalk qui vérifie la quantité commandée et, si elle est supérieure à 100, applique la remise de 10 %.

Vous pouvez également étendre cette stratégie d'entreprise. Supposez que votre objectif consiste à augmenter les ventes en Caroline du Nord. En plus de la remise de 10 % sur les commandes de 100 articles ou plus, vous offrez également la livraison gratuite si la commande vient de Caroline du Nord. Vous pouvez facilement ajouter cette condition « Caroline du Nord » à votre règle BizTalk existante.

Tout cela avec ces applications API et sans écrire de code. Allons-y.


## Créer les applications API
Vous pouvez créer des applications API d'intégration à l'aide du portail Azure ou avec des API REST.


### Créer des applications API avec des API REST
Consultez la page [Utiliser les API REST](http://go.microsoft.com/fwlink/p/?LinkId=529766).


### Créer des applications API d'intégration dans le portail Azure
Dans le portail Azure, vous pouvez créer des applications API d'intégration BizTalk lors de la création d'applications logiques, d'applications web ou d'applications mobiles. Vous pouvez aussi en créer une à l'aide de son propre panneau. Les deux méthodes étant faciles, vous choisirez en fonction de vos besoins et préférences. Certains utilisateurs préfèrent d'abord créer toutes les applications API d'intégration avec leurs propriétés spécifiques. Ensuite, ils créent les applications logiques, web ou mobiles et ajoutent les applications API d'intégration qu'ils ont créées.

Les étapes suivantes permettent de créer des applications API d'intégration BizTalk à l'aide du panneau Applications API :

1. Dans le Tableau d’accueil du portail Azure (la page d’accueil), sélectionnez **Marketplace**. **Applications API** répertorie tous les connecteurs et les applications API existants. Vous pouvez également **rechercher** une application API BizTalk spécifique.
2. Sélectionnez l'application API. Dans le nouveau panneau, sélectionnez **Créer**. 
3. Entrez les propriétés : 

	Propriété | Description
--- | ---
Nom | Entrez un nom pour votre application API. Par exemple, vous pouvez la nommer *RulesDiscountTaxCode* ou *APIAppValidateXML*.
Plan App Service | Indique votre plan de paiement. Vous pouvez le modifier si vous avez besoin de plus ou moins de ressources.
Niveau de tarification | Propriété en lecture seule qui indique la catégorie de tarification dans votre abonnement Azure. 
Groupe de ressources | Créez-en un nouveau ou utilisez un groupe existant. Toutes les applications API et tous les connecteurs pour vos applications logiques, web et mobiles doivent être dans le même groupe de ressources. Cette propriété est décrite dans la section <br/><br/>[Utiliser des groupes de ressources](../resource-group-overview.md). 
Abonnement | Propriété en lecture seule qui indique votre abonnement actuel.
Emplacement | Emplacement géographique qui héberge votre service Azure. 
Ajouter au Tableau d'accueil | Sélectionnez cette option pour ajouter les applications API d'intégration à votre Tableau d'accueil (la page d'accueil).


## Configurer les applications API BizTalk
Dans le portail de gestion Azure, ouvrez votre application API BizTalk. Dans la section **Composants**, vous pouvez ajouter les composants supplémentaires nécessaires pour terminer l’application API :

	API App | Tasks
--- | ---
Encodeur de fichier plat BizTalk | Entrez un fichier plat, par exemple un fichier Excel ou csv que vous souhaitez convertir au format XML. Ou entrez un fichier XML à convertir en fichier plat.
Encodeur JSON BizTalk | Entrez un fichier JSON à convertir au format XML. Ou entrez un fichier XML à convertir au format JSON. 
Règles BizTalk | Ajoutez vos vocabulaires et créez vos règles IF THEN. Consultez [Utiliser des règles BizTalk](app-service-logic-use-biztalk-rules.md). 
Transformation BizTalk | Ajoutez un mappage, puis entrez un schéma XML d'entrée et un schéma XML de sortie. Utilisez les fonctions intégrées pour manipuler le message ou les données entrantes pour qu'ils correspondent à votre schéma XML de sortie. Consultez [Transformer des documents XML](app-service-logic-transform-xml-documents.md). 
Validateur XML BizTalk | Entrez le code XML à valider par rapport à un schéma XML prédéfini. Vous pouvez utiliser des schémas basés sur des instances de fichiers plats, des instances JSON ou des connecteurs existants. 
Extracteur XPath BizTalk | Recherchez et extrayez des données à partir de contenu XML en fonction d'une expression XPath spécifique.
Wait | Entrez une durée ou une heure spécifique pour exécuter les applications web, mobiles ou logiques.


## Surveiller vos applications API
Dans le portail de gestion Azure, ouvrez votre application API BizTalk. Dans la section **Opérations** sont affichées différentes opérations de gestion. Vous pouvez par exemple afficher :

- Les événements d'information et d'erreur
- Le nombre de threads et l'utilisation de la mémoire du processus de travail (w3wp)
- Les journaux de serveur web et d'application

Pour en savoir plus, consultez [Surveiller vos applications logiques](app-service-logic-monitor-your-logic-apps.md).


## Ajouter les applications API BizTalk à votre application 
Microsoft Azure App Service expose différents types d'applications qui peuvent utiliser ces applications API d'intégration. Vous pouvez créer une application API BizTalk ou ajouter vos applications API BizTalk existantes à des applications logiques, mobiles ou web.

Dans votre application, il vous suffit de sélectionner vos applications API BizTalk dans la galerie pour les ajouter à votre application.

Les étapes suivantes permettent d'ajouter des applications API BizTalk à des applications logiques, mobiles ou web :

1. Dans le Tableau d’accueil du portail Azure (page d’accueil), accédez à **Marketplace** et recherchez vos applications logiques, mobiles ou web. 

	Si vous créez une application, recherchez Applications logiques, Applications mobiles ou Applications web. Sélectionnez l’application puis, dans le nouveau panneau, sélectionnez **Créer**. [Créer une application logique](app-service-logic-create-a-logic-app.md) répertorie les étapes.

2. Ouvrez votre application et sélectionnez **Déclencheurs et actions**.

3. Dans la **Galerie**, sélectionnez l’application API BizTalk. Elle est ajoutée automatiquement à votre application.

4. Sélectionnez **OK** pour enregistrer vos modifications.


## Autres ressources d'applications API d'intégration
[Créer une application logique EAI à l’aide de VETR](app-service-logic-create-EAI-logic-app-using-VETR.md)<br/> [Transformer des documents XML](app-service-logic-transform-xml-documents.md)<br/> [Utiliser des règles BizTalk](app-service-logic-use-biztalk-rules.md)<br/> [Qu’est-ce qu’un connecteur et une application API BizTalk ?](app-service-logic-what-are-biztalk-api-apps.md)


## En savoir plus sur les applications logiques et les applications web
[Qu’est-ce qu’une application logique ?](app-service-logic-what-are-logic-apps.md)<br/> [Sites Web et Web Apps dans Azure App Service](../app-service-web/app-service-web-overview.md)


## Autres connecteurs
[Connecteurs d’entreprise](app-service-logic-enterprise-connectors.md)<br/> [Connecteurs B2B](app-service-logic-b2b-connectors.md)<br/> [Connecteurs sociaux](app-service-logic-social-connectors.md)<br/> [Connecteurs de protocole](app-service-logic-protocol-connectors.md)<br/> [Connecteurs application + services de données](app-service-logic-data-connectors.md)<br/> [Liste des connecteurs et des applications API](app-service-logic-connectors-list.md)<br/><br/> [Qu’est-ce qu’un connecteur et une application API BizTalk](app-service-logic-what-are-biztalk-api-apps.md)

<!---HONumber=62-->
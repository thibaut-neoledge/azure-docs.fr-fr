<properties 
	pageTitle="Connecteurs d'entreprise dans Microsoft Azure App Service | Azure" 
	description="Découvrez comment créer et configurer un connecteur d'entreprise ; architecture microservices" 
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
	ms.date="07/08/2015" 
	ms.author="mandia"/>

# Connecteurs d'entreprise dans Microsoft Azure App Service

> [AZURE.NOTE]Cette rubrique va être supprimée. Reportez-vous à la page [Liste des connecteurs et applications API](app-service-logic-connectors-list.md) pour connaître l’ensemble des connecteurs et applications API intégrés disponibles.

Microsoft Azure App Service (ou App Service) comprend plusieurs connecteurs qui peuvent être utilisés avec des systèmes d'entreprise, tels que SAP et Marketo. Grâce à ces connecteurs, vous pouvez facilement vous connecter à un système d'entreprise et effectuer différentes tâches.

Ces connecteurs d'entreprise offrent des fonctionnalités de « déclencheurs » ou d'« action ». Un déclencheur démarre une nouvelle instance en fonction d'un événement spécifique, telle que la mise à jour d'une entrée dans MongoDb. Une action est le résultat, par exemple, après la mise à jour d'une entrée de MongoDB, obtenir une entrée MongoDB.


## Qu'est-ce qu'un connecteur d'entreprise ?
Les connecteurs d'entreprise sont des « applications API » existantes qui peuvent se connecter à des systèmes d'entreprise locaux et dans le cloud, notamment :

Connecteur | Description
--- | ---
Marketo | Connecteur **Action**. Se connecter à Marketo et exécuter différentes actions, telles que créer/mettre à jour des prospects, obtenir les modifications de prospects, planifier une campagne, demander une campagne, obtenir des prospects, obtenir des informations sur des campagnes/une liste, ajouter des prospects à une liste et supprimer des prospects d'une liste.
MongoDB | Connecteur **Déclencheur** et **Action**. Se connecter au serveur Mongo local ou à la base de données du serveur Mongo. Sur une collection MongoDB, effectuer différentes actions telles que créer, mettre à jour, obtenir et supprimer des entrées. 
QuickBooks | Connecteur **Action**. Se connecter à QuickBooks et utiliser vos applications pour créer, mettre à jour, lire, supprimer et interroger différentes entités Intuit QuickBooks, telles que des clients, des articles, des factures et ainsi de suite.
Salesforce |Connecteur **Action**. Se connecter à SalesForce pour gérer différentes entités, telles que des comptes, des prospects, des opportunités, des cas, et ainsi de suite, dans votre compte Salesforce. Vous pouvez effectuer différentes actions, telles que créer, mettre à jour, upsert, interroger et supprimer sur les différentes entités. 
SugarCRM | Connecteur **Action**. Se connecter à SugarCRM en ligne et utiliser vos applications pour créer, mettre à jour, lire et supprimer différents types de modules, tels que des comptes, des contacts, des produits et ainsi de suite.
SAP | Connecteur **Action**. Se connecter à un serveur SAP local. Appeler des RFC, BAPI et tRFC et également envoyer des IDOC.


Grâce à ces connecteurs, vous pouvez effectuer différentes tâches sur le système d'entreprise. Par exemple, avec le connecteur QuickBooks, vous pouvez effectuer des tâches ordinaires dans QuickBooks, par exemple ajouter un nouveau client, mettre à jour une facture ou supprimer un élément.

Vous pouvez créer autant de connecteurs que vous le souhaitez, en toute simplicité. Vous pouvez aussi réutiliser un connecteur dans plusieurs scénarios ou flux de travail.

Par exemple, supposons que lors d'une conférence vous rencontrez un client qui souhaite vous acheter 10 articles. Sur votre appareil mobile, vous pouvez ouvrir votre application mobile qui utilise le connecteur QuickBooks que vous avez créé, ajouter les détails du client et créer une facture.

Pendant que vous êtes à la conférence, un client qui se trouve au siège de votre entreprise souhaite payer une facture existante. L'employé ouvre une application qui utilise le même connecteur QuickBooks, recherche les informations sur le client et met à jour la facture pour refléter le nouveau solde.

Il s'agit de flux différents qui utilisent le même connecteur QuickBooks. Vous pouvez créer et utiliser ces connecteurs sans écrire de code. Allons-y.


## Conditions préalables requises
Quand vous créez un connecteur d'entreprise, certaines ressources sont nécessaires. Vous devez créer ces éléments avant de pouvoir les utiliser dans le connecteur. Les conditions sont les suivantes :

Prérequis | Description
--- | ---
Espace de noms Service Bus et ses valeurs de clé | Lors de l'utilisation locale de SAP ou MongoDB, un espace de noms Service Bus et ses valeurs de clés sont nécessaires. Si vous ne vous connectez pas à un système local, vous n’avez pas besoin d’espace de noms Service Bus.<br/><br/>[Créer un espace de noms Service Bus](http://msdn.microsoft.com/library/azure/hh690931.aspx)
Gestionnaire de connexions hybrides local | Quand vous vous connectez à un système local, vous installez le Gestionnaire de connexions hybrides sur ce système. Quand vous créez le connecteur local, le téléchargement est disponible dans les paramètres ou les propriétés du connecteur. 
Propriétés spécifiques au système | Lors de l'utilisation de systèmes d'entreprise, vous avez besoin de certaines propriétés spécifiques au système (comme le nom d'utilisateur et le mot de passe). 


## Créer un connecteur
Vous pouvez créer un connecteur à l’aide du portail Azure.

### Créer un connecteur dans le portail Azure
Dans le portail Azure, vous pouvez créer un connecteur d'entreprise lors de la création d'applications logiques, d'applications web ou d'applications mobiles. Vous pouvez aussi en créer une à l'aide de son propre panneau. Les deux méthodes étant faciles, vous choisirez en fonction de vos besoins et préférences. Certains utilisateurs préfèrent d'abord créer tous les connecteurs avec leurs propriétés spécifiques. Ensuite, ils créent les applications logiques, web ou mobiles et ajoutent le connecteur qu'ils ont créé.

Les étapes suivantes permettent de créer un connecteur d'entreprise à l'aide du panneau du connecteur :

1. Dans le Tableau d’accueil du portail Azure (la page d’accueil), sélectionnez **Marketplace**. **Applications API** répertorie tous les connecteurs existants. Vous pouvez aussi **Rechercher** un connecteur spécifique.
2. Sélectionnez le connecteur. Dans le nouveau panneau, sélectionnez **Créer**. 
3. Entrez les propriétés : 

	Propriété | Description
--- | ---
Nom | Entrez un nom pour le connecteur. Par exemple, vous pouvez le nommer *SAPConnector*, *SalesForceGetAccounts* ou *QuickBooksGetItems*.
Paramètres du package | Entrez les paramètres du système d’entreprise, comme le *nom d’utilisateur SAP* ou l’*URL du serveur SugarCRM*. Consultez [Propriétés spécifiques au système d’entreprise](#AddProperties) dans cette rubrique. 
Plan App Service | Indique votre plan de paiement. Vous pouvez le modifier si vous avez besoin de plus ou moins de ressources.
Niveau de tarification | Propriété en lecture seule qui indique la catégorie de tarification dans votre abonnement Azure. 
Groupe de ressources | Créez-en un nouveau ou utilisez un groupe existant. Toutes les applications API et tous les connecteurs pour vos applications logiques, web et mobiles doivent être dans le même groupe de ressources. Cette propriété est décrite dans la section <br/><br/>[Utiliser des groupes de ressources](../resource-group-overview.md). 
Abonnement | Propriété en lecture seule qui indique votre abonnement actuel.
Emplacement | Emplacement géographique qui héberge votre service Azure. 
Ajouter au Tableau d'accueil | Sélectionnez cette option pour ajouter le connecteur à votre Tableau d'accueil (la page d'accueil).


	**<a name="AddProperties"></a>Propriétés spécifiques du système d’entreprise**

> [AZURE.IMPORTANT]Chaque connecteur possède des propriétés qui sont spécifiques à ce système d'entreprise. Lors de la connexion à SAP, vous entrez des propriétés spécifiques à SAP. Lors de la connexion à Salesforce, vous entrez des propriétés spécifiques à Salesforce, et ainsi de suite. Le tableau suivant répertorie les propriétés de système d'entreprise obligatoires.
	
	Enterprise System | Required Properties
--- | ---
Marketo | <ul><li>Point de terminaison</li><li>Nom du fournisseur</li></ul>
MongoDB| <ul><li>Chaîne de connexion</li><li>Hôte</li><li>Port</li><li>Nom d’utilisateur</li><li>Mot de passe</li><li>Base de données</li><li>Utiliser le chiffrement SSL</li><li>Local : entrez False si elle est basée dans le cloud. Si le système MongoDb est local, entrez True et entrez les propriétés suivantes :<ul><li>Nom de clé d’accès partagé</li><li>Espace de noms Service Bus</li><li>Chemin d’accès de relais</li><li>Envoyer la clé</li></ul></li></ul>
QuickBooks | <ul><li>ID de la société</li><li>Nom du fournisseur</li></ul>
SAP | <ul><li>Nom d’hôte</li><li>Langage</li><li>Nom d’utilisateur</li><li>Mot de passe</li><li>Numéro du système</li><li>Chaîne de connexion de Service Bus</li><li>Noms RFC</li><li>Noms TRFC</li><li>Noms BAPI</li><li>Nom IDOC</li></ul>
Salesforce | <ul><li>Nom du fournisseur</li><li>Instance</li><li>Version</li><li>Entités (valeurs séparées par des virgules)</li></ul>
SugarCRM | <ul><li>URL du serveur</li><li>Nom du fournisseur</li><li>Nom des modules</li></ul>

4. Sélectionnez **Créer**.


## Installer le Gestionnaire de connexions hybrides local
Après avoir créé le connecteur d'entreprise qui se connecte à un système local, comme SAP, vous devez installer le Gestionnaire de connexions hybrides :

1. Sur le système d'entreprise local, ouvrez le portail de gestion Azure et sélectionnez votre connecteur d'entreprise. 
2. Dans le panneau, sélectionnez **Connexion hybride**. 
3. Copiez la valeur **Chaîne de configuration de la passerelle principale**. 
4. Sélectionnez **Télécharger et configurer**. Pendant l'installation, collez la chaîne de configuration de la passerelle que vous avez copiée et poursuivez l'installation. 
5. Pour confirmer la connectivité, ouvrez votre panneau de connecteur d'entreprise. L’état indiqué doit être **Connecté**. 

Vous trouverez un exemple dans la rubrique [Intégrer un serveur SAP local](app-service-logic-integrate-with-an-on-premise-SAP-server.md).


## Surveiller vos applications API
Dans le portail de gestion Azure, ouvrez votre application API d'entreprise. Dans la section **Opérations** sont affichées différentes opérations de gestion. Vous pouvez par exemple afficher :

- Les événements d'information et d'erreur
- Le nombre de threads et l'utilisation de la mémoire du processus de travail (w3wp)
- Les journaux de serveur web et d'application

Pour en savoir plus, consultez [Surveiller vos applications logiques](app-service-logic-monitor-your-logic-apps.md).


## Ajouter le connecteur à votre application 
Microsoft Azure App Service expose différents types d'applications qui peuvent utiliser ces connecteurs. Vous pouvez créer un connecteur ou ajouter vos connecteurs existants à des applications logiques, mobiles ou web.

Dans votre application, il vous suffit de sélectionner votre connecteur dans la galerie pour l'ajouter à votre application.

Les étapes suivantes permettent d'ajouter un connecteur d'entreprise à des applications logiques, mobiles ou web :

1. Dans le Tableau d’accueil du portail Azure (page d’accueil), accédez à **Marketplace** et recherchez vos applications logiques, mobiles ou web.

	Si vous créez une application, recherchez Applications logiques, Applications mobiles ou Applications web. Sélectionnez l’application puis, dans le nouveau panneau, sélectionnez **Créer**. [Créer une application logique](app-service-logic-create-a-logic-app.md) répertorie les étapes.

2. Ouvrez votre application et sélectionnez **Déclencheurs et actions**.

3. À partir de la **Galerie**, ajoutez les connecteurs que vous avez créés. Ils sont ajoutés automatiquement à votre application.
4. Sélectionnez **OK** pour enregistrer vos modifications. 


## Sécurité
Certains connecteurs d'entreprise utilisent la sécurité OAuth. Quand vous ajoutez un connecteur à votre application, vous **autorisez** le connecteur en vous connectant au système d’entreprise avec votre compte de connexion et vous acceptez les termes du contrat. Quand vous effectuez cette opération, votre application et le connecteur utilisent le compte de connexion pour s'authentifier auprès du système.


### Accéder au connecteur avec des API REST
[Consultez la documentation relative à l’utilisation des API REST avec le connecteur.](http://go.microsoft.com/fwlink/p/?LinkId=529766)

## Autres ressources sur les connecteurs d'entreprise
[Intégrer un serveur SAP local](app-service-logic-integrate-with-an-on-premise-SAP-server.md)<br/> [Qu’est-ce qu’un connecteur et une application API BizTalk ?](app-service-logic-what-are-biztalk-api-apps.md)


## En savoir plus sur les applications logiques et les applications web
[Qu’est-ce qu’une application logique ?](app-service-logic-what-are-logic-apps.md)<br/> [Sites Web et Web Apps dans Azure App Service](../app-service-web/app-service-web-overview.md)



## Autres connecteurs

[Liste des connecteurs et applications API](app-service-logic-connectors-list.md)<br/><br/> [Qu’est-ce qu’un connecteur et une application API BizTalk ?](app-service-logic-what-are-biztalk-api-apps.md)

<!---HONumber=July15_HO3-->
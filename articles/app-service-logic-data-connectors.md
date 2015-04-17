<properties 
	pageTitle="Connecteurs de données d'applications API Microsoft Azure | Microservice d'applications API" 
	description="Découvrez comment créer des applications API de connecteur de données Microsoft Azure et ajouter l'application API à votre application logique ; microservices" 
	services="app-service-logic" 
	documentationCenter="" 
	authors="MSFTMan" 
	manager="dwrede" 
	editor=""/>

<tags
	ms.service="app-service-logic" 
	ms.workload="data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="deonhe"/>


# Connecteurs de données dans Microsoft Azure App Service


## Qu'est-ce qu'un connecteur d'application API Application + services de données ?
Les connecteurs Application + services de données sont des " applications API " qui peuvent se connecter à une large gamme d'applications de services de données et effectuer plusieurs *Actions* pour le compte de l'utilisateur authentifié. Vous pouvez également configurer la plupart de ces connecteurs avec un *Trigger*. Les déclencheurs sont des événements (semblables aux événements dans .NET Framework) qui peuvent être configurés sur certains connecteurs pour initier un flux de travail. Par exemple, vous pouvez configurer une instance du connecteur Dropbox avec un déclencheur  *new file*, qui est activé chaque fois qu'un nouveau fichier est ajouté au compte Dropbox analysé. Vous pouvez ensuite configurer ce déclencheur pour initier une *Action* qui  *obtient* le fichier et le télécharge vers une liste SharePoint locale.


Voici une brève présentation de chacun des connecteurs Application + services de données disponibles dans la galerie Azure. 

<table>
<tr>
<th> Nom</th>
<th> Description</th>
<th> Déclencheurs</th>
<th>Actions</th>

<tr>
<td>Azure Media Services
<td>Le connecteur Azure Media Services vous permet de créer des flux de travail multimédias de bout en bout avec encodage, empaquetage et distribution flexibles et extensibles. Vous pouvez également télécharger, stocker, encoder et diffuser du contenu vidéo ou audio en toute sécurité pour la diffusion en continu à la demande et en direct vers une large gamme de téléviseurs, PC et points de terminaison d'appareils mobiles.
<td>Aucun
<td>Aucune 
</tr>

<tr>
<td>Azure Mobile Services
<td>Un connecteur Azure Mobile Services vous permet de créer et de modifier des données et d'appeler des API personnalisées.
<td>Aucun
<td><li>Interroger
<li>Insérer
<li>Supprimer
<li>Mettre à jour
<li>Appel d'API personnalisée

</tr>

<tr>
<td>Azure Service Bus
<td>Le connecteur Azure Services Bus vous permet d'envoyer des messages à partir d'entités Service Bus telles que des files d'attente et des rubriques, et de recevoir des messages provenant d'entités Services Bus telles que des files d'attente et des abonnements.
<td>Nouveaux messages
<td>Envoyer un message
</tr>

<tr>
<td>Azure Storage Blob
<td>Azure Storage Blob vous permet de vous connecter au stockage d'objets blob et d'effectuer différentes actions.
<td>Nouveau fichier ajouté
<td><li>Télécharger <li>Obtenir <li>Supprimer <li>Énumérer <li>Copier <li>Instantané
</tr>

<tr>
<td>Azure Storage Table
<td>Le connecteur Azure Storage Table vous permet de vous connecter à Azure Storage Table et d'effectuer différentes actions, telles qu'Obtenir une entité, Interroger des entités, Insérer une entité, Mettre à jour une entité, Supprimer une entité et un déclencheur pour extraire des données.
<td>Nouvelle entité
<td><li>Obtenir une entité
	<li>Insérer une entité
	<li>Supprimer une entité
	<li>Mettre à jour une entité
	<li>Interroger

</tr>

<tr>
<td>Box
<td>Le connecteur Box vous permet de vous connecter à Box et d'effectuer différentes actions sur vos fichiers. 
<td>Nouveaux fichiers ajoutés
<td><li>Télécharger un fichier
<li>Obtenir un fichier
<li>Supprimer un fichier
<li>Énumérer des fichiers
</tr>

<tr>
<td>Dropbox
<td>Le connecteur Dropbox vous permet de vous connecter à Dropbox et d'effectuer diverses actions, telles que Télécharger un fichier, Obtenir un fichier, Supprimer un fichier, Énumérer des fichiers et un déclencheur pour extraire des fichiers.
<td>Nouveaux fichiers ajoutés
<td><li>Télécharger un fichier
<li>Obtenir un fichier
<li>Supprimer un fichier
<li>Énumérer des fichiers
</tr>

<tr>
<td>HDInsight
<td>Un connecteur HDInsight vous permet de créer un cluster Hadoop sur Azure et de soumettre différentes tâches Hadoop telles que Hive, Pig, MapReduce et Streaming MapReduce. Grâce à ce connecteur, vous pouvez faire tourner un cluster, soumettre un travail et attendre que le travail soit terminé.
<td>N/A
<td><li>Créer un cluster
		<li>Attendre la création du cluster
		<li>Soumettre un travail Pig
		<li>Soumettre un travail Hive
		<li>Soumettre un travail MapReduce
		<li>Attendre la fin du travail
		<li>Supprimer un cluster
<li>Soumettre un travail MapReduce
<li>Soumettre un travail Streaming MapReduce

</tr>

<tr>
<td>Microsoft SQL
<td>Un connecteur Microsoft SQL vous permet de créer et de modifier des entrées dans des tables de bases de données Microsoft SQL Server et SQL Azure.
<td>N/A
<td><li>Messages : envoyer, énumérer, obtenir et rechercher
	<li>Numéros de téléphone : acheter, énumérer et obtenir des numéros locaux et gratuits
</tr>

<tr>
<td>MongoDB
<td>Un connecteur MongoDB vous permet de créer, mettre à jour, supprimer et obtenir des documents à partir d'une collection MongoDB.
<td>Nouveau document
<td>	<li>Ajouter un document
		<li>Mettre à jour un document
		<li>Obtenir des documents
		<li>Upsert de document
		<li>Supprimer un document
</tr>

<tr>
<td>Office 365
<td>Un connecteur Office 365 vous permet d'envoyer et de recevoir du courrier électronique et de gérer votre calendrier et vos contacts.
<td>Nouveau message
<td>	<li>Envoyer du courrier électronique
		<li>Répondre au courrier
		<li>Envoyer un événement
		<li>Ajouter un contact
</tr>

<tr>
<td>OneDrive
<td>Le connecteur OneDrive vous permet de vous connecter à votre compte de stockage Microsoft OneDrive personnel et d'effectuer différentes actions comme le chargement, l'obtention, la suppression et l'énumération de fichiers.
<td>Nouveau fichier
<td><li>Fichiers : télécharger, supprimer, énumérer, télécharger
</tr>

<tr>
<td>Oracle
<td>Un connecteur de base de données Oracle vous permet de créer et de modifier des entrées dans une table de base de données Oracle.
<td>Nouvelles données, en fonction de la requête
<td><li>Table : insérer, mettre à jour, sélectionner, supprimer
<li>Appeler une procédure stockée
</tr>

<tr>
<td>SharePoint Online
<td>Un connecteur Microsoft SharePoint vous permet de créer et de modifier des documents et des éléments de liste dans SharePoint Server ou Microsoft SharePoint Online.
<td><li>Nouveau document
<li>Nouvel élément de liste
<td><li>Bibliothèque de documents : télécharger, supprimer et obtenir
<li>Liste : insérer un élément

</tr>

<tr>
<td>SharePoint Server
<td>Le connecteur SharePoint Server vous permet de gérer des documents et des éléments de liste dans votre serveur SharePoint. Les informations d'identification par défaut, l'authentification Windows et l'authentification basée sur les formulaires sont prises en charge. Vous devez fournir une chaîne de connexion Service Bus et installer l'agent écouteur local avant de vous connecter au serveur.
<td><li>Nouveau document
<li>Nouvel élément de liste
<td><li>Bibliothèque de documents : télécharger, supprimer et obtenir
<li>Liste : insérer un élément
</tr>
</table>


##Pourquoi utiliser des connecteurs ?

Les connecteurs accélèrent le développement d'application et permettent même aux non-développeurs de créer des applications d'entreprise entièrement fonctionnelles sans avoir à apprendre un langage de programmation ou à écrire du code.

Maintenant que vous avez une idée de ce dont sont capables nos connecteurs de services de données et d'applications, nous allons examiner quelques exemples d'utilisation simples pour ces connecteurs. 

### Analyse de votre dossier d'échang et mise à jour de SharePoint ###
Imaginez que vous travaillez pour une société de construction qui reçoit des fichiers très volumineux contenant des plans. Ces fichiers sont généralement trop gros pour être gérés par courrier électronique. Votre société ouvre donc un compte Dropbox et demande à ses clients de déposer les plans dans le dossier Dropbox. Vous pourriez alors demander à chaque employé de vérifier constamment si les dossiers Dropbox contiennent de nouveaux projets, puis les télécharger vers votre serveur SharePoint. Néanmoins, vous êtes convaincu qu'il doit exister un meilleur moyen de procéder. Heureusement, vous avez découvert que Microsoft avait récemment publié son connecteur Application + services de données pour Dropbox, Sharepoint et d'autres services de données. Vous pouvez facilement créer des connecteurs pour Dropbox et SharePoint, les ajouter à une application logique et les configurer pour télécharger chaque nouveau fichier  depuis votre compte Dropbox vers votre liste SharePoint. Puisque le connecteur Dropbox a un déclencheur *new message*, vous pouvez l'utiliser pour signaler à votre application logique qu'un nouveau fichier est disponible. Le connecteur Dropbox peut ensuite télécharger le fichier. Vous pouvez ensuite configurer votre connecteur Sharepoint pour télécharger le fichier vers une liste SharePoint à l'aide de l'action SharePoint *upload*. Vous pouvez faire tout ceci sans écrire une seule ligne de code.  

Allons-y. 

##Créer un connecteur

Il existe deux façons de créer des connecteurs :

- à l'aide du portail Microsoft Azure (sans aucun codage) ;
- à l'aide d'API REST. 

### Créer un connecteur SharePoint dans le portail Microsoft Azure

1. Dans le portail Azure, sélectionnez **Nouveau** > **Web + Mobile** > **Azure Marketplace**.
2. **Recherchez** le connecteur ou sélectionnez-le dans la liste. Une fois le connecteur sélectionné, un nouveau panneau ou volet s'ouvre. Sélectionnez **Créer**. 
3. Entrez les propriétés suivantes pour le connecteur : 
	<table>
	    <tr><th>Propriété</th> <th>Description</th> </tr>
	    <tr><td>Nom</td> <td>Entrez un nom pour votre application API. Par exemple, vous pouvez la nommer RulesDiscountTaxCode ou APIAppValidateXML.</td> </tr>
	    <tr><td>Plan App Service</td> <td>Indique votre plan de paiement. Vous pouvez le modifier si vous avez besoin de plus ou moins de ressources.</th> </td>
	    <tr><td>Niveau de tarification</td> <td>Propriété en lecture seule qui indique la catégorie de tarification dans votre abonnement Azure.</td> </tr>
	    <tr><td>Groupe de ressources</td> <td>Créez-en un nouveau ou utilisez un groupe existant. Cette propriété est décrite dans la section Utiliser des groupes de ressources.</td> </tr>
	    <tr><td>Abonnement</td> <td>Propriété en lecture seule qui indique votre abonnement actuel</td> </tr>
	    <tr><td>Emplacement</td> <td>Emplacement géographique qui héberge votre service Azure. </td></tr>
        <tr><td>Ajouter au Tableau d'accueil</td> <td>Sélectionnez cette option pour ajouter l'application API à votre Tableau d'accueil (la page d'accueil).</td></tr>
	</table> 
4. Sélectionnez **Créer**. Votre connecteur est créé. L'opération peut prendre un certain temps et l'écran d'accueil s'affiche lors de la création du connecteur. Utilisez l'élément de menu Notifications à gauche pour surveiller l'état de votre connecteur.

Maintenant que vous avez créé votre premier connecteur, vous pouvez créer une application web, mobile ou logique avec. 


### Créer un connecteur avec des API REST

[Créer des connecteurs avec des API REST](http://go.microsoft.com/fwlink/p/?LinkId=529766)

## Ajouter votre connecteur à une application 
Microsoft Azure App Service expose différents types d'applications qui peuvent utiliser ces connecteurs. Par exemple, vous pouvez créer une application *Logic* en combinant un ou plusieurs de vos connecteurs *logically* dans une même application.

Pour utiliser vos connecteurs dans votre application *Logic*, vous devez sélectionner un connecteur configuré dans la liste, l'ajouter à votre flux de travail de conception et apporter les modifications de configuration nécessaires. Il est alors prêt à être utilisé. 

Pour suivre ces étapes, vous avez besoin d'une application web, mobile ou logique. Consultez <> pour connaître les étapes spécifiques. Une fois que votre application est disponible, ajoutez vos connecteurs. Voici comment procéder :

Exécutez les étapes suivantes pour ajouter un connecteur à une application logique : 

1. Dans le Tableau d'accueil du portail Azure (page d'accueil), accédez à **Marketplace**et recherchez votre  application logique, mobile ou web. 

	Si vous créez une application, recherchez Application logique, Application mobile ou Application web. Sélectionnez l'application et, dans le nouveau panneau, sélectionnez **Créer**. [Créer une application logique](app-service-logic-create-a-logic-app.md) répertorie les étapes. 

2. Ouvrez votre application et sélectionnez **Déclencheurs et actions**. 
3. Dans la **Galerie**, sélectionnez le connecteur. Il est ajouté à votre application.
4. Configurez le connecteur :
5. Chaque connecteur possède des propriétés qui sont spécifiques au service et à l'environnement auquel il se connecte. Entrez les détails des propriétés. N'oubliez pas que certaines propriétés sont facultatives.
6. Sélectionnez **OK** pour enregistrer vos modifications.


## Sécurité
Les connecteurs utilisent OAuth ou des noms d'utilisateurs et des mots de passe. 


## En savoir plus sur les applications logiques et les applications web
[Qu'est-ce qu'une application logique ?](app-service-logic-what-are-logic-apps.md) |
[Sites web et applications web dans Azure App Service](app-service-web-app-azure-portal.md) |



## Autres connecteurs

[Connecteurs d'intégration BizTalk](app-service-logic-integration-connectors.md) |
[Connecteurs d'entreprise](app-service-logic-enterprise-connectors.md) |
[Connecteurs B2B](app-service-logic-b2b-connectors.md) |
[Connecteurs de réseaux sociaux](app-service-logic-social-connectors.md) |
[Connecteurs de protocoles](app-service-logic-protocol-connectors.md) |
[Connecteurs d'application et de services de données](app-service-logic-data-connectors.md) |
[Liste de connecteurs et d'applications API](app-service-logic-connectors-list.md)

<!--HONumber=49-->
<properties 
	pageTitle="Connecteurs de données d'applications API Microsoft Azure | Microservice d'applications API" 
	description="Découvrez comment créer des applications API de connecteur de données Microsoft Azure et ajouter l’application API à votre application logique ; microservices" 
	services="app-service\logic" 
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
	ms.date="03/31/2015" 
	ms.author="deonhe"/>


# Connecteurs de données dans Microsoft Azure App Service


## Qu’est-ce qu’un connecteur d’application API Application + services de données ?
Les connecteurs Application + services de données sont des « applications API » qui peuvent se connecter à une large gamme d’applications de services de données et effectuer plusieurs *actions* pour le compte de l’utilisateur authentifié. Vous pouvez également configurer la plupart de ces connecteurs avec un *déclencheur*. Les déclencheurs sont des événements (semblables aux événements dans .NET Framework) qui peuvent être configurés sur certains connecteurs pour initier un flux de travail.

Par exemple, vous pouvez configurer une instance du connecteur Dropbox avec un déclencheur *nouveau fichier* qui est activé chaque fois qu’un nouveau fichier est ajouté au compte Dropbox analysé. Vous pouvez ensuite configurer ce déclencheur pour initier une *action* Get qui *récupère* le fichier et le télécharge vers une liste SharePoint locale.


Voici une brève présentation de chacun des connecteurs Application + services de données disponibles dans la galerie Azure :

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
<td>Aucun 
</tr>

<tr>
<td>Azure Service Bus
<td>Le connecteur Azure Services Bus vous permet d'envoyer des messages à partir d'entités Service Bus telles que des files d'attente et des rubriques, et de recevoir des messages provenant d'entités Services Bus telles que des files d'attente et des abonnements.
<td>Nouveaux messages
<td>Envoyer un message
</tr>
<tr>
<td>Azure Storage Table
<td>Le connecteur Azure Storage Table vous permet de vous connecter à Azure Storage Table et d'effectuer différentes actions, telles qu'Obtenir une entité, Interroger des entités, Insérer une entité, Mettre à jour une entité, Supprimer une entité et un déclencheur pour extraire des données.
<td>Nouvelle entité
<td><li>Obtenir une entité
	<li>Insérer une entité
	<li>Supprimer une entité
	<li>Mettre à jour une entité
	<li>Requête

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
<td>DB2
<td>Le connecteur DB2 se connecte à une base de données IBM DB2, sur site et sur une machine virtuelle Azure exécutant un système d’exploitation Windows. Il peut mapper les opérations API web et API OData avec les commandes SQL Informix. Lorsque vous l’utilisez en local, VPN ou ExpressRoute Azure peuvent être utilisés. Ce connecteur comprend également le client Microsoft pour DRDA pour se connecter à un serveur Informix via un réseau TCP/IP.
<td>Aucun
<td><li>Sélection de la table
<li>Insérer
<li>Mettre à jour
<li>Supprimer
<li>Instruction personnalisée
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
<td>Aucun
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
<td>Informix
<td>Le connecteur Informix se connecte à une base de données locale IBM Informix, localement et sur une machine virtuelle Azure exécutant un système d’exploitation Windows. Il peut mapper les opérations API web et API OData avec les commandes SQL Informix. Lorsque vous l’utilisez en local, VPN ou ExpressRoute Azure peuvent être utilisés. Ce connecteur comprend également un client Microsoft pour DRDA pour se connecter à un serveur Informix via un réseau TCP/IP.
<td>Aucun
<td><li>Sélection de la table
<li>Insérer
<li>Mettre à jour
<li>Supprimer
<li>Instruction personnalisée
</tr>

<tr>
<td>Microsoft SQL
<td>Un connecteur Microsoft SQL vous permet de créer et de modifier des entrées dans des tables de bases de données Microsoft SQL Server et SQL Azure.
<td>Interrogation des données
<td><li>Insérer dans la table
	<li>Mettre à jour la table
	<li>Sélectionner dans la table
	<li>Supprimer de la table
	<li>Appeler une procédure stockée
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
<td>MQ
<td>Le connecteur MQ se connecte à une base de données IBM WebSphere MQ server version&#160;8, sur site et sur une machine virtuelle Azure exécutant un système d’exploitation Windows. Lorsque vous l’utilisez en local, VPN ou ExpressRoute Azure peuvent être utilisés. Ce connecteur comprend également le client Microsoft pour MQ.<br/><br/><strong>Remarque</strong> Ne peut actuellement pas être utilisé avec Logic Apps.
<td>Aucun
<td>Aucun
</tr>

<tr>
<td>Office&#160;365
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
<td><li>Fichiers&#160;: télécharger, supprimer, énumérer, télécharger
</tr>

<tr>
<td>Oracle
<td>Un connecteur de base de données Oracle vous permet de créer et de modifier des entrées dans une table de base de données Oracle.
<td>Nouvelles données, en fonction de la requête
<td><li>Table&#160;: insérer, mettre à jour, sélectionner, supprimer
<li>Appeler une procédure stockée
</tr>

<tr>
<td>SharePoint Online
<td>Un connecteur Microsoft SharePoint vous permet de créer et de modifier des documents et des éléments de liste dans SharePoint Server ou Microsoft SharePoint Online.
<td><li>Nouveau document
<li>Nouvel élément de liste
<td><li>Bibliothèque de documents&#160;: télécharger, supprimer et obtenir
<li>Liste&#160;: insérer un élément

</tr>

<tr>
<td>SharePoint Server
<td>Le connecteur SharePoint Server vous permet de gérer des documents et des éléments de liste dans votre serveur SharePoint. Les informations d'identification par défaut, l'authentification Windows et l'authentification basée sur les formulaires sont prises en charge. Vous devez fournir une chaîne de connexion Service Bus et installer l'agent écouteur local avant de vous connecter au serveur.
<td><li>Nouveau document
<li>Nouvel élément de liste
<td><li>Bibliothèque de documents&#160;: télécharger, supprimer et obtenir
<li>Liste&#160;: insérer un élément
</tr>
</table>


## Pourquoi utiliser des connecteurs ?

Les connecteurs accélèrent le développement d'application et permettent même aux non-développeurs de créer des applications d'entreprise entièrement fonctionnelles sans avoir à apprendre un langage de programmation ou à écrire du code.

Maintenant que vous avez une idée de ce dont sont capables nos connecteurs de services de données et d'applications, nous allons examiner quelques exemples d'utilisation simples pour ces connecteurs.

### Analyse de votre dossier d'échang et mise à jour de SharePoint
Imaginez que vous travaillez pour une société de construction qui reçoit des fichiers très volumineux contenant des plans. Ces fichiers sont généralement trop gros pour être gérés par courrier électronique. Votre société ouvre donc un compte Dropbox et demande à ses clients de déposer les plans dans le dossier Dropbox. Vous pourriez alors demander à chaque employé de vérifier constamment si les dossiers Dropbox contiennent de nouveaux projets, puis les télécharger vers votre serveur SharePoint. Néanmoins, vous êtes convaincu qu'il doit exister un meilleur moyen de procéder. Heureusement, vous avez découvert que Microsoft avait récemment publié son connecteur Application + services de données pour Dropbox, Sharepoint et d'autres services de données. Vous pouvez facilement créer des connecteurs pour Dropbox et SharePoint, les ajouter à une application logique et les configurer pour télécharger chaque nouveau fichier depuis votre compte Dropbox vers votre liste SharePoint. Puisque le connecteur Dropbox a un déclencheur de *nouveau message*, vous pouvez l’utiliser pour signaler à votre application logique qu’un nouveau fichier est disponible. Le connecteur Dropbox peut ensuite télécharger le fichier. Vous pouvez ensuite configurer votre connecteur Sharepoint pour télécharger le fichier vers une liste SharePoint à l’aide de l’action *télécharger* de SharePoint. Vous pouvez faire tout ceci sans écrire une seule ligne de code.

Allons-y.

## Créer un connecteur
Vous pouvez créer les applications API de connecteur à l’aide du portail Azure.

### Créer un connecteur SharePoint dans le portail Microsoft Azure

1. Dans le portail Azure, sélectionnez **NOUVEAU** > **Web + mobile** > **Azure Marketplace**
2. **Recherchez** le connecteur ou sélectionnez-le dans la liste. Une fois le connecteur sélectionné, un nouveau panneau ou une nouvelle fenêtre s'ouvre. Sélectionnez **Créer**. 
3. Entrez les propriétés suivantes pour le connecteur : <table> <tr><th>Propriété</th> <th>Description</th> </tr> <tr><td>Nom</td> <td>Entrez n’importe quel nom pour votre application API. Par exemple, vous pouvez la nommer RulesDiscountTaxCode ou APIAppValidateXML.</td> </tr> <tr><td>Plan App Service</td> <td>Indique votre plan de paiement. Vous pouvez le modifier si vous avez besoin de plus ou moins de ressources</th> </td> <tr><td>Niveau de tarification</td> <td>Propriété en lecture seule qui indique la catégorie de tarification dans votre abonnement Azure.</td> </tr> <tr><td>Groupe de ressources</td> <td>Créez-en un nouveau ou utilisez un groupe existant. Cette propriété est décrite dans la section Utiliser des groupes de ressources.</td> </tr> <tr><td>Abonnement</td> <td>Propriété en lecture seule qui indique votre abonnement actuel</td> </tr> <tr><td>Emplacement</td> <td>Emplacement géographique qui héberge votre service Azure.</td></tr> <tr><td>Ajouter au tableau d’accueil</td> <td>Sélectionnez cette option pour ajouter l’application API à votre Tableau d’accueil (la page d’accueil)</td></tr> </table> 
4. Sélectionnez **Créer**. Votre connecteur est créé. L'opération peut prendre un certain temps et l'écran d'accueil s'affiche lors de la création du connecteur. Utilisez l'élément de menu Notifications à gauche pour surveiller l'état de votre connecteur.

Maintenant que vous avez créé votre premier connecteur, vous pouvez créer une application web, mobile ou logique avec.


### Accéder au connecteur avec des API REST

[Accéder aux connecteurs avec des API REST](http://go.microsoft.com/fwlink/p/?LinkId=529766)

## Ajouter votre connecteur à une application 
Microsoft Azure App Service expose différents types d'applications qui peuvent utiliser ces connecteurs. Par exemple, vous pouvez créer une application *logique* en combinant *de façon logique* un ou plusieurs de vos connecteurs dans une même application.

Pour utiliser vos connecteurs dans votre application *logique*, vous devez sélectionner un connecteur configuré dans la liste, l’ajouter à votre flux de travail de conception et apporter les modifications de configuration nécessaires. Il est alors prêt à être utilisé.

Pour suivre ces étapes, vous avez besoin d'une application web, mobile ou logique. Consultez <> pour connaître les étapes spécifiques. Une fois que votre application est disponible, ajoutez vos connecteurs. Voici comment procéder :

Exécutez les étapes suivantes pour ajouter un connecteur à une application logique :

1. Dans le Tableau d’accueil du portail Azure (page d’accueil), accédez à **Marketplace** et recherchez vos applications logiques, mobiles ou web. 

	Si vous créez une application, recherchez Application logique, Application mobile ou Application web. Sélectionnez l’application puis, dans le nouveau panneau, sélectionnez **Créer**. [Créer une application logique](app-service-logic-create-a-logic-app.md) répertorie les étapes.

2. Ouvrez votre application et sélectionnez **Déclencheurs et actions**.
3. Dans la **Galerie**, sélectionnez le connecteur. Il est ajouté à votre application.
4. Configurez le connecteur :
5. Chaque connecteur possède des propriétés qui sont spécifiques au service et à l'environnement auquel il se connecte. Entrez les détails des propriétés. N'oubliez pas que certaines propriétés sont facultatives.
6. Sélectionnez **OK** pour enregistrer vos modifications.


## Sécurité
Les connecteurs utilisent OAuth ou des noms d'utilisateurs et des mots de passe.


## En savoir plus sur les applications logiques et les applications web
[Qu’est-ce qu’une application logique ?](app-service-logic-what-are-logic-apps.md) | [Sites Web et Web Apps dans Azure App Service](app-service-web-app-azure-portal.md) |



## Autres connecteurs
[Connecteurs d’intégration BizTalk](app-service-logic-integration-connectors.md) | [Connecteurs d’entreprise](app-service-logic-enterprise-connectors.md) | [Connecteur B2B](app-service-logic-b2b-connectors.md) | [Connecteurs sociaux](app-service-logic-social-connectors.md) | [Connecteurs de protocole](app-service-logic-protocol-connectors.md) | [Connecteurs application + services de données](app-service-logic-data-connectors.md) | [Liste des connecteurs et des applications API](app-service-logic-connectors-list.md)<br/><br/> [Qu’est-ce qu’un connecteur et une application API BizTalk](app-service-logic-what-are-biztalk-api-apps.md)

<!--HONumber=54-->
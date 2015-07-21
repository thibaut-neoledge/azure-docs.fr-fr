<properties 
	pageTitle="Connecteurs de réseaux sociaux d'applications API Microsoft Azure | Microservice d'applications API" 
	description="Description=&quot;Découvrez comment créer des applications API de connecteurs de réseaux sociaux Microsoft Azure et ajouter l'application API à votre application logique ; microservices" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor=""/>

<tags
	ms.service="app-service-logic" 
	ms.workload="connectors" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="mandia"/>

# Connecteurs de réseaux sociaux dans Microsoft Azure App Service


## Qu'est-ce qu'un connecteur de réseau social d'application API ?
Les connecteurs de réseaux sociaux sont des « applications API » qui peuvent se connecter à une large gamme d’applications de réseaux sociaux et effectuer plusieurs *actions* pour le compte de l’utilisateur authentifié. Vous pouvez également configurer la plupart des connecteurs avec un *déclencheur*. Les déclencheurs sont des événements (semblables aux événements dans .NET Framework) qui peuvent être configurés sur certains connecteurs. Les déclencheurs peuvent ensuite exécuter les actions qui ont été configurées pour le connecteur (ou tout autre connecteur). Par exemple, une instance du connecteur Twitter peut être configurée avec un déclencheur *nouveau message*, où le déclencheur est défini comme tout nouveau tweet qui mentionne @VisualStudio. Vous pouvez ensuite configurer ce déclencheur pour initier une *Action* pour retweeter le tweet.

Les connecteurs de réseaux sociaux suivants sont disponibles auprès de Microsoft :

- Facebook
- Twitter
- Chatter
- Yammer
- Twilio

Voici une brève description de chacun des connecteurs de réseaux sociaux :

<table>
<tr>
<th> Nom</th>
<th> Description</th>
<th> Déclencheurs</th>
<th>Actions</th>

<tr>
<td>Facebook
<td>Un connecteur Facebook vous permet de publier et de lire des messages, des commentaires, des événements et des états à partir de votre flux Facebook. Vous pouvez également publier des photos et des vidéos.
<td>Nouvelle publication
<td><li>Publier une publication
	<li>Publier une photo 
</tr>

<tr>
<td>Twitter
<td>Un connecteur Twitter vous permet d'effectuer plusieurs opérations, telles que publier, recevoir des tweets et rechercher des tweets. Vous pouvez également utiliser un connecteur Twitter pour vous tenir au courant de ce qui se passe sur Twitter et obtenir des détails tels que des tweets, des chronologies, des amis et des abonnés.
<td>Nouveaux tweets
<td><li>Rechercher des tweets
	<li>Tweeter
	<li>Obtenir la chronologie de l'utilisateur
	<li>Retweeter
</tr>

<tr>
<td>Chatter
<td>Un connecteur Chatter peut servir à lire, publier et rechercher des messages sur votre flux Chatter.
<td>Nouveau message
<td><li>Publier un message
<li>Rechercher un message
</tr>

<tr>
<td>Yammer
<td>Un connecteur Yammer vous permet de publier et de lire des messages de groupes Yammer.
<td>Nouveaux messages
<td><li>Publier un message	
	<li>Obtenir le message à partir du groupe\flux	
</tr>

<tr>
<td>Twilio
<td>Un connecteur Twilio vous permet d'envoyer et de recevoir des SMS à partir de votre compte Twilio. Il vous permet également de récupérer des numéros de téléphone et des données d'utilisation.
<td>N/A
<td><li>Messages&#160;: envoyer, énumérer, obtenir et rechercher
	<li>Numéros de téléphone&#160;: acheter, énumérer et obtenir des numéros locaux et gratuits
</tr>
</table>


Maintenant que vous avez une idée de ce dont sont capables nos connecteurs de réseaux sociaux, nous allons examiner quelques exemples d'utilisation simples pour ces connecteurs.

## Pourquoi utiliser des connecteurs ?

Les connecteurs accélèrent le développement d'application et permettent même aux non-développeurs de créer des applications d'entreprise entièrement fonctionnelles sans avoir à apprendre un langage de programmation ou à écrire du code.

### Analyse des commentaires des clients ###
Imaginez que votre société a récemment publié une nouvelle application et que l'équipe veut savoir ce qu'en disent les clients sur les réseaux sociaux. Chaque membre de l'équipe pourrait vérifier régulièrement les différents sites de réseaux sociaux et deviner les mots clés que les clients pourraient utiliser pour discuter de votre application. Une manière plus élégante consiste à créer simplement un connecteur Twitter et à le configurer pour surveiller des hashtags, mentions et mots clés Twitter spécifiques. Vous pouvez ensuite utiliser le connecteur SMTP pour envoyer aux membres de votre équipe un message électronique avec le contenu des tweets pertinents. Un scénario semblable consiste à surveiller Facebook et Yammer et à envoyer un SMS, via un connecteur Twilio, à votre équipe DevOps si le contenu des publications Facebook ou Yammer suggèrent que vos clients ont rencontré un problème critique. Vous pouvez faire tout ceci sans écrire une seule ligne de code.

Allons-y.


## Créer un connecteur
Vous pouvez créer un connecteur sur le portail Azure.

### Créer un connecteur de réseaux sociaux dans le portail Microsoft Azure

1. Dans le portail Azure, sélectionnez **Nouveau** > **Web + Mobile** > **Azure Marketplace**.
2. **Recherchez** le connecteur ou sélectionnez-le dans la liste. Une fois le connecteur sélectionné, un nouveau panneau ou une nouvelle fenêtre s'ouvre. Sélectionnez **Créer**. 
3. Entrez les propriétés suivantes pour le connecteur : 
	<table>
    <tr><th>Propriété</th> <th>Description</th> </tr>
    <tr><td>Nom</td> <td>Entrez un nom pour votre application API. Par exemple, vous pouvez la nommer RulesDiscountTaxCode ou APIAppValidateXML.</td> </tr>
    <tr><td>Plan App Service</td> <td>Indique votre plan de paiement. Vous pouvez le modifier si vous avez besoin de plus ou moins de ressources.</th> </td>
    <tr><td>Niveau de tarification</td> <td>Propriété en lecture seule qui indique la catégorie de tarification dans votre abonnement Azure.</td> </tr>
    <tr><td>Groupe de ressources</td> <td>Créez-en un nouveau ou utilisez un groupe existant. Cette propriété est décrite dans la section Utiliser des groupes de ressources.</td> </tr>
    <tr><td>Abonnement</td> <td>Propriété en lecture seule qui indique votre abonnement actuel</td> </tr>
    <tr><td>Emplacement</td> <td>Emplacement géographique qui héberge votre service Azure. </td></tr>
    <tr><td>Ajouter au Tableau d'accueil</td> <td>Sélectionnez cette option pour ajouter l'application d'API à votre Tableau d'accueil (la page d'accueil).</td></tr>
</table>
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
[Qu’est-ce qu’une application logique ?](app-service-logic-what-are-logic-apps.md) | 
[Sites Web et Web Apps dans Azure App Service](../app-service-web/app-service-web-overview.md) |


## Autres connecteurs

[Connecteurs d’intégration BizTalk](app-service-logic-integration-connectors.md) | 
[Connecteurs d’entreprise](app-service-logic-enterprise-connectors.md) | 
[Connecteurs B2B](app-service-logic-b2b-connectors.md) | 
[Connecteurs de protocole](app-service-logic-protocol-connectors.md) | 
[Connecteurs application + services de données](app-service-logic-data-connectors.md) | 
[Liste des connecteurs et des applications API](app-service-logic-connectors-list.md)<br/><br/> 
[Qu’est-ce qu’un connecteur et une application API BizTalk](app-service-logic-what-are-biztalk-api-apps.md)
 

<!---HONumber=62-->
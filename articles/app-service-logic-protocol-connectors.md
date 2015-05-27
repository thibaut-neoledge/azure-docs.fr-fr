<properties 
	pageTitle="Connecteurs de protocoles d'applications API Microsoft Azure | Microservice d'applications API" 
	description="Description=&quot;Découvrez comment créer des applications API de connecteur de protocole Microsoft Azure et ajouter l'application API à votre application logique ; microservices" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="deonhe"/>


# Connecteurs de protocoles dans Microsoft Azure App Service


## Qu'est-ce un connecteur de protocole ?
Les connecteurs de protocoles sont des applications API* que vous pouvez utiliser pour créer des applications qui communiquent à l'aide d'une large gamme de protocoles tels que HTTP, SMTP, FTP et ainsi de suite.

Vous pouvez accéder aux connecteurs de protocoles dans le portail Microsoft Azure en cliquant sur **Nouveau** > **Web + Mobile** > **Azure Marketplace** > entrez le mot clé de recherche **protocole** > appuyez sur Entrée.

Les connecteurs de protocoles suivants sont disponibles dans Azure Marketplace :

- SFTP
- POP3
- FTP
- HTTP
- SMTP
- Fichier

Voici une brève description de chacun des connecteurs de protocoles :

<table>
<tr>
<th> Nom</th>
<th> Description</th>
<th> Déclencheurs</th>
<th> Actions</th>

<tr>
<td>SFTP
<td>Un connecteur SFTP vous permet de télécharger des fichiers vers ou à partir d'un serveur SFTP.
<td>Nouveau fichier disponible dans un dossier SFTP

<td>Fichiers&#160;: télécharger, supprimer, énumérer et obtenir 

</tr>

<tr>
<td>POP3
<td>Un connecteur POP3 vous permet de télécharger du courrier électronique à partir d'un serveur POP3.
<td>Arrivée d'un nouveau message
<td>N/A
</tr>

<tr>
<td>FTP
<td>Un connecteur FTP vous permet de télécharger des fichiers vers ou à partir d'un serveur FTP.
<td>Nouveau fichier disponible dans un dossier FTP
<td>Fichier&#160;: télécharger, obtenir, supprimer et énumérer
</tr>

<tr>
<td>HTTP
<td>Un connecteur HTTP vous permet d'envoyer des données à des serveurs HTTP à l'aide du protocole HTTP ou HTTPS.
<td>Aucune
<td>Publier, obtenir, supprimer, placer
</tr>

<tr>
<td>SMTP
<td>Un connecteur SMTP vous permet d'envoyer du courrier électronique à partir d'un serveur SMTP.
<td>N/A
<td>Envoi de courrier électronique
</tr>

<tr>
<td>Fichier
<td>Un connecteur de fichier vous permet de vous connecter aux serveur de fichiers locaux et d'effectuer des actions telles que le téléchargement, l'obtention, la suppression et l'énumération de fichiers. Il existe également un déclencheur pour extraire des fichiers.
<td>Ajout de nouveau fichier à l'appareil
<td>Fichier&#160;: télécharger, obtenir, supprimer et énumérer
</tr>


</table>

Maintenant que vous avez une idée de ce dont sont capables nos connecteurs de protocoles, nous allons examiner quelques exemples d'utilisation simples pour ces connecteurs.

### Analyse des commentaires des clients ###
Imaginez que votre société a récemment publié une nouvelle application et que l'équipe veut savoir ce qu'en disent les clients sur les réseaux sociaux. Chaque membre de l'équipe pourrait vérifier régulièrement les différents sites de réseaux sociaux et deviner les mots clés que les clients pourraient utiliser pour discuter de votre application. Une manière plus élégante consiste à créer simplement un connecteur Twitter et à le configurer pour surveiller des hashtags, mentions et mots clés Twitter spécifiques. Vous pouvez ensuite utiliser le connecteur SMTP pour créer un message électronique avec le contenu des tweets pertinents et l'envoyer aux membres de votre équipe. Vous pouvez ensuite utiliser le connecteur HTTP pour publier ces tweets sur un site web d'entreprise interne. Vous pouvez faire tout ceci sans écrire une seule ligne de code.

Allons-y.

## Créer un connecteur

Vous pouvez créer un connecteur à l’aide du portail Azure.

### Créer un connecteur dans le portail Azure

Examinons comment créer un connecteur SMTP à l'aide d'Azure Marketplace.

1. Connectez-vous au [portail](https://manage.windowsazure.com) Microsoft Azure.
2. Sélectionnez **Nouveau** > **Web + Mobile** > **Azure Marketplace**.
3. Dans la **Zone de recherche**, entrez **protocole** puis appuyez sur **Entrée**. Vous pouvez également le sélectionner dans la liste. Une fois le connecteur sélectionné, un nouveau panneau ou une nouvelle fenêtre s'ouvre. Sélectionnez **Créer**. 
4. Entrez les propriétés suivantes :

<table>
<tr><th>Propriété</th> <th>Description</th> </tr>
<tr><td>Nom</td> <td>Description</td> </tr>
<tr><td>Paramètres du package</td> <td>Utilisez la propriété de paramètres du package pour entrer toutes les informations d'authentification, telles que le nom d'utilisateur, le mot de passe, l'adresse du serveur, le numéro de port et ainsi de suite pour le serveur SMTP. </td> </tr>
<tr><td>Plan App Service</td> <td>Indique votre plan de paiement. Vous pouvez le modifier si vous avez besoin de plus ou moins de ressources.</th> </td>
<tr><td>Niveau de tarification</td> <td>Propriété en lecture seule qui indique la catégorie de tarification dans votre abonnement Azure.</td> </tr>
<tr><td>Groupe de ressources</td> <td>Créez-en un nouveau ou utilisez un groupe existant. Cette propriété est décrite dans la section Utiliser des groupes de ressources.</td> </tr>
<tr><td>Abonnement</td> <td>Propriété en lecture seule qui indique votre abonnement actuel</td> </tr>
<tr><td>Emplacement</td> <td>Emplacement géographique qui héberge votre service Azure.</th> </td>
<tr><td>Ajouter au Tableau d'accueil</td> <td>Sélectionnez cette option pour ajouter l'application d'API à votre Tableau d'accueil (la page d'accueil).</td></tr>
</table>

### Accéder aux connecteurs avec des API REST
[Accéder aux connecteurs avec des API REST](http://go.microsoft.com/fwlink/p/?LinkId=529766)

## Ajouter un connecteur à votre application 
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

[Connecteurs d’intégration BizTalk](app-service-logic-integration-connectors.md) | [Connecteurs d’entreprise](app-service-logic-enterprise-connectors.md) | [Connecteur B2B](app-service-logic-b2b-connectors.md) | [Connecteurs sociaux](app-service-logic-social-connectors.md) | [Connecteurs application + services de données](app-service-logic-data-connectors.md) | [Liste des connecteurs et des applications API](app-service-logic-connectors-list.md)<br/><br/> [Qu’est-ce qu’un connecteur et une application API BizTalk](app-service-logic-what-are-biztalk-api-apps.md)

<!--HONumber=54-->
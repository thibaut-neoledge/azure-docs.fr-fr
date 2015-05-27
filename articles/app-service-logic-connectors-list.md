<properties 
	pageTitle="Liste des connecteurs et des applications API | Azure" 
	description="En savoir plus sur les connecteurs et les applications API dans Azure App Service ; architecture microservices" 
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


# Liste des connecteurs et applications API dans Microsoft Azure App Service
Cette rubrique répertorie tous les connecteurs et toutes les applications API disponibles créés par Microsoft.

Pour plus d’informations sur la tarification et une liste de ce qui est inclus dans chaque niveau de service, consultez la page [Tarification Azure App Service](http://azure.microsoft.com/pricing/details/app-service/).


## Connecteurs standard
Le tableau suivant répertorie tous les connecteurs et toutes les applications API créés par Microsoft et disponibles dans les connecteurs standard :

<table border="1">
<tr bgcolor="FAF9F9">
        <th>Nom</th>
        <th>Description</th>
</tr>

<tr>
<td colspan="2"><strong>Connecteurs de services de données et d’applications</strong><br/><br/>La section <a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-data-connectors/">Connecteurs de services de données et d’applications</a> contient des informations plus détaillées sur ces connecteurs.</td>
</tr>

<tr>
<td>Azure&#160;HDInsight</td>
<td>Azure HDInsight déploie et approvisionne des clusters Apache Hadoop dans le cloud, et fournit une infrastructure logicielle conçue pour gérer et analyser les données volumineuses, et générer des rapports à leur sujet. Ce connecteur crée un cluster Hadoop sur Azure et soumet différents travaux Hadoop, notamment Hive, Pig, MapReduce et Streaming MapReduce. Grâce à ce connecteur, vous pouvez faire tourner un cluster, soumettre un travail et attendre que le travail soit terminé.</td>
</tr>

<tr>
<td>Azure Service&#160;Bus</td>
<td>Azure Service Bus est un système de messagerie générique, basé sur le cloud qui permet de pratiquement tout connecter. Il permet l'échange de messages d'une façon faiblement couplée pour une meilleure évolutivité et une meilleure fiabilité. À l’aide de ce connecteur, vous pouvez envoyer des messages à partir d’entités Service Bus comme les files d’attente et les rubriques et recevoir des messages d’entités Service Bus comme les files d’attente et les abonnements.</td>
</tr>

<tr>
<td>Azure Storage Blob</td>
<td>Azure Storage Blob stocke de gros volumes de données non structurées, telles que du texte ou des données binaires, accessibles depuis n'importe où dans le monde. À l’aide de ce connecteur, vous pouvez vous connecter au stockage d’objets blob et télécharger, obtenir, supprimer, copier un objet blob, en répertorier dans un conteneur, en créer un instantané et utiliser un déclencheur pour en récupérer.</td>
</tr>

<tr>
<td>Box</td>
<td>Box est un service de partage de fichiers. Le connecteur Box se connecte à Box et permet de télécharger, d’obtenir, de supprimer un fichier, de répertorier des fichiers et utilise un déclencheur pour récupérer des fichiers.</td>
</tr>

<tr>
<td>Dropbox</td>
<td>DropBox est un service d'hébergement de fichiers. À l’aide de ce connecteur, vous pouvez vous connecter à DropBox et télécharger, obtenir, supprimer un fichier, répertorier des fichiers et utiliser un déclencheur pour récupérer des fichiers.</td>
</tr>

<tr>
<td>Microsoft Office&#160;365</td>
<td>Le connecteur Office&#160;365 permet d'envoyer et recevoir des courriers électroniques, de gérer votre calendrier et de gérer vos contacts à l'aide de votre compte Office&#160;365. Vous pouvez également envoyer, recevoir et obtenir des messages électroniques, créer et supprimer des événements dans votre calendrier, ainsi que créer, mettre à jour, obtenir et supprimer vos contacts.</td>
</tr>

<tr>
<td>Microsoft&#160;OneDrive</td>
<td>Microsoft OneDrive est un service d'hébergement de fichiers. Ce connecteur OneDrive se connecte à votre Microsoft OneDrive personnel et permet de télécharger, d’obtenir, de supprimer un fichier, de répertorier des fichiers et d’utiliser un déclencheur pour récupérer des fichiers.</td>
</tr>

<tr>
<td>Microsoft SharePoint</td>
<td>Le connecteur Microsoft SharePoint se connecte au serveur Microsoft SharePoint Server local ou à SharePoint Online et permet de gérer des documents et éléments de liste. Vous pouvez également créer, mettre à jour, obtenir et supprimer des documents et éléments de liste. Différentes méthodes d'authentification comme les informations d'identification par défaut, OAuth&#160;2.0, l'authentification Windows et l'authentification par formulaire sont prises en charge.</td>
</tr>

<tr>
<td>Microsoft SQL Server</td>
<td>Le connecteur Microsoft SQL se connecte au serveur SQL Server local ou à la base de données SQL Azure. Vous pouvez créer, mettre à jour, obtenir et supprimer des entrées dans une table de base de données SQL.</td>
</tr>

<tr>
<td colspan="2"><strong>Connecteurs de réseaux sociaux</strong><br/><br/>La section <a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-social-connectors/">Connecteurs de réseaux sociaux</a> contient des informations plus détaillées sur ces connecteurs.</td>
</tr>

<tr>
<td>Chatter</td>
<td>Chatter est un réseau social d'entreprise. Le connecteur Chatter se connecte à Chatter et permet de publier un message, de rechercher et d’utiliser un déclencheur pour récupérer les nouveaux messages.</td>
</tr>

<tr>
<td>Facebook</td>
<td>Facebook est un service de réseau social. Le connecteur Facebook se connecte à Facebook et permet de publier un message, une vidéo, des photos, des offres et ainsi de suite. Vous pouvez également récupérer des messages, commentaires, événements, flux d'utilisateur, informations utilisateur sur les différents «&#160;J'aime&#160;», comme les livres, jeux, films et ainsi de suite. Ce connecteur inclut également un déclencheur pour récupérer les nouveaux messages publiés sur une page.</td>
</tr>

<tr>
<td>Microsoft Yammer</td>
<td>Microsoft Yammer est un réseau social d'entreprise. Le connecteur Yammer se connecte à Yammer, inclut une action de publication de message et un déclencheur pour récupérer les nouveaux messages.</td>
</tr>

<tr>
<td>Twilio</td>
<td>Twilio est un service SaaS orienté communication. Le connecteur Twilio se connecte à Twilio et permet d’envoyer, d’obtenir, de répertorier des messages, de répertorier l’utilisation, d’obtenir des numéros verts disponibles, d’obtenir des numéros de portables disponibles, d’obtenir des numéros locaux disponibles, de répertorier les numéros de téléphone entrants, d’obtenir le numéro de téléphone entrant et d’ajouter un numéro de téléphone entrant.</td>
</tr>

<tr>
<td>Twitter</td>
<td>Twitter est un service de réseau social en ligne qui permet aux utilisateurs d'envoyer et de lire des messages courts de 140&#160;caractères. Le connecteur Twitter se connecte à Twitter et permet d'obtenir le fil d'actualité de l'utilisateur, de rechercher des tweets, d'obtenir des abonnés, d'obtenir des amis, de rechercher un utilisateur, d'obtenir le fil d'actualité de la page d'accueil, celui des mentions, de publier un tweet, de publier un tweet pour un utilisateur et d'envoyer un message direct. Le connecteur Twitter utilise également des déclencheurs pour obtenir des tweets par mot clé, par descripteur d’utilisateur et par hashtag.</td>
</tr>

<tr>
<td colspan="2"><strong>Connecteurs de protocoles</strong><br/><br/>La section <a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-protocol-connectors/">Connecteurs de protocoles</a> contient des informations plus détaillées sur ces connecteurs.</td>
</tr>

<tr>
<td>Fichier</td>
<td>Le connecteur File permet aux clients d'envoyer ou de télécharger des fichiers vers un système de fichiers ou un réseau, et de recevoir ou télécharger des fichiers à partir d'un système de fichiers ou un réseau. À l’aide de ce connecteur, vous pouvez vous connecter au serveur de fichiers local et télécharger, obtenir, supprimer un fichier, répertorier des fichiers et utiliser un déclencheur pour récupérer des fichiers.</td>
</tr>

<tr>
<td>FTP<br/>FTPS</td>
<td>Le protocole FTP (File Transfer Protocol) est un protocole réseau couramment utilisé pour transférer des fichiers d'un hôte à l'autre. Le connecteur FTP permet de télécharger, d’obtenir, de supprimer un fichier, de répertorier des fichiers et également d’utiliser un déclencheur pour récupérer des fichiers.</td>
</tr>

<tr>
<td>Écouteur HTTP</td>
<td>L’écouteur HTTP ouvre un point de terminaison qui agit comme serveur HTTP et écoute les demandes HTTP entrantes.</td>
</tr>

<tr>
<td>POP3<br/>IMAP</td>
<td>POP3 (Post Office Protocol) est le protocole utilisé par un client de messagerie pour récupérer du courrier électronique auprès d'un serveur de messagerie. Le connecteur POP3 se connecte à un serveur POP3 et inclut un déclencheur pour récupérer les messages électroniques avec des pièces jointes.</td>
</tr>

<tr>
<td>SFTP</td>
<td>SFTP (SSH File Transfer Protocol) est un protocole commun utilisé pour transférer des fichiers de manière sécurisée. Le connecteur SFTP permet de télécharger, d’obtenir, de supprimer un fichier, de répertorier des fichiers et d’utiliser un déclencheur pour récupérer des fichiers.</td>
</tr>

<tr>
<td>SMTP</td>
<td>SMTP (Simple Mail Transfer Protocol) est le protocole utilisé entre un client de messagerie et un serveur de messagerie pour envoyer des messages. Le connecteur SMTP se connecte à un serveur SMTP et permet d'envoyer du courrier électronique avec des pièces jointes.</td>
</tr>

<tr>
<td colspan="2"><strong>Connecteurs d’entreprise</strong><br/><br/>La section <a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-enterprise-connectors">Connecteurs d’entreprise</a> contient des informations plus détaillées sur ces connecteurs.</td>
</tr>

<tr>
<td>Marketo</td>
<td>Marketo développe des logiciels d'automatisation marketing qui offrent des services de marketing entrant, marketing social, CRM et d'autres services associés. Le connecteur Marketo se connecte à Marketo et permet de créer/mettre à jour des prospects, d’obtenir les modifications de prospect, de planifier une campagne, de demander une campagne, d’obtenir des prospects, d’obtenir des campagnes/répertorier des informations, d’ajouter des prospects à la liste et d’en supprimer de la liste.</td>
</tr>

<tr>
<td>QuickBooks</td>
<td>À l’aide du connecteur QuickBooks, vous pouvez créer, mettre à jour, lire, supprimer et interroger différentes entités d’Intuit QuickBooks, comme des clients, articles, factures, etc.</td>
</tr>

<tr>
<td>Salesforce</td>
<td>Le connecteur Salesforce gère différentes entités, comme des comptes, des prospects, des opportunités, des cas, etc. dans votre compte Salesforce. Vous pouvez également créer, mettre à jour, insérer, interroger et supprimer diverses entités.</td>
</tr>

<tr>
<td>SugarCRM</td>
<td>Le connecteur SugarCRM se connecte à SugarCRM Online et permet de créer, mettre à jour, lire et supprimer différents types de modules comme des comptes, des contacts, des produits et ainsi de suite.</td>
</tr>

</table>


## Applications API IAE BizTalk
Le tableau suivant répertorie toutes les applications API créées par Microsoft, disponibles dans les services IAE BizTalk :

	Name  | Description
------------- | -------------
Encodeur de fichier plat BizTalk | Fournit une interopérabilité entre des données de fichier plat (comme Excel et csv) et des données XML. Cette application API peut convertir une instance de fichier plat au format XML et vice versa.
Service BizTalk Transform | L'application API BizTalk Transform convertit des données d'un format à l'autre. Vous pouvez également télécharger un mappage existant (fichier .trfm), afficher la représentation graphique du mappage qui affiche les liens entre les schémas source et cible, et utiliser une fonctionnalité de test avec un exemple de contenu XML d'entrée. Différentes fonctions intégrées sont également disponibles, notamment les manipulations de chaînes, l’affectation conditionnelle, les expressions arithmétiques, les formateurs de date et d’heure, l’itération, etc.
Validateur XML BizTalk | L'application API Validateur valide les données XML par rapport aux schémas XML prédéfinis. Vous pouvez utiliser les schémas existants ou en générer en fonction d’une instance de fichier plat, d’une instance JSON ou de connecteurs existants.
Extracteur XPath BizTalk | L’application API Extracteur XPath recherche et extrait des données du contenu XML en fonction d’une expression XPath donnée.

Consultez [Connecteurs d’intégration BizTalk](app-service-logic-integration-connectors.md) pour plus d’informations sur ces applications API.


## Connecteurs et applications API EDI BizTalk
Le tableau suivant répertorie tous les connecteurs et toutes les applications API créées par Microsoft, disponibles dans les services EDI BizTalk :

	Name  | Description
------------- | -------------
Connecteur AS2 | Le connecteur AS2 peut recevoir et envoyer des messages via le protocole de transport AS2 dans les communications entreprise-entreprise. Les données sont transportées de manière fiable et sécurisée sur Internet à l’aide de certificats numériques et du chiffrement.
BizTalk EDIFACT | L’application API EDIFACT reçoit et envoie des messages à l’aide du protocole EDIFACT dans les communications entreprise-entreprise.
BizTalk X12 | L’application API X12 reçoit et envoie des messages à l’aide du protocole X12 dans les communications entreprise-entreprise.
Gestion des partenaires commerciaux BizTalk | L'application API TPM (Trading Partner Management) de gestion des partenaires commerciaux définit et rend persistantes les relations entreprise-entreprise à l'aide de partenaires, contrats, schémas et certificats utilisés dans les contrats. Ces relations sont mises en œuvre à l’aide des applications API AS2, EDIFACT et X12.

Consultez [Connecteurs B2B](app-service-logic-b2b-connectors.md) pour plus d’informations sur ces applications API.


## Application API de règles BizTalk
Le tableau suivant répertorie toutes les applications API créées par Microsoft, disponibles dans les règles BizTalk :

	Name  | Description
------------- | -------------
Règles BizTalk | Les règles BizTalk définissent et contrôlent la structure, le fonctionnement et la stratégie d'une organisation. Les stratégies d’entreprise peuvent être mises à jour sans recompilation et sans redéploiement des applications associées.

Consultez [Connecteurs d’intégration BizTalk](app-service-logic-integration-connectors.md) pour plus d’informations sur cette application API.


## Connecteurs Premium
Le tableau suivant répertorie les connecteurs et les applications API créés par Microsoft et disponibles dans les connecteurs Premium :

	Name  | Description
------------- | -------------
Connecteur DB2 | Le connecteur DB2 se connecte à une base de données IBM DB2, localement et sur une machine virtuelle Azure exécutant un système d’exploitation Windows. Il peut mapper les opérations API web et API OData avec les commandes SQL Informix. Les actions comprennent les instructions table select, insert, update, delete et les instructions personnalisées<br/><br/>[Connecteurs de services de données et d’applications](app-service-logic-data-connectors.md)
Informix | Le connecteur Informix se connecte à une base de données locale IBM Informix, localement et sur une machine virtuelle Azure exécutant un système d’exploitation Windows. Il peut mapper les opérations API web et API OData avec les commandes SQL Informix. Les actions comprennent les instructions table select, insert, update, delete et les instructions personnalisées.<br/><br/>[Connecteurs de services de données et d’applications](app-service-logic-data-connectors.md)
MQ | Le connecteur MQ se connecte au serveur IBM WebSphere MQ Server, localement et sur une machine virtuelle Azure exécutant un système d’exploitation Windows.<br/><br/><strong>Remarque</strong> Ne peut actuellement pas être utilisé avec Logic Apps.<br/><br/>[Application + connecteurs de services de données](app-service-logic-data-connectors.md)
Oracle Database | Le connecteur de base de données Oracle se connecte à une base de données Oracle localement et peut créer, mettre à jour, obtenir et supprimer des entrées dans une table de la base de données. <br/><br/>[Connecteurs de services de données et d’applications](app-service-logic-data-connectors.md)
SAP | Le connecteur SAP se connecte à un serveur SAP local et appelle RFC, BAPI et tRFC et envoie des IDOC.<br/><br/>[Connecteurs d’entreprise](app-service-logic-enterprise-connectors.md)


## Créer vos propres connecteurs et applications API
Consultez la page [Créer des connecteurs avec les API REST](http://go.microsoft.com/fwlink/p/?LinkId=529766).


## Autres connecteurs

[Connecteurs d’intégration BizTalk](app-service-logic-integration-connectors.md)<br/> [Connecteurs d’entreprise](app-service-logic-enterprise-connectors.md)<br/> [Connecteurs B2B](app-service-logic-b2b-connectors.md)<br/> [Connecteurs sociaux](app-service-logic-social-connectors.md)<br/> [Connecteurs de protocole](app-service-logic-protocol-connectors.md)<br/> [Connecteurs application + services de données](app-service-logic-data-connectors.md)<br/><br/> [Qu’est-ce qu’un connecteur et une application API BizTalk](app-service-logic-what-are-biztalk-api-apps.md)

<!--HONumber=54-->
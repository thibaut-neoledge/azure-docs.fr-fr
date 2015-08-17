<properties 
	pageTitle="Connecteurs et applications API entreprise-entreprise dans Microsoft Azure App Service | Azure" 
	description="Découvrez comment créer et configurer des connecteurs EDI, EDIFACT, AS2 et TPM ; architecture microservices" 
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
	ms.date="08/04/2015" 
	ms.author="mandia"/>

# Connecteurs et applications API B2B dans Microsoft Azure App Service
Microsoft Azure App Service (ou App Service) comprend plusieurs applications API BizTalk qui sont essentielles pour les environnements d'intégration. Ces applications API sont basées sur des concepts et des outils utilisés dans BizTalk Server, mais elles sont désormais disponibles dans le cadre d'Azure App Service.

Parmi ces applications API figure la catégorie des applications API entreprise-entreprise (B2B). À l'aide des applications API B2B, vous pouvez facilement ajouter des partenaires, créer des accords et faire tout ce que vous feriez localement avec EDI, AS2 et EDIFACT.

Ces applications API B2B offrent des fonctionnalités « Déclencheur » et « Action ». Un déclencheur démarre une nouvelle instance en fonction d'un événement spécifique, comme l'arrivée d'un message X12 provenant d'un partenaire. Une action correspond au résultat, par exemple, après la réception d'un message X12, le message est envoyé avec AS2.


## Connecteur ou applications API entreprise-entreprise
La fonctionnalité entreprise-entreprise (B2B) inclut des applications API existantes et prédéfinies qui permettent à différentes sociétés, divisions, applications, etc. de communiquer en utilisant AS2, EDI et EDIFACT.

Les applications API B2B incluent :

Connecteur ou applications API | Description
--- | ---
Gestion des partenaires commerciaux BizTalk | Application API qui crée des relations entreprise-entreprise (B2B) à l'aide de partenaires et d'accords. Ces relations utilisent le protocole AS2, EDIFACT et X12.<br/><br/>L’application API TPM (Gestion des partenaires commerciaux) est la configuration de base requise du connecteur AS2 et des applications API X12 or EDIFACT. 
Connecteur AS2 | Connecteur qui reçoit et envoie des messages via le transport AS2. Le connecteur transporte les données de manière fiable et sécurisée sur Internet.
BizTalk EDIFACT | Application API qui reçoit et envoie des messages via EDIFACT. EDIFACT est également communément appelé UN/EDIFACT (United Nations/Electronic Data Interchange For Administration, Commerce and Transport) et son utilisation est très répandue dans le monde de l'industrie.
BizTalk X12 | Application API qui reçoit et envoie des messages via le protocole X12. Le protocole X12 est également communément appelé ASC X12 (Accredited Standards Committee X12) et son utilisation est très répandue dans le monde de l'industrie. 


À l'aide de ces applications API, vous pouvez effectuer différentes tâches de messagerie EDI. Par exemple, à l'aide du connecteur AS2, vous pouvez recevoir et envoyer différents types de messages (EDI, XML, fichier plat, etc.) de manière sécurisée à un client, une division de votre société comme les ressources humaines ou toute personne qui utilise AS2.

Vous pouvez créer autant d'applications API que vous le souhaitez, en toute simplicité. Vous pouvez également réutiliser une application API particulière dans plusieurs scénarios ou workflows.

Et ce, sans avoir à écrire de code. Allons-y.


## Conditions préalables requises
Quand vous créez des applications API B2B, certaines ressources sont obligatoires. Vous devez créer vous-même ces éléments pour pouvoir les utiliser dans d'autres applications API. Les conditions sont les suivantes :

Prérequis | Description
--- | ---
Base de données SQL Azure | Stocke les éléments B2B, notamment les partenaires, les schémas, les certificats et les accords. Chacune des applications API B2B requiert sa propre base de données SQL Azure. <br/><br/>**Remarque** Copiez la chaîne de connexion dans cette base de données.<br/><br/>[Créer une base de données SQL Azure](../sql-database-get-started.md)
Conteneur de stockage d'objets blob Azure | Stocke les propriétés de message quand l'archivage AS2 est activé. Si vous n’avez pas besoin de l’archivage des messages AS2, le conteneur de stockage n’est pas utile. <br/><br/>**Remarque** Si vous activez l’archivage, copiez la chaîne de connexion dans ce stockage d’objets blob.<br/><br/>[À propos des comptes de stockage Azure](../storage-create-storage-account.md)
Espace de noms Service Bus et ses valeurs de clé | Stocke les données de traitement par lot X12 et EDIFACT. Si vous n’avez pas besoin de traitement par lot, un espace de noms Service Bus n’est pas utile.<br/><br/>**Remarque** Si vous activez le traitement par lot, copiez ces valeurs.<br/><br/>[Créer un espace de noms Service Bus](http://msdn.microsoft.com/library/azure/hh690931.aspx)
Instance TPM | Une instance BizTalk TPM (Trading Partner Management, Gestion des partenaires commerciaux) est requise pour créer un connecteur AS2 et une application API X12 ou EDIFACT. Quand vous créez l’application API TPM, vous créez l’instance TPM. <br/><br/>**Remarque** Mémorisez le nom de votre application API TPM. 


## Créer les applications API
Les applications API B2B peuvent être créées à l'aide du portail Azure ou des API REST.


### Créer les applications API à l'aide des API REST
[Consultez la documentation relative à l’utilisation des API REST.](http://go.microsoft.com/fwlink/p/?LinkId=529766)


### Créer les applications API B2B dans le portail Azure
Dans le portail Azure, vous pouvez créer des applications API B2B lors de la création d'applications logiques, web ou mobiles. Vous pouvez aussi en créer une à l'aide de son propre panneau. Les deux méthodes étant faciles, vous choisirez en fonction de vos besoins et préférences. Certains utilisateurs préfèrent créer d'abord toutes les applications API B2B avec leurs propriétés spécifiques. Ensuite, ils créent les applications logiques/web/mobiles, puis ajoutent les applications API B2B créées.

Les étapes suivantes créent les applications API B2B à l'aide du panneau API Apps.


#### Créer des applications API TPM (gestion des partenaires commerciaux) BizTalk

> [AZURE.NOTE]Une instance BizTalk TPM (Trading Partner Management, Gestion des partenaires commerciaux) est requise pour créer un connecteur AS2 et une application API X12 ou EDIFACT. Quand vous créez l'application API TPM, vous créez l'instance TPM.

Les étapes suivantes créent l'instance TPM :

1. Dans le Tableau d’accueil du portail Azure (la page d’accueil), sélectionnez **Marketplace**. **Applications API** répertorie tous les connecteurs et applications API existants. Vous pouvez également **rechercher** les applications API B2B spécifiques.
2. Sélectionnez **Gestion des partenaires commerciaux BizTalk**. Dans le nouveau panneau, sélectionnez **Créer**. 
3. Entrez les propriétés : 

	Propriété | Description
--- | ---
Nom | Entrez un nom pour l’instance TPM. Par exemple, vous pouvez la nommer *AccountsPayableTPM*.
Paramètres du package | Entrez la **chaîne de connexion de base de données** ADO.NET à la base de données SQL Azure que vous avez créée. <br/><br/>Quand vous copiez la chaîne de connexion, le mot de passe n’y est pas ajouté. Veillez à entrer le mot de passe dans la chaîne de connexion.
Plan App Service | Indique votre plan de paiement. Vous pouvez le modifier si vous avez besoin de plus ou moins de ressources.
Niveau de tarification | Propriété en lecture seule qui indique la catégorie de tarification dans votre abonnement Azure. 
Groupe de ressources | Créez-en un nouveau ou utilisez un groupe existant. Toutes les applications API et tous les connecteurs pour vos applications logiques, web et mobiles doivent être dans le même groupe de ressources. Cette propriété est décrite dans la section <br/><br/>[Utiliser des groupes de ressources](../resource-group-overview.md). 
Abonnement | Propriété en lecture seule qui indique votre abonnement actuel.
Emplacement | Emplacement géographique qui héberge votre service Azure. 
Ajouter au Tableau d'accueil | Sélectionnez cette option pour ajouter l'application API B2B à votre tableau d'accueil (la page d'accueil).

4. Sélectionnez **Créer**.

Une fois l'application API TPM (instance TPM) créée, vous pouvez créer le connecteur AS2 et/ou les applications API X12 ou EDIFACT.


#### Créer le connecteur AS2

1. Dans le Tableau d’accueil du portail Azure (la page d’accueil), sélectionnez **Marketplace**. **Applications API** répertorie tous les connecteurs et applications API existants. Vous pouvez également **rechercher** les applications API B2B spécifiques.
2. Sélectionnez **Connecteur AS2**. Dans le nouveau panneau, sélectionnez **Créer**. 
3. Entrez les propriétés : 

	Propriété | Description
--- | ---
Nom | Entrez un nom pour le connecteur AS2. Par exemple, vous pouvez le nommer *AS2Connector*.
Paramètres du package | Entrez les paramètres propres à cette application API, comme le nom de l'instance TPM. <br/><br/>Consultez [Ajouter des paramètres de package AS2](#AddAS2Conn) dans cette rubrique pour connaître les propriétés spécifiques. 
Plan App Service | Indique votre plan de paiement. Vous pouvez le modifier si vous avez besoin de plus ou moins de ressources.
Niveau de tarification | Propriété en lecture seule qui indique la catégorie de tarification dans votre abonnement Azure. 
Groupe de ressources | Créez-en un nouveau ou utilisez un groupe existant. Cette propriété est décrite dans la section [Utiliser des groupes de ressources](../resource-group-overview.md). 
Abonnement | Propriété en lecture seule qui indique votre abonnement actuel.
Emplacement | Emplacement géographique qui héberge votre service Azure. 
Ajouter au Tableau d'accueil | Sélectionnez cette option pour ajouter l'application API B2B à votre tableau d'accueil (la page d'accueil).

	**<a name="AddAS2Conn"></a>Paramètres de package du connecteur AS2**

	Propriété | Description
--- | --- 
Chaîne de connexion de base de données | Entrez la chaîne de connexion ADO.NET dans la base de données SQL Azure que vous avez créée. Quand vous copiez la chaîne de connexion, le mot de passe n'y est pas ajouté. Veillez à entrer le mot de passe dans la chaîne de connexion avant de coller.
Activer l'archivage des messages entrants | facultatif. Activez cette propriété pour stocker les propriétés d'un message AS2 entrant reçu d'un partenaire. 
Chaîne de connexion de stockage d'objets blob Azure | Entrez la chaîne de connexion dans le conteneur de stockage d'objets blob Azure que vous avez créé. Quand l'archivage est activé, les messages encodés et décodés sont stockés dans ce conteneur de stockage.
Nom de l'instance TPM | Entrez le nom de l’application API **Gestion des partenaires commerciaux BizTalk** que vous avez créée précédemment. Quand vous créez le connecteur AS2, il exécute uniquement les accords AS2 inclus dans cette instance TPM spécifique.

4. Sélectionnez **Créer**.


#### Créer les applications API X12 ou EDIFACT

1. Dans le Tableau d’accueil du portail Azure (la page d’accueil), sélectionnez **Marketplace**. **Applications API** répertorie tous les connecteurs et applications API existants. Vous pouvez également **rechercher** les applications API B2B spécifiques.
2. Sélectionnez **BizTalk X12** ou **BizTalk EDIFACT**. Dans le nouveau panneau, sélectionnez **Créer**. 
3. Entrez les propriétés : 

	Propriété | Description
--- | ---
Nom | Entrez un nom pour l'application API B2B. Par exemple, vous pouvez la nommer *EDI850APIApp*.
Paramètres du package | Entrez les paramètres propres à cette application API, comme le nom de l'instance TPM. <br/><br/>Consultez [Paramètres de package X12 ou EDIFACT](#AddX12) dans cette rubrique pour connaître les propriétés spécifiques. 
Plan App Service | Indique votre plan de paiement. Vous pouvez le modifier si vous avez besoin de plus ou moins de ressources.
Niveau de tarification | Propriété en lecture seule qui indique la catégorie de tarification dans votre abonnement Azure. 
Groupe de ressources | Créez-en un nouveau ou utilisez un groupe existant. Cette propriété est décrite dans la section [Utiliser des groupes de ressources](../resource-group-overview.md). 
Abonnement | Propriété en lecture seule qui indique votre abonnement actuel.
Emplacement | Emplacement géographique qui héberge votre service Azure. 
Ajouter au Tableau d'accueil | Sélectionnez cette option pour ajouter l'application API B2B à votre tableau d'accueil (la page d'accueil).

	**<a name="AddX12"></a>Paramètres de package des applications API X12 et EDIFACT**

	Propriété | Description
--- | --- 
Chaîne de connexion de base de données | Entrez la chaîne de connexion ADO.NET dans la base de données SQL Azure que vous avez créée. Quand vous copiez la chaîne de connexion, le mot de passe n'y est pas ajouté. Veillez à entrer le mot de passe dans la chaîne de connexion avant de coller.
Espace de noms Service Bus | Entrez l'espace de noms Service Bus que vous avez créé. Requis uniquement quand le traitement par lot est activé. 
Nom de la clé d'accès partagé de l'espace de noms Service Bus | Entrez la clé d'accès de l'espace de noms Service Bus que vous avez créée. Requis uniquement quand le traitement par lot est activé. 
Valeur de la clé d'accès partagée de l'espace de noms Service Bus | Entrez la valeur de la clé d'accès de l'espace de noms Service Bus que vous avez créée. Requis uniquement quand le traitement par lot est activé. 
Nom de l'instance TPM | Entrez le nom de l’application API **Gestion des partenaires commerciaux BizTalk** que vous avez créée précédemment. Quand vous créez les applications API X12 ou EDIFACT, cette application API exécute uniquement les accords X12/EDIFACT inclus dans cette instance TPM spécifique.

4. Sélectionnez **Créer**.


## Ajouter vos partenaires, accords, certificats et schémas 
Dans le portail de gestion Azure, ouvrez votre application API TPM. Dans la section **Composants**, ajoutez vos partenaires, accords, certificats et schémas.

Vous pouvez également ajouter des accords à vos connecteurs AS2, applications API X12 et applications API EDIFACT.


## Surveiller vos applications API
Dans le portail de gestion Azure, ouvrez votre application API TPM. Dans la section **Opérations** sont affichées différentes opérations de gestion. Vous pouvez par exemple afficher :

- Les événements d'information et d'erreur
- Le nombre de threads et l'utilisation de la mémoire du processus de travail (w3wp)
- Les journaux des serveurs web et d'applications

Pour en savoir plus, consultez [Surveiller vos applications logiques](app-service-logic-monitor-your-logic-apps.md).


## Ajouter les applications API à votre application 
Microsoft Azure App Service expose différents types d'application qui peuvent utiliser ces applications API B2B. Vous pouvez créer une application API B2B ou ajouter vos applications API existantes à des applications logiques, mobiles ou web.

Dans votre application, sélectionnez tout simplement les applications API B2B à partir de la galerie pour les ajouter automatiquement à votre application.

> [AZURE.IMPORTANT]Pour ajouter des connecteurs et des applications API que vous avez créés précédemment, créez les applications logiques, mobiles ou web dans le même groupe de ressources.

Les étapes suivantes ajoutent les applications API B2B aux applications logiques, mobiles ou web :

1. Dans le Tableau d’accueil du portail Azure (page d’accueil), accédez au **Marketplace** et recherchez vos applications logiques, mobiles ou web. 

	Si vous créez une application, recherchez Applications logiques, Applications mobiles ou Applications web. Sélectionnez l’application puis, dans le nouveau panneau, sélectionnez **Créer**. [Créer une application logique](app-service-logic-create-a-logic-app.md) répertorie les étapes.

2. Ouvrez votre application et sélectionnez **Déclencheurs et actions**.

3. Dans la **Galerie**, sélectionnez l’application API B2B, ce qui l’ajoute automatiquement à votre application. Vous pouvez également créer une application API B2B.

	> [AZURE.IMPORTANT]Le connecteur AS2 et les applications API X12 ou EDIFACT requièrent une instance TPM. Par conséquent, si vous créez des applications API B2B, créez d'abord l'application API TPM, puis créez le connecteur AS2 et l'application API X12 ou EDIFACT.

4. Sélectionnez **OK** pour enregistrer vos modifications.


## Autres ressources B2B

[Création d’un processus B2B](app-service-logic-create-a-b2b-process.md)<br/> [Création d’un accord de partenariat commercial](app-service-logic-create-a-trading-partner-agreement.md)<br/> [Qu’est-ce qu’un connecteur et une application API BizTalk](app-service-logic-what-are-biztalk-api-apps.md)


## En savoir plus sur les applications logiques et les applications web
[Qu’est-ce qu’une application logique ?](app-service-logic-what-are-logic-apps.md)<br/> [Sites Web et Web Apps dans Azure App Service](../app-service-web/app-service-web-overview.md)


## Autres connecteurs

[Liste des connecteurs et applications API](app-service-logic-connectors-list.md)<br/><br/> [Qu’est-ce qu’un connecteur et une application API BizTalk ?](app-service-logic-what-are-biztalk-api-apps.md)

<!---HONumber=August15_HO6-->
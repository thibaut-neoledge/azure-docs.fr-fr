<properties 
   pageTitle="Utilisation du connecteur AS2 dans Microsoft Azure App Service"
	description="Utilisation du connecteur AS2"
	services="app-service\logic"
	documentationCenter=".net,nodejs,java"
	authors="rajeshramabathiran"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="app-service-logic"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="integration"
	ms.date="08/19/2015"
	ms.author="rajram"/>

# Connecteur Microsoft AS2
Le connecteur Microsoft Azure AS2 vous permet de recevoir et d’envoyer des messages par le biais du protocole de transport AS2 (Applicability Statement 2) dans les communications interentreprises. Les données sont transportées de manière fiable et sécurisée sur Internet. Le chiffrement et les certificats numériques garantissent la sécurité.

## Déclencheurs et actions
Un déclencheur démarre une nouvelle instance en fonction d’un événement spécifique, comme l’arrivée d’un message AS2 provenant d’un partenaire. Une action correspond au résultat : par exemple, après la réception d’un message AS2, le message est envoyé avec AS2.

Le connecteur AS2 peut être utilisé comme un déclencheur ou une action dans une application logique et prend en charge les données aux formats JSON et XML. Le connecteur AS2 comporte les déclencheurs et les actions suivants :

Déclencheurs | Actions
--- | ---
Recevoir et décoder | Encoder et envoyer

## Conditions préalables requises
Vous devez créer les éléments ci-après pour qu’ils puissent être utilisés par le connecteur AS2 :

Prérequis | Description
--- | ---
Application API TPM | Avant de créer un connecteur AS2, vous devez créer un [connecteur de gestion des partenaires commerciaux BizTalk][1]. <br/><br/>**Remarque** Vous devez connaître le nom de votre application API TPM. 
Base de données SQL Azure | Stocke les éléments B2B, notamment les partenaires, les schémas, les certificats et les accords. Chacune des applications API B2B requiert sa propre base de données SQL Azure. <br/><br/>**Remarque** Copiez la chaîne de connexion dans cette base de données.<br/><br/>[Créer une base de données SQL Azure](../sql-database-get-started.md)
Conteneur de stockage d'objets blob Azure | Stocke les propriétés de message quand l'archivage AS2 est activé. Si vous n’avez pas besoin de l’archivage des messages AS2, le conteneur de stockage n’est pas utile. <br/><br/>**Remarque** Si vous activez l’archivage, copiez la chaîne de connexion dans ce stockage d’objets blob.<br/><br/>[À propos des comptes de stockage Azure](../storage-create-storage-account.md).

## Créer le connecteur AS2

Un connecteur peut être créé dans une application logique ou directement à partir d'Azure Marketplace. Pour créer un connecteur à partir de Marketplace :

1. Dans le tableau d'accueil Azure, sélectionnez **Marketplace**.
2. Recherchez « Connecteur AS2 », sélectionnez-le et sélectionnez **Créer**.
3. Entrez le nom, le plan App Service et d'autres propriétés.
4. Entrez les paramètres de package suivants :

	Propriété | Description
--- | --- 
Chaîne de connexion de base de données | Entrez la chaîne de connexion ADO.NET dans la base de données SQL Azure que vous avez créée. Quand vous copiez la chaîne de connexion, le mot de passe n'y est pas ajouté. Veillez à entrer le mot de passe dans la chaîne de connexion avant de coller.
Activer l'archivage des messages entrants | facultatif. Activez cette propriété pour stocker les propriétés d'un message AS2 entrant reçu d'un partenaire. 
Chaîne de connexion de stockage d'objets blob Azure | Entrez la chaîne de connexion dans le conteneur de stockage d'objets blob Azure que vous avez créé. Quand l'archivage est activé, les messages encodés et décodés sont stockés dans ce conteneur de stockage.
Nom de l'instance TPM | Entrez le nom de l’application API **Gestion des partenaires commerciaux BizTalk** que vous avez créée précédemment. Quand vous créez le connecteur AS2, il exécute uniquement les accords AS2 inclus dans cette instance TPM spécifique.

5. Sélectionnez **Créer**.

Les partenaires commerciaux sont des entités impliquées dans des communications B2B (entreprise à entreprise). Lorsque deux partenaires établissent une relation, il est question d'un « accord ». L’accord défini est basé sur le type de communication dont les deux partenaires souhaitent bénéficier. Il est propre au protocole ou au transport.

Les étapes de création d’un accord de partenariat commercial sont documentées [ici][2].

## Utiliser le connecteur comme un déclencheur

1. Lors de la création ou de la modification d'une application logique, sélectionnez le connecteur AS2 que vous avez créé dans le volet droit : ![Paramètres du déclencheur][3]

2. Cliquez sur la flèche droite → : ![Options du déclencheur][4]

3. Le connecteur AS2 expose un seul déclencheur. Sélectionnez *Recevoir et décoder* : ![Entrée Recevoir et décoder][5]

4. Ce déclencheur ne possède aucune entrée. Cliquez sur la flèche droite → : ![Recevoir et décoder configuré][6]

Dans le cadre de la sortie, le connecteur renvoie la charge utile AS2, ainsi que les métadonnées propres à AS2.

Le déclencheur est déclenché lorsqu'une charge utile AS2 prend la forme d'une méthode POST vers https://{Host URL}/decode. Vous pouvez rechercher l’URL de l’hôte dans les paramètres de l’application API. Vous devrez peut-être également redéfinir le niveau d’accès de l’application API dans les paramètres de l’application sur Public (authentifié ou anonyme).

## Utiliser le connecteur comme une action
1. Derrière votre déclencheur (ou si vous choisissez d'exécuter cette logique manuellement), ajoutez le connecteur AS2 que vous avez créé à partir du volet droit : ![Paramètres d'action][7]

2. Cliquez sur la flèche droite → : ![Liste d'actions][8]

3. Le connecteur AS2 ne prend en charge qu’une seule action. Sélectionnez *Encoder et envoyer* : ![Entrée Encoder et envoyer][9]

4. Indiquez les entrées de l'action et configurez celle-ci : ![Encoder et envoyer configuré][10]

	Les paramètres sont les suivants :

	Paramètre | Type | Description
--- | --- | ---
Payload | objet| Contenu de la charge utile à encoder et à publier dans le point de terminaison configuré. La charge utile doit être fournie sous la forme d’un objet JSON.
AS2 à partir de | string | Identité AS2 de l’expéditeur du message AS2. Ce paramètre est utilisé pour rechercher l’accord approprié permettant d’envoyer le message.
AS2 vers | string | Identité AS2 du récepteur du message AS2. Ce paramètre est utilisé pour rechercher l’accord approprié permettant d’envoyer le message.
URL du partenaire | string | Point de terminaison du partenaire auquel le message doit être envoyé.
Activer l'archivage | booléenne | Détermine si le message sortant doit être archivé.

L'action retourne un code de réponse HTTP 200 en cas de réussite.

## En faire plus avec votre connecteur
Pour plus d'informations sur les applications logiques, consultez [Qu'est-ce qu'une application logique ?](app-service-logic-what-are-logic-apps.md).

Affichez la référence d’API REST Swagger sur [Référence de connecteurs et d’applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Pour plus d'informations, consultez [Gestion et contrôle de vos connecteurs et applications API intégrés](app-service-logic-monitor-your-connectors.md).

<!--References -->
[1]: app-service-logic-connector-tpm.md
[2]: app-service-logic-create-a-trading-partner-agreement.md
[3]: ./media/app-service-logic-connector-as2/TriggerSettings.PNG
[4]: ./media/app-service-logic-connector-as2/TriggerOptions.PNG
[5]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeInput.PNG
[6]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeConfigured.PNG
[7]: ./media/app-service-logic-connector-as2/ActionSettings.PNG
[8]: ./media/app-service-logic-connector-as2/ListOfActions.PNG
[9]: ./media/app-service-logic-connector-as2/EncodeAndSendInput.PNG
[10]: ./media/app-service-logic-connector-as2/EncodeAndSendConfigured.PNG

<!---HONumber=August15_HO8-->
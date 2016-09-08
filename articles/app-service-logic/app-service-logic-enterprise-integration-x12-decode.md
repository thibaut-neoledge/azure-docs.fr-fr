<properties 
	pageTitle="En savoir plus sur le connecteur Enterprise Integration Pack Decode X12 Message | Microsoft Azure App Service | Microsoft Azure" 
	description="Découvrez comment utiliser les partenaires avec Enterprise Integration Pack et vos applications logiques" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="padmavc" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/15/2016" 
	ms.author="padmavc"/>

# Prise en main de Decode X12 Message

Valide l’EDI et les propriétés spécifiques au partenaire, génère un document XML pour chaque document informatisé ainsi qu’un accusé de réception pour la transaction traitée.

## Créer la connexion

### Conditions préalables

* Un compte Azure (que vous pouvez [créer gratuitement)](https://azure.microsoft.com/free)

* Un compte d’intégration est nécessaire pour utiliser le connecteur Decode X12 Message. Plus d’informations sur la création d’un [compte d’intégration](./app-service-logic-enterprise-integration-create-integration-account.md), de [partenaires](./app-service-logic-enterprise-integration-partners.md) et d’un [contrat X12](./app-service-logic-enterprise-integration-x12.md)

### Connectez-vous à Decode X12 Message en procédant comme suit :

1. La page [Créer une application logique](./app-service-logic-create-a-logic-app.md) vous fournit un exemple.

2. Ce connecteur ne possède aucun déclencheur. Utilisez d’autres déclencheurs pour démarrer l’application logique, tel qu’un déclencheur de demande. Dans le concepteur d’applications logiques, ajoutez un déclencheur et une action. Sélectionnez Afficher les API gérées par Microsoft dans la liste déroulante, puis saisissez « x12 » dans la zone de recherche. Sélectionnez X12 – Decode X12 Message.

	![recherche x12](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage1.png)

3. Si vous n’avez pas encore créé de connexion aux comptes d’intégration, vous êtes invité à saisir les informations de connexion.

	![connexion de compte d’intégration](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage4.png)

4. Entrez les détails du compte d’intégration. Les propriétés marquées d’un astérisque sont obligatoires.

	| Propriété | Détails |
	| -------- | ------- |
	| Nom de connexion * | Entrez un nom pour votre connexion. |
	| Compte d’intégration * | Entrez le nom du compte d’intégration. Vérifiez que votre compte d’intégration et votre application logique se trouvent dans le même emplacement Azure. |

	Une fois complets, les détails de votre connexion se présentent comme suit.
	
	![connexion de compte d’intégration créée](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage5.png)

5. Sélectionnez **Créer**.
	
6. Vous pouvez voir que la connexion a été créée.

	![détails de connexion de compte d’intégration](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage6.png)

7. Sélectionnez le message de fichier plat X12 à décoder.

	![remplir les champs obligatoires](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage7.png)

## X12 Decode effectue les actions suivantes

* Valide l’enveloppe par rapport au contrat de partenariat commercial
* Génère un document XML pour chaque document informatisé
* Valide l’EDI et les propriétés spécifiques au partenaire
	* Validation de la structure EDI et validation du schéma étendue
	* Validation de la structure de l’enveloppe d’échange.
	* Validation de schéma de l’enveloppe par rapport au schéma de contrôle.
	* Validation de schéma des éléments de données du document informatisé par rapport au schéma de message.
	* Validation EDI effectuée sur les éléments de données du document informatisé.
* Vérifie que les numéros de contrôle de l’échange, du groupe et du document informatisé ne sont pas des doublons.
	* Vérifie le numéro de contrôle de l’échange par rapport aux échanges reçus précédemment.
	* Vérifie le numéro de contrôle du groupe par rapport aux autres numéros de contrôle de groupe dans l’échange.
	* Vérifie le numéro de contrôle du document informatisé par rapport aux autres numéros de contrôle de document informatisé dans ce groupe.
* Convertit la totalité de l’échange au format XML.
	* Fractionne l’échange en documents informatisés - suspend les documents informatisés en cas d’erreur : analyse chaque document informatisé d’un échange dans un document XML distinct. Si la validation d’un ou plusieurs documents informatisés de l’échange échoue, X12 Decode suspend uniquement ces documents informatisés.
	* Fractionne l’échange en documents informatisés - suspend l’échange en cas d’erreur : analyse chaque document informatisé d’un échange dans un document XML distinct. Si la validation d’un ou plusieurs documents informatisés de l’échange échoue, X12 Decode suspend l’intégralité de l’échange.
	* Préserve l’échange - suspend les documents informatisés en cas d’erreur : crée un document XML pour l’intégralité de l’échange par lot. X12 Decode suspend uniquement les documents informatisés dont la validation échoue, tout en continuant à traiter tous les autres documents informatisés.
	* Préserve l’échange - suspend l’échange en cas d’erreur : crée un document XML pour l’intégralité de l’échange par lot. Si la validation d’un ou plusieurs documents informatisés de l’échange échoue, X12 Decode suspend l’intégralité de l’échange.
* Génère un accusé de réception fonctionnel et/ou technique (si configuré).
	* Suite à la validation de l’en-tête, un accusé de réception technique est généré. L’accusé de réception technique renvoie l’état du traitement de l’en-tête et du code de fin d’un échange par le récepteur de l’adresse.
	* Suite à la validation du corps, un accusé de réception fonctionnel est généré. L’accusé de réception fonctionnel signale chaque erreur rencontrée lors du traitement du document reçu.

## Étapes suivantes

[En savoir plus sur Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "En savoir plus sur Enterprise Integration Pack")

<!---HONumber=AcomDC_0824_2016-->
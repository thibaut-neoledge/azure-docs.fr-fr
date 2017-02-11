---
title: "Connecteur webhook d’application logique | Microsoft Docs"
description: "Vue d’ensemble de l’action et des déclencheurs webhook permettant d’effectuer des actions comme Filtrer le tableau."
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 71775384-6c3a-482c-a484-6624cbe4fcc7
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: c0edbe421050ad46f6d31fd6416df4b344b233ad
ms.openlocfilehash: 2c24699f6253fc7952b331ebcce4b937f1c83603


---
# <a name="get-started-with-the-webhook-connector"></a>Prise en main du connecteur webhook
Avec le déclencheur et l’action webhook, vous pouvez déclencher, suspendre et reprendre les flux afin d’effectuer les tâches suivantes :

* Créer un déclenchement à partir [d’Azure Event Hub dès qu’un élément](https://github.com/logicappsio/EventHubAPI) est reçu.
* Attendre une approbation avant de poursuivre un flux de travail.

Pour plus d’informations sur la création d’une API qui prend en charge l’abonnement à un webhook, consultez [cet article sur la création de connecteurs d’application logique](../logic-apps/logic-apps-create-api-app.md).

- - -
## <a name="use-the-webhook-trigger"></a>Utilisation du déclencheur webhook
Un déclencheur est un événement qui peut être utilisé pour lancer le workflow défini dans une application logique. [En savoir plus sur les déclencheurs](connectors-overview.md).  Un déclencheur webhook est particulièrement utile car il ne repose pas sur l’interrogation des nouveaux éléments, contrairement au [déclencheur de requête](connectors-native-reqres.md) ; l’application logique se déclenche dès qu’un événement se produit.  Pour ce faire, elle enregistre une *URL de rappel* sur un service qui peut être utilisée pour déclencher l’application logique lorsque nécessaire.

Voici un exemple de séquence de configuration d’un déclencheur HTTP dans le concepteur d’application logique.  Cet exemple suppose que vous avez déjà déployé ou que vous accédez à une API qui suit [le modèle d’abonnement et de résiliation d’abonnement au webhook utilisé dans les applications logiques](../logic-apps/logic-apps-create-api-app.md#webhook-triggers).  L’appel d’abonnement est effectué à chaque fois qu’une application logique est enregistrée avec un nouveau webhook, ou basculée de l’état désactivé à l’état activé.  L’appel de résiliation d’abonnement est effectué à chaque fois qu’un déclencheur webhook d’application logique est supprimé et enregistré, ou basculé de l’état activé à l’état désactivé.

1. Ajoutez le déclencheur **HTTP Webhook** en tant que première étape dans une application logique
2. Renseignez les paramètres pour les appels d’abonnement et de résiliation d’abonnement au webhook

   * Le modèle est identique au format de [l’action HTTP](connectors-native-http.md)

     ![Déclencheur HTTP](./media/connectors-native-webhook/using-trigger.png)
3. Ajoutez au moins une action
4. Cliquez sur Enregistrer pour publier l’application logique. Cela permet d’appeler le point de terminaison d’abonnement avec l’URL de rappel nécessaire pour déclencher cette application logique
5. À chaque fois que le service effectue un `HTTP POST` à l’URL de rappel, l’application logique se déclenche (et inclut toutes les données transmises dans la requête)

## <a name="use-the-webhook-action"></a>Utilisation de l’action webhook
Une action est une opération effectuée par le flux de travail défini dans une application logique. [En savoir plus sur les actions.](connectors-overview.md)  Une action webhook est particulièrement utile car elle enregistre une *URL de rappel* avec un service et attend jusqu'à ce que l’URL soit appelée avant de se déclencher à nouveau.  [« Envoyer un message électronique d’approbation »](connectors-create-api-office365-outlook.md) est un exemple de connecteur qui suit ce modèle.  Vous pouvez étendre ce modèle à n’importe quel service à l’aide de l’action webhook.  Cet exemple suppose que vous avez déjà déployé ou que vous accédez à une API qui suit [le modèle d’abonnement et de résiliation d’abonnement au webhook utilisé dans les applications logiques](../logic-apps/logic-apps-create-api-app.md#webhook-actions).  L’appel d’abonnement est effectué à chaque fois qu’une application logique exécute l’action webhook.  L’appel de résiliation d’abonnement est effectué à chaque fois qu’une exécution est annulée en attendant une réponse, ou avant que l’exécution de l’application logique n’expire.

Pour ajouter une action webhook :

1. Sélectionnez le bouton **Nouvelle étape**
2. Choisissez **Ajouter une action**
3. Dans la zone de recherche d’action , tapez « webhook » pour répertorier l’action **HTTP Webhook**

    ![Sélectionner l’action de requête](./media/connectors-native-webhook/using-action-1.png)
4. Renseignez les paramètres pour les appels d’abonnement et de résiliation d’abonnement au webhook

   * Le modèle est identique au format de [l’action HTTP](connectors-native-http.md)

     ![Terminer l’action de requête](./media/connectors-native-webhook/using-action-2.png)
   * Lors de l’exécution, l’application logique appelle le point de terminaison d’abonnement lorsque l’étape est atteinte
5. Cliquez sur Enregistrer dans le coin supérieur gauche de la barre d’outils, et votre application logique est à la fois enregistrée et publiée (activation)

- - -
## <a name="technical-details"></a>Détails techniques
Vous trouverez ci-dessous les détails des déclencheurs et des actions pris en charge par webhook.

## <a name="webhook-triggers"></a>Déclencheurs Webhook
Un déclencheur est une opération de démarrage d’un flux de travail. [En savoir plus sur les déclencheurs.](connectors-overview.md) Ce connecteur a 1 déclencheur.

| Action | Description |
| --- | --- |
| HTTP Webhook |Permet d’abonner une URL de rappel à un service qui peut appeler l’URL pour déclencher l’application logique lorsque nécessaire. |

### <a name="trigger-details"></a>Détails du déclencheur
Le connecteur webhook est créé avec 1 déclencheur possible. Vous trouverez ci-dessous plus d’informations sur l’action, ses champs obligatoires et facultatifs, et les détails des résultats correspondants associés à son utilisation.

#### <a name="http-webhook"></a>HTTP Webhook
Permet d’abonner une URL de rappel à un service qui peut appeler l’URL pour déclencher l’application logique lorsque nécessaire.
Une * signifie que le champ est obligatoire.

| Display Name | Nom de la propriété | Description |
| --- | --- | --- |
| Méthode d’abonnement* |statique |Méthode HTTP à utiliser pour la demande d’abonnement |
| URI d’abonnement* |URI |URI HTTP à utiliser pour la demande d’abonnement |
| Méthode de résiliation d’abonnement* |statique |Méthode HTTP à utiliser pour la demande de résiliation d’abonnement |
| URI de résiliation d’abonnement* |URI |URI HTTP à utiliser pour la demande de résiliation d’abonnement |
| Corps d’abonnement |body |Corps de la demande HTTP pour s’abonner |
| En-têtes de l’abonnement |headers |En-têtes de la demande HTTP pour s’abonner |
| Authentification de l’abonnement |authentification |Authentification HTTP à utiliser pour s’abonner. Voir [Connecteur HTTP](connectors-native-http.md#authentication) pour plus d’informations |
| Corps de résiliation d’abonnement |body |Corps de la demande HTTP de résiliation d’abonnement |
| En-têtes de résiliation d’abonnement |headers |En-têtes de la demande HTTP de résiliation d’abonnement |
| Authentification de la résiliation d’abonnement |authentication |Authentification HTTP à utiliser pour la résiliation d’abonnement. Voir [Connecteur HTTP](connectors-native-http.md#authentication) pour plus d’informations |

<br>

**Détails des résultats**

Requête Webhook

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| headers |objet |En-têtes de requête Webhook |
| body |objet |Objet de la requête Webhook |
| Code d’état |int |Code d’état de la requête Webhook |

## <a name="webhook-actions"></a>Actions de webhook
Une action est une opération effectuée par le flux de travail défini dans une application logique. [En savoir plus sur les actions.](connectors-overview.md) Le connecteur a 1 action possible.

| Action | Description |
| --- | --- |
| HTTP Webhook |Permet d’abonner une URL de rappel à un service qui peut appeler l’URL redémarrer une étape du flux de travail lorsque nécessaire. |

### <a name="action-details"></a>Détails de l’action
Le connecteur webhook est créé avec 1 action possible. Vous trouverez ci-dessous plus d’informations sur l’action, ses champs obligatoires et facultatifs, et les détails des résultats correspondants associés à son utilisation.

#### <a name="http-webhook"></a>HTTP Webhook
Permet d’abonner une URL de rappel à un service qui peut appeler l’URL redémarrer une étape du flux de travail lorsque nécessaire.
Une * signifie que le champ est obligatoire.

| Display Name | Nom de la propriété | Description |
| --- | --- | --- |
| Méthode d’abonnement* |statique |Méthode HTTP à utiliser pour la demande d’abonnement |
| URI d’abonnement* |URI |URI HTTP à utiliser pour la demande d’abonnement |
| Méthode de résiliation d’abonnement* |statique |Méthode HTTP à utiliser pour la demande de résiliation d’abonnement |
| URI de résiliation d’abonnement* |URI |URI HTTP à utiliser pour la demande de résiliation d’abonnement |
| Corps d’abonnement |body |Corps de la demande HTTP pour s’abonner |
| En-têtes de l’abonnement |headers |En-têtes de la demande HTTP pour s’abonner |
| Authentification de l’abonnement |authentification |Authentification HTTP à utiliser pour s’abonner. Voir [Connecteur HTTP](connectors-native-http.md#authentication) pour plus d’informations |
| Corps de résiliation d’abonnement |body |Corps de la demande HTTP de résiliation d’abonnement |
| En-têtes de résiliation d’abonnement |headers |En-têtes de la demande HTTP de résiliation d’abonnement |
| Authentification de la résiliation d’abonnement |authentication |Authentification HTTP à utiliser pour la résiliation d’abonnement. Voir [Connecteur HTTP](connectors-native-http.md#authentication) pour plus d’informations |

<br>

**Détails des résultats**

Requête Webhook

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| headers |objet |En-têtes de requête Webhook |
| body |objet |Objet de la requête Webhook |
| Code d’état |int |Code d’état de la requête Webhook |

- - -
## <a name="next-steps"></a>Étapes suivantes
Vous trouverez ci-dessous plus d’informations sur la manière d’avancer le développement de vos applications logiques, ainsi que sur notre communauté.

## <a name="create-a-logic-app"></a>Créer une application logique
Essayez la plateforme et [créez une application logique](../logic-apps/logic-apps-create-a-logic-app.md) dès maintenant. Vous pouvez explorer les autres connecteurs disponibles dans les applications logiques en examinant notre [liste d’API](apis-list.md).



<!--HONumber=Feb17_HO2-->



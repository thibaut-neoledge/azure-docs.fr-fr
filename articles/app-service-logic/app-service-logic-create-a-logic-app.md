<properties
	pageTitle="Créer une application logique | Microsoft Azure"
	description="Apprendre à créer une application logique connectant les services SaaS"
	authors="jeffhollan"
	manager="dwrede"
	editor=""
	services="logic-apps"
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/16/2016"
	ms.author="jehollan"/>

# Créer une application logique connectant les services SaaS

Cette rubrique vous explique comment vous pouvez vous familiariser avec [Azure Logic Apps](app-service-logic-what-are-logic-apps.md) en quelques minutes. Nous allons étudier un workflow simple qui vous permet d’envoyer des tweets intéressants à votre adresse e-mail.

Pour activer ce scénario, vous avez besoin des éléments suivants :

- Un abonnement Azure
- un compte Twitter ;
- Une boîte aux lettres Outlook.com ou Office 365 hébergée

## Créer une application logique pour vous envoyer des tweets par courrier électronique

1. Dans le [tableau de bord du Portail Azure](https://portal.azure.com), sélectionnez **Nouveau**.
2. Dans la barre de recherche, recherchez « application logique », puis sélectionnez **Application logique**. Vous pouvez également sélectionner **Nouveau**, **Web + mobile**, puis **Application logique**.
3. Entrez un nom pour votre application logique, sélectionnez un emplacement et un groupe de ressources, puis sélectionnez **Créer**. Si vous sélectionnez **Épingler au tableau de bord**, l’application logique s’ouvre automatiquement une fois déployée.
4. Après avoir ouvert votre application logique pour la première fois, vous pouvez sélectionner un modèle à partir duquel démarrer. Pour l’instant, cliquez sur **Blank Logic App** (Application logique vide) pour créer une application de toutes pièces.
1. Le premier élément que vous devez créer est le déclencheur. Il s’agit de l’événement qui démarrera votre application logique. Recherchez **twitter** dans la zone de recherche de déclencheur, puis sélectionnez cette application.
7. Ensuite, tapez un terme de recherche qui servira de déclencheur. Les champs **Fréquence** et **Intervalle** déterminent la fréquence à laquelle votre application logique recherchera de nouveaux tweets (et renverra tous les tweets pendant cet intervalle de temps). ![Recherche Twitter](./media/app-service-logic-create-a-logic-app/twittersearch.png)

5. Sélectionnez le bouton **Nouvelle étape**, puis choisissez **Ajouter une action** ou **Ajouter une condition**.
6. Lorsque vous sélectionnez **Ajouter une action**, vous pouvez effectuer une recherche parmi les [connecteurs disponibles](../connectors/apis-list.md) pour choisir une action. Par exemple, vous pouvez sélectionner **Outlook.com - Envoyer un message électronique** pour envoyer du courrier à partir d’une adresse outlook.com : ![Actions](./media/app-service-logic-create-a-logic-app/actions.png)

7. À présent, vous devez renseigner les paramètres du message électronique : ![Paramètres](./media/app-service-logic-create-a-logic-app/parameters.png)

8. Pour finir, vous pouvez sélectionner **Enregistrer** pour générer votre application logique.

## Gérer votre application logique après la création

Votre application logique est maintenant opérationnelle. Elle recherchera à intervalles réguliers des tweets contenant le terme de recherche que vous avez entré. Si elle trouve un tweet correspondant, elle vous enverra un e-mail. Pour finir, vous allez découvrir comment désactiver l'application ou analyser ses performances.

1. Accédez au [Portail Azure](https://portal.azure.com).

1. Cliquez sur **Parcourir** sur le côté gauche de l’écran, et sélectionnez **Applications logiques**.

2. Cliquez sur la nouvelle application logique que vous venez de créer pour afficher l'état actuel et des informations générales.

3. Pour modifier votre nouvelle application logique, cliquez sur **Modifier**.

5. Pour désactiver l’application, cliquez sur **Désactiver** dans la barre de commandes.

1. Visualisez les historiques d’exécution et de déclencheur pour surveiller les moments où votre application logique est en cours d’exécution. Vous pouvez cliquer sur **Actualiser** pour visualiser les données les plus récentes.

En moins de cinq minutes, vous avez réussi à configurer une application logique simple exécutée dans le cloud. Pour en savoir plus sur l’utilisation des fonctionnalités des applications logiques, consultez [Utiliser les fonctionnalités des applications logiques]. Pour en savoir plus sur les définitions d'application logique, consultez la rubrique [Créer des définitions d'application logique](app-service-logic-author-definitions.md).

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Utiliser les fonctionnalités des applications logiques]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0803_2016-->
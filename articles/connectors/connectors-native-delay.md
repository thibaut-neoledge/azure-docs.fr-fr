<properties
	pageTitle="Ajout d’un retard dans des applications logiques | Microsoft Azure"
	description="Vue d’ensemble des actions Retarder et Retarder jusqu’à et de leur utilisation avec une application Azure logique."
	services=""
	documentationCenter=""
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# Prise en main des actions Retarder et Retarder jusqu’à

Les actions Retarder et Retarder jusqu’à vous permettent d’exécuter des scénarios de workflow.

Vous pouvez par exemple afficher :

- Attendre un jour de semaine pour envoyer une mise à jour d’état par e-mail.
- Retarder le workflow jusqu’à ce qu’un appel HTTP dispose d’assez de temps avant la reprise et la récupération du résultat.

Pour commencer à utiliser l’action Retarder dans une application logique, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Utilisation d’actions Retarder

Une action est une opération effectuée par le flux de travail défini dans une application logique. [En savoir plus sur les actions](connectors-overview.md).

Voici un exemple de séquence d’utilisation d’une étape de retard dans une application logique :

1. Après avoir ajouté un déclencheur, cliquez sur **Nouvelle étape** pour ajouter une action.
2. Recherchez **Retarder** pour afficher les actions Retarder. Dans cet exemple, nous sélectionnons **Retarder**.

	![Action Retarder](./media/connectors-native-delay/using-action-1.png)

3. Exécutez toutes les propriétés de l’action pour configurer le retard.

	![Configuration du retard](./media/connectors-native-delay/using-action-2.png)

4. Cliquez sur **Enregistrer** pour publier et activer l’application logique.


## Détails de l’action

Le déclencheur de périodicité a les propriétés suivantes qui peuvent être configurées.

### Action Retarder

Cette action retarde l’exécution pour un intervalle de temps donné. A * désigne est un champ obligatoire.

|Nom complet|Nom de la propriété|Description|
|---|---|---|
|Count*|count|Le nombre d’unités à retarder|
|Unit*|unité|L’unité de temps : `Second`, `Minute`, `Hour` ou `Day`|
<br>

### Action Retarder jusqu'à

Cette action retarde l’exécution jusqu’à une date/heure spécifique. A * désigne est un champ obligatoire.

|Nom complet|Nom de la propriété|Description|
|---|---|---|
|Year*|timestamp|L’année jusqu’à laquelle le retard doit être défini (GMT)|
|Month*|timestamp|Le mois jusqu’auquel le retard doit être défini (GMT)|
|Day*|timestamp|Le jour jusqu’auquel le retard doit être défini (GMT)|
<br>


## Étapes suivantes

Essayez maintenant la plateforme et [créez une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md). Vous pouvez explorer les autres connecteurs disponibles dans les applications logiques en examinant notre [liste d’API](apis-list.md).

<!---HONumber=AcomDC_0810_2016-->
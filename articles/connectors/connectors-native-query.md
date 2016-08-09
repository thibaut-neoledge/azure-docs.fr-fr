<properties
	pageTitle="Ajout de l’action de requête dans des applications logiques | Microsoft Azure"
	description="Vue d’ensemble de l’action de requête permettant d’effectuer des actions comme Filtrer le tableau."
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
   ms.date="07/20/2016"
   ms.author="jehollan"/>

# Prise en main de l’action de requête

L’action de requête vous permet d’utiliser des lots et des tableaux pour obtenir des flux de travail afin d’effectuer les tâches suivantes :

- Créer une tâche pour tous les enregistrements à priorité élevée à partir d’une base de données.
- Enregistrer toutes les pièces jointes PDF pour les messages électroniques dans l’objet blob Azure.

Pour commencer à utiliser l’action de requête dans une application logique, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

---

## Utilisation de l’action de requête
	
Une action est une opération effectuée par le flux de travail défini dans une application logique. [En savoir plus sur les actions.](connectors-overview.md) L’action de requête a actuellement une opération exposée dans le concepteur : filtrer le tableau. Celle-ci vous permet d’interroger un tableau et de retourner un jeu de résultats filtrés. Voici comment vous pouvez l’ajouter dans une application logique :

1. Sélectionnez le bouton **Nouvelle étape**
1. Choisissez **Ajouter une action**
1. Dans la zone de recherche d’action, tapez « Filtrer » pour afficher l’action **Filtrer le tableau**

	![Sélectionner l’action de requête](./media/connectors-native-query/using-action-1.png)

1. Sélectionnez un tableau à filtrer (la capture d’écran ci-dessous illustre le tableau des résultats d’une recherche sur Twitter)
1. Créez une condition à évaluer pour chaque élément (la capture d’écran ci-dessous filtre les tweets provenant d’utilisateurs ayant plus de 100 abonnés)

	![Terminer l’action de requête](./media/connectors-native-query/using-action-2.png)

1. L’action génère un nouveau tableau qui contient uniquement les résultats respectant les exigences du filtre
1. Cliquez sur Enregistrer dans le coin supérieur gauche de la barre d’outils, et votre application logique est à la fois enregistrée et publiée (activation)

---

## Détails techniques

Vous trouverez ci-dessous les détails des actions que ce connecteur prend en charge.

## Actions de requête

Une action est une opération effectuée par le flux de travail défini dans une application logique. [En savoir plus sur les actions.](connectors-overview.md) Le connecteur a 1 action possible.

|Action|Description|
|---|---|
|Filtrer le tableau|Évaluer une condition pour chaque élément d’un tableau et retourner les résultats|

### Détails de l’action

L’action de requête est créée avec 1 action possible. Vous trouverez ci-dessous plus d’informations sur chacune des actions, leurs champs obligatoires et facultatifs, et les détails des résultats correspondants associés à leur utilisation.

#### Filtrer le tableau
Exécuter une requête HTTP sortante. Une * signifie que le champ est obligatoire.

|Display Name|Nom de la propriété|Description|
|---|---|---|
|De*|from|Le tableau à filtrer|
|Condition*|où|La condition à évaluer pour chaque élément|
<br>

**Détails des résultats**

Réponse HTTP

|Nom de la propriété|Type de données|Description|
|---|---|---|
|Tableau filtré|array|Tableau contenant un objet pour chaque résultat filtré|

---

## Étapes suivantes

Vous trouverez ci-dessous plus d’informations sur la manière d’avancer le développement de vos applications logiques, ainsi que sur notre communauté.

## Créer une application logique

Essayez la plateforme et [créez une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md) dès maintenant. Vous pouvez explorer les autres connecteurs disponibles dans les applications logiques en examinant notre [liste d’API](apis-list.md).

<!---HONumber=AcomDC_0727_2016-->
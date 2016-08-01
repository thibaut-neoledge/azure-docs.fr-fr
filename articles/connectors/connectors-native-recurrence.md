<properties
	pageTitle="Ajout du déclencheur de périodicité dans des applications logiques | Microsoft Azure"
	description="Vue d’ensemble du déclencheur de périodicité et de son utilisation avec une application logique Azure."
	services=""
	documentationCenter="" 
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="app-service-logic"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# Prise en main du déclencheur de périodicité

Avec le déclencheur de périodicité, vous pouvez créer des workflows puissants dans le cloud, notamment :

- planifier un workflow pour l’exécution quotidienne d’une procédure SQL stockée ;
- envoyer par e-mail un résumé de tous les tweets de la semaine écoulée sur la base d’un hashtag spécifique.

Pour commencer à utiliser le déclencheur de périodicité dans une application logique, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

---

## Utilisation d’un déclencheur de périodicité

Un déclencheur est un événement qui peut être utilisé pour lancer le workflow défini dans une application logique. [En savoir plus sur les déclencheurs](connectors-overview.md).

Voici un exemple de séquence de configuration d’un déclencheur de périodicité dans une application logique.

1. Ajoutez le déclencheur **Périodicité** en tant que première étape dans une application logique
1. Renseignez les paramètres pour l’intervalle de périodicité
1. L’application logique sera exécutée après l’écoulement de chaque intervalle de temps

![Déclencheur HTTP](./media/connectors-native-recurrence/using-trigger.png)

---

## Détails techniques

Vous trouverez ci-dessous les détails pour le déclencheur de périodicité et les paramètres que vous pouvez configurer.

### Détails du déclencheur

Le déclencheur de périodicité a les propriétés suivantes qui peuvent être configurées.

#### Périodicité
Déclencher une application logique après un intervalle de temps spécifié. Une * signifie que le champ est obligatoire.

|Display Name|Nom de la propriété|Description|
|---|---|---|
|Frequency (Fréquence)*|frequency|L’unité de temps : `Second`, `Minute`, `Hour`, `Day` ou `Year`|
|Interval (Intervalle)*|interval|Intervalle de la fréquence donnée pour la récurrence|
|Time Zone (Fuseau horaire)|timeZone|Si une heure de début (startTime) est spécifiée sans décalage UTC, ce fuseau horaire est utilisé|
|Heure de début|startTime|Heure de début au [format ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Si une heure de début (startTime) est spécifiée sans décalage UTC, ce fuseau horaire est utilisé|
<br>


## Étapes suivantes

Vous trouverez ci-dessous plus d’informations sur la manière d’avancer le développement de vos applications logiques, ainsi que sur notre communauté.

## Créer une application logique

Essayer la plateforme et [créez une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md) dès maintenant. Vous pouvez explorer les autres connecteurs disponibles dans les applications logiques en examinant notre [liste d’API](apis-list.md).

<!---HONumber=AcomDC_0720_2016-->
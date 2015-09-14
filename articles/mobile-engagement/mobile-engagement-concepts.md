<properties
	pageTitle="Concepts de Mobile Engagement | Microsoft Azure"
	description="Concepts d’Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/10/2015"
	ms.author="piyushjo"/>

# Concepts d’Azure Mobile Engagement

Mobile Engagement définit quelques concepts communs à toutes les plateformes prises en charge. Cet article décrit brièvement ces concepts.

Cet article est également un bon point de départ si vous débutez avec Mobile Engagement. Assurez-vous de lire la documentation propre à la plateforme que vous utilisez, car elle définira plus en détail les concepts décrits ici, avec plus d’exemples et en indiquant les éventuelles limitations.

## Appareils et utilisateurs
Mobile Engagement identifie les utilisateurs en générant un identificateur unique pour chaque appareil. Cet identificateur est appelé identificateur de l'appareil (ou `deviceid`). Il est généré de sorte que toutes les applications en cours d'exécution sur le même appareil partagent le même identificateur d'appareil.

Implicitement, cela signifie que Mobile Engagement considère qu'un appareil appartient à un seul utilisateur et que, par conséquent, les utilisateurs et les appareils sont des concepts équivalents.

## Sessions et activités
Une session désigne une utilisation de l’application effectuée par un utilisateur, à partir du moment où il commence à l’utiliser jusqu’à ce qu’il arrête.

Une activité désigne une utilisation d'une sous-partie de l'application effectuée par un utilisateur (il s'agit généralement d'un écran, mais il peut s'agir de tout autre élément de l'application).

Un utilisateur peut effectuer seulement une activité à la fois.

Une activité est identifiée par un nom (limité à 64 caractères) et peut éventuellement incorporer des données supplémentaires (dans la limite de 1 024 octets).

Les sessions sont calculées automatiquement à partir de la séquence d’activités effectuées par les utilisateurs. Une session démarre quand l’utilisateur démarre sa première activité et s’arrête lorsqu’il a terminé sa dernière activité. Cela signifie qu’il n’est pas nécessaire de démarrer ou d’arrêter une session de manière explicite. En revanche, les activités sont démarrées ou arrêtées de manière explicite. Si aucune activité n’est signalée, aucune session n’est signalée.

## Événements
Les événements sont utilisés pour signaler des actions instantanées (comme un appui sur un bouton ou des articles lus par les utilisateurs).

Un événement peut être associé à la session active ou à une tâche en cours, ou être autonome.

Un événement est identifié par un nom (limité à 64 caractères) et peut éventuellement incorporer des données supplémentaires (dans la limite de 1 024 octets).

## Erreur
Les erreurs sont utilisés pour signaler des problèmes correctement détectés par l'application (tels que des actions incorrectes de l'utilisateur ou des échecs d'appel d'API).

Une erreur peut être associée à la session active ou à une tâche en cours, ou être autonome.

Une erreur est identifiée par un nom (limité à 64 caractères) et peut éventuellement incorporer des données supplémentaires (dans la limite de 1 024 octets).

## Travail
Les tâches sont utilisées pour signaler des actions ayant une durée (comme la durée des appels d'API, la durée d'affichage des publicités, la durée des tâches d'arrière-plan ou la durée des actions de l'utilisateur).

Une tâche n’est pas liée à une session, car elle peut être effectuée en arrière-plan, sans aucune interaction de l’utilisateur.

Une tâche est identifiée par un nom (limité à 64 caractères) et peut éventuellement incorporer des données supplémentaires (dans la limite de 1 024 octets).

## Blocage
Les blocages sont émis automatiquement par le SDK Mobile Engagement pour signaler les échecs de l’application (problèmes non détectés par l’application qui entraînent son blocage).

## Informations de l'application
Les informations de l’application sont utilisées pour baliser les utilisateurs, c’est-à-dire pour associer des données aux utilisateurs d’une application (elles sont assimilables aux cookies web, sauf qu’elles sont stockées côté serveur sur la plateforme Azure Mobile Engagement).

Une information de l’application peut être enregistrée à l’aide de l’API du SDK Mobile Engagement ou de l’API d’appareil de la plateforme Mobile Engagement.

Une information de l’application est une paire clé/valeur associée à un appareil. La clé est le nom de l'information de l'application (limité à 64 lettres ASCII [a-zA-Z], chiffres [0-9] et traits de soulignement [\_]). La valeur (limitée à 1 024 caractères) peut être une chaîne, un entier, une date (aaaa-MM-jj) ou une valeur booléenne (true ou false).

N'importe quelle quantité d'informations de l'application peut être associée à un appareil, dans les limites définies par les conditions de tarification de Mobile Engagement. Pour une clé donnée, Mobile Engagement effectue le suivi uniquement de la dernière valeur définie (aucun historique). La définition ou la modification de la valeur d'une information de l'application oblige Mobile Engagement à réévaluer les critères d'audience définis sur cette information de l'application (le cas échéant), ce qui signifie que les informations de l'application peuvent être utilisées pour déclencher des opérations push en temps réel.

## Données supplémentaires
Les données supplémentaires (ou extras) sont des données arbitraires qui peuvent être associées à des événements, des erreurs, des activités et des tâches.

Les extras sont structurés de la même façon que les objets JSON : ils sont constitués d'une arborescence de paires clé/valeur. Les clés sont limitées à 64 lettres ASCII [a-zA-Z], chiffres [0-9] et traits de soulignement [\_]) et la taille totale des extras est limitée à 1 024 caractères (une fois encodés au format JSON par le SDK Mobile Engagement).

L'intégralité de l'arborescence de paires clé/valeur est stockée en tant qu'objet JSON. Toutefois, seul le premier niveau de clés/valeurs est décomposé pour être directement accessible à certaines fonctions avancées, telles que les segments (par exemple, vous pouvez facilement définir un segment appelé « Fans SciFi » constitué de tous les utilisateurs ayant envoyé au moins 10 fois l’événement « content\_viewed » avec la clé extra « content\_type » défini sur la valeur « scifi » le mois dernier). Il est donc fortement recommandé de n’envoyer que des extras constitués de listes simples de paires clé/valeur utilisant des valeurs scalaires (par exemple, chaînes, dates, entiers ou valeurs booléennes).

## Étapes suivantes

- [Vue d’ensemble du Kit de développement logiciel (SDK) de Windows Universal pour Azure Engagement Mobile](mobile-engagement-windows-store-sdk-overview.md)
- [Vue d’ensemble du Kit de développement logiciel (SDK) Silverlight de Windows Phone pour Azure Engagement Mobile](mobile-engagement-windows-phone-sdk-overview.md)
- [Kit de développement logiciel (SDK) iOS pour Azure Mobile Engagement](mobile-engagement-ios-sdk-overview.md)
- [SDK Android pour Azure Mobile Engagement](mobile-engagement-android-sdk-overview.md)

<!---HONumber=September15_HO1-->
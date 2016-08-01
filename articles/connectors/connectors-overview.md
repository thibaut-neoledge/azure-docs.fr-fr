<properties
	pageTitle="Vue d’ensemble des connecteurs d’applications logique | Microsoft Azure"
	description="Vue d’ensemble des connecteurs pouvant être utilisés dans une application logique"
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
   ms.date="07/15/2016"
   ms.author="jehollan"/>

# Utilisation des connecteurs dans une application logique

Les connecteurs permettent d’accéder rapidement aux événements, aux données et aux actions à travers tous les services, protocoles et plates-formes. La liste complète des connecteurs pris en charge par les applications logiques [est disponible ici](apis-list.md). Les connecteurs peuvent être utilisés comme déclencheurs ou comme actions dans une application logique, et leur utilisation peut nécessiter une *connexion* configurée (par exemple : autoriser un compte Twitter pour accéder ou publier de votre part).

## Concepts de base

Les connecteurs sont des services hébergés accessibles à partir des applications logiques et permettant de relier ces dernières à d’autres services tels que Dynamics, Azure, Salesforce, [et d’autres encore](apis-list.md). Ils sont déployés et gérés par Microsoft, ce qui signifie que vous pouvez créer vos flux de travail d’intégration sans avoir à vous soucier de la mise à l’échelle, du débit ou de la sécurité. Vous pouvez ajouter un connecteur à une application logique en recherchant et en sélectionnant une action ou un déclencheur de connecteur sous **Afficher les API gérées par Microsoft**.

![Menu Action pour la sélection de déclencheurs][1]

Chaque action ou déclencheur de connecteur dispose d’un ensemble de propriétés spécifiques à configurer. Vous pouvez cliquer sur les boutons d’information pour en apprendre plus sur une action, ou vous reporter à la documentation associée [pour en savoir plus](apis-list.md).

Pour une intégration à un service ou une API qui ne fait pas encore office de connecteur, vous pouvez également étendre les applications logiques via un [connecteur personnalisé](../app-service-logic/app-service-logic-create-api-app.md), ou simplement appeler directement le service via un protocole tel que HTTP.

## Déclencheurs

Certains connecteurs disposent d’un déclencheur, ce qui signifie qu’un événement lié à ce connecteur va déclencher une application logique, et transmettre toutes les données dans le cadre de l’action du déclencheur. Un déclencheur constitue toujours la première étape dans une application logique. Les déclencheurs populaires incluent des opérations telles que :
 
 * Périodicité - exécution toutes les heures
 * Quand une requête HTTP est reçue
 * Lorsqu’un élément est ajouté à une file d’attente
 * Lors de la réception d’un message électronique
 
Certains déclencheurs agissent instantanément lors d’un événement en envoyant une notification à l’application logique, tandis que d’autres nécessitent la configuration d’un intervalle de périodicité pour définir la fréquence à laquelle l’application logique vérifiera la présence d’une événement dans le service (jusqu’à une fois toutes les 15 secondes).

Une fois qu’un événement est reçu, l’application logique se déclenche et les actions du flux de travail s’enclenchent. Vous serez également en mesure d’accéder aux données à partir du déclencheur à n’importe quelle étape du flux de travail (par exemple, le déclencheur 'On a new tweet (Sur un nouveau tweet)' transmettra le tweet à l’exécution).

## Actions

La plupart des connecteurs ont une ou plusieurs actions qui peuvent être exécutées dans le cadre du flux de travail. Les actions sont les étapes qui se produisent après le déclenchement de l’exécution par un déclencheur. Pour ajouter une action, cliquez sur le bouton **Nouvelle étape**, puis recherchez le connecteur que vous souhaitez utiliser. Une fois sélectionné (et après avoir configuré les [connexions](#connections) qui peuvent être nécessaires), vous verrez s’afficher la fiche d’action que vous pouvez configurer. Vous pouvez sélectionner des données à partir des étapes précédentes en cliquant sur un des jetons de sorties, ou rentrer toutes les autres configurations requises.

![Configuration d’une action de connecteur][2]

## Connexions

La plupart des connecteurs nécessitent de configurer une *connexion* avant de pouvoir être utilisés. Une *connexion* est une configuration d’accès ou de connexion nécessaire pour accéder au connecteur. Pour les connecteurs utilisant OAuth, créer une connexion signifie se connecter au service (tel que Office 365, Salesforce ou GitHub) où votre jeton d’accès peut être chiffré et stocké de manière sécurisée dans un magasin de secrets Azure. D’autres connecteurs (tels que FTP et SQL) nécessitent une connexion configurée à l’aide de paramètres tels que l’adresse du serveur, le nom d’utilisateur et mot de passe. Ces informations de configuration de connexion sont également chiffrées et stockées de manière sécurisée. Les connexions pourront accéder au service aussi longtemps que le service l’autorise. Pour les connexions Azure Active Directory OAuth (telles que Office 365 et Dynamics), nous pouvons continuer à actualiser le jeton d’accès indéfiniment. D’autres services peuvent limiter la durée au cours de laquelle nous pouvons utiliser un jeton sans synchronisation. En général, certaines actions, telles que la modification d’un mot de passe, invalident tous les jetons d’accès.

Vous pouvez consulter et gérer les connexions depuis Azure en cliquant sur **Parcourir**, puis en sélectionnant **Connexions API**. À partir de la ressource de connexions API, vous pouvez afficher, modifier, mettre à jour ou renouveler l’autorisation de toutes les connexions que vous avez créées.

## Étapes suivantes

- [Créez votre première application logique](../app-service-logic/app-service-logic-create-a-logic-app.md)
- [Découvrez des utilisations et des exemples courant(e)s d’applications logiques](../app-service-logic/app-service-logic-examples-and-scenarios.md)
- [Commencez à utiliser les déclencheurs et actions d’intégration d’entreprise](../app-service-logic/app-service-logic-enterprise-integration-overview.md)

<!--Image References -->
[1]: ./media/connectors-overview/addAction.png
[2]: ./media/connectors-overview/configureAction.png

<!---HONumber=AcomDC_0720_2016-->
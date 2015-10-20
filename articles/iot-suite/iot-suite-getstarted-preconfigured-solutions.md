<properties
	pageTitle="Prise en main des solutions préconfigurées | Microsoft Azure"
	description="Suivez ce didacticiel pour apprendre à déployer une solution préconfigurée Azure IoT Suite."
	services=""
	documentationCenter=""
	authors="aguilaaj"
	manager="timlt"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="araguila"/>

# Didacticiel : prise en main des solutions préconfigurées IoT

## Introduction

Les solutions préconfigurées Azure IoT connectent plusieurs services Azure IoT pour illustrer une solution de bout en bout qui répond à un scénario d’entreprise IoT (Internet des objets).

Ce didacticiel montre comment configurer une solution préconfigurée nommée **Surveillance à distance**. Il montre également comment afficher les fonctionnalités de base de la solution préconfigurée Surveillance à distance.

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

-   Un abonnement Azure actif.

    Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][].

## Configurer la solution préconfigurée Surveillance à distance

1.  Ouvrez une session sur https://www.azureiotsuite.com, puis cliquez sur **+** pour créer une solution.

2.  Sélectionnez **Surveillance à distance** comme type de solution.

3.  Tapez un **Nom de solution** pour votre solution de surveillance à distance préconfigurée.

4.  Validez la **Région** et l’**Abonnement** pour lesquels vous voulez configurer cette solution.

5.  Cliquez sur **Créer la solution**.

## Afficher le tableau de bord de la solution de surveillance à distance

1.  Une fois que l’approvisionnement est terminé et que la vignette de votre solution préconfigurée indique **Prêt**, cliquez sur **Lancer** pour ouvrir le tableau de bord de votre solution de surveillance à distance dans un nouvel onglet.

2.  Par défaut, le **Tableau de bord** est sélectionné dans le menu de gauche. Il s’agit du tableau de bord de votre solution.

## Afficher la liste des appareils de la solution

1.  Cliquez sur **Appareils** dans le menu de gauche pour accéder à la liste des appareils de cette solution.

2.  Lors de l’approvisionnement, vous verrez quatre appareils simulés.

3.  Cliquez sur un **appareil spécifique** dans la liste pour afficher les détails de cet appareil.

## Envoyer une commande à un appareil

1.  Cliquez sur **Envoyer la commande** dans le volet d’informations de l’appareil simulé sélectionné.

2.  Sélectionnez **PingDevice** dans la liste de commandes.

3.  Cliquez sur **Envoyer la commande**.

4.  Visualisez l’état de la commande tel qu’il apparaît dans l’historique des commandes.

## Ajouter un nouvel appareil simulé

1.  Cliquez sur **←** (flèche retour) pour revenir à la liste des appareils.

2.  Cliquez sur **+ Ajouter un appareil** dans le coin inférieur gauche pour ajouter un nouvel appareil.

3.  Cliquez sur **Ajouter un nouveau** pour **Appareil simulé**.

4.  Sélectionnez **Me laisser définir mon propre ID d’appareil** et ajoutez un nom unique d’ID d’appareil.

5.  Cliquez sur **Create**.

6.  Cliquez sur **←** (flèche retour) pour revenir à la liste des appareils.

7.  Vérifiez que votre appareil est **En cours d’exécution** dans la liste des appareils.

## Afficher et modifier des règles de solution

1.  Notez la présence du tableau **Historique des alertes** dans le **Tableau de bord de la solution**.

2.  Ces alertes sont déclenchées par une sortie de règle **AlarmTemp** spécifiée dans **Règles**.

3.  Cliquez sur **Règles** dans le menu de gauche pour accéder aux règles de cette solution.

4.  Pendant l’approvisionnement, vous verrez une règle déjà activée.

5.  Cliquez sur la **règle** dans la liste des règles pour afficher ses propriétés.

6.  Cliquez sur **Modifier** dans le volet de propriétés de règle.

7.  Définissez un **Seuil** de 30 et conservez toutes les autres propriétés.

8.  Cliquez sur **Enregistrer et afficher les règles**.

9.  Revenez au tableau **Historique des alertes** dans le **Tableau de bord de la solution** et notez le changement de déclencheur dû à la mise à jour de la règle.

## Étapes suivantes

Maintenant que vous avez créé une solution préconfigurée opérationnelle, vous pouvez passer aux scénarios suivants :

-   [Conseils sur la personnalisation des solutions préconfigurées][]

-   [Vue d’ensemble d’IoT Suite][]

[Version d'évaluation gratuite d'Azure]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-FR%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F%20target=

[Conseils sur la personnalisation des solutions préconfigurées]: https://azure.microsoft.com/documentation/articles/iot-suite-guidance-on-customizing-preconfigured-solutions/
[Vue d’ensemble d’IoT Suite]: https://azure.microsoft.com/documentation/articles/iot-suite-overview/

<!---HONumber=Oct15_HO3-->
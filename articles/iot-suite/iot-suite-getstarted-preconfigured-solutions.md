<properties
	pageTitle="Prise en main des solutions préconfigurées | Microsoft Azure"
	description="Suivez ce didacticiel pour apprendre à déployer une solution préconfigurée Azure IoT Suite."
	services=""
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="12/03/2015"
     ms.author="dobett"/>

# Didacticiel : prise en main des solutions préconfigurées IoT

## Introduction

Les [solutions préconfigurées][lnk-preconfigured-solutions] d’Azure IoT Suite regroupent plusieurs services Azure IoT pour offrir des solutions de bout en bout permettant d’implémenter des scénarios IoT d’entreprise.

Ce didacticiel montre comment configurer la solution préconfigurée de *surveillance à distance*. Il présente également les fonctionnalités de base de la solution préconfigurée de surveillance à distance.

Pour suivre le didacticiel, vous devez disposer d’un abonnement Azure actif.

> [AZURE.NOTE]Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][lnk_free_trial].

## Configurer la solution préconfigurée de surveillance à distance

1.  Connectez-vous à [azureiotsuite.com][lnk-azureiotsuite] à l’aide des identifiants de votre compte Azure, puis cliquez sur **+** pour créer une nouvelle solution.

2.  Cliquez sur **Sélectionner** sur la vignette **Surveillance à distance**.

3.  Entrez un **Nom de solution** pour votre solution préconfigurée de surveillance à distance.

4.  Sélectionnez la **Région** et l’**Abonnement** pour lesquels vous voulez déployer la solution.

5.  Cliquez sur **Créer une solution** pour commencer le processus de déploiement. Cette opération prend généralement plusieurs minutes.

## Afficher le tableau de bord de la solution de surveillance à distance

1.  Une fois que l’approvisionnement est terminé et que la vignette de votre solution préconfigurée indique **Prêt**, cliquez sur **Lancer** pour ouvrir le portail de votre solution de surveillance à distance dans un nouvel onglet.

    ![][img-launch-solution]

2.  Par défaut, le portail de solution affiche le *tableau de bord de solution*. Vous pouvez sélectionner d’autres vues à partir du menu de gauche.

    ![][img-dashboard]

## Afficher la liste des appareils de la solution

1.  Cliquez sur **Appareils** dans le menu de gauche pour accéder à la *liste des appareils* de cette solution.

    ![][img-devicelist]

2.  Vous pouvez voir que quatre appareils simulés ont été créés par le processus de déploiement.

3.  Cliquez sur un appareil de la liste pour en afficher les détails.

    ![][img-devicedetails]

## Envoyer une commande à un appareil

1.  Cliquez sur **Commandes** dans le volet d’informations de l’appareil sélectionné.

    ![][img-devicecommands]

2.  Sélectionnez **PingDevice** dans la liste de commandes.

3.  Cliquez sur **Envoyer la commande**.

4.  Vous pouvez visualiser l’état de la commande dans l’historique des commandes.

    ![][img-pingcommand]

## Ajouter un nouvel appareil simulé

1.  Retournez à la liste des appareils.

2.  Cliquez sur **+ Ajouter un appareil** dans le coin inférieur gauche pour ajouter un nouvel appareil.

    ![][img-adddevice]

3.  Cliquez sur **Ajouter un nouveau** sur la vignette **Appareil simulé**.

    ![][img-addnew]

4.  Sélectionnez **Me laisser définir mon propre ID d’appareil** et ajoutez un nom unique d’ID d’appareil, par exemple **monappareil\_01**.

5.  Cliquez sur **Create**.

    ![][img-definedevice]

6. À l’étape 3 de la procédure **Ajouter un appareil simulé**, cliquez sur **Terminé** pour revenir à la liste des appareils.

7.  Vous pouvez vérifier que votre appareil est **En cours d’exécution** dans la liste des appareils.

    ![][img-runningnew]

## Afficher et modifier des règles de solution

1.  Retournez au tableau de bord de la solution et consultez la vignette **Historique des alertes**.

    ![][img-alarmhistory]

2.  La règle **AlarmTemp** déclenche ces alertes.

3.  Cliquez sur **Règles** dans le menu de gauche pour afficher les règles de cette solution.

    ![][img-rules]

4.  La solution préconfigurée déploie deux règles.

5.  Cliquez sur la règle **Température** dans la liste des règles pour afficher ses propriétés.

6.  Cliquez sur **Modifier** dans le volet de propriétés de la règle.

    ![][img-displayrule]

7.  Définissez un **Seuil** de 30 et conservez toutes les autres propriétés.

8.  Cliquez sur **Enregistrer et afficher les règles**.

    ![][img-editrule]

9.  Revenez au tableau **Historique des alertes** dans le **Tableau de bord de la solution** et notez le changement de comportement dû à la mise à jour de la règle.

    ![][img-newhistory]

## Étapes suivantes

Maintenant que vous avez créé une solution préconfigurée opérationnelle, vous pouvez passer aux scénarios suivants :

-   [Conseils sur la personnalisation des solutions préconfigurées][lnk-customize]
-   [Présentation de la solution préconfigurée de maintenance prédictive][lnk-predictive]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-alarmhistory]: media/iot-suite-getstarted-preconfigured-solutions/alarmhistory.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-displayrule]: media/iot-suite-getstarted-preconfigured-solutions/displayrule.png
[img-editrule]: media/iot-suite-getstarted-preconfigured-solutions/editrule.png
[img-newhistory]: media/iot-suite-getstarted-preconfigured-solutions/newhistory.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-predictive]: iot-suite-predictive-overview.md

<!---HONumber=AcomDC_1210_2015-->
<properties
	pageTitle="Prise en main des solutions préconfigurées | Microsoft Azure"
	description="Suivez ce didacticiel pour apprendre à déployer une solution préconfigurée Azure IoT Suite."
	services=""
    suite="iot-suite"
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="03/02/2016"
     ms.author="dobett"/>

# Didacticiel : prise en main des solutions préconfigurées IoT

## Introduction

Les [solutions préconfigurées][lnk-preconfigured-solutions] d’Azure IoT Suite regroupent plusieurs services Azure IoT pour offrir des solutions de bout en bout permettant d’implémenter des scénarios IoT d’entreprise.

Ce didacticiel montre comment configurer la solution préconfigurée de *surveillance à distance*. Il présente également les fonctionnalités de base de la solution préconfigurée de surveillance à distance.

Pour suivre le didacticiel, vous devez disposer d’un abonnement Azure actif.

> [AZURE.NOTE]  Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][lnk_free_trial].

## Configurer la solution préconfigurée de surveillance à distance

1.  Connectez-vous à [azureiotsuite.com][lnk-azureiotsuite] à l’aide des identifiants de votre compte Azure, puis cliquez sur **+** pour créer une nouvelle solution.

    > [AZURE.NOTE] Si vous rencontrez des problèmes avec les autorisations nécessaires pour approvisionner une solution, consultez [Autorisations sur le site azureiotsuite.com][lnk-permissions] pour obtenir de l’aide.

2.  Cliquez sur **Sélectionner** sur la vignette **Surveillance à distance**.

3.  Entrez un **Nom de solution** pour votre solution préconfigurée de surveillance à distance.

4.  Sélectionnez la **Région** et l’**Abonnement** pour lesquels vous voulez déployer la solution.

5.  Cliquez sur **Créer une solution** pour commencer le processus de déploiement. Cette opération prend généralement plusieurs minutes.

## Attendre la fin du processus d’approvisionnement

1. Cliquez sur la vignette de votre solution avec l’état **Approvisionnement**.
 
2. Notez les **états d’approvisionnement** à mesure que les services Azure sont déployés dans votre abonnement Azure.

3. Une fois l’approvisionnement terminé, l’état devient **Prêt**.

4. Cliquez sur la vignette ; les détails de votre solution apparaissent alors dans le volet de droite.

> [AZURE.NOTE] Si vous rencontrez des problèmes lors du déploiement de la solution préconfigurée, consultez [Autorisations sur le site azureiotsuite.com][lnk-permissions] et la [FAQ][lnk-faq]. Si les problèmes persistent, créez un ticket de service sur le [portail][lnk-portal].

Certains détails de votre solution semblent-ils faire défaut ? Faites-nous part de vos suggestions concernant les fonctionnalités sur [User Voice](https://feedback.azure.com/forums/321918-azure-iot).

## Afficher le tableau de bord de la solution de surveillance à distance

Grâce au tableau de bord de solution, vous pouvez gérer la solution déployée. Par exemple, vous pouvez afficher les données de télémétrie, ajouter des appareils et configurer des règles.

1.  Une fois que l’approvisionnement est terminé et que la vignette de votre solution préconfigurée indique **Prêt**, cliquez sur **Lancer** pour ouvrir le portail de votre solution de surveillance à distance dans un nouvel onglet.

    ![][img-launch-solution]

2.  Par défaut, le portail de solution affiche le *tableau de bord de solution*. Vous pouvez sélectionner d’autres vues à partir du menu de gauche.

    ![][img-dashboard]

Le tableau de bord affiche les informations suivantes :

- La carte affiche l'emplacement de chaque appareil connecté à la solution. Lors de la première exécution de la solution, quatre appareils sont simulés. Les appareils simulés sont implémentés en tant qu’Azure WebJobs, et la solution utilise l'API Bing Maps pour tracer les informations sur la carte.
- Le panneau **Historique de télémétrie** panneau trace la télémétrie de l'humidité et de la température d'un appareil sélectionné en temps quasi-réel et affiche les données d'agrégation telles que l’humidité maximale, minimale et moyenne.
- Le panneau **Historique d'alarme** affiche les alarme récentes lorsqu'une valeur de télémétrie a dépassé un seuil défini. Vous pouvez définir vos propres alarmes en plus des exemples créés par la solution préconfigurée.

## Afficher la liste des appareils de la solution

La liste des appareils montre tous les appareils inscrits dans la solution. Vous affichez et modifiez les métadonnées des appareils, ajoutez ou supprimez des appareils et envoyez des commandes aux appareils.

1.  Cliquez sur **Appareils** dans le menu de gauche pour accéder à la *liste des appareils* de cette solution.

    ![][img-devicelist]

2.  La liste des appareils montre que quatre appareils simulés ont été créés par le processus de déploiement.

3.  Cliquez sur un appareil de la liste pour en afficher les détails.

    ![][img-devicedetails]

Le panneau **Détails de l'appareil** comprend trois sections :

- La section **Actions** répertorie les actions que vous pouvez effectuer sur l'appareil. Si vous désactivez l’appareil, il ne sera plus autorisé à envoyer des données de télémétrie ou à recevoir des commandes. Si vous désactivez un appareil, vous pourrez le réactiver ensuite. Vous pouvez ajouter une règle associée à l'appareil qui déclenche une alerte lorsqu'une valeur de télémétrie dépasse un seuil défini. Vous pouvez également envoyer une commande à un appareil. Lorsqu’un appareil se connecte pour la première fois, il indique à la solution à quelles commandes il peut répondre.
- La section **Propriétés d'un appareil** répertorie les métadonnées de l'appareil. Certaines de ces métadonnées proviennent de l'appareil proprement dit (par exemple, le fabricant) et certaines sont générées par la solution (par exemple, l'heure de création). Vous pouvez modifier les métadonnées de l'appareil à partir d'ici.
- La section **Clés d'authentification** répertorie les clés que l'appareil peut utiliser pour s'authentifier avec la solution.

## Envoyer une commande à un appareil

Le volet des détails de l’appareil affiche toutes les commandes prises en charge par un appareil spécifique et vous permet d’envoyer des commandes à un appareil. Lors du premier démarrage d'un appareil, ce dernier envoie des informations sur les commandes qu'il prend en charge à la solution.

1.  Cliquez sur **Commandes** dans le volet d’informations de l’appareil sélectionné.

    ![][img-devicecommands]

2.  Sélectionnez **PingDevice** dans la liste de commandes.

3.  Cliquez sur **Envoyer la commande**.

4.  Vous pouvez visualiser l’état de la commande dans l’historique des commandes.

    ![][img-pingcommand]

La solution effectue le suivi de l'état de chaque commande qu'elle envoie. Initialement, le résultat est **En attente**. Lorsque l’appareil signale qu’il a correctement exécuté la commande, le résultat est défini sur **Réussite** :

## Ajouter un nouvel appareil simulé

1.  Retournez à la liste des appareils.

2.  Cliquez sur **+ Ajouter un appareil** dans le coin inférieur gauche pour ajouter un nouvel appareil.

    ![][img-adddevice]

3.  Cliquez sur **Ajouter un nouveau** sur la vignette **Appareil simulé**.

    ![][img-addnew]
    
    Outre la création d'un nouvel appareil simulé, vous pouvez également ajouter un appareil physique si vous choisissez de créer un **appareil personnalisé**. Pour en savoir plus à ce sujet, [connectez votre appareil à la solution préconfigurée de surveillance à distance IoT Suite][lnk-connecting-devices].

4.  Sélectionnez **Me laisser définir mon propre ID d’appareil** et ajoutez un nom unique d’ID d’appareil, par exemple **monappareil\_01**.

5.  Cliquez sur **Create**.

    ![][img-definedevice]

6. À l’étape 3 de la procédure **Ajouter un appareil simulé**, cliquez sur **Terminé** pour revenir à la liste des appareils.

7. Vous pouvez vérifier que votre appareil est **En cours d’exécution** dans la liste des appareils.

    ![][img-runningnew]

8. Vous pouvez également afficher les données de télémétrie provenant de votre nouvel appareil sur le tableau de bord :

    ![][img-runningnew-2]

## Modifier les métadonnées de l’appareil

1.  Retournez à la liste des appareils.

2.  Sélectionnez votre nouvel appareil dans la **Liste des appareils**, puis cliquez sur **Modifier** pour modifier les **Propriétés d'un appareil** :

    ![][img-editdevice]

3. Faites défiler vers le bas et modifiez les valeurs de latitude et de longitude. Puis cliquez sur **Enregistrer les modifications dans le registre de l’appareil**.

    ![][img-editdevice2]

4. Retournez au tableau de bord, l'emplacement de l’appareil a changé sur la carte :

    ![][img-editdevice3]

## Ajouter une règle pour le nouvel appareil

Il n'existe aucune règle pour le nouvel appareil que vous venez d'ajouter. Dans cette section, vous allez ajouter une règle qui déclenche une alarme lorsque la température signalée par le nouvel appareil dépasse 47 degrés. Avant de commencer, notez que l'historique de télémétrie pour le nouvel appareil sur le tableau de bord affiche que la température de l’appareil ne dépasse jamais 45 degrés.

1.  Retournez à la liste des appareils.

2.  Sélectionnez votre nouvel appareil dans la **Liste des appareils**, puis cliquez sur **Ajouter une règle** pour ajouter une nouvelle règle pour l’appareil.

3. Créez une règle qui utilise **Température** comme champ de données et utilisez **AlarmTemp** comme sortie lorsque la température dépasse 47 degrés :

    ![][img-adddevicerule]

4. Cliquez sur **Enregistrer et afficher les règles** pour enregistrer vos modifications.

5.  Cliquez sur **Commandes** dans le volet Détails du nouvel appareil.

    ![][img-adddevicerule2]

6.  Sélectionnez **ChangeSetPointTemp** dans la liste de commandes et définissez **SetPointTemp** sur 45. Cliquez ensuite sur **Envoyer la commande** :

    ![][img-adddevicerule3]

7.  Retournez au tableau de bord de la solution. Après un bref moment, vous verrez une nouvelle entrée dans le volet **Historique d'alarme** lorsque la température signalée par votre nouvel appareil dépassera le seuil de 47 degrés :

    ![][img-adddevicerule4]

8. Vous pouvez consulter et modifier toutes les règles sur le tableau de bord **Règles** :

    ![][img-rules]

9. Vous pouvez consulter et modifier toutes les mesures qui peuvent être prises en réponse à une règle sur le tableau de bord **Actions** :

    ![][img-actions]

> [AZURE.NOTE] Il est possible de définir des actions pouvant envoyer un message électronique ou un SMS en réponse à une règle ou s’intégrer dans un système métier via une [Application logique][lnk-logic-apps].

## Dans les coulisses

Lorsque vous déployez une solution préconfigurée, le processus de déploiement crée plusieurs ressources dans l'abonnement Azure que vous avez sélectionné. Vous pouvez afficher ces ressources dans le [portail][lnk-portal] Azure. Le processus de déploiement crée un **groupe de ressources** avec un nom basé sur le nom que vous avez choisi pour votre solution préconfigurée :

![][img-portal]

Vous pouvez afficher les paramètres de chaque ressource en la sélectionnant dans la liste des ressources dans le groupe de ressources. La capture d'écran ci-dessus illustre les paramètres pour l’IoT Hub utilisé dans la solution préconfigurée.

Vous pouvez également afficher le code source pour la solution préconfigurée. Le code source de la solution préconfigurée de surveillance à distance est [azure-iot-remote-monitoring][lnk-rmgithub] :

- Le dossier **DeviceAdministration** contient le code source pour le tableau de bord.
- Le dossier **Simulator** contient le code source pour l’appareil simulé.
- Le dossier **EventProcessor** contient le code source pour le processus principal qui gère les données de télémétrie entrantes.

Quand vous avez terminé, vous pouvez supprimer la solution préconfigurée de votre abonnement Azure sur le site [azureiotsuite.com][lnk-azureiotsuite]. Ceci vous permet de supprimer toutes les ressources qui ont été approvisionnées lors de la création de la solution préconfigurée.

> [AZURE.NOTE] Pour vous assurer que vous supprimez tout ce qui concerne la solution préconfigurée, supprimez-la depuis m [azureiotsuite.com][lnk-azureiotsuite] et ne supprimez pas simplement le groupe de ressources dans le portail.

## Étapes suivantes

Une solution préconfigurée opérationnelle étant créée, vous pouvez passer aux procédures suivantes :

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
[img-runningnew-2]: media/iot-suite-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-editdevice]: media/iot-suite-getstarted-preconfigured-solutions/editdevice.png
[img-editdevice2]: media/iot-suite-getstarted-preconfigured-solutions/editdevice2.png
[img-editdevice3]: media/iot-suite-getstarted-preconfigured-solutions/editdevice3.png
[img-adddevicerule]: media/iot-suite-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-getstarted-preconfigured-solutions/portal.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-predictive]: iot-suite-predictive-overview.md
[lnk-connecting-devices]: iot-suite-connecting-devices.md
[lnk-permissions]: https://azure.microsoft.com/documentation/articles/iot-suite-permissions/
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-faq]: https://azure.microsoft.com/documentation/articles/iot-suite-faq/

<!---HONumber=AcomDC_0420_2016-->
---
title: "Prise en main des solutions préconfigurées | Microsoft Docs"
description: "Suivez ce didacticiel pour apprendre à déployer une solution préconfigurée Azure IoT Suite."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 6ab38d1a-b564-469e-8a87-e597aa51d0f7
ms.service: iot-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: f86a70a5207f19063e9992325c8f8d696ca7823e


---
# <a name="tutorial-get-started-with-the-preconfigured-solutions"></a>Didacticiel : prise en main des solutions préconfigurées
## <a name="introduction"></a>Introduction
Les [solutions préconfigurées][lnk-preconfigured-solutions] d’Azure IoT Suite regroupent plusieurs services Azure IoT pour offrir des solutions de bout en bout permettant d’implémenter des scénarios IoT d’entreprise. La solution préconfigurée de *surveillance à distance* se connecte et surveille vos appareils. Cela vous permet d’analyser le flux de données de vos appareils et d’améliorer les résultats de l’entreprise grâce à des processus qui répondent automatiquement à ce flux de données.

Ce didacticiel montre comment configurer la solution préconfigurée de surveillance à distance. Il présente également les fonctionnalités de base de la solution de surveillance à distance. Vous pouvez accéder à la plupart de ces fonctionnalités via le tableau de bord de solution déployé avec la solution préconfigurée :

![Tableau de bord de solution préconfigurée de surveillance à distance][img-dashboard]

Pour suivre ce didacticiel, vous avez besoin d’un compte Azure actif.

> [!NOTE]
> Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, consultez la rubrique [Version d’évaluation gratuite d’Azure][lnk_free_trial].
> 
> 

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="view-the-solution-dashboard"></a>Afficher le tableau de bord de solution
Grâce au tableau de bord de solution, vous pouvez gérer la solution déployée. Par exemple, vous pouvez afficher les données de télémétrie, ajouter des appareils et configurer des règles.

1. Une fois que l’approvisionnement est terminé et que la vignette de votre solution préconfigurée indique **Prêt**, cliquez sur **Lancer** pour ouvrir le portail de votre solution de surveillance à distance dans un nouvel onglet.
   
   ![Lancer la solution préconfigurée][img-launch-solution]
2. Par défaut, le portail de solution affiche le *tableau de bord de solution*. Vous pouvez sélectionner d’autres vues à partir du menu de gauche.
   
   ![Tableau de bord de solution préconfigurée de surveillance à distance][img-dashboard]

Le tableau de bord affiche les informations suivantes :

* La carte affiche l'emplacement de chaque appareil connecté à la solution. Lors de la première exécution de la solution, quatre appareils sont simulés. Les appareils simulés sont implémentés en tant qu’Azure WebJobs, et la solution utilise l'API Bing Maps pour tracer les informations sur la carte.
* Le panneau **Historique de télémétrie** trace la télémétrie de l’humidité et de la température d’un appareil sélectionné en temps quasi-réel et affiche les données d’agrégation telles que l’humidité maximale, minimale et moyenne.
* Le panneau **Historique des alertes** affiche les alarmes récentes lorsqu’une valeur de télémétrie a dépassé un seuil défini. Vous pouvez définir vos propres alarmes en plus des exemples créés par la solution préconfigurée.

## <a name="view-the-device-list"></a>Afficher la liste des appareils
La liste des appareils montre tous les appareils inscrits dans la solution. Vous affichez et modifiez les métadonnées des appareils, ajoutez ou supprimez des appareils et envoyez des commandes aux appareils.

1. Cliquez sur **Appareils** dans le menu de gauche pour accéder à la *liste des appareils* de cette solution.
   
   ![Liste des appareils dans le tableau de bord][img-devicelist]
2. La liste des appareils montre que quatre appareils simulés ont été créés par le processus de déploiement.
3. Cliquez sur un appareil de la liste pour en afficher les détails.
   
   ![Détails de l’appareil dans le tableau de bord][img-devicedetails]

Le panneau **Détails de l’appareil** comprend trois sections :

* La section **Actions** répertorie les actions que vous pouvez exécuter sur l’appareil. Si vous désactivez l’appareil, il ne sera plus autorisé à envoyer des données de télémétrie ou à recevoir des commandes. Si vous désactivez un appareil, vous pourrez le réactiver ensuite. Vous pouvez ajouter une règle associée à l'appareil qui déclenche une alerte lorsqu'une valeur de télémétrie dépasse un seuil défini. Vous pouvez également envoyer une commande à un appareil. Lorsqu’un appareil se connecte pour la première fois, il indique à la solution les commandes auxquelles il peut répondre.
* La section **Propriétés d’un appareil** répertorie les métadonnées de l’appareil. Certaines de ces métadonnées proviennent de l'appareil proprement dit (par exemple, le fabricant) et certaines sont générées par la solution (par exemple, l'heure de création). Vous pouvez modifier les métadonnées de l'appareil à partir d'ici.
* La section **Clés d’authentification** répertorie les clés que l’appareil peut utiliser pour s’authentifier avec la solution.

## <a name="send-a-command-to-a-device"></a>Envoyer une commande à un appareil
Le volet des détails de l’appareil affiche toutes les commandes prises en charge par un appareil spécifique et vous permet d’envoyer des commandes à un appareil. Lors du premier démarrage d'un appareil, ce dernier envoie des informations sur les commandes qu'il prend en charge à la solution.

1. Cliquez sur **Commandes** dans le volet d’informations de l’appareil sélectionné.
   
   ![Commandes de l’appareil dans le tableau de bord][img-devicecommands]
2. Sélectionnez **PingDevice** dans la liste de commandes.
3. Cliquez sur **Envoyer la commande**.
4. Vous pouvez visualiser l’état de la commande dans l’historique des commandes.
   
   ![État de la commande dans le tableau de bord][img-pingcommand]

La solution effectue le suivi de l'état de chaque commande qu'elle envoie. Initialement, le résultat est **En attente**. Lorsque l’appareil signale qu’il a correctement exécuté la commande, le résultat prend la valeur **Réussite**.

## <a name="add-a-new-simulated-device"></a>Ajouter un nouvel appareil simulé
Lorsque vous déployez la solution préconfigurée, vous approvisionnez automatiquement les quatre exemples d’appareils que vous pouvez voir dans la liste des appareils. Ces appareils sont des *simulations d’appareils* en cours d’exécution dans un Azure WebJob. Les appareils simulés vous permettent d’expérimenter plus facilement la solution préconfigurée sans avoir à déployer des appareils physiques réels. Si vous ne souhaitez pas connecter un appareil physique à la solution, consultez le didacticiel [Connexion de votre appareil à la solution préconfigurée de surveillance à distance][lnk-connect-rm].

Les étapes suivantes vous montrent comment ajouter un appareil simulé à la solution :

1. Retournez à la liste des appareils.
2. Cliquez sur **+ Ajouter un appareil** dans le coin inférieur gauche pour ajouter un appareil.
   
   ![Ajouter un appareil à la solution préconfigurée][img-adddevice]
3. Cliquez sur **Ajouter un nouveau** sur la vignette **Appareil simulé**.
   
   ![Définir les détails du nouvel appareil dans le tableau de bord][img-addnew]
   
   Outre la création d’un appareil simulé, vous pouvez également ajouter un appareil physique si vous choisissez de créer un **appareil personnalisé**. Pour plus d’informations sur la connexion d’appareils physiques à la solution, consultez [Connexion de votre appareil à la solution préconfigurée de surveillance à distance][lnk-connect-rm].
4. Sélectionnez **Me laisser définir mon propre ID d’appareil** et ajoutez un nom unique d’ID d’appareil, par exemple **monappareil_01**.
5. Cliquez sur **Create**.
   
   ![Enregistrer un nouvel appareil][img-definedevice]
6. À l’étape 3 de la procédure **Ajouter un appareil simulé**, cliquez sur **Terminé** pour revenir à la liste des appareils.
7. Vous pouvez vérifier que votre appareil est **En cours d’exécution** dans la liste des appareils.
   
    ![Afficher le nouvel appareil dans la liste des appareils][img-runningnew]
8. Vous pouvez également afficher les données de télémétrie provenant de votre nouvel appareil sur le tableau de bord :
   
    ![Afficher la télémétrie du nouvel appareil][img-runningnew-2]

## <a name="edit-the-device-metadata"></a>Modifier les métadonnées de l’appareil
Lors de sa première connexion à la solution, un appareil envoie ses métadonnées à la solution. Lorsque vous modifiez les métadonnées de l’appareil via le tableau de bord de solution, cette dernière envoie les nouvelles valeurs de métadonnées à l’appareil et stocke les nouvelles valeurs dans la base de données DocumentDB de la solution. Pour plus d’informations, consultez [Registre d’identité des appareils et DocumentDB][lnk-devicemetadata].

1. Retournez à la liste des appareils.
2. Sélectionnez votre nouvel appareil dans la **Liste des appareils**, puis cliquez sur **Modifier** pour modifier les **Propriétés d’un appareil** :
   
   ![Modifier les métadonnées de l’appareil][img-editdevice]
3. Faites défiler vers le bas et modifiez les valeurs de latitude et de longitude. Puis cliquez sur **Enregistrer les modifications dans le registre de l’appareil**.
   
    ![Modifier les métadonnées de l’appareil][img-editdevice2]
4. Retournez au tableau de bord, l'emplacement de l’appareil a changé sur la carte :
   
    ![Modifier les métadonnées de l’appareil][img-editdevice3]

## <a name="add-a-rule-for-the-new-device"></a>Ajouter une règle pour le nouvel appareil
Il n'existe aucune règle pour le nouvel appareil que vous venez d'ajouter. Dans cette section, vous allez ajouter une règle qui déclenche une alarme lorsque la température signalée par le nouvel appareil dépasse 47 degrés. Avant de commencer, notez que l'historique de télémétrie pour le nouvel appareil sur le tableau de bord affiche que la température de l’appareil ne dépasse jamais 45 degrés.

1. Retournez à la liste des appareils.
2. Sélectionnez votre nouvel appareil dans la **Liste des appareils**, puis cliquez sur **Ajouter une règle** pour ajouter une règle le concernant.
3. Créez une règle qui utilise **Température** comme champ de données et **AlarmTemp** en tant que sortie lorsque la température dépasse 47 degrés :
   
    ![Ajouter une règle d’appareil][img-adddevicerule]
4. Cliquez sur **Enregistrer et afficher les règles** pour enregistrer vos modifications.
5. Cliquez sur **Commandes** dans le volet Détails du nouvel appareil.
   
   ![Ajouter une règle d’appareil][img-adddevicerule2]
6. Sélectionnez **ChangeSetPointTemp** dans la liste de commandes et définissez **SetPointTemp** sur 45. Cliquez ensuite sur **Envoyer la commande**:
   
   ![Ajouter une règle d’appareil][img-adddevicerule3]
7. Retournez au tableau de bord de la solution. Rapidement s’affiche une nouvelle entrée dans le volet **Historique des alarmes** , lorsque la température signalée par votre nouvel appareil dépasse le seuil de 47 degrés :
   
   ![Ajouter une règle d’appareil][img-adddevicerule4]
8. Vous pouvez consulter et modifier toutes les règles dans le tableau de bord **Règles** :
   
    ![Répertorier les règles d’appareil][img-rules]
9. Vous pouvez consulter et modifier toutes les mesures qui peuvent être prises en réponse à une règle sur le tableau de bord **Actions** :
   
    ![Répertorier les actions d’appareil][img-actions]

> [!NOTE]
> Il est possible de définir des actions pouvant envoyer un message électronique ou un SMS en réponse à une règle ou s’intégrer à un système métier par le biais d’une [application logique][lnk-logic-apps]. Pour plus d’informations consultez [Connecter Logic App à la solution préconfigurée de surveillance à distance IoT Suite][lnk-logicapptutorial].
> 
> 

## <a name="other-features"></a>Autres fonctionnalités
Vous pouvez utiliser le portail de solutions pour rechercher des appareils ayant des caractéristiques spécifiques (par exemple, un numéro de modèle) :

![Rechercher un appareil][img-search]

Vous pouvez désactiver un appareil puis le supprimer :

![Désactiver et supprimer un appareil][img-disable]

## <a name="behind-the-scenes"></a>Dans les coulisses
Lorsque vous déployez une solution préconfigurée, le processus de déploiement crée plusieurs ressources dans l’abonnement Azure sélectionné. Vous pouvez afficher ces ressources dans le [portail][lnk-portal] Azure. Le processus de déploiement crée un **groupe de ressources** avec un nom basé sur celui que vous avez choisi pour votre solution préconfigurée :

![Solution préconfigurée dans le portail Azure][img-portal]

Vous pouvez afficher les paramètres de chaque ressource en la sélectionnant dans la liste des ressources dans le groupe de ressources.

Vous pouvez également afficher le code source pour la solution préconfigurée. Le code source de la solution préconfigurée de surveillance à distance se trouve dans le référentiel GitHub [azure-iot-remote-monitoring][lnk-rmgithub] :

* Le dossier **DeviceAdministration** contient le code source pour le tableau de bord.
* Le dossier **Simulator** contient le code source pour l’appareil simulé.
* Le dossier **EventProcessor** contient le code source pour le processus principal qui gère les données de télémétrie entrantes.

Lorsque vous avez terminé, vous pouvez supprimer la solution préconfigurée à partir de votre abonnement Azure sur le site [azureiotsuite.com][lnk-azureiotsuite]. Ce site vous permet de supprimer facilement toutes les ressources qui ont été configurées lors de la création de la solution préconfigurée.

> [!NOTE]
> Pour vous assurer que vous supprimez tout ce qui concerne la solution préconfigurée, supprimez cette dernière sur le site [azureiotsuite.com][lnk-azureiotsuite] ; ne supprimez pas le groupe de ressources dans le portail.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
À présent que vous avez déployé une solution préconfigurée opérationnelle, vous pouvez poursuivre la prise en main d’IoT Suite en lisant les articles suivants :

* [Présentation de la solution préconfigurée de surveillance à distance][lnk-rm-walkthrough]
* [Connexion de votre appareil à la solution préconfigurée de surveillance à distance][lnk-connect-rm]
* [Autorisations sur le site azureiotsuite.com][lnk-permissions]

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
[img-search]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_07.png
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devicemetadata]: iot-suite-what-are-preconfigured-solutions.md#device-identity-registry-and-documentdb
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md



<!--HONumber=Feb17_HO3-->



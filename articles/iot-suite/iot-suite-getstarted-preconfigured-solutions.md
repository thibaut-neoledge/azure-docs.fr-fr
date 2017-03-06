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
ms.date: 02/15/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 9e1bcba086a9f70c689a5d7d7713a8ecdc764492
ms.openlocfilehash: 8248e0a02cb0775a87f0c8130e53b98f8bcfe581
ms.lasthandoff: 02/27/2017


---
# <a name="tutorial-get-started-with-the-preconfigured-solutions"></a>Didacticiel : prise en main des solutions préconfigurées
## <a name="introduction"></a>Introduction
Les [solutions préconfigurées][lnk-preconfigured-solutions] d’Azure IoT Suite regroupent plusieurs services Azure IoT pour offrir des solutions de bout en bout permettant d’implémenter des scénarios IoT d’entreprise. La solution préconfigurée de *surveillance à distance* se connecte et surveille vos appareils. Cela vous permet d’analyser le flux de données de vos appareils et d’améliorer les résultats de l’entreprise grâce à des processus qui répondent automatiquement à ce flux de données.

Ce didacticiel montre comment configurer la solution préconfigurée de surveillance à distance. Il présente également les fonctionnalités de base de la solution préconfigurée. Vous pouvez accéder à la plupart de ces fonctionnalités à partir du tableau de bord de solution déployé avec la solution préconfigurée :

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
* Le panneau **Tâches** affiche des informations sur les tâches planifiées. Vous pouvez planifier vos propres tâches sur la page **Gestion des tâches**.

## <a name="view-the-device-list"></a>Afficher la liste des appareils
La *liste des appareils* montre tous les appareils inscrits dans la solution. Dans la liste des appareils, vous pouvez consulter et modifier les métadonnées des appareils, ajouter ou supprimer des appareils et appeler des méthodes sur les appareils.

1. Cliquez sur **Appareils** dans le menu de gauche pour accéder à la liste des appareils de cette solution.
   
   ![Liste des appareils dans le tableau de bord][img-devicelist]
2. La liste des appareils montre au départ quatre appareils simulés créés par le processus de déploiement. Vous pouvez ajouter des appareils physiques et simulés supplémentaires à la solution.
3. Vous pouvez personnaliser les informations affichées dans la liste des appareils en cliquant sur **Éditeur de colonne**. Vous pouvez ajouter et supprimer des colonnes dans lesquelles figurent des valeurs de balises et de propriétés signalées. Vous pouvez également réorganiser et renommer les colonnes :
   
   ![Éditeur de colonne dans le tableau de bord][img-columneditor]
4. Pour afficher les détails concernant un appareil, cliquez sur l’appareil dans la liste.
   
   ![Détails de l’appareil dans le tableau de bord][img-devicedetails]

Le volet **Détails de l’appareil** comprend six sections :

* Un ensemble de liens qui vous permettent de personnaliser l’icône de l’appareil, de désactiver l’appareil, d’ajouter une règle, d’appeler une méthode ou d’envoyer une commande. Pour voir une comparaison des commandes (messages appareil-à-cloud) et des méthodes (méthodes directes), consultez [Conseils pour les communications cloud-à-appareil][lnk-c2d-guidance].
* La section **Jumeau d’appareil - Balises** vous permet de modifier les valeurs des balises pour l’appareil. Vous pouvez afficher les valeurs des balises dans la liste des appareils et utiliser les valeurs des balises pour filtrer cette liste.
* La section **Jumeau d’appareil - Propriétés souhaitées** vous permet de définir des valeurs de propriétés qui doivent être envoyées à l’appareil.
* La section **Jumeau d’appareil - Propriétés signalées** affiche les valeurs de propriétés envoyées par l’appareil.
* La section **Propriétés de l’appareil** contient des informations du registre des identités, telles que l’ID de l’appareil et ses clés d’authentification.
* La section **Tâches récentes** inclut des informations sur toutes les tâches qui ont récemment ciblé cet appareil.

## <a name="customize-the-device-icon"></a>Personnaliser l’icône de l’appareil

Vous pouvez personnaliser l’icône de l’appareil qui apparaît dans la liste des appareils à partir du volet **Détails de l’appareil**, en procédant comme suit :

1. Cliquez sur l’icône représentant un crayon pour ouvrir le volet **Modifier une image** pour un appareil :
   
   ![Ouvrir l’éditeur d’image d’appareil][img-startimageedit]
2. Téléchargez une nouvelle image ou utilisez une image existante, puis cliquez sur **Enregistrer** :
   
   ![Modifier l’éditeur d’image d’appareil][img-imageedit]
3. L’image que vous avez sélectionnée s’affiche dans la colonne **Icône** de l’appareil.

> [!NOTE]
> L’image est enregistrée dans le stockage Blob. Une balise du jumeau d’appareil contient un lien vers l’image dans le stockage Blob.
> 
> 

## <a name="invoke-a-method-on-a-device"></a>Appeler une méthode sur un appareil
Dans le volet **Détails de l’appareil**, vous pouvez appeler des méthodes sur l’appareil. Lors du premier démarrage d’un appareil, ce dernier envoie des informations sur les méthodes qu’il prend en charge à la solution.

1. Cliquez sur **Méthodes** dans le volet **Détails de l’appareil** correspondant à l’appareil sélectionné :
   
   ![Méthodes d’appareil dans le tableau de bord][img-devicemethods]
2. Sélectionnez **Redémarrer** dans la liste des méthodes.
3. Cliquez sur **Appeler une méthode**.
4. L’état de l’appel de méthode est indiqué dans l’historique de la méthode.
   
   ![État de la méthode dans le tableau de bord][img-pingmethod]

La solution effectue le suivi de l’état de chaque méthode qu’elle appelle. Lorsque l’appareil exécute la méthode, une nouvelle entrée apparaît dans la table d’historique de la méthode.

Certaines méthodes lancent des tâches asynchrones sur l’appareil. Par exemple, la méthode **InitiateFirmwareUpdate** démarre une tâche asynchrone pour effectuer la mise à jour. L’appareil utilise des propriétés signalées pour indiquer l’état de la mise à jour du microprogramme à mesure qu’elle progresse.

## <a name="send-a-command-to-a-device"></a>Envoyer une commande à un appareil
Dans le volet **Détails de l’appareil**, vous pouvez envoyer des commandes à l’appareil. Lors du premier démarrage d'un appareil, ce dernier envoie des informations sur les commandes qu'il prend en charge à la solution.

1. Cliquez sur **Commandes** dans le volet **Détails de l’appareil** correspondant à l’appareil sélectionné :
   
   ![Commandes de l’appareil dans le tableau de bord][img-devicecommands]
2. Sélectionnez **PingDevice** dans la liste de commandes.
3. Cliquez sur **Envoyer la commande**.
4. Vous pouvez visualiser l’état de la commande dans l’historique des commandes.
   
   ![État de la commande dans le tableau de bord][img-pingcommand]

La solution effectue le suivi de l'état de chaque commande qu'elle envoie. Initialement, le résultat est **En attente**. Lorsque l’appareil signale qu’il a correctement exécuté la commande, le résultat prend la valeur **Réussite**.

## <a name="add-a-new-simulated-device"></a>Ajouter un nouvel appareil simulé
Lorsque vous déployez la solution préconfigurée, vous approvisionnez automatiquement quatre exemples d’appareils que vous pouvez voir dans la liste des appareils. Ces appareils sont des *simulations d’appareils* en cours d’exécution dans un Azure WebJob. Les appareils simulés vous permettent d’expérimenter plus facilement la solution préconfigurée sans avoir à déployer des appareils physiques réels. Si vous ne souhaitez pas connecter un appareil physique à la solution, consultez le didacticiel [Connexion de votre appareil à la solution préconfigurée de surveillance à distance][lnk-connect-rm].

Les étapes suivantes vous montrent comment ajouter un appareil simulé à la solution :

1. Retournez à la liste des appareils.
2. Pour ajouter un appareil, cliquez sur **+ Ajouter un appareil** dans le coin inférieur gauche.
   
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

## <a name="device-properties"></a>Propriétés de l’appareil
La solution préconfigurée de surveillance à distance utilise les [jumeaux d’appareil][lnk-device-twin] pour synchroniser les métadonnées des appareils entre les appareils et le serveur principal de solution. Un jumeau d’appareil est un document JSON stocké dans IoT Hub et dans lequel sont enregistrées les valeurs des propriétés d’un appareil. Les appareils envoient régulièrement des métadonnées au serveur principal de solution en tant que *propriétés signalées* à stocker dans le jumeau d’appareil. Le serveur principal de solution peut définir des *propriétés souhaitées* dans le jumeau d’appareil de façon à ce que les mises à jour des métadonnées soient envoyées aux appareils. Les propriétés signalées indiquent les valeurs de métadonnées les plus récentes envoyées par l’appareil. Pour plus d’informations, consultez [Registre d’identité des appareils, jumeau d’appareil et DocumentDB][lnk-devicemetadata].

> [!NOTE]
> La solution utilise également une base de données DocumentDB pour stocker les données de l’appareil relatives aux commandes et aux méthodes.
> 
> 

1. Retournez à la liste des appareils.
2. Sélectionnez votre nouvel appareil dans la **Liste des appareils**, puis cliquez sur **Modifier** pour modifier **Jumeau d’appareil - Propriétés souhaitées** :
   
   ![Modifier les propriétés souhaitées de l’appareil][img-editdevice]
3. Définissez le **nom de la propriété souhaitée** sur **Latitude** et sa valeur sur **47.639521**. Cliquez ensuite sur **Enregistrer les modifications dans le registre de l’appareil** :
   
    ![Modifier la propriété souhaitée de l’appareil][img-editdevice2]
4. Dans le volet **Détails de l’appareil**, la nouvelle valeur de latitude apparaît initialement comme propriété souhaitée, tandis que l’ancienne valeur de latitude apparaît comme propriété signalée :
   
    ![Afficher la propriété signalée][img-editdevice3]
5. Actuellement, les appareils simulés dans la solution préconfigurée traitent uniquement les propriétés souhaitées **Desired.Config.TemperatureMeanValue** et **Desired.Config.TelemetryInterval**. Un périphérique réel doit lire toutes les propriétés souhaitées à partir de IoT Hub, apporter les modifications à la configuration et déclarer les nouvelles valeurs à IoT Hub en tant que propriétés signalées.

Dans le volet **Détails de l’appareil**, vous pouvez également modifier **Jumeau d’appareil - Balises** de la même façon que vous avez modifié **Jumeau d’appareil - Propriétés souhaitées**. Toutefois, contrairement aux propriétés souhaitées, les balises ne se synchronisent pas avec l’appareil. Les balises se trouvent uniquement dans le jumeau d’appareil sur IoT Hub. Les balises sont utiles pour créer des filtres personnalisés dans la liste des appareils.

## <a name="sort-the-device-list"></a>Trier la liste des appareils

Vous pouvez trier la liste des appareils en cliquant à côté des titres de colonne. En cliquant une fois, le tri se fait par ordre croissant ; en cliquant deux fois, le tri se fait par ordre décroissant :

![Trier la liste des appareils][img-sortdevices]

## <a name="filter-the-device-list"></a>Filtrer la liste des appareils

Dans la liste des appareils, vous pouvez créer, enregistrer et recharger des filtres pour afficher une liste personnalisée des appareils connectés à votre hub. Pour créer un filtre :

1. Cliquez sur l’icône de modification de filtre au-dessus de la liste des appareils :
   
   ![Ouvrir l’éditeur de filtre][img-editfiltericon]
2. Dans l’**éditeur de filtre**, ajoutez les champs, les opérateurs et les valeurs permettant de filtrer la liste des appareils. Vous pouvez ajouter plusieurs clauses pour affiner votre filtre. Cliquez sur **Filtrer** pour appliquer le filtre :
   
   ![Créer un filtre][img-filtereditor]
3. Dans cet exemple, la liste est filtrée par fabricant et numéro de modèle :
   
   ![Liste de filtrage][img-filterelist]
4. Pour enregistrer votre filtre avec un nom personnalisé, cliquez sur l’icône **Enregistrer sous** :
   
   ![Enregistrer un filtre][img-savefilter]
5. Pour réappliquer un filtre que vous avez enregistré précédemment, cliquez sur l’icône **Ouvrir un filtre enregistré** :
   
   ![Ouvrir un filtre][img-openfilter]

Vous pouvez créer des filtres en fonction de l’id de l’appareil, de son état, des propriétés souhaitées, des propriétés signalées et des balises.

> [!NOTE]
> Dans l’**éditeur de filtre**, vous pouvez utiliser la **vue avancée** pour modifier directement le texte de la requête.
> 
> 

## <a name="add-a-rule-for-the-new-device"></a>Ajouter une règle pour le nouvel appareil
Il n'existe aucune règle pour le nouvel appareil que vous venez d'ajouter. Dans cette section, vous allez ajouter une règle qui déclenche une alarme lorsque la température signalée par le nouvel appareil dépasse 47 degrés. Avant de commencer, notez que l'historique de télémétrie pour le nouvel appareil sur le tableau de bord affiche que la température de l’appareil ne dépasse jamais 45 degrés.

1. Retournez à la liste des appareils.
2. Pour ajouter une règle concernant un nouvel appareil, sélectionnez ce dernier dans la **liste des appareils**, puis cliquez sur **Ajouter une règle**.
3. Créez une règle qui utilise **Température** comme champ de données et **AlarmTemp** en tant que sortie lorsque la température dépasse 47 degrés :
   
    ![Ajouter une règle d’appareil][img-adddevicerule]
4. Pour enregistrer vos modifications, cliquez sur **Enregistrer et afficher les règles**.
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

## <a name="disable-and-remove-devices"></a>Désactiver et supprimer des appareils
Vous pouvez désactiver un appareil puis le supprimer :

![Désactiver et supprimer un appareil][img-disable]

## <a name="run-jobs"></a>Exécuter des tâches
Vous pouvez planifier des tâches pour exécuter des opérations en bloc sur vos appareils. Vous créez une tâche pour une liste d’appareils. Cette liste peut contenir tous vos appareils ou il peut s’agir d’une liste filtrée que vous avez créée à l’aide des [outils de filtre](#filter-the-device-list) dans la liste des appareils. Une tâche peut appeler une méthode sur chaque appareil de la liste ou elle peut mettre à jour le jumeau de chaque appareil de la liste des appareils.

### <a name="create-a-job-to-invoke-a-method"></a>Créer une tâche pour appeler une méthode

Les étapes suivantes vous expliquent comment créer une tâche qui appelle la méthode de mise à jour du microprogramme sur chaque appareil d’une liste. La méthode est appelée uniquement sur les appareils qui la prennent en charge :

1. Utilisez les outils de filtre dans la liste des appareils pour créer une liste d’appareils qui doivent recevoir la mise à jour du microprogramme :
   
   ![Ouvrir l’éditeur de filtre][img-editfiltericon]
2. Dans votre liste filtrée, cliquez sur **Planificateur de travaux** :
   
   ![Ouvrir le planificateur de travaux][img-clickjobscheduler]
3. Dans le volet **Planifier le travail**, cliquez sur **Appeler une méthode**.
4. Sur la page **Appeler une méthode**, entrez les détails de la méthode à appeler, puis cliquez sur **Planifier** :
   
   ![Configurer la tâche de méthode][img-invokemethodjob]

La méthode **InitiateFirmwareUpdate** démarre une tâche de façon asynchrone sur l’appareil et revient immédiatement. Le processus de mise à jour du microprogramme utilise ensuite les propriétés signalées pour créer un rapport sur la mise à jour pendant son exécution.

### <a name="create-a-job-to-edit-the-device-twin"></a>Créer une tâche pour modifier le jumeau d’appareil

Les étapes suivantes vous expliquent comment créer une tâche qui modifie le jumeau de chaque appareil d’une liste :

1. Utilisez les outils de filtre dans la liste des appareils pour créer une liste d’appareils dont les jumeaux doivent être modifiés :
   
   ![Ouvrir l’éditeur de filtre][img-editfiltericon]
2. Dans votre liste filtrée, cliquez sur **Planificateur de travaux** :
   
   ![Ouvrir le planificateur de travaux][img-clickjobscheduler]
3. Dans le volet **Planifier le travail**, cliquez sur **Modifier le jumeau d’appareil**.
4. Sur la page **Modifier le jumeau d’appareil** , entrez les informations sur les **propriétés souhaitées** et les **balises** à modifier, puis cliquez sur **Planifier** :
   
   ![Configurer la tâche de méthode][img-edittwinjob]

### <a name="monitor-the-job"></a>Surveiller la tâche
Vous pouvez surveiller le statut des tâches que vous planifiez sur la page **Gestion des tâches**. Le volet **Détails de la tâche** fournit des informations relatives à la tâche sélectionnée :
   
   ![Afficher le statut de la tâche][img-jobstatus]

Vous pouvez également consulter des informations sur les tâches sur le **tableau de bord** :
   
   ![Afficher les tâches sur le tableau de bord][img-jobdashboard]


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
[img-devicemethods]: media/iot-suite-getstarted-preconfigured-solutions/devicemethods.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-pingmethod]: media/iot-suite-getstarted-preconfigured-solutions/pingmethod.png
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
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png
[img-columneditor]: media/iot-suite-getstarted-preconfigured-solutions/columneditor.png
[img-startimageedit]: media/iot-suite-getstarted-preconfigured-solutions/imagedit1.png
[img-imageedit]: media/iot-suite-getstarted-preconfigured-solutions/imagedit2.png
[img-sortdevices]: media/iot-suite-getstarted-preconfigured-solutions/sortdevices.png
[img-editfiltericon]: media/iot-suite-getstarted-preconfigured-solutions/editfiltericon.png
[img-filtereditor]: media/iot-suite-getstarted-preconfigured-solutions/filtereditor.png
[img-filterelist]: media/iot-suite-getstarted-preconfigured-solutions/filteredlist.png
[img-savefilter]: media/iot-suite-getstarted-preconfigured-solutions/savefilter.png
[img-openfilter]:  media/iot-suite-getstarted-preconfigured-solutions/openfilter.png
[img-clickjobscheduler]: media/iot-suite-getstarted-preconfigured-solutions/clickscheduler.png
[img-invokemethodjob]: media/iot-suite-getstarted-preconfigured-solutions/invokemethodjob.png
[img-edittwinjob]: media/iot-suite-getstarted-preconfigured-solutions/edittwinjob.png
[img-jobstatus]: media/iot-suite-getstarted-preconfigured-solutions/jobstatus.png
[img-jobdashboard]: media/iot-suite-getstarted-preconfigured-solutions/jobdashboard.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devicemetadata]: iot-suite-what-are-preconfigured-solutions.md#device-identity-registry-device-twin-and-documentdb
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md

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
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: e68815c2dafc596c3560ad3fcb2a7bf96d29182b
ms.lasthandoff: 03/06/2017


---
# <a name="tutorial-get-started-with-the-preconfigured-solutions"></a>Didacticiel : prise en main des solutions préconfigurées
## <a name="introduction"></a>Introduction
Les [solutions préconfigurées][lnk-preconfigured-solutions] d’Azure IoT Suite regroupent plusieurs services Azure IoT pour offrir des solutions de bout en bout permettant d’implémenter des scénarios IoT d’entreprise. La solution préconfigurée de *surveillance à distance* se connecte et surveille vos appareils. Cela vous permet d’analyser le flux de données de vos appareils et d’améliorer les résultats de l’entreprise grâce à des processus qui répondent automatiquement à ce flux de données.

Ce didacticiel montre comment configurer la solution préconfigurée de surveillance à distance. Il présente également les fonctionnalités de base de la solution préconfigurée. Vous pouvez accéder à la plupart de ces fonctionnalités à partir du *tableau de bord* de solution déployé avec la solution préconfigurée :

![Tableau de bord de solution préconfigurée de surveillance à distance][img-dashboard]

Pour suivre ce didacticiel, vous avez besoin d’un compte Azure actif.

> [!NOTE]
> Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, consultez la rubrique [Version d’évaluation gratuite d’Azure][lnk_free_trial].
> 
> 

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="scenario-overview"></a>Présentation du scénario

Lorsque vous déployez la solution préconfigurée de surveillance à distance, elle est préremplie avec les ressources qui vous permettent de parcourir un scénario courant de surveillance à distance. Dans ce scénario, plusieurs appareils connectés à la solution présentent des valeurs de température inattendues. Les sections suivantes vous montrent comment :

* identifier les appareils qui envoient des valeurs de température inattendues ;
* configurer ces appareils pour envoyer des données de télémétrie plus détaillées ;
* résoudre le problème en mettant à jour le microprogramme sur ces appareils ;
* vérifier que votre action a résolu le problème.

Une fonctionnalité clé de ce scénario est que vous pouvez effectuer toutes ces actions à distance à partir du tableau de bord de solution. Vous n’avez pas besoin d’un accès physique aux appareils.

## <a name="view-the-solution-dashboard"></a>Afficher le tableau de bord de solution

Grâce au tableau de bord de solution, vous pouvez gérer la solution déployée. Par exemple, vous pouvez afficher les données de télémétrie, ajouter des appareils et configurer des règles.

1. Une fois que l’approvisionnement est terminé et que la vignette de votre solution préconfigurée indique **Prêt**, choisissez **Lancer** pour ouvrir le portail de votre solution de surveillance à distance dans un nouvel onglet.

    ![Lancer la solution préconfigurée][img-launch-solution]

1. Par défaut, le portail de solution affiche le *tableau de bord*. Vous pouvez accéder à d’autres zones du portail de solution à l’aide du menu sur le côté gauche de la page.

    ![Tableau de bord de solution préconfigurée de surveillance à distance][img-menu]

Le tableau de bord affiche les informations suivantes :

* Une carte qui affiche l’emplacement de chaque appareil connecté à la solution. Lors de la première exécution de la solution, 25 appareils sont simulés. Les appareils simulés sont implémentés en tant qu’Azure WebJobs, et la solution utilise l'API Bing Maps pour tracer les informations sur la carte. Consultez le [FAQ] [ lnk-faq] pour savoir comment rendre le mappage dynamique.
* Un panneau **Historique de télémétrie** qui trace la télémétrie de l’humidité et de la température d’un appareil sélectionné en temps quasi-réel et affiche les données d’agrégation telles que l’humidité maximale, minimale et moyenne.
* Un panneau **Historique des alertes** qui affiche les alarmes récentes lorsqu’une valeur de télémétrie a dépassé un seuil défini. Vous pouvez définir vos propres alarmes en plus des exemples créés par la solution préconfigurée.
* Un panneau **Tâches** qui affiche des informations sur les travaux planifiés. Vous pouvez planifier vos propres tâches sur la page **Gestion des tâches**.

## <a name="view-alarms"></a>Afficher les alarmes

Le panneau Historique des alarmes montre que cinq appareils signalent des valeurs de télémétrie plus élevées que celles attendues.

![TODO Historique des alarmes dans le tableau de bord de solution][img-alarms]

> [!NOTE]
> Ces alarmes sont générées par une règle qui est incluse dans la solution préconfigurée. Cette règle génère une alerte lorsque la valeur de température envoyée par un appareil dépasse 60. Vous pouvez définir vos propres règles et actions en choisissant [Règles](#add-a-rule) et [Actions](#add-an-action) dans le menu de gauche.

## <a name="view-devices"></a>Afficher les appareils

La liste des *appareils* montre tous les appareils inscrits dans la solution. Dans la liste des appareils, vous pouvez consulter et modifier les métadonnées des appareils, ajouter ou supprimer des appareils et appeler des méthodes sur les appareils. Vous pouvez filtrer et trier la liste des appareils dans la liste. Vous pouvez également personnaliser les colonnes affichées dans la liste des appareils.

1. Choisissez **Appareils** pour afficher la liste des appareils pour cette solution.

   ![Affichage de la liste des appareils dans le portail de solution][img-devicelist]

1. La liste des appareils montre au départ 25 appareils simulés créés par le processus de déploiement. Vous pouvez ajouter des appareils physiques et simulés supplémentaires à la solution.

1. Pour afficher les détails d’un appareil, choisissez un appareil dans la liste.

   ![Affichage des détails de l’appareil dans le portail de solution][img-devicedetails]

Le volet **Détails de l’appareil** comprend six sections :

* Un ensemble de liens qui vous permettent de personnaliser l’icône de l’appareil, de désactiver l’appareil, d’ajouter une règle, d’appeler une méthode ou d’envoyer une commande. Pour voir une comparaison des commandes (messages appareil-à-cloud) et des méthodes (méthodes directes), consultez [Conseils pour les communications cloud-à-appareil][lnk-c2d-guidance].
* La section **Jumeau d’appareil - Balises** vous permet de modifier les valeurs des balises pour l’appareil. Vous pouvez afficher les valeurs des balises dans la liste des appareils et utiliser les valeurs des balises pour filtrer cette liste.
* La section **Jumeau d’appareil - Propriétés souhaitées** vous permet de définir des valeurs de propriétés qui doivent être envoyées à l’appareil.
* La section **Jumeau d’appareil - Propriétés signalées** affiche les valeurs de propriétés envoyées par l’appareil.
* La section **Propriétés de l’appareil** contient des informations du registre des identités, telles que l’ID de l’appareil et ses clés d’authentification.
* La section **Tâches récentes** inclut des informations sur toutes les tâches qui ont récemment ciblé cet appareil.

## <a name="filter-the-device-list"></a>Filtrer la liste des appareils

Vous pouvez utiliser un filtre pour afficher uniquement les appareils qui envoient des valeurs de température inattendues. La solution préconfigurée de surveillance à distance inclut le filtre **Appareils défectueux** pour afficher les appareils ayant une valeur de température moyenne supérieure à 60. Vous pouvez également [créer vos propres filtres](#add-a-filter).

1. Choisissez **Ouvrir un filtre enregistré** pour afficher la liste des filtres disponibles. Puis choisissez **Appareils défectueux** pour appliquer le filtre :

    ![Affichage de la liste des filtres][img-unhealthy-filter]

1. La liste des appareils indique à présent uniquement les appareils ayant une valeur de température moyenne supérieure à 60.

    ![Affichage de la liste des appareils filtrée montrant les appareils défectueux][img-filtered-unhealthy-list]

## <a name="update-desired-properties"></a>Mettre à jour les propriétés souhaitées

Vous avez désormais identifié un ensemble d’appareils nécessitant une correction. Toutefois, vous décidez que la fréquence de données de 15 secondes n’est pas suffisante pour un diagnostic clair du problème. La modification de la fréquence de télémétrie à cinq secondes pour vous fournir plus de données permet de mieux diagnostiquer le problème. Vous pouvez transmettre cette modification de configuration aux appareils distants à partir du portail de la solution. Vous pouvez effectuer cette modification une fois, évaluer l’impact et ensuite agir en fonction des résultats.

Procédez comme suit pour exécuter un travail qui modifie la propriété souhaitée **TelemetryInterval** pour les appareils affectés. Lorsque les appareils reçoivent la nouvelle valeur de propriété **TelemetryInterval**, ils modifient leur configuration pour envoyer la télémétrie toutes les 5 secondes au lieu de 15 :

1. Pendant que vous visualisez la liste des appareils défectueux dans la liste des appareils, choisissez **Planificateur de travaux**, puis **Modifier le jumeau d’appareil**.

1. Appelez le travail **Modification de l’intervalle de télémétrie**.

1. Modifiez la valeur du nom de la **propriété souhaitée** **desired.Config.TelemetryInterval** à cinq secondes.

1. Choisissez **Planification**.

    ![Modification de la propriété TelemetryInterval à cinq secondes][img-change-interval]

1. Vous pouvez surveiller la progression du travail sur la page **Gestion des tâches** du portail.

> [!NOTE]
> Si vous souhaitez modifier une valeur de propriété souhaitée pour un appareil en particulier, utilisez la section **Propriétés souhaitées** dans le panneau **Détails de l’appareil** au lieu d’exécuter un travail.

Ce travail définit la valeur de la propriété souhaitée **TelemetryInterval** dans le jumeau d’appareil pour tous les appareils sélectionnés par le filtre. Les appareils extraient cette valeur du jumeau d’appareil et mettent à jour leur comportement. Lorsqu’un appareil récupère et traite une propriété souhaitée à partir d’un jumeau d’appareil, il définit la propriété de la valeur signalée correspondante.

## <a name="invoke-methods"></a>Appeler des méthodes

Pendant l’exécution du travail, vous remarquez dans la liste des appareils défectueux que tous ces appareils possèdent une version ancienne de microprogramme (antérieur à la version 1.6).

![Affichage de la version du microprogramme signalée pour les appareils défectueux][img-old-firmware]

Cette version du microprogramme peut être la cause de valeurs de température inattendues, car vous savez que d’autres appareils sains ont récemment été mis à jour vers la version 2.0. Vous pouvez utiliser le filtre **Appareils avec ancien microprogramme** pour identifier tous les appareils dotés d’anciennes versions du microprogramme. À partir du portail, vous pouvez ensuite mettre à jour à distance tous les appareils exécutant toujours d’anciennes versions du microprogramme :

1. Choisissez **Ouvrir un filtre enregistré** pour afficher la liste des filtres disponibles. Puis choisissez **Appareils avec ancien microprogramme** pour appliquer le filtre :

    ![Affichage de la liste des filtres][img-old-filter]

1. La liste des appareils indique à présent uniquement les appareils avec d’anciennes versions du microprogramme. Cette liste inclut les cinq appareils identifiés par le filtre **Appareils défectueux** et trois appareils supplémentaires :

    ![Affichage de la liste des appareils filtrée montrant les anciens appareils][img-filtered-old-list]

1. Choisissez **Planificateur de travaux**, puis **Appeler une méthode**.

1. Définissez le **Nom du travail** sur **Mise à jour du microprogramme vers la version 2.0**.

1. Choisissez **InitiateFirmwareUpdate** comme **méthode**.

1. Définissez le paramètre **FwPackageUri** sur **https://iotrmassets.blob.core.windows.net/firmwares/FW20.bin**.

1. Choisissez **Planification**. La valeur par défaut pour le travail est une exécution immédiate.

    ![Création de travail pour mettre à jour le microprogramme des appareils sélectionnés][img-method-update]

> [!NOTE]
> Si vous souhaitez appeler une méthode sur un appareil en particulier, choisissez **Méthodes** dans le panneau **Détails de l’appareil** au lieu d’exécuter un travail.

Ce travail appelle la méthode directe **InitiateFirmwareUpdate** sur tous les appareils sélectionnés par le filtre. Les appareils répondent immédiatement à IoT Hub puis lancent le processus de mise à jour du microprogramme de façon asynchrone. Les appareils fournissent des informations d’état sur le processus de mise à jour du microprogramme via les valeurs de propriété signalées, comme indiqué dans les captures d’écran suivantes. Choisissez l’icône **Actualiser** pour mettre à jour les informations dans les listes d’appareils et de travaux :

![Liste de travaux affichant la liste de mises à jour de microprogramme en cours d’exécution][img-update-1]
![Liste des appareils affichant l’état de mise à jour du microprogramme][img-update-2]
![Liste de travaux montrant la liste de mise à jour de microprogramme terminée][img-update-3]

> [!NOTE]
> Dans un environnement de production, vous pouvez planifier des travaux à exécuter pendant une fenêtre de maintenance désignée.

## <a name="scenario-review"></a>Analyse du scénario

Dans ce scénario, vous avez identifié un problème potentiel avec certains de vos appareils à distance à l’aide de l’historique des alarmes sur le tableau de bord et d’un filtre. Vous avez ensuite utilisé le filtre et un travail pour configurer à distance les appareils pour fournir plus d’informations pour vous aider à diagnostiquer le problème. Enfin, vous avez utilisé un filtre et un travail pour planifier la maintenance sur les appareils affectés. Si vous retournez au tableau de bord, vous pouvez vérifier qu’il ne reste aucune alarme provenant des appareils de votre solution. Vous pouvez utiliser un filtre pour vérifier que le microprogramme est à jour sur tous les appareils de votre solution et qu’il ne reste aucun appareil défectueux :

![Filtre indiquant que tous les appareils disposent d’un microprogramme à jour][img-updated]

![Filtre indiquant que tous les appareils sont sains][img-healthy]

## <a name="other-features"></a>Autres fonctionnalités

Les sections suivantes décrivent quelques fonctionnalités supplémentaires de la solution préconfigurée de surveillance à distance qui ne sont pas décrites dans le cadre du scénario précédent.

### <a name="customize-columns"></a>Personnaliser les colonnes

Vous pouvez personnaliser les informations affichées dans la liste des appareils en choisissant **Éditeur de colonne**. Vous pouvez ajouter et supprimer des colonnes dans lesquelles figurent des valeurs de balises et de propriétés signalées. Vous pouvez également réorganiser et renommer les colonnes :

   ![Icône de l’éditeur de colonne dans la liste des appareils][img-columneditor]

### <a name="customize-the-device-icon"></a>Personnaliser l’icône de l’appareil

Vous pouvez personnaliser l’icône de l’appareil qui apparaît dans la liste des appareils à partir du volet **Détails de l’appareil**, en procédant comme suit :

1. Choisissez l’icône représentant un crayon pour ouvrir le volet **Modifier une image** pour un appareil :

   ![Ouvrir l’éditeur d’image d’appareil][img-startimageedit]

1. Téléchargez une nouvelle image ou utilisez une image existante, puis choisissez **Enregistrer** :

   ![Modifier l’éditeur d’image d’appareil][img-imageedit]

1. L’image que vous avez sélectionnée s’affiche dans la colonne **Icône** de l’appareil.

> [!NOTE]
> L’image est enregistrée dans le stockage Blob. Une balise du jumeau d’appareil contient un lien vers l’image dans le stockage Blob.

### <a name="add-a-device"></a>Ajout d’un appareil

Lorsque vous déployez la solution préconfigurée, vous approvisionnez automatiquement 25 exemples d’appareils que vous pouvez voir dans la liste des appareils. Ces appareils sont des *simulations d’appareils* en cours d’exécution dans un Azure WebJob. Les appareils simulés vous permettent d’expérimenter plus facilement la solution préconfigurée sans avoir à déployer des appareils physiques réels. Si vous ne souhaitez pas connecter un appareil physique à la solution, consultez le didacticiel [Connexion de votre appareil à la solution préconfigurée de surveillance à distance][lnk-connect-rm].

Les étapes suivantes vous montrent comment ajouter un appareil simulé à la solution :

1. Retournez à la liste des appareils.

1. Pour ajouter un appareil, choisissez **+ Ajouter un appareil** dans le coin inférieur gauche.

   ![Ajouter un appareil à la solution préconfigurée][img-adddevice]

1. Choisissez **Ajouter un nouveau** sur la vignette **Appareil simulé**.

   ![Définir les détails du nouvel appareil dans le tableau de bord][img-addnew]

   Outre la création d’un appareil simulé, vous pouvez également ajouter un appareil physique si vous choisissez de créer un **appareil personnalisé**. Pour plus d’informations sur la connexion d’appareils physiques à la solution, consultez [Connexion de votre appareil à la solution préconfigurée de surveillance à distance][lnk-connect-rm].

1. Sélectionnez **Me laisser définir mon propre ID d’appareil** et ajoutez un nom unique d’ID d’appareil, par exemple **monappareil_01**.

1. Cliquez sur **Créer**.

   ![Enregistrer un nouvel appareil][img-definedevice]

1. À l’étape 3 de la procédure **Ajouter un appareil simulé**, choisissez **Terminé** pour revenir à la liste des appareils.

1. Vous pouvez vérifier que votre appareil est **En cours d’exécution** dans la liste des appareils.

    ![Afficher le nouvel appareil dans la liste des appareils][img-runningnew]

1. Vous pouvez également afficher les données de télémétrie provenant de votre nouvel appareil sur le tableau de bord :

    ![Afficher la télémétrie du nouvel appareil][img-runningnew-2]

### <a name="disable-and-delete-a-device"></a>Désactiver et supprimer un appareil

Vous pouvez désactiver un appareil puis le supprimer :

![Désactiver et supprimer un appareil][img-disable]

### <a name="add-a-rule"></a>Ajouter une règle

Il n'existe aucune règle pour le nouvel appareil que vous venez d'ajouter. Dans cette section, vous allez ajouter une règle qui déclenche une alarme lorsque la température signalée par le nouvel appareil dépasse 47 degrés. Avant de commencer, notez que l'historique de télémétrie pour le nouvel appareil sur le tableau de bord affiche que la température de l’appareil ne dépasse jamais 45 degrés.

1. Retournez à la liste des appareils.

1. Pour ajouter une règle concernant un nouvel appareil, sélectionnez ce dernier dans la **liste des appareils**, puis choisissez **Ajouter une règle**.

1. Créez une règle qui utilise **Température** comme champ de données et **AlarmTemp** en tant que sortie lorsque la température dépasse 47 degrés :

    ![Ajouter une règle d’appareil][img-adddevicerule]

1. Pour enregistrer vos modifications, choisissez **Enregistrer et afficher les règles**.

1. Choisissez **Commandes** dans le volet Détails du nouvel appareil.

    ![Ajouter une règle d’appareil][img-adddevicerule2]

1. Sélectionnez **ChangeSetPointTemp** dans la liste de commandes et définissez **SetPointTemp** sur 45. Puis choisissez **Envoyer la commande** :

    ![Ajouter une règle d’appareil][img-adddevicerule3]

1. Revenez au tableau de bord. Rapidement s’affiche une nouvelle entrée dans le volet **Historique des alarmes** , lorsque la température signalée par votre nouvel appareil dépasse le seuil de 47 degrés :

    ![Ajouter une règle d’appareil][img-adddevicerule4]

1. Vous pouvez consulter et modifier toutes les règles dans le tableau de bord **Règles** :

    ![Répertorier les règles d’appareil][img-rules]

1. Vous pouvez consulter et modifier toutes les mesures qui peuvent être prises en réponse à une règle sur le tableau de bord **Actions** :

    ![Répertorier les actions d’appareil][img-actions]

> [!NOTE]
> Il est possible de définir des actions pouvant envoyer un message électronique ou un SMS en réponse à une règle ou s’intégrer à un système métier par le biais d’une [application logique][lnk-logic-apps]. Pour plus d’informations consultez [Connecter Logic App à la solution préconfigurée de surveillance à distance IoT Suite][lnk-logicapptutorial].

### <a name="manage-filters"></a>Gérer les filtres

Dans la liste des appareils, vous pouvez créer, enregistrer et recharger des filtres pour afficher une liste personnalisée des appareils connectés à votre hub. Pour créer un filtre :

1. Choisissez l’icône de modification de filtre au-dessus de la liste des appareils :

    ![Ouvrir l’éditeur de filtre][img-editfiltericon]

1. Dans l’**éditeur de filtre**, ajoutez les champs, les opérateurs et les valeurs permettant de filtrer la liste des appareils. Vous pouvez ajouter plusieurs clauses pour affiner votre filtre. Choisissez **Filtrer** pour appliquer le filtre :

    ![Créer un filtre][img-filtereditor]

1. Dans cet exemple, la liste est filtrée par fabricant et numéro de modèle :

    ![Liste de filtrage][img-filterelist]

1. Pour enregistrer votre filtre avec un nom personnalisé, choisissez l’icône **Enregistrer sous** :

    ![Enregistrer un filtre][img-savefilter]

1. Pour réappliquer un filtre que vous avez enregistré précédemment, choisissez l’icône **Ouvrir un filtre enregistré** :

    ![Ouvrir un filtre][img-openfilter]

Vous pouvez créer des filtres en fonction de l’id de l’appareil, de son état, des propriétés souhaitées, des propriétés signalées et des balises. Vous ajoutez vos propres balises personnalisées à un appareil dans la section **Balises** du panneau **Détails de l’appareil** ou exécutez un travail pour mettre à jour les balises sur plusieurs appareils.

> [!NOTE]
> Dans l’**éditeur de filtre**, vous pouvez utiliser la **vue avancée** pour modifier directement le texte de la requête.

### <a name="commands"></a>Commandes

Dans le volet **Détails de l’appareil**, vous pouvez envoyer des commandes à l’appareil. Lors du premier démarrage d'un appareil, ce dernier envoie des informations sur les commandes qu'il prend en charge à la solution. Pour une explication des différences entre les commandes et les méthodes, consultez [Options cloud vers appareil Azure IoT Hub][lnk-c2d-guidance].

1. Choisissez **Commandes** dans le panneau **Détails de l’appareil** correspondant à l’appareil sélectionné :

   ![Commandes de l’appareil dans le tableau de bord][img-devicecommands]

1. Sélectionnez **PingDevice** dans la liste de commandes.

1. Choisissez **Envoyer la commande**.

1. Vous pouvez visualiser l’état de la commande dans l’historique des commandes.

   ![État de la commande dans le tableau de bord][img-pingcommand]

La solution effectue le suivi de l'état de chaque commande qu'elle envoie. Initialement, le résultat est **En attente**. Lorsque l’appareil signale qu’il a correctement exécuté la commande, le résultat prend la valeur **Réussite**.

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

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez déployé une solution préconfigurée opérationnelle, vous pouvez poursuivre la prise en main d’IoT Suite en lisant les articles suivants :

* [Présentation de la solution préconfigurée de surveillance à distance][lnk-rm-walkthrough]
* [Connexion de votre appareil à la solution préconfigurée de surveillance à distance][lnk-connect-rm]
* [Autorisations sur le site azureiotsuite.com][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-menu]: media/iot-suite-getstarted-preconfigured-solutions/menu.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-alarms]: media/iot-suite-getstarted-preconfigured-solutions/alarms.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
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
[img-editfiltericon]: media/iot-suite-getstarted-preconfigured-solutions/editfiltericon.png
[img-filtereditor]: media/iot-suite-getstarted-preconfigured-solutions/filtereditor.png
[img-filterelist]: media/iot-suite-getstarted-preconfigured-solutions/filteredlist.png
[img-savefilter]: media/iot-suite-getstarted-preconfigured-solutions/savefilter.png
[img-openfilter]:  media/iot-suite-getstarted-preconfigured-solutions/openfilter.png
[img-unhealthy-filter]: media/iot-suite-getstarted-preconfigured-solutions/unhealthyfilter.png
[img-filtered-unhealthy-list]: media/iot-suite-getstarted-preconfigured-solutions/unhealthylist.png
[img-change-interval]: media/iot-suite-getstarted-preconfigured-solutions/changeinterval.png
[img-old-firmware]: media/iot-suite-getstarted-preconfigured-solutions/noticeold.png
[img-old-filter]: media/iot-suite-getstarted-preconfigured-solutions/oldfilter.png
[img-filtered-old-list]: media/iot-suite-getstarted-preconfigured-solutions/oldlist.png
[img-method-update]: media/iot-suite-getstarted-preconfigured-solutions/methodupdate.png
[img-update-1]: media/iot-suite-getstarted-preconfigured-solutions/jobupdate1.png
[img-update-2]: media/iot-suite-getstarted-preconfigured-solutions/jobupdate2.png
[img-update-3]: media/iot-suite-getstarted-preconfigured-solutions/jobupdate3.png
[img-updated]: media/iot-suite-getstarted-preconfigured-solutions/updated.png
[img-healthy]: media/iot-suite-getstarted-preconfigured-solutions/healthy.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-faq]: iot-suite-faq.md

<properties 
   pageTitle="Afficher et gérer les tâches StorSimple Virtual Array | Microsoft Azure"
   description="Décrit la page Tâches du service StorSimple Manager et explique comment l’utiliser pour effectuer le suivi des tâches récentes et actuelles pour le StorSimple Virtual Array."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="06/07/2016"
   ms.author="alkohli" />

# Utiliser le service StorSimple Manager pour afficher les tâches liées au StorSimple Virtual Array

## Vue d'ensemble

La page **Tâches** est un portail centralisé unique qui permet de consulter et de gérer les tâches qui sont lancées sur les Virtual Arrays (également appelés appareils virtuels locaux) connectés à votre service StorSimple Manager. Vous pouvez consulter les tâches en cours d’exécution, terminées et en échec pour plusieurs appareils virtuels. Les résultats sont présentés sous forme de tableau.

![Page Tâches](./media/storsimple-ova-manage-jobs/ovajobs1.png)

Vous pouvez rechercher rapidement les tâches qui vous intéressent en filtrant sur les champs, à savoir :

- **État** : vous pouvez rechercher la totalité des tâches ou celles en cours d’exécution, terminées ou en échec.
- **De et À** : les tâches peuvent être filtrées selon la date et l'heure.
- **Type** : le type de tâche peut concerner toutes les tâches, ou les tâches de sauvegarde, de restauration, de basculement, de téléchargement de mises à jour ou d’installation de mises à jour.
- **Appareils** : les tâches sont initiées sur un appareil spécifique connecté à votre service. Les tâches filtrées sont ensuite affichées sous forme de tableau sur la base des attributs suivants :

    - **Type** : le type de tâche peut concerner toutes les tâches, ou les tâches de sauvegarde, de restauration, de basculement, de téléchargement de mises à jour ou d’installation de mises à jour.

    - **État** : peut concerner la totalité des tâches ou celles en cours d’exécution, terminées ou en échec.

    - **Entité** : les tâches peuvent être associées à un volume, un partage ou un appareil.

    - **Appareil** : nom de l'appareil sur lequel la tâche a été lancée.

    - **Démarré le** : heure à laquelle la tâche a été lancée.

    - **Progression** : pourcentage d'achèvement d'une tâche en cours d'exécution. Pour une tâche terminée, le pourcentage doit toujours être de 100 %.

La liste des tâches est actualisée toutes les 30 secondes.

## Affichage des détails d’une tâche

Pour afficher les détails d’une tâche, procédez comme suit.

#### Pour afficher les détails d’une tâche

1. Dans la page **Tâches**, affichez la ou les tâches qui vous intéressent en exécutant une requête avec les filtres appropriés. Vous pouvez rechercher des tâches terminées ou en cours d’exécution.

2. Sélectionnez une tâche dans la liste des tâches sous forme de tableau.

3. En bas de la page, cliquez sur **Détails**.

4. La boîte de dialogue **Détails** indique l’état, les détails et les statistiques horaires. La figure suivante illustre la boîte de dialogue **Détails du travail Sauvegarde**.
 
    ![Page Détails de la tâche](./media/storsimple-ova-manage-jobs/ovajobs2.png)

#### Échec des tâches lorsque la machine virtuelle est en pause dans l'hyperviseur

Lorsqu'une tâche est en cours sur votre StorSimple Virtual Array et que le périphérique (machine virtuelle configurée dans l'hyperviseur) est en pause pendant plus de 15 minutes, la tâche échoue. Ceci en raison du fait que l’heure de votre StorSimple Virtual Array est désynchronisée avec l'heure de Microsoft Azure. Voici un exemple d'échec d'une tâche de restauration sur la capture d'écran suivante.

![Échec d’une tâche de restauration](./media/storsimple-ova-manage-jobs/restorejobfailure.png)

Ces échecs s’appliquent aux tâches de sauvegarde, de restauration, de mise à jour et de basculement. Si votre machine virtuelle est configurée dans Hyper-V, elle finira par synchroniser son heure avec celle de votre hyperviseur. Une fois que ce sera le cas, vous pourrez redémarrer votre tâche.

## Étapes suivantes

[Découvrez comment utiliser l’interface utilisateur web locale pour gérer votre StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0622_2016-->
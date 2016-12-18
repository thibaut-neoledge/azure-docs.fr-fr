---
title: Solution de suivi des modifications dans Log Analytics | Microsoft Docs
description: "Vous pouvez utiliser la solution de suivi des modifications de configuration dans Log Analytics pour vous aider à identifier facilement les logiciels et les modifications des services Windows qui se produisent dans votre environnement : l’identification de ces modifications de configuration peut vous aider à identifier les problèmes opérationnels."
services: operations-management-suite
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: f8040d5d-3c89-4f0c-8520-751c00251cb7
ms.service: operations-management-suite
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 56faeccbb25d2f0e3dbe6b104b5e9bc95a06ca36


---
# <a name="change-tracking-solution-in-log-analytics"></a>Solution de suivi des modifications dans Log Analytics
Cet article vous aidera à utiliser la solution de suivi des modifications de configuration de Log Analytics pour identifier facilement les modifications dans votre environnement. La solution suit les modifications apportées aux logiciels, services Windows, démons Linux et packages Linux. Identifier les modifications de configuration peut vous aider à identifier les problèmes opérationnels. Vous pouvez également configurer la solution pour suivre les modifications apportées à des fichiers Windows spécifiques.

Vous installez la solution pour mettre à jour le type d’agent que vous avez installé. Les modifications apportées à des logiciels installés, aux services Windows et aux démons Linux sur les serveurs analysés sont lues, puis les données sont envoyées au service cloud Log Analytics pour traitement. La logique est appliquée aux données reçues et le service cloud enregistre les données. Lorsque des modifications sont détectées, les serveurs comprenant des modifications sont affichés dans le tableau de bord de suivi des modifications. En utilisant les informations du tableau de bord de suivi des modifications, vous pouvez facilement voir les modifications apportées à votre infrastructure de serveur.

## <a name="installing-and-configuring-the-solution"></a>Installation et configuration de la solution
Utilisez les informations suivantes pour installer et configurer la solution.

* Vous devez disposer d’un agent [Windows](log-analytics-windows-agents.md), d'un [Operations Manager](log-analytics-om-agents.md) ou d'un agent [Linux](log-analytics-linux-agents.md) sur chaque ordinateur où vous souhaitez analyser les modifications.
* Ajoutez la solution de suivi des modifications à votre espace de travail OMS en suivant la procédure décrite dans [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md).  Aucune configuration supplémentaire n’est requise.

### <a name="configure-windows-files-to-track"></a>Configuration des fichiers Windows à suivre
Utilisez les étapes suivantes pour configurer les fichiers à suivre sur les ordinateurs Windows.

1. Dans le portail OMS, cliquez sur **Paramètres** (symbole d'engrenage).
2. Sur la page **Paramètres** cliquez sur **Données**, puis sur **Suivi des fichiers Windows**.
3. Sous Suivi des modifications des fichiers Windows, tapez le chemin d’accès complet, y compris le nom du fichier que vous souhaitez suivre, puis cliquez sur le symbole **Ajouter**. Par exemple : C:\Program Files (x86)\Internet Explorer\iexplore.exe ou C:\Windows\System32\drivers\etc\hosts.
4. Cliquez sur **Save**.  
   ![Suivi des modifications des fichiers Windows](./media/log-analytics-change-tracking/windows-file-change-tracking.png)

### <a name="limitations"></a>Limitations
La solution de suivi des modifications ne prend pas en charge les éléments suivants :

* dossiers (répertoires)
* récursivité
* caractères génériques
* variables de chemin d'accès
* systèmes de fichiers réseau

Autres limitations :

* La colonne **Taille maximale des fichiers** et ses valeurs ne sont pas utilisées dans l’implémentation actuelle.
* Si vous collectez plus de 2500 fichiers dans le cycle de collecte de 30 minutes, les performances de la solution peuvent être dégradées.
* Lorsque le trafic réseau est élevé, l'affichage des enregistrements de modifications peut prendre jusqu'à six heures.
* Si vous modifiez la configuration lorsqu’un ordinateur est arrêté, l’ordinateur risque de publier des modifications de fichiers appartenant à la configuration précédente.

## <a name="change-tracking-data-collection-details"></a>Détails de la collecte de données de suivi des modifications
Le suivi des modifications collecte des métadonnées d’inventaire logiciel et de service Windows à l’aide des agents que vous avez activés.

Le tableau suivant présente les méthodes de collecte des données et d’autres informations sur le mode de collecte en vue du suivi des modifications.

| plateforme | Agent direct | Agent SCOM | Agent Linux | Azure Storage | SCOM requis ? | Données de l’agent SCOM envoyées via un groupe d’administration | fréquence de collecte |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Windows et Linux |![Oui](./media/log-analytics-change-tracking/oms-bullet-green.png) |![Oui](./media/log-analytics-change-tracking/oms-bullet-green.png) |![Oui](./media/log-analytics-change-tracking/oms-bullet-green.png) |![Non](./media/log-analytics-change-tracking/oms-bullet-red.png) |![Non](./media/log-analytics-change-tracking/oms-bullet-red.png) |![Oui](./media/log-analytics-change-tracking/oms-bullet-green.png) |Toutes les heures |

## <a name="use-change-tracking"></a>Utilisation du suivi des modifications
Une fois la solution installée, vous pouvez afficher le résumé des modifications pour vos serveurs analysés à l’aide de la vignette **Suivi des modifications** sur la page **Vue d’ensemble** d’OMS.

![image de la vignette de suivi des modifications](./media/log-analytics-change-tracking/oms-changetracking-tile.png)

Vous pouvez afficher les modifications apportées à votre infrastructure, puis étudier en détail les informations pour les catégories suivantes :

* Modifications selon le type de configuration pour les logiciels et services Windows
* Modifications logicielles des applications et mises à jour pour chaque serveur
* Nombre total de modifications logicielles pour chaque application
* Packages Linux
* Modifications de services Windows pour chaque serveur
* Modifications du démon Linux

![image du tableau de bord de suivi des modifications](./media/log-analytics-change-tracking/oms-changetracking01.png)

![image du tableau de bord de suivi des modifications](./media/log-analytics-change-tracking/oms-changetracking02.png)

### <a name="to-view-changes-for-any-change-type"></a>Affichage des modifications pour tout type de modification
1. Dans la page **Vue d’ensemble**, cliquez sur la vignette **Suivi des modifications**.
2. Sur le tableau de bord **Suivi des modifications**, passez en revue les informations résumées de l’un des panneaux de type de modification, puis cliquez sur l’une d’entre elles pour afficher des informations détaillées dans la page **Recherche de journal**.
3. Sur l’une des pages de recherche de journal, vous pouvez afficher les résultats par date, les résultats détaillés et votre historique de recherches de journaux. Vous pouvez également filtrer par facettes pour affiner les résultats.

## <a name="next-steps"></a>Étapes suivantes
* Utilisez les [recherches de journaux dans Log Analytics](log-analytics-log-searches.md) pour afficher les données détaillées du suivi des modifications.




<!--HONumber=Nov16_HO3-->



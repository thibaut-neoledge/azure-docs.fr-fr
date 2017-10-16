---
title: "Analyse d’événements Azure Service Fabric avec OMS | Microsoft Docs"
description: "Découvrez l’analyse et la visualisation d’événements à l’aide d’OMS pour la surveillance et le diagnostic de clusters Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/26/2017
ms.author: dekapur
ms.openlocfilehash: f52988f2a67f2cafe93a7e0a358c599658d068b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="event-analysis-and-visualization-with-oms"></a>Analyse et visualisation d’événements avec OMS

OMS (Operations Management Suite) est une collection de services de gestion liés à la surveillance et au diagnostic d’applications et de services hébergés dans le cloud. Pour obtenir une présentation plus détaillée d’OMS et de ce qu’il propose, lisez [Qu’est-ce qu’OMS ?](../operations-management-suite/operations-management-suite-overview.md)

## <a name="log-analytics-and-the-oms-workspace"></a>Log Analytics et l’espace de travail OMS

Log Analytics collecte des données à partir de ressources gérées, notamment un agent ou une table de stockage Azure, et les conserve dans un référentiel central. Les données peuvent ensuite être utilisées pour l’analyse, l’alerte et la visualisation, ou une nouvelle exportation. Log Analytics prend en charge les événements, les données de performances ou toute autre donnée personnalisée.

Quand OMS est configuré, vous avez accès à un *espace de travail OMS* spécifique, à partir duquel les données peuvent être interrogées ou visualisées dans des tableaux de bord.

Après réception des données par Log Analytics, OMS dispose de plusieurs *solutions de gestion* qui sont des solutions prépackagées permettant de surveiller les données entrantes, personnalisées pour plusieurs scénarios. Ces solutions incluent une solution *Service Fabric Analytics* et une solution *Conteneurs*, qui sont les deux solutions les plus appropriées pour les diagnostics et le suivi lors de l’utilisation des clusters Service Fabric. Il en existe aussi plusieurs autres qu’il est conseillé d’explorer. OMS permet également de créer des solutions personnalisées. Vous trouverez plus d’informations à ce sujet [ici](../operations-management-suite/operations-management-suite-solutions.md). Chaque solution que vous choisissez d’utiliser pour un cluster est configurée dans le même espace de travail OMS, avec Log Analytics. Les espaces de travail autorisent les tableaux de bord personnalisés et la visualisation de données, ainsi que les modifications des données que vous voulez collecter, traiter et analyser.

## <a name="setting-up-an-oms-workspace-with-the-service-fabric-solution"></a>Configuration d’un espace de travail OMS avec la solution Service Fabric

Il est recommandé d’inclure la solution Service Fabric dans votre espace de travail OMS, car elle fournit un tableau de bord utile qui montre les différents canaux de journaux entrants du niveau plateforme et application, ainsi que la possibilité d’interroger des journaux Service Fabric spécifiques. Voici à quoi ressemble une solution Service Fabric relativement simple, avec une seule application déployée sur le cluster :

![Solution SF OMS](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-solution.png)

Il existe deux façons d’approvisionner et de configurer un espace de travail OMS : via un modèle Resource Manager ou directement à partir de la Place de marché Azure. Utilisez la première quand vous déployez un cluster, et la deuxième si vous disposez déjà d’un cluster déployé avec l’option Diagnostics activée.

### <a name="deploying-oms-using-a-resource-management-template"></a>Déploiement d’OMS à l’aide du modèle Resource Management

Cette opération est effectuée pendant l’étape de création de clusters : lors du déploiement d’un cluster à l’aide d’un modèle Resource Manager, le modèle peut également créer un nouvel espace de travail OMS, lui ajouter la solution Service Fabric et le configurer pour lire des données provenant des tables de stockage appropriées.

>[!NOTE]
>Pour que cela fonctionne, l’option Diagnostics doit être activée pour que les tables de stockage Azure existent afin qu’OMS/Log Analytics puissent y lire des informations.

Vous trouverez [ici](https://azure.microsoft.com/resources/templates/service-fabric-oms/) un exemple de modèle que vous pouvez utiliser et modifier conformément à l’exigence, et qui effectue les actions mentionnées ci-dessus. Si vous souhaitez disposer de plus de choix, il existe d’autres modèles qui vous offrent des options différentes en fonction d’où vous en êtes dans le processus de configuration d’un espace de travail OMS. Ils sont disponibles dans [Modèles Service Fabric et OMS](https://azure.microsoft.com/resources/templates/?term=service+fabric+OMS).

### <a name="deploying-oms-using-through-azure-marketplace"></a>Déploiement d’OMS à l’aide de la Place de marché Azure

Si vous préférez ajouter un espace de travail OMS après avoir déployé un cluster, accédez à la Place de marché Azure et recherchez *« Service Fabric Analytics »*. Une seule ressource doit s’afficher dans la catégorie « Surveillance + gestion », comme ci-dessous :

![SF Analytics OMS dans la Place de marché](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

Si vous cliquez sur **Créer**, vous devez spécifier un espace de travail OMS. Cliquez sur **Sélectionner un espace de travail**, puis sur **Créer un espace de travail**. Renseignez les entrées obligatoires. Ici, la seule exigence est que l’abonnement pour le cluster Service Fabric et celui pour l’espace de travail OMS soient identiques. Une fois vos entrées validées, votre espace de travail OMS est déployé en quelques minutes. Pendant que le déploiement se termine, la création du panneau de solution Service Fabric reste toujours ouverte. Vérifiez que le même espace de travail s’affiche sous *Espace de travail OMS*, puis appuyez sur **Créer** en bas pour ajouter la solution Service Fabric à l’espace de travail.

## <a name="using-the-oms-agent"></a>Utilisation de l’agent OMS

Il est recommandé d’utiliser EventFlow et WAD comme solutions d’agrégation, car ils permettent d’adopter une approche plus modulable des diagnostics et du suivi. Par exemple si vous voulez changer vos sorties à partir d’EventFlow, aucune modification de votre instrumentation réelle n’est nécessaire, mais juste une simple modification de votre fichier de configuration. Toutefois, si vous décidez d’investir dans OMS et que vous souhaitez l’utiliser pour l’analyse des événements (ce ne doit pas obligatoirement être la seule plateforme que vous utilisez, mais ce sera au moins l’une des plateformes), nous vous recommandons d’explorer la configuration de l’[agent OMS](../log-analytics/log-analytics-windows-agents.md). Vous devez également utiliser l’agent OMS lors du déploiement de conteneurs sur votre cluster, comme indiqué ci-dessous.

Le processus permettant d’effectuer cette opération est relativement simple : il vous suffit d’ajouter l’agent comme extension de groupe de machines virtuelles identiques à votre modèle Resource Manager, en vérifiant qu’il est installé sur chacun de vos nœuds. Nous proposons un exemple de modèle Resource Manager qui déploie l’espace de travail OMS avec la solution Service Fabric (comme ci-dessus) et ajoute l’agent à vos nœuds pour les clusters exécutant [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) ou [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

Les avantages de cette approche sont les suivants :

* Données enrichies du côté des compteurs de performances et des indicateurs de performance.
* Données faciles à configurer collectées à partir du cluster et modifiables sans avoir à redéployer vos applications ou le cluster, car les modifications apportées aux paramètres de l’agent peuvent être effectuées à partir de l’espace de travail OMS et réinitialiseront juste l’agent automatiquement. Pour configurer l’agent OMS afin de récupérer des compteurs de performance spécifiques, accédez à la **Page d’accueil de l’espace de travail > Paramètres > Données > Compteurs de performances Windows**, puis sélectionnez les données que vous voulez collecter.
* Les données s’affichent plus rapidement que si elles devaient être stockées avant d’être récupérées par OMS/Log Analytics.
* La surveillance des conteneurs est beaucoup plus facile, car elle peut sélectionner les journaux Docker (stdout, stderror) et les statistiques (indicateurs de performance au niveau des conteneurs et des nœuds).

La principale considération ici est qu’étant donné qu’il s’agit d’un agent, il sera déployé sur votre cluster avec toutes vos applications. Par conséquent, l’impact sur les performances de vos applications sur le cluster est minimal.

## <a name="monitoring-containers"></a>Surveillance des conteneurs

Quand vous déployez des conteneurs dans un cluster Service Fabric, il est recommandé de configurer le cluster avec l’agent OMS et d’ajouter la solution Conteneurs à votre espace de travail OMS pour permettre le suivi et les diagnostics. Voici à quoi ressemble la solution Conteneurs dans un espace de travail :

![Tableau de bord OMS de base](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

L’agent permet de collecter plusieurs journaux spécifiques aux conteneurs qui peuvent être interrogés dans OMS ou utilisés pour visualiser les indicateurs de performance. Les types de journaux collectés sont :

* ContainerInventory : affiche des informations sur les images, le nom et l’emplacement du conteneur
* ContainerImageInventory : informations sur les images déployées, ID ou tailles compris
* ContainerLog : journaux d’erreurs spécifiques, journaux de docker (stdout, etc.) et autres entrées
* ContainerServiceLog : les commandes de démon docker qui ont été exécutées
* Performances : les compteurs de performances, dont l’utilisation par le conteneur du processeur, de la mémoire, du E/S de disque, du trafic réseau et de mesures personnalisées à partir des machines hôtes

Cet article décrit les étapes requises pour configurer la surveillance de conteneur pour votre cluster. Pour en savoir plus sur la solution de conteneurs d’OMS, consultez leur [documentation](../log-analytics/log-analytics-containers.md).

Pour configurer la solution Conteneurs dans votre espace de travail, vérifiez que l’agent OMS est déployé sur les nœuds de votre cluster en suivant les étapes mentionnées ci-dessus. Une fois le cluster prêt, déployez un conteneur dessus. N’oubliez pas que la première fois qu’une image de conteneur est déployée sur un cluster, plusieurs minutes peuvent être nécessaires pour télécharger l’image en fonction de sa taille.

Sur la Place de marché Azure, recherchez *Solution de monitorage des conteneurs* et créez le résultat **Solution de monitorage des conteneurs** qui devrait s’afficher, sous la catégorie Monitorage + gestion.

![Ajout de la solution Conteneurs](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

Lors de l’étape de création, un espace de travail OMS est demandé. Sélectionnez celui qui a été créé avec le déploiement ci-dessus. Cette étape ajoute une solution Conteneurs à votre espace de travail OMS, et est automatiquement détectée par l’agent d’OMS déployé par le modèle. L’agent commence à collecter des données sur les processus des conteneurs dans le cluster, et après moins de 15 minutes environ, vous devez voir la solution s’actualiser avec des données, comme dans l’image du tableau de bord ci-dessus.


## <a name="next-steps"></a>Étapes suivantes

Explorez les outils et options OMS suivants pour personnaliser un espace de travail en fonction de vos besoins :

* Pour les clusters locaux, OMS propose une passerelle (proxy de transfert HTTP) qui peut être utilisée pour envoyer des données à OMS. Pour plus d’informations à ce sujet, consultez [Connexion d’ordinateurs à OMS sans accès Internet à l’aide de la passerelle OMS](../log-analytics/log-analytics-oms-gateway.md)
* Configurez OMS pour configurer l’[alerte automatisée](../log-analytics/log-analytics-alerts.md) afin de faciliter la détection et les diagnostics
* Familiarisez-vous avec les fonctionnalités de [requêtes et recherches dans les journaux](../log-analytics/log-analytics-log-searches.md) offertes dans le cadre de Log Analytics
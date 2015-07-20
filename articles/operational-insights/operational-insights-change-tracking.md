<properties
   pageTitle="Identification des différences avec le suivi des modifications"
   description="Utilisez la solution de suivi des modifications de configuration dans Microsoft Azure Operational Insights pour vous aider à identifier facilement les modifications des logiciels et des services Windows qui se produisent dans votre environnement"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="banders" />

# Identification des différences avec le suivi des modifications

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Vous pouvez utiliser la solution de suivi des modifications de configuration dans Microsoft Azure Operational Insights pour vous aider à identifier facilement les logiciels et les modifications des services Windows qui se produisent dans votre environnement : l’identification de ces modifications de configuration peut vous aider à identifier les problèmes opérationnels.

Vous installez la solution pour mettre à jour l’agent Operations Manager et le module de configuration de base pour Operational Insights. Les modifications apportées à des logiciels installés et aux services Windows sur les serveurs analysés sont lues, puis les données sont envoyées au service Operational Insights dans le cloud pour le traitement. La logique est appliquée aux données reçues et le service cloud enregistre les données. Lorsque des modifications sont détectées, les serveurs comprenant des modifications sont affichés dans le tableau de bord de suivi des modifications. En utilisant les informations du tableau de bord de suivi des modifications, vous pouvez facilement voir les modifications apportées à votre infrastructure de serveur.

## Utilisation du suivi des modifications

Avant de pouvoir utiliser le suivi des modifications dans Operational Insights, vous devez avoir installé la solution. Pour plus d'informations sur l'installation de solutions, consultez la rubrique [Utilisation de la galerie de solutions pour ajouter ou supprimer des solutions](operational-insights-add-solution.md).

Après son installation, vous pouvez afficher le résumé des modifications pour vos serveurs analysés à l’aide de la vignette **Suivi des modifications** sur la page **Vue d’ensemble** d’Operational Insights.

![image de la vignette de suivi des modifications](./media/operational-insights-change-tracking/overview-change-track.png)

Vous pouvez afficher les modifications apportées à votre infrastructure, puis étudier en détail les informations pour les catégories suivantes :

- Modifications selon le type de configuration pour les logiciels et services Windows

- Modifications logicielles des applications et mises à jour pour chaque serveur

- Nombre total de modifications logicielles pour chaque application

- Modifications de services Windows pour chaque serveur

![image du tableau de bord de suivi des modifications](./media/operational-insights-change-tracking/gallery-changetracking-01.png) ![image du tableau de bord de suivi des modifications](./media/operational-insights-change-tracking/gallery-changetracking-02.png)

### Affichage des modifications pour tout type de modification

1. Sur la page **Vue d’ensemble**, cliquez sur la vignette **Suivi des modifications**.

2. Sur le tableau de bord **Suivi des modifications**, passez en revue les informations résumées de l’un des panneaux de type de modification, puis cliquez sur l’une d’entre elles pour afficher des informations détaillées dans la page **Recherche de journaux**.

3. Sur l’une des pages de recherche de journaux, vous pouvez afficher les résultats par date, les résultats détaillés et votre historique de recherche de journaux. Vous pouvez également filtrer par facettes pour affiner les résultats.

4. Depuis n’importe quelle page de la section Recherche de journaux, vous pouvez **Exporter** les résultats détaillés dans un fichier CSV que vous pouvez ouvrir avec Excel ou une autre application, afin de l’afficher ou de le modifier.

<!---HONumber=July15_HO2-->
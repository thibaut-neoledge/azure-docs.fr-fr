<properties 
   pageTitle="Mise à jour des serveurs avec des mises à jour du système"
   description="Découvrez comment utiliser le pack d'analyse décisionnelle des mises à jour du système dans Microsoft Azure Operational Insights pour vous aider à appliquer les mises à jour manquantes aux serveurs dans votre infrastructure"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/20/2015"
   ms.author="banders" />

# Mise à jour des serveurs avec des mises à jour du système

Vous pouvez utiliser le pack d'analyse décisionnelle des mises à jour du système dans Microsoft Azure Operational Insights pour vous aider à appliquer les mises à jour manquantes aux serveurs dans votre infrastructure. Vous installez le pack d'analyse décisionnelle pour mettre à jour l'agent Operations Manager et le module de configuration de base pour Operational Insights. Les informations de mise à jour sont lues sur les serveurs analysés, puis les données de mise à jour sont envoyées au service Operational Insights dans le cloud pour le traitement. La logique est appliquée aux données de mise à jour et le service cloud enregistre les données. Si des mises à jour manquantes sont trouvées, elles sont affichées sur le tableau de bord **Mises à jour**. Vous pouvez utiliser le tableau de bord **Mises à jour** pour trouver les mises à jour manquantes et définir un programme pour les appliquer aux serveurs qui en ont besoin.

## Utilisation des mises à jour du système pour mettre à jour des serveurs

Avant de pouvoir utiliser les mises à jour du système dans Microsoft Azure Operational Insights, vous devez disposer du pack d'analyse décisionnelle. Pour plus d'informations sur l'installation des packs d'analyse décisionnelle, consultez [Utilisation de la galerie pour ajouter ou supprimer des packs d'analyse décisionnelle](operational-insights-add-intelligence-packs.md). Après son installation, vous pouvez afficher les mises à jour manquantes sur vos serveurs analysés à l'aide de la vignette **Évaluation des mises à jour du système** sur la page **Présentation** d'Operational Insights. 

![image de vignette d'évaluation des mises à jour du système](./media/operational-insights-updates/overview-update.png)

La vignette ouvre le tableau de bord **Mises à jour** où vous pouvez afficher un résumé de l'ensemble de mises à jour manquantes. La page présente les catégories suivantes :

- Les serveurs avec des mises à jour manquantes

- Les serveurs qui n'ont pas été mis à jour récemment

- Les mises à jour qui doivent être appliquées à des serveurs spécifiques

- Le type de mises à jour manquantes

Vous pouvez cliquer sur n'importe quelle vignette ou n'importe quel élément pour en afficher les détails dans la page **Recherche**, afin d'obtenir plus d'informations sur la mise à jour manquante. 

![image du tableau de bord Mises à jour](./media/operational-insights-updates/gallery-sysupdate-01.png)

![image du tableau de bord Mises à jour](./media/operational-insights-updates/gallery-sysupdate-02.png)

## Résultats de recherche##
Les résultats de recherche de mises à jour comprennent :

- Serveur

- Titre de la mise à jour

- ID de la base de connaissances

- Produit concerné par la mise à jour

- Gravité de la mise à jour

- Date de publication

Les résultats de recherche de *serveur* comprennent :

- Nom du serveur

- Nom de version de système d'exploitation

- Méthode d'activation de la mise à jour automatique

- Jours écoulés depuis la dernière mise à jour

- Version de l'agent Windows Update

## Utilisation des mises à jour

1. Sur la page **Présentation**, cliquez sur la vignette **Évaluation des mises à jour du système**.

2. Sur le tableau de bord **Mises à jour**, affichez les catégories de mise à jour et choisissez-en une à utiliser.

3. Cliquez sur une vignette ou un élément pour afficher les informations détaillées les concernant dans la page **Recherche**.

4. En utilisant les informations fournies, vous pouvez définir un programme pour appliquer les mises à jour manquantes.

<!--HONumber=52-->
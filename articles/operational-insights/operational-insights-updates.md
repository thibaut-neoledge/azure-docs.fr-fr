<properties
   pageTitle="Mise à jour des serveurs avec des mises à jour du système"
   description="Découvrez comment utiliser la solution des mises à jour du système dans Microsoft Azure Operational Insights pour vous aider à appliquer les mises à jour manquantes aux serveurs dans votre infrastructure."
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
   ms.date="07/21/2015"
   ms.author="banders" />

# Mise à jour des serveurs avec des mises à jour du système

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Vous pouvez utiliser la solution des mises à jour du système dans Microsoft Azure Operational Insights pour vous aider à appliquer les mises à jour manquantes aux serveurs dans votre infrastructure. Vous installez la solution pour mettre à jour l’agent Operations Manager et le module de configuration de base pour Operational Insights. Les informations de mise à jour sont lues sur les serveurs analysés, puis les données de mise à jour sont envoyées au service Operational Insights dans le cloud pour le traitement. La logique est appliquée aux données de mise à jour et le service cloud enregistre les données. Si des mises à jour manquantes sont trouvées, elles sont affichées sur le tableau de bord des **Mises à jour**. Vous pouvez utiliser le tableau de bord des **Mises à jour** pour trouver les mises à jour manquantes et définir un programme pour les appliquer aux serveurs qui en ont besoin.

## Utilisation des mises à jour du système pour mettre à jour des serveurs

Avant de pouvoir utiliser les mises à jour du système dans Microsoft Azure Operational Insights, vous devez avoir installé la solution. Pour plus d'informations sur l'installation de solutions, consultez [Utilisation de la galerie de solutions pour ajouter ou supprimer des solutions](operational-insights-add-solution.md). Après son installation, vous pouvez afficher les mises à jour manquantes sur vos serveurs surveillés à l’aide de la vignette **Évaluation des mises à jour du système** sur la page **Vue d’ensemble** d’Operational Insights.

### Utilisation des mises à jour

1. Sur la page **Vue d’ensemble**, cliquez sur la vignette **Évaluation des mises à jour du système**. ![image de la page Vue d'ensemble](./media/operational-insights-updates/updates01.png)
2. Sur le tableau de bord des **Mises à jour**, affichez les catégories de mise à jour. ![image de la page Mises à jour](./media/operational-insights-updates/updates02.png)
3. Faites défiler vers la droite de la page pour afficher le panneau **Type de mises à jour manquantes**, puis cliquez sur **Mises à jour de sécurité**. ![image de la page Mises à jour](./media/operational-insights-updates/updates03.png)
4. Sur la page de recherche, une liste de mises à jour de sécurité qui étaient manquantes sur les serveurs dans votre infrastructure s'affiche. Cliquez sur l’ID d’un article de la Base de connaissances (KIBID) pour afficher plus d'informations sur la mise à jour manquante. Dans cet exemple, *KBID 3032323*. ![image de la page Mises à jour](./media/operational-insights-updates/updates04.png)
5. Votre navigateur web ouvre l'article de la Base de connaissances qui décrit la mise à jour. ![image de la page Mises à jour](./media/operational-insights-updates/updates05.png)
6. En utilisant les informations fournies, vous pouvez définir un programme pour appliquer les mises à jour manquantes.

[AZURE.INCLUDE [operational-insights-export](../../includes/operational-insights-export.md)]

<!---HONumber=July15_HO4-->
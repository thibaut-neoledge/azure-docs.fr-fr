<properties
   pageTitle="Affichage des alertes d'Operations Manager"
   description="Découvrez comment gérer les alertes d'Operations Manager concernant les serveurs analysés dans votre infrastructure"
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
   ms.date="09/10/2015"
   ms.author="banders" />



# Affichage des alertes d'Operations Manager

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Avant de pouvoir utiliser la gestion des alertes dans Microsoft Azure Operational Insights, vous devez avoir installé la solution. Pour plus d'informations sur l'installation des solutions, consultez [Configuration de votre espace de travail](operational-insights-setup-workspace.md). La solution ne fonctionne que lorsque vos serveurs sont analysés par des agents d'Operations Manager. Pour plus d’informations sur l’utilisation d’Operations Manager avec Operational Insights, consultez [Connexion à Operational Insights à partir de System Center Operations Manager](operational-insights-connect-scom.md).

Une fois la solution installée, les alertes concernant vos serveurs analysés peuvent être visualisées à l'aide de la vignette **Gestion des alertes** présente sur le tableau de bord **Aperçu** dans Operational Insights.

![image de la vignette de gestion des alertes](./media/operational-insights-alerts/overview-alert.png)


Vous pouvez afficher et gérer les alertes Microsoft Azure Operational Insights depuis le tableau de bord **Alertes**. Dans le tableau de bord, les informations concernant les alertes sont affichées selon les catégories suivantes :

- Alertes actives
	- Alertes critiques
	- Alertes d'avertissement
	- Sources d'alerte
- Toutes les alertes actives
	- Affiche le pourcentage des alertes critiques, des alertes d'avertissement et des alertes informatives.
- Requêtes d'alerte courantes
	- Cette zone contient des requêtes prédéfinies que l'équipe de développement du logiciel Operational Insights a créées pour vous aider à commencer à utiliser les alertes.


Les alertes vous indiquent qu'un problème a été détecté, le serveur concerné, ainsi que la priorité et le nom de l'alerte en question.

![image du tableau de bord Alertes](./media/operational-insights-alerts/alert-drilldown1.png)

![image du tableau de bord Alertes](./media/operational-insights-alerts/alert-drilldown2.png)


Sur le tableau de bord **Gestion des alertes**, vous pouvez prendre connaissance de toutes les alertes que Microsoft Azure Operational Insights a trouvées.

## Afficher les alertes Operational Insights

1. Sur la page **Aperçu**, cliquez sur la vignette **Gestion des alertes**.
2. Sur le tableau de bord **Gestion des alertes**, affichez les catégories d'alerte et sélectionnez celle avec laquelle vous souhaitez travailler.
3. Cliquez sur une vignette ou un élément pour afficher les informations détaillées les concernant dans la page **Recherche**.
4. En utilisant les informations que vous avez trouvées, vous pouvez étudier le niveau de gravité de l'alerte et déterminer les mesures supplémentaires à prendre pour résoudre le problème.

[AZURE.INCLUDE [operational-insights-export](../../includes/operational-insights-export.md)]

<!---HONumber=Sept15_HO3-->
<properties
	pageTitle="Solution d’évaluation des mises à jour système dans Log Analytics | Microsoft Azure"
	description="Vous pouvez utiliser la solution d’évaluation des mises à jour système dans Log Analytics pour vous aider à appliquer les mises à jour manquantes aux serveurs de votre infrastructure."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/06/2016"
	ms.author="banders"/>

# Solution d’évaluation des mises à jour système dans Log Analytics


Vous pouvez utiliser la solution d’évaluation des mises à jour système dans Log Analytics pour vous aider à appliquer les mises à jour manquantes aux serveurs de votre infrastructure. Une fois la solution installée, vous pouvez afficher les mises à jour manquantes de vos serveurs analysés à l’aide de la vignette **Évaluation des mises à jour système** dans la page **Vue d’ensemble** d’OMS.

Si des mises à jour manquantes sont trouvées, des informations détaillées sont affichées sur le tableau de bord **Mises à jour**. Vous pouvez utiliser le tableau de bord des **Mises à jour** pour trouver les mises à jour manquantes et définir un programme pour les appliquer aux serveurs qui en ont besoin.

## Installation et configuration de la solution
Utilisez les informations suivantes pour installer et configurer la solution.

- Ajoutez la solution d’évaluation des mises à jour système à votre espace de travail OMS en suivant la procédure décrite dans la rubrique [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md). Aucune configuration supplémentaire n’est requise.

## Détails de la collecte de données des mises à jour système

Le tableau suivant présente les méthodes de collecte des données et d’autres informations sur le mode de collecte en vue de l’évaluation des mises à jour système.

| plateforme | Agent direct | Agent SCOM | Azure Storage | SCOM requis ? | Données de l’agent SCOM envoyées via un groupe d’administration | fréquence de collecte |
|---|---|---|---|---|---|---|
|Windows|![Oui](./media/log-analytics-system-update/oms-bullet-green.png)|![Oui](./media/log-analytics-system-update/oms-bullet-green.png)|![Non](./media/log-analytics-system-update/oms-bullet-red.png)| ![Non](./media/log-analytics-system-update/oms-bullet-red.png)|![Oui](./media/log-analytics-system-update/oms-bullet-green.png)| Au moins 2 fois par jour et 15 minutes après l’installation d’une mise à jour|


### Utilisation des mises à jour

1. Sur la page **Vue d’ensemble**, cliquez sur la vignette **Évaluation des mises à jour du système**. ![image de la page Vue d'ensemble](./media/log-analytics-system-update/oms-updates01.png)
2. Sur le tableau de bord des **Mises à jour**, affichez les catégories de mise à jour. ![image de la page Mises à jour](./media/log-analytics-system-update/oms-updates02.png)
3. Faites défiler vers la droite de la page pour afficher le panneau **Type de mises à jour manquantes**, puis cliquez sur **Mises à jour de sécurité**. ![image de la page Mises à jour](./media/log-analytics-system-update/oms-updates03.png)
4. Sur la page de recherche, une liste de mises à jour de sécurité qui étaient manquantes sur les serveurs dans votre infrastructure s'affiche. Cliquez sur l’ID d’un article de la Base de connaissances (KIBID) pour afficher plus d'informations sur la mise à jour manquante. Dans cet exemple, *KBID 3032323*. ![image de la page Mises à jour](./media/log-analytics-system-update/oms-updates04.png)
5. Votre navigateur web ouvre l'article de la Base de connaissances qui décrit la mise à jour. ![image de l’article de la Base de connaissances](./media/log-analytics-system-update/oms-updates05.png)
6. En utilisant les informations fournies, vous pouvez définir un programme pour appliquer les mises à jour manquantes.

## Étapes suivantes

- [Lancez une recherche dans les journaux](log-analytics-log-searches.md) pour afficher les données de mise à jour système détaillées.

<!---HONumber=AcomDC_0518_2016-->
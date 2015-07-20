<properties
   pageTitle="Gestion de la capacité de l'infrastructure"
   description="Découvrez comment utiliser la solution de planification de la capacité dans Operational Insights pour mieux comprendre la capacité de votre infrastructure de serveur."
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

# Gestion de la capacité de l'infrastructure

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Vous pouvez utiliser la solution de planification de la capacité dans Microsoft Azure Operational Insights pour vous aider à comprendre la capacité de votre infrastructure serveur. Vous installez la solution pour mettre à jour l’agent Operations Manager et le module de configuration de base dans Operational Insights. La solution lit les compteurs de performance sur le serveur analysé et envoie les données d'utilisation au service Operational Insights dans le cloud pour le traitement. La logique est appliquée aux données d'utilisation et le service cloud enregistre les données. Au fil du temps, des modèles d'utilisation sont identifiés et la capacité est projetée, en fonction de la consommation actuelle.

Par exemple, une projection peut identifier le moment où de la mémoire supplémentaire ou des cœurs de processeur supplémentaires sont nécessaires pour un serveur. Dans cet exemple, la projection peut indiquer que dans 30 jours, le serveur aura besoin de mémoire supplémentaire. Cela peut vous aider à planifier une mise à niveau de la mémoire au cours de la fenêtre de maintenance suivante du serveur, qui peut avoir lieu toutes les deux semaines.

## Tableau de bord Gestion de la capacité

Avant de pouvoir utiliser le tableau de bord de gestion de la capacité dans Microsoft Azure Operational Insights, vous devez avoir installé la solution. Pour plus d'informations sur l'installation de solutions, consultez la rubrique [Utilisation de la galerie de solutions pour ajouter ou supprimer des solutions](operational-insights-add-solution.md). Une fois la solution de planification de la capacité installée, vous pouvez consulter la capacité de vos serveurs analysés à l'aide de la vignette **Planification de la capacité** sur la page **Vue d’ensemble** d'Operational Insights.

![image de la vignette Planification de la capacité](./media/operational-insights-capacity/overview-cap-plan.png)

La vignette ouvre le tableau de bord **Gestion de la capacité**, où vous pouvez afficher un résumé de la capacité de votre serveur. La page présente les vignettes suivantes :

- *Capacité des ordinateurs virtuels* : indique la capacité restante (en jours) des ordinateurs virtuels.

- *Calcul* : affiche les cœurs de processeurs et la mémoire disponible.

- *Stockage* : indique l'espace disque utilisé et la latence.

- *Recherche* : explorateur de données que vous pouvez utiliser pour rechercher des données dans le système Operational Insights.

>[AZURE.NOTE]Pour afficher les données de gestion de capacité, vous devez activer la connectivité d'Operations Manager avec Virtual Machine Manager (VMM). Pour plus d'informations sur la connexion des systèmes, voir Comment connecter VMM à Operations Manager.

![image du tableau de bord Gestion de la capacité](./media/operational-insights-capacity/gallery-capacity-01.png)

### Pour afficher une page de capacité

- Sur la page **Vue d’ensemble**, cliquez sur **Gestion de la capacité**, puis sur **Calcul** ou **Stockage**.

## Page Calcul

Vous pouvez utiliser le tableau de bord **Calcul** de Microsoft Azure Operational Insights pour afficher les informations de capacité d'utilisation, les jours de capacité restants et l'efficacité de votre infrastructure. La zone **Utilisation** permet de consulter le taux d'utilisation du cœur de processeur et de la mémoire des hôtes de vos ordinateurs virtuels. Vous pouvez utiliser l'outil de projection pour estimer la capacité disponible pour une période donnée. Vous pouvez utiliser la zone **Efficacité** pour consulter l'efficacité des hôtes de vos ordinateurs virtuels. Vous pouvez afficher les détails des éléments liés en cliquant dessus.

Vous pouvez générer un classeur Excel pour les catégories suivantes :

- Hôtes aux meilleurs taux d'utilisation des cœurs

- Hôtes aux meilleurs taux d'utilisation de mémoire

- Hôtes dont les ordinateurs virtuels sont les moins efficaces

- Hôtes aux meilleurs taux d'utilisation

- Hôtes aux taux d'utilisation les plus faibles

![image de la page Calcul de gestion de la capacité](./media/operational-insights-capacity/gallery-capacity-02.png)


Le tableau de bord **Calcul** présente les zones suivantes :

**Utilisation** : indique le taux d'utilisation du cœur de processeur et de la mémoire des hôtes de vos ordinateurs virtuels.

- *Cœurs utilisés* : somme de tous les hôtes (% d'utilisation du processeur multiplié par le nombre de cœurs physiques sur l'hôte).

- *Cœurs libres* : nombre total de cœurs moins les cœurs utilisés.

- *Pourcentage de cœurs disponibles* : nombre de cœurs physiques libres divisé par le nombre total de cœurs physiques.

- *Cœurs virtuels par ordinateur virtuel* : nombre total de cœurs dans le système divisé par le nombre total d'ordinateurs virtuels dans le système.

- *Rapport cœurs virtuels/cœurs physiques* : rapport entre le nombre total de cœurs physiques et le nombre de cœurs physiques utilisés par des ordinateurs virtuels du système.

- *Nombre de cœurs virtuels disponibles* : rapport entre le nombre de cœurs virtuels et de cœurs physiques multiplié par le nombre de cœurs physiques disponibles.

- *Mémoire utilisée* : somme de la mémoire utilisée par tous les hôtes.

- *Mémoire disponible* : mémoire physique totale moins la mémoire utilisée.

- *Pourcentage de mémoire disponible* : mémoire physique disponible divisée par la mémoire physique totale.

- *Mémoire virtuelle par ordinateur virtuel* : mémoire virtuelle totale du système divisée par le nombre total d'ordinateurs virtuels dans le système.

- *Rapport mémoire virtuelle/mémoire physique* : mémoire virtuelle totale du système divisée par la mémoire physique totale du système.

- *Mémoire virtuelle disponible* : rapport entre la mémoire virtuelle et la mémoire physique multiplié par la mémoire physique disponible.

**Outil de projection**

L'outil de projection vous permet d'afficher l'historique des tendances d'utilisation de vos ressources. Cela comprend les tendances d'utilisation des machines virtuelles, de la mémoire, des cœurs et de stockage. La fonction de projection utilise un algorithme de projection pour vous aider à planifier l'épuisement de chaque ressource. Cela vous permet de calculer les capacités avec précision et de prévoir l'achat de capacité supplémentaire (mémoire, cœurs ou stockage).

**Efficacité**

- *Ordinateurs virtuels inactifs* : ordinateurs utilisant moins de 10 % du processeur et de 10 % de la mémoire pour une période donnée.

- *Ordinateurs virtuels surexploités* : ordinateurs utilisant plus de 90 % du processeur et de 90 % de la mémoire pour une période donnée.

- *Hôte inactif* : ordinateurs utilisant moins de 10 % du processeur et de 10 % de la mémoire pour une période donnée.

- *Hôte surexploité* : ordinateurs utilisant plus de 90 % du processeur et de 90 % de la mémoire pour une période donnée.

### Utilisation des éléments de la page Calcul

1. Dans la zone **Utilisation** du tableau de bord **Calcul**, vous pouvez consulter les informations de capacité des cœurs de processeurs et de la mémoire utilisés.

2. Cliquez sur un élément pour l'ouvrir dans la page de **recherche** et en afficher les informations détaillées.

3. Dans l'outil de **projection**, déplacez le curseur de date pour afficher une projection de la capacité qui sera utilisée à la date choisie.

3. Dans la zone **Efficacité**, vous pouvez afficher l'efficacité de la capacité des ordinateurs virtuels et des hôtes d'ordinateurs virtuels.

##Page Stockage en attachement direct

Le tableau de bord **Stockage en attachement direct** de Microsoft Azure Operational Insights vous permet d'afficher des informations de capacité relatives à l'utilisation du stockage et à la performance des disques, ainsi que la capacité restante des disques (en jours). La zone **Utilisation** permet de consulter le taux d'utilisation de l'espace disque des hôtes de vos ordinateurs virtuels. La zone **Performances du disque** permet de consulter le taux d'utilisation et de latence de l'espace disque des hôtes de vos ordinateurs virtuels. Vous pouvez également utiliser l'outil de projection pour estimer la capacité disponible pour une période donnée. Vous pouvez afficher les détails des éléments liés en cliquant dessus.

Vous pouvez générer un classeur Excel à partir de ces informations de capacité pour les catégories suivantes :

- Utilisation la plus élevée de l'espace disque par hôte

- Latence moyenne la plus élevée par hôte

La page **Stockage** présente les zones suivantes :

- *Utilisation* : permet de consulter le taux d'utilisation de l'espace disque de vos hôtes d'ordinateurs virtuels.

- *Espace disque total* : somme (espace disque logique) de tous les hôtes.

- *Espace disque utilisé* : somme (espace disque logique utilisé) de tous les hôtes.

- *Espace disque disponible* : espace disque total moins l'espace disque utilisé.

- *Pourcentage de disque utilisé* : espace disque utilisé divisé par l'espace disque total.

- *Pourcentage de disque disponible* : espace disque disponible divisé par l'espace disque total.

![image de la page Stockage en attachement direct de gestion de la capacité](./media/operational-insights-capacity/gallery-capacity-03.png)

**Performances du disque**

Operational Insights vous permet de consulter l'historique des tendances d'utilisation de l'espace disque. La fonction de projection utilise un algorithme pour estimer l'utilisation future. Elle vous permet notamment de prévoir l'épuisement de l'espace disque. Cela vous permet de planifier le stockage approprié et de savoir quand acheter plus de stockage.

**Outil de projection**

L'outil de projection vous permet d'afficher l'historique des tendances d'utilisation de l'espace disque. La fonction de projection vous permet également de prévoir l'épuisement de l'espace disque. Cela vous permet de planifier la capacité appropriée et de savoir quand acheter plus de capacité.

### Utilisation des éléments de la page Stockage en attachement direct

1. La zone **Utilisation** du tableau de bord **Stockage en attachement direct** vous permet d'afficher les informations relatives à l'utilisation des disques.

2. Cliquez sur un élément lié pour l'ouvrir dans la page de **recherche** et en afficher les informations détaillées.

3. La zone **Performances du disque** permet de consulter le taux d'utilisation et de latence des disques.

4. Dans l'**outil de projection**, déplacez le curseur de date pour afficher une projection de la capacité qui sera utilisée à la date choisie.

<!---HONumber=July15_HO2-->
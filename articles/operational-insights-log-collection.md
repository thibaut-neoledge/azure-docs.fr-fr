<properties 
   pageTitle="Utilisation de la gestion des journaux" 
   description="Avec la gestion des journaux dans Microsoft Azure Operational Insights, vous pouvez afficher les événements de journal collectés depuis vos serveurs analysés" 
   services="operational-insights" 
   documentationCenter="" 
   authors="bandersmsft" 
   manager="jwhit" 
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="operational-insights" 
   ms.date="03/20/2015"
   ms.author="banders"/>

# Utilisation de la gestion des journaux 

Avant de pouvoir utiliser la gestion des journaux dans Microsoft Azure Operational Insights, vous devez disposer du pack d'analyse décisionnelle. Dans la mesure où ce pack doit être configuré et ne collecte rien par défaut, il est activé par défaut dans tout nouvel espace de travail. Si vous l'avez supprimé, vous pouvez l'ajouter de nouveau à partir de la galerie de packs d'analyse décisionnelle, consultez [Utilisation de la galerie pour ajouter ou supprimer des packs d'analyse décisionnelle](operational-insights-add-intelligence-packs.md). 

Vous pouvez ajouter de nouveaux journaux pour collecter des événements et choisir les niveaux ou la gravité d'événements que vous souhaitez collecter dans les journaux.
Une fois la gestion des journaux configurée, elle transfère votre stratégie de collection et commence à collecter des événements.
Vous pouvez accéder à certaines analyses d'événements de journaux collectés à partir de vos serveurs surveillés à l'aide de la vignette **Gestion des journaux** de la page **Présentation** d'Operational Insights. 

![image de la vignette Gestion du journal](./media/operational-insights-log-collection/overview-log-mgt.png)

La vignette ouvre la page **Gestion des journaux**, où vous pouvez afficher un résumé des événements contenus dans les journaux.
Ou vous pouvez directement utiliser la recherche, comme pour tout autre type de données.


La page présente les catégories suivantes :

- Journaux d'événements Windows
- Journaux IIS
- Autres journaux que vous avez ajoutés

![image du tableau de bord de Gestion des journaux](./media/operational-insights-log-collection/gallery-logmgt-01.png)

![image du tableau de bord de Gestion des journaux](./media/operational-insights-log-collection/gallery-logmgt-02.png)

## Format de fichier journal IIS

Le seul format de journal IIS actuellement pris en charge est W3C. Ne vous inquiétez pas, c'est le format le plus courant et le format par défaut dans IIS 7 et IIS 8. Par conséquent, si vous vous connectez au format natif NCSA ou IIS, Operational Insights n'acceptera pas ces journaux. Même pour le format W3C, vous verrez que tous les champs ne sont pas journalisés par défaut. Vous pouvez en savoir plus sur ce format sur la page [Sélection des champs W3C à consigner (IIS 7)](https://technet.microsoft.com/library/cc754702(v=WS.10).aspx). 


> [AZURE.TIP] Pour obtenir la meilleure expérience de recherche, nous vous recommandons de sélectionner tous les champs de journalisation pour chaque site Web à l'aide de la **Journalisation** dans IIS. Nous vous recommandons également de modifier le calendrier de **Substitution de fichier journal** pour les nouveaux journaux sur **Toutes les heures**, afin de télécharger de plus petits fichiers sur le cloud et économiser ainsi de la bande passante.


## Collection des journaux des événements Windows à partir d'Operations Manager ou d'agents directement connectés

1. Sur la page **Présentation**, cliquez sur la vignette **Gestion des journaux**. 

2. Sur le tableau de bord **Gestion des journaux**, cliquez sur la vignette **Configuration**.
 
3. Tapez le nom du journal des événements à partir duquel vous souhaitez collecter des informations. Si vous n'êtes pas sûr du nom à utiliser, sélectionnez les propriétés du journal des événements Windows dans **l'Observateur d'événements**, copiez le nom du champ **FullName**, puis collez-le dans la zone **Collecter les événements depuis les journaux d'événements suivants**.

4. Cliquez sur **+** pour ajouter le journal.

5. Sélectionnez les niveaux ou la gravité d'événements que vous souhaitez collecter dans le journal. **Les événements de **Succès de l'audit** et d'**Échec de l'audit** ne sont pas pris en charge dans cette version.

6. Répétez les étapes précédentes pour chaque journal à partir duquel vous souhaitez collecter des informations, puis cliquez sur **Enregistrer**.

7. Les événements apparaissent dans Operational Insights en quelques minutes, et vous pouvez ensuite rechercher les données. 



## Collection des journaux IIS à partir d'Operations Manager ou d'agents directement connectés

1. Sur la page **Présentation**, cliquez sur la vignette **Gestion des journaux**. 

2. Sur le tableau de bord **Gestion des journaux**, cliquez sur la vignette **Configuration**.
 
3. Sous **Journaux IIS**, sélectionnez **Collecter les journaux depuis Operations Manager**.


## Collection des journaux IIS et/ou des événements Windows à partir d'Azure Diagnostics
Vous pouvez configurer ce paramètre à partir du portail de gestion Azure, et non du portail Operational Insights. Dans votre espace de travail, accédez à l'onglet **Stockage**. Vous pouvez activer la collecte de journaux à partir de ce compte de stockage.


## Utilisation des fichiers journaux
 
1. Sur la page **Présentation**, cliquez sur la vignette **Gestion des journaux**.

2. Sur le tableau de bord **Gestion des journaux**, affichez les journaux des événements et choisissez-en un à utiliser.
  
3. Cliquez sur un élément pour afficher les informations détaillées le concernant dans la page **Recherche**.

4. Vous pouvez effectuer des recherches supplémentaires à partir des résultats initiaux, puis analyser des journaux et les mettre en corrélation.

 
# Ressources supplémentaires
- [Exigences de format de journal IIS dans Azure Operational Insights](http://blogs.technet.com/b/momteam/archive/2014/09/19/iis-log-format-requirements-in-system-center-advisor.aspx)
- Consultez les autres sources de données et types de journaux que la communauté nous demande de mettre en œuvre dans notre [Forum de commentaires](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy).


<!--HONumber=52-->
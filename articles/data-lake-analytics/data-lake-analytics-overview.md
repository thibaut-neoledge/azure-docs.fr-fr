<properties 
   pageTitle="Vue d'ensemble de Microsoft Azure Data Lake Analytics | Azure" 
   description="Data Lake Analytics est un service Azure de calcul de Big Data qui vous permet d'utiliser des données pour piloter votre entreprise en utilisant les informations issues de vos données dans le cloud, quels qu'en soient l'emplacement et la taille. Data Lake Analytics permet cette utilisation de la manière la plus simple, la plus évolutive et la plus économique possible." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="01/06/2016"
   ms.author="jgao"/>

# Vue d'ensemble de Microsoft Azure Data Lake Analytics

## Définition d'Azure Data Lake Analytics

Azure Data Lake Analytics est un nouveau service, conçu pour faciliter l'analyse de Big Data. Ce service vous permet de vous concentrer sur l'écriture, l'exécution et la gestion des travaux, plutôt que le fonctionnement de l'infrastructure distribuée. Au lieu de déployer, de configurer et d'optimiser le matériel, vous pouvez écrire des requêtes pour transformer vos données et en extraire des informations pertinentes. Le service d'analyse peut traiter les travaux instantanément, quelle que soit leur échelle, en définissant le compteur sur la puissance requise. Vous payez les travaux uniquement lorsque ceux-ci sont exécutés, ce qui rend le service économique. Le service d'analyse prend en charge Azure Active Directory, ce qui vous permet de gérer les accès et les rôles et de bénéficier d'une intégration avec vos systèmes d'identité locaux. Il inclut également U-SQL, un langage qui associe les avantages de SQL avec la puissance d'expression du code utilisateur. Le runtime distribué et évolutif de U-SQL vous permet d'analyser efficacement les données situées dans le magasin et les serveurs SQL Server dans Azure, Azure SQL Database et Azure SQL Data Warehouse.


## Fonctionnalités clés

- **Mise à l'échelle dynamique** 

    Data Lake Analytics a été conçu pour fournir des performances et une mise à l'échelle dans le cloud. Il approvisionne dynamiquement les ressources et vous permet d'analyser plusieurs téraoctets voire exaoctets de données. Lorsque le travail est terminé, le service ralentit automatiquement les ressources et vous payez uniquement la puissance de traitement utilisée. Il n'est pas nécessaire de réécrire le code à mesure que vous augmentez ou diminuez la taille des données stockées ou le volume de calcul utilisé. Vous pouvez ainsi vous concentrer uniquement sur votre logique métier, et non sur la manière dont vous traitez et stockez les jeux de données volumineux.

- **Développez plus rapidement, déboguez et optimisez de manière plus intelligente à l'aide d'outils familiers**

    L'étroite intégration de Data Lake Analytics avec Visual Studio vous permet d'utiliser des outils familiers pour exécuter, déboguer et optimiser votre code. Les visualisations de vos travaux U-SQL vous permettent de voir la façon dont votre code est exécuté à l'échelle. Vous pouvez ainsi facilement identifier les goulots d'étranglement en matière de performances et optimiser les coûts.

- **U-SQL : simple et familier, puissant et extensible**

    Data Lake Analytics inclut U-SQL, un langage de requête qui étend la nature connue, simple, déclarative de SQL avec la puissance d'expression de C#. Le langage U-SQL est basé sur le runtime distribué qui alimente les systèmes Big Data au sein de Microsoft. Des millions de développeurs SQL et .NET peuvent désormais traiter et analyser l'ensemble de leurs données avec leurs compétences actuelles.

- **S'intègre de façon transparente avec vos investissements informatiques**

    Data Lake Analytics peut utiliser vos investissements informatiques existants pour l'identité, la gestion, la sécurité et l'entreposage des données. Cela simplifie la gouvernance des données et facilite l'extension de vos applications de données actuelles. Data Lake Analytics est intégré à Active Directory pour les autorisations et la gestion des utilisateurs. Il est fourni avec des capacités de surveillance et d'audit intégrées.

- **Abordable et économique**

    Data Lake Analytics est une solution économique permettant d'exécuter vos charges de travail Big Data. Vous payez pour chaque travail au cours duquel des données sont traitées. Aucun matériel, aucune licence ni aucun contrat de support technique propre au service ne sont requis. Le système est mis à l'échelle automatiquement au démarrage du travail puis à la fin de son exécution. Par conséquent, vous ne payez que ce dont vous avez réellement besoin.

- **Fonctionne avec toutes vos données Azure**

    Data Lake Analytics peut travailler avec plusieurs sources de données Azure : objets Blob de stockage Azure, base de données SQL Azure et, bien entendu, Data Lake Analytics est spécialement optimisé pour travailler avec Azure Data Lake Store et fournit le plus haut niveau de performances, de débit et de parallélisation pour vos charges de travail Big Data.

## Voir aussi

- Prise en main
    - [Prise en main des analyses Data Lake à l’aide du portail Azure](data-lake-analytics-get-started-portal.md)
    - [Prise en main de Data Lake Analytics à l'aide d'Azure PowerShell](data-lake-analytics-get-started-powershell.md)
    - [Prise en main de Data Lake Analytics à l'aide du Kit de développement logiciel (SDK) .NET Azure](data-lake-analytics-get-started-net-sdk.md)
    - [Développer des scripts U-SQL avec les outils Data Lake pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
    - [Prise en main du langage U-SQL Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md)
    
- U-SQL et développement
    - [Prise en main du langage U-SQL Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md)
    - [Utiliser les fonctions U-SQL dans les travaux d’analyse Azure Data Lake](data-lake-analytics-use-window-functions.md)
    - [Développer des opérateurs définis par l'utilisateur U-SQL pour des travaux Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
    
- Gestion
    - [Gérer les analyses Azure Data Lake à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md)
    - [Gestion d'Azure Data Lake Analytics à l'aide d'Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
    - [Surveiller et résoudre les problèmes des tâches d’analyse Azure Data Lake à l’aide du portail Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

- Didacticiel de bout en bout
    - [Utiliser les didacticiels interactifs Azure Data Lake Analytics](data-lake-analytics-use-interactive-tutorials.md)
    - [Analyser les journaux des sites web à l'aide d'Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md)

- Donnez-nous votre avis
    - [Commentaires sur le backlog de la documentation](data-lake-analytics-documentation-backlog.md)
    - [Soumettre une demande de fonctionnalité](http://aka.ms/adlafeedback)
    - [Obtenir de l'aide dans les forums](http://aka.ms/adlaforums)

<!---HONumber=AcomDC_0107_2016-->
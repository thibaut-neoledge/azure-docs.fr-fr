<properties
   pageTitle="Développer des solutions intégrées avec SQL Data Warehouse | Microsoft Azure"
   description="Outils et partenaires proposant des solutions s’intégrant avec SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/22/2015"
   ms.author="lodipalm"/>

#Intégrer des services à SQL Data Warehouse
Outre ses fonctionnalités principales, SQL Data Warehouse permet aux utilisateurs de tirer parti de nombreux autres services dans Azure. Plus précisément, nous avons pris des mesures pour l’intégrer étroitement aux éléments suivants :

+ Power BI
+ Azure Data Factory
+ Azure Machine Learning
+ Azure Stream Analytics

Outre ces éléments, nous travaillons à un partenariat plus étroit avec un certain nombre d’autres services dans l’écosystème Azure.

##Power BI
L’intégration de Power BI permet aux utilisateurs de largement tirer parti de la puissance de calcul de SQL Data Warehouse avec la création de rapports dynamiques et la visualisation de Power BI. L’intégration à Power BI inclut actuellement les éléments suivants :

+ **Connexion directe** : une connexion plus avancée avec un menu déroulant logique dans SQL Data Warehouse. Ce faisant, les analyses sont plus rapides à une plus grande échelle.
+ **Ouvrir dans Power BI** : ce bouton transmet les informations d’instance à Power BI, permettant une connexion plus fluide. 

Pour plus d’informations, consultez [Intégrer à Power BI](../sql-data-warehouse-integrate-power-bi.md) ou la [documentation Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx).

##Azure Data Factory
Azure Data Factory offre aux utilisateurs une plateforme gérée pour créer des pipelines d’extraction-transformation-chargement complexes. L’intégration de SQL Data Warehouse à Azure Data Factory inclut les éléments suivants :

+ **Déplacement des données** : planifiez le transfert de données entre plusieurs services sur site et Azure.
+ **Procédures stockées** : orchestrez l’exécution de procédures stockées dans SQL Data Warehouse.

Pour plus d’informations, consultez [Intégrer à Azure Data Factory](../sql-data-warehouse-integrate-azure-data-factory.md) ou la [documentation Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/).

##Azure Machine Learning
Azure Machine Learning est un service d’analyse entièrement géré qui permet aux utilisateurs de créer des modèles complexes exploitant un large ensemble d’outils prédictifs. SQL Data Warehouse est pris en charge à la fois comme source et destination de ces modèles avec les fonctionnalités suivantes :

+ **Lire les données :** pilotez des modèles à l’échelle à l’aide de T-SQL dans SQL Data Warehouse. 
+ **Écrire des données :** valider les modifications d’un modèle dans SQL Data Warehouse.

Pour plus d’informations, consultez [Intégrer à Azure Machine Learning](../sql-data-warehouse-integrate-azure-machine-learning.md) ou la [documentation Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/).

##Azure Stream Analytics
Azure Stream Analytics est une infrastructure complexe, entièrement gérée pour le traitement et l’utilisation des données d’événement générées à partir d’Azure Event Hubs. L’intégration à SQL Data Warehouse permet de traiter efficacement et de stocker les données de diffusion en continu avec des données relationnelles, ce qui permet une analyse plus approfondie et plus avancée.

+ **Sortie de la tâche :** envoyez une sortie des tâches Stream Analytics directement à SQL Data Warehouse.

Pour plus d’informations, consultez [Intégrer à Azure Stream Analytics](../sql-data-warehouse-integrate-azure-stream-analytics.md) ou la [documentation Azure Stream Analytics](https://azure.microsoft.com/documentation/services/stream-analytics/).

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop/

[Azure Data Factory]: sql-data-warehouse-integrate-azure-data-factory.md
[Azure Machine Learning]: sql-data-warehouse-integrate-azure-machine-learning.md
[Azure Stream Analytics]: sql-data-warehouse-integrate-azure-stream-analytics.md
[Power BI]: sql-data-warehouse-integrate-power-bi.md
[Partners]: sql-data-warehouse-integrate-solution-partners.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO3-->
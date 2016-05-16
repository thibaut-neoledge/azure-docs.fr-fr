<properties
   pageTitle="Gestion des tâches d’évolutivité de l’Azure SQL Data Warehouse (portail Azure) | Microsoft Azure"
   description="Tâches du portail Azure permettant une montée en charge des performances de l’Azure SQL Data Warehouse. Modifiez les ressources de calcul en ajustant les unités DWU Ou suspendez et reprenez des ressources de calcul pour réduire les coûts."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/28/2016"
   ms.author="barbkess;sonyama"/>

# Gestion des tâches d’évolutivité de l’Azure SQL Data Warehouse (portail Azure)

> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-data-warehouse-overview-scalability.md)
- [Portail](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)
- [REST](sql-data-warehouse-manage-scale-out-tasks-rest-api.md)
- [TSQL](sql-data-warehouse-manage-scale-out-tasks-tsql.md)


Mettez à l'échelle de façon élastique les ressources de calcul et la mémoire pour répondre aux besoins changeants de votre charge de travail, et réduisez vos coûts en réduisant les ressources pendant les heures creuses.

Cette collection de tâches utilise le portail Azure pour :

- Mise à l’échelle des performances en ajustant les unités DWU
- Interrompre des ressources de calcul
- Reprendre des ressources de calcul

Pour en savoir plus, consultez la [vue d’ensemble de l’évolutivité des performances][] (en anglais).

<a name="scale-performance-bk"></a>

## Mise à l’échelle des performances

[AZURE.INCLUDE [Description de la mise à l’échelle des unités DWU SQL Data Warehouse](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Pour modifier les ressources de calcul :

1. Ouvrez le [portail Azure][], ouvrez votre base de données, puis cliquez sur **Mettre à l’échelle**.

    ![Cliquez sur Mettre à l’échelle.][1]

1. Dans le panneau de mise à l’échelle, déplacez le curseur vers la gauche ou vers la droite pour modifier le paramètre DWU.

    ![Déplacez le curseur][2]

1. Cliquez sur **Enregistrer**. Un message de confirmation s’affiche. Cliquez sur **Oui** pour confirmer ou sur **Non** pour annuler.

    ![Cliquez sur Enregistrer.][3]

<a name="pause-compute-bk"></a>

## Suspension du calcul

[AZURE.INCLUDE [Description de la suspension de SQL Data Warehouse](../../includes/sql-data-warehouse-pause-description.md)]

Pour suspendre une base de données :

1. Ouvrez le [portail Azure][], puis votre base de données. Notez que l’état est **En ligne**. 

    ![État En ligne][6]

1. Pour interrompre les ressources de calcul et de mémoire, cliquez sur **Pause**. Un message de confirmation s’affiche. Cliquez sur **Oui** pour confirmer ou sur **Non** pour annuler.

    ![Confirmer la pause][7]

1. Lorsque SQL Data Warehouse démarre la base de données, l’état est « Interruption ».
2. Lorsque l’état est **Suspendu**, l’opération de suspension est effectuée et aucune DWU ne vous est facturée.

    ![État Pause][4]

<a name="resume-compute-bk"></a>

## Reprise du calcul

[AZURE.INCLUDE [Description de la reprise de SQL Data Warehouse](../../includes/sql-data-warehouse-resume-description.md)]Pour reprendre une base de données :

1. Ouvrez le [portail Azure][], puis votre base de données. Notez que l’état est **Suspendu**. 

    ![Suspendre la base de données][4]

1. Pour reprendre la base de données, cliquez sur **Démarrer**. Un message de confirmation s’affiche. Cliquez sur **Oui** pour confirmer ou sur **Non** pour annuler.

    ![Confirmer la reprise][5]

1. Lorsque SQL Data Warehouse démarre la base de données, l’état est « Reprise ».
2. La base de données est prête lorsque l’état est **en ligne**.

    ![État En ligne][6]

<a name="next-steps-bk"></a>

## Étapes suivantes
Pour plus d'informations, consultez la [vue d’ensemble de la gestion][].

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-scale-out-tasks/click-scale.png
[2]: ./media/sql-data-warehouse-manage-scale-out-tasks/move-slider.png
[3]: ./media/sql-data-warehouse-manage-scale-out-tasks/click-save.png
[4]: ./media/sql-data-warehouse-manage-scale-out-tasks/resume-database.png
[5]: ./media/sql-data-warehouse-manage-scale-out-tasks/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-scale-out-tasks/pause-database.png
[7]: ./media/sql-data-warehouse-manage-scale-out-tasks/pause-confirm.png

<!--Article references-->
[vue d’ensemble de la gestion]: ./sql-data-warehouse-overview-manage.md
[vue d’ensemble de l’évolutivité des performances]: ./sql-data-warehouse-overview-scalability.md

<!--MSDN references-->


<!--Other Web references-->

[portail Azure]: http://portal.azure.com/

<!---HONumber=AcomDC_0504_2016-->
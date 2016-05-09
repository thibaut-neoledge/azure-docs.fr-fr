
<!--
includes/sql-data-warehouse-include-pause-description.md

Latest Freshness check:  2016-04-22 , barbkess.

As of circa 2016-04-22, the following topics might include this include:
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks.md
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks-powershell.md
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks-rest-api.md

-->
Pour réduire les coûts, vous pouvez interrompre et reprendre des ressources de calcul à la demande. Par exemple, si vous n’utilisez pas la base de données pendant la nuit et les week-ends, vous pouvez la suspendre à ces moments et la reprendre pendant la journée. Les unités DWU ne vous seront pas facturées pendant que la base de données est suspendue.

Lorsque vous suspendez une base de données :

- Les ressources de calcul et de mémoire sont renvoyées dans le pool des ressources disponibles du centre de données.
- Les coûts DWU sont nuls pour la durée de la pause.
- Le stockage de données n’est pas affecté et vos données restent intactes. 
- SQL Data Warehouse annule toutes les opérations en cours d’exécution ou en file d’attente.

<!---HONumber=AcomDC_0427_2016-->
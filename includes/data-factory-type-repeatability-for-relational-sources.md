## Répétabilité pendant la copie

Lors de la copie des données depuis et vers des magasins relationnels, vous devez garder à l’esprit la répétabilité afin d’éviter des résultats imprévus.

Une tranche peut être réexécutée automatiquement dans Azure Data Factory en fonction de la stratégie relative aux nouvelles tentatives qui a été spécifiée. Nous vous recommandons de définir une stratégie relative aux nouvelles tentatives pour éviter les échecs passagers. Par conséquent, la répétabilité est un aspect important à prendre en compte pendant le déplacement des données.

**En tant que source :**

> [AZURE.NOTE] Les exemples suivants concernent SQL Azure, mais sont applicables à tout autre magasin de données prenant en charge les jeux de données rectangulaires. Vous pouvez ajuster le **type** de source et la propriété de **requête** (par exemple : query au lieu de sqlReaderQuery) pour le magasin de données.

En général, vous pouvez lire uniquement les données des magasins relationnels qui correspondent à cette tranche. Pour cela, vous pouvez utiliser les variables WindowStart et WindowEnd disponibles dans Azure Data Factory. Pour plus d’informations sur les variables et les fonctions dans Azure Data Factory, lisez l’article intitulé [Planification et exécution](../articles/data-factory/data-factory-scheduling-and-execution.md). Exemple :
	
	  "source": {
	    "type": "SqlSource",
	    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
	  },

Cette requête lit les données de « MyTable » comprises dans la plage de durée de la tranche. La réexécution de cette tranche garantit toujours un tel comportement.

Dans d’autres cas, vous pourriez souhaiter lire l’intégralité de la table (par exemple, dans le cas d’un déplacement unique). Vous devrez alors définir sqlReaderQuery comme suit :

	
	"source": {
	            "type": "SqlSource",
	            "sqlReaderQuery": "select * from MyTable"
	          },
	

<!---HONumber=AcomDC_0914_2016-->
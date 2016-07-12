<properties 
	pageTitle="Azure Data Factory - Forum aux Questions" 
	description="Forum aux Questions sur Azure Data Factory." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/28/2016" 
	ms.author="spelluru"/>

# Azure Data Factory - Forum aux Questions

## Questions générales

### qu'est-ce qu'Azure Data Factory ?

Data Factory est un service d’intégration de données dans le cloud qui **automatise le déplacement et la transformation des données**. À la manière d’une usine de fabrication, qui utilise des machines visant à transformer des matières premières en produits manufacturés, Data Factory orchestre des services existants qui collectent des données brutes et les transforment en informations prêtes à l'emploi.
 
Le service Data Factory vous permet de créer des flux de travail pilotés par les données pour déplacer des données entre des magasins de données locaux et cloud et traiter/transformer des données avec des services de calcul comme Azure HDInsight et Azure Data Lake Analytics. Après avoir créé un pipeline qui exécute l'action dont vous avez besoin, vous pouvez planifier son exécution périodique (toutes les heures, tous les jours, toutes les semaines, etc.).

Consultez les sections [Vue d'ensemble et Concepts clés](data-factory-introduction.md) pour plus de détails.

### où puis-je trouver des informations de tarification pour Azure Data Factory ?

Consultez la [page de tarification de Data Factory][adf-pricing-details] pour plus d'informations sur la tarification d'Azure Data Factory.

### comment prendre en main Azure Data Factory ?

- Pour obtenir une vue d'ensemble d'Azure Data Factory, consultez [Présentation d'Azure Data Factory](data-factory-introduction.md).
- Pour obtenir un didacticiel expliquant comment **copier/déplacer des données** au moyen de l’activité de copie, consultez [Copie de données d’Azure Blob Storage vers une base de données SQL Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
- Pour obtenir un didacticiel sur la façon de **transformer des données** au moyen de l’activité Hive de HDInsight, Consultez [Traiter des données en exécutant un script Hive sur un cluster Hadoop](data-factory-build-your-first-pipeline.md).
  
### dans quelle région sera disponible Data Factory ?
Data Factory est disponible dans les régions **Ouest des États-Unis** et **Europe du Nord**. Les services de calcul et de stockage utilisés par les fabriques de données peuvent être proposés dans d'autres régions. Consultez [Régions prises en charge](data-factory-introduction.md#supported-regions).
 
### quelles sont les limites du nombre de fabriques de données/pipelines/activités/jeux de données ?
 
Consultez la section **Limites d’Azure Data Factory** de l’article [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md#data-factory-limits).

### Qu’est-il possible de faire avec le service Azure Data Factory en termes de création/développement ?

Vous pouvez créer des fabriques de données à l'aide de l'une des solutions suivantes :

- **Portail Azure** : les panneaux du service Data Factory dans le portail Azure fournissent une interface utilisateur complète qui vous permet de créer des fabriques de données et des services liés. **Data Factory Editor**, qui fait également partie du portail, vous permet de créer facilement des services liés, des tables, des jeux de données et des pipelines en spécifiant des définitions JSON pour ces artefacts. Consultez [Créer votre premier pipeline de données en utilisant le portail Azure](data-factory-build-your-first-pipeline-using-editor.md) pour obtenir un exemple d’utilisation du portail et de l’éditeur pour créer et déployer une fabrique de données.

- **Visual Studio** : vous pouvez utiliser Visual Studio pour créer une fabrique de données Azure. Pour plus d’informations, consultez [Créer votre premier pipeline de données à l’aide de Visual Studio](data-factory-build-your-first-pipeline-using-vs.md).

- **Azure PowerShell** : consultez [Créer et surveiller Azure Data Factory à l’aide d’Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) pour obtenir un didacticiel/procédure pas à pas pour créer une fabrique de données à l’aide de PowerShell. Consultez [Informations de référence sur les applets de commande Data Factory][adf-powershell-reference] dans la bibliothèque MSDN pour obtenir une documentation complète sur les applets de commande Data Factory.
   
- **Bibliothèque de classes .NET** Vous pouvez créer par programmation des fabriques de données à l'aide du Kit de développement logiciel (SDK) Data Factory .NET. Consultez [Créer, surveiller et gérer des fabriques de données à l'aide du Kit de développement logiciel (SDK) .NET](data-factory-create-data-factories-programmatically.md) pour découvrir comment créer une fabrique de données à l'aide du Kit de développement logiciel (SDK) .NET. Consultez [Informations de référence sur la bibliothèque de classes Data Factory][msdn-class-library-reference] pour obtenir une documentation complète sur le Kit de développement logiciel (SDK) Data Factory .NET.

- **API REST** Vous pouvez également utiliser l'API REST exposée par le service Azure Data Factory pour créer et déployer des fabriques de données. Consultez [Informations de référence sur l'API REST Data Factory][msdn-rest-api-reference] pour obtenir une documentation complète de l'API REST Data Factory.
 
- **Modèle Azure Resource Manager** Consultez [Didacticiel : concevoir votre première fabrique de données Azure à l’aide du modèle Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md) pour plus d’informations.

### Puis-je renommer une fabrique de données ?
Non. Tout comme les autres ressources Azure, le nom d'une fabrique de données Azure ne peut pas être modifié.

### Puis-je déplacer une fabrique de données d’un abonnement Azure à un autre ? 
Oui. Utilisez le bouton **Déplacer** sur votre panneau Data Factory comme indiqué ci-dessous :

![Déplacer la fabrique de données](media/data-factory-faq/move-data-factory.png)

## Activités - Forum Aux Questions
### Quels sont les différents types d’activités que vous pouvez utiliser dans un pipeline Data Factory ? 

- [Activités de déplacement des données](data-factory-data-movement-activities.md) pour déplacer les données.
- [Activités de transformation des données](data-factory-data-transformation-activities.md) pour traiter/transformer les données.

### Quand une activité s'exécute-t-elle ?
Les paramètres de configuration de la **disponibilité** présents dans la table de données de sortie déterminent quand l'activité doit être exécutée. L'activité vérifie si toutes les dépendances de données d'entrée sont satisfaites (par exemple, l’état **Prêt**) avant de s'exécuter si des jeux de données d’entrée sont spécifiés.

## Activité de copie - Forum Aux Questions
### Est-il préférable d'avoir un pipeline avec plusieurs activités ou un pipeline distinct pour chaque activité ? 
Les pipelines sont censés regrouper des activités connexes. Logiquement, vous pouvez conserver les activités dans un seul pipeline si les tables qui les relient ne sont pas utilisées par d'autres activités extérieures au pipeline. De cette façon, vous n'aurez pas besoin de relier les périodes actives du pipeline pour qu'elles s'accordent les unes avec les autres. En outre, l'intégrité des données dans les tables internes au pipeline est mieux préservée lors de la mise à jour du pipeline. La mise à jour d'un pipeline arrête toutes les activités du pipeline, les supprime et les crée de nouveau. En termes de création, il peut être plus facile de voir le flux de données au sein des activités connexes dans un seul fichier JSON pour le pipeline.

### Où est effectuée l’opération de copie ? 

Consultez la section relative au [déplacement des données disponibles à l’échelle mondiale](data-factory-data-movement-activities.md#global) pour plus d’informations. En bref, lorsqu’il s’agit d’un magasin de données local, l’opération de copie est effectuée par la passerelle de gestion des données dans votre environnement local. Lorsque le déplacement des données se fait entre deux magasins cloud, l’opération de copie est effectuée dans la région la plus proche de l’emplacement du récepteur dans la même zone géographique.


## Activité de HDInsight - Forum Aux Questions

### Quelles sont les régions prises en charge par HDInsight ?

Consultez la page [Tarification relative à HDInsight][hdinsight-supported-regions] ou la section Disponibilité géographique dans l'article suivant.

### quelle est la région utilisée par un cluster HDInsight à la demande ?

Le cluster HDInsight à la demande est créé dans la même région que le stockage que vous avez spécifié pour être utilisé avec le cluster.

### Comment associer des comptes de stockage supplémentaires à votre cluster HDInsight ?

Si vous utilisez votre propre cluster HDInsight (BYOC, Bring Your Own Cluster), consultez les rubriques suivantes :

- [Utilisation d'un cluster HDInsight avec des comptes de stockage et des metastores secondaires][hdinsight-alternate-storage]
- [Utilisation de comptes de stockage supplémentaires avec Hive HDInsight][hdinsight-alternate-storage-2]

Si vous utilisez un cluster à la demande créé par le service Data Factory, vous devez spécifier les comptes de stockage supplémentaires pour le service lié HDInsight afin que le service Data Factory puisse les inscrire en votre nom. Dans la définition JSON pour le service lié à la demande, utilisez la propriété **additionalLinkedServiceNames** pour spécifier les comptes de stockage secondaires, comme indiqué dans l'extrait de code JSON suivant :
 
	{
	    "name": "MyHDInsightOnDemandLinkedService",
	    "properties":
	    {
	        "type": "HDInsightOnDemandLinkedService",
			"typeProperties": {
	        	"clusterSize": 1,
		        "timeToLive": "00:01:00",
		        "linkedServiceName": "LinkedService-SampleData",
		        "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ] 
			}
	    }
	} 

Dans l'exemple ci-dessus, otherLinkedServiceName1 et otherLinkedServiceName2 représentent les services liés dont les définitions contiennent des informations d'identification nécessaires au cluster HDInsight pour accéder aux comptes de stockage secondaires.

## Tranches - Forum Aux Questions

### Pourquoi mes tranches d’entrée ne sont-elles pas à l’état Prêt ?  
L’une des erreurs les plus courantes est de ne pas définir la propriété **external** sur **true** dans le jeu de données d’entrée quand les données d’entrée sont externes à la fabrique de données (c’est-à-dire qu’elles n’ont pas été générées par celle-ci).

Dans l’exemple suivant, vous devez uniquement définir **external** sur true pour **dataset1**.

**DataFactory1** Pipeline 1: dataset1 -> activity1 -> dataset2 -> activity2 -> dataset3 Pipeline 2: dataset3-> activity3 -> dataset4

Si vous avez une autre fabrique de données avec un pipeline qui prend dataset4 (généré par le pipeline 2 dans DataFactory1), vous devez marquer dataset4 comme un jeu de données externe, car il a été généré par une autre fabrique de données (DataFactory1, et non DataFactory2).

**DataFactory2** Pipeline 1: dataset4->activity4->dataset5

Si la propriété « external » est définie correctement, vérifiez que les données d’entrée se trouvent bien dans l’emplacement spécifié dans la définition du jeu de données d’entrée.

### Comment choisir une heure d’exécution autre que minuit pour une tranche qui est générée quotidiennement ?
Utilisez la propriété **offset** pour spécifier l’heure à laquelle la tranche doit être générée. Pour plus d’informations sur cette propriété, consultez la section [Disponibilité du jeu de données](data-factory-create-datasets.md#Availability). Voici un exemple rapide :

	"availability":
	{
	    "frequency": "Day",
	    "interval": 1,
	    "offset": "06:00:00"
	}

Les tranches quotidiennes commencent à **6 h** au lieu de minuit, qui est l’heure par défaut.

### Comment puis-je réexécuter une tranche ?
Vous pouvez réexécuter une tranche de l'une des manières suivantes :

- Utilisez l’application Surveiller et gérer pour réexécuter une fenêtre d’activité ou une tranche. Pour obtenir des instructions, consultez [Réexécuter les fenêtres d’activité sélectionnées](data-factory-monitor-manage-app.md#re-run-selected-activity-windows).
- Cliquez sur **Exécuter** dans la barre de commandes du panneau **TRANCHE DE DONNÉES** de la tranche, dans le portail.
- Exécutez l’applet de commande **Set-AzureRmDataFactorySliceStatus** en ayant affecté la valeur **En attente** à l’état de la tranche.
	
		Set-AzureRmDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00" 

Consultez [Set-AzureRmDataFactorySliceStatus][set-azure-datafactory-slice-status] pour plus d’informations sur l’applet de commande.

### Combien de temps dure le traitement d'une tranche ?
Utilisez l’Explorateur de fenêtres d’activité dans l’application Surveiller et gérer pour connaître la durée du traitement d’une tranche de données. Pour plus d’informations, consultez [Explorateur de fenêtres d’activité](data-factory-monitor-manage-app.md#activity-window-explorer).

Vous pouvez également faire ce qui suit dans le portail Azure :

1. Cliquez sur la vignette **Jeux de données** dans le panneau **DATA FACTORY** de votre fabrique de données.
2. Cliquez sur le jeu de données spécifique dans le panneau **Jeux de données**.
3. Sélectionnez la tranche qui vous intéresse dans la liste **Tranches récentes** située dans le panneau **TABLE**.
4. Cliquez sur l'exécution dans la liste **Exécutions d'activité** située dans le panneau **TRANCHE DE DONNÉES**.
5. Cliquez sur la vignette **Propriétés** dans le panneau **DÉTAILS DE L'EXÉCUTION D'ACTIVITÉ**.
6. Une valeur doit apparaître dans le champ **DURÉE**. Il s'agit du temps nécessaire pour traiter la tranche.

### Comment arrêter une tranche en cours d'exécution ?
Si vous devez arrêter l’exécution du pipeline, vous pouvez utiliser l’applet de commande [Suspend-AzureRmDataFactoryPipeline](https://msdn.microsoft.com/library/mt603721.aspx). Actuellement, l'interruption du pipeline n'arrête pas les exécutions de tranche en cours. Une fois que les exécutions en cours sont terminées, aucune tranche supplémentaire n'est récupérée.

Si vous voulez vraiment arrêter immédiatement toutes les exécutions, le seul moyen est de supprimer le pipeline et de le recréer. Si vous choisissez de supprimer le pipeline, il est INUTILE de supprimer les tables et les services liés qu'il utilise.


[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx
[azure-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: https://msdn.microsoft.com/library/mt603522.aspx

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
 

<!---HONumber=AcomDC_0629_2016-->
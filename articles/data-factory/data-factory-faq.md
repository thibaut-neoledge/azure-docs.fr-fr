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
	ms.date="04/23/2015" 
	ms.author="spelluru"/>

# Azure Data Factory - Forum aux Questions

## Questions générales

### Q: quelle est la fabrique de données Azure ?

Data Factory est un service entièrement géré permettant aux développeurs de composer des services de stockage, de déplacement et de traitement des données dans des pipelines de données à haute disponibilité et à tolérance de pannes. Data Factory fonctionne avec du stockage de données local ou dans le cloud. Un pipeline est un ensemble d'entrées de données, d'activités de traitement et de sorties de données qui est défini avec des scripts simples JSON et activé via des commandes PowerShell. Une fois activé, Data Factory gère et planifie les pipelines à exécuter sur HDInsight (Hadoop) avec des options pour la gestion de cluster automatique, pour l'utilisateur. Data Factory fournit également une expérience visuelle pour la surveillance et la gestion via le portail Azure en version préliminaire afin de surveiller tous les pipelines avec des informations opérationnelles et de contrôle d'intégrité de service étoffées dans un tableau de bord.
 
### Q: quel défi de client est Data Factory résoudre ?

Azure Data Factory permet d'exploiter avec agilité les services divers de stockage, de traitement et de déplacement des données dans un stockage relationnel classique comportant des données non structurées, avec des fonctionnalités de contrôle et de surveillance d'un service entièrement géré.

### Q: quels sont le public ciblé par la fabrique de données ?


- Développeurs de données : ils sont responsables de la création de services d'intégration entre Hadoop et d'autres systèmes :
	- ils doivent suivre le rythme et intégrer des données dans un paysage en évolution et croissance constantes ;
	- ils doivent écrire du code personnalisé pour la production d'informations, qui s'avère coûteux et difficile à mettre à jour, n'offre pas une haute disponibilité ou une tolérance aux pannes ;

- Professionnels de l'informatique : ils cherchent à intégrer des données plus variées dans leur infrastructure informatique :
	- ils doivent consulter toutes les données d'une organisation pour obtenir un aperçu éclairé de l'activité ;
	- ils doivent gérer les ressources de calcul et de stockage pour équilibrer les coûts et la mise à l'échelle au niveau local et du cloud ;
	- ils doivent ajouter rapidement des sources variées et effectuer un traitement pour répondre aux nouveaux besoins des entreprises, tout en conservant la visibilité sur toutes les ressources de calcul et de stockage.

###  Q: où puis-je trouver la tarification détails de la fabrique de données Azure ?

Voir [page de tarification de la fabrique de données][adf-pricing-details] pour plus d'informations sur la tarification de la fabrique de données Azure.

### Q. Comment prendre en main Azure Data Factory ?

- Pour une vue d'ensemble de la fabrique de données Azure, consultez [Introduction à la fabrique de données Azure][adf-introduction].
- Pour obtenir un didacticiel rapide, consultez [prise en main Azure Data Factory][adfgetstarted].
- Pour une documentation complète, consultez [documentation Azure Data Factory][adf-documentation-landingpage].

  
### Q: comment les clients accèdent aux données fabrique ?

Les clients pouvant accéder à la fabrique de données à l'aide du [portail Azure Preview][azure-preview-portal].

### Q: quelle est la disponibilité de la région de la fabrique de données ?

En version publique, Data Factory sera uniquement disponible dans l'Ouest des États-Unis. Les services de calcul et de stockage utilisés par les fabriques de données peuvent être situés dans d'autres régions.
 
### Q: quelles sont les limites sur le nombre de données fabriques/pipelines/activités/datasets ? 


- Nombre de fabriques de données dans un abonnement : 50
- Nombre de pipelines dans une fabrique de données : 100
- Nombre d'activités au sein d'un pipeline : 10
- Nombre de datasets à l'aide d'une fabrique de données : 100

### Q: quelle est l'expérience de création/développement service Factory de données Azure ?

Vous pouvez créer des fabriques de données à l'aide d'un des dispositifs suivants :

- **Portail azure Preview**. Les panneaux du service Data Factory dans le portail Azure en version préliminaire fournissent une interface utilisateur complète qui vous permet de créer des fabriques de données et des services liés. Le **données fabrique éditeur**, qui fait également partie du portail, vous permet de créer facilement des services liés, les tables, les jeux de données et les pipelines en spécifiant des définitions de JSON pour ces artefacts. Voir [données fabrique éditeur][data-factory-editor] pour une vue d'ensemble de l'éditeur et [prise en main Data Factory][datafactory-getstarted] pour obtenir un exemple d'utilisation de l'éditeur de portail/pour créer et déployer une fabrique de données.   
- **Azure PowerShell** Si vous êtes familiarisé avec PowerShell et que vous le préférez à l'interface utilisateur du portail, vous pouvez utiliser les applets de commande Azure Data Factory fournies dans le cadre d'Azure PowerShell pour créer et déployer des fabriques de données. Voir [Create et analyse fabrique de données Azure à l'aide d'Azure PowerShell][create-data-factory-using-powershell] pour obtenir un exemple simple et [didacticiel : déplacer et traiter des fichiers journaux à l'aide de la fabrique de données][adf-tutorial] pour obtenir un exemple d'utilisation de PowerShell avancé cmdles pour créer une annonce déployer une fabrique de données. Voir [référence d'applet de commande données fabrique][adf-powershell-reference] contenu dans MSDN Library pour une documentation complète des applets de commande de fabrique de données.  
- **Bibliothèque de classes .NET**. Vous pouvez créer par programmation des fabriques de données à l'aide du Kit de développement logiciel (SDK) Data Factory .NET. Voir [créer, surveiller et gérer des fabriques de données à l'aide du Kit de développement .NET][create-factory-using-dotnet-sdk] pour une procédure pas à pas de création d'une fabrique de données à l'aide du Kit de développement .NET. Voir [référence de bibliothèque de classes de fabrique données][msdn-class-library-reference] pour une documentation complète de données fabrique .NET SDK.  
- **API REST**. Vous pouvez également utiliser l'API REST exposée par le service Azure Data Factory pour créer et déployer des fabriques de données. Voir [données fabrique de référence de l'API REST][msdn-rest-api-reference] pour une documentation complète de l'API REST de fabrique de données. 

## Activités - FAQ
### Q: quelles sont les sources de données prises en charge et les activités ?

- **Prise en charge des sources de données :** Azure Storage (objets Blob et Tables), SQL Server, base de données SQL Azure, système de fichiers, base de données Oracle.
- **Prise en charge des activités :**: activité de copie (sur site au nuage et nuage local), HDInsight activité (Pig, Hive, MapReduce, Hadoop transformations de diffusion en continu), Azure Machine Learning lot notation activité, activité de la procédure stockée et c# des activités personnalisées.

### Lorsqu'une activité s'exécute-t-il ?
Le **disponibilité** de configuration dans la table de données de sortie détermine lorsque l'activité est exécutée. L'activité vérifie si toutes les dépendances de données d'entrée sont satisfaites (par exemple, **prêt** état) avant le démarrage en cours d'exécution.

## Copier une activité - FAQ
### Q: quelles régions sont pris en charge par l'activité de copie ?

Activité de copie prend en charge la copie des données dans les régions suivantes : est des États-Unis 2 est des États-Unis, ouest des États-Unis, États-Unis, Amérique du Nord, Amérique du Sud, Europe du Nord, Europe de l'ouest et Asie du Sud-est.

Copie de données dans les autres régions est également pris en charge, à l'aide de l'une des régions ci-dessus pour le routage des données. L'opération de copie est mesurée en fonction de la région via laquelle les données sont acheminées.

Région de destination de copie | Région utilisé pour le routage
-------------------------- | -----------------------
Asie de l'Est | Asie du Sud-Est
Est du Japon | Ouest des États-Unis
Ouest du Japon | Ouest des États-Unis
Sud du Brésil | Est des États-Unis 2

### Comment puis-je copier à plusieurs tables de sortie ?
Vous pouvez avoir plusieurs tables de sortie dans un pipeline, comme illustré dans l'exemple suivant :

	"outputs":  [ 
		{ "name": “outputtable1” }, 
		{ "name": “outputtable2” }  
	],
 
### Est-ce mieux d'avoir un pipeline avec plusieurs activités ou un pipeline distinct pour chaque activité ? 
Les pipelines sont censés pour regrouper des activités connexes. Logiquement, vous pouvez conserver les activités dans un seul pipeline si les tables qui les relient ne sont pas utilisés par toute autre activité en dehors du pipeline. De cette façon, vous souhaitez inutile de périodes active du pipeline chaîne afin qu'ils s'alignent avec eux. En outre, l'intégrité des données dans les tables internes au pipeline est mieux conservée lors de la mise à jour le pipeline. Mise à jour de pipeline essentiellement arrête toutes les activités dans le pipeline, les supprime et les crée à nouveau. À partir de la création du point de vue, il peut être plus facile à voir le flux de données dans les activités connexes dans un seul fichier JSON pour le pipeline.

## Activité de HDInsight - FAQ

### Q: quelles régions sont pris en charge par HDInsight ?

Consultez la section disponibilité géographique dans l'article suivant : ou [HDInsight tarification][hdinsight-supported-regions].

### Q: quelle est la région est utilisée par un cluster de HDInsight à la demande ?

Le cluster HDInsight à la demande est créé dans la même région que le stockage que vous avez spécifié pour être utilisé avec le cluster.

### Q: comment associer des comptes de stockage supplémentaires pour votre cluster HDInsight ?

Si vous utilisez votre propre HDInsight Cluster (BYOC - mettre de votre propre Cluster), consultez les rubriques suivantes :

- [À l'aide d'un Cluster HDInsight avec Metastores et les comptes de stockage de remplacement][hdinsight-alternate-storage]
- [Utiliser des comptes de stockage supplémentaire avec Hive HDInsight][hdinsight-alternate-storage-2]

Si vous utilisez un cluster à la demande qui est créé par le service de fabrique de données, vous devez spécifier les comptes de stockage supplémentaire pour le HDInsight lié service afin que le service de données fabrique peut vous inscrire à votre place. Dans la définition de JSON pour le service lié à la demande, utilisez **additionalLinkedServiceNames** propriété pour spécifier les comptes de stockage de remplacement comme indiqué dans l'extrait JSON suivant :
 
	{
	    "name": "MyHDInsightOnDemandLinkedService",
	    "properties":
	    {
	        "type": "HDInsightOnDemandLinkedService",
	        "clusterSize": 1,
	        "timeToLive": "00:01:00",
	        "linkedServiceName": "LinkedService-SampleData",
	        "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ] 
	    }
	} 

Dans l'exemple ci-dessus, otherLinkedServiceName1 et otherLinkedServiceName2 représentent les services liés dont les définitions contiennent des informations d'identification du cluster HDInsight pour accéder à des comptes de stockage de remplacement.

## Activité de la procédure stockée - FAQ
### Quelles sources de données prises en charge par l'activité de procédure stockée ?
L'activité de la procédure stockée prend en charge uniquement les base de données de SQL Azure à ce stade.

## Secteurs - FAQ

### Comment puis-je réexécuter une tranche de données ?
Vous pouvez réexécuter une tranche de l'une des manières suivantes :

- Cliquez sur **exécuter** dans la barre de commandes sur le **tranche de données** lame pour le secteur dans le portail. 
- Exécutez **Set-AzureDataFactorySliceStatus** applet de commande avec l'état la valeur **PendingExecution** du secteur.   
	
		Set-AzureDataFactorySliceStatus -Status PendingExecution -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00" 

Voir [Set-AzureDataFactorySliceStatus][set-azure-datafactory-slice-status] pour plus d'informations sur l'applet de commande.

### Combien de temps a-t-il fallu pour traiter une tranche de données ?
1. Cliquez sur **Datasets** vignette sur la **DATA FACTORY** lame pour votre fabrique de données.
2. Cliquez sur le groupe de données spécifique dans le **Datasets** lame.
3. Sélectionner la tranche qui vous intéressent dans le **tranches récentes** liste sur le **TABLE** lame.
4. Cliquez sur l'activité à exécuter à partir de la **activité** liste sur le **tranche de données** lame. 
5. Cliquez sur **propriétés** vignette sur la **Détails de l'exécution activité** lame. 
6. Vous devez voir les **durée** champ avec une valeur. Il s'agit du temps nécessaire pour traiter la tranche.   

### Comment arrêter une tranche de données en cours d'exécution ?
Si vous avez besoin d'arrêter le pipeline de l'exécution, vous pouvez utiliser [Suspend-AzureDataFactoryPipeline](https://msdn.microsoft.com/library/dn834939.aspx) applet de commande. Actuellement, la suspension du pipeline n'arrête pas les exécutions de tranche sont en cours. Une fois que les exécutions en cours terminé, aucune tranche supplémentaire n'est récupéré.

Si vous voulez vraiment arrêter toutes les exécutions immédiatement, la seule façon serait le pipeline et créer de nouveau. Si vous choisissez de supprimer le pipeline, il est inutile de le supprimer des tables et des services liés utilisés par le pipeline.



[image-rerun-slice]: ./media/data-factory-faq/rerun-slice.png

[adfgetstarted]: data-factory-get-started.md
[adf-introduction]: data-factory-introduction.md
[adf-troubleshoot]: data-factory-troubleshoot.md
[data-factory-editor]: data-factory-editor.md
[datafactory-getstarted]: data-factory-get-started.md
[create-data-factory-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-tutorial]: data-factory-tutorial.md
[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx
[adf-documentation-landingpage]: http://go.microsoft.com/fwlink/?LinkId=516909
[azure-preview-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: https://msdn.microsoft.com/library/azure/dn835095.aspx

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx

<!---HONumber=GIT-SubDir--> 
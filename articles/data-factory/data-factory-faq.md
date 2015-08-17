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
	ms.date="06/16/2015" 
	ms.author="spelluru"/>


# Azure Data Factory - Forum aux Questions

## Questions générales

### Q : qu'est-ce qu'Azure Data Factory ?

Data Factory est un service d’intégration de données dans le cloud qui gère et automatise le déplacement et la transformation des données. À la manière d’une usine de fabrication, qui utilise des machines visant à transformer des matières premières en produits manufacturés, Data Factory orchestre des services existants qui collectent des données brutes et les transforment en informations prêtes à l'emploi.

Data Factory fonctionne pour des sources de données locales et dans le cloud et avec les SaaS pour réceptionner, préparer, transformer, analyser et publier vos données. Utilisez Data Factory pour constituer des services dans des pipelines de flux de données gérés afin de transformer vos données à l'aide de services tels qu’[Azure HDInsight (Hadoop)](http://azure.microsoft.com/documentation/services/hdinsight/) et [Azure Batch](http://azure.microsoft.com/documentation/services/batch/) pour vos besoins en calcul de Big Data, et avec [Azure Machine Learning](http://azure.microsoft.com/documentation/services/machine-learning/) pour configurer vos solutions d'analyse. Car une simple vue tabulaire pour surveiller vos projets ne suffit plus, utilisez les affichages élaborés de Data Factory et visualisez d’un seul coup d’œil le lignage et les dépendances de vos pipelines de données. Analysez tous vos pipelines de flux de données à partir d'une vue unifiée pour identifier les problèmes et configurer des alertes d'analyse en toute simplicité.

Consultez les sections [Vue d'ensemble et Concepts clés](data-factory-introduction.md) pour plus de détails.
 
### Q : Quel défi le client peut-il relever avec Data Factory ?

Azure Data Factory permet d'exploiter avec agilité les services divers de stockage, de traitement et de déplacement des données dans un stockage relationnel classique comportant des données non structurées, avec des fonctionnalités de contrôle et de surveillance d'un service entièrement géré.

### Q : à qui s'adresse Data Factory ?


- Développeurs de données chargés de créer des services d'intégration entre Hadoop et d'autres systèmes :
	- Ils doivent rester à la page et s'adapter à l'évolution et à la croissance constantes des données.
	- Ils doivent écrire du code personnalisé pour produire des informations, ce qui s'avère coûteux, difficile à mettre à jour et sans haute disponibilité ou tolérance aux pannes.

- Professionnels de l'informatique qui cherchent à intégrer des données plus variées dans leur infrastructure informatique :
	- Ils doivent consulter toutes les données d'une organisation pour obtenir un aperçu éclairé de l'activité.
	- Ils doivent gérer les ressources de calcul et de stockage pour équilibrer les coûts et la mise à l'échelle au niveau local et cloud.
	- Ils doivent ajouter rapidement des sources variées et se charger de répondre aux nouveaux besoins des entreprises, tout en conservant la visibilité sur toutes les ressources de calcul et de stockage.

###  Q : où puis-je trouver des informations de tarification sur Azure Data Factory ?

Consultez la [page de tarification de Data Factory][adf-pricing-details] pour plus d'informations sur la tarification d'Azure Data Factory.

### Q : comment prendre en main Azure Data Factory ?

- Pour obtenir une vue d'ensemble d'Azure Data Factory, consultez [Présentation d'Azure Data Factory][adf-introduction].
- Pour accéder à un didacticiel rapide, consultez [Prise en main d'Azure Data Factory][adfgetstarted].
- Pour obtenir des informations complètes, consultez la [documentation Azure Data Factory][adf-documentation-landingpage].

  
### Q : Comment les clients accèdent-ils à Data Factory ?

Les clients peuvent accéder à Data Factory via le [portail Azure en version préliminaire][azure-preview-portal].

### Q : Dans quelle région sera disponible Data Factory ?

Data Factory est disponible dans l'Ouest des États-Unis et en Europe du Nord. Les services de calcul et de stockage utilisés par les fabriques de données peuvent être proposés dans d'autres régions.
 
### Q : quelles sont les limites quant au nombre de fabriques de données/pipelines/activités/jeux de données ?
 
Consultez la section **Limites d’Azure Data Factory** de l'article [Abonnement Azure et limites de service, quotas et contraintes](azure-subscription-service-limits.md/#data-factory-limits).


### Q : qu'est-il possible de faire avec le service Azure Data Factory en termes de création/développement ?

Vous pouvez créer des fabriques de données à l'aide de l'une des solutions suivantes :

- **Portail Azure en version préliminaire** Les panneaux du service Data Factory dans le portail Azure en version préliminaire fournissent une interface utilisateur complète qui vous permet de créer des fabriques de données et des services liés. **Data Factory Editor**, qui fait également partie du portail, vous permet de créer facilement des services liés, des tables, des jeux de données et des pipelines en spécifiant des définitions JSON pour ces artefacts. Consultez [Data Factory Editor][data-factory-editor] pour obtenir une vue d'ensemble de l'éditeur et [Prise en main de Data Factory][datafactory-getstarted] pour obtenir un exemple d'utilisation du portail et de l'éditeur pour créer et déployer une fabrique de données.   
- **Azure PowerShell** Si vous êtes familiarisé avec PowerShell et que vous le préférez à l'interface utilisateur du portail, vous pouvez utiliser les applets de commande Azure Data Factory fournies dans le cadre d'Azure PowerShell pour créer et déployer des fabriques de données. Consultez [Créer et surveiller des fabriques de données Azure à l'aide d'Azure PowerShell][create-data-factory-using-powershell] pour obtenir un exemple simple et [Didacticiel : déplacement et traitement des fichiers journaux à l'aide d'Azure Data Factory][adf-tutorial] pour obtenir un exemple complet d'utilisation des applets de commande PowerShell pour créer et déployer une fabrique de données. Consultez [Informations de référence sur les applets de commande Data Factory][adf-powershell-reference] dans la bibliothèque MSDN pour obtenir une documentation complète sur les applets de commande Data Factory.  
- **Visual Studio**. Vous pouvez également utiliser Visual Studio pour créer, surveiller et gérer des fabriques de données par programme. Consultez l’article [Créer, surveiller et gérer des fabriques de données Azure à l'aide du Kit de développement logiciel (SDK) Data Factory .NET](data-factory-create-data-factories-programmatically) pour plus de détails.  
- **Bibliothèque de classes .NET** Vous pouvez créer par programmation des fabriques de données à l'aide du Kit de développement logiciel (SDK) Data Factory .NET. Consultez [Créer, surveiller et gérer des fabriques de données à l'aide du Kit de développement logiciel (SDK) .NET][create-factory-using-dotnet-sdk] pour découvrir comment créer une fabrique de données à l'aide du Kit de développement logiciel (SDK) .NET. Consultez [Informations de référence sur la bibliothèque de classes Data Factory][msdn-class-library-reference] pour obtenir une documentation complète sur le Kit de développement logiciel (SDK) Data Factory .NET.  
- **API REST** Vous pouvez également utiliser l'API REST exposée par le service Azure Data Factory pour créer et déployer des fabriques de données. Consultez [Informations de référence sur l'API REST Data Factory][msdn-rest-api-reference] pour obtenir une documentation complète de l'API REST Data Factory. 

### Q : puis-je renommer une fabrique de données ?
Non. Tout comme les autres ressources Azure, le nom d'une fabrique de données Azure ne peut pas être modifié.

## Activités - Forum Aux Questions
### Q : Quelles sont les sources de données et les activités prises en charge ?

Consultez les articles [Activités de déplacement des données](data-factory-data-movement-activities.md) et [Activités de transformation des données](data-factory-data-transformation-activities.md) pour plus de détails sur les sources de données et activités prises en charge.

### Quand une activité s'exécute-t-elle ?
Les paramètres de configuration de la **disponibilité** présents dans la table de données de sortie déterminent quand l'activité doit être exécutée. L'activité vérifie si toutes les dépendances de données d'entrée sont satisfaites (par exemple, état **prêt**) avant de s'exécuter.

## Activité de copie - Forum Aux Questions
### Q : quelles sont les régions prises en charge par l'activité de copie ?

L'activité de copie prend en charge la copie des données dans les régions suivantes : est des États-Unis, est des États-Unis 2, ouest des États-Unis, centre des États-Unis, centre-nord des États-Unis, centre-sud des États-Unis, Europe du Nord, Europe de l'Ouest, Asie du Sud-Est, est du Japon et sud du Brésil.

La copie de données est également prise en charge dans d'autres régions, en utilisant l'une des régions ci-dessus pour le routage des données. L'opération de copie est mesurée en fonction de la région via laquelle les données sont acheminées.

Région de destination de la copie | Région utilisée pour le routage
-------------------------- | -----------------------
Asie de l'Est | Asie du Sud-Est
Ouest du Japon | Est du Japon
 
### Est-il préférable d'avoir un pipeline avec plusieurs activités ou un pipeline distinct pour chaque activité ? 
Les pipelines sont censés regrouper des activités connexes. Logiquement, vous pouvez conserver les activités dans un seul pipeline si les tables qui les relient ne sont pas utilisées par d'autres activités extérieures au pipeline. De cette façon, vous n'aurez pas besoin de relier les périodes actives du pipeline pour qu'elles s'accordent les unes avec les autres. En outre, l'intégrité des données dans les tables internes au pipeline est mieux préservée lors de la mise à jour du pipeline. La mise à jour d'un pipeline arrête toutes les activités du pipeline, les supprime et les crée de nouveau. En termes de création, il peut être plus facile de voir le flux de données au sein des activités connexes dans un seul fichier JSON pour le pipeline.

## Activité de HDInsight - Forum Aux Questions

### Q : quelles sont les régions prises en charge par HDInsight ?

Consultez la page [Tarification relative à HDInsight][hdinsight-supported-regions] ou la section Disponibilité géographique dans l'article suivant.

### Q : Quelle est la région utilisée par un cluster HDInsight à la demande ?

Le cluster HDInsight à la demande est créé dans la même région que le stockage que vous avez spécifié pour être utilisé avec le cluster.

### Q : Comment associer des comptes de stockage supplémentaires à votre cluster HDInsight ?

Si vous utilisez votre propre cluster HDInsight (BYOC, Bring Your Own Cluster), consultez les rubriques suivantes :

- [Utilisation d'un cluster HDInsight avec des comptes de stockage et des metastores secondaires][hdinsight-alternate-storage]
- [Utilisation de comptes de stockage supplémentaires avec Hive HDInsight][hdinsight-alternate-storage-2]

Si vous utilisez un cluster à la demande créé par le service Data Factory, vous devez spécifier les comptes de stockage supplémentaires pour le service lié HDInsight afin que le service Data Factory puisse les inscrire en votre nom. Dans la définition JSON pour le service lié à la demande, utilisez la propriété **additionalLinkedServiceNames** pour spécifier les comptes de stockage secondaires, comme indiqué dans l'extrait de code JSON suivant :
 
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

Dans l'exemple ci-dessus, otherLinkedServiceName1 et otherLinkedServiceName2 représentent les services liés dont les définitions contiennent des informations d'identification nécessaires au cluster HDInsight pour accéder aux comptes de stockage secondaires.

## Activité de procédure stockée - Forum Aux Questions
### Quelles sources de données sont prises en charge par l'activité de procédure stockée ?
Actuellement, l'activité de procédure stockée prend uniquement en charge la base de données SQL Azure.

## Tranches - Forum Aux Questions

### Comment puis-je réexécuter une tranche ?
Vous pouvez réexécuter une tranche de l'une des manières suivantes :

- Cliquez sur **Exécuter** dans la barre de commandes du panneau **TRANCHE DE DONNÉES** de la tranche, dans le portail. 
- Exécutez l'applet de commande **Set-AzureDataFactorySliceStatus** en ayant affecté à l'état de la tranche la valeur **PendingExecution**.   
	
		Set-AzureDataFactorySliceStatus -Status PendingExecution -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00" 

Consultez [Set-AzureDataFactorySliceStatus][set-azure-datafactory-slice-status] pour plus d'informations sur l'applet de commande.

### Combien de temps dure le traitement d'une tranche ?
1. Cliquez sur la vignette **Jeux de données** dans le panneau **DATA FACTORY** de votre fabrique de données.
2. Cliquez sur le jeu de données spécifique dans le panneau **Jeux de données**.
3. Sélectionnez la tranche qui vous intéresse dans la liste **Tranches récentes** située dans le panneau **TABLE**.
4. Cliquez sur l'exécution dans la liste **Exécutions d'activité** située dans le panneau **TRANCHE DE DONNÉES**. 
5. Cliquez sur la vignette **Propriétés** dans le panneau **DÉTAILS DE L'EXÉCUTION D'ACTIVITÉ**. 
6. Une valeur doit apparaître dans le champ **DURÉE**. Il s'agit du temps nécessaire pour traiter la tranche.   

### Comment arrêter une tranche en cours d'exécution ?
Si vous devez arrêter l'exécution du pipeline, vous pouvez utiliser l'applet de commande [Suspend-AzureDataFactoryPipeline](https://msdn.microsoft.com/library/dn834939.aspx). Actuellement, l'interruption du pipeline n'arrête pas les exécutions de tranche en cours. Une fois que les exécutions en cours sont terminées, aucune tranche supplémentaire n'est récupérée.

Si vous voulez vraiment arrêter immédiatement toutes les exécutions, le seul moyen est de supprimer le pipeline et de le recréer. Si vous choisissez de supprimer le pipeline, il est INUTILE de supprimer les tables et les services liés qu'il utilise.



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
 

<!---HONumber=August15_HO6-->
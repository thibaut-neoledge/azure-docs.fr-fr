<properties title="Azure Data Factory - Release Notes" pageTitle="Data Factory - Notes de publication | Azure" description="Notes de publication de Data Factory" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/04/2014" ms.author="spelluru" />

# Notes de publication sur Azure Data Factory

## Notes pour la version du 11/12/2014 de Data Factory ##

### Nouvelles améliorations

- Intégration d'Azure Machine Learning
	- Cette version du service Azure Data Factory vous permet d'intégrer Azure Data Factory à Azure Machine Learning (ML) via **AzureMLLinkedService** et **AzureMLBatchScoringActivity**. Pour plus d'informations, consultez [Créer des pipelines prédictifs à l'aide d'Azure Data Factory et Azure Machine Learning][adf-azure-ml]. 
- Indication de l'état de la version de la passerelle
	- L'état " NewVersionAvailable " est affiché dans le portail Azure en version préliminaire et dans la sortie de l'applet de commande Get-AzureDataFactoryGateway, s'il existe une version de la passerelle plus récente que celle qui est actuellement installée. Vous pouvez ensuite suivre les indications du portail pour télécharger le nouveau fichier d'installation (.msi), et l'exécuter pour installer la passerelle la plus récente. Aucune configuration supplémentaire n'est nécessaire.

### Changements

- Suppression de JobsContainer dans HdInsightOnDemandLinkedService.
	- Dans la définition JSON de HDInsightOnDemandLinkedService, vous n'avez plus besoin de spécifier la propriété **jobsContainer**. Si la propriété est spécifiée pour un service lié à la demande, elle est ignorée. Vous pouvez supprimer la propriété de la définition JSON du service lié, et mettre à jour la définition du service lié à l'aide de l'applet de commande New-AzureDataFactoryLinkedService.
- Paramètres de configuration facultatifs pour HDInsightOnDemandLinkedService
	- Cette version introduit la prise en charge de quelques paramètres de configuration facultatifs pour HDInsightOnDemandLinked (cluster HDInsight à la demande). Pour plus d'informations, consultez les [propriétés de ClusterCreateParameters][on-demand-hdi-parameters].
- Suppression de l'emplacement de la passerelle
	- Durant la création d'une passerelle Azure Data Factory via le portail ou PowerShell (New-AzureDataFactoryGateway), vous n'avez plus besoin de spécifier l'emplacement de la passerelle. La région de la fabrique de données est héritée. De même, pour configurer un service SQL Server lié à l'aide de JSON, la propriété " gatewayLocation " n'est plus nécessaire. Le Kit de développement logiciel (SDK) Data Factory .NET est également mis à jour pour refléter ces changements.
	- Si vous utilisez une ancienne version du Kit de développement logiciel (SDK) et d'Azure PowerShell, vous devez toujours fournir le paramètre d'emplacement.
 
     

#### Dernières modifications
	
- CustomActivity devient DotNetActivity
	- L'interface **ICustomActivity** est renommée en **IDotNetActivity**. Vous devrez mettre à jour les packages NuGet Data Factory et remplacer ICustomActivity par IDotNetActivity dans le code source de votre activité personnalisée.  
	- Vous devez changer le type d'activité personnalisée dans la définition JSON de votre activité personnalisée en remplaçant **CustomActivity** par **DotNetActivity**. 
	- Les classes **CustomActivity** et **CustomActivityProperties** ont été renommées en **DotNetActivity** et **DotNetActivityProperties** avec le même jeu de propriétés.

		Si vous utilisez l'ancienne version du Kit de développement logiciel (SDK) et d'Azure PowerShell, vous pouvez continuer à utiliser CustomActivity au lieu de DotNetActivity.
    
  		Consultez [Utiliser des activités personnalisées dans un pipeline Azure Data Factory][adf-custom-activities] pour suivre une procédure pas à pas permettant de créer une activité personnalisée et de l'utiliser dans un pipeline Azure Data Factory.  

[adf-azure-ml]: ../data-factory-create-predictive-pipelines
[adf-custom-activities]: ../data-factory-use-custom-activities
[on-demand-hdi-parameters]: http://msdn.microsoft.com/fr-fr/library/microsoft.windowsazure.management.hdinsight.clustercreateparameters_properties.aspx



<!--HONumber=35.2-->

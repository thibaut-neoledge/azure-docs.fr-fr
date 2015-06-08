<properties 
	pageTitle="Data Factory - Notes de publication | Azure" 
	description="Data Factory release notes" 
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
	ms.date="04/10/2015" 
	ms.author="spelluru"/>

# Notes de publication sur Azure Data Factory

## Notes de version 10/04/2015 de fabrique de données
Vous verrez la **récemment mis à jour les tranches** et **Échec récemment tranches** listes sur le **TABLE** lame maintenant. Ces listes sont triés par l'heure de mise à jour de la tranche. L'heure de mise à jour d'une tranche de données est modifié dans les situations suivantes.

-  Vous mettre à jour l'état de la coupe manuellement, par exemple, à l'aide de la **Set-AzureDataFactorySliceStatus** (ou) en cliquant sur **exécuter** sur la **tranche** lame pour la tranche.
-  La tranche modifie l'état en raison d'une exécution (par exemple, une exécution démarré, une exécution s'est terminée et a échoué, une exécution s'est terminée et réussie, etc.).

Cliquez sur le titre des listes ou des **... (points de suspension)** Pour consulter la liste supérieure des tranches. Cliquez sur **filtre** sur la barre d'outils pour filtrer les secteurs.
 
Vous pouvez toujours afficher les tranches triés par les temps de coupe en cliquant sur **tranches de données (par heure de tranche)** vignette. Les tranches de ces collections sont classés par temps de tranche. Par exemple, dans le cas d'un planning, les tranches serait: - 4/4/2015 5 pm en cours - 4/4/2015 4 pm Succeeded - 3 de 4/4/2015 pm a échoué

Mais, si une tranche antérieure est relancée, il ne serait pas apparaître en haut de cette liste, même si c'est probablement ce que l'utilisateur n'est plus intéressé.

## Notes de version de 31/3/2015 de fabrique de données
- Mise à jour **passerelle de gestion des données** package d'installation a été publié sur [Microsoft Download Center][adf-gateway-download].
- Copie à partir de **local système de fichiers sur Azure blob** est maintenant pris en charge. Consultez les rubriques suivantes pour plus d'informations.
	-  [Système de fichiers liés de Service sur site](https://msdn.microsoft.com/library/dn930836.aspx)
	-  [Propriétés OnPremisesFileSystemLocation dans un tableau JSON](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem)
	-  [Prise en charge de Sources et récepteurs](https://msdn.microsoft.com/library/dn894007.aspx). Consultez la matrice de la copie mise à jour et **FileSystemSource** propriétés. 
-  Copie à partir de **base de données Oracle sur site pour les objets blob Azure** est maintenant pris en charge. Consultez les rubriques suivantes pour plus d'informations. 
	-  [Oracle local lié Service](https://msdn.microsoft.com/library/dn948537.aspx)
	-  [Propriétés OnPremisesOracleTableLocation dans un tableau JSON](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) 
	-  [Prise en charge de Sources et récepteurs](https://msdn.microsoft.com/library/dn894007.aspx). Consultez la matrice de la copie mise à jour et **OracleSource** propriétés.
-  Vous pouvez spécifier le codage pour les fichiers texte dans un objet Blob Azure. Consultez les nouveaux [encodingName propriété](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob). 
- Vous pouvez appeler une procédure stockée avec des paramètres supplémentaires lors de la copie dans le récepteur de SQL. Voir [appeler la procédure stockée pour SQL récepteur][adf-copy-advanced] pour plus d'informations.   

Consultez l'objet blob : [mise à jour de fabrique Azure Data - nouveaux magasins de données](http://azure.microsoft.com/blog/2015/03/30/azure-data-factory-update-new-data-stores/) pour plus d'informations, notamment des exemples.

## Notes de version du 27/2/2015 de fabrique de données

### Nouvelles améliorations
- **Éditeur de fabrique de données azure**. L'éditeur de fabrique de données, qui fait partie de la version préliminaire du portail Azure, vous permet de créer, modifier et déployer des fichiers JSON qui définissent des services liés, des jeux de données et des pipelines. L'objectif principal de l'éditeur est de vous fournir une rapide et léger interface utilisateur (IU) pour créer des artefacts de la fabrique de données Azure sans avoir à installer Azure PowerShell et prise en main de sur l'utilisation des applets de commande PowerShell. Consultez le [Azure données fabrique éditeur - une lumière poids Web][adf-editor-blog] billet de blog pour une présentation rapide et une vidéo sur l'éditeur de fabrique de données. Pour obtenir une présentation détaillée de l'éditeur, consultez le [données fabrique éditeur][adf-editor] l'article.          

### Changements

## Notes de version du 26/1/2015 de fabrique de données ##

### Changements
- Mise à jour **passerelle de gestion des données** package d'installation a été publié sur [Microsoft Download Center][adf-gateway-download]. À partir de cette version, vous pouvez trouver la passerelle de gestion des données plus récentes à utiliser avec Azure Data Factory à cet emplacement de téléchargement. Ce package d'installation sert de fabrique de données Azure et Power BI pour les services Office 365. Si vous utilisez les deux services, notez que passerelles pour Power BI et de fabrique de données doivent être installés sur des ordinateurs différents et configurés différemment selon les instructions de la documentation de la fabrique de données ou Power BI.
- Le **activité copie** prend désormais en charge copie de données entre la base de données SQL Server sur site et une base de données SQL Azure. Voir [activité copie][adf-copy-activity] pour plus d'informations et [GitHub][adf-github-samples] pour obtenir des exemples JSON.
- **SqlSink** prend en charge une nouvelle propriété : **WriteBatchTimeout**. Cette propriété vous permet de configurer la durée d'attente de l'opération d'insertion de lot terminer avant l'expiration de l'opération. Pour obtenir une copie hybride (opération de copie qui implique une source de données sur site et une source de données du cloud), vous devez disposer de la passerelle de version 1.4 ou ultérieure pour utiliser cette propriété. 
- **SQL Server lié service** prend désormais en charge **l'authentification Windows**. 
	- Lors de la création d'un serveur SQL lié à l'aide du portail de service, vous pouvez maintenant choisir utiliser l'authentification Windows et définir les informations d'identification appropriées. Pour cela, vous avez la passerelle de version 1.4 ou ultérieure. 
	- Lors de la création d'un serveur SQL lié service à l'aide d'Azure PowerShell, vous pouvez spécifier des informations de connexion en texte brut ou chiffrer les informations de connexion en utilisant mis à jour [applet de commande New-AzureDataFactoryEncryptValue][adf-encrypt-value-cmdlet] puis utilisez la chaîne chiffrée pour la propriété de chaîne de connexion dans la charge utile JSON de services liés. Voir [Services liés][adf-msdn-linked-services] pour plus d'informations sur la définition d'un service lié au format JSON. La fonctionnalité de chiffrement n'est pas pris en charge par l'applet de commande New-AzureDataFactoryEncryptValue encore. 

## Notes de version 11/12/2014 de fabrique de données ##

### Nouvelles améliorations

- Intégration d'Azure Machine Learning
	- Cette version de service Factory de données Azure vous permet d'intégrer fabrique de données Azure avec Azure Machine Learning (ML) à l'aide de **AzureMLLinkedService** et **AzureMLBatchScoringActivity**. Voir [créer prédictives pipelines à l'aide de la fabrique de données et de formation de Machine Azure][adf-azure-ml] pour plus d'informations. 
- Indication de l'état de la version de la passerelle
	- L'état « NewVersionAvailable » est affiché dans le portail Azure en version préliminaire et dans la sortie de l'applet de commande Get-AzureDataFactoryGateway, s'il existe une version de la passerelle plus récente que celle qui est actuellement installée. Vous pouvez ensuite suivre les indications du portail pour télécharger le nouveau fichier d'installation (.msi), et l'exécuter pour installer la passerelle la plus récente. Aucune configuration supplémentaire n'est nécessaire.

### Changements

- Suppression de JobsContainer dans HdInsightOnDemandLinkedService.
	- Dans la définition de JSON pour un HDInsightOnDemandLinkedService, vous n'avez pas besoin de spécifier **jobsContainer** plus de propriété. Si la propriété est spécifiée pour un service lié à la demande, elle est ignorée. Vous pouvez supprimer la propriété de la définition JSON du service lié, et mettre à jour la définition du service lié à l'aide de l'applet de commande New-AzureDataFactoryLinkedService.
- Paramètres de configuration facultatifs pour HDInsightOnDemandLinkedService
	- Cette version introduit la prise en charge de quelques paramètres de configuration facultatifs pour HDInsightOnDemandLinked (cluster HDInsight à la demande). Voir [ClusterCreateParameters propriétés][on-demand-hdi-parameters] pour plus d'informations.
- Suppression de l'emplacement de la passerelle
	- Durant la création d'une passerelle Azure Data Factory via le portail ou PowerShell (New-AzureDataFactoryGateway), vous n'avez plus besoin de spécifier l'emplacement de la passerelle. La région de la fabrique de données est héritée. De même, pour configurer un service SQL Server lié à l'aide de JSON, la propriété « gatewayLocation » n'est plus nécessaire. Le Kit de développement logiciel (SDK) Data Factory .NET est également mis à jour pour refléter ces changements.
	- Si vous utilisez une ancienne version du Kit de développement logiciel (SDK) et d'Azure PowerShell, vous devez toujours fournir le paramètre d'emplacement.
 
     

#### Dernières modifications
	
- CustomActivity devient DotNetActivity
	- **ICustomActivity** interface est renommé en **IDotNetActivity**. Vous devrez mettre à jour les packages NuGet Data Factory et remplacer ICustomActivity par IDotNetActivity dans le code source de votre activité personnalisée.  
	- Le type d'activité personnalisée dans la définition de JSON pour l'activité personnalisée doit être modifié à partir de **CustomActivity** à **DotNetActivity**. 
	- Le **CustomActivity** et **CustomActivityProperties** classes ont été renommées **DotNetActivity** et **DotNetActivityProperties** avec le même jeu de propriétés.

		Si vous utilisez l'ancienne version du Kit de développement logiciel (SDK) et d'Azure PowerShell, vous pouvez continuer à utiliser CustomActivity au lieu de DotNetActivity.
    
  		Voir [utiliser des activités personnalisées dans un pipeline de fabrique de données Azure][adf-custom-activities] pour une procédure pas à pas sur la façon de créer une activité personnalisée et l'utiliser dans un pipeline de fabrique de données Azure.

[adf-azure-ml]: data-factory-create-predictive-pipelines.md
[adf-custom-activities]: data-factory-use-custom-activities.md
[adf-copy-activity]: data-factory-copy-activity.md
[adf-editor]: data-factory-editor.md
[adf-copy-advanced]: data-factory-copy-activity-advanced.md

[adf-editor-video]: http://channel9.msdn.com/Blogs/Windows-Azure/New-Azure-Data-Factory-Editor-UI
[adf-editor-blog]: http://azure.microsoft.com/blog/2015/03/02/azure-data-factory-editor-a-light-weight-web-editor/
[on-demand-hdi-parameters]: http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clustercreateparameters_properties.aspx
[adf-gateway-download]: http://www.microsoft.com/download/details.aspx?id=39717
[adf-github-samples]: https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON
[adf-msdn-linked-services]: https://msdn.microsoft.com/library/dn834986.aspx
[adf-encrypt-value-cmdlet]: https://msdn.microsoft.com/library/dn834940.aspx

<!---HONumber=GIT-SubDir-->
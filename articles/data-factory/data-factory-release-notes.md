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
	ms.date="07/07/2015" 
	ms.author="spelluru"/>

# Notes de publication sur Azure Data Factory

## Notes pour la version du 10/04/2015 de Data Factory
Vous voyez maintenant les listes **Tranches récemment mises à jour** et **Tranches ayant échoué récemment** dans le panneau **TABLE**. Ces listes sont triées selon l’heure de mise à jour de la tranche. L’heure de mise à jour d’une tranche est modifiée dans les situations suivantes.

-  Vous mettez à jour l’état de la tranche manuellement, par exemple, à l’aide de **Set-AzureDataFactorySliceStatus** (ou) en cliquant sur **EXÉCUTER** dans le panneau **TRANCHE** de la tranche.
-  La tranche change d’état en raison d’une exécution (par exemple, une exécution a démarré, une exécution s’est terminée et a échoué, une exécution s’est terminée et a réussi, etc.).

Cliquez sur le titre des listes ou sur **... (points de suspension)** pour consulter la liste plus complète des tranches. Cliquez sur **Filtre** dans la barre d’outils pour filtrer les tranches.
 
Vous pouvez toujours afficher les tranches triées en fonction de leur heure de tranche en cliquant sur la vignette **Tranches de données (par heure de tranche)**. Les tranches de ces collections sont classées par heure de tranche. Par exemple, dans le cas d’une planification horaire, les tranches sont : - 4/4/2015 17:00 en cours - 4/4/2015 16:00 réussite - 4/4/2015 15:00 échec

Mais, si une tranche antérieure est réexécutée, elle n’apparaît pas en haut de cette liste, même si c’est probablement ce que l’utilisateur souhaite le plus.

## Notes pour la version du 31/03/2015 de Data Factory
- Une mise à jour du package d’installation **Passerelle de gestion des données** a été publiée dans le [Centre de téléchargement Microsoft][adf-gateway-download].
- La copie à partir du **système de fichiers local vers l’objet blob Azure** est maintenant prise en charge. Pour plus d’informations, consultez les rubriques suivantes.
	-  [Service lié de système de fichiers local](https://msdn.microsoft.com/library/dn930836.aspx)
	-  [Propriétés OnPremisesFileSystemLocation dans une table JSON](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem)
	-  [Sources et récepteurs pris en charge](https://msdn.microsoft.com/library/dn894007.aspx) Consultez la matrice de la copie mise à jour et les propriétés **FileSystemSource**. 
-  La copie à partir de la **base de données Oracle locale vers l’objet blob Azure** est maintenant prise en charge. Pour plus d’informations, consultez les rubriques suivantes. 
	-  [Service Oracle lié local (en anglais)](https://msdn.microsoft.com/library/dn948537.aspx)
	-  [Propriétés OnPremisesOracleTableLocation dans une table JSON](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) 
	-  [Sources et récepteurs pris en charge](https://msdn.microsoft.com/library/dn894007.aspx) Consultez la matrice de copie mise à jour et les propriétés **OracleSource**.
-  Vous pouvez spécifier le codage pour les fichiers texte dans un objet Blob Azure. Consultez la nouvelle [propriété encodingName](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob). 
- Vous pouvez appeler une procédure stockée avec des paramètres supplémentaires lors de la copie dans un récepteur SQL. Pour plus d’informations, consultez la page [Appel d’une procédure stockée pour un récepteur SQL][adf-copy-advanced].   

Consultez le billet de blog : [Mise à jour d’Azure Data Factory - nouveaux magasins de données](http://azure.microsoft.com/blog/2015/03/30/azure-data-factory-update-new-data-stores/) pour plus d’informations, notamment des exemples.

## Notes pour la version du 27/02/2015 de Data Factory

### Nouvelles améliorations
- **Azure Data Factory Editor** Azure Data Factory Editor, qui fait partie du portail Azure en version préliminaire, vous permet de créer, de modifier et de déployer des fichiers JSON qui définissent des services liés, des jeux de données et des pipelines. L'objectif principal de l'éditeur est de vous fournir une interface utilisateur (IU) rapide et facile d'utilisation pour créer des artefacts Azure Data Factory sans avoir à installer Azure PowerShell et accélérer l'utilisation des applets de commande PowerShell. Consultez le billet de blog [Azure Data Factory Editor - Un éditeur web compact][adf-editor-blog] pour accéder à une présentation rapide et à une vidéo sur Data Factory Editor. Pour obtenir une présentation détaillée de l’éditeur, consultez l’article [Data Factory Editor][adf-editor].          

### Changements

## Notes pour la version du 26/1/2015 de Data Factory ##

### Changements
- Une mise à jour du package d’installation **Passerelle de gestion des données** a été publiée dans le [Centre de téléchargement Microsoft][adf-gateway-download]. À partir de cette version, vous pouvez trouver la dernière passerelle de gestion des données à utiliser avec Azure Data Factory à cet emplacement de téléchargement. Ce package d’installation concerne à la fois Azure Data Factory et Power BI pour les services Office 365. Si vous utilisez les deux services, notez que les passerelles pour Data Factory et Power BI doivent être installées sur des ordinateurs différents, et configurées différemment en fonction des instructions figurant dans la documentation de Data Factory ou Power BI.
- L’**activité de copie** prend désormais en charge la copie de données entre la base de données SQL Server locale et une base de données SQL Azure. Consultez [Activité de copie][adf-copy-activity] pour plus d’informations et [GitHub][adf-github-samples] pour obtenir des exemples JSON.
- **SqlSink** prend en charge une nouvelle propriété : **WriteBatchTimeout**. Cette propriété vous permet de configurer la durée d’attente de l’opération d’insertion par lots avant l’arrivée à expiration de l’opération. Pour une copie hybride (opération de copie qui implique une source de données locale et une source de données du cloud), vous devez disposer de la passerelle version 1.4 ou ultérieure pour pouvoir utiliser cette propriété. 
- Le **service lié SQL Server** prend désormais en charge **l’authentification Windows**. 
	- Lors de la création d’un service lié SQL Server à l’aide du portail, vous pouvez maintenant choisir d’utiliser l’authentification Windows et définir les informations d’identification appropriées. Pour cela, vous avez besoin de la passerelle version 1.4 ou ultérieure. 
	- Lors de la création d’un service lié SQL Server à l’aide d’Azure PowerShell, vous pouvez spécifier les informations de connexion en texte brut ou les chiffrer en utilisant l’[applet de commande New-AzureDataFactoryEncryptValue][adf-encrypt-value-cmdlet] mise à jour, puis utiliser la chaîne chiffrée pour la propriété de chaîne de connexion dans la charge utile JSON du service lié. Consultez [Services liés][adf-msdn-linked-services] pour plus d’informations sur la définition d’un service lié au format JSON. La fonctionnalité de chiffrement n’est pas encore prise en charge par l’applet de commande New-AzureDataFactoryEncryptValue. 

## Notes pour la version du 11/12/2014 de Data Factory ##

### Nouvelles améliorations

- Intégration d'Azure Machine Learning
	- Cette version du service Azure Data Factory vous permet d’intégrer Azure Data Factory à Azure Machine Learning (ML) via **AzureMLLinkedService** et **AzureMLBatchScoringActivity**. Pour plus d’informations, consultez la page [Créer des pipelines prédictifs à l’aide d’Azure Data Factory et Azure Machine Learning][adf-azure-ml]. 
- Indication de l'état de la version de la passerelle
	- L'état « NewVersionAvailable » est affiché dans le portail Azure en version préliminaire et dans la sortie de l'applet de commande Get-AzureDataFactoryGateway, s'il existe une version de la passerelle plus récente que celle qui est actuellement installée. Vous pouvez ensuite suivre les indications du portail pour télécharger le nouveau fichier d'installation (.msi), et l'exécuter pour installer la passerelle la plus récente. Aucune configuration supplémentaire n'est nécessaire.

### Changements

- Suppression de JobsContainer dans HdInsightOnDemandLinkedService.
	- Dans la définition JSON de HDInsightOnDemandLinkedService, vous n’avez plus besoin de spécifier la propriété **jobsContainer**. Si la propriété est spécifiée pour un service lié à la demande, elle est ignorée. Vous pouvez supprimer la propriété de la définition JSON du service lié, et mettre à jour la définition du service lié à l'aide de l'applet de commande New-AzureDataFactoryLinkedService.
- Paramètres de configuration facultatifs pour HDInsightOnDemandLinkedService
	- Cette version introduit la prise en charge de quelques paramètres de configuration facultatifs pour HDInsightOnDemandLinked (cluster HDInsight à la demande). Pour plus d’informations, consultez les [propriétés de ClusterCreateParameters][on-demand-hdi-parameters].
- Suppression de l'emplacement de la passerelle
	- Durant la création d'une passerelle Azure Data Factory via le portail ou PowerShell (New-AzureDataFactoryGateway), vous n'avez plus besoin de spécifier l'emplacement de la passerelle. La région de la fabrique de données est héritée. De même, pour configurer un service SQL Server lié à l'aide de JSON, la propriété « gatewayLocation » n'est plus nécessaire. Le Kit de développement logiciel (SDK) Data Factory .NET est également mis à jour pour refléter ces changements.
	- Si vous utilisez une ancienne version du Kit de développement logiciel (SDK) et d'Azure PowerShell, vous devez toujours fournir le paramètre d'emplacement.
 
     

#### Dernières modifications
	
- CustomActivity devient DotNetActivity
	- L’interface **ICustomActivity** est renommée en **IDotNetActivity**. Vous devrez mettre à jour les packages NuGet Data Factory et remplacer ICustomActivity par IDotNetActivity dans le code source de votre activité personnalisée.  
	- Vous devez changer le type d’activité personnalisée dans la définition JSON de votre activité personnalisée en remplaçant **CustomActivity** par **DotNetActivity**. 
	- Les classes **CustomActivity** et **CustomActivityProperties** ont été renommées en **DotNetActivity** et **DotNetActivityProperties** avec le même jeu de propriétés.

		Si vous utilisez l'ancienne version du Kit de développement logiciel (SDK) et d'Azure PowerShell, vous pouvez continuer à utiliser CustomActivity au lieu de DotNetActivity.
    
  		Consultez [Utilisation des activités personnalisées dans un pipeline Azure Data Factory][adf-custom-activities] pour suivre une procédure pas à pas permettant de créer une activité personnalisée et de l’utiliser dans un pipeline Azure Data Factory.

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



 

<!---HONumber=July15_HO3-->
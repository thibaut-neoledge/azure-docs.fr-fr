<properties 
	pageTitle="Surveillance et gestion d'Azure Data Factory à l'aide de la version préliminaire du portail Azure" 
	description="Découvrez comment utiliser le portail de gestion Azure pour analyser et gérer les fabriques de données Azure que vous avez créées." 
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
	ms.date="05/18/2015" 
	ms.author="spelluru"/>

# Surveillance d'Azure Data Factory à l'aide de la version préliminaire du portail Azure
Cet article décrit différents scénarios d'utilisation du portail Azure Preview pour surveiller et gérer la fabrique de données Azure.

## <a name="AllDataFactories"></a> Afficher toutes les fabriques de données dans un abonnement Azure

- Ouvrez une session dans le [portail Azure Preview][azure-preview-portal].
- Cliquez sur **Parcourir** concentrateur à gauche et cliquez sur **fabriques données**.  

	![Parcourir hub -> fabriques de données][image-data-factory-browse-datafactories]

	Si vous ne voyez pas **fabriques données**, cliquez sur **tout** puis cliquez sur **factorries de données** dans les **Parcourir** lame.

	![Parcourir hub -> tout][image-data-factory-browse-everything]

- Vous devez voir toutes les fabriques de données dans le **fabriques données** lame.

	![Lame fabriques][image-data-factory-datafactories-blade]

    
## <a name="DataFactoryDetails"></a> Afficher les détails sur une fabrique de données

Pour afficher les détails d'une fabrique de données, effectuez l'une des opérations suivantes :


- Cliquez sur une fabrique de données dans le **fabriques données** lame ci-dessus.
- Cliquez sur le lien de la fabrique de données sur le **tableau d'accueil**. **Tableau d'accueil** est le volet que vous voyez lorsque vous vous connectez au portail Azure Preview. Si vous aviez sélectionné **Ajouter au tableau d'accueil** lors de la création d'une fabrique de données (option par défaut), vous devez voir le factory de données sur le tableau d'accueil lien comme indiqué dans l'image suivante. Dans cet exemple, **ADFTutorialDataFactory**, **ADFTutorialDataFactoryDF** et **LogProcessingFactory** données fabrique de liens est disponibles sur le **tableau d'accueil**.


![Fabrique de données depuis le tableau d'accueil][image-data-factory-datafactory-from-startboard]

Dans les deux cas, vous verrez la **DATA FACTORY** lame pour la fabrique de données sélectionnée, comme indiqué dans l'image suivante.

 ![Page d'accueil de fabrique de données][image-data-factory-datafactory-home-page]
 
## <a name="DataFactoryDiagram"></a> Vue de diagramme de vue de la fabrique de données
Dans le **DATA FACTORY** lame pour la fabrique de données, cliquez sur **diagramme** vignette pour afficher la vue de diagramme de la fabrique de données.

![Affichage de schéma de fabrique de données][image-data-factory-diagram-view]
 
### Ouvrez un pipeline dans la vue de diagramme
Vous pouvez afficher toutes les activités dans un pipeline en cliquant le pipeline de la vue de diagramme, puis en cliquant sur **ouverte**. Vous devez voir les activités dans le pipeline, ainsi que des groupes de données d'entrée et de sortie pour les activités. ![Ouverte](./media/data-factory-monitor-manage-using-management-portal/DiagramView-OpenPipeline.png)

Cliquez sur **Data factory** dans l'arborescence de navigation dans le coin supérieur gauche pour revenir à la vue de diagramme. La vue de diagramme affiche tous les pipelines. Dans cet exemple, vous avez seulement créé un pipeline.

## <a name="DataFactoryLinkedServices"></a> Afficher les services liés dans une fabrique de données
Dans le **DATA FACTORY** lame pour la fabrique de données, cliquez sur **Services liés** carré pour voir tous les services liés dans une liste.

![Lame de Services liés][image-data-factory-linked-services]

## <a name="DataFactoryLinkedService"></a> Afficher les détails sur un service lié
Dans le **SERVICES liés** lame, cliquez sur le service lié à partir de la liste pour afficher les détails.

![Lame de services liés][image-data-factory-linked-service]

## <a name="DataFactoryDatasets"></a> Afficher les datasets dans une fabrique de données 
Dans le **DATA FACTORY** lame pour la fabrique de données, cliquez sur **Datasets** carré pour voir toutes les tables de la fabrique de données.

![Lame de jeux de données][image-data-factory-datasets]

## <a name="DataFactoryDataset"></a> Afficher les détails sur un groupe de données
Cliquez sur le jeu de données dans la liste des jeux de données du panneau JEUX DE DONNÉES pour en afficher les détails. Notez qu'une table est un jeu de données rectangulaire qui dispose d'un schéma. Il s'agit du seul type de jeu de données pris en charge actuellement.

![Lame de table][image-data-factory-table]

Dans le **TABLE** lame ci-dessus, les deux **récemment mis à jour les tranches** et **Échec récemment tranches** listes sont triés par le **heure de dernière mise à jour**. L'heure de mise à jour d'une tranche de données est modifié dans les situations suivantes.

-  Vous mettre à jour l'état de la coupe manuellement, par exemple, à l'aide de la **Set-AzureDataFactorySliceStatus** (ou) en cliquant sur **exécuter** sur la **tranche** lame pour la tranche.
-  La tranche modifie l'état en raison d'une exécution (par exemple, une exécution démarré, une exécution s'est terminée et a échoué, une exécution s'est terminée et réussie, etc.).
 
	
Pour afficher les tranches de données triés par les heures de début et de fin de tranche au lieu de cela, cliquez sur **tranches de données (par heure de tranche)** vignette.
 
![Tranches de données par heure de secteur][DataSlicesBySliceTime]

Cliquez sur le titre des listes ou des **... (points de suspension)** Pour consulter la liste supérieure des tranches.

![Toutes les tranches d'une Table][image-data-factory-all-slices]

Sur le **tranches de données** lame, cliquez sur le **filtre** bouton pour afficher les **filtre** volet qui vous permet de **filtre** tranches pour voir les tranches spécifiques que vous souhaitez examiner. La lame semblable à celle ci-dessous s'affiche lorsque vous cliquez sur **filtre** sur la **tranches de données** lame avec secteurs **classées par heure de la mise à jour**.

![Volet de filtre][image-data-factory-filter-blade]

Le **filtre** lame vous permet de filtrer selon **dernière mise à jour de temps** et **découper état**. Le tableau suivant décrit tous les États de tranche et leur description.
 
État du secteur | Description
------------ | ------------
PendingExecution | Le traitement des données n'a pas encore démarré.
InProgress | Le traitement des données est en cours.
Ready | Le traitement des données est terminé et la tranche de données est prête.
Failed | Échec de l'exécution qui produit la tranche.
Skip | Le traitement de la tranche est ignoré.
Retry | Nouvelle tentative de l'exécution qui produit la tranche.
Timed Out | Le traitement des données du secteur a expiré.
PendingValidation | La tranche de données est en attente de validation par rapport aux stratégies de validation avant d'être traitée.
RetryValidation | Nouvelle tentative de la validation du secteur.
FailedValidation | Échec de validation de la tranche.
LongRetry | Une tranche est dans cet état si LongRetry est spécifié dans le tableau JSON et si les tentatives régulières de la tranche ont échoué.
ValidationInProgress | Validation de la tranche (en fonction des stratégies définies du tableau JSON) en cours d'exécution.

Lorsque vous cliquez sur **filtre** sur le **tranches de données** lame avec secteurs **triés par le temps de tranche**, vous verrez un autre type de **filtre** lame.

![Volet filtre 2][image-data-factory-filter-blade-2]


Lorsque vous lancez le **filtre** lames, le **à** champ est automatiquement défini sur l'heure la plus récente (arrondie) pour limiter le nombre d'enregistrements renvoyés. Le **de** champ est défini automatiquement ainsi. Vous pouvez modifier le **de** date en cliquant sur le **calendrier** bouton. Le **à** date est automatiquement modifié lorsque vous modifiez le **de** date.

Vous pouvez cliquer sur **Précédent**/ ** suivant ** boutons pour afficher les tranches précédente période/période suivante. La plage de temps pour **Précédent** et **Suivant** boutons est définie en fonction de la fréquence du secteur et l'intervalle comme indiqué dans le tableau suivant.

Fréquence | Valeur d'intervalle | Segment de temps obtenu
----------| -------------------- | --------------------
Minute | 1-4 | 6 heures
Minute | 5-29 | 1 jour
Minute | 30 à 180 | 7 derniers jours
Minute | 180 + | 28 jours (approximatif. mois)
Heure | 1-3 | 7 derniers jours
Heure | 4-11 | 28 jours (approximatif. mois)
Heure | 12-72 | 182 jours (approximatifs. 6 mois)
Heure | 73 + | 1 an
Jour | 1-6 | 1 an
Jour | 7-20 | 5 ans
Jour | + 21 | 10 ans
Semaine | 1-3 | 5 ans
Semaine | 4 + | 10 ans
Mois | tout | 10 ans
 
Par exemple, si vous définissez **fréquence** en tant que **heure** et **intervalle** de **2**, sur le **Suivant**/ ** précédent ** boutons déplacement la plage horaire **7 jours** dans les deux sens. Cette logique s'applique au panneau Filtre, quels que soient les tranches que vous affichez : totalité des tranches, tranches récentes ou tranches à problème.




## <a name="DataFactorySlice"></a> Afficher les détails sur une tranche de données
Cliquez sur un secteur dans la liste des tranches soit sur le **TABLE** lame ou **tranches de données** blade pour afficher des détails sur ce secteur.

![Tranche de données][image-data-factory-dataslice]

Si la section n'est pas dans le **prêt** état, vous pouvez voir les tranches en amont qui ne sont pas prêts et bloquent la tranche actuelle à partir de l'exécution dans le **tranches en amont qui ne sont pas prêts** liste.

### <a name="DataFactoryActivtyRuns"></a> Afficher que toutes les activités s'exécute pour une tranche de données
Une tranche peut compter plusieurs exécutions. Par exemple, lorsqu'une tranche échoue, le service peut réessayer pendant un certain temps. Vous pouvez également réexécuter une tranche dont toutes les tentatives ont échoué. Vous pouvez voir toutes les activités qui s'exécute sur le ** tranche de données ** lames dans la liste du bas.

## <a name="DataFactoryActivtyRunDetails"></a> Afficher les détails sur une activité à exécuter
Cliquez sur une activité à exécuter à partir de la liste des exécutions sur le **tranche de données** lame pour afficher les détails sur l'activité de s'exécuter.

![Activité exécuter des détails][image-data-factory-activity-run-details]

## <a name="EventsInThePastweek"></a> Opérations LENTILLE - événements de la semaine dernière
Dans le **DATA FACTORY** blade (ou page d'accueil) de la fabrique de données, cliquez sur **les événements de la semaine dernière** dans **Operations** lentille pour afficher les événements de la semaine dernière. Cela vous permet d'obtenir une vue d'ensemble des opérations effectuées par la fabrique de données au cours de la semaine écoulée. Cela vous permet également de résoudre des erreurs de déplacement/traitement des données.

![Événements de fabrique de données][image-data-factory-events]


## Voir aussi

Article | Description
------ | ---------------
[Analyse et gestion de fabrique données Azure à l'aide de PowerShell][monitor-manage-using-powershell] | Cet article décrit comment surveiller une fabrique de données Azure à l'aide des applets de commande PowerShell Azure. 


[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[azure-preview-portal]: http://portal.azure.com/

[image-data-factory-filter-blade]: ./media/data-factory-monitor-manage-using-management-portal/FilterBlade.png

[image-data-factory-filter-blade-2]: ./media/data-factory-monitor-manage-using-management-portal/FilterBlade2.png


[image-data-factory-browse-everything]: ./media/data-factory-monitor-manage-using-management-portal/BrowseEverything.png

[image-data-factory-browse-datafactories]: ./media/data-factory-monitor-manage-using-management-portal/BrowseDataFactories.png

[image-data-factory-datafactories-blade]: ./media/data-factory-monitor-manage-using-management-portal/DataFactories.png

[image-data-factory-datafactory-from-startboard]: ./media/data-factory-monitor-manage-using-management-portal/DataFactoryFromStartboard.png

[image-data-factory-datafactory-home-page]: ./media/data-factory-monitor-manage-using-management-portal/DataFactoryHomePage.png

[image-data-factory-diagram-view]: ./media/data-factory-monitor-manage-using-management-portal/DiagramView.png

[image-data-factory-linked-services]: ./media/data-factory-monitor-manage-using-management-portal/LinkedServicesBlade.png

[image-data-factory-linked-service]: ./media/data-factory-monitor-manage-using-management-portal/LinkedServiceBlade.png

[image-data-factory-datasets]: ./media/data-factory-monitor-manage-using-management-portal/Datasets.png

[image-data-factory-table]: ./media/data-factory-monitor-manage-using-management-portal/Table.png

[image-data-factory-all-slices]: ./media/data-factory-monitor-manage-using-management-portal/AllSlices.png

[image-data-factory-filter]: ./media/data-factory-monitor-manage-using-management-portal/Filter.png

[image-data-factory-dataslice]: ./media/data-factory-monitor-manage-using-management-portal/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-monitor-manage-using-management-portal/ActivityRunDetails.png

[image-data-factory-events]: ./media/data-factory-monitor-manage-using-management-portal/Events.png
[DataSlicesBySliceTime]: ./media/data-factory-monitor-manage-using-management-portal/DataSlicesBySliceTime.png

<!---HONumber=GIT-SubDir--> 
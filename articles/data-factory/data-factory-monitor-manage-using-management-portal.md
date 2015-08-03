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
Cet article décrit différents scénarios d'utilisation du portail Azure en version préliminaire pour la surveillance et la gestion d'Azure Data Factory.

## <a name="AllDataFactories"></a> Affichage de toutes les fabriques de données d’un abonnement Azure

- Connectez-vous au [Portail Azure en version préliminaire][azure-preview-portal].
- Cliquez sur le concentrateur **PARCOURIR** à gauche, puis sur **Fabriques de données**.  

	![Concentrateur PARCOURIR -> Fabriques de données][image-data-factory-browse-datafactories]

	Si vous ne voyez pas **Fabriques de données**, cliquez sur **Tout**, puis sur **Fabriques de données** dans le panneau **Parcourir**.

	![Concentrateur PARCOURIR -> Tout][image-data-factory-browse-everything]

- Vous devez voir toutes les fabriques de données dans le panneau **Fabriques de données**.

	![Panneau Fabriques de données][image-data-factory-datafactories-blade]

    
## <a name="DataFactoryDetails"></a> Affichage des détails d’une fabrique de données

Pour afficher les détails d'une fabrique de données, effectuez l'une des opérations suivantes :


- Cliquez sur une fabrique de données dans le panneau **Fabriques de données** illustré ci-dessus.
- Cliquez sur le lien de la fabrique de données dans le **tableau d’accueil**. Le **tableau d’accueil** est le panneau affiché quand vous vous connectez au portail Azure en version préliminaire. Si vous avez sélectionné **Ajouter au tableau d’accueil** durant la création d’une fabrique de données (option par défaut), vous devez voir le lien de la fabrique de données sur le tableau d’accueil comme illustré ci-après. Dans cet exemple, les liens des fabriques de données **ADFTutorialDataFactory**, **ADFTutorialDataFactoryDF** et **LogProcessingFactory** sont disponibles sur le **tableau d’accueil**.


![Fabrique de données depuis le tableau d’accueil][image-data-factory-datafactory-from-startboard]

Dans les deux cas, le panneau **FABRIQUE DE DONNÉES** est affiché pour la fabrique de données comme illustré ci-après.

 ![Page d’accueil Data Factory][image-data-factory-datafactory-home-page]
 
## <a name="DataFactoryDiagram"></a> Affichage d’une vue schématique d’une fabrique de données
Dans le panneau **FABRIQUE DE DONNÉES** de la fabrique de données, cliquez sur la vignette **Diagramme** pour afficher la vue schématique de la fabrique de données.

![Vue schématique Data Factory][image-data-factory-diagram-view]
 
### Ouverture d’un pipeline dans la vue schématique
Vous pouvez afficher toutes les activités dans un pipeline en cliquant sur ce dernier avec le bouton droit dans la vue schématique, puis en cliquant sur **Ouvrir un pipeline**. Vous devez voir les activités dans le pipeline, ainsi que des jeux de données d’entrée et de sortie pour les activités. ![Pipeline ouvert](./media/data-factory-monitor-manage-using-management-portal/DiagramView-OpenPipeline.png)

Cliquez sur **Fabrique de données** dans la barre de navigation située dans le coin supérieur gauche pour revenir à la vue schématique. La vue schématique affiche tous les pipelines. Dans cet exemple, vous avez créé un seul pipeline.

## <a name="DataFactoryLinkedServices"></a> Affichage des services liés dans une fabrique de données
Dans le panneau **FABRIQUE DE DONNÉES** de la fabrique de données, cliquez sur la vignette **Services liés** pour voir la liste de tous les services liés.

![Panneau Services liés][image-data-factory-linked-services]

## <a name="DataFactoryLinkedService"></a> Affichage des détails d’un service lié
Dans le panneau **SERVICES LIÉS**, cliquez sur le service lié dans la liste pour en afficher les détails.

![Panneau Service lié][image-data-factory-linked-service]

## <a name="DataFactoryDatasets"></a> Affichage des jeux de données dans une fabrique de données 
Dans le panneau **FABRIQUE DE DONNÉES** de la fabrique de données, cliquez sur la vignette **Jeux de données** pour afficher toutes les tables de la fabrique de données.

![Panneau Jeux de données][image-data-factory-datasets]

## <a name="DataFactoryDataset"></a> Affichage des détails d’un jeu de données
Cliquez sur le jeu de données dans la liste des jeux de données du panneau JEUX DE DONNÉES pour en afficher les détails. Notez qu'une table est un jeu de données rectangulaire qui dispose d'un schéma. Il s'agit du seul type de jeu de données pris en charge actuellement.

![Panneau Table][image-data-factory-table]

Dans le panneau **TABLE** ci-dessus, les listes **Tranches récemment mises à jour** et **Tranches ayant échoué récemment** sont triées en fonction de l’**HEURE DE LA DERNIÈRE MISE À JOUR**. L’heure de mise à jour d’une tranche est modifiée dans les situations suivantes.

-  Vous mettez à jour l’état de la tranche manuellement, par exemple, à l’aide de **Set-AzureDataFactorySliceStatus** (ou) en cliquant sur **EXÉCUTER** dans le panneau **TRANCHE** de la tranche.
-  La tranche change d’état en raison d’une exécution (par exemple, une exécution a démarré, une exécution s’est terminée et a échoué, une exécution s’est terminée et a réussi, etc.).
 
	
Pour afficher les tranches de données triées en fonction des heures de début et de fin de tranche, cliquez sur la vignette **Tranches de données (par heure de tranche)**.
 
![Tranches de données par heure de tranche][DataSlicesBySliceTime]

Cliquez sur le titre des listes ou sur **... (points de suspension)** pour consulter la liste plus complète des tranches.

![Toutes les tranches d’une table][image-data-factory-all-slices]

Dans le panneau **Tranches de données**, cliquez sur le bouton **Filtrer** pour voir le panneau **Filtre**. Celui-ci vous permet de **filtrer** les tranches et d’afficher les tranches spécifiques que vous souhaitez examiner. Un panneau semblable à celui figurant ci-dessous s’affiche lorsque vous cliquez sur **Filtrer** dans le panneau **Tranches de données** avec les tranches **classées par heure de mise à jour**.

![Panneau Filtrer][image-data-factory-filter-blade]

Le panneau **Filtre** vous permet de filtrer selon l’**heure de dernière mise à jour** et l’**état de la tranche**. Le tableau suivant fournit l’état de toutes les tranches et leur description.
 
État de la tranche | Description
------------ | ------------
PendingExecution | Le traitement des données n'a pas encore démarré.
InProgress | Le traitement des données est en cours.
Ready | Le traitement des données est terminé et la tranche de données est prête.
Failed | Échec de l'exécution qui produit la tranche.
Skip | Le traitement de la tranche est ignoré.
Retry | Nouvelle tentative de l'exécution qui produit la tranche.
Timed Out | Le traitement des données du secteur a expiré.
PendingValidation | La tranche de données est en attente de validation par rapport aux stratégies de validation avant d'être traitée.
RetryValidation | Nouvelle tentative de validation de la tranche.
FailedValidation | Échec de validation de la tranche.
LongRetry | Une tranche est dans cet état si LongRetry est spécifié dans le tableau JSON et si les tentatives régulières de la tranche ont échoué.
ValidationInProgress | Validation de la tranche (en fonction des stratégies définies du tableau JSON) en cours d'exécution.

Lorsque vous cliquez sur **Filtrer** dans le panneau **Tranches de données** avec les tranches **triées par heure de tranche**, vous voyez un autre type de panneau **Filtre**.

![Panneau Filtrer 2][image-data-factory-filter-blade-2]


Quand vous lancez le panneau **Filtre**, le champ **À** prend automatiquement la valeur de l’heure la plus récente (arrondie) pour limiter le nombre d’enregistrements retournés. Le champ **De** est également défini automatiquement. Vous pouvez modifier la date **De** en cliquant sur le bouton **Calendrier**. La date **À** change automatiquement quand vous modifiez la date **De**.

Vous pouvez cliquer sur les boutons **Précédent**/**Suivant** pour afficher les tranches de la période précédente/suivante. L’intervalle de temps des boutons **Précédent** et **Suivant** est défini en fonction de la fréquence et de l’intervalle des tranches, comme indiqué dans le tableau suivant.

Fréquence | Plage de valeurs de l’intervalle | Bloc de temps résultant
----------| -------------------- | --------------------
Minute | 1 à 4 | 6 heures
Minute | 5 à 29 | 1 jour
Minute | 30 à 180 | 7 jours
Minute | Plus de 180 | 28 jours (environ mois calendaire)
Heure | 1 à 3 | 7 jours
Heure | 4 à 11 | 28 jours (environ mois calendaire)
Heure | 12 à 72 | 182 jours (environ 6 mois)
Heure | Plus de 73 | 1 an
Jour | 1 à 6 | 1 an
jour | 7 à 20 | 5 ans
jour | Plus de 21 | 10 ans
Semaine | 1-3 | 5 ans
Semaine | Plus de 4 | 10 ans
Mois | Quelconque | 10 ans
 
Par exemple, si vous définissez la **fréquence** en **heures** et si vous définissez un **intervalle** égal à **2**, quand vous cliquez sur les boutons **Suivant/**Précédent****, cela entraîne un déplacement de l’intervalle de temps de **7 jours** dans les deux directions. Cette logique s'applique au panneau Filtre, quels que soient les tranches que vous affichez : totalité des tranches, tranches récentes ou tranches à problème.




## <a name="DataFactorySlice"></a> Affichage des détails d’une tranche
Cliquez sur une tranche dans la liste dans l’un des panneaux **TABLE** ou **Tranches de données** pour afficher les détails de cette tranche.

![Tranche de données][image-data-factory-dataslice]

Si la tranche n’a pas l’état **Prêt**, vous pouvez voir les tranches en amont qui ne sont pas prêtes et qui empêchent l’exécution de la tranche actuelle dans la liste **Tranches en amont qui ne sont pas prêtes**.

### <a name="DataFactoryActivtyRuns"></a> Affichage de l’exécution de l’activité pour une tranche
Une tranche peut compter plusieurs exécutions. Par exemple, lorsqu'une tranche échoue, le service peut réessayer pendant un certain temps. Vous pouvez également réexécuter une tranche dont toutes les tentatives ont échoué. Vous pouvez afficher toutes les exécutions d’activité dans le panneau **Tranches de données** dans la liste en bas.

## <a name="DataFactoryActivtyRunDetails"></a> Affichage des détails d’une exécution d’activité
Cliquez sur une exécution d’activité dans la liste des exécutions du panneau **Tranches de données** pour afficher tous les détails de l’exécution d’activité.

![Détails sur l’exécution d’activité][image-data-factory-activity-run-details]

## <a name="EventsInThePastweek"></a> Filtre Opérations - Événements au cours de la dernière semaine
Dans le panneau **FABRIQUE DE DONNÉES** (ou sur la page d’accueil) de la fabrique de données, cliquez sur **Événements** au cours de la dernière semaine dans le filtre **Opérations** pour afficher les événements de la semaine écoulée. Cela vous permet d'obtenir une vue d'ensemble des opérations effectuées par la fabrique de données au cours de la semaine écoulée. Cela vous permet également de résoudre des erreurs de déplacement/traitement des données.

![Événements Data Factory][image-data-factory-events]


## Voir aussi

Article | Description
------ | ---------------
[Surveillance et gestion d’Azure Data Factory à l’aide de PowerShell][monitor-manage-using-powershell] | Cet article décrit comment surveiller une fabrique de données Azure à l’aide d’applets de commande Azure PowerShell. 


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

<!---HONumber=July15_HO4-->
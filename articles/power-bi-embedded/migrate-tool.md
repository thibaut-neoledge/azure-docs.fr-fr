---
title: "Utiliser l’outil de migration de Power BI Embedded | Microsoft Docs"
description: "Vous pouvez utiliser l’outil de migration de Power BI Embedded pour copier vos rapports à partir de Collections d’espaces de travail Power BI vers Power BI Embedded."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: 0b7b5089045daf6dd88fcd84e316b2bd44f8c927
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-power-bi-embedded-migration-tool"></a>Utiliser l’outil de migration de Power BI Embedded

Vous pouvez utiliser l’outil de migration de Power BI Embedded pour copier vos rapports à partir de Collections d’espaces de travail Power BI vers Power BI Embedded.

La migration de votre contenu de vos collections d’espaces de travail vers le service Power BI peut s’effectuer en parallèle avec votre solution actuelle et ne nécessite aucun temps d’arrêt.

## <a name="limitations"></a>Limites

* Les jeux de données envoyés ne peuvent pas être téléchargés et doivent être recréés à l’aide des API REST de Power BI pour le service Power BI.
* Les fichiers PBIX importés avant le 26 novembre 2016 ne seront pas téléchargeables.

## <a name="download"></a>Télécharger

Vous pouvez télécharger l’exemple d’outil de migration à partir de [GitHub](https://github.com/Microsoft/powerbi-migration-sample). Vous pouvez télécharger un fichier zip du dépôt ou vous pouvez le cloner localement. Une fois téléchargé, vous pouvez ouvrir *powerbi-migration-sample.sln* dans Visual Studio pour générer et exécuter l’outil de migration.

## <a name="migration-plans"></a>Plans de migration

Votre plan de migration se compose simplement de métadonnées qui répertorient le contenu Collections d’espaces de travail Power BI et qui indiquent comment vous souhaitez les publier dans Power BI Embedded.

### <a name="start-with-a-new-migration-plan"></a>Démarrer avec un nouveau plan de migration

Un plan de migration est constitué des métadonnées des éléments disponibles dans Collections d’espaces de travail Power BI que vous souhaitez ensuite déplacer vers Power BI Embedded. Le plan de migration est stocké en tant que fichier XML.

Vous devez commencer par créer un plan de migration. Pour cela, effectuez les étapes suivantes.

1. Sélectionnez **File (Fichier)** > **New Migration Plan (Nouveau plan de migration)**.

    ![Nouveau plan de migration](media/migrate-tool/migrate-tool-plan.png)

2. Dans la boîte de dialogue **Select Power BI Workspace Collections Resource Group (Sélectionner le groupe de ressources Collections d’espaces de travail Power BI)**, vous devrez sélectionner la liste déroulante **Environment** et sélectionner production.

3. Vous serez invité à vous connecter. Vous utiliserez la connexion de votre abonnement Azure.

    > [!IMPORTANT]
    > Il ne s’agit **pas** de votre compte d’organisation Office 365 avec lequel vous vous connectez à Power BI.

4. Sélectionnez l’abonnement Azure qui stocke votre ressource Collections d’espaces de travail Power BI.

    ![Sélectionner votre abonnement Azure](media/migrate-tool/migrate-tool-select-resource-group.png)

5. Sous la liste des abonnements, sélectionnez le **Groupe de ressources** qui contient vos collections d’espaces de travail, puis sélectionnez **Select**.

    ![Sélectionner le groupe de ressources](media/migrate-tool/migrate-tool-select-resource-group2.png)

6. Sélectionnez **Analyze**. Vous obtenez alors un inventaire des éléments dans votre abonnement Azure, à partir duquel vous pouvez élaborer votre plan.

    ![Sélectionner le bouton Analyze](media/migrate-tool/migrate-tool-analyze-group.png)

    > [!NOTE]
    > Le processus d’analyse peut prendre plusieurs minutes, en fonction du nombre de collections d’espaces de travail et du volume de contenu existant dans la collection d’espaces de travail.

7. Une fois **l’analyse** terminée, vous êtes invité à enregistrer votre plan de migration.

À ce stade, vous avez connecté votre plan de migration à votre abonnement Azure. Lisez la suite de cet article pour comprendre le flux de travail du plan de migration. Cela comprend les étapes Analyser et planifier la migration, Télécharger, Créer des groupes et Charger.

### <a name="save-your-migration-plan"></a>Enregistrer votre plan de migration

Vous pouvez enregistrer votre plan de migration pour une utilisation ultérieure. Cela crée un fichier XML contenant toutes les informations de votre plan de migration.

Pour enregistrer votre plan de migration, effectuez les étapes suivantes.

1. Sélectionnez **File (Fichier)** > **Save Migration Plan (Enregistrer le plan de migration)**.

    ![Option de menu Enregistrer le plan de migration](media/migrate-tool/migrate-tool-save-plan.png)

2. Donnez un nom à votre fichier ou utilisez le nom de fichier généré, puis sélectionnez **Save (Enregistrer)**.

### <a name="open-an-existing-migration-plan"></a>Ouvrir un plan de migration existant

Vous pouvez ouvrir un plan de migration enregistré pour continuer à travailler sur votre migration.

Pour ouvrir votre plan de migration existant, effectuez les étapes suivantes.

1. Sélectionnez **File (Fichier)** > **Open Existing Migration Plan (Ouvrir un plan de migration existant)**.

    ![Option de menu Ouvrir un plan de migration existant](media/migrate-tool/migrate-tool-open-plan.png)

2. Sélectionnez votre fichier de migration, puis **Open (Ouvrir)**.

## <a name="step-1-analyze-and-plan-migration"></a>Étape 1 : Analyser et planifier la migration

L’onglet **Analyze & Plan Migration (Analyser et planifier la migration)** vous donne un aperçu de ce qui figure actuellement dans le groupe de ressources de votre abonnement Azure.

![Onglet Analyser et planifier la migration](media/migrate-tool/migrate-tool-step1.png)

Nous allons examiner *SampleResourceGroup* en guise d’exemple.

### <a name="paas-topology"></a>Topologie PaaS

Il s’agit d’une liste de votre *Groupe de ressources > Collections d’espaces de travail > Espaces de travail*. Les collections d’espaces de travail et le groupe de ressources ont un nom convivial. Pour les espaces de travail, un GUID est affiché.

Les éléments de la liste affichent également une couleur et un nombre au format (#/#). Cela indique le nombre de rapports qui peuvent être téléchargés.

La couleur noire signifie que tous les rapports peuvent être téléchargés. La couleur rouge signifie que certains rapports ne peuvent pas être téléchargés. Le nombre de gauche indique le nombre total de rapports qui peuvent être téléchargés. Le nombre de droite indique le nombre total de rapports dans le regroupement.

Vous pouvez sélectionner un élément dans la topologie PaaS pour afficher les rapports dans la section Rapports.

### <a name="reports"></a>Rapports

La section sur les rapports répertorie les rapports disponibles et indique s’ils peuvent être téléchargés ou non.

![Liste des rapports dans Collections d’espaces de travail Power BI](media/migrate-tool/migrate-tool-analyze-reports.png)

### <a name="target-structure"></a>Structure cible

La **structure cible** est l’endroit où vous indiquez à l’outil où les éléments seront téléchargés et comment les charger.

#### <a name="download-plan"></a>Plan de téléchargement

Un chemin est créé automatiquement pour vous. Vous pouvez le modifier si vous le souhaitez. Si vous modifiez le chemin, vous devez sélectionner **Update paths (Mettre à jour les chemins)**.

**Cela n’exécute pas réellement le téléchargement.** Cela spécifie uniquement la structure de l’emplacement où les rapports seront téléchargés.

#### <a name="upload-plan"></a>Plan de chargement

Ici, vous pouvez spécifier un préfixe à utiliser pour les espaces de travail d’application qui seront créés dans le service Power BI. Après le préfixe se trouve le GUID de l’espace de travail qui existait dans Azure.

![Spécifier le préfixe de nom de groupe](media/migrate-tool/migrate-tool-upload-plan.png)

**Cela ne crée pas réellement les groupes dans le service Power BI.** Cela définit uniquement la structure de nommage pour les groupes.

Si vous changez le préfixe, vous devez sélectionner **Generate Upload Plan (Générer le plan de chargement)**.

Vous pouvez cliquer avec le bouton droit sur un groupe et choisir de le renommer dans le plan de chargement directement, si vous le souhaitez.

![Option de menu contextuel Rename group (Renommer le groupe)](media/migrate-tool/migrate-tool-upload-report-rename-item.png)

> [!NOTE]
> Le nom du *groupe* ne doit pas contenir d’espace ou de caractère non valide.

## <a name="step-2-download"></a>Étape 2 : Télécharger

Sous l’onglet **Download (Télécharger)** figure la liste des rapports et des métadonnées associées. Vous pouvez voir l’état actuel et l’état précédent de l’exportation.

![Onglet Download (Télécharger)](media/migrate-tool/migrate-tool-download-tab.png)

Deux options s’offrent à vous.

* Sélectionnez des rapports spécifiques, puis **Download Selected (Télécharger la sélection)**.
* Sélectionnez **Download All (Tout télécharger)**.

![Bouton Download Selected (Télécharger la sélection)](media/migrate-tool/migrate-tool-download-options.png)

Pour un téléchargement ayant réussi, l’état affiché est *Done (Terminé)* et reflète le fait que le fichier PBIX existe.

Une fois le téléchargement terminé, sélectionnez l’onglet **Create Groups (Créer des groupes)**.

## <a name="step-3-create-groups"></a>Étape 3 : Créer des groupes

Une fois que vous avez téléchargé les rapports disponibles, vous pouvez accéder à l’onglet **Create Groups (Créer des groupes)**. Cet onglet crée les espaces de travail d’application dans le service Power BI en fonction du plan de migration que vous avez créé. L’espace de travail d’application est créé avec le nom que vous avez indiqué sous l’onglet **Upload (Charger)** dans **Analyze & Plan Migration (Analyser et planifier la migration)**.

![Onglet Create Groups (Créer des groupes)](media/migrate-tool/migrate-tool-create-groups.png)

Pour créer les espaces de travail d’application, vous pouvez sélectionner **Create Selected Groups (Créer les groupes sélectionnés)** ou **Create All Missing Groups (Créer tous les groupes manquants)**.

Quand vous sélectionnez l’une de ces options, vous êtes invité à vous connecter. *Vous devez utiliser vos informations d’identification pour le service Power BI sur lequel vous souhaitez créer les espaces de travail d’application.*

![Écran de connexion de Power BI](media/migrate-tool/migrate-tool-create-group-sign-in.png)

Cela crée l’espace de travail d’application dans le service Power BI. Cela ne charge pas les rapports dans l’espace de travail d’application.

Vous pouvez vérifier que l’espace de travail d’application a été créé en vous connectant à Power BI et en vérifiant que l’espace de travail existe. Vous remarquerez que rien ne se trouve dans l’espace de travail.

![Espace de travail d’application dans le service Power BI](media/migrate-tool/migrate-tool-app-workspace.png)

Une fois l’espace de travail créé, vous pouvez passer à l’onglet **Upload (Charger)**.

## <a name="step-4-upload"></a>Étape 4 : Charger

Sous l’onglet **Upload (Charger)**, les rapports sont chargés vers le service Power BI. Vous verrez une liste des rapports que nous avons téléchargés sous l’onglet Download (Télécharger), ainsi que le nom du groupe cible en fonction de votre plan de migration.

![Onglet Upload (Charger)](media/migrate-tool/migrate-tool-upload-tab.png)

Vous pouvez charger les rapports sélectionnés ou charger tous les rapports. Vous pouvez également réinitialiser l’état du chargement afin de recharger des éléments.

Vous avez aussi la possibilité de sélectionner quoi faire s’il existe un rapport du même nom. Vous pouvez choisir entre **Abort (Abandonner)**, **Ignore (Ignorer)** et **Overwrite (Remplacer)**.

![Liste déroulante d’options disponibles si un rapport existe déjà](media/migrate-tool/migrate-tool-upload-report-same-name.png)

![Charger les résultats sélectionnés](media/migrate-tool/migrate-tool-upload-selected.png)

### <a name="duplicate-report-names"></a>Noms de rapport en double

Si vous avez un rapport qui porte le même nom, mais que vous savez qu’il s’agit d’un rapport différent, vous devez changer le **TargetName** du rapport. Vous pouvez changer le nom en modifiant manuellement le fichier XML du plan de migration.

Vous devez fermer l’outil de migration pour apporter la modification, puis rouvrir l’outil et le plan de migration.

Dans l’exemple ci-dessus, l’un des rapports clonés a échoué et vous êtes averti qu’il existe un rapport du même nom. Si nous examinons le fichier XML du plan de migration, nous observons ce qui suit.

```
<ReportMigrationData>
    <PaaSWorkspaceCollectionName>SampleWorkspaceCollection</PaaSWorkspaceCollectionName>
    <PaaSWorkspaceId>4c04147b-d8fc-478b-8dcb-bcf687149823</PaaSWorkspaceId>
    <PaaSReportId>525a8328-b8cc-4f0d-b2cb-c3a9b4ba2efe</PaaSReportId>
    <PaaSReportLastImportTime>1/3/2017 2:10:19 PM</PaaSReportLastImportTime>
    <PaaSReportName>cloned</PaaSReportName>
    <IsPushDataset>false</IsPushDataset>
    <IsBoundToOldDataset>false</IsBoundToOldDataset>
    <PbixPath>C:\MigrationData\SampleResourceGroup\SampleWorkspaceCollection\4c04147b-d8fc-478b-8dcb-bcf687149823\cloned-525a8328-b8cc-4f0d-b2cb-c3a9b4ba2efe.pbix</PbixPath>
    <ExportState>Done</ExportState>
    <LastExportStatus>OK</LastExportStatus>
    <SaaSTargetGroupName>SampleMigrate</SaaSTargetGroupName>
    <SaaSTargetGroupId>6da6f072-0135-4e6c-bc92-0886d8aeb79d</SaaSTargetGroupId>
    <SaaSTargetReportName>cloned</SaaSTargetReportName>
    <SaaSImportState>Failed</SaaSImportState>
    <SaaSImportError>Report with the same name already exists</SaaSImportError>
</ReportMigrationData>
```

Pour l’élément ayant échoué, nous pouvons changer le nom de SaaSTargetReportName.

```
<SaaSTargetReportName>cloned2</SaaSTargetReportName>
```

Nous pouvons ensuite rouvrir le plan, dans l’outil de migration, et charger le rapport ayant échoué.

Si nous revenons à Power BI, nous pouvons constater que les rapports et les jeux de données ont été chargés dans l’espace de travail d’application.

![Liste des rapports dans le service Power BI](media/migrate-tool/migrate-tool-upload-app-workspace.png)

<a name="upload-local-file"></a>
### <a name="upload-a-local-pbix-file"></a>Charger un fichier PBIX local

Vous pouvez charger une version locale d’un fichier Power BI Desktop. Vous devez fermer l’outil, modifier le fichier XML et placer le chemin complet de votre fichier PBIX local dans la propriété **PbixPath**.

```
<PbixPath>[Full Path to PBIX file]</PbixPath>
```

Après avoir modifié le fichier xml, rouvrez le plan dans l’outil de migration et chargez le rapport.

<a name="directquery-reports"></a>
### <a name="directquery-reports"></a>Rapports DirectQuery

Vous devez mettre à jour la chaîne de connexion pour les rapports DirectQuery. Vous pouvez le faire dans *powerbi.com*, ou vous pouvez interroger par programmation la chaîne de connexion à partir de Power BI Embedded (Paas). Pour obtenir un exemple, consultez [Extraire une chaîne de connexion DirectQuery à partir d’un rapport PaaS](migrate-code-snippets.md#extract-directquery-connection-string-from-power-bi-workspace-collections).

Vous pouvez ensuite mettre à jour la chaîne de connexion pour le jeu de données dans le service Power BI et définir les informations d’identification pour la source de données. Vous pouvez consulter les exemples suivants pour voir comment effectuer cette opération.

* [Mettre à jour une chaîne de connexion DirectQuery dans Power BI Embedded](migrate-code-snippets.md#update-directquery-connection-string-in-power-bi-embedded)
* [Définir les informations d’identification DirectQuery dans Power BI Embedded](migrate-code-snippets.md#set-directquery-credentials-in-power-bi-embedded)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vos rapports ont été migrés de Collections d’espaces de travail Power BI vers Power BI Embedded, vous pouvez mettre à jour votre application et commencer l’incorporation des rapports dans cet espace de travail d’application.

Pour plus d’informations, consultez [Guide pratique pour migrer du contenu Collections d’espaces de travail Power BI vers Power BI Embedded](migrate-from-power-bi-workspace-collections.md).

Des questions ? [Essayer d’interroger la communauté Power BI](http://community.powerbi.com/)
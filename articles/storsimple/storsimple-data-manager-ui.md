---
title: Interface utilisateur Microsoft Azure StorSimple Data Manager | Microsoft Docs
description: "Décrit comment utiliser l’interface utilisateur du service StorSimple Data Manager (version préliminaire privée)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/22/2016
ms.author: vidarmsft
translationtype: Human Translation
ms.sourcegitcommit: 692f00bf342e2cd6d3d76754d18751c773aeedc5
ms.openlocfilehash: 53a8599df2c647613122cd791b680e2e658586b0

---

# <a name="manage-using-the-storsimple-data-manager-service-ui-private-preview"></a>Gérer l’utilisation de l’interface utilisateur du service StorSimple Data Manager (version préliminaire privée)

Cet article vous explique comment utiliser l’interface utilisateur StorSimple Data Manager pour effectuer la transformation des données hébergées sur les appareils StorSimple série 8000. Les données transformées peuvent ensuite être consommées par d’autres services Azure, comme Azure Media Services, Azure HDInsight, Azure Machine Learning et Azure Search. 


## <a name="use-storsimple-data-transformation"></a>Utiliser la transformation des données StorSimple

StorSimple Data Manager est la ressource dans laquelle il est possible d’instancier la transformation des données. Le service de transformation de données vous permet de déplacer des données de votre appareil StorSimple sur site vers des objets blob du stockage Azure. Par conséquent, dans le workflow, il vous faudra spécifier les détails sur l’appareil StorSimple et les données intéressantes que vous souhaitez transférer sur le compte de stockage.

### <a name="create-a-storsimple-data-manager-service"></a>Créer un service StorSimple Data Manager

Pour créer un service StorSimple Data Manager, procédez comme suit.

1. Pour créer un service StorSimple Data Manager, accédez à l’adresse [https://aka.ms/HybridDataManager](https://aka.ms/HybridDataManager).

2. Cliquez sur l’icône **+**, puis recherchez StorSimple Data Manager. Cliquez sur votre service StorSimple Data Manager, puis sur **Créer**.

3. Si votre abonnement prend en charge la création de ce service, le panneau suivant s’affiche.

    ![Créer une ressource StorSimple Data Manager](./media/storsimple-data-manager-ui/create-new-data-manager-service.png)

4. Indiquez les entrées, puis cliquez sur **Créer**. L’emplacement spécifié doit être celui qui héberge vos comptes de stockage et votre service StorSimple Manager. Actuellement, seules les États-Unis de l’Ouest et l’Europe de l’Ouest sont prises en charge. Par conséquent, votre service StorSimple Manager, votre service Data Manager et le compte de stockage associé doivent tous être situés dans les régions précédemment citées prises en charge. La création du service prend environ 1 minute.

### <a name="create-a-data-transformation-job-definition"></a>Créer une définition de travail de transformation de données

Au sein d’un service StorSimple Data Manager, vous devez créer une définition de travail de transformation de données. Une définition de travail spécifie des détails sur les données que vous souhaitez transférer dans un compte de stockage au format natif. 

Procédez comme suit pour créer un travail de transformation de données.

1.  Accédez au service créé. Cliquez sur **+ Définition du travail**.

    ![Cliquez sur +Définition du travail](./media/storsimple-data-manager-ui/click-add-job-definition.png)

2. Le nouveau panneau de définition de travail s’ouvre. Attribuez un nom à votre définition de travail, puis cliquez sur **Source**. Dans le panneau **Configurer la source de données**, spécifiez les détails de votre appareil StorSimple et les données intéressantes.

    ![Créer une définition de travail](./media/storsimple-data-manager-ui//create-new-job-deifnition.png)

3. Dans la mesure où il s’agit d’un nouveau service Data Manager, aucun référentiel de données n’est configuré. Pour ajouter votre instance StorSimple manager en tant que référentiel de données, cliquez sur **Ajouter nouveau** dans la liste déroulante des référentiels de données, puis cliquez sur **Add Data Repository** (Ajouter un référentiel de données).

4. Sélectionnez **StorSimple 8000 series Manager** (StorSimple Manager série 8000) en tant que type de référentiel, puis saisissez les propriétés de votre instance **StorSimple Manager**. Pour le champ **ID de ressource**, vous devez saisir le numéro avant le symbole **:** dans la clé d’enregistrement de votre instance StorSimple Manager.

    ![Créer une source de données](./media/storsimple-data-manager-ui/create-new-data-source.png)

5.  Une fois l’opération terminée, cliquez sur **OK**. Votre référentiel de données est enregistré, et cette instance StorSimple Manager peut être réutilisée dans d’autres définitions de travail, sans nouvelle saisie de ces paramètres. Une fois que vous avez cliqué sur **OK**, attendez quelques secondes pour voir apparaître StorSimple Manager dans la liste déroulante.

6.  Dans le panneau **Configurer la source de données**, saisissez le nom de l’appareil et le nom de volume associé à vos données intéressantes.

7.  Dans la sous-section **Filtrer**, saisissez le répertoire racine comportant vos données intéressantes (ce champ doit commencer par `\`). Les filtres de fichiers s’ajoutent également ici.

8.  Le service de transformation de données traite les données qui sont envoyées à Azure via des instantanés. Durant l’exécution du travail, vous pouvez décider de procéder à une sauvegarde à chaque itération (pour traiter les données les plus récentes) ou d’utiliser la dernière sauvegarde existante dans le cloud (si vous traitez des données archivées).

    ![Nouveaux détails de la source de données](./media/storsimple-data-manager-ui/new-data-source-details.png)

9. Ensuite, vous devez configurer les paramètres de la cible. Il existe 2 types de cibles prises en charge : les comptes de stockage Azure et les comptes Azure Media Services. Choisissez les comptes de stockage, afin de placer des fichiers dans des objets blob de ce compte. Choisissez le compte de services multimédia, afin de placer des fichiers dans les ressources de ce compte. Là encore, il nous faut ajouter un référentiel. Dans la liste déroulante, sélectionnez **Ajouter nouveau**, puis **Configuration des paramètres**.

    ![Créer un récepteur de données](./media/storsimple-data-manager-ui/create-new-data-sink.png)

10. Ici, vous pouvez sélectionner le type de référentiel que vous souhaitez ajouter et les autres paramètres qui lui sont associés. Dans les deux cas, une file d’attente de stockage est créée durant l’exécution du travail. Cette file d’attente est renseignée avec des messages sur les objets blob transformés, au fur et à mesure de leur mise à disposition. Le nom de cette file d’attente est identique à celui de la définition du travail. Si vous sélectionnez **Services Multimédia** comme type de référentiel, vous pouvez également saisir les informations d’identification du compte de stockage à l’emplacement de création de la file d’attente.

    ![Détails sur le nouveau récepteur de données](./media/storsimple-data-manager-ui/new-data-sink-details.png)

11. Une fois le référentiel de données ajouté (cette opération prend quelques secondes), vous trouverez le référentiel dans la liste déroulante de l’option **Nom de compte cible**.  Choisissez la cible dont vous avez besoin.

12. Cliquez sur **OK** pour créer la définition du travail. Votre définition de travail est désormais configurée. Vous pouvez utiliser cette définition de travail plusieurs fois via l’interface utilisateur.

    ![Ajouter une définition de travail](./media/storsimple-data-manager-ui/add-new-job-definition.png)

### <a name="run-the-job-definition"></a>Exécuter la définition de travail

Chaque fois qu’il vous est nécessaire de transférer des données de StorSimple vers le compte de stockage spécifié dans la définition de travail, vous devez appeler cette dernière. Il existe une certaine flexibilité dans la façon de modifier les paramètres chaque fois que vous appelez le travail. La procédure comporte trois étapes :

1. Sélectionnez votre service StorSimple Data Manager, puis accédez à **Analyse**. Cliquez sur **Exécuter maintenant**.

    ![Déclencher la définition de travail](./media/storsimple-data-manager-ui/run-now.png)

2. Sélectionnez la définition de travail que vous souhaitez exécuter. Cliquez sur **Paramètres d'exécution** afin de modifier les paramètres à changer pour cette exécution de travail.

    ![Exécuter les paramètres de travail](./media/storsimple-data-manager-ui/run-settings.png)

3. Cliquez sur **OK**, puis sur **Exécuter** pour lancer votre travail. Pour analyser ce travail, accédez à la page **Travaux** de votre instance StorSimple Data Manager.

    ![Liste et état des travaux](./media/storsimple-data-manager-ui/jobs-list-and-status.png)

4. En plus du panneau **Travaux**, vous pouvez également examiner la file d’attente de stockage, dans laquelle un message est ajouté chaque fois qu’un fichier est déplacé de StorSimple vers le compte de stockage.


## <a name="next-steps"></a>Étapes suivantes

[Utilisez .NET SDK pour lancer les travaux StorSimple Data Manager](storsimple-data-manager-dotnet-jobs.md).


<!--HONumber=Nov16_HO4-->



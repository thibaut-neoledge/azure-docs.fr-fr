---

title: "Chargement de données dans Azure SQL Data Warehouse – Data Factory | Microsoft Docs"
description: "Ce didacticiel charge les données dans Azure SQL Data Warehouse à l’aide d’Azure Data Factory et utilise une base de données SQL Server comme source de données."
services: sql-data-warehouse
documentationcenter: NA
author: linda33wj
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse;azure-data-factory
ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: jingwang;kevin;barbkess
ms.custom: loading
translationtype: Human Translation
ms.sourcegitcommit: 6474104846eefa1aa7e137e7914b7a7f1ee8a83a
ms.openlocfilehash: aad76a633b127d23d59dae995d7a503023c5eac7
ms.lasthandoff: 02/09/2017


---

# <a name="load-data-into-sql-data-warehouse-with-data-factory"></a>Chargement de données dans SQL Data Warehouse avec Data Factory

Vous pouvez utiliser Azure Data Factory pour charger des données dans Azure SQL Data Warehouse à partir d’un des [magasins de données sources pris en charge](../data-factory/data-factory-data-movement-activities.md#supported-data-stores-and-formats). Par exemple, vous pouvez charger des données dans un entrepôt de données SQL à partir d’une base de données SQL Azure ou d’une base de données Oracle à l’aide de Data Factory. Le didacticiel de cet article vous montre comment charger des données dans un entrepôt de données SQL à partir d’une base de données SQL Server locale.

**Durée estimée** : ce didacticiel dure environ 10 à 15 minutes une fois les conditions préalables remplies.

## <a name="prerequisites"></a>Composants requis

- Vous avez besoin d’une **base de données SQL Server** avec des tables qui contiennent les données à copier dans SQL Data Warehouse.  

- Vous avez besoin d’un **SQL Data Warehouse** en ligne. Si vous n’en possédez pas encore, découvrez comment [créer un SQL Data Warehouse](sql-data-warehouse-get-started-provision.md).

- Vous avez besoin d’un **compte de stockage Azure**. Si vous ne possédez pas encore de compte de stockage Azure, découvrez [comment créer un compte de stockage](../storage/storage-create-storage-account.md). Les performances se trouvent améliorées si le compte de stockage et l’entrepôt de données se situent dans la même région Azure.

## <a name="configure-a-data-factory"></a>Configurer une fabrique de données
1. Connectez-vous au [portail Azure][].
2. Localisez votre entrepôt de données et cliquez pour l’ouvrir.
3. Dans le panneau principal, cliquez sur **Charger les données** > **Azure Data Factory**.

    ![Lancer l’Assistant Charger des données](media/sql-data-warehouse-load-with-data-factory/launch-load-data-wizard.png)

4. Si vous n’avez pas de fabrique de données dans votre abonnement Azure, une boîte de dialogue **Nouvelle fabrique de données** apparaît dans un onglet distinct du navigateur. Renseignez les informations demandées et cliquez sur **Créer**. Une fois la fabrique de données créée, la boîte de dialogue **Nouvelle fabrique de données** se ferme et la boîte de dialogue **Sélectionner une fabrique de données** apparaît.

    Si vous avez déjà une ou plusieurs fabriques de données dans l’abonnement Azure, la boîte de dialogue **Sélectionner une fabrique de données** s’affiche. Dans cette boîte de dialogue, vous pouvez sélectionner une fabrique de données existante ou cliquer sur **Créer une fabrique de données** pour en créer une nouvelle.

    ![Configurer une fabrique de données](media/sql-data-warehouse-load-with-data-factory/configure-data-factory.png)

5. Dans la boîte de dialogue **Sélectionner une fabrique de données**, l’option **charger les données** est sélectionnée par défaut. Cliquez sur **Suivant** pour commencer à créer une tâche de chargement de données.

## <a name="configure-the-data-factory-properties"></a>Configurer les propriétés de la fabrique de données
Une fois la fabrique de données créée, l’étape suivante consiste à configurer la planification de chargement des données.

1. Pour le **nom de la tâche**, entrez **DWLoadData-fromSQLServer**.
2. Utilisez l’option par défaut **Exécuter une fois**, cliquez sur **Suivant**.

    ![Configurer la planification](media/sql-data-warehouse-load-with-data-factory/configure-load-schedule.png)

## <a name="configure-the-source-data-store-and-gateway"></a>Configurer le magasin de données source et la passerelle
Maintenant, il convient d’indiquer la base de données SQL Server locale à partir de laquelle vous souhaitez charger des données.

1. Choisissez **SQL Server** à partir du catalogue de stockage des données source pris en charge, puis cliquez sur **Suivant**.

    ![Choisir la source du serveur SQL](media/sql-data-warehouse-load-with-data-factory/choose-sql-server-source.png)

2. Une boîte de dialogue **Specify the on-premises SQL Server database** (Définir la base de données SQL Server locale) s’affiche. Le premier champ **Nom de la connexion** est automatiquement renseigné. Le deuxième champ vous demande le nom de la **passerelle**. Si vous utilisez une fabrique de données existante qui dispose déjà d’une passerelle, vous pouvez réutiliser la passerelle en la sélectionnant dans la liste déroulante. Cliquez sur le lien **Créer une passerelle** pour créer une passerelle de gestion des données.  

    > [!NOTE]
    > Si le magasin de données source est sur site ou dans une machine virtuelle Azure IaaS, une passerelle de gestion des données est requise. Une passerelle a une relation 1-1 avec une fabrique de données. Elle ne peut pas être utilisée à partir d’une autre fabrique de données, mais elle peut être utilisée par plusieurs tâches de chargement de données au sein de la même fabrique de données. Une passerelle peut être utilisée pour se connecter à différents magasins de données lors de l’exécution de tâches de chargement de données.
    >
    > Pour obtenir des informations détaillées sur la passerelle, consultez l’article [Passerelle de gestion des données](../data-factory/data-factory-data-management-gateway.md).

3. Une boîte de dialogue **Créer une passerelle** s’affiche. Dans le champ Nom, entrez **GatewayForDWLoading**, puis cliquez sur **Créer**.

4. Une boîte de dialogue **Configurer la passerelle** s’affiche. Cliquez sur **Lancer le programme d’installation Express sur cet ordinateur** pour télécharger, installer et enregistrer automatiquement la passerelle de gestion des données sur votre ordinateur actuel. La progression s’affiche dans une fenêtre contextuelle. Si l’ordinateur ne parvient pas à se connecter au magasin de données, vous pouvez [télécharger et installer manuellement la passerelle](https://www.microsoft.com/download/details.aspx?id=39717) sur un ordinateur en mesure de se connecter au magasin de données, puis utiliser la clé pour l’inscrire.
    > [!NOTE]
    > Le programme d’installation express fonctionne en mode natif avec Microsoft Edge et Internet Explorer. Si vous utilisez Google Chrome, vous devez tout d’abord installer l’extension ClickOnce à partir du Chrome Web Store.

    ![Lancer le programme d’installation express](media/sql-data-warehouse-load-with-data-factory/launch-express-setup.png)

5. Attendez la fin du programme d’installation de la passerelle. Une fois la passerelle enregistrée et en ligne, la fenêtre contextuelle se ferme et la nouvelle passerelle s’affiche dans le champ prévu à cet effet. Puis remplissez le reste des champs requis comme suit, et cliquez sur **Suivant**.
    - **Nom du serveur** : nom du serveur SQL local.
    - **Nom de la base de données** : base de données SQL Server.
    - **Chiffrement des informations d’identification** : utilisez la valeur par défaut « By web browser » (Par navigateur Web).
    - **Type d’authentification** : choisissez le type d’authentification que vous utilisez.
    - **Nom d’utilisateur** et **mot de passe** : entrez le nom d’utilisateur et le mot de passe pour un utilisateur autorisé à copier les données.

    ![Lancer le programme d’installation express](media/sql-data-warehouse-load-with-data-factory/configure-sql-server.png)

6. L’étape suivante consiste à choisir les tables à partir desquelles copier les données. Vous pouvez filtrer les tables à l’aide de mots-clés. Vous pouvez également afficher un aperçu des données et du schéma de table dans le panneau inférieur. Une fois la sélection terminée, cliquez sur **Suivant**.

    ![Sélectionner des tables](media/sql-data-warehouse-load-with-data-factory/select-tables.png)

## <a name="configure-the-destination-your-sql-data-warehouse"></a>Configurer la destination, qui est votre SQL Data Warehouse

À présent, vous allez indiquer les informations de destination à Data Factory.

1. Vos informations de connexion SQL Data Warehouse sont renseignées automatiquement. Entrez le mot de passe correspondant au nom d’utilisateur. , puis cliquez sur **Suivant**.

    ![Configurer la destination](media/sql-data-warehouse-load-with-data-factory/configure-destination.png)

2. Un mappage de table intelligent mettant en correspondance la source avec des tables de destination en fonction des noms de la table, s’affiche. Si la table n’existe pas dans la destination, par défaut ADF en créera une avec le même nom (cela s’applique à SQL Server ou Azure SQL Database comme source). Vous pouvez également choisir d’effectuer un mappage vers une table existante. Vérifiez, puis cliquez sur **Suivant**.

    ![Mapper les tables](media/sql-data-warehouse-load-with-data-factory/table-mapping.png)

3. Vérifiez le mappage de schéma et recherchez les messages d’erreur ou d’avertissement. Le mappage intelligent repose sur le nom de colonne. Si la conversion du type de données n’est pas prise en charge entre la colonne de destination source et de destination, un message d’erreur s’affiche le long de la table correspondante. Si vous choisissez de laisser la fabrique de données créer automatiquement les tables, une conversion appropriée du type de données peut se produire si nécessaire pour corriger l’incompatibilité entre les magasins source et de destination.

    ![Mapper le schéma](media/sql-data-warehouse-load-with-data-factory/schema-mapping.png)

4. Cliquez sur **Next**.

## <a name="configure-the-performance-settings"></a>Configurer les paramètres de performances
Dans le cadre des configurations de performances, vous configurez un compte de stockage Azure utilisé pour le transit des données avant leur chargement dans SQL Data Warehouse efficacement à l’aide de [PolyBase](sql-data-warehouse-best-practices.md#use-polybase-to-load-and-export-data-quickly). Une fois la copie terminée, les données temporaires du stockage seront nettoyées automatiquement.

Sélectionnez un compte de stockage Azure existant, puis cliquez sur **Suivant**.

![Configurer le blob intermédiaire](media/sql-data-warehouse-load-with-data-factory/configure-staging-blob.png)

## <a name="review-summary-information-and-deploy-the-pipeline"></a>Passez en revue les informations de résumé et déployez le pipeline

Passez en revue la configuration, puis cliquez sur le bouton **Terminer** pour déployer le pipeline.

![Déployer la fabrique de données](media/sql-data-warehouse-load-with-data-factory/deploy-data-factory.png)

## <a name="monitor-data-loading-progress"></a>Surveiller la progression du chargement des données

La progression et les résultats du déploiement s’affichent sur la page **Déploiement**.

1. Une fois le déploiement terminé, cliquez sur le lien **Click here to monitor copy pipeline** (Cliquez ici pour surveiller la copie du pipeline), afin de surveiller la progression du chargement des données.

    ![Surveillance d’un pipeline](media/sql-data-warehouse-load-with-data-factory/monitor-pipeline.png)

2. Le pipeline de chargement des données **DWLoadData-fromSQLServer** nouvellement créé est automatiquement sélectionné à partir de l’**Explorateur de ressources** situé sur la gauche.

    ![Afficher le pipeline](media/sql-data-warehouse-load-with-data-factory/view-pipeline.png)

3. Cliquez sur le pipeline du panneau du milieu pour afficher l’état détaillé de chaque table qui correspond à une activité.

    ![Afficher l’activité de la table](media/sql-data-warehouse-load-with-data-factory/view-table-activity.png)

4. Cliquez sur une activité pour que les informations relatives au chargement des données, y compris la taille des données, les lignes, le débit, etc., s’affichent dans le volet droit.

    ![Afficher les informations relatives à l’activité des tables](media/sql-data-warehouse-load-with-data-factory/view-table-activity-details.png)

5. Pour lancer cet affichage d’analyse ultérieurement, accédez à votre SQL Data Warehouse, cliquez sur **Charger les données > Azure Data Factory**, sélectionnez votre fabrique et choisissez **Surveiller les tâches de chargement existant**.

## <a name="next-steps"></a>Étapes suivantes

Pour migrer votre base de données vers SQL Data Warehouse, consultez [Vue d’ensemble de la migration](sql-data-warehouse-overview-migrate.md).

Pour en savoir plus sur Azure Data Factory et ses capacités de déplacement de données, consultez les articles suivants :

- [Présentation du service Azure Data Factory](../data-factory/data-factory-introduction.md)
- [Déplacer des données à l’aide de l’activité de copie](../data-factory/data-factory-data-movement-activities.md)
- [Déplacer des données vers et depuis Azure SQL Data Warehouse à l’aide d’Azure Data Factory](../data-factory/data-factory-azure-sql-data-warehouse-connector.md)

Pour explorer vos données dans SQL Data Warehouse, consultez les articles suivants :

- [Se connecter à SQL Data Warehouse avec Visual Studio et SSDT](sql-data-warehouse-query-visual-studio.md)
- [Données visuelles avec Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)

<!-- Azure references -->
[portail Azure]: https://portal.azure.com


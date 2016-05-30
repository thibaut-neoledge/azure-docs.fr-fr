<properties 
   pageTitle="Gestion d'Azure Data Lake Analytics à l'aide du portail Azure | Azure" 
   description="Apprenez à gérer des comptes Data Lake Analytics, des sources de données, des utilisateurs et des travaux." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# Gestion d'Azure Data Lake Analytics à l'aide du portail Azure

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Apprenez à gérer des comptes Azure Data Lake Analytics, des sources de données, des utilisateurs et des travaux à l'aide du portail Azure. Pour afficher les rubriques de gestion à l'aide d'autres outils, cliquez sur l'onglet de sélection ci-dessus.

**Configuration requise**

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).




<!-- ################################ -->
<!-- ################################ -->
## Gérer les comptes

Avant d'exécuter des travaux Data Lake Analytics, vous devez avoir un compte Data Lake Analytics. Contrairement à Azure HDInsight, vous ne payez pas pour un compte Analytics lorsque celui-ci n'exécute aucun travail. Vous payez uniquement lorsqu'il exécute un travail. Pour plus d'informations, consultez [Présentation d'Azure Data Lake Analytics](data-lake-analytics-overview.md).

**Pour créer des comptes Data Lake Analytics**

1. Inscrivez-vous au nouveau [portail Azure](https://portal.azure.com).
2. Cliquez sur **Nouveau**, **Données + Analyse**, puis sur **Data Lake Analytics**.
6. Tapez ou sélectionnez les valeurs suivantes :

	![Volet du portail Azure Data Lake Analytics](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)

	- **Nom** : nom du compte Analytics.
	- **Data Lake Store** : chaque compte Data Lake Analytics possède un compte Azure Data Lake Store dépendant. Le compte Data Lake Analytics et le compte Data Lake Store dépendant doivent se trouver dans le même centre de données Azure. Suivez les instructions pour créer un compte Data Lake Store ou sélectionnez-en un existant.
	- **Abonnement** : choisissez l'abonnement Azure utilisé pour le compte Analytics.
	- **Groupe de ressources**. Sélectionnez un groupe de ressources Azure existant ou créez-en un. Azure Resource Manager (ARM) vous permet de manipuler les ressources de votre application sous la forme d'un groupe. Pour plus d'informations, consultez [Présentation d'Azure Resource Manager](resource-group-overview.md). 
	- **Emplacement**. Sélectionnez un centre de données Azure pour le compte Data Lake Analytics. 

8. Cliquez sur **Create**. L'écran d'accueil du portail s'affiche. Une nouvelle vignette est ajoutée au Tableau d'accueil indiquant « Déploiement d'Azure Data Lake Analytics ». La création d'un compte Data Lake Analytics prend plusieurs minutes. Lorsque le compte est créé, le portail ouvre le compte dans un nouveau volet.

	![Volet du portail Azure Data Lake Analytics](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-blade.png)

<a name="access-adla-account"></a> **Pour accéder à/ouvrir un compte Data Lake Analytics**

1. Inscrivez-vous au nouveau [portail Azure](https://portal.azure.com/).
2. Cliquez sur **Parcourir** dans le menu de gauche, puis cliquez sur **Data Lake Analytics**.
3. Cliquez sur le compte Data Lake Analytics auquel vous souhaitez accéder. Le compte s'ouvre dans un nouveau volet.

**Pour supprimer un compte Data Lake Analytics**

1. Ouvrez le compte Data Lake Analytics que vous voulez supprimer. Pour obtenir des instructions, consultez [Accès aux comptes Data Lake Analytics](#access-adla-account).
2. Cliquez sur **Supprimer** à partir du menu du bouton en haut du volet.
3. Tapez le nom du compte, puis cliquez sur **Supprimer**.

Le fait de supprimer un compte Analytics ne supprime pas le compte Data Lake Store dépendant. Pour obtenir des instructions sur la suppression de comptes de stockage Data Lake, consultez [Supprimer un compte Data Lake Store](data-lake-store-get-started-portal.md#delete-azure-data-lake-store-account).




























<!-- ################################ -->
<!-- ################################ -->
## Gestion des sources de données du compte

Data Lake Analytics prend actuellement en charge les sources de données suivantes :

- [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
- [Azure Storage](../storage/storage-introduction.md)

Lorsque vous créez un compte Data Lake Analytics, vous devez désigner un compte Azure Data Lake Store comme compte de stockage par défaut. Le compte Data Lake Store par défaut est utilisé pour stocker les métadonnées du travail et les journaux d'audit du travail. Après la création d'un compte Data Lake Analytics, vous pouvez ajouter des comptes Data Lake Store et/ou des comptes Azure Storage supplémentaires.

<a name="default-adl-account"></a>**Pour rechercher le compte de stockage Data Lake par défaut**

- Ouvrez le compte Data Lake Analytics que vous voulez gérer. Pour obtenir des instructions, consultez [Accès aux comptes Data Lake Analytics](#access-adla-account). Le compte Data Lake Store par défaut est affiché sous **Essential** :

	![Ajouter une source de données Azure Data Lake Analytics](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-default-adl-storage-account.png)

**Pour ajouter des sources de données supplémentaires**

1. Ouvrez le compte Data Lake Analytics que vous voulez gérer. Pour obtenir des instructions, consultez [Accès aux comptes Data Lake Analytics](#access-adla-account).
2. Cliquez sur **Paramètres**, puis sur **Sources de données**. Le compte Data Lake Store par défaut doit être répertorié à cet endroit. 
3. Cliquez sur **Ajouter une source de données**.

	![Ajouter une source de données Azure Data Lake Analytics](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-add-data-source.png)

	Pour ajouter un compte Azure Data Lake Store, vous avez besoin du nom du compte et de l'accès au compte pour pouvoir l'interroger. Pour ajouter un stockage d'objets Blob Azure, vous avez besoin du compte de stockage et de la clé de compte, que vous trouverez en accédant au compte de stockage dans le portail.

<a name="explore-data-sources"></a>**Pour explorer les sources de données**

1. Ouvrez le compte Analytics que vous voulez gérer. Pour obtenir des instructions, consultez [Accès aux comptes Data Lake Analytics](#access-adla-account).
2. Cliquez sur **Paramètres**, puis sur **Explorateur de données**. 
 
	![Explorateur de données Azure Data Lake Analytics](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-data-explorer.png)
	
3. Cliquez sur un compte Data Lake Store pour l'ouvrir.

	![Compte de stockage Explorateur de données Azure Data Lake Analytics](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-explore-adls.png)
	
	Pour chaque compte Data Lake Store, vous pouvez
	
	- **Nouveau dossier** : ajouter un nouveau dossier.
	- **Télécharger** : télécharger des fichiers vers le compte de stockage à partir de votre poste de travail.
	- **Accès** : configurer les autorisations d'accès.
	- **Renommer le dossier** : renommer un dossier.
	- **Propriétés du dossier** : afficher les propriétés du fichier ou du dossier, comme le chemin WASB, le chemin WEBHDFS, l'heure de la dernière modification, et ainsi de suite.
	- **Supprimer le dossier** : supprimer un dossier.

<a name="upload-data-to-adls"></a> **Pour télécharger des fichiers vers le compte Data Lake Store**

1. À partir du portail, cliquez sur **Parcourir** dans le menu de gauche, puis cliquez sur **Data Lake Store**.
2. Cliquez sur le compte Data Lake Store vers lequel vous souhaitez télécharger des données. Pour trouver le compte de stockage Data Lake par défaut, consultez [ceci](#default-adl-account).
3. Cliquez sur **Explorateur de données** dans le menu supérieur.
4. Cliquez sur **Nouveau répertoire** pour créer un nouveau dossier ou cliquez sur le nom d'un dossier pour modifier le dossier.
6. Cliquez sur **Télécharger** dans le menu supérieur pour télécharger le fichier.


<a name="upload-data-to-wasb"></a> **Pour télécharger des fichiers vers le compte de stockage d'objets blob Azure**

Consultez [Téléchargement de données pour les tâches Hadoop dans HDInsight](../hdinsight/hdinsight-upload-data.md). Les informations s'appliquent à Data Lake Analytics.


## Gestion des utilisateurs

Data Lake Analytics utilise un contrôle d'accès basé sur le rôle avec Azure Active Directory. Lorsque vous créez un compte Data Lake Analytics, un rôle « Administrateurs d'abonnements » est ajouté au compte. Vous pouvez ajouter des utilisateurs et des groupes de sécurité supplémentaires avec les rôles suivants :

|Rôle|Description|
|----|-----------|
|Propriétaire|Vous permet de gérer tout, y compris l'accès aux ressources.|
|Collaborateur|Accéder au portail ; soumettre et contrôler les travaux. Pour être en mesure de soumettre des travaux, un collaborateur a également besoin de l'autorisation de lecture ou d'écriture sur les comptes Data Lake Store.|
|DataLakeAnalyticsDeveloper | L'utilisateur peut soumettre des travaux, surveiller tous les travaux, mais peut uniquement annuler ses propres travaux. Il ne peut pas gérer son propre compte, par exemple, ajouter des utilisateurs, modifier les autorisations ou supprimer le compte. Pour pouvoir exécuter des travaux, l'utilisateur a besoin de l'accès en lecture ou en écriture aux comptes Data Lake Store | 
|Lecteur|Vous permet de tout afficher, mais sans apporter de modifications.|  
|Utilisateur de DevTest Labs|Vous permet de tout afficher et connecter, démarrer, redémarrer et arrêter les machines virtuelles.|  
|Administrateur de l'accès utilisateur|Vous permet de gérer l'accès utilisateur aux ressources Azure.|  

Pour plus d'informations sur la création d'utilisateurs et de groupes de sécurité Azure Active Directory, consultez [Qu'est-ce qu'Azure Active Directory ?](../active-directory/active-directory-whatis.md).

**Pour ajouter des utilisateurs ou des groupes de sécurité à un compte Analytics**

1. Ouvrez le compte Analytics que vous voulez gérer. Pour obtenir des instructions, consultez [Accès aux comptes Data Lake Analytics](#access-adla-account).
2. Cliquez sur **Paramètres**, puis sur **Utilisateurs**. Vous pouvez également cliquer sur **Accès** dans la barre de titre **Essentials**, comme illustré dans la capture d'écran suivante :

	![Ajouter des utilisateurs au compte Azure Data Lake Analytics](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-access-button.png)
3. Dans le volet **Utilisateur**, cliquez sur **Ajouter**.
4. Sélectionnez un rôle et ajoutez un utilisateur, puis cliquez sur **OK**.

**Remarque : si cet utilisateur ou groupe de sécurité a besoin d'envoyer des travaux, il devra avoir l'autorisation d'accéder à Data Lake Store également. Pour plus d'informations, consultez [Sécuriser les données stockées dans Data Lake Store](../data-lake-store/data-lake-store-secure-data.md).**





<!-- ################################ -->
<!-- ################################ -->
## Gestion des travaux

Vous devez disposer d'un compte Data Lake Analytics avant de pouvoir exécuter des travaux quelconques. Pour plus d'informations, consultez [Gestion des comptes Data Lake Analytics](#manage-data-lake-analytics-accounts).

<a name="create-job"></a>**Pour créer un travail**

1. Ouvrez le compte Analytics que vous voulez gérer. Pour obtenir des instructions, consultez [Accès aux comptes Data Lake Analytics](#access-adla-account).
2. Cliquez sur **Nouveau travail**.

	![créer des travaux U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-create-job-button.png)

	Un nouveau volet semblable à ce qui suit s'affiche :

	![créer des travaux U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-new-job.png)

	Pour chaque travail, vous pouvez configurer


	|Nom|Description|
	|----|-----------|
	|Nom du travail|Entrez le nom du travail.|
	|Priorité|Un numéro inférieur représente une priorité plus élevée. Si deux travaux sont en file d'attente, celui dont la priorité est la plus faible sera exécuté en premier|
	|Parallélisme |Nombre maximal de processus de calcul qui peuvent se produire en même temps. L'augmentation de cette valeur peut améliorer les performances, mais peut également augmenter le coût.|
	|Script|Entrez le script U-SQL pour le travail.|

	À l'aide de la même interface, vous pouvez également explorer les sources de données du lien et ajouter des fichiers supplémentaires aux sources de données liées. 
3. Cliquez sur **Soumettre le travail** si vous souhaitez soumettre le travail.

**Pour soumettre un travail**

Consultez [Créer des travaux Data Lake Analytics](#create-job).

<a name="monitor-jobs"></a>**Pour surveiller les travaux**

1. Ouvrez le compte Analytics que vous voulez gérer. Pour obtenir des instructions, consultez [Accès aux comptes Data Lake Analytics](#access-adla-account). Le volet Gestion des travaux affiche les informations de base du travail :

	![gérer des travaux U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-manage-jobs.png)

3. Cliquez sur **Gestion des travaux** comme le montre la capture d'écran précédente.

	![gérer des travaux U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-manage-jobs-details.png)

4. Cliquez sur un travail dans la liste. Ou cliquez sur **Filtre** pour vous aider à trouver les travaux :

	![filtrer des travaux U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-filter-jobs.png)

	Vous pouvez filtrer les travaux par **Période**, **Nom du travail** et **Auteur**.
5. Cliquez sur **Renvoyer** si vous souhaitez soumettre à nouveau le travail.

**Pour renvoyer un travail**

Consultez [Surveiller les travaux Data Lake Analytics](#monitor-jobs).

##Surveillance de l'utilisation du compte

**Pour surveiller l'utilisation du compte**

1. Ouvrez le compte Analytics que vous voulez gérer. Pour obtenir des instructions, consultez [Accès aux comptes Data Lake Analytics](#access-adla-account). Le panneau d'utilisation affiche l'utilisation :

	![surveiller l'utilisation Azure Data Lake Analytics](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-monitor-usage.png)

2. Double-cliquez sur le volet pour afficher plus de détails.

##Affichage du catalogue U-SQL

Le [catalogue U-SQL](data-lake-analytics-use-u-sql-catalog.md) est utilisé pour structurer les données et le code afin que les scripts U-SQL puissent les partager. Le catalogue permet les meilleures performances possibles avec les données comprises dans Azure Data Lake. À partir du portail Azure, vous êtes en mesure d'afficher le catalogue U-SQL.

**Pour parcourir le catalogue U-SQL**

1. Ouvrez le compte Analytics que vous voulez gérer. Pour obtenir des instructions, consultez [Accès aux comptes Data Lake Analytics](#access-adla-account).
2. Cliquez sur **Explorateur de données** dans le menu supérieur.
3. Développez **Catalogue**, **master**, **Tables, ou **Fonctions table** ou **Assemblys**. La capture d'écran suivante montre une fonction table.

	![Compte de stockage Explorateur de données Azure Data Lake Analytics](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-explore-catalog.png)


<!-- ################################ -->
<!-- ################################ -->
## Utilisation des groupes Azure Resource Manager

Les applications sont généralement constituées de nombreux composants, par exemple une application web, base de données, serveur de base de données, stockage et services tiers. Azure Resource Manager (ARM) vous permet de manipuler les ressources de votre application sous la forme d’un groupe, nommé groupe de ressources Azure. Vous pouvez déployer, mettre à jour, surveiller ou supprimer toutes les ressources de votre application dans le cadre d’une opération unique et coordonnée. Vous utilisez un modèle de déploiement pouvant fonctionner avec différents environnements (environnements de test, intermédiaire et de production). Vous pouvez clarifier la facturation pour votre organisation en visualisant les coûts cumulés pour l’ensemble du groupe. Pour plus d'informations, consultez [Présentation d'Azure Resource Manager](../resource-group-overview.md).

Un service Data Lake Analytics peut inclure les composants suivants :

- Compte Azure Data Lake Analytics
- Compte Azure Data Lake Store par défaut requis
- Comptes Azure Data Lake Store supplémentaires
- Comptes Azure Storage supplémentaires

Vous pouvez créer tous ces composants dans un groupe ARM pour les rendre plus facile à gérer.

![compte et stockage Azure Data Lake Analytics](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Un compte Data Lake Analytics et les compte de stockage dépendants doivent se trouver dans le même centre de données Azure. Le groupe ARM peut cependant se trouver dans un autre centre de données.



##Voir aussi 

- [Vue d'ensemble de Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Prise en main de Data Lake Analytics à l'aide du portail Azure](data-lake-analytics-get-started-portal.md)
- [Gestion d'Azure Data Lake Analytics à l'aide d'Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
- [Surveiller et résoudre les problèmes des tâches Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

<!---HONumber=AcomDC_0518_2016-->
---
title: "Portail Azure : gérer l’audit de base de données SQL | Microsoft Docs"
description: "Configurez l’audit de base de données SQL Azure dans le portail Azure pour suivre les événements de base de données et les écrire dans un journal d’audit, dans votre compte Microsoft Azure Storage."
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: giladm
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: secure and protect
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/25/2017
ms.author: ronitr;giladm
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: d1350081aa4f70660802c61a77250496e1e7fc2a
ms.lasthandoff: 03/10/2017


---

# <a name="configure-and-manage-sql-database-auditing-in-the-azure-portal"></a>Configurer et gérer l’audit de base de données SQL dans le portail Azure

La section suivante explique comment configurer et gérer l’audit avec le Portail Azure. Pour configurer et gérer l’audit à l’aide de PowerShell, consultez [Configurer l’audit avec PowerShell](sql-database-auditing-powershell.md). 

Pour une présentation de l’audit, consultez [Audit de base de données SQL](sql-database-auditing.md).

## <a name="configure-blob-auditing"></a>Configurer l’audit d’objets blob

1. Ouvrez le [portail Azure](https://portal.azure.com) à l’adresse https://portal.azure.com.
2. Accédez au panneau des paramètres de Base de données SQL/SQL Server que vous voulez auditer. Dans le panneau Paramètres, sélectionnez **Audit et détection des menaces**.

    ![Volet de navigation](./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png)
3. Dans le panneau de configuration de l’audit de la base de données, vous pouvez cocher la case **Hériter des paramètres du serveur** pour indiquer que cette base de données doit être auditée en fonction des paramètres de son serveur. Si cette option est sélectionnée, vous voyez un lien **Afficher les paramètres d’audit du serveur** qui vous permet d’afficher ou de modifier les paramètres d’audit du serveur à partir de ce contexte.

    ![Volet de navigation][2]
4. Si vous préférez activer l’audit Objet blob sur le niveau base de données (en plus ou au lieu de l’audit au niveau serveur), **décochez** l’option **Hériter des paramètres du serveur**, définissez Audit sur **ACTIVÉ** et choisissez le type d’audit **Objet blob**.

    ![Volet de navigation][3]
5. Sélectionnez **Détails du stockage** pour ouvrir le panneau Stockage des journaux d’audit. Sélectionnez le compte de stockage Azure où les journaux sont enregistrés et la période de rétention après laquelle les anciens journaux sont supprimés, puis cliquez sur **OK** en bas. **Conseil :** utilisez le même compte de stockage pour toutes les bases de données auditées afin de profiter au mieux des modèles de rapport d’audit.

    <a id="storage-screenshot"></a>
    ![Volet de navigation][4]
6. Si vous souhaitez personnaliser les événements audités, vous pouvez le faire avec PowerShell ou l’API REST.
7. Une fois que vous avez configuré vos paramètres d’audit, vous pouvez activer la nouvelle fonctionnalité Détection des menaces (préversion) et configurer les adresses e-mail de réception des alertes de sécurité. La détection des menaces vous permet de recevoir des alertes proactives sur des activités anormales de la base de données qui peuvent indiquer des menaces de sécurité potentielles. Consultez la section [Détection de menaces](sql-database-threat-detection.md) pour plus de détails.
8. Cliquez sur **Save**.


## <a name="table-auditing"></a>Audit de table

> [!IMPORTANT]
> Avant de configurer **l’audit Table**, vérifiez que vous utilisez bien un [« Client de niveau inférieur »](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md). En outre, si vous avez des paramètres de pare-feu stricts, notez que le [point de terminaison IP de votre base de données change](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) lors de l’activation de l’audit Table.
>

1. Ouvrez le [portail Azure](https://portal.azure.com) à l’adresse https://portal.azure.com.
2. Accédez au panneau des paramètres de Base de données SQL/SQL Server que vous voulez auditer. Dans le panneau Paramètres, sélectionnez **Audit et détection des menaces** (*[voir la capture d’écran dans la section Audit Objet blob](#auditing-screenshot)*).
3. Dans le panneau de configuration de l’audit de la base de données, vous pouvez cocher la case **Hériter des paramètres du serveur** pour indiquer que cette base de données doit être auditée en fonction des paramètres de son serveur. Si cette option est sélectionnée, vous voyez un lien **Afficher les paramètres d’audit du serveur** qui vous permet d’afficher ou de modifier les paramètres d’audit du serveur à partir de ce contexte.

    ![Volet de navigation][2]
4. Si vous préférez ne pas hériter des paramètres d’audit du serveur, **décochez** l’option **Hériter des paramètres d’audit du serveur**, définissez Audit sur **ACTIVÉ** et choisissez le type d’audit **Table**.

    ![Volet de navigation][3-tbl]
5. Sélectionnez **Détails du stockage** pour ouvrir le panneau Stockage des journaux d’audit. Sélectionnez le compte de stockage Azure où les journaux sont enregistrés et la période de rétention après laquelle les anciens journaux sont supprimés. **Conseil :** Utilisez le même compte de stockage pour toutes les bases de données auditées afin de bénéficier au mieux des modèles de rapport d’audit (*[voir la capture d’écran de la section Audit Objet blob](#storage-screenshot)*).
6. Cliquez sur **Événements audités (Audited Events)** pour personnaliser les événements à auditer. Dans le panneau Journalisation par événement, cliquez sur **Succès** et sur **Échec** pour journaliser tous les événements, ou choisissez des catégories d’événements individuelles.

    ![Volet de navigation][5]
7. Une fois que vous avez configuré vos paramètres d’audit, vous pouvez activer la nouvelle fonctionnalité Détection des menaces (préversion) et configurer les adresses e-mail de réception des alertes de sécurité. La détection des menaces vous permet de recevoir des alertes proactives sur des activités anormales de la base de données qui peuvent indiquer des menaces de sécurité potentielles. Consultez la section [Détection de menaces](sql-database-threat-detection.md) pour plus de détails.
8. Cliquez sur **Save**.

## <a name="auditing-geo-replicated-databases"></a>Audit des bases de données géorépliquées

Quand vous utilisez des bases de données géorépliquées, il est possible de configurer l’audit sur la base de données primaire, sur la base de données secondaire ou sur les deux, selon le type d’audit.

**Audit Table** : vous pouvez configurer une stratégie distincte, au niveau base de données ou au niveau serveur, pour chacune des deux bases de données (primaire et secondaire).

**Audit Objet blob** : suivez ces instructions :

1. **Base de données primaire :** activez l’audit des objets blob sur le serveur ou la base de données.
2. **Base de données secondaire** : l’audit Objet blob ne peut pas être activé/désactivé depuis les paramètres d’audit de la base de données primaire.

   * Activez l’audit Objet blob sur la base de données primaire. L’audit Objet blob doit être activé sur la *base de données primaire elle-même*, et non pas sur le serveur.
   * Une fois que l’audit Blob est activé sur la base de données primaire, il devient également activé sur la base de données secondaire.

    > [!IMPORTANT]
    > Par défaut, les paramètres de stockage de la base de données secondaire sont identiques à ceux de la base de données primaire, ce qui provoque un trafic entre régions. Vous pouvez éviter cela en activant l’audit Blob sur le serveur secondaire et en configurant le stockage local dans les paramètres de stockage du serveur secondaire (ceci remplace l’emplacement de stockage de la base de données secondaire et fait que chaque base de données enregistre les journaux d’audit dans le stockage local).  

## <a name="viewing-blob-auditing-logs"></a>Affichage des journaux d’audit Objet blob

Les journaux d’audit Objet blob sont enregistrés sous la forme d’une collection de fichiers d’objets blob dans un conteneur nommé « **sqldbauditlogs** ».

Pour plus d’informations sur la hiérarchie des dossiers de stockage des journaux d’audit Objet blob, sur la convention de nommage des objets blob et sur le format des journaux, consultez les [informations de référence sur le format des journaux d’audit Objet blob (téléchargement de fichiers doc)](https://go.microsoft.com/fwlink/?linkid=829599).

Il existe plusieurs méthodes pour afficher des journaux d’audit Objet blob :

* Via le [portail Azure](https://portal.azure.com) : ouvrez la base de données correspondante. En haut du panneau Audit et détection des menaces de la base de données, cliquez sur **Afficher les journaux d’audit**.

    ![Volet de navigation][10]

    Un panneau Enregistrements d’audit s’ouvre, dans lequel s’affichent les journaux.

    - Vous pouvez choisir d’afficher des dates spécifiques en cliquant sur **Filtrer** en haut du panneau Enregistrements d’audit
    - Vous pouvez basculer entre les enregistrements d’audit qui ont été créés par la stratégie de serveur ou par l’audit de stratégie de base de données

    ![Volet de navigation][11]
* Téléchargez les fichiers journaux à partir de votre conteneur d’objets blob Stockage Azure via le portail ou avec un outil comme [l’Explorateur de stockage Azure](http://storageexplorer.com/).

    Une fois que vous avez téléchargé localement le fichier journal, vous pouvez double-cliquer sur le fichier pour ouvrir, afficher et analyser les journaux dans SSMS.

* Autres méthodes :

   * Vous pouvez télécharger plusieurs fichiers simultanément via l’Explorateur de stockage Azure : cliquez sur un sous-dossier spécifique (un sous-dossier qui inclut tous les fichiers journaux pour une date spécifique) et choisissez **Enregistrer sous** pour enregistrer dans un dossier local.

       Après avoir téléchargé plusieurs fichiers (ou toute une journée, comme décrit ci-dessus), vous pouvez les fusionner localement comme suit :

       **Ouvrez SSMS -> Fichier -> Ouvrir -> Fusionner les événements étendus -> Choisissez tous les fichiers à fusionner**
   * Par programmation :

     * [Lecteur des événements étendus de la bibliothèque C#](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/)
     * [Interrogation des fichiers d’événements étendus avec PowerShell](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/)

   * Nous avons créé un [exemple d’application](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration) qui s’exécute dans Azure et utilise les API publiques OMS pour envoyer les journaux d’audit SQL à OMS à des fins d’utilisation via le tableau de bord OMS.

## <a name="viewing-table-audit-logs"></a>Consultation des journaux d’audit Table

Les journaux d’audit Table sont enregistrés sous la forme d’une collection de tables Stockage Azure avec un préfixe **SQLDBAuditLogs**.

Pour plus d’informations sur le format des journaux d’audit Table, consultez les [informations de référence sur les formats des journaux d’audit Table](http://go.microsoft.com/fwlink/?LinkId=506733).

Il existe plusieurs méthodes pour afficher des journaux d’audit Table :

* Via le [portail Azure](https://portal.azure.com) : ouvrez la base de données correspondante. En haut du panneau Audit et détection des menaces de la base de données, cliquez sur **Afficher les journaux d’audit**.

    ![Volet de navigation][10]

    Un panneau Enregistrements d’audit s’ouvre, dans lequel s’affichent les journaux.

    * Vous pouvez choisir d’afficher des dates spécifiques en cliquant sur **Filtrer** en haut du panneau Enregistrements d’audit
    * Vous pouvez télécharger et afficher les journaux d’audit au format Excel en cliquant sur **Ouvrir dans Excel** en haut du panneau Enregistrements d’audit

    ![Volet de navigation][12]

* Par ailleurs, un modèle de rapport préconfiguré est disponible sous forme de [feuille de calcul Excel téléchargeable](http://go.microsoft.com/fwlink/?LinkId=403540) pour vous aider à analyser rapidement les données des journaux. Pour utiliser le modèle sur vos journaux d’audit, il vous faut Excel 2013 ou une version ultérieure et [Power Query(http://www.microsoft.com/download/details.aspx?id=39379).

* Vous pouvez aussi importer vos journaux d’audit dans le modèle Excel directement depuis votre compte de stockage Azure à l’aide de Power Query. Vous pouvez explorer ensuite vos enregistrements d’audit et créer des tableaux de bord et des rapports outre les données du journal.

    ![Volet de navigation][9]

## <a name="storage-key-regeneration"></a>Régénération des clés de stockage
Dans un environnement de production, vous allez probablement actualiser périodiquement vos clés de stockage. Lors de l’actualisation de vos clés, vous devez réenregistrer la stratégie d’audit. Pour ce faire, procédez comme suit :

1. Dans le panneau des détails de stockage, faites passer la **clé d’accès de stockage** de *Principale**Secondaire*, puis cliquez sur **OK** dans le bas. Cliquez sur **ENREGISTRER** en haut du panneau de configuration de l’audit.

    ![Volet de navigation][6]
2. Revenez au volet de configuration du stockage, puis **régénérez** la *clé d’accès primaire*.

    ![Volet de navigation][8]
3. Revenez au panneau de configuration de l’audit, faites passer la **clé d’accès de stockage** de *Secondaire* à *Principale*, puis cliquez sur **OK** dans le bas. Cliquez sur **ENREGISTRER** en haut du panneau de configuration de l’audit.
4. Revenez au panneau de configuration du stockage, puis **régénérez** la *clé d’accès secondaire* (en préparation du cycle suivant d’actualisation des clés).


<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation (PowerShell / REST API)]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)  

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[3-tbl]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on_table.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_audited_events.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_storage_key_regeneration.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_activity_log.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_regenerate_key.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_report_template.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_blob_view_audit_logs.png
[11]: ./media/sql-database-auditing-get-started/11_auditing_get_started_blob_audit_records.png
[12]: ./media/sql-database-auditing-get-started/12_auditing_get_started_table_audit_records.png

## <a name="next-steps"></a>Étapes suivantes

* Pour configurer et gérer l’audit à l’aide de PowerShell, consultez [Configurer l’audit de base de données avec PowerShell](sql-database-auditing-powershell.md).
* Pour une présentation de l’audit, consultez [Audit de base de données](sql-database-auditing.md).



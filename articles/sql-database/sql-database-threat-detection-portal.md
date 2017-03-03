---
title: "Portail Azure : configuration de la détection de menaces - SQL Database | Microsoft Docs"
description: "Configuration et gestion de la détection de menaces avec le portail Azure"
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: v-romcal
ms.assetid: 
ms.service: sql-database
ms.custom: secure and protect
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 07/10/2016
ms.author: ronmat; ronitr
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: 0222847321b82c9572e361cc5e578b779d859792
ms.lasthandoff: 02/17/2017


---
# <a name="configure-and-manage-azure-sql-database-threat-detection-in-the-azure-portal"></a>Configuration et gestion de la détection de menaces pour SQL Database dans le portail Azure

Cette rubrique vous montre comment configurer la détection des menaces et explorer les activités de base de données anormales. Pour une présentation de la détection des menaces dans Azure SQL Database, consultez [Présentation de la détection de menaces](sql-database-threat-detection.md).

## <a name="set-up-threat-detection-for-your-database"></a>Configurer Threat Detection pour votre base de données
1. Accédez à l’adresse [https://portal.azure.com](https://portal.azure.com)et exécutez le portail Azure.
2. Accédez au volet de configuration de la base de données SQL que voulez surveiller. Dans le panneau Paramètres, sélectionnez **Audit et détection des menaces**.
   
    ![Volet de navigation][1]
3. Dans le panneau de configuration **Audit et détection des menaces**, **activez** l’audit pour afficher les paramètres Détection des menaces.
   
    ![Volet de navigation][2]
4. **Activez** la détection des menaces.
5. Configurez la liste des adresses électroniques qui recevront les alertes de sécurité en cas de détection d’activités anormales sur la base de données.
6. Cliquez sur **Enregistrer** dans le panneau **Audit et détection des menaces** pour enregistrer la stratégie d’audit et de détection des menaces que vous avez créée ou modifiée.
   
    ![Volet de navigation][3]

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Explorer les activités anormales sur la base de données en cas de détection d’un événement suspect
1. Vous recevrez une notification par courrier électronique lorsque des activités anormales sont détectées au niveau de la base de données. <br/>
   Le courrier électronique contiendra des informations sur l’événement de sécurité suspect, notamment la nature des activités anormales, le nom de la base de données, le nom du serveur et l’heure de l’événement. Il fournit également des informations sur les causes possibles et les mesures recommandées afin d’examiner et atténuer la menace potentielle pesant sur la base de données.<br/>
   
    ![Volet de navigation][4]
2. Dans le courrier électronique, cliquez sur le lien **Azure SQL Auditing Log** pour ouvrir le portail Azure et afficher les enregistrements d’audit pertinents au moment de l’événement suspect.
   
    ![Volet de navigation][5]
3. Cliquez sur les enregistrements d’audit pour afficher plus de détails sur les activités de base de données suspects, comme l’instruction SQL, la cause de l’échec et l’adresse IP client.
   
    ![Volet de navigation][6]
4. Dans le panneau des enregistrements d’audit, cliquez sur **Ouvrir dans Excel** pour ouvrir un modèle Excel préconfiguré à importer et exécuter une analyse plus approfondie du journal d’audit au moment de l’événement suspect.<br/>
   **Remarque :** dans Excel 2010 ou version ultérieure, les paramètres Power Query et **Combinaison rapide** sont requis.
   
    ![Volet de navigation][7]
5. Pour configurer le paramètre **Combinaison rapide** : sous l’onglet du ruban **POWER QUERY**, sélectionnez **Options** pour afficher la boîte de dialogue correspondante. Sélectionnez la section Confidentialité et choisissez la deuxième option « gnore the Privacy Levels and potentially improve performance » :
   
    ![Volet de navigation][8]
6. Pour charger les journaux d’audit SQL, vérifiez que les paramètres de l’onglet Paramètres sont correctement définis, puis sélectionnez le ruban « Données » et cliquez sur le bouton « Actualiser tout ».
   
    ![Volet de navigation][9]
7. Les résultats s’affichent dans la feuille **SQL Audit Logs** , qui vous permet d’analyser de manière plus approfondie les activités anormales détectées et de limiter l’impact de l’événement de sécurité sur votre application.

## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation de la détection des menaces dans SQL Database, consultez [Détection de menaces](sql-database-threat-detection.md).
* Pour une présentation de l’audit de SQL Database, consultez [Audit de base de données](sql-database-auditing.md).


<!--Image references-->
[1]: ./media/sql-database-threat-detection-get-started/1_td_click_on_settings.png
[2]: ./media/sql-database-threat-detection-get-started/2_td_turn_on_auditing.png
[3]: ./media/sql-database-threat-detection-get-started/3_td_turn_on_threat_detection.png
[4]: ./media/sql-database-threat-detection-get-started/4_td_email.png
[5]: ./media/sql-database-threat-detection-get-started/5_td_audit_records.png
[6]: ./media/sql-database-threat-detection-get-started/6_td_audit_record_details.png
[7]: ./media/sql-database-threat-detection-get-started/7_td_audit_records_open_excel.png
[8]: ./media/sql-database-threat-detection-get-started/8_td_excel_fast_combine.png
[9]: ./media/sql-database-threat-detection-get-started/9_td_excel_parameters.png


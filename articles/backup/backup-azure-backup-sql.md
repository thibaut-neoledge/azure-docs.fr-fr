---
title: "Sauvegarde Azure pour les charges de travail SQL Server à l’aide de DPM | Microsoft Docs"
description: "Présentation de la sauvegarde de bases de données SQL Server à l&quot;aide du service Azure Backup"
services: backup
documentationcenter: 
author: adigan
manager: Nkolli
editor: 
ms.assetid: 59df5bec-d959-457d-8731-7b20f7f1013e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: adigan;giridham;jimpark;markgal;trinadhk
translationtype: Human Translation
ms.sourcegitcommit: 82b7541ab1434179353247ffc50546812346bda9
ms.openlocfilehash: c9edc066ea2edc9cd4b8453047d5584a588174dc
ms.lasthandoff: 03/02/2017


---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>Sauvegarde de SQL Server sur Azure en tant que charge de travail DPM
Cet article vous guide tout au long des étapes de configuration de la sauvegarde des bases de données SQL Server à l’aide de la Sauvegarde Azure.

Pour sauvegarder des bases de données SQL Server dans Azure, vous avez besoin d’un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).

La gestion de sauvegarde et de récupération de base de données SQL Server dans Azure implique trois étapes :

1. Créez une stratégie de sauvegarde pour protéger les bases de données SQL Server dans Azure.
2. Créez des copies de sauvegarde à la demande vers Azure.
3. Récupérer la base de données à partir d’Azure.

## <a name="before-you-start"></a>Avant de commencer
Avant de commencer, vérifiez que toutes les [conditions préalables](backup-azure-dpm-introduction.md#prerequisites) à l'utilisation de Microsoft Azure Backup pour protéger les charges de travail ont été remplies. Les conditions préalables couvrent des tâches telles que : création d’un coffre de sauvegarde, téléchargement des informations d’identification du coffre, installation de l’agent de sauvegarde Azure et inscription du serveur auprès du coffre.

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Créer une stratégie de sauvegarde pour protéger les bases de données SQL Server dans Azure
1. Sur le serveur DPM, cliquez sur l’espace de travail **Protection** .
2. Dans le ruban des outils, cliquez sur **Nouveau** pour créer un nouveau groupe de protection.

    ![Créer un groupe de protection](./media/backup-azure-backup-sql/protection-group.png)
3. DPM affiche l'écran de démarrage avec les conseils destinés à la création d'un **groupe de protection**. Cliquez sur **Next**.
4. Sélectionnez **Serveurs**.

    ![Sélectionner le type de groupe de protection - « Servers »](./media/backup-azure-backup-sql/pg-servers.png)
5. Développez l'ordinateur SQL Server sur lequel se trouvent les bases de données à sauvegarder. DPM affiche diverses sources de données pouvant être sauvegardés à partir de ce serveur. Développez **Tous les partages SQL** et sélectionnez les bases de données (dans ce cas, nous avons sélectionné ReportServer$ MSDPM2012 et ReportServer$ MSDPM2012TempDB) à sauvegarder. Cliquez sur **Next**.

    ![Sélectionner la base de données SQL](./media/backup-azure-backup-sql/pg-databases.png)
6. Donnez un nom au groupe de protection, puis cochez la case **Je souhaite une protection en ligne** .

    ![Méthode de Protection des données - disque à court terme et Azure en ligne](./media/backup-azure-backup-sql/pg-name.png)
7. Dans l'écran **Spécifier les objectifs à court terme** , incluez les entrées nécessaires à la création de points de sauvegarde sur disque.

    Nous voyons ici que la **Durée de rétention** est définie sur *5 jours*, **la fréquence de synchronisation** sur une fois toutes les *15 minutes* qui est la fréquence à laquelle la sauvegarde est effectuée. **sauvegarde expresse rapide** est définie sur *20h00*.

    ![Spécifier les objectifs à court terme](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > À 20 h 00 (selon la saisie de l’écran), un point de sauvegarde est créé chaque jour par le transfert de données modifiées depuis le point de sauvegarde de 20 h 00 du jour précédent. Ce processus est appelé **Sauvegarde expresse rapide**. Lorsque les journaux de transaction sont synchronisés toutes les 15 minutes, s'il faut récupérer la base de données à 21h00, le point est créé suite à la relecture des journaux à partir du point de sauvegarde complète rapide (20h00 dans le cas présent).
   >
   >

8. Cliquez sur **Suivant**

    DPM affiche l’espace de stockage global disponible et l’utilisation potentielle de l’espace disque.

    ![Allocation de disque](./media/backup-azure-backup-sql/pg-storage.png)

    Par défaut, DPM crée un volume par source de données (base de données SQL Server) utilisée pour la copie de sauvegarde initiale. Suivant cette approche, le Gestionnaire de disque logique (LDM) limite la protection DPM à 300 sources de données (bases de données SQL Server). Pour contourner cette limitation, sélectionnez l’option **Colocaliser les données dans le pool de stockage DPM**. Si vous choisissez cette option, DPM utilise un même volume pour plusieurs sources de données, ce qui permet à DPM de protéger jusqu’à 2 000 bases de données SQL.

    Si l’option **Augmenter automatiquement les volumes** est sélectionnée, DPM peut gérer l’augmentation du volume de sauvegarde à mesure que les données de production augmentent. Si l’option **Augmenter automatiquement les volumes** n’est pas sélectionnée, DPM limite le stockage de sauvegarde utilisé pour les sources de données dans le groupe de protection.
9. Les administrateurs peuvent opter pour le transfert manuel de cette sauvegarde initiale manuellement (hors réseau) pour éviter l’encombrement de la bande passante ou sur le réseau. Ils peuvent également configurer l’heure à laquelle le transfert initial peut se produire. Cliquez sur **Next**.

    ![Méthode de réplication initiale](./media/backup-azure-backup-sql/pg-manual.png)

    La copie de sauvegarde initiale nécessite le transfert de la source de données complète (base de données SQL Server) à partir du serveur de production (ordinateur SQL Server) vers le serveur DPM. Ces données peuvent être volumineuses et leur transfert sur le réseau peut dépasser la bande passante. Pour cette raison, les administrateurs peuvent choisir de transférer la sauvegarde initiale : **Manuellement** (à l’aide d’un support amovible) afin d’éviter la congestion de la bande passante, ou **Automatiquement sur le réseau** (à un moment précis).

    Une fois la sauvegarde initiale terminée, le reste des sauvegardes se compose de sauvegardes incrémentielles sur la copie de sauvegarde initiale. Les sauvegardes incrémentielles sont en général très limitées et sont faciles à transférer sur le réseau.
10. Choisissez si vous souhaitez ou non exécuter la vérification de cohérence, puis cliquez sur **Suivant**.

    ![Vérifier la cohérence](./media/backup-azure-backup-sql/pg-consistent.png)

    DPM peut effectuer un contrôle de cohérence pour vérifier l’intégrité du point de sauvegarde. Il calcule la somme de contrôle du fichier de sauvegarde sur le serveur de production (ordinateur SQL Server dans ce scénario) et des données sauvegardée pour ce fichier sur DPM. En cas de conflit, on considère que le fichier sauvegardé sur DPM est endommagé. DPM corrige les données sauvegardées en envoyant les blocs correspondant à l’incohérence du contrôle de cohérence. Le contrôle de cohérence étant une opération exigeante en matière de performances, les administrateurs ont la possibilité de le planifier ou de l’exécuter automatiquement.
11. Pour spécifier la protection des sources de données en ligne, sélectionnez les bases de données à protéger sur Azure et cliquez sur **Suivant**.

    ![Sélectionner les sources de données](./media/backup-azure-backup-sql/pg-sqldatabases.png)
12. Les administrateurs peuvent choisir les planifications de sauvegarde et les stratégies de rétention adaptées à leurs stratégies d’entreprise.

    ![Planification de sauvegarde et rétention](./media/backup-azure-backup-sql/pg-schedule.png)

    Dans cet exemple, les sauvegardes sont effectuées une fois par jour à 12 h 00 et 20 h 00 (partie inférieure de l’écran)

    > [!NOTE]
    > Il est conseillé de disposer de plusieurs points de récupération à court terme sur disque pour une récupération rapide. Ces points de récupération sont utilisés pour la « restauration opérationnelle ». Azure constitue un bon emplacement hors site, avec des contrats de niveau de service supérieurs et une disponibilité garantie.
    >
    >

    **Meilleure pratique**: assurez-vous que les sauvegardes Azure Backup sont prévues après l'exécution de sauvegardes sur disque local à l'aide de DPM. Cela permet la copie de la dernière sauvegarde de disque sur Azure.

13. Cliquez sur la planification de stratégie de rétention. Les détails du fonctionnement de la stratégie de rétention sont fournis dans la section [Utilisation d'Azure Backup pour remplacer votre infrastructure sur bande](backup-azure-backup-cloud-as-tape.md).

    ![Stratégie de rétention](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Dans cet exemple :

    * Les sauvegardes sont effectuées une fois par jour à 12 h 00 et 20 h 00 (partie inférieure de l’écran) et sont conservées pendant 180 jours.
    * La sauvegarde le samedi à 12 h 00 est conservée pendant 104 semaines
    * La sauvegarde le dernier samedi à 12 h 00 est conservée pendant 60 mois
    * La sauvegarde le dernier samedi de mars à 12 h 00 est conservée pendant 10 ans
14. Cliquez sur **Suivant** et sélectionnez l'option appropriée pour le transfert de la copie de sauvegarde initiale vers Azure. Vous pouvez choisir **Automatiquement sur le réseau** ou **Sauvegarde hors connexion**.

    * **Automatiquement sur le réseau** transfère les données de sauvegarde vers Azure en respectant la planification de sauvegarde sélectionnée.
    * Le fonctionnement de la **Sauvegarde en mode hors connexion** est décrit à dans la section [Flux de travail de sauvegarde en mode hors connexion dans Azure Backup](backup-azure-backup-import-export.md).

    Choisissez le mécanisme de transfert adapté à l'envoi de la copie de sauvegarde initiale vers Azure, puis cliquez sur **Suivant**.
15. Après avoir passé en revue les détails de la stratégie dans l’écran **Résumé**, cliquez sur le bouton **Créer un groupe** pour terminer le flux de travail. Vous pouvez cliquer sur le bouton **Fermer** et surveiller la progression du travail d'analyse de l'espace de travail.

    ![Créer un groupe de Protection en cours en progression](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Sauvegarde à la demande d’une base de données SQL Server
Alors que les étapes précédentes ont créé une stratégie de sauvegarde, un « point de récupération » est créé uniquement à l’occasion de la première sauvegarde. Au lieu d’attendre que le planificateur arrive, les étapes ci-dessous déclenchent la création manuelle d’un point de récupération.

1. Attendez que l'état du groupe de protection indique **OK** pour la base de données avant de créer le point de récupération.

    ![Membres du groupe de protection](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Cliquez avec le bouton droit sur la base de données, puis sélectionnez **Créer un point de récupération**.

    ![Créer un point de récupération en ligne](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Choisissez **Protection en ligne** dans le menu déroulant, puis cliquez sur **OK**. Ceci démarre la création d’un point de récupération dans Azure.

    ![Créer un point de récupération](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Vous pouvez afficher la progression du travail dans l’espace de travail **Surveillance** , où vous trouverez un travail en cours similaire à celui qui est représenté dans la figure suivante.

    ![Console de surveillance](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Récupération d'une base de données SQL Server à partir d'Azure
Les étapes suivantes sont nécessaires pour récupérer une entité protégée (base de données SQL Server) à partir d'Azure.

1. Ouvrir la Console de gestion du serveur DPM. Accédez à l’espace de travail **Récupération** où vous pourrez voir les serveurs sauvegardés par DPM. Accédez à la base de données requise (dans ce cas, ReportServer $MSDPM2012). Sélectionnez une durée **Restauration depuis** qui se termine par **En ligne**.

    ![Sélectionner un point de récupération](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Cliquez avec le bouton droit sur le nom de base de données et cliquez sur **Récupérer**.

    ![Récupérer depuis Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. DPM affiche les détails du point de récupération. Cliquez sur **Next**. Pour remplacer la base de données, sélectionnez le type de récupération **Récupérer l’instance d’origine de SQL Server**. Cliquez sur **Next**.

    ![Récupérer à l’emplacement d’origine](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    Dans cet exemple, DPM permet la récupération de la base de données vers une autre instance SQL Server ou dans un dossier de réseau autonome.
4. Dans l'écran **Spécifier des options de récupération** , vous pouvez sélectionner les options de récupération telles que la limitation de bande passante réseau pour limiter la bande passante utilisée par la récupération. Cliquez sur **Next**.
5. Dans l’écran **Résumé** , vous voyez toutes les configurations de récupération fournies jusqu’à présent. Cliquez sur **Restaurer**.

    L’état de récupération indique la base de données en cours de récupération. Vous pouvez cliquer sur **Fermer** pour fermer l’Assistant et afficher la progression dans l’espace de travail **Surveillance**.

    ![Initier le processus de récupération](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Une fois la restauration terminée, la base de données restaurée est cohérente avec l’application.

### <a name="next-steps"></a>Étapes suivantes :
•    [Sauvegarde Azure - FAQ](backup-azure-backup-faq.md)


<properties
	pageTitle="Protection du serveur DPM/Azure Backup Server d’une batterie de serveurs SharePoint sur Azure | Microsoft Azure"
	description="Cet article fournit une vue d’ensemble de la protection du serveur DPM/Azure Backup Server d’une batterie de serveurs SharePoint sur Azure"
	services="backup"
	documentationCenter=""
	authors="nkolli1"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/06/2015"
	ms.author="giridham;jimpark"/>


# Sauvegarder une batterie de serveurs SharePoint dans Azure
Vous sauvegardez une batterie de serveurs SharePoint sur Microsoft Azure à l’aide de System Center Data Protection Manager (DPM) de la même façon que vous sauvegardez d’autres sources de données. Azure Backup offre une flexibilité dans la planification d’une sauvegarde pour créer des points de sauvegarde quotidiens, hebdomadaires, mensuels ou annuels, et vous offre des options de stratégie de conservation pour les différents points de sauvegarde. DPM vous permet de stocker des copies sur disque local pour bénéficier d’objectifs de temps de récupération (RTO) rapides, ainsi que de stocker des copies sur Azure pour une conservation à long terme économique.

## Versions SharePoint prises en charge et scénarios de protection associés
Azure Backup pour DPM prend en charge les scénarios suivants.

| Charge de travail | Version | Déploiement de SharePoint | Type de déploiement de DPM | DPM - System Center 2012 R2 | Protection et récupération |
| -------- | ------- | --------------------- | ------------------- | --------------------------- | ----------------------- |
| SharePoint | SharePoint 2013, SharePoint 2010, SharePoint 2007, SharePoint 3.0 | SharePoint déployé comme un serveur physique ou une machine virtuelle Hyper-V/VMware <br> -------------- <br> SQL AlwaysOn | Serveur physique ou machine virtuelle Hyper-V locale | Prend en charge la sauvegarde vers Azure à partir du correctif cumulatif 5 | Options de protection de la récupération de la batterie de serveurs SharePoint : batterie de serveurs de récupération, base de données, fichier ou élément de liste à partir de points de récupération de disque. Récupération d’une batterie de serveurs et d’une base de données à partir de points de récupération Azure. |

## Avant de commencer
Quelques points doivent être confirmés avant de sauvegarder une batterie de serveurs SharePoint sur Azure.

### Configuration requise
Avant de continuer, vérifiez que toutes les [conditions préalables](backup-azure-dpm-introduction.md#prerequisites) à l’utilisation de Microsoft Azure Backup pour protéger les charges de travail ont été remplies. Certaines tâches liées aux conditions préalables incluent : créer un coffre de sauvegarde, télécharger les informations d’identification du coffre, installer l’Agent Azure Backup et inscrire le serveur DPM/Azure Backup Server auprès du coffre.

### Agent DPM
L’agent DPM doit être installé sur le serveur qui exécute SharePoint, les serveurs qui exécutent SQL et tous les serveurs qui font partie de la batterie de serveurs SharePoint. Pour plus d’informations sur la configuration de l’agent de protection, consultez la rubrique [Configuration de l’agent de protection](https://technet.microsoft.com/library/hh758034(v=sc.12).aspx). La seule exception concerne l’installation de l’agent uniquement sur un seul serveur Web frontal (WFE). DPM n’a besoin de l’agent que sur un serveur WFE pour servir de point d’entrée pour la protection.

### Batterie de serveurs SharePoint
Pour chaque 10 millions d'éléments dans la batterie de serveurs, il doit y avoir au moins 2 Go d'espace sur le volume où figure le dossier DPM. Cet espace est nécessaire pour la génération du catalogue. Pour permettre à DPM de restaurer des éléments spécifiques (collections de sites, sites, listes, bibliothèques de documents, dossiers, documents et éléments de liste), la génération du catalogue crée une liste de toutes les URL contenues dans chaque base de données de contenu. Vous pouvez afficher la liste des URL dans le volet des éléments récupérables de la zone de tâches **Récupération** de la console administrateur DPM.

### SQL Server
DPM s’exécute comme un compte système local. Pour sauvegarder les bases de données SQL Server, DPM nécessite les droits d’administrateur système sur ce compte pour le serveur qui exécute SQL Server. Définissez NT AUTHORITY\\SYSTEM sur *sysadmin* sur le serveur qui exécute SQL Server avant de le sauvegarder.

Si la batterie de serveurs SharePoint contient des bases de données SQL Server configurées avec des alias SQL Server, installez les composants clients SQL Server sur le serveur Web frontal que DPM protégera.

### SharePoint Server
Bien que les performances dépendent de nombreux facteurs tels que la taille de la batterie de serveurs SharePoint, tenez compte du fait qu’un seul serveur DPM peut protéger une batterie de serveurs SharePoint de 25 To.

### Correctif cumulatif DPM 5
Pour commencer la protection d’une batterie de serveurs SharePoint sur Azure, vous devez installer le correctif cumulatif DPM 5 ou version ultérieure. Le correctif cumulatif 5 permet de protéger une batterie de serveurs SharePoint sur Azure si la batterie est configurée à l’aide de SQL AlwaysOn. Pour plus d’informations, consultez le billet de blog qui présente le [correctif cumulatif DPM 5](http://blogs.technet.com/b/dpm/archive/2015/02/11/update-rollup-5-for-system-center-2012-r2-data-protection-manager-is-now-available.aspx)

### Ce qui n'est pas pris en charge
- DPM qui protège une batterie de serveurs SharePoint ne protège pas les index de recherche ni les bases de données de service d’application. Vous devrez configurer la protection de ces bases de données séparément.
- DPM ne fournit aucune sauvegarde des bases de données SQL Server SharePoint hébergées sur des partages de type SOFS (Scale out file server).

## Configuration de la protection SharePoint
Avant d’utiliser DPM pour protéger SharePoint, vous devez configurer le service SharePoint VSS Writer (service WSS Writer) à l’aide de **ConfigureSharePoint.exe**.

Le fichier **ConfigureSharePoint.exe** se trouve dans le dossier [Chemin d’installation de DPM]\\bin sur le serveur Web frontal. Cet outil fournit l'agent de protection avec les informations d'identification pour la batterie de serveurs SharePoint. Vous l'exécutez sur un seul serveur Web frontal (WFE). Si vous avez plusieurs serveurs WFE, n’en sélectionnez qu’un lorsque vous configurez un groupe de protection.

### Configuration du service SharePoint VSS Writer
1. Sur le serveur WFE, à l'invite de commandes, accédez à [Emplacement d'installation DPM]\\bin\\
2. Entrez ConfigureSharePoint -EnableSharePointProtection
3. Entrez les informations d'identification de l’administrateur de la batterie de serveurs. Ce compte doit être membre du groupe administrateur local sur le serveur Web frontal (WFE). Si l'administrateur de la batterie de serveurs n'est pas un administrateur local, accordez les autorisations suivantes sur le serveur Web frontal (WFE) :
  - Accordez le contrôle total du groupe WSS\_Admin\_WPG au dossier DPM (%Program Files%\\Microsoft Data Protection Manager\\DPM).
  - Accordez au groupe WSS\_Admin\_WPG un droit d'accès en lecture à la clé de registre DPM (HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Microsoft Data Protection Manager).

>[AZURE.NOTE] Vous devrez réexécuter le fichier ConfigureSharePoint.exe à chaque fois modification des informations d'identification de l'administrateur de la batterie de serveurs SharePoint.

## Sauvegarde d’une batterie de serveurs SharePoint à l’aide de DPM
Une fois que vous avez configuré DPM et la batterie de serveurs SharePoint comme expliqué ci-dessus, SharePoint peut être protégé par DPM.

### Protection d’une batterie de serveurs SharePoint
1. Dans l’onglet **Protection** de la console administrateur DPM, cliquez sur **Nouveau**. ![Onglet Nouvelle protection](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)

2. Sur la page **Sélectionner le type de groupe de protection** de l’assistant **Créer un groupe de protection**, sélectionnez **Serveurs**, puis cliquez sur **Suivant**.

    ![Sélectionner le type de groupe de protection](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)

3. Dans l’écran **Sélectionner les membres du groupe**, cochez le serveur SharePoint que vous souhaitez protéger, puis cliquez sur **Suivant**.

    ![Sélectionner les membres du groupe](./media/backup-azure-backup-sharepoint/select-group-members2.png)

    >[AZURE.NOTE] Une fois l'agent DPM installé, vous pouvez voir le serveur dans l'assistant. DPM affiche également sa structure. Comme le fichier ConfigureSharePoint.exe a été exécuté, DPM communique avec le service SharePoint VSS Writer et ses bases de données SQL Server correspondantes et reconnaît la structure de la batterie de serveurs SharePoint, les bases de données de contenu associées et les éléments correspondants.

4. Sur la page **Sélectionner la méthode de protection des données**, entrez le nom du **groupe de Protection** et sélectionnez vos *méthodes de protection* préférées. Cliquez sur **Next**.

    ![Sélectionner la méthode de protection des données](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

    >[AZURE.NOTE] La méthode de protection du disque permet de répondre aux objectifs de temps de récupération courts. Azure est une cible de protection à long terme économique par rapport aux bandes. Pour plus d’informations, consultez [Utilisation d’Azure Backup pour remplacer votre infrastructure sur bande](https://azure.microsoft.com/documentation/articles/backup-azure-backup-cloud-as-tape/)

5. Sur la page **Spécifier les objectifs à court terme**, sélectionnez votre **plage de rétention** préférée et indiquez quand vous souhaitez effectuer les sauvegardes.

    ![Spécifier les objectifs à court terme](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

    >[AZURE.NOTE] La récupération étant le plus souvent nécessaire pour les données de moins de cinq jours, nous avons sélectionné une plage de rétention de cinq jours sur le disque et vérifié que la sauvegarde survient pendant les heures de production, dans cet exemple.

6. Vérifiez l’espace disque de pool de stockage alloué au groupe de protection, puis cliquez sur **Suivant**.

7. Pour chaque groupe de protection, DPM alloue de l'espace disque pour stocker et gérer des réplicas. À ce stade, DPM doit créer une copie des données sélectionnées. Sélectionnez quand et comment vous souhaitez que le réplica soit créé, puis cliquez sur **Suivant**.

    ![Choisir la méthode de création d’un réplica](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

    >[AZURE.NOTE] Pour s’assurer que le trafic réseau ne soit pas affecté, sélectionnez une heure en dehors des heures de production.

8. DPM garantit l'intégrité des données en effectuant des vérifications de cohérence sur le réplica. Deux options sont disponibles. Vous pouvez planifier des vérifications de cohérence ou autoriser DPM à vérifier automatiquement la cohérence du réplica chaque fois qu’il devient incohérent. Sélectionnez votre option préférée, puis cliquez sur **Suivant**.

    ![Vérification de la cohérence](./media/backup-azure-backup-sharepoint/consistency-check.png)

9. Sur la page **Spécifier les données de protection en ligne**, sélectionnez la batterie de serveurs SharePoint que vous souhaitez protéger, puis cliquez sur **Suivant**.

    ![DPM SharePoint Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)

10. Sur la page **Spécifier la planification de sauvegarde en ligne**, sélectionnez une planification et cliquez sur **Suivant**.

    ![Online\_backup\_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    >[AZURE.NOTE] DPM fournit un maximum de deux sauvegardes quotidiennes sur Azure à des heures différentes. Azure Backup peut également contrôler la quantité de bande passante WAN qui peut être utilisée pour les sauvegardes aux heures de pointe et aux heures creuses, à l’aide de la [limitation réseau Azure Backup](https://azure.microsoft.com/en-in/documentation/articles/backup-configure-vault/#enable-network-throttling).

11. Selon la planification de sauvegarde que vous avez sélectionnée, sur la page **Spécifier la stratégie de rétention en ligne**, sélectionnez la stratégie de rétention pour les points de sauvegarde quotidiens, hebdomadaires, mensuels et annuels.

    ![Online\_retention\_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    >[AZURE.NOTE] DPM utilise un schéma de rétention grand-père-père-fils dans lequel une autre stratégie de rétention peut être choisie pour différents points de sauvegarde.

12. Comme pour le disque, un réplica de point de référence initial doit être créé dans Azure. Sélectionnez votre option préférée pour la création de la copie de sauvegarde initiale sur Azure, puis cliquez sur **Suivant**.

    ![Online\_replica](./media/backup-azure-backup-sharepoint/online-replication.png)

13. Vérifiez vos paramètres sélectionnés sur la page **Résumé**, puis cliquez sur **Créer un groupe**. Un message confirme la création du groupe de protection.

    ![Résumé](./media/backup-azure-backup-sharepoint/summary.png)

## Restauration d’un élément SharePoint à partir du disque à l'aide de DPM
Dans l'exemple ci-dessous, l’*élément de récupération SharePoint* a été supprimé accidentellement et doit être récupéré. ![DPM SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Ouvrez la **console administrateur DPM**. Toutes les batteries de serveurs SharePoint protégées par DPM apparaissent dans l’onglet **Protection**.

    ![DPM SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)

2. Pour commencer la récupération de l’élément, sélectionnez l’onglet **Récupération**.

    ![DPM SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)

3. Vous pouvez rechercher l’*élément de récupération SharePoint* dans SharePoint à l’aide d’une recherche avec des caractères génériques dans une plage de points de récupération.

    ![DPM SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)

4. Sélectionnez le point de récupération approprié dans les résultats de la recherche, cliquez avec le bouton droit sur l’élément et sélectionnez **Restaurer**.

5. Vous pouvez également parcourir les différents points de récupération et sélectionner une base de données ou un élément à restaurer. Sélectionnez **Date > Temps de récupération** puis sélectionnez **Base de données > Batterie de serveurs SharePoint > Point de récupération > Élément**.

    ![DPM SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)

6. Cliquez avec le bouton droit sur l’élément, puis sélectionnez **Restaurer** pour ouvrir l’**assistant de récupération**. Cliquez sur **Next**.

    ![Vérifier la sélection à restaurer](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)

7. Sélectionnez le type de récupération que vous souhaitez effectuer, puis cliquez sur **Suivant**.

    ![Type de récupération](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

    >[AZURE.NOTE] La sélection de l’option **Récupérer sur le site d’origine** dans l’exemple récupère l’élément sur le site SharePoint d’origine.

8. Sélectionnez le **processus de récupération** que vous souhaitez utiliser.
    - Sélectionnez l’option de **récupération sans batterie de serveurs** si la batterie de serveurs SharePoint n’a pas changé et qu’elle est la même que le point de restauration en cours de restauration.
    - Sélectionnez l’option de **récupération avec une batterie de serveurs** si la batterie de serveurs SharePoint a changé depuis la création du point de restauration.

    ![Processus de récupération](./media/backup-azure-backup-sharepoint/recovery-process.png)

9. Spécifiez l’emplacement intermédiaire de l'instance SQL Server pour la restauration temporaire de la base de données, ainsi qu’un partage de fichiers intermédiaire sur le serveur DPM et le serveur qui exécute SharePoint afin de restaurer l’élément.

    ![Emplacement intermédiaire1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    DPM connecte la base de données de contenu, qui héberge l’élément SharePoint, à l’instance SQL Server temporaire. À partir de la base de données de contenu, le serveur DPM restaure l’élément et le place à l’emplacement du fichier intermédiaire sur le serveur DPM. L’élément récupéré sur l’emplacement intermédiaire, sur le serveur DPM, doit maintenant être exporté vers l’emplacement intermédiaire sur la batterie de serveurs SharePoint.

    ![Emplacement intermédiaire2](./media/backup-azure-backup-sharepoint/staging-location2.png)

10. Sélectionnez **Spécifier les options de récupération** et appliquez des paramètres de sécurité à la batterie de serveurs SharePoint, ou appliquez les paramètres de sécurité du point de récupération. Cliquez sur **Next**.

    ![Options de récupération](./media/backup-azure-backup-sharepoint/recovery-options.png)

    >[AZURE.NOTE] Vous pouvez choisir de limiter l'utilisation de la bande passante réseau. Cela réduit l'impact sur le serveur de production pendant les heures de production.

11. Passez en revue les informations de résumé et cliquez sur **Récupérer** pour commencer la récupération du fichier.

    ![Résumé de la récupération](./media/backup-azure-backup-sharepoint/recovery-summary.png)

12. Sélectionnez maintenant l’onglet **Surveillance** dans la **console administrateur DPM** pour afficher l’**état** de la récupération.

    ![État de la récupération](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    >[AZURE.NOTE] Le fichier est maintenant restauré. Vous pouvez actualiser le site SharePoint pour vérifier le fichier restauré.

## Restauration d’une base de données SharePoint à partir d’Azure à l'aide de DPM

1. Pour récupérer une base de données de contenu SharePoint, parcourez les différents points de récupération (comme indiqué ci-dessus), puis sélectionnez le point de récupération à restaurer.

    ![DPM SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)

2. Double-cliquez sur le point de récupération SharePoint pour afficher les informations du catalogue SharePoint disponibles.

    > [AZURE.NOTE] Comme la batterie de serveurs SharePoint est protégée pour une rétention à long terme dans Azure, aucune information du catalogue (métadonnées) n’est disponible sur le serveur DPM. Par conséquent, chaque fois qu’une base de données contenu SharePoint ponctuelle doit être récupérée, vous devez de nouveau cataloguer la batterie de serveurs SharePoint.

3. Cliquez sur **Recataloguer**.

    ![DPM SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    La fenêtre d’état **Recataloguer le cloud** s’ouvre.

    ![DPM SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Une fois le catalogage terminé, l’état passe à *Succès*. Cliquez sur **Fermer**.

    ![DPM SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)

4. Cliquez sur l’objet SharePoint affiché dans l’onglet **Récupération** DPM pour obtenir la structure de base de données de contenu. Cliquez avec le bouton droit sur l’élément, puis cliquez sur **Récupérer**.

    ![DPM SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)

5. À ce stade, suivez les [étapes de récupération mentionnées précédemment dans cet article](#restore-a-sharepoint-item-from-disk-using-dpm) pour la récupération d’une base de données de contenu SharePoint à partir du disque.

## FAQ
Q : Quelles versions de DPM prennent en charge SQL Server 2014 et SQL 2012 (SP2) ?<br> R : DPM 2012 R2 avec le correctif cumulatif 4 prend en charge les deux.

Q : Puis-je restaurer un élément SharePoint sur l’emplacement d’origine si SharePoint est configuré avec SQL AlwaysOn (avec protection sur disque) ?<br> R : Oui, l’élément peut être récupéré sur le site SharePoint d’origine.

Q : Puis-je restaurer une base de données SharePoint sur l’emplacement d’origine si SharePoint est configuré avec SQL AlwaysOn ?<br> R : Comme les bases de données SharePoint sont configurées dans SQL AlwaysOn, elles ne peuvent pas être modifiées, à moins que le groupe de disponibilité n’ait été supprimé. Par conséquent, DPM ne peut pas restaurer la base de données sur l’emplacement d’origine. Vous pouvez récupérer une base de données SQL Server sur une autre instance SQL Server.

## Étapes suivantes
- Pour en savoir plus sur la protection DPM de SharePoint, consultez la [série de vidéos - Protection DPM de SharePoint](http://channel9.msdn.com/Series/Azure-Backup/Microsoft-SCDPM-Protection-of-SharePoint-1-of-2-How-to-create-a-SharePoint-Protection-Group)
- Consultez les [notes de publication pour System Center 2012 - Data Protection Manager](https://technet.microsoft.com/library/jj860415.aspx)
- Consultez les [notes de publication pour Data Protection Manager dans System Center 2012 SP1](https://technet.microsoft.com/library/jj860394.aspx)

<!---HONumber=AcomDC_0608_2016-->
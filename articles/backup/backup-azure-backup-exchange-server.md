<properties
	pageTitle="Sauvegarder un serveur Exchange dans Azure Backup avec System Center 2012 R2 DPM | Microsoft Azure"
	description="Apprenez à sauvegarder un serveur Exchange dans Azure Backup à l’aide de System Center 2012 R2 DPM"
	services="backup"
	documentationCenter=""
	authors="MaanasSaran"
	manager="NKolli1"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/19/2016"
	ms.author="anuragm;jimpark;delhan;trinadhk;markgal"/>


# Sauvegarder un serveur Exchange dans Azure Backup avec System Center 2012 R2 DPM
Cet article décrit comment configurer un serveur System Center 2012 R2 Data Protection Manager (DPM) pour sauvegarder un serveur Microsoft Exchange dans Azure Backup.

## Mises à jour
Pour enregistrer correctement le serveur DPM sur Azure Backup, vous devez installer le dernier correctif cumulatif pour System Center 2012 R2 DPM ainsi que la dernière version de l’agent Azure Backup. Pour obtenir le dernier correctif cumulatif, consultez le [catalogue Microsoft](http://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager).

>[AZURE.NOTE] Pour les exemples de cet article, nous avons utilisé la version 2.0.8719.0 de l’agent Azure Backup et installé le correctif cumulatif 6 sur System Center 2012 R2 DPM.

## Composants requis
Avant de continuer, vérifiez que toutes les [conditions préalables](backup-azure-dpm-introduction.md#prerequisites) à l’utilisation de Microsoft Azure Backup pour protéger les charges de travail ont bien été remplies. Vous devez au préalable :

- Créer un coffre de sauvegarde sur le site Azure.
- Télécharger les informations d’identification de l’agent et du coffre sur le serveur DPM.
- Installer l’agent sur le serveur DPM.
- Utiliser les informations d’identification pour enregistrer le serveur DPM.
- Si vous protégez Exchange 2016, mettez à niveau vers DPM 2012 R2 UR9 ou une version ultérieure

## Agent de protection DPM  
Pour installer l’agent de protection DPM sur le serveur Exchange, procédez comme suit :

1. Assurez-vous que les pare-feux sont correctement configurés. Consultez la page [Configuration d’exceptions de pare-feu pour l’agent](https://technet.microsoft.com/library/Hh758204.aspx).

2. Installez l’agent sur le serveur Exchange en cliquant sur **Gestion > Agents > Installer** dans la Console Administrateur DPM. Pour obtenir des instructions détaillées, consultez la page [Installation de l’agent de protection DPM](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396).

## Créer un groupe de protection pour le serveur Exchange

1. Dans la Console Administrateur DPM, cliquez sur **Protection**, puis cliquez sur **Nouveau** dans la barre d’outils pour ouvrir l’assistant **Créer un nouveau groupe de Protection**.

2. Dans l’écran d’**accueil** de l’assistant, cliquez sur **Suivant**.

3. Dans l’écran **Sélectionner le type de groupe de protection**, sélectionnez **Serveurs**, puis cliquez sur **Suivant**.

4. Sélectionnez la base de données du serveur Exchange que vous souhaitez protéger, puis cliquez sur **Suivant**.

    >[AZURE.NOTE] Si vous protégez Exchange 2013, vérifiez les [Conditions préalables pour Exchange 2013](https://technet.microsoft.com/library/dn751029.aspx).

    Dans l’exemple suivant, la base de données Exchange 2010 est sélectionnée.

    ![Sélectionner les membres du groupe](./media/backup-azure-backup-exchange-server/select-group-members.png)

5. Sélectionnez la méthode de protection des données.

    Attribuez un nom au groupe de protection, puis sélectionnez les deux options suivantes :

    - Je souhaite une protection à court terme à l’aide de Disque.
    - Je voudrais une protection en ligne.

6. Cliquez sur **Next**.

7. Sélectionnez l’option **Exécuter Eseutil pour vérifier l’intégrité des données** si vous souhaitez vérifier l’intégrité des bases de données Exchange Server.

    Après avoir sélectionné cette option, une vérification de la cohérence de sauvegarde s’exécutera sur le serveur DPM afin d’éviter le trafic d’E/S généré lors de l’exécution de la commande **eseutil** sur le serveur Exchange.

    >[AZURE.NOTE] Pour utiliser cette option, vous devez copier les fichiers Ese.dll et Eseutil.exe dans le répertoire C:\\Program Files\\Microsoft System Center 2012 R2\\DPM\\DPM\\bin sur le serveur DPM. Dans le cas contraire, l’erreur suivante est déclenchée : ![erreur eseutil](./media/backup-azure-backup-exchange-server/eseutil-error.png)

8. Cliquez sur **Next**.

9. Sélectionnez la base de données pour **Sauvegarde de copie**, puis cliquez sur **Suivant**.

    >[AZURE.NOTE] Si vous ne sélectionnez pas « Sauvegarde complète » pour au moins une copie DAG d’une base de données, les journaux ne seront pas tronqués.

10. Configurez les objectifs de **Sauvegarde à court terme**, puis cliquez sur **Suivant**.

11. Vérifiez l’espace disque disponible, puis cliquez sur **Suivant**.

12. Sélectionnez l’heure à laquelle le serveur DPM devra créer la réplication initiale, puis cliquez **Suivant**.

13. Sélectionnez les options de vérification de cohérence, puis cliquez sur **Suivant**.

14. Choisissez la base de données que vous souhaitez sauvegarder sur Azure, puis cliquez sur **Suivant**. Par exemple :

    ![Spécifier les données de protection en ligne](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)

15. Définissez la planification pour **Azure Backup**, puis cliquez sur **Suivant**. Par exemple :

    ![Spécifier la planification de sauvegarde en ligne](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    >[AZURE.NOTE] Notez que les points de récupération en ligne sont basés sur des points de récupération complète express. Par conséquent, vous devez planifier le point de récupération en ligne après l’heure spécifiée pour le point de récupération complète express.

16. Configurer la stratégie de rétention pour **Azure Backup**, puis cliquez sur **Suivant**.

17. Choisissez une option de réplication en ligne, puis cliquez sur **Suivant**.

    Si vous disposez d’une base de données volumineuse, la création de la sauvegarde initiale sur le réseau peut prendre un long moment. Pour éviter ce problème, vous pouvez créer une sauvegarde hors connexion.

    ![Spécifier la stratégie de rétention en ligne](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)

18. Confirmez les paramètres, puis cliquez sur **Créer un groupe**.

19. Cliquez sur **Fermer**.

## Récupérer la base de données Exchange

1. Pour récupérer une base de données Exchange, cliquez sur **Récupération** dans la Console Administrateur DPM.

2. Localisez la base de données Exchange que vous souhaitez récupérer.

3. Sélectionnez un point de récupération en ligne dans la liste déroulante *Heure de récupération*.

4. Cliquez sur **Récupérer** pour démarrer l’**Assistant Récupération**.

Pour les points de récupération en ligne, il existe cinq types de récupération :

- **Récupérer à l’emplacement d’origine du serveur Exchange :** les données seront récupérées sur le serveur Exchange d’origine.
- **Récupérer vers une autre base de données sur un serveur Exchange :** les données seront récupérées dans une autre base de données sur un autre serveur Exchange.
- **Récupérer dans une base de données de récupération :** les données seront récupérées dans une base de données de récupération Exchange (RDB).
- **Copier dans un dossier réseau :** les données seront récupérées dans un dossier réseau.
- **Copier sur bande :** si une bibliothèque de bandes ou un lecteur de bandes autonome est attaché(e) et configuré(e) sur le serveur DPM, le point de récupération sera copié sur une bande disponible.

    ![Choisir la réplication en ligne](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## Étapes suivantes

- [Azure Backup - Forum Aux Questions](backup-azure-backup-faq.md)

<!---HONumber=AcomDC_0803_2016-->
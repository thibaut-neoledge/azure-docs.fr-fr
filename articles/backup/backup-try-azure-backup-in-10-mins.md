<properties
   pageTitle="Apprendre à sauvegarder des fichiers et dossiers d’un environnement Windows vers Azure | Microsoft Azure"
   description="Apprenez à sauvegarder vos données Windows Server en créant un coffre, en installant l’agent de sauvegarde, puis en sauvegardant vos fichiers et dossiers dans Azure."
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""
   keywords="comment sauvegarder; procédure de sauvegarde"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.date="04/14/2016"
   ms.author="jimpark;"/>

# Premier aperçu : sauvegarder des fichiers et des dossiers de Windows Server ou client vers Azure

Cet article explique comment sauvegarder vos fichiers et dossiers Windows Server (ou d’un client Windows) sur Azure à l’aide d’Azure Backup. Il s’agit d’un didacticiel destiné à vous présenter les notions de base. Vous verrez qu’il est extrêmement simple d’utiliser Azure Backup.

Si vous souhaitez en savoir plus sur Azure Backup, lisez cette [présentation](backup-introduction-to-azure-backup.md).

La sauvegarde des fichiers et dossiers sur Azure nécessite les activités suivantes :

![Étape 1 :](./media/backup-try-azure-backup-in-10-mins/step-1.png) Obtenir un abonnement Azure (si vous n’en avez pas encore).<br> ![Étape 2](./media/backup-try-azure-backup-in-10-mins/step-2.png) Créer un coffre de sauvegarde et télécharger les éléments nécessaires.<br> ![Étape 3](./media/backup-try-azure-backup-in-10-mins/step-3.png) Installer et inscrire l’agent Backup.<br> ![Étape 4](./media/backup-try-azure-backup-in-10-mins/step-4.png) Sauvegarder vos fichiers et dossiers.


![Comment sauvegarder votre ordinateur Windows avec Azure Backup](./media/backup-try-azure-backup-in-10-mins/windows-machine-backup-process.png)

## Étape 1 : obtenir un abonnement Azure

Si vous ne disposez pas d’un abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) qui vous permet d’accéder à n’importe quel service Azure.

## Étape 2 : créer un coffre de sauvegarde et télécharger les éléments nécessaires

Pour sauvegarder vos fichiers et dossiers, vous devez créer un coffre de sauvegarde dans la région où vous souhaitez stocker les données. Vous déterminez également le mode de réplication du stockage et téléchargez les informations d’identification et l’agent Backup.

### Pour créer un archivage de sauvegarde

1. Si ce n’est pas déjà fait, connectez-vous au [portail Azure](https://portal.azure.com/) à l’aide de votre abonnement Azure.

2. Cliquez sur **Nouveau > Intégration hybride > Sauvegarde**.

    ![Préparez-vous à sauvegarder vos fichiers et dossiers](./media/backup-try-azure-backup-in-10-mins/second-blade-backup.png)

3. Pour **Nom**, entrez un nom convivial permettant d'identifier le coffre de sauvegarde.

4. Pour **Région**, sélectionnez la région la plus proche de votre emplacement pour accélérer les transferts de fichiers.

5. Cliquez sur **CRÉER UN ARCHIVAGE**.

    ![création d'un coffre](./media/backup-try-azure-backup-in-10-mins/demo-vault-name.png)

    Une fois le coffre de sauvegarde prêt, il est répertorié dans les ressources de Recovery Services en tant qu’**Actif**.

    ![L’état du coffre est actif](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

Après avoir créé le coffre de sauvegarde, vous sélectionnez le mode de réplication de votre stockage.

>[AZURE.NOTE] Vous devez choisir le mode de réplication du stockage juste après la création d’un coffre de sauvegarde et avant l’inscription de machines auprès de celui-ci. Une fois qu’un élément a été inscrit dans l’archivage, la réplication du stockage est verrouillée et ne peut pas être modifiée.

### Pour sélectionner le mode de réplication du stockage

1. Cliquez sur le coffre que vous avez créé.
2. Dans la page Démarrage rapide, cliquez sur **Configurer**.

    ![Configurer le coffre](./media/backup-try-azure-backup-in-10-mins/configure-vault.png)

3. Choisissez l’option de stockage appropriée.

    Si vous utilisez Azure comme sauvegarde principale, choisissez [Stockage géo-redondant](../storage/storage-redundancy.md#geo-redundant-storage). Si vous utilisez Azure comme sauvegarde tertiaire, choisissez [Stockage localement redondant](../storage/storage-redundancy.md#locally-redundant-storage).

    ![Choisir l’option de réplication du stockage](./media/backup-try-azure-backup-in-10-mins/geo-redundant.png)

4. Si vous avez sélectionné **Localement redondant**, vous devez cliquer sur **Enregistrer** car **Géo-redondant** est l’option par défaut.

Vous utilisez les informations d’identification de coffre pour authentifier votre ordinateur auprès de l’archivage de sauvegarde. Voici comment télécharger ces informations d’identification.

### Pour télécharger les informations d’identification du coffre
Le fichier d’informations d’identification du coffre est utilisé uniquement pendant le processus d’inscription et expire au bout de 48 heures.

1. Pour revenir à la page **Démarrage rapide** de votre coffre, cliquez sur ![Sélectionner votre nouveau coffre](./media/backup-try-azure-backup-in-10-mins/quick-start-icon.png)

2. Cliquez sur **Télécharger les informations d’identification de coffre > Enregistrer**.

Vous devez à présent télécharger l’agent Backup.

### Pour télécharger l’agent Backup

Cliquez ru **Agent pour Windows Server ou System Center Data Protection Manager ou Windows Client > Enregistrer**.

![Enregistrer l’agent Azure Backup](./media/backup-try-azure-backup-in-10-mins/agent.png)

Maintenant que votre coffre est créé et que vous avez téléchargé tous les éléments, vous devez installer et inscrire l’agent Backup.

## Étape 3 : installer et inscrire l’agent Backup

1. Double-cliquez sur le fichier **MARSagentinstaller.exe** dans l’emplacement d’enregistrement.
2. Exécutez l’Assistant Installation de l’Agent Microsoft Azure Recovery Services. Pour terminer l’Assistant, vous devez :
    - Choisir un emplacement pour le dossier d’installation et de cache.
    - Fournir les informations relatives au serveur proxy, si vous en utilisez un pour vous connecter à Internet.
    - Fournir votre nom d’utilisateur et votre mot de passe si vous utilisez un proxy authentifié.
    - Enregistrer la phrase secrète de chiffrement dans un emplacement sécurisé.

    >[AZURE.NOTE] En cas de perte ou d’oubli de la phrase secrète, Microsoft ne pourra pas vous aider à récupérer les données de sauvegarde. Enregistrez le fichier dans un emplacement sécurisé. Il est nécessaire pour restaurer une sauvegarde.

L’agent est désormais installé et votre ordinateur est inscrit dans le coffre. Vous êtes prêt à configurer et à planifier votre sauvegarde.

## Étape 4 : sauvegarder vos fichiers et dossiers
Si l’agent Backup n’est pas ouvert, vous pouvez le trouver en recherchant Microsoft Azure Backup sur votre ordinateur.

1. Dans l’**agent Backup**, cliquez sur **Planifier la sauvegarde**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-try-azure-backup-in-10-mins/snap-in-schedule-backup-closeup.png)

2. Exécutez l’Assistant Planification d’une sauvegarde. Pendant ce processus, vous allez :

    - Sélectionner les fichiers et les dossiers que vous souhaitez sauvegarder.
    - Spécifier votre planification de sauvegarde (quotidienne ou hebdomadaire).
    - Déterminer votre stratégie de rétention.
    - Choisir la façon dont vous souhaitez effectuer la sauvegarde initiale (sur le réseau ou hors connexion).

    En savoir plus sur l’[exécution de la sauvegarde initiale hors connexion](backup-azure-backup-import-export.md). <br><br>

3. Une fois l’Assistant terminé, revenez dans l’**agent Backup** et cliquez sur **Sauvegarder maintenant** pour effectuer la sauvegarde initiale sur le réseau.

    ![Sauvegarder Windows Server maintenant](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

4. Sur l’écran **Confirmation**, cliquez sur **Sauvegarde**. Si vous fermez l’Assistant avant la fin du processus de sauvegarde, celui-ci continuera de s’exécuter en arrière-plan.

    Lorsque la sauvegarde initiale est terminée, la vue **Travaux** de la console indique que le travail est terminé.

    ![Sauvegarde initiale terminée](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

Félicitations ! Vous avez sauvegardé vos fichiers et vos dossiers dans Azure Backup.

## Étapes suivantes
- En savoir plus sur la [sauvegarde de machines Windows](backup-configure-vault.md)
- Maintenant que vous avez sauvegardé vos fichiers et vos dossiers, vous pouvez [gérer vos coffres et vos serveurs](backup-azure-manage-windows-server.md).
- Si vous avez besoin de restaurer une sauvegarde, utilisez cet article pour [restaurer des fichiers sur un ordinateur Windows](backup-azure-restore-windows-server.md).

<!---HONumber=AcomDC_0420_2016-->
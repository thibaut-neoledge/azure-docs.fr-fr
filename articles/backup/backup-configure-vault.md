<properties
	pageTitle="Sauvegarder un client Windows ou un serveur Windows Server dans Azure avec Azure Backup et le modèle de déploiement Resource Manager | Microsoft Azure"
	description="Sauvegardez des serveurs ou clients Windows sur Azure en créant un coffre de sauvegarde, en téléchargeant des informations d’identification, en installant l’agent de sauvegarde et en effectuant une sauvegarde initiale de vos fichiers et dossiers."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""
	keywords="coffre de sauvegarde ; sauvegarder un serveur Windows ; sauvegarder windows ;"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/10/2016"
	ms.author="jimpark; trinadhk; markgal"/>

# Sauvegarder un client Windows ou un serveur Windows Server dans Azure avec Azure Backup et le modèle de déploiement Resource Manager

> [AZURE.SELECTOR]
- [Portail Azure](backup-configure-vault.md)
- [Portail classique](backup-configure-vault-classic.md)

Cet article explique comment sauvegarder vos fichiers et dossiers Windows Server (ou d’un client Windows) sur Azure avec Azure Backup et le modèle de déploiement Resource Manager.

![Étapes du processus de sauvegarde](./media/backup-configure-vault/initial-backup-process.png)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] modèle de déploiement classique.

## Avant de commencer
Pour sauvegarder un serveur ou un client sur Azure, vous devez disposer d’un compte Azure. Si vous n’en possédez pas, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/) en quelques minutes.

## Étape 1 : Création du coffre Recovery Services

Un coffre Recovery Services est une entité qui stocke l’ensemble des sauvegardes et des points de récupération créés au fil du temps. Le coffre Recovery Services contient également la stratégie de sauvegarde appliquée aux fichiers et aux dossiers protégés. Lorsque vous créez un coffre Recovery Services, vous devez également sélectionner l’option de redondance de stockage appropriée.

### Pour créer un archivage de Recovery Services

1. Si ce n’est pas déjà fait, connectez-vous au [portail Azure](https://portal.azure.com/) à l’aide de votre abonnement Azure.

2. Dans le menu Hub, cliquez sur **Parcourir** et, dans la liste des ressources, tapez **Recovery Services**. Au fur et à mesure des caractères saisis, la liste est filtrée. Cliquez sur **Coffres Recovery Services**.

    ![Créer un archivage de Recovery Services - Étape 1](./media/backup-configure-vault/browse-to-rs-vaults.png) <br/>

    La liste des coffres Recovery Services est affichée.

3. Dans le menu **Coffres Recovery Services**, cliquez sur **Ajouter**.

    ![Créer un archivage de Recovery Services - Étape 2](./media/backup-configure-vault/rs-vault-menu.png)

    Le panneau du coffre Recovery Services s’affiche et vous invite à renseigner les champs **Nom**, **Abonnement**, **Groupe de ressources** et **Emplacement**.

    ![Créer un archivage de Recovery Services - Étape 5](./media/backup-configure-vault/rs-vault-attributes.png)

4. Sous **Nom**, entrez un nom convivial permettant d’identifier le coffre. Le nom doit être unique pour l’abonnement Azure. Tapez un nom contenant entre 2 et 50 caractères. Il doit commencer par une lettre, et ne peut contenir que des lettres, des chiffres et des traits d’union.

5. Cliquez sur **Abonnement** pour afficher la liste des abonnements disponibles. Si vous n’êtes pas sûr de l’abonnement à utiliser, utilisez l’abonnement par défaut (ou suggéré). Vous ne disposez de plusieurs choix que si votre compte professionnel est associé à plusieurs abonnements Azure.

6. Cliquez sur **Groupe de ressources** pour afficher la liste des groupes de ressources disponibles ou sur **Nouveau** pour en créer un. Pour plus d’informations sur les groupes de ressources, consultez la section [Utilisation du portail Azure pour déployer et gérer vos ressources Azure](../azure-portal/resource-group-portal.md).

7. Cliquez sur **Emplacement** pour sélectionner la région géographique du coffre. Ce choix définit la région géographique où vos données de sauvegarde sont envoyées. En choisissant une région proche de votre emplacement, vous pouvez réduire la latence du réseau lors de la sauvegarde sur Azure.

8. Cliquez sur **Create**. La création de l’archivage de Recovery Services peut prendre un certain temps. Surveillez les notifications d'état dans l'angle supérieur droit du portail. Une fois votre coffre créé, il doit s’ouvrir dans le portail. Si votre coffre n’est pas répertorié après avoir été créé, cliquez sur **Actualiser**. Une fois la liste actualisée, cliquez sur le nom de l’archivage.

### Pour spécifier la redondance du stockage
Lorsque vous créez un archivage de Recovery Services pour la première fois, vous devez spécifier le mode de réplication du stockage.

1. Dans le panneau **Paramètres** qui s’ouvre automatiquement avec le tableau de bord de votre coffre, cliquez sur **Infrastructure de sauvegarde**.

2. Dans le panneau Infrastructure de sauvegarde, cliquez sur **Configuration de la sauvegarde** pour afficher le paramètre **Type de réplication de stockage**.

    ![Créer un archivage de Recovery Services - Étape 5](./media/backup-configure-vault/backup-infrastructure.png)

3. Choisissez l’option de réplication de stockage correspondant à votre archivage.

    ![Liste des archivages de Recovery Services](./media/backup-configure-vault/choose-storage-configuration.png)

    Par défaut, votre archivage utilise un stockage géo-redondant. Si vous utilisez Azure comme principal point de terminaison du stockage de sauvegarde, laissez cette option inchangée. Si vous utilisez Azure comme point de terminaison secondaire du stockage de sauvegarde, choisissez le stockage localement redondant. Vous pourrez ainsi réduire les coûts du stockage de données dans Azure. Pour en savoir plus sur les options de stockage [géo-redondant](../storage/storage-redundancy.md#geo-redundant-storage) et [localement redondant](../storage/storage-redundancy.md#locally-redundant-storage), consultez cette [présentation](../storage/storage-redundancy.md).

    Après avoir sélectionné l’option de stockage pour votre coffre, vous pouvez associer vos fichiers et dossiers à celui-ci.

Maintenant que vous avez créé un coffre, vous devez préparer votre infrastructure de sauvegarde des fichiers et des dossiers en téléchargeant et en installant l’agent Microsoft Azure Recovery Services, en téléchargeant les informations d’identification du coffre et en utilisant ces informations pour enregistrer l’agent auprès du coffre.

## Étape 2 : Téléchargement des fichiers

>[AZURE.NOTE] La sauvegarde via le portail Azure sera disponible prochainement. Pour l’instant, vous devez utiliser l’agent Microsoft Azure Recovery Services en local pour sauvegarder vos fichiers et dossiers.

1. Dans le tableau de bord du coffre Recovery Services, cliquez sur **Paramètres**.

    ![Ouvrir le panneau Backup Goal (Objectif de la sauvegarde)](./media/backup-configure-vault/settings-button.png)

2. Dans le panneau Paramètres, cliquez sur **Mise en route > Sauvegarde**.

    ![Ouvrir le panneau Backup Goal (Objectif de la sauvegarde)](./media/backup-configure-vault/getting-started-backup.png)

3. Dans le panneau Sauvegarde, cliquez sur **Objectif de la sauvegarde**.

    ![Ouvrir le panneau Backup Goal (Objectif de la sauvegarde)](./media/backup-configure-vault/backup-goal.png)

4. Sélectionnez **Local** dans le menu « Where is you workload running? » (Où votre charge de travail s’exécute-t-elle ?).

5. Sélectionnez **Fichiers et dossiers** dans le menu « What do you want to backup? » (Que voulez-vous sauvegarder ?), puis cliquez sur **OK**.

#### Télécharger l’agent Azure Recovery Services

1. Dans le panneau **Prepare infrastructure** (Préparer l’infrastructure), cliquez sur **Download Agent for Windows Server or Windows Client** (Télécharger l’agent pour Windows Server ou un client Windows).

    ![Préparer l’infrastructure](./media/backup-configure-vault/prepare-infrastructure-short.png)

2. Dans la fenêtre de téléchargement, cliquez sur **Enregistrer**. Par défaut, le fichier **MARSagentinstaller.exe** est enregistré dans le dossier Téléchargements.

#### Télécharger les informations d'identification de coffre

1. Dans le panneau Préparer l’infrastructure, cliquez sur **Télécharger > Enregistrer**.

    ![Préparer l’infrastructure](./media/backup-configure-vault/prepare-infrastructure-download.png)

## Étape 3 : Installation et inscription de l’agent

1. Recherchez et double-cliquez sur **MARSagentinstaller.exe** dans le dossier Téléchargements (ou tout autre emplacement d’enregistrement).

2. Exécutez l’Assistant Installation de l’Agent Microsoft Azure Recovery Services. Pour terminer l’Assistant, vous devez :

    - Choisir un emplacement pour le dossier d’installation et de cache.
    - Fournir les informations relatives au serveur proxy, si vous en utilisez un pour vous connecter à Internet.
    - Fournir votre nom d’utilisateur et votre mot de passe si vous utilisez un proxy authentifié.
    - Fournir les informations d’identification de l’archivage téléchargées.
    - Enregistrer la phrase secrète de chiffrement dans un emplacement sécurisé.

    >[AZURE.NOTE] En cas de perte ou d’oubli de la phrase secrète, Microsoft ne pourra pas vous aider à récupérer les données de sauvegarde. Enregistrez le fichier dans un emplacement sécurisé. Il est nécessaire pour restaurer une sauvegarde.

L’agent est désormais installé et votre ordinateur est inscrit dans le coffre. Vous êtes prêt à configurer et à planifier votre sauvegarde.

## Étape 4 : Exécuter la sauvegarde initiale

La sauvegarde initiale comprend deux tâches principales :

- Planifier la sauvegarde
- Sauvegarder les fichiers et dossiers pour la première fois

Pour effectuer la sauvegarde initiale, vous devez utiliser l’agent Microsoft Azure Backup.

### Pour planifier la sauvegarde

1. Ouvrez l’agent Microsoft Azure Backup. Vous pouvez le trouver en recherchant **Microsoft Azure Backup** sur votre ordinateur.

    ![Démarrer l’agent Azure Backup](./media/backup-configure-vault/snap-in-search.png)

2. Dans l’agent Backup, cliquez sur **Planifier la sauvegarde**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-configure-vault/schedule-first-backup.png)

3. Sur la page Mise en route de l’Assistant Planifier la sauvegarde, cliquez sur **Suivant**.

4. Sur la page Sélectionner les éléments à sauvegarder, cliquez sur **Ajouter des éléments**.

5. Sélectionnez les fichiers et les dossiers que vous souhaitez sauvegarder, puis cliquez sur **OK**.

6. Cliquez sur **Next**.

7. Sur la page **Spécifier une planification de la sauvegarde**, spécifiez la **planification de la sauvegarde**, puis cliquez sur **Suivant**.

    Vous pouvez planifier des sauvegardes quotidiennes (au maximum 3 fois par jour) ou hebdomadaires.

    ![Éléments de sauvegarde de Windows Server](./media/backup-configure-vault/specify-backup-schedule-close.png)

    >[AZURE.NOTE] Pour plus d’informations sur la spécification de la planification de la sauvegarde, consultez l’article [Utilisation d’Azure Backup pour remplacer votre infrastructure sur bande](backup-azure-backup-cloud-as-tape.md).

8. Sur la page **Sélectionner une stratégie de rétention**, définissez la **stratégie de rétention** pour la copie de sauvegarde.

    La stratégie de rétention spécifie la durée de stockage de la sauvegarde. Au lieu de simplement spécifier une même stratégie pour tous les points de sauvegarde, vous pouvez spécifier différentes stratégies de rétention en fonction du moment où est effectuée la sauvegarde. Vous pouvez modifier les stratégies de rétention quotidiennes, hebdomadaires, mensuelles et annuelles pour répondre à vos besoins.

9. Sur la page Choisir un type de sauvegarde initiale, sélectionnez le type de sauvegarde initiale. Laissez l’option **Automatiquement sur le réseau** sélectionnée, puis cliquez sur **Suivant**.

    Vous pouvez effectuer des sauvegardes automatiques sur le réseau ou vous pouvez sauvegarder en mode hors connexion. Le reste de cet article décrit le processus de sauvegarde automatique. Si vous préférez effectuer une sauvegarde en mode hors connexion, consultez l’article [Flux de travail de la sauvegarde hors connexion dans Azure Backup](backup-azure-backup-import-export.md) pour plus d’informations.

10. Sur la page Confirmation, passez en revue les informations, puis cliquez sur **Terminer**.

11. Lorsque l’Assistant a terminé la création de la planification de la sauvegarde, cliquez sur **Fermer**.

### Activer la limitation du réseau (facultatif)

L’agent Backup prend en charge la limitation du réseau. La limitation contrôle l’utilisation de la bande passante réseau durant le transfert de données. Cela peut s’avérer utile si vous avez besoin de sauvegarder des données pendant les heures de travail, mais ne souhaitez pas que le processus de sauvegarde interfère avec le reste du trafic internet. La limitation s’applique aux activités de sauvegarde et de restauration.

>[AZURE.NOTE] La limitation du réseau n’est pas disponible sur Windows Server 2008 R2 SP1, Windows Server 2008 SP2 ou Windows 7 (avec service packs). La fonctionnalité de limitation du réseau Azure Backup implique une qualité de Service (QoS) sur le système d’exploitation local. Même si Azure Backup est en mesure de protéger ces systèmes d’exploitation, la version de la qualité de service disponible sur ces plates-formes ne fonctionne pas avec la fonctionnalité de limitation du réseau Azure Backup. La limitation du réseau peut être utilisée sur tous les autres [systèmes d’exploitation pris en charge](backup-azure-backup-faq.md#installation-amp-configuration).

**Pour activer la limitation du réseau**

1. Dans l’agent Backup, cliquez sur **Modifier les propriétés**.

    ![Modifier les propriétés](./media/backup-configure-vault/change-properties.png)

2. Dans l’onglet **Limitation**, activez la case à cocher **Activer la limitation de la bande passante sur Internet pour les opérations de sauvegarde**.

    ![Limitation du réseau](./media/backup-configure-vault/throttling-dialog.png)

3. Une fois que vous avez activé la limitation, spécifiez la bande passante autorisée pour le transfert des données de sauvegarde durant les **Heures de travail** et les **Heures chômées**.

    Les valeurs de bande passante, qui démarrent à 512 kilo-octets par seconde, peuvent aller jusqu’à 1 023 mégaoctets par seconde. Vous pouvez également définir le début et la fin des **Heures de travail** et identifier les jours de la semaine considérés comme des jours de travail. Les heures non comprises dans les heures de travail définies sont considérées comme des heures chômées.

4. Cliquez sur **OK**.

### Pour sauvegarder les fichiers et dossiers pour la première fois

1. Dans l’agent Backup, cliquez sur **Sauvegarder maintenant** pour effectuer l’amorçage initial sur le réseau.

    ![Sauvegarder Windows Server maintenant](./media/backup-configure-vault/backup-now.png)

2. Sur la page Confirmation, vérifiez les paramètres utilisés par l’Assistant Sauvegarder maintenant pour sauvegarder les données de l’ordinateur, Puis cliquez sur **Sauvegarder**.

3. Cliquez sur **Fermer** pour fermer l’Assistant. Si vous fermez l’Assistant avant la fin du processus de sauvegarde, celui-ci continuera de s’exécuter en arrière-plan.

Une fois la sauvegarde initiale terminée, le statut **Tâche terminée** apparaît dans la console Backup.

![RI terminé](./media/backup-configure-vault/ircomplete.png)

## Des questions ?
Si vous avez des questions ou si vous souhaitez que certaines fonctionnalités soient incluses, [envoyez-nous vos commentaires](http://aka.ms/azurebackup_feedback).

## Étapes suivantes
Pour plus d’informations sur la sauvegarde des machines virtuelles ou d’autres charges de travail, consultez les références suivantes :

- Maintenant que vous avez sauvegardé vos fichiers et vos dossiers, vous pouvez [gérer vos coffres et vos serveurs](backup-azure-manage-windows-server.md).
- Si vous avez besoin de restaurer une sauvegarde, référez-vous à cet article pour [restaurer des fichiers sur un ordinateur Windows](backup-azure-restore-windows-server.md).

<!---HONumber=AcomDC_0713_2016-->
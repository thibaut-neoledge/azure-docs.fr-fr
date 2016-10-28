<properties
	pageTitle="Découverte : Protéger les machines virtuelles Azure avec un coffre de sauvegarde| Microsoft Azure"
	description="Protégez les machines virtuelles Azure avec un coffre de sauvegarde. Ce didacticiel explique comment créer un archivage, inscrire des machines virtuelles, créer des stratégies et protéger des machines virtuelles dans Azure."
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="cfreeman"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="09/15/2016"
	ms.author="markgal; jimpark"/>


# Découverte : sauvegarde des machines virtuelles Azure

> [AZURE.SELECTOR]
- [Protéger les machines virtuelles avec un coffre Recovery Services](backup-azure-vms-first-look-arm.md)
- [Protéger les machines virtuelles Azure avec un coffre de sauvegarde](backup-azure-vms-first-look.md)

Ce didacticiel vous accompagne tout au long de la procédure de sauvegarde d’une machine virtuelle Azure vers un archivage de sauvegarde dans Azure. Cet article décrit le modèle Classic ou le modèle de déploiement Service Manager, pour la sauvegarde des machines virtuelles. Si vous souhaitez sauvegarder une machine virtuelle dans un coffre Recovery Services qui appartient à un groupe de ressources, consultez l’article [Découverte : Protéger les machines virtuelles avec un coffre Recovery Services](backup-azure-vms-first-look-arm.md). Pour pouvoir suivre ce didacticiel, ces conditions préalables doivent être réunies :

- Vous avez créé une machine virtuelle dans votre abonnement Azure.
- La machine virtuelle dispose d’une connectivité à des adresses IP publiques Azure. Pour plus d’informations, consultez la rubrique [Connectivité réseau](./backup-azure-vms-prepare.md#network-connectivity).

La sauvegarde d’une machine virtuelle se compose de cinq étapes principales :

![étape-1](./media/backup-azure-vms-first-look/step-one.png) Créez un coffre de sauvegarde ou identifiez un coffre de sauvegarde existant. <br/> ![étape-2](./media/backup-azure-vms-first-look/step-two.png) Utilisez le portail Azure Classic pour découvrir et inscrire les machines virtuelles. <br/> ![étape-3](./media/backup-azure-vms-first-look/step-three.png) Installez l’agent de machine virtuelle. <br/> ![étape-4](./media/backup-azure-vms-first-look/step-four.png) Créez la stratégie de protection des machines virtuelles. <br/> ![étape-5](./media/backup-azure-vms-first-look/step-five.png) Exécutez la sauvegarde.

![Vue d’ensemble du processus de sauvegarde de machine virtuelle](./media/backup-azure-vms-first-look/backupazurevm-classic.png)

>[AZURE.NOTE] Azure dispose de deux modèles de déploiement pour créer et utiliser des ressources : [Azure Resource Manager et Azure Classic](../resource-manager-deployment-model.md). Ce didacticiel est destiné aux machines virtuelles qui peuvent être créées dans le portail Azure Classic. Le service Azure Backup prend en charge les machines virtuelles basées sur Resource Manager. Pour plus d’informations sur la sauvegarde des machines virtuelles dans un coffre Recovery Services, consultez l’article [Découverte : Protéger les machines virtuelles avec un coffre Recovery Services](backup-azure-vms-first-look-arm.md).



## Étape 1 : création d’un archivage (ou coffre) de sauvegarde pour une machine virtuelle

Un coffre de sauvegarde est une entité qui stocke les sauvegardes et les points de récupération créés au fil du temps. Le coffre de sauvegarde contient également les stratégies de sauvegarde qui sont appliquées aux machines virtuelles en cours de sauvegarde.

1. Connectez-vous au [portail Azure Classic](http://manage.windowsazure.com/).

2. Dans le coin inférieur gauche du portail Azure, cliquez sur **Nouveau**.

    ![Cliquez sur le menu Nouveau](./media/backup-azure-vms-first-look/new-button.png)

3. Dans l’Assistant Création rapide, cliquez sur **Data Services** > **Recovery Services** > **Coffre de sauvegarde** > **Création rapide**.

    ![Créer un archivage de sauvegarde](./media/backup-azure-vms-first-look/new-vault-wizard-one-subscription.png)

    L’Assistant vous invite à entrer le **Nom** et la **Région**. Si vous gérez plusieurs abonnements, une boîte de dialogue de choix de l'abonnement s'affiche.

4. Pour le champ **Nom**, entrez un nom convivial permettant d’identifier le coffre. Le nom doit être unique pour l’abonnement Azure.

5. Dans **Region**, sélectionnez la région géographique du coffre. Le coffre **doit** se trouver dans la même région que les machines virtuelles qu’il protège.

    Si vous ne connaissez pas la région dans laquelle se trouve votre machine virtuelle, fermez cet Assistant et cliquez sur **Machines virtuelles** dans la liste des services Azure. La colonne Emplacement fournit le nom de la région. Si vous avez des machines virtuelles dans plusieurs régions, créez un coffre de sauvegarde par région.

6. Si la boîte de dialogue de l’Assistant ne contient aucun **Abonnement**, passez à l’étape suivante. Si vous travaillez avec plusieurs abonnements, sélectionnez un abonnement à associer au nouveau coffre de sauvegarde.

    ![Créer une notification toast l’archivage](./media/backup-azure-vms-first-look/backup-vaultcreate.png)

7. Cliquez sur **Create vault**. La création du coffre de sauvegarde peut prendre du temps. Surveillez les notifications d'état en bas du portail.

    ![Créer une notification toast l’archivage](./media/backup-azure-vms-first-look/create-vault-demo.png)

    Un message confirme que le coffre a été correctement créé. Celui-ci est répertorié dans la page **Recovery Services** comme étant **Actif**.

    ![Créer une notification toast l’archivage](./media/backup-azure-vms-first-look/create-vault-demo-success.png)

8. Dans la liste des coffres de la page **Recovery Services**, sélectionnez le coffre que vous avez créé pour ouvrir la page **Démarrage rapide**.

    ![Liste des archivages de sauvegarde](./media/backup-azure-vms-first-look/active-vault-demo.png)

9. Dans la page **Démarrage rapide**, cliquez sur **Configurer** pour accéder à l’option de réplication du stockage. ![Liste des archivages de sauvegarde](./media/backup-azure-vms-first-look/configure-storage.png)

10. Dans l’option **Réplication du stockage**, choisissez l’option de réplication de votre coffre.

    ![Liste des archivages de sauvegarde](./media/backup-azure-vms-first-look/backup-vault-storage-options-border.png)

    Par défaut, votre archivage utilise un stockage géo-redondant. Si vous utilisez Azure comme sauvegarde principale, choisissez Stockage géo-redondant. Choisissez Stockage localement redondant si vous souhaitez une option plus économique, mais moins durable. Pour en savoir plus sur les options de stockage géo-redondant et localement redondant, consultez l’article [Réplication Azure Storage](../storage/storage-redundancy.md).

Après avoir sélectionné l’option de stockage pour votre archivage, vous pouvez associer la machine virtuelle à l’archivage. Pour commencer l’association, découvrez et enregistrez les machines virtuelles Azure.

## Étape 2 : découverte et enregistrement des machines virtuelles Azure
Avant d’enregistrer la machine virtuelle dans un coffre, lancez le processus de découverte pour identifier les nouvelles machines virtuelles éventuelles. Ce dernier renvoie la liste des machines virtuelles de l’abonnement et des informations supplémentaires, comme le nom du service cloud et la région.

1. Connectez-vous au [portail Azure Classic](http://manage.windowsazure.com/).

2. Dans le portail Azure Classic, cliquez sur **Recovery Services** pour ouvrir la liste des coffres Recovery Services. ![Sélectionner la charge de travail](./media/backup-azure-vms-first-look/recovery-services-icon.png)

3. Dans la liste des coffres, sélectionnez le coffre de sauvegarde d’une machine virtuelle.

    Votre coffre sélectionné s’ouvre à la page **Démarrage rapide**.

4. Dans le menu du coffre, cliquez sur **Éléments inscrits**.

    ![Sélectionner la charge de travail](./media/backup-azure-vms-first-look/configure-registered-items.png)

5. Dans le menu **Type**, sélectionnez **Machine virtuelle Azure**.

    ![Sélectionner la charge de travail](./media/backup-azure-vms/discovery-select-workload.png)

6. Cliquez sur **DÉCOUVRIR** en bas de la page. ![Bouton découverte](./media/backup-azure-vms/discover-button-only.png)

    Le processus de découverte peut durer quelques minutes, le temps que les machines virtuelles soient affichées sous forme de tableau. Une notification affichée en bas de l’écran vous informe que le processus est en cours d’exécution.

    ![Détection des machines virtuelles](./media/backup-azure-vms/discovering-vms.png)

    La notification change lorsque le processus est terminé.

    ![Détection exécutée](./media/backup-azure-vms-first-look/discovery-complete.png)

7. Cliquez sur **INSCRIRE** en bas de la page. ![Bouton inscription](./media/backup-azure-vms-first-look/register-icon.png)

8. Dans le menu contextuel **Inscrire les éléments**, choisissez les machines virtuelles que vous souhaitez inscrire.

    >[AZURE.TIP] Plusieurs machines virtuelles peuvent être inscrites en même temps.

    Un travail est créé pour chaque machine virtuelle sélectionnée.

9. Cliquez sur **Afficher le travail** dans la notification pour accéder à la page **Travaux**.

    ![Inscrire du travail](./media/backup-azure-vms/register-create-job.png)

    La machine virtuelle est également affichée dans la liste des éléments inscrits avec l’état de l’opération d’inscription.

    ![État de l’inscription 1](./media/backup-azure-vms/register-status01.png)

    Une fois l’opération terminée, l’état change pour refléter l’état *inscrit*.

    ![État de l’inscription 2](./media/backup-azure-vms/register-status02.png)

## Étape 3 : installation de l’agent de machine virtuelle sur la machine virtuelle

L’agent de machine virtuelle Azure doit être installé sur la machine virtuelle Azure pour permettre la prise en charge de l’extension Backup. Si votre machine virtuelle a été créée à partir de la galerie Azure, l’agent est déjà installé sur la machine virtuelle ! Vous pouvez passer à la [protection de vos machines virtuelles](backup-azure-vms-first-look.md#step-4-protect-azure-virtual-machines).

Si votre machine virtuelle a migré à partir d'un centre de données local, il est probable que l’agent de machine virtuelle n’y soit pas installé. Vous devez installer l'agent sur la machine virtuelle avant de passer à l’étape de protection de la machine virtuelle. Pour obtenir des instructions détaillées sur l’installation de l’agent de machine virtuelle, consultez la [section Agent VM de l’article sur la sauvegarde des machines virtuelles](backup-azure-vms-prepare.md#vm-agent).


## Étape 4 : création de la stratégie de sauvegarde
Avant de déclencher le travail de sauvegarde initiale, définissez la planification des instantanés de sauvegarde. La planification des instantanés de sauvegarde et la durée de rétention de ces instantanés constituent la stratégie de sauvegarde. Les informations de rétention sont basées sur le schéma de rotation des sauvegardes grand-père-père-fils.

1. Accédez au coffre de sauvegarde qui se trouve sous **Recovery Services** dans le portail Azure Classic, puis cliquez sur **Éléments inscrits**.
2. Sélectionnez **Machine virtuelle Azure** dans le menu déroulant.

    ![Sélectionner la charge de travail dans le portail](./media/backup-azure-vms/select-workload.png)

3. En bas de la page, cliquez sur **PROTÉGER**. ![Cliquez sur Protéger](./media/backup-azure-vms-first-look/protect-icon.png)

    **L’Assistant Protection des éléments** s’affiche et répertorie *uniquement* les machines virtuelles inscrites qui ne sont pas protégées.

    ![Configurer les paramètres de protection pendant la mise à jour](./media/backup-azure-vms/protect-at-scale.png)

4. Sélectionnez les machines virtuelles que vous souhaitez protéger.

    Si au moins deux machines virtuelles portent le même nom, utilisez le service cloud pour les distinguer.

5. Dans le menu **Configurer la protection**, sélectionnez une stratégie existante ou créez-en une pour protéger les machines virtuelles que vous avez identifiées.

    Les nouveaux coffres de sauvegarde ont une stratégie par défaut associée au coffre. Cette stratégie prend un instantané chaque soir ; celui-ci est conservé pendant 30 jours. Vous pouvez associer plusieurs machines virtuelles à chaque stratégie de sauvegarde. Toutefois, vous ne pouvez associer votre machine virtuelle qu’à une seule stratégie à la fois.

    ![Protéger grâce à la nouvelle stratégie](./media/backup-azure-vms/policy-schedule.png)

    >[AZURE.NOTE] Une stratégie de sauvegarde inclut le schéma de rétention des sauvegardes planifiées. Si vous sélectionnez une stratégie de sauvegarde existante, vous ne pourrez pas modifier les options de rétention à l’étape suivante.

6. Dans le champ **Durée de rétention**, définissez l’intervalle des points de sauvegarde spécifiques (quotidien, hebdomadaire, mensuel ou annuel).

    ![La machine virtuelle est sauvegardée avec un point de récupération](./media/backup-azure-vms/long-term-retention.png)

    La stratégie de rétention spécifie la durée de stockage d’une sauvegarde. Vous pouvez spécifier des stratégies de rétention différentes en fonction de la date à laquelle la sauvegarde est effectuée.

7. Cliquez sur **Travaux** pour afficher la liste des travaux **Configurer la protection**.

    ![Configurer le travail de protection](./media/backup-azure-vms/protect-configureprotection.png)

    Maintenant que vous avez créé la stratégie, passez à l'étape suivante et lancez la sauvegarde initiale.

## Étape 5 : sauvegarde initiale

Une fois la machine virtuelle protégée par une stratégie, vous pouvez afficher cette relation dans l’onglet **Éléments protégés**. Avant l’exécution de la sauvegarde initiale, la valeur **Protégé (en attente de sauvegarde initiale)** s’affiche sous **État de la protection**. Par défaut, la première sauvegarde planifiée est la *sauvegarde initiale*.

![Sauvegarde en attente](./media/backup-azure-vms-first-look/protection-pending-border.png)

Pour lancer la sauvegarde initiale :

1. Cliquez sur le bouton **Sauvegarder maintenant** en bas de la page **Éléments protégés**. ![Icône Sauvegarder maintenant](./media/backup-azure-vms-first-look/backup-now-icon.png)

    Le service Azure Backup crée un travail de sauvegarde pour l’opération de sauvegarde initiale.

2. Cliquez sur l’onglet **Travaux** pour afficher la liste des travaux.

    ![Sauvegarde en cours](./media/backup-azure-vms-first-look/protect-inprogress.png)

    Une fois la sauvegarde initiale terminée, l’état de la machine virtuelle indiquée dans l’onglet **Éléments protégés** est défini sur la valeur *Protégé*.

    ![La machine virtuelle est sauvegardée avec un point de récupération](./media/backup-azure-vms/protect-backedupvm.png)

    >[AZURE.NOTE] La sauvegarde des machines virtuelles est un processus local. Vous ne pouvez pas sauvegarder les machines virtuelles d’une région donnée vers un archivage de sauvegarde d’une autre région. Par conséquent, il faut créer au moins un archivage pour chaque région Azure équipée de machines virtuelles nécessitant une sauvegarde.

## Étapes suivantes
Maintenant que vous êtes parvenu à sauvegarder une machine virtuelle, d’autres étapes peuvent vous intéresser. L’étape la plus logique consiste à vous familiariser avec la restauration des données sur une machine virtuelle. Toutefois, certaines tâches de gestion vous aideront à comprendre comment protéger vos données et réduire les coûts.

- [Gestion et surveillance de vos machines virtuelles](backup-azure-manage-vms.md)
- [Restauration des machines virtuelles](backup-azure-restore-vms.md)
- [Instructions pour la résolution des problèmes](backup-azure-vms-troubleshoot.md)


## Des questions ?
Si vous avez des questions ou si vous souhaitez que certaines fonctionnalités soient incluses, [envoyez-nous vos commentaires](http://aka.ms/azurebackup_feedback).

<!---HONumber=AcomDC_0921_2016-->
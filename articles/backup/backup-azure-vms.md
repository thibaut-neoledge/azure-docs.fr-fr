<properties
	pageTitle="Sauvegarde des machines virtuelles Azure | Microsoft Azure"
	description="Découvrez, inscrivez et sauvegardez vos machines virtuelles avec ces procédures pour la sauvegarde de la machine virtuelle Azure."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""
	keywords="sauvegarde de machine virtuelle ; sauvegarder la machine virtuelle ; sauvegarde et récupération d’urgence"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="11/17/2015"
	ms.author="trinadhk; aashishr; jimpark; markgal"/>


# Sauvegarde des machines virtuelles Azure
Cet article fournit les procédures de sauvegarde des machines virtuelles Azure existantes, pour protéger vos VM conformément aux stratégies de sauvegarde et de récupération d’urgence.

Tout d’abord, vous devez prendre en compte certains éléments avant de sauvegarder une machine virtuelle Azure. Si vous ne l’avez pas déjà fait, effectuez la [configuration requise](backup-azure-vms-prepare.md) pour préparer votre environnement pour la sauvegarde de machines virtuelles avant de continuer.

Si vous recherchez des informations sur la [planification de votre infrastructure de sauvegarde de machines virtuelles dans Azure](backup-azure-vms-introduction.md) ou sur les [machines virtuelles Azure](https://azure.microsoft.com/documentation/services/virtual-machines/), vous pouvez suivre ces liens pour obtenir ces informations.

Les trois principales étapes de la sauvegarde des machines virtuelles sont les suivantes :

![Trois étapes pour sauvegarder une machine virtuelle IaaS Azure](./media/backup-azure-vms/3-steps-for-backup.png)

>[AZURE.NOTE]La sauvegarde de machine virtuelle est locale. Vous ne pouvez pas sauvegarder les machines virtuelles d’une région donnée vers un archivage de sauvegarde d’une autre région. Par conséquent, au moins 1 archivage de sauvegarde doit être créé dans cette région pour chaque région Azure équipée de machines virtuelles nécessitant une sauvegarde.

## Étape 1 - Découverte des machines virtuelles Azure
Le processus de découverte doit toujours être exécuté en premier pour s’assurer que les nouvelles machines virtuelles ajoutées à l’abonnement sont identifiées. Le processus interroge Azure pour obtenir la liste des machines virtuelles de l’abonnement et des informations supplémentaires, comme le nom du service cloud et la région.

1. Accédez à l’archivage de sauvegarde qui se trouve sous **Recovery Services** dans le portail Azure, puis cliquez sur **Éléments inscrits**.

2. Sélectionnez **Machine virtuelle Azure** dans le menu déroulant.

    ![sélectionner la charge de travail](./media/backup-azure-vms/discovery-select-workload.png)

3. Cliquez sur **DÉCOUVRIR** en bas de la page. ![bouton découverte](./media/backup-azure-vms/discover-button-only.png)

    Le processus de découverte peut durer quelques minutes, le temps que les machines virtuelles soient affichées sous forme de tableau. Une notification affichée en bas de l’écran vous indique que le processus est en cours d’exécution.

    ![découvrir des machines virtuelles](./media/backup-azure-vms/discovering-vms.png)

    La notification change lorsque le processus est terminé.

    ![découverte terminée](./media/backup-azure-vms/discovery-complete.png)

##  Étape 2 - Inscription des machines virtuelles Azure
Vous inscrivez une machine virtuelle Azure pour l’associer au service Azure Backup. L’inscription est généralement une activité unique.

1. Accédez à l’archivage de sauvegarde qui se trouve sous **Recovery Services** dans le portail Azure, puis cliquez sur **Éléments inscrits**.

2. Sélectionnez **Machine virtuelle Azure** dans le menu déroulant.

    ![sélectionner la charge de travail](./media/backup-azure-vms/discovery-select-workload.png)

3. Cliquez sur **INSCRIRE** en bas de la page. ![bouton inscription](./media/backup-azure-vms/register-button-only.png)

4. Dans le menu contextuel **Inscrire les éléments**, choisissez les machines virtuelles que vous souhaitez inscrire. Si au moins deux machines virtuelles portent le même nom, utilisez le service cloud pour les distinguer.

    >[AZURE.TIP]Plusieurs machines virtuelles peuvent être inscrites en même temps.

    Un travail est créé pour chaque machine virtuelle sélectionnée.

5. Cliquez sur **Afficher le travail** dans la notification pour accéder à la page **Travaux**.

    ![inscrire le travail](./media/backup-azure-vms/register-create-job.png)

    La machine virtuelle est également affichée dans la liste des éléments inscrits avec l’état de l’opération d’inscription.

    ![État de l’inscription 1](./media/backup-azure-vms/register-status01.png)

    Une fois l’opération terminée, l’état change pour refléter l’état *inscrit*.

    ![État de l’inscription 2](./media/backup-azure-vms/register-status02.png)

## Étape 3 - Protection des machines virtuelles Azure
Vous pouvez désormais configurer une stratégie de sauvegarde et de rétention pour la machine virtuelle. Plusieurs machines virtuelles peuvent être protégées à l’aide d’une même action de protection.

Les archivages Azure Backup créés après mai 2015 sont livrés avec une stratégie par défaut intégrée. Cette stratégie par défaut est fournie avec une durée de rétention par défaut de 30 jours et une fréquence quotidienne de sauvegarde d’une fois par jour.

1. Accédez à l’archivage de sauvegarde qui se trouve sous **Recovery Services** dans le portail Azure, puis cliquez sur **Éléments inscrits**.
2. Sélectionnez **Machine virtuelle Azure** dans le menu déroulant.

    ![Sélectionner la charge de travail dans le portail](./media/backup-azure-vms/select-workload.png)

3. En bas de la page, cliquez sur **PROTÉGER**.

    L’Assistant **Protection des éléments** s’affiche. Cet Assistant ne répertorie que les machines virtuelles qui sont inscrites et non protégées. Il permet de sélectionner les machines virtuelles que vous souhaitez protéger.

    Si au moins deux machines virtuelles portent le même nom, utilisez le service cloud pour les distinguer.

    >[AZURE.TIP]Vous pouvez protéger plusieurs machines virtuelles en même temps.

    ![Configurer les paramètres de protection pendant la mise à jour](./media/backup-azure-vms/protect-at-scale.png)

4. Choisissez une **planification de sauvegarde** pour sauvegarder les machines virtuelles que vous avez sélectionnées. Vous pouvez sélectionner un ensemble de stratégies existant ou en définir un nouveau.

    Vous pouvez associer plusieurs machines virtuelles à chaque stratégie de sauvegarde. Toutefois, vous ne pouvez associer votre machine virtuelle qu’à une seule stratégie à un moment donné.

    ![Protéger grâce à la nouvelle stratégie](./media/backup-azure-vms/policy-schedule.png)

    >[AZURE.NOTE]Une stratégie de sauvegarde inclut le schéma de rétention des sauvegardes planifiées. Si vous sélectionnez une stratégie de sauvegarde existante, vous ne pourrez pas modifier les options de rétention à l’étape suivante.

5. Choisissez une **plage de rétention** à associer aux sauvegardes.

    ![Protéger avec la rétention flexible](./media/backup-azure-vms/policy-retention.png)

    La stratégie de rétention spécifie la durée de stockage d’une sauvegarde et vous pouvez spécifier différentes stratégies de rétention en fonction du moment où est effectuée la sauvegarde. Par exemple, le point de sauvegarde effectué à la fin de chaque trimestre peut devoir être conservé plus longtemps à des fins d’audit, alors que le point de sauvegarde effectué quotidiennement (qui fait office de point de récupération opérationnel) doit être conservé uniquement pendant 90 jours.

    ![La machine virtuelle est sauvegardée avec un point de récupération](./media/backup-azure-vms/long-term-retention.png)

    Dans cet exemple :

    - **Stratégie de rétention quotidienne** : les sauvegardes effectuées quotidiennement sont stockées pendant 30 jours.
    - **Stratégie de rétention hebdomadaire** : les sauvegardes effectuées tous les dimanches sont conservées pendant 104 semaines.
    - **Stratégie de rétention mensuelle** : les sauvegardes effectuées le dernier dimanche de chaque mois sont conservées pendant 120 mois.
    - **Stratégie de rétention annuelle** : les sauvegardes effectuées le premier dimanche de janvier de chaque année sont conservées pendant 99 ans.

    Un travail est créé pour configurer la stratégie de protection et associer à celle-ci les machines virtuelles sélectionnées.

6. Cliquez sur **Travaux** et sélectionnez le filtre approprié pour afficher la liste des travaux **Configurer la protection**.

    ![Configurer le travail de protection](./media/backup-azure-vms/protect-configureprotection.png)

## Sauvegarde initiale
Une fois la machine virtuelle protégée par une stratégie, elle apparaît sous l’onglet **Éléments protégés** avec l’état *Protégé (sauvegarde initiale en attente)*. Par défaut, la première sauvegarde planifiée est la *sauvegarde initiale*.

Pour déclencher la sauvegarde initiale immédiatement après la configuration de la protection :

1. Cliquez sur le bouton **Sauvegarder maintenant** en bas de la page **Éléments protégés**.

    Le service Azure Backup crée un travail de sauvegarde pour l’opération de sauvegarde initiale.

2. Cliquez sur l’onglet **Travaux** pour afficher la liste des travaux.

    ![Sauvegarde en cours](./media/backup-azure-vms/protect-inprogress.png)

>[AZURE.NOTE]Dans le cadre de l’opération de sauvegarde, le service Azure Backup émet une commande vers l’extension de sauvegarde de chaque machine virtuelle pour vider toutes les écritures et prendre un instantané cohérent.

Une fois la sauvegarde initiale terminée, l’état de la machine virtuelle présente dans l’onglet **Éléments protégés** s’affiche en tant que *Protégé*.

![La machine virtuelle est sauvegardée avec un point de récupération](./media/backup-azure-vms/protect-backedupvm.png)

## Affichage des détails et de l’état de sauvegarde
Une fois la protection appliquée, le nombre de machines virtuelles augmente également sur la page de synthèse nommée **Tableau de bord**. La page **Tableau de bord** affiche également le nombre de travaux des dernières 24 heures ayant *réussi*, ayant *échoué* ou toujours *en cours*. Cliquez sur n’importe quelle catégorie pour l’examiner plus en détail dans la page **Travaux**.

![État de la sauvegarde sur la page Tableau de bord](./media/backup-azure-vms/dashboard-protectedvms.png)

Les valeurs du tableau de bord sont actualisées toutes les 24 heures.

## Résolution des erreurs
Si vous rencontrez des problèmes pendant la sauvegarde de votre machine virtuelle, consultez ces conseils de [dépannage](backup-azure-vms-troubleshoot.md) pour obtenir de l’aide.

## Étapes suivantes

- [Gestion et surveillance de vos machines virtuelles](backup-azure-manage-vms.md)
- [Restauration des machines virtuelles](backup-azure-restore-vms.md)

<!---HONumber=Nov15_HO4-->

<properties
	pageTitle="Restaurer des machines virtuelles à partir d’une sauvegarde à l’aide du portail Azure | Microsoft Azure"
	description="Restaurer une machine virtuelle Azure à partir d’un point de récupération à l’aide du portail Azure"
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="jwhit"
	editor=""
	keywords="restauration de sauvegarde ; restauration ; point de récupération ;"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/19/2016"
	ms.author="trinadhk; jimpark;"/>


# Utiliser le portail Azure pour restaurer des machines virtuelles

> [AZURE.SELECTOR]
- [Restaurer des machines virtuelles dans le portail classique](backup-azure-restore-vms.md)
- [Restaurer des machines virtuelles dans le portail Azure](backup-azure-arm-restore-vms.md)


Vous protégez vos données avec le service Backup en prenant des instantanés de vos données à des intervalles définis. Ces instantanés sont considérés comme des points de récupération stockés dans des coffres Recovery Services. Lorsqu’il est nécessaire de réparer ou de générer de nouveau une machine virtuelle, sa restauration peut s’effectuer à partir des points de récupération enregistrés. La restauration d’un point de récupération permet de revenir à l’état dans lequel se trouvait la machine virtuelle au moment de l’enregistrement du point de récupération. Cet article explique comment restaurer une machine virtuelle.

> [AZURE.NOTE] Azure dispose de deux modèles de déploiement pour créer et utiliser des ressources : [Resource Manager et classique](../resource-manager-deployment-model.md). Cet article fournit les informations et les procédures relatives à la restauration des machines virtuelles déployées à l’aide du modèle Resource Manager.



## Restaurer un point de récupération

1. Connectez-vous au [portail Azure](http://ms.portal.azure.com/).

2. Dans le menu Azure, cliquez sur **Parcourir** et, dans la liste des services, tapez **Recovery Services**. La liste des services s’ajuste en fonction de ce que vous tapez. Lorsque vous voyez **Coffres Recovery Services**, cliquez dessus.

    ![Ouvrir le coffre Recovery Services](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    La liste des coffres de l’abonnement s’affiche.

    ![Liste des coffres Recovery Services](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)

3. Dans la liste, sélectionnez le coffre associé à la machine virtuelle que vous souhaitez restaurer. Lorsque vous cliquez sur le coffre, son tableau de bord s’affiche.

    ![Liste des coffres Recovery Services](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade.png)

4. Dans le tableau de bord du coffre, effectuez les opérations suivantes : Sur la vignette **Éléments de sauvegarde**, cliquez sur **Azure Virtual Machines** pour afficher les machines virtuelles associées au coffre.

    ![tableau de bord du coffre](./media/backup-azure-arm-restore-vms/vault-dashboard.png)

    Le panneau **Éléments de sauvegarde** s’ouvre et affiche la liste des machines virtuelles.

    ![liste des machines virtuelles du coffre](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault.png)

5. Dans la liste, sélectionnez une machine virtuelle pour ouvrir le tableau de bord.

    ![liste des machines virtuelles du coffre](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault-selected.png)

    Le tableau de bord de la machine virtuelle s’ouvre sur la zone de surveillance qui contient la vignette Points de récupération.

    ![liste des machines virtuelles du coffre](./media/backup-azure-arm-restore-vms/vm-blade.png)

6. Dans le menu du tableau de bord de la machine virtuelle, cliquez sur **Restaurer**

    ![liste des machines virtuelles du coffre](./media/backup-azure-arm-restore-vms/vm-blade-menu-restore.png)

    Le panneau de restauration s’ouvre.

    ![panneau de restauration](./media/backup-azure-arm-restore-vms/restore-blade.png)

7. Sur le panneau **Restaurer**, cliquez sur **Point de récupération** pour ouvrir le panneau **Sélectionner le point de récupération**.

    ![panneau de restauration](./media/backup-azure-arm-restore-vms/recovery-point-selector.png)

    Par défaut, la boîte de dialogue affiche tous les points de récupération des 30 derniers jours. Modifiez les listes **Période**, **Année**, et **Point de restauration de cohérence** comme vous le souhaitez. Pour plus d’informations sur chaque type de point de restauration, consultez l’explication de la [cohérence des données](./backup-azure-vms-introduction.md#data-consistency).
    - **Période** correspond au mois de l’année, ou aux 30 derniers jours.
    - **Année** correspond à l’année en question.
    - **Point de restauration de cohérence**. Vous pouvez le choisir à partir de cette liste :
        - points de restauration cohérents d’incident ;
        - points de restauration cohérents d’application ;
        - points de restauration cohérents de système de fichiers ;
        - tous les points de restauration.  

8. Choisissez un point de récupération et cliquez sur **OK**.

    ![choisir un point de récupération](./media/backup-azure-arm-restore-vms/select-recovery-point.png)

    Le panneau **Restaurer** indique que le point de récupération est défini.

    ![le point de récupération est défini](./media/backup-azure-arm-restore-vms/recovery-point-set.png)

9. Sur le panneau **Restaurer**, cliquez sur **Configuration de la récupération** pour ouvrir son panneau.

    ![l’assistant de configuration de récupération est défini](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

## Choisir une configuration de récupération de machine virtuelle

Maintenant que vous avez sélectionné le point de récupération, choisissez un compte de stockage et la méthode de configuration de votre machine virtuelle de récupération. Pour configurer la machine virtuelle de récupération, vous pouvez utiliser le portail Azure ou PowerShell. Lorsque vous choisissez un compte de stockage, vous devez choisir un compte qui partage le même emplacement que le coffre Recovery Services. Les comptes de stockage redondant dans une zone ne sont pas pris en charge. S’il n’existe aucun compte de stockage avec le même emplacement que le coffre Recovery Services, vous devez en créer un avant de commencer l’opération de restauration.

1. Si vous n’en n’êtes pas encore là, accédez au panneau **Restaurer**. Vérifiez qu’un **Point de récupération** a bien été sélectionné, puis cliquez sur **Configuration de la récupération** pour ouvrir le panneau **Configuration de la récupération**.

    ![l’assistant de configuration de récupération est défini](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

2. Sur le panneau **Configuration de la récupération**, cliquez sur **Compte de stockage** pour ouvrir la liste des comptes de stockage au même emplacement que le coffre Recovery Services.

3. Dans la liste, choisissez un compte de stockage, puis cliquez sur OK.

    ![liste des comptes de stockage](./media/backup-azure-arm-restore-vms/list-of-storage-accounts.png)

    Dans le panneau **Configuration de la récupération**, le compte de stockage choisi s’affiche dans la boîte de dialogue **Compte de stockage**.

    ![choisir des comptes de stockage](./media/backup-azure-arm-restore-vms/selected-storage-account.png)

4. Vous pouvez configurer une machine virtuelle ARM ou classique lorsque vous restaurez une machine virtuelle dans le portail. Si vous souhaitez restaurer une configuration complexe - comme une des options de la liste suivante, vous devez utiliser PowerShell pour configurer votre machine virtuelle à partir du disque. Si tel n’est pas le cas, passez à l’étape 5.

    ![liste des configurations complexes de machines virtuelles](./media/backup-azure-arm-restore-vms/complex-vm-configurations.png)

    Si la restauration de la configuration *est* considérée comme complexe, sur le panneau **Configuration de la récupération**, cliquez sur **OK**. Le panneau **Configuration de la récupération** se ferme et une coche apparaît en regard de la configuration de la récupération. Cliquez sur **Restaurer** pour démarrer la tâche de restauration du disque. Ignorez l’étape 5 et passez à [Restauration de machines virtuelles avec des configurations de réseau spéciales](#restoring-vms-with-special-network-configurations).

    ![Restauration configurée](./media/backup-azure-arm-restore-vms/restore-configured.png)

5. Sur le panneau **Configuration de la récupération**, cliquez sur **Configuration de la machine virtuelle** pour ouvrir le panneau **Créer une VM**.

    ![Ouvrir le panneau Créer une VM](./media/backup-azure-arm-restore-vms/recovery-configuration-create-vm.png)

6. Sur le panneau **Créer une VM**, entrez ou sélectionnez des valeurs pour chacun des champs suivants :

    - **Nom de la machine virtuelle** : entrez un nom pour la machine virtuelle. Le nom doit être unique pour le groupe de ressources (pour une machine virtuelle ARM) ou le service cloud (pour une machine virtuelle classique). Si vous remplacez une machine virtuelle existante portant le même nom, commencez par supprimer la machine virtuelle existante ainsi que les disques de données, puis restaurez les données à partir d’Azure Backup.
    - **Groupe de ressources** : sélectionnez un groupe de ressources existant ou créez-en un. Si vous restaurez une machine virtuelle classique, utilisez ce champ pour spécifier le nom d’un nouveau service cloud. Lorsque vous créez un nouveau groupe de ressources/service cloud, le nom doit être globalement unique. En général, le nom du service cloud est associé à une URL publique. Par exemple : [cloudservice]. cloudapp.net. Si vous essayez d’utiliser un nom de service cloud/groupe de ressources cloud déjà utilisé, Azure attribue au service cloud/groupe de ressources le même nom que la machine virtuelle. Azure affiche les groupes de ressources/services cloud et les machines virtuelles qui ne sont associées à aucun groupe d’affinités. Pour en savoir plus, consultez [Comment migrer des groupes d’affinités vers un réseau virtuel régional](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
    - **Réseau virtuel** : sélectionnez le réseau virtuel lors de la création de la machine virtuelle. Le champ indique tous les réseaux virtuels associés à l’abonnement.

    > [AZURE.NOTE] Vous devez sélectionner un réseau virtuel lors de la restauration d’une machine virtuelle ARM. Un réseau virtuel est facultatif pour une machine virtuelle classique.

    - **Sous-réseau** : si le réseau virtuel a des sous-réseaux, le premier sous-réseau est sélectionné par défaut. En présence de sous-réseaux supplémentaires, sélectionnez le sous-réseau souhaité.

7. Sur le panneau **Créer une VM**, cliquez sur **OK** pour terminer la configuration.

    Le panneau **Créer une VM** se ferme et le panneau **Configuration de la récupération** affiche le nom de la nouvelle machine virtuelle.

    ![Configuration de la récupération terminée](./media/backup-azure-arm-restore-vms/recovery-configuration-complete.png)

8. Sur le panneau **Configuration de la récupération**, cliquez sur **OK** pour finaliser la configuration de la restauration.

9. Sur le panneau **Restaurer**, cliquez sur **Restaurer** pour déclencher l’opération de restauration.

    ![Configuration de la récupération terminée](./media/backup-azure-arm-restore-vms/trigger-restore-operation.png)

## Suivi de l’opération de restauration

Une fois que vous déclenchez l’opération de restauration, le service de sauvegarde crée une tâche pour le suivi de l’opération de restauration. Par ailleurs, le service de sauvegarde crée et affiche temporairement la notification.

![Restauration déclenchée](./media/backup-azure-arm-restore-vms/restore-triggered.png)

Si vous ne voyez pas la notification, vous avez toujours la possibilité de cliquer sur l’icône Notifications pour afficher les notifications.

![Restauration déclenchée](./media/backup-azure-arm-restore-vms/restore-notification.png)

Pour voir l’opération en cours de traitement, ou l’afficher lorsqu’elle est terminée, ouvrez la liste des tâches de sauvegarde.

1. Dans le menu Azure, cliquez sur **Parcourir** et, dans la liste des services, tapez **Recovery Services**. La liste des services s’ajuste en fonction de ce que vous tapez. Lorsque vous voyez **Coffres Recovery Services**, cliquez dessus.

    ![Ouvrir le coffre Recovery Services](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    La liste des coffres de l’abonnement s’affiche.

    ![Liste des coffres Recovery Services](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)

2. Dans la liste, sélectionnez le coffre associé à la machine virtuelle que vous avez restaurée. Lorsque vous cliquez sur le coffre, son tableau de bord s’affiche.

    ![Liste des coffres Recovery Services](./media/backup-azure-arm-restore-vms/select-vault-open-vault-jobs.png)

3. Sur la vignette **Travaux de sauvegarde** du tableau de bord du coffre, cliquez sur **Azure Virtual Machines** pour afficher les tâches associées au coffre.

    ![tableau de bord du coffre](./media/backup-azure-arm-restore-vms/vault-dashboard-jobs.png)

    Le panneau **Travaux de sauvegarde** s’ouvre et affiche la liste des travaux.

    ![liste des machines virtuelles du coffre](./media/backup-azure-arm-restore-vms/restore-job-in-progress.png)




## Restauration de machines virtuelles avec des configurations de réseau spéciales
Il est possible de sauvegarder et de restaurer des machines virtuelles avec les configurations de réseau spéciales. Toutefois, ces configurations requièrent une attention particulière au cours du processus de restauration.

- Machines virtuelles sous un équilibreur de charge (interne et externe)
- Machines virtuelles avec plusieurs adresses IP réservées
- Machines virtuelles avec plusieurs cartes d'interface réseau

>[AZURE.IMPORTANT] Lors de la création de la configuration réseau spéciale pour les machines virtuelles, vous devez utiliser PowerShell pour effectuer une restauration à partir du disque.


Pour pouvoir recréer entièrement les machines virtuelles après les avoir restaurées sur le disque, procédez comme suit :

1. Restaurez les disques à partir d’un coffre Recovery services à l’aide d’[Azure Backup PowerShell](../backup-azure-vms-automation.md#restore-an-azure-vm)

2. Créez la configuration de machine virtuelle requise pour l’équilibreur de charge/plusieurs cartes réseau/plusieurs adresses IP réservées à l’aide des applets de commande PowerShell et utilisez-la pour créer la machine virtuelle avec la configuration souhaitée.
	- Créer une machine virtuelle dans le service cloud avec un [équilibreur de charge interne ](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)
	- Créer une machine virtuelle pour vous connecter à l'[équilibreur de charge accessible sur Internet](https://azure.microsoft.com/fr-FR/documentation/articles/load-balancer-internet-getstarted/)
	- Créer une machine virtuelle avec [plusieurs cartes d’interface réseau](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/)
	- Créer des machines virtuelles avec [plusieurs adresses IP réservées](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/)

## Étapes suivantes
Maintenant que vous pouvez restaurer vos machines virtuelles, consultez l’article de résolution des problèmes pour en savoir plus sur les erreurs courantes relatives aux machines virtuelles. En outre, consultez l’article sur la gestion des tâches avec vos machines virtuelles.

- [Résolution des erreurs](backup-azure-vms-troubleshoot.md#restore)
- [Gestion des machines virtuelles](backup-azure-manage-vms.md)

<!---HONumber=AcomDC_0420_2016-->
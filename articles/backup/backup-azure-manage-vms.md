
<properties
	pageTitle="Gestion des sauvegardes de machines virtuelles déployées via Resource Manager | Microsoft Azure"
	description="Découvrez comment gérer et surveiller les sauvegardes d’une machine virtuelle déployée via Resource Manager"
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2016"
	ms.author="jimpark; markgal; trinadhk"/>

# Gestion des sauvegardes de machines virtuelles Azure

> [AZURE.SELECTOR]
- [Gestion des sauvegardes de machines virtuelles Azure](backup-azure-manage-vms.md)
- [Gestion des sauvegardes de machines virtuelles classiques](backup-azure-manage-vms-classic.md)

Cet article fournit des conseils sur la gestion des sauvegardes de machines virtuelles et décrit les données d’alertes de sauvegarde disponibles dans le tableau de bord du portail. Les instructions contenues dans cet article s’appliquent à l’utilisation de machines virtuelles avec des coffres Recovery Services. Cet article ne couvre pas la création des machines virtuelles et n’explique pas comment protéger les machines virtuelles. Pour une introduction à la protection des machines virtuelles déployées via Azure Resource Manager dans Azure à l’aide d’un coffre Recovery Services, consultez la page [Premier aperçu : sauvegarder les machines virtuelles ARM dans un archivage de Recovery Services](backup-azure-vms-first-look-arm.md).

## Gérer les coffres et les machines virtuelles protégées

Dans le portail Azure, le tableau de bord Coffre Recovery Services permet d’accéder à des informations concernant le coffre, notamment :

- l’instantané de sauvegarde le plus récent, c’est-à-dire le dernier point de restauration <br>
- la stratégie de sauvegarde <br>
- la taille totale de tous les instantanés de sauvegarde <br>
- le nombre de machines virtuelles protégées par le coffre <br>

L’ouverture du coffre dans le tableau de bord permet d’initier de nombreuses tâches de gestion associées à une sauvegarde de machine virtuelle. Dans la mesure où les coffres peuvent être utilisés pour protéger plusieurs éléments (ou plusieurs machines virtuelles), vous devez cependant ouvrir le tableau de bord de l’élément du coffre pour afficher les détails d’une machine virtuelle spécifique. La procédure suivante vous montre comment ouvrir le *tableau de bord du coffre* puis le *tableau de bord de l’élément du coffre*. Pour les deux procédures, nous vous proposons des « astuces » pour vous aider à ajouter le coffre et l’élément du coffre au tableau de bord Azure à l’aide de la commande Épingler au tableau de bord. La commande Épingler au tableau de bord permet de créer un raccourci vers le coffre ou l’élément. Vous pouvez également utiliser le raccourci pour exécuter des commandes courantes.

>[AZURE.TIP] Si vous avez ouvert plusieurs tableaux de bord et panneaux, utilisez le curseur bleu foncé situé en bas de la fenêtre pour faire défiler le tableau de bord Azure.

![Vue complète avec curseur](./media/backup-azure-manage-vms/bottom-slider.png)

### Ouvrez un coffre Recovery Services dans le tableau de bord :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Dans le menu Hub, cliquez sur **Parcourir** et, dans la liste des ressources, tapez **Recovery Services**. Au fur et à mesure de la saisie, la liste est filtrée. Cliquez sur **Coffre Recovery Services**.

    ![Créer un archivage de Recovery Services - Étape 1](./media/backup-azure-manage-vms/browse-to-rs-vaults.png) <br/>

    La liste des archivages de Recovery Services est affichée.

    ![Liste des coffres Recovery Services](./media/backup-azure-manage-vms/list-o-vaults.png) <br/>

    >[AZURE.TIP] Si vous épinglez un coffre au tableau de bord Azure, ce coffre est immédiatement accessible à l’ouverture du portail Azure. Pour épingler un coffre au tableau de bord, accédez à la liste des coffres, cliquez avec le bouton droit sur le coffre, puis sélectionnez **Épingler au tableau de bord**.

3. Dans la liste des coffres, sélectionnez le coffre pour ouvrir le tableau de bord correspondant. Lorsque vous sélectionnez le coffre, vous accédez à son tableau de bord et au panneau **Paramètres**. Dans la capture d’écran ci-dessous, le tableau de bord **Contoso-vault** est mis en surbrillance.

    ![Ouvrir le tableau de bord du coffre et le panneau Paramètres](./media/backup-azure-manage-vms/full-view-rs-vault.png)

### Ouvrir le tableau de bord d’un élément du coffre

Dans la procédure précédente, vous avez ouvert le tableau de bord du coffre. Pour ouvrir maintenant le tableau de bord d’un élément du coffre, procédez comme suit :

1. Dans la mosaïque **Éléments de sauvegarde** du tableau de bord du coffre, cliquez sur **Machines virtuelles Azure**.

    ![Ouvrir la mosaïque Éléments de sauvegarde](./media/backup-azure-manage-vms/contoso-vault-1606.png)

    Le panneau **Éléments de sauvegarde** affiche la dernière tâche de sauvegarde pour chaque élément. Dans cet exemple, une machine virtuelle nommée demovm-markgal est protégée par ce coffre.

    ![Mosaïque Éléments de sauvegarde](./media/backup-azure-manage-vms/backup-items-blade.png)

    >[AZURE.TIP] Pour y accéder plus facilement, vous pouvez épingler un élément du coffre au tableau de bord Azure. Pour épingler un élément du coffre, accédez à la liste des éléments du coffre, cliquez avec le bouton droit sur l’élément, puis sélectionnez **Épingler au tableau de bord**.

2. Dans le panneau **Éléments de sauvegarde**, cliquez sur l’élément pour ouvrir le tableau de bord correspondant.

    ![Mosaïque Éléments de sauvegarde](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

    Le tableau de bord de l’élément du coffre s’ouvre sur le panneau **Paramètres**.

    ![Tableau de bord Éléments de sauvegarde avec panneau Paramètres](./media/backup-azure-manage-vms/item-dashboard-settings.png)

    Le tableau de bord de l’élément du coffre vous permet d’accomplir plusieurs tâches de gestion essentielles, par exemple :

    - modifier des stratégies ou créer une stratégie de sauvegarde <br>
	- afficher les points de restauration et vérifier leur état de cohérence <br>
	- sauvegarde à la demande d’une machine virtuelle <br>
	- suspendre la protection des machines virtuelles <br>
	- reprendre la protection d’une machine virtuelle <br>
	- supprimer des données de sauvegarde (ou un point de récupération) <br>
	- [restaurer une sauvegarde (ou un point de récupération)](./backup-azure-arm-restore-vms.md#restore-a-recovery-point) <br>

Pour les procédures suivantes, nous allons travailler à partir du tableau de bord de l’élément du coffre.

## Gestion des stratégies de sauvegarde

1. Dans le [tableau de bord de l’élément du coffre](backup-azure-manage-vms.md#open-a-vault-item-dashboard), cliquez sur **Tous les paramètres** pour ouvrir le panneau **Paramètres**.

    ![Panneau Stratégie de sauvegarde](./media/backup-azure-manage-vms/all-settings-button.png)

2. Dans le panneau **Paramètres**, cliquez sur **Stratégie de sauvegarde** pour ouvrir le panneau correspondant.

    Ce panneau affiche les détails de la plage de rétention et de la fréquence de sauvegarde.

    ![Panneau Stratégie de sauvegarde](./media/backup-azure-manage-vms/backup-policy-blade.png)

3. Dans le menu **Choisir une stratégie de sauvegarde** :
    - Pour modifier les stratégies, sélectionnez une autre stratégie, puis cliquez sur **Enregistrer**. La nouvelle stratégie est appliquée immédiatement au coffre. <br>
    - Pour créer une stratégie, sélectionnez **Créer**.

    ![Sauvegarde de machine virtuelle](./media/backup-azure-manage-vms/backup-policy-create-new.png)

    Pour savoir comment créer une stratégie de sauvegarde, consultez la section [Définition d’une stratégie de sauvegarde](backup-azure-manage-vms.md#defining-a-backup-policy).

[AZURE.INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]


## Sauvegarde à la demande d’une machine virtuelle
Vous pouvez effectuer une sauvegarde à la demande d’une machine virtuelle une fois que celle-ci est configurée pour la protection. Si la sauvegarde initiale est en attente, la sauvegarde à la demande crée une copie complète de la machine virtuelle dans le coffre Recovery Services. Si la sauvegarde initiale est terminée, une sauvegarde à la demande enverra au coffre Recovery Services uniquement les modifications par rapport à l’instantané précédent. Autrement dit, les sauvegardes suivantes sont toujours incrémentielles.

>[AZURE.NOTE] La plage de rétention pour une sauvegarde à la demande correspond à la valeur de rétention spécifiée pour le point de sauvegarde quotidien de la stratégie. Si aucun point de sauvegarde quotidien n’est sélectionné, le point de sauvegarde hebdomadaire est utilisé.

Pour déclencher une sauvegarde à la demande d’une machine virtuelle :

- Sur le [tableau de bord de l’élément du coffre](backup-azure-manage-vms.md#open-a-vault-item-dashboard), cliquez sur **Sauvegarder maintenant**.

    ![Bouton Sauvegarder maintenant](./media/backup-azure-manage-vms/backup-now-button.png)

    Le portail vous demande de confirmer que vous souhaitez démarrer un travail de sauvegarde à la demande. Cliquez sur **Oui** pour démarrer le travail de sauvegarde.

    ![Bouton Sauvegarder maintenant](./media/backup-azure-manage-vms/backup-now-check.png)

    Le travail de sauvegarde crée un point de récupération. La plage de rétention du point de récupération est identique à celle spécifiée dans la stratégie associée à la machine virtuelle. Pour suivre la progression du travail, dans le tableau de bord du coffre, cliquez sur la mosaïque **Travaux de sauvegarde**.


## Arrêt de la protection des machines virtuelles
Si vous décidez d’arrêter la protection d’une machine virtuelle, vous devrez indiquer si vous souhaitez conserver les points de récupération. Il existe deux façons de suspendre la protection des machines virtuelles :
- arrêter tous les travaux de sauvegarde à venir et supprimer tous les points de récupération, ou
- arrêter tous les travaux de sauvegarde à venir en conservant les points de récupération <br/>

La conservation des points de récupération dans le stockage présente un coût, mais elle a l’avantage de vous permettre de restaurer ultérieurement la machine virtuelle, si vous le souhaitez. Pour plus d’informations sur les coûts de conservation des points de récupération, consultez la [tarification](https://azure.microsoft.com/pricing/details/backup/). Si vous choisissez de supprimer tous les points de récupération, vous ne pourrez pas restaurer la machine virtuelle.

Pour arrêter la protection d’une machine virtuelle :

1. Sur le [tableau de bord de l’élément du coffre](backup-azure-manage-vms.md#open-a-vault-item-dashboard), cliquez sur **Arrêter la sauvegarde**.

    ![Bouton Arrêter la sauvegarde](./media/backup-azure-manage-vms/stop-backup-button.png)

    Le panneau Arrêter la sauvegarde s’ouvre.

    ![Panneau Arrêter la sauvegarde](./media/backup-azure-manage-vms/stop-backup-blade.png)

2. Dans le panneau **Arrêter la sauvegarde**, indiquez si vous souhaitez conserver ou supprimer les données de sauvegarde. La zone d’informations fournit des détails pour vous guider dans votre choix.

    ![Arrêter la protection](./media/backup-azure-manage-vms/retain-or-delete-option.png)

3. Si vous avez choisi de conserver les données de sauvegarde, passez à l’étape 4. Si vous avez choisi de supprimer les données de sauvegarde, confirmez que vous souhaitez bien arrêter les travaux de sauvegarde et supprimer les points de récupération. Saisissez le nom de l’élément.

    ![Arrêter la vérification](./media/backup-azure-manage-vms/item-verification-box.png)

    Si vous n’êtes pas sûr du nom de l’élément, survolez le point d’exclamation pour en afficher le nom. Le nom de l’élément figure également sous **Arrêter la sauvegarde** en haut du panneau.

4. Vous pouvez, si vous le souhaitez, indiquer une **Raison** ou un **Commentaire**.

5. Pour arrêter le travail de sauvegarde pour l’élément actif, cliquez sur ![Bouton Arrêter la sauvegarde](./media/backup-azure-manage-vms/stop-backup-button-blue.png)

    Un message de notification vous informe que les travaux de sauvegarde ont été interrompus.

    ![Confirmation l’arrêt de la protection](./media/backup-azure-manage-vms/stop-message.png)


## Reprendre la protection d’une machine virtuelle
Si vous avez choisi l’option **Conserver les données de sauvegarde** au moment de l’arrêt de la protection de la machine virtuelle, vous avez la possibilité de restaurer la protection. Si vous avez choisi l’option **Supprimer les données de sauvegarde**, vous ne pourrez pas restaurer la protection de la machine virtuelle.

Pour reprendre la protection de la machine virtuelle

1. Sur le [tableau de bord de l’élément du coffre](backup-azure-manage-vms.md#open-a-vault-item-dashboard), cliquez sur **Reprendre la sauvegarde**.

    ![Reprendre la protection](./media/backup-azure-manage-vms/resume-backup-button.png)

    Le panneau Stratégie de sauvegarde s’ouvre.

    >[AZURE.NOTE] Lors de l’application d’une nouvelle protection à la machine virtuelle, vous pouvez choisir une autre stratégie que la stratégie avec laquelle la machine virtuelle a été initialement protégée.

2. Suivez les étapes de la section [Modifier des stratégies ou créer une stratégie de sauvegarde](backup-azure-manage-vms.md#change-policies-or-create-a-new-backup-policy) pour attribuer la stratégie de la machine virtuelle.

    Une fois la stratégie de sauvegarde appliquée à la machine virtuelle, le message suivant s’affiche.

    ![Machine virtuelle protégée](./media/backup-azure-manage-vms/success-message.png)

## Suppression des données de sauvegarde
Vous pouvez supprimer les données de sauvegarde associées à une machine virtuelle au cours du travail **Arrêter la sauvegarde**, ou à tout moment après la fin du travail de sauvegarde. Il peut même être préférable d’attendre plusieurs jours ou semaines avant de supprimer les points de récupération. Contrairement à la restauration des points de récupération, lors de la suppression des données de sauvegarde, vous ne pouvez pas choisir des points de récupération spécifiques à supprimer. Si vous choisissez de supprimer vos données de sauvegarde, vous supprimerez tous les points de récupération associés à l’élément.

La procédure suivante suppose que le travail de sauvegarde de la machine virtuelle a été arrêté ou désactivé. Les options **Reprendre la sauvegarde** et **Supprimer la sauvegarde** sont accessibles dans le tableau de bord de l’élément du coffre seulement lorsque l’opération de sauvegarde a été désactivée.

![Boutons Reprendre et Supprimer](./media/backup-azure-manage-vms/resume-delete-buttons.png)

Pour supprimer les données de sauvegarde d’une machine virtuelle avec l’option *Sauvegarde désactivée* :

1. Sur le [tableau de bord de l’élément du coffre](backup-azure-manage-vms.md#open-a-vault-item-dashboard), cliquez sur **Supprimer la sauvegarde**.

    ![Type de machine virtuelle](./media/backup-azure-manage-vms/delete-backup-buttom.png)

    Le panneau **Supprimer les données de sauvegarde** s’ouvre.

    ![Type de machine virtuelle](./media/backup-azure-manage-vms/delete-backup-blade.png)

2. Saisissez le nom de l’élément pour confirmer la suppression des points de récupération.

    ![Arrêter la vérification](./media/backup-azure-manage-vms/item-verification-box.png)

    Si vous n’êtes pas sûr du nom de l’élément, survolez le point d’exclamation pour en afficher le nom. Le nom de l’élément figure également sous **Supprimer les données de sauvegarde** en haut du panneau.

3. Vous pouvez, si vous le souhaitez, indiquer une **Raison** ou un **Commentaire**.

4. Pour supprimer les données de sauvegarde pour l’élément actif, cliquez sur ![Bouton Arrêter la sauvegarde](./media/backup-azure-manage-vms/delete-button.png)

    Un message de notification vous informe que les données de sauvegarde ont été supprimées.


## Étapes suivantes

Pour plus d’informations sur la manière de recréer une machine virtuelle à partir d’un point de récupération, consultez [Restauration de machines virtuelles Azure](backup-azure-restore-vms.md). Pour plus d’informations sur la protection de vos machines virtuelles, consultez [Premier aperçu : sauvegarder les machines virtuelles ARM dans un archivage de Recovery Services](backup-azure-vms-first-look-arm.md). Pour plus d’informations sur la surveillance des événements, consultez [Monitor alerts for Azure virtual machine backups](backup-azure-monitor-vms.md) (Surveiller les alertes des sauvegardes de machines virtuelles Azure).

<!---HONumber=AcomDC_0817_2016-->
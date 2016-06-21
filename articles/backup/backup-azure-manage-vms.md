
<properties
	pageTitle="Gestion et surveillance des sauvegardes de machines virtuelles déployées via Resource Manager | Microsoft Azure"
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
	ms.date="06/03/2016"
	ms.author="jimpark; markgal;"/>

# Gestion et surveillance des sauvegardes de machines virtuelles Azure

> [AZURE.SELECTOR]
- [Gestion des sauvegardes de machines virtuelles Azure](backup-azure-manage-vms.md)
- [Gestion des sauvegardes de machines virtuelles classiques](backup-azure-manage-vms-classic.md)

Cet article fournit des conseils sur la gestion des sauvegardes de vos machines virtuelles et clarifie les informations de sauvegarde disponibles dans le tableau de bord du portail. Les instructions contenues dans cet article s’appliquent à l’utilisation de machines virtuelles avec des coffres Recovery Services. Cet article ne couvre pas la création des machines virtuelles et n’explique pas comment protéger les machines virtuelles. Pour une introduction à la protection des machines virtuelles déployées via Azure Resource Manager dans Azure à l’aide d’un coffre Recovery Services, consultez la page [Premier aperçu : sauvegarder les machines virtuelles ARM dans un archivage de Recovery Services](backup-azure-vms-first-look-arm.md).

## Accéder aux coffres et aux machines virtuelles protégées

Dans le portail Azure, le tableau de bord Coffre Recovery Services permet d’accéder à des informations concernant le coffre, notamment :

- l’instantané de sauvegarde le plus récent, c’est-à-dire le dernier point de restauration <br>
- la stratégie de sauvegarde <br>
- la taille totale de tous les instantanés de sauvegarde <br>
- le nombre de machines virtuelles protégées par le coffre <br>

L’ouverture du coffre dans le tableau de bord permet d’initier de nombreuses tâches de gestion associées à une sauvegarde de machine virtuelle. Dans la mesure où les coffres peuvent être utilisés pour protéger plusieurs éléments (ou plusieurs machines virtuelles), vous devez cependant ouvrir le tableau de bord de l’élément du coffre pour afficher les détails d’une machine virtuelle spécifique. La procédure suivante vous montre comment ouvrir le *tableau de bord du coffre* puis le *tableau de bord de l’élément du coffre*. Pour les deux procédures, nous vous proposons des « astuces » pour vous aider à ajouter le coffre et l’élément du coffre au tableau de bord Azure à l’aide de la commande Épingler au tableau de bord. La commande Épingler au tableau de bord permet de créer un raccourci vers le coffre ou l’élément. Vous pouvez également utiliser le raccourci pour exécuter des commandes courantes.

>[AZURE.TIP] Si vous avez ouvert plusieurs tableaux de bord et panneaux, vous pouvez utiliser le curseur bleu foncé situé en bas de la fenêtre pour faire défiler la vue dans le tableau de bord Azure.

![Vue complète avec curseur](./media/backup-azure-manage-vms/bottom-slider.png)

### Ouvrez un coffre Recovery Services dans le tableau de bord :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Dans le menu Hub, cliquez sur **Parcourir** et, dans la liste des ressources, tapez **Recovery Services**. Au fur et à mesure des caractères saisis, la liste est filtrée. Cliquez sur **Coffre Recovery Services**.

    ![Créer un archivage de Recovery Services - Étape 1](./media/backup-azure-manage-vms/browse-to-rs-vaults.png) <br/>

    La liste des archivages de Recovery Services est affichée.

    ![Liste des coffres Recovery Services](./media/backup-azure-manage-vms/list-o-vaults.png) <br/>

    >[AZURE.TIP] Si vous épinglez un coffre au tableau de bord Azure, ce coffre est immédiatement accessible à l’ouverture du portail Azure. Pour épingler un coffre au tableau de bord, accédez à la liste des coffres, cliquez avec le bouton droit sur le coffre, puis sélectionnez **Épingler au tableau de bord**.

3. Dans la liste des coffres, sélectionnez le coffre pour ouvrir le tableau de bord correspondant. Lorsque vous sélectionnez le coffre, vous accédez à son tableau de bord et au panneau **Paramètres**. Dans la capture d’écran ci-dessous, le tableau de bord **Contoso-vault** est mis en surbrillance.

    ![Ouvrir le tableau de bord du coffre et le panneau Paramètres](./media/backup-azure-manage-vms/full-view-rs-vault.png)

### Ouvrir le tableau de bord d’un élément du coffre

Dans la procédure précédente, vous avez ouvert le tableau de bord du coffre. Pour ouvrir maintenant le tableau de bord d’un élément du coffre, procédez comme suit :

1. Dans la mosaïque **Éléments de sauvegarde** du tableau de bord du coffre, cliquez sur **Machines virtuelles Azure**.

    ![Ouvrir la mosaïque Éléments de sauvegarde](./media/backup-azure-manage-vms/contoso-vault.png)

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
	- effectuer une sauvegarde à la demande d’une machine virtuelle <br>
	- suspendre la protection des machines virtuelles <br>
	- reprendre la protection d’une machine virtuelle <br>
	- supprimer des données de sauvegarde (ou un point de récupération) <br>
	- [restaurer une sauvegarde (ou un point de récupération)](./backup-azure-arm-restore-vms.md#restore-a-recovery-point) <br>

Pour les procédures suivantes, nous allons travailler à partir du tableau de bord de l’élément du coffre.

## Modifier des stratégies ou créer une stratégie de sauvegarde

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


## Sauvegarde à la demande d’une machine virtuelle
Vous pouvez effectuer une sauvegarde à la demande d’une machine virtuelle une fois que celle-ci est configurée pour la protection. Si la sauvegarde initiale est en attente pour la machine virtuelle, la sauvegarde à la demande crée une copie complète de la machine virtuelle dans le coffre Recovery Services. Si la sauvegarde initiale est terminée, une sauvegarde à la demande enverra au coffre Recovery Services uniquement les modifications par rapport à l’instantané précédent, ce qui signifie qu’elle est toujours incrémentielle.

>[AZURE.NOTE] La plage de rétention pour une sauvegarde à la demande correspond à la valeur de rétention spécifiée pour le point de sauvegarde quotidien de la stratégie. Si aucun point de sauvegarde quotidien n’est sélectionné, le point de sauvegarde hebdomadaire est utilisé.

Pour déclencher une sauvegarde à la demande d’une machine virtuelle :

1. Sur le [tableau de bord de l’élément du coffre](backup-azure-manage-vms.md#open-a-vault-item-dashboard), cliquez sur **Sauvegarder maintenant**.

    ![Bouton Sauvegarder maintenant](./media/backup-azure-manage-vms/backup-now-button.png)

    Le portail vous demande de confirmer que vous souhaitez démarrer un travail de sauvegarde à la demande. Cliquez sur **Oui** pour démarrer le travail de sauvegarde.

    ![Bouton Sauvegarder maintenant](./media/backup-azure-manage-vms/backup-now-check.png)

    Le travail de sauvegarde crée un nouveau point de récupération. La plage de rétention du point de récupération créé est identique à celle spécifiée dans la stratégie associée à la machine virtuelle. Pour suivre la progression du travail, dans le tableau de bord du coffre, cliquez sur la mosaïque **Travaux de sauvegarde**.


## Arrêt de la protection des machines virtuelles
Si vous décidez d’arrêter la protection d’une machine virtuelle, vous devrez indiquer si vous souhaitez conserver les points de récupération. Il existe deux façons d’arrêter la protection des machines virtuelles : arrêter tous les travaux de sauvegarde à venir et supprimer tous les points de récupération, ou arrêter tous les travaux de sauvegarde à venir en conservant les points de récupération. La conservation des points de récupération dans le stockage présente un coût, mais elle a l’avantage de vous permettre de restaurer ultérieurement la machine virtuelle, si vous le souhaitez. Pour connaître les détails de la tarification de ces machines virtuelles, cliquez [ici](https://azure.microsoft.com/pricing/details/backup/). Si vous choisissez de supprimer tous les points de récupération, vous ne pourrez pas restaurer la machine virtuelle.

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

    Une fois la stratégie de sauvegarde appliquée à la machine virtuelle, vous verrez le message suivant.

    ![Machine virtuelle protégée](./media/backup-azure-manage-vms/success-message.png)

## Suppression des données de sauvegarde
Vous pouvez supprimer les données de sauvegarde associées à une machine virtuelle au cours du travail **Arrêter la sauvegarde**, ou à tout moment après l’arrêt des sauvegardes. Vous avez la possibilité d’autoriser l’arrêt du travail de sauvegarde d’une machine virtuelle et d’attendre quelques jours ou semaines avant de décider de supprimer ou non les points de récupération. Contrairement à la restauration des points de récupération, lors de la suppression des données de sauvegarde, vous ne pouvez pas choisir des points de récupération spécifiques à supprimer. Si vous choisissez de les supprimer, vous supprimerez tous les points de récupération associés à l’élément.

La procédure suivante suppose que le travail de sauvegarde de la machine virtuelle a été arrêté ou désactivé. Les options **Reprendre la sauvegarde** et **Supprimer la sauvegarde** sont accessibles dans le tableau de bord de l’élément du coffre seulement lorsque l’opération de sauvegarde a été désactivée.

![Boutons Reprendre et Supprimer](./media/backup-azure-manage-vms/resume-delete-buttons.png)

Pour supprimer les données de sauvegarde d’une machine virtuelle avec l’option *Sauvegarde désactivé* :

1. Sur le [tableau de bord de l’élément du coffre](backup-azure-manage-vms.md#open-a-vault-item-dashboard), cliquez sur **Supprimer la sauvegarde**.

    ![Type de machine virtuelle](./media/backup-azure-manage-vms/delete-backup-buttom.png)

    Le panneau **Supprimer les données de sauvegarde** s’ouvre.

    ![Type de machine virtuelle](./media/backup-azure-manage-vms/delete-backup-blade.png)

2. Vous devez saisir le nom de l’élément pour confirmer la suppression des points de récupération.

    ![Arrêter la vérification](./media/backup-azure-manage-vms/item-verification-box.png)

    Si vous n’êtes pas sûr du nom de l’élément, survolez le point d’exclamation pour en afficher le nom. Le nom de l’élément figure également sous **Supprimer les données de sauvegarde** en haut du panneau.

3. Vous pouvez, si vous le souhaitez, indiquer une **Raison** ou un **Commentaire**.

4. Pour supprimer les données de sauvegarde pour l’élément actif, cliquez sur ![Bouton Arrêter la sauvegarde](./media/backup-azure-manage-vms/delete-button.png)

    Un message de notification vous informe que les données de sauvegarde ont été supprimées.

## Audit des opérations
Vous pouvez vérifier les journaux d’opérations et d’événements pour afficher les opérations de gestion effectuées sur le coffre Recovery Services. Les journaux d’opérations activent l’assistance post mortem et d’audit des opérations de sauvegarde. Vous pouvez utiliser la fonctionnalité Journaux d’audit pour afficher les journaux de toutes les opérations *de l’abonnement*. Pour plus d’informations sur les journaux d’événements, d’opérations et d’audit, consultez l’article [Affichage des événements et des journaux d’audit](../azure-portal/insights-debugging-with-events.md). Utilisez le paramètre **Journaux d’audit** pour afficher les journaux d’événements et d’opérations propres à un coffre ou à un élément du coffre Recovery Services.

Les opérations suivantes sont consignées dans les journaux d’audit :

- S’inscrire
- Annuler l’inscription
- Configurer la protection
- Sauvegarde (sauvegarde planifiée et à la demande)
- Restauration
- Arrêter la protection
- Supprimer les données de sauvegarde
- Add policy
- Supprimer la stratégie
- Mettre à jour la stratégie
- Annuler le travail

Pour afficher les journaux des événements pour un coffre Recovery Services :

1. Dans le [tableau de bord du coffre](backup-azure-manage-vms.md#open-a-recovery-services-vault-in-the-dashboard), recherchez **Journaux d’audit** et cliquez dessus pour ouvrir le panneau **Événements**.

    ![Journaux d’audit](./media/backup-azure-manage-vms/audit-logs.png)

    Le panneau Événements s’ouvre sur les événements opérationnels filtrés pour le coffre actif. Le panneau affiche la liste des événements critiques, erreurs, avertissements et informations qui se sont produits au cours de la semaine passée. L’intervalle de temps est une valeur par défaut définie dans le paramètres **Filtre**. Le panneau **Événements** affiche également un graphique à barres qui retrace le déroulement des événements. Si vous ne souhaitez pas voir ce graphique, accédez au menu **Événements** et cliquez sur **Afficher le graphique** pour désactiver le graphique.

    ![Filtre des journaux d’audit](./media/backup-azure-manage-vms/audit-logs-filter.png)

2. Pour plus d’informations sur une opération, cliquez sur l’opération de votre choix dans la liste des opérations pour ouvrir le panneau correspondant. Le panneau contient des informations détaillées sur l’opération et répertorie les événements qui se sont produits au cours de l’intervalle spécifié.

    ![Détails de l'opération](./media/backup-azure-manage-vms/audit-logs-details-window.png)

3. Pour afficher des informations détaillées sur un événement particulier, cliquez sur l’événement de votre choix dans la liste des événements pour ouvrir le panneau Détails correspondant.

    ![Détails de l’événement](./media/backup-azure-manage-vms/audit-logs-details-window-deep.png)

    Les informations au niveau de l’événement sont répertoriées au fur et à mesure de leur disponibilité. Le reste de cette procédure explique comment modifier les informations disponibles.

4. Pour modifier la liste des filtres disponibles, dans le menu **Événements**, cliquez sur **Filtre** pour ouvrir le panneau correspondant.

    ![ouvrir le panneau Filtre](./media/backup-azure-manage-vms/audi-logs-filter-button.png)

5. Dans le panneau **Filtre**, ajustez les filtres **Niveau**, **Intervalle de temps** et **Appelant**. Les autres filtres ne sont pas disponibles dans la mesure où ils ont été définis pour fournir des informations actuelles concernant le coffre Recovery Services.

    ![Détails de la requête de journaux d’audit](./media/backup-azure-manage-vms/filter-blade.png)

    Vous pouvez spécifier le **niveau** de l’événement : critique, erreur, avertissement ou information. Vous pouvez combiner plusieurs niveaux d’événements, mais vous devez sélectionner au moins un niveau. Activez ou désactivez le niveau. Le filtre **Intervalle de temps** vous permet de spécifier la durée de collecte des événements. Si vous utilisez un intervalle de temps personnalisé, vous pouvez définir les heures de début et de fin.

6. Une fois que vous êtes prêt à interroger les journaux d’opérations à l’aide de votre filtre, cliquez sur **Mettre à jour**. Les résultats s’affichent dans le panneau **Événements**.

    ![Détails de l'opération](./media/backup-azure-manage-vms/edited-list-of-events.png)


## Notifications d’alerte
Vous pouvez obtenir des notifications d’alerte personnalisées pour les travaux du portail. Pour cela, définissez des règles d’alerte basées sur PowerShell sur les événements de journaux des opérations. Utilisez *PowerShell version 1.3.0 ou ultérieure*.

Pour définir une notification personnalisée et signaler les échecs de sauvegarde, utilisez une commande du type :

```
PS C:\> $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail contoso@microsoft.com
PS C:\> Add-AzureRmLogAlertRule -Name backupFailedAlert -Location "East US" -ResourceGroup RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US -OperationName Microsoft.Backup/RecoveryServicesVault/Backup -Status Failed -TargetResourceId /subscriptions/86eeac34-eth9a-4de3-84db-7a27d121967e/resourceGroups/RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US/providers/microsoft.backupbvtd2/RecoveryServicesVault/trinadhVault -Actions $actionEmail
```

**ResourceId** : vous pouvez obtenir ces informations à partir des journaux d’audit. L’élément ResourceId est fourni dans la colonne Ressource des journaux d’opérations.

**OperationName** : cette valeur utilise le format « Microsoft.RecoveryServices/recoveryServicesVault/<EventName> », où EventName peut être Register, Unregister, ConfigureProtection, Backup, Restore, StopProtection, DeleteBackupData, CreateProtectionPolicy, DeleteProtectionPolicy ou UpdateProtectionPolicy

**État** : les valeurs prises en charge sont Démarré, Réussi et Échec.

**ResourceGroup** : groupe de ressources auquel appartient la ressource. Vous pouvez ajouter la colonne Groupe de ressources aux journaux générés. Le groupe de ressources représente l’un des types d’informations disponibles sur les événements.

**Nom** : nom de la règle d’alerte.

**CustomEmail** : spécifiez l’adresse de messagerie personnalisée à laquelle vous voulez envoyer des notifications d’alerte.

**SendToServiceOwners** : cette option envoie des notifications d’alerte à tous les administrateurs et coadministrateurs de l’abonnement. Elle peut être utilisée dans l’applet de commande **New-AzureRmAlertRuleEmail**.

### Limitations sur les alertes
Les alertes basées sur des événements sont soumises aux limitations suivantes :

1. Des alertes sont déclenchées sur toutes les machines virtuelles du coffre Recovery Services. Vous ne pouvez pas personnaliser l’alerte pour un ensemble spécifique de machines virtuelles à l’intérieur d’un coffre Recovery Services.
2. Cette fonctionnalité est en version préliminaire. [En savoir plus](../azure-portal/insights-powershell-samples.md#create-alert-rules)
3. Les alertes sont envoyées à partir de l’adresse « alerts-noreply@mail.windowsazure.com ». Actuellement, vous ne pouvez pas modifier l’expéditeur de courrier électronique.

[AZURE.INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

## Étapes suivantes

Pour plus d’informations sur la manière de recréer une machine virtuelle à partir d’un point de récupération, consultez [Restauration de machines virtuelles dans Azure](backup-azure-restore-vms.md). Pour plus d’informations sur la protection de vos machines virtuelles, consultez [Premier aperçu : sauvegarder les machines virtuelles ARM dans un archivage de Recovery Services](backup-azure-vms-first-look-arm.md).

<!---HONumber=AcomDC_0608_2016-->
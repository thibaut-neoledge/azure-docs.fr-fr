<properties
	pageTitle="Récupération de données à partir d'un autre serveur DPM dans le coffre de sauvegarde | Microsoft Azure"
	description="Récupérez les données que vous avez protégées dans un coffre Azure Backup à partir de n'importe quel serveur DPM inscrit auprès de ce coffre."
	services="backup"
	documentationCenter=""
	authors="giridharreddy"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/14/2015" ms.author="giridham"; "jimpark"/>

# Récupération de données à partir d'un autre serveur DPM dans le coffre de sauvegarde
Vous pouvez maintenant récupérer les données que vous avez protégées dans un coffre Azure Backup à partir de n'importe quel serveur DPM inscrit auprès de ce coffre. Ainsi, le processus permettant d’effectuer cette opération est totalement intégré à la console d'administration DPM et est semblable aux autres flux de travail de récupération.

Pour récupérer des données à partir d'un autre serveur DPM dans le coffre de sauvegarde, vous avez besoin de [System Center Data Protection Manager UR7](https://support.microsoft.com/fr-FR/kb/3065246) et du [dernier agent Azure Backup](http://aka.ms/azurebackup_agent).

## Récupération de données à partir d'un autre serveur DPM
Pour récupérer des données à partir d'un autre serveur DPM, procédez comme suit :

1. Dans l’onglet **Récupération** de la console d'administration DPM, cliquez sur **« Ajouter un DPM externe »** (dans la partie supérieure gauche de l'écran).

    ![Ajouter un serveur DPM externe](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)

2. Fournissez les **informations d'identification** associées au serveur DPM pour lequel les données sont récupérées, choisissez le **serveur DPM** dans la liste des serveurs DPM inscrits auprès du coffre de sauvegarde et saisissez la **phrase secrète de chiffrement** associée au serveur DPM pour lequel les données sont en cours de récupération.

    ![Informations d'identification d’un serveur DPM externe](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

    >[AZURE.NOTE]Seuls les serveurs DPM associés au même coffre d’inscription peuvent récupérer des données entre eux.

    Une fois le serveur DPM externe ajouté, vous pouvez parcourir les données du serveur DPM externe et du serveur DPM local à partir de l’onglet **Récupération**.

3. Parcourez la liste des serveurs de production protégés par le serveur DPM externe et sélectionnez la source de données appropriée.

    ![Parcourir un serveur DPM externe](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)

4. Sélectionnez **le mois et l'année** à partir de la liste déroulante **Points de récupération**. Sélectionnez la **date de récupération** souhaitée correspondant au moment de la création du point de récupération, puis sélectionnez l’**Heure de récupération**.

    Une liste de fichiers et de dossiers s'affiche dans le volet inférieur, qui peuvent être parcourus et récupérés sur n'importe quel emplacement.

    ![Points de récupération d’un serveur DPM externe](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)

5. Cliquez avec le bouton droit sur l'élément approprié puis cliquez sur **Récupérer**.

    ![Récupération d’un serveur DPM externe](./media/backup-azure-alternate-dpm-server/recover.png)

6. Vérifiez la **sélection à récupérer**. Vérifiez les données et l’heure de la copie de sauvegarde à récupérer, ainsi que la source à partir de laquelle la copie de sauvegarde a été créée. Si la sélection est incorrecte, cliquez sur **Annuler** pour revenir à l'onglet Récupération et sélectionnez le point de récupération approprié. Si la sélection est correcte, cliquez sur **Suivant**.

    ![Résumé de la récupération d’un serveur DPM externe](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)

7. Sélectionnez **Récupérer à un autre emplacement**. **Rechercher** l'emplacement correct pour la récupération.

    ![Emplacement de remplacement de la récupération d’un serveur DPM externe](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)

8. Choisissez entre les options **Créer une copie**, **Ignorer** ou **Remplacer**.
    - L’option **Créer une copie** crée une copie du fichier en cas de conflit de nom.
    - L’option **Ignorer** ignore la récupération du fichier en cas de conflit de nom.
    - L’option **Remplacer** remplace la copie existante à l'emplacement spécifié en cas de conflit de nom.

    Choisissez l'option appropriée pour **Restaurer la sécurité**. Vous pouvez appliquer les paramètres de sécurité de l'ordinateur de destination sur lequel les données sont récupérées ou les paramètres de sécurité qui étaient applicables au produit au moment de la création du point de récupération.

    Indiquez si une **Notification** doit être envoyée une fois la récupération terminée.

    ![Notifications de la récupération d’un serveur DPM externe](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)

9. L’écran **Résumé** répertorie les options sélectionnées jusqu'à présent. Lorsque vous cliquerez sur **« Récupérer »**, les données seront récupérées sur l'emplacement local approprié.

    ![Résumé des options de la récupération d’un serveur DPM externe](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

    >[AZURE.NOTE]La tâche de récupération peut être surveillée dans l’onglet **Analyse** du serveur DPM.

    ![Surveillance de la récupération](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)

10. Vous pouvez cliquer sur **Effacer le DPM externe** dans l’onglet **Récupération** du serveur DPM pour supprimer l’affichage du serveur DPM externe.

    ![Effacer un serveur DPM externe](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## Dépannage des messages d'erreur
|N° |	Message d’erreur |	Étapes de dépannage |
| :-------------: |:-------------| :-----|
|1\.|		Ce serveur n'est pas enregistré dans le coffre spécifié par les informations d'identification de coffre.|	**Raison :** cette erreur apparaît lorsque le fichier d'informations d'identification de coffre sélectionné n'appartient pas au coffre de sauvegarde associé au serveur DPM sur lequel la récupération est tentée. <br> **Résolution :** téléchargez le fichier d'informations d'identification de coffre du coffre de sauvegarde auquel le serveur DPM est inscrit.|
|2\.|		Les données récupérables ne sont pas disponibles ou le serveur sélectionné n'est pas un serveur DPM.|	**Raison :** aucun autre serveur DPM avec DPM 2012 R2 UR7 inscrit auprès du coffre de sauvegarde, ni les serveurs DPM avec DPM 2012 R2 UR7 n'ont encore téléchargé les métadonnées. Ou bien, le serveur sélectionné n'est pas un serveur DPM (également appelé serveur Windows Server ou client Windows). <br> **Résolution :** s'il existe d’autres serveurs DPM inscrits auprès du coffre de sauvegarde, assurez-vous que SCDPM 2012 R2 UR7 et le dernier agent Azure Backup sont installées. <br>S'il existe d’autres serveurs DPM inscrits auprès du coffre de sauvegarde avec DPM 2012 R2 UR7, patientez un jour après l'installation d'UR7 pour lancer le processus de récupération. La tâche nocturne téléchargera les métadonnées de toutes les sauvegardes précédemment protégées dans le cloud. Les données seront disponibles pour la récupération.|
|3\.|		Aucun autre serveur DPM n'est inscrit auprès de ce coffre.|	**Raison :** il n’y a aucun autre serveur DPM avec DPM 2012 R2 UR7 (ou une version ultérieure) inscrit auprès du coffre à partir duquel une tentative de récupération est en cours.<br>**Résolution :** s'il existe d’autres serveurs DPM inscrits auprès du coffre de sauvegarde, assurez-vous que SCDPM 2012 R2 UR7 et le dernier agent Azure Backup sont installés.<br>S'il existe d’autres serveurs DPM inscrits auprès du coffre de sauvegarde avec DPM 2012 R2 UR7, patientez un jour après l'installation d'UR7 pour lancer le processus de récupération. La tâche nocturne téléchargera les métadonnées de toutes les sauvegardes précédemment protégées dans le cloud. Les données seront disponibles pour la récupération.|
|4\.|		La phrase secrète de chiffrement fournie ne correspond pas à la phrase secrète associée au serveur suivant : **<server name>**|	**Raison :** la phrase secrète de chiffrement utilisée dans le processus de chiffrement des données à partir des données du serveur DPM en cours de récupération ne correspond pas à la phrase secrète de chiffrement fournie. L'agent ne peut pas déchiffrer les données. Par conséquent, la récupération échoue.<br>** Résolution :** veuillez fournir la même phrase secrète de chiffrement associée au serveur DPM pour lequel les données sont en cours de récupération.|

## Forum Aux Questions :
1. **Pourquoi ne puis-je pas ajouter un serveur DPM externe à partir d'un autre serveur DPM après avoir installé UR7 et le dernier agent Azure Backup ?**

    A) Pour les serveurs DPM existants avec des sources de données protégées dans le cloud (en utilisant un correctif cumulatif antérieur au correctif cumulatif 7), vous devez attendre au moins un jour après l'installation d’UR7 et du dernier agent Azure Backup avant de commencer à *ajouter un serveur DPM externe*. Ce temps d’attente est nécessaire pour télécharger les métadonnées des groupes de protection DPM sur Azure. Cela se produit la première fois par le biais d’une tâche nocturne.

2. **Quelle est la version minimale de l'agent Azure Backup nécessaire ?**

    (A) La version minimale de l'agent Azure Backup pour activer cette fonctionnalité est 2.0.8719.0. Vous pouvez connaître la version de l'agent Azure Backup en accédant au Panneau de configuration **>** Tous les Panneaux de configuration **>** Programmes et fonctionnalités **>** Microsoft Azure Recovery Services Agent. Si la version est inférieure à 2.0.8719.0, téléchargez le [dernier agent Azure Backup](https://go.microsoft.com/fwLink/?LinkID=288905) et installez-le.

    ![Effacer un serveur DPM externe](./media/backup-azure-alternate-dpm-server/external-dpm-azurebackupagentversion.png)

## Étapes suivantes :
• [Azure Backup - Forum Aux Questions](backup-azure-backup-faq.md)

<!---HONumber=Oct15_HO3-->
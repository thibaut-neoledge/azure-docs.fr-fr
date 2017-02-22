---
title: "Récupérer des données à partir d’un serveur de sauvegarde Azure | Microsoft Docs"
description: "Récupérez les données que vous avez protégées dans un coffre de sauvegarde Azure à partir de n’importe quel serveur de sauvegarde Azure inscrit auprès de ce coffre."
services: backup
documentationcenter: 
author: nkolli1
manager: shreeshd
editor: 
ms.assetid: a55f8c6b-3627-42e1-9d25-ed3e4ab17b1f
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: adigan;giridham;trinadhk;markgal
translationtype: Human Translation
ms.sourcegitcommit: 14cc190a7d1cde1181a6f26ef83095bc601f7fbb
ms.openlocfilehash: 36c4e1865c99dd1c55be798e1d310a02f2af0864


---
# <a name="recovering-data-from-another-azure-backup-server-in-the-backup-vault"></a>Récupération de données à partir d’un autre serveur de sauvegarde Azure dans le coffre de sauvegarde
Vous pouvez désormais récupérer les données que vous avez protégées dans un coffre de sauvegarde Azure à partir de n’importe quel serveur de sauvegarde Azure inscrit auprès de ce coffre. Ainsi, le processus permettant d’effectuer cette opération est totalement intégré à la console d’administration ABS et semblable aux autres workflows de récupération.

> [!NOTE]
> Cet article et les étapes suivantes sont également applicables à [System Center Data Protection Manager UR7] (https://support.microsoft.com/en-us/kb/3065246) avec le [dernier agent de Sauvegarde Azure](http://aka.ms/azurebackup_agent).
>
>

## <a name="recover-data-from-another-azure-backup-server"></a>Récupération de données à partir d’un autre serveur de sauvegarde Azure
Pour récupérer des données à partir d’un autre serveur de sauvegarde Azure :

1. Dans l’onglet **Récupération** de la console d’administration ABS, cliquez sur **« Ajouter un DPM externe »** (dans la partie supérieure gauche de l’écran).   
    ![Ajouter un serveur DPM externe](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Téléchargez à partir du coffre les nouvelles **informations d’identification** associées au **serveur de sauvegarde Azure** pour lequel les données sont récupérées, choisissez le serveur de sauvegarde Azure dans la liste des serveurs de sauvegarde Azure inscrits auprès du coffre de sauvegarde et saisissez la **phrase secrète de chiffrement** associée au serveur pour lequel les données sont en cours de récupération.

    ![Informations d'identification d’un serveur DPM externe](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Seuls les serveurs de sauvegarde Azure associés au même coffre d’inscription peuvent récupérer des données entre eux.
   >
   >

    Une fois le serveur de sauvegarde Azure externe ajouté, vous pouvez parcourir les données du serveur externe et du serveur de sauvegarde Azure local dans l’onglet **Récupération**.
3. Parcourez la liste des serveurs de production protégés par le serveur de sauvegarde Azure externe et sélectionnez la source de données appropriée.

    ![Parcourir un serveur DPM externe](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Sélectionnez **le mois et l’année** dans la liste déroulante **Points de récupération**. Sélectionnez la **date de récupération** souhaitée correspondant au moment de la création du point de récupération, puis sélectionnez **l’Heure de récupération**.

    Une liste de fichiers et de dossiers s'affiche dans le volet inférieur, qui peuvent être parcourus et récupérés sur n'importe quel emplacement.

    ![Points de récupération d’un serveur DPM externe](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Cliquez avec le bouton droit sur l'élément approprié puis cliquez sur **Récupérer**.

    ![Récupération d’un serveur DPM externe](./media/backup-azure-alternate-dpm-server/recover.png)
6. Vérifiez la **sélection à récupérer**. Vérifiez les données et l’heure de la copie de sauvegarde à récupérer, ainsi que la source à partir de laquelle la copie de sauvegarde a été créée. Si la sélection est incorrecte, cliquez sur **Annuler** pour revenir à l'onglet Récupération et sélectionnez le point de récupération approprié. Si la sélection est correcte, cliquez sur **Suivant**.

    ![Résumé de la récupération d’un serveur DPM externe](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Sélectionnez **Récupérer à un autre emplacement**. **Rechercher** l'emplacement correct pour la récupération.

    ![Emplacement de remplacement de la récupération d’un serveur DPM externe](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Choisissez entre les options **Créer une copie**, **Ignorer** et **Remplacer**.

   * **Créer une copie** crée une copie du fichier en cas de conflit de nom.
   * **Ignorer** ignore la récupération du fichier en cas de conflit de nom.
   * **Remplacer** remplace la copie existante à l'emplacement spécifié en cas de conflit de nom.

     Choisissez l'option appropriée pour **Restaurer la sécurité**. Vous pouvez appliquer les paramètres de sécurité de l'ordinateur de destination sur lequel les données sont récupérées ou les paramètres de sécurité qui étaient applicables au produit au moment de la création du point de récupération.

     Indiquez si une **Notification** doit être envoyée une fois la récupération terminée.

     ![Notifications de la récupération d’un serveur DPM externe](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. L’écran **Résumé** répertorie les options sélectionnées jusqu'à présent. Lorsque vous cliquerez sur **« Récupérer »**, les données seront récupérées sur l'emplacement local approprié.

    ![Résumé des options de la récupération d’un serveur DPM externe](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > Le travail de récupération peut être surveillé dans l’onglet **Analyse** du serveur de sauvegarde Azure.
   >
   >

    ![Surveillance de la récupération](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. Vous pouvez cliquer sur **Effacer le DPM externe** dans l’onglet **Récupération** du serveur DPM pour supprimer l’affichage du serveur DPM externe.

    ![Effacer un serveur DPM externe](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Dépannage des messages d'erreur
| Non. | Message d’erreur | Étapes de dépannage |
|:---:|:--- |:--- |
| 1. |Ce serveur n'est pas enregistré dans le coffre spécifié par les informations d'identification de coffre. |**Raison :** cette erreur apparaît lorsque le fichier d’informations d’identification du coffre sélectionné n’appartient pas au coffre de sauvegarde associé au serveur de sauvegarde Azure sur lequel la récupération est tentée. <br> **Résolution :** téléchargez le fichier d’informations d’identification du coffre de sauvegarde auquel le serveur de sauvegarde Azure est inscrit. |
| 2. |Les données récupérables ne sont pas disponibles ou le serveur sélectionné n'est pas un serveur DPM. |**Raison :** aucun autre serveur de sauvegarde Azure n’est inscrit auprès du coffre de sauvegarde, les serveurs n’ont pas encore chargé les métadonnées ou le serveur sélectionné n’est pas un serveur de sauvegarde Azure (également appelé serveur Windows Server ou client Windows). <br> **Résolution :** s’il existe d’autres serveurs de sauvegarde Azure inscrits auprès du coffre de sauvegarde, assurez-vous que le dernier agent de sauvegarde Azure est installé. <br>S’il existe d’autres serveurs de sauvegarde Azure inscrits auprès du coffre de sauvegarde, patientez un jour après l’installation pour lancer le processus de récupération. Le travail nocturne chargera les métadonnées de toutes les sauvegardes protégées dans le cloud. Les données seront disponibles pour la récupération. |
| 3. |Aucun autre serveur DPM n'est inscrit auprès de ce coffre. |**Cause :** aucun autre serveur de sauvegarde Azure n’est enregistré dans le coffre à partir duquel la récupération est tentée.<br>**Résolution :** s’il existe d’autres serveurs de sauvegarde Azure inscrits auprès du coffre de sauvegarde, assurez-vous que le dernier agent de sauvegarde Azure est installé.<br>S’il existe d’autres serveurs de sauvegarde Azure inscrits auprès du coffre de sauvegarde, patientez un jour après l’installation pour lancer le processus de récupération. Le travail nocturne chargera les métadonnées de toutes les sauvegardes protégées dans le cloud. Les données seront disponibles pour la récupération. |
| 4. |La phrase secrète de chiffrement fournie ne correspond pas à la phrase secrète associée au serveur suivant : **<server name>** |**Raison :** la phrase secrète de chiffrement utilisée dans le processus de chiffrement des données à partir des données du serveur de sauvegarde Azure en cours de récupération ne correspond pas à la phrase secrète de chiffrement fournie. L'agent ne peut pas déchiffrer les données. Par conséquent, la récupération échoue.<br>**Résolution :** veuillez fournir la phrase secrète de chiffrement associée au serveur de sauvegarde Azure pour lequel les données sont en cours de récupération. |

## <a name="frequently-asked-questions"></a>Forum Aux Questions :
1. **Pourquoi ne puis-je pas ajouter un serveur DPM externe à partir d’un autre serveur DPM après avoir installé UR7 et le dernier agent de sauvegarde Azure ? (Applicable si vous utilisez SC DPM 2012 R2)**

    A) Pour les serveurs DPM existants avec des sources de données protégées dans le cloud (en utilisant un correctif cumulatif antérieur au correctif cumulatif 7), vous devez attendre au moins un jour après l'installation d’UR7 et du dernier agent Azure Backup avant de commencer à *ajouter un serveur DPM externe*. Ce temps d’attente est nécessaire pour télécharger les métadonnées des groupes de protection DPM sur Azure. Cela se produit la première fois par le biais d’une tâche nocturne.
2. **Quelle est la version minimale de l'agent Azure Backup nécessaire ?**

    (A) La version minimale de l'agent Azure Backup pour activer cette fonctionnalité est 2.0.8719.0.  Vous pouvez connaître la version de l’agent de sauvegarde Azure en accédant au Panneau de configuration **>** Tous les Panneaux de configuration **>** Programmes et fonctionnalités **>** Agent Microsoft Azure Recovery Services. Si la version est inférieure à 2.0.8719.0, téléchargez le [dernier agent Azure Backup](https://go.microsoft.com/fwLink/?LinkID=288905) et installez-le.

    ![Effacer un serveur DPM externe](./media/backup-azure-alternate-dpm-server/external-dpm-azurebackupagentversion.png)

## <a name="next-steps"></a>Étapes suivantes :
•    [Sauvegarde Azure - FAQ](backup-azure-backup-faq.md)



<!--HONumber=Jan17_HO4-->



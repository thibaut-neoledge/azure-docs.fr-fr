<properties
  pageTitle="Préparation de la sauvegarde des charges de travail à l’aide d’Azure Backup Server | Microsoft Azure"
  description="Assurez-vous que votre environnement est correctement préparé à la sauvegarde des charges de travail à l’aide d’Azure Backup Server"
  services="backup"
  documentationCenter=""
  authors="pvrk"
  manager="shivamg"
  editor=""
  keywords="serveur de sauvegarde Azure ; coffre de sauvegarde"/>

<tags
  ms.service="backup"
  ms.workload="storage-backup-recovery"
  ms.tgt_pltfrm="na"
  ms.devlang="na"
  ms.topic="article"
  ms.date="02/04/2016"
  ms.author="jimpark;trinadhk;pullabhk"/>

# Préparation de la sauvegarde des charges de travail à l’aide d’Azure Backup Server

> [AZURE.SELECTOR]
- [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
- [System Center DPM](backup-azure-dpm-introduction.md)

Cet article concerne la préparation de votre environnement à la sauvegarde des charges de travail à l’aide d’Azure Backup Server. Avec Azure Backup Server, vous pouvez protéger des charges de travail d’application telles que les machines virtuelles Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange et les clients Windows à partir d’une console unique :

>[AZURE.WARNING] Azure Backup Server hérite des fonctionnalités de Data Protection Manager (DPM) pour la sauvegarde de charge de travail. Vous trouverez des liens vers la documentation DPM pour certaines de ces fonctionnalités. Cependant, le serveur Microsoft Azure Backup ne fournit pas de protection sur bande et ne s’intègre pas à System Center.

## 1\. Machine Windows Server

![étape 1](./media/backup-azure-microsoft-azure-backup/step1.png)

La première étape de la mise en service d’Azure Backup Server consiste à se procurer un ordinateur Windows Server.

| Emplacement | Configuration minimale requise | Instructions supplémentaires |
| -------- | -------------------- | ----------------------- |
| Microsoft Azure | Machine virtuelle IaaS Azure<br><br>Standard A2 : 2 cœurs, 3,5 Go de RAM | Vous pouvez démarrer avec une simple image de la galerie de Windows Server 2012 R2 Datacenter. [La protection des charges de travail IaaS à l’aide d’Azure Backup Server (DPM)](https://technet.microsoft.com/library/jj852163.aspx) peut prendre plusieurs formes. Veillez à lire l’article dans son intégralité avant de déployer la machine. |
| Local | Machine virtuelle Hyper-V,<br> machine virtuelle VMWare<br> ou un hôte physique<br><br>2 cœurs et 4 Go de RAM | Vous pouvez dédupliquer le stockage DPM en vous servant de la fonction de déduplication Windows Server. En savoir plus sur le fonctionnement du [DPM et de la déduplication](https://technet.microsoft.com/library/dn891438.aspx) en cas de déploiement sur des machines virtuelles Hyper-V. |

> [AZURE.NOTE] Il est conseillé d’installer Azure Backup Server sur un ordinateur équipé de Windows Server 2012 R2 Datacenter. La dernière version du système d’exploitation Windows est automatiquement conforme aux conditions préalables requises.

Si vous envisagez d’ajouter ce serveur à un domaine à un moment donné, il est conseillé d’exécuter l’activité du domaine associé avant l’installation du serveur de sauvegarde Azure. Le déplacement d’une machine Azure Backup Server vers un nouveau domaine après le déploiement *n’est pas pris en charge*.

## 2\. Archivage de sauvegarde

![étape 2](./media/backup-azure-microsoft-azure-backup/step2.png)

Que vous envoyiez des données de sauvegarde à Azure, ou que vous les conserviez en local, le logiciel doit être connecté à Azure. Pour être plus précis, l’ordinateur Azure Backup Server doit être enregistré dans un coffre de sauvegarde.

Pour créer un archivage de sauvegarde :

1. Connectez-vous au [portail de gestion](http://manage.windowsazure.com/).

2. Cliquez sur **Nouveau** -> **Services de données** -> **Services de récupération** -> **Archivage de sauvegarde** > **Création rapide**. Si vous disposez de plusieurs abonnements associés à votre compte professionnel, choisissez l’abonnement correct à associer à l’archivage de sauvegarde.

3. Dans **Name**, entrez un nom convivial pour identifier le coffre. Cette opération doit être unique pour chaque abonnement.

4. Dans **Region**, sélectionnez la région géographique du coffre. En règle générale, la région dans laquelle le coffre se trouve est choisie en fonction de la souveraineté ou des contraintes de latence réseau.

    ![Créer un archivage de sauvegarde](./media/backup-azure-microsoft-azure-backup/backup_vaultcreate.png)

5. Cliquez sur **Create vault**. La création du coffre de sauvegarde peut prendre du temps. Surveillez les notifications d'état en bas du portail.

    ![Créer une notification toast l’archivage](./media/backup-azure-microsoft-azure-backup/creating-vault.png)

6. Un message confirme que l’archivage a été correctement créé et l’archivage est affiché dans la page Services de récupération avec l’état Actif. ![Liste des archivages de sauvegarde](./media/backup-azure-microsoft-azure-backup/backup_vaultslist.png)

  > [AZURE.IMPORTANT] Assurez-vous que l'option de redondance de stockage appropriée est choisie juste après la création de l'archivage. En savoir plus sur les options [géo-redondant](../storage/storage-redundancy.md#geo-redundant-storage) et [localement redondant](../storage/storage-redundancy.md#locally-redundant-storage) dans cette [présentation](../storage/storage-redundancy.md).


## 3\. Package logiciel

![étape 3](./media/backup-azure-microsoft-azure-backup/step3.png)

### Téléchargement du package logiciel

Vous pouvez télécharger Microsoft Azure Backup pour les charges de travail d’application depuis la **page de démarrage rapide** d’un coffre de sauvegarde au même titre que les informations d’identification.

1. Cliquez sur **Pour les charges de travail d’application (Disque à Disque vers Cloud)**. Vous serez alors orienté vers la page du centre de téléchargement à partir de laquelle le package logiciel peut être téléchargé.

    ![Écran d’accueil Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/dpm-venus1.png)

2. Cliquez sur **Télécharger**.

    ![Centre de téléchargement 1](./media/backup-azure-microsoft-azure-backup/downloadcenter1.png)

3. Sélectionnez tous les fichiers, puis cliquez sur **Suivant**. Téléchargez tous les fichiers en provenance de la page de téléchargement Microsoft Azure Backup et placez tous les fichiers dans le même dossier. ![Centre de téléchargement 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Puisque la taille de téléchargement de l’ensemble des fichiers est > 3G, sur un lien de téléchargement de 10 Mbits/s, le téléchargement peut prendre jusqu’à 60 minutes.


### Extraction du package logiciel

Une fois que vous avez téléchargé tous les fichiers, cliquez sur **MicrosoftAzureBackupInstaller.exe**. L’**Assistant Installation de Microsoft Azure Backup** démarrera l’extraction des fichiers d’installation vers l’emplacement que vous avez spécifié. Suivez les instructions de l’Assistant et cliquez sur le bouton **Extraire** pour commencer le processus d’extraction.

> [AZURE.WARNING] Au moins 4 Go d’espace libre sont nécessaires pour l’extraction des fichiers d’installation.


![Assistant Installation de Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Une fois le processus d’extraction terminé, cochez la case pour exécuter le fichier *setup.exe* que vous venez d’extraire pour installer le serveur de sauvegarde Microsoft Azure Backup, puis cliquez sur le bouton **Terminer**.

### Installation du package logiciel

1. Cliquez sur **Microsoft Azure Backup** pour lancer l’Assistant d’installation.

    ![Assistant Installation de Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)

2. Dans l’écran d’accueil, cliquez sur **Suivant**. Vous êtes redirigé vers la section *Vérification des conditions préalables*. Sur cet écran, cliquez sur le bouton **Vérifier** afin de déterminer si les configurations matérielle et logicielle requises pour le serveur Microsoft Azure Backup sont respectées. Si c’est le cas, un message indiquant que la machine respecte les conditions préalables requises s’affiche. Cliquez sur le bouton **Suivant**.

    ![Azure Backup Server - Accueil et contrôle des conditions préalables requises](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

3. Microsoft Azure Backup Server requiert SQL Server Standard, et le package d’installation d’Azure Backup Server est fourni avec les fichiers binaires SQL Server appropriés. Lorsque vous démarrez avec une nouvelle installation Azure Backup Server, vous devez sélectionner l’option **Installer une nouvelle instance de SQL Server avec ce programme d’installation** et cliquer sur le bouton **Vérifier et installer**. Une fois les composants requis installés, cliquez sur **Suivant**.

    ![Serveur de sauvegarde Azure - Vérification SQL](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Si une défaillance se produit et qu’il est conseillé de redémarrer la machine, faites-le, puis cliquez sur **Vérifier à nouveau**.

    > [AZURE.NOTE] Azure Backup Server ne fonctionne pas avec une instance de serveur SQL distante. L’instance utilisée par le serveur de sauvegarde Azure doit être installée en local.

4. Fournissez un emplacement pour l’installation des fichiers de serveur Microsoft Azure Backup, puis cliquez sur **Suivant**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    Un emplacement temporaire est requis pour la sauvegarde sur Azure. Vérifiez que l’emplacement temporaire correspond à au moins 5 % du volume qu’il est prévu de sauvegarder dans le cloud. Pour la protection de disque, des disques séparés doivent être séparés une fois l’installation terminée. Pour plus d’informations sur les pools de stockage, consultez [Configurer des pools de stockage et de stockage sur disque](https://technet.microsoft.com/library/hh758075.aspx).

5. Fournissez un mot de passe fort pour les comptes utilisateur locaux restreints et cliquez sur **Suivant**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/security-screen.png)

6. Indiquez si vous souhaitez utiliser *Microsoft Update* pour vérifier les mises à jour et cliquez sur **Suivant**.

    >[AZURE.NOTE] Nous conseillons de faire en sorte que Windows Update soit redirigé vers Microsoft Update, qui offre une sécurité et des mises à jour importantes pour Windows et autres produits, tels que le serveur Microsoft Azure Backup.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

7. Examinez le *Résumé des paramètres* et cliquez sur **Installer**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)

8. L’installation se déroule en plusieurs phases. Lors de la première phase, l’Agent Microsoft Azure Recovery Services est installé sur le serveur. L’Assistant vérifie également la connectivité à Internet. Si la connectivité à Internet est disponible, vous pouvez poursuivre l’installation, sinon, vous devez fournir les informations de proxy détaillées pour se connecter à Internet.

    L’étape suivante consiste à configurer l’Agent Microsoft Azure Recovery Services. Dans le cadre de la configuration, vous devrez fournir les informations d’identification de coffre pour inscrire l’ordinateur dans le coffre de sauvegarde. Vous allez également fournir une phrase secrète pour chiffrer/déchiffrer les données circulant entre Azure et votre environnement local. Vous pouvez automatiquement générer une phrase secrète ou fournir votre propre phrase secrète d’au minimum 16 caractères. Continuez avec l’Assistant jusqu’à ce que l’agent soit configuré.

    ![PreReq2 de serveur de sauvegarde Azure](./media/backup-azure-microsoft-azure-backup/mars/04.png)

9. Une fois l’inscription du serveur Microsoft Azure Backup terminée et réussie, l’Assistant général d’installation procède à l’installation et à la configuration de SQL Server et des composants Azure Backup Server. Une fois l’installation du composant SQL Server terminée, les composants Azure Backup Server sont installés.

    ![Azure Backup Server](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)


Lorsque l’étape d’installation est terminée, les icônes du produit sur le bureau sont en principe créées elles aussi. Double-cliquez sur l’icône pour lancer le produit.

### Ajouter de l’espace de stockage pour la sauvegarde

La première copie de sauvegarde est conservée sur l’espace de stockage associé à l’ordinateur du serveur de sauvegarde Azure. Pour plus d’informations sur l’ajout de disques, consultez la section [Configurer des pools de stockage et un disque de stockage](https://technet.microsoft.com/library/hh758075.aspx).

> [AZURE.NOTE] Vous devez ajouter un stockage de sauvegarde même si vous prévoyez d’envoyer des données à Azure. Dans l’architecture actuelle d’Azure Backup Server, le coffre de sauvegarde Azure conserve la *deuxième* copie des données, alors que le stockage local conserve la première copie (obligatoire).

## 4\. Connectivité réseau

![step4](./media/backup-azure-microsoft-azure-backup/step4.png)

Pour que le produit fonctionne correctement, Azure Backup Server exige la connectivité au service Azure Backup. Pour vérifier que l’ordinateur est bien connecté à Azure, utilisez l’applet de commande ```Get-DPMCloudConnection``` dans la console du serveur de sauvegarde Azure PowerShell. Si le résultat de l’applet de commande est TRUE, la connectivité existe. Dans le cas contraire, la connectivité n’existe pas.

Dans le même temps, l’abonnement Azure doit être dans un état correct. Pour déterminer l’état de votre abonnement et le gérer, connectez-vous au [portail d’abonnement](https://account.windowsazure.com/Subscriptions).

Une fois que vous connaissez l’état de la connectivité d’Azure et de l’abonnement Azure, vous pouvez utiliser le tableau ci-dessous pour déterminer l’impact sur la fonctionnalité de sauvegarde/restauration proposée.

| État de la connectivité | Abonnement Azure | Sauvegarde sur Azure| Sauvegarde sur disque | Restauration à partir d’Azure | Restauration à partir d’un disque |
| -------- | ------- | --------------------- | ------------------- | --------------------------- | ----------------------- |
| Connecté | Actif | Autorisé | Autorisé | Autorisé | Autorisé |
| Connecté | Expiré | Arrêté | Arrêté | Autorisé | Autorisé |
| Connecté | Approvisionnement annulé | Arrêté | Arrêté | Arrêté et points de restauration Azure supprimés | Arrêté |
| Connectivité perdue depuis > 15 jours | Actif | Arrêté | Arrêté | Autorisé | Autorisé |
| Connectivité perdue depuis > 15 jours | Expiré | Arrêté | Arrêté | Autorisé | Autorisé |
| Connectivité perdue depuis > 15 jours | Approvisionnement annulé | Arrêté | Arrêté | Arrêté et points de restauration Azure supprimés | Arrêté |

### Récupération après la perte de connectivité
Si vous êtes équipé d’un pare-feu ou d’un proxy qui empêche l’accès à Azure, vous devez mettre sur liste blanche les adresses de domaine suivantes dans le profil de pare-feu/proxy :

- www.msftncsi.com
- *.Microsoft.com
- *.MicrosoftAzure.com
- *.microsoftonline.com
- *.windows.net

Une fois la connectivité à Azure restaurée sur l’ordinateur Azure Backup Server, les opérations qui peuvent être exécutées sont déterminées par l’état de l’abonnement Azure. Le tableau ci-dessus comporte les détails des opérations autorisées une fois l’ordinateur « connecté ».

### Gestion des états d’abonnement

Il est possible de faire passer un abonnement Azure de l’état *Expiré* ou *Approvisionnement annulé* à l’état *Actif*. Cependant, cette opération a certaines conséquences sur le comportement du produit lorsque l’état n’est pas *Actif* :

- Un abonnement dont l’*approvisionnement est annulé* ne fonctionne pas pendant la période pour laquelle l’approvisionnement est annulé. En redevenant *Actif*, la fonctionnalité de sauvegarde/restauration du produit est rétablie. Les données de sauvegarde présentes sur le disque local peuvent également être récupérées si le délai de rétention est suffisant. Toutefois, les données de sauvegarde dans Azure sont irrémédiablement perdues une fois que l’abonnement passe à l’état *Approvisionnement annulé*.
- Un abonnement *Expiré* ne fonctionne plus jusqu’à ce qu’il ait été *réactivé*. Lorsque l’abonnement est à l’état *Expiré*, les sauvegardes planifiées ne sont pas exécutées.


## Résolution de problèmes

Si le serveur Microsoft Azure Backup échoue avec des erreurs pendant la phase d’installation (ou de sauvegarde ou restauration), reportez-vous à ce [document de codes d’erreur](https://support.microsoft.com/kb/3041338) pour plus d’informations. Vous pouvez également vous reporter au [FAQ relatives à la sauvegarde Azure](backup-azure-backup-faq.md)


## Étapes suivantes

Vous pouvez obtenir des informations détaillées sur la [préparation de votre environnement pour DPM](https://technet.microsoft.com/library/hh758176.aspx) sur le site Microsoft TechNet. Ce dernier contient également des informations relatives aux configurations prises en charge sur lesquelles Azure Backup Server peut être déployé et utilisé.

Vous pouvez utiliser ces articles pour apprendre à mieux connaître la notion de protection de charge de travail à l’aide du serveur Microsoft Azure Backup.

- [Sauvegarde SQL Server](backup-azure-backup-sql.md)
- [Sauvegarde de serveur SharePoint](backup-azure-backup-sharepoint.md)
- [Sauvegarde sur un autre serveur](backup-azure-alternate-dpm-server.md)

<!---HONumber=AcomDC_0316_2016-->
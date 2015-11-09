<properties
	pageTitle="Préparation de votre environnement pour la sauvegarde des machines virtuelles Azure | Microsoft Azure"
	description="Assurez-vous que votre environnement est prêt pour la sauvegarde des machines virtuelles Azure"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/23/2015"
	ms.author="trinadhk; aashishr; jimpark; markgal"/>

# Préparation de votre environnement pour la sauvegarde des machines virtuelles Azure
Avant de sauvegarder une machine virtuelle Azure, vous devez remplir ces conditions préalables requises pour préparer votre environnement. Si cela est déjà fait, vous pouvez démarrer la [sauvegarde de vos machines virtuelles](backup-azure-vms.md) ; sinon, exécutez les étapes ci-dessous pour vous assurer que votre environnement est prêt.

## 1\. Archivage de sauvegarde
Pour démarrer la sauvegarde de vos machines virtuelles Azure, vous devez d'abord créer un archivage de sauvegarde. Un archivage est une entité qui stocke les sauvegardes et les points de récupération créés au fil du temps. L’archivage contient également les stratégies de sauvegarde qui seront appliquées aux machines virtuelles en cours de sauvegarde.

Cette image illustre les relations entre les différentes entités d'Azure Backup : ![Entités et relation d’Azure Backup](./media/backup-azure-vms-prepare/vault-policy-vm.png)

Pour créer un archivage de sauvegarde :

1. Connectez-vous au [portail de gestion](http://manage.windowsazure.com/).

2. Cliquez sur **Nouveau** -> **Services de données** -> **Services de récupération** -> **Archivage de sauvegarde** > **Création rapide**. Si vous disposez de plusieurs abonnements associés à votre compte professionnel, choisissez l’abonnement correct à associer à l’archivage de sauvegarde. Dans chaque abonnement Azure, vous pouvez avoir plusieurs archivages de sauvegarde pour organiser les machines virtuelles protégées.

3. Dans **Name**, entrez un nom convivial pour identifier le coffre. Cette opération doit être unique pour chaque abonnement.

4. Dans **Region**, sélectionnez la région géographique du coffre. L'archivage doit se trouver dans la même région que les machines virtuelles que vous souhaitez protéger. Si vous avez des machines virtuelles dans différentes régions, créez un archivage dans chacune d’elles. Il est inutile de spécifier des comptes de stockage pour stocker les données de sauvegarde : l'archivage de sauvegarde et le service Azure Backup s'en chargent automatiquement.

    ![Créer un archivage de sauvegarde](./media/backup-azure-vms-prepare/backup_vaultcreate.png)

5. Cliquez sur **Create vault**. La création du coffre de sauvegarde peut prendre du temps. Surveillez les notifications d'état en bas du portail.

    ![Créer une notification toast l’archivage](./media/backup-azure-vms-prepare/creating-vault.png)

6. Un message confirme que l'archivage a été correctement créé et l'archivage est affiché dans la page Services de récupération avec l'état Actif. Assurez-vous que l'option de redondance de stockage appropriée est choisie juste après la création de l'archivage. En savoir plus sur la [définition de l’option de redondance de stockage dans le coffre de sauvegarde](backup-configure-vault.md#azure-backup---storage-redundancy-options).

    ![Liste des archivages de sauvegarde](./media/backup-azure-vms-prepare/backup_vaultslist.png)

7. Cliquez sur l'archivage de sauvegarde pour accéder à la page **Démarrage rapide**, où sont affichées les instructions pour la sauvegarde des machines virtuelles Azure.

    ![Instructions de sauvegarde de machines virtuelles dans la page Tableau de bord](./media/backup-azure-vms-prepare/vmbackup-instructions.png)

## 2\. Agent VM
Avant de sauvegarder la machine virtuelle Azure, assurez-vous que l'agent de machine virtuelle Azure est correctement installé sur la machine virtuelle. L'agent de machine virtuelle étant un composant facultatif au moment de la création de la machine virtuelle, assurez-vous que la case de l'agent de machine virtuelle est cochée avant de configurer la machine virtuelle.

En savoir plus sur l’[agent de machine virtuelle](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) et [comment l’installer](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

### Extension de sauvegarde
Pour sauvegarder la machine virtuelle, le service Azure Backup installe une extension vers l'agent de machine virtuelle. Le service Azure Backup met à niveau et corrige en toute transparence l'extension de sauvegarde sans intervention supplémentaire de l'utilisateur.

L’extension de sauvegarde est installée si la machine virtuelle est en cours de fonctionnement. Une machine virtuelle en cours d'exécution présente également le plus de chance d'obtenir un point de récupération d'application cohérent. Toutefois, le service Azure Backup poursuit la sauvegarde de machine virtuelle, même si elle est éteinte et si l'extension n'a pas été installée (c'est-à-dire, si la machine virtuelle est hors ligne). Dans ce cas, le point de récupération sera *cohérent suite à l'incident* comme indiqué ci-dessus.

## 3\. Connexion réseau
L'extension de sauvegarde requiert une connectivité à Internet pour fonctionner correctement, car elle envoie des commandes à un point de terminaison Azure Storage (URL HTTP) pour gérer les instantanés de la machine virtuelle. Sans connectivité Internet, ces requêtes HTTP de la machine virtuelle expirent et l'opération de sauvegarde échoue.

## Limites

- La sauvegarde de machines virtuelles basées sur Azure Resource Manager (ou « IaaS V2 ») n'est pas prise en charge.
- La sauvegarde de machines virtuelles ayant plus de 16 disques de données n'est pas prise en charge.
- La sauvegarde de machines virtuelles à l'aide du stockage Premium n'est pas prise en charge.
- La sauvegarde de machines virtuelles ayant plusieurs adresses IP réservées n'est pas prise en charge.
- La sauvegarde de machines virtuelles avec une adresse IP réservée et aucun point de terminaison défini n'est pas prise en charge.
- La sauvegarde de machines virtuelles à l'aide de plusieurs cartes réseau n'est pas prise en charge.
- La sauvegarde de machines virtuelles dans une configuration à charge équilibrée (interne ou accessible via Internet) n'est pas prise en charge.
- Le remplacement d’une machine virtuelle existante pendant la restauration n’est pas pris en charge. Commencez par supprimer la machine virtuelle existante et tous les disques associés, puis restaurez les données de sauvegarde.
- La sauvegarde et la restauration entre différentes régions ne sont pas prises en charge.
- La sauvegarde de machines virtuelles à l'aide du service Azure Backup n'est pas prise en charge dans toutes les régions publiques d'Azure. Voici la [liste](http://azure.microsoft.com/regions/#services) des régions prises en charge. Si la région que vous recherchez n’est pas prise en charge aujourd’hui, elle n’apparaît pas dans la liste déroulante lors de la création de l’archivage.
- La sauvegarde de machines virtuelles à l'aide du service Azure Backup n'est prise en charge que pour certaines versions de système d'exploitation :
  - **Linux** : la liste des distributions approuvées par Azure est disponible [ici](../virtual-machines-linux-endorsed-distributions.md). D’autres distributions « Bring-Your-Own-Linux » fonctionnent également tant que l’agent de machine virtuelle est disponible sur la machine virtuelle.
  - **Windows Server** : les versions antérieures à Windows Server 2008 R2 ne sont pas prises en charge.
- La restauration d'une machine virtuelle de contrôleur de domaine qui fait partie d'une configuration à plusieurs contrôleurs de domaine est prise en charge uniquement par le biais de PowerShell. En savoir plus sur la [restauration d'un contrôleur de domaine dans un environnement à plusieurs contrôleurs de domaine](backup-azure-restore-vms.md#restoring-domain-controller-vms)

## Des questions ?
Si vous avez des questions ou si vous souhaitez que certaines fonctionnalités soient incluses, [envoyez-nous vos commentaires](http://aka.ms/azurebackup_feedback).

## Étapes suivantes

- [Planification de votre infrastructure de sauvegarde de machines virtuelles](backup-azure-vms-introduction.md)
- [Sauvegarde de machines virtuelles](backup-azure-vms.md)
- [Gestion de la sauvegarde de machine virtuelle](backup-azure-manage-vms.md)

<!---HONumber=Nov15_HO1-->
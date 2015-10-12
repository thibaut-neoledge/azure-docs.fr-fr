<properties
	pageTitle="Présentation de la sauvegarde des machines virtuelles Azure | Microsoft Azure"
	description="Présentation de la sauvegarde des machines virtuelles dans Azure à l’aide du service Azure Backup"
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/24/2015" ms.author="trinadhk";"aashishr";"jimpark"/>

# Sauvegarde de la machine virtuelle Azure

Cette section présente l’utilisation de Microsoft Azure Backup pour protéger vos machines virtuelles Azure. Elle vous permettra de découvrir :

- le fonctionnement de la sauvegarde des machines virtuelles Azure ;
- la procédure de sauvegarde de votre machine virtuelle Azure ;
- les conditions requises pour une expérience de sauvegarde fluide ;
- les erreurs classiques rencontrées et comment les gérer ;
- la liste des scénarios non pris en charge et leur influence sur les modifications apportées au produit.

Pour en savoir plus sur les machines virtuelles Azure, consultez la [Documentation sur les machines virtuelles](https://azure.microsoft.com/documentation/services/virtual-machines/).

## Pourquoi sauvegarder une machine virtuelle Azure ?
Le cloud computing permet aux applications de s’exécuter dans un environnement extensible à haut niveau de disponibilité, c’est pourquoi Microsoft a développé les machines virtuelles Azure. Les données générées à partir de ces machines virtuelles Azure sont importantes et doivent être sauvegardées pour être placées en lieu sûr. Les scénarios classiques qui requièrent la restauration des données sauvegardées sont les suivants :

- Suppression accidentelle ou malveillante des fichiers
- Corruption de la machine virtuelle pendant une mise à jour des correctifs
- Suppression accidentelle ou malveillante de l’intégralité de la machine virtuelle

Les données peuvent être sauvegardées à partir de ces machines virtuelles de deux façons différentes :

- Sauvegarde de chaque source de données à partir de la machine virtuelle
- Sauvegarde de l’intégralité de la machine virtuelle

La sauvegarde de l’intégralité de la machine virtuelle est souvent utilisée, car elle est beaucoup plus simple à gérer et facilite également les restaurations de l’ensemble des applications et du système d’exploitation. Azure Backup peut être utilisé pour une sauvegarde de données in-guest ou la sauvegarde de l’intégralité de la machine virtuelle.

Les avantages commerciaux de l’utilisation d’Azure Backup pour la sauvegarde des machines virtuelles sont les suivants :

- Automatisation des flux de travail de sauvegarde et de restauration pour vos machines virtuelles
- Sauvegardes cohérentes au niveau des applications pour vous assurer que les données restaurées partent d’un état cohérent.
- Pas d’interruption de service lors de la sauvegarde de la machine virtuelle.
- Les machines virtuelles Windows ou Linux peuvent être sauvegardées.
- Des points de récupération sont disponibles pour une restauration facile dans l’archivage de sauvegarde Azure.
- Opérations automatiques de nettoyage et de garbage collection des points de récupération les plus anciens.

## Comment fonctionne la sauvegarde des machines virtuelles Azure ?
Pour sauvegarder une machine virtuelle, il est d’abord nécessaire de capturer un instantané des données. Le service Azure Backup lance le travail de sauvegarde à l’heure planifiée et déclenche l’extension de sauvegarde pour capturer un instantané. L’extension de sauvegarde se coordonne avec le service VSS in-guest pour assurer la cohérence et appelle l’API d’instantané d’objet blob du service Azure Storage une fois que la cohérence a été atteinte. Cela permet d’obtenir un instantané cohérent des disques de la machine virtuelle sans avoir à l’arrêter.

Une fois l’instantané capturé, les données sont transférées par le service Azure Backup dans l’archivage de sauvegarde. Le service se charge d’identifier et de transférer uniquement les blocs qui ont été modifiés depuis la dernière sauvegarde, ce qui garantit l’efficacité du stockage des sauvegardes. Une fois le transfert de données terminé, l’instantané est supprimé et un point de récupération est créé. Ce point de récupération est affiché dans le portail de gestion Azure.

![Architecture de la sauvegarde des machines virtuelles Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

>[AZURE.NOTE]Pour les machines virtuelles Linux, seule une sauvegarde cohérente au niveau des fichiers est possible.

## Calcul des instances protégées
Les machines virtuelles Azure sauvegardées à l’aide d’Azure Backup sont soumises à la [Tarification d’Azure Backup](http://azure.microsoft.com/pricing/details/backup/). Le calcul des instances protégées est basé sur la taille *réelle* de la machine virtuelle, qui est la somme de toutes les données de la machine virtuelle, à l’exception du « disque de ressources ». Vous n’êtes *pas* facturé en fonction de la taille maximale prise en charge pour chaque disque de données attaché à la machine virtuelle, mais en fonction des données réelles stockées sur le disque de données. De même, la facture du stockage de sauvegarde est basée sur la quantité de données stockées avec Azure Backup, qui est la somme des données réelles de chaque point de récupération.

Prenons l’exemple d’une machine virtuelle de taille standard A2 avec deux disques de données supplémentaires d’une taille maximale de 1 To. Le tableau ci-dessous indique les données réelles stockées sur chacun de ces disques :

|Type de disque|Taille maximale|Données réelles présentes|
|---------|--------|------|
| Disque de système d’exploitation | 1 023 Go | 17 Go |
| Disque local/Disque de ressources | 135 Go | 5 Go (non inclus pour la sauvegarde) |
| Disque de données 1 |	1 023 Go | 30 Go |
| Disque de données 2 | 1 023 Go | 0 Go |

La taille *réelle* de la machine virtuelle est dans ce cas 17 Go + 30 Go + 0 = 47 Go. Il s’agit de la taille d’instance protégée sur laquelle est basée la facture mensuelle. À mesure que la quantité de données de la machine virtuelle augmente, la taille d’instance protégée utilisée pour la facturation augmente proportionnellement.

La facturation ne commence pas avant la fin de la première sauvegarde réussie. À partir de ce moment, la facturation du stockage et des instances protégées commence. La facturation continue tant que des *données de sauvegarde sont stockées avec Azure Backup* pour la machine virtuelle. L’opération Arrêter la protection n’arrête pas la facturation si les données de sauvegarde sont conservées. La facturation pour une machine virtuelle spécifiée est interrompue uniquement si la protection est arrêtée *et* que les données de sauvegarde sont supprimées. Si aucun travail de sauvegarde n’est actif (et que la protection a été arrêtée), la taille de la machine virtuelle au moment de la dernière sauvegarde réussie devient la taille d’instance protégée sur laquelle est basée la facture mensuelle.

## Composants requis
### 1\. Archivage de sauvegarde
Pour démarrer la sauvegarde de vos machines virtuelles Azure, vous devez d’abord créer un archivage de sauvegarde. L’archivage est une entité qui stocke les sauvegardes et les points de récupération créés au fil du temps. L’archivage contient également les stratégies de sauvegarde qui seront appliquées aux machines virtuelles en cours de sauvegarde.

L’image ci-dessous illustre les relations entre les différentes entités d’Azure Backup : ![Entités et relation d’Azure Backup](./media/backup-azure-vms-introduction/vault-policy-vm.png)

### Pour créer un archivage de sauvegarde

1. Connectez-vous au [portail de gestion](http://manage.windowsazure.com/).

2. Cliquez sur **Nouveau** -> **Services de données** -> **Services de récupération** -> **Archivage de sauvegarde** > **Création rapide**. Si vous disposez de plusieurs abonnements associés à votre compte professionnel, choisissez l’abonnement correct à associer à l’archivage de sauvegarde. Dans chaque abonnement Azure, vous pouvez avoir plusieurs archivages de sauvegarde pour organiser les machines virtuelles protégées.

3. Dans **Name**, entrez un nom convivial pour identifier le coffre. Cette opération doit être unique pour chaque abonnement.

4. Dans **Region**, sélectionnez la région géographique du coffre. Notez que l’archivage doit se trouver dans la même région que les machines virtuelles que vous souhaitez protéger. Si vous avez des machines virtuelles dans différentes régions, créez un archivage dans chacune d’elles. Il est inutile de spécifier des comptes de stockage pour stocker les données de sauvegarde : l’archivage de sauvegarde et le service Azure Backup s’en chargent automatiquement. ![Créer un archivage de sauvegarde](./media/backup-azure-vms-introduction/backup_vaultcreate.png)

5. Cliquez sur **Créer un archivage**. La création du coffre de sauvegarde peut prendre du temps. Surveillez les notifications d’état en bas du portail. ![Créer une notification toast l’archivage](./media/backup-azure-vms-introduction/creating-vault.png)

6. Un message confirme que l'archivage a été correctement créé et l'archivage est affiché dans la page Services de récupération avec l'état Actif. Assurez-vous que l’option de redondance de stockage appropriée est choisie juste après la création de l’archivage. En savoir plus sur la [définition de l’option de redondance de stockage dans le coffre de sauvegarde](../backup-azure-backup-create-vault.md#storage-redundancy-options). ![Liste des archivages de sauvegarde](./media/backup-azure-vms-introduction/backup_vaultslist.png)

7. En cliquant sur l’archivage de sauvegarde, vous accédez à la page **Démarrage rapide**, où sont affichées les instructions pour la sauvegarde des machines virtuelles Azure. ![Instructions de sauvegarde de machines virtuelles dans la page Tableau de bord](./media/backup-azure-vms-introduction/vmbackup-instructions.png)


### 2\. Agent VM
Avant de commencer à sauvegarder la machine virtuelle Azure, assurez-vous que l’agent de machine virtuelle Azure est correctement installé sur la machine virtuelle. Pour sauvegarder la machine virtuelle, le service Azure Backup installe une extension vers l’agent de machine virtuelle. L’agent de machine virtuelle étant un composant facultatif au moment de la création de la machine virtuelle, vous devez vous assurer que la case de l’agent de machine virtuelle est cochée avant de configurer la machine virtuelle.

En savoir plus sur l’[agent de machine virtuelle](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) et [comment l’installer](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

## Limitations

- La sauvegarde de machines virtuelles basées sur Azure Resource Manager (ou « IaaS V2 ») n’est pas prise en charge.
- La sauvegarde de machines virtuelles ayant plus de 16 disques de données n’est pas prise en charge.
- La sauvegarde de machines virtuelles à l’aide du stockage Premium n’est pas prise en charge.
- La sauvegarde de machines virtuelles ayant plusieurs adresses IP réservées n’est pas prise en charge.
- La sauvegarde de machines virtuelles avec une adresse IP réservée et aucun point de terminaison défini n’est pas prise en charge.
- La sauvegarde de machines virtuelles à l’aide de plusieurs cartes réseau ou dans une configuration à charge équilibrée n’est pas prise en charge.
- Le remplacement d’une machine virtuelle existante pendant la restauration n’est pas pris en charge. Commencez par supprimer la machine virtuelle existante et tous les disques associés, puis restaurez les données de sauvegarde.
- La sauvegarde et la restauration entre différentes régions ne sont pas prises en charge.
- La sauvegarde de machines virtuelles à l’aide du service Azure Backup n’est pas prise en charge dans toutes les régions publiques d’Azure. Voici la [liste](http://azure.microsoft.com/regions/#services) des régions prises en charge. Si la région que vous recherchez n’est pas prise en charge aujourd’hui, elle n’apparaît pas dans la liste déroulante lors de la création de l’archivage.
- La sauvegarde de machines virtuelles à l’aide du service Azure Backup n’est prise en charge que pour certaines versions de système d’exploitation :
  - **Linux** : la liste des distributions approuvées par Azure est disponible [ici](../virtual-machines-linux-endorsed-distributions.md). D’autres distributions « Bring-Your-Own-Linux » fonctionnent également tant que l’agent de machine virtuelle est disponible sur la machine virtuelle.
  - **Windows Server** : les versions antérieures à Windows Server 2008 R2 ne sont pas prises en charge.
- La restauration d'une machine virtuelle de contrôleur de domaine qui fait partie d'une configuration à plusieurs contrôleurs de domaine est prise en charge uniquement par le biais de PowerShell. En savoir plus sur la [restauration d’un contrôleur de domaine dans un environnement à plusieurs contrôleurs de domaine](backup-azure-restore-vms.md#restoring-domain-controller-vms)

Si vous souhaitez que certaines fonctionnalités soient incluses, [envoyez-nous vos commentaires](http://aka.ms/azurebackup_feedback).

## Étapes suivantes
Pour bien démarrer avec la sauvegarde des machines virtuelles, découvrez comment :

- [Sauvegarde des machines virtuelles](backup-azure-vms.md)
- [Restauration des machines virtuelles](backup-azure-restore-vms.md)
- [Gestion de la sauvegarde de machine virtuelle](backup-azure-manage-vms.md)

<!---HONumber=Oct15_HO1-->
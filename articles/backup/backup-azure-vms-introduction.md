<properties
	pageTitle="Introduction à la sauvegarde de virtual machine Azure"
	description="Introduction à la sauvegarde des machines virtuelles dans Azure à l'aide du service de sauvegarde Azure"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/26/2015"
	ms.author="aashishr"/>

# Sauvegarde de la machine virtuelle Azure - Introduction

Cette section fournit une introduction à l'utilisation de Microsoft Azure Backup pour protéger vos ordinateurs virtuels Azure. En le lisant, vous découvrirez à :

- Travaux de sauvegarde machine virtuelle Azure
- La procédure pour sauvegarder votre machine virtuelle Azure
- Les conditions préalables pour atteindre une expérience sans heurts de sauvegarde
- Erreurs classiques et comment les gérer
- La liste des scénarios non pris en charge et influencent les modifications apportées au produit

Pour en savoir plus sur les machines virtuelles rapidement, consultez le [documentation de l'ordinateur virtuel](https://azure.microsoft.com/documentation/services/virtual-machines/).

## Pourquoi sauvegarder la machine virtuelle Azure ?
Le cloud computing permet aux applications de s'exécuter dans un environnement hautement disponible et évolutif – c'est pourquoi Microsoft a développé des machines virtuelles. Les données générées à partir de ces machines virtuelles sont importantes et requiert la sauvegarde en lieu sûr. Les scénarios qui requièrent des données à restaurer à partir de sauvegardes sont :

- Suppression accidentelle ou malveillante des fichiers
- Corruption de la machine virtuelle pendant une mise à jour des correctifs
- Suppression accidentelle ou malveillante de la machine virtuelle complète

Données peuvent être sauvegardées à partir de ces ordinateurs virtuels de deux façons différentes :

- Sauvegarder les sources de données à partir de l'ordinateur virtuel individuels
- Sauvegarder la machine virtuelle

Sauvegarde de la machine virtuelle complète est souvent utilisé car il est beaucoup plus simple à gérer et facilite également la restauration facile de l'ensemble de l'application et le système d'exploitation. Sauvegarde Azure peut être utilisée pour la sauvegarde de données dans l'invité ou de la machine virtuelle complète.

Les avantages de l'utilisation de sauvegarde Azure pour la sauvegarde des machines virtuelles sont :

- Automatisation des workflows de sauvegarde et de restauration pour les machines virtuelles
- Des sauvegardes cohérentes avec les applications pour vous assurer que récupérée données démarre à partir d'un état cohérent.
- Aucune interruption de service impliqués lors de la sauvegarde de l'ordinateur virtuel.
- Windows ou Linux des machines virtuelles peuvent être sauvegardés.
- Points de récupération sont disponibles pour une restauration facile dans le coffre de sauvegarde Azure.
- Automatique le nettoyage et le garbage collection de points de récupération plus anciens.

## Comment fonctionne la sauvegarde de la machine virtuelle Azure ?
Pour sauvegarder un ordinateur virtuel, tout d'abord un instantané des données est nécessaire. Le service de sauvegarde Azure lance l'opération de sauvegarde à l'heure planifiée et déclenche l'extension pour prendre un instantané de sauvegarde. L'extension de sauvegarde coordonne avec le service VSS dans l'invité pour assurer la cohérence et appelle l'API de capture instantanée blob du service de stockage Azure une fois que la cohérence a été atteint. Pour cela, pour obtenir un instantané cohérent des disques de l'ordinateur virtuel, sans avoir à arrêter.

Une fois l'instantané a été pris, les données sont transférées par le service de sauvegarde Azure dans le coffre de sauvegarde. Le service s'occupe d'identification et de transférer uniquement les blocs qui ont changé depuis la dernière sauvegarde, ce qui rend le stockage de sauvegardes efficace. Lorsque le transfert de données est terminé, l'instantané est supprimé et un point de récupération est créé. Ce point de récupération peut être constaté dans le portail de gestion Azure.

![Architecture de sauvegarde de machine virtuelle Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

>[AZURE.NOTE]Pour les ordinateurs virtuels Linux uniquement cohérente de fichier de sauvegarde est possible.

## Calcul protégé des instances
Machines virtuelles sont sauvegardées à l'aide de la sauvegarde Azure sera soumis pour [la tarification d'Azure Backup](http://azure.microsoft.com/pricing/details/backup/). Le calcul d'Instances protégé est basé sur le *réel* taille de la machine virtuelle, qui est la somme de toutes les données sur l'ordinateur virtuel – à l'exception de « disque de ressources ». Vous êtes *pas* facturé en fonction de la taille maximale prise en charge pour chaque disque de données attaché à la machine virtuelle, mais les données actuelles stockées dans le disque de données. De même, la nomenclature de stockage de sauvegarde est basée sur la quantité de données stockées avec Azure Backup, qui est la somme des données réelles sur chaque point de récupération.

Par exemple, prenez une machine virtuelle en taille Standard A2 qui a deux disques de données supplémentaires avec une taille maximale de 1 To. Le tableau ci-dessous donne les données stockées sur chacun de ces disques :

|Type de disque|Taille maximale|Données réelles présentes|
|---------|--------|------|
| Disque de système d'exploitation | 1 023 GO | 17 GO |
| Disque local / disque de ressources | 135 GO | 5 Go (non inclus pour la sauvegarde) |
| Disque de données 1 |	1 023 GO | 30 GO |
| Disque de données 2 | 1 023 GO | 0 GO |

Le *réel* taille de l'ordinateur virtuel est dans ce cas 17 Go + 30 Go + 0 = 47 go. Cela devient la taille d'Instance protégé basé sur la facture mensuelle. À mesure que la quantité de données dans l'ordinateur virtuel augmente la taille d'Instance protégé utilisée pour la facturation également changera correctement.

La facturation ne commence pas avant la fin de la première sauvegarde réussie. La facturation pour le stockage et Instances protégé commence à ce stade. La facturation continue tant qu'il est *une sauvegarde des données stockées avec Azure Backup* pour l'ordinateur virtuel. L'opération d'arrêter la Protection n'arrête pas la facturation si les données de sauvegarde sont conservées. La facturation pour une machine virtuelle spécifiée sera supprimée uniquement si la protection est arrêtée *et* des données de sauvegarde sont supprimées. Lorsqu'il n'y a aucune tâche de sauvegarde active (lors de la protection a été arrêtée), la taille de l'ordinateur virtuel au moment de la dernière sauvegarde réussie devient la taille d'Instance protégé basé sur la facture mensuelle.

## Composants requis
### 1. Coffre de sauvegarde
Pour démarrer la sauvegarde de vos machines virtuelles, vous devez d'abord créer un coffre de sauvegarde. Le coffre est une entité qui stocke les sauvegardes et les points de récupération qui ont été créées au fil du temps. Le coffre contient également les stratégies de sauvegarde seront appliquées aux ordinateurs virtuels en cours de sauvegarde.

L'image ci-dessous montre les relations entre les diverses entités de sauvegarde Azure : ![Relation et les entités de sauvegarde azure](./media/backup-azure-vms-introduction/vault-policy-vm.png)

### Pour créer un coffre de sauvegarde

1. Connectez-vous au [portail de gestion](http://manage.windowsazure.com/).

2. Cliquez sur **nouveau** > **Data Services** > **Services de récupération** > **coffre de sauvegarde** > **Création rapide**. Si vous avez plusieurs abonnements associés à votre compte professionnel, choisissez l'abonnement approprié à associer le coffre de sauvegarde. Dans chaque abonnement Azure, vous pouvez avoir plusieurs coffres de sauvegarde pour organiser les ordinateurs virtuels protégés.

3. Dans **Name**, entrez un nom convivial pour identifier le coffre. Cette opération doit être unique pour chaque abonnement.

4. Dans **Region**, sélectionnez la région géographique du coffre. Notez que le coffre doit être dans la même région que les ordinateurs virtuels que vous souhaitez protéger. Si vous avez des ordinateurs virtuels dans différentes régions créer un coffre dans chacun d'eux. Il est inutile de spécifier les comptes de stockage pour stocker les données de sauvegarde : le coffre de sauvegarde et le service de sauvegarde Azure qui va gérer cela automatiquement. ![Créer le coffre de sauvegarde](./media/backup-azure-vms-introduction/backup_vaultcreate.png)

    >[AZURE.NOTE]Ordinateur virtuel à l'aide du service de sauvegarde Azure est uniquement pris en charge dans Sélectionner des régions. Vérifier la liste de [prise en charge des régions](http://azure.microsoft.com/regions/#services). Si la région que vous recherchez est aujourd'hui non pris en charge, il n'apparaît pas dans la liste déroulante lors de la création du coffre.

5. Cliquez sur **créer coffre**. La création du coffre de sauvegarde peut prendre du temps. Surveiller les notifications d'état au bas du portail. ![Créer une notification toast de coffre](./media/backup-azure-vms-introduction/creating-vault.png)

6. Un message confirme que le coffre a été créé et apparaît dans la page Services de récupération actif. ![Liste des coffres de sauvegarde](./media/backup-azure-vms-introduction/backup_vaultslist.png)

7. Cliquez sur le coffre de sauvegarde accède à la **Quick Start** page, où les instructions pour la sauvegarde des machines virtuelles sont affichées. ![Instructions de sauvegarde d'ordinateur virtuel dans la page tableau de bord](./media/backup-azure-vms-introduction/vmbackup-instructions.png)

    >[AZURE.NOTE]Assurez-vous que l'option de redondance de stockage approprié est choisie juste après que le coffre a été créé. En savoir plus sur [définissant l'option de redondance de stockage dans le coffre de sauvegarde][vault-storage-redundancy].

### 2. Agent VM
Avant de commencer à sauvegarder la machine virtuelle Azure, assurez-vous que l'Agent de machine virtuelle Azure est correctement installé sur l'ordinateur virtuel. Pour sauvegarder la machine virtuelle, le service de sauvegarde Azure installe une extension à l'Agent de machine virtuelle. Étant donné que l'agent de machine virtuelle est un composant facultatif au moment de la machine virtuelle est créée, vous devez vous assurer que la case à cocher pour l'agent de machine virtuelle est sélectionné avant que la machine virtuelle est configurée.

En savoir plus sur les [Agent de machine virtuelle](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) et [comment l'installer](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

>[AZURE.NOTE]Si vous envisagez de migrer votre machine virtuelle à partir de votre centre de données local vers Azure, assurez-vous que vous téléchargez et installez l'agent de machine virtuelle MSI avant le début du processus de migration. Cela s'applique également aux ordinateurs virtuels protégés vers Azure à l'aide de la récupération de Site Azure.

## Limitations lors de l'aperçu

- Sauvegarde des machines virtuelles avec plus de 5 disques n'est pas pris en charge.
- Sauvegarde d'ordinateurs virtuels à l'aide du stockage Premium n'est pas pris en charge.
- Sauvegarde d'ordinateurs virtuels à l'aide de plusieurs cartes réseau ou dans une configuration à charge équilibrée n'est pas pris en charge.
- Remplacement d'un ordinateur virtuel existant au cours de la restauration n'est pas pris en charge. Tout d'abord supprimer l'ordinateur virtuel existant et tous les disques associés et puis restaurer les données de sauvegarde.
- Sauvegarde des machines virtuelles restaurées à l'aide de la récupération de Site Azure n'est pas pris en charge.
- Entre régions sauvegarde et restauration n'est pas pris en charge.
- Ordinateur virtuel à l'aide du service de sauvegarde Azure est uniquement pris en charge dans Sélectionner des régions. Vérifier la liste de [prise en charge des régions](http://azure.microsoft.com/regions/#services). Si la région que vous recherchez est aujourd'hui non pris en charge, il n'apparaît pas dans la liste déroulante lors de la création du coffre.
- Sauvegarde des machines virtuelles à l'aide du service de sauvegarde Azure est uniquement pris en charge uniquement pour certaines versions de système d'exploitation :
  - **Linux**: la liste des distributions approuvées par Azure est disponible [ici](../virtual-machines-linux-endorsed-distributions.md). Autres Bring-votre-propriétaire-distributions Linux doivent fonctionner tant que l'Agent de machine virtuelle est disponible sur l'ordinateur virtuel.
  - **Windows Server**: les Versions antérieures de Windows Server 2008 R2 ne sont pas pris en charge.

S'il existe des fonctionnalités que vous aimeriez voir inclus, [nous envoyer vos commentaires](http://aka.ms/azurebackup_feedback).

## Étapes suivantes
Pour commencer avec la sauvegarde des machines virtuelles, découvrez comment :

- [Découvrir, d'enregistrer et de protéger les machines virtuelles](backup-azure-vms.md)

- [Restauration des machines virtuelles](backup-azure-restore-vms.md)

+ Surveiller les tâches de sauvegarde

<!---HONumber=GIT-SubDir--> 
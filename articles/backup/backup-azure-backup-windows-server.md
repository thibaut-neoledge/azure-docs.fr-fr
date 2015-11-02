<properties
   pageTitle="Sauvegarder des fichiers et dossiers d’un serveur Windows ou d’un ordinateur client Windows sur Azure | Microsoft Azure"
   description="Découvrez comment sauvegarder des fichiers et dossiers d’un serveur Windows ou d’un ordinateur client Windows sur Azure."
   services="backup"
   documentationCenter=""
   authors="aashishr"
   manager="jwhit"
   editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/21/2015" ms.author="jimpark"; "aashishr"/>

# Sauvegarder des fichiers et dossiers d’un serveur Windows ou d’un ordinateur client Windows sur Azure
Cet article décrit les étapes requises pour sauvegarder des fichiers et dossiers d’un serveur Windows ou d’un ordinateur client Windows sur Azure.

## Avant de commencer
Avant de continuer, vérifiez que toutes les [conditions préalables](backup-configure-vault.md#before-you-start) à l’utilisation de Microsoft Azure Backup pour protéger le serveur Windows et l’ordinateur client Windows ont été remplies. Les conditions préalables couvrent des tâches telles que : création d’un coffre de sauvegarde, téléchargement des informations d’identification du coffre, installation de l’agent Azure Backup et inscription de l’ordinateur auprès du coffre.

## Fichiers de sauvegarde
1. Une fois l’ordinateur inscrit, ouvrez le composant logiciel enfichable MMC de Microsoft Azure Backup.

    ![Résultat de la recherche](./media/backup-azure-backup-windows-server/result.png)

2. Cliquez sur **Planifier la sauvegarde**.

    ![Planification d’une sauvegarde](./media/backup-azure-backup-windows-server/schedulebackup.png)

3. Sélectionnez les éléments que vous souhaitez sauvegarder. Azure Backup sur un serveur/client Windows (c’est-à-dire sans System Center Data Protection Manager) permet de protéger des fichiers et dossiers.

    ![Éléments à sauvegarder](./media/backup-azure-backup-windows-server/items.png)

4. Spécifiez la planification de la sauvegarde et la stratégie de rétention qui sont expliquées en détail dans l’[article](backup-azure-backup-cloud-as-tape.md) suivant.

5. Choisissez la méthode d’envoi de la sauvegarde initiale. Vous choisissez d’effectuer l’amorçage initial en fonction de la quantité de données que vous voulez sauvegarder et de la vitesse de votre lien de téléchargement Internet. Si vous envisagez de sauvegarder plusieurs Go ou To de données sur une connexion avec une bande passante faible et une latence élevée, il est recommandé d’effectuer la sauvegarde initiale en envoyant un disque au centre de données Azure le plus proche. Il s’agit de l’option « Sauvegarde en mode hors connexion », traitée en détail dans cet [article](backup-azure-backup-import-export.md). Si vous avez suffisamment de bande passante, nous vous recommandons d’effectuer la sauvegarde initiale sur le réseau.

    ![Sauvegarde initiale](./media/backup-azure-backup-windows-server/initialbackup.png)

6. Une fois la planification de la sauvegarde effectuée, retournez au composant logiciel enfichable MMC et cliquez sur **Sauvegarder maintenant** pour effectuer l’amorçage initial sur le réseau.

    ![Sauvegarder maintenant](./media/backup-azure-backup-windows-server/backupnow.png)

7. Une fois l’amorçage initial terminé, la vue **Travaux** de la console Azure Backup indique l’état.

    ![RI terminé](./media/backup-azure-backup-windows-server/ircomplete.png)

## Étapes suivantes
- [Gérer un serveur Windows Server ou un client Windows](backup-azure-manage-windows-server.md)
- [Restaurer un serveur Windows Server ou un client Windows à partir d’Azure](backup-azure-restore-windows-server.md)
- [Azure Backup - Forum Aux Questions](backup-azure-backup-faq.md)

<!---HONumber=Oct15_HO4-->
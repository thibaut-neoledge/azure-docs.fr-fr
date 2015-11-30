<properties
   pageTitle="Sauvegarder des fichiers et dossiers d’un serveur Windows ou d’un ordinateur client Windows sur Azure | Microsoft Azure"
   description="Cette procédure simple permet de sauvegarder un Windows Server ou un ordinateur client Windows sur Azure. Vous pouvez sauvegarder des fichiers et dossiers Windows dans le cloud en quelques étapes simples."
   services="backup"
   documentationCenter=""
   authors="aashishr"
   manager="jwhit"
   editor=""
   keywords="sauvegarde de windows server ; sauvegarder windows server"/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/17/2015" ms.author="jimpark"; "aashishr"/>

# Sauvegarder des fichiers et dossiers d’un serveur Windows ou d’un ordinateur client Windows sur Azure
Cette procédure simple perme de sauvegarder facilement des dossiers et fichiers Windows Azure. Si vous ne l’avez pas déjà fait, effectuez la [configuration requise](backup-configure-vault.md#before-you-start) pour préparer votre environnement pour la sauvegarde de votre machine Windows avant de continuer.

## Fichiers de sauvegarde
1. Une fois l’ordinateur inscrit, ouvrez le composant logiciel enfichable MMC de Microsoft Azure Backup.

    ![Résultat de la recherche](./media/backup-azure-backup-windows-server/result.png)

2. Cliquez sur **Planifier la sauvegarde**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-azure-backup-windows-server/schedulebackup.png)

3. Sélectionnez les éléments que vous souhaitez sauvegarder. Azure Backup sur un serveur/client Windows (c’est-à-dire sans System Center Data Protection Manager) permet de protéger des fichiers et dossiers.

    ![Éléments de sauvegarde de Windows Server](./media/backup-azure-backup-windows-server/items.png)

4. Spécifiez la planification de la sauvegarde et la stratégie de rétention qui sont expliquées en détail dans l’[article](backup-azure-backup-cloud-as-tape.md) suivant.

5. Choisissez la méthode d’envoi de la sauvegarde initiale. Vous choisissez d’effectuer l’amorçage initial en fonction de la quantité de données que vous voulez sauvegarder et de la vitesse de votre lien de téléchargement Internet. Si vous envisagez de sauvegarder plusieurs Go ou To de données sur une connexion avec une bande passante faible et une latence élevée, il est recommandé d’effectuer la sauvegarde initiale en envoyant un disque au centre de données Azure le plus proche. Il s’agit de l’option « Sauvegarde en mode hors connexion », traitée en détail dans cet [article](backup-azure-backup-import-export.md). Si vous avez suffisamment de bande passante, nous vous recommandons d’effectuer la sauvegarde initiale sur le réseau.

    ![Sauvegarde initiale de Windows Server](./media/backup-azure-backup-windows-server/initialbackup.png)

6. Une fois la planification de la sauvegarde effectuée, retournez au composant logiciel enfichable MMC et cliquez sur **Sauvegarder maintenant** pour effectuer l’amorçage initial sur le réseau.

    ![Sauvegarder Windows Server maintenant](./media/backup-azure-backup-windows-server/backupnow.png)

7. Une fois l’amorçage initial terminé, la vue **Travaux** de la console Azure Backup indique l’état.

    ![RI terminé](./media/backup-azure-backup-windows-server/ircomplete.png)

## Étapes suivantes
- [Gérer un serveur Windows Server ou un client Windows](backup-azure-manage-windows-server.md)
- [Restaurer un serveur Windows Server ou un client Windows à partir d’Azure](backup-azure-restore-windows-server.md)
- [Azure Backup - Forum Aux Questions](backup-azure-backup-faq.md)

<!---HONumber=Nov15_HO4-->
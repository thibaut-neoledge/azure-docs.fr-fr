<properties pageTitle="Azure Backup - Sauvegarde et restauration à partir d’un serveur/client Windows" | Description Microsoft Azure="Découvrez comment sauvegarder et restaurer des fichiers à partir d’un serveur/client Windows.. Cet article décrit également la récupération sur un autre serveur.="backup" documentationCenter="" authors="Jim-Parker" manager="jwhit" editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="07/01/2015" ms.author="jimpark"; "aashishr"/>

# Sauvegarde et restauration à partir d’un serveur Windows ou d’un ordinateur client Windows
Cet article décrit les étapes requises pour effectuer une sauvegarde à partir d’un serveur Windows ou d’un ordinateur client Windows. Il décrit également les étapes requises pour restaurer les fichiers sauvegardés sur le même ordinateur et pour restaurer les fichiers sauvegardés sur un autre ordinateur.

## Fichiers de sauvegarde

1. Une fois l’ordinateur inscrit, ouvrez le composant logiciel enfichable MMC de Microsoft Azure Backup.

    ![Résultat de la recherche](./media/backup-azure-backup-and-recover/result.png)

2. Cliquez sur **Planifier la sauvegarde**

    ![Planification d’une sauvegarde](./media/backup-azure-backup-and-recover/schedulebackup.png)

3. Sélectionnez les éléments que vous voulez sauvegarder. Azure Backup sur un serveur/client Windows (c’est-à-dire sans System Center Data Protection Manager) permet de protéger des fichiers et dossiers.

    ![Éléments à sauvegarder](./media/backup-azure-backup-and-recover/items.png)

4. Spécifiez la planification de la sauvegarde et la stratégie de rétention qui sont expliquées en détail dans l’[article](backup-azure-backup-cloud-as-tape.md) suivant.

5. Choisissez la méthode d’envoi de la sauvegarde initiale. Vous choisissez d’effectuer l’amorçage initial en fonction de la quantité de données que vous voulez sauvegarder et de la vitesse de votre lien de téléchargement Internet. Si vous envisagez de sauvegarder plusieurs Go ou To de données sur une connexion avec une bande passante faible et une latence élevée, il est recommandé d’effectuer la sauvegarde initiale en envoyant un disque au centre de données Azure le plus proche. Il s’agit de l’option « Sauvegarde en mode hors connexion », traitée en détail dans cet [article](backup-azure-backup-import-export.md). Si vous avez suffisamment de bande passante, nous vous recommandons d’effectuer la sauvegarde initiale sur le réseau.

    ![Sauvegarde initiale](./media/backup-azure-backup-and-recover/initialbackup.png)

6. Une fois le processus terminé, retournez au composant logiciel enfichable MMC et cliquez sur **Sauvegarder maintenant** pour effectuer l’amorçage initial sur le réseau.

    ![Sauvegarder maintenant](./media/backup-azure-backup-and-recover/backupnow.png)

7. Une fois l’amorçage initial terminé, la vue **Travaux** de la console Azure Backup indique l’état.

    ![RI terminé](./media/backup-azure-backup-and-recover/ircomplete.png)

## Récupération des données sur le même ordinateur
Si vous avez supprimé accidentellement un fichier et que vous voulez restaurer un fichier/volume sur le même ordinateur (à partir duquel la sauvegarde est effectuée), les étapes suivantes vous aident à récupérer les données.

1. Ouvrez le composant logiciel enfichable **Microsoft Azure Backup**.

2. Cliquez sur **Récupérer des données** pour lancer le flux de travail.

    ![Récupérer des données](./media/backup-azure-backup-and-recover/recover.png)

3. Sélectionnez l’option **Ce serveur (*nomdevotremachine*)** pour restaurer le fichier sauvegardé sur le même ordinateur.

    ![Même ordinateur](./media/backup-azure-backup-and-recover/samemachine.png)

4. Vous pouvez choisir **Naviguer jusqu’aux fichiers** ou **Rechercher des fichiers**. Conservez l’option par défaut si vous envisagez de restaurer un ou plusieurs fichiers dont le chemin est connu. Si vous n’êtes pas certain de la structure de dossiers, mais que vous voulez rechercher un fichier, choisissez l’option **Rechercher des fichiers**. Dans cette section, nous choisissons l’option par défaut.

    ![Parcourir les fichiers](./media/backup-azure-backup-and-recover/browseandsearch.png)

5. Sélectionnez le volume à partir duquel vous voulez restaurer le fichier. Cet écran vous permet d’effectuer une restauration de n’importe quel point dans le temps. Les dates qui s’affichent en **gras** dans le contrôle Calendrier indiquent la disponibilité d’un point de restauration. Une fois qu’une date est sélectionnée, en fonction de votre planification de sauvegarde (et de la réussite d’une opération de sauvegarde), vous pouvez sélectionner un point dans le temps dans la liste déroulante **Heure**.

    ![Volume et date](./media/backup-azure-backup-and-recover/volanddate.png)

6. Sélectionnez les éléments que vous souhaitez restaurer. Vous pouvez sélectionner plusieurs fichiers/dossiers à restaurer.

    ![Sélectionner des fichiers](./media/backup-azure-backup-and-recover/selectfiles.png)

7. Spécifiez les paramètres de récupération.

    ![Options de récupération](./media/backup-azure-backup-and-recover/recoveroptions.png)

  - Vous avez la possibilité d’effectuer la restauration à l’emplacement d’origine (dans lequel le fichier/dossier sera remplacé) ou dans un autre emplacement sur le même ordinateur.
  - Si le fichier/dossier que vous voulez restaurer existe dans l’emplacement cible, vous avez la possibilité de créer des copies (deux versions du même fichier), de remplacer les fichiers dans l’emplacement cible ou d’ignorer la récupération des fichiers qui existent dans la cible.
  - Il est vivement recommandé de conserver l’option par défaut qui consiste à restaurer les ACL sur les fichiers récupérés.

8. Une fois ces entrées fournies, le flux de travail de récupération démarre et restaure les fichiers sur cet ordinateur.

## Récupération sur un autre ordinateur
Si votre serveur entier est perdu, vous pouvez toujours récupérer le fichier/volume sur un autre ordinateur. Les étapes suivantes illustrent le flux de travail.

La nomenclature utilisée dans les étapes est la suivante : - *Ordinateur source* : ordinateur d’origine à partir duquel la sauvegarde a été effectuée et qui est actuellement indisponible. - *Ordinateur cible* : ordinateur sur lequel les données sont récupérées. - *Exemple d’archivage* – archivage de sauvegarde dans lequel l’*ordinateur source* et l’*ordinateur cible* sont enregistrés. <br/>

> [AZURE.NOTE]Les sauvegardes effectuées à partir d’un ordinateur ne peuvent pas être restaurées sur un ordinateur qui exécute une version antérieure du système d’exploitation. Par exemple, si les sauvegardes sont effectuées à partir d’un ordinateur Windows 7, elles peuvent être restaurées sur un ordinateur Windows 8 ou supérieur. Toutefois l’inverse n’est pas vrai.

1. Ouvrez le composant logiciel enfichable **Microsoft Azure Backup** sur l’*ordinateur cible*.

2. Vérifiez que l’*ordinateur cible* et l’*ordinateur source* sont restaurés dans le même archivage de sauvegarde.

3. Cliquez sur **Récupérer des données** pour lancer le flux de travail.

    ![Récupérer des données](./media/backup-azure-backup-and-recover/recover.png)

4. Sélectionnez **Un autre serveur**

    ![Autre serveur](./media/backup-azure-backup-and-recover/anotherserver.png)

5. Fournissez le fichier d’informations d’identification de coffre qui correspond à l’*exemple d’archivage*. Si le fichier d’informations d’identification de coffre n’est pas valide (ou a expiré), téléchargez un nouveau fichier d’informations d’identification de coffre à partir de l’*exemple d’archivage* dans le portail Azure. Une fois que le fichier d’informations d’identification de coffre est fourni, l’archivage de sauvegarde correspondant au fichier d’informations d’identification de coffre s’affiche.

6. Sélectionnez l’*ordinateur source* dans la liste des ordinateurs affichés.

    ![Liste des ordinateurs](./media/backup-azure-backup-and-recover/machinelist.png)

7. Comme précédemment, sélectionnez l’option **Rechercher des fichiers** ou **Naviguer jusqu’aux fichiers**. Dans cette section, nous utilisons l’option **Rechercher des fichiers**.

    ![action](./media/backup-azure-backup-and-recover/search.png)

8. Dans l’écran suivant, sélectionnez le volume et la date. Recherchez le nom de fichier/dossier que vous souhaitez restaurer.

    ![Éléments de recherche](./media/backup-azure-backup-and-recover/searchitems.png)

9. Sélectionnez l’emplacement vers lequel les fichiers doivent être restaurés.

    ![Emplacement de restauration](./media/backup-azure-backup-and-recover/restorelocation.png)

10. Fournissez la phrase secrète de chiffrement qui a été fournie pendant l’inscription de l’*ordinateur source* dans l’*exemple d’archivage*.

    ![Chiffrement](./media/backup-azure-backup-and-recover/encryption.png)

11. Une fois l’entrée fournie, cliquez sur le bouton **Récupérer** qui déclenche la restauration des fichiers de sauvegarde dans la destination fournie.

## Étapes suivantes
- [Azure Backup - Forum Aux Questions](backup-azure-backup-faq.md)

<!---HONumber=July15_HO4-->
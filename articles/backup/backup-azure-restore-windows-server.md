<properties
   pageTitle="Restauration de données sur un serveur Windows ou un ordinateur client Windows à partir d’Azure à l’aide du modèle de déploiement Resource Manager | Microsoft Azure"
   description="Découvrez comment restaurer des fichiers à partir d’un serveur/client Windows."
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
	 ms.date="05/10/2016"
	 ms.author="trinadhk; jimpark; markgal;"/>

# Restauration de fichiers sur un serveur Windows ou un ordinateur client Windows à l’aide du modèle de déploiement Resource Manager

> [AZURE.SELECTOR]
- [Portail Azure](backup-azure-restore-windows-server.md)
- [Portail classique](backup-azure-restore-windows-server-classic.md)

Cet article présente les étapes requises pour effectuer deux types d’opérations de restauration :

- Restaurer des données sur l’ordinateur à partir duquel les sauvegardes ont été effectuées.
- Restaurer les données sur n’importe quel autre ordinateur.

Dans les deux cas, les données sont récupérées à partir du coffre Azure Recovery Services.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique.

## Récupération des données sur le même ordinateur
Si vous avez supprimé accidentellement un fichier et que vous voulez le restaurer sur le même ordinateur (à partir duquel la sauvegarde est effectuée), les étapes suivantes vous aident à récupérer les données.

1. Ouvrez le composant logiciel enfichable **Microsoft Azure Backup**.
2. Cliquez sur **Récupérer des données** pour lancer le flux de travail.

    ![Récupérer des données](./media/backup-azure-restore-windows-server/recover.png)

3. Sélectionnez l’option **Ce serveur (*nomdevotremachine*)** pour restaurer le fichier sauvegardé sur le même ordinateur.

    ![Même ordinateur](./media/backup-azure-restore-windows-server/samemachine.png)

4. Vous pouvez choisir **Naviguer jusqu’aux fichiers** ou **Rechercher des fichiers**.

    Conservez l’option par défaut si vous envisagez de restaurer un ou plusieurs fichiers dont le chemin est connu. Si vous n’êtes pas certain de la structure de dossiers, mais que vous voulez rechercher un fichier, choisissez l’option **Rechercher des fichiers**. Dans cette section, nous choisissons l’option par défaut.

    ![Parcourir les fichiers](./media/backup-azure-restore-windows-server/browseandsearch.png)

5. Sélectionnez le volume à partir duquel vous voulez restaurer le fichier.

    Vous pouvez restaurer le fichier à partir de n’importe quel point dans le temps. Les dates qui s’affichent en **gras** dans le contrôle Calendrier indiquent la disponibilité d’un point de restauration. Une fois qu’une date est sélectionnée, en fonction de votre planification de sauvegarde (et de la réussite d’une opération de sauvegarde), vous pouvez sélectionner un point dans le temps dans la liste déroulante **Heure**.

    ![Volume et date](./media/backup-azure-restore-windows-server/volanddate.png)

6. Sélectionnez les éléments à restaurer. Vous pouvez sélectionner plusieurs fichiers/dossiers à restaurer.

    ![Sélectionner des fichiers](./media/backup-azure-restore-windows-server/selectfiles.png)

7. Spécifiez les paramètres de récupération.

    ![Options de récupération](./media/backup-azure-restore-windows-server/recoveroptions.png)

  - Vous avez la possibilité d’effectuer la restauration à l’emplacement d’origine (dans lequel le fichier/dossier sera remplacé) ou dans un autre emplacement sur le même ordinateur.
  - Si le fichier/dossier que vous voulez restaurer existe dans l’emplacement cible, vous avez la possibilité de créer des copies (deux versions du même fichier), de remplacer les fichiers dans l’emplacement cible ou d’ignorer la récupération des fichiers qui existent dans la cible.
  - Il est vivement recommandé de conserver l’option par défaut qui consiste à restaurer les ACL sur les fichiers récupérés.

8. Une fois ces entrées fournies, cliquez sur **Suivant**. Le flux de travail de récupération, qui consiste à restaurer les fichiers sur cet ordinateur, commence.

## Récupération sur un autre ordinateur
Si votre serveur entier est perdu, vous pouvez toujours récupérer les données d’Azure Backup sur un autre ordinateur. Les étapes suivantes illustrent le flux de travail.

Les termes ci-après sont utilisés pour cette procédure :

- *Ordinateur source* : ordinateur d’origine à partir duquel la sauvegarde a été effectuée et qui est actuellement indisponible.
- *Ordinateur cible* : ordinateur sur lequel les données sont récupérées.
- *Exemple d’archivage* : coffre Recovery Services dans lequel l’*ordinateur source* et l’*ordinateur cible* sont enregistrés. <br/>

> [AZURE.NOTE] Les sauvegardes effectuées à partir d’un ordinateur ne peuvent pas être restaurées sur un ordinateur qui exécute une version antérieure du système d’exploitation. Par exemple, si les sauvegardes sont effectuées à partir d’un ordinateur Windows 7, elles peuvent être restaurées sur un ordinateur Windows 8 ou supérieur. Toutefois l’inverse n’est pas vrai.

1. Ouvrez le composant logiciel enfichable **Microsoft Azure Backup** sur l’*ordinateur cible*.
2. Vérifiez que l’*ordinateur cible* et l’*ordinateur source* sont inscrits auprès du même coffre Recovery Services.
3. Cliquez sur **Récupérer des données** pour lancer le flux de travail.

    ![Récupérer des données](./media/backup-azure-restore-windows-server/recover.png)

4. Sélectionnez **Un autre serveur**

    ![Autre serveur](./media/backup-azure-restore-windows-server/anotherserver.png)

5. Fournissez le fichier d’informations d’identification de coffre qui correspond à l’*exemple d’archivage*. Si le fichier d’informations d’identification de coffre n’est pas valide (ou a expiré), téléchargez un nouveau fichier d’informations d’identification de coffre à partir de l’*exemple d’archivage* dans le portail Azure. Une fois que le fichier d’informations d’identification de coffre est fourni, le coffre Recovery Services correspondant au fichier d’informations d’identification de coffre s’affiche.

6. Sélectionnez l’*ordinateur source* dans la liste des ordinateurs affichés.

    ![Liste des ordinateurs](./media/backup-azure-restore-windows-server/machinelist.png)

7. Sélectionnez l’option **Rechercher des fichiers** ou **Naviguer jusqu’aux fichiers**. Dans cette section, nous utilisons l’option **Rechercher des fichiers**.

    ![action](./media/backup-azure-restore-windows-server/search.png)

8. Dans l’écran suivant, sélectionnez le volume et la date. Recherchez le nom du fichier/dossier que vous souhaitez restaurer.

    ![Éléments de recherche](./media/backup-azure-restore-windows-server/searchitems.png)

9. Sélectionnez l’emplacement vers lequel les fichiers doivent être restaurés.

    ![Emplacement de restauration](./media/backup-azure-restore-windows-server/restorelocation.png)

10. Fournissez la phrase secrète de chiffrement qui a été fournie pendant l’inscription de l’*ordinateur source* dans l’*exemple d’archivage*.

    ![Chiffrement](./media/backup-azure-restore-windows-server/encryption.png)

11. Une fois l’entrée fournie, cliquez sur **Récupérer** pour déclencher la restauration des fichiers de sauvegarde dans la destination fournie.

## Étapes suivantes
- Maintenant que vous avez restauré vos fichiers et vos dossiers, vous pouvez [gérer vos sauvegardes](backup-azure-manage-windows-server.md).

<!---HONumber=AcomDC_0518_2016-->
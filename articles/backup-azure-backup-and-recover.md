<properties
	pageTitle="Sauvegarde - sauvegarde et restauration à partir d'un serveur Windows ou un Client Windows Azure"
	description="Découvrez comment sauvegarder et restaurer à partir d'un serveur Windows ou un Client Windows. Cet article décrit également la récupération sur un autre serveur"
	services="backup"
	documentationCenter=""
	authors="prvijay"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.date="04/02/2015"
	 ms.author="prvijay"/>

# Sauvegarde et restauration à partir d'un serveur Windows ou l'ordinateur du client Windows
Cet article décrit les étapes requises pour sauvegarde à partir d'un serveur Windows ou un ordinateur client de Windows. Elle décrit également les étapes nécessaires à la restauration sauvegardés des fichiers sur le même ordinateur et les étapes requises pour restaurer les fichiers sauvegardés sur un autre ordinateur.

## Fichiers de sauvegarde
1. Une fois que l'ordinateur est inscrit, ouvrez le composant logiciel enfichable mmc de Microsoft Azure Backup. <br/> ![Résultat de la recherche][1]

2. Cliquez sur **planifier la sauvegarde** <br/> ![Planifier la sauvegarde][2]

3. Sélectionnez les éléments que vous souhaitez sauvegarder. Sauvegarde Azure sur le Client Windows Server et Windows (c'est-à-dire sans System Center Data Protection Manager) permet de protéger les fichiers et dossiers. <br/> ![Éléments à sauvegarder][3]

4. Spécifier la stratégie de planification et de rétention sauvegarde qui est expliquée en détail dans le tableau ci-dessous [article](backup-azure-backup-cloud-as-tape.md)

5. Choisissez la méthode d'envoi de la sauvegarde initiale. Votre choix de la fin de l'amorçage initial dépend de la quantité de données à la sauvegarde et la vitesse de votre lien de téléchargement internet. Si vous envisagez de sauvegarde Go to de données sur une latence élevée, à connexion à bande passante faible, il est recommandé d'effectuer la sauvegarde initiale par l'envoi d'un disque au centre de données Azure le plus proche. Est appelé « Sauvegarde en mode hors connexion » et est traité en détail dans cette [article](https://msdn.microsoft.com/library/azure/dn894419.aspx). Si vous avez suffisamment de bande passante connexion, il est recommandé d'effectuer la sauvegarde initiale sur le réseau. <br/> ![Sauvegarde initiale][4]

6. Une fois le workflow terminé, accédez à revenir à la console mmc de composant logiciel enfichable, puis cliquez sur **Sauvegarder maintenant** pour effectuer l'amorçage initial sur le réseau. <br/> ![Sauvegarde maintenant][5]

7. Une fois l'amorçage initial est terminé, le **travaux** affichage dans la console de sauvegarde Azure indique l'état. <br/> ![IR terminée][6]

## Récupérer des données sur le même ordinateur
Si vous avez supprimé accidentellement un fichier et si vous souhaitez restaurer un fichier/volume dans le même ordinateur (à partir de laquelle la sauvegarde est effectuée), les étapes suivantes seraient vous aider à récupérer les données.

1. Ouvrez le **Microsoft Azure Backup** dans l'alignement.

2. Cliquez sur **récupérer des données** pour lancer le flux de travail. <br/> ![Récupérer des données][7]

3. Sélectionnez **ce serveur (* nomdevotremachine *)** option que vous projetez de restaurer la sauvegarde des fichiers sur le même ordinateur. <br/> ![Même machine][8]

4. Vous pouvez choisir de **Parcourir vos fichiers** ou **recherche de fichiers**. Laissez l'option par défaut si vous envisagez de restaurer un ou plusieurs fichiers dont le chemin est connu. Si vous êtes pas certain de la structure de dossiers, mais que vous souhaitez rechercher un fichier, choisissez le **recherche de fichiers** option. Dans cette section, nous allons poursuivre avec l'option par défaut. <br/> ![Parcourir les fichiers][9]

5. Dans l'écran suivant, sélectionnez le volume à partir duquel vous souhaitez restaurer le fichier. Permet de l'écran que vous restaurez à partir de n'importe quel point dans le temps. Les dates qui figurent dans le **gras** dans le contrôle calendar, indiquer la disponibilité d'un point de restauration. Une fois qu'une date est sélectionnée, en fonction de votre planification de sauvegarde (et la réussite d'une opération de sauvegarde), vous pouvez sélectionner un point dans le temps à partir de la **temps** déroulante. <br/> ![Volume et la Date][10]

6. Dans l'écran suivant, sélectionnez les éléments que vous souhaitez récupérer. Vous pouvez sélections plusieurs fichiers/dossiers que vous souhaitez restaurer. <br/> ![Sélectionnez les fichiers][11]

7. Dans l'écran suivant, spécifiez les paramètres de récupération. <br/> ![Options de récupération][12]
  + Vous avez une option de restauration à l'emplacement d'origine (dans lequel le fichier/dossier seront remplacé) ou vers un autre emplacement dans le même ordinateur.

  + Si le fichier/dossier que vous souhaitez restaurer, existe dans l'emplacement cible, vous avez la possibilité de créer des copies (deux versions du même fichier), ou remplacer les fichiers dans l'emplacement cible ou d'ignorer la récupération des fichiers qui existent dans la cible.

  + Il est vivement recommandé de laisser l'option par défaut de la restauration des ACL sur les fichiers qui ont été récupérés.

8. Une fois que ces entrées sont fournies, le flux de travail de récupération démarre qui restaure les fichiers à cet ordinateur.

## Récupérer vers un autre ordinateur
Si votre serveur entier est perdu, vous pouvez toujours récupérer le fichier/volume dans un autre ordinateur. Les étapes suivantes illustrent le flux de travail.

La nomenclature utilisée dans les étapes sont les suivantes: + *machine Source* – l'ordinateur d'origine à partir duquel la sauvegarde a été effectuée et qui est actuellement indisponible.

  + *De l'ordinateur cible* – l'ordinateur sur lequel les données sont récupérées.

  + *Coffre exemple* – coffre de sauvegarde l'auquel le *machine Source* et *de l'ordinateur cible* sont enregistrés. <br/>

> [AZURE.NOTE]Impossible de restaurer les sauvegardes effectuées à partir d'un ordinateur sur un ordinateur qui exécute une version antérieure du système d'exploitation. Par exemple, si les sauvegardes sont effectuées à partir d'un ordinateur Windows 7, il peut être restaurée sur un Windows 8 ou supérieur machine. Toutefois l'inverse n'est pas vraie.

1. Ouvrir le **Microsoft Azure Backup** ancrée dans le *de l'ordinateur cible*.

2. Vérifiez que le *de l'ordinateur cible* et *machine Source* sont restaurés dans le coffre de sauvegarde même (dans cet article - *coffre exemple*).

3. Cliquez sur **récupérer des données** pour lancer le flux de travail. <br/> ![Récupérer des données][7]

4. Sélectionnez **un autre serveur** <br/> ![Un autre serveur][13]

5. Fournissez le fichier d'informations d'identification de coffre qui correspond à la *coffre exemple*. Si le fichier d'informations d'identification de coffre est non valide (ou expiré), téléchargez un nouveau fichier d'informations d'identification de coffre à partir de la *coffre exemple* dans le portail Azure. Une fois que le fichier d'informations d'identification de coffre est fourni, le coffre de sauvegarde sur le fichier d'informations d'identification de coffre s'affiche.

6. Sélectionnez le *machine Source* dans la liste des ordinateurs affichées. <br/> ![Liste des ordinateurs][14]

7. Sélectionnez comme avant la **recherche de fichiers** ou **Parcourir vos fichiers** option. Dans cette section, nous allons utiliser le **recherche de fichiers** option. <br/> ![action][15]

8. Dans l'écran suivant, sélectionnez le volume et la date. Recherchez le nom de fichier/dossier que vous souhaitez restaurer. <br/> ![Éléments de recherche][16]

9. Sélectionnez l'emplacement vers lequel les fichiers doivent être restaurées. <br/> ![Emplacement de restauration][17]

10. Fournir la phrase secrète de chiffrement qui a été fournie pendant *l'ordinateur Source* inscription *coffre exemple*. <br/> ![Chiffrement][18]

Une fois l'entrée fournie, cliquez sur le **récupérer** bouton qui déclenche la restaure la sauvegarde des fichiers dans la destination fournis.

<!--Image references-->
[1]: ./media/backup-azure-backup-and-recover/result.png
[2]: ./media/backup-azure-backup-and-recover/schedulebackup.png
[3]: ./media/backup-azure-backup-and-recover/items.png
[4]: ./media/backup-azure-backup-and-recover/initialbackup.png
[5]: ./media/backup-azure-backup-and-recover/backupnow.png
[6]: ./media/backup-azure-backup-and-recover/ircomplete.png

[7]: ./media/backup-azure-backup-and-recover/recover.png
[8]: ./media/backup-azure-backup-and-recover/samemachine.png
[9]: ./media/backup-azure-backup-and-recover/browseandsearch.png
[10]: ./media/backup-azure-backup-and-recover/volanddate.png
[11]: ./media/backup-azure-backup-and-recover/selectfiles.png
[12]: ./media/backup-azure-backup-and-recover/recoveroptions.png

[13]: ./media/backup-azure-backup-and-recover/anotherserver.png
[14]: ./media/backup-azure-backup-and-recover/machinelist.png
[15]: ./media/backup-azure-backup-and-recover/search.png
[16]: ./media/backup-azure-backup-and-recover/searchitems.png
[17]: ./media/backup-azure-backup-and-recover/restorelocation.png
[18]: ./media/backup-azure-backup-and-recover/encryption.png

<!---HONumber=GIT-SubDir-->
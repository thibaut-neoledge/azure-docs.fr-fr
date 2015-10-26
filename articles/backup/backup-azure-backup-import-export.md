<properties
   pageTitle="Azure Backup - Sauvegarde hors connexion ou amorçage initial à l’aide du service Azure Import/Export | Microsoft Azure"
   description="Découvrez comment Azure Backup vous permet d’envoyer des données en dehors du réseau à l’aide du service Azure Import/Export. Cet article décrit l’amorçage hors connexion des données de sauvegarde initiales à l’aide du service Azure Import/Export."
   services="backup"
   documentationCenter=""
   authors="aashishr"
   manager="shreeshd"
   editor=""/>
<tags  ms.service="backup" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="storage-backup-recovery" ms.date="08/28/2015" ms.author="aashishr"; "jimpark"/>

# Flux de travail de la sauvegarde hors connexion dans Azure Backup

Azure Backup est profondément intégré au service Azure Import/Export qui vous permet de transférer rapidement les données de sauvegarde initiales. Si vous devez transférer des téraoctets de données de sauvegarde initiales sur un réseau à latence élevée et à faible bande passante, vous pouvez utiliser le service Azure Import/Export pour expédier la copie de sauvegarde initiale sur un ou plusieurs disques durs à un centre de données Azure. Cet article fournit une vue d’ensemble des étapes requises pour effectuer ce flux de travail.

## Vue d'ensemble

Avec Azure Backup et Azure Import/Export, vous téléchargez simplement et directement les données dans Azure en mode hors connexion par le biais de disques. Au lieu de transférer la copie initiale complète sur le réseau, les données de sauvegarde sont écrites dans un *emplacement intermédiaire*. L’emplacement intermédiaire peut être un dispositif de stockage attaché ou un partage réseau. Une fois la copie initiale terminée, l’*outil Azure Import/Export* écrit ces données sur un disque SATA qui est expédié au centre de données Azure. Selon la taille de la sauvegarde initiale, un ou plusieurs disques SATA peuvent être requis pour effectuer cette opération. L’outil Azure Import/Export prend en charge ces scénarios. Une fois les sauvegardes écrites sur le disque, celui-ci peut être envoyé au centre de données le plus proche pour le téléchargement sur Azure. Azure Backup copie ensuite les données de sauvegarde dans l’archivage de sauvegarde et des sauvegardes incrémentielles sont planifiées.

## Composants requis

1. Il est important de vous familiariser avec le flux de travail d’Azure Import/Export, qui est indiqué [ici](../storage-import-export-service.md).
2. Avant de lancer le flux de travail, assurez-vous qu’un archivage de sauvegarde Azure a été créé, que les informations d’identification de coffre ont été téléchargées, que l’agent Azure Backup a été installé sur votre serveur/client Windows ou votre serveur System Center Data Protection Manager (SCDPM) et que l’ordinateur est inscrit dans l’archivage de sauvegarde Azure.
3. Téléchargez les paramètres du fichier de publication Azure [ici](https://manage.windowsazure.com/publishsettings) sur l’ordinateur duquel vous prévoyez de sauvegarder les données.
4. Préparez un *emplacement intermédiaire*, qui peut être un partage réseau ou un disque supplémentaire sur l’ordinateur. Assurez-vous que l’emplacement intermédiaire dispose de suffisamment d’espace disque pour stocker votre copie initiale. Par exemple, si vous tentez de sauvegarder un serveur de fichiers de 500 Go, assurez-vous que la zone intermédiaire dispose d’au moins 500 Go (bien qu’une quantité inférieure soit utilisée). La zone intermédiaire est un « stockage temporaire » utilisé temporairement pendant ce flux de travail.
5. Enregistreur de disque SATA externe et disque SATA de 3,5 pouces externe : seuls les disques durs SATA II/III de 3,5 pouces sont pris en charge par le service Import/Export. Les disques durs de plus de 6 To ne sont pas pris en charge. Vous pouvez raccorder un disque SATA II/III par voie externe à la plupart des ordinateurs à l'aide d'un adaptateur USB SATA II/III. Consultez la documentation d’Azure Import/Export pour connaître la dernière série de disques pris en charge par le service.
6. Activez BitLocker sur l’ordinateur auquel est connecté l’enregistreur de disque SATA.
7. Téléchargez l’outil Azure Import/Export [ici](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) sur l’ordinateur auquel est connecté l’enregistreur de disque SATA.

## Workflow
Les informations fournies dans cette section concernent la fin du flux de travail **Sauvegarde hors connexion** afin que vos données puissent être remises à un centre de données Azure et téléchargées vers le stockage Azure. Si vous avez des questions sur le service Import ou sur un aspect du processus, consultez la vue d’ensemble du service Import traitée [ci-dessus](../storage-import-export-service.md).

### Lancer la sauvegarde hors connexion

1. Dans le cadre de la planification d’une sauvegarde, l’écran suivant s’affiche (dans Windows Server, le client Windows ou SCDPM).

    ![Écran d’importation](./media/backup-azure-backup-import-export/importscreen.png)

    L’écran correspondant dans SCDPM se présente comme suit. <br/> ![Écran d’importation DPM](./media/backup-azure-backup-import-export/dpmoffline.png)

    où :

    - **Emplacement intermédiaire** : fait référence à l’emplacement de stockage temporaire dans lequel la copie de sauvegarde initiale est écrite. Il peut s’agir d’un partage réseau ou de l’ordinateur local.
    - **Nom de la tâche d’importation Azure** : dans le cadre de ce flux de travail, vous devez créer une *tâche d’importation* dans le portail Azure (traitée dans la dernière partie du document). Fournissez une entrée que vous utiliserez également plus tard dans le portail Azure.
    - **Paramètres de publication Azure** : il s’agit d’un fichier XML qui contient des informations sur le profil de votre abonnement. Il contient également les informations d’identification sécurisées associées à votre abonnement. Le fichier peut être téléchargé [ici](https://manage.windowsazure.com/publishsettings). Fournissez le chemin d’accès local au fichier de paramètres de publication.
    - **ID d’abonnement Azure** : fournissez l’ID d’abonnement Azure dans lequel vous prévoyez de lancer la tâche d’importation Azure. Si vous avez plusieurs abonnements Azure, utilisez l’ID associé à la tâche d’importation.
    - **Compte de stockage Azure** : entrez le nom du compte de stockage Azure qui sera associé à cette tâche d’importation.
    - **Conteneur de stockage Azure** : entrez le nom de l’objet blob de stockage cible où les données de cette tâche seront importées.

2. Terminez le flux de travail et sélectionnez **Sauvegarder maintenant** dans la console MMC Azure Backup pour lancer la copie de sauvegarde en mode hors connexion. La sauvegarde initiale est écrite dans la zone intermédiaire à cette étape.

    ![Sauvegarder maintenant](./media/backup-azure-backup-import-export/backupnow.png)

    Le flux de travail correspondant dans SCDPM est activé en cliquant sur le **groupe de protection** et en choisissant l’option **Créer un point de récupération**. Ensuite, choisissez l’option **Protection en ligne**.

    ![Sauvegarder maintenant (DPM)](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Une fois l’opération terminée, des fichiers *.AIBBlob* et *.BaseBlob* sont créés dans l’emplacement intermédiaire.

    ![Sortie](./media/backup-azure-backup-import-export/opbackupnow.png)

### Préparer le disque SATA

1. Téléchargez l’[outil Microsoft Azure Import/Export](http://go.microsoft.com/fwlink/?linkid=301900&clcid=0x409) sur l’*ordinateur de copie*. Assurez-vous que l’emplacement intermédiaire (voir l’étape précédente) est accessible à partir de l’ordinateur sur lequel vous souhaitez exécuter le jeu de commandes suivant. Si nécessaire, l’ordinateur de copie peut être le même que l’ordinateur source.

2. Décompressez le fichier *WAImportExport.zip*. Exécutez l’outil *WAImportExport* pour formater le disque SATA, écrire les données de sauvegarde sur le disque SATA et les chiffrer. Avant d’exécuter la commande suivante, assurez-vous que BitLocker est activé sur l’ordinateur. <br/>

    *.\\WAImportExport.exe PrepImport /j:<*JournalFile*>.jrn /id: <*SessionId*> /sk:<*StorageAccountKey*> /BlobType:**PageBlob** /t:<*TargetDriveLetter*> /format /encrypt /srcdir:<*staging location*> /dstdir: <*DestinationBlobVirtualDirectory*>/*


| Paramètre | Description
|-------------|-------------|
| /j:<*JournalFile*>| Chemin d’accès du fichier journal. Chaque disque doit avoir un seul fichier journal. Notez que le fichier journal ne doit pas résider sur le disque cible. L’extension du fichier journal est .jrn, elle est créée à l’exécution de cette commande.|
|/id:<*SessionId*> | L’ID de session identifie une *session de copie*. Il est utilisé pour assurer la précision de la récupération d’une session de copie interrompue. Les fichiers copiés dans une session de copie sont stockés dans un répertoire dont le nom fait référence à l’ID de session sur le disque cible.|
| /sk:<*StorageAccountKey*> | Clé du compte de stockage vers lequel les données seront importées. |
| /BlobType | Spécifiez **PageBlob** (ce flux de travail aboutit uniquement si l’option PageBlob est spécifiée). Ce paramètre n’est pas l’option par défaut et doit être indiqué dans cette commande. |
|/t:<*TargetDriveLetter*> | Lettre de lecteur du disque dur cible pour la session de copie actuelle, sans les deux-points de fin.|
|/format | Spécifiez ce paramètre quand le disque doit être formaté, sinon, ignorez-le. Avant de formater le disque, l’outil demande confirmation à partir de la console. Pour supprimer la confirmation, spécifiez le paramètre /silentmode.|
|/encrypt | Spécifiez ce paramètre quand le disque n’a pas encore été chiffré avec BitLocker et doit être chiffré par l’outil. Si le disque a déjà été chiffré avec BitLocker, ignorez ce paramètre et spécifiez le paramètre /bk, en fournissant la clé BitLocker existante. Si vous spécifiez le paramètre /format, vous devez également spécifier le paramètre /encrypt. |
|/srcdir:<*SourceDirectory*> | Répertoire source qui contient les fichiers à copier sur le disque cible. Le chemin d’accès du répertoire doit être un chemin d’accès absolu (et non relatif).|
|/dstdir:<*DestinationBlobVirtualDirectory*> | Chemin d’accès au répertoire virtuel de destination dans votre compte de stockage Microsoft Azure. Veillez à utiliser des noms de conteneur valides quand vous spécifiez des répertoires virtuels de destination ou des objets blob. N’oubliez pas que les noms de conteneur doivent être en minuscules.|

  >[AZURE.NOTE]Un fichier journal est créé dans le dossier WAImportExport et capture toutes les informations du flux de travail. Vous aurez besoin de ce fichier lors de la création d’une tâche d’importation dans le portail Azure.

  ![Sortie PowerShell](./media/backup-azure-backup-import-export/psoutput.png)

### Créer la tâche d’importation dans le portail Azure
1. Accédez à votre compte de stockage dans le [portail de gestion](https://manage.windowsazure.com/) et cliquez sur **Import/Export**, puis sur **Créer une tâche d’importation** dans le volet des tâches.

    ![Portail](./media/backup-azure-backup-import-export/azureportal.png)

2. À l’étape 1 de l’Assistant, précisez que vous avez préparé votre lecteur et que le fichier journal du lecteur est disponible. À l’étape 2 de l’Assistant, fournissez les coordonnées de la personne responsable de cette tâche d’importation.
3. À l’étape 3, téléchargez les fichiers journaux de lecteur que vous avez obtenus dans la section précédente.
4. À l’étape 4, attribuez un nom descriptif à la tâche d’importation. Notez que le nom que vous entrez ne peut contenir que des minuscules, des chiffres, des tirets et des traits de soulignement, qu'il doit commencer par une lettre et qu'il ne peut pas contenir d'espaces. Le nom que choisissez vous servira à suivre la tâche pendant et après son exécution.
5. Sélectionnez ensuite la région du centre de données dans la liste. Cette dernière indique à quel centre de données et à quelle adresse vous devez expédier votre colis.

    ![Contrôleur de domaine](./media/backup-azure-backup-import-export/dc.png)

6. À l'étape 5, sélectionnez votre transporteur dans la liste, puis entrez son numéro de compte. Microsoft utilise ce compte pour réexpédier vos lecteurs une fois la tâche d’importation terminée.

7. Expédiez le disque et entrez le numéro de suivi pour suivre l’état de l’expédition. Dès que le disque arrive dans le centre de données, il est copié dans le compte de stockage et l’état est mis à jour.

    ![État Terminé](./media/backup-azure-backup-import-export/complete.png)

### Fin du flux de travail
Une fois que les données de sauvegarde initiales sont disponibles dans votre compte de stockage, l’agent Azure Backup copie le contenu des données de ce compte vers le compte de stockage de sauvegarde mutualisé. Lors de la prochaine sauvegarde planifiée, l’agent Azure Backup ajoute la sauvegarde incrémentielle à la copie de sauvegarde initiale.

## Étapes suivantes
- Pour toute question sur le flux de travail Azure Import/Export, reportez-vous à cet [article](../storage-import-export-service.md).
- Reportez-vous à la section Sauvegarde hors connexion du [Forum Aux Questions](backup-azure-backup-faq.md) Azure Backup pour toute question concernant le flux de travail.

<!---HONumber=Oct15_HO3-->
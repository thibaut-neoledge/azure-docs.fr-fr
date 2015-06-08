<properties
	pageTitle="Azure sauvegarde - sauvegarde hors connexion ou amorçage Initial à l'aide du Service d&#39;importation/exportation Azure"
	description="Découvrez comment Azure Backup vous permet d'envoyer des données sur le réseau à l'aide du service d'importation/exportation Azure. Cet article explique l'amorçage en mode hors connexion des données de sauvegarde initiales en utilisant le service Azure importation/exportation"
	services="backup"
	documentationCenter=""
	authors="prvijay"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="04/07/2015"
	ms.author="prvijay"/>

# Travail de sauvegarde hors connexion dans Azure Backup

Sauvegarde Azure est profondément intégrée avec le service d'importation/exportation Azure qui vous permet de transférer les données de sauvegarde initiales rapidement. Si vous avez des téraoctets de données de sauvegarde initiales qui doivent être transféré via une latence élevée et un réseau à faible bande passante, vous pouvez utiliser le service d'importation/exportation Azure à livrer la copie de sauvegarde initiale sur un ou plusieurs disques durs, à un centre de données Azure. Cet article fournit une vue d'ensemble des étapes requises pour terminer ce flux de travail.

## Vue d'ensemble

Avec la sauvegarde de Azure et Azure Import/Export, il est simple et directe pour télécharger les données vers Azure en mode hors connexion par le biais des disques. Au lieu de transférer la copie initiale complète sur le réseau, les données de sauvegarde sont écrite dans un *emplacement intermédiaire*. L'emplacement intermédiaire peut pourrait être un stockage en attachement direct ou un partage réseau. Une fois la copie initiale est terminée, à l'aide de la *outil Azure Import/Export*, ces données sont écrites sur un disque SATA qui est finalement livré au centre de données Azure. Selon la taille de la sauvegarde initiale, un ou plusieurs disques SATA peuvent être requis pour terminer cette opération. L'outil Azure Import/Export comptes pour ces scénarios. Après que les sauvegardes sont écrites sur le disque, il peuvent être envoyés à l'emplacement le plus proche du centre de données à télécharger vers Azure. Sauvegarde Azure copie ensuite les données de sauvegarde dans le coffre de sauvegarde et les sauvegardes incrémentielles sont planifiées.

## Composants requis

1. Il est important de vous familiariser avec le flux de travail Azure importation exportation répertoriée [ici](storage-import-export-service.md).

2. Avant de lancer le flux de travail, assurez-vous qu'un coffre de sauvegarde Azure a été créé, les informations d'identification de coffre ont été téléchargées, Azure Backup agent a été installé sur le serveur de votre Client de Windows Server et Windows ou System Center Data Protection Manager (SCDPM) et que l'ordinateur est inscrit dans le coffre de sauvegarde Azure.

3. Télécharger les paramètres à partir du fichier de publication Azure [ici](https://manage.windowsazure.com/publishsettings) sur l'ordinateur à partir de laquelle vous envisagez de nos données de sauvegarde.

4. Préparer un *emplacement intermédiaire* qui peut être un partage réseau ou un disque supplémentaire sur l'ordinateur. Assurez-vous que l'emplacement intermédiaire dispose de suffisamment d'espace disque pour stocker votre copie initiale. Pour par exemple, si vous tentez de sauvegarder un serveur de fichiers de 500 Go, assurez-vous que la zone de transit est au moins de 500 Go (bien qu'une quantité moindre sera utilisée). La zone de transit est « stockage temporaire » et est utilisée temporairement au cours de ce flux de travail.

5. Enregistreur de disque SATA externe et un disque SATA de 3,5 pouces externe. seuls les disques durs SATA II/III de 3,5 pouces sont pris en charge par le service Import/Export. Les disques durs de plus de 4 To ne sont pas pris en charge. Vous pouvez raccorder un disque SATA II/III par voie externe à la plupart des ordinateurs à l'aide d'un adaptateur USB SATA II/III. Consultez la documentation d'importation/exportation Azure pour la dernière série de lecteurs qui sont pris en charge par le service.

5. Activer BitLocker sur l'ordinateur auquel est connecté l'enregistreur de disque SATA.

6. Télécharger l'outil Azure Import/Export [ici](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) à l'ordinateur auquel est connecté l'enregistreur de disque SATA.


## Workflow
Les informations fournies dans cette section concerne la fin de la **sauvegarde hors connexion** workflow afin de vos données peuvent être remises à un centre de données Azure et téléchargées vers le stockage Azure. Si vous avez des questions sur le service d'importation ou de n'importe quel aspect du processus, consultez la vue d'ensemble du service importation référencée [ci-dessus](storage-import-export-service.md).

### Initier la sauvegarde hors connexion

1. Dans le cadre de la planification d'une sauvegarde, vous rencontrerez l'écran suivant (dans Windows Server, Windows client ou SCDPM). <br/> ![ImportScreen][1]

  L'écran coresponding dans SCDPM se présente comme suit. <br/> ![Écran d'importation de DPM][8]

où :

+ **Emplacement intermédiaire** -fait référence à l'emplacement de stockage temporaire dans lequel la copie de sauvegarde initiale est écrit. Cela peut être sur un partage réseau ou sur l'ordinateur local.

+ **Nom de travail d'importation azure** -dans le cadre de la fin de ce flux de travail, vous devez créer un *travail d'importation* dans le portail Azure (traité dans la dernière partie du document). Fournir une entrée que vous envisagez d'utiliser plus tard dans le portail de Azure.

+ **Les paramètres de publication azure** -il s'agit d'un fichier XML qui contient des informations sur le profil de votre abonnement. Il contient également des informations d'identification sécurisées associées à votre abonnement. Le fichier peut être téléchargé à partir de [ici](https://manage.windowsazure.com/publishsettings). Fournir le chemin d'accès local au fichier de paramètres de publication.

+ **ID d'abonnement azure** -fournir l'id d'abonnement Azure dans lequel vous envisagez de lancer la tâche d'importation Azure. Si vous avez plusieurs abonnements Azure, utilisez l'ID associé à la tâche d'importation.

+ **Compte de stockage azure** -Entrez le nom du compte de stockage Azure qui sera associé à cette tâche d'importation.

+ **Conteneur de stockage azure** -Entrez le nom de l'objet blob de stockage cible où les données de ce travail seront importées.


Terminer le flux de travail et sélectionnez **sauvegarde maintenant** dans la console mmc sauvegarde Azure, pour lancer la copie de sauvegarde en mode hors connexion. La sauvegarde initiale est écrit dans la zone de transit dans le cadre de cette étape.<br/>

  ![Sauvegarde maintenant][2]

Le flux de travail correspondant dans SCDPM est activé en cliquant sur le **groupe de Protection** et en choisissant le **créer un point de récupération** option. Cela est suivi en choisissant le **Protection en ligne** option.<br/> ![Sauvegarde DPM maintenant][9]

Une fois l'opération terminée, un *. AIBBlob* et *. BaseBlob* fichier est créé dans l'emplacement intermédiaire. <br/> ![Sortie][3]

### Préparer le disque SATA

1. Téléchargez le [Microsoft Azure Import/Export outil](http://go.microsoft.com/fwlink/?linkid=301900&clcid=0x409) pour le *ordinateur de copie*. Assurez-vous que l'emplacement intermédiaire (à l'étape précédente) est accessible à partir de l'ordinateur dans lequel vous souhaitez exécuter le jeu de commandes suivant. Si nécessaire, l'ordinateur de copie peut être le même que l'ordinateur source.

2. Décompressez le *WAImportExport.zip* fichier. Exécutez le *WAImportExport* outil formater le lecteur SATA, écrire les données de sauvegarde sur le disque SATA et chiffrer. Avant d'exécuter la commande suivante, assurez-vous que BitLocker est activé sur l'ordinateur. <br/>

*.\\WAImportExport.exe PrepImport /j: < * JournalFile*>/ID .jrn : < * SessionId * > /sk: < * StorageAccountKey * > /BlobType: ** PageBlob ** / t: < * TargetDriveLetter * > format /Encrypt /srcdir : < * intermédiaire emplacement * > /dstdir : < * DestinationBlobVirtualDirectory * > / *


| Paramètre | Description|
|-------------|-------------|
| /j: < * JournalFile * >| Le chemin d'accès du fichier journal. Chaque lecteur doit avoir exactement un fichier journal. Notez que le fichier journal ne doit pas résider sur le lecteur cible. L'extension de fichier journal est .jrn et est créée dans le cadre de l'exécution de cette commande.|
|/ ID : < * SessionId * > | La session ID identifie une *session de copie*. Il est utilisé pour assurer la récupération précise d'une session de copie interrompue. Les fichiers sont copiés dans une session de copie sont stockés dans un répertoire nommé d'après l'ID de session sur le lecteur cible.|
| /SK: < * StorageAccountKey * > | La clé de compte pour le compte de stockage vers lequel les données seront importées. |
| / BlobType | Spécifier **PageBlob**, ce flux de travail réussit uniquement si l'option PageBlob est spécifiée. Cela n'est pas l'option par défaut et doit être indiquée dans cette commande. |
|/ t: < * TargetDriveLetter * > | La lettre de lecteur du disque dur cible pour la session de copie en cours, sans les deux-points de fin.|
|/ format | Spécifiez ce paramètre lorsque le lecteur doit être formaté ; dans le cas contraire, l'omettre. Avant que l'outil de formatage du disque, il demande une confirmation à partir de la console. Pour supprimer la confirmation, spécifiez le paramètre /silentmode.|
|/ chiffrer | Spécifiez ce paramètre lorsque le lecteur n'a pas encore été chiffré avec BitLocker et doit être chiffré par l'outil. Si le lecteur a déjà été chiffré avec BitLocker, omettez ce paramètre, puis spécifiez le paramètre /bk, en fournissant la clé BitLocker existante. Si vous spécifiez le paramètre/Format, vous devez également spécifier le / crypter le paramètre. |
|/srcdir : < * SourceDirectory * > | Le répertoire source qui contient les fichiers à copier sur le lecteur cible. Le chemin d'accès doit être un chemin d'accès absolu (pas un chemin d'accès relatif).|
|/dstdir : < * DestinationBlobVirtualDirectory * > | Le chemin d'accès au répertoire virtuel de destination dans votre compte de stockage Microsoft Azure. Veillez à utiliser des noms de conteneur valides lorsque vous spécifiez des répertoires virtuels de destination ou des objets BLOB. N'oubliez pas que les noms de conteneur doivent être en minuscules.|

  >[AZURE.NOTE]Un fichier journal est créé dans le dossier WAImportExport qui capture les informations du flux de travail entiers. Vous aurez besoin de ce fichier lors de la création d'un travail d'importation dans le portail Azure.

  ![Sortie de PowerShell][4]

### Créer le travail d'importation dans le portail Azure
1. Accédez à votre compte de stockage dans le [portail de gestion](https://manage.windowsazure.com/), puis cliquez sur **Import/Export** puis, dans **travail d'importation créer** dans le volet Office. <br/> ![Portail][5]

2. À l’étape 1 de l’Assistant, précisez que vous avez préparé votre lecteur et que le fichier journal du lecteur est disponible. À l'étape 2 de l'Assistant, fournissez les coordonnées de la personne responsable de cette tâche d'importation.

3. À l'étape 3, téléchargez les fichiers de journal de lecteur que vous avez obtenue au cours de la section précédente.

4. À l’étape 4, attribuez un nom descriptif à la tâche d’importation. Notez que le nom que vous entrez ne peut contenir que des minuscules, des chiffres, des tirets et des traits de soulignement, qu'il doit commencer par une lettre et qu'il ne peut pas contenir d'espaces. Le nom que choisissez vous servira à suivre la tâche pendant et après son exécution.

5. Sélectionnez ensuite la région du centre de données dans la liste. La région de centre de données indique le centre de données et l'adresse à laquelle vous devez expédier votre package. <br/> ![Contrôleur de domaine][6]

6. À l’étape 5, sélectionnez votre transporteur dans la liste, puis entrez son numéro de compte. Microsoft utilise ce compte pour réexpédier vos lecteurs une fois la tâche d’importation terminée.

7. Expédier le disque et entrez le numéro de suivi pour suivre l'état de l'expédition. Une fois le disque arrive dans le centre de données, il est copié dans le compte de stockage et l'état est mis à jour. <br/>

![État terminé][7]

### À la fin du flux de travail
Une fois que les données de sauvegarde initiales sont disponibles dans votre compte de stockage, l'agent de sauvegarde Azure copie le contenu des données à partir de ce compte au compte de stockage de sauvegarde avec clients multiples. Dans la prochaine heure de sauvegarde planifiée, l'agent de sauvegarde Azure effectue la sauvegarde incrémentielle sur la copie de sauvegarde initiale.

## Étapes suivantes
+ Pour toute question sur le flux de travail d'importation/exportation Azure, reportez-vous à ce [article](storage-import-export-service.md).

+ Reportez-vous à la section sauvegarde hors connexion de la sauvegarde Azure [FAQ](backup-azure-backup-faq.md) pour toute question concernant le flux de travail

<!--Image references-->
[1]: ./media/backup-azure-backup-import-export/importscreen.png
[2]: ./media/backup-azure-backup-import-export/backupnow.png
[3]: ./media/backup-azure-backup-import-export/opbackupnow.png
[4]: ./media/backup-azure-backup-import-export/psoutput.png
[5]: ./media/backup-azure-backup-import-export/azureportal.png
[6]: ./media/backup-azure-backup-import-export/dc.png
[7]: ./media/backup-azure-backup-import-export/complete.png
[8]: ./media/backup-azure-backup-import-export/dpmoffline.png
[9]: ./media/backup-azure-backup-import-export/dpmbackupnow.png

<!---HONumber=GIT-SubDir-->
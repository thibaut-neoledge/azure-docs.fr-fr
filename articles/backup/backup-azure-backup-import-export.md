<properties
   pageTitle="Azure Backup - Sauvegarde hors connexion ou amorçage initial à l’aide du service Azure Import/Export | azure.microsoft.com/ Azure"
   description="Découvrez comment Azure Backup vous permet d’envoyer des données en dehors du réseau à l’aide du service Azure Import/Export. Cet article décrit l’amorçage hors connexion des données de sauvegarde initiales à l’aide du service Azure Import/Export."
   services="backup"
   documentationCenter=""
   authors="saurabhsensharma"
   manager="shivamg"
   editor=""/>
<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="08/16/2016"
   ms.author="jimpark;saurabhsensharma;nkolli;trinadhk"/>

# Flux de travail de la sauvegarde hors connexion dans Azure Backup
Azure Backup offre plusieurs fonctionnalités intégrées pour enregistrer les coûts de stockage et de réseau pendant les sauvegardes « complètes » initiales des données dans Azure. Les sauvegardes « complètes » initiales transfèrent généralement de grandes quantités de données et, par conséquent, nécessitent davantage de bande passante, en comparaison avec les sauvegardes suivantes qui ne transfèrent les données deltas/incrémentielles. Azure Backup ne se contente pas de compresser les sauvegardes initiales. Par le biais du processus d’amorçage hors connexion, il fournit également un mécanisme pour télécharger les données de sauvegardes initiales compressées en mode hors connexion dans Azure via des disques.

Le processus d’amorçage hors connexion d’Azure Backup est étroitement intégré au [service Azure Import/Export](../storage/storage-import-export-service.md) qui vous permet de transférer rapidement des données vers Azure à l’aide de disques. Si vous devez transférer des téraoctets de données de sauvegarde initiales sur un réseau à latence élevée et à faible bande passante, vous pouvez utiliser le workflow d’amorçage hors connexion pour expédier la copie de sauvegarde initiale sur un ou plusieurs disques durs à un centre de données Azure. Cet article fournit une vue d’ensemble des étapes requises pour effectuer ce flux de travail.

## Vue d'ensemble

Grâce à la fonction d’amorçage hors connexion d’Azure Backup et au service Azure Import/Export, vous téléchargez simplement et directement les données dans Azure en mode hors connexion par le biais de disques. Au lieu de transférer la copie initiale complète sur le réseau, les données de sauvegarde sont écrites dans un *emplacement intermédiaire*. Une fois que la copie initiale a été effectuée vers *l’emplacement intermédiaire*, ces données sont écrites sur un ou plusieurs disques SATA, selon la taille de la sauvegarde initiale, à l’aide de *l’outil Azure Import/Export*. Ces disques sont expédiés au centre de données Azure le plus proche.

La [mise à jour d’Azure Backup du mois d’août 2016 (et versions ultérieures)](http://go.azure.microsoft.com/.com/fwlink/?LinkID=229525&clcid=0x409) est fournie avec un *outil de préparation des disques Azure* nommé **AzureOfflineBackupDiskPrep** qui :

   - Vous aide à préparer vos lecteurs Azure Import en toute simplicité à l’aide de l’outil Azure Import/Export
   - Crée automatiquement un travail d’importation Azure pour le service Azure Import/Export sur [le portail Azure Classic](https://manage.windowsazure.com) (dans les versions antérieures d’Azure Backup, le travail d’importation devait être créé manuellement)

Une fois que les données de sauvegarde ont été transférées dans Azure, Azure Backup copie les données de sauvegarde dans le coffre de sauvegarde et des sauvegardes incrémentielles sont planifiées.

  > [AZURE.NOTE] Pour utiliser l’outil de préparation des disques Azure, assurez-vous d’avoir installé la mise à jour d’Azure Backup du mois d’août 2016 (ou une version ultérieure) et effectuez toutes les étapes du flux de travail associé. Si vous utilisez une version antérieure d’Azure Backup, vous pouvez préparer le disque SATA à l’aide de *l’outil Azure Import/Export* comme détaillé dans les sections suivantes de cet article.

## Composants requis

1. Il est important de vous familiariser avec le flux de travail d’Azure Import/Export décrit [ici](../storage/storage-import-export-service.md).
2. Avant de lancer le flux de travail, assurez-vous qu’un coffre de sauvegarde Azure a été créé, que les informations d’identification de coffre ont été téléchargées, que l’agent Azure Backup a été installé sur votre serveur/client Windows ou votre serveur System Center Data Protection Manager (SCDPM) et que l’ordinateur est inscrit dans le coffre de sauvegarde Azure.
3. Téléchargez les paramètres du fichier de publication Azure [ici](https://manage.windowsazure.com/publishsettings) sur l’ordinateur duquel vous prévoyez de sauvegarder les données.
4. Préparez un *emplacement intermédiaire*, qui peut être un partage réseau ou un disque supplémentaire sur l’ordinateur. L’emplacement intermédiaire est un « stockage temporaire » utilisé pendant ce flux de travail. Assurez-vous que l’emplacement intermédiaire dispose de suffisamment d’espace disque pour stocker votre copie initiale. Par exemple, si vous tentez de sauvegarder un serveur de fichiers de 500 Go, assurez-vous que la zone intermédiaire dispose d’au moins 500 Go (bien qu’une quantité inférieure soit utilisée grâce à la compression).
5. Enregistreur de disque SATA externe et disque SATA de 3,5 pouces externe : seuls les disques durs SATA II/III de 3,5 pouces sont pris en charge par le service Import/Export. Les disques durs de plus de 8 To ne sont pas pris en charge. Vous pouvez raccorder un disque SATA II/III par voie externe à la plupart des ordinateurs à l'aide d'un adaptateur USB SATA II/III. Consultez la documentation d’Azure Import/Export pour connaître la dernière série de disques pris en charge par le service.
6. Activez BitLocker sur l’ordinateur auquel est connecté l’enregistreur de disque SATA.
7. Téléchargez l’outil Azure Import/Export [ici](http://go.azure.microsoft.com/.com/fwlink/?LinkID=301900&clcid=0x409) sur l’ordinateur auquel est connecté l’enregistreur de disque SATA. Cette étape n’est pas nécessaire si vous avez téléchargé et installé la mise à jour d’Azure Backup du mois d’août 2016 (ou une version ultérieure).

## Workflow
Les informations fournies dans cette section concernent la fin du flux de travail **Sauvegarde hors connexion** afin que vos données puissent être remises à un centre de données Azure et téléchargées vers le stockage Azure. Si vous avez des questions sur le service Import ou sur un aspect du processus, consultez la documentation [Vue d’ensemble du service Import](../storage/storage-import-export-service.md) susmentionnée.

### Lancer la sauvegarde hors connexion

1. Dans le cadre de la planification d’une sauvegarde, l’écran suivant s’affiche (dans Windows Server, le client Windows ou SCDPM).

    ![Écran d’importation](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)
    
    L’écran correspondant dans SCDPM se présente comme suit. <br/> ![Écran d’importation DPM](./media/backup-azure-backup-import-export/dpmoffline.png)

    Les champs sont décrits ci-dessous :

    - **Emplacement intermédiaire** : fait référence à l’emplacement de stockage temporaire dans lequel la copie de sauvegarde initiale est écrite. Il peut s’agir d’un partage réseau ou de l’ordinateur local. Si *l’ordinateur de copie* et *l’ordinateur source* sont différents, il est recommandé de spécifier le chemin d’accès réseau complet de l’emplacement intermédiaire.
    - **Nom de la tâche d’importation Azure** : fait référence au nom unique utilisé par le service Azure Import et Azure Backup pour effectuer le suivi du transfert des données envoyées dans Azure à l’aide de disques.
    - **Paramètres de publication Azure** : il s’agit d’un fichier XML qui contient des informations sur le profil de votre abonnement. Il contient également les informations d’identification sécurisées associées à votre abonnement. Le fichier peut être téléchargé [ici](https://manage.windowsazure.com/publishsettings). Fournissez le chemin d’accès local au fichier de paramètres de publication.
    - **ID d’abonnement Azure** : l’ID d’abonnement Azure dans lequel vous prévoyez de lancer la tâche d’importation Azure. Si vous avez plusieurs abonnements Azure, utilisez l’ID auquel vous souhaitez associer la tâche d’importation.
    - **Compte de stockage Azure** : le compte de stockage « classique » de l’abonnement Azure fourni qui sera associé à la tâche d’importation Azure.
    - **Conteneur de stockage Azure** : le nom de l’objet blob de stockage cible dans le compte de stockage Azure où les données de cette tâche sont importées.
    
    > [AZURE.NOTE] Si vous avez inscrit votre serveur dans un coffre Recovery Services à partir du [nouveau portail Azure](https://ms.portal.azure.com) pour vos sauvegardes et que vous ne disposez d’aucun abonnement Fournisseur de solutions cloud (CSP), vous pouvez créer un compte de stockage de type « classique » à partir du nouveau portail Azure et l’utiliser pour le flux de travail de sauvegarde hors connexion.
    
    Enregistrez toutes ces informations séparément, car elles devront être entrées une nouvelle fois dans les étapes suivantes. Seul *l’emplacement intermédiaire* est requis si vous utilisez *l’outil de préparation des disques Azure* pour préparer les disques.
    

2. Terminez le flux de travail et sélectionnez **Sauvegarder maintenant** dans la console de gestion Azure Backup pour lancer la copie de sauvegarde en mode hors connexion. La sauvegarde initiale est écrite dans la zone intermédiaire à cette étape.

    ![Sauvegarder maintenant](./media/backup-azure-backup-import-export/backupnow.png)
    
    Le flux de travail correspondant dans SCDPM est activé en cliquant avec le bouton droit sur le **groupe de protection** et en choisissant l’option **Créer un point de récupération**. Ensuite, choisissez l’option **Protection en ligne**.

    ![Sauvegarder maintenant (DPM)](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Une fois l’opération terminée, l’emplacement intermédiaire est prêt à être utilisé pour la préparation du disque.

    ![Sortie](./media/backup-azure-backup-import-export/opbackupnow.png)

###Préparer le disque SATA et créer la tâche d’importation Azure à l’aide de *l’outil de préparation des disques Azure*
*L’outil de préparation des disques Azure* est disponible dans le répertoire d’installation de l’agent azure.microsoft.com/ Azure Recovery Services (version d’août 2016 et ultérieure) via le chemin d’accès suivant.

   *\\azure.microsoft.com/* *Azure* *Recovery* *Services* *Agent\\Utils*

1. Accédez au répertoire ci-dessus et copiez le répertoire **AzureOfflineBackupDiskPrep** sur un *ordinateur de copie* sur lequel les disques à préparer ont été montés. Vérifier les éléments suivants concernant *l’ordinateur de copie*

      - *L’emplacement intermédiaire* spécifié pour le flux de travail d’amorçage hors connexion est accessible à partir de *l’ordinateur de copie* via le même chemin d’accès réseau que celui fourni lors du flux de travail **Lancer la sauvegarde hors connexion**.
      
      - BitLocker est activé sur l’ordinateur.
      
      - L’ordinateur a accès au portail Azure.
      
      Si nécessaire, *l’ordinateur de copie* peut être le même que *l’ordinateur source*.

2. Ouvrez une invite de commande avec élévation de privilèges sur l’ordinateur de copie avec le répertoire de l’outil de préparation des disques Azure comme répertoire en cours et exécutez la commande suivante.

      *.\\AzureOfflineBackupDiskPrep.exe* s:<*Staging Location Path*> [p:<*Path to PublishSettingsFile*>]

| Paramètre | Description
|-------------|-------------|
|s:<*Staging Location Path*> | Entrée obligatoire utilisée pour fournir le chemin d’accès vers l’emplacement intermédiaire saisi lors du lancement de la sauvegarde hors connexion |
|p:<*Path to PublishSettingsFile*> | Entrée facultative utilisée pour fournir le chemin d’accès vers le fichier de paramètres de publication saisi lors du lancement de la sauvegarde hors connexion |

   > [AZURE.NOTE] Le fichier de paramètres de publication est requis lorsque *l’ordinateur de copie* et *l’ordinateur source* sont différents

Au moment d’exécuter la commande, l’outil vous demande de sélectionner la tâche d’importation Azure pour laquelle les disques doivent être préparés. S’il existe une seule tâche d’importation associée à l’emplacement intermédiaire spécifié, un écran s’affiche tel que suit.

   ![Entrée d’outil de préparation des disques Azure](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>
3. Entrez la **lettre de lecteur**, sans les deux-points de fin, correspondant au disque monté que vous souhaitez préparer pour le transfert vers Azure. Puis, confirmez la mise en forme du disque lorsque vous y êtes invité.

L’outil commence ensuite à préparer le disque avec les données de sauvegarde. Vous devrez peut-être ajouter des disques supplémentaires lorsque vous y êtes invité par l’outil, au cas où le disque fourni n’a pas suffisamment d’espace pour héberger toutes les données de sauvegarde. <br/>

Après l’exécution de l’outil, le ou les disques que vous avez fournis sont préparés pour l’envoi à Azure et une tâche d’importation portant le nom que vous avez fourni lors du flux de travail **Lancer la sauvegarde hors connexion** est créée sur le portail Azure Classic. En outre, l’outil affiche l’adresse du centre de données Azure vers lequel les disques doivent être expédiés, ainsi que le lien vers la tâche d’importation sur le portail Azure Classic.

   ![Préparation des disques Azure terminée](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>
4. Expédiez les disques à l’adresse indiquée par l’outil et conservez le numéro de suivi pour référence.<br/>
5. Cliquez sur le lien affiché dans l’outil pour accéder au **compte de stockage Azure** spécifié dans le flux de travail de sauvegarde hors connexion. La nouvelle tâche d’importation s’affiche sous l’onglet **IMPORT/EXPORT** du compte de stockage.

   ![Tâche d’importation créée](./media/backup-azure-backup-import-export/ImportJobCreated.png)<br/>
6. Cliquez sur **INFORMATIONS D’EXPÉDITION** en bas de la page pour mettre à jour vos **coordonnées** comme indiqué ci-dessous. azure.microsoft.com/ utilise ces informations pour vous renvoyer vos disques une fois la tâche d’importation terminée.

   ![Informations de contact](./media/backup-azure-backup-import-export/contactInfoAddition.PNG)<br/>
7. Entrez les **informations d’expédition** sur l’écran suivant comme indiqué ci-dessous. Fournissez les coordonnées du transporteur et le numéro de suivi correspondant aux disques que vous avez expédiés au centre de données Azure.
   
   ![Informations d’expédition](./media/backup-azure-backup-import-export/shippingInfoAddition.PNG)<br/>

### Fin du flux de travail
Une fois la tâche d’importation terminée, les données de sauvegarde initiale sont disponibles dans votre compte de stockage. L’agent azure.microsoft.com/ Azure Recovery Services copie le contenu des données à partir de ce compte vers le coffre Backup ou le coffre Recovery Services, selon ce qui est applicable. Lors de la prochaine sauvegarde planifiée, l’agent Azure Backup ajoute la sauvegarde incrémentielle à la copie de sauvegarde initiale.


> [AZURE.NOTE] Les sections suivantes s’appliquent aux utilisateurs de versions antérieures d’Azure Backup qui n’ont pas accès à *l’outil de préparation des disques Azure*

### Préparer le disque SATA

1. Téléchargez l’[outil azure.microsoft.com/ Azure Import/Export](http://go.azure.microsoft.com/.com/fwlink/?linkid=301900&clcid=0x409) sur l’*ordinateur de copie*. Assurez-vous que l’emplacement intermédiaire (voir l’étape précédente) est accessible à partir de l’ordinateur sur lequel vous souhaitez exécuter le jeu de commandes suivant. Si nécessaire, l’ordinateur de copie peut être le même que l’ordinateur source.

2. Décompressez le fichier *WAImportExport.zip*. Exécutez l’outil *WAImportExport* pour formater le disque SATA, écrire les données de sauvegarde sur le disque SATA et les chiffrer. Avant d’exécuter la commande suivante, assurez-vous que BitLocker est activé sur l’ordinateur. <br/>

    *.\\WAImportExport.exe PrepImport /j:<*JournalFile*>.jrn /id: <*SessionId*> /sk:<*StorageAccountKey*> /BlobType:**PageBlob** /t:<*TargetDriveLetter*> /format /encrypt /srcdir:<*staging location*> /dstdir: <*DestinationBlobVirtualDirectory*>/*
    
    > [AZURE.NOTE] Si vous avez installé la mise à jour d’Azure Backup du mois d’août 2016 (ou une version ultérieure), assurez-vous que *l’emplacement intermédiaire* spécifié est identique à celui affiché sur l’écran **Sauvegarder maintenant** et qu’il contient les fichiers *AIB* et *Base Blob*.

| Paramètre | Description
|-------------|-------------|
| /j:<*JournalFile*>| Chemin d’accès du fichier journal. Chaque disque doit avoir un seul fichier journal. Le fichier journal ne doit pas résider sur le disque cible. L’extension du fichier journal est .jrn, elle est créée à l’exécution de cette commande.|
|/id:<*SessionId*> | L’ID de session identifie une *session de copie*. Il est utilisé pour assurer la précision de la récupération d’une session de copie interrompue. Les fichiers copiés dans une session de copie sont stockés dans un répertoire dont le nom fait référence à l’ID de session sur le disque cible.|
| /sk:<*StorageAccountKey*> | Clé du compte de stockage vers lequel les données sont importées. Elle doit être identique à celle qui a été entrée lors de la création de la stratégie de sauvegarde/du groupe de protection.|
| /BlobType | Spécifiez **PageBlob** (ce flux de travail aboutit uniquement si l’option PageBlob est spécifiée). Ce paramètre n’est pas l’option par défaut et doit être indiqué dans cette commande. |
|/t:<*TargetDriveLetter*> | Lettre de lecteur du disque dur cible pour la session de copie actuelle, sans les deux-points de fin.|
|/format | Spécifiez ce paramètre quand le disque doit être formaté, sinon, ignorez-le. Avant de formater le disque, l’outil demande confirmation à partir de la console. Pour supprimer la confirmation, spécifiez le paramètre /silentmode.|
|/encrypt | Spécifiez ce paramètre quand le disque n’a pas encore été chiffré avec BitLocker et doit être chiffré par l’outil. Si le disque a déjà été chiffré avec BitLocker, ignorez ce paramètre et spécifiez le paramètre /bk, en fournissant la clé BitLocker existante. Si vous spécifiez le paramètre /format, vous devez également spécifier le paramètre /encrypt. |
|/srcdir:<*SourceDirectory*> | Répertoire source qui contient les fichiers à copier sur le disque cible. Assurez-vous que le nom de répertoire spécifié ici est le chemin d’accès complet (et non un chemin d’accès relatif).|
|/dstdir:<*DestinationBlobVirtualDirectory*> | Chemin d’accès au répertoire virtuel de destination dans votre compte de stockage azure.microsoft.com/ Azure. Veillez à utiliser des noms de conteneur valides quand vous spécifiez des répertoires virtuels de destination ou des objets blob. N’oubliez pas que les noms de conteneur doivent être en minuscules. Ce nom de conteneur doit être identique à celui qui a été entré au cours de la création de la stratégie de sauvegarde/du groupe de protection.|

  > [AZURE.NOTE] Un fichier journal est créé dans le dossier WAImportExport et capture toutes les informations du flux de travail. Vous avez besoin de ce fichier lors de la création d’une tâche d’importation dans le portail Azure.

  ![Sortie PowerShell](./media/backup-azure-backup-import-export/psoutput.png)

### Créer la tâche d’importation dans le portail Azure
1. Accédez à votre compte de stockage dans le [portail Azure Classic](https://manage.windowsazure.com/) et cliquez sur **Import/Export**, puis sur **Créer une tâche d’importation** dans le volet des tâches.

    ![Portail](./media/backup-azure-backup-import-export/azureportal.png)

2. À l'étape 1 de l'Assistant, précisez que vous avez préparé votre lecteur et que le fichier journal du lecteur est disponible. À l’étape 2 de l’Assistant, fournissez les coordonnées de la personne responsable de cette tâche d’importation.
3. À l’étape 3, téléchargez les fichiers journaux de lecteur que vous avez obtenus dans la section précédente.
4. À l’étape 4, entrez un nom descriptif pour la tâche d’importation qui a été entrée lors de la création de la stratégie de sauvegarde/du groupe de protection. Le nom que vous entrez ne peut contenir que des minuscules, des chiffres, des tirets et des traits de soulignement, il doit commencer par une lettre et ne peut pas contenir d'espaces. Le nom que vous choisissez vous servira à suivre la tâche pendant et après son exécution.
5. Sélectionnez ensuite la région du centre de données dans la liste. Cette dernière indique à quel centre de données et à quelle adresse vous devez expédier votre colis.

    ![Contrôleur de domaine](./media/backup-azure-backup-import-export/dc.png)

6. À l'étape 5, sélectionnez votre transporteur dans la liste, puis entrez son numéro de compte. azure.microsoft.com/ utilise ce compte pour réexpédier vos lecteurs une fois la tâche d’importation terminée.

7. Expédiez le disque et entrez le numéro de suivi pour suivre l’état de l’expédition. Dès que le disque arrive dans le centre de données, il est copié dans le compte de stockage et l’état est mis à jour.

    ![État Terminé](./media/backup-azure-backup-import-export/complete.png)

### Fin du flux de travail
Une fois que les données de sauvegarde initiale sont disponibles dans votre compte de stockage, l’agent azure.microsoft.com/ Azure Recovery Services copie le contenu des données à partir de ce compte vers le coffre Backup ou le coffre Recovery Services, selon ce qui est applicable. Lors de la prochaine sauvegarde planifiée, l’agent Azure Backup ajoute la sauvegarde incrémentielle à la copie de sauvegarde initiale.

## Étapes suivantes
- Pour toute question sur le flux de travail Azure Import/Export, reportez-vous à cet [article](../storage/storage-import-export-service.md).
- Reportez-vous à la section Sauvegarde hors connexion du [Forum Aux Questions](backup-azure-backup-faq.md) Azure Backup pour toute question concernant le flux de travail.

<!---HONumber=AcomDC_0817_2016-->
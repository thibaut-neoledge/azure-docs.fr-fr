<properties
	pageTitle="Sauvegarder un client Windows ou un serveur Windows Server dans Azure | Microsoft Azure"
	description="Sauvegardez des serveurs Windows Server ou des clients Windows sur Azure en créant un coffre de sauvegarde, en téléchargeant des informations d’identification, en installant l’agent de sauvegarde et en effectuant une sauvegarde initiale de vos fichiers et dossiers."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""
	keywords="coffre de sauvegarde; sauvegarde un serveur Windows Server; sauvegarder windows;"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/19/2016"
	ms.author="jimpark; trinadhk; markgal"/>


# Sauvegarder un client Windows ou un serveur Windows Server dans Azure
Cet article vous présente les procédures à exécuter pour préparer votre environnement et sauvegarder un serveur Windows Server (ou un client) sur Azure. Il aborde également les considérations de déploiement de votre solution de sauvegarde. Si vous souhaitez essayer Azure Backup pour la première fois, [cet article](backup-configure-vault.md) vous présente le processus.

![Créer le coffre](./media/backup-configure-vault/initial-backup-process.png)

## Avant de commencer
Pour sauvegarder un serveur Windows Server ou un client Windows sur Azure, vous devez disposer d’un compte Azure. Si vous n’en possédez pas, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/) en quelques minutes.

## Étape 1 : Créer un coffre de sauvegarde
Pour sauvegarder des fichiers et des dossiers d’un serveur ou d’un client Windows, vous devez créer un coffre de sauvegarde dans la région géographique où vous souhaitez stocker les données.

### Pour créer un coffre de sauvegarde

1. Connectez-vous au [portail de gestion](https://manage.windowsazure.com/).

2. Cliquez sur **Nouveau** -> **Services de données** -> **Services de récupération** -> **Coffre de sauvegarde**, puis sélectionnez **Création rapide**.

3. Pour le paramètre **Nom**, entrez un nom convivial permettant d’identifier l’archivage de sauvegarde. Tapez un nom contenant entre 2 et 50 caractères. Il doit commencer par une lettre, et ne peut contenir que des lettres, des chiffres et des traits d’union. Cette opération doit être unique pour chaque abonnement.

    Pour le paramètre **Région**, sélectionnez la région géographique de l’archivage de sauvegarde. Le choix définit la région géographique où vos données de sauvegarde sont envoyées. En choisissant une région proche de votre emplacement, vous pouvez réduire la latence du réseau lors de la sauvegarde dans Azure.

    Cliquez sur **Create vault**.

    ![Créer le coffre](./media/backup-configure-vault/demo-vault-name.png)

    La création du coffre de sauvegarde peut prendre du temps. Pour vérifier l’état d’avancement de l’opération, vous pouvez contrôler les notifications au bas du portail.

    Une fois l’opération terminée, un message vous indique que l’archivage a bien été créé. Le coffre est également répertorié dans les ressources Recovery Services en tant que **Actif**.

    ![Création d’un état de l’archivage](./media/backup-configure-vault/recovery-services-select-vault.png)

4. Sélection l’option de **redondance de stockage**.

    >[AZURE.IMPORTANT] Nous vous recommandons d’identifier votre option de redondance de stockage juste après la création de l’archivage et avant l’inscription d’une machine dans l’archivage. Une fois qu’un élément a été inscrit dans l’archivage, l’option de redondance de stockage est verrouillée et ne peut pas être modifiée.

    Si vous utilisez Azure en tant que point de terminaison de stockage de sauvegarde principal (par exemple, vous sauvegardez vos données dans Azure à partir de Windows Server), choisissez l’option de [stockage géo-redondant](../storage/storage-redundancy.md#geo-redundant-storage) (par défaut).

    Si vous utilisez Azure comme point de terminaison de stockage de sauvegarde tertiaire (par exemple, vous utilisez SCDPM pour disposer d’une copie de sauvegarde localement et utilisez Azure pour vos besoins de rétention à long terme), choisissez le [stockage localement redondant](../storage/storage-redundancy.md#locally-redundant-storage). Cela vous permet de diminuer les coûts de stockage de données dans Azure tout en fournissant un niveau inférieur de durabilité de vos données pouvant être acceptables pour des copies tertiaires.

    **Pour sélectionner l’option de redondance de stockage :**

    a. Cliquez sur le coffre que vous venez de créer.

    b. Dans la page **Démarrage rapide**, cliquez sur **Configurer**.

    ![Configurer l’état du coffre](./media/backup-configure-vault/configure-vault.png)

    c. Choisissez l’option de redondance de stockage appropriée.

    Vous devez cliquer sur **Enregistrer** si vous avez sélectionné **Localement redondant**, car **Géo-redondant** est l’option par défaut.

    d. Dans le volet de navigation gauche, cliquez sur **Recovery Services** pour revenir à la liste des ressources pour **Recovery Services**.

## Étape 2 : Télécharger le fichier d’informations d’identification de coffre
L’ordinateur local (client Windows ou Windows Server) doit être authentifié avec un coffre de sauvegarde avant de pouvoir sauvegarder des données dans Azure. L’authentification s’effectue à l’aide des *informations d’identification du coffre*. Le fichier d’informations d’identification de coffre est téléchargé via un canal sécurisé depuis le portail Azure. Le service Azure Backup n’a pas connaissance de la clé privée du certificat et cette dernière n’est pas conservée dans le portail ou le service.

En savoir plus sur l’[utilisation des informations d’identification de coffre pour s’authentifier auprès du service Azure Backup](backup-introduction-to-azure-backup.md#what-is-the-vault-credential-file).

### Pour télécharger les informations d’identification de coffre sur un ordinateur local

1. Cliquez sur **Recovery Services** dans le volet de navigation gauche et sélectionnez le coffre de sauvegarde que vous avez créé.

    ![RI terminé](./media/backup-configure-vault/rs-left-nav.png)

2.  Dans la page de **démarrage rapide**, cliquez sur **Télécharger les informations d’identification du coffre**.

    Le portail générera une information d'identification de coffre en combinant le nom du coffre et la date actuelle. Le fichier d’informations d’identification de coffre est utilisé uniquement pendant le flux de travail d’inscription et expire au bout de 48 heures.

    Le fichier d’informations d’identification de coffre peut être téléchargé à partir du portail.

3. Cliquez sur **Save** pour télécharger les informations d'identification de coffre vers les dossier de téléchargements du compte local, ou sélectionnez **Save As** à partir du menu **Save** pour spécifier un emplacement pour les informations d'identification de coffre.

    >[AZURE.NOTE] Vérifiez que le fichier des informations d’identification de coffre est enregistré dans un emplacement accessible à partir de votre ordinateur. Si elles sont stockées dans un partage de fichiers/SMB, vérifiez les autorisations d’accès.

## Étape 3 : Télécharger, installer et inscrire l’agent Azure Backup
Après avoir créé le coffre Azure Backup et téléchargé les informations d’identification associées, vous devez installer un agent sur chacune des machines Windows (Windows Server ou client Windows).

### Pour télécharger, installer et inscrire l’agent

1. Cliquez sur **Recovery Services**, puis sélectionnez le coffre de sauvegarde que vous souhaitez enregistrer avec un serveur.

2. Dans la page de démarrage rapide, cliquez sur **Agent pour Windows Server ou System Center Data Protection Manager ou Windows Client > Enregistrer**.

    ![Enregistrer l’agent](./media/backup-configure-vault/agent.png)

3. Une fois le téléchargement de *MARSagentinstaller.exe* terminé, cliquez sur **Exécuter** (ou double-cliquez sur **MARSAgentInstaller.exe** dans l’emplacement d’enregistrement).

4. 	Choisissez le *dossier d’installation* et le *dossier de cache* requis pour l’agent, puis cliquez sur **Suivant**.

    L’emplacement de cache spécifié doit présenter un espace libre au moins égal à 5 % du volume des données de sauvegarde.

5. Vous pouvez continuer à vous connecter à Internet via les paramètres du proxy par défaut, ou si vous utilisez un serveur proxy pour vous connecter à Internet, dans l’écran **Configuration du proxy**, cochez la case **Utiliser les paramètres du proxy personnalisés** et saisissez les détails du serveur proxy.

    Si vous utilisez un proxy authentifié, entrez les informations de nom d’utilisateur et mot de passe.

    Cliquez sur **Next**.

6. Cliquez sur **Installer** pour lancer le processus d’installation de l’agent.

    L’agent Azure Backup installe .NET Framework 4.5 et Windows PowerShell (s’il n’est pas déjà installé) pour terminer l’installation.

7. Une fois l’agent installé, cliquez sur **Procéder à l’enregistrement** pour poursuivre le flux de travail.

8. Dans l’écran **Identification du coffre**, recherchez et sélectionnez le *fichier d’informations d’identification du coffre* téléchargé précédemment.

    Le fichier d’informations d’identification du coffre est uniquement valide pendant 48 heures (à partir de son téléchargement depuis le portail). Si vous rencontrez une erreur dans cet écran (par exemple, « Le fichier d’informations d’identification de coffre fourni a expiré »), connectez-vous au portail Azure et téléchargez de nouveau le fichier d’informations d’identification du coffre.

    Assurez-vous que le fichier d’informations d’identification du coffre se trouve dans un emplacement accessible par le programme d’installation. Si vous rencontrez des erreurs liées à l’accès, copiez le fichier d’informations d’identification d’archivage dans un emplacement temporaire sur cet ordinateur et recommencez l’opération.

    Si vous rencontrez une erreur d’informations d’identification de coffre non valides (par exemple, « Les informations d’identification de coffre fournies ne sont pas valides »), cela signifie que le fichier est endommagé ou qu’il ne possède pas les dernières informations d’identification associées au service de récupération. Recommencez l’opération après avoir téléchargé un nouveau fichier d’informations d’identification de coffre à partir du portail. Cette erreur se produit généralement si l’utilisateur clique sur l’option *Télécharger les informations d’identification du coffre* coup sur coup. Dans ce cas, seul le dernier fichier d’informations d’identification du coffre est valide.

9. Sur l’écran **Paramètre de chiffrement**, vous pouvez *générer* ou *fournir* une phrase secrète (16 caractères minimum). Pensez à enregistrer le mot de passe dans un emplacement sécurisé.

    Cliquez sur **Terminer**.

    > [AZURE.WARNING] Si la phrase secrète est perdue ou oubliée, Microsoft ne peut pas vous aider à récupérer les données de sauvegarde. L’utilisateur final détient la phrase secrète de chiffrement et Microsoft n’a aucune visibilité sur cette dernière. Enregistrez le fichier dans un emplacement sécurisé, car il vous sera demandé pour les opérations de récupération.

    L’**Assistant d’enregistrement du serveur** inscrit le serveur auprès de Microsoft Azure Backup.

10. Une fois la **clé de chiffrement** définie, laissez la case **Démarrer Microsoft Azure Recovery Services Agent** cochée et cliquez sur **Fermer**.

## Étape 4 : Exécuter la sauvegarde initiale

La sauvegarde initiale est composée de ces deux tâches : **établissement de la planification de sauvegarde** et **sauvegarde des fichiers et des dossiers pour la première fois**. Une fois la sauvegarde initiale effectuée, en fonction de la planification définie, la stratégie de sauvegarde crée des points de sauvegarde à utiliser lorsque vous souhaitez récupérer les données.

### Planifier la sauvegarde

1. Ouvrez l’**agent Microsoft Azure Backup** (il s’ouvre automatiquement si vous avez laissé la case **Démarrer Microsoft Azure Recovery Services Agent** cochée lors de la fermeture de l’**Assistant d’enregistrement du serveur**, sinon recherchez *Microsoft Azure Backup* dans votre ordinateur).

    ![Planifier une sauvegarde de Windows Server](./media/backup-configure-vault/snap-in-search.png)

2. Dans l’**agent Microsoft Azure Backup **, cliquez sur **Planifier la sauvegarde**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-configure-vault/schedule-backup-close.png)

3. Dans l’écran ** Assistant Planifier la sauvegarde Prise en main**, cliquez sur **Suivant**.

4. Dans l’écran **Sélectionner les éléments à sauvegarder**, cliquez sur **Ajouter des éléments**.

5. Sélectionnez les fichiers et les dossiers que vous souhaitez sauvegarder, puis cliquez sur **OK**.

6. Cliquez sur **Next**.

7. Sur l’écran **Spécifier la planification de sauvegarde**, spécifiez la **planification de sauvegarde**, puis cliquez sur **Suivant**.

    Vous pouvez planifier des sauvegardes quotidiennes (au maximum 3 fois par jour) ou hebdomadaires.

    ![Éléments de sauvegarde de Windows Server](./media/backup-configure-vault/specify-backup-schedule-close.png)

    >[AZURE.NOTE] La définition de la planification de sauvegarde est décrite en détail dans cet [article](backup-azure-backup-cloud-as-tape.md).

8. Dans l’écran **Sélectionnez une stratégie de rétention**, sélectionnez la **Stratégie de rétention** pour la copie de sauvegarde.

    La stratégie de rétention spécifie la durée de stockage de la sauvegarde. Au lieu de simplement spécifier une même stratégie pour tous les points de sauvegarde, vous pouvez spécifier différentes stratégies de rétention en fonction du moment où est effectuée la sauvegarde. Vous pouvez modifier les stratégies de rétention quotidiennes, hebdomadaires, mensuelles et annuelles pour répondre à vos besoins.

     Cliquez sur **Next**.

9. Sur l’écran **Choisir un type de sauvegarde initiale**, sélectionnez le type de sauvegarde initiale**.

    Vous pouvez effectuer des sauvegardes automatiques sur le réseau ou vous pouvez sauvegarder en mode hors connexion. Le reste de cet article décrit le processus de sauvegarde automatique. Si vous préférez effectuer une sauvegarde en mode hors connexion, lisez cet article pour en savoir plus sur le [flux de travail de sauvegarde en mode hors connexion dans Azure Backup](backup-azure-backup-import-export.md).

    Laissez l’option **Automatiquement sur le réseau** sélectionnée, puis cliquez sur **Suivant**.

10. Dans l’écran **Confirmation**, passez en revue les informations, puis cliquez sur **Terminer**.

11. Dès que l’Assistant a terminé la création de la **planification de sauvegarde**, cliquez sur **Fermer**.

### Activer la limitation du réseau (facultatif)

L’agent Azure Backup prend en charge la limitation du réseau. La limitation contrôle l’utilisation de la bande passante réseau durant le transfert de données. Cela peut s’avérer utile si vous avez besoin de sauvegarder des données pendant les heures de travail, mais ne souhaitez pas que le processus de sauvegarde interfère avec le reste du trafic internet. La limitation du transfert de données s’applique aux activités de sauvegarde et de restauration.

1. Dans **Backup Agent**, cliquez sur **Modifier les propriétés**.

    ![Modifier les propriétés](./media/backup-configure-vault/change-properties.png)

2. Sur l’onglet **Limitation**, sélectionnez la case **Activer la limitation de la bande passante sur Internet pour les opérations de sauvegarde**.

    ![Limitation du réseau](./media/backup-configure-vault/throttling-dialog.png)

3. Une fois que vous avez activé la limitation, spécifiez la bande passante autorisée pour le transfert des données de sauvegarde durant les **Heures de travail** et les **Heures chômées**.

    Les valeurs de bande passante, qui démarrent à 512 kilo-octets par seconde, peuvent aller jusqu’à 1 023 mégaoctets par seconde. Vous pouvez également définir le début et la fin des **Heures de travail**, et identifier les jours de la semaine considérés comme des jours de travail. Les intervalles de temps situés en dehors des heures de travail sont considérés comme des périodes (heures) chômées.

4. Cliquez sur **OK**.

### Sauvegarder maintenant

1. Dans **Backup Agent**, cliquez sur **Sauvegarder maintenant** pour effectuer l’amorçage initial sur le réseau.

    ![Sauvegarder Windows Server maintenant](./media/backup-configure-vault/backup-now.png)

2. Dans l’écran **Confirmation**, vérifiez les paramètres utilisés par l’Assistant pour sauvegarder les données de l’ordinateur et cliquez sur **Sauvegarder**.

3. Cliquez sur **Fermer** pour fermer l’Assistant. Vous pouvez effectuer cette opération avant la fin du **processus de sauvegarde**, lequel continuera de s’exécuter à l’arrière-plan.

Une fois la sauvegarde initiale terminée, le statut *Tâche terminée* apparaît dans la **console Azure Backup**.

![RI terminé](./media/backup-configure-vault/ircomplete.png)

## Étapes suivantes
- Ouvrir [gratuitement un compte Azure](https://azure.microsoft.com/free/)

Pour plus d’informations sur la sauvegarde des machines virtuelles ou d’autres charges de travail, consultez les références suivantes :

- [Sauvegarder des machines virtuelles IaaS](backup-azure-vms-prepare.md)
- [Préparation de la sauvegarde des charges de travail à l’aide d’Azure Backup Server](backup-azure-microsoft-azure-backup.md)
- [Préparation de la sauvegarde des charges de travail dans Azure avec DPM](backup-azure-dpm-introduction.md)

<!---HONumber=AcomDC_0323_2016-->
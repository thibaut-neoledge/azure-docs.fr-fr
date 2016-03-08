<properties
   pageTitle="Aperçu : Sauvegarde d’un serveur Windows Server ou d’un client Windows dans Azure | Microsoft Azure"
   description="Découvrir comment sauvegarder un serveur Windows Server à l’aide d’Azure Backup en suivant ces étapes simples"
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""
   keywords="comment sauvegarder; procédure de sauvegarde"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="hero-article"
	 ms.date="02/21/2016"
	 ms.author="jimpark;"/>

# Sauvegarder un client Windows ou un serveur Windows Server dans Azure

En seulement quelques étapes, vous pouvez sauvegarder votre ordinateur Windows (client Windows ou serveur Windows Server) dans Azure. Lorsque vous aurez terminé les 4 étapes ci-dessous, vous aurez :

- Configuré un abonnement Azure (si nécessaire).
- Créé un coffre de sauvegarde et téléchargé les composants nécessaires.
- Préparé votre serveur Windows Server ou votre client Windows en installant et en inscrivant ces composants.
- Sauvegardé vos données.

## Étape 1 : obtenir un abonnement Azure

Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/) qui vous permet d’accéder à n’importe quel service Azure.

>[AZURE.NOTE] Vous pouvez ignorer cette étape si vous possédez déjà un abonnement Azure.

## Étape 2: Créer un coffre de sauvegarde

Pour sauvegarder des fichiers et des données d’un ordinateur Windows dans Azure, vous devez créer un coffre de sauvegarde dans la région géographique où vous souhaitez stocker les données.

1. Si ce n’est pas déjà fait, connectez-vous au [portail Azure](https://portal.azure.com/) à l’aide de votre abonnement Azure.

2. Cliquez sur **Nouveau > Intégration hybride > Backup**.

    ![Créer le coffre](./media/backup-try-azure-backup-in-10-mins/second-blade-backup.png)

    a. Dans l’écran qui s’affiche, dans **Nom**, entrez un nom convivial permettant d’identifier le coffre de sauvegarde. Tapez un nom contenant entre 2 et 50 caractères. Il doit commencer par une lettre, et ne peut contenir que des lettres, des chiffres et des traits d’union.

    b. Pour **Région**, sélectionnez la région géographique du coffre de sauvegarde. En choisissant une région proche de votre emplacement, vous pouvez réduire la latence du réseau lors de la sauvegarde dans Azure.

    c. Cliquez sur **Create vault**.

    ![Créer le coffre](./media/backup-try-azure-backup-in-10-mins/demo-vault-name.png)

    Pour connaître l’état d’avancement de l’opération, vous pouvez surveiller les notifications au bas du portail.

    ![Création d’un archivage](./media/backup-try-azure-backup-in-10-mins/creatingvault1.png)

    Une fois le coffre de sauvegarde créé, il est répertorié dans les ressources de Recovery Services en tant qu’**Actif**.

    ![Création d’un état de l’archivage](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

3. Sélectionnez les options de **redondance de stockage**.

    Nous vous recommandons de sélectionner l’option de redondance de stockage juste après avoir créé un coffre et avant d’y inscrire des ordinateurs. Une fois qu’un élément a été inscrit dans l’archivage, l’option de redondance de stockage est verrouillée et ne peut pas être modifiée.

    Si vous utilisez Azure en tant que point de terminaison de stockage de sauvegarde principal (par exemple, vous sauvegardez vos données dans Azure à partir de Windows Server), choisissez l’option de [stockage géo-redondant](../storage/storage-redundancy.md#geo-redundant-storage) (par défaut).

    Si vous utilisez Azure comme point de terminaison de stockage de sauvegarde tertiaire (par exemple, vous utilisez SCDPM pour disposer d’une copie de sauvegarde en local et vous utilisez Azure pour vos besoins de rétention à long terme), choisissez le [stockage localement redondant](../storage/storage-redundancy.md#locally-redundant-storage). Cela vous permet de diminuer les coûts de stockage de données dans Azure tout en fournissant un niveau inférieur de durabilité de vos données pouvant être acceptables pour des copies tertiaires.

    a. Cliquez sur le coffre que vous venez de créer.

    b. Dans la page Démarrage rapide, sélectionnez **Configurer**.

    ![Configurer l’état du coffre](./media/backup-try-azure-backup-in-10-mins/configure-vault.png)

    c. Choisissez l’option de redondance de stockage appropriée.

    Vous devez cliquer sur **Enregistrer** si vous avez sélectionné **Localement redondant**, car **Géo-redondant** est l’option par défaut.

    ![GRS](./media/backup-try-azure-backup-in-10-mins/geo-redundant.png)

    d. Dans le volet de navigation de gauche, cliquez sur **Recovery Services** pour revenir à la liste des ressources pour **Recovery Services**.

    ![Sélectionner un coffre de sauvegarde](./media/backup-try-azure-backup-in-10-mins/rs-left-nav.png)

    Votre ordinateur Windows doit maintenant être authentifié avec le coffre de sauvegarde que vous venez de créer. L’authentification s’effectue à l’aide des informations d’identification du coffre.

4.  Cliquez sur le coffre que vous venez de créer.

    ![Création d’un état de l’archivage](./media/backup-try-azure-backup-in-10-mins/demo-vault-active-full-screen.png)

5. Dans la page **Démarrage rapide**, cliquez sur **Télécharger les informations d’identification du coffre**.

    ![Télécharger](./media/backup-try-azure-backup-in-10-mins/downloadvc.png)

    Le portail générera une information d'identification de coffre en combinant le nom du coffre et la date actuelle.

    >[AZURE.NOTE] Le fichier d’informations d’identification de coffre est utilisé uniquement pendant le flux de travail d’inscription et expire au bout de 48 heures.

6. Cliquez sur **Enregistrer** pour télécharger les informations d’identification du coffre dans le dossier **Téléchargements** du compte local, ou sélectionnez **Enregistrer sous** dans le menu **Enregistrer** pour spécifier un emplacement pour les informations d’identification du coffre.

    ![Sélectionner un coffre de sauvegarde](./media/backup-try-azure-backup-in-10-mins/save.png)

    Vérifiez que les informations d’identification de coffre sont enregistrées dans un emplacement accessible à partir de votre machine. Si le fichier est stocké dans un partage de fichiers/SMB, vérifiez les autorisations d’accès.

    >[AZURE.NOTE] Vous n’avez pas besoin d’ouvrir les informations d’identification de coffre pour l’instant.

    Vous devez à présent télécharger l’agent de sauvegarde.

7. Cliquez sur **Recovery Services** dans le volet de navigation de gauche, puis sur le coffre de sauvegarde que vous souhaitez inscrire auprès d’un serveur.

    ![Sélectionner un coffre de sauvegarde](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

8. Dans la page Démarrage rapide, cliquez sur **Agent pour Windows Server ou System Center Data Protection Manager ou Client Windows > Enregistrer** (ou sélectionnez **Enregistrer sous** dans le menu **Enregistrer** pour spécifier un emplacement pour l’agent).

    ![Enregistrer l’agent](./media/backup-try-azure-backup-in-10-mins/agent.png)

À ce stade, vous avez achevé la création d’un coffre de sauvegarde et le téléchargement des composants nécessaires. À présent, vous allez installer l’agent de sauvegarde.

## Étape 3 : Installer l’agent Azure Backup sur votre serveur Windows Server ou sur votre client Windows

1. Une fois le téléchargement terminé, double-cliquez sur le fichier **MARSagentinstaller.exe** dans l’emplacement enregistré (vous pouvez également cliquer sur **Exécuter** au lieu d’enregistrer le fichier à l’étape précédente).

2. Choisissez le **dossier d’installation** et le **dossier du cache** requis pour l’agent.

    L’emplacement de cache spécifié doit présenter un espace libre au moins égal à 5 % du volume des données de sauvegarde.

    Cliquez sur **Next**.

    ![Espace de travail et cache](./media/backup-try-azure-backup-in-10-mins/recovery-services-agent-setup-wizard-1.png)

3. Vous pouvez continuer à vous connecter à Internet via les paramètres du proxy par défaut, ou si vous utilisez un serveur proxy pour vous connecter à Internet, dans l’écran **Configuration du proxy**, cochez la case **Utiliser les paramètres du proxy personnalisés** et entrez les détails du serveur proxy.

    Si vous utilisez un proxy authentifié, entrez les informations de nom d’utilisateur et mot de passe.

    Cliquez sur **Next**.

    ![Configuration du proxy](./media/backup-try-azure-backup-in-10-mins/proxy-configuration.png)

4. Cliquez sur **Installer**.

    ![Configuration du proxy](./media/backup-try-azure-backup-in-10-mins/agent-installation.png)

    L’agent Azure Backup installe .NET Framework 4.5 et Windows PowerShell (s’ils ne sont pas déjà installés) pour terminer l’installation.

5. Une fois l’agent installé, cliquez sur **Procéder à l’enregistrement** pour continuer le flux de travail.

    ![S’inscrire](./media/backup-try-azure-backup-in-10-mins/register.png)

6. Dans l’écran **Identification du coffre**, recherchez et sélectionnez le **fichier d’informations d’identification du coffre** téléchargé précédemment. Assurez-vous que le fichier d’informations d’identification du coffre se trouve dans un emplacement accessible par le programme d’installation.

    Cliquez sur **Next**.

    ![Informations d’identification du coffre](./media/backup-try-azure-backup-in-10-mins/vault-identification-with-creds.png)

7. Sur l’écran **Paramètre de chiffrement**, vous pouvez générer ou fournir une phrase secrète (16 caractères minimum). Pensez à enregistrer le mot de passe dans un emplacement sécurisé.

    > [AZURE.WARNING] Si la phrase secrète est perdue ou oubliée, Microsoft ne peut pas vous aider à récupérer les données de sauvegarde. L’utilisateur final détient la phrase secrète de chiffrement et Microsoft n’a aucune visibilité sur cette dernière. Enregistrez le fichier dans un emplacement sécurisé, car il vous sera demandé pour les opérations de récupération.

    Cliquez sur **Terminer**.

    ![Chiffrement](./media/backup-try-azure-backup-in-10-mins/encryption.png)

    L’**Assistant d’enregistrement du serveur** inscrit le serveur auprès de Microsoft Azure Backup.

    ![Chiffrement](./media/backup-try-azure-backup-in-10-mins/registering-server.png)

8. Une fois la **clé de chiffrement** définie, laissez la case **Démarrer Microsoft Azure Recovery Services Agent** cochée et cliquez sur **Fermer**.

    ![Chiffrement](./media/backup-try-azure-backup-in-10-mins/close-server-registration.png)

    L’ordinateur est désormais inscrit dans le coffre, et vous êtes prêt à configurer et à planifier vos options de sauvegarde.

## Étape 4 : Sauvegarder les fichiers et dossiers

1. Dans le composant logiciel enfichable MMC (qui s’ouvre automatiquement si vous avez laissé la case **Démarrer Microsoft Azure Recovery Services Agent** cochée), cliquez sur **Planifier la sauvegarde**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-try-azure-backup-in-10-mins/snap-in-schedule-backup-closeup.png)

2. Dans l’écran **Mise en route**, cliquez sur **Suivant**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-try-azure-backup-in-10-mins/getting-started.png)

3. Dans l’écran **Sélectionner les éléments à sauvegarder**, cliquez sur **Ajouter des éléments**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-try-azure-backup-in-10-mins/add-items.png)

    Sélectionnez les éléments que vous souhaitez sauvegarder, puis cliquez sur **OK**. Azure Backup sur un serveur Windows Server ou un client Windows vous permet de protéger les fichiers et dossiers.

    ![Éléments de sauvegarde de Windows Server](./media/backup-try-azure-backup-in-10-mins/added-items.png)

    Cliquez sur **Next**.

    ![Éléments de sauvegarde de Windows Server](./media/backup-try-azure-backup-in-10-mins/selected-items.png)

4. Spécifiez la **planification de sauvegarde**, puis cliquez sur **Suivant**.

    Vous pouvez planifier des sauvegardes quotidiennes (au maximum 3 fois par jour) ou hebdomadaires.

    ![Éléments de sauvegarde de Windows Server](./media/backup-try-azure-backup-in-10-mins/specify-backup-schedule.png)

5. Sélectionnez la **stratégie de rétention** pour la copie de sauvegarde. Vous pouvez modifier la stratégie de rétention quotidienne, hebdomadaire, mensuelle et annuelle pour répondre à vos besoins.

    >[AZURE.NOTE] La planification de sauvegarde est détaillée dans cet [article](backup-azure-backup-cloud-as-tape.md).

     Cliquez sur **Suivant**

    ![Éléments de sauvegarde de Windows Server](./media/backup-try-azure-backup-in-10-mins/select-retention-policy.png)

6. Choisissez le type de sauvegarde initiale.

    Vous pouvez effectuer des sauvegardes automatiques sur le réseau ou vous pouvez sauvegarder en mode hors connexion. Le reste de cet article suit le processus de sauvegarde automatique. Si vous préférez effectuer une sauvegarde en mode hors connexion, lisez cet article pour en savoir plus sur le [flux de travail de sauvegarde en mode hors connexion dans Azure Backup](backup-azure-backup-import-export.md).

    Cliquez sur **Suivant**

    ![Sauvegarde initiale de Windows Server](./media/backup-try-azure-backup-in-10-mins/choose-initial-backup-type.png)

7. Dans l’écran **Confirmation**, passez en revue les informations, puis cliquez sur **Terminer**.

    ![Sauvegarde initiale de Windows Server](./media/backup-try-azure-backup-in-10-mins/confirmation.png)

8. Dès que l’Assistant a terminé la création de la **planification de sauvegarde**, cliquez sur **Fermer**.

    ![Sauvegarde initiale de Windows Server](./media/backup-try-azure-backup-in-10-mins/backup-schedule-created.png)

9. Dans le composant logiciel enfichable MMC, cliquez sur **Sauvegarder maintenant** pour effectuer l’amorçage initial sur le réseau.

    ![Sauvegarder Windows Server maintenant](./media/backup-try-azure-backup-in-10-mins/snap-in-backup-now.png)

10. Dans l’écran **Confirmation**, vérifiez les paramètres utilisés par l’Assistant pour sauvegarder les données de l’ordinateur et cliquez sur **Sauvegarder**.

    ![Sauvegarder Windows Server maintenant](./media/backup-try-azure-backup-in-10-mins/backup-now-confirmation.png)

11. Cliquez sur **Fermer** pour fermer l’Assistant. Vous pouvez effectuer cette opération avant la fin du **processus de sauvegarde** lequel continuera de s’exécuter à l’arrière-plan.

    ![Sauvegarder Windows Server maintenant](./media/backup-try-azure-backup-in-10-mins/backup-progress.png)

12. Une fois la sauvegarde initiale terminée, la vue **Tâches** de la console Azure Backup indique l’état « Tâche terminée ».

    ![RI terminé](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

Félicitations ! Vous avez sauvegardé vos fichiers et vos dossiers dans Azure Backup.

## Étapes suivantes
- Pour en savoir plus sur Azure Backup, consultez la [vue d’ensemble d’Azure Backup](backup-introduction-to-azure-backup.md).
- En savoir plus sur la [préparation de votre environnement pour sauvegarder des ordinateurs Windows](backup-configure-vault.md)
- En savoir plus sur la [sauvegarde d’un serveur Windows Server](backup-azure-backup-windows-server.md)
- Consultez le [forum Azure Backup](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=AcomDC_0302_2016-->
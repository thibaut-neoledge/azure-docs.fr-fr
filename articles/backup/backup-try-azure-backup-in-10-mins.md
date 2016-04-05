<properties
   pageTitle="Apprendre à sauvegarder des fichiers et dossiers d’un environnement Windows vers Azure | Microsoft Azure"
   description="Apprenez à sauvegarder vos données Windows Server en créant un coffre, en installant l’agent de sauvegarde, puis en sauvegardant vos fichiers et dossiers dans Azure."
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
	 ms.date="03/28/2016"
	 ms.author="jimpark;"/>

# Premier aperçu : sauvegarder des fichiers et des dossiers de Windows Server ou client vers Azure

Cet article explique comment sauvegarder vos fichiers et dossiers Windows Server (ou d’un client Windows) sur Azure à l’aide d’Azure Backup. Vous verrez qu’il est extrêmement simple d’utiliser Azure Backup.

Pour sauvegarder vos fichiers et dossiers, il vous suffit d’exécuter ces quelques étapes :

![Étape 1 :](./media/backup-try-azure-backup-in-10-mins/step-1.png) Obtention d’un abonnement Azure (si nécessaire).<br> ![Étape 2](./media/backup-try-azure-backup-in-10-mins/step-2.png) Création d’un coffre de sauvegarde et téléchargement des composants nécessaires.<br> ![Étape 3](./media/backup-try-azure-backup-in-10-mins/step-3.png) Préparation de votre serveur Windows Server ou votre client Windows en installant et en inscrivant ces composants.<br> ![Étape 4](./media/backup-try-azure-backup-in-10-mins/step-4.png) Sauvegarde de vos données.


![Comment sauvegarder votre ordinateur Windows avec Azure Backup](./media/backup-try-azure-backup-in-10-mins/windows-machine-backup-process.png)

## Étape 1 : obtenir un abonnement Azure

Si vous ne disposez pas d’un abonnement Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/) qui vous permet d’accéder à n’importe quel service Azure.

>[AZURE.NOTE] Vous pouvez ignorer cette étape si vous possédez déjà un abonnement Azure.

## Étape 2 : création d’un coffre de sauvegarde et téléchargement des composants nécessaires.

Pour sauvegarder vos fichiers et dossiers, vous devez créer un coffre de sauvegarde dans la région géographique où vous souhaitez stocker les données.

1. Si ce n’est pas déjà fait, connectez-vous au [portail Azure](https://portal.azure.com/) à l’aide de votre abonnement Azure.

2. Cliquez sur **Nouveau > Intégration hybride > Sauvegarde**.

    ![Préparez-vous à sauvegarder vos fichiers et dossiers](./media/backup-try-azure-backup-in-10-mins/second-blade-backup.png)

    a. Dans l’écran qui s’affiche, dans **Nom**, entrez un nom convivial permettant d’identifier le coffre de sauvegarde. Tapez un nom contenant entre 2 et 50 caractères. Il doit commencer par une lettre, et ne peut contenir que des lettres, des chiffres et des traits d’union.

    b. Pour **Région**, sélectionnez la région géographique du coffre de sauvegarde. En choisissant une région proche de votre emplacement, vous pouvez réduire la latence du réseau lors de la sauvegarde dans Azure.

    c. Cliquez sur **Create vault**.

    ![création d'un coffre](./media/backup-try-azure-backup-in-10-mins/demo-vault-name.png)

    Pour connaître l’état d’avancement de l’opération, vous pouvez surveiller les notifications au bas du portail.

    ![Processus d’archivage dans un coffre](./media/backup-try-azure-backup-in-10-mins/creatingvault1.png)

    Une fois le coffre de sauvegarde créé, il est répertorié dans les ressources de Recovery Services en tant qu’**Actif**.

    ![L’état du coffre est actif](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

3. Sélectionnez les options de **redondance de stockage**.

    Nous vous recommandons de sélectionner l’option de redondance de stockage juste après avoir créé un coffre et avant d’y inscrire des ordinateurs. Une fois qu’un élément a été inscrit dans l’archivage, l’option de redondance de stockage est verrouillée et ne peut pas être modifiée.

    Si vous utilisez Azure comme principal point de terminaison du stockage de sauvegarde, pensez à utiliser l’option [Stockage géo-redondant](../storage/storage-redundancy.md#geo-redundant-storage) (par défaut).

    Si vous utilisez Azure comme point de terminaison du stockage de sauvegarde secondaire, utilisez plutôt le [Stockage redondant en local](../storage/storage-redundancy.md#locally-redundant-storage). Cette option vous permet d’alléger vos coûts de stockage de données dans Azure tout en bénéficiant d’un niveau de durabilité inférieur acceptable pour des copies tertiaires.

    a. Cliquez sur le coffre que vous venez de créer.

    b. Dans la page Démarrage rapide, cliquez sur **Configurer**.

    ![Configurer le coffre](./media/backup-try-azure-backup-in-10-mins/configure-vault.png)

    c. Choisissez l’option de redondance de stockage appropriée.

    ![Choisir les options de redondance du stockage](./media/backup-try-azure-backup-in-10-mins/geo-redundant.png)

    Si vous avez sélectionné **Localement redondant**, vous devez cliquer sur **Enregistrer** car **Géo-redondant** est l’option par défaut.

    d. Pour revenir à la liste des ressources de **Recovery Services**, cliquez sur **Recovery Services** dans le volet de navigation gauche.

    ![Services de récupération](./media/backup-try-azure-backup-in-10-mins/rs-left-nav.png)

    Vous devez à présent authentifier votre ordinateur Windows auprès du coffre de sauvegarde que vous venez de créer. L’authentification s’effectue à l’aide des informations d’identification du coffre.

4.  Cliquez sur le coffre que vous venez de créer.

    ![Sélectionner votre nouveau coffre](./media/backup-try-azure-backup-in-10-mins/demo-vault-active-full-screen.png)

5. Dans la page **Démarrage rapide**, cliquez sur **Télécharger les informations d’identification du coffre**.

    ![Télécharger les informations d’identification du coffre](./media/backup-try-azure-backup-in-10-mins/downloadvc.png)

    Le portail génère une information d’identification de coffre en associant le nom du coffre à la date du jour.

    >[AZURE.NOTE] Le fichier d’informations d’identification du coffre est utilisé uniquement pendant le processus d’inscription et expire au bout de 48 heures.

6. Cliquez sur **Enregistrer** pour télécharger les informations d’identification du coffre dans le dossier **Téléchargements** en local. Vous pouvez également sélectionner l’option **Enregistrer sous** à partir du menu **Enregistrer** pour spécifier un emplacement pour les informations d’identification du coffre.

    Vérifiez que les informations d’identification de coffre sont enregistrées dans un emplacement accessible à partir de votre machine. Si le fichier est stocké dans un partage de fichiers/SMB, vérifiez les autorisations d’accès.

    >[AZURE.NOTE] Vous n’avez pas besoin d’ouvrir les informations d’identification du coffre à ce stade de la procédure.

    Vous devez à présent télécharger l’agent de sauvegarde.

7. Cliquez sur **Recovery Services** dans le volet de navigation de gauche, puis sur le coffre de sauvegarde que vous souhaitez inscrire auprès d’un serveur.

    ![Sélectionner le coffre de sauvegarde](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

8. Sur la page de démarrage rapide, cliquez sur **Agent pour Windows Server ou System Center Data Protection Manager ou Windows Client > Enregistrer**.

    ![Enregistrer l’agent Azure Backup](./media/backup-try-azure-backup-in-10-mins/agent.png)

À ce stade, vous avez achevé la création d’un coffre de sauvegarde et le téléchargement des composants nécessaires. Vous allez maintenant installer l’agent de sauvegarde.

## Étape 3 : préparation de votre serveur Windows Server ou de votre client Windows par l’installation et l’enregistrement des composants de sauvegarde

1. Une fois le téléchargement terminé, double-cliquez sur le fichier **MARSagentinstaller.exe** dans l’emplacement enregistré (vous pouvez également cliquer sur **Exécuter** au lieu d’enregistrer le fichier à l’étape précédente).

2. Choisissez le **dossier d’installation** et le **dossier du cache** requis pour l’agent.

    L’emplacement de cache spécifié doit présenter un espace libre au moins égal à 5 % du volume des données de sauvegarde.

    Cliquez sur **Next**.

    ![Dossier d’installation et de cache](./media/backup-try-azure-backup-in-10-mins/recovery-services-agent-setup-wizard-1.png)

3. (Facultatif) Si vous utilisez un serveur proxy pour vous connecter à Internet, dans l’écran **Configuration du proxy**, cochez la case **Utiliser les paramètres du proxy personnalisés** et entrez les détails du serveur proxy.

    Si vous utilisez un proxy authentifié, entrez les informations de nom d’utilisateur et mot de passe.

    Cliquez sur **Next**.

    ![Configuration du proxy](./media/backup-try-azure-backup-in-10-mins/proxy-configuration.png)

4. Cliquez sur **Installer**.

    L’agent Azure Backup installe .NET Framework 4.5 et Windows PowerShell (s’ils ne sont pas déjà installés) pour terminer l’installation.

5. Une fois l’agent installé, cliquez sur **Procéder à l’enregistrement** pour poursuivre le flux de travail.

    ![S’inscrire](./media/backup-try-azure-backup-in-10-mins/register.png)

6. Dans l’écran **Identification du coffre**, recherchez et sélectionnez le **fichier d’informations d’identification du coffre** téléchargé précédemment. Assurez-vous que le fichier d’informations d’identification du coffre se trouve dans un emplacement accessible par le programme d’installation.

    Cliquez sur **Next**.

    ![Identifier le coffre](./media/backup-try-azure-backup-in-10-mins/vault-identification-with-creds.png)

7. Sur l’écran **Paramètre de chiffrement**, vous pouvez générer ou fournir une phrase secrète (16 caractères minimum). Pensez à enregistrer le mot de passe dans un emplacement sécurisé.

    > [AZURE.WARNING] En cas de perte ou d’oubli de la phrase secrète, Microsoft ne pourra pas vous aider à récupérer les données de sauvegarde. Vous êtes seul détenteur de la phrase secrète de chiffrement et Microsoft n’a aucune visibilité sur cette dernière. Enregistrez le fichier dans un emplacement sécurisé, car il vous sera demandé pour les opérations de récupération.

    Cliquez sur **Terminer**.

    ![Paramètre de chiffrement](./media/backup-try-azure-backup-in-10-mins/encryption.png)

    L’**Assistant d’enregistrement du serveur** inscrit le serveur auprès de Microsoft Azure Backup.

8. Une fois la **clé de chiffrement** définie, laissez la case **Démarrer Microsoft Azure Recovery Services Agent** cochée et cliquez sur **Fermer**.

    L’ordinateur est désormais inscrit dans le coffre, et vous êtes prêt à configurer et à planifier vos options de sauvegarde.

## Étape 4 : sauvegarde de vos données

1. Dans **Backup Agent** (qui s’ouvre automatiquement si vous avez laissé la case **Démarrer Microsoft Azure Recovery Services Agent** cochée), cliquez sur **Planifier la sauvegarde**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-try-azure-backup-in-10-mins/snap-in-schedule-backup-closeup.png)

2. Dans l’écran **Mise en route**, cliquez sur **Suivant**.

3. Dans l’écran **Sélectionner les éléments à sauvegarder**, cliquez sur **Ajouter des éléments**. Azure Backup sur un serveur Windows Server ou un client Windows vous permet de protéger vos fichiers et dossiers.

    ![Planifier une sauvegarde de Windows Server](./media/backup-try-azure-backup-in-10-mins/add-items.png)

    Sélectionnez les éléments que vous souhaitez sauvegarder, puis cliquez sur **OK**.

    Cliquez sur **Next**.

4. Spécifiez la **planification de sauvegarde**, puis cliquez sur **Suivant**.

    Vous pouvez planifier des sauvegardes quotidiennes (au maximum 3 fois par jour) ou hebdomadaires.

    ![Spécifier la planification de sauvegarde](./media/backup-try-azure-backup-in-10-mins/specify-backup-schedule.png)

5. Sélectionnez **Stratégie de rétention** pour la copie de sauvegarde. Vous pouvez modifier la stratégie de rétention quotidienne, hebdomadaire, mensuelle et annuelle pour répondre à vos besoins.

    >[AZURE.NOTE] La planification de sauvegarde est détaillée dans cet [article](backup-azure-backup-cloud-as-tape.md).

     Cliquez sur **Suivant**

    ![Sélectionner la stratégie de rétention](./media/backup-try-azure-backup-in-10-mins/select-retention-policy.png)

6. Choisissez le type de sauvegarde initiale.

    Vous pouvez effectuer des sauvegardes automatiques sur le réseau ou vous pouvez sauvegarder en mode hors connexion. Le reste de cet article suit le processus de sauvegarde automatique. Si vous préférez effectuer une sauvegarde en mode hors connexion, lisez cet article pour en savoir plus sur le [flux de travail de sauvegarde en mode hors connexion dans Azure Backup](backup-azure-backup-import-export.md).

    Cliquez sur **Suivant**

    ![Type de sauvegarde initiale](./media/backup-try-azure-backup-in-10-mins/choose-initial-backup-type.png)

7. Dans l’écran **Confirmation**, passez en revue les informations, puis cliquez sur **Terminer**.

8. Dès que l’Assistant a terminé la création de la **planification de sauvegarde**, cliquez sur **Fermer**.

9. Dans **Backup Agent**, cliquez sur **Sauvegarder maintenant** pour effectuer l’amorçage initial sur le réseau.

    ![Sauvegarder Windows Server maintenant](./media/backup-try-azure-backup-in-10-mins/snap-in-backup-now.png)

10. Dans l’écran **Confirmation**, vérifiez les paramètres utilisés par l’Assistant pour sauvegarder les données de l’ordinateur et cliquez sur **Sauvegarder**.

11. Cliquez sur **Fermer** pour fermer l’Assistant. Vous pouvez fermer l’Assistant avant la fin du processus de sauvegarde ; celui-ci continuera de s’exécuter en arrière-plan.

12. Une fois la sauvegarde initiale terminée, la vue **Tâches** de la console Azure Backup indique l’état « Tâche terminée ».

    ![Sauvegarde initiale terminée](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

Félicitations ! Vous avez sauvegardé vos fichiers et vos dossiers dans Azure Backup.

## Étapes suivantes
- Pour en savoir plus sur Azure Backup, consultez la page [Présentation de la Sauvegarde Microsoft Azure](backup-introduction-to-azure-backup.md).
- En savoir plus sur la [préparation de votre environnement et la sauvegarde d’ordinateurs Windows](backup-configure-vault.md).
- Découvrez comment sauvegarder des machines virtuelles Azure en lisant cet [article d’introduction](backup-azure-vms-first-look.md).
- Lisez les dernières discussions sur Azure Backup dans le [Forum Azure Backup](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=AcomDC_0330_2016-->
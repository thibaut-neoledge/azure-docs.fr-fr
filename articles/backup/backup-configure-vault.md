<properties
	pageTitle="Préparer votre environnement à la sauvegarde d’un serveur Windows ou d’un ordinateur client Windows | Microsoft Azure"
	description="Préparez votre environnement à des fenêtres de sauvegarde en créant un coffre de sauvegarde, en téléchargeant les informations d’identification et en installant l’agent de sauvegarde."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""
	keywords="coffre de sauvegarde ; agent de sauvegarde ; fenêtres de sauvegarde ;"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="02/05/2016"
	ms.author="trinadhk; jimpark; markgal"/>


# Préparer votre environnement pour la sauvegarde de machines Windows dans Azure
Cet article répertorie les étapes requises pour préparer votre environnement pour la sauvegarde d’une machine Windows dans Azure.

| Étape | Nom | Détails |
| :------: | ---- | ------- |
| 1 | [création d'un coffre](#create-a-backup-vault) | Créez un coffre dans le [portail de gestion Azure Backup](http://manage.windowsazure.com). |
| 2 | [Télécharger les informations d’identification du coffre](#download-the-vault-credential-file) | Téléchargez les informations d’identification qui serviront à inscrire la machine Windows dans l’archivage de sauvegarde. |
| 3 | [Installer l’agent Azure Backup](#download-install-and-register-the-azure-backup-agent) | Installez l’agent et inscrivez le serveur dans l’archivage de sauvegarde à l’aide des informations d’identification du coffre. |

Si vous avez déjà suivi l’ensemble des étapes générales ci-dessus, vous pouvez démarrer la [sauvegarde de vos machines Windows](backup-azure-backup-windows-server.md). Sinon, exécutez les étapes détaillées ci-dessous pour vérifier que votre environnement est prêt.

## Avant de commencer
Pour préparer votre environnement pour la sauvegarde de machines Windows, vous avez besoin d’un compte Azure. Si vous n’en possédez pas, vous pouvez créer un [compte d’évaluation gratuit](https://azure.microsoft.com/pricing/free-trial/) en quelques minutes.

## Créer un archivage de sauvegarde
Pour sauvegarder des fichiers et données à partir d’une machine Windows ou de Data Protection Manager (DPM) dans Azure ou lors de la sauvegarde de machines virtuelles IaaS dans Azure, vous devez créer un archivage de sauvegarde dans la région géographique où vous voulez stocker les données.

**Pour créer un archivage de sauvegarde :**

1. Connectez-vous au [portail de gestion](https://manage.windowsazure.com/).

2. Cliquez sur **Nouveau** -> **Services de données** -> **Services de récupération** -> **Archivage de sauvegarde** et choisissez **Création rapide**.

    ![Créer le coffre](./media/backup-configure-vault/createvault1.png)

3. Pour le paramètre **Nom**, entrez un nom convivial permettant d’identifier l’archivage de sauvegarde. Cette opération doit être unique pour chaque abonnement.

    Pour le paramètre **Région**, sélectionnez la région géographique de l’archivage de sauvegarde. Le choix définit la région géographique où vos données de sauvegarde sont envoyées. En choisissant une zone géographique proche de votre emplacement, vous pouvez réduire la latence du réseau lors de la sauvegarde dans Azure.

    Cliquez sur **Créer un archivage** pour terminer le flux de travail.

    La création du coffre de sauvegarde peut prendre du temps. Pour vérifier l’état d’avancement de l’opération, vous pouvez contrôler les notifications au bas du portail.

    ![Création d’un archivage](./media/backup-configure-vault/creatingvault1.png)

    Une fois l’opération terminée, un message vous indique que l’archivage a bien été créé. L’archivage est également répertorié dans les ressources Recovery Services en tant que **Actif**. ![Création d’un état de l’archivage](./media/backup-configure-vault/backupvaultstatus1.png)

> [AZURE.IMPORTANT] Nous vous recommandons d’identifier votre option de redondance de stockage juste après la création de l’archivage et avant l’inscription d’une machine dans l’archivage. Une fois qu’un élément a été inscrit dans l’archivage, l’option de redondance de stockage est verrouillée et ne peut pas être modifiée.
>
> **Pour en savoir plus sur les options de redondance de stockage, consultez cette [vue d’ensemble](backup-azure-storage-redundancy-options.md).**

## Téléchargement du fichier d’informations d’identification de coffre
Le serveur local (client Windows ou Windows Server ou serveur Data Protection Manager) doit être authentifié avec un archivage de sauvegarde avant de pouvoir sauvegarder des données dans Azure. L’authentification s’effectue à l’aide des « informations d’identification du coffre ». Le fichier d’informations d’identification de coffre est téléchargé via un canal sécurisé depuis le portail Azure et le service Azure Backup n’a pas connaissance de la clé privée du certificat, qui n’est pas conservée dans le portail ou le service.

En savoir plus sur l’[utilisation des informations d’identification de coffre pour s’authentifier auprès du service Azure Backup](backup-introduction-to-azure-backup.md#what-is-the-vault-credential-file).

**Pour télécharger les informations d’identification de coffre sur un ordinateur local :**

1. Connectez-vous au [portail de gestion](https://manage.windowsazure.com/).

2. Cliquez sur **Recovery Services** dans le volet de navigation gauche et sélectionnez l’archivage de sauvegarde que vous avez créé.

3.  Cliquez sur l’icône du cloud pour accéder à la page de *démarrage rapide* de l’archivage de sauvegarde.

    ![Aperçu rapide](./media/backup-configure-vault/quickview.png)

4. Dans la page de **démarrage rapide**, cliquez sur **Télécharger les informations d’identification du coffre**.

    ![Télécharger](./media/backup-configure-vault/downloadvc.png)

    Le portail générera une information d'identification de coffre en combinant le nom du coffre et la date actuelle. Le fichier d’informations d’identification de coffre est utilisé uniquement pendant le flux de travail d’inscription et expire au bout de 48 heures.

    Le fichier d’informations d’identification de coffre peut être téléchargé à partir du portail.

5. Cliquez sur **Save** pour télécharger les informations d'identification de coffre vers les dossier de téléchargements du compte local, ou sélectionnez **Save As** à partir du menu *Save* pour spécifier un emplacement pour les informations d'identification de coffre.

    Vous n’avez pas besoin d’ouvrir les informations d’identification de coffre pour l’instant.

    Vérifiez que les informations d’identification de coffre sont enregistrées dans un emplacement accessible à partir de votre machine. Si elles sont stockées dans un partage de fichiers/SMB, vérifiez les autorisations d’accès.

## Téléchargement, installation et inscription de l’agent Azure Backup
Après avoir créé l’archivage de sauvegarde Azure, un agent doit être installé sur chacune de vos machines Windows (Windows Server, client Windows, System Center Data Protection Manager ou Azure Backup Server) pour permettre la sauvegarde des données et des applications dans Azure.

**Pour télécharger, installer et inscrire l’agent :**

1. Connectez-vous au [portail de gestion](https://manage.windowsazure.com/).

2. Cliquez sur **Recovery Services**, puis sélectionnez le coffre de sauvegarde que vous souhaitez enregistrer avec un serveur.

3. Dans la page de démarrage rapide, cliquez sur **Windows Server ou System Center Data Protection Manager ou Windows Client > Enregistrer**.

    ![Enregistrer l’agent](./media/backup-configure-vault/agent.png)

4. Une fois le téléchargement de *MARSagentinstaller.exe* terminé, cliquez sur **Exécuter** (ou double-cliquez sur **MARSAgentInstaller.exe** dans l’emplacement d’enregistrement). Choisissez le *dossier d’installation* et le *dossier de cache* requis pour l’agent, puis cliquez sur **Suivant**.

    L’emplacement de cache spécifié doit présenter un espace libre au moins égal à 5 % du volume des données de sauvegarde.

    ![Espace de travail et cache](./media/backup-configure-vault/recovery-services-agent-setup-wizard-1.png)

5. Si vous utilisez un serveur proxy pour vous connecter à Internet, dans l’écran **Configuration du proxy**, entrez les détails du serveur proxy. Si vous utilisez un proxy authentifié, entrez les informations de nom d’utilisateur et mot de passe, puis cliquez sur **Suivant**.

    L’agent Azure Backup installe .NET Framework 4.5 et Windows PowerShell (s’il n’est pas déjà installé) pour terminer l’installation.

6. Une fois l’agent installé, cliquez sur **Procéder à l’enregistrement** pour continuer le flux de travail.

    ![S’inscrire](./media/backup-configure-vault/register.png)

7. Dans l’écran **Identification du coffre**, recherchez et sélectionnez le *fichier d’informations d’identification du coffre* téléchargé précédemment.

    ![Informations d’identification du coffre](./media/backup-configure-vault/vc.png)

    Le fichier d’informations d’identification du coffre est uniquement valide pendant 48 heures (à partir de son téléchargement depuis le portail). Si vous rencontrez une erreur dans cet écran (par exemple, « Le fichier d’informations d’identification du coffre a expiré »), connectez-vous au portail Azure et téléchargez de nouveau le fichier d’informations d’identification du coffre.

    Assurez-vous que le fichier d’informations d’identification du coffre se trouve dans un emplacement accessible par le programme d’installation. Si vous rencontrez des erreurs liées à l’accès, copiez le fichier d’informations d’identification d’archivage dans un emplacement temporaire sur cet ordinateur et recommencez l’opération.

    Si vous rencontrez une erreur indiquant que les informations d’identification du coffre ne sont pas valides (par exemple, « Informations d’identification du coffre fournies non valides. »), cela signifie que le fichier est endommagé ou qu’il ne contient pas les dernières informations d’identification associées au service de récupération. Recommencez l’opération après avoir téléchargé un nouveau fichier d’informations d’identification de coffre à partir du portail. Cette erreur se produit généralement si l’utilisateur clique sur l’option *Télécharger les informations d’identification du coffre* coup sur coup. Dans ce cas, seul le dernier fichier d’informations d’identification du coffre est valide.

8. Sur l’écran **Paramètre de chiffrement**, vous pouvez *générer* ou *fournir* une phrase secrète (16 caractères minimum). Pensez à enregistrer le mot de passe dans un emplacement sécurisé.

    ![Chiffrement](./media/backup-configure-vault/encryption.png)

    > [AZURE.WARNING] Si la phrase secrète est perdue ou oubliée, Microsoft ne peut pas vous aider à récupérer les données de sauvegarde. L’utilisateur final détient la phrase secrète de chiffrement et Microsoft n’a aucune visibilité sur cette dernière. Enregistrez le fichier dans un emplacement sécurisé, car il vous sera demandé pour les opérations de récupération.

9. Cliquez sur **Terminer**.

    La machine est désormais inscrite dans l’archivage et vous êtes prêt à démarrer la sauvegarde dans Microsoft Azure.

## Étapes suivantes
- Inscription pour un [compte d’évaluation gratuite de Microsoft Azure](https://azure.microsoft.com/pricing/free-trial/)
- [Sauvegarder un serveur ou un ordinateur client Windows](backup-azure-backup-windows-server.md).
- Si vous avez encore des questions, consultez le [Forum aux questions Azure Backup](backup-azure-backup-faq.md).
- Rendez-vous sur le [forum Azure Backup](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=AcomDC_0211_2016-->
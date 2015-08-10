<properties
   pageTitle="Téléchargement des informations d’identification de coffre dans Azure Backup | Microsoft Azure"
   description="Découvrez comment utiliser les informations d’identification de coffre pour authentifier votre ordinateur auprès de l’archivage de sauvegarde et du service Azure Backup"
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="shreeshd"
   editor=""/>
<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="07/01/2015"
   ms.author="jimpark"; "aashishr"/>

# Utilisation des informations d’identification de coffre pour s’authentifier auprès du service Azure Backup
Le serveur local (client Windows ou Windows Server ou serveur SCDPM) doit être authentifié avec un archivage de sauvegarde avant de pouvoir sauvegarder des données dans Azure. L’authentification s’effectue à l’aide des « informations d’identification du coffre ». Le concept d’informations d’identification de coffre est similaire à celui du fichier de « paramètres de publication » utilisé dans Azure PowerShell.

## Qu’est-ce que le fichier d’informations d’identification de coffre ?
Le fichier d’informations d’identification de coffre est un certificat qui est généré par le portail pour chaque archivage de sauvegarde. Le portail télécharge ensuite la clé publique pour le Service de contrôle d’accès (ou ACS). La clé privée du certificat est accessible à l’utilisateur dans le cadre du flux de travail donné comme entrée dans le flux de travail d’inscription de machine. Cela authentifie l’ordinateur pour envoyer des données de sauvegarde dans un archivage identifié dans le service Azure Backup.

Il est important de signaler que les informations d’identification de coffre sont utilisées uniquement pendant le flux de travail d’inscription. Il est de la responsabilité de l’utilisateur de s’assurer que le fichier d’informations d’identification de coffre n’est pas compromis. S’il tombe entre les mains d’un utilisateur non autorisé, le fichier d’informations d’identification de coffre peut servir à inscrire d’autres ordinateurs pour le même archivage. Toutefois, comme les données de sauvegarde sont chiffrées à l’aide d’une phrase secrète qui appartient au client, les données de sauvegarde existantes ne peuvent pas être compromises. Pour atténuer ce problème, les informations d’identification de coffre expirent au bout de 48 heures. Vous pouvez télécharger les informations d’identification d’un archivage de sauvegarde autant de fois que vous le souhaitez, mais seul le dernier fichier d’informations d’identification de coffre est applicable pendant le flux de travail d’inscription.

## Téléchargement du fichier d’informations d’identification de coffre
Le fichier d’informations d’identification de coffre est téléchargé via un canal sécurisé depuis le portail Azure. Le service Azure Backup n’a pas connaissance de la clé privée du certificat et cette dernière n’est pas conservée dans le portail ou le service. Procédez comme suit pour télécharger les informations d’identification de coffre sur un ordinateur local.

1.  Connectez-vous au [portail de gestion](https://manage.windowsazure.com/).
2.  Cliquez sur **Recovery Services** dans le volet de navigation gauche et sélectionnez l’archivage de sauvegarde que vous avez créé.
3. Cliquez sur l’icône du cloud pour accéder à la page de démarrage rapide de l’archivage de sauvegarde.

    ![Aperçu rapide](./media/backup-azure-backup-download-vc/quickview.png)

4.  Dans la page de démarrage rapide, cliquez sur **Télécharger les informations d’identification du coffre**. Le portail génère le fichier d’informations d’identification de coffre que vous pouvez télécharger.

    ![Télécharger](./media/backup-azure-backup-download-vc/downloadvc.png)

5.  Le portail générera une information d'identification de coffre en combinant le nom du coffre et la date actuelle. Cliquez sur **Enregistrer** pour télécharger les informations d’identification de coffre dans le dossier de téléchargements du compte local, ou sélectionnez Enregistrer sous dans le menu Enregistrer pour spécifier un emplacement pour les informations d’identification du coffre.

## Remarques
- Depuis mars 2015, les utilisateurs n’ont pas la possibilité de télécharger les informations d’identification de coffre par programme (par ex., via PowerShell).
- Vérifiez que les informations d’identification de coffre sont enregistrées dans un emplacement accessible à partir de votre ordinateur. Si elles sont stockées dans un partage de fichiers/SMB, vérifiez les autorisations d’accès.
- Le fichier d’informations d’identification de coffre est utilisé uniquement pendant le flux de travail d’inscription.
- Le fichier d’informations d’identification de coffre expire au bout de 48 heures et peut être téléchargé à partir du portail.
- Reportez-vous au [Forum Aux Questions](backup-azure-backup-faq.md) Azure Backup pour toute question sur le flux de travail.

## Étapes suivantes
[Téléchargement, inscription et installation de l’agent Azure Backup](backup-azure-backup-download-register)

<!-----HONumber=July15_HO5-->
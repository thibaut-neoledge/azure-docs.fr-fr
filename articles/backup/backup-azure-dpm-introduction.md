<properties
	pageTitle="Présentation d'Azure DPM Backup | Microsoft Azure"
	description="Présentation de la sauvegarde de serveurs DPM à l'aide du service Azure Backup"
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
	ms.date="06/12/2015"
	ms.author="jimpark"/>

# Présentation d'Azure DPM Backup

Cet article présente l'utilisation de Microsoft Azure Backup pour protéger vos serveurs System Center Data Protection Manager (DPM) et vos charges de travail. En le lisant, vous comprendrez :

- le fonctionnement de la sauvegarde du serveur Azure DPM ;
- les conditions requises pour une expérience de sauvegarde fluide ;
- les erreurs classiques rencontrées et comment les gérer ;
- Scénarios pris en charge

System Center DPM sauvegarde les données des fichiers et des applications. Les données sauvegardées dans DPM peuvent être stockées sur bande, sur disque, ou sauvegardées dans Azure avec Microsoft Azure Backup. DPM interagit avec Azure Backup comme suit :

- **DPM déployé comme un serveur physique ou une machine virtuelle en local** : si DPM est déployé comme un serveur physique ou comme une machine virtuelle Hyper-V en local, vous pouvez sauvegarder les données dans un coffre Azure Backup en plus d'une sauvegarde sur disque et sur bande.
- **DPM déployé comme une machine virtuelle Azure** : depuis la mise à jour 3 de System Center 2012 R2, DPM peut être déployé comme une machine virtuelle Azure. Si DPM est déployé comme une machine virtuelle Azure, vous pouvez sauvegarder les données sur des disques Azure connectés à la machine virtuelle DPM Azure, ou décharger le stockage de données en les sauvegardant dans un coffre Azure Backup.

## Pourquoi sauvegarder vos serveurs DPM ?

Les avantages commerciaux de l'utilisation d'Azure Backup pour la sauvegarde de serveurs DPM sont les suivants :

- Pour le déploiement DPM en local, vous pouvez utiliser la sauvegarde Azure au lieu d'un déploiement à long terme sur bande.
- Pour les déploiements DPM dans Azure, Azure Backup vous permet de décharger le stockage du disque Azure et ainsi d'évoluer en stockant les données plus anciennes dans Azure Backup et les nouvelles données sur un disque.

## Comment fonctionne la sauvegarde d'un serveur DPM ?
Pour sauvegarder une machine virtuelle, il est d’abord nécessaire de capturer un instantané des données. Le service Azure Backup lance le travail de sauvegarde à l’heure planifiée et déclenche l’extension de sauvegarde pour capturer un instantané. L’extension de sauvegarde se coordonne avec le service VSS in-guest pour assurer la cohérence et appelle l’API d’instantané d’objet blob du service Azure Storage une fois que la cohérence a été atteinte. Cela permet d’obtenir un instantané cohérent des disques de la machine virtuelle sans avoir à l’arrêter.

Une fois l’instantané capturé, les données sont transférées par le service Azure Backup dans le coffre de sauvegarde. Le service se charge d'identifier et de transférer uniquement les blocs qui ont été modifiés depuis la dernière sauvegarde, ce qui garantit l'efficacité du stockage des sauvegardes et les performances du réseau. Une fois le transfert de données terminé, l’instantané est supprimé et un point de récupération est créé. Ce point de récupération est affiché dans le portail de gestion Azure.

>[AZURE.NOTE]Pour les machines virtuelles Linux, seule une sauvegarde cohérente au niveau des fichiers est possible.

## Composants requis
Préparer Azure Backup pour sauvegarder des données DPM comme suit :

1. **Créer un coffre de sauvegarde** : créez un coffre dans la console Azure Backup.
2. **Télécharger les informations d'identification de l'archivage** : dans Azure Backup, téléchargez dans l'archivage le certificat de gestion que vous avez créé.
3. **Installer l'Agent Azure Backup et inscrire le serveur** : à partir d'Azure Backup, installez l'agent sur chaque serveur DPM et inscrivez le serveur DPM dans le coffre de sauvegarde.

### Créer un coffre de sauvegarde
Pour démarrer la sauvegarde de vos machines virtuelles Azure, vous devez d’abord créer un coffre de sauvegarde. L’archivage est une entité qui stocke les sauvegardes et les points de récupération créés au fil du temps. L’archivage contient également les stratégies de sauvegarde qui seront appliquées aux machines virtuelles en cours de sauvegarde.

1. Connectez-vous au [portail de gestion](http://manage.windowsazure.com/).
2. Cliquez sur **Nouveau** -> **Services de données** -> **Services de récupération** -> **Archivage de sauvegarde** > **Création rapide**. Si vous disposez de plusieurs abonnements associés à votre compte professionnel, choisissez l’abonnement correct à associer au coffre de sauvegarde. Dans chaque abonnement Azure, vous pouvez avoir plusieurs coffres de sauvegarde pour organiser les données protégées.
3. Dans **Name**, entrez un nom convivial pour identifier le coffre. Cette opération doit être unique pour chaque abonnement.
4. Dans **Region**, sélectionnez la région géographique du coffre. Notez que l’archivage doit se trouver dans la même région que les machines virtuelles que vous souhaitez protéger. Si vous avez des machines virtuelles dans différentes régions, créez un archivage dans chacune d’elles. Il est inutile de spécifier des comptes de stockage pour stocker les données de sauvegarde : le coffre de sauvegarde et le service Azure Backup s'en chargent automatiquement.
    > [AZURE.NOTE] La sauvegarde de machines virtuelles à l'aide du service Azure Backup est uniquement prise en charge dans les [régions sélectionnées](http://azure.microsoft.com/regions/#services). Vérifiez la liste des régions prises en charge. Si la région que vous recherchez n'est pas prise en charge pour le moment, elle n'apparaîtra pas dans la liste déroulante pendant la création de l'archivage.

5. Dans **Abonnement**, entrez l'abonnement Azure à utiliser avec le coffre de sauvegarde.
6. Cliquez sur **Créer un archivage**. ![Créer un coffre de sauvegarde](./media/backup-azure-dpm-introduction/backup_vaultcreate.png)

    La création du coffre de sauvegarde peut prendre du temps. Surveillez les notifications d’état en bas du portail. ![Créer une notification toast l’archivage](./media/backup-azure-dpm-introduction/creating-vault.png)

    Un message confirme que l'archivage a été correctement créé et l'archivage est affiché dans la page Services de récupération avec l'état **Actif**.

    ![Liste des archivages de sauvegarde](./media/backup-azure-dpm-introduction/backup_vaultslist.png)

7. Cliquez sur le coffre de sauvegarde pour accéder à la page **Démarrage rapide**, où sont affichées les instructions pour la sauvegarde des serveurs DPM. ![Instructions de sauvegarde de machines virtuelles dans la page Tableau de bord](./media/backup-azure-dpm-introduction/vmbackup-instructions.png)

    > [AZURE.NOTE]Assurez-vous que l’option de redondance de stockage appropriée est choisie juste après la création de l’archivage. En savoir plus sur la [définition de l’option de redondance de stockage dans le coffre de sauvegarde](http://azure.microsoft.com/documentation/articles/backup-azure-backup-create-vault/#azure-backup---storage-redundancy-options).

### Télécharger les informations d'identification de coffre
1. Cliquez sur **Services de récupération** puis sur le coffre de sauvegarde. Dans la page de **démarrage rapide**, cliquez sur **Télécharger les informations d'identification de coffre** pour télécharger le fichier d'informations d'identification et l'enregistrer dans un emplacement sécurisé. Comme vous ne pouvez pas modifier les informations d'identification, vous n'avez pas besoin d'ouvrir l'emplacement. Pour des raisons de sécurité, la clé dans le fichier expire au bout de 48 heures.

2. Copiez le fichier vers un emplacement sécurisé facilement accessible par les serveurs DPM que vous souhaitez inscrire dans le coffre de sauvegarde Azure. Vous devez sélectionner le fichier lorsque vous installez l'agent Azure Backup.

### Installer l'agent Azure Backup et inscrire le serveur
Vous allez télécharger le fichier d'installation de l'Agent et l’exécuter sur chaque serveur DPM contenant les données que vous souhaitez sauvegarder. Les agents sont stockés dans le **Centre de téléchargement Azure** et ils ont leur propre processus d'installation. Lorsque vous exécutez le programme d'installation, l'agent est installé et le serveur DPM est inscrit auprès du coffre. Notez les points suivants :

- Vous aurez besoin des autorisations administratives sur le serveur DPM pour installer l'agent.
- Pour installer l'agent sur plusieurs serveurs DPM, vous pouvez placer le fichier d'installation sur une ressource réseau partagée ou utiliser la stratégie de groupe ou des produits de gestion comme System Center Configuration Manager.
- Vous n'avez pas besoin de redémarrer le serveur DPM après l'installation.

#### Pour installer l'agent de sauvegarde et inscrire le serveur

1. Dans la page de **démarrage rapide** du coffre Azure Backup, cliquez sur **Télécharger l'agent Azure Backup**, sélectionnez **Pour Windows Server ou System Center Data Protection Manager ou client Windows**. Téléchargez l'application sur le serveur DPM sur lequel vous souhaitez l'exécuter.
2. Exécutez le fichier d'installation **MARSAgentInstaller.exe**. Acceptez les termes du contrat de service et installez les logiciels requis manquants.
3. Dans la page **Paramètres d'installation**, sélectionnez le **dossier d'installation** et l'**emplacement du cache**.

    L'emplacement par défaut du cache est <system drive>:\Program Files\Azure Backup Agent. Dans l'emplacement du cache, le processus d'installation crée un sous-dossier nommé **Scratch** dans le dossier **Azure Backup Agent**. L'emplacement du cache doit avoir au moins 2,5 gigaoctets (Go) d'espace libre (ou 10 % de la taille des données qui seront sauvegardées dans Azure). Seuls les administrateurs système et les membres locaux du groupe Administrateurs ont accès au répertoire du cache pour empêcher les attaques par déni de service.

4. Dans la page **Configuration du proxy**, définissez les paramètres de proxy personnalisés permettant à l'agent de se connecter à Azure. Si vous ne configurez aucun paramètre, les paramètres d'accès à Internet par défaut sur le serveur DPM seront utilisés. Notez que si vous utilisez un serveur proxy nécessitant une authentification, vous devez compléter les informations sur cette page.
5. Dans la page **Abonnement à Microsoft Update**, nous vous recommandons d'activer les mises à jour. Si le serveur est déjà activé pour les mises à jour automatiques, cette étape est ignorée. Notez que les paramètres Microsoft Update s'appliquent à toutes les mises à jour de produits Microsoft et ne sont pas exclusifs à l'agent Azure Backup.
6. La page **Installation** s'affiche. L'installation vérifie que le logiciel requis est installé puis finalise la configuration. Lorsque l'opération est terminée, vous recevrez un message confirmant que l'agent Azure Backup a été installé avec succès. À ce stade, vous pouvez choisir de rechercher les mises à jour. Nous vous recommandons d'autoriser la vérification des mises à jour.
7. Cliquez sur **Passer à l'inscription** pour inscrire le serveur dans l'archivage.
8. Dans la page **Identification de l'archivage**, sélectionnez le fichier d'inscription d'archivage généré dans le coffre Azure Backup.
9. Dans la page **Paramètre de chiffrement**, indiquez les détails du code secret ou générez-en un automatiquement.
10. Cliquez sur Générer un code secret suivi par Copier dans le Presse-papiers. Vous recevrez un message indiquant que votre code secret a été copié dans le Presse-papiers. Il est maintenant recommandé d'ouvrir le bloc-notes et de coller le code secret à partir du Presse-papiers, d'enregistrer le fichier, puis de l'imprimer avant de mettre la copie en lieu sûr. Cliquez sur S'inscrire pour inscrire votre serveur DPM auprès de votre coffre de sauvegarde.

    > [AZURE.TIP] Au moment de définir le paramètre de chiffrement, n'oubliez pas de copier la phrase secrète dans le presse-papiers.
11. Cliquez sur **S'inscrire**.

    Une fois l'inscription terminée, la console DPM affiche la disponibilité d'Azure Backup.

    Azure Backup chiffre toujours les données à la source avec le code secret (chaîne alphanumérique) que vous spécifiez ou générez automatiquement.
    >[AZURE.NOTE]Azure Backup ne conserve jamais le code secret. Si vous le perdez, les données ne peuvent pas être restaurées, ni récupérées. Nous vous recommandons vivement de sauvegarder la clé dans un emplacement externe.

Lorsque vous spécifiez un code secret puis cliquez sur **Terminer**, quelques secondes sont nécessaires pour que l'agent inscrive le serveur de production dans le coffre de sauvegarde. Une fois l'inscription dans le coffre terminée, une page récapitulative **Inscription du serveur** s'affiche.

## Spécifications (et limitations)

- DPM peut s'exécuter en tant que serveur physique ou machine virtuelle Hyper-V installée sur System Center 2012 SP1 ou System Center 2012 R2. DPM peut également s'exécuter en tant que machine virtuelle Azure sur System Center 2012 R2 avec au moins le correctif cumulatif 3 pour DPM 2012 R2, ou en tant que machine virtuelle Windows dans VMWare sur System Center 2012 R2 avec au moins le correctif cumulatif 5.
- Si vous exécutez DPM avec System Center 2012 SP1, vous devez installer le correctif cumulatif 2 pour System Center Data Protection Manager SP1. Cette opération est nécessaire avant d'installer l'agent Azure Backup.
- Windows PowerShell et .Net Framework 4.5 doivent être installés sur le serveur DPM.
- DPM permet de sauvegarder la plupart des charges de travail dans Azure Backup. Pour une liste complète des éléments pris en charge par Azure Backup, consultez la liste ci-dessous.
- Des données stockées dans Azure Backup ne peuvent pas être récupérées avec l'option « Copie sur bande ».
- Vous devez posséder un compte Azure avec la fonctionnalité Azure Backup activée. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. En savoir plus sur la [tarification d'Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
- L'utilisation d'Azure Backup nécessite l'installation de l'agent Azure Backup sur les serveurs que vous souhaitez sauvegarder. Chaque serveur doit disposer d'au moins 2,5 Go d'espace de stockage local disponible (15 Go étant recommandés) pour l'emplacement du cache.
- Les données seront stockées dans le coffre Azure. Il n'existe aucune limite à la quantité de données que vous pouvez sauvegarder dans un coffre Azure Backup, mais la taille d'une source de données (par exemple, une machine virtuelle ou une base de données) ne doit pas dépasser 1,65 To.

Les types de fichiers suivants sont pris en charge pour une sauvegarde vers Azure :

- Chiffré (sauvegardes complètes uniquement)
- Compressé (sauvegardes incrémentielles prises en charge)
- Partiellement alloué (sauvegardes incrémentielles prises en charge)
- Compressé et partiellement alloué (traité comme partiellement alloué)

Et les types suivants ne sont pas pris en charge :

- Les serveurs sur des systèmes de fichiers respectant la casse ne sont pas pris en charge.
- Liens physiques (ignorés)
- Points d'analyse (ignorés)
- Chiffré et compressé (ignoré)
- Chiffré et partiellement alloué (ignoré)
- Flux compressé
- Flux partiellement alloué

>[AZURE.NOTE]À partir de System Center 2012 DPM avec SP1, vous pouvez sauvegarder dans Azure des charges de travail protégées par DPM grâce à Microsoft Azure Backup.

<!---HONumber=62-->
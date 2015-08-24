<properties 
	pageTitle="Charge de travail des applications métier, phase 5 : création du groupe de disponibilité et ajout des bases de données d'application" 
	description="Dans cette phase finale du déploiement d'une application métier à haute disponibilité, vous créez un groupe de disponibilité et lui ajoutez vos bases de données d'applications." 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/11/2015" 
	ms.author="josephd"/>

# Charge de travail des applications métier, phase 5 : création du groupe de disponibilité et ajout des bases de données d'application

Au cours de cette dernière phase de déploiement d'une application métier à haute disponibilité dans des services d'infrastructure Azure, vous créez un groupe de disponibilité SQL Server AlwaysOn et ajoutez les bases de données de l'application.

Voir [Déployer une application métier à haute disponibilité dans Azure](virtual-machines-workload-high-availability-LOB-application-overview.md) (en anglais) pour toutes les phases.

## Création du groupe de disponibilité et ajout des bases de données

Pour chaque base de données de l'application métier :

1.	Effectuez une sauvegarde complète et une sauvegarde du journal des transactions de la base de données dans la machine virtuelle SQL Server principale.
2.	Restaurez les sauvegardes complètes et les sauvegardes de journaux sur la machine virtuelle SQL Server de sauvegarde.

Une fois que les bases de données ont été sauvegardées et restaurées, elles peuvent être ajoutées au groupe de disponibilité. SQL Server autorise uniquement les bases de données sauvegardées (au moins une fois) et restaurées sur un autre ordinateur, à faire partie du groupe.

### Partage des dossiers de sauvegarde

Pour activer la sauvegarde et la restauration, les fichiers de sauvegarde (.bak) doivent être accessibles à partir du serveur de base de données secondaire. Procédez comme suit :

1.	Connectez-vous au serveur de base de données principal en tant que **[domain]\\sqladmin**. 
2.	Accédez au disque F:\\. 
3.	Cliquez avec le bouton droit sur le dossier **Backup**, puis cliquez sur **Partager avec**et sur **Des personnes spécifiques**.
4.	Dans la boîte de dialogue **Partage de fichiers**, entrez **domain\\sqlservice**, puis cliquez sur **Ajouter**.
5.	Cliquez sur la colonne **Niveau d’autorisation** correspondant au compte **sqlservice**, puis cliquez sur **Lecture/Écriture**. 
6.	Cliquez sur **Partager**, puis sur **Terminé**.

Exécutez la procédure ci-dessus sur le serveur de bases de données secondaire, mais octroyez au compte sqlservice l'accès en **Lecture** au dossier F:\\Backup à l'étape 5.

### Sauvegarde et restauration d’une base de données

Les procédures suivantes doivent être répétées pour chaque base de données à ajouter au groupe de disponibilité.

Procédez comme suit pour sauvegarder une base de données.

1.	Dans l'écran d'accueil du serveur de bases de données principal, entrez **SQL Studio**, puis cliquez sur **SQL Server Management Studio**.
2.	Cliquez sur **Connecter**.
3.	Dans le volet gauche, développez le nœud **Bases de données**.
4.	Cliquez avec le bouton droit sur une base de données à sauvegarder, pointez la souris sur **Tâches**, puis cliquez sur **Sauvegarder**.
5.	Dans la section **Destination**, cliquez sur **Supprimer** afin de supprimer le chemin d’accès de fichier par défaut correspondant au fichier de sauvegarde.
6.	Cliquez sur **Add**. Sous **Nom de fichier**, entrez **\\machineName\\backup[databaseName].bak**, où **machineName** désigne le nom de l'**ordinateur SQL Server** principal et **databaseName** désigne le nom de la base de données. Cliquez sur **OK**, puis cliquez une nouvelle fois sur **OK** dès que le message confirmant la réussite de la sauvegarde apparaît.
7.	Dans le volet gauche, cliquez avec le bouton droit sur **[databaseName]**, pointez sur **Tâches**, puis cliquez sur **Sauvegarder**.
8.	Dans **Type de sauvegarde**, sélectionnez **Journal des transactions**, puis cliquez sur **OK** deux fois.
9.	Gardez cette session Bureau à distance ouverte.

Procédez comme suit pour restaurer une base de données.

1.	Connectez-vous au serveur de bases de données secondaire en tant que [domainName]\\sp\_farm\_db.
2.	Sur l’écran d’accueil, entrez **SQL Studio**, puis cliquez sur **SQL Server 2014 Management Studio**.
3.	Cliquez sur **Connecter**.
4.	Dans le volet gauche, cliquez avec le bouton droit sur **Bases de données**, puis cliquez sur **Restaurer la base de données**.
5.	Dans la section **Source**, sélectionnez **Unité**, puis cliquez sur le bouton Parcourir (…).
6.	Dans **Sélectionner les unités de sauvegarde**, cliquez sur **Ajouter**.
7.	Dans **Emplacement des fichiers de sauvegarde**, entrez **\\machineName\\backup**, appuyez sur **Entrée**, sélectionnez **[databaseName].bak**, puis cliquez deux fois sur **OK**. La sauvegarde complète et la sauvegarde des journaux doivent désormais apparaître dans la section **Jeux de sauvegarde à restaurer**.
8.	Sous **Sélectionner une page**, cliquez sur **Options**. Sous **Options de restauration**, dans **État de récupération**, sélectionnez **RESTORE WITH NORECOVERY**, puis cliquez sur **OK**. 
9.	À l’invite, cliquez sur **OK**.

### Création d’un groupe de disponibilité

Après la préparation d’au moins une base de données (à l’aide de la méthode de sauvegarde et de restauration), vous créez un groupe de disponibilité.

1.	Revenez à la session Bureau à distance pour le serveur de bases de données principal.
2.	Dans le volet gauche de **SQL Server Management Studio**, cliquez avec le bouton droit sur **Haute disponibilité AlwaysOn**, puis cliquez sur **Assistant Nouveau groupe de disponibilité**.
3.	Sur la page **Introduction**, cliquez sur **Suivant**. 
4.	Sur la page **Spécifier le nom du groupe de disponibilité**, entrez le nom de votre groupe de disponibilité dans **Nom du groupe de disponibilité** (par exemple, AG1), puis cliquez sur **Suivant**.
5.	Sur la page **Sélectionner des bases de données**, sélectionnez les bases de données de l'application qui ont été sauvegardées, puis cliquez sur **Suivant**. Ces bases de données répondent à la configuration requise pour un groupe de disponibilité, car vous avez effectué au moins une sauvegarde complète sur le réplica principal prévu.
6.	Sur la page **Spécifier les réplicas**, cliquez sur **Ajouter un réplica**.
7.	Sous **Se connecter au serveur**, entrez le nom du serveur de bases de données secondaire, puis cliquez sur **Connecter**. 
8.	Sur la page **Spécifier les réplicas**, le serveur de bases de données secondaire est répertorié dans **Réplicas de disponibilité**. Pour les deux instances, définissez les valeurs des options suivantes : 

Rôle initial | Option | Valeur 
--- | --- | ---
Primaire | Basculement automatique (jusqu’à 2) | Sélectionné
Secondaire | Basculement automatique (jusqu’à 2) | Sélectionné
Primaire | Validation synchrone (jusqu’à 3) | Sélectionné
Secondaire | Validation synchrone (jusqu’à 3) | Sélectionné
Primaire | Secondaire lisible | Oui
Secondaire | Secondaire lisible | Oui
		
9.	Cliquez sur **Next**.
10.	Sur la page **Sélectionner la synchronisation de données initiale**, cliquez sur **Joindre uniquement**, puis cliquez sur **Suivant**. La synchronisation des données s’exécute manuellement par le biais des sauvegardes complètes et de transactions sur le serveur principal, puis de la restauration effectuée à partir de la sauvegarde. Vous pouvez également sélectionner **Complet** pour permettre à l’Assistant Nouveau groupe de disponibilité d’effectuer la synchronisation des données à votre place. Toutefois, la synchronisation n’est pas recommandée pour les grandes bases de données de certaines entreprises.
11.	Sur la page **Validation**, cliquez sur **Suivant**. Un avertissement concernant la configuration de l’écouteur manquant s’affiche, car un écouteur du groupe de disponibilité n’est pas configuré. 
12.	Sur la page **Résumé**, cliquez sur **Terminer**. Une fois l’Assistant terminé, examinez la page **Résultats** afin de vérifier la création du groupe de disponibilité. Si le groupe de disponibilité a bien été créé, cliquez sur **Fermer** pour quitter l’Assistant. 
13.	Sur l’écran d’accueil, entrez **Basculement**, puis cliquez sur **Gestionnaire du cluster de basculement**. Dans le volet gauche, ouvrez votre cluster, puis cliquez sur **Rôles**. Un nouveau rôle portant le nom de votre groupe de disponibilité doit s’afficher.

Vous avez configuré un groupe de disponibilité SQL Server AlwaysOn pour vos bases de données d'applications.

![](./media/virtual-machines-workload-high-availability-LOB-application-phase5/workload-lobapp-phase4.png)

Vous êtes maintenant prêt à commencer le déploiement de cette nouvelle application pour vos utilisateurs intranet.

## Configuration d’un écouteur pour le groupe de disponibilité AlwaysOn

Vous pouvez créer une configuration d’écouteur pour le groupe de disponibilité AlwaysOn. Pour prendre connaissance de la procédure à suivre, consultez [Didacticiel : Configuration d’un écouteur pour les groupes de disponibilité AlwaysOn](https://msdn.microsoft.com/library/dn425027.aspx). Ces instructions vous guident lors de la création d'un écouteur unique (recommandé) et d'utiliser l'adresse IP statique d'une instance d'équilibrage de charge interne.

Une fois l'écouteur configuré, vous devez configurer toutes les machines virtuelles du serveur Web de manière à ce qu'elles utilisent l'écouteur au lieu du nom du premier serveur SQL dans le cluster. Au lieu d'utiliser un nouveau nom DNS et un enregistrement mappés sur l'adresse IP virtuelle de l'instance d'équilibrage de charge interne, configurez les machines virtuelles du serveur web pour utiliser un alias SQL. Pour avoir plus de détails et prendre connaissance de la procédure à suivre, consultez [Alias SQL pour SharePoint](http://blogs.msdn.com/b/priyo/archive/2013/09/13/sql-alias-for-sharepoint.aspx) (en anglais).

## Ressources supplémentaires

[Déployer une application métier à haute disponibilité dans Azure](virtual-machines-workload-high-availability-LOB-application-overview.md) (en anglais)

[Plan de l'architecture des applications métier](http://msdn.microsoft.com/dn630664)

[Configuration d’une application métier web dans un cloud hybride à des fins de test](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Instructions d’implémentation des services d’infrastructure Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

<!---HONumber=August15_HO7-->
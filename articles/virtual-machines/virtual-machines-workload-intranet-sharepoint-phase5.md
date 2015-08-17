<properties
	pageTitle="Phase 5 de la charge de travail de la batterie de serveurs  SharePoint intranet : Création du groupe de disponibilité et ajout des bases de données SharePoint"
	description="Dans cette phase finale du déploiement d’une batterie de serveurs SharePoint 2013 intranet uniquement, vous allez créer un groupe de disponibilité et lui ajouter vos bases de données SharePoint."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2015"
	ms.author="josephd"/>

# Phase 5 de la charge de travail de la batterie de serveurs SharePoint intranet : Création du groupe de disponibilité et ajout des bases de données SharePoint

Au cours de cette dernière phase de déploiement d’une batterie de serveurs SharePoint 2013 intranet uniquement avec les groupes de disponibilité SQL Server AlwaysOn dans les services d’infrastructure Azure, vous créez un groupe de disponibilité AlwaysOn et ajoutez les bases de données de la batterie de serveurs SharePoint.

Consultez [Déploiement de SharePoint avec des groupes de disponibilité SQL Server AlwaysOn dans Azure](virtual-machines-workload-intranet-sharepoint-overview.md) pour prendre connaissance de toutes les phases.

## Création du groupe de disponibilité et ajout des bases de données

SharePoint crée plusieurs bases de données dans le cadre de la configuration initiale. Ces bases de données doivent être préparées comme suit :

1.	Effectuez une sauvegarde complète et une sauvegarde du journal des transactions de la base de données sur la machine principale.
2.	Restaurez la sauvegarde complète et les sauvegardes des journaux sur la machine de sauvegarde.

Une fois que les bases de données ont été sauvegardées et restaurées, elles peuvent être ajoutées au groupe de disponibilité. SQL Server autorise à faire partie du groupe uniquement les bases de données sauvegardées (au moins une fois) et restaurées sur une autre machine.

### Partage des dossiers de sauvegarde

Pour permettre la sauvegarde et la restauration, assurez-vous que les fichiers de sauvegarde (.bak) sont accessibles à partir de la machine virtuelle SQL Server secondaire. Procédez comme suit :

1.	Connectez-vous à l’hôte principal SQL Server en tant que **[domain]\\sp\_farm\_db**.
2.	Accédez au disque F:\\.
3.	Cliquez avec le bouton droit sur le dossier **Backup**, puis cliquez sur **Partager avec** et sur **Personnes spécifiques**.
4.	Dans la boîte de dialogue **Partage de fichiers**, entrez **domain\\sqlservice**, puis cliquez sur **Ajouter**.
5.	Cliquez sur la colonne **Niveau d’autorisation** correspondant au compte **sqlservice**, puis cliquez sur **Lecture/Écriture**.
6.	Cliquez sur **Partager**, puis sur **Terminé**.

Exécutez la procédure ci-dessus sur l’hôte SQL Server secondaire, mais octroyez au compte sqlservice l’autorisation **Lecture** pour le dossier F:\\Backup à l’étape 5.

### Sauvegarde et restauration des bases de données

Les procédures suivantes doivent être répétées pour chaque base de données à ajouter au groupe de disponibilité. Certaines bases de données SharePoint 2013 ne prennent pas en charge les groupes de disponibilité SQL Server AlwaysOn. Pour plus d’informations, consultez [Options de haute disponibilité et de récupération d’urgence prises en charge pour les bases de données SharePoint](http://technet.microsoft.com/library/jj841106.aspx).

Procédez comme suit pour sauvegarder une base de données :

1.	Dans l’écran d’accueil de la machine SQL Server principale, entrez **SQL Studio**, puis cliquez sur **SQL Server Management Studio**.
2.	Cliquez sur **Connecter**.
3.	Dans le volet gauche, développez le nœud **Bases de données**.
4.	Cliquez avec le bouton droit sur une base de données à sauvegarder, pointez la souris sur **Tâches**, puis cliquez sur **Sauvegarder**.
5.	Dans la section **Destination**, cliquez sur **Supprimer** afin de supprimer le chemin d’accès de fichier par défaut correspondant au fichier de sauvegarde.
6.	Cliquez sur **Add**. Sous **Nom de fichier**, entrez **\\machineName\\backup[databaseName].bak**, où machineName désigne le nom de la machine SQL Server principale et databaseName désigne le nom de la base de données. Cliquez sur **OK**, puis cliquez une nouvelle fois sur **OK** dès que le message confirmant la réussite de la sauvegarde apparaît.
7.	Dans le volet gauche, cliquez sur **[databaseName]**, pointez la souris sur **Tâches**, puis cliquez sur **Sauvegarder**.
8.	Dans **Type de sauvegarde**, sélectionnez **Journal des transactions**, puis cliquez sur **OK** deux fois.
9.	Gardez cette session Bureau à distance ouverte.

Procédez comme suit pour restaurer une base de données :

1.	Connectez-vous à la machine SQL Server secondaire en tant que **[domainName]\\sp\_farm\_db**.
2.	Sur l’écran d’accueil, entrez **SQL Studio**, puis cliquez sur **SQL Server 2014 Management Studio**.
3.	Cliquez sur **Connecter**.
4.	Dans le volet gauche, cliquez avec le bouton droit sur **Bases de données**, puis cliquez sur **Restaurer la base de données**.
5.	Dans la section **Source**, sélectionnez **Unité**, puis cliquez sur le bouton Parcourir (…).
6.	Dans **Sélectionner les unités de sauvegarde**, cliquez sur **Ajouter**.
7.	Dans **Emplacement du fichier de sauvegarde**, tapez **\\machineName\\backup**, appuyez sur Entrée, sélectionnez **[databaseName].bak**, puis cliquez sur **OK** deux fois. La sauvegarde complète et la sauvegarde des journaux doivent désormais apparaître dans la section **Jeux de sauvegarde à restaurer**.
8.	Sous **Sélectionner une page**, cliquez sur **Options**. Sous **Options de restauration**, dans **État de récupération**, sélectionnez **RESTORE WITH NORECOVERY**, puis cliquez sur **OK**.
9.	À l’invite, cliquez sur **OK**.

### Création d’un groupe de disponibilité

Après la préparation d’au moins une base de données (à l’aide de la méthode de sauvegarde et de restauration), vous créez un groupe de disponibilité.

1.	Revenez à la session Bureau à distance de la machine SQL Server principale.
2.	Dans le volet gauche de **SQL Server Management Studio**, cliquez avec le bouton droit sur **Haute disponibilité AlwaysOn**, puis cliquez sur **Assistant Nouveau groupe de disponibilité**.
3.	Dans la page **Introduction**, cliquez sur **Suivant**.
4.	Dans la page **Spécifier le nom du groupe de disponibilité**, entrez le nom de votre groupe de disponibilité dans **Nom du groupe de disponibilité** (par exemple, AG1), puis cliquez sur **Suivant**.
5.	Dans la page **Sélectionner des bases de données**, sélectionnez les bases de données de la batterie de serveurs SharePoint qui ont été sauvegardées, puis cliquez sur **Suivant**. Ces bases de données répondent à la configuration requise pour un groupe de disponibilité, car vous avez effectué au moins une sauvegarde complète sur le réplica principal prévu.
6.	Dans la page **Spécifier les réplicas**, cliquez sur **Ajouter un réplica**.
7.	Sous **Se connecter au serveur**, entrez le nom de la machine SQL Server secondaire, puis cliquez sur **Connecter**.
8.	Dans la page **Spécifier les réplicas**, l’hôte SQL Server secondaire est répertorié dans **Réplicas de disponibilité**. Pour les deux instances, définissez les valeurs des options suivantes :

Rôle initial | Option | Valeur
--- | --- | ---
Primaire | Basculement automatique (jusqu’à 2) | Sélectionné
Secondaire | Basculement automatique (jusqu’à 2) | Sélectionné
Primaire | Validation synchrone (jusqu’à 3) | Sélectionné
Secondaire | Validation synchrone (jusqu’à 3) | Sélectionné
Primaire | Secondaire lisible | Oui
Secondaire | Secondaire lisible | Oui

9.	Cliquez sur **Next**.  
10.	Dans la page **Sélectionner la synchronisation de données initiale**, cliquez sur **Joindre uniquement**, puis cliquez sur **Suivant**. La synchronisation des données s’exécute manuellement par le biais des sauvegardes complètes et de transactions sur le serveur principal, puis de la restauration effectuée à partir de la sauvegarde. Vous pouvez également sélectionner **Complet** pour permettre à l’Assistant Nouveau groupe de disponibilité d’effectuer la synchronisation des données à votre place. Toutefois, la synchronisation n’est pas recommandée pour les grandes bases de données de certaines entreprises.  
11.	Dans la page **Validation**, cliquez sur **Suivant**. Un avertissement concernant la configuration de l’écouteur manquant s’affiche, car un écouteur du groupe de disponibilité n’est pas configuré.
12.	Dans la page **Résumé**, cliquez sur **Terminer**. Une fois l’Assistant terminé, examinez la page **Résultats** afin de vérifier la création du groupe de disponibilité. Si le groupe de disponibilité a bien été créé, cliquez sur **Fermer** pour quitter l’Assistant.
13.	Sur l’écran d’accueil, entrez **Basculement**, puis cliquez sur **Gestionnaire du cluster de basculement**. Dans le volet gauche, ouvrez votre cluster, puis cliquez sur **Rôles**. Un nouveau rôle portant le nom de votre groupe de disponibilité doit s’afficher.  

Vous avez configuré un groupe de disponibilité SQL Server AlwaysOn pour vos bases de données SharePoint.

## Configuration d’un écouteur pour le groupe de disponibilité AlwaysOn

Vous pouvez créer une configuration d’écouteur pour le groupe de disponibilité AlwaysOn. Pour prendre connaissance de la procédure à suivre, consultez [Didacticiel : Configuration d’un écouteur pour les groupes de disponibilité AlwaysOn](https://msdn.microsoft.com/library/dn425027.aspx). Vous devez créer un seul écouteur et le configurer afin d’utiliser l’adresse IP virtuelle d’une instance d’équilibrage de charge interne.

Une fois que l’écouteur est créé, vous devez configurer les machines virtuelles SharePoint de sorte qu’elles utilisent toutes l’écouteur, et pas seulement le premier ordinateur SQL Server du cluster. Au lieu de noms, configurez les machines virtuelles SharePoint pour qu’elles utilisent un alias SQL. Pour plus d’informations et connaître la procédure à suivre, consultez [SQL alias for SharePoint](http://blogs.msdn.com/b/priyo/archive/2013/09/13/sql-alias-for-sharepoint.aspx).

Pour plus d’informations sur SharePoint avec les groupes de disponibilité SQL Server AlwaysOn, consultez [Configurer des groupes de disponibilité AlwaysOn SQL Server 2012 pour SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).


## Ressources supplémentaires

[Déploiement de SharePoint avec des groupes de disponibilité SQL Server AlwaysOn dans Azure](virtual-machines-workload-intranet-sharepoint-overview.md)

[Batteries de serveurs SharePoint hébergés dans des services d’infrastructure Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Infographie SharePoint avec SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788)

[Architectures Microsoft Azure pour SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Instructions d’implémentation des services d’infrastructure Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

<!---HONumber=August15_HO6-->
<properties
	pageTitle="Sauvegarde Azure - créer un coffre de sauvegarde et spécifiez la redondance du stockage"
	description="Apprenez à créer un coffre de sauvegarde, spécifiez des options de redondance de stockage de sauvegarde Azure"
	services="backup"
	documentationCenter=""
	authors="prvijay"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.date="04/08/2015"
	 ms.author="prvijay"/>

# Créer un coffre de sauvegarde
Pour sauvegarder des fichiers et les données de votre serveur Windows ou de System Center Data Protection Manager (SCDPM) pour Azure ou lors de la sauvegarde des machines virtuelles IaaS sur Azure, vous devez créer un coffre de sauvegarde dans la zone géographique où vous souhaitez stocker les données.

Ce didacticiel vous guidera tout au long de la création du coffre que vous utiliserez pour stocker les sauvegardes.

1. Connectez-vous à la [portail de gestion](https://manage.windowsazure.com/)
2. Cliquez sur **nouveau** -> **Data Services** -> **Services de récupération** -> **coffre de sauvegarde** et choisissez **Création rapide** <br/> ![Créer le coffre][1]

3. Pour le **nom** paramètre, entrez un nom convivial pour identifier le coffre de sauvegarde. Cette opération doit être unique pour chaque abonnement.

4. Pour le **région** paramètre, sélectionnez la région géographique du coffre de sauvegarde. Le choix détermine la géographique à laquelle vos données de sauvegarde sont envoyées. En choisissant un géographiquement proche de votre emplacement, vous pouvez réduire la latence du réseau lors de la sauvegarde dans Azure.

5. Cliquez sur **créer un coffre** pour terminer le flux de travail. La création du coffre de sauvegarde peut prendre du temps. Pour vérifier l'état, vous pouvez surveiller les notifications au bas du portail. <br/> ![Création du coffre][2]

6. Après avoir créé le coffre de sauvegarde, un message vous indiquera le coffre a été créé et qu'il apparaît dans les ressources pour les Services de récupération en tant que **Active**. <br/> ![Création de statut du coffre][3]


## Sauvegarde Azure - Options de redondance de stockage

Le meilleur moment pour identifier votre option de redondance de stockage est juste après la création du coffre, et avant que tous les ordinateurs sont inscrits dans le coffre. Une fois qu'un élément a été enregistré dans le coffre, l'option de redondance de stockage est verrouillée et ne peut pas être modifiée.

Besoins de votre entreprise détermine la redondance de stockage du stockage principal de sauvegarde Azure. Si vous utilisez Azure comme un point de terminaison de stockage de sauvegarde principal (par exemple, vous sauvegardez sur Azure à partir d'un serveur Windows), vous devez envisager d'option de stockage géo-redondant de prélèvement (par défaut). Cela se produit sous la **configurer** possibilité de votre coffre de sauvegarde. <br/> ![GRS][4]

### Stockage géo-redondant (GRS).
Le stockage GRS effectue six copies de vos données. Avec le stockage GRS, vos données sont répliquées trois fois dans la région primaire et trois fois dans une région secondaire située à des centaines de kilomètres de la région primaire, ce qui confère aux données le plus haut niveau de durabilité disponible. En cas de défaillance dans la région principale, en stockant les données dans GRS, sauvegarde Azure garantit que vos données restent durables dans deux régions distinctes.

### Stockage localement redondant (LRS)
Le stockage localement redondant (LRS) effectue trois copies de vos données. Le stockage LRS est répliqué trois fois par installation et par région. LRS protège vos données contre les défaillances matérielles normales, mais pas à partir de l'échec d'une installation Azure ensemble.

Si vous utilisez Azure comme un point de terminaison de stockage de sauvegarde tertiaires (par exemple, vous utilisez SCDPM de disposer d'une sauvegarde locale copie sur site et à l'aide d'Azure pour votre rétention à long terme doit), vous devriez choisir le stockage localement redondant à partir de la **configurer** possibilité de votre coffre de sauvegarde. Cela entraîne un dysfonctionnement du coût de stockage des données dans Azure, tout en fournissant un niveau inférieur de la durabilité de vos données peuvent être acceptables pour les copies tertiaires. <br/> ![LRS][5]




## Remarque

+ Depuis mars 2015, les clients n'ont pas par programmation (par ex: PowerShell) moyen de créer un coffre de sauvegarde.

+ La redondance de stockage doit être sélectionnée juste après la création du coffre et avant tous les ordinateurs sont inscrits dans le coffre. Une fois qu'un élément a été enregistré dans le coffre, l'option de redondance de stockage est verrouillée et ne peut pas être modifiée.

<!--Image references-->
[1]: ./media/backup-azure-backup-create-vault/createvault1.png
[2]: ./media/backup-azure-backup-create-vault/creatingvault1.png
[3]: ./media/backup-azure-backup-create-vault/backupvaultstatus1.png
[4]: ./media/backup-azure-backup-create-vault/grs.png
[5]: ./media/backup-azure-backup-create-vault/lrs.png

<!---HONumber=GIT-SubDir-->
<properties
   pageTitle="Azure Backup - Création d’un archivage de sauvegarde et spécification de la redondance de stockage"
   description="Découvrez comment créer un archivage de sauvegarde et spécifier des options de redondance de stockage dans Azure Backup."
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

# Créer un archivage de sauvegarde
Pour sauvegarder des fichiers et données à partir de Windows Server ou de System Center Data Protection Manager (SCDPM) dans Azure ou lors de la sauvegarde de machines virtuelles IaaS dans Azure, vous devez créer un archivage de sauvegarde dans la région géographique où vous voulez stocker les données.

Ce didacticiel vous guide tout au long des étapes de création de l’archivage que vous utiliserez pour stocker les sauvegardes.

1. Connectez-vous au [portail de gestion](https://manage.windowsazure.com/).
2. Cliquez sur **Nouveau** -> **Services de données** -> **Services de récupération** -> **Archivage de sauvegarde** et choisissez **Création rapide** <br/> ![Créer un archivage][1]

3. Pour le paramètre **Nom**, entrez un nom convivial permettant d’identifier l’archivage de sauvegarde. Cette opération doit être unique pour chaque abonnement.

4. Pour le paramètre **Région**, sélectionnez la région géographique de l’archivage de sauvegarde. Le choix détermine la zone géographique dans laquelle vos données de sauvegarde sont envoyées. En choisissant une zone géographique proche de votre emplacement, vous pouvez réduire la latence du réseau lors de la sauvegarde dans Azure.

5. Cliquez sur **Créer un archivage** pour terminer le flux de travail. La création du coffre de sauvegarde peut prendre du temps. Pour vérifier la progression, vous pouvez consulter les notifications en bas du portail. <br/> ![Création d’un archivage][2]

6. Une fois l’archivage de sauvegarde créé, vous en êtes informé par un message et l’archivage s’affiche dans les ressources de Recovery Services avec l’état **Actif**. <br/> ![Création d’un état de l’archivage][3]


## Azure Backup - Options de redondance de stockage

Idéalement, vous identifiez votre option de redondance de stockage juste après la création de l’archivage et avant l’inscription d’un ordinateur dans l’archivage. Une fois qu’un élément a été inscrit dans l’archivage, l’option de redondance de stockage est verrouillée et ne peut pas être modifiée.

Les besoins de votre entreprise déterminent la redondance du stockage Azure Backup principal. Si vous utilisez Azure en tant que point de terminaison de stockage de sauvegarde principal (par exemple, vous sauvegardez vos données dans Azure à partir de Windows Server), choisissez l’option de stockage géo-redondant (par défaut). Elle se trouve sous l’option **Configurer** de votre archivage de sauvegarde. <br/> ![GRS][4]

### Stockage géo-redondant (GRS).
Le stockage GRS effectue six copies de vos données. Avec le stockage GRS, vos données sont répliquées trois fois dans la région primaire et trois fois dans une région secondaire située à des centaines de kilomètres de la région primaire, ce qui confère aux données le plus haut niveau de durabilité disponible. En cas de défaillance au niveau de la région principale, en stockant les données dans GRS, Azure Backup garantit que vos données sont conservées dans deux régions distinctes.

### Stockage localement redondant (LRS)
Le stockage localement redondant (LRS) effectue trois copies de vos données. Le stockage LRS est répliqué trois fois par installation et par région. Ce stockage protège vos données des défaillances matérielles normales, mais pas de la défaillance de l’intégralité d’une installation Azure.

Si vous utilisez Azure comme point de terminaison de stockage de sauvegarde tertiaire (par exemple, vous utilisez SCDPM pour disposer d’une copie de sauvegarde localement et utilisez Azure pour vos besoins de rétention à long terme), choisissez le stockage localement redondant sous l’option **Configurer** de votre archivage de sauvegarde. Cela vous permet de diminuer les coûts de stockage de données dans Azure tout en fournissant un niveau inférieur de durabilité de vos données pouvant être acceptables pour des copies tertiaires. <br/> ![LRS][5]




## Remarque

+ Depuis mars 2015, les clients n’ont pas la possibilité de créer un archivage de sauvegarde par programme (par ex., via PowerShell).

+ La redondance de stockage doit être sélectionnée juste après la création de l’archivage et avant l’inscription d’un ordinateur dans l’archivage. Une fois qu’un élément a été inscrit dans l’archivage, l’option de redondance de stockage est verrouillée et ne peut pas être modifiée.

<!--Image references-->
[1]: ./media/backup-azure-backup-create-vault/createvault1.png
[2]: ./media/backup-azure-backup-create-vault/creatingvault1.png
[3]: ./media/backup-azure-backup-create-vault/backupvaultstatus1.png
[4]: ./media/backup-azure-backup-create-vault/grs.png
[5]: ./media/backup-azure-backup-create-vault/lrs.png
 

<!---HONumber=July15_HO4-->
<properties
	pageTitle="Sauvegarde Azure - restaurer un ordinateur virtuel"
	description="Découvrez comment restaurer une machine virtuelle Azure"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/27/2015"
	ms.author="aashishr"/>

# Restaurer un ordinateur virtuel
Vous pouvez restaurer un ordinateur virtuel à une machine virtuelle à partir des sauvegardes stockées dans le coffre de sauvegarde Azure à l'aide de la tâche de restauration.

## Choisissez un élément à restaurer

1. Accédez à la **éléments protégés** onglet et sélectionnez l'ordinateur virtuel que vous souhaitez restaurer vers une nouvelle machine virtuelle.

    ![Éléments protégés](./media/backup-azure-restore-vms/protected-items.png)

    Le **Point de récupération** colonne dans le **éléments protégés** page vous indiquera le nombre de points de récupération pour un ordinateur virtuel. Le **Point de récupération plus récent** colonne indique l'heure de la sauvegarde la plus récente à partir de laquelle un ordinateur virtuel peut être restauré.

2. Cliquez sur **restauration** pour ouvrir le **restaurer un élément** Assistant.

    ![Restaurer un élément](./media/backup-azure-restore-vms/restore-item.png)

## Choisir un point de récupération

1. Dans le **Sélectionner un point de récupération** écran, vous pouvez restaurer à partir du point de récupération plus récent ou à partir d'un point antérieur dans le temps. L'option par défaut sélectionnée lors de l'Assistant s'ouvre est le Point de récupération plus récent.

    ![Sélectionnez un point de récupération](./media/backup-azure-restore-vms/select-recovery-point.png)

2. Pour sélectionner un point antérieur dans le temps, choisissez la **Sélectionner une Date** option dans la liste déroulante et sélectionnez une date dans le contrôle calendar en cliquant sur l'icône de calendrier. Dans le contrôle, toutes les dates qui ont des points de récupération sont remplis avec un niveau de gris clair et peuvent être sélectionnés par l'utilisateur.

    ![Sélectionnez une date](./media/backup-azure-restore-vms/select-date.png)

    Une fois que vous cliquez sur une date dans le contrôle calendar, des points de récupération disponibles sur la date apparaît dans le tableau de points de récupération ci-dessous. Le **temps** colonne indique l'heure à laquelle l'instantané a été pris. Le **Type** colonne affiche la [cohérence](https://azure.microsoft.com/documentation/articles/backup-azure-vms/#consistency-of-recovery-points) du point de récupération. L'en-tête du tableau indique le nombre de points de récupération disponibles ce jour-là entre parenthèses.

    ![Points de récupération](./media/backup-azure-restore-vms/recovery-points.png)

3. Sélectionnez le point de récupération à partir de la **Points de récupération** table, puis cliquez sur la flèche suivant pour accéder à l'écran suivant.

## Spécifiez un emplacement de destination

1. Dans le **Sélectionnez Restaurer instance** écran spécifier les détails de l'emplacement de restaurer l'ordinateur virtuel.

  - Spécifiez le nom d'ordinateur virtuel : dans un service cloud donné, le nom de l'ordinateur virtuel doit être unique. Si vous envisagez de remplacer un ordinateur virtuel existant portant le même nom, d'abord supprimer l'ordinateur virtuel existant et les disques de données et puis restaurer les données de sauvegarde Azure.
  - Sélectionnez un service cloud pour la machine virtuelle : ce champ est obligatoire pour la création d'une machine virtuelle. Vous pouvez soit utiliser un service cloud existant ou créer un service cloud.

        Whatever cloud service name is picked should be globally unique. Typically, the cloud service name gets associated with a public-facing URL in the form of [cloudservice].cloudapp.net. Azure will not allow you to create a new cloud service if the name has already been used. If you choose to create select create a new cloud service, it will be given the same name as the virtual machine – in which case the VM name picked should be unique enough to be applied to the associated cloud service.

        We only display cloud services and virtual networks that are not associated with any affinity groups in the restore instance details. [Learn More](https://msdn.microsoft.com/en-us/library/azure/jj156085.aspx).

2. Sélectionnez un compte de stockage pour la machine virtuelle : ce champ est obligatoire pour la création de la machine virtuelle. Vous pouvez sélectionner à partir des comptes de stockage existant dans la même région que le coffre de sauvegarde Azure. Nous ne prend en charge les comptes de stockage sont redondantes ou de type de stockage Premium.

    S'il n'existe aucun compte de stockage avec la configuration prise en charge, créez un compte de stockage de configurations prises en charge avant de commencer l'opération de restauration.

    ![Sélectionnez un réseau virtuel](./media/backup-azure-restore-vms/restore-sa.png)

3. Sélectionnez un réseau virtuel : le réseau virtuel (VNET) pour l'ordinateur virtuel doit être sélectionné au moment de la création de la machine virtuelle. La restauration de l'interface utilisateur affiche tous les réseaux virtuels au sein de cet abonnement peut être utilisé. Il n'est pas obligatoire pour sélectionner un réseau virtuel pour la machine virtuelle restaurée, vous serez capable de se connecter à l'ordinateur virtuel restauré sur internet, même si le réseau virtuel n'est pas appliqué.

    Si le service de cloud sélectionné est associé à un réseau virtuel, vous ne pouvez pas modifier le réseau virtuel.

    ![Sélectionnez un réseau virtuel](./media/backup-azure-restore-vms/restore-cs-vnet.png)

4. Sélectionner un sous-réseau : au cas où le réseau virtuel possède des sous-réseaux, le premier sous-réseau sera sélectionné par défaut. Choisissez le sous-réseau de votre choix parmi les options de liste déroulante. Pour les détails du sous-réseau, accédez à l'extension de réseaux dans le [page d'accueil de portail](https://manage.windowsazure.com/), accédez à des réseaux virtuels et sélectionnez le réseau virtuel et explorez les configurer pour afficher les détails du sous-réseau.

    ![Sélectionner un sous-réseau](./media/backup-azure-restore-vms/select-subnet.png)

5. Cliquez sur le **Submit** l'icône de l'Assistant pour envoyer les détails et de créer un travail de restauration.

## Effectuer le suivi de l'opération de restauration
Après avoir toutes les informations dans l'Assistant Restauration d'entrée et soumis Azure Backup essaie de créer une tâche pour effectuer le suivi de l'opération de restauration.

![Création d'un travail de restauration](./media/backup-azure-restore-vms/create-restore-job.png)

Si la création d'un travail est réussie, vous verrez qu'un travail indiquant de notification toast est créé. Vous pouvez obtenir plus de détails en cliquant sur le **Afficher le travail** bouton que vous avez besoin pour **travaux** onglet.

![Travail de restauration créé](./media/backup-azure-restore-vms/restore-job-created.png)

Une fois l'opération de restauration est terminée, il est marqué comme terminé dans **travaux** onglet.

![Restaurer la tâche terminée](./media/backup-azure-restore-vms/restore-job-complete.png)

Après la restauration de l'ordinateur virtuel, vous devrez réinstaller les extensions existantes sur l'ordinateur virtuel d'origine et [recréer les points de terminaison](virtual-machines-set-up-endpoints) pour l'ordinateur virtuel dans le portail Azure.

## Résolution des erreurs
Pour la plupart des erreurs, vous pouvez suivre l'action recommandée suggérée dans les détails de l'erreur. Voici quelques points supplémentaires pour aider au dépannage :

| Opération de sauvegarde | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| Restauration | Échec de la restauration avec une erreur interne du Cloud | <ol><li>Service de cloud auquel vous essayez de restaurer est configuré avec les paramètres DNS. Vous pouvez vérifier <br>$deployment = Get-AzureDeployment - ServiceName « ServiceName »-emplacement « Production » Get-AzureDns - DnsSettings $deployment. DnsSettings<br>en l'absence d'adresse configurée, cela signifie que les paramètres DNS sont configurés.<br> <li>Service cloud auquel vous essayez de restaurer est configuré avec l'adresse IP réservée et machines virtuelles existantes dans le service cloud sont dans un état arrêté.<br>Vous pouvez vérifier un service cloud a réservé IP à l'aide de la suite applets de commande powershell :<br>$deployment = Get-AzureDeployment - ServiceName « servicename »-dépendant de l'emplacement « Production » $ ReservedIPName</ol> |

## Étapes suivantes
- [Gérer des ordinateurs virtuels](backup-azure-manage-vms)

<!---HONumber=GIT-SubDir-->
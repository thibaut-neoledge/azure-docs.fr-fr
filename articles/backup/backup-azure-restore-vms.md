
<properties
	pageTitle="Azure Backup - Restauration d’une machine virtuelle"
	description="Découvrez comment restaurer une machine virtuelle Azure."
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="07/17/2015" ms.author="trinadhk"; "jimpark"/>

# Restaurer une machine virtuelle
Vous pouvez restaurer une machine virtuelle sur une nouvelle machine virtuelle à partir des sauvegardes stockées dans l’archivage de sauvegarde Azure à l’aide de la tâche de restauration.

## Choix d’un élément à restaurer

1. Accédez à l’onglet **Éléments protégés** et sélectionnez la machine virtuelle que vous voulez restaurer sur une nouvelle machine virtuelle.

    ![Éléments protégés](./media/backup-azure-restore-vms/protected-items.png)

    La colonne **Point de récupération** de la page **Éléments protégés** vous indique le nombre de points de récupération pour une machine virtuelle. La colonne **Point de récupération le plus récent** indique l’heure de la sauvegarde la plus récente à partir de laquelle une machine virtuelle peut être restaurée.

2. Cliquez sur **Restaurer** pour ouvrir l’Assistant **Restaurer un élément**.

    ![Restaurer un élément](./media/backup-azure-restore-vms/restore-item.png)

## Choix d’un point de récupération

1. Dans l’écran **Sélectionner un point de récupération**, vous pouvez effectuer une restauration à partir du point de récupération le plus récent ou d’un point antérieur dans le temps. L’option par défaut sélectionnée à l’ouverture de l’Assistant est *Point de récupération le plus récent*.

    ![Sélectionner un point de récupération](./media/backup-azure-restore-vms/select-recovery-point.png)

2. Pour sélectionner un point antérieur dans le temps, choisissez l’option **Sélectionner une date** dans la liste déroulante, puis sélectionnez une date dans le contrôle Calendrier en cliquant sur l’**icône de calendrier**. Dans le contrôle, toutes les dates associées à des points de récupération sont indiquées par une couleur de remplissage gris clair et peuvent être sélectionnées par l’utilisateur.

    ![Sélectionner une date](./media/backup-azure-restore-vms/select-date.png)

    Quand vous cliquez sur une date dans le contrôle Calendrier, les points de récupération disponibles pour cette date apparaissent dans le tableau de points de récupération ci-dessous. La colonne **Heure** indique l’heure à laquelle l’instantané a été capturé. La colonne **Type** affiche la [cohérence](https://azure.microsoft.com/documentation/articles/backup-azure-vms/#consistency-of-recovery-points) du point de récupération. L’en-tête du tableau indique entre parenthèses le nombre de points de récupération disponibles ce jour-là.

    ![Points de récupération](./media/backup-azure-restore-vms/recovery-points.png)

3. Sélectionnez le point de récupération dans le tableau **Points de récupération**, puis cliquez sur la flèche Suivant pour accéder à l’écran suivant.

## Spécification d’un emplacement de destination

1. Dans l’écran **Sélectionner l’instance à restaurer**, spécifiez les détails de l’emplacement où restaurer la machine virtuelle.

  - Spécifiez le nom de la machine virtuelle : dans un service cloud donné, le nom de la machine virtuelle doit être unique. Si vous envisagez de remplacer une machine virtuelle existante portant le même nom, commencez par supprimer la machine virtuelle existante ainsi que les disques de données, puis restaurez les données à partir d’Azure Backup.
  - Sélectionnez un service cloud pour la machine virtuelle : ce champ est obligatoire pour la création d’une machine virtuelle. Vous pouvez utiliser un service cloud existant ou en créer un.

        Quel que soit le nom du service cloud choisi, il doit être globalement unique. En règle générale, le nom du service cloud est associé à une URL destinée au public au format [cloudservice].cloudapp.net. Azure ne vous autorise pas à créer un nouveau service cloud si le nom a déjà été utilisé. Si vous choisissez de créer un nouveau service cloud, le nom de la machine virtuelle lui sera attribué. Dans ce cas, le nom de la machine virtuelle choisi doit être suffisamment particulier pour être appliqué au service cloud associé.

         Nous affichons uniquement les services cloud et les réseaux virtuels qui ne sont associés à aucun groupe d’affinités dans les informations de l’instance à restaurer. [En savoir plus](https://msdn.microsoft.com/fr-fr/library/azure/jj156085.aspx).

2. Sélectionnez un compte de stockage pour la machine virtuelle : ce champ est obligatoire pour la création de la machine virtuelle. Vous pouvez sélectionner un compte de stockage existant dans la même région que l’archivage de sauvegarde Azure. Nous ne prenons pas en charge les comptes de stockage redondants dans une zone ou de type Premium.

    S’il n’existe aucun compte de stockage avec la configuration prise en charge, créez-en un avant de commencer l’opération de restauration.

    ![Sélectionner un réseau virtuel](./media/backup-azure-restore-vms/restore-sa.png)

3. Sélectionnez un réseau virtuel : le réseau virtuel de la machine virtuelle doit être sélectionné au moment de la création de la machine virtuelle. L’interface utilisateur de restauration affiche tous les réseaux virtuels de l’abonnement qui peuvent être utilisés. Il n’est pas obligatoire de sélectionner un réseau virtuel pour la machine virtuelle restaurée, car vous pourrez vous connecter à la machine virtuelle restaurée sur Internet même si le réseau virtuel n’est pas appliqué.

    Si le service cloud sélectionné est associé à un réseau virtuel, vous ne pouvez pas modifier le réseau virtuel.

    ![Sélectionner un réseau virtuel](./media/backup-azure-restore-vms/restore-cs-vnet.png)

4. Sélectionnez un sous-réseau : si le réseau virtuel possède des sous-réseaux, le premier sous-réseau est sélectionné par défaut. Choisissez le sous-réseau de votre choix parmi les options de liste déroulante. Pour plus d’informations sur les sous-réseaux, accédez à la page d’accueil du [portail d’extension de réseaux](https://manage.windowsazure.com/), accédez à Réseaux virtuels, puis sélectionnez un réseau virtuel et explorez les détails des sous-réseaux en cliquant sur Configurer.

    ![Sélectionner un sous-réseau](./media/backup-azure-restore-vms/select-subnet.png)

5. Cliquez sur l’icône **Envoyer** de l’Assistant pour envoyer les détails et créer un travail de restauration.

## Suivi de l’opération de restauration
Une fois que vous avez entré toutes les informations dans l’Assistant Restauration et que vous les avez envoyées, Azure Backup essaie de créer un travail pour effectuer le suivi de l’opération de restauration.

![Création d’un travail de restauration](./media/backup-azure-restore-vms/create-restore-job.png)

Si le travail est correctement créé, une notification toast vous en informe. Vous pouvez obtenir plus de détails en cliquant sur le bouton **Afficher le travail** qui vous renvoie vers l’onglet **Travaux**.

![Travail de restauration créé](./media/backup-azure-restore-vms/restore-job-created.png)

Une fois l’opération de restauration terminée, elle est marquée comme terminée sous l’onglet **Travaux**.

![Travail de restauration terminé](./media/backup-azure-restore-vms/restore-job-complete.png)

Après la restauration de la machine virtuelle, vous devrez peut-être réinstaller les extensions de la machine virtuelle d’origine et [modifier les points de terminaison](virtual-machines-set-up-endpoints) de la machine virtuelle dans le portail Azure.

## Résolution des erreurs
Pour la plupart des erreurs, vous pouvez suivre l’action recommandée suggérée dans les détails de l’erreur. Voici quelques points supplémentaires pour vous aider à résoudre les erreurs :

| Opération de sauvegarde | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| Restauration | Échec de la restauration avec une erreur interne du cloud | <ol><li>Le service cloud sur lequel vous essayez d’effectuer la restauration est configuré avec des paramètres DNS. Vous pouvez vérifier <br>$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production" Get-AzureDns -DnsSettings $deployment.DnsSettings<br>Si une adresse est configurée, cela signifie que des paramètres DNS sont configurés.<br> <li>Le service cloud sur lequel vous essayez d’effectuer la restauration est configuré avec une adresse IP réservée et les machines virtuelles existantes dans le service cloud sont dans un état arrêté.<br>Vous pouvez vérifier qu’un service cloud a une adresse IP réservée à l’aide des applets de commande PowerShell suivantes :<br>$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName</ol> |

## Étapes suivantes
- [Gestion des machines virtuelles](backup-azure-manage-vms)

<!---HONumber=July15_HO4-->
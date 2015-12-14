<properties 
	pageTitle="Extension de groupes de disponibilité AlwaysOn locaux à Azure | Microsoft Azure"
	description="Ce didacticiel utilise des ressources créées avec le modèle de déploiement classique, et explique comment utiliser l’Assistant Ajout d’un réplica dans SQL Server Management Studio (SSMS) pour ajouter un réplica de groupe de disponibilité AlwaysOn dans Azure."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="11/13/2015"
	ms.author="jroth" />

# Étendre des groupes de disponibilité AlwaysOn locaux à Azure

Les groupes de disponibilité AlwaysOn fournissent une haute disponibilité pour les groupes de bases de données en ajoutant des réplicas secondaires. Ces réplicas autorisent le basculement des bases de données en cas de défaillance. Ils permettent en outre de décharger les charges de travail de lecture ou les tâches de sauvegarde.

Pour étendre les groupes de disponibilité locaux à Microsoft Azure, vous pouvez approvisionner une ou plusieurs machines virtuelles Azure avec SQL Server et les ajouter en tant que réplicas à vos groupes de disponibilité locaux.

Ce didacticiel part du principe que vous disposez des éléments suivants :

- Un abonnement Azure actif. Vous pouvez vous inscrire à un [essai gratuit](http://azure.microsoft.com/pricing/free-trial).

- Un groupe de disponibilité AlwaysOn local existant. Pour plus d’informations sur les groupes de disponibilité, consultez la page [Groupes de disponibilité AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx).

- Connectivité entre le réseau local et votre réseau virtuel Azure. Pour plus d’informations sur la création de ce réseau virtuel, consultez la page [Configurer un VPN de site à site en utilisant le portail Azure Classic](../vpn-gateway/vpn-gateway-site-to-site-create.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager

## assistant Add Azure Replica

Cette section vous montre comment utiliser l’**Assistant Ajout d’un réplica Azure** pour étendre votre solution de groupe de disponibilité AlwaysOn de manière à inclure des réplicas Azure.

1. Dans SQL Server Management Studio, développez **Haute disponibilité AlwaysOn** > **Groupes de disponibilité** > **[Nom de votre groupe de disponibilité]**.

1. Cliquez avec le bouton droit sur **Réplicas de disponibilité**, puis sélectionnez **Ajouter un réplica**.

1. Par défaut, l’**Assistant Ajout d’un réplica à un groupe de disponibilité** s’affiche. Cliquez sur **Next**. Si vous avez sélectionné l’option **Ne plus afficher cette page** en bas de la page au précédent démarrage de l’Assistant, cet écran ne s’affiche pas.

	![SQL](./media/virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups/IC742861.png)

1. Vous devrez vous connecter à tous les réplicas secondaires existants. Vous pouvez cliquer sur **Connecter...** en regard de chaque réplica ou sur **Connecter tout...** en bas de l’écran. Une fois authentifié, cliquez sur **Suivant** pour accéder à l’écran suivant.

1. Sur la page **Spécifier les réplicas**, plusieurs onglets apparaissent dans la partie supérieure : **Réplicas**, **Points de terminaison**, **Préférences de sauvegarde** et **Écouteur**. Sous l’onglet **Réplicas**, cliquez sur **Ajouter un réplica Azure…** pour démarrer l’Assistant Ajout d’un réplica Azure.

	![SQL](./media/virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups/IC742863.png)

1. Sélectionnez un certificat de gestion Azure existant dans le magasin de certificats Windows local si vous en avez déjà installé un. Sélectionnez ou entrez l’ID d’un abonnement Azure si vous en avez utilisé un précédemment. Vous pouvez cliquer sur Télécharger pour télécharger et installer un certificat de gestion Azure et télécharger la liste des abonnements à l’aide d’un compte Azure.

	![SQL](./media/virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups/IC742864.png)

1. Renseignez les champs de la page avec les valeurs qui seront utilisées pour créer la machine virtuelle Azure qui hébergera le réplica.

	|Paramètre|Description|
|---|---|
|**Image**|Sélectionnez la combinaison système d’exploitation- SQL Server souhaitée|
|**Taille de la machine virtuelle**|Sélectionnez la taille de machine virtuelle qui correspond le mieux à vos besoins|
|**Nom de la machine virtuelle**|Spécifiez un nom unique pour la nouvelle machine virtuelle. Le nom doit contenir entre 3 et 15 caractères, peut contenir uniquement des lettres, des chiffres et des traits d’union, et doit commencer par une lettre et se terminer par une lettre ou un chiffre.|
|**Nom d’utilisateur de la machine virtuelle**|Spécifiez un nom d’utilisateur qui deviendra le compte d’administrateur sur la machine virtuelle|
|**Mot de passe administrateur de la machine virtuelle**|Indiquez un mot de passe pour le nouveau compte|
|**Confirmer le mot de passe**|Confirmez le mot de passe du nouveau compte|
|**Réseau virtuel**|Indiquez le réseau virtuel Azure que la nouvelle machine virtuelle doit utiliser. Pour plus d’informations sur les réseaux virtuels, voir [Présentation du réseau virtuel](..\virtual-network\virtual-networks-overview.md).|
|**Sous-réseau de réseau virtuel**|Indiquez le sous-réseau de réseau virtuel que la nouvelle machine virtuelle doit utiliser|
|**Domaine**|Confirmez que la valeur par défaut indiquée pour le domaine est correcte|
|**Nom d’utilisateur de domaine**|Spécifiez un compte qui se trouve dans le groupe d’administrateurs local sur les nœuds du cluster local|
|**Mot de passe**|Spécifiez le mot de passe du nom d’utilisateur du domaine|

1. Cliquez sur **OK** pour valider les paramètres de déploiement.

1. Les mentions légales s’affichent. Lisez-les et cliquez sur **OK** si vous les acceptez.

1. La page **Spécifier les réplicas** apparaît de nouveau. Vérifiez les paramètres du nouveau réplica Azure sous les onglets **Réplicas**, **Points de terminaison** et **Préférences de sauvegarde**. Modifiez les paramètres pour les adapter aux besoins de votre entreprise. Pour plus d’informations sur les paramètres figurant sous ces onglets, voir [Page Spécifier les réplicas (Assistant Nouveau groupe de disponibilité/Assistant Ajout d’un réplica)](https://msdn.microsoft.com/library/hh213088.aspx).Notez que les écouteurs ne peuvent pas être créés à l’aide de l’onglet Écouteur pour les groupes de disponibilité contenant des réplicas Azure. Par ailleurs, si un écouteur a déjà été créé avant le démarrage de l’Assistant, vous recevrez un message indiquant qu’il n’est pas pris en charge dans Azure. Nous allons voir comment créer des écouteurs dans la section **Créer un écouteur de groupe de disponibilité**.

	![SQL](./media/virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups/IC742865.png)

1. Cliquez sur **Next**.

1. Sélectionnez la méthode de synchronisation de données à utiliser sur la page **Sélectionner la synchronisation de données initiale** et cliquez sur **Suivant**. Pour la plupart des scénarios, sélectionnez **Synchronisation complète des données**. Pour plus d’informations sur les méthodes de synchronisation de données, voir [Page Sélectionner la synchronisation de données initiale (Assistants Groupes de disponibilité AlwaysOn)](https://msdn.microsoft.com/library/hh231021.aspx).

1. Passez en revue les résultats sur la page **Validation**. Corrigez les problèmes et réexécutez la validation si nécessaire. Cliquez sur **Next**.

	![SQL](./media/virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups/IC742866.png)

1. Vérifiez les paramètres sur la page **Synthèse**, puis cliquez sur **Terminer**.

1. Le processus d’approvisionnement commence. Lorsque l’Assistant a terminé, cliquez sur **Fermer** pour quitter l’Assistant.

>[AZURE.NOTE]L’Assistant Ajout d’un réplica Azure crée un fichier journal dans <Users><nom\_utilisateur>\\AppData\\Local\\SQL Server\\AddReplicaWizard. Ce fichier journal peut servir à résoudre les déploiements de réplica Azure ayant échoué. Si l’Assistant ne parvient pas à exécuter une action, toutes les opérations précédentes sont annulées, y compris la suppression de la machine virtuelle approvisionnée.

## Créer un écouteur de groupe de disponibilité

Après avoir créé le groupe de disponibilité, vous devez créer un écouteur pour que les clients puissent se connecter aux réplicas. Les écouteurs dirigent les connexions entrantes vers le réplica principal ou un réplica secondaire en lecture seule. Pour plus d’informations sur les écouteurs, voir [Configuration d’un écouteur à équilibrage de charge interne pour des groupes de disponibilité AlwaysOn dans Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md).

## Étapes suivantes

En plus d’utiliser l’**Assistant Ajout d’un réplica Azure** pour étendre votre groupe de disponibilité AlwaysOn dans Azure, vous pouvez également déplacer entièrement des charges de travail SQL Server vers Azure. Pour commencer, voir [Approvisionnement d’une machine virtuelle SQL Server dans Azure](virtual-machines-provision-sql-server.md).

Pour d’autres rubriques relatives à l’utilisation de SQL Server sur des machines virtuelles Azure, voir [SQL Server sur les machines virtuelles Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=AcomDC_1203_2015-->
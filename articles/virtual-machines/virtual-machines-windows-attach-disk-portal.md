<properties
	pageTitle="Association d’un disque de données | Microsoft Azure"
	description="Explique comment attacher un disque de données nouveau ou existant à une machine virtuelle dans le portail Azure à l’aide du modèle de déploiement Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="cynthn"/>

# Attachement d’un disque de données dans le portail Azure

[AZURE.INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## <a id="initializeinWS"></a>Procédure d’initialisation d’un nouveau disque de données dans Windows Server

1. Connectez-vous à la machine virtuelle. Pour plus d’informations, consultez [Connexion à une machine virtuelle exécutant Windows Server](virtual-machines-windows-log-on.md).

2. Une fois que vous êtes connecté à la machine virtuelle, ouvrez **Server Manager**. Dans le volet gauche, sélectionnez **Services de fichiers et de stockage**.

	![Ouvrir le gestionnaire de serveur](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)

3. Développez le menu et sélectionnez **Disques**.

4. La section **Disques** répertorie les disques. Dans la plupart des cas, elle contient le disque 0, le disque 1 et le disque 2. Le disque 0 est le disque du système d'exploitation, le disque 1 est le disque temporaire et le disque 2 est le disque de données que vous venez d'attacher à la machine virtuelle. Le nouveau disque de données répertorie la Partition comme **Inconnue**. Cliquez avec le bouton droit sur le disque et sélectionnez **Initialiser**.

5.	Vous êtes averti que toutes les données seront supprimées lors de l’initialisation du disque. Cliquez sur **Oui** pour accuser réception de l'avertissement et initialiser le disque. Une fois l'opération terminée, la partition sera répertoriée comme **GPT**. Cliquez de nouveau avec le bouton droit sur le disque et sélectionnez **Nouveau volume**.

6.	Parcourez les étapes de l’Assistant en acceptant les valeurs par défaut. Lorsque l'assistant est terminé, la section **Volumes** répertorie le nouveau volume. Le disque est désormais en ligne et prêt à stocker des données.


	![Volume correctement initialisé](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)

> [AZURE.NOTE] La taille de la machine virtuelle détermine le nombre de disques que vous pouvez attacher à celle-ci. Pour en savoir plus, voir la rubrique [Tailles de machines virtuelles](virtual-machines-linux-sizes.md).


## Étapes suivantes

Vous pouvez [changer la lettre de lecteur du disque temporaire Windows] si votre application doit utiliser le lecteur D pour stocker des données.

<!---HONumber=AcomDC_0323_2016-->
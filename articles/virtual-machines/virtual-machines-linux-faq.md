<properties
	pageTitle="Forum aux questions sur les machines virtuelles Linux | Microsoft Azure"
	description="Fournit des réponses à certaines questions courantes sur les machines virtuelles Linux créées avec le modèle de déploiement Resource Manager."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/16/2016"
	ms.author="cynthn"/>

# Forum aux questions sur les machines virtuelles Linux 


Cet article traite certaines questions courantes concernant les machines virtuelles Linux créées dans Azure avec le modèle de déploiement Resource Manager. Pour la version Windows de cette rubrique, consultez les [Questions fréquences sur les machines virtuelles Windows](virtual-machines-windows-faq.md).

## Qu’est-il possible d’exécuter sur une machine virtuelle Azure ?

Tous les abonnés peuvent exécuter des logiciels serveur sur une machine virtuelle Azure. Pour plus d’informations, consultez [Linux dans des distributions prises en charge par Azure](virtual-machines-linux-endorsed-distros.md)


## Quelle quantité de stockage puis-je utiliser avec une machine virtuelle ?

Chaque disque de données peut avoir une capacité allant jusqu’à 1 To Le nombre de disques de données que vous pouvez utiliser dépend de la taille de la machine virtuelle. Pour en savoir plus, consultez la rubrique [Tailles de machines virtuelles](virtual-machines-linux-sizes.md).

Un compte de stockage Azure fournit le stockage pour le disque du système d’exploitation et tout disque de données. Chaque disque est un fichier .vhd stocké sous la forme d’un objet blob de pages. Pour plus d’informations sur la tarification, voir [Tarification – Stockage](https://azure.microsoft.com/pricing/details/storage/).



## Comment puis-je accéder à ma machine virtuelle ?

Vous devez établir une connexion à distance pour vous connecter à la machine virtuelle, à l’aide de SSH (Secure Shell). Consultez les instructions sur la connexion [à partir de Windows](virtual-machines-linux-ssh-from-windows.md) ou [à partir de Linux et Mac](virtual-machines-linux-ssh-from-linux.md). Par défaut, SSH autorise un maximum de 10 connexions simultanées. Vous pouvez augmenter ce nombre en modifiant le fichier de configuration.


Si vous rencontrez des problèmes, consultez [Dépanner les connexions Secure Shell (SSH)](virtual-machines-linux-troubleshoot-ssh-connection.md).

## Est-ce que je peux utiliser le disque temporaire (/dev/sdb1) pour stocker les données ?

N’utilisez pas le disque temporaire (/dev/sdb1) pour stocker des données. Il ne sert qu’au stockage temporaire. Vous risquez donc de perdre des données sans pouvoir les récupérer.

## Puis-je copier ou cloner une machine virtuelle Azure ?

Oui. Pour obtenir des instructions, consultez [Création d’une copie d’une machine virtuelle Linux dans le modèle de déploiement Resource Manager](virtual-machines-linux-copy-vm.md).

## Pourquoi ne vois-je pas les régions Centre et Est du Canada dans Azure Resource Manager ?

Les deux régions Centre et Est du Canada ne sont pas enregistrées automatiquement lors de la création de machines virtuelles pour des abonnements Azure existants. Cet enregistrement s’effectue automatiquement lorsqu’une machine virtuelle est déployée par le biais du portail Azure dans n’importe quelle autre région à l’aide d’Azure Resource Manager. Une fois une machine virtuelle déployée dans toute autre région Azure, les nouvelles régions doivent être disponibles pour les machines virtuelles suivantes.

## Puis-je ajouter une carte réseau à ma machine virtuelle après sa création ?

Non. L’ajout d’une carte réseau n’est possible que lors de la création.

<!---HONumber=AcomDC_0817_2016-->
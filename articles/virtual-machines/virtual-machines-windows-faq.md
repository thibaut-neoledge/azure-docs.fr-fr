<properties
	pageTitle="Forum aux questions sur les machines virtuelles Windows | azure.microsoft.com/ Azure"
	description="Fournit des réponses à certaines questions courantes sur les machines virtuelles Azure créées avec le modèle de déploiement Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/16/2016"
	ms.author="cynthn"/>

# Questions fréquentes sur les machines virtuelles Azure 


Cet article traite certaines questions courantes concernant les machines virtuelles Windows créées dans Azure avec le modèle de déploiement Resource Manager. Pour la version Linux de cette rubrique, consultez [Forum aux questions sur les machines virtuelles Linux](virtual-machines-linux-faq.md).

## Qu’est-il possible d’exécuter sur une machine virtuelle Azure ?

Tous les abonnés peuvent exécuter des logiciels serveur sur une machine virtuelle Azure. Pour plus d’informations sur la stratégie de prise en charge des logiciels serveur azure.microsoft.com/ en cours d’exécution dans Azure, consultez [Prise en charge des logiciels serveur azure.microsoft.com/ pour les machines virtuelles azure.microsoft.com/ Azure](https://support.azure.microsoft.com/.com/kb/2721672).

Certaines versions de Windows 7 et Windows 8.1 sont disponibles pour les abonnés MSDN Azure et les abonnés Développement et test MSDN avec paiement à l’utilisation (pour les tâches de test et de développement). Pour plus d’informations, notamment des instructions et des limitations, voir [Images de client Windows pour les abonnés MSDN](http://azure.azure.microsoft.com/.com/blog/2014/05/29/windows-client-images-on-azure/).


## Quelle quantité de stockage puis-je utiliser avec une machine virtuelle ?

Chaque disque de données peut avoir une capacité allant jusqu’à 1 To Le nombre de disques de données que vous pouvez utiliser dépend de la taille de la machine virtuelle. Pour en savoir plus, consultez la rubrique [Tailles de machines virtuelles](virtual-machines-windows-sizes.md).

Un compte de stockage Azure fournit le stockage pour le disque du système d’exploitation et tout disque de données. Chaque disque est un fichier .vhd stocké sous la forme d’un objet blob de pages. Pour plus d’informations sur la tarification, voir [Tarification – Stockage](https://azure.azure.microsoft.com/.com/pricing/details/storage/).


## Comment puis-je accéder à ma machine virtuelle ?

Vous devez établir une connexion à distance à l’aide du protocole RDP (Remote Desktop Protocol) pour une machine virtuelle Windows. Pour plus d’informations, consultez [Connexion à une machine virtuelle Azure exécutant Windows](virtual-machines-windows-connect-logon.md). Deux connexions simultanées maximum sont prises en charge, sauf si le serveur est configuré en tant qu’hôte de session Services Bureau à distance.


En cas de problème de connexion, consultez [Résolution des problèmes de connexion Bureau à distance avec une machine virtuelle Azure exécutant Windows](virtual-machines-windows-troubleshoot-rdp-connection.md).

Si vous connaissez bien Hyper-V, vous pouvez rechercher un outil similaire à VMConnect. Azure n’offre pas d’outil similaire car l’accès console à une machine virtuelle n’est pas pris en charge.

## Puis-je utiliser le disque temporaire (lecteur D: par défaut) pour stocker des données ?

N’utilisez pas le disque temporaire pour stocker des données. Il ne sert qu’au stockage temporaire. Vous risqueriez donc de perdre des données sans pouvoir les récupérer. Cela peut arriver si la machine virtuelle est déplacée vers un autre hôte, après le redimensionnement d’une machine virtuelle, la mise à jour de l’hôte ou une panne matérielle sur l’hôte, par exemple.

Si vous avez une application qui doit utiliser le lecteur D:, vous pouvez réaffecter les lettres de lecteur afin que le disque temporaire utilise une autre lettre que D. Pour obtenir des instructions, consultez la page [Modification de la lettre de lecteur du disque temporaire Windows](virtual-machines-windows-classic-change-drive-letter.md).

## Comment puis-je modifier la lettre de lecteur d’un disque temporaire ?

Vous pouvez changer la lettre de lecteur en déplaçant le fichier d’échange et en réaffectant les lettres de lecteur. Toutefois, vous devrez veiller à effectuer les étapes dans un ordre spécifique. Pour obtenir des instructions, consultez la page [Modification de la lettre de lecteur du disque temporaire Windows](virtual-machines-windows-classic-change-drive-letter.md).

## Puis-je ajouter une machine virtuelle à un groupe à haute disponibilité ?

Non. Si vous souhaitez que votre machine virtuelle fasse partie d’un groupe à haute disponibilité, vous devez la créer dans le groupe. Il n’existe actuellement aucun moyen d’ajouter une machine virtuelle dans un groupe à haute disponibilité, une fois celle-ci créée.

## Puis-je télécharger une machine virtuelle dans Azure ?

Oui. Pour obtenir des instructions, consultez [Charger une image de machine virtuelle Windows dans azure.microsoft.com/ Azure pour des déploiements Resource Manager](virtual-machines-windows-upload-image.md)

## Puis-je redimensionner le disque du système d’exploitation ?

Oui. Pour obtenir des instructions, consultez [Extension du lecteur de système d’exploitation d’une machine virtuelle dans un groupe de ressources Azure](virtual-machines-windows-expand-os-disk.md).

## Puis-je copier ou cloner une machine virtuelle Azure ?

Oui. Pour obtenir des instructions, consultez [Création d’une copie d’une machine virtuelle Windows dans le modèle de déploiement Resource Manager](virtual-machines-windows-specialized-image.md).

## Pourquoi les régions Centre et Est du Canada ne sont-elles pas visibles dans Azure Resource Manager ?

Les deux régions Centre et Est du Canada ne sont pas enregistrées automatiquement lors de la création de machines virtuelles pour des abonnements Azure existants. Cet enregistrement s’effectue automatiquement lorsqu’une machine virtuelle est déployée par le biais du portail Azure dans n’importe quelle autre région à l’aide d’Azure Resource Manager. Une fois une machine virtuelle déployée dans toute autre région Azure, les nouvelles régions doivent être disponibles pour les machines virtuelles suivantes.

## Azure prend-il en charge les machines virtuelles Linux ?

Oui. Pour créer rapidement une machine virtuelle Linux de test, consultez [Création d’une machine virtuelle Linux sur Azure à l’aide du portail](virtual-machines-linux-quick-create-portal.md).

## Puis-je ajouter une carte réseau à ma machine virtuelle après sa création ?

Non. L’ajout d’une carte réseau n’est possible que lors de la création.

<!---HONumber=AcomDC_0817_2016-->
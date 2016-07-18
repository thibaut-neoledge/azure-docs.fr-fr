<properties
	pageTitle="Optimisation de votre machine virtuelle Linux sur Azure | Microsoft Azure"
	description="Découvrez différents conseils d’optimisation qui vous permettront de configurer votre machine virtuelle Linux de manière à en optimiser les performances sur Azure."
	keywords="machine virtuelle linux, linux machine virtuelle, machine virtuelle ubuntu" 
	services="virtual-machines-linux"
	documentationCenter=""
	authors="rickstercdn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/21/2016"
	ms.author="rclaus"/>

# Optimiser votre machine virtuelle Linux sur Azure

Il est simple de créer une machine virtuelle Linux à partir de la ligne de commande ou du portail. Ce didacticiel vous explique comment configurer votre machine virtuelle de manière à en optimiser les performances sur la plateforme Microsoft Azure. Dans cette rubrique, une machine virtuelle de serveur Ubuntu est utilisée, mais vous pouvez également créer des machines virtuelles Linux en utilisant vos [propres images en tant que modèles](virtual-machines-linux-create-upload-generic.md).

## Composants requis

Cette rubrique repose sur l’hypothèse que vous disposez déjà d’un abonnement Azure actif ([obtenir une version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/)), que vous avez [installé l’interface de ligne de commande Azure](../xplat-cli-install.md) et que vous avez déjà approvisionné une machine virtuelle dans votre abonnement Azure. Avant de faire quoi que ce soit avec Azure, vous devez vous authentifier auprès de votre abonnement. Pour effectuer cette opération avec l’interface de ligne de commande Azure, il vous suffit de taper `azure login` afin de démarrer le processus interactif.

## Disque de système d’exploitation Azure

Une fois que vous avez créé une machine virtuelle Linux dans Azure, deux disques lui sont associés : /dev/sda est votre disque de système d’exploitation, et /dev/sdb votre disque temporaire. Le disque de système d’exploitation principal (/dev/sda) est exclusivement destiné au système d’exploitation, car il est optimisé pour le démarrage rapide des machines virtuelles et ne délivrera pas de performances adéquates pour vos charges de travail. Vous pourrez attacher un ou plusieurs disques à votre machine virtuelle afin de bénéficier d’un stockage persistant et optimisé pour vos données.

## Ajout de disques selon vos objectifs de taille et de performances 

Selon la taille de machine virtuelle que vous choisissez, vous pouvez attacher jusqu’à 16 disques supplémentaires dans une machine virtuelle série A, 32 disques dans une machine série D et 64 disques dans une machine série G, chacun de ces disques pouvant présenter une taille maximale de 1 To. Nous vous recommandons d’ajouter des disques en fonction de vos besoins en matière d’espace et d’E/S par seconde. Les performances ciblées de chaque disque sont de 500 E/S par seconde pour un stockage Standard et de 5 000 E/S par seconde maximum pour un stockage Premium. Pour plus d’informations sur les disques Premium Storage, consultez [Premium Storage : stockage à hauts niveaux de performances pour les machines virtuelles Azure](../storage/storage-premium-storage.md).

Pour bénéficier des plus hauts niveaux d’E/S par seconde sur les disques de stockage Premium dont le paramètre de cache est défini sur « ReadOnly » ou sur « None », vous devez désactiver les « barrières » lors du montage du système de fichiers dans Linux. Ces barrières sont inutiles, car les écritures sur les disques de stockage Premium sont pérennes avec ces paramètres de cache.

- Si vous utilisez **reiserFS**, désactivez les barrières à l'aide de l'option de montage « barrier=none ». (Pour activer les barrières, utilisez « barrier=flush ».)
- Si vous utilisez **ext3/ext4**, désactivez les barrières à l'aide de l'option de montage « barrier=0 ». (Pour activer les barrières, utilisez « barrier=1 ».)
- Si vous utilisez **XFS**, désactivez les barrières à l'aide de l'option de montage « nobarrier ». (Pour activer les barrières, utilisez l’option « barrier ».)

## Considérations relatives aux comptes de stockage

Lorsque vous créez votre machine virtuelle Linux dans Azure, veillez à attacher les disques à partir de comptes de stockage résidant dans la même région que votre machine virtuelle afin d’assurer une proximité étroite et de minimiser la latence du réseau. Chaque compte de stockage Standard présente une limite de 20 000 E/S par seconde et une taille maximale de 500 To. Ceci correspond à environ 40 disques utilisés de manière intensive, incluant le disque de système d’exploitation et tous les disques de données que vous créez. Pour les comptes de stockage Premium, il n’existe aucun nombre maximal d’E/S par seconde, mais une limite de taille de 32 To.

Lorsque vous devez gérer des charges de travail d’E/S par seconde très élevées et que vous avez choisi le stockage Standard pour vos disques, vous pouvez avoir besoin de fractionner les disques entre plusieurs comptes de stockage pour ne pas risquer d’atteindre la limite de 20 000 E/S par seconde propre aux comptes de stockage Standard. Votre machine virtuelle peut contenir une combinaison de disques associés à différents comptes de stockage et types de comptes de stockage pour vous offrir la configuration optimale souhaitée.

## Votre lecteur temporaire de machine virtuelle

Lorsque vous créez une machine virtuelle, Azure vous fournit par défaut un disque de système d’exploitation (/dev/sda) et un disque temporaire (/dev/sdb). Tous les disques que vous ajouterez apparaîtront sous la forme /dev/sdc, /dev/sdd, /dev/sde, etc. Les données figurant sur votre disque temporaire (/dev/sdb) ne sont pas pérennes et risquent d’être perdues si des événements spécifiques, tels qu’un redimensionnement, un redéploiement ou une maintenance de machine virtuelle, imposent un redémarrage de votre machine virtuelle. La taille et le type de votre disque temporaire sont liés à la taille de machine virtuelle que vous avez choisie au moment du déploiement. Dans le cas des machines virtuelles de taille Premium (séries DS, G et DS\_V2), le lecteur temporaire est secondé par un disque SSD local offrant un surcroît de performances pouvant atteindre 48 000 E/S par seconde.

## Fichier d’échange Linux

Les images de machine virtuelle déployées à partir d’Azure Marketplace présentent un agent Linux de machine virtuelle intégré au système d’exploitation qui permet à la machine virtuelle d’interagir avec divers services Azure. Si vous ayez déployé une image standard à partir d’Azure Marketplace, vous devez suivre la procédure ci-après pour configurer les paramètres de votre fichier d’échange Linux de manière adéquate :

Vous devez rechercher et modifier deux entrées spécifiques dans le fichier **/etc/waagent.conf**. Ces entrées contrôlent l’existence d’un fichier d’échange dédié et la taille de ce fichier. Les paramètres à modifier sont `ResourceDisk.EnableSwap=N` et `ResourceDisk.SwapSizeMB=0`.

Vous devez les modifier comme suit :

* ResourceDisk.EnableSwap=Y
* ResourceDisk.SwapSizeMB={taille en Mo en fonction de vos besoins}

Une fois que vous avez effectué les modifications, vous devez redémarrer waagent ou votre machine virtuelle Linux afin de refléter ces modifications. Vous pouvez vérifier que ces modifications ont été implémentées et qu’un fichier d’échange a été créé en utilisant la commande `free` pour visualiser l’espace disponible. L’exemple ci-dessous correspond à la création d’un fichier d’échange de 512 Mo suite à la modification du fichier waagent.conf.

    admin@mylinuxvm:~$ free
                total       used       free     shared    buffers     cached
    Mem:       3525156     804168    2720988        408       8428     633192
    -/+ buffers/cache:     162548    3362608
    Swap:       524284          0     524284
    admin@mylinuxvm:~$
 
## Algorithme de planification d’E/S pour le stockage Premium

Avec le noyau Linux 2.6.18, l’algorithme de planification d’E/S par défaut est passé de Deadline à CFQ (algorithme de file d’attente complètement équitable). Pour les modèles d’E/S à accès aléatoire, les différences de performances entre CFQ et Deadline sont minimes. Pour les disques SSD qui présentent un modèle d’E/S de disque principalement séquentiel, le retour à l’algorithme NOOP ou Deadline peut contribuer à améliorer les performances d’E/S.

### Visualiser le planificateur d’E/S actuel

Utilisez la commande suivante :

	admin@mylinuxvm:~# cat /sys/block/sda/queue/scheduler

Vous verrez la sortie suivante, indiquant le planificateur actuel.

	noop [deadline] cfq

###Changement du dispositif actuel (/dev/sda) de l’algorithme de planification d’E/S

Utilisez les commandes suivantes :

	azureuser@mylinuxvm:~$ sudo su -
	root@mylinuxvm:~# echo "noop" >/sys/block/sda/queue/scheduler
	root@mylinuxvm:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
	root@mylinuxvm:~# update-grub

>[AZURE.NOTE] Définir cette propriété pour /dev/sda uniquement n’est pas utile. Cette propriété doit être définie sur tous les disques de données présentant un modèle d’E/S majoritairement séquentiel.

Vous devriez voir la sortie suivante, qui indique que grub.cfg a été régénéré avec succès et que le planificateur par défaut a été mis à jour vers NOOP.

	Generating grub configuration file ...
	Found linux image: /boot/vmlinuz-3.13.0-34-generic
	Found initrd image: /boot/initrd.img-3.13.0-34-generic
	Found linux image: /boot/vmlinuz-3.13.0-32-generic
	Found initrd image: /boot/initrd.img-3.13.0-32-generic
	Found memtest86+ image: /memtest86+.elf
	Found memtest86+ image: /memtest86+.bin
	done

Pour la famille de distribution Redhat, la commande suivante suffit :

	echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

## Utilisation d’un RAID logiciel pour optimiser le nombre d’E/S par seconde

Si vos charges de travail nécessitent un niveau d’E/S par seconde supérieur à celui fourni par un seul disque, vous devrez utiliser une configuration de RAID logiciel composée de plusieurs disques. Étant donné qu’Azure assure déjà la résilience de disque au niveau de la couche de structure locale, une configuration d’entrelacement RAID-0 vous offrira un niveau de performances optimal. Vous devrez approvisionner et créer des disques dans l’environnement Azure et les attacher à votre machine virtuelle Linux avant de procéder au partitionnement, au formatage et au montage des lecteurs. Pour plus d’informations sur la configuration d’un RAID logiciel sur votre machine virtuelle Linux dans Azure, consultez le document **[Configuration d’un RAID logiciel sur Linux](virtual-machines-linux-configure-raid.md)**.


## Étapes suivantes

Comme dans toute procédure d’optimisation, n’oubliez pas que vous devrez effectuer des tests avant et après chaque modification afin d’en évaluer l’impact. L’optimisation est un processus graduel dont les résultats varient d’une machine à l’autre dans votre environnement. Ce qui fonctionne bien pour une configuration donnée n’offre pas nécessairement de bons résultats pour d’autres.

Voici quelques liens utiles vous permettant d’accéder à des ressources supplémentaires :

- [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure.](../storage/storage-premium-storage.md)
- [Guide d’utilisateur de l’agent Linux Azure](virtual-machines-linux-agent-user-guide.md)
- [Optimisation des performances MySQL sur les machines virtuelles Linux Azure](virtual-machines-linux-classic-optimize-mysql.md)
- [Configuration d’un RAID logiciel sur Linux](virtual-machines-linux-configure-raid.md)

<!---HONumber=AcomDC_0706_2016-->
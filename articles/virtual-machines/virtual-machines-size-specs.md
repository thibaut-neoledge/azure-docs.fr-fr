<properties
 pageTitle="Tailles des machines virtuelles | Microsoft Azure"
 description="Répertorie les différentes tailles de machines virtuelles et leurs capacités."
 services="virtual-machines"
 documentationCenter=""
 authors="cynthn"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>

<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="09/02/2015"
 ms.author="cynthn"/>

# Tailles de machines virtuelles

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Vue d'ensemble

Cet article décrit les tailles et options disponibles pour les ressources de calcul des machines virtuelles que vous pouvez utiliser pour exécuter vos applications et charges de travail. Il expose également les points à prendre en considération pour le déploiement quand vous planifiez l’utilisation de ces ressources.

Azure Virtual Machines et Azure Cloud Services sont deux des nombreux types de ressource de calcul proposés par Azure. Pour obtenir plus d’explications, consultez [Calcul des options d’hébergement fournies par Azure](http://go.microsoft.com/fwlink/p/?LinkID=311926).

>[AZURE.NOTE]Pour connaître les limites d’Azure en la matière, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md).

## Considérations en matière de planification

Les machines virtuelles sont disponibles en deux niveaux : de base et standard. Les deux types offrent différents choix de tailles, mais le niveau de base ne fournit pas certaines des fonctionnalités disponibles avec le niveau standard, comme l’équilibrage de charge et la mise à l’échelle automatique. Le niveau standard des tailles se compose de différentes séries : A, D, DS, G et GS. Voici des considérations quant à certaines de ces tailles :

*   Les machines virtuelles de la série D sont conçues pour exécuter des applications qui nécessitent une puissance de calcul et des performances de disque temporaire supérieures. Ces machines virtuelles se caractérisent par des processeurs plus rapides, un rapport mémoire-cœur plus élevé et un disque SSD pour le disque temporaire. Pour plus d'informations, consultez l'annonce sur le blog Azure : [Tailles des machines virtuelles de la nouvelle série D](http://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).

*   La série Dv2, suite de la série D d’origine, comprend un processeur plus puissant. Le processeur de la série Dv2 est environ 35 % plus rapide que le processeur de la série D. Il est basé sur la dernière génération de processeur 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) et comporte la technologie 2.0 Intel Turbo Boost, et peut atteindre 3,2 GHz. La série Dv2 a les mêmes configurations de disque et de mémoire que la série D.

    La série Dv2 sera disponible dans les différentes régions selon le calendrier suivant : Octobre 2015 : Est des États-Unis 2, Centre des États-Unis, Nord-Centre des États-Unis, Ouest des États-Unis Novembre 2015 : Est des États-Unis, Europe septentrionale, Europe occidentale Janvier 2016 : Sud-Centre des États-Unis, Est de l’Asie-Pacifique, Sud-est de l’Asie-Pacifique, Est du Japon, Ouest du Japon, Est de l’Australie, Sud-est de l’Australie, Sud du Brésil


*   Les machines virtuelles de la série G offrent la plus grande taille et les meilleures performances ; elles s’exécutent sur des hôtes équipés de processeurs de la famille Intel Xeon E5 V3.

*   Les machines virtuelles de la série DS et GS peuvent utiliser un stockage Premium, qui offre un stockage hautes performances à faible latence pour les charges de travail impliquant des E/S intensives. Ces machines virtuelles utilisent des disques SSD pour héberger les disques de la machine virtuelle et offrent également un cache de disque SSD local. Le stockage Premium est disponible dans certaines régions. Pour plus d’informations, consultez [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../storage-premium-storage-preview-portal.md).

La taille de la machine virtuelle a une incidence sur la tarification. La taille influe également sur les capacités de traitement, de mémoire et de stockage de la machine virtuelle. Les coûts de stockage sont calculés séparément en fonction des pages utilisées dans le compte de stockage. Pour plus d’informations, voir les pages [Machines virtuelles Tarification](http://azure.microsoft.com/pricing/details/virtual-machines/) et [Tarification Azure Storage](http://azure.microsoft.com/pricing/details/storage/). Pour plus d’informations sur le stockage pour les machines virtuelles, consultez [À propos des disques et des disques durs virtuels pour les machines virtuelles](virtual-machines-disks-vhds.md).

Les considérations ci-dessous peuvent vous aider à choisir une taille :

*   La taille A0\\Basic\_A0 est disponible seulement via l’utilisation du Kit de développement logiciel (SDK) Azure version 1.3 ou ultérieure.  

*   La taille A1\\Basic\_A1 est la taille minimale recommandée pour les charges de travail de production.

*   Quand vous utilisez SQL Server Enterprise Edition, sélectionnez une machine virtuelle avec 4 ou 8 cœurs de processeur.

*   Certains hôtes physiques des centres de données Azure ne prennent pas en charge les tailles de machines virtuelles élevées, comme A5 à A11. Ainsi, vous pouvez obtenir le message d’erreur **Échec de la configuration de la machine virtuelle <machine name>** ou **Échec de la création de la machine virtuelle <machine name>** pendant le redimensionnement d’une machine virtuelle existante, la création d’une machine virtuelle dans un réseau virtuel créé avant le 16 avril 2013 ou l’ajout d’une machine virtuelle à un service cloud existant. Pour découvrir les solutions de contournement pour chaque scénario de déploiement, voir la rubrique [Erreur : « Échec de la configuration de la machine virtuelle »](https://social.msdn.microsoft.com/Forums/fr-FR/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) (en anglais) sur le forum d’assistance.

*   Les tailles de machines virtuelles A8/A10 et A9/A11 présentent les mêmes capacités. Les instances de machine virtuelle A8 et A9 intègrent une carte réseau supplémentaire qui est connectée à un réseau RDMA pour accélérer la communication entre les machines virtuelles. Les instances A8 et A9 sont conçues pour les applications de calcul hautes performances qui nécessitent une communication constante et à faible latence entre les nœuds pendant l'exécution, comme les applications qui utilisent l'interface MPI (Message Passing Interface). Les instances de machine virtuelle A10 et A11 ne sont pas équipées de cette carte réseau supplémentaire. Ces instances sont conçues pour les applications de calcul hautes performances qui n'ont pas besoin d'une communication constante et à faible latence entre les nœuds, également appelées applications paramétriques ou massivement parallèles.

*   Les séries Dv2 et D sont idéales pour les applications qui exigent des processeurs plus rapides, de meilleures performances de disque local, ou qui ont des exigences de mémoire plus élevées. Elles offrent une combinaison puissante pour de nombreuses applications professionnelles.

## Limites générales

Ce tableau indique les limites qui s’appliquent, quelle que soit la taille d’une machine virtuelle, pour les machines virtuelles créées en utilisant le modèle de déploiement de gestion des services.

[AZURE.INCLUDE [azure-virtual-machines-limits](../../includes/azure-virtual-machines-limits.md)]

Ce tableau indique les limites qui s’appliquent, quelle que soit la taille d’une machine virtuelle, pour les machines virtuelles créées en utilisant le modèle de déploiement Resource Manager.

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

## Tableaux des tailles

Les tableaux ci-après indiquent les tailles et les capacités qu’elles offrent.

>[AZURE.NOTE]La capacité de stockage est représentée avec 1024^3 octets comme unité de mesure pour les Go. Cette unité est parfois appelée gibioctet ou définition en base 2. Quand vous comparez des tailles qui utilisent des systèmes en base différente, n’oubliez pas que les tailles en base 2 peuvent paraître plus petites que celles en base 10, mais que pour une taille spécifique (par exemple, 1 Go), un système en base 2 offre une capacité plus élevée qu’un système en base 10, car 1 024^3 est supérieur à 1 000^3.

### Niveau de base

|Taille – Portail Azure\\applets de commande et API|Cœurs d’unité centrale|Mémoire|Cartes réseau (max)|Tailles de disque maximales – Machine virtuelle|Nombre maximal de disques de données (1 023 Go chacun)|Bande passante Nombre maximal d’opérations d’E/S par seconde (300 par disque)|
|---|---|---|---|---|---|---|
|0A0\\Basic\_A0|1|768 Mo|1|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 20 Go</p>|1|1 x 300|
|A1\\Basic\_A1|1|1,75 Go|1|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 40 Go</p>|2|2 x 300|
|A2\\Basic\_A2|2|3,5 Go|1|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 60 Go</p>|4|4 x 300|
|A3\\Basic\_A3|4|7 Go|1|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 120 Go</p>|8|8 x 300|
|A4\\Basic\_A4|8|14 Go|1|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 240 Go</p>|16|16 x 300|

### Niveau standard : série A

|Taille – Portail Azure\\applets de commande et API|Cœurs d’unité centrale|Mémoire|Cartes réseau (max)|Tailles de disque maximales – Machine virtuelle|Nombre maximal de disques de données (1 023 Go chacun)|Bande passante Nombre maximal d’opérations d’E/S par seconde (500 par disque)|
|---|---|---|---|---|---|---|
|A0\\très petite|1|768 Mo|1|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 20 Go</p>|1|1 x 500|
|A1\\petite|1|1,75 Go|1|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 70 Go</p>|2|2 x 500|
|A2\\moyenne|2|3,5 Go|1|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 135 Go</p>|4|4 x 500|
|A3\\grande|4|7 Go|2|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 285 Go</p>|8|8 x 500|
|A4\\très grande|8|14 Go|4|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 605 Go</p>|16|16 x 500|
|A5\\identique|2|14 Go|1|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 135 Go</p>|4|4 x 500|
|A6\\identique|4|28 Go|2|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 285 Go</p>|8|8 x 500|
|A7\\identique|8|56 Go|4|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 605 Go</p>|16|16 x 500|
|A8\\identique|8|56 Go|2|<p><p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 382 Go</p><blockquote><p>Remarque : pour plus d’informations et pour connaître les éléments à prendre en considération sur l’utilisation de cette taille, consultez <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">À propos des instances de calcul intensif A8, A9, A10 et A11</a>.</p></blockquote>|16|16 x 500|
|A9\\identique|16|112 Go|4|<p><p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 382 Go</p><blockquote><p>Remarque : pour plus d’informations et pour connaître les éléments à prendre en considération sur l’utilisation de cette taille, consultez <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">À propos des instances de calcul intensif A8, A9, A10 et A11</a>.</p></blockquote>|16|16 x 500|
|A10\\identique|8|56 Go|2|<p><p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 382 Go</p><blockquote><p>Remarque : pour plus d’informations et pour connaître les éléments à prendre en considération sur l’utilisation de cette taille, consultez <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">À propos des instances de calcul intensif A8, A9, A10 et A11</a>.</p></blockquote>|16|16 x 500|
|A11\\identique|16|112 Go|4|<p><p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 382 Go</p><blockquote><p>Remarque : pour plus d’informations et pour connaître les éléments à prendre en considération sur l’utilisation de cette taille, consultez <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">À propos des instances de calcul intensif A8, A9, A10 et A11</a>.</p></blockquote>|16|16 x 500|

### Niveau standard : série D

|Taille – Portail Azure\\applets de commande et API|Cœurs d’unité centrale|Mémoire|Cartes réseau (max)|Tailles de disque maximales – Machine virtuelle|Nombre maximal de disques de données (1 023 Go chacun)|Bande passante Nombre maximal d’opérations d’E/S par seconde (500 par disque)|
|---|---|---|---|---|---|---|
|Standard\_D1\\identique|1|3,5 Go|1|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire (SSD) = 50 Go</p>|2|2 x 500|
|Standard\_D2\\identique|2|7 Go|2|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire (SSD) = 100 Go</p>|4|4 x 500|
|Standard\_D3\\identique|4|14 Go|4|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire (SSD) = 200 Go</p>|8|8 x 500|
|Standard\_D4\\identique|8|28 Go|8|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire (SSD) = 400 Go</p>|16|16 x 500|
|Standard\_D11\\identique|2|14 Go|2|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire (SSD) = 100 Go</p>|4|4 x 500|
|Standard\_D12\\identique|4|28 Go|4|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire (SSD) = 200 Go</p>|8|8 x 500|
|Standard\_D13\\identique|8|56 Go|8|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire (SSD) = 400 Go</p>|16|16 x 500|
|Standard\_D14\\identique|16|112 Go|16|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire (SSD) = 800 Go</p>|32|32 x 500|

### Niveau standard : série Dv2

|Taille – Portail Azure\\applets de commande et API|Cœurs d’unité centrale|Mémoire|Cartes réseau (max)|Tailles de disque maximales – Machine virtuelle|Nombre maximal de disques de données (1 023 Go chacun)|Bande passante Nombre maximal d’opérations d’E/S par seconde (500 par disque)|
|---|---|---|---|---|---|---|
|Standard\_D1\_v2\\same|1|3,5 Go|1|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire (SSD) = 50 Go</p>|2|2 x 500|
|Standard\_D2\_v2\\same|2|7 Go|2|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire (SSD) = 100 Go</p>|4|4 x 500|
|Standard\_D3\_v2\\same|4|14 Go|4|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire (SSD) = 200 Go</p>|8|8 x 500|
|Standard\_D4\_v2\\same|8|28 Go|8|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire (SSD) = 400 Go</p>|16|16 x 500|
|Standard\_D5\_v2\\same|16|56 Go|16|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire (SSD) = 800 Go</p>|32|32 x 500|
|Standard\_D11\_v2\\same|2|14 Go|2|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire (SSD) = 100 Go</p>|4|4 x 500|
|Standard\_D12\_v2\\same|4|28 Go|4|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire (SSD) = 200 Go</p>|8|8 x 500|
|Standard\_D13\_v2\\same|8|56 Go|8|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire (SSD) = 400 Go</p>|16|16 x 500|
|Standard\_D14\_v2\\same|16|112 Go|16|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire (SSD) = 800 Go</p>|32|32 x 500|

### Niveau standard : série DS*

|Taille – Portail Azure\\applets de commande et API|Cœurs d’unité centrale|Mémoire|Cartes réseau (max)|Tailles de disque maximales – Machine virtuelle|Nombre maximal de disques de données (1 023 Go chacun)|Taille de cache (Go)|Nombre maximal d’opérations d’E/S par seconde du disque et bande passante|
|---|---|---|---|---|---|---|---|
|Standard\_DS1\\identique|1|3,5|1|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 7 Go</p>|2|43|<p>3 200</p><p>32 Mo par seconde</p>|
|Standard\_DS2\\identique|2|7|2|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 14 Go</p>|4|86|<p>6 400</p><p>64 Mo par seconde</p>|
|Standard\_DS3\\identique|4|14|4|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 28 Go</p>|8|172|<p>12 800</p><p>128 Mo par seconde</p>|
|Standard\_DS4\\identique|8|28|8|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 56 Go</p>|16|344|<p>25 600</p><p>256 Mo par seconde</p>|
|Standard\_DS11\\identique|2|14|2|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 28 Go</p>|4|72|<p>6 400</p><p>64 Mo par seconde</p>|
|Standard\_DS12\\identique|4|28|4|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 56 Go</p>|8|144|<p>12 800</p><p>128 Mo par seconde</p>|
|Standard\_DS13\\identique|8|56|8|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 112 Go</p>|16|288|<p>25 600</p><p>256 Mo par seconde</p>|
|Standard\_DS14\\identique|16|112|16|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 224 Go</p>|32|576|<p>50 000</p><p>512 Mo par seconde</p>|

** Le nombre maximal d’opérations d’entrée/sortie par seconde (IOPS) et le débit (bande passante) possibles avec une machine virtuelle de la série DS sont affectés par la taille du disque. Pour plus d’informations, consultez [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../storage-premium-storage-preview-portal.md).

### Niveau standard : série G

|Taille – Portail Azure\\applets de commande et API|Cœurs d’unité centrale|Mémoire|Cartes réseau (max)|Tailles de disque maximales – Machine virtuelle|Nombre maximal de disques de données (1 023 Go chacun)|Bande passante Nombre maximal d’opérations d’E/S par seconde (500 par disque)|
|---|---|---|---|---|---|---|
|Standard\_G1\\identique|2|28 Go|1|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 384 Go</p>|4|4 x 500|
|Standard\_G2\\identique|4|56 Go|2|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 768 Go</p>|8|8 x 500|
|Standard\_G3\\identique|8|112 Go|4|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 1 536 Go</p>|16|16 x 500|
|Standard\_G4\\identique|16|224 Go|8|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 3 072 Go</p>|32|32 x 500|
|Standard\_G5\\identique|32|448 Go|16|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 6 144 Go</p>|64|<p>64 x 500</p>|

### Niveau standard : série GS

|Taille – Portail Azure\\applets de commande et API|Cœurs d’unité centrale|Mémoire|Cartes réseau (max)|Tailles de disque maximales – Machine virtuelle|Nombre maximal de disques de données (1 023 Go chacun)|Taille de cache (Go)|Nombre maximal d’opérations d’E/S par seconde du disque et bande passante|
|---|---|---|---|---|---|---|---|
|Standard\_GS1|2|28|1|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 56 Go</p>|4|264|<p>5 000</p><p>125 Mo par seconde</p>|
|Standard\_GS2|4|56|2|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 112 Go</p>|8|528|<p>10 000</p><p>250 Mo par seconde</p>|
|Standard\_GS3|8|112|4|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 224 Go</p>|16|1 056|<p>20 000</p><p>500 Mo par seconde</p>|
|Standard\_GS4|16|224|8|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 448 Go</p>|32|2 112|<p>40 000</p><p>1 000 Mo par seconde</p>|
|Standard\_GS5|32|448|16|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 896 Go</p>|64|4 224|<p>80 000</p><p>2 000 Mo par seconde</p>|


### Voir aussi

[Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md)

[À propos des instances de calcul intensif A8, A9, A10 et A11](virtual-machines-a8-a9-a10-a11-specs.md)

<!---HONumber=Oct15_HO4-->
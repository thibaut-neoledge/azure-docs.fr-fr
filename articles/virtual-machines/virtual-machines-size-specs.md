<properties
 pageTitle="Tailles de machines virtuelles"
 description="Répertorie les différentes tailles de machines virtuelles et leurs capacités."
 services="virtual-machines"
 documentationCenter=""
 authors="KBDAzure"
 manager="timlt"
 editor=""/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="07/02/2015"
 ms.author="kathydav"/>

# Tailles de machines virtuelles

## Vue d'ensemble

Cet article décrit les tailles et options disponibles pour les ressources de calcul des machines virtuelles que vous pouvez utiliser pour exécuter vos applications et charges de travail. Il expose également les points à prendre en considération pour le déploiement quand vous planifiez l’utilisation de ces ressources.

Azure Virtual Machines et Azure Cloud Services sont deux des nombreux types de ressource de calcul proposés par Azure. Pour plus de détails, consultez l'article [Modèles d'exécution Azure](http://go.microsoft.com/fwlink/p/?LinkID=311926).

>[AZURE.NOTE]Pour connaître les limites Azure associées, voir l’article [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md).

## Considérations en matière de déploiement

Les machines virtuelles sont disponibles en deux niveaux : de base et standard. Les deux types offrent un choix de tailles, mais le niveau de base ne fournit pas certaines des fonctionnalités disponibles avec le niveau standard, comme l’équilibrage de charge et la mise à l’échelle automatique. Le niveau standard des tailles englobe la série A, la série D, la série DS et la série G.

*   Les machines virtuelles de la série D sont conçues pour exécuter des applications qui nécessitent une puissance de calcul et des performances de disque temporaire supérieures. Ces machines virtuelles se caractérisent par des processeurs plus rapides, un rapport mémoire-cœur plus élevé et un disque SSD pour le disque temporaire. Pour plus d’informations, voir l’annonce suivante sur le blog Azure : [Nouvelles tailles de machines virtuelles de la série D](http://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/) (en anglais).  

*   Les machines virtuelles de la série DS peuvent utiliser un stockage Premium, qui offre un stockage hautes performances à faible latence pour les charges de travail utilisant une quantité importante d’E/S. Ce stockage utilise des disques SSD pour héberger les disques d’une machine virtuelle et offre un cache de disque SSD local. Le stockage Premium est disponible en version préliminaire dans certaines régions. Pour plus d’informations, voir l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../storage-premium-storage-preview-portal.md).

*   Les machines virtuelles de la série G offrent la taille la plus importante et les meilleures performances ; elles s’exécutent sur des hôtes équipés de processeurs de la famille Intel Xeon E5 V3.

La taille de la machine virtuelle a une incidence sur la tarification. La taille influe également sur les capacités de traitement, de mémoire et de stockage de la machine virtuelle. Les coûts de stockage sont calculés séparément en fonction des pages utilisées dans le compte de stockage. Pour plus d’informations, voir les pages [Machines virtuelles Tarification](http://go.microsoft.com/fwlink/p/?LinkId=398570) et [Tarification Azure Storage](http://azure.microsoft.com/pricing/details/storage/). Pour plus d’informations sur les disques et le stockage utilisés dans Azure Virtual Machines, voir l’article [À propos des disques et des images dans Azure](https://msdn.microsoft.com/library/jj672979).

Les considérations ci-dessous peuvent vous aider à choisir une taille :

*   La taille A0\\Basic_A0 est uniquement disponible avec le Kit de développement logiciel (SDK) Azure version 1.3 ou ultérieure.  

*   La taille A1\\Basic_A1 est la taille minimale recommandée pour les charges de travail de production.

*   Quand vous utilisez SQL Server Enterprise Edition, sélectionnez une machine virtuelle avec 4 ou 8 cœurs d’unité centrale.

*   Certains hôtes physiques des centres de données Azure ne prennent pas en charge les tailles de machines virtuelles élevées, comme A5 à A11. Ainsi, vous pouvez obtenir le message d’erreur **Échec de la configuration de la machine virtuelle <machine name>** ou **Échec de la création de la machine virtuelle <machine name>** pendant le redimensionnement d’une machine virtuelle existante, la création d’une machine virtuelle dans un réseau virtuel créé avant le 16 avril 2013 ou l’ajout d’une machine virtuelle à un service cloud existant. Pour découvrir les solutions de contournement pour chaque scénario de déploiement, voir la rubrique [Erreur : « Échec de la configuration de la machine virtuelle »](http://social.msdn.microsoft.com/Forums/WAVirtualMachinesforWindows/thread/9693f56c-fcd3-4d42-850e-5e3b56c7d6be) (en anglais) sur le forum d’assistance.

*   Les tailles de machines virtuelles A8/A10 et A9/A11 présentent les mêmes capacités. Les instances de machine virtuelle A8 et A9 intègrent une carte réseau supplémentaire qui est connectée à un réseau RDMA pour accélérer la communication entre les machines virtuelles. Les instances A8 et A9 sont conçues pour les applications de calcul hautes performances qui nécessitent une communication constante et à faible latence entre les nœuds pendant l'exécution, comme les applications qui utilisent l'interface MPI (Message Passing Interface). Les instances de machine virtuelle A10 et A11 ne sont pas équipées de cette carte réseau supplémentaire. Ces instances sont conçues pour les applications de calcul hautes performances qui n'ont pas besoin d'une communication constante et à faible latence entre les nœuds, également appelées applications paramétriques ou massivement parallèles.

## Limites générales

Ce tableau indique les limites qui s’appliquent quelle que soit la taille d’une machine virtuelle, pour les machines virtuelles créées avec les outils de gestion des services.


[AZURE.INCLUDE [azure-virtual-machines-limits](../../includes/azure-virtual-machines-limits.md)]

Ce tableau indique les limites qui s’appliquent quelle que soit la taille d’une machine virtuelle, pour les machines virtuelles créées avec Resource Manager.

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

## Tableaux de taille

Les tableaux ci-après indiquent les tailles et les capacités offertes par chaque série.

>[AZURE.NOTE]La capacité de stockage est représentée par l’utilisation de 1 024^3 octets comme unité de mesure pour les Go. Cette unité est parfois appelée gibioctet ou définition en base 2. Quand vous comparez des tailles qui utilisent des systèmes en base différente, n’oubliez pas que les tailles en base 2 peuvent paraître plus petites que celles en base 10, mais que pour une taille spécifique (par exemple, 1 Go), un système en base 2 offre une capacité plus élevée qu’un système en base 10, car 1 024^3 est supérieur à 1 000^3.

## Niveau de base

|Taille – Portail de gestion\\applets de commande et API|Cœurs d’unité centrale|Mémoire|Tailles de disque maximales – Machine virtuelle|Nombre maximal de disques de données (1 023 Go chacun)|Bande passante Nombre maximal d’opérations d’E/S par seconde (300 par disque)|
|---|---|---|---|---|---|
|0A0\\Basic_A0|1|768 Mo|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 20 Go</p>|1|1 x 300|
|A1\\Basic_A1|1|1,75 Go|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 40 Go</p>|2|2 x 300|
|A2\\Basic_A2|2|3,5 Go|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 60 Go</p>|4|4 x 300|
|A3\\Basic_A3|4|7 Go|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 120 Go</p>|8|8 x 300|
|A4\\Basic_A4|8|14 Go|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 240 Go</p>|16|16 x 300|

## Niveau standard
### Série A et série D

|Taille – Portail de gestion\\applets de commande et API|Cœurs d’unité centrale|Mémoire|Tailles de disque maximales – Machine virtuelle|Nombre maximal de disques de données (1 023 Go chacun)|Bande passante Nombre maximal d’opérations d’E/S par seconde (500 par disque)|
|---|---|---|---|---|---|
|A0\\ très petite|1|768 Mo|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 20 Go</p>|1|1 x 500|
|A1\\petite|1|1,75 Go|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 70 Go</p>|2|2 x 500|
|A2\\moyenne|2|3,5 Go|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 135 Go</p>|4|4 x 500|
|A3\\grande|4|7 Go|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 285 Go</p>|8|8 x 500|
|A4\\très grande|8|14 Go|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 605 Go</p>|16|16 x 500|
|A5(identique)|2|14 Go|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 135 Go</p>|4|4 x 500|
|A6(identique)|4|28 Go|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 285 Go</p>|8|8 x 500|
|A7(identique)|8|56 Go|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 605 Go</p>|16|16 x 500|
|A8(identique)|8|56 Go|<p><p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 382 Go</p><blockquote><p>Remarque : pour plus d’informations et pour connaître les éléments à prendre en considération sur l’utilisation de cette taille, consultez l’article <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">À propos des instances de calcul intensif A8, A9, A10 et A11</a>.</p></blockquote>|16|16 x 500|
|A9(identique)|16|112 Go|<p><p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 382 Go</p><blockquote><p>Remarque : pour plus d’informations et pour connaître les éléments à prendre en considération sur l’utilisation de cette taille, consultez l’article <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">À propos des instances de calcul intensif A8, A9, A10 et A11</a>.</p></blockquote>|16|16 x 500|
|A10(identique)|8|56 Go|<p><p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 382 Go</p><blockquote><p>Remarque : pour plus d’informations et pour connaître les éléments à prendre en considération sur l’utilisation de cette taille, consultez l’article <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">À propos des instances de calcul intensif A8, A9, A10 et A11</a>.</p></blockquote>|16|16 x 500|
|A11(identique)|16|112 Go|<p><p>Système d’exploitation = 1 023 Go</p><p>Temporaire = 382 Go</p><blockquote><p>Remarque : pour plus d’informations et pour connaître les éléments à prendre en considération sur l’utilisation de cette taille, consultez l’article <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">À propos des instances de calcul intensif A8, A9, A10 et A11</a>.</p></blockquote>|16|16 x 500|
|Standard_D1(identique)|1|3,5 Go|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire (SSD) = 50 Go</p>|2|2 x 500|
|Standard_D2(identique)|2|7 Go|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire (SSD) = 100 Go</p>|4|4 x 500|
|Standard_D3(identique)|4|14 Go|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire (SSD) = 200 Go</p>|8|8 x 500|
|Standard_D4(identique)|8|28 Go|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire (SSD) = 400 Go</p>|16|16 x 500|
|Standard_D11(identique)|2|14 Go|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire (SSD) = 100 Go</p>|4|4 x 500|
|Standard_D12(identique)|4|28 Go|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire (SSD) = 200 Go</p>|8|8 x 500|
|Standard_D13(identique)|8|56 Go|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire (SSD) = 400 Go</p>|16|16 x 500|
|Standard_D14(identique)|16|112 Go|<p>Système d’exploitation = 1 023 Go</p><p>Temporaire (SSD) = 800 Go</p>|32|32 x 500|


### Niveau standard – Série DS*

|Taille – Portail de gestion\\applets de commande et API|Cœurs d’unité centrale|Mémoire|Tailles de disque maximales – Machine virtuelle|Nombre maximal de disques de données (1 023 Go chacun)|Taille de cache (Go)|Nombre maximal d’opérations d’E/S par seconde du disque et bande passante|
|---|---|---|---|---|---|---|
|Standard_DS1(identique)|1|3,5|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 7 Go</p>|2|43|<p>3 200</p><p>32 Mo par seconde</p>|
|Standard_DS2(identique)|2|7|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 14 Go</p>|4|86|<p>6 400</p><p>64 Mo par seconde</p>|
|Standard_DS3(identique)|4|14|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 28 Go</p>|8|172|<p>12 800</p><p>128 Mo par seconde</p>|
|Standard_DS4(identique)|8|28|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 56 Go</p>|16|344|<p>25 600</p><p>256 Mo par seconde</p>|
|Standard_DS11(identique)|2|14|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 28 Go</p>|4|72|<p>6 400</p><p>64 Mo par seconde</p>|
|Standard_DS12(identique)|4|28|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 56 Go</p>|8|144|<p>12 800</p><p>128 Mo par seconde</p>|
|Standard_DS13(identique)|8|56|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 112 Go</p>|16|288|<p>25 600</p><p>256 Mo par seconde</p>|
|Standard_DS14(identique)|16|112|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 224 Go</p>|32|576|<p>50 000</p><p>512 Mo par seconde</p>|

*Le nombre maximal d’opérations d’entrée/sortie par seconde (IOPS) et le débit (bande passante) possibles avec une machine virtuelle de la série DS sont affectés par la taille du disque. Pour plus d’informations, voir l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../storage-premium-storage-preview-portal.md).

### Niveau standard – Série G

|Taille – Portail de gestion\\applets de commande et API|Cœurs d’unité centrale|Mémoire|Tailles de disque maximales – Machine virtuelle|Nombre maximal de disques de données (1 023 Go chacun)|Bande passante Nombre maximal d’opérations d’E/S par seconde (500 par disque)|
|---|---|---|---|---|---|
|Standard_G1(identique)|2|28 Go|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 384 Go</p>|4|4 x 500|
|Standard_G2(identique)|4|56 Go|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 768 Go</p>|8|8 x 500|
|Standard_G3(identique)|8|112 Go|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 1 536 Go</p>|16|16 x 500|
|Standard_G4(identique)|16|224 Go|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 3 072 Go</p>|32|32 x 500|
|Standard_G5(identique)|32|448 Go|<p>Système d’exploitation = 1 023 Go</p><p>Disque SSD local = 6 144 Go</p>|64|<p>64 x 500</p>|

### Voir aussi

[Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md)

[À propos des instances de calcul intensif A8, A9, A10 et A11](virtual-machines-a8-a9-a10-a11-specs.md)

<!---HONumber=July15_HO2-->
<properties 
   pageTitle="Présentation de StorSimple" 
   description="Décrit l'architecture et les fonctionnalités StorSimple et présente les composants StorSimple." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carolz" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="08/14/2015"
   ms.author="v-sharos@microsoft.com"/>

# Présentation de StorSimple 

## Vue d'ensemble

Microsoft Azure StorSimple est une solution efficace, rentable et facile à gérer qui élimine la plupart des problèmes et des frais liés à la protection des données et du stockage d’entreprise. Il utilise un appareil propriétaire (l’appareil Microsoft Azure StorSimple), une intégration avec des services cloud et un ensemble d’outils de gestion pour fournir une vue claire de tout le stockage d’entreprise, y compris le stockage cloud.

StorSimple utilise la hiérarchisation du stockage pour gérer les données stockées sur différents supports de stockage. Les données couramment utilisées sont stockées sur site sur des disques SSD, les données utilisées moins fréquemment sont stockées sur des lecteurs de disques durs et les données d’archivage sont placées dans le cloud. En outre, StorSimple utilise la déduplication et la compression pour réduire la quantité de stockage utilisée par les données. Le processus de hiérarchisation du stockage se déroule comme suit :

1. Un administrateur système configure un compte de stockage cloud Microsoft Azure.
2. L’administrateur utilise la console série et le service StorSimple Manager (exécuté dans le portail de gestion Azure) pour configurer l’appareil et le serveur de fichiers, en créant des volumes et des stratégies de protection des données. Le serveur de fichiers local utilise l’interface iSCSI (Internet Small Computer System Interface) pour accéder à l’appareil StorSimple.
3. À la base, StorSimple stocke les données sur le niveau SSD rapide de l’appareil.
4. Au fur et à mesure que le niveau SSD approche de sa capacité maximale, StorSimple déduplique et compresse les blocs de données les plus anciens et les déplace vers le niveau disque dur (HDD).
5. Au fur et à mesure que le niveau disque dur (HDD) approche de sa capacité maximale, StorSimple chiffre les blocs de données les plus anciens et les envoie de façon sécurisée au compte de stockage Microsoft Azure via HTTPS.
6. Microsoft Azure crée plusieurs réplicas des données dans son centre de données et dans un centre de données distant, ce qui permet de garantir la récupération des données en cas de sinistre. 
7. Quand le serveur de fichiers demande des données stockées dans le cloud, StorSimple les retourne de façon transparente et stocke une copie au niveau SSD de l’appareil StorSimple.

Outre la gestion du stockage, les fonctionnalités de protection des données StorSimple permettent de créer des sauvegardes à la demande et planifiées, et de les stocker localement ou dans le cloud. Les sauvegardes sont effectuées sous la forme d’instantanés incrémentiels, ce qui signifie qu’elles peuvent être créés et restaurés rapidement. Les instantanés cloud peuvent être d’une importance critique dans les scénarios de récupération d’urgence, car ils remplacent les systèmes de stockage secondaire (comme la sauvegarde sur bande) et vous permettent de restaurer des données sur votre centre de données ou sur d’autres sites si nécessaire.

>[AZURE.NOTE]La série StorSimple 8000 avec la mise à jour logicielle 1 ou ultérieure prend en charge Amazon S3 avec les services cloud RRS, HP et OpenStack, ainsi que Microsoft Azure. (Vous aurez néanmoins toujours besoin d’un compte de stockage Microsoft Azure pour la gestion de l’appareil.) Pour plus d’informations, consultez la rubrique [Configuration d’un nouveau compte de stockage pour le service](storsimple-deployment-walkthrough.md#configure-a-new-storage-account-for-the-service).

## Pourquoi utiliser StorSimple ?

Microsoft Azure StorSimple offre les avantages suivants :

- **Intégration transparente** : Microsoft Azure StorSimple utilise le protocole iSCSI (Internet Small Computer System Interface) pour lier de façon invisible les équipements de stockage de données. Cela garantit que les données stockées dans le cloud, dans un centre de données ou sur des serveurs distants semblent être stockées dans un emplacement unique.
- **Réduction des coûts de stockage** : Microsoft Azure StorSimple alloue suffisamment de stockage local ou de cloud pour répondre aux besoins actuels et étend le stockage cloud uniquement lorsque cela est nécessaire. Il réduit les besoins et les dépenses de stockage en éliminant les redondances de données (déduplication) et en utilisant la compression.
- **Gestion simplifiée du stockage** : Microsoft Azure StorSimple fournit des outils d’administration que vous pouvez utiliser pour configurer et gérer les données stockées sur site, sur un serveur distant et dans le cloud. En outre, vous pouvez gérer la sauvegarde et la restauration des fonctions depuis un composant logiciel enfichable Microsoft Management Console (MMC). StorSimple fournit une interface distincte et facultative que vous pouvez utiliser pour étendre les services de protection des données et de gestion StorSimple au contenu stocké sur des serveurs SharePoint. 
- **Récupération d’urgence et conformité améliorées** : Microsoft Azure StorSimple ne nécessite pas un long temps de récupération. Au contraire, il restaure les données lorsque vous en avez besoin. Cela signifie que les opérations normales peuvent se poursuivre, avec une interruption minimale. En outre, vous pouvez configurer des stratégies pour spécifier les planifications de sauvegarde et la conservation des données.
- **Mobilité des données** : les données téléchargées sur Microsoft Azure Cloud Services sont accessibles à partir d’autres sites à des fins de récupération et de migration. En outre, vous pouvez utiliser StorSimple pour configurer les appareils virtuels StorSimple sur les machines virtuelles de Microsoft Azure. Les machines virtuelles peuvent ensuite utiliser les appareils virtuels pour accéder aux données stockées à des fins de test ou de récupération. 

Le diagramme suivant fournit une vue d’ensemble de la solution Microsoft Azure StorSimple.

![Architecture de StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-system-architecture.png)

**Architecture de Microsoft Azure StorSimple**

## Composants de StorSimple

La solution Microsoft Azure StorSimple comprend les composants suivants :

- **Appareil Microsoft Azure StorSimple** : un groupe de stockage hybride local qui contient des disques SSD et des disques durs, ainsi que des contrôleurs redondants et des fonctionnalités de basculement automatique. Les contrôleurs gèrent la hiérarchisation du stockage en plaçant les données actuellement (ou souvent) utilisées sur le stockage local (sur les serveurs de l'appareil ou sur site), tout en déplaçant les données moins fréquemment utilisées sur le cloud.
- **Appareil virtuel StorSimple** : également appelé appliance virtuelle StorSimple, il s’agit d’une version logicielle du périphérique StorSimple qui réplique l’architecture et les fonctionnalités de l’appareil de stockage hybride physique. L’appareil virtuel StorSimple s’exécute sur un nœud unique dans une machine virtuelle Azure. L’appareil virtuel est approprié pour des tests et de petits scénarios pilotes. Il est impossible de créer un appareil virtuel StorSimple sur un serveur local ou sur un appareil StorSimple.
- **Windows PowerShell pour StorSimple** : une interface de ligne de commande que vous pouvez utiliser pour gérer l’appareil StorSimple. Windows PowerShell pour StorSimple dispose de fonctionnalités qui vous permettent d’inscrire votre appareil StorSimple, de configurer l’interface réseau sur votre appareil, d’installer certains types de mises à jour, de résoudre les problèmes de votre appareil en accédant à la session de support et de modifier l’état de l’appareil. Vous pouvez accéder à Windows PowerShell pour StorSimple en vous connectant à la console série ou à l’aide de l’accès distant Windows PowerShell.
- **Cmdlets PowerShell d’Azure StorSimple** : une collection de cmdlets Windows PowerShell qui vous permettent d'automatiser les tâches de niveau de service et la migration à partir de la ligne de commande. Pour plus d’informations sur les cmdlets Azure PowerShell pour StorSimple, consultez la page [Documentation de référence des cmdlets](https://msdn.microsoft.com/library/dn920427.aspx).
- **Service StorSimple Manager** : une extension du portail de gestion Azure qui vous permet de gérer un appareil physique StorSimple ou un appareil virtuel StorSimple à partir d’une seule interface Web. Vous pouvez utiliser le service StorSimple Manager pour créer et gérer les services, afficher et gérer des appareils, afficher les alertes, gérer des volumes, ainsi qu’afficher et gérer des stratégies de sauvegarde et le catalogue de sauvegarde.
- **Gestionnaire d’instantanés StorSimple** : un composant logiciel enfichable MMC qui utilise des groupes de volumes et Windows Volume Shadow Copy Service pour générer des sauvegardes cohérentes avec les applications. En outre, vous pouvez utiliser le gestionnaire d’instantanés StorSimple pour créer des planifications de sauvegarde et cloner ou restaurer des volumes. 
- **Adaptateur StorSimple pour SharePoint** : un outil qui étend le stockage et la protection des données Microsoft Azure StorSimple de manière transparente aux batteries de serveurs SharePoint, tout en permettant d’afficher et de gérer le stockage StorSimple à partir du portail d’administration de SharePoint.

## Étapes suivantes

Lisez la [Vue d’ensemble des solutions StorSimple série 8000](http://www.microsoft.com/server-cloud/products/storsimple/resources.aspx) et consultez la [présentation de StorSimple](http://www.microsoft.com/server-cloud/products/storsimple/Features.aspx).

En savoir plus sur [les composants et la terminologie de StorSimple](storsimple-components.md).


 

<!---HONumber=August15_HO8-->
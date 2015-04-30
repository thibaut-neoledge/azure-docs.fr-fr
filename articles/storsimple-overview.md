<properties 
   pageTitle="Présentation de StorSimple?" 
   description="Décrit les fonctionnalités, l'architecture et les composants StorSimple." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="AdinaH" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="02/17/2015"
   ms.author="v-sharos@microsoft.com"/>

# Présentation de StorSimple 

Microsoft Azure StorSimple est une solution efficace, rentable et facile à gérer qui élimine la plupart des problèmes et des frais liés à la protection des données et du stockage d'entreprise. Il utilise un appareil propriétaire (l'appareil Microsoft Azure StorSimple) et des outils de gestion intégrés pour fournir une vue claire de tout le stockage d'entreprise, y compris le stockage cloud.

## Pourquoi utiliser StorSimple ?

Microsoft Azure StorSimple offre les avantages suivants :

- **Intégration transparente** : Microsoft Azure StorSimple utilise le protocole Internet Small Computer System Interface (iSCSI) pour lier de manière invisible les installations de stockage de données. Cela garantit que les données stockées dans le cloud, dans un centre de données ou sur des serveurs distants semblent être stockées dans un emplacement unique.
- **Réduction des coûts de stockage** : Microsoft Azure StorSimple alloue suffisamment de stockage local ou de cloud pour répondre aux besoins actuels et étend le stockage cloud uniquement lorsque cela est nécessaire. Il réduit les besoins et les dépenses de stockage en éliminant les redondances de données (déduplication) et en utilisant la compression.
- **Gestion simplifiée du stockage** : Microsoft Azure StorSimple fournit des outils d'administration que vous pouvez utiliser pour configurer et gérer les données stockées sur site, sur un serveur distant et dans le cloud. En outre, vous pouvez gérer la sauvegarde et la restauration des fonctions depuis un composant logiciel enfichable Microsoft Management Console (MMC). StorSimple fournit une interface distincte et facultative que vous pouvez utiliser pour étendre les services de protection des données et de gestion StorSimple au contenu stocké sur des serveurs SharePoint. 
- **Récupération d'urgence et conformité améliorées** : Microsoft Azure StorSimple ne nécessite pas un long temps de récupération. Au contraire, il restaure les données lorsque vous en avez besoin. Cela signifie que les opérations normales peuvent se poursuivre, avec une interruption minimale. En outre, vous pouvez configurer des stratégies pour spécifier les planifications de sauvegarde et la conservation des données.
- **Mobilité des données** : les données téléchargées sur Microsoft Azure Cloud Services sont accessibles à partir d'autres sites à des fins de récupération et de migration. En outre, vous pouvez utiliser StorSimple pour configurer les périphériques virtuels StorSimple sur les machines virtuelles (VM) de Microsoft Azure. Les machines virtuelles peuvent ensuite utiliser les périphériques virtuels pour accéder aux données stockées à des fins de test ou de récupération. 

Le diagramme suivant fournit une vue d'ensemble de la solution Microsoft Azure StorSimple.

![Architecture StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-system-architecture.png)

**Architecture de Microsoft Azure StorSimple**

## Composants de StorSimple

La solution Microsoft Azure StorSimple comprend les composants suivants :

- **Appareil Microsoft Azure StorSimple** : un groupe de stockage hybride local qui contient des disques SSD et des disques durs, ainsi que des contrôleurs redondants et des fonctionnalités de basculement automatique. Les contrôleurs gèrent la hiérarchisation du stockage en plaçant les données actuellement (ou souvent) utilisées sur le stockage local (sur les serveurs du périphérique ou sur site), tout en déplaçant les données moins fréquemment utilisées sur le cloud.
- **Appareil virtuel StorSimple** : également appelé appliance virtuelle StorSimple, il s'agit d'une version logicielle du périphérique StorSimple qui réplique l'architecture et les fonctionnalités de l'appareil de stockage hybride physique. L'appareil virtuel StorSimple s'exécute sur un nœud unique dans une machine virtuelle Azure. L'appareil virtuel est approprié pour des tests et de petits scénarios pilotes. Il est impossible de créer un appareil virtuel StorSimple sur un serveur local ou sur un appareil StorSimple.
- **Windows PowerShell pour StorSimple** : une interface de ligne de commande que vous pouvez utiliser pour gérer l'appareil StorSimple. Windows PowerShell pour StorSimple dispose de fonctionnalités qui vous permettent d'inscrire votre appareil StorSimple, de configurer l'interface réseau sur votre périphérique, d'installer certains types de mises à jour, de résoudre les problèmes de votre appareil en accédant à la session de support et de modifier l'état de l'appareil. Vous pouvez accéder à Windows PowerShell pour StorSimple en vous connectant à la console série ou à l'aide de l'accès distant Windows PowerShell.
- **Service StorSimple Manager** : une extension du portail de gestion Azure qui vous permet de gérer un appareil physique StorSimple ou un appareil virtuel StorSimple à partir d'une seule interface Web. Vous pouvez utiliser le service StorSimple Manager pour créer et gérer les services, afficher et gérer des appareils, afficher les alertes, gérer des volumes, ainsi qu'afficher et gérer des stratégies de sauvegarde et le catalogue de sauvegarde.
- **Gestionnaire d'instantanés StorSimple** : un composant logiciel enfichable MMC qui utilise des groupes de volumes et Windows Volume Shadow Copy Service pour générer des sauvegardes cohérentes avec les applications. En outre, vous pouvez utiliser le gestionnaire d'instantanés StorSimple pour créer des planifications de sauvegarde et cloner ou restaurer des volumes. 
- **Adaptateur StorSimple pour SharePoint** : un outil qui étend le stockage et la protection des données Microsoft Azure StorSimple de manière transparente aux batteries de serveurs SharePoint, tout en permettant d'afficher et de gérer le stockage StorSimple à partir du portail d'administration de SharePoint.

## Étapes suivantes

En savoir plus sur les [composants StorSimple](https://technet.microsoft.com/library/cc754482.aspx) et consulter les[notes de publication de StorSimple](https://msdn.microsoft.com/library/azure/dn772367.aspx)




<!--HONumber=52-->
<properties 
   pageTitle="Présentation des composants StorSimple?" 
   description="Décrit les technologies d'appareil, de services et de gestion de StorSimple." 
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


# Présentation des composants StorSimple 

Bienvenue dans Microsoft Azure StorSimple, une solution de stockage intégrée qui gère les tâches de stockage entre les périphériques locaux et le stockage cloud Microsoft Azure.  StorSimple est conçu pour réduire les coûts de stockage, simplifier la gestion du stockage, améliorer l'efficacité et les capacités de reprise après sinistre et fournir une mobilité des données.

Les sections suivantes décrivent les composants de Microsoft Azure StorSimple et expliquent comment la solution organise les données, alloue le stockage et facilite la gestion du stockage et la protection des données. 

> [AZURE.NOTE] Les informations de déploiement StorSimple publiées sur le site Web Microsoft Azure s'appliquent aux appareils StorSimple de la série 8000 uniquement. Pour plus d'informations sur les appareils de la série 7000, consultez : [Aide de StorSimple](http://onlinehelp.storsimple.com/).

## Appareil StorSimple

L'appareil Microsoft Azure StorSimple est un groupe de stockage hybride local qui fournit un stockage principal et un accès iSCSI aux données stockées ailleurs. Il gère la communication avec le stockage cloud et contribue à garantir la sécurité et la confidentialité de toutes les données stockées sur la solution Microsoft Azure StorSimple.

L'appareil StorSimple inclut les disques SSD et les disques durs, ainsi que la prise en charge du basculement automatique et du clustering. Il contient un processeur partagé, un stockage partagé et deux contrôleurs en miroir. Chaque contrôleur offre les avantages suivants :

- Connexion à un ordinateur hôte
- Jusqu'à six ports réseau pour se connecter au réseau local (LAN)
- Surveillance du matériel
- Mémoire vive non volatile (NVRAM), qui conserve les informations même si l'alimentation est interrompue
- Mise à jour adaptée aux clusters pour gérer les mises à jour logicielles sur les serveurs d'un cluster de basculement, afin que les mises à jour aient une incidence minime ou inexistante sur la disponibilité du service
- Service de cluster, fonctionnant comme un cluster principal, pour fournir une haute disponibilité et réduire les effets négatifs pouvant se produire si un lecteur de disque dur ou un disque SSD est défaillant ou passe hors connexion

Un seul contrôleur est actif à un instant donné. Si le contrôleur actif est défaillant, le deuxième contrôleur devient automatiquement actif. 

Pour plus d'informations, consultez l'article [Appareils StorSimple](https://msdn.microsoft.com/library/azure/dn772363.aspx).

## Appareil virtuel StorSimple

Vous pouvez utiliser StorSimple pour créer un appareil virtuel qui réplique l'architecture et les fonctionnalités de l'appareil physique de stockage hybride. 

L'appareil virtuel StorSimple (ou appliance virtuelle Storsimple) s'exécute sur un nœud unique dans une machine virtuelle Azure. (Un appareil virtuel peut uniquement être créé sur une machine virtuelle Azure. Il est impossible d'en créer un sur un serveur local ou sur un appareil StorSimple.) Un appareil virtuel StorSimple se distingue d'un appareil physique StorSimple pour les raisons suivantes : 

- L'appareil virtuel n'a qu'une interface, tandis que l'unité physique comporte six interfaces réseau. 
- Vous inscrivez l'appareil virtuel lors de la configuration de l'appareil, et non comme une tâche distincte.
- Vous ne pouvez pas régénérer la clé de chiffrement des données de service à partir d'un appareil virtuel. Au cours de la substitution de la clé, vous régénérez la clé sur l'appareil physique, puis mettez à jour l'appareil virtuel avec la nouvelle clé.
- Si vous devez appliquer des mises à jour à l'appareil virtuel, vous serez confronté à des temps d'arrêt. Cela ne se produit pas avec un appareil physique StorSimple.

Nous vous recommandons d'utiliser l'appareil virtuel StorSimple pour les scénarios de récupération d'urgence dans lesquels un appareil physique n'est pas disponible, comme les petits déploiements pilotes ou de test.

Pour plus d'informations, consultez l'article [Appareil virtuel StorSimple](https://msdn.microsoft.com/library/azure/dn772390.aspx).


## Technologies de gestion de stockage
 
Outre l'appareil virtuel et un appareil StorSimple dédié, Microsoft Azure StorSimple utilise les technologies logicielles suivantes pour fournir un accès rapide aux données et réduire la consommation de stockage :

- Hiérarchisation automatique du stockage 
- Allocation dynamique 
- Déduplication et compression 

### Hiérarchisation automatique du stockage

Microsoft Azure StorSimple organise automatiquement les données en niveaux logiques, en fonction de l'utilisation actuelle, de l'âge et des relations à d'autres données. Les données les plus actives sont stockées localement, tandis que les données les moins actives ou inactives sont automatiquement transférées vers le cloud. Le diagramme suivant illustre cette méthode de stockage.
 
![Niveaux de stockage StorSimple](./media/storsimple-components/hcs-data-services-storsimple-components-tiers.png)

**Niveaux de stockage StorSimple**

Pour permettre un accès rapide, StorSimple stocke les données très actives sur des disques SSD de l'unité StorSimple. Il stocke les données occasionnellement utilisées sur des disques durs de l'appareil ou sur des serveurs du centre de données. Il déplace les données inactives, les données de sauvegarde et les données conservées à des fins d'archivage ou de conformité vers le cloud. 

StorSimple ajuste et réorganise les données et modifie les affectations de stockage conformément aux modifications du schéma d'utilisation. Par exemple, certaines informations peuvent devenir moins actives au fil du temps. À mesure que leur utilisation diminue, elles sont transférées d'un disque SSD vers le disque dur, puis vers le cloud. Si ces mêmes données redeviennent actives, elles sont transférées à nouveau sur le périphérique de stockage.

### Allocation dynamique

L'allocation dynamique est une technologie de virtualisation dans laquelle le stockage disponible semble dépasser les ressources physiques. Plutôt que de réserver un espace de stockage suffisant à l'avance, StorSimple utilise l'allocation dynamique pour allouer juste assez d'espace pour répondre aux besoins actuels. La nature évolutive du stockage cloud simplifie cette approche, car StorSimple peut augmenter ou diminuer le stockage cloud pour répondre aux demandes changeantes. 

### Déduplication et compression

Microsoft Azure StorSimple utilise la compression des données et la déduplication pour réduire encore davantage les besoins de stockage.

La déduplication réduit la quantité globale de données stockées en éliminant les redondances dans le jeu de données stocké. Lorsque des données sont modifiées, StorSimple ignore les informations non modifiées et enregistre uniquement les modifications. En outre, StorSimple réduit la quantité de données stockées en identifiant et en supprimant les informations inutiles. 

## Windows PowerShell pour StorSimple

Windows PowerShell pour StorSimple fournit une interface de ligne de commande que vous pouvez utiliser pour créer et gérer le service Microsoft Azure StorSimple, ainsi que pour configurer et surveiller les appareils StorSimple. C'est une interface de ligne de commande Windows PowerShell, qui inclut des cmdlets dédiées à la gestion de votre appareil StorSimple. Windows PowerShell pour StorSimple dispose de fonctionnalités qui vous permettent d'effectuer les opérations suivantes :

- Enregistrer un appareil.
- Configurer l'interface réseau sur un périphérique.
- Installer certains types de mises à jour.
- Dépanner votre appareil en accédant à la session de support.
- Modifier l'état de l'appareil.

Vous pouvez accéder à Windows PowerShell pour StorSimple à partir d'une console série (sur un ordinateur hôte connecté directement à l'appareil) ou à distance, à l'aide de l'accès distant Windows PowerShell. Notez que certains travaux Windows PowerShell pour StorSimple, comme l'inscription initiale de l'appareil, peuvent uniquement être effectués sur la console série. 

Pour plus d'informations, consultez [Windows PowerShell pour StorSimple](https://msdn.microsoft.com/library/azure/dn772425.aspx).

## Service StorSimple Manager

Microsoft Azure StorSimple fournit une interface utilisateur Web (le service StorSimple Manager) qui vous permet de gérer les centres de données et le stockage cloud de manière centralisée. Vous pouvez utiliser le service StorSimple Manager pour effectuer les tâches suivantes :

- Configurer les paramètres système pour les appareils StorSimple.
- Configurer et gérer les paramètres de sécurité pour les appareils StorSimple.
- Configurer les propriétés et les informations d'identification cloud.
- Configurer et gérer des volumes sur un serveur.
- Configurer des groupes de volumes.
- Sauvegarder et restaurer des données.
- Analyser les performances.
- Passer en revue les paramètres système et identifier les problèmes possibles.

Vous pouvez utiliser le service StorSimple Manager pour effectuer toutes les tâches d'administration, à l'exception de celles nécessitant un temps d'arrêt du système, comme la configuration initiale et l'installation de mises à jour.

Pour plus d'informations, consultez l'article [Service StorSimple Manager](https://msdn.microsoft.com/library/azure/dn772396.aspx).

## Gestionnaire d'instantanés StorSimple

Le gestionnaire d'instantanés StorSimple est un composant logiciel enfichable MMC (Microsoft Management Console) qui vous permet de créer des copies de sauvegarde à un moment donné, cohérentes, des données locales et du cloud. Le composant logiciel enfichable s'exécute sur un hôte Windows Server. Vous pouvez utiliser le gestionnaire d'instantanés StorSimple pour :

- Configurer, sauvegarder et supprimer des volumes.
- Configurer des groupes de volumes pour garantir que les données sauvegardées sont cohérentes pour l'application.
- Gérer les stratégies de sauvegarde afin que les données soient sauvegardées selon un calendrier prédéterminé et stockées dans un emplacement désigné (localement ou dans le cloud).
- Restaurer des volumes et des fichiers individuels.

Les sauvegardes sont capturées sous forme d'instantanés, qui enregistrent uniquement les modifications apportées depuis la dernière capture instantanée et nécessitent beaucoup moins d'espace de stockage que les sauvegardes complètes. Vous pouvez créer des planifications de sauvegarde ou effectuer des sauvegardes immédiates en fonction de vos besoins. En outre, vous pouvez utiliser le gestionnaire d'instantanés StorSimple pour établir des stratégies de rétention qui contrôlent le nombre d'instantanés enregistrés. Si vous avez besoin de restaurer les données à partir d'une sauvegarde par la suite, le gestionnaire d'instantanés StorSimple vous permet de choisir dans le catalogue d'instantanés locaux ou de cloud. 

Si un incident se produit ou si vous devez restaurer des données pour une autre raison, le gestionnaire d'instantanés StorSimple les restaure de façon incrémentielle, selon les besoins. La restauration des données ne nécessite pas d'arrêter l'ensemble du système lorsque vous restaurez un fichier, remplacez un équipement ou déplacez des opérations vers un autre site.

Pour plus d'informations, consultez l'article [Gestionnaire d'instantanés StorSimple](https://msdn.microsoft.com/library/azure/dn772365.aspx).

## Adaptateur StorSimple pour SharePoint

Microsoft Azure StorSimple inclut l'adaptateur StorSimple pour SharePoint, un composant facultatif qui étend de manière transparente les fonctionnalités de protection des données et de stockage StorSimple aux batteries de serveurs SharePoint. L'adaptateur fonctionne avec un fournisseur de stockage d'objets Blob distants (RBS) et la fonctionnalité RBS de SQL Server, ce qui vous permet de déplacer des objets blob vers un serveur sauvegardé par le système Microsoft Azure StorSimple. Microsoft Azure StorSimple stocke ensuite les données d'objets blob localement ou dans le cloud, selon leur utilisation.

L'adaptateur StorSimple pour SharePoint est géré depuis le portail d'Administration centrale de SharePoint. Par conséquent, la gestion de SharePoint reste centralisée et tout le stockage apparaît comme étant dans la batterie de serveurs SharePoint.

Pour plus d'informations, consultez [Adaptateur StorSimple pour SharePoint](https://msdn.microsoft.com/library/azure/dn757737.aspx). 


## Étapes suivantes

Examinez les [Notes de publication de StorSimple](https://msdn.microsoft.com/library/azure/dn772367.aspx)





<!--HONumber=52-->
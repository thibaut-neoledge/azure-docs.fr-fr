<properties 
   pageTitle="Présentation des composants StorSimple | Microsoft Azure" 
   description="Décrit l’appareil, les services et les technologies de gestion StorSimple." 
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
   ms.date="08/11/2015"
   ms.author="v-sharos"/>


# Présentation des composants StorSimple 

Bienvenue dans Microsoft Azure StorSimple, solution de stockage intégrée qui gère les tâches de stockage entre les appareils locaux et le stockage cloud Microsoft Azure. StorSimple est conçu pour réduire les coûts de stockage, simplifier la gestion du stockage, améliorer l’efficacité et les capacités de reprise après sinistre et fournir une mobilité des données.

Les sections suivantes décrivent les composants de Microsoft Azure StorSimple et expliquent comment la solution organise les données, alloue le stockage et facilite la gestion du stockage et la protection des données. La dernière section fournit les définitions de quelques termes importants liés aux composants StorSimple et à leur gestion.

> [AZURE.NOTE]Les informations de déploiement StorSimple publiées sur le site web Microsoft Azure s’appliquent aux appareils StorSimple de la série 8000 uniquement. Pour plus d’informations sur les appareils de la série 7000, consultez : [Aide de StorSimple](http://onlinehelp.storsimple.com/).

## Appareil StorSimple

L’appareil Microsoft Azure StorSimple est un groupe de stockage hybride local qui fournit un stockage principal et un accès iSCSI aux données qui y sont stockées. Il gère la communication avec le stockage cloud et contribue à garantir la sécurité et la confidentialité de toutes les données stockées sur la solution Microsoft Azure StorSimple.

L’appareil StorSimple inclut les disques SSD et les disques durs, ainsi que la prise en charge du basculement automatique et du clustering. Il contient un processeur partagé, un stockage partagé et deux contrôleurs en miroir. Chaque contrôleur offre les avantages suivants :

- Connexion à un ordinateur hôte
- Jusqu’à six ports réseau pour se connecter au réseau local (LAN)
- Surveillance du matériel
- Mémoire vive non volatile (NVRAM), qui conserve les informations même si l’alimentation est interrompue
- Mise à jour adaptée aux clusters pour gérer les mises à jour logicielles sur les serveurs d’un cluster de basculement, afin que les mises à jour aient une incidence minime ou inexistante sur la disponibilité du service
- Service de cluster, fonctionnant comme un cluster principal, pour fournir une haute disponibilité et réduire les effets négatifs pouvant se produire si un lecteur de disque dur ou un disque SSD est défaillant ou passe hors connexion

Un seul contrôleur est actif à un instant donné. Si le contrôleur actif est défaillant, le deuxième contrôleur devient automatiquement actif.

Pour plus d’informations, consultez [Composants matériels de StorSimple et leur état](storsimple-monitor-hardware-status.md).

## Appareil virtuel StorSimple

Vous pouvez utiliser StorSimple pour créer un appareil virtuel qui réplique l’architecture et les fonctionnalités de l’appareil physique de stockage hybride.

L’appareil virtuel StorSimple (ou appliance virtuelle Storsimple) s’exécute sur un nœud unique dans une machine virtuelle Azure. (Un appareil virtuel peut uniquement être créé sur une machine virtuelle Azure. Il est impossible d’en créer un sur un serveur local ou sur un appareil StorSimple.) Un appareil virtuel StorSimple se distingue d’un appareil physique StorSimple pour les raisons suivantes :

- L’appareil virtuel n’a qu’une interface, tandis que l’appareil physique comporte six interfaces réseau. 
- Vous inscrivez l’appareil virtuel lors de la configuration de l’appareil, et non comme une tâche distincte.
- Vous ne pouvez pas régénérer la clé de chiffrement des données de service à partir d’un appareil virtuel. Au cours de la substitution de la clé, vous régénérez la clé sur l’appareil physique, puis mettez à jour l’appareil virtuel avec la nouvelle clé.
- Si vous devez appliquer des mises à jour à l’appareil virtuel, vous serez confronté à des temps d’arrêt. Cela ne se produit pas avec un appareil physique StorSimple.

Nous vous recommandons d’utiliser l’appareil virtuel StorSimple pour les scénarios de récupération d’urgence dans lesquels un appareil physique n’est pas disponible, comme les scénarios de développement de cloud et de test.

Pour plus d’informations, consultez [Appareil virtuel StorSimple](storsimple-virtual-device.md).


## Technologies de gestion de stockage
 
Outre l’appareil virtuel et un appareil StorSimple dédié, Microsoft Azure StorSimple utilise les technologies logicielles suivantes pour fournir un accès rapide aux données et réduire la consommation de stockage :

- Hiérarchisation automatique du stockage 
- Allocation dynamique 
- Déduplication et compression 

### Hiérarchisation automatique du stockage

Microsoft Azure StorSimple organise automatiquement les données en niveaux logiques, en fonction de l’utilisation actuelle, de l’âge et des relations à d’autres données. Les données les plus actives sont stockées localement, tandis que les données les moins actives ou inactives sont automatiquement transférées vers le cloud. La figure 1 illustre cette approche de stockage.
 
![Niveaux de stockage StorSimple](./media/storsimple-components/hcs-data-services-storsimple-components-tiers.png)

**Figure 1: stockage StorSimple**

Pour permettre un accès rapide, StorSimple stocke les données très actives sur des disques SSD de l’appareil StorSimple. Il stocke les données occasionnellement utilisées sur des disques durs de l’appareil ou sur des serveurs du centre de données. Il déplace les données inactives, les données de sauvegarde et les données conservées à des fins d’archivage ou de conformité vers le cloud.

StorSimple ajuste et réorganise les données et modifie les affectations de stockage conformément aux modifications du schéma d’utilisation. Par exemple, certaines informations peuvent devenir moins actives au fil du temps. À mesure que leur utilisation diminue, elles sont transférées d’un disque SSD vers le disque dur, puis vers le cloud. Si ces mêmes données redeviennent actives, elles sont transférées à nouveau sur le dispositif de stockage.

Le processus de hiérarchisation du stockage se déroule comme suit :

1. Un administrateur système configure un compte de stockage cloud Microsoft Azure.
2. L’administrateur utilise la console série et le service StorSimple Manager (exécuté dans le portail de gestion Azure) pour configurer l’appareil et le serveur de fichiers, en créant des volumes et des stratégies de protection des données. Le serveur de fichiers local utilise l’interface iSCSI (Internet Small Computer System Interface) pour accéder à l’appareil StorSimple.
3. À la base, StorSimple stocke les données sur le niveau SSD rapide de l’appareil.
4. Au fur et à mesure que le niveau SSD approche de sa capacité maximale, StorSimple déduplique et compresse les blocs de données les plus anciens et les déplace vers le niveau disque dur (HDD).
5. Au fur et à mesure que le niveau disque dur (HDD) approche de sa capacité maximale, StorSimple chiffre les blocs de données les plus anciens et les envoie de façon sécurisée au compte de stockage Microsoft Azure via HTTPS.
6. Microsoft Azure crée plusieurs réplicas des données dans son centre de données et dans un centre de données distant, ce qui permet de garantir la récupération des données en cas de sinistre. 
7. Quand le serveur de fichiers demande des données stockées dans le cloud, StorSimple les retourne de façon transparente et stocke une copie au niveau SSD de l’appareil StorSimple.

### Allocation dynamique

L’allocation dynamique est une technologie de virtualisation dans laquelle le stockage disponible semble dépasser les ressources physiques. Plutôt que de réserver un espace de stockage suffisant à l’avance, StorSimple utilise l’allocation dynamique pour allouer juste assez d’espace pour répondre aux besoins actuels. La nature évolutive du stockage cloud simplifie cette approche, car StorSimple peut augmenter ou diminuer le stockage cloud pour répondre aux demandes changeantes.

### Déduplication et compression

Microsoft Azure StorSimple utilise la compression des données et la déduplication pour réduire encore davantage les besoins de stockage.

La déduplication réduit la quantité globale de données stockées en éliminant les redondances dans le jeu de données stocké. Lorsque des données sont modifiées, StorSimple ignore les informations non modifiées et enregistre uniquement les modifications. En outre, StorSimple réduit la quantité de données stockées en identifiant et en supprimant les informations inutiles.

## Windows PowerShell pour StorSimple

Windows PowerShell pour StorSimple fournit une interface de ligne de commande que vous pouvez utiliser pour créer et gérer le service Microsoft Azure StorSimple, ainsi que pour configurer et surveiller les appareils StorSimple. C’est une interface de ligne de commande Windows PowerShell, qui inclut des applets de commande dédiées à la gestion de votre appareil StorSimple. Windows PowerShell pour StorSimple dispose de fonctionnalités qui vous permettent d’effectuer les opérations suivantes :

- Enregistrer un appareil.
- Configurer l’interface réseau sur un appareil.
- Installer certains types de mises à jour.
- Dépanner votre appareil en accédant à la session de support.
- Modifier l’état de l’appareil.

Vous pouvez accéder à Windows PowerShell pour StorSimple à partir d’une console série (sur un ordinateur hôte connecté directement à l’appareil) ou à distance, à l’aide de l’accès distant Windows PowerShell. Notez que certains travaux Windows PowerShell pour StorSimple, comme l’inscription initiale de l’appareil, peuvent uniquement être effectués sur la console série.

Pour plus d'informations, consultez [Utiliser Windows PowerShell pour StorSimple pour gérer votre appareil](storsimple-windows-powershell-administration.md).

## Applets de commande Azure PowerShell StorSimple

Les applets de commande Azure PowerShell StorSimple sont une collection d'applets de commande Windows PowerShell qui vous permettent d'automatiser les tâches de niveau de service et la migration à partir de la ligne de commande. Pour plus d’informations sur les cmdlets Azure PowerShell pour StorSimple, consultez la page [Documentation de référence des cmdlets](https://msdn.microsoft.com/library/dn920427.aspx).

## Service StorSimple Manager

Microsoft Azure StorSimple fournit une interface utilisateur web (le service StorSimple Manager) qui vous permet de gérer les centres de données et le stockage cloud de manière centralisée. Vous pouvez utiliser le service StorSimple Manager pour effectuer les tâches suivantes :

- Configurer les paramètres système pour les appareils StorSimple.
- Configurer et gérer les paramètres de sécurité pour les appareils StorSimple.
- Configurer les propriétés et les informations d’identification cloud.
- Configurer et gérer des volumes sur un serveur.
- Configurer des groupes de volumes.
- Sauvegarder et restaurer des données.
- Analyser les performances.
- Passer en revue les paramètres système et identifier les problèmes possibles.

Vous pouvez utiliser le service StorSimple Manager pour effectuer toutes les tâches d’administration, à l’exception de celles nécessitant un temps d’arrêt du système, comme la configuration initiale et l’installation de mises à jour.

Pour plus d'informations, consultez [Utilisation du service StorSimple Manager pour gérer votre appareil StorSimple](storsimple-manager-service-administration.md).

## StorSimple Snapshot Manager

StorSimple Snapshot Manager est un composant logiciel enfichable MMC (Microsoft Management Console) qui vous permet de créer des copies de sauvegarde à un moment donné, cohérentes, des données locales et du cloud. Le composant logiciel enfichable s’exécute sur un hôte Windows Server. Vous pouvez utiliser StorSimple Snapshot Manager pour :

- Configurer, sauvegarder et supprimer des volumes.
- Configurer des groupes de volumes pour garantir que les données sauvegardées sont cohérentes pour l’application.
- Gérer les stratégies de sauvegarde afin que les données soient sauvegardées selon un calendrier prédéterminé et stockées dans un emplacement désigné (localement ou dans le cloud).
- Restaurer des volumes et des fichiers individuels.

Les sauvegardes sont capturées sous forme d’instantanés, qui enregistrent uniquement les modifications apportées depuis la dernière capture instantanée et nécessitent beaucoup moins d’espace de stockage que les sauvegardes complètes. Vous pouvez créer des planifications de sauvegarde ou effectuer des sauvegardes immédiates en fonction de vos besoins. En outre, vous pouvez utiliser StorSimple Snapshot Manager pour établir des stratégies de rétention qui contrôlent le nombre d’instantanés enregistrés. Si vous avez besoin de restaurer les données à partir d’une sauvegarde par la suite, StorSimple Snapshot Manager vous permet de choisir dans le catalogue d’instantanés locaux ou de cloud.

Si un incident se produit ou si vous devez restaurer des données pour une autre raison, StorSimple Snapshot Manager les restaure de façon incrémentielle, selon les besoins. La restauration des données ne nécessite pas d’arrêter l’ensemble du système lorsque vous restaurez un fichier, remplacez un équipement ou déplacez des opérations vers un autre site.

Pour plus d'informations, consultez [Qu'est-ce que StorSimple Snapshot Manager ?](storsimple-what-is-snapshot-manager.md).

## StorSimple Adapter for SharePoint

Microsoft Azure StorSimple inclut StorSimple Adapter for SharePoint, un composant facultatif qui étend de manière transparente les fonctionnalités de protection des données et de stockage StorSimple aux batteries de serveurs SharePoint. L’adaptateur fonctionne avec un fournisseur de stockage d’objets Blob distants (RBS) et la fonctionnalité RBS de SQL Server, ce qui vous permet de déplacer des objets blob vers un serveur sauvegardé par le système Microsoft Azure StorSimple. Microsoft Azure StorSimple stocke ensuite les données d’objets blob localement ou dans le cloud, selon leur utilisation.

StorSimple Adapter for SharePoint est géré depuis le portail d’Administration centrale de SharePoint. Par conséquent, la gestion de SharePoint reste centralisée et tout le stockage apparaît comme étant dans la batterie de serveurs SharePoint.

Pour plus d’informations, consultez [Adaptateur StorSimple pour SharePoint](storsimple-adapter-for-sharepoint.md).


## Terminologie StorSimple 

Avant de déployer votre solution Microsoft Azure StorSimple, nous vous recommandons de consulter les termes et définitions ci-après.

### Termes et définitions clés

| Terme (acronyme ou abréviation) | Description |
| ------------------------------ | ---------------- |
| enregistrement de contrôle d’accès (ACR) | Enregistrement associé à un volume sur votre appareil Microsoft Azure StorSimple et qui détermine quels sont les hôtes qui peuvent s’y connecter. Ce choix est basé sur le nom complet iSCSI (IQN) des hôtes (dans l’enregistrement de contrôle d’accès) qui se connectent à votre appareil StorSimple.|
| AES-256 | Algorithme AES (Advanced Encryption Standard) 256 bits de chiffrement des données en provenance et à destination du cloud. |
| taille d’unité d’allocation | Plus petite quantité d’espace disque pouvant être allouée pour la conservation d’un fichier sur les systèmes de fichiers Windows. Si la taille d’un fichier n’est pas un multiple pair de la taille du cluster, l’espace supplémentaire doit être utilisé pour la conservation du fichier (jusqu’au prochain multiple de la taille du cluster), ce qui entraîne une perte d’espace et la fragmentation du disque dur. <br>La taille d'unité d'allocation recommandée pour les volumes Azure StorSimple est de 64 Ko, car elle convient aux algorithmes de déduplication.|
| hiérarchisation du stockage automatisé | Déplacement automatique des données moins actives vers un certain niveau du cloud, puis activation de la gestion de l’ensemble du stockage à partir d’une interface utilisateur centrale.|
| catalogue de sauvegarde | Ensemble de sauvegardes habituellement liées par le type d’application utilisé. Cet ensemble est affiché dans la page Catalogue de sauvegarde de l’interface utilisateur du service StorSimple Manager.|
| fichier de catalogue de sauvegarde | Fichier contenant une liste des instantanés disponibles actuellement stockés dans la base de données de sauvegarde de StorSimple Snapshot Manager. |
| stratégie de sauvegarde | Sélection de volumes, type de sauvegarde et emploi du temps qui vous permettent de créer des sauvegardes selon une planification prédéfinie.|
| objets blob (BLOB) | Ensemble de données binaires stockées sous forme d’une seule entité dans un système de gestion de base de données. Les objets blob sont généralement des images, du contenu audio ou d’autres objets multimédias, bien que parfois le code exécutable binaire soit aussi stocké sous forme d’objet blob.|
| protocole CHAP | Protocole utilisé pour authentifier l’homologue d’une connexion, quand l’homologue partage un mot de passe ou un secret. CHAP peut être unidirectionnel ou mutuel. Dans une authentification CHAP unidirectionnelle, la cible authentifie un initiateur. Dans une authentification CHAP mutuelle, la cible authentifie l’initiateur et inversement. | 
| clone | Copie dupliquée d’un volume. |
|Cloud as a Tier (CaaT) | Stockage cloud intégré sous forme de niveau dans l’architecture de stockage pour que l’ensemble du stockage donne l’impression de faire partie d’un seul et même réseau de stockage en entreprise.|
| fournisseur de services cloud (CSP) | Fournisseur de services de cloud computing.|
| instantané cloud | Copie jusqu’à une date et heure des données de volume stockées dans le cloud. Un instantané cloud est équivalent à un instantané répliqué sur un système de stockage hors site distinct. Les instantanés cloud sont particulièrement utiles dans les scénarios de récupération d’urgence.|
| clé de chiffrement de stockage cloud | Mot de passe ou clé permettant à votre appareil StorSimple d’accéder aux données chiffrées envoyées par votre appareil vers le cloud.|
| mise à jour adaptée aux clusters | Gestion des mises à jour logicielles des serveurs dans un cluster de basculement, qui permet d’atténuer ou de supprimer leur impact sur la disponibilité du service.|
| chemin de données | Ensemble d’unités fonctionnelles qui effectuent des opérations de traitement de données interconnectées.|
| désactiver | Action permanente qui interrompt la connexion entre l’appareil StorSimple et le service cloud associé. Les instantanés cloud de l’appareil sont conservés après ce processus et peuvent être clonés ou utilisés pour la récupération d’urgence.|
| mise en miroir de disques | Réplication de volumes de disques logiques sur des disques durs distincts en temps réel pour assurer une disponibilité continue.|
| mise en miroir de disques dynamiques | Réplication de volumes de disques logiques sur des disques dynamiques.|
| disques dynamiques | Format de volume de disque qui utilise le gestionnaire de disque logique (LDM, Logical Disk Manager) pour stocker et gérer les données sur plusieurs disques physiques. Les disques dynamiques peuvent être étendus pour fournir un espace libre plus important.|
| boîtier EBOD | Boîtier secondaire de votre appareil Microsoft Azure StorSimple contenant des disques durs supplémentaires pour étendre la capacité de stockage.|
| allocation fixe | Allocation conventionnelle en matière de stockage, où l’espace de stockage est alloué en fonction des besoins anticipés (généralement au-delà du besoin actuel). Voir aussi *allocation dynamique*.|
| lecteur de disque dur (HDD) | Lecteur qui utilise des plateaux en rotation pour stocker des données.|
| stockage cloud hybride | Architecture de stockage qui utilise des ressources locales et hors site, y compris le stockage cloud.|
| Internet Small Computer System Interface (iSCSI) | Norme réseau de stockage basée sur le protocole IP. Elle permet de lier les équipements ou les installations de stockage de données.|
| initiateur iSCSI | Composant logiciel qui permet à un ordinateur hôte exécutant Windows de se connecter à un réseau de stockage iSCSI externe.|
| nom complet iSCSI (IQN) | Nom unique qui identifie une cible ou un initiateur iSCSI.|
| cible iSCSI | Composant logiciel qui fournit des sous-systèmes de disque iSCSI centralisés dans les réseaux de zone de stockage.|
| archivage dynamique | Approche du stockage dans laquelle les données d’archivage sont accessibles tout le temps (par exemple, elles ne sont pas stockées sur bande hors site). Microsoft Azure StorSimple utilise l’archivage dynamique.|
| instantané local | Copie jusqu’à une date et heure des données de volume stockées sur l’appareil Microsoft Azure StorSimple.|
| Microsoft Azure StorSimple | Puissante solution composée d’un appareil et d’un logiciel de stockage de centre de données. Elle permet aux organisations informatiques de tirer parti du stockage cloud comme s’il s’agissait d’un stockage de centre de données, et de simplifier la protection et la gestion des données tout en réduisant les coûts. Cette solution regroupe le stockage principal, l’archivage, la sauvegarde et la récupération d’urgence via son intégration transparente au cloud. En associant le stockage SAN et la gestion des données cloud sur une plateforme d’entreprise, les appareils StorSimple fournissent la vitesse, la simplicité et la fiabilité recherchées pour tous les besoins relatifs au stockage.|
| module d’alimentation et de refroidissement (PCM) | Les composants matériels de votre appareil StorSimple incluent l’alimentation électrique et le ventilateur de refroidissement, d’où le nom de module d’alimentation et de refroidissement. Le boîtier principal de l’appareil a deux modules d’alimentation et de refroidissement de 764 W alors que le boîtier EBOD a deux modules d’alimentation et de refroidissement de 580 W.|
| boîtier principal | Boîtier principal de votre appareil StorSimple, qui contient les contrôleurs de la plateforme d’applications.|
| objectif de délai de récupération (RTO) | Délai maximal pour la restauration complète d’un processus ou système métier après une défaillance majeure.| 
|Serial Attached SCSI (SAS) | Type de lecteur de disque dur (HDD).|
| clé de chiffrement de données du service | Clé disponible pour tout nouvel appareil StorSimple qui s’inscrit auprès du service StorSimple Manager. Les données de configuration transférées entre le service StorSimple Manager et l’appareil sont chiffrées à l’aide d’une clé publique. Ensuite, elles ne peuvent être déchiffrées que sur l’appareil à l’aide d’une clé privée. La clé de chiffrement de données du service permet au service d’obtenir cette clé privée pour les opérations de déchiffrement.|
| clé d’inscription du service | Clé qui permet d’inscrire l’appareil StorSimple auprès du service StorSimple Manager. Ainsi, l’appareil s’affiche dans le portail de gestion où il peut être géré de manière plus approfondie.|
| Small Computer System Interface (SCSI) | Ensemble de normes permettant de connecter physiquement des ordinateurs et de transférer des données entre ces derniers.|
| Solid State Drive (SSD) | Disque qui ne contient aucune pièce mobile, par exemple un disque mémoire flash.|
| compte de stockage | Ensemble d’informations d’identification d’accès liées à votre compte de stockage pour un fournisseur de services cloud donné.| 
| StorSimple Adapter for SharePoint| Composant Microsoft Azure StorSimple qui étend de manière transparente la protection des données et le stockage StorSimple à des batteries de serveurs SharePoint.|
| service StorSimple Manager | Extension du portail de gestion Azure, qui vous permet de gérer vos appareils Azure StorSimple locaux et virtuels.|
| StorSimple Snapshot Manager | Composant logiciel enfichable Microsoft Management Console (MMC) pour la gestion des opérations de sauvegarde et de restauration dans Microsoft Azure StorSimple.|
| exécuter une sauvegarde | Fonctionnalité qui permet à l’utilisateur d’exécuter une sauvegarde interactive d’un volume. Il s’agit d’une autre façon d’effectuer une sauvegarde manuelle d’un volume par opposition à une sauvegarde automatique via une stratégie définie.|
| allocation dynamique | Méthode qui permet d’optimiser l’efficacité avec laquelle l’espace de stockage disponible est utilisé dans les systèmes de stockage. Dans une allocation dynamique, le stockage est réparti entre plusieurs utilisateurs en fonction de l’espace minimal dont chaque utilisateur a besoin à un moment donné. Voir aussi *allocation fixe*.|
| volume | Zones de stockage logiques présentées sous forme de disques. Les volumes StorSimple correspondent aux volumes montés par l’hôte, notamment ceux détectés via l’utilisation d’iSCSI et d’un appareil StorSimple.|
 | conteneur de volumes | Groupe de volumes et paramètres qui s’y appliquent. Tous les volumes de votre appareil StorSimple sont regroupés dans des conteneurs de volumes. Les paramètres de conteneur de volumes incluent les comptes de stockage, les paramètres de chiffrement des données envoyées vers le cloud avec la clé de chiffrement associée, ainsi que la bande passante consommée pour les opérations impliquant le cloud.|
| groupe de volumes | Dans StorSimple Snapshot Manager, un groupe de volumes est un ensemble de volumes configurés pour faciliter le traitement de la sauvegarde.|
| service VSS| Service du système d’exploitation Windows Server qui facilite la cohérence des applications en communiquant avec les applications compatibles VSS pour coordonner la création d’instantanés incrémentiels. Le service VSS vérifie que les applications sont temporairement inactives quand les instantanés sont créés.|
| Windows PowerShell pour StorSimple | Interface de ligne de commande basée sur Windows PowerShell. Elle permet de faire fonctionner et de gérer votre appareil StorSimple. Tout en conservant certaines des fonctionnalités de base de Windows PowerShell, cette interface offre des applets de commande dédiées supplémentaires pour gérer des appareils StorSimple.|


## Étapes suivantes

En savoir plus sur la [sécurité StorSimple](storsimple-security.md).




 

<!---HONumber=August15_HO7-->
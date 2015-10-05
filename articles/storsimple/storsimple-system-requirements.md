<properties 
   pageTitle="Configuration requise du système StorSimple | Microsoft Azure" 
   description="Décrit la configuration système requise et les meilleures pratiques pour les logiciels, la haute disponibilité et la mise en réseau d’une solution Azure StorSimple." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="alkohli" 
   manager="carolz" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="09/21/2015"
   ms.author="alkohli"/>

# Configuration requise logicielle, de haute disponibilité et de réseau StorSimple

## Vue d’ensemble

Bienvenue dans Microsoft Azure StorSimple. Cet article décrit les configurations système requises et les meilleures pratiques pour votre appareil StorSimple et pour les clients de stockage accédant à l’appareil. Les conditions sont les suivantes :

- **Configuration logicielle requise pour les clients de stockage** : décrit les systèmes d’exploitation pris en charge et les conditions supplémentaires requises pour ces systèmes d’exploitation.
- **Conditions requises de haute disponibilité pour StorSimple** : décrit les exigences de haute disponibilité et les meilleures pratiques pour votre ordinateur hôte et votre appareil StorSimple. 
- **Conditions requises de mise en réseau pour l’appareil StorSimple** : fournit des informations sur les ports qui doivent être ouverts dans votre pare-feu pour autoriser iSCSI, le cloud ou le trafic de gestion.

Nous vous recommandons de lire attentivement les informations suivantes avant de déployer votre système Azure StorSimple, puis d’y revenir si nécessaire pendant le déploiement et les opérations suivantes.

## Configuration logicielle requise pour les clients de stockage 

La configuration logicielle suivante est requise pour les clients de stockage qui accèdent à votre appareil StorSimple.

| Systèmes d’exploitation pris en charge | Version requise | Détails/notes |
| --------------------------- | ---------------- | ------------- |
| Windows Server | 2008 R2 SP1, 2012, 2012 R2 |<ul><li>Les volumes iSCSI StorSimple sont pris en charge uniquement sur les types de disque Windows suivants :<ul><li>volume simple et volume en miroir sur disque de base</li><li>volume simple et volume en miroir sur disque dynamique</li></ul>L’utilisation du Gestionnaire d’instantanés StorSimple sur Windows Server est requis pour la sauvegarde/restauration des disques dynamiques en miroir et pour les sauvegardes cohérentes au niveau des applications.</li><li>Le Gestionnaire d’instantanés StorSimple est pris en charge uniquement sur Windows Server 2008 R2 SP1 (64 bits), Windows 2012 R2 et Windows Server 2012. <ul><li>Si vous utilisez Windows Server 2012, vous devez installer .NET 3.5 / 4.5 avant d’installer le Gestionnaire d’instantanés StorSimple.</li><li>Si vous utilisez Windows Server 2008 R2 SP1, vous devez installer Windows Management Framework 3.0 avant d’installer le Gestionnaire d’instantanés StorSimple.</li></ul></li><li>L’allocation dynamique de Windows Server 2012 et les fonctionnalités ODX sont prises en charge si vous utilisez un volume iSCSI StorSimple.<ul><li>StorSimple peut créer uniquement les volumes alloués dynamiquement. Il ne peut pas créer des volumes entièrement ou partiellement configurés.</li><li>Le reformatage d’un volume alloué dynamiquement peut prendre beaucoup de temps. Nous vous recommandons de supprimer le volume, puis d’en créer un nouveau au lieu de le reformater.</li><li>Si vous préférez toujours reformater un volume :<ul><li>exécutez la commande suivante avant le reformatage pour éviter les retards de récupération d’espace : <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>une fois le formatage terminé, utilisez la commande suivante pour réactiver une récupération de l’espace :<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>appliquez le correctif Windows Server 2012, comme décrit dans l’article KB2870270, sur votre ordinateur Windows Server.</li></ul></li></ul><li>L’adaptateur StorSimple pour SharePoint est uniquement pris en charge sur SharePoint 2010 et SharePoint 2013. RBS requiert SQL Server Enterprise Edition, version 2008 R2 ou 2012.</li></ul>|
| VMWare | Testé avec VMware vSphere 5.1 en tant que client iSCSI | La fonctionnalité VAAI-block est testée avec VMware vSphere v.5.1 sur les appareils StorSimple. 
| Linux RHEL/CentOS | Prise en charge des clients Linux iSCSI avec initiateur Open-iSCSI versions 5 et 6 | Prise en charge uniquement avec un initiateur Open-iSCSI uniquement. |
| Linux | SUSE Linux 11 | |
 >[AZURE.NOTE]Du fait des tests limités effectués en interne, IBM AIX n’est pas pris en charge actuellement avec StorSimple.

## Configuration requise pour la haute disponibilité de StorSimple

La plateforme matérielle fournie avec la solution StorSimple offre des fonctionnalités de disponibilité et de fiabilité de niveau professionnel, qui constituent la base d’une infrastructure de stockage à tolérance de panne et à haute disponibilité dans votre centre de données. Cependant, vous devez vous conformer aux meilleures pratiques et conditions requises pour garantir la disponibilité de votre solution Azure StorSimple. Avant de déployer Azure StorSimple, examinez attentivement les conditions requises et meilleures pratiques suivantes pour l’appareil StorSimple et les ordinateurs hôtes connectés.

### Configuration requise pour la haute disponibilité et procédures pour votre appareil StorSimple

Lisez attentivement les informations suivantes pour garantir la haute disponibilité de votre appareil StorSimple.

#### Modules d’alimentation et de refroidissement (PCM)

Les appareils StorSimple incluent des modules PCM redondants et échangeables à chaud. Chaque module PCM est doté d’une capacité suffisante pour prendre en charge l’ensemble du châssis. Les deux modules PCM doivent être installés pour assurer un haut niveau de disponibilité.

- Connectez vos modules PCM à différentes sources d’alimentation pour assurer la disponibilité en cas de panne d’une source d’alimentation.

- Si un module PCM tombe en panne, demandez immédiatement son remplacement.

- Ne retirez un module PCM en panne que si vous disposez d’un module de rechange et êtes prêt à l’installer.

- Ne supprimez pas les deux modules PCM en même temps. Le module PCM inclut la batterie. Le retrait des deux modules PCM entraînera un arrêt sans protection par la batterie.

#### Modules de contrôleur

Les appareils StorSimple incluent des modules de contrôleur redondants et échangeables à chaud. Les modules de contrôleur fonctionnent en mode actif/passif. À tout moment, un module de contrôleur est actif et fournit un service, tandis que l’autre est passif. Le module de contrôleur passif est sous tension et devient opérationnel en cas de panne ou de retrait du module de contrôleur actif. Chaque module de contrôleur est doté d’une capacité suffisante pour prendre en charge l’ensemble du châssis. Les deux modules de contrôleur doivent être installés pour assurer un haut niveau de disponibilité.

- Assurez-vous que les deux modules de contrôleur sont installés en permanence.

- En cas de panne d’un module de contrôleur, demandez immédiatement son remplacement.

- Ne retirez un module de contrôleur défaillant que si vous disposez d’un module de rechange et êtes prêt à l’installer. La suppression d’un module pendant des périodes étendues affectera la ventilation et, par conséquent, le refroidissement du système.

- Assurez-vous que les connexions réseau aux deux modules de contrôleur sont identiques, et que les interfaces réseau connectées ont une configuration réseau identique.

- Si un module de contrôleur tombe en panne ou doit être remplacé, assurez-vous que l’autre module de contrôleur est dans un état actif avant de procéder au remplacement du module défaillant.

- Ne supprimez pas les deux modules de contrôleur en même temps.

- Si un contrôleur est en cours de basculement, n’arrêtez pas le module de contrôleur de secours, et ne le retirez pas du châssis.

- Après un basculement de contrôleur, attendez au moins cinq minutes avant de retirer l’un des deux modules de contrôleur.

#### Interfaces réseau

Les modules de contrôleur d’un appareil StorSimple possèdent chacun quatre interfaces réseau 1 Gigabit et deux interfaces réseau 10 Gigabit Ethernet.

- Assurez-vous que les connexions réseau aux deux modules de contrôleur sont identiques, et que les interfaces réseau auxquelles les interfaces des modules de contrôleur sont connectées ont une configuration réseau identique.

- Si possible, déployez les connexions réseau sur différents commutateurs pour assurer la disponibilité du service en cas de panne d’appareil réseau.

- Lorsque vous déconnectez la seule ou la dernière interface compatible iSCSI restante (avec affectation des adresses IP), désactivez tout d’abord l’interface, puis débranchez les câbles. Si l’interface est débranchée en premier, il s’ensuit un basculement du contrôleur actif vers le contrôleur passif. Si les interface correspondantes du contrôleur passif sont également débranchées, les deux contrôleurs redémarrent plusieurs fois avant qu’un seul contrôleur. ne soit choisi.

- Connectez au moins deux interfaces DATA au réseau à partir de chaque module de contrôleur.

- Si vous avez activé les deux interfaces de 10 GbE, déployez-les entre les différents commutateurs.

- Si possible, utilisez MPIO sur les serveurs pour vous assurer qu’ils peuvent tolérer une panne de liaison, de réseau ou d’interface.

Pour plus d'informations sur la mise en réseau de votre appareil pour la haute disponibilité et les performances, consultez [Installation de votre appareil StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) ou [Installation de votre appareil StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### Disques SSD et disques durs

Un appareil StorSimple comprend des disques SSD et des disques durs qui sont protégés au moyen d’espaces en miroir. Un disque d’échange à chaud est aussi prévu pour les disques durs. Grâce aux espaces en miroir, un appareil est capable de tolérer une panne d’un ou plusieurs disques SSD ou disques durs.

- Assurez-vous que tous les disques SSD et disques durs sont en place.

- Si un disque SSD ou un disque dur tombe en panne, demandez immédiatement son remplacement.

- Si un disque SSD ou un disque dur doit être remplacé, veillez à retirer uniquement le disque défectueux.

- Ne retirez pas plusieurs disques SSD ou disques durs du système en même temps.

- Durant le remplacement, surveillez l’**État du matériel** dans la page **Maintenance** des disques SSD et des disques durs. Une coche verte indique que les disques sont en bon état, tandis qu’un point d’exclamation rouge indique un disque SSD ou dur défectueux.

- Si au moins deux disques d’un même type (disque dur ou disque SSD) tombent en panne ou que plusieurs pannes consécutives surviennent en peu de temps, un dysfonctionnement du système et une perte potentielle de données risquent de se produire. Si cela se produit, contactez le support technique. Nous vous recommandons de configurer des instantanés cloud pour tous les volumes à protéger en cas de panne du système.

#### Boîtier EBOD

L’appareil StorSimple (modèle 8600) est équipé d’un boîtier EBOD (Extended Bunch of Disks) en plus du boîtier principal. Un boîtier EBOD se compose de contrôleurs EBOD et de disques durs qui sont protégés à l’aide d’espaces en miroir. Grâce aux espaces en miroir, un appareil est capable de tolérer une panne d’un ou de plusieurs disques durs. Le boîtier EBOD est connecté au boîtier principal avec de câbles SAS redondants.

- Assurez-vous que les deux modules de contrôleur du boîtier EBOD, les câbles SAS et tous les disques durs sont installés en permanence.

- Si un disque dur ou un module de contrôleur de boîtier EBOD tombe en panne, demandez immédiatement son remplacement.

- Si un module de contrôleur de boîtier EBOD tombe en panne, avant de remplacer le module défectueux, assurez-vous que l’autre module de contrôleur est actif.

- Si un disque dur est en panne ou doit être remplacé, veillez à retirer uniquement le disque dur défectueux. Avant de retirer un disque dur, vous devez vous assurer que les disques et la baie sont en bon état.

- Ne retirez pas plusieurs disques durs du système en même temps.

- Lors du remplacement d’un module de contrôleur EBOD ou d’un disque dur, surveillez en permanence l’état du composant dans le service StorSimple Manager. Pour cela, accédez à **Maintenance** - **Matériel**.

- Si un câble SAS est défectueux ou doit être remplacé (cette décision doit être prise après consultation du support technique Microsoft), veillez à retirer uniquement le câble à remplacer.

- Ne retirez pas les deux câbles SAS du système en même temps.

### Spécifications à respecter pour la haute disponibilité de vos ordinateurs hôtes

Lisez attentivement les spécifications et meilleures pratiques recommandées pour assurer la haute disponibilité des hôtes connectés à votre appareil StorSimple.

- Configurez StorSimple avec des [configurations de cluster de serveur de fichiers à deux nœuds][1]. En supprimant les points de défaillance uniques et en assurant la redondance côté hôte, l’ensemble de la solution devient hautement disponible.

- Utilisez des partages disponibles en continu avec Windows Server 2012 (SMB 3.0) pour bénéficier d’un haut niveau de disponibilité lors du basculement des contrôleurs de stockage. Pour plus d’informations sur la configuration de clusters de serveurs de fichiers et de partages disponibles en continu avec Windows Server 2012, consultez cette [vidéo de démonstration](http://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## Configuration réseau requise pour votre appareil StorSimple

Votre appareil StorSimple est un appareil verrouillé. Toutefois, les ports doivent être ouverts dans votre pare-feu pour autoriser le trafic iSCSI, cloud ou de gestion. Le tableau suivant répertorie les ports devant être ouverts dans votre pare-feu. Dans ce tableau, *entrée* ou *entrant* représente la direction à partir de laquelle les demandes client entrantes accèdent à votre appareil. *Sortant* ou *Sortie* représente la direction vers laquelle votre appareil StorSimple envoie des données de façon externe, au delà du déploiement : par exemple, sortant vers Internet.

| Numéro de port <sup>1,2</sup> | Entrant ou sortant | Étendue de ports | Requis | Remarques |
|------------------------|-----------|------------|----------|-------| 
|TCP 80 (HTTP)<sup>3</sup>| Sortie | WAN | Non |<ul><li>Le port de sortie est utilisé pour accéder à Internet pour récupérer les mises à jour.</li><li>L’utilisateur peut configurer le proxy web sortant.</li><li>Pour autoriser les mises à jour du système, ce port doit également être ouvert pour les adresses IP fixes du contrôleur.</li></ul> |
|TCP 443 (HTTPS)<sup>3</sup>| Sortie | WAN | Oui |<ul><li>Le port de sortie est utilisé pour accéder aux données dans le cloud.</li><li>L’utilisateur peut configurer le proxy web sortant.</li><li>Pour autoriser les mises à jour du système, ce port doit également être ouvert pour les adresses IP fixes du contrôleur.</li></ul>|
|UDP 53 (DNS) | Sortie | WAN | Dans certains cas, consultez les notes. |Ce port est requis seulement si vous utilisez un serveur DNS Internet. |
| UDP 123 (NTP) | Sortie | WAN | Dans certains cas, consultez les notes. |Ce port est requis seulement si vous utilisez un serveur NTP Internet. |
| TCP 9354 | Sortie | WAN | Dans certains cas, consultez les notes. |Le port de sortie est utilisé par le service StorSimple Manager pour communiquer avec l’appareil. Ce port est requis si votre réseau actuel ne prend pas en charge HTTP 1.1 pour se connecter à Internet, par exemple lors de l’utilisation d’un serveur proxy HTTP 1.0. <br> Si vous vous connectez via un serveur proxy, consultez [les spécifications de Service Bus](https://msdn.microsoft.com/library/azure/ee706729.aspx) pour plus d’informations. |
| 3260 (iSCSI) | Dans | LAN | Non | Ce port est utilisé pour accéder aux données via iSCSI.|
| 5985 | Dans | LAN | Non | Le port entrant est utilisé par le gestionnaire d’instantanés StorSimple pour communiquer avec le périphérique StorSimple.<br>Ce port est également utilisé lorsque vous vous connectez à distance à Windows PowerShell pour StorSimple via HTTP. |
| 5986 | Dans | LAN | Non | Ce port est utilisé lorsque vous vous connectez à distance à Windows PowerShell pour StorSimple via HTTPS. |

<sup>1</sup> Aucun port entrant ne doit être ouvert sur l’Internet public.

<sup>2</sup> Si plusieurs ports comportent une configuration de passerelle, l’ordre du trafic routé sortant est déterminé par l’ordre de routage des ports décrit ci-dessous.

<sup>3</sup> Les adresses IP fixes du contrôleur sur votre appareil StorSimple doivent être routables et pouvoir se connecter à Internet. Les adresses IP fixes sont utilisées pour traiter les mises à jour apportées à l’appareil. Si les contrôleurs ne peuvent pas se connecter à Internet via les adresses IP fixes, vous ne pouvez pas mettre à jour votre appareil StorSimple.

### Routage de port

Le routage de port est différent selon la version logicielle s’exécutant sur votre appareil StorSimple.

- Si l’appareil exécute une version logicielle antérieure à Update 1, telles que la version GA, 0.1, 0.2 ou 0.3, le routage de port est déterminé comme suit :

     Dernière configuration interface réseau 10 GbE > Autre interface réseau 10 GbE > Dernière configuration interface réseau 1 Gigabit Ethernet > Autre interface réseau 1 Gigabit Ethernet

- Si l’appareil exécute Update 1, le routage de port est déterminé comme suit :

     DATA 0 > Dernière configuration interface réseau 10 GbE > Autre interface réseau 10 GbE > Dernière configuration interface réseau 1 Gigabit Ethernet > Autre interface réseau 1 Gigabit Ethernet

Dans Update 1, le métrique de routage de DATA 0 est effectué au plus bas ; par conséquent, tout le trafic cloud est acheminé via DATA 0. Prenez-en note au cas où il existerait plusieurs interfaces réseau compatibles cloud sur votre appareil StorSimple.

### Meilleures pratiques de mise en réseau

Outre les exigences de mise en réseau ci-dessus, pour obtenir des performances optimales pour votre solution StorSimple, veuillez respecter les meilleures pratiques suivantes :

- Assurez-vous que votre appareil StorSimple a une bande passante dédiée de 40 Mbits/s (ou plus) disponible à tout moment. La bande passante ne doit pas être partagée avec d’autres applications.

- Vérifiez que la connectivité réseau à Internet est disponible en permanence. Les connexions Internet sporadiques ou non fiables aux appareils, y compris aucune connectivité Internet quelle qu’elle soit, se traduisent par l’absence de prise en charge de la configuration.

- Isolez le trafic iSCSI et le trafic cloud en dédiant les interfaces réseau de votre appareil pour l’accès iSCSI et cloud. Pour plus d’informations, consultez la section relative à la [modification des interfaces réseau](storsimple-modify-device-config.md#modify-network-interfaces) sur votre périphérique StorSimple.

## Étapes suivantes

- [En savoir plus sur les limites du système StorSimple](storsimple-limits.md).
- [Découvrez comment déployer votre solution StorSimple](storsimple-deployment-walkthrough.md).
 
<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx

<!---HONumber=Sept15_HO4-->
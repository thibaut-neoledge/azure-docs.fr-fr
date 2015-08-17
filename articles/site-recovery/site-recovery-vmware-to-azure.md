<properties
	pageTitle="Configurer la protection entre des machines virtuelles ou des serveurs physiques VMWare locaux et Azure" 
	description="Décrit Azure Site Recovery pour coordonner la réplication, le basculement et la récupération des serveurs physiques Windows/Linux ou ordinateurs virtuels VMware locaux vers Azure." 
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/30/2015"
	ms.author="raynew"/>

# Configurer la protection entre des machines virtuelles ou des serveurs physiques VMWare locaux et Azure

Cet article explique comment déployer la récupération de sites pour :

- **protéger les machines virtuelles VMware**— coordonner la réplication, le basculement et la récupération des machines virtuelles VMware locales vers Azure
- **protéger les serveurs physiques**— coordonner la réplication, le basculement et la récupération des serveurs physiques Windows et Linux vers Azure à l’aide du service Azure Site Recovery.

Cet article fournit une vue d'ensemble, décrit les conditions préalables au déploiement et communique les instructions d'installation. À la fin de l'article, vos machines virtuelles ou serveurs physiques VMware seront répliqués vers Azure. Si vous rencontrez des problèmes, posez vos questions sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Qu’est-ce que le service Azure Site Recovery ?

Le service Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité des activités et de récupération d'urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles et de serveurs physiques. Les machines peuvent être répliquées vers Azure ou vers un centre de données local secondaire. Découvrez plus d’informations sur [Azure Site Recovery](site-recovery-overview.md).

## Comment protège-t-il les ressources locales ?

Site Recovery permet de protéger vos ressources locales en mettant en œuvre et en simplifiant la réplication, le basculement et la restauration dans de nombreux [scénarios de déploiement](site-recovery-overview.md). Si vous souhaitez protéger vos machines virtuelles ou serveurs physiques Windows ou Linux WMware locaux, voici comment Site Recovery peut vous aider :

- Permet aux utilisateurs VMware de répliquer des machines virtuelles vers Azure.
- Permet la réplication de serveurs physiques locaux vers Azure.
- Fournit un emplacement unique pour configurer et gérer la réplication, le basculement et la récupération.
- Permet un basculement facile à partir de votre infrastructure locale vers Azure et une restauration à partir d’Azure vers votre infrastructure locale.
- Implémente des plans de récupération pour un basculement facile des charges de travail échelonnées sur plusieurs machines.
- Fournit une cohérence multimachine virtuelle pour que les machines virtuelles et les serveurs physiques exécutant des charges de travail spécifiques puissent être récupérés ensemble à un point de données cohérent.
- Prend en charge la réplication des données via Internet, sur une connexion VPN de site à site ou sur Azure ExpressRoute.
- Fournit une détection automatisée des machines virtuelles VMWare.


## De quoi ai-je besoin ?

Ce diagramme illustre les composants de déploiement.

![Nouveau coffre](./media/site-recovery-vmware-to-azure/ASRVMWare_Arch.png)

Voici ce dont vous aurez besoin :

**Composant** | **Déploiement** | **Détails**
--- | --- | ---
**Serveur de configuration** | <p>Vous le déployez comme une machine virtuelle A3 standard Azure dans le même abonnement que Site Recovery.</p> <p>Vous configurez dans le portail Site Recovery</p> | Ce serveur coordonne la communication entre les ordinateurs protégés, le serveur de traitement et les serveurs cibles maîtres dans Azure. Il configure la réplication et coordonne la récupération dans Azure lors du basculement.
**Serveur cible maître** | <p>Vous le déployez comme une machine virtuelle Azure — comme serveur Windows basé sur une image de la galerie Windows Server 2012 R2 (pour protéger les ordinateurs Windows) ou comme serveur Linux basé sur une image de la galerie OpenLogic CentOS 6.6 (pour protéger les ordinateurs Linux).</p> <p>Deux options de dimensionnement sont disponibles : A4 standard et D14 standard.<p><p>Le serveur est connecté au même réseau Azure que le serveur de configuration.</p><p>Vous configurez dans le portail Site Recovery</p> | <p>Il reçoit et stocke les données répliquées à partir de vos ordinateurs protégés à l'aide de disques durs virtuels attachés créés sur le stockage d'objets blob dans votre compte de stockage Azure.</p>   
**Serveur de traitement** | <p>Vous le déployez comme un serveur virtuel ou physique local exécutant Windows Server 2012 R2</p> <p>Nous vous recommandons de le placer sur le même réseau et le même segment de réseau local que les ordinateurs que vous souhaitez protéger, mais vous pouvez l'exécuter sur un autre réseau tant que les ordinateurs protégés disposent d'une visibilité de réseau L3.<p>Vous le configurez et l’enregistrer sur le serveur de configuration dans le portail Site Recovery.</p> | <p>Les ordinateurs protégés envoient des données de réplication au serveur de traitement local. Il possède un cache disque pour mettre en cache les données de réplication qu'il reçoit. Il exécute un certain nombre d’actions sur ces données.</p><p>Il optimise les données en les mettant en cache, en les comprimant et en les chiffrant avant de les envoyer au serveur cible maître.</p><p>Il gère l’installation Push du service de mobilité.</p><p>Il effectue la détection automatique des machines virtuelles VMware.</p>
**Ordinateurs locaux** | Il s’agit soit de machines virtuelles exécutée sur un hyperviseur VMWare, soit de serveurs physiques exécutant Windows ou Linux. | Vous définissez les paramètres de réplication qui s'appliquent aux machines virtuelles et serveurs. Vous pouvez basculer un ordinateur individuel ou plus fréquemment dans le cadre d'un plan de récupération contenant plusieurs machines virtuelles qui basculent ensemble.
**Service de mobilité** | <p>S’installe sur chaque machine virtuelle ou serveur physique que vous souhaitez protéger</p><p>Peut être installé manuellement ou transmis et installé automatiquement par le serveur de traitement lorsque la protection est activée sur le serveur. | Le service de mobilité envoie des données au serveur de traitement dans le cadre de la réplication initiale (resync). Une fois que le serveur a atteint un état protégé (une fois la resynchronisation terminée), le service de mobilité effectue une capture en mémoire des écritures sur le disque et l'envoie au serveur de traitement. L'infrastructure VSS permet d'obtenir la cohérence des applications pour les serveurs Windows.
**Coffre Azure Site Recovery** | Il se configure après avoir souscrit au service Site Recovery. | Vous enregistrez des serveurs dans un coffre Site Recovery. Le coffre coordonne et orchestre la réplication, le basculement et la récupération des données entre votre site local et Azure.
**Mécanisme de réplication** | <p>**Via Internet** : communique et réplique les données à partir de serveurs locaux protégés et d'Azure à l’aide d’un canal de communication SSL/TLS sécurisé via une connexion internet publique. Il s’agit de l’option par défaut. </p><p>**VPN/ExpressRoute** : communique et réplique les données entre les serveurs locaux et Azure via une connexion VPN. Vous allez devoir configurer un VPN de site à site ou une connexion [ExpressRoute](../expressroute-introduction.md) entre le site local et votre réseau Azure.</p><p>Vous allez sélectionner comment vous souhaitez répliquer pendant le déploiement de Site Recovery. Vous ne pouvez pas modifier le mécanisme une fois qu'il est configuré sans compromettre la protection sur les serveurs déjà protégés.| <p>Aucune de ces options ne vous oblige à ouvrir des ports réseau entrants sur les ordinateurs protégés. Toutes les communications réseau sont initiées à partir du site local.</p> 

Vous pouvez en savoir plus sur les composants de Site Recovery, fournisseurs et agents dans [Composants de Site Recovery](site-recovery-components.md).

## Planification de la capacité

Les principaux domaines à prendre en considération sont les suivants :

- **Environnement source** : l’infrastructure VMware, les paramètres de l'ordinateur source et la configuration requise.
- **Serveurs de composants** : le serveur de traitement, le serveur de configuration et le serveur cible maître 

### Considérations relatives à l'environnement source

- **Taille de disque maximale** : la taille maximale actuelle du disque qui peut être attaché à une machine virtuelle est de 1 To. Par conséquent, la taille maximale d'un disque source qui peut être répliqué est également limitée à 1 To.
- **Taille maximale par source** : la taille maximale d'un seul ordinateur source est de 31 To (avec 31 disques) et avec une instance D14 configurée pour le serveur cible maître. 
- **Nombre de sources par serveur cible maître** : plusieurs ordinateurs source peuvent être protégés avec un seul serveur cible maître. Cependant, un seul ordinateur source ne peut pas être protégé sur plusieurs serveurs cibles maîtres, car à mesure que les disques sont répliqués, un disque dur virtuel qui reflète le volume du disque est créé dans le stockage d'objets blob Azure et attaché en tant que disque de données au serveur cible maître.  
- **Taux de modification quotidien maximum par source** : il y a trois facteurs à prendre en considération lorsque vous envisagez le taux de modification par source recommandé. Pour les considérations basées sur la cible, deux IOPS sont requises sur le disque cible pour chaque opération sur la source. Cela est dû au fait qu’il y aura une lecture d’anciennes données et une écriture de nouvelles données sur le disque cible. 
	- **Taux de modification quotidien pris en charge par le serveur de traitement** : un ordinateur source ne peut pas couvrir plusieurs serveurs de traitement. Un seul serveur de traitement peut prendre en charge jusqu'à 1 To de taux de modification quotidien. Par conséquent, 1 To est le taux de modification quotidien maximal des données pris en charge pour un ordinateur source. 
	- **Débit maximal pris en charge par le disque cible** : l’attrition maximale par disque source ne peut pas dépasser 144 Go/jour (avec une taille d'écriture de 8 Ko). Reportez-vous au tableau dans la section cible principale pour le débit et les IOPS de la cible pour différentes tailles d’écriture. Ce nombre doit être divisé en deux, car chaque IOP source génère 2 IOPS sur le disque cible. 
	- **Débit maximal pris en charge par le compte de stockage** : une source ne peut pas couvrir plusieurs comptes de stockage. Étant donné qu'un compte de stockage prend un maximum de 20 000 requêtes par seconde et que chaque IOP source génère 2 IOPS sur le serveur cible maître, nous vous recommandons de maintenir le nombre d'IOPS sur la source à 10 000. 

### Considérations relatives aux serveurs de composants

Le tableau 1 résume les tailles de machine virtuelle pour le serveur de configuration et le serveur cible maître.

**Composant** | **Instances Azure déployées** | **Cœurs** | **Mémoire** | **Disques max** | **Taille du disque**
--- | --- | --- | --- | --- | ---
Serveur de configuration | A3 standard | 4 | 7 Go | 8 | 1 023 Go
Serveur cible maître | A4 standard | 8 | 14 Go | 16 | 1 023 Go
 | D14 standard | 16 | 112 Go | 32 | 1 023 Go

**Tableau 1**

#### Considérations relatives aux serveurs de traitement

Le dimensionnement du serveur de traitement dépend généralement du taux de modification quotidien pour toutes les charges de travail protégées. Les considérations principales sont les suivantes :

-	Vous avez besoin d’une puissance de calcul suffisante pour effectuer des tâches telles que le chiffrement et la compression en ligne.
-	Le serveur de traitement utilise le cache disque. Assurez-vous que le débit de disque et d’espace cache recommandé est disponible pour faciliter les modifications de données stockées en cas de panne ou de goulot d'étranglement du réseau. 
-	Veillez à avoir une bande passante suffisante pour que le serveur de traitement puisse télécharger les données vers le serveur cible maître afin de fournir une protection continue des données. 

Le tableau 2 fournit un résumé des instructions relatives au serveur de traitement.

**Taux de modification des données** | **UC** | **Mémoire** | **Taille du disque cache**| **Débit du disque cache** | **Bande passante en entrée/sortie**
--- | --- | --- | --- | --- | ---
< 300 Go | 4 processeurs virtuels (2 sockets * 2 cœurs à 2,5 GHz) | 4 Go | 600 Go | 7 à 10 Mo par seconde | 30 Mbits/s / 21 Mbits/s
300 à 600 Go | 8 processeurs virtuels (2 sockets * 4 cœurs à 2,5 GHz) | 6 Go | 600 Go | 11 à 15 Mo par seconde | 60 Mbits/s / 42 Mbits/s
600 Go à 1 To | 12 processeurs virtuels (2 sockets * 6 cœurs à 2,5 GHz) | 8 Go | 600 Go | 16 à 20 Mo par seconde | 100 Mbits/s / 70 Mbits/s
> 1 To | Déployer un autre serveur de traitement | | | | 

**Tableau 2**

Où :

- L’entrée correspond à la bande passante de téléchargement en aval (intranet entre la source et le serveur de traitement).
- La sortie correspond à la bande passante utilisée (internet entre le serveur de traitement et le serveur cible maître). Les nombres concernant la sortie supposent une compression de serveur de traitement moyenne de 30 %.
- Pour le disque cache, un disque de système d'exploitation distinct de 128 Go minimum est recommandé pour tous les serveurs de traitement.
- Pour le débit du cache disque, le stockage suivant a été utilisé pour l’analyse comparative : 8 disques SAS de 10 000 tr/min avec une configuration RAID 10.

#### Considérations relatives aux serveurs de configuration

Chaque serveur de configuration peut prendre en charge jusqu'à 100 ordinateurs source avec 3-4 volumes. Si ces chiffres sont dépassés, nous vous recommandons de déployer un autre serveur de configuration. Reportez-vous au tableau 1 pour les propriétés de machine virtuelle par défaut du serveur de configuration.

#### Considérations relatives aux comptes de stockage et serveurs cibles maîtres

Le stockage pour chaque serveur cible maître se compose d'un disque de système d'exploitation, d'un volume de rétention et de disques de données. Le lecteur de rétention conserve le journal des modifications de disque pendant la durée de la fenêtre de rétention définie dans le portail Site Recovery. Reportez-vous au tableau 1 pour les propriétés de machine virtuelle du serveur cible maître. Le tableau 3 montre comme les disques de A4 sont utilisés.

**Instance** | **Disque de système d’exploitation** | **Rétention** | **Disques de données**
--- | --- | --- | ---
 | | **Rétention** | **Disques de données**
A4 standard | 1 disque (1 * 1 023 Go) | 1 disque (1 * 1 023 Go) | 15 disques (15 * 1 023 Go)
D14 standard | 1 disque (1 * 1 023 Go) | 1 disque (1 * 1 023 Go) | 31 disques (15 * 1 023 Go)

**Tableau 3**

La planification de la capacité pour le serveur cible maître dépend des points suivants :

- Limitations et performances de stockage azure
	- 500 IOPS par disque
	- 20 000 requêtes par compte de stockage
	- En fonction de ces chiffres, idéalement 40 disques sont pris en charge par un compte de stockage, chaque disque gérant 500 IOPS. 
-	Taux de modification quotidien 
-	Stockage de volume de rétention.

Notez les points suivants :

- Une seule source ne peut pas couvrir plusieurs comptes de stockage. Cela concerne le disque de données qui va avec les comptes de stockage sélectionnés lorsque vous configurez la protection. Le disque de système d'exploitation et le disque de rétention vont généralement avec le compte de stockage automatiquement déployé.
- Le volume de stockage de rétention requis varie en fonction du taux de modification quotidien et du nombre de jours de rétention. Stockage de rétention requis par le serveur cible maître = attrition totale à partir de la source par jour * nombre de jours de rétention. 
- Chaque serveur cible maître n'a qu'un seul volume de rétention. Le volume de rétention est partagé entre les disques attachés au serveur cible maître. Par exemple :
	- S'il existe un ordinateur source avec 5 disques et que chaque disque génère 120 IOPS (taille de 8 Ko) sur la source, cela se traduit par 240 IOPS par disque (2 opérations sur le disque cible par E/S source). Le nombre 240 IOPS se trouve dans la limite Azure de 500 IOPS par disque.
	- Sur le volume de rétention, cela devient 120 * 5 = 600 IOPS, ce qui peut créer un goulot d’étranglement. Dans ce scénario, une bonne stratégie serait d’ajouter des disques au volume de rétention et que ces derniers le couvrent, dans une configuration de bandes RAID. Cela améliore les performances, car les IOPS sont réparties sur plusieurs disques. Le nombre de lecteurs à ajouter au volume de rétention sera le suivant :
		- Nombre total d'IOPS à partir de l'environnement source / 500
		- Attrition totale par jour à partir de l'environnement source (non compressé) / 287 Go. 287 Go est le débit maximal pris en charge par un disque cible par jour. Cette métrique varie en fonction de la taille d'écriture avec un facteur de 8 Ko, car, dans ce cas, 8 Ko est la taille d'écriture supposée. Par exemple, si la taille d'écriture est de 4 Ko, le débit sera de 287/2. Et si la taille d'écriture est de 16 Ko, le débit sera de 287*2.
- Nombre de comptes de stockage requis = nombre total d’IOPS source / 10 000.


## Avant de commencer

**Composant** | **Configuration requise** | **Détails**
--- | --- | --- 
**Compte Azure** | Vous aurez besoin d’un compte [Microsoft Azure](http://azure.microsoft.com/). Vous pouvez commencer avec une [version d'évaluation gratuite](pricing/free-trial/).
**Stockage Azure** | <p>Vous devez avoir un compte de stockage Azure pour stocker les données répliquées</p><p>La géo-réplication doit être activée pour ce compte.</p><p>Il doit se trouver dans la même région que le service Azure Site Recovery et être associé au même abonnement.</p><p>Pour en savoir plus, consultez [Introduction à Microsoft Azure Storage](../storage/storage-introduction.md)</p>
**Réseau virtuel Azure** | Vous aurez besoin d'un réseau virtuel Azure sur lequel le serveur de configuration et le serveur cible maître seront déployés. Il doit être dans le même abonnement et la même région que le coffre Azure Site Recovery. Si vous souhaitez répliquer des données avec une connexion ExpressRoute ou VPN, le réseau virtuel Azure doit être connecté à votre réseau local par le biais d'une connexion ExpressRoute ou d'un VPN de site à site.
**Ressources Azure** | Assurez-vous d'avoir suffisamment de ressources Azure pour déployer tous les composants. Découvrez plus d’informations dans [Limites d’abonnement Azure](../azure-subscription-service-limits.md).
**Machines virtuelles Azure** | <p>Les machines virtuelles que vous souhaitez protéger doivent être conformes aux [conditions préalables Azure](site-recovery-best-practices.md).</p><p>** Nombre de disques** : un maximum de 31 disques peut être pris en charge sur un seul serveur protégé</p><p>** Tailles de disque** : la capacité d’un disque ne doit pas être supérieure à 1 023 Go</p><p>** Clustering ** : les serveurs en cluster ne sont pas pris en charge</p><p>** Démarrage ** : le démarrage Unified Extensible Firmware Interface (UEFI) / Extensible Firmware Interface (EFI) n'est pas pris en charge</p><p>** Volumes ** : les volumes chiffrés Bitlocker ne sont pas pris en charge</p><p> **Noms de serveur** : les noms doivent contenir entre 1 et 63 caractères (lettres, chiffres et traits d’union). Le nom doit commencer par une lettre ou un chiffre et se terminer par une lettre ou un chiffre. Une fois qu'un ordinateur est protégé, vous pouvez modifier le nom Azure.</p>
**Serveur de configuration** | <p>Une machine virtuelle A3 standard basée sur une image de la galerie Azure Site Recovery Windows Server 2012 R2 est créée dans votre abonnement pour le serveur de configuration. Elle est créée comme première instance d'un nouveau service cloud. Si vous sélectionnez Internet public comme type de connectivité pour le serveur de configuration, le service cloud sera créé avec une adresse IP publique réservée.</p><p>Le chemin d'installation doit contenir uniquement des caractères anglais.</p>
**Serveur cible maître** | <p>Machine virtuelle Azure A4 ou D14 standard.</p><p>Le chemin d'installation doit uniquement comprendre des caractères anglais. Par exemple, le chemin d'accès doit être **/usr/local/ASR** pour un serveur cible maître exécutant Linux.</p></p>
**Serveur de traitement** | <p>Vous pouvez déployer le serveur de traitement sur un ordinateur physique ou virtuel exécutant Windows Server 2012 R2 avec les dernières mises à jour. Effectuez l’installation sur C:/.</p><p>Nous vous recommandons de placer le serveur sur le même réseau et sous-réseau que les ordinateurs que vous souhaitez protéger.</p><p>Installez VMware vSphere CLI 5.5.0 sur le serveur de traitement. Le composant VMware vSphere CLI est requis sur le serveur de traitement pour pouvoir détecter les ordinateurs virtuels gérés par un serveur vCenter ou les ordinateurs virtuels exécutés sur un hôte ESXi.</p><p>Le chemin d'installation doit uniquement comprendre des caractères anglais.</p>
**VMware** | <p>Un serveur VMWare vCenter qui gère vos hyperviseurs VMware vSphere. Il doit exécuter vCenter version 5.1 ou 5.5 avec les dernières mises à jour.</p><p>Un ou plusieurs hyperviseurs vSphere contenant des ordinateurs virtuels VMware que vous souhaitez protéger. L'hyperviseur doit exécuter ESX/ESXi version 5.1 ou 5.5 avec les dernières mises à jour.</p><p>Des outils VMware doivent être installés et exécutés sur les ordinateurs virtuels VMware.</p>
**Ordinateurs Windows** | <p>Les ordinateurs virtuels VMware ou serveurs physiques protégés sous Windows ont un certain nombre d'exigences.</p><p>Un système d'exploitation 64 bits pris en charge : **Windows Server 2012 R2**, **Windows Server 2012** ou **Windows Server 2008 R2 avec au moins SP1**.</p><p>Le nom d'hôte, les points de montage, les noms de périphériques, le chemin d'accès au système Windows (par ex : C:\\Windows) doivent uniquement être en anglais.</p><p>Le système d'exploitation doit être installé sur le lecteur C:\\.</p><p>Seuls les disques de base sont pris en charge. Les disques dynamiques ne sont pas pris en charge.</p><p><Firewall rules on protected machines should allow them to reach the configuration and master target servers in Azure.p><p>Vous devez fournir un compte administrateur (vous devez être un administrateur local sur l'ordinateur Windows) pour effectuer l'installation Push du service de mobilité sur les serveurs Windows. Si le compte fourni n'est pas un compte de domaine, vous devez désactiver le contrôle d'accès utilisateur distant sur l'ordinateur local. Pour cela, ajoutez l’entrée de registre DWORD LocalAccountTokenFilterPolicy avec une valeur de 1 dans HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\System. Pour ajouter l'entrée de registre à partir d’une CLI, ouvrez cmd ou powershell et entrez **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**. [En savoir plus](https://msdn.microsoft.com/library/aa826699.aspx) sur le contrôle d’accès.</p><p>Après un basculement, si vous voulez vous connecter à des ordinateurs virtuels Windows dans Azure avec le Bureau à distance, assurez-vous que le Bureau à distance est activé pour l’ordinateur local. Si vous ne vous connectez pas via un VPN, les règles de pare-feu doivent autoriser les connexions Bureau à distance via Internet.</p>
**Ordinateurs Linux** | <p> Un système d'exploitation 64 bits pris en charge : **Centos 6.4, 6.5, 6.6** ; **Oracle Enterprise Linux 6.4, 6.5 exécutant le noyau compatible Red Hat ou Unbreakable Enterprise Kernel Release 3 (UEK3)**, **SUSE Linux Enterprise Server 11 SP3**.</p><p>Les règles de pare-feu sur les ordinateurs protégés doivent leur permettre de joindre les serveurs de configuration et les serveurs cibles maîtres dans Azure.</p><p>Les fichiers /etc/hosts sur des ordinateurs protégés doivent contenir des entrées qui mappent le nom d'hôte local aux adresses IP associées à toutes les cartes réseau </p><p>Si vous souhaitez vous connecter à une machine virtuelle Azure exécutant Linux après un basculement à l'aide d'un client Secure Shell (ssh), assurez-vous que le service Secure Shell sur l'ordinateur protégé est configuré pour démarrer automatiquement au démarrage du système et que les règles de pare-feu autorisent une connexion ssh à celui-ci.</p><p>Le nom d'hôte, les points de montage, les noms de périphériques et les chemins d'accès système et noms de fichier Linux (par ex : /etc/ ; /usr) doivent uniquement être en anglais.</p><p>La protection peut être activée pour les ordinateurs locaux avec le stockage suivant : - <br>système de fichiers : EXT3, ETX4, ReiserFS, XFS<br>Mappeur périphérique-logiciel (multichemin)<br>Gestionnaire de volume : LVM2<br>Les serveurs physiques avec le stockage contrôleur HP CCISS ne sont pas pris en charge.</p>
**Tiers** | Le fonctionnement correct de certains composants de déploiement de ce scénario dépend de logiciels tiers. Pour la liste complète, consultez [Informations et remarques relatives aux logiciels tiers](#third-party)

## Déploiement

Le graphique résume les étapes du déploiement.

![Étapes du déploiement](./media/site-recovery-vmware-to-azure/VMWare2AzureSteps.png)

## Type de la connectivité réseau

Vous disposez de deux options pour configurer la connectivité réseau entre votre site local et le réseau virtuel Azure sur lequel les composants de votre infrastructure (serveur de configuration, serveurs cibles maîtres) sont déployés. Vous devrez choisir l'option de connectivité réseau à utiliser avant de déployer votre serveur de configuration. Ce choix doit se faire au moment du déploiement et ne peut pas être modifié ultérieurement.

**Internet public :** la communication et la réplication des données entre les serveurs locaux (serveur de traitement, serveurs protégés) et les serveurs composant l'infrastructure Azure (serveur de configuration, serveur cible maître) se produit sur une connexion SSL/TLS sécurisée du site vers les points de terminaison publics sur le serveur de configuration et le serveur cible maître. La seule exception est la connexion entre le serveur de traitement et le serveur cible maître sur le port TCP 9080 qui est non chiffrée. Cette connexion n'est utilisée que pour l'échange d'informations de contrôle relatives au protocole de réplication utilisé pour la configuration de la réplication.

![Diagramme de déploiement Internet](./media/site-recovery-vmware-to-azure/ASRVmware_deploymentInternet.png)

**VPN :** la communication et la réplication des données entre les serveurs locaux (serveur de traitement, serveurs protégés) et les serveurs composant l'infrastructure Azure (serveur de configuration, serveur cible maître) se produit sur une connexion VPN entre votre réseau local et le réseau virtuel Azure sur lequel le serveur de configuration et les serveurs cibles maîtres sont déployés. Assurez-vous que votre réseau local est connecté au réseau virtuel Azure par une connexion ExpressRoute ou une connexion VPN de site à site.

![Diagramme de déploiement VPN](./media/site-recovery-vmware-to-azure/ASRVmware_deploymentVPN.png)


## Étape 1 : Créer un coffre

1. Connectez-vous au [portail de gestion](https://portal.azure.com).


2. Développez **Services de données** > **Services de récupération**, puis cliquez sur **Coffre Site Recovery**.


3. Cliquez sur **Créer nouveau** > **Création rapide**.

4. Dans **Name**, entrez un nom convivial pour identifier le coffre.

5. Dans **Region**, sélectionnez la région géographique du coffre. Pour découvrir les régions prises en charge, référez-vous à la disponibilité géographique de la page [Détails des prix d'Azure Site Recovery](pricing/details/site-recovery/)

6. Cliquez sur **Create vault**.

	![Nouveau coffre](./media/site-recovery-vmware-to-azure/ASRVMWare_CreateVault.png)

Vérifiez la barre d'état pour vous assurer que le coffre a été créé correctement. Le coffre apparaît comme **Actif** dans la page **Recovery Services**.

## Étape 2 : déployer un serveur de configuration

### Configurez les paramètres du serveur

1. Sur la page **Recovery Services**, cliquez sur le coffre pour ouvrir la page Démarrage rapide. Vous pouvez aussi ouvrir cette page à tout moment au moyen de l'icône.

	![Icône Quick Start](./media/site-recovery-vmware-to-azure/ASRVMWare_QuickStartIcon.png)

2. Dans la liste déroulante, sélectionnez **Entre un site local comportant des serveurs physiques/VMware et Azure**.
3. Dans **Préparer les ressources (Azure) cibles**, cliquez sur **Déployer le serveur de configuration**.

	![Déployer un serveur de configuration](./media/site-recovery-vmware-to-azure/ASRVMWare_DeployCS2.png)

4. Dans **Détails du nouveau serveur de configuration**, spécifiez :

	- un nom pour le serveur de configuration et les informations d'identification pour s’y connecter.
	- Dans la liste déroulante pour le type de connectivité réseau, sélectionnez Internet public ou VPN.[AZURE.NOTE]Ce choix doit se faire au moment du déploiement et ne peut pas être modifié ultérieurement.  
	- Sélectionnez le réseau Azure sur lequel le serveur doit se trouver. Si vous avez spécifié VPN comme type de connectivité réseau, assurez-vous que ce réseau virtuel Azure est connecté à votre site local par le biais d'une connexion ExpressRoute ou d'un VPN de site à site.
	- Spécifiez le sous-réseau et l'adresse IP interne à affecter au serveur. Notez que les quatre premières adresses IP d’un sous-réseau sont réservées à un usage interne Azure. Utilisez une autre adresse IP disponible.
	
	![Déployer un serveur de configuration](./media/site-recovery-vmware-to-azure/ASRVMware_CSDetails2.png)

5. Lorsque vous cliquez sur **OK**, une machine virtuelle A3 standard basée sur une image de la galerie Azure Site Recovery Windows Server 2012 R2 est créée dans votre abonnement pour le serveur de configuration. Elle est créée comme première instance d'un nouveau service cloud. Si vous avez spécifié Internet Public comme type de connectivité réseau, le service cloud est créé avec une adresse IP publique réservée. Vous pouvez surveiller la progression dans l'onglet **Tâches**.

	![Surveiller la progression](./media/site-recovery-vmware-to-azure/ASRVMWare_MonitorConfigServer.png)

6.  **Cette étape s'applique uniquement si votre type de connectivité est Internet public.** Une fois le serveur de configuration déployé, notez l'adresse IP publique qui lui est affectée à la page **Machines virtuelles** du portail Azure. Ensuite, sous l'onglet **Points de terminaison**, notez le port HTTPS public mappé au port privé 443. Vous aurez besoin de ces informations ultérieurement, lors de l'inscription du serveur cible maître et du serveur de traitement auprès du serveur de configuration. Le serveur de configuration est déployé avec ces points de terminaison :

	- HTTPS : le port public est utilisé pour coordonner les communications entre les serveurs de composants et Azure via Internet. Le port privé 443 est utilisé pour coordonner les communications entre les serveurs de composants et Azur via VPN.
	- Personnalisé : le port public est utilisé pour la communication des outils de restauration automatique via
	- Internet. Le port privé 9443 est utilisé pour la communication des outils de restauration automatique via VPN.
	- PowerShell : port privé 5986
	- Bureau à distance : port privé 3389
	
	![Points de terminaison de MV](./media/site-recovery-vmware-to-azure/ASRVMWare_VMEndpoints.png)

    >[AZURE.WARNING]Ne supprimez ou ne modifiez pas le numéro de port public ou privé des points de terminaison créés lors du déploiement du serveur de configuration.

Le serveur de configuration est déployé dans un service cloud Azure créé automatiquement avec une adresse IP réservée. L'adresse réservée est nécessaire pour s'assurer que l'adresse IP du service cloud du serveur de configuration reste la même pour tous les redémarrages des machines virtuelles (y compris le serveur de configuration) sur le service cloud. La réservation de l'adresse IP publique réservée doit être annulée manuellement lorsque le serveur de configuration est désactivé ou cette adresse restera réservée. Il existe une limite par défaut de 20 adresses IP publiques réservées par abonnement. [En savoir plus](../virtual-network/virtual-networks-reserved-private-ip.md) sur les adresses IP réservées.

### inscrire le serveur de configuration dans le coffre

1. À la page **Démarrage rapide**, cliquez sur **Préparer les ressources cibles** > **Télécharger une clé d'inscription**. Le fichier de clé est généré automatiquement. Il est valide pendant cinq jours après sa création. Copiez-le sur le serveur de configuration.
2. Dans **Machines virtuelles**, sélectionner le serveur de configuration dans la liste des machines virtuelles. Ouvrez l'onglet **Tableau de bord** et cliquez sur **Connexion**. **Ouvrez** le fichier RDP téléchargé pour vous connecter au serveur de configuration avec le Bureau à distance. Si votre serveur de configuration est déployé sur un réseau VPN, utilisez l'adresse IP interne (il s'agit de l'adresse IP spécifiée lorsque vous avez déployé le serveur de configuration et vous pouvez également la trouver sur la page du tableau de bord des machines virtuelles pour la machine virtuelle du serveur configuration) du serveur de configuration pour vous y connecter par le biais du Bureau à distance à partir de votre réseau local. Quand vous vous connectez pour la première fois, l'Assistant Installation du serveur de configuration Azure Site Recovery s'exécute automatiquement.

	![Inscription](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationSplashscreen.png)

3. Dans **Installation de logiciels tiers**, cliquez sur **J'accepte** pour télécharger et installer MySQL.

	![Installation de MySQL](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationMySQLEULA.png)

4. Dans **Détails du serveur MySQL**, créez des informations d'identification pour vous connecter à l'instance MySQL Server.

	![Informations d'identification de MySQL](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationMySQLPWD.png)

5. Dans **Paramètres Internet**, indiquez comment le serveur de configuration se connecte à Internet. Notez les points suivants :

	- Si vous souhaitez utiliser un proxy personnalisé, vous devez le configurer avant d'installer le fournisseur.
	- Lorsque vous cliquez sur **Suivant**, un test est exécuté pour vérifier la connexion proxy.
	- Si vous n'utilisez pas de proxy personnalisé ou si votre proxy par défaut nécessite une authentification, vous devez saisir les détails du proxy, y compris l'adresse du proxy, le port et les informations d’identification.
	- Les URL suivantes doivent être accessibles via le proxy :
		- **.hypervrecoverymanager.windowsazure.com
- **.accesscontrol.windows.net
- **.backup.windowsazure.com
- **.blob.core.windows.net
- **.store.core.windows.net
- Si votre pare-feu a des règles basées sur l’adresse IP, assurez-vous qu’elles autorisent la communication à partir du serveur de configuration vers les adresses IP décrites dans la section [Plages d’adresses IP du centre de données Azure](https://msdn.microsoft.com/fr-fr/library/azure/dn175718.aspx) et pour le protocole HTTPS (443). Vous devez autoriser les plages IP de la région Azure que vous prévoyez d’utiliser, ainsi que celles de la région ouest des États-Unis.

	![Inscription de proxy](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationProxy.png)

6. Dans **Paramètres de localisation de message d’erreur fournisseur**, indiquez la langue dans laquelle vous souhaitez que les messages d’erreur apparaissent.

	![Inscription de message d’erreur](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationLocale.png)

7. Dans **Inscription à Azure Site Recovery**, recherchez et sélectionnez le fichier de clé que vous avez copié sur le serveur.

	![Inscription de fichier de clé](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationVault.png)

8. Sur la page de fin de l'Assistant, sélectionnez ces options :

	- Sélectionnez **Lancer la boîte de dialogue Gestion des comptes** pour indiquer que la boîte de dialogue Gérer les comptes doit s’ouvrir une fois l’Assistant terminé.
	- Sélectionnez **Créer une icône de bureau pour Cspsconfigtool** pour ajouter un raccourci bureau sur le serveur de configuration afin de pouvoir ouvrir la boîte de dialogue **Gérer les comptes** à tout moment sans avoir à réexécuter l’Assistant.

	![Terminer l’inscription](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationFinal.png)

9. Cliquez sur **Terminer** pour terminer l’Assistant. Une phrase secrète est générée. Copiez-la à un emplacement sécurisé. Vous en aurez besoin pour authentifier et inscrire le serveur de traitement et le serveur cible maître auprès du serveur de configuration. Elle sert également à garantir l'intégrité du canal lors des communications avec le serveur de configuration. Vous pouvez regénérer la phrase secrète, mais vous devrez dans ce cas réinscrire le serveur de traitement et le serveur cible maître à l'aide de la nouvelle phrase secrète.

	![Phrase secrète](./media/site-recovery-vmware-to-azure/passphrase.png)

Après l’inscription, le serveur de configuration est répertorié sur la page **Serveurs de configuration** dans le coffre.

### Configurer et gérer des comptes

Lors du déploiement, Site Recovery demande des informations d'identification pour les actions suivantes :

- Lorsque vous ajoutez un serveur vCenter pour la découverte automatique des machines virtuelles gérées par le serveur vCenter. Un compte vCenter est requis pour la découverte automatique des machines virtuelles.
- Lorsque vous ajoutez des ordinateurs à protéger, afin que Site Recovery puisse installer le service de mobilité sur ces derniers.

Après avoir inscrit le serveur de configuration, vous pouvez ouvrir la boîte de dialogue **Gérer les comptes** pour ajouter et gérer des comptes qui doivent être utilisés pour ces actions. Il existe plusieurs manières de procéder :

- Ouvrez le raccourci que vous avez choisi de créer pour la boîte de dialogue sur la dernière page du programme d'installation pour le serveur de configuration (cspsconfigtool).
- Ouvrez la boîte de dialogue de fin du programme d'installation du serveur de configuration.

1. Dans **Gérer les comptes**, cliquez sur **Ajouter un compte**. Vous pouvez également modifier et supprimer des comptes existants.

	![Gérer les comptes](./media/site-recovery-vmware-to-azure/ASRVMWare_ManageAccount.png)

2. Dans **Détails du compte**, spécifiez un nom de compte à utiliser dans Azure et des informations d’identification (nom de domaine / nom d’utilisateur).

	![Gérer les comptes](./media/site-recovery-vmware-to-azure/ASRVMWare_AccountDetails.png)

### Connexion au serveur de configuration 

Il existe deux façons de se connecter au serveur de configuration :

- Via une connexion VPN de site à site ou ExpressRoute
- Via internet 

Notez les points suivants :

- Une connexion internet utilise les points de terminaison de la machine virtuelle avec l'adresse IP virtuelle publique du serveur.
- Une connexion VPN utilise l'adresse IP interne du serveur et les ports privés du point de terminaison.
- Une décision irrévocable doit être prise pour déterminer s'il faut se connecter (données de réplication et contrôle) à partir de vos serveurs locaux aux différents serveurs de composant (serveur de configuration, serveur cible maître) en cours d'exécution dans Azure via une connexion VPN ou internet. Vous ne pouvez pas modifier ce paramètre par la suite. Si vous le faites, vous devez redéployer le scénario et protéger vos ordinateurs de nouveau.  


## Étape 3 : déployer le serveur cible maître

1. Dans **Préparer les ressources (Azure) cibles**, cliquez sur **Déployer le serveur cible maître**.
2. Spécifiez les détails du serveur cible maître et les informations d'identification. Le serveur sera déployé sur le même réseau Azure que le serveur de configuration auprès duquel vous l'inscrivez. Quand vous cliquez sur Terminer, une machine virtuelle Azure est créée avec une image de la galerie Windows ou Linux.

	![Paramètres du serveur cible](./media/site-recovery-vmware-to-azure/ASRVMWare_TSDetails.png)

Notez que les quatre premières adresses IP d’un sous-réseau sont réservées à un usage interne Azure. Indiquez une autre adresse IP disponible.

3. Une machine virtuelle de serveur cible maître Windows est créée avec ces points de terminaison (les points de terminaison publics sont créés uniquement si votre déploiement est de type Internet public) :

	- Personnalisé : le port public est utilisé par le serveur de traitement pour envoyer des données de réplication via internet. Le port privé 9443 est utilisé par le serveur de traitement pour envoyer des données de réplication au serveur cible maître via VPN.
	- Personnalisé1 : le port public est utilisé par le serveur de traitement pour envoyer des métadonnées de contrôle via internet. Le port privé 9080 est utilisé par le serveur de traitement pour envoyer des métadonnées de contrôle au serveur cible maître via VPN.
	- PowerShell : port privé 5986
	- Bureau à distance : port privé 3389

4. Une machine virtuelle de serveur cible maître Linux est créée avec ces points de terminaison (les points de terminaison publics sont créés uniquement si votre déploiement est de type Internet public) :

	- Personnalisé : le port public est utilisé par le serveur de traitement pour envoyer des données de réplication via internet. Le port privé 9443 est utilisé par le serveur de traitement pour envoyer des données de réplication au serveur cible maître via VPN.
	- Personnalisé1 : le port public est utilisé par le serveur de traitement pour envoyer des métadonnées de contrôle via internet. Le port privé 9080 est utilisé par le serveur de traitement pour envoyer des données de contrôle au serveur cible maître via VPN.
	- SSH : port privé 22

    >[AZURE.WARNING]Ne supprimez ou ne modifiez pas le numéro de port public ou privé des points de terminaison créés lors du déploiement du serveur cible maître.

5. Dans **Machines virtuelles**, attendez que la machine virtuelle démarre.

	- Si vous avez configuré le serveur avec Windows, notez les détails du Bureau à distance.
	- Si vous avez configuré le serveur avec Linux et que vous vous connectez via VPN, notez l'adresse IP interne de la machine virtuelle. Si vous vous connectez via internet, notez l'adresse IP publique.

6.  Ouvrez une session sur le serveur pour terminer l'installation et l'inscrire auprès du serveur de configuration.
7.  Si vous exécutez Windows :

	1. Initiez une connexion Bureau à distance à la machine virtuelle. La première fois que vous ouvrez une session, un script s'exécute dans une fenêtre PowerShell. Ne la fermez pas. Une fois terminé, l'outil de configuration de l'agent hôte s'ouvre automatiquement pour inscrire le serveur.
	2. Dans **Configuration de l’agent hôte**, spécifiez l’adresse IP interne du serveur de configuration et le port 443. Vous pouvez utiliser l'adresse interne et le port privé 443 même si vous ne vous connectez pas avec VPN, car la machine virtuelle est associée au même réseau Azure que le serveur de configuration. Laissez l’option **Utiliser HTTPS** activée. Entrez la phrase secrète pour le serveur de configuration que vous avez notée précédemment. Cliquez sur **OK** pour inscrire le serveur. Notez que vous pouvez ignorer les options NAT. Elles ne sont pas utilisées.
	3. Si le lecteur de rétention estimée doit être supérieur à 1 To, vous pouvez configurer le volume de rétention (R:) à l’aide d’un disque virtuel et d’[espaces de stockage](http://blogs.technet.com/b/askpfeplat/archive/2013/10/21/storage-spaces-how-to-configure-storage-tiers-with-windows-server-2012-r2.aspx)
	
	![Serveur cible maître Windows](./media/site-recovery-vmware-to-azure/ASRVMWare_TSRegister.png)

8. Si vous exécutez Linux :
	1. Assurez-vous que vous avez installé les derniers services d'intégration Linux (LIS) avant d'installer le logiciel du serveur cible maître. Vous trouverez la dernière version de LIS, ainsi que des instructions d’installation [ici](https://www.microsoft.com/fr-fr/download/details.aspx?id=46842). Redémarrez la machine après l'installation de LIS.
	2. Dans **Préparer les ressources (Azure) cibles**, cliquez sur **Télécharger et installer des logiciels supplémentaires (uniquement pour le serveur cible maître Linux)** pour télécharger le package du serveur cible maître Linux. Copiez le fichier tar téléchargé sur l'ordinateur virtuel à l'aide d'un client sftp. Ou vous pouvez aussi vous connecter au serveur cible maître Linux déployé et utiliser *wgethttp://go.microsoft.com/fwlink/?LinkID=529757&clcid=0x409* pour télécharger le fichier.
2. Connectez-vous au serveur à l'aide d'un client Secure Shell. Notez que si vous êtes connecté au réseau Azure via VPN, vous devez utiliser l'adresse IP interne. Sinon, utilisez l'adresse IP externe et le point de terminaison public SSH.
	3. Extrayez les fichiers du programme d’installation compressé avec gzip en exécutant : **tar –xvzf Microsoft-ASR\_UA\_8.4.0.0\_RHEL6-64*** ![Serveur cible maître Linux](./media/site-recovery-vmware-to-azure/ASRVMWare_TSLinuxTar.png)
	4. Assurez-vous que vous êtes dans le répertoire dans lequel vous avez extrait le contenu du fichier tar.
	5. Copiez la phrase secrète du serveur de configuration dans un fichier local à l’aide de la commande **echo*`<passphrase>`* > passphrase.txt**
	6. Exécutez la commande « **sudo ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i *`<Configuration server internal IP address>`* -p 443 -s y -c https -P passphrase.txt** ».

	![Inscrire un serveur cible](./media/site-recovery-vmware-to-azure/Linux-MT-install.png)

9. Patientez 10 à 15 minutes et, sur la page **Serveurs** > **Serveurs de configuration**, vérifiez que le serveur cible maître est répertorié comme inscrit sous l’onglet **Détails du serveur**. Si vous exécuté Linux et que l'inscription n'a pas été effectué, réexécutez l'outil de configuration d'hôte à partir de /usr/local/ASR/Vx/bin/hostconfigcli. Vous devrez définir des autorisations d'accès en exécutant chmod en tant qu’utilisateur racine.

	![Vérifier le serveur cible](./media/site-recovery-vmware-to-azure/ASRVMWare_TSList.png)

>[AZURE.NOTE]Une fois l’inscription terminée, notez que cela peut prendre jusqu’à 15 minutes avant que le serveur cible maître ne soit répertorié sous le serveur de configuration. Pour mettre à jour immédiatement, actualisez le serveur de configuration en cliquant sur le bouton Actualiser en bas de la page Serveurs de configuration.

## Étape 4 : déployer le serveur de traitement local

>[AZURE.NOTE]Nous vous recommandons de configurer une adresse IP statique sur le serveur de traitement afin de garantir son caractère permanent pour tous les redémarrages.

1. Cliquez sur Démarrage rapide > **Installer un serveur de traitement local** > **Télécharger et installer le serveur de traitement**.

	![Installer un serveur de traitement](./media/site-recovery-vmware-to-azure/ASRVMWare_PSDeploy.png)

2.  Copiez le fichier zip téléchargé sur le serveur sur lequel vous allez installer le serveur de traitement. Le fichier .zip contient deux fichiers d'installation :

	- Microsoft-ASR\_CX\_TP\_8.4.0.0\_Windows*
	- Microsoft-ASR\_CX\_8.4.0.0\_Windows*

3. Décompressez l'archive et copiez les fichiers d'installation à un emplacement sur le serveur.
4. Exécutez le fichier d’installation **Microsoft-ASR\_CX\_TP\_8.4.0.0\_Windows*** et suivez les instructions. Cette opération installe les composants tiers nécessaires au déploiement.
5. Puis exécutez **Microsoft-ASR\_CX\_8.4.0.0\_Windows***.
6. Sur la page **Mode du serveur**, sélectionnez **Serveur de traitement**.

	![Mode de sélection du serveur](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerSelection.png)

7. Sur la page **Détails de l’environnement**, procédez comme suit :


	- Si vous souhaitez protéger les machines virtuelles VMware, cliquez sur **Oui**
	- Si vous souhaitez uniquement protéger les serveurs physiques et que vous n’avez donc pas besoin que VMware vCLI soit installé sur le serveur de traitement. Cliquez sur **Non** et continuez.
		
	![Inscrire un serveur de configuration](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerVirtualPhysical.png)

8. Notez les points suivants lorsque vous installez VMware vCLI :

	- **Seule la version VMware vSphere CLI 5.5.0 est prise en charge**. Le serveur de traitement ne fonctionne pas avec d'autres versions ou mises à jour de vSphere CLI.
	- Téléchargez vSphere CLI 5.5.0 [ici.](https://my.vmware.com/web/vmware/details?downloadGroup=VCLI550&productId=352)
	- Si vous avez installé l’outil vSphere CLI juste avant de commencer l'installation du serveur de traitement et que le programme d'installation ne le détecte pas, attendez cinq minutes avant de recommencer l'installation. Cela garantit que toutes les variables d'environnement nécessaires pour la détection de vSphere CLI ont été correctement initialisés.

9.	Dans **Sélection de carte réseau pour le serveur de traitement**, sélectionnez la carte réseau que le serveur de traitement doit utiliser.

	![Sélectionner une carte](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerNICSelection.png)

10.	Dans **Détails du serveur de configuration** :

	- Pour l’adresse IP et le port, si vous vous connectez via un VPN, spécifiez l'adresse IP interne du serveur de configuration et le port 443. Sinon, spécifiez l'adresse IP virtuelle publique et un point de terminaison HTTP public mappé.
	- Tapez la phrase secrète du serveur de configuration.
	- Désactivez l’option **Vérifier la signature logicielle du service de mobilité** si vous souhaitez désactiver la vérification lors de l’utilisation de la transmission automatique pour installer le service. La vérification de la signature nécessite une connectivité internet à partir du serveur de traitement.
	- Cliquez sur **Next**.

	![Inscrire un serveur de configuration](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerConfigServer.png)


11. Dans **Sélectionner le lecteur d’installation**, sélectionnez un lecteur de cache. Le serveur de traitement a besoin d'un lecteur de cache avec au moins 600 Go d'espace libre. Cliquez ensuite sur **Installer**.

	![Inscrire un serveur de configuration](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerCacheConfig.png)

12. Notez que vous devrez peut-être redémarrer le serveur pour terminer l’installation. Dans **Serveur de configuration** > **Détails du serveur**, vérifiez que le serveur de traitement apparaît et qu’il a été inscrit dans le coffre.

>[AZURE.NOTE]Une fois l’inscription terminée, notez que cela peut prendre jusqu’à 15 minutes avant que le serveur de traitement ne soit répertorié sous le serveur de configuration. Pour mettre à jour immédiatement, actualisez le serveur de configuration en cliquant sur le bouton Actualiser en bas de la page Serveurs de configuration
 
![Valider un serveur de traitement](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerRegister.png)

Si vous n'avez pas désactivé la vérification de la signature pour le service de mobilité lors de l'inscription du serveur de traitement, vous pouvez le faire ultérieurement comme suit :

1. Ouvrez une session sur le serveur de traitement en tant qu'administrateur et ouvrez le fichier C:\\pushinstallsvc\\pushinstaller.conf. Dans la section **[PushInstaller.transport]**, ajoutez cette ligne : **SignatureVerificationChecks=”0”**. Enregistrez et fermez le fichier.
2. Redémarrez le service InMage PushInstall.


## Étape 5 : installer les dernières mises à jour

Avant de poursuivre, vérifiez que les dernières mises à jour sont installées. N'oubliez pas d'installer les mises à jour dans l'ordre suivant :

1. Serveur de configuration
2. Serveur de traitement
3. Serveur cible maître

Vous pouvez obtenir les mises à jour sur le **Tableau de bord** de Site Recovery. Pour l’installation Linux, extrayez les fichiers du programme d’installation compressé avec gzip et exécutez la commande « sudo ./install » pour installer la mise à jour

Si vous exécutez des ordinateurs virtuels ou des serveurs physiques sur lesquels le service de mobilité est déjà installé, vous pouvez obtenir les mises à jour de ce service comme suit :

- Téléchargez les mises à jour de ce service comme suit :
	- [Windows Server (64 bits uniquement)](http://download.microsoft.com/download/8/4/8/8487F25A-E7D9-4810-99E4-6C18DF13A6D3/Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe)
	- [CentOS 6.4,6.5,6.6 (64 bits uniquement)](http://download.microsoft.com/download/7/E/D/7ED50614-1FE1-41F8-B4D2-25D73F623E9B/Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz)
	- [Oracle Enterprise Linux 6.4,6.5 (64 bits uniquement)](http://download.microsoft.com/download/5/2/6/526AFE4B-7280-4DC6-B10B-BA3FD18B8091/Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz)
	- [SUSE Linux Enterprise Server SP3 (64 bits uniquement)](http://download.microsoft.com/download/B/4/2/B4229162-C25C-4DB2-AD40-D0AE90F92305/Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz)
- Vous pouvez également obtenir la version mise à jour du service de mobilité à partir du dossier C:\\pushinstallsvc\\repository sur le serveur de traitement après avoir mis à jour ce dernier.
- Si vous avez une machine déjà protégée avec une version antérieure du service mobilité installé, vous pouvez également mettre à niveau automatiquement le service de mobilité sur les machines protégées à partir du portail de gestion. Pour ce faire, sélectionnez le groupe de protection auquel appartient la machine, mettez en surbrillance la machine protégée et cliquez sur le bouton Mettre à jour le service de mobilité en bas. Le bouton Mettre à jour le service de mobilité est activé uniquement si une version plus récente du service de mobilité est disponible. Assurez-vous que le serveur de traitement exécute la dernière version du logiciel du serveur de traitement avant la mise à jour du service de mobilité. Le serveur protégé doit satisfaire toutes les [conditions préalables d’installation push automatique](#install-the-mobility-service-automatically) pour que la mise à jour du service de mobilité fonctionne.

![Sélectionnez un serveur vCenter](./media/site-recovery-vmware-to-azure/ASRVmware_UpdateMobility1.png)

Dans Sélectionnez les comptes, spécifiez le compte administrateur à utiliser pour mettre à jour le service de mobilité sur le serveur protégé. Cliquez sur OK et attendez que la tâche déclenchée se termine.


## Étape 6 : ajouter des serveurs vCenter ou hôtes ESXi

1. Sous l’onglet **Serveurs** > **Serveurs de configuration**, sélectionnez le serveur de configuration et cliquez sur **AJOUTER UN SERVEUR VCENTER** pour ajouter un serveur vCenter ou un hôte ESXi.

	![Sélectionnez un serveur vCenter](./media/site-recovery-vmware-to-azure/ASRVMWare_AddVCenter.png)

2. Spécifiez les détails du serveur vCenter ou hôte ESXi et sélectionnez le serveur de traitement qui sera utilisé pour le détecter.

	- Si le serveur vCenter n'est pas exécuté sur le port 443 par défaut, indiquez le numéro de port sur lequel il est exécuté.
	- Le serveur de traitement doit se trouver sur le même réseau que le serveur vCenter ou hôte ESXi et l’outil VMware vSphere CLI 5.5.0 doit être installé dessus.

	![Paramètres du serveur vCenter](./media/site-recovery-vmware-to-azure/ASRVMWare_AddVCenter4.png)


3. Une fois la détection terminée, le serveur vCenter apparaît sous les détails du serveur de configuration.

	![Paramètres du serveur vCenter](./media/site-recovery-vmware-to-azure/ASRVMWare_AddVCenter2.png)

4. Si vous utilisez un compte non administrateur pour ajouter le serveur vCenter ou hôte ESXi, assurez-vous que le compte possède les privilèges suivants :

	- Les privilèges Centre de données, Magasin de données, Dossier, Hôte, Réseau, Ressource, Stockage, Ordinateur virtuel et vSphere Distributed Switch doivent être activés pour les comptes vCenter.
	- Les privilèges Centre de données, Magasin de données, Dossier, Hôte, Réseau, Ressource, Vues de stockage, Ordinateur virtuel et vSphere Distributed Switch doivent être activés pour les comptes vCenter.



## Étape 7 : créer un groupe de protection

1. Ouvrez **Éléments protégés** > **Groupe de protection** et cliquez pour ajouter un groupe de protection.

	![Créer un groupe de protection](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG1.png)

2. Sur la page **Spécifier les paramètres du groupe de protection**, entrez un nom pour le groupe et sélectionnez le serveur de configuration sur lequel vous souhaitez créer le groupe.

	![Paramètres du groupe de protection](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG2.png)

3. Sur la page **Spécifier les paramètres de réplication**, configurez les paramètres de réplication qui seront utilisés pour toutes les machines du groupe.

	![Réplication du groupe de protection](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG3.png)

4. Paramètres :
	- **Cohérence multimachine virtuelle** : si vous activez cette option, elle crée des points de récupération cohérents au niveau de l’application, partagés entre les machines du groupe de protection. Ce paramètre est particulièrement important quand tous les ordinateurs du groupe de protection exécutent la même charge de travail. Tous les ordinateurs seront récupérés au même point de données. Disponible uniquement pour les serveurs Windows.
	- **Seuil de RPO** : des alertes sont générées quand la valeur du RPO (objectif de point de récupération) de réplication de la protection continue des données dépasse la valeur du seuil de RPO configurée.
	- **Rétention de point de récupération** : spécifie la fenêtre de rétention. Les ordinateurs protégés peuvent être récupérés à tout moment dans cette fenêtre.
	- **Fréquence des instantanés cohérents au niveau des applications** : spécifie la fréquence de création des points de récupération contenant des instantanés cohérents au niveau des applications.

Vous pouvez surveiller le groupe de protection à mesure que les points de récupération sont créés sur la page **Éléments protégés**.

## Étape 8 : configurer les ordinateurs à protéger

Vous devez installer le service de mobilité sur les ordinateurs virtuels et les serveurs physiques que vous souhaitez protéger. Il existe deux méthodes pour le faire :

- Transmettre et installer automatiquement le service sur chaque ordinateur du serveur de traitement ;
- Installer manuellement le service. 

### Installer automatiquement le service de mobilité

Quand vous ajoutez des ordinateurs à un groupe de protection, le service de mobilité est automatiquement envoyé et installé sur chaque ordinateur par le serveur de traitement.

**Transmettre et installer automatiquement le service de mobilité sur des serveurs Windows :**

1. Installez les dernières mises à jour pour le serveur de traitement, comme décrit dans [Étape 5 : installer les dernières mises à jour](#step-5-install-latest-updates) et assurez-vous que le serveur de traitement est disponible. 
2. Vérifiez qu’il y a une connectivité réseau entre l'ordinateur source et le serveur de traitement, et que l'ordinateur source est accessible depuis le serveur de traitement.  
3. Configurez le Pare-feu Windows de manière à autoriser le **Partage de fichiers et d’imprimantes** et l’**Infrastructure de gestion Windows**. Dans les paramètres du Pare-feu Windows, sélectionnez l'option « Autoriser une application ou une fonctionnalité via le pare-feu » et sélectionnez les applications comme indiqué dans l'image ci-dessous. Pour les ordinateurs qui appartiennent à un domaine, vous pouvez configurer la stratégie de pare-feu avec un objet de stratégie de groupe.

	![Paramètres du pare-feu](./media/site-recovery-vmware-to-azure/ASRVMWare_PushInstallFirewall.png)

4. Le compte utilisé pour effectuer l'installation Push doit appartenir au groupe Administrateurs sur l'ordinateur que vous souhaitez protéger. Ces informations d'identification sont uniquement utilisées pour l'installation Push du service de mobilité et vous devez les fournir lorsque vous ajoutez un ordinateur à un groupe de protection.
5. Si le compte fourni n'est pas un compte de domaine, vous devez désactiver le contrôle d'accès utilisateur distant sur l'ordinateur local. Pour cela, ajoutez l’entrée de registre DWORD LocalAccountTokenFilterPolicy avec une valeur de 1 dans HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\System. Pour ajouter l’entrée de registre à partir d’une CLI, ouvrez cmd ou powershell et entrez **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**. 

**Transmettre et installer automatiquement le service de mobilité sur des serveurs Linux :**

1. Installez les dernières mises à jour pour le serveur de traitement, comme décrit dans [Étape 5 : installer les dernières mises à jour](#step-5-install-latest-updates) et assurez-vous que le serveur de traitement est disponible.
2. Vérifiez qu’il y a une connectivité réseau entre l'ordinateur source et le serveur de traitement, et que l'ordinateur source est accessible depuis le serveur de traitement.  
3. Assurez-vous que le compte est un utilisateur racine sur le serveur Linux source.
4. Assurez-vous que les fichiers /etc/hosts sur le serveur Linux source contiennent des entrées qui mappent le nom d'hôte local aux adresses IP associées à toutes les cartes réseau.
5. Installez les packages openssh, openssh-server et openssl les plus récents sur l'ordinateur que vous souhaitez protéger.
6. Vérifiez que SSH est activé et exécuté sur le port 22. 
7. Activez le sous-système SFTP et l'authentification par mot de passe dans le fichier sshd\_config comme suit : 

	- a) Connectez-vous en tant qu’utilisateur racine.
	- b) Dans le fichier /etc/ssh/sshd\_config, recherchez la ligne commençant par **PasswordAuthentication**.
	- c) Supprimez les commentaires de la ligne et remplacez la valeur « no » par « yes ».

		![Mobilité Linux](./media/site-recovery-vmware-to-azure/ASRVMWare_LinuxPushMobility1.png)

	- d) Recherchez la ligne qui commence par « Subsystem » et supprimez les commentaires de la ligne.
	
		![Mobilité push de Linux](./media/site-recovery-vmware-to-azure/ASRVMWare_LinuxPushMobility2.png)

8. Assurez-vous que la variante Linux de la machine source est prise en charge.
 
### Installer le service de mobilité manuellement

Les packages de logiciel utilisés pour installer le service de mobilité sont sur le serveur de traitement dans C:\\pushinstallsvc\\repository. Connectez-vous au serveur de traitement et copiez le package d'installation approprié vers l'ordinateur source selon le tableau ci-dessous :

| Système d’exploitation source | Package de service de mobilité sur le serveur de traitement |
|---------------------------------------------------	|------------------------------------------------------------------------------------------------------	|
| Windows Server (64 bits uniquement) | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe` |
| CentOS 6.4, 6.5, 6.6 (64 bits uniquement) | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz` |
| SUSE Linux Enterprise Server 11 SP3 (64 bits uniquement) | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz`|
| Oracle Enterprise Linux 6.4, 6.5 (64 bits uniquement) | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz` |


**Pour installer le service de mobilité manuellement sur un serveur Windows**, procédez comme suit :

1. Copiez le package **Microsoft-ASR\_UA\_8.4.0.0\_Windows\_GA\_28Jul2015\_release.exe** depuis le répertoire du serveur de traitement dont le chemin d’accès est indiqué dans le tableau ci-dessus vers l’ordinateur source.
2. Installez le service de mobilité en exécutant le fichier exécutable sur l'ordinateur source.
3. Suivez les instructions du programme d’installation :
4. Sélectionnez **Service de mobilité** en tant que rôle et cliquez sur **Suivant**.
	
	![Installer le service de mobilité](./media/site-recovery-vmware-to-azure/ASRVMWare_MobilityServiceInstall1.png)

5. Laissez le répertoire d’installation en tant que chemin d’installation par défaut et cliquez sur **Installer**.
6. Dans **Configuration de l’agent hôte**, spécifiez l’adresse IP et le port HTTPS du serveur de configuration.

	- Si vous vous connectez via internet, spécifiez l'adresse IP virtuelle publique et le point de terminaison HTTPS public comme port.
	- Si vous vous connectez via un VPN, spécifiez l'adresse IP interne et le port 443. Laissez l’option **Utiliser HTTPS** cochée.

	![Installer le service de mobilité](./media/site-recovery-vmware-to-azure/ASRVMWare_MobilityServiceInstall2.png)

7. Indiquez la phrase secrète du serveur de configuration et cliquez sur **OK** pour inscrire le service de mobilité auprès du serveur de configuration.

**Pour exécuter depuis la ligne de commande :**

1. Copiez la phrase secrète depuis le CX vers le fichier « C:\\connection.passphrase » sur le serveur et exécutez cette commande. Dans notre exemple, CX est 104.40.75.37 et le port HTTPS est 62519 :

    `C:\Microsoft-ASR_UA_8.2.0.0_Windows_PREVIEW_20Mar2015_Release.exe" -ip 104.40.75.37 -port 62519 -mode UA /LOG="C:\stdout.txt" /DIR="C:\Program Files (x86)\Microsoft Azure Site Recovery" /VERYSILENT  /SUPPRESSMSGBOXES /norestart  -usesysvolumes  /CommunicationMode https /PassphrasePath "C:\connection.passphrase"`

**Installer le service de mobilité manuellement sur un serveur Linux** :

1. Copiez l'archive tar appropriée selon le tableau ci-dessus, depuis le serveur de traitement vers l'ordinateur source.
2. Ouvrez un interpréteur de commandes et décompressez l’archive tar vers un chemin d’accès local en exécutant `tar -xvzf Microsoft-ASR_UA_8.2.0.0*`
3. Créez un fichier passphrase.txt dans le répertoire local dans lequel vous avez extrait le contenu de l’archive tar en entrant *`echo <passphrase> >passphrase.txt`* à partir de l’interpréteur de commandes.
4. Installez le service de mobilité en entrant *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Spécifiez l'adresse IP et le port :

	- Si vous vous connectez au serveur de configuration via Internet, spécifiez l’adresse IP publique virtuelle et le point de terminaison HTTPS public du serveur de configuration dans `<IP address>` et `<port>`.
	- Si vous vous connectez via une connexion VPN, spécifiez l'adresse IP interne et le port 443.

**Pour exécuter depuis la ligne de commande :**

1. Copiez la phrase secrète depuis le CX vers le fichier « passphrase.txt » sur le serveur et exécutez cette commande. Dans notre exemple, CX est 104.40.75.37 et le port HTTPS est 62519 :

Pour installer sur un serveur de production :

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt
 
Pour installer sur le serveur cible :


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt

>[AZURE.NOTE]Lorsque vous ajoutez à un groupe de protection des ordinateurs qui exécutent déjà une version appropriée du service de mobilité, l'installation Push est ignorée.


## Étape 9 : activer la protection

Ajoutez des machines virtuelles à un groupe de protection pour activer leur protection. Avant de commencer, notez les points suivants :

- Les ordinateurs virtuels sont détectés toutes les 15 minutes et cela peut prendre jusqu'à 15 minutes avant qu’ils n’apparaissent dans Azure Site Recovery une fois détectés.
- Cela peut également prendre jusqu’à 15 minutes avant que les modifications de l'environnement sur l'ordinateur virtuel (par exemple, installation d’outils VMware) ne soient mises à jour dans Site Recovery.
- Vous pouvez vérifier l’heure de la dernière détection dans le champ **DERNIER CONTACT À** pour le serveur vCenter ou l’hôte ESXi sur la page **Serveurs de configuration**.
- Si vous avez déjà créé un groupe de protection et que vous ajoutez un serveur vCenter ou un hôte ESXi après cela, il faut quinze minutes pour que le portail Azure Site Recovery s’actualise et que les machines virtuelles apparaissent dans la boîte de dialogue **Ajouter des ordinateurs à un groupe de protection**.
- Si vous souhaitez ajouter immédiatement des ordinateurs au groupe de protection sans attendre la détection planifiée, mettez en surbrillance le serveur de configuration (ne cliquez pas dessus) et cliquez sur le bouton **Actualiser**.
- Lorsque vous ajoutez des ordinateurs virtuels ou ordinateurs physiques à un groupe de protection, le serveur de traitement transmet et installe automatiquement le service de mobilité sur le serveur source s’il n'est pas déjà installé.
- Pour que le mécanisme d'envoi automatique fonctionne, assurez-vous d'avoir configuré vos ordinateurs protégés comme décrit à l'étape précédente.

Ajoutez des ordinateurs comme suit :

1. **Éléments protégés** > **Groupe de protection** > onglet **Machines**. Cliquez sur **AJOUTER DES MACHINES**. Il est recommandé que les groupes de protection reflètent vos charges de travail, afin que vous ajoutiez des ordinateurs qui exécutent une application spécifique au même groupe.
2. Dans **Sélectionner les machines virtuelles** si vous protégez des serveurs physiques, dans l’assistant **Ajouter des machines physiques**, entrez l’adresse IP et un nom convivial. Sélectionnez ensuite la famille de systèmes d'exploitation.

	![Ajouter un serveur V-Center](./media/site-recovery-vmware-to-azure/ASRVMWare_PhysicalProtect.png)

3. Dans **Sélectionner les machines virtuelles** si vous protégez des machines virtuelles VMware, sélectionnez un serveur vCenter qui gère vos machines virtuelles (ou l’hôte EXSi sur lequel elles sont exécutées), puis sélectionnez les machines.

	![Ajouter un serveur V-Center](./media/site-recovery-vmware-to-azure/ASRVMWare_SelectVMs.png)

4. Dans **Spécifier les ressources cibles**, sélectionnez les serveurs cibles maîtres et le stockage à utiliser pour la réplication, puis déterminez si les paramètres doivent être utilisés pour toutes les machines virtuelles.

	![Serveur vCenter](./media/site-recovery-vmware-to-azure/ASRVMWare_MachinesResources.png)

4. Dans **Spécifier les comptes**, sélectionnez le compte à utiliser pour installer le service de mobilité sur des ordinateurs protégés. Les informations d'identification de compte sont nécessaires pour l’installation automatique du service de mobilité. Si vous ne pouvez pas sélectionner un compte, assurez-vous que vous en avez établi un comme décrit à l'étape 2. Notez que ce compte ne peut pas être accédé par Azure. Pour Windows server, le compte doit disposer de privilèges d'administrateur sur le serveur source. Pour Linux, le compte doit être racine.

	![Informations d’identification Linux](./media/site-recovery-vmware-to-azure/ASRVMWare_VMMobilityInstall.png)

5. Cochez la case pour terminer l'ajout d'ordinateurs au groupe de protection et démarrer la réplication initiale pour chaque ordinateur. Vous pouvez surveiller l’état sur la page **Tâches**.

	![Ajouter un serveur V-Center](./media/site-recovery-vmware-to-azure/ASRVMWare_PGJobs2.png)

5. En outre, vous pouvez surveiller l’état de protection en cliquant sur **Éléments protégés** > nom du groupe de protection > **Machines virtuelles**. Une fois que la réplication initiale est terminée et que les ordinateurs synchronisent des données, ils affichent l’état **Protégé**.

	![Tâches relatives aux ordinateurs virtuels](./media/site-recovery-vmware-to-azure/ASRVMWare_PGJobs.png)


### définir les propriétés de l'ordinateur protégé

1. Dès qu’un ordinateur est à l’état **Protégé**, vous pouvez configurer ses propriétés de basculement. Dans les détails du groupe de protection, sélectionnez l’ordinateur et ouvrez l’onglet **Configurer**.
2. Vous pouvez modifier le nom qui sera attribué à l'ordinateur dans Azure après le basculement, ainsi que la taille d’ordinateur virtuel Azure. Vous pouvez également sélectionner le réseau Azure auquel l'ordinateur sera connecté après le basculement.

	![Définir les propriétés des ordinateurs virtuels](./media/site-recovery-vmware-to-azure/ASRVMWare_VMProperties.png)

Notez les points suivants :

- Le nom de l’ordinateur Azure doit satisfaire aux exigences Azure.
- Par défaut, les machines virtuelles répliquées dans Azure ne sont pas connectées à un réseau Azure. Si vous souhaitez que des machines virtuelles communiquent, veillez à définir le même réseau Azure pour chacune d'elles.
- Si vous redimensionnez un volume sur un serveur physique ou un ordinateur virtuel VMware, il passe dans un état critique. Si vous n'avez pas besoin de modifier la taille, procédez comme suit :

	- a) Modifiez le paramètre de taille.
	- b) Dans l’onglet **Machines virtuelles**, sélectionnez la machine virtuelle et cliquez sur **Supprimer**.
	- c) Dans **Supprimer la machine virtuelle**, sélectionnez l’option **Désactiver la protection (à utiliser pour l’exploration de la récupération et le redimensionnement du volume)**. Cette option désactive la protection, mais conserve les points de récupération dans Azure.

		![Définir les propriétés des ordinateurs virtuels](./media/site-recovery-vmware-to-azure/ASRVMWare_RemoveVM.png)

	- d) Réactivez la protection de l’ordinateur virtuel. Lorsque vous réactiverez la protection, les données du volume redimensionné seront transférées vers Azure.

	

## Étape 10 : exécuter un basculement

Actuellement, vous ne pouvez exécuter que des basculements non planifiés pour les serveurs physiques et les ordinateurs virtuels VMware protégés. Notez les points suivants :



- Avant d'initier un basculement, assurez-vous que le serveur de configuration et le serveur cible maître sont en cours d'exécution et sains. Le basculement échouera dans le cas contraire.
- Les ordinateurs sources ne sont pas arrêtés dans le cadre d'un basculement non planifié. L’exécution d’un basculement non planifié arrête la réplication des données pour les serveurs protégés. Vous devez retirer les ordinateurs du groupe de protection et les ajouter à nouveau pour les protéger à nouveau une fois le basculement non planifié terminé.
- Si vous souhaitez basculer sans perdre de données, assurez-vous que les ordinateurs virtuels de site principal sont désactivés avant de lancer le basculement.

1. Sur la page **Plans de récupération**, ajoutez un plan de récupération. Spécifiez les détails du plan et sélectionnez **Azure** comme cible.

	![Configurer le plan de récupération](./media/site-recovery-vmware-to-azure/ASRVMWare_RP1.png)

2. Dans **Sélectionner les machines virtuelles**, sélectionnez un groupe de protection, puis sélectionnez les ordinateurs du groupe à ajouter au plan de récupération. [Découvrez plus d’informations](site-recovery-create-recovery-plans.md) sur les plans de récupération.

	![Ajouter des machines virtuelles](./media/site-recovery-vmware-to-azure/ASRVMWare_RP2.png)

3. Si nécessaire, vous pouvez personnaliser le plan pour créer des groupes et définir l'ordre dans lequel les ordinateurs du plan de récupération sont basculés. Vous pouvez également ajouter des invites pour des actions manuelles et des scripts. Lors de la récupération vers Azure, les scripts peuvent être ajoutés à l’aide des [runbooks Azure Automation](site-recovery-runbook-automation.md).

4. Sur la page **Plans de récupération**, sélectionnez le plan et cliquez sur **Basculement non planifié**.
5. Dans **Confirmer le basculement**, vérifiez le sens du basculement (Vers Azure) et sélectionnez le point de récupération vers lequel basculer.
6. Attendez que la tâche de basculement soit terminée, puis vérifiez que le basculement a fonctionné comme prévu et que les machines virtuelles répliquées démarrent dans Azure.




## Étape 11 : restaurer automatiquement les ordinateurs basculés depuis Azure

[Découvrez plus d’informations](site-recovery-failback-azure-to-vmware.md) sur comment restaurer dans votre environnement local vos ordinateurs basculés s’exécutant dans Azure.


## Gérer vos serveurs de traitement

Le serveur de traitement envoie des données de réplication au serveur cible maître dans Azure et détecte les nouveaux ordinateurs virtuels VMware ajoutés à un serveur vCenter. Dans les cas suivants, vous souhaitez modifier le serveur de traitement dans votre déploiement :

- Si le serveur de traitement actuel tombe en panne
- Si votre objectif de point de récupération (RPO) atteint un niveau inacceptable pour votre organisation.

Si nécessaire, vous pouvez déplacer la réplication de quelques-uns ou l’ensemble de vos serveurs physiques ou ordinateurs virtuels VMware locaux vers un autre serveur de traitement. Par exemple :

- **Échec** : si un serveur de traitement tombe en panne ou n’est pas disponible, vous pouvez déplacer la réplication des ordinateurs protégés vers un autre serveur de traitement. Les métadonnées de l'ordinateur source et de l'ordinateur de réplication seront déplacées vers le nouveau serveur de traitement et les données sont resynchronisées. Le nouveau serveur de traitement se connecte automatiquement au serveur vCenter pour effectuer la détection automatique. Vous pouvez surveiller l'état des serveurs de traitement sur le tableau de bord de Site Recovery.
- **Équilibrage de la charge pour ajuster le RPO** : pour améliorer l’équilibrage de la charge, vous pouvez sélectionner un autre serveur de traitement dans le portail de Site Recovery et y déplacer la réplication d’un ou plusieurs ordinateurs en vue d’un équilibrage de charge manuel. Dans ce cas, les métadonnées de l’ordinateur source et de l’ordinateur de réplication sélectionnés sont déplacées vers le nouveau serveur de traitement. Le serveur de traitement d'origine reste connecté au serveur vCenter. 

### Surveiller le serveur de traitement.

Si un serveur de traitement est dans un état critique, un avertissement d'état s'affiche sur le tableau de bord de Site Recovery. Vous pouvez cliquer sur l'état pour ouvrir l'onglet Événements et accéder ensuite à des tâches spécifiques dans l'onglet Tâches.

### Modifier le serveur de traitement utilisé pour la réplication

1. Accédez à la page **SERVEURS DE CONFIGURATION** sous **SERVEURS**
2. Cliquez sur le nom du serveur de configuration et accédez aux **Détails du serveur**.
3. Dans la liste **Serveurs de traitement**, cliquez sur **Modifier le serveur de traitement** à côté du serveur que vous souhaitez modifier. ![Modifier le serveur de traitement 1](./media/site-recovery-vmware-to-azure/ASRVMware_ChangePS1.png)
4. Dans la boîte de dialogue **Modifier le serveur de traitement**, sélectionnez le nouveau serveur dans **Serveur de traitement cible**, puis sélectionnez les ordinateurs virtuels que vous souhaitez répliquer vers le nouveau serveur. Cliquez sur l’icône d’informations à côté du nom de serveur pour obtenir des informations le concernant, telles que l’espace libre ou la mémoire utilisée. L’espace moyen requis pour répliquer chaque ordinateur virtuel sélectionné vers le nouveau serveur de traitement s’affiche pour vous aider à prendre des décisions quant à la charge. ![Modifier le serveur de traitement 2](./media/site-recovery-vmware-to-azure/ASRVMware_ChangePS2.png)
5. Cliquez sur la coche pour commencer la réplication vers le nouveau serveur de traitement. Si vous supprimez tous les ordinateurs virtuels d'un serveur de traitement qui était essentiel, il ne doit plus afficher un avertissement critique dans le tableau de bord.


## Informations et remarques relatives aux logiciels tiers

Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”). Microsoft is the not original author of the Third Party Code. The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only. This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

Le fichier complet se trouve dans le [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

<!---HONumber=August15_HO6-->
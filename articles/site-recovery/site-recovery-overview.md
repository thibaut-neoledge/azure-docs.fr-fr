<properties
	pageTitle="Vue d’ensemble de Microsoft Azure Site Recovery" 
	description="Microsoft Azure Site Recovery coordonne la réplication, le basculement et la récupération des machines virtuelles et serveurs physiques situés en local sur Microsoft Azure, ou sur un site local secondaire." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>


<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="05/10/2015" 
	ms.author="raynew"/>


#  Vue d’ensemble de Microsoft Azure Site Recovery

Le service Site Recovery offre une solution fiable de continuité d’activité et de récupération d’urgence, qui protège vos machines virtuelles et vos serveurs physiques locaux en orchestrant et en automatisant la réplication et le basculement vers Microsoft Azure ou un centre de données secondaire local.

- **Simplification** : Microsoft Azure Site Recovery vous permet de simplifier votre stratégie de continuité d’activité et de récupération d’urgence en facilitant la configuration de la réplication et en exécutant des tâches de basculement et de récupération pour les applications et charges de travail locales.
- **Réplication** : vous pouvez répliquer des charges de travail locales vers Microsoft Azure Storage ou un centre de données secondaire. 
- **Coffre** : pour gérer la réplication, vous configurez un coffre Microsoft Azure Site Recovery dans la région Microsoft Azure que vous sélectionnez. Toutes les métadonnées sont conservées dans cette région.
- **Métadonnées** : aucune donnée d’application n’est envoyée à Microsoft Azure. Microsoft Azure Site Recovery requiert certaines métadonnées, telles que les noms des clouds VMM et des machines virtuelles, pour orchestrer le basculement et la réplication. 
- **Connexion à Microsoft Azure** : les serveurs de gestion communiquent avec Microsoft Azure en fonction de votre scénario de déploiement. Par exemple, si vous effectuez une réplication des machines virtuelles situées sur un cloud VMM local, le serveur VMM communique avec Site Recovery via une connexion HTTPS sortante chiffrée. Aucune connexion n’est requise sur les machines virtuelles ou les hôtes Hyper-V.
- **Réplica Hyper-V** : Microsoft Azure Site Recovery s’appuie sur le réplica Hyper-V pour effectuer la gestion du processus de réplication. Il peut également utiliser la réplication SAN si vous effectuez une réplication entre deux sites VMM locaux. Site Recovery utilise la réplication intelligente : seuls les blocs de données sont répliqués, et non l’ensemble du disque dur virtuel, lors de la réplication initiale. Lors de la réplication en continu, seul le delta des changements est répliqué. Site Recovery prend en charge le transfert de données hors ligne et utilise des optimiseurs WAN.
- **Tarification** : vous pouvez [découvrir plus d’informations](pricing/details/site-recovery) sur la tarification relative à Site Recovery.


## Scénarios de déploiement

Ce tableau récapitule les scénarios de réplication pris en charge par Site Recovery.

**Réplication vers** | **Réplication depuis (en local)** | **Détails** | **Article**
---|---|---|---
Microsoft Azure | Site Hyper-V | Répliquez une machine virtuelle sur un ou plusieurs serveurs hôtes Hyper-V locaux, définis en tant que sites Hyper-V sur Microsoft Azure. Aucun serveur VMM n’est requis. | [En savoir plus](site-recovery-hyper-v-site-to-azure.md)
Microsoft Azure| Serveur VMM | Répliquez des machines virtuelles sur un ou plusieurs serveurs hôtes Hyper-V locaux, situés sur un cloud VMM dans Microsoft Azure. | [En savoir plus](site-recovery-vmm-to-azure.md) 
Microsoft Azure | Serveur Windows physique | Répliquez un serveur Windows ou Linux physique vers Microsoft Azure. | [En savoir plus](site-recovery-vmware-to-azure.md)
Microsoft Azure | Machine virtuelle VMware | Répliquez des machines virtuelles VMware sur Microsoft Azure. | [En savoir plus](site-recovery-vmware-to-azure.md)
Centre de données secondaire | Serveur VMM | Répliquez des machines virtuelles sur des serveurs hôtes Hyper-V locaux situés sur un cloud VMM vers un serveur VMM secondaire, dans un autre centre de données. | [En savoir plus](site-recovery-vmm-to-vmm.md)
Centre de données secondaire | Serveur VMM avec SAN | Répliquez des machines virtuelles sur des serveurs hôtes Hyper-V locaux situés sur un cloud VMM vers un serveur VMM secondaire, dans un autre centre de données, au moyen de la réplication SAN.| [En savoir plus](site-recovery-vmm-san.md)
Centre de données secondaire | Serveur VMM unique | Répliquez des machines virtuelles sur des serveurs hôtes Hyper-V locaux situés sur un cloud VMM vers un serveur VMM secondaire, sur le même serveur VMM. | [En savoir plus](site-recovery-single-vmm.md) 


## Aide relative à la charge de travail

Les technologies de réplication ASR sont compatibles avec n’importe quelle application en cours d’exécution sur une machine virtuelle. Nous avons effectué des tests supplémentaires en partenariat avec les équipes chargées des produits d’application, afin d’assurer la prise en charge complète de chaque application.

**Charge de travail** | <p>**Repliquer machines virtuelles Hyper-V**</p> <p>**(vers le site secondaire)**</p> | <p>**Répliquer machines virtuelles Hyper-V**</p> <p>**(vers Azure)**</p> | <p>**Répliquer machines virtuelles VMware**</p> <p>**(vers site secondaire)**</p> | <p>**Répliquer machines virtuelles VMware**</p><p>**(vers Azure)****</p> ---|---|---|---|--- Active Directory, DNS | Y | Y | Y | Applications Web bientôt disponibles (IIS, SQL) | Y | Y | Y | SCOM bientôt disponible | Y | Y | Y | Sharepoint bientôt disponible | Y | Y | Y | Bientôt disponible <p>SAP</p><p>Répliquer site SAP vers Azure pour non cluster</p> | Y (testé par Microsoft) | Y (testé par Microsoft) | Y (testé Microsoft) | Exchange (non-DAG) bientôt disponible | Y | Bientôt tranquille | Y | Remote Desktop/VDI bientôt disponible | Y | Y | Y | Bientôt disponible <p>Linux</p> <p>(système d’exploitation et applications)</p> | Y (testé par Microsoft) | Y (testé par Microsoft) | Y (testé par Microsoft) | Dynamics AX bientôt disponible | Y | Y | Y | Dynamics CRM bientôt disponible | Bientôt disponible | Bientôt disponible | Y | Oracle bientôt disponible | Bientôt disponible | Bientôt disponible | Y (testé par Microsoft) | Bientôt disponible


## Fonctionnalités et conditions requises 

Ce tableau récapitule les principales fonctionnalités du logiciel Site Recovery et indique de quelle manière elles sont traitées lors de la réplication vers Microsoft Azure, de la réplication vers un site secondaire au moyen de la réplication via le Réplica Hyper-V par défaut, et via SAN.

Fonctionnalité|Réplication vers Microsoft Azure|Réplication vers un site secondaire (Réplica Hyper-V)|Réplication vers un site secondaire (SAN)
---|---|---|---
Réplication de données|Les métadonnées relatives aux serveurs locaux et aux machines virtuelles sont stockées dans le coffre de récupération des sites.</p> <p>Les données répliquées sont stockées dans le stockage Azure.|Les métadonnées relatives aux serveurs locaux et aux machines virtuelles sont stockées dans le coffre de récupération des sites.</p> <p>Les données répliquées sont stockées à l’emplacement spécifié par le serveur Hyper-V cible.|Les métadonnées relatives aux serveurs locaux et aux machines virtuelles sont stockées dans le coffre de récupération des sites.</p> <p>Les données répliquées sont stockées dans le stockage à baies cible.
Conditions requises pour les coffres|Compte Microsoft Azure (avec service Site Recovery).|Compte Microsoft Azure (avec service Site Recovery).|Compte Microsoft Azure (avec service Site Recovery).
Réplication|Répliquer la machine virtuelle à partir de l’hôte Hyper-V source vers Microsoft Azure Storage. Récupération automatique vers l’emplacement source.|Réplication de la machine virtuelle à partir de l’hôte Hyper-V source vers un hôte Hyper-V cible. Récupération automatique vers l’emplacement source.|Réplication des machines virtuelles depuis l’appareil de stockage SAN source vers un appareil SAN cible. Récupération automatique vers l’emplacement source.
Machine virtuelle|Disque dur de machine virtuelle stocké dans un stockage Azure|Disque dur de machine virtuelle stocké sur un hôte Hyper-V.|Disque dur de machine virtuelle stocké dans une baie de stockage SAN.
Stockage Azure|Requis pour stocker les disques durs des machines virtuelles répliquées.|Non applicable|Non applicable
Baie de stockage SAN|Non applicable|Non applicable|Une baie de stockage SAN doit être disponible sur le site source comme sur le site cible. Elle doit être gérée par VMM.
Serveur VMM|Un seul serveur VMM est requis sur le site source uniquement.|L’utilisation de serveurs VMM sur les sites source et cible est recommandée. Vous pouvez effectuer la réplication entre des clouds sur un seul serveur VMM.|Présence d’un serveur VMM sur les sites VMM source et cible. Les clouds doivent contenir au moins un cluster Hyper-V.
Version de VMM|System Center 2012 R2<p>System Center 2012 with SP1|System Center 2012 R2|System Center 2012 R2 avec VMM Update Rollup 5.0
Configuration des serveurs VMM|Configurer des clouds sur des sites sources et cible</p><p>Configurer des réseaux de machines virtuelles sur le site source et cible<p>Configurer les classifications de stockage sur les sites sources et cibles <p>Installer le fournisseur sur les serveurs VMM source et cible|Configurer les clouds sur le site source</p><p>Configurer le stockage SAN</p><p>Configurer les réseaux de machines virtuelles sur le site source</p><p>Installer le fournisseur sur le serveur VMM source</p><p>Activer la protection de la machine virtuelle|Configurer des clouds sur des sites sources et cible</p><p>Configurer des réseaux de machines virtuelles sur les sites sources et cibles</p><p>Installer le fournisseur sur les serveurs VMM source et cible</p><p>Activer la protection des machines virtuelles
Fournisseur Azure Site Recovery</p><p>Utilisé pour la connexion à Site Recovery, via le protocole HTTPS|Installer sur le serveur VMM source|Installer sur les serveurs VMM source et cible|Installer sur les serveurs VMM source et cible
Agent Azure Site Recovery</p><p>Utilisé pour la connexion à Site Recovery via HTTPS|Installer sur les serveurs hôtes Hyper-V|Non requis|Non requis
Points de récupération de machine virtuelle|Définir les points de récupération par heure.</p> <p>Détermine la période de conservation d’un point de récupération (entre 0 et 24 heures).|Définir les points de récupération par quantité.</p> <p>Spécifie combien de points de récupération supplémentaires doivent être conservés (0-15). Par défaut, un point de récupération est créé toutes les heures.|Configuré dans les paramètres de stockage sur baie.
Mappage réseau|Mapper les réseaux de machines virtuelles vers des réseaux Azure.</p> <p>Le mappage réseau est l’assurance que toutes les machines virtuelles qui basculent dans le même réseau de machines virtuelles source peuvent se connecter après le basculement. Par ailleurs, si une passerelle réseau est configurée sur le réseau Azure cible, les machines virtuelles peuvent se connecter aux machines virtuelles locales. </p><p>Si le mappage n’est pas activé, seules les machines virtuelles qui basculent dans le même plan de récupération peuvent se connecter l’une à l’autre après le basculement vers Azure.|Mapper les réseaux de machines virtuelles sources aux réseaux de machines virtuelles cibles.</p> <p>Le mappage réseau est utilisé pour placer les machines virtuelles répliquées sur des serveurs hôtes Hyper-V optimaux et veiller à ce que les machines virtuelles associées au réseau de machines virtuelles source soient associées au réseau cible mappé après le basculement. </p><p>Si le mappage n’est pas activé, les machines virtuelles répliquées ne sont pas connectées au réseau.|Mapper les réseaux de machines virtuelles sources aux réseaux de machines virtuelles cibles.</p> <p>Le mappage réseau garantit que les machines virtuelles associées au réseau de machines virtuelles source sont associées au réseau cible mappé après le basculement. </p><p>Si le mappage n’est pas activé, les machines virtuelles répliquées ne sont pas connectées au réseau.
Mappage de stockage|Non applicable|Mappe les classifications de stockage sur les serveurs VMM sources aux classifications de stockage sur les serveurs VMM cibles.</p> <p>Lorsque le mappage est activé, les disques durs des machines virtuelles de la classification de stockage source sont situés dans la classification de stockage cible après le basculement.</p><p>Si le mappage de stockage n’est pas activé, les disques durs virtuels répliqués sont stockés dans l’emplacement par défaut, sur le serveur hôte Hyper-V cible.|Mappages entre les baies et les pools de stockage des sites principal et secondaire.

## Étapes suivantes

Une fois que vous avez fini de consulter cette présentation, [lisez les meilleures pratiques](site-recovery-best-practices.md) pour vous aider à prendre en main la planification du déploiement.
 

<!---HONumber=August15_HO6-->
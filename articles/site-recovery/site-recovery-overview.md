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

**Charge de travail** | <p>**Réplication de VM Hyper-V**</p> <p>**(vers un site secondaire)**</p> | <p>**Réplication de VM Hyper-V**</p> <p>**(vers Microsoft Azure)**</p> | <p>**Réplication de VM VMware**</p> <p>**(vers un site secondaire)**</p> | <p>**Réplication de VM VMware**</p><p>**(vers Microsoft Azure)**</p>
---|---|---|---|---
Active Directory, DNS | O | O | O | Bientôt disponible 
Applications web (IIS, SQL) | O | O | O | Bientôt disponible
SCOM | O | O | O | Bientôt disponible
SharePoint | O | O | O | Bientôt disponible
<p>SAP</p><p>Réplication d’un site SAP vers Microsoft Azure (aucun cluster)</p> | O (opération testée par Microsoft) | O (opération testée par Microsoft) | O (opération testée par Microsoft) | Bientôt disponible
Microsoft Exchange (aucun DAG) | O | Bientôt disponible | O | Bientôt disponible
Bureau à distance/VDI | O | O | O | Bientôt disponible 
<p>Linux</p> <p>(système d’exploitation et applications)</p> | O (opération testée par Microsoft) | O (opération testée par Microsoft) | O (opération testée par Microsoft) | Bientôt disponible 
Dynamics AX | O | O | O | Bientôt disponible
Dynamics CRM | Bientôt disponible | Bientôt disponible | O | Bientôt disponible
Oracle | Bientôt disponible | Bientôt disponible | O (opération testée par Microsoft) | Bientôt disponible


## Fonctionnalités et conditions requises 

Ce tableau récapitule les principales fonctionnalités du logiciel Site Recovery et indique de quelle manière elles sont traitées lors de la réplication vers Microsoft Azure, de la réplication vers un site secondaire au moyen de la réplication via le Réplica Hyper-V par défaut, et via SAN.

<table border="1">
<thead>
<tr>
	<th>Fonctionnalité</th><th>Réplication vers Microsoft&#160;Azure</th>
	<th>Réplication vers un site secondaire (Réplica&#160;Hyper-V)</th>
	<th>Réplication vers un site secondaire (SAN)</th>
</tr>
</thead>

<tr>
<td>Réplication de données</td>
<td><p>Les métadonnées sur les serveurs locaux et les machines virtuelles sont stockées dans le coffre de Site&#160;Recovery.</p> <p>Les données répliquées sont stockées dans le stockage Azure.</p></td>
<td><p>Les métadonnées sur les serveurs locaux et les machines virtuelles sont stockées dans le coffre de Site&#160;Recovery.</p> <p>Les données répliquées sont stockées à l’emplacement spécifié par le serveur Hyper-V cible.</p></td>
<td><p>Les métadonnées sur les serveurs locaux et les machines virtuelles sont stockées dans le coffre de Site&#160;Recovery.</p> <p>Les données répliquées sont stockées dans le stockage à baies cible.</p></td>
</tr>

<tr>
<td>Conditions requises pour les coffres</td>
<td><p>Compte Microsoft&#160;Azure (avec service Site&#160;Recovery).</p></td>
<td><p>Compte Microsoft&#160;Azure (avec service Site&#160;Recovery).</p>
</td><td><p>Compte Microsoft&#160;Azure (avec service Site&#160;Recovery).</p></td>
</tr>

<tr>
<td>Réplication</td>
<td>Répliquer la machine virtuelle à partir de l’hôte Hyper-V source vers Microsoft&#160;Azure&#160;Storage. Récupération automatique vers l’emplacement source.</td>
<td>Réplication de la machine virtuelle à partir de l’hôte Hyper-V source vers un hôte Hyper-V cible. Récupération automatique vers l’emplacement source.</td>
<td>Réplication des machines virtuelles depuis l’appareil de stockage&#160;SAN source vers un appareil&#160;SAN cible. Récupération automatique vers l’emplacement source.</td>
</tr>

<tr>
<td>Machine virtuelle</td>
<td>Disque dur de machine virtuelle stocké dans un stockage Azure</td>
<td>Disque dur de machine virtuelle stocké sur un hôte Hyper-V.</td>
<td>Disque dur de machine virtuelle stocké dans une baie de stockage&#160;SAN.</td>
</tr>

<tr>
<td>Stockage Azure</td>
<td>Requis pour stocker les disques durs des machines virtuelles répliquées.</td>
<td>Non applicable</td>
<td>Non applicable</td>
</tr>

<tr>
<td>Baie de stockage SAN</td>
<td><p>Non applicable</p></td>
<td>Non applicable</td>
<td>Une baie de stockage&#160;SAN doit être disponible sur le site source comme sur le site cible. Elle doit être gérée par VMM.</td>
</tr>

<tr>
<td>Serveur VMM</td>
<td>Un seul serveur&#160;VMM est requis sur le site source uniquement. </td>
<td>L’utilisation de serveurs&#160;VMM sur les sites source et cible est recommandée. Vous pouvez effectuer la réplication entre des clouds sur un seul serveur&#160;VMM.</td>
<td>Présence d’un serveur&#160;VMM sur les sites&#160;VMM source et cible. Les clouds doivent contenir au moins un cluster Hyper-V.</td>
</tr>

<tr>
<td>Version de VMM</td>
<td>System Center&#160;2012&#160;R2</td>
<td><p>System Center&#160;2012 avec SP1</p><p>System Center&#160;2012&#160;R2</p></td>
<td><p>System Center 2012 R2 avec VMM Update Rollup&#160;5.0</p></td>
</tr>

<tr>
<td>Configuration des serveurs VMM</td>
<td><p>Configurer des clouds dans les sites source et cible</p><p>Configurer des réseaux de machines virtuelles dans les sites source et cible</p><p>Configurer des classifications de stockage dans les sites source et cible <p>Installer le fournisseur sur les serveurs VMM source et cible</p></td>
<td><p>Configurer des clouds dans le site source</p><p>Configurer un stockage SAN</p><p>Configurer des réseaux de machines virtuelles dans le site source</p><p>Installer le fournisseur sur le serveur VMM source</p><p>activer la protection des machines virtuelles</p></td>
<td><p>Configurer des clouds dans les sites source et cible</p><p>Configurer des réseaux de machines virtuelles dans les sites source et cible</p><p>Installer le fournisseur sur les serveurs VMM source et cible</p><p>activer la protection des machines virtuelles</p></td>
</tr>

<tr>
<td><p>Fournisseur Azure Site Recovery</p><p>Utilisé pour la connexion à Site&#160;Recovery, via le protocole&#160;HTTPS</p></td>
<td>Installer sur le serveur VMM source</td>
<td>Installer sur les serveurs VMM source et cible</td>
<td>Installer sur les serveurs VMM source et cible</td>
</tr>

<tr>
<td><p>Agent Azure Recovery Services</p><p>Utilisé pour la connexion à Site&#160;Recovery, via le protocole&#160;HTTPS</p></td>
<td>Installer sur les serveurs hôtes Hyper-V</td>
<td>Non requis</td>
<td>Non requis</td>
</tr>

<tr>
<td>Points de récupération de machine virtuelle</td>
<td><p>Définissez les points de récupération par heure.</p> <p>Détermine la période de conservation d’un point de récupération (entre 0 et 24&#160;heures).</p></td>
<td><p>Définissez les points de récupération par quantité.</p> <p>Spécifie combien de points de récupération supplémentaires doivent être conservés (0-15). Par défaut, un point de récupération est créé toutes les heures.</p></td>
<td>Configuré dans les paramètres de stockage sur baie.</td>
</tr>

<tr>
<td>Mappage réseau</td>
<td><p>Mappage de réseaux de machines virtuelles vers des réseaux Azure.</p> <p>Le mappage réseau est l’assurance que toutes les machines virtuelles qui basculent dans le même réseau de machines virtuelles source peuvent se connecter après le basculement. Par ailleurs, si une passerelle réseau est configurée sur le réseau Azure cible, les machines virtuelles peuvent se connecter aux machines virtuelles locales. </p><p>Si le mappage n’est pas activé, seules les machines virtuelles qui basculent dans le même plan de récupération peuvent se connecter l’une à l’autre après le basculement vers Azure.</p></td>
<td><p>Mappez les réseaux de machines virtuelles sources aux réseaux de machines virtuelles cibles.</p> <p>Le mappage réseau est utilisé pour placer les machines virtuelles répliquées sur des serveurs hôtes Hyper-V optimaux et veiller à ce que les machines virtuelles associées au réseau de machines virtuelles source soient associées au réseau cible mappé après le basculement. </p><p>Si le mappage n’est pas activé, les machines virtuelles répliquées ne sont pas connectées au réseau.</p></td>
<td><p>Mappez les réseaux de machines virtuelles sources aux réseaux de machines virtuelles cibles.</p> <p>Le mappage réseau garantit que les machines virtuelles associées au réseau de machines virtuelles source sont associées au réseau cible mappé après le basculement. </p><p>Si le mappage n’est pas activé, les machines virtuelles répliquées ne sont pas connectées au réseau.</p></td>
</tr>

<tr>
<td>Mappage de stockage</td>
<td>Non applicable</td>
<td><p>Mappe les classifications de stockage sur les serveurs VMM sources aux classifications de stockage sur les serveurs VMM cibles.</p> <p>Lorsque le mappage est activé, les disques durs des machines virtuelles dans la classification de stockage source sont situés dans la classification de stockage cible après le basculement.</p><p>Si le mappage de stockage n’est pas activé, les disques durs virtuels répliqués sont stockés à l’emplacement par défaut sur le serveur hôte Hyper-V cible.</p></td>
<td><p>Mappages entre les baies et les pools de stockage des sites principal et secondaire.</p></td>
</tr>

</table>

## Étapes suivantes

Une fois que vous avez fini de consulter cette présentation, [lisez les meilleures pratiques](site-recovery-best-practices.md) pour vous aider à prendre en main la planification du déploiement.
 

<!---HONumber=July15_HO4-->
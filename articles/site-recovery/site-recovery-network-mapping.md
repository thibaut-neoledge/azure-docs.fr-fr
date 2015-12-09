<properties
	pageTitle="Préparer le mappage réseau pour la protection des machines virtuelles Hyper-V avec VMM dans Azure Site Recovery | Microsoft Azure"
	description="Configurez le mappage réseau pour la réplication de machines virtuelles Hyper-V à partir d’un centre de données local sur Azure ou sur un site secondaire."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="12/01/2015"
	ms.author="raynew"/>


# Préparer le mappage réseau pour la protection des machines virtuelles Hyper-V avec VMM dans Azure Site Recovery

Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité des activités et de récupération d'urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles et de serveurs physiques.

Cet article décrit le mappage réseau, qui vous aide à configurer de façon optimale les paramètres réseau lorsque vous utilisez Site Recovery pour répliquer des machines virtuelles Hyper-V situées dans des clouds VMM entre deux centres de données locaux ou entre le centre de données local et Azure. Notez que si vous répliquez des machines virtuelles Hyper-V sans cloud VMM, ou que si vous répliquez des machines virtuelles VMware ou des serveurs physiques, cet article ne s’applique pas.

Après avoir lu cet article, posez des questions sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)


## Vue d'ensemble

Le mappage réseau est utilisé lorsqu’Azure Site Recovery est déployé pour répliquer des machines virtuelles Hyper-V sur Azure ou sur un centre de données secondaire à l’aide de Réplica Hyper-V ou de la réplication SAN.

- **Machines virtuelles Hyper-V de réplication dans les clouds VMM entre deux centres de données locaux** : le mappage réseau effectue le mappage entre des réseaux de machines virtuelles sur un serveur VMM source et des réseaux de machines virtuelles sur un serveur VMM cible, afin d’obtenir les résultats décrits ci-après.

	- **Connexion des machines virtuelles après basculement** : le système vérifie que les machines virtuelles sont connectées aux réseaux appropriés après le basculement. L’ordinateur virtuel de réplication sera connecté au réseau cible mappé au réseau source.
	- **Positionnement des ordinateurs virtuels de réplication sur les serveurs hôtes** : les ordinateurs virtuels de réplication sont positionnés de manière optimale sur les serveurs hôtes Hyper-V. Ces ordinateurs sont placés sur des hôtes ayant accès aux réseaux de machines virtuelles mappés.
	- **Aucun mappage réseau** : si vous ne configurez pas le mappage réseau, les machines virtuelles répliquées ne sont pas connectés aux réseaux de machines virtuelles après le basculement.

- **Réplication de machines virtuelles Hyper-V dans un cloud VMM local vers Azure** : le mappage réseau effectue un mappage entre les réseaux de machines virtuelles sur le serveur VMM source et les réseaux Azure cible, afin d’obtenir les résultats décrits ci-après.
	- **Connexion des machines virtuelles après le basculement** : toutes les machines qui basculent sur le même réseau peuvent se connecter les unes aux autres, quel que soit le plan de récupération qui leur est associé.
	- **Passerelle réseau** : si une passerelle réseau est configurée sur le réseau Azure cible, les machines virtuelles peuvent se connecter à d’autres machines virtuelles locales.
	- **Aucun mappage réseau** : si vous ne configurez pas le mappage réseau, seules les machines virtuelles ayant basculé au sein d’un même plan de récupération peuvent se connecter les unes aux autres après le basculement vers Azure.


## Exemple de mappage réseau

Le mappage réseau peut être configuré entre des réseaux de machines virtuelles sur deux serveurs VMM, ou sur un seul serveur VMM, lorsque deux sites sont gérés par le même serveur. Lorsque le mappage réseau est correctement configuré et que la réplication est activée, une machine virtuelle située à l’emplacement principal est connectée à un réseau, et son réplica (à l’emplacement cible) est connecté à son réseau mappé.

Si les réseaux ont été correctement configurés dans VMM, lorsque vous sélectionnez un réseau de machines virtuelles cible dans le cadre du mappage réseau, les clouds sources VMM qui utilisent ce réseau de machines virtuelles source seront affichés, ainsi que les réseaux de machines virtuelles cibles disponibles sur les clouds cibles à utiliser pour la protection.

Voici un exemple permettant d’illustrer ce processus. Prenons l’exemple d’une entreprise ayant ouvert deux bureaux, l’un à New York et l’autre à Chicago.

**Emplacement** | **Serveur VMM** | **Réseaux de machines virtuelles** | **Mappés à**
---|---|---|---
New York | VMM-NewYork| VMNetwork1-NewYork | Mappé au réseau VMNetwork1-Chicago
 | | VMNetwork2-NewYork | Non mappé
Chicago | VMM-Chicago| VMNetwork1-Chicago | Mappé au réseau VMNetwork1-NewYork
 | | VMNetwork1-Chicago | Non mappé

Dans cet exemple :

- Lorsqu’un ordinateur virtuel de réplication est créé pour une machine virtuelle connectée au réseau VMNetwork1-NewYork, il est connecté au réseau VMNetwork1-Chicago.
- Lorsqu’un ordinateur virtuel de réplication est créé pour le réseau VMNetwork2-NewYork ou VMNetwork2-Chicago, il n’est pas connecté à un réseau.

Voici comment les clouds VMM sont configurés dans notre exemple d’organisation, ainsi que les réseaux logiques associés aux clouds.

### Paramètres de protection des clouds

**Cloud protégé** | **Cloud de protection** | **Réseau logique (New York)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>N/D</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>N/D</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

### Paramètres des réseaux de machines virtuelles et des réseaux logiques

**Emplacement** | **Réseau logique** | **Réseau de machines virtuelles associé**
---|---|---
New York | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

### Réseaux cibles

Selon ces paramètres, lorsque vous sélectionnez le réseau de machines virtuelles cible, le tableau suivant répertorie les choix disponibles.

**Sélection** | **Cloud protégé** | **Cloud de protection** | **Réseau cible disponible**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Disponible
 | GoldCloud1 | GoldCloud2 | Disponible
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | Non disponible
 | GoldCloud1 | GoldCloud2 | Disponible



## Plusieurs sous-réseaux

Si le réseau cible est associé à plusieurs sous-réseaux et que l’un d’eux présente le même nom que le sous-réseau dans lequel se trouve la machine virtuelle source, l’ordinateur virtuel de réplication est connecté à ce sous-réseau cible après le basculement. S’il n’existe aucun sous-réseau cible avec un nom correspondant, la machine virtuelle sera connectée au premier sous-réseau du réseau.


### Restauration automatique

Pour voir ce qui se produit en cas de restauration automatique (réplication inverse), supposons que le réseau VMNetwork1-NewYork est mappé au réseau VMNetwork1-Chicago, avec les paramètres suivants.


**Machine virtuelle** | **Connectée au réseau de machines virtuelles**
---|---
MV1 | VMNetwork1-Network
VM2 (réplica de VM1) | VMNetwork1-Chicago

Examinons ce qui se passe dans différents scénarios possibles avec ces paramètres.

**Scénario** | **Résultat**
---|---
Aucune modification n’est apportée aux propriétés du réseau de la machine VM2 après le basculement. | La machine VM1 reste connectée au réseau source.
Les propriétés du réseau de la machine VM2 sont modifiées après le basculement ; la machine est déconnectée. | La machine VM1 est déconnectée.
Les propriétés du réseau de la machine VM2 sont modifiées après le basculement ; la machine est connectée au réseau VMNetwork2-Chicago. | Si le réseau VMNetwork2-Chicago n’est pas mappé, la machine VM1 est déconnectée.
Le mappage réseau de VMNetwork1-Chicago est modifié. | La machine VM1 est connectée au réseau désormais mappé à VMNetwork1-Chicago.


## Étapes suivantes

Maintenant que vous comprenez mieux le mappage réseau, consultez [Meilleures pratiques du déploiement de Site Recovery](site-recovery-best-practices.md).

<!---HONumber=Nov15_HO4-->
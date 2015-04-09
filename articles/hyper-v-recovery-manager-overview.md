<properties 
	pageTitle="Vue d'ensemble d'Azure Site Recovery" 
	description="Déployez Azure Site Recovery pour protéger les machines virtuelles locales sur un autre site local ou sur Azure." 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="raynew"/>

# Vue d'ensemble d'Azure Site Recovery

<div class="dev-callout"> 
<p>Azure Site Recovery orchestre la réplication et le basculement dans de nombreux scénarios :</p>


<ul>
<li>**Protection entre un site local Hyper-V et Azure avec la réplication Hyper-V** : orchestre la réplication, le basculement et la récupération à partir d'un site local doté d'un ou plusieurs serveurs Hyper-V, mais sans System Center VMM. Les données des machines virtuelles sont répliquées d'un serveur hôte Hyper-V source vers Azure. Consultez <a href="http://go.microsoft.com/fwlink/?LinkId=522298">Prise en main d'Azure Site Recovery : protection entre un site local Hyper-V et Azure avec la réplication Hyper-V</a>.</li>
<li>**Protection entre deux sites locaux VMM avec la réplication Hyper-V** : orchestre la réplication, le basculement et la récupération entre les sites locaux VMM. Les données des machines virtuelles sont répliquées d'un serveur hôte Hyper-V source vers un serveur hôte cible. Consultez <a href="http://go.microsoft.com/fwlink/?LinkId=398765">Prise en main d'Azure Site Recovery : protection entre deux sites locaux VMM avec la réplication Hyper-V</a>.</li>

<li>**Protection entre deux sites locaux VMM avec la réplication SAN** : orchestre de bout en bout la réplication, le basculement et la récupération en utilisant la réplication basée sur des baies de stockage entre les périphériques SAN qui hébergent les données des machines virtuelles des sites locaux source et cible. Consultez <a href="http://go.microsoft.com/fwlink/?LinkId=518683">Prise en main d'Azure Site Recovery : : protection entre deux sites locaux VMM avec la réplication SAN</a>.</li>

<li>**Protection entre un site local VMM et Azure** : orchestre la réplication, le basculement et la récupération entre un site local VMM et Azure. Les données répliquées des machines virtuelles sont stockées dans le stockage Azure. Consultez <a href="http://go.microsoft.com/fwlink/?LinkId=398764">Prise en main d'Azure Site Recovery : protection entre un site local VMM et Azure.</a></li>

<li>**Réplication entre sites locaux VMWare avec InMage** : InMage Scout est une acquisition récente de Microsoft qui assure une réplication en temps réel entre des sites locaux VMWare. Pour l'heure, InMage est un produit disponible séparément. Vous pouvez l'obtenir via un abonnement au service Azure Site Recovery. Consultez <a href="http://go.microsoft.com/fwlink/?LinkId=518684">Prise en main d'Azure Site Recovery : protection entre sites VMWare locaux avec InMage</a>.</li>
</ul>

<p>La matrice des fonctionnalités résume ces scénarios.</p>

<table border="1">
<thead>
<tr>
	<th>Fonctionnalité</th><th>Site local vers Azure</th>
	<th>Site local vers site local (Hyper-V)</th>
	<th>Site local vers site local (SAN)</th>
</tr>
</thead>

<tr>
<td>Données vers Azure Site Recovery</td>
<td><p>Les métadonnées du cloud sont envoyées à Azure Site Recovery.</p> <p>Les données répliquées sont stockées dans le stockage Azure.</p></td>
<td><p>Les métadonnées du cloud sont envoyées à Azure Site Recovery.</p> <p>Les données répliquées sont stockées à l'emplacement spécifié par le serveur Hyper-V cible.</p></td>
<td><p>Les métadonnées du cloud sont envoyées à Azure Site Recovery.</p> <p>Les données répliquées sont stockées dans le stockage à baies cible.</p></td>
</tr>

<tr>
<td>Conditions requises pour les coffres</td>
<td><p>Vous avez besoin d'un compte Azure pour configurer un coffre Azure Site Recovery. Consultez la page <a href="http://aka.ms/try-azure">Évaluation gratuite d'Azure</a>. Pour plus d'informations sur la tarification, consultez la page <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Tarification d'Azure Site Recovery Manager</a>.</p></td>
<td><p>Compte Azure avec Azure Site Recovery activé.</p>
</td><td><p>Compte Azure avec Azure Site Recovery activé.</p></td>
</tr>

<tr>
<td>Réplication</td>
<td>Les machines virtuelles sont répliquées du serveur local Hyper-V vers le stockage Azure cible. Vous pouvez configurer une réplication inverse pour répliquer en retour vers l'emplacement source.</td>
<td>Les machines virtuelles sont répliquées d'un serveur Hyper-V local vers un autre. Vous pouvez configurer une réplication inverse pour répliquer en retour vers l'emplacement source.</td>
<td>Les machines virtuelles sont répliquées du périphérique de stockage SAN source vers le périphérique SAN cible. Vous pouvez configurer une réplication inverse pour répliquer en retour vers l'emplacement source.</td>
</tr>

<tr>
<td>Stockage de machine virtuelle</td>
<td>Disque dur de machine virtuelle stocké dans un stockage Azure</td>
<td>Disque dur de machine virtuelle stocké sur un hôte Hyper-V.</td>
<td>Disque dur de machine virtuelle stocké dans une baie de stockage SAN.</td>
</tr>

<tr>
<td>Stockage Azure</td>
<td><p>Compte de stockage Azure requis.</p> <p>Ce dernier doit avoir son option de géo-réplication activée, se trouver dans la même région que le service Azure Site Recovery et être associé au même abonnement.</p></td>
<td>Non applicable</td>
<td>Non applicable</td>
</tr>

<tr>
<td>Baie de stockage SAN</td>
<td><p>Non applicable</p></td>
<td>Non applicable</td>
<td>Le site source et le site cible doivent tous deux disposer d'une baie de stockage SAN, qui doit être gérée par VMM. Pour plus d'informations, consultez les <a href="http://go.microsoft.com/fwlink/?LinkId=518685">conditions préalables au déploiement</a>. </td>
</tr>

<tr>
<td>Serveur VMM</td>
<td>Nécessite un seul serveur VMM dans le site source uniquement. Le serveur VMM doit disposer d'au moins un cloud contenant au moins un serveur hôte Hyper-V ou un cluster.</td>
<td>Nécessite des serveurs VMM source et cible avec au moins un cloud sur chacun d'eux ou un serveur VMM unique avec deux clouds. Les clouds doivent contenir au moins un serveur hôte ou un cluster Hyper-V.</td>
<td>Nécessite des serveurs VMM source et cible avec au moins un cloud sur chacun d'eux. Les clouds doivent contenir au moins un cluster Hyper-V.</td>
</tr>

<tr>
<td>Version de System Center sur les serveurs VMM</td>
<td>System Center 2012 R2</td>
<td><p>System Center 2012 avec SP1</p><p>System Center 2012 R2</p></td>
<td><p>System Center 2012 R2 avec VMM Update Rollup 5.0</p></td>
</tr>

<tr>
<td>Configuration des serveurs VMM</td>
<td><p>Configurer des clouds dans les sites source et cible</p><p>Configurer des réseaux de machines virtuelles dans les sites source et cible</p><p>Configurer des classifications de stockage dans les sites source et cible <p>Installer le fournisseur sur les serveurs VMM source et cible</p></td>
<td><p>Configurer des clouds dans le site source</p><p>Configurer un stockage SAN</p><p>Configurer des réseaux de machines virtuelles dans le site source</p><p>Installer le fournisseur sur le serveur VMM source</p><p>Activer la protection des machines virtuelles</p></td>
<td><p>Configurer des clouds dans les sites source et cible</p><p>Configurer des réseaux de machines virtuelles dans les sites source et cible</p><p>Installer le fournisseur sur les serveurs VMM source et cible</p><p>Activer la protection des machines virtuelles</p></td>
</tr>

<tr>
<td>Fournisseur Azure Site Recovery</td>
<td>Installer sur le serveur VMM source</td>
<td>Installer sur les serveurs VMM source et cible</td>
<td>Installer sur les serveurs VMM source et cible</td>
</tr>

<tr>
<td>Agent Azure Recovery Services</td>
<td>Installer sur des serveurs hôtes Hyper-V situés dans des clouds VMM</td>
<td>Non requis</td>
<td>Non requis</td>
</tr>

<tr>
<td>Points de récupération de machine virtuelle</td>
<td><p>Définissez les points de récupération par heure.</p> <p>Spécifie pendant combien de temps un point de récupération doit être conservé (0-24 heures). Un point de récupération est créé avec la formule 90/fréquence_de_copie pendant la première heure, puis une fois par heure.</p></td>
<td><p>Définissez les points de récupération par quantité.</p> <p>Spécifie combien de points de récupération supplémentaires doivent être conservés (0-15). Par défaut, un point de récupération est créé toutes les heures. Lorsque la valeur est égale à zéro, le dernier point de récupération est stocké sur le serveur hôte Hyper-V réplica.</p></td>
<td>Configuré dans les paramètres de stockage sur baie.</td>
</tr>

<tr>
<td>Mappage réseau</td>
<td><p>Mappage de réseaux de machines virtuelles vers des réseaux Azure.</p> <p>Le mappage réseau est l'assurance que toutes les machines virtuelles qui basculent dans le même réseau de machines virtuelles source peuvent se connecter après le basculement Par ailleurs, si une passerelle réseau est configurée sur le réseau Azure cible, les machines virtuelles peuvent se connecter aux machines virtuelles locales. </p><p>Si le mappage n'est pas activé, seules les machines virtuelles qui basculent dans le même plan de récupération peuvent se connecter l'une à l'autre après le basculement vers Azure.</p></td>
<td><p>Mappez les réseaux de machines virtuelles sources aux réseaux de machines virtuelles cibles.</p> <p>Le mappage réseau est utilisé pour placer les machines virtuelles répliquées sur des serveurs hôtes Hyper-V optimaux et veiller à ce que les machines virtuelles associées au réseau de machines virtuelles source soient associées au réseau cible mappé après le basculement. </p><p>Si le mappage n'est pas activé, les machines virtuelles répliquées ne sont pas connectées au réseau.</p></td>
<td><p>Mappez les réseaux de machines virtuelles sources aux réseaux de machines virtuelles cibles.</p> <p>Le mappage réseau garantit que les machines virtuelles associées au réseau de machines virtuelles source sont associées au réseau cible mappé après le basculement. </p><p>Si le mappage n'est pas activé, les machines virtuelles répliquées ne sont pas connectées au réseau.</p></td>
</tr>

<tr>
<td>Mappage de stockage</td>
<td>Non applicable</td>
<td><p>Mappe les classifications de stockage sur les serveurs VMM sources aux classifications de stockage sur les serveurs VMM cibles.</p> <p>Lorsque le mappage est activé, les disques durs des machines virtuelles dans la classification de stockage source sont situés dans la classification de stockage cible après le basculement.</p><p>Si le mappage de stockage n'est pas activé, les disques durs virtuels répliqués sont stockés à l'emplacement par défaut sur le serveur hôte Hyper-V cible.</p></td>
<td><p>Mappages entre les baies et les pools de stockage des sites principal et secondaire.</p></td>
</tr>

</table>

<p>Pour toute question, visitez le <a href="http://go.microsoft.com/fwlink/?LinkId=313628">forum Azure Recovery Services</a>.</p> 

<!--HONumber=49-->
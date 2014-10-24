<properties linkid="Azure Site Recovery Overview" urlDisplayName="Azure Site Recovery Overview" pageTitle="Azure Site Recovery Overview" metaKeywords="Azure Site Recovery, on-premises, clouds, Azure, VMM, Hyper-V" description="Deploy Azure Site Recovery to protect virtual machines on Hyper-V host servers that are located in VMM clouds. You can deploy from one on-premises site to another, or from an on-premises site to Azure." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Azure Site Recovery Overview" editor="jimbe" manager="cfreeman" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew"></tags>

# Vue d'ensemble d'Azure Site Recovery

<div class="dev-callout">

<p>Déployez Azure Site Recovery pour protéger les machines virtuelles exécutées sur des serveurs hôtes Hyper-V qui sont situés dans des clouds System Center Virtual Machine Manager (VMM). Vous pouvez déployer Azure Site Recovery de deux façons :</p>
<ul>
<li><b>Protection d'un serveur local vers un autre</b> : répliquez les machines virtuelles d'un site local vers un autre. Vous pouvez configurer et activer les paramètres de protection dans des coffres Azure Site Recovery. Les données des machines virtuelles sont répliquées d'un serveur hôte Hyper-V source vers un serveur hôte cible. Azure Site Recovery orchestre le processus. Un didacticiel est disponible pour ce scénario : consultez la page <a href="http://go.microsoft.com/fwlink/?LinkId=398765">Prise en main d'Azure Site Recovery : protection d'un serveur local vers un autre</a>.</li>
<li><b>Protection d'un serveur local vers Azure</b> : répliquez les machines virtuelles d'un site local vers Microsoft Azure. Vous pouvez configurer et activer les paramètres de protection dans des coffres Azure Site Recovery. Azure Site Recovery orchestre le processus et les données des machines virtuelles répliquées sont stockées dans un stockage Azure. Un didacticiel est disponible pour ce scénario : consultez la page <a href="http://go.microsoft.com/fwlink/?LinkId=398764">Prise en main d'Azure Site Recovery : protection d'un serveur local vers Azure</a>.</li>
</ul>
<p>Le tableau suivant récapitule et compare les deux options de déploiement.</p>

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Fonctionnalité</th>
<th align="left">Serveur local vers Azure</th>
<th align="left">Serveur local vers un serveur local</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Données vers Azure Site Recovery</td>
<td align="left">Les métadonnées du cloud sont envoyées à Azure Site Recovery. Les données répliquées sont stockées dans un stockage Azure.</td>
<td align="left">Les métadonnées du cloud sont envoyées à Azure Site Recovery. Les données répliquées sont stockées sur le serveur hôte Hyper-V.</td>
</tr>
<tr class="even">
<td align="left">Conditions requises pour les coffres</td>
<td align="left"><p>Le coffre exige un certificat qui respecte les <a href="%20http://go.microsoft.com/fwlink/?LinkId=386485">conditions requises pour les coffres</a>.</p>
<p>Une clé de coffre est générée automatiquement. La clé garantit l'intégrité du trafic entre le fournisseur Azure Site Recovery sur le serveur VMM et Azure Site Recovery.</p></td>
<td align="left"><p>Le coffre exige un certificat qui respecte les <a href="%20http://go.microsoft.com/fwlink/?LinkId=386485">conditions requises pour les coffres</a>.</p>
<p>Une clé de coffre est générée automatiquement. La clé garantit l'intégrité du trafic entre le fournisseur Azure Site Recovery sur le serveur VMM et Azure Site Recovery.</p></td>
</tr>
<tr class="odd">
<td align="left">Réplication</td>
<td align="left">Les données des machines virtuelles sont répliquées d'un serveur Hyper-V local vers un stockage Azure.</td>
<td align="left">Les machines virtuelles sont répliquées d'un serveur Hyper-V local vers un autre.</td>
</tr>
<tr class="even">
<td align="left">Stockage de machine virtuelle</td>
<td align="left">Disque dur de machine virtuelle stocké dans un stockage Azure</td>
<td align="left">Disque dur de machine virtuelle stocké sur un hôte Hyper-V.</td>
</tr>
<tr class="odd">
<td align="left">Stockage Azure</td>
<td align="left"><p>Compte de stockage Azure requis.</p>
<p>Ce dernier doit avoir son option de géo-réplication activée, se trouver dans la même région que le service Azure Site Recovery et être associé au même abonnement.</p></td>
<td align="left">Non applicable</td>
</tr>
<tr class="even">
<td align="left">Serveur VMM</td>
<td align="left">Exige un serveur VMM source uniquement</td>
<td align="left">Exige des serveurs VMM source et cible avec au moins un cloud chacun, ou un seul serveur VMM avec deux clouds</td>
</tr>
<tr class="odd">
<td align="left">Version System Center du serveur VMM</td>
<td align="left">System Center 2012 R2</td>
<td align="left"><p>System Center 2012 avec SP1</p>
<p>System Center 2012 R2</p></td>
</tr>
<tr class="even">
<td align="left">Fournisseur Azure Site Recovery</td>
<td align="left">Effectuez l'installation sur le serveur VMM source</td>
<td align="left">Effectuez l'installation sur les serveurs VMM source et cible</td>
</tr>
<tr class="odd">
<td align="left">Agent Azure Recovery Services</td>
<td align="left">Effectuez l'installation sur des serveurs hôtes Hyper-V situés dans des clouds VMM</td>
<td align="left">Non requis</td>
</tr>
<tr class="even">
<td align="left">Points de récupération de machine virtuelle</td>
<td align="left"><p>Définissez les points de récupération par heure.</p>
<p>Spécifie pendant combien de temps un point de récupération doit être conservé (0-24 heures). Un point de récupération est créé avec la formule 90/fréquence_de_copie pendant la première heure, puis une fois par heure.</p></td>
<td align="left"><p>Définissez les points de récupération par quantité.</p>
<p>Spécifie combien de points de récupération supplémentaires doivent être conservés (0-15). Par défaut, un point de récupération est créé toutes les heures. Lorsque la valeur est égale à zéro, le dernier point de récupération est stocké sur le serveur hôte Hyper-V réplica.</p></td>
</tr>
<tr class="odd">
<td align="left">Mappage réseau</td>
<td align="left"><p>Mappage de réseaux de machines virtuelles vers des réseaux Azure.</p>
<p>Le mappage réseau est l'assurance que toutes les machines virtuelles qui basculent dans le même réseau de machines virtuelles source peuvent se connecter après le basculement Par ailleurs, si une passerelle réseau est configurée sur le réseau Azure cible, les machines virtuelles peuvent se connecter aux machines virtuelles locales.</p>
<p>Si le mappage n'est pas activé, seules les machines virtuelles qui basculent dans le même plan de récupération peuvent se connecter l'une à l'autre après le basculement vers Azure.</p></td>
<td align="left"><p>Mappez les réseaux de machines virtuelles sources aux réseaux de machines virtuelles cibles.</p>
<p>Le mappage réseau est utilisé pour placer les machines virtuelles répliquées sur des serveurs hôtes Hyper-V optimaux et veiller à ce que les machines virtuelles associées au réseau de machines virtuelles source soient associées au réseau cible mappé après le basculement.</p>
<p>Si le mappage n'est pas activé, les machines virtuelles répliquées ne sont pas connectées au réseau.</p></td>
</tr>
<tr class="even">
<td align="left">Mappage de stockage</td>
<td align="left">Non applicable</td>
<td align="left"><p>Mappe les classifications de stockage sur les serveurs VMM sources aux classifications de stockage sur les serveurs VMM cibles.</p>
<p>Lorsque le mappage est activé, les disques durs des machines virtuelles dans la classification de stockage source sont situés dans la classification de stockage cible après le basculement.</p>
<p>Si le mappage de stockage n'est pas activé, les disques durs virtuels répliqués sont stockés à l'emplacement par défaut sur le serveur hôte Hyper-V cible.</p></td>
</tr>
</tbody>
</table>

Pour toute question, visitez le <a href="http://go.microsoft.com/fwlink/?LinkId=313628">forum Azure Recovery Services</a>.

</div>

  [Prise en main d'Azure Site Recovery : protection d'un serveur local vers un autre]: http://go.microsoft.com/fwlink/?LinkId=398765
  [Prise en main d'Azure Site Recovery : protection d'un serveur local vers Azure]: http://go.microsoft.com/fwlink/?LinkId=398764
  [conditions requises pour les coffres]: %20http://go.microsoft.com/fwlink/?LinkId=386485
  [forum Azure Recovery Services]: http://go.microsoft.com/fwlink/?LinkId=313628

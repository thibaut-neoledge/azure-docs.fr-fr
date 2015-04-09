<properties 
	pageTitle="Informations de confidentialité pour Azure Site Recovery" 
	description="Décrit les informations de confidentialité supplémentaires pour Azure Site Recovery" 
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
	ms.date="03/19/2015" 
	ms.author="raynew"/>

# Informations de confidentialité pour Azure Site Recovery

Ce tableau fournit des informations de confidentialité supplémentaires pour le service Microsoft Azure Site Recovery (le " Service "). Pour consulter la déclaration de confidentialité relative aux services Microsoft Azure, consultez la page
[Déclaration de confidentialité de Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=324899)

<table>
<thead>
<tr>
	<th>Fonctionnalité</th>
	<th>Résultat</th>
	<th>Informations collectées</th>
	<th>Utilisation</th>
	<th>Choix</th>
</tr>
</thead>
<tbody>
<tr>
	<td><p><b>Inscription</b></p></td>
	<td><p>Inscrit le serveur auprès du service dans le but de protéger les machines virtuelles</p></td>
	<td><p>Après avoir inscrit un service, le Service collecte, traite et transmet ces informations :</p>
		<ul>
			<li>Informations de certificat de gestion du serveur VMM chargé d'assurer la récupération d'urgence en utilisant le nom de Service du serveur VMM</li>
			<li>Noms des clouds de machines virtuelles de votre serveur VMM</li>
		</ul>
</td>
	<td><p>Voici comment le Service utilise les informations précédentes :</p>
		<ul>
			<li>Certificat de gestion : permet d'identifier et d'authentifier le serveur VMM inscrit pour l'accès au Service. Le Service utilise la portion de clé publique du certificat pour sécuriser un jeton auquel seul le serveur VMM inscrit a accès. Le serveur a besoin d'utiliser ce jeton pour accéder aux fonctionnalités du Service.</li>
			<li>Nom du serveur VMM : ce nom est nécessaire pour identifier et communiquer avec le serveur VMM approprié sur lequel les clouds sont situés. 
			</li>
			<li>Noms de clouds du serveur VMM : les noms de clouds sont nécessaires en cas d'utilisation de la fonctionnalité de couplage/découplage de clouds du Service décrite ci-dessous. Quand vous décidez de coupler le cloud d'un centre de données principal avec un autre cloud du centre de données de récupération, les noms de tous les clouds du centre de données de récupération sont présentés.</li>
		</ul>
</td>
	<td><p>Ces informations sont une part essentielle du processus d'inscription du Service dans la mesure où elles vous permettent, à vous et au Service, d'identifier le serveur VMM pour lequel fournir la protection Azure Site Recovery et d'identifier le serveur VMM inscrit approprié. Si vous ne souhaitez pas communiquer ces informations au Service, n'utilisez pas ce Service. Si vous inscrivez votre serveur et que vous décidez par la suite d'annuler son inscription, il vous suffit de supprimer les informations du serveur VMM depuis le portail du Service (c'est-à-dire, le portail Azure).</p></td>
</tr>

<tr>
	<td><p><b>Activer la protection Azure Site Recovery</b></p></td>
	<td><p>Le fournisseur Azure Site Recovery installé sur le serveur VMM est l'intermédiaire qui permet de communiquer avec le Service. Le fournisseur est une bibliothèque de liens dynamiques (DLL) hébergée dans le processus VMM. Une fois que le fournisseur est installé, la fonctionnalité " Datacenter Recovery " est activée dans la console Administrateur VMM. Les ordinateurs virtuels nouveaux ou existants d'un cloud peuvent activer une propriété appelée " Datacenter Recovery ", qui assure leur protection. Une fois que cette propriété est définie, le fournisseur envoie le nom et l'ID de la machine virtuelle au Service. La protection virtuelle est activée par la technologie de réplication Hyper-V de Windows Server 2012 ou Windows Server 2012 R2. Les données des machines virtuelles sont répliquées d'un hôte Hyper-V vers un autre (généralement situé dans un autre centre de données de " récupération ").</p></td>
	<td><p>Le Service collecte, traite et transmet les métadonnées de la machine virtuelle, à savoir, le nom, l'ID, le réseau virtuel et le nom du cloud auquel elle appartient.</p>
	</td>
	<td><p>Le Service utilise ces informations pour compléter les informations de machine virtuelle sur votre portail Service.</p>
	</td>
	<td><p>Il s'agit d'un rôle essentiel du Service qui ne peut pas être désactivé. Si vous ne voulez pas que ces informations soient envoyées au Service, n'activez pas la protection Azure Site Recovery pour les machines virtuelles. Notez que toutes les données que le fournisseur transmet au Service sont envoyées via HTTPS.</p></td>
</tr>
<tr>
	<td><p><b>Plan de récupération</b></p></td>
	<td><p>Cette fonctionnalité vous permet de créer un plan d'orchestration pour le centre de données de " récupération ". Vous pouvez définir l'ordre de démarrage des machines virtuelles ou d'un groupe de machines virtuelles sur le site de récupération. Pour chaque machine virtuelle, vous pouvez aussi spécifier les scripts automatisés à exécuter ou l'action manuelle à entreprendre au moment de la récupération. Le basculement (décrit dans la section suivante) est généralement déclenché au niveau du plan de récupération pour une récupération coordonnée.</p></td>
	<td><p>Le Service collecte, traite et transmet ces informations dans le cadre de la fonctionnalité Plan de récupération :</p>
		<ul>
			<li>le plan de récupération, y compris les métadonnées des machines virtuelles ;</li>
			<li>les métadonnées du script d'automatisation ou la note d'action manuelle.</li>
		</ul>
	</td>
	<td><p>Les métadonnées décrites ci-dessus servent à générer le plan de récupération dans le portail du Service.</p>
	</td>
	<td><p>Il s'agit d'un rôle essentiel du Service qui ne peut pas être désactivé. Si vous ne voulez pas que ces informations soient envoyées au Service, ne créez pas de plans de récupération dans ce Service.</p></td>
</tr>
<tr>
	<td><p><b>Mappage réseau</b></p></td>
	<td><p>Cette fonctionnalité vous permet de mapper les informations de réseau du centre de données principal vers le centre de données de récupération. Dès lors que les machines virtuelles sont récupérées sur le site de récupération, ce mappage permet de leur établir une connectivité réseau.</p></td>
	<td><p>Dans le cadre de la fonctionnalité de mappage réseau, le Service collecte, traite et transmet les métadonnées des réseaux logiques pour chaque site (principal et centre de données).</p>
	</td>
	<td><p>Le Service utilise les métadonnées pour renseigner votre portail Service où vous pouvez mapper les informations de réseau.</p>
	</td>
	<td><p>Il s'agit d'un rôle essentiel du Service qui ne peut pas être désactivé. Si vous ne voulez pas que ces informations soient envoyées au Service, n'utilisez pas la fonctionnalité de mappage réseau.</p></td>
</tr>
<tr>
	<td><p><b>Échec - planifié, non planifié, texte</b></p></td>
	<td><p>Cette fonctionnalité permet de faire basculer une machine virtuelle d'un centre de données géré VMM vers un autre. L'action de basculement est déclenchée par l'utilisateur sur son portail Service. Parmi les raisons possibles d'un basculement, citons un événement imprévu (par exemple, une catastrophe naturelle) ; un événement prévu (par exemple, l'équilibrage de charge d'un centre de données) ; un test de basculement (par exemple, la répétition d'un plan de récupération).</p>
	<p>Le fournisseur du serveur VMM est averti de l'événement par le Service et exécute une action de basculement sur l'hôte Hyper-V via les interfaces VMM. Le basculement effectif de la machine virtuelle d'un hôte Hyper-V vers un autre (généralement exécuté dans un autre centre de données de " récupération ") est géré par la technologie de réplication Hyper-V de Windows Server 2012 ou Windows Server 2012 R2. À l'issue du basculement, le fournisseur installé sur le serveur VMM du centre de données de " récupération " envoie au Service des informations indiquant la réussite de l'opération.</p>
	</td>
	<td><p>Le Service utilise ces informations pour spécifier l'état des informations de l'action de basculement votre portail Service.</p>
	</td>
	<td><p>Voici comment le Service utilise ces informations :</p>
		<ul>
			<li>Certificat de gestion : permet d'identifier et d'authentifier le serveur VMM inscrit pour l'accès au Service. Le Service utilise la portion de clé publique du certificat pour sécuriser un jeton auquel seul le serveur VMM inscrit a accès. Le serveur a besoin d'utiliser ce jeton pour accéder aux fonctionnalités du Service.</li>
			<li>Nom du serveur VMM : ce nom est nécessaire pour identifier et communiquer avec le serveur VMM approprié sur lequel les clouds sont situés. 
			</li>
			<li>Noms de clouds du serveur VMM : les noms de clouds sont nécessaires en cas d'utilisation de la fonctionnalité de couplage/découplage de clouds du Service décrite ci-dessous. Quand vous décidez de coupler le cloud d'un centre de données principal avec un autre cloud du centre de données de récupération, les noms de tous les clouds du centre de données de récupération sont présentés.</li>
		</ul>
	</td>
	<td><p>Il s'agit d'un rôle essentiel du Service qui ne peut pas être désactivé. Si vous ne voulez pas que ces informations soient envoyées au Service, n'utilisez pas ce Service.</p></td>
</tr>
</table>



<!--HONumber=49-->
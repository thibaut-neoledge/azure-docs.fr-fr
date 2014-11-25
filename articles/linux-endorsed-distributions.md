<properties linkid="manage-linux-other-resources-endorsed-distributions" urlDisplayName="Endorsed distributions" pageTitle="Endorsed distributions of Linux in Azure" metaKeywords="" description="Learn about Linux on Azure-endorsed distributions, including guidelines for Ubuntu, OpenLogic, and SUSE." metaCanonical="" services="virtual-machines" documentationCenter="" title="Linux on Azure-Endorsed Distributions" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

Linux dans les distributions approuvées par Azure
=================================================

Les images de distribution présentes dans la galerie sont fournies par les partenaires ci-dessous, et nous collaborons avec la communauté pour mettre à disposition encore plus de distributions approuvées. En attendant, vous pouvez toujours apporter votre propre distribution Linux en suivant les instructions fournies dans cette page.

Canonical
---------

<http://www.ubuntu.com/cloud/azure>

L'ingénierie Canonical et le mode de gouvernance de la communauté open source sont les éléments moteurs de la réussite d'Ubuntu dans les environnements client, serveur et cloud, y compris les services cloud personnels à destination du grand public. Des fournisseurs de services de cloud public aux fabricants d'appareils électroniques grand public, en passant par les experts en technologies, bon nombre d'acteurs plébiscitent l'idée de Canonical de proposer une plateforme gratuite unifiée dans Ubuntu, de la téléphonie jusqu'au cloud, mettant à disposition une famille d'interfaces cohérentes pour les téléphones, les tablettes, la télévision et les ordinateurs de bureau.

Avec des développeurs et des centres d'ingénierie présents dans le monde entier, Canonical est particulièrement bien placé pour collaborer avec les fabricants de matériel, les fournisseurs de contenu et les développeurs de logiciels pour mettre sur le marché des solutions Ubuntu qui s'adressent aussi bien aux utilisateurs de PC, de serveurs ou de périphériques de poche.

OpenLogic
---------

<http://www.openlogic.com/azure>

OpenLogic est l'un des principaux fournisseurs de solutions open source d'entreprise pour le cloud et les centres de données. OpenLogic permet à des centaines d'entreprises phares de divers secteurs d'activité d'acquérir, de prendre en charge et de contrôler des logiciels open source en toute sécurité. OpenLogic propose un support technique et un système d'indemnisation de qualité commerciale pour quelque 600 packages open source soutenus par la communauté d'experts OpenLogic, y compris un support de niveau entreprise pour CentOS. Par ailleurs, OpenLogic est un fournisseur partenaire d'images Centos sur Azure.

Oracle
------

<http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html>

La stratégie d'Oracle vise à offrir un portefeuille complet de solutions pour clouds publics et privés, tout en laissant à ses clients une grande latitude quant à la façon de déployer les logiciels Oracle dans les clouds Oracle et autres. À la faveur du partenariat conclu entre Oracle et Microsoft, les clients peuvent déployer les logiciels Oracle dans les clouds publics et privés Microsoft tout en étant assurés de bénéficier de la certification et du support d'Oracle. L'engagement et l'investissement d'Oracle vis-à-vis des solutions de cloud public et privé Oracle sont intacts.

SUSE
----

<http://www.suse.com/suse-linux-enterprise-server-on-azure>

SUSE Linux Enterprise Server sur Azure est une plateforme éprouvée qui offre une fiabilité et un niveau de sécurité supérieurs pour le cloud computing. Polyvalente, la plateforme Linux de SUSE s'intègre en toute transparence aux services cloud Azure pour fournir un environnement cloud facile à gérer. Et avec plus de 9 200 applications certifiées issues de plus de 1 800 éditeurs de logiciels indépendants pour SUSE Linux Enterprise Server, SUSE est l'assurance que les charges de travail prises en charge dans les centres de données peuvent être déployées en toute confiance sur Azure.

Versions prises en charge
-------------------------

Le tableau ci-dessous présente les différentes versions de distributions, les pilotes LIS (Linux Integration Services) et les versions de l'agent Linux Azure ayant réussi les tests de compatibilité Azure. Les pilotes LIS sont disponibles à l'adresse <http://www.microsoft.com/fr-fr/download/details.aspx?id=34603>. Les versions de l'agent Linux sont disponibles à l'adresse <https://github.com/windowsazure/walinuxagent>.

Le tableau comporte également un lien vers le correctif de compatibilité du noyau, dont certaines versions de distributions ont besoin pour fonctionner de manière optimale dans Azure.

<table data-morhtml="true" border="1" width="600">
  <tr data-morhtml="true" bgcolor="#E9E7E7">
		<th data-morhtml="true">Distribution</th>		
	    <th data-morhtml="true">Version</th>
	    <th data-morhtml="true">Pilotes</th>
		<th data-morhtml="true">Correctif de compatibilité du noyau</th>
		<th data-morhtml="true">Agent</th>
			</tr>
	<tr data-morhtml="true">
		<th data-morhtml="true">  UBUNTU de Canonical </th>
		<td data-morhtml="true"> Ubuntu 12.04.1, 12.10 et 13.04</td>
		<td data-morhtml="true">Dans le noyau</td>
		<td data-morhtml="true"><a data-morhtml="true" href="http://go.microsoft.com/fwlink/?LinkID=275152&amp;clcid=0x409">Nécessaire pour la version 12.04 ou 12.04.01 uniquement</a></td>
		<td data-morhtml="true">Package : dans le référentiel de packages sous walinuxagent <br data-morhtml="true" /> Source : <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GITHUB</a></td>
			</tr>
	<tr data-morhtml="true">
		<th data-morhtml="true"> CENTOS d'Open Logic </th>
		<td data-morhtml="true"> CentOS 6.3+</td>
	    <td data-morhtml="true">CentOS 6.3 : <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=254263">pilotes LIS</a> ; pilotes CentOS 6.4+ : dans le noyau</td>
		<td data-morhtml="true"><a data-morhtml="true" href="http://go.microsoft.com/fwlink/?LinkID=275153&amp;clcid=0x409">Nécessaire pour la version 6.3 uniquement</a></td>
		<td data-morhtml="true">Package : dans le <a data-morhtml="true" href="http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/">référentiel de packages Open Logic</a> sous walinuxagent<br data-morhtml="true" /> Source : <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GITHUB</a></td>
 		
	</tr>
	<tr data-morhtml="true">
		<th data-morhtml="true"> Oracle Linux </th>
		<td data-morhtml="true"> 6.4+</td>
        <td data-morhtml="true">Dans le noyau</td>
		<td data-morhtml="true">N/A</td>
		<td data-morhtml="true">Package : dans le référentiel, nom : WALinuxAgent <br data-morhtml="true" /> Source : <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GITHUB</a></td>
		
	</tr><tr data-morhtml="true">
		<th data-morhtml="true"> SUSE Linux Enterprise </th>
		<td data-morhtml="true"> SLES 11 SP3+</td>
        <td data-morhtml="true">Dans le noyau</td>
		<td data-morhtml="true">N/A</td>
		<td data-morhtml="true">Package : dans le référentiel <a data-morhtml="true" href="https://build.opensuse.org/project/show/Cloud:Tools">Cloud:Tools</a>, nom : WALinuxAgent <br data-morhtml="true" /> Code source : <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GITHUB</a></td>
		
	</tr>
	<tr data-morhtml="true">
		<th data-morhtml="true"> openSUSE </th>
		<td data-morhtml="true"> OpenSUSE 13.1+</td>
		<td data-morhtml="true">Dans le noyau</td>
		<td data-morhtml="true">N/A</td>
		<td data-morhtml="true">Package : dans le référentiel <a data-morhtml="true" href="https://build.opensuse.org/project/show/Cloud:Tools">Cloud:Tools</a>, nom : WALinuxAgent <br data-morhtml="true" /> Code source : <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GITHUB</a></td>
		
	</tr>
</table>


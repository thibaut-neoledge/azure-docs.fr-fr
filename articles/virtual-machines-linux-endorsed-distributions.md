<properties urlDisplayName="Endorsed distributions" pageTitle="Distributions approuvées de Linux dans Azure" metaKeywords="" description="Découvrez les distributions Linux approuvées sur Azure, y compris des instructions pour Ubuntu, OpenLogic et SUSE." metaCanonical="" services="virtual-machines" documentationCenter="" title="Linux on Azure-Endorsed Distributions" authors="szark" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="12/05/2014" ms.author="szark" />





#Linux dans les distributions approuvées par Azure

Les images de distribution présentes dans la galerie Azure sont fournies par les partenaires ci-dessous, et nous collaborons avec les différentes communautés Linux pour mettre à disposition encore plus de distributions approuvées. En attendant, pour les distributions non disponibles dans la galerie, vous pouvez toujours apporter votre propre distribution Linux en suivant les instructions fournies sur [cette page](../virtual-machines-linux-create-upload-vhd/).

## Canonical ##
 
[http://www.ubuntu.com/cloud/azure](http://www.ubuntu.com/cloud/azure)

L'ingénierie Canonical et le mode de gouvernance de la communauté open source sont les éléments moteurs de la réussite d'Ubuntu dans les environnements client, serveur et cloud, y compris les services cloud personnels à destination du grand public. Des fournisseurs de services de cloud public aux fabricants d'appareils électroniques grand public, en passant par les experts en technologies, bon nombre d'acteurs plébiscitent l'idée de Canonical de proposer une plateforme gratuite unifiée dans Ubuntu, de la téléphonie jusqu'au cloud, mettant à disposition une famille d'interfaces cohérentes pour les téléphones, les tablettes, la télévision et les ordinateurs de bureau.

Avec des développeurs et des centres d'ingénierie présents dans le monde entier, Canonical est particulièrement bien placé pour collaborer avec les fabricants de matériel, les fournisseurs de contenu et les développeurs de logiciels pour mettre sur le marché des solutions Ubuntu qui s'adressent aussi bien aux utilisateurs de PC, de serveurs ou de périphériques de poche.

## OpenLogic ##
 
[http://www.openlogic.com/azure](http://www.openlogic.com/azure)

OpenLogic est l'un des principaux fournisseurs de solutions open source d'entreprise pour le cloud et les centres de données. OpenLogic permet à des centaines d'entreprises phares de divers secteurs d'activité d'acquérir, de prendre en charge et de contrôler des logiciels open source en toute sécurité. OpenLogic propose un support technique et un système d'indemnisation de qualité commerciale pour quelque 600 packages open source soutenus par la communauté d'experts OpenLogic, y compris un support de niveau entreprise pour CentOS. Par ailleurs, OpenLogic est un fournisseur partenaire d'images Centos sur Azure.

## Oracle##
[http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

La stratégie d'Oracle vise à offrir un portefeuille complet de solutions pour clouds publics et privés, tout en laissant à ses clients une grande latitude quant à la façon de déployer les logiciels Oracle dans les clouds Oracle et autres.  À la faveur du partenariat conclu entre Oracle et Microsoft, les clients peuvent déployer les logiciels Oracle dans les clouds publics et privés Microsoft tout en étant assurés de bénéficier de la certification et du support d'Oracle.  L'engagement et l'investissement d'Oracle vis-à-vis des solutions de cloud public et privé Oracle sont intacts.

##SUSE##
 
[http://www.suse.com/suse-linux-enterprise-server-on-azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server sur Azure est une plateforme éprouvée qui offre une fiabilité et un niveau de sécurité supérieurs pour le cloud computing. Polyvalente, la plateforme Linux de SUSE s'intègre en toute transparence aux services cloud Azure pour fournir un environnement cloud facile à gérer. Et avec plus de 9 200 applications certifiées issues de plus de 1 800 éditeurs de logiciels indépendants pour SUSE Linux Enterprise Server, SUSE est l'assurance que les charges de travail prises en charge dans les centres de données peuvent être déployées en toute confiance sur Azure.

## Versions prises en charge ##

Le tableau ci-dessous présente les différentes versions de distributions, les pilotes LIS (Linux Integration Services) et les versions de l'agent Linux Azure ayant réussi les tests de compatibilité Azure. Les pilotes LIS sont soit intégrés au noyau de la distribution par défaut, soit disponibles [ici](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409). Les versions de l'agent Linux sont disponibles dans le référentiel de packages de la distribution ou sur [Github](https://github.com/azure/walinuxagent).

Le tableau comprend aussi un lien vers le [Correctif de compatibilité du noyau Linux](http://go.microsoft.com/fwlink/?LinkID=403027&clcid=0x409) nécessaire au fonctionnement optimal de certaines versions du noyau/de distribution dans Azure.

<table border="1" width="600">
  <tr bgcolor="#E9E7E7">
		<th>Distribution</th>		
	    <th>Version</th>
	    <th>Pilotes</th>
		<th>Correctif de compatibilité du noyau</th>
		<th>Agent</th>
			</tr>
	<tr>
		<th>  Ubuntu de Canonical </th>
		<td> Ubuntu 12.04.1+, 14.04 et 14.10 </td>
		<td>Dans le noyau</td>
		<td><a href="http://go.microsoft.com/fwlink/?LinkID=275152&clcid=0x409">Nécessaire pour la version 12.04 ou 12.04.01 uniquement</a></td>
		<td>Package : dans le référentiel de packages sous walinuxagent <br />
			Source : <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
			</tr>
	<tr>
		<th> CentOS par OpenLogic </th>
		<td> CentOS 6.3+</td>
	    <td>CentOS 6.3 : <a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409">pilotes LIS </a>; pilotes CentOS 6.4+ : dans le noyau</td>
		<td><a href="http://go.microsoft.com/fwlink/?LinkID=275153&clcid=0x409">Nécessaire pour la version 6.3 uniquement</a></td>
		<td>Package : dans le <a href="http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/">référentiel de packages Open Logic </a> sous walinuxagent<br />
			Source : <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
 		
	</tr>
	<tr>
		<th> <a href="https://coreos.com/docs/running-coreos/cloud-providers/azure/">CoreOS</a> </th>
		<td> 494.4.0+ </td>
        <td> Dans le noyau </td>
		<td> N/A </td>
		<td> Source : <a href="https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent">GitHub</a></td>
		
	</tr>
	<tr>
		<th> Oracle Linux </th>
		<td> 6.4+</td>
        <td>Dans le noyau</td>
		<td>N/A</td>
		<td>Package : dans le référentiel, nom : WALinuxAgent<br />
			Source : <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
		
	</tr>
	<tr>
		<th> SUSE Linux Enterprise </th>
		<td> SLES 11 SP3+</td>
        <td>Dans le noyau</td>
		<td>N/A</td>
		<td>Package : Dans <a href="https://build.opensuse.org/project/show/Cloud:Tools" >Cloud:Tools</a> repo, name: WALinuxAgent<br />
			Source Code: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
		
	</tr>
	<tr>
		<th> openSUSE </th>
		<td> openSUSE 13.1+</td>
		<td>Dans le noyau</td>
		<td>N/A</td>
		<td>Package : Dans <a href="https://build.opensuse.org/project/show/Cloud:Tools" >Cloud:Tools</a> repo, name: WALinuxAgent<br />
			Source Code: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
		
	</tr>
</table>


<!--HONumber=35.1-->

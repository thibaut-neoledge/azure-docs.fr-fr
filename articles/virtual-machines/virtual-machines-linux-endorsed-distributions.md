<properties 
	pageTitle="Distributions approuvées de Linux | Microsoft Azure" 
	description="Découvrez les distributions Linux approuvées sur Azure, y compris des instructions pour Ubuntu, OpenLogic et SUSE." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor="tysonn"
	tags="azure-service-management,azure-resource-manager" 
	/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="szark"/>



#Linux dans les distributions approuvées par Azure

Les images Linux présentes dans la galerie Azure sont fournies par plusieurs partenaires, et nous collaborons avec les différentes communautés Linux pour enrichir davantage la liste des distributions approuvées. En attendant, pour les distributions non disponibles dans la galerie, vous pouvez toujours apporter votre propre distribution Linux en suivant les instructions fournies [sur cette page](virtual-machines-linux-create-upload-vhd.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## Distributions et versions prises en charge ##

Le tableau suivant répertorie les distributions et les version Linux prises en charge sur Microsoft Azure.

Les pilotes LIS (Linux Integration Services) pour Hyper-V et Azure sont des modules noyau que Microsoft distribue directement au noyau Linux en amont. Les pilotes LIS sont intégrés au noyau de distribution par défaut, ou les distributions basées sur RHEL/CentOS sont disponibles au téléchargement [ici](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409). Pour plus d’informations sur les pilotes LIS, consultez [cet article](virtual-machines-linux-create-upload-vhd-generic.md#linux-kernel-requirements).

L’agent Azure Linux, qui est pré-installé sur les images de la galerie Azure, est généralement disponible à partir du référentiel du package de distribution. Le code source est disponible sur [GitHub](https://github.com/azure/walinuxagent).

Distribution|Version|Pilotes|Agent
---|---|---|---
Ubuntu de Canonical|Ubuntu 12.04, 14.04, 14.10 et 15.04|Dans le noyau|Package : dans le référentiel sous "walinuxagent" <p><p>Source : [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
CentOS par OpenLogic |CentOS 6.3+, 7.0+| CentOS 6.3 : [Téléchargement LIS](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)<p>CentOS 6.4+ : Dans le noyau|Package : dans le référentiel <a href="http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/">OpenLogic sous "WALinuxAgent"<p><p>Source : [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
[CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/)|494\.4.0+ |Dans le noyau|Source : [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent)
Oracle Linux| 6\.4+, 7.0+|Dans le noyau|Package : dans le référentiel sous "WALinuxAgent" <p><p>Source : [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
SUSE Linux Enterprise |SLES 11 SP3+, SLES 12+ et <p><p> SLES pour SAP 11.3+ |Dans le noyau|Package : dans le référentiel [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools), sous "WALinuxAgent"<p><p>Source : [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
openSUSE |openSUSE 13.1+|Dans le noyau|Package : dans le référentiel [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools), sous "WALinuxAgent"<p><p>Code source : [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)

## Partenaires

### Canonical
[http://www.ubuntu.com/cloud/azure](http://www.ubuntu.com/cloud/azure)

L'ingénierie Canonical et le mode de gouvernance de la communauté open source sont les éléments moteurs de la réussite d'Ubuntu dans les environnements client, serveur et cloud, y compris les services cloud personnels à destination du grand public. Des fournisseurs de services de cloud public aux fabricants d'appareils électroniques grand public, en passant par les experts en technologies, bon nombre d'acteurs plébiscitent l'idée de Canonical de proposer une plateforme gratuite unifiée dans Ubuntu, de la téléphonie jusqu'au cloud, mettant à disposition une famille d'interfaces cohérentes pour les téléphones, les tablettes, la télévision et les ordinateurs de bureau.

Avec des développeurs et des centres d'ingénierie présents dans le monde entier, Canonical est particulièrement bien placé pour collaborer avec les fabricants de matériel, les fournisseurs de contenu et les développeurs de logiciels pour mettre sur le marché des solutions Ubuntu qui s'adressent aussi bien aux utilisateurs de PC, de serveurs ou de périphériques de poche.


### CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

Depuis le site Web CoreOS :

*CoreOS est conçu pour la sécurité, la cohérence et la fiabilité. Au lieu d’installer des packages via yum ou apt, CoreOS utilise des conteneurs Linux pour gérer vos services à un niveau supérieur d’abstraction. Un code de service unique et l’ensemble des dépendances sont déployés au sein d’un conteneur pouvant être exécuté sur une ou plusieurs machines CoreOS.*


### OpenLogic
[http://www.openlogic.com/azure](http://www.openlogic.com/azure)

OpenLogic est l'un des principaux fournisseurs de solutions open source d'entreprise pour le cloud et les centres de données. OpenLogic permet à des centaines d'entreprises phares de divers secteurs d'activité d'acquérir, de prendre en charge et de contrôler des logiciels open source en toute sécurité. OpenLogic propose un support technique et un système d'indemnisation de qualité commerciale pour quelque 600 packages open source soutenus par la communauté d'experts OpenLogic, y compris un support de niveau entreprise pour CentOS. Par ailleurs, OpenLogic est un fournisseur partenaire d'images Centos sur Azure.


### Oracle
[http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

La stratégie d'Oracle vise à offrir un portefeuille complet de solutions pour clouds publics et privés, tout en laissant à ses clients une grande latitude quant à la façon de déployer les logiciels Oracle dans les clouds Oracle et autres. À la faveur du partenariat conclu entre Oracle et Microsoft, les clients peuvent déployer les logiciels Oracle dans les clouds publics et privés Microsoft tout en étant assurés de bénéficier de la certification et du support d'Oracle. L'engagement et l'investissement d'Oracle vis-à-vis des solutions de cloud public et privé Oracle sont intacts.


### SUSE
[http://www.suse.com/suse-linux-enterprise-server-on-azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server sur Azure est une plateforme éprouvée qui offre une fiabilité et un niveau de sécurité supérieurs pour le cloud computing. Polyvalente, la plateforme Linux de SUSE s'intègre en toute transparence aux services cloud Azure pour fournir un environnement cloud facile à gérer. Et avec plus de 9 200 applications certifiées issues de plus de 1 800 éditeurs de logiciels indépendants pour SUSE Linux Enterprise Server, SUSE est l'assurance que les charges de travail prises en charge dans les centres de données peuvent être déployées en toute confiance sur Azure.

 

<!---HONumber=Oct15_HO4-->
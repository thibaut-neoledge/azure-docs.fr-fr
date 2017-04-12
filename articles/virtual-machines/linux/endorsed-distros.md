---
title: "Distributions approuvées de Linux | Microsoft Docs"
description: "Découvrez les distributions Linux approuvées sur Azure, y compris des instructions pour Ubuntu, OpenLogic et SUSE."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 3a9edce4b8e7208579ee810ad40a65b22db20cac
ms.lasthandoff: 04/03/2017


---
# <a name="linux-on-azure-endorsed-distributions"></a>Linux dans les distributions approuvées par Azure
> [!NOTE]
> Si vous avez un moment, aidez-nous à améliorer la documentation relative aux machines virtuelles Azure Linux en répondant à cette [enquête rapide](https://aka.ms/linuxdocsurvey) concernant vos expériences. Chaque réponse nous aide à vous faciliter la tâche.
> 
> 

Les images Linux présentes dans la galerie Azure ou Marketplace sont fournies par plusieurs partenaires, et nous collaborons avec les différentes communautés Linux pour enrichir davantage la liste des distributions approuvées. En attendant, pour les distributions non disponibles dans la galerie, vous pouvez toujours apporter votre propre distribution Linux en suivant les instructions fournies [sur cette page](classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="supported-distributions--versions"></a>Distributions et versions prises en charge
Le tableau suivant répertorie les distributions et les version Linux prises en charge sur Microsoft Azure. Reportez-vous également à [Prise en charge d’images Linux dans Microsoft Azure](https://support.microsoft.com/en-us/kb/2941892) pour plus d’informations.

Les pilotes LIS (Linux Integration Services) pour Hyper-V et Azure sont des modules noyau que Microsoft distribue directement au noyau Linux en amont.  Les pilotes LIS sont intégrés au noyau de distribution par défaut, ou les distributions basées sur RHEL/CentOS sont disponibles au téléchargement [ici](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409).  Pour plus d’informations sur les pilotes LIS, consultez [cet article](create-upload-generic.md#linux-kernel-requirements) .

L’agent Azure Linux, qui est pré-installé sur les images de la galerie Azure, est généralement disponible à partir du référentiel du package de distribution.  Le code source est disponible sur [GitHub](https://github.com/azure/walinuxagent).

| Distribution | Version | Pilotes | Agent |
| --- | --- | --- | --- |
| CentOS par OpenLogic |CentOS 6.3+, 7.0+ |CentOS 6.3 : [téléchargement LIS](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)<p>CentOS 6.4+ : Dans le noyau |Package : dans le [référentiel de packages OpenLogic](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) sous « WALinuxAgent » <br/>Code source : [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |Dans le noyau |Code source : [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7.9+, 8.2+ |Dans le noyau |Package : dans le référentiel sous « waagent »  <br/>Code source : [GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4+, 7.0+ |Dans le noyau |Package : dans le référentiel sous « WALinuxAgent »  <br/>Code source : [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 6.7+, 7.1+ |Dans le noyau |Package : dans le référentiel sous « WALinuxAgent »  <br/>Code source : [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SLES pour SAP<br>11 SP4<br>12 SP1+|Dans le noyau |Package :<p> pour 11 dans le référentiel [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools)<br>pour 12 inclus dans le module « Cloud public » sous « python-azure-agent »<br/>Code source : [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE Leap 42.1+ |Dans le noyau |Package: dans le référentiel [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) sous « python-azure-agent » <br/>Code source : [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04, 14.04, 16.04, 16.10 |Dans le noyau |Package : dans le référentiel sous « WALinuxAgent »  <br/>Code source : [GitHub](https://github.com/Azure/WALinuxAgent) |

## <a name="partners"></a>Partenaires
### <a name="openlogic"></a>OpenLogic
[http://www.openlogic.com/azure](http://www.openlogic.com/azure)

OpenLogic est l'un des principaux fournisseurs de solutions open source d'entreprise pour le cloud et les centres de données. OpenLogic permet à des centaines d'entreprises phares de divers secteurs d'activité d'acquérir, de prendre en charge et de contrôler des logiciels open source en toute sécurité. OpenLogic propose un support technique et un système d'indemnisation de qualité commerciale pour quelque 600 packages open source soutenus par la communauté d'experts OpenLogic, y compris un support de niveau entreprise pour CentOS. Par ailleurs, OpenLogic est un fournisseur partenaire d'images Centos sur Azure.

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

Depuis le site Web CoreOS :

*CoreOS est conçu pour la sécurité, la cohérence et la fiabilité. Au lieu d’installer des packages via yum ou apt, CoreOS utilise des conteneurs Linux pour gérer vos services à un niveau supérieur d’abstraction. Un code de service unique et l’ensemble des dépendances sont déployés au sein d’un conteneur pouvant être exécuté sur une ou plusieurs machines CoreOS.*

### <a name="credativ"></a>Credativ
[http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ est une société de conseil et de services indépendante, spécialisée dans le développement et la mise en œuvre de solutions professionnelles grâce à l’utilisation de logiciels libres. En tant de leader spécialiste de l’open source, Credative a acquis une reconnaissance internationale et de nombreux services informatiques font appel à ses services. En collaboration avec Microsoft, credativ prépare actuellement les images Debian correspondantes pour Debian 8 (Jessica) et Debian avant version 7 (Wheezy), qui sont spécialement conçues pour s’exécuter sur Azure et qui peuvent être facilement gérées par le biais de la plateforme. credativ prendra également en charge la maintenance sur le long terme et la mise à jour des images Debian pour Azure grâce à ses centres de support Open Source.

### <a name="oracle"></a>Oracle
[http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

La stratégie d'Oracle vise à offrir un portefeuille complet de solutions pour clouds publics et privés, tout en laissant à ses clients une grande latitude quant à la façon de déployer les logiciels Oracle dans les clouds Oracle et autres.  À la faveur du partenariat conclu entre Oracle et Microsoft, les clients peuvent déployer les logiciels Oracle dans les clouds publics et privés Microsoft tout en étant assurés de bénéficier de la certification et du support d'Oracle.  L'engagement et l'investissement d'Oracle vis-à-vis des solutions de cloud public et privé Oracle sont intacts.

### <a name="red-hat"></a>Red Hat
[http://www.redhat.com/en/partners/Strategic-Alliance/Microsoft](http://www.redhat.com/en/partners/strategic-alliance/microsoft)

Le leader mondial de solutions open source, Red Hat permet à plus de 90 % des sociétés du classement Fortune 500 de résoudre les défis commerciaux, d’aligner leur stratégies informatique et commerciale, et d’anticiper l’évolution des technologies. Red Hat y parvient en proposant des solutions sécurisées grâce à un modèle commercial ouvert et à un modèle d’abonnement abordable et prévisible.

### <a name="suse"></a>SUSE
[http://www.suse.com/suse-linux-enterprise-server-on-azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server sur Azure est une plateforme éprouvée qui offre une fiabilité et un niveau de sécurité supérieurs pour le cloud computing. Polyvalente, la plateforme Linux de SUSE s'intègre en toute transparence aux services cloud Azure pour fournir un environnement cloud facile à gérer. Et avec plus de 9 200 applications certifiées issues de plus de 1 800 éditeurs de logiciels indépendants pour SUSE Linux Enterprise Server, SUSE est l'assurance que les charges de travail prises en charge dans les centres de données peuvent être déployées en toute confiance sur Azure.

### <a name="canonical"></a>Canonical
[http://www.ubuntu.com/cloud/azure](http://www.ubuntu.com/cloud/azure)

L’ingénierie Canonical et le mode de gouvernance de la communauté open source sont les éléments moteurs de la réussite d’Ubuntu dans les environnements client, serveur et cloud, y compris les services cloud personnels à destination du grand public. Des fournisseurs de services de cloud public aux fabricants d'appareils électroniques grand public, en passant par les experts en technologies, bon nombre d'acteurs plébiscitent l'idée de Canonical de proposer une plateforme gratuite unifiée dans Ubuntu, de la téléphonie jusqu'au cloud, mettant à disposition une famille d'interfaces cohérentes pour les téléphones, les tablettes, la télévision et les ordinateurs de bureau.

Avec des développeurs et des centres d'ingénierie présents dans le monde entier, Canonical est particulièrement bien placé pour collaborer avec les fabricants de matériel, les fournisseurs de contenu et les développeurs de logiciels pour mettre sur le marché des solutions Ubuntu qui s'adressent aussi bien aux utilisateurs de PC, de serveurs ou de périphériques de poche.



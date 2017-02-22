---
title: "Présentation et architecture de SAP HANA sur Azure (grandes instances) | Microsoft Docs"
description: "Présentation de l’architecture du déploiement de SAP HANA sur Azure (grandes instances)."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: 42a2f5488bac192311cd0652286e45a2280032f2
ms.openlocfilehash: 8bb2c8138d5f5ef76490e5439ba2427cbaf9b33d


---
# <a name="overview-and-architecture-of-sap-hana-on-azure-large-instances"></a>Présentation et architecture de SAP HANA sur Azure (grandes instances)
Ce guide sur l’architecture et le déploiement technique, divisé en cinq parties, fournit des informations pour vous aider à déployer SAP sur le nouveau système SAP HANA sur Azure (grandes instances) dans Azure. Il n’est pas exhaustif et ne couvre pas les détails spécifiques concernant l’installation des solutions SAP. Au lieu de cela, il vous fournit des informations précieuses pour faciliter votre déploiement initial et les opérations en cours. Il ne remplace pas la documentation SAP relative à l’installation de SAP HANA (ou les nombreuses Notes de prise en charge SAP qui couvrent la rubrique). Il donne également des détails sur l’installation de SAP HANA sur Azure (grandes instances).


Les cinq parties de ce guide traitent des thèmes suivants :

- [Présentation et architecture](sap-hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Infrastructure et connectivité](sap-hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Installation de SAP HANA](sap-hana-overview-sap-hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Haute disponibilité et récupération d’urgence](sap-hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Dépannage et surveillance](sap-hana-overview-troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="definitions"></a>Définitions

Plusieurs définitions communes sont largement utilisées dans ce guide sur l’architecture et le déploiement technique. Veuillez noter les termes suivants et leur signification :

- **IaaS :** Infrastructure as a Service
- **PaaS :** Platform as a Service
- **SaaS :** Software as a Service
- **Composant SAP :** application SAP individuelle telle que ECC, BW, Solution Manager ou EP. Les composants SAP peuvent être basés sur des technologies ABAP ou Java traditionnelles ou une application non basée sur NetWeaver telle que Business Objects.
- **Environnement SAP :** un ou plusieurs composants SAP regroupés de manière logique pour exécuter une fonction métier telle que le développement, l’assurance qualité, la formation, la récupération d’urgence ou la production.
- **Paysage SAP :** ce terme fait référence à l’ensemble des ressources SAP dans votre paysage informatique. Le paysage SAP englobe tous les environnements de production et les autres types d’environnements.
- **Système SAP :** ensemble couche SGBD/couche Application d’un système de développement SAP ERP, d’un système de test SAP BW, d’un système de production SAP CRM, etc. Les déploiements Azure ne prennent pas en charge la séparation de ces deux couches entre les sites et Azure. Cela signifie qu’un système SAP doit être déployé en local ou dans Azure. Toutefois, vous pouvez déployer les différents systèmes d’un paysage SAP dans Azure ou en local. Par exemple, vous pouvez déployer les systèmes de test et de développement SAP CRM dans Azure et le système de production SAP CRM en local. Dans le cas de SAP HANA sur Azure (grandes instances), il est possible d’exécuter la couche Application SAP des systèmes SAP dans des machines virtuelles Azure et l’instance HANA sur une unité dans le tampon de grande instance.
- **Tampon de grande instance :** une pile d’infrastructure matérielle certifiée SAP HANA TDI et dédiée à l’exécution des instances SAP HANA dans Azure.
- **SAP HANA sur Azure (grandes instances) :** nom officiel de l’offre dans Azure permettant d’exécuter des instances HANA sur le matériel SAP HANA TDI certifié qui est déployé dans les tampons de grande instance dans différentes régions Azure. Le terme associé **grandes instances HANA** est l’abréviation de SAP HANA sur Azure (grandes instances) et est largement utilisé dans ce guide sur le déploiement technique.
- **Intersite :** décrit un scénario dans lequel les machines virtuelles sont déployées vers un abonnement Azure qui dispose d’une connectivité de site à site, multisite ou ExpressRoute entre les centres de données locaux et Azure. Dans la documentation Azure courante, ces types de déploiements sont également décrits comme des scénarios de déploiement entre différents locaux. La connexion a pour but d’étendre les domaines locaux, le répertoire Active Directory/OpenLDAP local et le DNS local à Azure. Le paysage local est étendu aux ressources Azure de l’abonnement. Grâce à cette extension, les machines virtuelles peuvent faire partie du domaine local. Les utilisateurs du domaine local peuvent accéder aux serveurs et exécuter des services sur ces machines virtuelles (tels que les services SGBD). La communication et la résolution de noms entre les machines virtuelles déployées en local et les machines virtuelles déployées dans Azure sont possibles. Il s’agit du scénario classique dans lequel la plupart des ressources SAP sont déployées. Consultez [Planification et conception de la passerelle VPN](../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) et [Création d’un réseau virtuel avec une connexion de site à site à l’aide du portail Azure](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pour plus d’informations.

Il existe de nombreuses ressources supplémentaires qui ont été publiées sur le sujet du déploiement de la charge de travail SAP sur un cloud public Microsoft Azure. Il est vivement recommandé que toute planification d’un déploiement de SAP HANA soit effectuée par une personne expérimentée, connaissant les principes d’Azure IaaS et le déploiement des charges de travail SAP sur Azure IaaS. Les ressources suivantes fournissent davantage d’informations et doivent être consultées avant de continuer :

- [Utilisation de SAP sur des machines virtuelles Windows](../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Utilisation de solutions SAP sur des machines virtuelles Microsoft Azure](../virtual-machines-linux-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="certification"></a>Certification

Outre la certification NetWeaver, SAP requiert une certification spéciale pour SAP HANA, afin de prendre en charge SAP HANA sur certaines infrastructures, telles que Azure IaaS.

La Note SAP principale sur NetWeaver, et dans une certaine mesure sur la certification SAP HANA, est [SAP Note #1928533 – SAP Applications on Azure: Supported Products and Azure VM types (Note SAP n° 1928533 - Applications SAP sur Azure : Types de machines virtuelles Azure et produits pris en charge)](https://launchpad.support.sap.com/#/notes/1928533).

La Note SAP [SAP Note #2316233 - SAP HANA on Microsoft Azure (Large Instances) (Note SAP n° 2316233 - SAP HANA sur Microsoft Azure (grandes instances))](https://launchpad.support.sap.com/#/notes/2316233/E) est également importante. Elle aborde la solution décrite dans ce guide. En outre, vous pouvez exécuter SAP HANA dans le type de machine virtuelle GS5 d’Azure. [Les informations relatives à cette situation sont disponibles sur le site Web SAP](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).

La solution SAP HANA sur Azure (grandes instances) à laquelle il est fait référence dans la Note SAP n° 2316233 fournit aux clients SAP et Microsoft la possibilité de déployer les ressources volumineuses SAP Business Suite, SAP Business Warehouse (BW), S/4 HANA, BW/4HANA ou d’autres charges de travail SAP HANA dans Azure. Cela est basé sur le tampon matériel dédié et certifié SAP-HANA ([SAP HANA Tailored Datacenter Integration – TDI](https://scn.sap.com/docs/DOC-63140)). Grâce à l’exécution en tant que solution configurée SAP TDI, vous avez l’assurance que toutes les applications SAP HANA (y compris SAP Business Suite sur SAP HANA, SAP Business Warehouse (BW) sur SAP HANA, S4/HANA et BW4/HANA) fonctionneront correctement.

En comparaison avec l’exécution de SAP HANA dans les machines virtuelles Azure, cette solution présente l’avantage de fournir des volumes de mémoire beaucoup plus importants. Si vous souhaitez activer cette solution, vous devez avoir connaissance de certains aspects importants :

- La couche Application SAP et les applications autres que SAP s’exécutent dans des machines virtuelles Azure hébergées dans les tampons matériels Azure habituels.
- Les déploiements d’applications, de centres de données et d’infrastructures locaux du client sont connectés à la plateforme cloud Microsoft Azure via Azure ExpressRoute (recommandé) ou un réseau privé virtuel (VPN). Le répertoire Active Directory (AD) et le DNS sont également étendus dans Azure.
- L’instance de base de données SAP HANA pour les charges de travail HANA s’exécute sur SAP HANA sur Azure (grandes instances). Le tampon de grande instance est connecté au réseau Azure, afin que les logiciels s’exécutant dans des machines virtuelles Azure puissent interagir avec l’instance HANA s’exécutant dans les grandes instances HANA.
- Le matériel de SAP HANA sur Azure (grandes instances) est un matériel dédié fourni dans une Infrastructure as a Service (IaaS) avec SUSE Linux Enterprise Server ou Red Hat Enterprise Linux préinstallé. Comme pour les machines virtuelles Azure, les mises à jour et la maintenance supplémentaires du système d’exploitation relèvent de votre responsabilité.
- L’installation de HANA ou de tous les autres composants nécessaires à l’exécution de SAP HANA sur des unités de grandes instances HANA relève de votre responsabilité, tout comme l’ensemble des administrations et opérations respectives en cours de SAP HANA sur Azure.
- Outre les solutions décrites ici, vous pouvez installer d’autres composants dans votre abonnement Azure qui se connecte à SAP HANA sur Azure (grandes instances).  Par exemple, les composants qui permettent la communication avec et directement avec la base de données SAP HANA (serveurs jump, serveurs RDP, SAP HANA Studio, scénarios SAP Data Services for SAP BI ou des solutions de surveillance réseau).
- Comme dans Azure, les grandes instances HANA prennent en charge des fonctionnalités permettant une haute disponibilité et récupération d’urgence.

## <a name="architecture"></a>Architecture

À un niveau élevé, la couche Application SAP de la solution SAP HANA sur Azure (grandes instances) réside sur des machines virtuelles Azure, tandis que la couche Base de données réside sur un matériel configuré SAP TDI situé dans un tampon de grande instance dans la même région Azure qui est connectée à Azure IaaS.

> [!NOTE]
> La couche Application SAP doit être déployée dans la même région Azure avec la couche SGBD SAP. Ce sujet est bien documenté dans les informations publiées sur la charge de travail SAP sur Azure.

L’architecture globale de SAP HANA sur Azure (grandes instances) fournit une configuration matérielle certifiée SAP TDI (serveur non virtualisé, nu et hautes performances pour la base de données SAP HANA), ainsi que la capacité et la flexibilité d’Azure pour mettre à l’échelle les ressources de la couche Application SAP, afin de répondre à vos besoins.

![Présentation de l’architecture de SAP HANA sur Azure (grandes instances)](./media/sap-hana-overview-architecture/image1-architecture.png)

L’architecture ci-dessus est divisée en trois sections :

- **À droite :** une infrastructure locale exécutant différentes applications dans des centres de données avec les utilisateurs finaux accédant aux applications métier (telles que SAP). Dans l’idéal, cette infrastructure locale est ensuite connectée à Azure avec Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Au centre :** montre Azure IaaS et, dans ce cas, l’utilisation des machines virtuelles Azure pour héberger SAP ou d’autres applications qui utilisent SAP HANA en tant que système SGBD. Les instances HANA plus petites qui fonctionnent avec la mémoire que les machines virtuelles Azure fournissent sont déployées dans les machines virtuelles Azure avec leur couche Application. En savoir plus sur les [machines virtuelles](https://azure.microsoft.com/services/virtual-machines/).
<br />La mise en réseau Azure permet de regrouper les systèmes SAP ainsi que d’autres applications dans les réseaux virtuels Azure. Ces réseaux virtuels se connectent à des systèmes locaux et à SAP HANA sur Azure (grandes instances).
<br />Pour les bases de données et les applications SAP NetWeaver qui sont prises en charge pour être exécutées dans Microsoft Azure, consultez [SAP Support Note #1928533 – SAP Applications on Azure: Supported Products and Azure VM types (Note de prise en charge SAP n° 1928533 - Applications SAP sur Azure : Types de machines virtuelles Azure et produits pris en charge)](https://launchpad.support.sap.com/#/notes/1928533). Pour la documentation sur le déploiement des solutions SAP sur Azure, veuillez consulter :

  -  [Utilisation de SAP sur des machines virtuelles Windows](../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Utilisation de solutions SAP sur des machines virtuelles Microsoft Azure](../virtual-machines-linux-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **À gauche :** montre le matériel certifié SAP HANA TDI dans le tampon de grande instance Azure. Les unités de grande instance HANA sont connectées aux réseaux virtuels Azure de votre abonnement à l’aide de la même technologie que la connectivité du système local à Azure.

Le tampon de grande instance Azure combine les composants suivants :

- **Calcul :** serveurs basés sur des processeurs Intel Xeon E7-8890v3 qui fournissent les capacités de calcul nécessaires et sont certifiés SAP HANA.
- **Réseau :** une structure réseau haut débit unifiée qui relie le calcul, le stockage et les composants de réseau local.
- **Stockage :** une infrastructure de stockage qui est accessible via une structure réseau unifiée. La capacité de stockage spécifique est fournie selon la configuration SAP HANA sur Azure particulière (grandes instances) en cours de déploiement (plus de capacité de stockage disponible pour un coût mensuel supplémentaire).

Dans l’infrastructure mutualisée du tampon de grande instance, les clients sont déployés en tant que locataires isolés. Ces locataires ont une relation 1:1 avec l’abonnement Azure. Cela signifie que vous ne pouvez pas accéder à la même instance de SAP HANA sur Azure (grandes instances) qui s’exécute dans un tampon de grande instance Azure en dehors de deux autres abonnements Azure.

Comme avec les machines virtuelles Azure, SAP HANA sur Azure (grandes instances) est proposé dans plusieurs régions Azure. Pour bénéficier de fonctionnalités de récupération d’urgence, vous pouvez choisir de vous y abonner. Les différents tampons de grande instance des diverses régions Azure sont connectés entre eux.

Tout comme vous avez le choix entre différents types de machines virtuelles avec Azure Virtual Machines, vous pouvez choisir parmi différentes références de grandes instances HANA qui sont adaptées aux divers types de charges de travail de SAP HANA. SAP applique la mémoire aux rapports de socket de processeur pour différentes charges de travail en fonction des générations de processeurs Intel. Quatre types de références sont proposés :

À partir de décembre 2016, SAP HANA sur Azure (grandes instances) est disponible en six configurations dans les régions Azure Ouest des États-Unis et Est des États-Unis :

| Solution SAP | UC | RAM | Storage |
| --- | --- | --- | --- |
| Optimisée pour OLAP : SAP BW, BW/4HANA<br /> ou SAP HANA pour les charges de travail OLAP génériques | SAP HANA sur Azure S72<br /> - 2 x processeurs Intel® Xeon® E7-8890 v3 |  768 Go |  3 To |
| --- | SAP HANA sur Azure S144<br /> - 4 x processeurs Intel® Xeon® E7-8890 v3 |  1,5 To |  6 To |
| --- | SAP HANA sur Azure S192<br /> - 4 x processeurs Intel® Xeon® E7-8890 v4 |  2 TO |  8 To |
| Optimisée pour OLTP : SAP Business Suite<br /> sur SAP HANA ou S/4HANA (OLTP),<br /> OLTP générique | SAP HANA sur Azure S72m<br /> - 2 x processeurs Intel® Xeon® E7-8890 v3 |  1,5 To |  6 To |
|---| SAP HANA sur Azure S144m<br /> - 4 x processeurs Intel® Xeon® E7-8890 v3 |  3 TO |  12 To |
|---| SAP HANA sur Azure S192m<br /> - 4 x processeurs Intel® Xeon® E7-8890 v4 |  4 TO |  16 TO |

Les différentes configurations ci-dessus sont référencées dans la Note de prise en charge SAP [SAP Support Note #2316233 – SAP HANA on Microsoft Azure (Large Instances) (Note de prise en charge SAP n° 2316233 - SAP HANA sur Microsoft Azure (grandes instances))](https://launchpad.support.sap.com/#/notes/2316233/E).

Les configurations spécifiques choisies dépendent de la charge de travail, des ressources d’UC et de la mémoire souhaitée. La charge de travail OLTP est en mesure de tirer profit des références optimisées pour les charges de travail OLAP. De même, vous pouvez utiliser des références OLTP pour les charges de travail OLAP HANA. Par contre, vous devez peut-être limiter la mémoire utilisée par HANA à la mémoire certifiée pour la génération du processeur Intel E7 comme [indiqué sur le site Web SAP](http://global12.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/appliances.html) pour le type d’appliance de _mise à l’échelle BW sur HANA_.

Il est important de noter que l’infrastructure complète du tampon de grande instance n’est pas exclusivement allouée à une utilisation pour un seul client. Cela s’applique à SAP HANA sur Azure (grandes instances), tout comme c’est le cas pour les racks de ressources de calcul et de stockage connectés via une structure réseau déployée dans Azure. L’infrastructure de grandes instances HANA, comme Azure, déploie différents &quot;locataires&quot; client qui sont séparés les uns des autres via l’isolement réseau. Par conséquent, ces déploiements de grandes instances HANA n’interfèrent pas entre eux et ne sont pas visibles entre eux. Un locataire déployé dans le tampon de grande instance est attribué à un abonnement Azure. Si vous avez un deuxième abonnement Azure qui nécessite également des grandes instances HANA, il est déployé dans un locataire distinct dans un tampon de grande instance avec l’intégralité de l’isolement réseau qui empêche la communication directe entre ces instances.

Toutefois, il existe des différences importantes entre l’exécution de SAP HANA sur les grandes instances et l’exécution de SAP HANA sur des machines virtuelles Azure déployées dans Azure :

- Il n’existe aucune couche Virtualisation pour SAP HANA sur Azure (grandes instances). Vous bénéficiez des performances du matériel nu sous-jacent.
- Contrairement à Azure, le serveur SAP HANA sur Azure (grandes instances) est dédié à un client spécifique. Un redémarrage ou un arrêt du serveur n’affecte pas le système d’exploitation, ni SAP HANA puisqu’il est déployé sur un autre serveur. (La seule exception est lorsqu’un serveur rencontre des problèmes et qu’un redéploiement doit être effectué sur une autre lame.)
- Contrairement à Azure, où les types de processeurs hôtes sont sélectionnés en fonction du meilleur rapport prix/performances, les types de processeurs choisis pour SAP HANA sur Azure (grandes instances) sont les plus performants de la gamme de processeurs Intel E7v3.

De nombreux clients seront déployés sur le matériel SAP HANA sur Azure (grandes instances), et chacun est protégé des autres par le déploiement de leurs propres réseaux locaux virtuels. Pour connecter de grandes instances HANA dans un réseau virtuel Azure, les composants réseau en place procèdent à la traduction d’adresses réseau entre la plage d’adresses IP de réseau virtuel Azure et l’espace d’adressage IP du réseau local virtuel au sein de l’infrastructure matérielle.

## <a name="operations-model-and-responsibilities"></a>Responsabilités et modèle opérationnel

Le service fourni avec SAP HANA sur Azure (grandes instances) est aligné avec les services Azure IaaS. Vous bénéficiez d’une instance de type grandes instances HANA avec un système d’exploitation installé qui est optimisé pour SAP HANA. Comme avec les machines virtuelles Azure IaaS, la plupart des tâches de renforcement du SE, d’installation des logiciels supplémentaires dont vous avez besoin, le fonctionnement du SE et de HANA et la mise à jour du SE et de HANA relèvent de votre responsabilité. Microsoft ne vous imposera pas les mises à jour du SE, ni de HANA.

![Responsabilités relatives à SAP HANA sur Azure (grandes instances)](./media/sap-hana-overview-architecture/image2-responsibilities.png)

Comme vous pouvez le voir dans le schéma ci-dessus, SAP HANA sur Azure (grandes instances) est une offre Infrastructure as a Service mutualisée. Et par conséquent, la répartition des responsabilités se trouve à la limite entre le SE et l’infrastructure, la plupart du temps. Microsoft est responsable de tous les aspects du service en dessous de la ligne correspondant au système d’exploitation, et vous êtes donc responsable de tout ce qui se trouve au-dessus de la ligne, y compris du système d’exploitation. Ainsi, les méthodes locales les plus récentes que vous utilisez peut-être pour la conformité, la sécurité, la gestion des applications, la base et la gestion du système d’exploitation peuvent toujours être utilisées. Les systèmes sont considérés comme faisant partie de votre réseau, à tous les égards.

Toutefois, ce service étant optimisé pour SAP HANA, vous devez collaborer avec Microsoft dans certains domaines pour utiliser les capacités de l’infrastructure sous-jacente et obtenir de meilleurs résultats.

La liste suivante fournit plus de détails sur chacune des couches et vos responsabilités :

**Réseau :** tous les réseaux internes pour le tampon de grande instance exécutant SAP HANA, son accès au stockage, la connectivité entre les instances (pour la montée en charge et d’autres fonctions), la connectivité avec le paysage et la connectivité à Azure, à l’endroit où se trouve la couche Application SAP résidant dans les machines virtuelles Azure. Il inclut également la connectivité WAN entre les centres de données Azure pour la réplication à des fins de récupération d’urgence. Tous les réseaux sont partitionnés par le locataire et la qualité de service est appliquée.

**Stockage :** le stockage partitionné virtualisé pour tous les volumes requis par les serveurs SAP HANA, ainsi que pour les captures instantanées.

**Serveurs :** les serveurs physiques dédiés à l’exécution des bases de données SAP HANA attribuées aux locataires. Il s’agit de matériel virtualisé.

**SDDC :** le logiciel de gestion qui est utilisé pour gérer les centres de données comme une entité logicielle définie. Il permet à Microsoft de regrouper des ressources à des fins de mise à l’échelle, de disponibilité et de performances.

**Système d’exploitation :** le SE que vous choisissez (SUSE Linux ou Red Hat Linux) et qui s’exécute sur les serveurs. Les images de systèmes d’exploitation que vous obtenez seront les images fournies par chaque fournisseur Linux à Microsoft pour les besoins de l’exécution de SAP HANA. Vous devez être abonné au fournisseur Linux de l’image optimisée pour SAP HANA spécifique. Vos responsabilités incluent l’enregistrement des images auprès du fournisseur du SE. À partir du point de remise de Microsoft, vous êtes également responsable de l’application des autres correctifs du système d’exploitation Linux. Ceci concerne les packages supplémentaires qui peuvent être nécessaires pour réussir l’installation de SAP HANA (veuillez consulter la documentation d’installation les notes relatives à SAP HANA) et qui ne sont pas inclus par le fournisseur Linux spécifique dans ses images de SE optimisé pour SAP HANA. Le client est également responsable de l’application des correctifs du SE associés à un dysfonctionnement et une optimisation du SE et de ses pilotes relatifs au matériel de serveur spécifique. Ou de toute application de correctif de fonctionnalité ou de sécurité du SE. Le client est également responsable de la surveillance et de la planification de capacité des éléments suivants :

- Consommation des ressources d’UC
- Consommation de mémoire
- Volumes de disques liés à l’espace libre, aux entrées et sorties par seconde et à la latence
- Trafic en volume du réseau entre la grande instance HANA et la couche Application SAP

L’infrastructure sous-jacente des grandes instances HANA fournit des fonctionnalités de sauvegarde et de restauration du volume de SE. L’utilisation de cette fonctionnalité relève également de votre responsabilité.

**Intergiciel :** l’instance SAP HANA principalement. L’administration, les opérations et la surveillance relèvent de votre responsabilité. Parmi les fonctionnalités fournies, certaines vous permettent d’utiliser des captures instantanées de stockage à des fins de récupération d’urgence et de sauvegarde/restauration. Ces fonctionnalités sont fournies par l’infrastructure. Toutefois, vos responsabilités incluent également la création d’une architecture haute disponibilité ou de récupération d’urgence avec ces fonctionnalités, leur utilisation et la surveillance de la bonne exécution des captures instantanées de stockage.

**Données :** vos données gérées par SAP HANA et d’autres données telles que les fichiers de sauvegarde situés sur des volumes ou des partages de fichiers. Vos responsabilités incluent la surveillance de l’espace disque disponible et la gestion du contenu sur les volumes, ainsi que la surveillance de l’exécution des sauvegardes sur les volumes de disque et les captures instantanées de stockage. Toutefois, l’exécution réussie de la réplication de données pour les sites de récupération d’urgence est la responsabilité de Microsoft.

**Applications :** les instances d’application SAP ou, dans le cas d’applications autres que SAP, la couche Application de ces applications. Vos responsabilités incluent le déploiement, l’administration, les opérations et la surveillance de ces applications liées à la planification de la capacité de la consommation des ressources d’UC, la consommation de mémoire, la consommation de stockage Azure et la consommation de bande passante réseau au sein des réseaux virtuels Azure et des réseaux virtuels Azure vers SAP HANA sur Azure (grandes instances).

**WAN :** les connexions que vous établissez en local vers des déploiements Azure pour les charges de travail. Si les charges de travail sont essentielles, utilisez Azure ExpressRoute. Cette connexion ne faisant pas partie de la solution SAP HANA sur Azure (grandes instances), vous êtes responsable de sa configuration.

**Archive :** vous préférerez peut-être archiver des copies de données à l’aide de vos propres méthodes dans les comptes de stockage. Cela inclut de la gestion, de la conformité, des coûts et des opérations. Vous êtes chargé de générer des copies de l’archive et des sauvegardes sur Azure et de les stocker d’une manière conforme.

Consultez le [SLA pour SAP HANA sur des grandes instances Azure](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/).

## <a name="sizing"></a>Dimensionnement

Le dimensionnement des grandes instances HANA n’est pas différent du dimensionnement HANA en général. Pour les systèmes déployés et existants, si vous souhaitez migrer d’autres SGBDR vers HANA, SAP fournit plusieurs rapports qui s’exécutent sur vos systèmes SAP existants. Ils vérifient les données et calculent les besoins en mémoire de la table si la base de données est migrée vers HANA. Lisez les Notes SAP suivantes pour obtenir plus d’informations sur l’exécution de ces rapports et savoir comment obtenir leurs correctifs/versions les plus récents :

- SAP Note #1793345 - Sizing for SAP Suite on HANA (Note SAP n° 1793345 - Dimensionnement de SAP Suite sur HANA)
- SAP Note #1872170 - Suite on HANA and S/4 HANA sizing report (Note SAP n° 1872170 - Rapport de dimensionnement de Suite sur HANA et S/4 HANA)
- SAP Note #2121330 - FAQ: SAP BW on HANA Sizing Report (Note SAP n° 2121330 - FAQ : Rapport de dimensionnement SAP BW sur HANA)
- SAP Note #1736976 - Sizing Report for BW on HANA (Note SAP n°1 736976 : Rapport de dimensionnement de BW sur HANA)
- SAP Note #2296290 - New Sizing Report for BW on HANA (Note SAP n° 2296290 - Nouveau rapport de dimensionnement pour BW sur HANA)

Pour les nouvelles implémentations, SAP Quick Sizer permet de calculer les besoins en mémoire de l’implémentation du logiciel SAP sur HANA.

La mémoire requise pour HANA augmentant en même temps que le volume de données, vous souhaiterez connaître la consommation de mémoire actuelle et être en mesure de prédire celle à venir. Selon les besoins en mémoire, vous pouvez ensuite mapper votre demande à l’une des références de grandes instances HANA.

## <a name="requirements"></a>Configuration requise

Il s’agit de la configuration requise pour l’exécution de SAP HANA sur Azure (plus grandes instances).

**Microsoft Azure :**

- Un abonnement Azure qui peut être lié à SAP HANA sur Azure (grandes instances).
- Support Mission Critical Premier Microsoft. Consultez [SAP Support Note #2015553 – SAP on Microsoft Azure: Support Prerequisites (Note de prise en charge SAP n° 2015553 - SAP sur Microsoft Azure : conditions préalables pour le support)](https://launchpad.support.sap.com/#/notes/2015553) pour des informations spécifiques liées à l’exécution de SAP dans Azure.
- Connaître les références des grandes instances HANA dont vous avez besoin après avoir effectué un exercice de dimensionnement avec SAP.

**Connectivité réseau :**

- Azure ExpressRoute entre le système local et Azure : assurez-vous de demander au moins une connexion de 1 Gbit/s à votre FAI pour connecter votre centre de données local à Azure.

**Système d’exploitation :**

- Licences pour SUSE Linux Enterprise Server 12 pour les applications SAP.

> [!NOTE] 
> Le système d’exploitation fourni par Microsoft n’est pas inscrit auprès de SUSE, ni connecté à une instance SMT.

- L’outil de gestion des abonnements SUSE Linux déployé dans Azure sur une machine virtuelle Azure. Cela offre la possibilité d’enregistrer SAP HANA sur Azure (grandes instances) et de le mettre à jour par SUSE (comme il n’existe aucun accès internet au sein du centre de données de grandes instances HANA). 
- Licences pour Red Hat Enterprise Linux 6.7 ou 7.2 pour SAP HANA.

> [!NOTE]
> Le système d’exploitation fourni par Microsoft n’est pas inscrit auprès de Red Hat, ni connecté à une instance Red Hat Subscription Manager.

- Gestionnaire d’abonnements Red Hat déployé dans Azure sur une machine virtuelle Azure. Cela offre la possibilité d’enregistrer SAP HANA sur Azure (grandes instances) et de le mettre à jour par Red Hat (comme il n’existe aucun accès internet direct depuis le locataire déployé sur le tampon de grande instance Azure).
- Contrat de service et de support conclu avec le fournisseur Linux implicitement inclus dans l’abonnement à la version Linux ou autre contrat de service et de support qui couvre la version spécifique de Linux utilisée et qui répond aux critères de SAP.

**Base de données :**

- Licences et composants d’installation logiciels pour SAP HANA (édition Enterprise ou Platform).

**Applications :**

- Licences et composants d’installation logiciels pour toutes les applications SAP se connectant à SAP HANA et associées aux contrats de support SAP.
- Licences et composants d’installation logiciels pour toutes les applications autres que SAP utilisées dans le cadre de l’environnement SAP HANA sur Azure (grandes instances) et associées aux contrats de support.

**Compétences :**

- Expérience et connaissances relatives à Azure IaaS et ses composants.
- Expérience et connaissances relatives au déploiement de charges de travail SAP dans Azure.
- Personnel certifié pour l’installation de SAP HANA.
- Compétences en architecture SAP pour créer la haute disponibilité et la récupération d’urgence autour de SAP HANA.

## <a name="networking"></a>Mise en réseau

L’architecture de mise en réseau Azure est un composant clé pour la réussite du déploiement des applications SAP. En règle générale, les déploiements SAP HANA sur Azure (grandes instances) ont un plus grand paysage SAP avec plusieurs solutions SAP distinctes et tailles de bases de données variées, une consommation des ressources d’UC et une utilisation de la mémoire différentes. Il est très probable que seuls un ou deux de ces systèmes SAP soient basés sur SAP HANA, et votre paysage SAP est certainement un hybride qui utilise :

- Des systèmes SAP déployés en local. En raison de leur taille, ces derniers ne peuvent actuellement pas être hébergés dans Azure ; un exemple classique est un système de production SAP ERP s’exécutant sur Microsoft SQL Server (comme la base de données) avec &gt;100 UC et 1 To de mémoire.
- Des systèmes SAP basés sur SAP HANA déployés en local (par exemple, un système SAP ERP qui requiert 6 To minimum de mémoire pour sa base de données SAP HANA).
- Des systèmes SAP déployés dans des machines virtuelles Azure. Ces systèmes peuvent être des instances de développement, de test, de bac à sable ou de production pour toutes les applications basées sur SAP NetWeaver qui peuvent être déployées correctement dans Azure (sur des machines virtuelles), en fonction de la demande de mémoire et de la consommation de ressources. Ils peuvent également être basés sur des bases de données comme SQL Server (consultez [SAP Support Note #1928533 – SAP Applications on Azure: Supported Products and Azure VM types (Note de prise en charge SAP n° 1928533 - Applications SAP sur Azure : Types de machines virtuelles Azure et produits pris en charge)](https://launchpad.support.sap.com/#/notes/1928533/E)) ou SAP HANA (consultez [SAP HANA Certified IaaS Platforms (Plateformes IaaS certifiées SAP HANA)](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)).
- Des serveurs d’applications SAP déployés dans Azure (sur des machines virtuelles) qui utilisent SAP HANA sur Azure (grandes instances) dans les tampons de grande instance Azure.

Même si un paysage SAP hybride (avec quatre scénarios de déploiement différents minimum) est monnaie courante, il existe des instances de paysage SAP entièrement dans Azure, et comme les machines virtuelles Microsoft Azure deviennent de plus en plus puissantes, le nombre de solutions SAP déployées sur les machines virtuelles Azure va croître.

La mise en réseau Azure dans le contexte des systèmes SAP déployés dans Azure n’est pas difficile. Elle est basée sur les principes suivants :

- Les réseaux virtuels Azure doivent être connectés au circuit Azure ExpressRoute qui se connecte au réseau local.
- Un circuit ExpressRoute doit généralement avoir une bande passante de 1 Gbit/s ou plus. Cela permet de bénéficier d’une bande passante suffisante pour transférer des données entre les systèmes locaux et les systèmes s’exécutant sur des machines virtuelles Azure (ainsi que pour la connexion aux systèmes Azure depuis les systèmes locaux des utilisateurs finaux).
- Tous les systèmes SAP dans Azure doivent être configurés dans les réseaux virtuels Azure pour communiquer entre eux.
- Le répertoire Active Directory et le DNS hébergés en local sont étendus à Azure via ExpressRoute.

**Recommandation :** déployez le paysage SAP complet sur Azure dans un seul abonnement Azure. De nombreuses procédures dans un paysage SAP nécessitent une connectivité réseau transparente, voire moindre, entre les instances de développement, de test et de production. En outre, l’architecture SAP NetWeaver dispose de plusieurs automatismes qui reposent sur cette mise en réseau transparente entre ces différentes instances. Par conséquent, conserver le paysage SAP complet dans un abonnement Azure, même lorsque l’environnement est déployé dans plusieurs régions Azure, est fortement conseillé.

L’architecture et les processus autour de SAP HANA sur Azure (grandes instances) sont basés sur la recommandation ci-dessus.

> [!NOTE] 
> Un seul abonnement Azure peut être associé à un seul locataire dans un tampon de grande instance d’une région Azure spécifique, et inversement, un seul locataire de tampon de grande instance peut être associé à un seul abonnement Azure.

Le déploiement de SAP HANA sur Azure (grandes instances) dans deux régions Azure différentes entraîne le déploiement d’un locataire distinct dans le tampon de grande instance. Toutefois, vous pouvez vous attendre à ce que ces deux locataires finissent dans le même abonnement Azure tant que ces instances font partie du même paysage SAP.

> [!IMPORTANT] 
> Seul le déploiement Azure Resource Manager est pris en charge avec SAP HANA sur Azure (grandes instances).

### <a name="additional-azure-vnet-information"></a>Informations supplémentaires sur les réseaux virtuels Azure

Pour connecter un réseau virtuel Azure à ExpressRoute, une passerelle Azure doit être créée, (consultez [À propos des passerelles de réseau virtuel pour ExpressRoute](../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Une passerelle Azure peut être utilisée avec ExpressRoute pour rediriger vers une infrastructure en dehors d’Azure (ou un tampon de grande instance Azure), ou pour la connexion entre les réseaux virtuels Azure (consultez [Configuration d’une connexion de réseau virtuel à réseau virtuel pour Resource Manager à l’aide de PowerShell](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Vous pouvez connecter la passerelle Azure à quatre connexions ExpressRoute différentes maximum, tant que celles-ci proviennent de MS Enterprise Exchanges (MSEE) distincts.

> [!NOTE] 
> Le débit fourni par une passerelle Azure est différent pour les deux cas d’utilisation (consultez [À propos de la passerelle VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Le débit maximal pouvant être atteint avec une passerelle de réseau virtuel est de 10 Gbits/s, à l’aide d’une connexion ExpressRoute. Gardez à l’esprit que la copie de fichiers entre une machine virtuelle Azure résidant dans un réseau virtuel Azure et un système local (en tant que flux de copie unique) ne bénéficiera pas du débit total des différentes références de passerelles. Pour tirer parti de l’intégralité de la bande passante de la passerelle de réseau virtuel, vous devez utiliser plusieurs flux ou copier différents fichiers dans des flux parallèles d’un seul fichier.

Lors de votre consultation des articles ci-dessus, veuillez noter soigneusement les informations relatives à la disponibilité de la référence de passerelle UltraPerformance dans différentes régions Azure.

### <a name="networking-for-sap-hana-on-azure"></a>Mise en réseau pour SAP HANA sur Azure

Pour la connexion à SAP HANA sur Azure (grandes instances), un réseau virtuel Azure doit être connecté via sa passerelle de réseau virtuel à l’aide d’ExpressRoute au réseau local d’un client. En outre, il doit être connecté via un deuxième circuit ExpressRoute aux grandes instances HANA situées dans un tampon de grande instance Azure. La passerelle de réseau virtuel aura au moins deux connexions ExpressRoute et ces deux connexions partageront la bande passante maximale de la passerelle de réseau virtuel.

> [!IMPORTANT] 
> Étant donné le trafic réseau global entre les couches Base de données et Applications SAP, seules les références de passerelle HighPerformance et UltraPerformance pour les réseaux virtuels sont prises en charge pour la connexion à SAP HANA sur Azure (grandes instances).

![Réseau virtuel Azure connecté à SAP HANA sur Azure (grandes instances) et en local](./media/sap-hana-overview-architecture/image3-on-premises-infrastructure.png)

### <a name="single-sap-system"></a>Système SAP unique

L’infrastructure locale présentée ci-dessus est connectée via ExpressRoute à Azure, et le circuit ExpressRoute se connecte à un routeur Microsoft Enterprise Edge Router (MSEE) (consultez [Présentation technique d’ExpressRoute](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Une fois mise en place, cette route se connecte à la dorsale principale de Microsoft Azure, ainsi qu’à toutes les régions Azure accessibles.

> [!NOTE] 
> Pour exécuter des paysages SAP dans Azure, connectez le MSEE le plus proche de la région Azure dans le paysage SAP. Les tampons de grande instance Azure sont connectés via les appareils MSEE dédiés pour réduire la latence réseau entre les machines virtuelles Azure dans Azure IaaS et les tampons de grande instance.

La passerelle de réseau virtuel pour les machines virtuelles Azure, hébergeant les instances d’application SAP, est connectée à ce circuit ExpressRoute, et ce même réseau virtuel est connecté à un routeur MSEE Router distinct, dédié à la connexion de tampons de grande instance.

Il s’agit d’un exemple simple d’un système SAP unique, où la couche Application SAP est hébergée dans Azure et où la base de données SAP HANA s’exécute sur SAP HANA sur Azure (grandes instances). Nous supposons que le fait que la bande passante de la passerelle de réseau virtuel soit de 2 Gbits/s ou de 10 Gbit/s ne représente pas un goulot d’étranglement.

### <a name="multiple-sap-systems-or-large-sap-systems"></a>Plusieurs systèmes SAP ou grands systèmes SAP

Si plusieurs systèmes SAP ou si de grands systèmes SAP sont déployés en se connectant à SAP HANA sur Azure (grandes instances), il est raisonnable de supposer que le débit de la référence de passerelle de réseau virtuel HighPerformance puisse devenir un goulot d’étranglement. Dans ce cas, la référence UltraPerformance doit être choisie, si elle est disponible. Toutefois, si seulement la référence HighPerformance (2 Gbits/s max.) est disponible, ou s’il est possible que la référence UltraPerformance (10 Gbit/s) ne soit pas suffisante, vous devrez fractionner les couches Application dans plusieurs réseaux virtuels Azure.

Pour une architecture réseau plus évolutive :

- Utilisez plusieurs réseaux virtuels Azure pour une couche Application SAP unique, plus grande.
- Déployez un réseau virtuel Azure distinct pour chaque système SAP déployé, plutôt que de combiner ces systèmes SAP dans des sous-réseaux séparés dans le même réseau virtuel.

 Une architecture réseau plus évolutive pour SAP HANA sur Azure (grandes instances) :

![Déploiement de la couche Application SAP sur plusieurs réseaux virtuels Azure](./media/sap-hana-overview-architecture/image4-networking-architecture.png)

Le déploiement de la couche Application SAP, ou des composants, sur plusieurs réseaux virtuels Azure comme présenté ci-dessus a introduit une augmentation inévitable de la latence qui s’est produite lorsque les applications hébergées dans ces réseaux virtuels Azure communiquent entre elles. Par défaut, le trafic réseau entre les machines virtuelles Azure situées dans différents réseaux virtuels passera par les routeurs MSEE Routers dans cette configuration. Toutefois, depuis septembre 2016, cela peut être évité et optimisé. La méthode d’optimisation et de réduction de la latence de communication entre les deux réseaux virtuels consiste à homologuer vos réseaux virtuels Azure dans la même région. Cela est valable même s’il s’agit d’abonnements différents. Grâce à l’homologation des réseaux virtuels Azure, des machines virtuelles de deux réseaux virtuels Azure peuvent utiliser le réseau Azure principal pour communiquer directement entre elles. La latence est donc la même que si les machines virtuelles se trouvaient dans le même réseau virtuel. Tandis que le trafic d’adressage des plages d’adresses IP connectées via la passerelle du réseau virtuel Azure est acheminé via la passerelle de réseau virtuel individuel du réseau virtuel. Pour obtenir plus d’informations sur l’homologation du réseau virtuel Azure, consultez l’article [Homologation de réseaux virtuels](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-peering-overview).
### <a name="minimal-deployment"></a>Déploiement minimal

Pour un petit système SAP (déploiement minimal), les machines virtuelles Azure hébergent la couche Application SAP dans un système Azure natif (au sein d’un réseau virtuel unique) et se connectent au tampon de grande instance via ExpressRoute. Suivez les étapes ci-dessous pour que SAP HANA sur Azure (grandes instances) soit prêt à être utilisé :

- Collectez des informations spécifiques relatives aux quatre plages d’adresses IP différentes :

  1. Une plage d’adresses /29 pour les connexions P2P à utiliser pour les circuits ExpressRoute.
  
  2. Un bloc CIDR unique /24 (recommandé) à utiliser pour affecter les adresses IP spécifiques nécessaires à SAP HANA sur Azure (grandes instances).
  3. Un ou plusieurs blocs CIDR /24 (recommandé) pour les sous-réseaux de locataire de votre réseau virtuel Azure. Il s’agit des sous-réseaux de l’abonnement Azure du client où se trouveront les machines virtuelles Azure liées à SAP ; les adresses pourront accéder à SAP HANA sur Azure (grandes instances). Il doit y avoir un bloc d’adresses de locataire par sous-réseau, et les blocs peuvent être agrégés s’ils sont contigus et dans le même réseau virtuel.
  4. Un CIDR /28 de vos sous-réseaux de passerelle de réseau virtuel (un CIDR /27 doit être utilisé si vous souhaitez une mise en réseau P2S).

  - Les deux premières plages sont nécessaires (une par abonnement et région Azure). Les plages d’adresses IP indiquées aux points 3 et 4 sont au minimum requises par réseau virtuel Azure, et si plusieurs sous-réseaux/locataires dans un réseau virtuel sont souhaités, plusieurs plages doivent être spécifiées pour le point 3.
![Plages d’adresses IP nécessaires pour le déploiement minimal de SAP HANA sur Azure (grandes instances)](./media/sap-hana-overview-architecture/image5-ip-address-range-a.png)

  -  Si vous configurez plusieurs sous-réseaux de locataire dans un réseau virtuel Azure : ![plages d’adresses IP avec espace d’adressage contigu pour un réseau virtuel Azure](./media/sap-hana-overview-architecture/image6-ip-address-range-b.png)

> [!IMPORTANT] 
> Chaque plage d’adresses IP spécifiée ci-dessus ne doit pas chevaucher une autre plage ; chaque plage doit être discrète et ne doit pas être un sous-réseau d’une autre plage. Seule l’adresse définie aux points 3 et 4 doit être appliquée à des réseaux virtuels Azure, toutes les autres sont utilisées pour le routage et la connectivité de grande instance. En outre, il est recommandé que les plages d’adresses de l’espace d’adressage soient mappées aux plages du sous-réseau et n’aient pas d’espace vide ou non assigné. En cas de chevauchement entre les plages définies aux points 1 et 2 avec les plages définies aux points 3 et 4, le réseau virtuel Azure ne se connecte pas au circuit ExpressRoute.

- Un circuit Express Route est créé par Microsoft entre votre abonnement Azure et le tampon de grande instance.
- Créez un locataire réseau sur le tampon de grande instance.
- Configurez la mise en réseau dans l’infrastructure de SAP HANA sur Azure (grandes instances) pour accepter les adresses IP de la plage spécifiée dans votre réseau virtuel Azure, qui communiqueront avec des grandes instances HANA.
- Configurez la traduction d’adresses réseau dans le locataire du client du tampon de grande instance (afin que l’adresse IP interne du locataire soit mappée à une adresse IP définie par le locataire pour Azure).
- En fonction de la référence SAP HANA sur Azure (grandes instances) spécifique achetée, attribuez une unité de calcul dans un réseau de locataire, allouez et montez le stockage et installez le système d’exploitation (SUSE ou RedHat Linux).

Déploiement minimal de l’architecture réseau SAP HANA sur Azure (grandes instances) :

![Déploiement minimal avec des plages d’adresses IP](./media/sap-hana-overview-architecture/image7-minimal-deployment.png)

### <a name="routing-in-azure"></a>Routage dans Azure

Deux points importants sont à prendre en compte en matière de routage pour SAP HANA sur Azure (grandes instances) :

1. L’accès à SAP HANA sur Azure (grandes instances) se fait uniquement via les machines virtuelles Azure dans la connexion ExpressRoute dédiée, et non directement via le système local. Par conséquent, les clients d’administration et toutes les applications nécessitant un accès direct, telles que SAP Solution Manager s’exécutant en local, ne peuvent pas se connecter à la base de données SAP HANA.

2. SAP HANA sur Azure (grandes instances) a une adresse IP attribuée à partir d’un pool de traduction d’adresses réseau défini. Cette adresse IP est accessible via l’abonnement Azure et ExpressRoute. Puisque l’adresse IP fait partie d’un pool de traduction d’adresses réseau, vous devrez effectuer une configuration de mise en réseau supplémentaire dans l’environnement. Pour plus d’informations, consultez l’article sur l’installation de SAP HANA.

> [!NOTE] 
> Si vous avez besoin de vous connecter à SAP HANA sur Azure (grandes instances) dans un scénario _d’entrepôt de données_, où les applications et/ou les utilisateurs finaux doivent se connecter à la base de données SAP HANA (exécution directe), un autre composant réseau doit être utilisé : un proxy inverse pour router les données dans les deux sens. Par exemple, F5 BIG-IP avec Traffic Manager déployé dans Azure en tant que solution de routage de trafic/pare-feu virtuelle.

### <a name="leveraging-in-multiple-regions"></a>Utilisation dans plusieurs régions

Vous pouvez avoir d’autres raisons de déployer SAP HANA sur Azure (grandes instances) dans plusieurs régions Azure, en plus de la récupération d’urgence. Vous voulez peut-être accéder aux grandes instances HANA à partir de chaque machine virtuelle déployée dans les différents réseaux virtuels dans les régions. Puisque les adresses IP transformées des différents serveurs de grandes instances HANA ne sont pas propagées au-delà des réseaux virtuels Azure (qui sont directement connectés via leur passerelle aux instances), il existe une légère modification de la conception du réseau virtuel présenté ci-dessus : une passerelle de réseau virtuel Azure peut gérer quatre circuits ExpressRoute différents de divers MSEE, et chaque réseau virtuel qui est connecté à l’un des tampons de grande instance peut être connecté au tampon de grande instance dans une autre région Azure.

![Réseaux virtuels Azure connectés aux tampons de grande instance Azure dans différentes régions Azure](./media/sap-hana-overview-architecture/image8-multiple-regions.png)

La figure ci-dessus illustre comment les différents réseaux virtuels Azure dans les deux régions sont connectés à deux circuits ExpressRoute distincts qui sont utilisés pour se connecter à SAP HANA sur Azure (grandes instances) dans les deux régions Azure. Les connexions nouvellement introduites sont représentées par les lignes rouges rectangulaires. Avec ces connexions en dehors des réseaux virtuels Azure, les machines virtuelles s’exécutant dans l’un de ces réseaux virtuels peuvent accéder à chacune des différentes unités de grandes instances HANA déployées dans les deux régions (en supposant que vous utilisez le même abonnement). Comme vous voyez dans le schéma ci-dessus, il est supposé que vous disposez de deux connexions ExpressRoute du système local aux deux régions Azure, qui sont recommandées pour des raisons de récupération d’urgence.

> [!IMPORTANT] 
> Si plusieurs circuits ExpressRoute sont utilisés, les paramètres de BGP Local Preference et du préfixe AS Path doivent être utilisés pour garantir le routage correct du trafic.





<!--HONumber=Jan17_HO1-->



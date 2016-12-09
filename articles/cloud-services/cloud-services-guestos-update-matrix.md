---
title: "En savoir plus sur les dernières publications du SE invité de Microsoft Azure | Microsoft Docs"
description: "Dernières informations de publication et de compatibilité du Kit de développement logiciel (SDK) pour le SE invité de Microsoft Azure Cloud Services."
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: 
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/01/2016
ms.author: raiye
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 46a27d34778b20ba23f406e58189f623e70a5941


---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Versions du SE invité et matrice de compatibilité du Kit de développement logiciel (SDK) Azure
Fournit des informations récentes sur les dernières publications du SE (système d’exploitation) invité de Microsoft Azure pour Cloud Services. Ces informations vous aideront à planifier votre mise à niveau avant la désactivation d’un SE invité. Si vous configurez vos rôles pour utiliser les mises à jour *automatiques* d’un SE invité comme cela est décrit dans [Paramètres de mise à jour du SE invité Azure][Paramètres de mise à jour du SE invité Azure], il n’est pas indispensable que vous lisiez cette page.

> [!IMPORTANT]
> Le contenu de cette page s’applique aux rôles web et de travail Cloud Services, qui s’exécutent sur un SE invité. Il **ne s’applique pas** aux machines virtuelles IaaS. 
> 
> 

<!-- -->

> [!TIP]
> Abonnez-vous au [Flux RSS de mise à jour du SE invité][rss] pour recevoir en temps voulu les notifications relatives à toutes les modifications de SE invités.
> 
> 

Vous ne savez pas exactement ce qu’est un SE invité ou en quoi consistent les publications du SE invité ? Lisez [cette](#how-it-works) section.

## <a name="news-updates"></a>Nouvelles mises à jour
###### <a name="october-23-2016"></a>**23 octobre 2016**
Windows Server 2016 sera publié en tant que famille du système d’exploitation 5 le 1er novembre 2016, avec prise en charge de .NET 4.6.

###### <a name="september-13-2016"></a>**13 septembre 2016**
Le déploiement du système d’exploitation invité du mois de septembre commence le 13 septembre 2016, et devrait être publié le 13 octobre 2016.

###### <a name="august-9-2016"></a>**9 août 2016**
Le déploiement du système d’exploitation invité du mois d’août commence le 9 août 2016, et devrait être publié le 8 septembre 2016. 

###### <a name="july-13-2016"></a>**13 juillet 2016**
Le déploiement du SE invité commence le 13 juillet 2016, et devrait être publié le 12 août 2016. 

###### <a name="june-15-2016"></a>**15 juin 2016**
Le déploiement du SE invité commence le 15 juin 2016, et devrait être publié le 14 juillet 2016. 

###### <a name="may-17-2016"></a>**17 mai 2016**
Le déploiement du SE invité du mois de mai commence le 17 mai 2016 et devrait être publié le 10 juin 2016. 

###### <a name="april-18-2016"></a>**18 avril 2016**
Le déploiement du SE invité du mois d’avril commence le 18 avril 2016, et devrait être publié le 12 mai 2016. 

###### <a name="march-14-2016"></a>**14 mars 2016**
Le déploiement du SE invité du mois de mars commence le 14 mars 2016, et devrait être publié le 8 avril 2016. 

###### <a name="february-22-2016"></a>**22 février 2016**
Le déploiement du SE invité du mois de février commence le 22 février 2016, et devrait être publié le 9 mars 2016.

###### <a name="january-18-2016"></a>**18 janvier 2016**
Le déploiement du SE invité du mois de janvier commence le 18 janvier 2016, et devrait être publié le 12 février 2016.

###### <a name="january-4-2016"></a>**4 janvier 2016**
Le déploiement du système d’exploitation invité de 201511-02 de novembre a été publié le 4 janvier 2016. Cette version du système d'exploitation n'étant pas définie comme système d'exploitation par défaut pour la mise à jour automatique, le délai d’approvisionnement du déploiement du système d’exploitation invité de 201511-02 de novembre devrait être légèrement plus long. 

## <a name="releases"></a>Publications
## <a name="family-5-releases"></a>Publications de famille 5
**Windows Server 2016**

Prend en charge .NET 4.0, 4.5, 4.5.1, 4.5.2, 4.6, 4.6.1, 4.6.2

> [!NOTE]
> Les dates précédées de * sont susceptibles de changer.
> 
> 

| Chaîne de configuration | Date de lancement | Date de désactivation | Date d’expiration |
| --- | --- | --- | --- |
| WA-GUEST-OS-5.2_201610-02 |1er novembre 2016 |30 janvier 2017 |TBD |

## <a name="family-4-releases"></a>Publications de famille 4
**Windows Server 2012 R2**

Prend en charge .NET 4.0, 4.5, 4.5.1, 4.5.2

> [!NOTE]
> Les dates précédées de * sont susceptibles de changer.
> 
> 

| Chaîne de configuration | Date de lancement | Date de désactivation | Date d’expiration |
| --- | --- | --- | --- |
| WA-GUEST-OS-4.36_201609-01 |13 octobre 2016 |Publication 4.38 |TBD |
| WA-GUEST-OS-4.35_201608-01 |13 septembre 2016 |Publ 4.37 |TBD |
| WA-GUEST-OS-4.34_201607-01 |8 août 2016 |Publication 4.36 |TBD |
| WA-GUEST-OS-4.33_201606-01 |13 juillet 2016 |13 octobre 2016 |TBD |
| WA-GUEST-OS-4.32_201605-01 |10 juin 2016 |8 septembre 2016 |TBD |
| WA-GUEST-OS-4.31_201604-01 |2 mai 2016 |13 août 2016 |TBD |
| WA-GUEST-OS-4.30_201603-01 |7 avril 2016 |10 juillet 2016 |TBD |
| WA-GUEST-OS-4.29_201602-02 |12 mars 2016 |2 juin 2016 |TBD |
| WA-GUEST-OS-4.28_201601-01 |12 février 2016 |7 mai 2016 |TBD |
| WA-GUEST-OS-4.27_201512-01 |12 janvier 2016 |12 avril 2016 |TBD |
| ~~WA-GUEST-OS-4.26_201511-02~~ |4 janvier 2016 |12 mars 2016 |TBD |
| ~~WA-GUEST-OS-4.26_201511-01~~ |10 décembre 2015 |12 mars 2016 |TBD |
| ~~WA-GUEST-OS-4.25_201510-01~~ |6 novembre 2015 |12 février 2016 |TBD |
| ~~WA-GUEST-OS-4.24_201509-01~~ |1er octobre 2015 |10 janvier 2016 |TBD |
| ~~WA-GUEST-OS-4.23_201508-02~~ |9 septembre 2015 |6 décembre 2015 |TBD |
| ~~WA-GUEST-OS-4.22_201507-02~~ |7 août 2015 |1 novembre 2015 |TBD |
| ~~WA-GUEST-OS-4.21_201506-01~~ |9 juillet 2015 |9 octobre 2015 |TBD |
| ~~WA-GUEST-OS-4.20_201505-02~~ |12 juin 2015 |7 septembre 2015 |TBD |
| ~~WA-GUEST-OS-4.19_201504-01~~ |17 avril 2015 |9 août 2015 |TBD |

## <a name="family-3-releases"></a>Publications de famille 3
**Windows Server 2012**

Prend en charge .NET 4.0, 4.5, 4.5.1, 4.5.2

> [!NOTE]
> Les dates précédées de * sont susceptibles de changer.
> 
> 

| Chaîne de configuration | Date de lancement | Date de désactivation | Date d’expiration |
| --- | --- | --- | --- |
| WA-GUEST-OS-3.43_201609-01 |13 octobre 2016 |Publication 3.45 |TBD |
| WA-GUEST-OS-3.42_201608-01 |13 septembre 2016 |Publ 3.44 |TBD |
| WA-GUEST-OS-3.41_201607-01 |8 août 2016 |Publication 3.43 |TBD |
| WA-GUEST-OS-3.40_201606-01 |13 juillet 2016 |13 octobre 2016 |TBD |
| WA-GUEST-OS-3.39_201605-01 |10 juin 2016 |8 septembre 2016 |TBD |
| WA-GUEST-OS-3.38_201604-01 |2 mai 2016 |13 août 2016 |TBD |
| WA-GUEST-OS-3.37_201603-01 |7 avril 2016 |10 juillet 2016 |TBD |
| WA-GUEST-OS-3.36_201602-02 |12 mars 2016 |2 juin 2016 |TBD |
| WA-GUEST-OS-3.35_201601-01 |12 février 2016 |7 mai 2016 |TBD |
| WA-GUEST-OS-3.34_201512-01 |12 janvier 2016 |12 avril 2016 |TBD |
| ~~WA-GUEST-OS-3.33_201511-02~~ |4 janvier 2016 |12 mars 2016 |TBD |
| ~~WA-GUEST-OS-3.33_201511-01~~ |10 décembre 2015 |12 mars 2016 |TBD |
| ~~WA-GUEST-OS-3.32_201510-01~~ |6 novembre 2015 |12 février 2016 |TBD |
| ~~WA-GUEST-OS-3.31_201509-01~~ |1er octobre 2015 |10 janvier 2016 |TBD |
| ~~WA-GUEST-OS-3.30_201508-02~~ |9 septembre 2015 |6 décembre 2015 |TBD |
| ~~WA-GUEST-OS-3.29_201507-02~~ |7 août 2015 |1 novembre 2015 |TBD |
| ~~WA-GUEST-OS-3.28_201506-01~~ |9 juillet 2015 |9 octobre 2015 |TBD |
| ~~WA-GUEST-OS-3.27_201505-02~~ |12 juin 2015 |7 septembre 2015 |TBD |
| ~~WA-GUEST-OS-3.26_201504-01~~ |17 avril 2015 |9 août 2015 |TBD |

## <a name="family-2-releases"></a>Publications de famille 2
**Windows Server 2008 R2 SP1**

Prend en charge .NET 3.5, 4.0, 4.5, 4.5.1, 4.5.2

> [!NOTE]
> Les dates précédées de * sont susceptibles de changer.
> 
> 

| Chaîne de configuration | Date de lancement | Date de désactivation | Date d’expiration |
| --- | --- | --- | --- |
| WA-GUEST-OS-2.55_201609-01 |13 octobre 2016 |Publication 2.57 |TBD |
| WA-GUEST-OS-2.54_201608-01 |13 septembre 2016 |Publ 2.56 |TBD |
| WA-GUEST-OS-2.53_201607-01 |8 août 2016 |Publication 2.55 |TBD |
| WA-GUEST-OS-2.52_201606-01 |13 juillet 2016 |13 octobre 2016 |TBD |
| WA-GUEST-OS-2.51_201605-01 |10 juin 2016 |8 septembre 2016 |TBD |
| WA-GUEST-OS-2.50_201604-01 |2 mai 2016 |13 août 2016 |TBD |
| WA-GUEST-OS-2.49_201603-01 |7 avril 2016 |10 juillet 2016 |TBD |
| WA-GUEST-OS-2.48_201602-02 |12 mars 2016 |2 juin 2016 |TBD |
| WA-GUEST-OS-2.47_201601-01 |12 février 2016 |7 mai 2016 |TBD |
| WA-GUEST-OS-2.46_201512-01 |12 janvier 2016 |12 avril 2016 |TBD |
| ~~WA-GUEST-OS-2.45_201511-02~~ |4 janvier 2016 |12 mars 2016 |TBD |
| ~~WA-GUEST-OS-2.45_201511-01~~ |10 décembre 2015 |12 mars 2016 |TBD |
| ~~WA-GUEST-OS-2.44_201510-01~~ |6 novembre 2015 |12 février 2016 |TBD |
| ~~WA-GUEST-OS-2.43_201509-01~~ |1er octobre 2015 |10 janvier 2016 |TBD |
| ~~WA-GUEST-OS-2.42_201508-02~~ |9 septembre 2015 |6 décembre 2015 |TBD |
| ~~WA-GUEST-OS-2.41_201507-02~~ |7 août 2015 |1 novembre 2015 |TBD |
| ~~WA-GUEST-OS-2.40_201506-01~~ |9 juillet 2015 |9 octobre 2015 |TBD |
| ~~WA-GUEST-OS-2.39_201505-02~~ |12 juin 2015 |7 septembre 2015 |TBD |
| ~~WA-GUEST-OS-2.38_201504-01~~ |17 avril 2015 |9 août 2015 |TBD |

## <a name="msrc-patch-updates"></a>Mises à jour correctives MSRC
La liste des correctifs inclus dans chaque publication mensuelle du SE invité est disponible [ici][patches].

## <a name="sdk-support"></a>Prise en charge des Kits de développement logiciel (SDK)
La [stratégie de déclassement pour le Kit de développement logiciel (SDK) Microsoft Azure][retire policy sdk] indique que seules les versions postérieures à la version 2.2 sont prises en charge. Cependant, certaines familles de SE invités permettent l’utilisation de versions antérieures. Vous devez toujours utiliser le Kit de développement logiciel (SDK) pris en charge le plus récent. 

| Famille de SE invité | Versions du Kit de développement logiciel (SDK) compatibles |
| --- | --- |
| 5 |Versions 2.9.5.1 et ultérieures |
| 4 |Versions 2.1 et ultérieures |
| 3 |Versions 1.8 et ultérieures |
| 2 |Versions 1.3 et ultérieures |
| 1 |Versions 1.0 et ultérieures |

## <a name="guest-os-release-information"></a>Informations de publication du SE invité
Trois dates sont importantes à propos des publications du SE invité : la date de **publication**, la date de **désactivation** et la date **d’expiration**. Un SE invité est considéré comme disponible quand il est proposé dans le portail et qu’il peut être sélectionné comme SE invité cible. Quand un SE invité atteint sa date de **désactivation** , il est supprimé d’Azure. Toutefois, tous les services cloud qui ciblent ce SE invité continuent de fonctionner normalement. 

Durant la période entre la date de **désactivation** et la date **d’expiration**, vous pouvez facilement passer de votre SE invité actuel à une version plus récente. Si vous utilisez la mise à jour *automatique* pour votre SE invité, vous disposez toujours de la version la plus récente. Vous n’avez donc pas à vous soucier de la date d’expiration. 

Après la date **d’expiration** du SE invité, tous les services cloud qui utilisent encore ce SE invité sont arrêtés, supprimés ou mis à niveau de manière forcée. Des informations supplémentaires sur la stratégie de déclassement sont fournies [ici][retirepolicy].

## <a name="guest-os-family-version-explanation"></a>Informations sur les versions des familles de SE invité
Les familles de SE invité sont basées sur les versions publiées de Microsoft Windows Server. Le SE invité est le système d’exploitation sous-jacent sur lequel s’exécute Azure Cloud Services. Chaque SE invité possède un numéro de famille, de version et de publication. 

* **Guest OS family**  
   Version du système d’exploitation Windows Server sur laquelle est basé un SE invité. Par exemple, la *famille 3* est basée sur Windows Server 2012.
* **Version de SE invité**  
  Correspond à l’image de la famille de SE invité et aux correctifs [Microsoft Security Response Center (MSRC)][msrc] appropriés qui sont disponibles à la date de production de la nouvelle version du SE invité. Il est possible que les correctifs ne soient pas tous inclus. 
  
    Les numéros commencent à 0 et augmentent de 1 chaque fois qu’un nouvel ensemble de mises à jour est ajouté. Les zéros à droite sont uniquement affichés s’ils sont importants. Autrement dit, la version 2.10 est une version différente, bien plus récente, que la version 2.1.
* **Publication de SE invité**  
   Nouvelle publication d’une version de SE invité. Une nouvelle publication est produite si Microsoft détecte des problèmes pendant les tests nécessitant des modifications. La publication la plus récente remplace toujours les publications précédentes, publiques ou non. Le portail Azure Classic permet uniquement aux utilisateurs de choisir la publication la plus récente pour une version donnée. Les déploiements s’exécutant sur une publication antérieure ne sont généralement pas mis à niveau de force en fonction de la gravité du bogue. 

Dans l’exemple ci-dessous, 2 est la famille, 12 est la version et « rel2 » est la publication.

**Publication du SE invité** - 2.12 rel2

**Chaîne de configuration pour cette publication** - WA-GUEST-OS-2.12_201208-02

Ces informations sont intégrées dans la chaîne de configuration d’un SE invité, ainsi qu’une date indiquant les correctifs MSRC envisagés pour cette publication. Dans cet exemple, les correctifs MSRC produits pour Windows Server 2008 R2 jusqu’à août 2012 inclus ont été envisagés pour l’inclusion. Seuls les correctifs s’appliquant spécifiquement à cette version de Windows Server sont inclus. Par exemple, si un correctif logiciel de MSRC s’applique à Microsoft Office, il ne sera pas inclus, car ce produit ne fait pas partie de l’image de base de Windows Server. 

## <a name="guest-os-system-update-process"></a>Processus de mise à jour du SE invité
Cette page contient des informations sur les prochaines publications de SE invités. Les clients ont indiqué qu’ils souhaitent être informés lors d’une publication, car leurs rôles de service cloud redémarreront s’ils sont définis pour la mise à jour « Automatique ». Les publications de SE invités se produisent généralement au moins 5 jours après la publication de mise à jour MSRC survenant le deuxième mardi de chaque mois. Les nouvelles publications incluent tous les correctifs MSRC adéquats pour chaque famille de SE invité. 

Microsoft Azure publie constamment des mises à jour. Le SE invité n’est qu’une mise à jour parmi d’autres. Une version peut être affectée par un certain nombre de facteurs trop nombreux pour être répertoriés ici. En outre, Azure s’exécute sur des centaines de milliers d’ordinateurs. Cela signifie qu’il est impossible de donner une date et une heure précises pour le redémarrage de vos rôles. Nous mettrons à jour le [Flux RSS de mise à jour du SE invité][rss] avec les dernières informations dont nous disposons, mais considérez les dates présentées comme approximatives. Nous sommes conscients que cela peut être problématique pour les clients et travaillons sur un plan pour limiter ou régler ces redémarrages. 

La propagation complète sur Azure d’une nouvelle publication de SE invité peut prendre du temps. Lorsque les services sont mis à jour pour le nouveau SE invité, ils sont redémarrés en respectant les domaines de mise à jour. Les services définis pour utiliser des mises à jour « Automatiques » obtiennent une publication en premier. Après la mise à jour, la nouvelle version du SE invité est répertoriée pour votre service dans le portail Azure Classic. De nouvelles publications peuvent se produire pendant cette période. Il est possible que certaines versions soient déployées sur de longues périodes et donc que des redémarrages de mise à niveau automatiques ne se produisent pas pendant plusieurs semaines après la date officielle de la publication. Une fois qu’un SE invité est disponible, vous pouvez choisir explicitement cette version à partir du portail ou dans votre fichier de configuration. 

Pour consulter un grand nombre d’informations utiles sur les redémarrages et des orientations vers d’autres informations techniques sur les mises à jour de SE invités et hôtes, consultez le blog de MSDN intitulé [Redémarrage d’instance de rôle en raison d’une mise à niveau du SE][restarts].

Si vous procédez à une mise à jour manuelle de votre SE invité, consultez la page [Stratégie de suppression du SE invité][retirepolicy].

## <a name="guest-os-supportability-and-retirement-policy"></a>Prise en charge et stratégie de suppression du SE invité
La prise en charge et la stratégie de suppression du SE invité sont détaillées [ici][retirepolicy].

[Installer .NET sur un rôle de service cloud]: https://azure.microsoft.com/en-us/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Paramètres de mise à jour du SE invité Azure]: cloud-services-how-to-configure.md
[rss]: http://sxp.microsoft.com/feeds/3.0/msdntn/WindowsAzureOSUpdates
[ssl3 announcement]: http://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
[ssl3-fixit]: http://go.microsoft.com/?linkid=9863266
[MS14-066]: https://technet.microsoft.com/library/security/ms14-066.aspx
[MS14-046]: https://technet.microsoft.com/library/security/ms14-046.aspx
[retire policy sdk]: https://msdn.microsoft.com/library/dn479282.aspx
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: http://azure.microsoft.com/support/options/
[net install pkg]: http://www.microsoft.com/download/details.aspx?id=42643
[msrc]: http://www.microsoft.com/security/msrc/default.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md




<!--HONumber=Nov16_HO3-->



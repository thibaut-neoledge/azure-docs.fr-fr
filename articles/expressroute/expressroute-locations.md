---
title: Emplacements ExpressRoute | Microsoft Docs
description: "Cet article fournit une vue d’ensemble détaillée des emplacements où les services sont proposés et de la façon de se connecter à des régions Azure."
services: expressroute
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
ms.assetid: c878513a-d594-42ad-8b0e-403efd0c4b25
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 45b3c231f28b810c5fe1361cee1c896c58b52993
ms.openlocfilehash: ae966cd39f8763e038acb5e992f388b8c0ad28ef


---
# <a name="expressroute-partners-and-peering-locations"></a>Partenaires ExpressRoute et emplacements d’homologation
Les tables de cet article offrent des informations sur les fournisseurs de connectivité ExpressRoute, la couverture géographique ExpressRoute, les services cloud Microsoft pris en charge via ExpressRoute et les intégrateurs système ExpressRoute.

## <a name="a-namepartnersaexpressroute-connectivity-providers"></a><a name="partners"></a>Fournisseurs de connectivité ExpressRoute
ExpressRoute est pris en charge dans tous les emplacements et régions Azure. La carte ci-dessous fournit une liste des régions Azure et des emplacements ExpressRoute. Les emplacements ExpressRoute se réfèrent à ceux où Microsoft s’associe à plusieurs fournisseurs de services.

![Carte des emplacements][0]

Vous aurez accès aux services Azure dans toutes les régions au sein d’une région géopolitique si vous êtes connecté à au moins un emplacement ExpressRoute dans la région géopolitique.

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>Régions Azure vers des emplacements ExpressRoute au sein d’une région géopolitique.
Le tableau ci-dessous fournit une carte des régions Azure vers des emplacements ExpressRoute au sein d’une région géopolitique.

| **Région géopolitique** | **Régions Azure** | **Emplacements ExpressRoute** |
| --- | --- | --- |
| **Amérique du Nord** |Est des États-Unis, Ouest des États-Unis, Est des États-Unis 2, Centre des États-Unis, Centre-Sud des États-Unis, Centre-Nord des États-Unis, Centre du Canada, Est du Canada |Atlanta, Chicago, Dallas, Las Vegas, Los Angeles, New York, Seattle, Silicon Valley, Washington DC, Montréal+, Québec+, Toronto |
| **Amérique du Sud** |Sud du Brésil |São Paulo |
| **Europe** |Europe du Nord, Europe de l’Ouest, Ouest du Royaume-Uni, Sud du Royaume-Uni |Amsterdam, Dublin, Londres, Newport (Pays de Galles), Paris |
| **Asie** |Asie orientale, Asie du Sud-Est |Hong Kong, Singapour |
| **Japon** |Ouest du Japon, Est du Japon |Osaka, Tokyo |
| **Australie** |Sud-est de l’Australie |Est de l’Australie |Melbourne, Sydney |
| **Inde** |Inde-Ouest, Inde-Centre, Inde-Sud |Chennai, Mumbai |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Régions et limites géopolitiques pour les clouds nationaux
Le tableau ci-dessous fournit des informations sur les régions et les limites géopolitiques et des clouds nationaux.

| **Région géopolitique** | **Régions Azure** | **Emplacements ExpressRoute** |
| --- | --- | --- | --- |
| **Cloud du gouvernement des États-Unis** |Gouvernement américain - Iowa, Gouvernement américain - Virginie |Chicago, Dallas, New York, Washington DC |
| **Chine** |Chine du Nord, Chine orientale |Beijing, Shanghai |
| **Allemagne** |Allemagne centrale, Allemagne de l’est |Berlin, Francfort |

La connectivité entre les régions géopolitiques n’est pas prise en charge dans la référence ExpressRoute Standard. Vous devez activer le module complémentaire ExpressRoute Premium pour prendre en charge la connectivité globale. La connectivité à des environnements de cloud nationaux n’est pas prise en charge. En cas de besoin, vous pouvez collaborer avec votre fournisseur de connectivité.

## <a name="a-namelocationsaconnectivity-provider-locations"></a><a name="locations"></a>Emplacements de fournisseur de connectivité
> [!div class="op_single_selector"]
> * [Emplacements par fournisseur](expressroute-locations.md#locations)
> * [Fournisseurs par emplacement](expressroute-locations-providers.md#locations)
>
>

### <a name="production-azure"></a>Production Azure
| **Fournisseur de services** | **Microsoft Azure** | **Office 365 et CRM Online** | **Emplacements** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/cloud-services-applications/azure-expressroute/)** |Pris en charge |Pris en charge |Melbourne, Sydney |
| **[Aryaka Networks](http://www.aryaka.com/)** |Pris en charge |Pris en charge |Amsterdam, Dallas, Silicon Valley, Singapour, Tokyo, Washington DC |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Pris en charge |Pris en charge |Amsterdam, Chicago, Dallas, Londres, Silicon Valley, Singapour, Sydney, Washington DC |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Pris en charge |Pris en charge |Toronto |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** |Pris en charge |Pris en charge |Amsterdam, Hong Kong, Londres, Silicon Valley, Singapour, Sydney, Tokyo, Washington DC |
| **[CenturyLink](http://www.centurylink.com/business/enterprise/services/data-network/mpls-vpn.html)** |Bientôt disponible |Bientôt disponible |Silicon Valley |
| **China Telecom Global** |Pris en charge |Non pris en charge |Hong Kong |
| **[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** |Pris en charge |Pris en charge |Dallas, Montréal+, Toronto |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Pris en charge |Pris en charge |Amsterdam, Dublin, Londres, Tokyo |
| **Comcast** |Pris en charge |Pris en charge |Chicago, Silicon Valley, Washington DC |
| **Console**| Pris en charge | Pris en charge |Silicon Valley |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Pris en charge |Pris en charge |Los Angeles |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Pris en charge |Pris en charge |Amsterdam, Atlanta, Chicago, Dallas, Hong Kong, Londres, Los Angeles, Melbourne, New York, Osaka, Paris+, Sao Paulo, Seattle, Silicon Valley, Singapour, Sydney, Tokyo, Toronto, Washington DC |
| **euNetworks** |Pris en charge |Pris en charge |Amsterdam |
| **GÉANT** |Pris en charge |Pris en charge |Amsterdam |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Pris en charge |Pris en charge |Osaka, Tokyo |
| **[InterCloud](https://www.intercloud.com/)** |Pris en charge |Pris en charge |Amsterdam, Londres, Singapour, Washington DC |
| **Internet Solutions - Cloud Connect** |Pris en charge |Pris en charge |Amsterdam, Londres |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)** |Pris en charge |Pris en charge |Amsterdam, Londres, Paris |
| **Jisc** |Pris en charge |Pris en charge |Londres |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Pris en charge |Pris en charge |Amsterdam, Chicago, Dallas, Las Vegas+, Londres, Seattle, Silicon Valley, Washington DC |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Pris en charge |Pris en charge |Dallas, Hong Kong, Las Vegas, Los Angeles, Melbourne, New York, Seattle, Singapour, Sydney, Toronto, Washington DC |
| **MTN** |Pris en charge |Pris en charge |Londres |
| **[Next Generation Data](http://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Pris en charge |Pris en charge |Newport (Nouvelle-Galles du Sud) |
| **NEXTDC** |Pris en charge |Pris en charge |Melbourne, Sydney |
| **NTT Communications** |Pris en charge |Pris en charge |Londres, Los Angeles, Osaka, Singapour, Tokyo, Washington DC |
| **[Orange](http://www.orange-business.com/en/products/business-vpn-galerie)** |Pris en charge |Pris en charge |Amsterdam, Hong Kong, Londres, Silicon Valley, Singapour, Sydney, Washington DC |
| **PCCW Global Limited** |Pris en charge |Pris en charge |Hong Kong |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Pris en charge |Pris en charge |Chennai |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Pris en charge |Pris en charge |Singapour |
| **Softbank** |Pris en charge |Pris en charge |Osaka, Tokyo |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Pris en charge |Pris en charge |Amsterdam, Chennai, Hong Kong, Londres, Mumbai, Silicon Valley, Singapour, Washington DC |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** |Pris en charge |Pris en charge |Amsterdam, Dublin, Londres |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Pris en charge |Pris en charge |São Paulo |
| **Telenor** |Pris en charge |Pris en charge |Amsterdam, Londres |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Pris en charge |Pris en charge |Melbourne, Sydney |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** |Pris en charge |Pris en charge |Amsterdam, Hong Kong, Londres, Silicon Valley, Singapour, Sydney, Tokyo, Washington DC |
| **[Vodafone](http://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Pris en charge |Non pris en charge |Londres |
| **[Zayo Group](http://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Pris en charge |Pris en charge |Chicago, Los Angeles, New York, Silicon Valley, Toronto, Washington DC |

 **+** = bientôt disponible

### <a name="national-cloud-environment"></a>Environnement de cloud national

### <a name="us-government-cloud"></a>Cloud du gouvernement des États-Unis
| **Fournisseur de services** | **Microsoft Azure** | **Office 365** | **Emplacements** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Pris en charge |Pris en charge |Chicago, Washington DC |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Pris en charge |Pris en charge |Chicago, Dallas, New York, Washington DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Pris en charge |Pris en charge |Chicago, New York+, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Pris en charge |Pris en charge |Chicago, Dallas, New York, Washington DC |

### <a name="china"></a>Chine
| **Fournisseur de services** | **Microsoft Azure** | **Office 365** | **Emplacements** |
| --- | --- | --- | --- |
| **China Telecom** |Pris en charge |Non pris en charge |Beijing, Shanghai |

Pour plus d’informations, consultez [ExpressRoute en Chine](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Allemagne
| **Fournisseur de services** | **Microsoft Azure** | **Office 365** | **Emplacements** |
| --- | --- | --- | --- |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Pris en charge |Non pris en charge |Berlin+, Francfort |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Pris en charge |Non pris en charge |Francfort |
| **e-shelter** |Pris en charge |Non pris en charge |Berlin |
| **Interxion** |Pris en charge |Non pris en charge |Francfort |

## <a name="a-namenonpartnersaconnectivity-through-service-providers-not-listed"></a><a name="nonpartners"></a>Connectivité via des fournisseurs de services non répertoriés
Si votre fournisseur de connectivité ne se trouve pas dans la liste des sections précédentes, vous pouvez quand même créer une connexion.

* Vérifiez auprès de votre fournisseur de connectivité s’il est connecté à l’un des échanges dans le tableau ci-dessous. Vous pouvez consulter les liens ci-dessous pour recueillir des informations supplémentaires sur les services proposés par les fournisseurs d’échange. Plusieurs fournisseurs de connectivité sont déjà connectés à des échanges Ethernet.
  * [Cologix](http://www.cologix.com/)
  * [CoreSite](http://www.coresite.com/)
  * [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](http://www.nextdc.com/)
  * [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
* Demandez à votre fournisseur de connectivité d’étendre votre réseau à l’emplacement d’homologation de votre choix.
  * Vérifiez que votre fournisseur de connectivité étend votre connectivité avec une haute disponibilité pour éviter tout point de défaillance unique.
* Commandez un circuit ExpressRoute avec échange en tant que fournisseur de connectivité pour se connecter à Microsoft.
  * Pour définir la connectivité, procédez de la manière décrite dans [Création d’un circuit ExpressRoute](expressroute-howto-circuit-classic.md) .

| **Fournisseur de connectivité** | **Microsoft Exchange** | **Emplacements** |
| --- | --- | --- |
| **[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Singapour |
| **[Arteria Networks Corporation](https://arteria-net.com/business/service/cloud_access/sca/)** |Equinix |Tokyo |
| **[Alaska Communications](http://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amsterdam |
| **[Exponential E](http://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | Londres |
| **[HSO](http://www.hso.co.uk/products/cloud-direct)** |Equinix | Londres, Slough |
| **[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure)** |Equinix |New York, Washington DC |
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington DC |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Telecity | Amsterdam, Frankfort |  
| **[Transtelco](http://www.transtelco.net/tcloud/microsoft)** |Equinix | Dallas, Los Angeles |  
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Francfort |  
| **[Windstream](http://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix |Chicago, Silicon Valley, Washington DC |
| **[XO Communications](http://www.xo.com/)** |Equinix |Silicon Valley |
| **[Zertia](http://www.zertia.es/index.php/novedades)**| Niveau 3 | Madrid |


## <a name="expressroute-system-integrators"></a>Intégrateurs système ExpressRoute
L’activation de la connectivité privée pour l’adapter à vos besoins peut s’avérer difficile selon l’échelle de votre réseau. Vous pouvez faire appel à l’un des intégrateurs système figurant dans le tableau ci-dessous pour vous aider à intégrer ExpressRoute.

| **Intégrateur système** | **Continent** |
| --- | --- |
| **[Avanade Inc.](http://www.avanade.com/)** |Asie, Europe, États-Unis |
| **[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)** |Europe |
| **[Equinix Professional Services](http://www.equinix.com/services/consulting/)** |FR |
| **[IT Consultancy](http://itconsult.com.au/microsoft-expressroute)** | Australie |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Europe (Allemagne) |
| **[Nelite](http://nelite.com/)** |Europe |
| **[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** |Asie |
| **[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** |FR |
| **[Project Leadership](http://www.projectleadership.net/azure)** |FR |


## <a name="next-steps"></a>Étapes suivantes
* Pour plus d'informations sur ExpressRoute, consultez le [FAQ sur ExpressRoute](expressroute-faqs.md).
* Assurez-vous que toutes les conditions préalables sont remplies. Consultez la page [Configuration requise pour ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Carte géographique"



<!--HONumber=Jan17_HO2-->



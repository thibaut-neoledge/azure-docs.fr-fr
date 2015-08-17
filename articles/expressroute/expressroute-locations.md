<properties
   pageTitle="Emplacements ExpressRoute"
   description="Cette page fournit une vue d’ensemble détaillée des emplacements où les services sont proposés et de la façon de se connecter à des régions Azure."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/04/2015"
   ms.author="cherylmc" />

# Partenaires ExpressRoute et emplacements d’homologation
Les tableaux sur cette page offrent des informations sur les fournisseurs de connectivité ExpressRoute (fournisseurs Exchange et fournisseurs de services réseau), la couverture géographique ExpressRoute, les services cloud Microsoft pris en charge via ExpressRoute et les intégrateurs système ExpressRoute.

## Fournisseurs de connectivité ExpressRoute
ExpressRoute est pris en charge dans tous les emplacements et régions Azure. La carte ci-dessous fournit une liste des régions Azure et des emplacements ExpressRoute. Les emplacements ExpressRoute se réfèrent à ceux où Microsoft s’associe à plusieurs fournisseurs de services.
 
![](./media/expressroute-locations/expressroute-locations-map.png)

Vous aurez accès aux services Azure dans toutes les régions au sein d’une une région géopolitique si vous êtes connecté à au moins un emplacement ExpressRoute dans la région géopolitique. Le tableau ci-dessous fournit une carte des régions Azure vers des emplacements ExpressRoute au sein d’une région géopolitique.

|**Région géopolitique**|**Régions Azure**|**Emplacements ExpressRoute**|
|---|---|---|
|**États-Unis**|Toutes les régions des États-Unis : Est des États-Unis, Ouest des États-Unis, Est des États-Unis 2, Centre des États-Unis, Centre-Sud des États-Unis, Centre-Nord des États-Unis|Atlanta, Chicago, Dallas, Los Angeles, New York, Seattle, Silicon Valley, Washington DC|
|**Gouvernement des États-Unis**|Iowa, Virginie|Ashburn, Chicago|
|**Amérique du Sud**|Sud du Brésil|São Paulo|
|**Europe**|Europe du Nord, Europe de l’Ouest|Amsterdam, Londres|
|**Asie**|Asie orientale, Asie du Sud-Est|Hong Kong, Singapour|
|**Japon**|Ouest du Japon, Est du Japon|Tokyo|
|**Australie**|Sud-est de l’Australie |Est de l’Australie|Melbourne, Sydney|
|**Inde**|Inde-Ouest, Inde-Centre, Inde-Sud|Chennai, Mumbai|

La connectivité entre les régions géopolitiques n’est pas prise en charge. Vous pouvez travailler avec votre fournisseur de connectivité pour étendre la connectivité entre les régions géopolitiques à l’aide de leur réseau.


## Emplacements du fournisseur Exchange

| **Fournisseur de services** |**Microsoft Azure** | **Office 365** | **Emplacements** |
|-----------------------|--------------------|----------------|---------------|
| **[Réseaux Aryaka](http://www.aryaka.com/)** | Pris en charge | Non pris en charge | Silicon Valley, Singapour, Washington DC |
| **[Colt Ethernet](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | Pris en charge | Non pris en charge | Amsterdam, Londres |
| **Comcast** | Pris en charge | Non pris en charge | Silicon Valley, Washington DC |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Pris en charge | Bientôt disponible | Amsterdam, Atlanta, Chicago, Dallas, Hong Kong, Londres, Los Angeles, Melbourne, New York, Sao Paulo, Seattle, Silicon Valley, Singapour, Sydney, Tokyo, Washington DC |
| **[InterCloud](https://www.intercloud.com/)** | Pris en charge | Non pris en charge | Amsterdam, Londres, Singapour, Washington DC |
| **Internet Solutions - Cloud Connect** | Pris en charge | Non pris en charge | Amsterdam, Londres |
| **Interxion** | Pris en charge | Non pris en charge | Amsterdam |
| **[Communications de niveau 3 - Exchange](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Pris en charge | Non pris en charge | Chicago, Dallas, Londres, Seattle, Silicon Valley, Washington DC |
| **NEXTDC** | Pris en charge | Non pris en charge | Melbourne, Sydney+ |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** | Pris en charge | Bientôt disponible | Amsterdam, Londres |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | Pris en charge | Non pris en charge | Melbourne+, Sydney |
| **[Zayo Group](http://www.zayo.com/)** | Pris en charge | Non pris en charge | Washington DC |

 **+** = bientôt disponible

Consultez la rubrique [Configurer votre connexion au fournisseur Exchange](expressroute-configuring-exps.md) pour savoir comment configurer votre connexion.

## Emplacements des fournisseurs de services réseau


| **Fournisseur de services** |**Microsoft Azure** | **Office 365** | **Emplacements** |
|-----------------------|--------------------|----------------|---------------|
| **[AT&T](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Pris en charge | Bientôt disponible | Amsterdam+, Londres+, Dallas, Silicon Valley, Washington DC |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | Pris en charge | Bientôt disponible | Amsterdam, Londres, Dallas, Silicon Valley+, Washington DC |
|**China Telecom Global** | Bientôt disponible | Non pris en charge | Hong Kong+ |
| **[Colt IPVPN](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | Pris en charge | Non pris en charge | Amsterdam, Londres |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2013/pdf/Azure_E.pdf)** | Pris en charge | Non pris en charge | Tokyo |
| **[Communications de niveau 3 - IPVPN](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Pris en charge | Non pris en charge | Chicago, Dallas, Londres, Seattle, Silicon Valley, Washington DC |
| **NTT Communications** | Bientôt disponible | Non pris en charge | Tokyo+ | 
| **[Orange](http://www.orange-business.com/)** | Pris en charge | Non pris en charge | Amsterdam, Londres, Silicon Valley, Washington DC |
| **PCCW Global Limited** | Pris en charge | Non pris en charge | Hong Kong |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** | Pris en charge | Non pris en charge | Singapour |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | Pris en charge | Bientôt disponible | Amsterdam, Chennai+, Hong Kong, Londres, Mumbai+, Singapour |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | Pris en charge | Non pris en charge | Melbourne+, Sydney |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | Pris en charge | Non pris en charge | Londres, Hong Kong, Silicon Valley, Washington DC, Iowa gouvernement des États-Unis, Virginie gouvernement des États-Unis |

 **+** = bientôt disponible

Consultez la rubrique [Configurer votre connexion au fournisseur de services réseau](expressroute-configuring-nsps.md) pour savoir comment configurer votre connexion.

## Connectivité via des fournisseurs de services non répertoriés 

Si votre fournisseur de connectivité ne se trouve pas dans la liste des sections précédentes, vous pouvez quand même créer une connexion.

- Vérifiez auprès de votre fournisseur de connectivité s’il est connecté à l’un des fournisseurs Exchange dans les emplacements correspondants répertoriés. Vous pouvez consulter les liens ci-dessous pour recueillir plus d’informations sur les services proposés par les fournisseurs Exchange. Plusieurs fournisseurs de connectivité sont déjà connectés à des échanges Ethernet de fournisseurs Exchange.
	- [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/) 
	- [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
- Demandez à votre fournisseur de connectivité d’étendre votre réseau à l’emplacement Exchange de prédilection.
	- Vérifiez que votre fournisseur de connectivité étend votre connectivité avec une haute disponibilité pour éviter tout point de défaillance unique.
	- Les fournisseurs de connectivité (en particulier les fournisseurs Ethernet) peuvent vous obliger à acquérir une paire de circuits pour les échanges Ethernet afin de garantir une haute disponibilité. 
- Commandez un circuit ExpressRoute via le fournisseur Exchange pour vous connecter à Azure.
	- Suivez les étapes de la rubrique [Configurer votre connexion au fournisseur Exchange](expressroute-configuring-exps.md) pour configurer la connectivité.

|**Fournisseur de connectivité**|**Fournisseurs Exchange**|**Emplacements d’homologation**|
|---|---|---|
|**[XO Communications](http://www.xo.com/)**|Equinix|Silicon Valley|

## Intégrateurs système ExpressRoute
L’activation de la connectivité privée pour l’adapter à vos besoins peut s’avérer difficile selon l’échelle de votre réseau. Vous pouvez faire appel à l’un des intégrateurs système figurant dans le tableau ci-dessous pour vous aider à utiliser ExpressRoute.


|**Intégrateur système**|**Continent**|
|---|---|
|**[Nimbo](http://www.nimbo.com/)**|FR||
|**[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)**|Europe/Moyen-Orient/Afrique|

## Étapes suivantes
- Vérifiez que vous répondez aux [conditions préalables ExpressRoute](expressroute-prerequisites.md).
- Visitez le [FAQ](expressroute-faqs.md) pour plus d’informations.
- Consultez la rubrique [Configurer votre connexion au fournisseur Exchange](expressroute-configuring-exps.md) ou [Configurer votre connexion au fournisseur de services réseau](expressroute-configuring-nsps.md) pour configurer une connexion ExpressRoute.
- Si vous souhaitez configurer une connexion VPN de site à site et ExpressRoute pour le même réseau virtuel, consultez la rubrique [Configurer des connexions ExpressRoute et VPN de site à site coexistantes](expressroute-coexist.md).
 

<!---HONumber=August15_HO6-->
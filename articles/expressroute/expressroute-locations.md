<properties
   pageTitle="Emplacements ExpressRoute"
   description="Cette page fournit une vue d’ensemble détaillée des emplacements où les services sont proposés et de la façon de se connecter à des régions Azure."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" /> <tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/29/2015"
   ms.author="cherylmc" />

# Partenaires ExpressRoute et emplacements d’homologation
Ce tableau fournit des détails sur :

1. Fournisseurs de connectivité ExpressRoute (fournisseurs Exchange et fournisseurs de services réseau)
2. Couverture géographique ExpressRoute
3. Services cloud Microsoft pris en charge via ExpressRoute
4. Intégrateurs système ExpressRoute

## Fournisseurs de connectivité ExpressRoute
ExpressRoute est pris en charge dans tous les emplacements et régions Azure. La carte ci-dessous fournit une liste des régions Azure et des emplacements ExpressRoute. Les emplacements ExpressRoute se réfèrent à ceux où Microsoft s’associe à plusieurs fournisseurs de services.
 
![](./media/expressroute-locations/expressroute-locations-map.png)

Vous aurez accès aux services Azure dans toutes les régions au sein d’une une région géopolitique si vous êtes connecté à au moins un emplacement ExpressRoute dans la région géopolitique. Le tableau ci-dessous fournit une carte des régions Azure vers des emplacements ExpressRoute au sein d’une région géopolitique.

|**Région géopolitique**|**Régions Azure**|**Emplacements ExpressRoute**|
|---|---|---|
|**États-Unis**|Toutes les régions des États-Unis : Est des États-Unis, Ouest des États-Unis, Est des États-Unis 2, Centre des États-Unis, Centre-Sud des États-Unis, Centre-Nord des États-Unis|Atlanta, Chicago, Dallas, Los Angeles, New York, Seattle, Silicon Valley, Washington DC|
|**Amérique du Sud**|Sud du Brésil|São Paulo|
|**Europe**|Europe du Nord, Europe de l’Ouest|Amsterdam, Londres|
|**Asie**|Asie orientale, Asie du Sud-Est|Hong Kong, Singapour|
|**Japon**|Ouest du Japon, Est du Japon|Tokyo|
|**Australie**|Sud-est de l’Australie |Est de l’Australie|Sydney|

La connectivité entre les régions géopolitiques n’est pas prise en charge. Vous pouvez travailler avec votre fournisseur de connectivité pour étendre la connectivité entre les régions géopolitiques à l’aide de leur réseau.


### Emplacements du fournisseur Exchange
- Consultez ce [tableau](https://msdn.microsoft.com/library/azure/4da69a0f-8f52-49ea-a990-dacd4202150a#BKMK_EXP) pour obtenir la liste des fournisseurs Exchange et des emplacements où ils sont pris en charge.
-  Consultez la rubrique [Configurer votre connexion au fournisseur Exchange](expressroute-configuring-exps.md) pour savoir comment configurer votre connexion.

### Emplacements des fournisseurs de services réseau
- Consultez ce [tableau](https://msdn.microsoft.com/library/azure/4da69a0f-8f52-49ea-a990-dacd4202150a#BKMK_NSP) pour obtenir la liste des fournisseurs de services réseau et les emplacements où ils sont pris en charge.
- Consultez la rubrique [Configurer votre connexion au fournisseur de services réseau](expressroute-configuring-nsps.md) pour savoir comment configurer votre connexion.

### Connectivité via des fournisseurs de services non répertoriés ci-dessus

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


## ExpressRoute et services cloud Microsoft
Les tableaux ci-dessous fournissent des informations sur les fournisseurs de connectivité et la liste des services cloud Microsoft qu’ils prennent en charge. Contactez votre fournisseur de services

**Fournisseurs Exchange**

|**Fournisseur de services**|**Services Microsoft Azure**|**Services Office 365**|
|---|---|---|
|**Aryaka**|Pris en charge||
|**Colt Ethernet**|Pris en charge||
|**Equinix**|Pris en charge|Bientôt disponible|
|**InterCloud**|Pris en charge||
|**Level 3 EVPL Service**|Pris en charge||
|**TeleCity Group**|Pris en charge||
|**Zayo Group**|Pris en charge||

**Fournisseurs de services réseau (NSP)**

|**Fournisseur de services**|**Services Microsoft Azure**|**Services Office 365**|
|---|---|---|
|**AT&T**|Pris en charge|Bientôt disponible|
|**British Telecom**|Pris en charge|Bientôt disponible|
|**Colt IPVPN**|Pris en charge||
|**Internet Initiative Japan Inc. - IIJ**|Pris en charge||
|**Level3 IPVPN**|Pris en charge||
|**Orange**|Pris en charge|| 
|**SingTel**|Pris en charge||
|**Tata Communications**|Pris en charge||
|**Telstra Corporation**|Pris en charge||
|**Verizon**|Pris en charge|| 


## Intégrateurs système ExpressRoute
L’activation de la connectivité privée pour l’adapter à vos besoins peut s’avérer difficile selon l’échelle de votre réseau. Vous pouvez faire appel à l’un des intégrateurs système figurant dans le tableau ci-dessous pour vous aider à utiliser ExpressRoute.


|**Intégrateur système**|**Continent**|
|---|---|
|**[Nimbo](http://www.nimbo.com/)**|FR||
|**[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)**|Europe/Moyen-Orient/Afrique|

## Étapes suivantes
- Vérifiez que vous répondez aux [conditions préalables ExpressRoute](expressroute-prerequisites.md).
- Visitez le [FAQ](expressroute-faqs.md) pour plus d’informations.
- Sélectionnez votre fournisseur et configurez votre connexion. Voir [Configurer votre connexion au fournisseur Exchange](expressroute-configuring-exps.md) ou [Configurer votre connexion au fournisseur de services réseau](expressroute-configuring-nsps.md) pour obtenir des informations de configuration.


<!---HONumber=54--> 
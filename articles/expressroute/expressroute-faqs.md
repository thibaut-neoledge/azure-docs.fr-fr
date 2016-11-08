---
title: Forum Aux Questions ExpressRoute
description: Le Forum aux questions ExpressRoute contient des informations sur les services Azure pris en charge, le coût, les données et connexions, le contrat de niveau de service, les fournisseurs et les emplacements, la bande passante et les détails techniques.
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: ''

ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: cherylmc

---
# <a name="expressroute-faq"></a>Forum Aux Questions ExpressRoute
## <a name="what-is-expressroute?"></a>Présentation d’ExpressRoute
ExpressRoute est un service Azure qui vous permet de créer des connexions privées entre les centres de données Microsoft et une infrastructure locale ou une installation de colocalisation. Les connexions ExpressRoute ne sont pas établies via le réseau public Internet et offrent plus de sécurité, de fiabilité et de rapidité, ainsi que moins de latences que les connexions classiques sur Internet.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections?"></a>Quels avantages présentent l’utilisation d’ExpressRoute et les connexions de réseau privé ?
Les connexions ExpressRoute ne sont pas établies via le réseau public Internet et offrent plus de sécurité, de fiabilité et de rapidité avec des latences inférieures et cohérentes par rapport aux connexions classiques sur Internet. Dans certains cas, l’utilisation de connexions ExpressRoute pour transférer des données entre les appareils locaux et Azure peut générer des économies significatives.

### <a name="what-microsoft-cloud-services-are-supported-over-expressroute?"></a>Quels services cloud Microsoft sont pris en charge via ExpressRoute ?
ExpressRoute prend maintenant en charge la plupart des services Microsoft Azure actuels, notamment Office 365.  Des informations sur la mise à la disposition générale seront bientôt disponibles.

### <a name="where-is-the-service-available?"></a>Où le service est-il disponible ?
Consultez cette page pour connaître l’emplacement du service et la disponibilité : [Partenaires et emplacements ExpressRoute](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-don’t-have-partnerships-with-one-of-the-expressroute-carrier-partners?"></a>Comment puis-je utiliser ExpressRoute pour me connecter à Microsoft si je n’ai pas conclu de partenariat avec l’un des partenaires opérateurs d’ExpressRoute ?
Vous pouvez sélectionner un opérateur régional et accéder à des connexions Ethernet établies avec l’un des emplacements de fournisseur Exchange pris en charge. Vous pouvez ensuite vous apparier avec Microsoft à l’emplacement du fournisseur. Vérifiez la dernière section de la rubrique [Partenaires et emplacements ExpressRoute](expressroute-locations.md) pour voir si votre fournisseur de services est présent dans l’un des emplacements Exchange. Vous pouvez ensuite commander un circuit ExpressRoute via le fournisseur de services pour vous connecter à Azure.

### <a name="how-much-does-expressroute-cost?"></a>Combien coûte ExpressRoute ?
Pour plus d'informations sur la tarification, consultez la page [Tarification](https://azure.microsoft.com/pricing/details/expressroute/) .

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth,-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed?"></a>Si j’achète un circuit ExpressRoute d’une bande passante donnée, la connexion VPN que j’achète auprès de mon fournisseur de services réseau doit-elle être de la même vitesse ?
Non. Vous pouvez acheter une connexion VPN de n’importe quelle vitesse chez votre fournisseur de services. Toutefois, votre connexion à Azure est limitée à la bande passante du circuit ExpressRoute que vous achetez.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth,-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-required?"></a>Si j’achète un circuit ExpressRoute d’une bande passante donnée, puis-je passer à des vitesses supérieures si nécessaire ?
Oui. Les circuits ExpressRoute sont configurés pour prendre en charge les cas où vous pouvez aller jusqu’à doubler la limite de la bande passante obtenue sans frais supplémentaire. Vérifiez auprès de votre fournisseur de services si cette fonctionnalité est prise en charge.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously?"></a>Puis-je utiliser la même connexion de réseau privé avec un réseau virtuel et d’autres services Azure simultanément ?
Oui. Un circuit ExpressRoute, une fois installé, vous permettra d’accéder simultanément à des services au sein d’un réseau virtuel et à d’autres services Azure. Vous allez vous connecter à des réseaux virtuels sur le chemin d’accès d’homologation privée et à d’autres services sur le chemin d’accès d’homologation publique.

### <a name="does-expressroute-offer-a-service-level-agreement-(sla)?"></a>ExpressRoute offre-t-il un contrat de niveau de service (SLA) ?
Pour plus d’informations, consultez la [page sur les contrats de niveau de service ExpressRoute](https://azure.microsoft.com/support/legal/sla/) .

## <a name="supported-services"></a>Services pris en charge
La plupart des services Azure sont pris en charge via ExpressRoute.

* La connectivité aux machines virtuelles et services cloud déployés dans des réseaux virtuels est prise en charge sur le chemin d’accès d’homologation privée.
* Sites Web Azure est pris en charge sur le chemin d’accès d’homologation publique.
* IoT Hub est pris en charge sur le chemin d’accès d’homologation publique.
* Office 365 est pris en charge sur le chemin d'accès homologation Microsoft.
* Tous les autres services sont accessibles via le chemin d’accès d’homologation publique. Les exceptions sont les suivantes.
  
    **Les services suivants ne sont pas pris en charge :**
  
  * CDN
  * Test de charge Visual Studio Team Services
  * Multi-Factor Authentication
  * Traffic Manager

## <a name="data-and-connections"></a>Données et connexions
### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute?"></a>Existe-t-il des limites sur la quantité de données que je peux transférer avec ExpressRoute ?
Nous ne définissons aucune limite sur la quantité de transfert de données. Pour plus d'informations sur les tarifs de bande passante, consultez la page de [tarification](https://azure.microsoft.com/pricing/details/expressroute/) .

### <a name="what-connection-speeds-are-supported-by-expressroute?"></a>Quelles vitesses de connexion sont prises en charge par ExpressRoute ?
Offres relatives à la bande passante prise en charge :

|50 Mbits/s, 100 Mbits/s, 200 Mbits/s, 500 Mbits/s, 1 Gbit/s, 2 Gbit/s, 5 Gbits/s, 10 Gbits/s|

### <a name="which-service-providers-are-available?"></a>Quels fournisseurs de services sont disponibles ?
Pour obtenir la liste des fournisseurs de services et des emplacements, consultez la page [Partenaires et emplacements ExpressRoute](expressroute-locations.md) .

## <a name="technical-details"></a>Détails techniques
### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure?"></a>Quelles sont les exigences techniques pour connecter mon emplacement local à Azure ?
Pour obtenir la configuration requise, consultez [la page des conditions préalables ExpressRoute](expressroute-prerequisites.md) .

### <a name="are-connections-to-expressroute-redundant?"></a>Les connexions à ExpressRoute sont-elles redondantes ?
Oui. Chaque circuit ExpressRoute dispose d’une paire redondante de connexions croisées configurées pour fournir une haute disponibilité.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail?"></a>Vais-je perdre ma connectivité en cas d’échec de l’un de mes liens ExpressRoute ?
Vous ne perdez pas votre connectivité si une des connexions croisées échoue. Une connexion redondante est disponible pour prendre en charge la charge de votre réseau. Vous pouvez également créer plusieurs circuits dans un autre emplacement d’homologation pour bénéficier de la tolérance de panne.

### <a name="<a-name="onep2plink"></a>if-i'm-not-co-located-at-a-cloud-exchange-and-my-service-provider-offers-point-to-point-connection,-do-i-need-to-order-two-physical-connections-between-my-on-premises-network-and-microsoft?"></a><a name="onep2plink"></a>Si je ne suis pas colocalisé au niveau d’un échange de cloud et que mon fournisseur de services offre une connexion point à point, dois-je commander deux connexions physiques entre mon réseau local et Microsoft ?
Non, vous n’avez besoin que d’une connexion physique si votre fournisseur de services peut établir deux circuits virtuels Ethernet sur la connexion physique. La connexion physique (par exemple, une fibre optique) s’achève sur un appareil de couche 1 (L1) (voir l'image ci-dessous). Les deux circuits virtuels Ethernet sont marqués avec des ID de VLAN différents, l’un pour le circuit principal et l’autre pour le circuit secondaire. Ces ID de VLAN se trouvent dans l’en-tête Ethernet 802.1Q externe. L’en-tête Ethernet 802.1Q interne (non illustré) est mappé à un [domaine de routage ExpressRoute](expressroute-circuit-peerings.md)spécifique. 

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute?"></a>Puis-je étendre l’un de mes réseaux locaux virtuels vers Azure avec ExpressRoute ?
Non. Nous ne prenons pas en charge les extensions de connectivité de couche 2 dans Azure.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription?"></a>Puis-je avoir plusieurs circuits ExpressRoute dans mon abonnement ?
Oui. Vous pouvez avoir plusieurs circuits ExpressRoute dans votre abonnement. La limite par défaut du nombre de circuits dédiés est définie à 10. Vous pouvez contacter le support technique de Microsoft pour augmenter la limite, si nécessaire.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers?"></a>Puis-je avoir des circuits ExpressRoute de différents fournisseurs de services ?
Oui. Vous pouvez avoir des circuits ExpressRoute de nombreux fournisseurs de services. Chaque circuit ExpressRoute est associé à un fournisseur de services uniquement.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Comment connecter mes réseaux virtuels à un circuit ExpressRoute ?
Les étapes de base sont décrites ci-dessous.

* Vous devez établir un circuit ExpressRoute, que le fournisseur de services activera.
* Vous ou votre fournisseur devez configurer la ou les homologations BGP.
* Vous devez lier le réseau virtuel au circuit ExpressRoute.

Consultez [Workflows ExpressRoute d’approvisionnement du circuit et états du circuit](expressroute-workflows.md) pour plus d’informations.

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit?"></a>Existe-t-il des limites de connectivité pour le circuit ExpressRoute ?
Oui. [Partenaires et emplacements ExpressRoute](expressroute-locations.md) offre une vue d’ensemble des limites de connectivité d’un circuit ExpressRoute. La connectivité d’un circuit ExpressRoute est limitée à une seule région géopolitique. La connectivité peut être étendue pour traverser des régions géopolitiques en activant la fonctionnalité Premium d’ExpressRoute.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit?"></a>Puis-je lier plusieurs réseaux virtuels à un circuit ExpressRoute ?
Oui. Vous pouvez lier jusqu’à 10 réseaux virtuels à un circuit ExpressRoute.

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks.-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit?"></a>Je possède plusieurs abonnements Azure qui contiennent des réseaux virtuels. Puis-je connecter des réseaux virtuels qui figurent dans des abonnements distincts à un circuit ExpressRoute ?
Oui. Vous pouvez autoriser jusqu’à 10 autres abonnements Azure à utiliser un même circuit ExpressRoute. Cette limite peut être augmentée en activant la fonctionnalité Premium d’ExpressRoute.

Pour plus d'informations, consultez la page [Partage d'un circuit ExpressRoute entre plusieurs abonnements](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other?"></a>Les réseaux virtuels sont-ils connectés à un même circuit en étant isolés les uns des autres ?
Non. Tous les réseaux virtuels liés à un même circuit ExpressRoute font partie du même domaine de routage et ne sont pas isolés les uns des autres, du point de vue du routage. Si vous devez isoler des itinéraires, vous devez créer un circuit ExpressRoute distinct.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit?"></a>Puis-je avoir un seul réseau virtuel connecté à plusieurs circuits ExpressRoute ?
Oui. Vous pouvez lier un seul réseau virtuel à 4 circuits ExpressRoute au plus. Ils doivent être transmis par le biais de quatre [emplacements ExpressRoute](expressroute-locations.md)différents.

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits?"></a>Puis-je accéder à Internet à partir de mes réseaux virtuels qui sont connectés à des circuits ExpressRoute ?
Oui. Si vous n’avez pas publié les itinéraires par défaut (0.0.0.0/0) ou les préfixes des itinéraires Internet via la session BGP, vous pouvez vous connecter à Internet à partir d’un réseau virtuel lié à un circuit ExpressRoute.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits?"></a>Puis-je bloquer la connectivité Internet à des réseaux virtuels qui sont connectés à des circuits ExpressRoute ?
Oui. Vous pouvez publier des itinéraires par défaut (0.0.0.0/0) pour bloquer la connectivité Internet de toutes les machines virtuelles qui sont déployées au sein d’un réseau virtuel et qui acheminent tout le trafic sortant via le circuit ExpressRoute. Notez que si vous publiez des itinéraires par défaut, nous forçons le trafic des services offerts sur l’homologation publique (comme le stockage Azure et la base de données SQL) en local. Vous devez configurer vos routeurs pour renvoyer le trafic vers Azure via le chemin d’accès d’homologation publique ou via Internet.

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other?"></a>Les réseaux virtuels liés à un même circuit ExpressRoute peuvent-ils communiquer entre eux ?
Oui. Les machines virtuelles qui sont déployées dans des réseaux virtuels connectés à un même circuit ExpressRoute peuvent communiquer entre elles.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute?"></a>Puis-je utiliser une connectivité de site à site pour les réseaux virtuels conjointement avec ExpressRoute ?
Oui. ExpressRoute peut coexister avec des réseaux VPN de site à site.

### <a name="can-i-move-a-virtual-network-from-site-to-site-/-point-to-site-configuration-to-use-expressroute?"></a>Puis-je déplacer un réseau virtuel à partir de la configuration de site à site/point à site pour utiliser ExpressRoute ?
Oui. Vous devez créer une passerelle ExpressRoute dans votre réseau virtuel. Le processus entraîne un léger temps d’arrêt.

### <a name="what-do-i-need-to-connect-to-azure-storage-over-expressroute?"></a>De quoi ai-je besoin pour me connecter au stockage Azure via ExpressRoute ?
Vous devez établir un circuit ExpressRoute et configurer des itinéraires pour l’homologation publique.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise?"></a>Existe-t-il des limites sur le nombre d’itinéraires que je peux publier ?
Oui. Nous acceptons jusqu’à 4000 préfixes d’itinéraires pour l’homologation privée et 200 pour l’homologation publique et l’homologation Microsoft. Vous pouvez augmenter ce nombre jusqu’à 10 000 itinéraires par homologation privée si vous activez la fonctionnalité Premium d’ExpressRoute.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session?"></a>Existe-t-il des restrictions de plages d’adresses IP que je peux publier sur la session BGP ?
Nous n’acceptons pas les préfixes privés (RFC1918) dans la session BGP d’homologation publique et Microsoft.

### <a name="what-happens-if-i-exceed-the-bgp-limits?"></a>Que se passe-t-il si je dépasse les limites du protocole BGP ?
Les sessions BGP sont supprimées. Elles sont ensuite réinitialisées lorsque le nombre de préfixes tombe en dessous de la limite.

### <a name="what-is-the-expressroute-bgp-hold-time?-can-it-be-adjusted?"></a>Quelle est la durée de conservation BGP d'ExpressRoute ? Peut-elle être ajustée ?
La durée de conservation est de 180. Les messages persistants sont envoyés toutes les 60 secondes. Il s'agit de paramètres fixes de Microsoft qui ne peuvent pas être modifiés.

### <a name="after-i-advertise-the-default-route-(0.0.0.0/0)-to-my-virtual-networks,-i-can't-activate-windows-running-on-my-azure-vms.-how-to-i-fix-this?"></a>Une fois que je publie l’itinéraire par défaut (0.0.0.0/0) sur mes réseaux virtuels, je ne peux pas activer Windows qui est en cours d’exécution sur mes machines virtuelles Azure. Comment faire pour résoudre ce problème ?
Les étapes suivantes aideront Azure à reconnaître la demande d’activation :

1. Établissez l’homologation publique pour votre circuit ExpressRoute.
2. Effectuez une recherche DNS et recherchez l’adresse IP de **kms.core.windows.net**
3. Effectuez ensuite l’une des deux actions suivantes afin que le service de gestion de clés reconnaisse que la demande d’activation provient d’Azure et respecte la demande.
   * Sur votre réseau local, réacheminez le trafic destiné à l’adresse IP (obtenue à l’étape 2) vers Azure via l’homologation publique.
   * Faites en sorte que votre fournisseur de services réseau renvoie le trafic vers Azure via l’homologation publique.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit?"></a>Puis-je modifier la bande passante d’un circuit ExpressRoute ?
Oui. Vous pouvez augmenter la bande passante d’un circuit ExpressRoute sans avoir à le détruire. Vous devez effectuer un suivi avec votre fournisseur de connectivité pour vous assurer qu’il met à jour les limitations dans ses réseaux pour prendre en charge l’augmentation de la bande passante. Vous n’êtes toutefois pas en mesure de réduire la bande passante d’un circuit ExpressRoute. La réduction de la bande passante signifie la destruction et la recréation d’un circuit ExpressRoute.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit?"></a>Comment modifier la bande passante d’un circuit ExpressRoute ?
Vous pouvez mettre à jour la bande passante du circuit ExpressRoute à l’aide de l’API du circuit dédié mise à jour et de l’applet de commande PowerShell.

## <a name="expressroute-premium"></a>ExpressRoute Premium
### <a name="what-is-expressroute-premium?"></a>En quoi consiste ExpressRoute Premium ?
ExpressRoute Premium est un ensemble de fonctionnalités répertoriées ci-dessous.

* Augmentation de la limite de la table d’itinéraires de 4 000 à 10 000 itinéraires pour l’homologation privée.
* Augmentation du nombre de réseaux virtuels qui peuvent être connectés à un circuit ExpressRoute (la valeur par défaut est 10). Pour plus d’informations, consultez le tableau ci-dessous.
* Connectivité globale sur le réseau principal Microsoft. Vous pouvez désormais lier un réseau virtuel dans une région géopolitique à un circuit ExpressRoute d’une autre région. **Exemple :** vous pouvez lier un réseau virtuel créé en Europe de l’Ouest à un circuit ExpressRoute créé dans la Silicon Valley.
* Connectivité aux services Office 365 et CRM Online.

### <a name="how-many-vnets-can-i-link-to-an-expressroute-circuit-if-i-enabled-expressroute-premium?"></a>Combien de réseaux virtuels est-il possible de lier à un circuit ExpressRoute en cas d’abonnement à ExpressRoute Premium ?
Les tableaux suivants indiquent les limites ExpressRoute et le nombre de réseaux virtuels par circuit ExpressRoute.

[!INCLUDE [expressroute-limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium?"></a>Comment activer ExpressRoute Premium ?
Il est possible d’activer les fonctionnalités ExpressRoute Premium lorsque la fonctionnalité est activée et de les arrêter en mettant à jour l’état du circuit. Vous pouvez activer ExpressRoute Premium quand vous créez le circuit ou vous pouvez appeler l’API mise à jour du circuit dédié/l’applet de commande PowerShell pour activer ExpressRoute Premium.

### <a name="how-do-i-disable-expressroute-premium?"></a>Comment désactiver ExpressRoute Premium ?
Vous pouvez désactiver ExpressRoute Premium en appelant l’API mise à jour du circuit dédié/l’applet de commande PowerShell. Vous devez vous assurer que vous avez redimensionné votre connectivité afin de respecter les limites par défaut avant de désactiver ExpressRoute Premium. Nous ne pouvons pas exécuter la demande de désactivation d’ExpressRoute Premium si votre utilisation dépasse les limites par défaut.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set?"></a>Puis-je choisir les fonctionnalités parmi l’ensemble des fonctionnalités Premium ?
Non. Vous ne pouvez pas sélectionner les fonctionnalités dont vous avez besoin. Nous activons toutes les fonctionnalités lorsque vous activez ExpressRoute Premium.

### <a name="how-much-does-expressroute-premium-cost?"></a>Combien coûte ExpressRoute Premium ?
Consultez la page de [tarification](https://azure.microsoft.com/pricing/details/expressroute/) pour connaître les coûts.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges?"></a>Dois-je payer pour ExpressRoute Premium en plus des frais ExpressRoute standard ?
Oui. Les frais d’ExpressRoute Premium s’ajoutent aux frais de circuit ExpressRoute et aux frais du fournisseur de connectivité.

## <a name="expressroute-and-office-365-services-and-crm-online"></a>ExpressRoute, services Office 365 et CRM Online
[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services-and-crm-online?"></a>Comment créer un circuit ExpressRoute pour se connecter à des services Office 365 et CRM Online ?
1. Consultez la [page des conditions préalables d’ExpressRoute](expressroute-prerequisites.md) pour vérifier que vous avez respecté les conditions.
2. Passez en revue la liste des fournisseurs de services et les emplacements dans la rubrique [Partenaires et emplacements ExpressRoute](expressroute-locations.md) pour vous assurer que vos besoins de connectivité sont remplis.
3. Planifiez vos besoins en capacité en consultant la page [Planification réseau et optimisation des performances pour Office 365](http://aka.ms/tune/).
4. Suivez les étapes répertoriées dans les flux de travail ci-dessous pour configurer la connectivité [Flux de travail ExpressRoute d’approvisionnement du circuit et états du circuit](expressroute-workflows.md).

> [!IMPORTANT]
> Assurez-vous d’avoir activé le module complémentaire ExpressRoute lors de la configuration de la connectivité aux services Office 365 et CRM Online.
> 
> 

### <a name="do-i-need-to-enable-azure-public-peering-to-connect-to-office-365-services-and-crm-online?"></a>Dois-je activer l’homologation publique Azure pour me connecter aux services Office 365 et CRM Online ?
Non, vous devez uniquement activer l’homologation Microsoft. Le trafic d'authentification vers Azure AD sera envoyé par l’homologation Microsoft. 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services-and-crm-online?"></a>Mes circuits ExpressRoute existants peuvent-ils prendre en charge la connectivité aux services Office 365 et CRM Online ?
Oui. Votre circuit ExpressRoute existant peut être configuré pour prendre en charge la connectivité aux services Office 365. Assurez-vous d'avoir une capacité suffisante pour vous connecter aux services Office 365 et d’avoir activé le module complémentaire premium. [Planification réseau et optimisation des performances pour Office 365](http://aka.ms/tune/) vous aidera à prévoir vos besoins de connectivité. Voir également [Création et modification d’un circuit ExpressRoute](expressroute-howto-circuit-classic.md).

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection?"></a>Quels services Office 365 sont accessibles via une connexion ExpressRoute ?
Reportez-vous à la page [URL et plages d’adresses IP Office 365](http://aka.ms/o365endpoints) pour obtenir une liste à jour des services pris en charge par le biais d’ExpressRoute.

### <a name="how-much-does-expressroute-for-office-365-services-and-crm-online-cost?"></a>Combien coûte ExpressRoute pour les services Office 365 et CRM Online ?
Les services Office 365 et CRM Online nécessitent l'activation d'un module premium complémentaire. La [page de tarification](https://azure.microsoft.com/pricing/details/expressroute/) fournit des informations détaillées sur les coûts d’ExpressRoute.

### <a name="what-regions-is-expressroute-for-office-365-supported-in?"></a>Quelles régions sont prises en charge dans ExpressRoute pour Office 365 ?
Consultez la rubrique [Partenaires et emplacements ExpressRoute](expressroute-locations.md) pour obtenir la liste des partenaires et des emplacements où ExpressRoute est pris en charge.

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization?"></a>Puis-je accéder à Office 365 via Internet même si ExpressRoute a été configuré pour mon organisation ?
Oui. Les points de terminaison du service Office 365 sont accessibles via Internet même si ExpressRoute a été configuré pour votre réseau. Si votre emplacement est configuré pour vous connecter aux services Office 365 via ExpressRoute, vous vous connectez via ExpressRoute.

### <a name="can-dynamics-ax-online-be-accessed-over-an-expressroute-connection?"></a>Dynamics AX Online est-il accessible par le biais d’une connexion ExpressRoute ?
Non, ce n’est pas pris en charge.

<!--HONumber=Oct16_HO2-->



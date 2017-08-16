---
title: "Services de domaine Azure AD : instructions de mise en réseau | Microsoft Docs"
description: "Considérations relatives à la mise en réseau pour les services de domaine Azure Active Directory"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: maheshu
ms.translationtype: HT
ms.sourcegitcommit: 99523f27fe43f07081bd43f5d563e554bda4426f
ms.openlocfilehash: 8306c1ff72d348f5f327b79617e1422a78e26bdb
ms.contentlocale: fr-fr
ms.lasthandoff: 08/05/2017

---
# <a name="networking-considerations-for-azure-ad-domain-services"></a>Considérations relatives à la mise en réseau pour les services de domaine Azure AD
## <a name="how-to-select-an-azure-virtual-network"></a>Comment sélectionner un réseau virtuel Azure
Les instructions suivantes vous aident à sélectionner un réseau virtuel en vue de l’utiliser avec les services de domaine Azure AD.

### <a name="type-of-azure-virtual-network"></a>Type de réseau virtuel Azure
* Vous pouvez activer les services de domaine Azure AD dans un réseau virtuel Azure Classic.
* Les services de domaine Azure AD **ne peuvent pas être activés dans les réseaux virtuels créés à l’aide d’Azure Resource Manager**.
* Vous pouvez connecter un réseau virtuel basé sur Resource Manager à un réseau virtuel Classic dans lequel les services de domaine Azure AD sont activés. Vous pouvez ensuite utiliser les services de domaine Azure AD dans le réseau virtuel basé sur Resource Manager. Pour plus d’informations, consultez la section [Connectivité réseau](active-directory-ds-networking.md#network-connectivity).
* **Réseaux virtuels régionaux**: si vous prévoyez d’utiliser un réseau virtuel existant, assurez-vous qu’il s’agit d’un réseau virtuel régional.

  * Les réseaux virtuels qui recourent au mécanisme des groupes d’affinités hérité ne peuvent pas être utilisés avec les services de domaine Azure AD.
  * Pour utiliser les services de domaine Azure AD, vous devez [migrer les réseaux virtuels hérités vers des réseaux virtuels régionaux](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

### <a name="azure-region-for-the-virtual-network"></a>Région Azure pour le réseau virtuel
* Votre domaine géré par les services de domaine Azure AD est déployé dans la même région Azure que le réseau virtuel dans lequel vous choisissez d’activer le service.
* Sélectionnez un réseau virtuel dans une région Azure prise en charge par les services de domaine Azure AD.
* Pour connaître les régions Azure dans lesquelles les services de domaine Azure AD sont disponibles, consultez la page [Services Azure par région](https://azure.microsoft.com/regions/#services/) .

### <a name="requirements-for-the-virtual-network"></a>Conditions préalables pour le réseau virtuel
* **Proximité avec les charges de travail Azure**: sélectionnez le réseau virtuel qui héberge actuellement ou qui hébergera des machines virtuelles ayant besoin d’accéder aux services de domaine Azure AD.
* **Serveurs DNS personnalisés/propres**: assurez-vous qu’aucun serveur DNS personnalisé n’est configuré pour le réseau virtuel.
* **Domaines existants portant le même nom de domaine** : vérifiez qu’aucun domaine existant portant le même nom de domaine n’est disponible sur ce réseau virtuel. Supposons par exemple qu’un domaine appelé « contoso.com » soit déjà disponible sur le réseau virtuel sélectionné. Par la suite, vous essayez d’activer un domaine géré par les services de domaine Azure AD portant le même nom de domaine (soit « contoso.com ») sur ce réseau virtuel. Vous rencontrez un échec lorsque vous essayez d’activer les services de domaine Azure AD en raison des conflits de noms de domaine sur ce réseau virtuel. Dans ce cas, vous devez utiliser un autre nom pour configurer votre domaine géré par les services de domaine Azure AD. Vous pouvez également annuler l’approvisionnement du domaine existant, puis passer à l’activation des services de domaine Azure AD.

> [!WARNING]
> Vous ne pouvez pas déplacer les services de domaine vers un autre réseau virtuel une fois le service activé.
>
>

## <a name="network-security-groups-and-subnet-design"></a>Conception de groupes de sécurité et de sous-réseaux
Un [groupe de sécurité réseau (NSG)](../virtual-network/virtual-networks-nsg.md) contient une liste des règles de liste de contrôle d’accès (ACL) qui autorise ou rejette les instances de machine virtuelle dans un réseau virtuel. Des groupes de sécurité réseau peuvent être associés à des sous-réseaux ou à des instances de machine virtuelle au sein de ce sous-réseau. Lorsqu’un groupe de sécurité réseau est associé à un sous-réseau, les règles ACL s’appliquent à toutes les instances de machine virtuelle présentes dans ce sous-réseau. En outre, le trafic vers un ordinateur virtuel individuel peut être limité par l’association d’un groupe de sécurité réseau directement à la machine virtuelle.

![Conception de sous-réseau recommandée](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

### <a name="best-practices-for-choosing-a-subnet"></a>Meilleures pratiques pour le choix d’un sous-réseau
* Déployez les services de domaine Azure AD dans un **sous-réseau dédié distinct** au sein de votre réseau virtuel Azure.
* N’appliquez pas de groupes de sécurité réseau au sous-réseau dédié pour votre domaine géré. Si vous devez appliquer des groupes de sécurité réseau au sous-réseau dédié, veillez à **ne pas bloquer les ports requis pour l’entretien et la gestion de votre domaine**.
* Ne limitez pas de manière excessive le nombre d’adresses IP disponibles au sein du sous-réseau dédié de votre domaine géré. Cette limitation empêche le service de mettre à disposition deux contrôleurs de domaine pour votre domaine géré.
* **N’activez pas les services de domaine Azure AD dans le sous-réseau de passerelle** de votre réseau virtuel.

> [!WARNING]
> Lorsque vous associez un groupe de sécurité réseau et un sous-réseau dans lequel les services de domaine Azure AD sont activés, vous pouvez affecter la capacité de Microsoft à gérer le domaine. En outre, la synchronisation entre votre client Azure AD et votre domaine géré est interrompue. **Le contrat de niveau de service (SLA) ne concerne pas les déploiements dans lesquels un groupe de sécurité réseau a été appliqué et qui empêche les services de domaine Azure AD de mettre à jour et de gérer votre domaine.**
>
>

### <a name="ports-required-for-azure-ad-domain-services"></a>Ports requis pour les services de domaine Azure AD
Les ports suivants sont requis pour les services de domaine Azure AD pour l’entretien et la gestion de votre domaine géré. Assurez-vous que ces ports ne sont pas bloqués pour le sous-réseau dans lequel vous avez activé votre domaine géré.

| Numéro de port | Objectif |
| --- | --- |
| 443 |Synchronisation avec votre client Azure AD |
| 3389 |Gestion de votre domaine |
| 5986 |Gestion de votre domaine |
| 636 |Sécuriser l’accès LDAP (LDAPS) à votre domaine géré |

### <a name="sample-nsg-for-virtual-networks-with-azure-ad-domain-services"></a>Exemple de groupe de sécurité réseau pour les réseaux virtuels avec Azure AD Domain Services
Le tableau suivant illustre un exemple de groupe de sécurité réseau que vous pouvez configurer pour un réseau virtuel avec un domaine managé Azure AD Domain Services. Cette règle autorise le trafic entrant à partir des ports spécifiés ci-dessus pour garantir que votre domaine managé reste corrigé, mis à jour et peut être surveillé par Microsoft. La règle « DenyAll » par défaut s’applique à tout autre trafic entrant en provenance d’internet.

En outre, le groupe de sécurité réseau illustre comment verrouiller l’accès LDAP sécurisé sur Internet. Ignorez cette règle si vous n’avez pas activé l’accès LDAP sécurisé à votre domaine managé sur Internet. Le groupe de sécurité réseau contient un ensemble de règles qui autorisent l’accès LDAPS entrant sur le port TCP 636 uniquement à partir d’un ensemble spécifique d’adresses IP. La règle de groupe de sécurité réseau pour autoriser l’accès LDAPS via Internet à partir d’adresses IP spécifiées prend le pas sur la règle DenyAll NSG.

![Exemple de groupe de sécurité réseau pour l’accès LDAP sécurisé via internet](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**Informations supplémentaires** - [Créer un groupe de sécurité réseau](../virtual-network/virtual-networks-create-nsg-arm-pportal.md).


## <a name="network-connectivity"></a>Connectivité réseau
Un domaine géré par les services de domaine Azure AD peut être activé uniquement au sein d’un seul réseau virtuel Classic dans Azure. Les réseaux virtuels créés à l’aide d’Azure Resource Manager ne sont pas pris en charge.

### <a name="scenarios-for-connecting-azure-networks"></a>Scénarios de connexion de réseaux Azure
Connectez des réseaux virtuels Azure en vue d’utiliser le domaine géré dans n’importe lequel des scénarios de déploiement suivants :

#### <a name="use-the-managed-domain-in-more-than-one-azure-classic-virtual-network"></a>Utiliser le domaine géré dans plusieurs réseaux virtuels Azure Classic
Vous pouvez connecter d’autres réseaux virtuels Azure Classic au réseau virtuel Azure Classic dans lequel vous avez activé les services de domaine Azure AD. Cette connexion VPN vous permet d’utiliser le domaine géré avec vos charges de travail déployées dans d’autres réseaux virtuels.

![Connexion entre des réseaux virtuels Classic](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### <a name="use-the-managed-domain-in-a-resource-manager-based-virtual-network"></a>Utiliser le domaine géré dans un réseau virtuel basé sur Resource Manager
Vous pouvez connecter un réseau virtuel basé sur Resource Manager au réseau virtuel Azure Classic dans lequel vous avez activé les services de domaine Azure AD. Cette connexion vous permet d’utiliser le domaine géré avec les charges de travail déployées dans le réseau virtuel basé sur Resource Manager.

![Connexion entre un réseau virtuel Resource Manager et un réseau virtuel Classic](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)

### <a name="network-connection-options"></a>Options de connexion réseau
* **Connexion entre deux réseaux virtuels à l’aide d’une connexion VPN de site à site**: la connexion entre deux réseaux virtuels est similaire à la connexion d’un réseau virtuel à un emplacement de site local. Les deux types de connectivité font appel à une passerelle VPN pour offrir un tunnel sécurisé utilisant Ipsec/IKE.

    ![Connexion entre des réseaux virtuels à l’aide d’une passerelle VPN](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [Plus d’informations : connecter des réseaux virtuels à l’aide d’une passerelle VPN](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)
* **Connexion entre deux réseaux virtuels à l’aide d’une homologation de réseaux virtuels**: l’homologation de réseaux virtuels est un mécanisme permettant de connecter deux réseaux virtuels situés dans la même région via le réseau principal Azure. Une fois homologués, les deux réseaux virtuels apparaissent comme un seul réseau pour tous les besoins de connectivité. Ils sont toujours gérés comme des ressources distinctes, mais les machines virtuelles se trouvant dans ces réseaux virtuels peuvent communiquer directement entre elles à l’aide d’adresses IP privées.

    ![Connexion entre des réseaux virtuels à l’aide d’une homologation](./media/active-directory-domain-services-design-guide/vnet-peering.png)

    [Plus d’informations : homologation de réseaux virtuels](../virtual-network/virtual-network-peering-overview.md)

<br>

## <a name="related-content"></a>Contenu connexe
* [Homologation de réseaux virtuels Azure](../virtual-network/virtual-network-peering-overview.md)
* [Configurer une connexion de réseau virtuel à réseau virtuel pour le modèle de déploiement classique](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)
* [Groupes de sécurité réseau Azure](../virtual-network/virtual-networks-nsg.md)
* [Créer des groupes de sécurité réseau à l’aide du portail Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)


---
title: Exemples de configuration des routeurs clients ExpressRoute | Microsoft Docs
description: Cette page fournit des exemples de configuration pour les routeurs Cisco et Juniper.
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
ms.assetid: 564826bc-017a-4683-a385-37c9fa814948
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: b77a20274e22827aaa8aa4d354b62d086a19b206
ms.openlocfilehash: 6fefb9cfa96b0a6b7acfe4d7fcb17cb13ec240a0


---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Exemples de configuration de routeur pour configurer et gérer le routage
Cette page fournit une interface et des exemples de configuration de routage pour les routeurs des séries Cisco IOS-XE et Juniper MX. Ces exemples sont fournis à titre indicatif uniquement et ne doivent pas être utilisés en l’état. Vous pouvez vous adresser au fournisseur pour rechercher les configurations adaptées à votre réseau. 

> [!IMPORTANT]
> Les exemples de cette page sont fournis à titre purement indicatif. Vous devez vous adresser à l’équipe commerciale/technique de votre fournisseur et à votre équipe de mise en réseau pour rechercher les configurations adaptées à vos besoins. Microsoft ne prend pas en charge les problèmes liés aux configurations répertoriées dans cette page. Vous devez contacter le fournisseur de votre périphérique pour la prise en charge des problèmes.
> 
> 

Les exemples de configuration de routeur ci-dessous s’appliquent à toutes les homologations. Pour plus d’informations sur le routage, voir [Homologations ExpressRoute](expressroute-circuit-peerings.md) et [Configuration requise pour le routage ExpressRoute](expressroute-routing.md).

## <a name="cisco-ios-xe-based-routers"></a>Routeurs Cisco IOS-XE
Les exemples de cette section s’appliquent à tous les routeurs exécutant la famille de systèmes d’exploitation IOS-XE.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Configuration des interfaces et des sous-interfaces
Vous aurez besoin d’une sous-interface par homologation dans chaque routeur que vous connectez à Microsoft. Une sous-interface peut être identifiée avec un ID de réseau local virtuel ou une paire empilée d’ID de réseau local virtuel, et une adresse IP.

**Définition de l’interface Dot1Q**

Cet exemple fournit la définition d’une sous-interface avec un ID de réseau local virtuel unique. L’ID de réseau local virtuel est unique pour chaque homologation. Le dernier octet de votre adresse IPv4 est toujours un nombre impair.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**Définition de l’interface QinQ**

Cet exemple fournit la définition d’une sous-interface avec deux ID de réseau local virtuel. L’ID de réseau local virtuel externe (s-tag), s’il est utilisé, est le même pour toutes les homologations. L’ID de réseau local virtuel interne (c-tag) est unique pour chaque homologation. Le dernier octet de votre adresse IPv4 est toujours un nombre impair.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="2-setting-up-ebgp-sessions"></a>2. Configuration de sessions eBGP
Vous devez configurer une session BGP avec Microsoft pour chaque homologation. L’exemple suivant vous permet de configurer une session BGP avec Microsoft. Si l’adresse IPv4 utilisée pour votre sous-interface est a.b.c.d, l’adresse IP du voisin BGP (Microsoft) est a.b.c.d+1. Le dernier octet de l’adresse IPv4 du voisin BGP est toujours un nombre pair.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Configuration des préfixes à publier sur la session BGP
Vous pouvez configurer votre routeur pour qu’il publie certains préfixes sur Microsoft. Pour ce faire, utilisez l’exemple ci-dessous.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="4-route-maps"></a>4. Cartes d’itinéraire
Vous pouvez utiliser des cartes d’itinéraire et des listes de préfixes pour filtrer les préfixes propagés sur votre réseau. L’exemple ci-dessous peut vous aider dans cette tâche. Assurez-vous d’avoir configuré les listes de préfixe appropriées.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
     exit-address-family
    !
    route-map <MS_Prefixes_Inbound> permit 10
     match ip address prefix-list <MS_Prefixes>
    !


## <a name="juniper-mx-series-routers"></a>Routeurs de la série Juniper MX
Les exemples de cette section s’appliquent à tous les routeurs de la série Juniper MX.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Configuration des interfaces et des sous-interfaces

**Définition de l’interface Dot1Q**

Cet exemple fournit la définition d’une sous-interface avec un ID de réseau local virtuel unique. L’ID de réseau local virtuel est unique pour chaque homologation. Le dernier octet de votre adresse IPv4 est toujours un nombre impair.

    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }


**Définition de l’interface QinQ**

Cet exemple fournit la définition d’une sous-interface avec deux ID de réseau local virtuel. L’ID de réseau local virtuel externe (s-tag), s’il est utilisé, est le même pour toutes les homologations. L’ID de réseau local virtuel interne (c-tag) est unique pour chaque homologation. Le dernier octet de votre adresse IPv4 est toujours un nombre impair.

    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           

### <a name="2-setting-up-ebgp-sessions"></a>2. Configuration de sessions eBGP
Vous devez configurer une session BGP avec Microsoft pour chaque homologation. L’exemple suivant vous permet de configurer une session BGP avec Microsoft. Si l’adresse IPv4 utilisée pour votre sous-interface est a.b.c.d, l’adresse IP du voisin BGP (Microsoft) est a.b.c.d+1. Le dernier octet de l’adresse IPv4 du voisin BGP est toujours un nombre pair.

    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Configuration des préfixes à publier sur la session BGP
Vous pouvez configurer votre routeur pour qu’il publie certains préfixes sur Microsoft. Pour ce faire, utilisez l’exemple ci-dessous.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="4-route-maps"></a>4. Cartes d’itinéraire
Vous pouvez utiliser des cartes d’itinéraire et des listes de préfixes pour filtrer les préfixes propagés sur votre réseau. L’exemple ci-dessous peut vous aider dans cette tâche. Assurez-vous d’avoir configuré les listes de préfixe appropriées.

    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
        prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez le [Forum Aux Questions sur ExpressRoute](expressroute-faqs.md) .




<!--HONumber=Nov16_HO3-->



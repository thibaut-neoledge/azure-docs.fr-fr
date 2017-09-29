---
title: "Utiliser Azure DNS avec d’autres services Azure | Microsoft Docs"
description: "Comprendre comment utiliser Azure DNS pour résoudre le nom d’autres services Azure"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: a6bba6b3b924564fe7ae16fa1265dd4d93bd6b94
ms.openlocfilehash: a286508fe445208b6bb348d07434b5722cc3f11e
ms.contentlocale: fr-fr
ms.lasthandoff: 09/28/2017

---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Fonctionnement d’Azure DNS avec d’autres services Azure

Azure DNS est un service hébergé de gestion de DNS et de résolution de noms. Il vous permet de créer des noms DNS publics pour les autres applications et services que vous avez déployés dans Azure. La création d’un nom pour un service Azure dans votre domaine personnalisé est aussi simple que l’ajout d’un enregistrement du type correct pour votre service.

* Pour les adresses IP allouées de manière dynamique, vous devez créer un enregistrement DNS CNAME qui mappe au nom DNS qu’Azure a créé pour votre service. Les normes DNS vous empêchent d’utiliser un enregistrement CNAME à l’extrémité de la zone.
* Pour les adresses IP allouées de manière statique, vous pouvez créer un enregistrement DNS A utilisant n’importe quel nom, notamment un nom de *domaine nu* à l’extrémité de la zone.

Le tableau suivant décrit les types d’enregistrement pris en charge qui peuvent être utilisés pour les différents services Azure. Comme vous pouvez le voir à partir de cette table, Azure DNS prend uniquement en charge les enregistrements DNS pour les ressources réseau accessibles sur Internet. Azure DNS ne peut pas être utilisé pour la résolution de nom d’adresses privées internes.

| Service Azure | Interface réseau | Description |
| --- | --- | --- |
| Application Gateway |[Adresse IP publique frontale](dns-custom-domain.md#public-ip-address) |Vous pouvez créer un enregistrement DNS A ou CNAME. |
| Équilibreur de charge |[Adresse IP publique frontale](dns-custom-domain.md#public-ip-address)  |Vous pouvez créer un enregistrement DNS A ou CNAME. L’équilibreur de charge peut disposer d’une adresse IP publique IPv6 affectée de manière dynamique. Par conséquent, vous devez créer un enregistrement CNAME pour une adresse IPv6. |
| Traffic Manager |Nom public |Vous pouvez uniquement créer un CNAME qui mappe au nom trafficmanager.net affecté à votre profil Traffic Manager. Pour plus d’informations, consultez l’article [Fonctionnement de Traffic Manager](../traffic-manager/traffic-manager-overview.md#traffic-manager-example). |
| Service cloud |[Adresse IP publique](dns-custom-domain.md#public-ip-address) |Pour les adresses IP allouées de manière statique, vous pouvez créer un enregistrement DNS A. Pour les adresses IP allouées de manière dynamique, vous devez créer un enregistrement CNAME qui mappe au nom *cloudapp.net* . Cette règle s’applique aux machines virtuelles créées dans le portail classique, car elles sont déployées sous la forme d’un service cloud. Pour plus d’informations, consultez [Configurer un nom de domaine personnalisé dans Services cloud](../cloud-services/cloud-services-custom-domain-name-portal.md). |
| App Service | [Adresse IP externe](dns-custom-domain.md#app-service-web-apps) |Pour les adresses IP externes, vous pouvez créer un enregistrement DNS A. Dans le cas contraire, vous devez créer un enregistrement CNAME qui mappe au nom azurewebsites.net. Pour plus d’informations, consultez [Mapper un nom de domaine personnalisé à une application Azure](../app-service/app-service-web-tutorial-custom-domain.md) |
| Machines virtuelles Resource Manager |[Adresse IP publique](dns-custom-domain.md#public-ip-address) |Les machines virtuelles Resource Manager peuvent avoir des adresses IP publiques. Une machine virtuelle avec une adresse IP publique peut également être derrière un équilibreur de charge. Vous pouvez créer un enregistrement DNS A ou CNAME pour l’adresse publique. Ce nom personnalisé peut être utilisé pour contourner l’adresse IP virtuelle de l’équilibreur de charge. |
| les machines virtuelles Classic, |[Adresse IP publique](dns-custom-domain.md#public-ip-address) |Les machines virtuelles classiques créées à l’aide de PowerShell ou de l’interface de ligne de commande peuvent être configurées avec une adresse virtuelle (réservée) dynamique ou statique. Vous pouvez créer un enregistrement DNS CNAME ou A, respectivement. |


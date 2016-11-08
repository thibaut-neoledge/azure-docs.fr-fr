---
title: Rediriger un domaine Internet d’entreprise vers un domaine Traffic Manager | Microsoft Docs
description: Cet article vous aide à rediriger votre nom de domaine d’entreprise vers un nom de domaine Traffic Manager.
services: traffic-manager
documentationcenter: ''
author: sdwheeler
manager: carmonm
editor: tysonn

ms.service: traffic-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/17/2016
ms.author: sewhee

---
# Rediriger d’un domaine Internet d’entreprise vers un domaine Azure Traffic Manager
Pour rediriger votre nom de domaine d’entreprise vers un nom de domaine Traffic Manager, modifiez l’enregistrement de ressource DNS sur votre serveur DNS Internet afin d’utiliser le type d’enregistrement CNAME, qui mappe votre nom de domaine d’entreprise au nom de domaine de votre profil Traffic Manager. Vous pouvez voir le nom de domaine Traffic Manager dans la section **Général** de la page Configuration du profil Traffic Manager.

Par exemple, pour rediriger le nom de domaine d’entreprise www.contoso.com vers le nom de domaine Traffic Manager contoso.trafficmanager.net, vous devez mettre à jour votre enregistrement de ressource DNS comme suit :

    www.contoso.com IN CNAME contoso.trafficmanager.net

L’intégralité des demandes de trafic pour *www.contoso.com* est alors redirigée vers *contoso.trafficmanager.net*.

> [!IMPORTANT]
> Vous ne pouvez pas rediriger un domaine de second niveau tel que *contoso.com* vers le domaine Traffic Manager. Il s’agit d’une limitation du protocole DNS qui n’autorise pas les enregistrements CNAME pour les noms de domaine de deuxième niveau.
> 
> 

## Étapes suivantes
[Méthodes de routage de Traffic Manager](traffic-manager-routing-methods.md)

[Traffic Manager - Désactiver, activer ou supprimer un profil](disable-enable-or-delete-a-profile.md)

[Traffic Manager - Désactiver ou activer un point de terminaison](disable-or-enable-an-endpoint.md)

<!---HONumber=AcomDC_0824_2016-->
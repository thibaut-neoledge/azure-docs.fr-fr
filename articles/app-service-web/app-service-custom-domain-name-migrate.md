---
title: "Migrer un domaine personnalisé actif vers Azure App Service | Microsoft Docs"
description: "Découvrez comment migrer un domaine personnalisé déjà affecté à un site de production à votre application dans Azure App Service sans interruption de service."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: jimbe
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d6d506eef720488969c5b33fe4b94c02752c6b58
ms.lasthandoff: 04/03/2017


---
# <a name="migrate-an-active-custom-domain-to-azure-app-service"></a>Migrer un domaine personnalisé actif vers Azure App Service

Cet article vous montre comment migrer un domaine personnalisé actif vers [Azure App Service](../app-service/app-service-value-prop-what-is.md) sans interruption de service.

Lorsque vous migrez un site de production et son nom de domaine vers App Service, ce nom de domaine s’occupe déjà du trafic de production, et vous ne souhaitez pas d’interruption de service pour la résolution DNS lors du processus de migration. Dans ce cas, vous devez lier le nom de domaine à votre application Azure de façon préventive pour la vérification du domaine. 

## <a name="prerequisites"></a>Composants requis

Cet article suppose que vous savez déjà comment [mapper manuellement un domaine personnalisé à App Service](web-sites-custom-domain-name.md).

## <a name="bind-the-domain-name-preemptively"></a>Lier le nom de domaine de manière préemptive

Lorsque vous liez un domaine personnalisé de manière préemptive, vous effectuez les deux opérations suivantes avant d’apporter des modifications à vos enregistrements DNS :

- Vérifier la propriété du domaine
- Activer le nom de domaine de votre application

Lorsque vous modifiez enfin l’enregistrement DNS pour pointer vers votre application App Service, les clients sont redirigés de votre ancien site vers votre application App Service sans interruption de service dans la résolution DNS.

Pour ce faire, procédez comme suit :

1. Commencez par créer un enregistrement TXT de vérification avec votre registre DNS en suivant les étapes de la section [Créer les enregistrements DNS](web-sites-custom-domain-name.md#createdns).
L’enregistrement TXT supplémentaire adopte la convention qui mappe de &lt;*sous-domaine*>.&lt;*domaine_racine*> à &lt;*nom_application*>.azurewebsites.net.
Pour des exemples, consultez le tableau suivant :  
 
    <table cellspacing="0" border="1">
    <tr>
    <th>Exemple de nom de domaine complet</th>
    <th>Hôte TXT</th>
    <th>Valeur TXT</th>
    </tr>
    <tr>
    <td>contoso.com (racine)</td>
    <td>awverify.contoso.com</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
    </tr>
    <tr>
    <td>www.contoso.com (sous-domaine)</td>
    <td>awverify.www.contoso.com</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
    </tr>
    <tr>
    <td>\*.contoso.com (caractère générique)</td>
    <td>awverify\*.contoso.com</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
    </tr>
    </table>

2. Puis, ajoutez votre nom de domaine personnalisé à votre application Azure en suivant les étapes de la section [Activer le nom de domaine personnalisé de votre application](web-sites-custom-domain-name.md#enable).

    Votre domaine personnalisé est maintenant activé dans votre application Azure. La seule chose qui vous reste à faire est de mettre à jour l’enregistrement DNS avec votre bureau d’enregistrement de domaine.

3. Enfin, mettez à jour l’enregistrement DNS de votre domaine pour pointer vers votre application Azure, comme indiqué dans la section [Créer les enregistrements DNS](web-sites-custom-domain-name.md#createdns). 

    Le trafic utilisateur devrait être redirigé vers votre application Azure immédiatement après la propagation DNS.

## <a name="next-steps"></a>Étapes suivantes
Apprenez à sécuriser votre nom de domaine personnalisé avec HTTPS en [achetant un certificat SSL dans Azure](web-sites-purchase-ssl-web-site.md) ou [à l’aide d’un certificat SSL depuis un autre emplacement](web-sites-configure-ssl-certificate.md).

> [!NOTE]
> Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](https://azure.microsoft.com/try/app-service/), où vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.
> 
> 

[Prise en main d’Azure DNS](../dns/dns-getstarted-create-dnszone.md)  
[Créer des enregistrements DNS pour une application web dans un domaine personnalisé](../dns/dns-web-sites-custom-domain.md)  
[Délégation de domaine à Azure DNS](../dns/dns-domain-delegation.md)



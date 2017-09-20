---
title: "Configuration d’un nom de domaine personnalisé pour une application web dans Azure App Service utilisant Traffic Manager pour l’équilibrage de charge."
description: "Utilisation d’un nom de domaine personnalisé pour une application web dans Azure App Service intégrant Traffic Manager pour équilibrer la charge."
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: 532a953c02ef40eb55705eac885d74c5574deed5
ms.contentlocale: fr-fr
ms.lasthandoff: 01/05/2017

---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Configuration d’un nom de domaine personnalisé pour une application web dans Azure App Service utilisant Traffic Manager
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Cet article contient des instructions génériques sur l’utilisation d’un nom de domaine personnalisé avec Azure App Service utilisant Traffic Manager pour équilibrer la charge.

[!INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>Présentation des enregistrements DNS
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>

## <a name="configure-your-web-apps-for-standard-mode"></a>Configuration de vos applications web en mode Standard
[!INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>Ajout d'un enregistrement DNS pour votre domaine personnalisé
> [!NOTE]
> Si vous avez acheté un domaine via Azure App Service Web Apps, ignorez les étapes suivantes et reportez-vous à l'étape finale de l'article [Acheter un domaine pour Web Apps](custom-dns-web-site-buydomains-web-app.md) .
> 
> 

Pour associer votre domaine personnalisé à une application web dans Azure App Service, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par le bureau d’enregistrement de domaines auprès duquel vous avez acheté votre nom de domaine. Pour localiser et utiliser les outils DNS, procédez comme suit.

1. Connectez-vous à votre compte du bureau d’enregistrement de domaines, puis recherchez une page pour la gestion des enregistrements DNS. Recherchez la mention **Domain Name**, **DNS** ou **Name Server Management**. Vous trouverez généralement un lien vers cette page en affichant vos informations de compte, puis en recherchant un lien tel que **Mes domaines**.
2. Lorsque vous avez trouvé la page de gestion de votre nom de domaine, recherchez un lien vous permettant de modifier les enregistrements DNS. Il est probablement répertorié en tant que lien de configuration de **fichier de zone**, d’**enregistrements DNS** ou **avancé**.
   
   * Il est possible que plusieurs enregistrements soient déjà créés pour cette page, par exemple, une entrée associant « **@** » ou « \* » à une page de parc de domaine. Cette page peut également contenir des enregistrements pour des sous-domaines courants, tels que **www**.
   * La page mentionnera les **enregistrements CNAME**ou fournira une liste déroulante permettant de sélectionner un type d'enregistrement. Elle peut également mentionner d’autres enregistrements, tels que des **enregistrements A** et des **enregistrements MX**. Dans certains cas, les enregistrements CNAME sont appelés différemment, par exemple **Enregistrement d'alias**.
   * Cette page comprend également des champs permettant de **mapper** à partir d’un **nom d’hôte** ou d’un **nom de domaine** vers un autre nom de domaine.
3. Bien que les spécificités de chaque bureau d’enregistrement varient, vous mappez généralement *à partir de* votre nom de domaine personnalisé (par exemple, **contoso.com**,) *vers* le nom de domaine Traffic Manager (**contoso.trafficmanager.net**) qui est utilisé pour votre application web.
   
   > [!NOTE]
   > Autrement, si un enregistrement est déjà utilisé et que vous devez lier vos applications à titre préventif, vous pouvez créer un enregistrement CNAME supplémentaire. Par exemple, pour lier **www.contoso.com** à votre application web à titre préventif, créez un enregistrement CNAME entre **awverify.www** et **contoso.trafficmanager.net**. Vous pouvez ensuite ajouter « www.contoso.com » à votre application web sans modifier l’enregistrement CNAME « www ». Pour plus d’informations, consultez [Créer des enregistrements DNS pour une application web dans un domaine personnalisé][CREATEDNS].
   > 
   > 
4. Lorsque vous avez fini d'ajouter ou de modifier des enregistrements DNS auprès de votre bureau d'enregistrement, enregistrez les modifications.

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>Activation de Traffic Manager
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez le [Centre pour développeurs Node.js](/develop/nodejs/).

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md


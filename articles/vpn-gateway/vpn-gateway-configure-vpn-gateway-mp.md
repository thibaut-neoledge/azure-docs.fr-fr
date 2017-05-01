---
title: "Configurer une passerelle VPN : portail classique : Azure | Microsoft Docs"
description: "Cet article vous indique comment configurer votre passerelle VPN de réseau virtuel et comment modifier un type de routage VPN de passerelle. Ces étapes s’appliquent au modèle de déploiement classique et au portail classique."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: fbe59ba8-b11f-4d21-9bb1-225ec6c6d351
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 2ea4e6bb86b1ba6f7b501b193d0713d3901457af
ms.lasthandoff: 04/06/2017


---
# <a name="configure-a-vpn-gateway-in-the-classic-portal"></a>Configurer une passerelle VPN dans le portail classique 
Si vous voulez créer une connexion intersite sécurisée entre Azure et votre emplacement local, vous devrez créer une passerelle de réseau virtuel. Une passerelle VPN est un type spécifique de passerelle de réseau virtuel. Dans le modèle de déploiement classique, le routage VPN d’une passerelle VPN peut être de type statique ou dynamique. Le type VPN que vous choisissez dépend de votre plan de conception de réseau et du périphérique VPN local à utiliser. Pour plus d’informations sur les périphériques VPN, voir [À propos des périphériques VPN](vpn-gateway-about-vpn-devices.md).

**À propos des modèles de déploiement Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-overview"></a>Présentation de la configuration
Les étapes suivantes vous expliquent comment configurer votre passerelle VPN dans le portail classique. Ces étapes s’appliquent aux passerelles pour les réseaux virtuels créés à l’aide du modèle de déploiement classique. Actuellement, les paramètres de configuration pour les passerelles ne sont pas tous disponibles dans le portail Azure. Quand ils le seront, nous créerons une série d’instructions qui s’appliquent au portail Azure.

### <a name="before-you-begin"></a>Avant de commencer
Avant de configurer votre passerelle, vous devez tout d’abord créer votre réseau virtuel. Pour savoir comment créer un réseau virtuel pour des connexions entre différents locaux, consultez [Configuration d’un réseau virtuel avec une connexion VPN de site à site](vpn-gateway-site-to-site-create.md) ou [Configuration d’un réseau virtuel avec une connexion VPN de point à site](vpn-gateway-point-to-site-create.md). Suivez ensuite les étapes ci-dessous pour configurer la passerelle VPN et rassemblez les informations qui vous sont nécessaires pour configurer votre périphérique VPN. 

Si vous disposez déjà d’une passerelle VPN et que vous souhaitez modifier le type de routage VPN, consultez la section [Modification du type de routage VPN de votre passerelle](#how-to-change-the-vpn-routing-type-for-your-gateway).

## <a name="create-a-vpn-gateway"></a>Créer une passerelle VPN
1. Dans le [portail Azure Classic](https://manage.windowsazure.com), dans la page **Réseaux**, vérifiez que la colonne d’état de votre réseau virtuel indique **Créé**.
2. Dans la colonne **Nom** , cliquez sur le nom de votre réseau virtuel.
3. Dans la page **Tableau de bord** , notez que pour ce réseau virtuel, aucune passerelle n’est encore configurée. Vous verrez cet état tout au long des étapes de configuration de votre passerelle.

![Passerelle non créée](./media/vpn-gateway-configure-vpn-gateway-mp/IC717025.png)

Cliquez ensuite sur **Créer une passerelle**en bas de la page. Vous pouvez sélectionner *Routage statique* ou *Routage dynamique*. Le type de routage VPN que vous sélectionnez dépend de quelques facteurs. Par exemple, ce que votre périphérique VPN prend en charge et si vous devez prendre en charge des connexions de point à site. Pour vérifier le type de routage dont vous avez besoin, consultez la rubrique [À propos des périphériques VPN pour Virtual Network Connectivity](vpn-gateway-about-vpn-devices.md) . Si vous souhaitez changer de type de routage de la passerelle VPN une fois celle-ci créée, il vous faudra la supprimer et en recréer une nouvelle. Lorsque le système vous demande de confirmer la création de la passerelle, cliquez sur **Oui**.

![Type de routage VPN de passerelle](./media/vpn-gateway-configure-vpn-gateway-mp/IC717026.png)

Lorsque la passerelle est en cours de création, notez que le graphique de la passerelle sur la page devient jaune et indique *Créer une passerelle*. La création de la passerelle peut durer jusqu’à 45 minutes. Attendez que la passerelle soit créée avant de pouvoir utiliser d’autres paramètres de configuration.

![Création de passerelle](./media/vpn-gateway-configure-vpn-gateway-mp/IC717027.png)

Lorsque l’état de la passerelle passe en *Connexion en cours*, vous pouvez collecter les informations dont vous aurez besoin pour votre périphérique VPN.

![Connexion de passerelle](./media/vpn-gateway-configure-vpn-gateway-mp/IC717028.png)

## <a name="site-to-site-connections"></a>Connexions site à site

### <a name="step-1-gather-information-for-your-vpn-device-configuration"></a>Étape 1. Collecter des informations pour la configuration de votre périphérique VPN
Si vous créez une connexion de site à site, une fois la passerelle créée, collectez les informations de configuration de votre périphérique VPN. Ces informations se trouvent sur la page **Tableau de bord** de votre réseau virtuel :

1. **Adresse IP de la passerelle** : l’adresse IP se trouve dans la page **Tableau de bord**. Vous ne pourrez le découvrir qu’après avoir créé votre passerelle.
2. **Clé partagée** : cliquez sur **Gérer la clé** au bas de l’écran. Cliquez sur l’icône située à côté de la clé pour la copier dans le Presse-papiers, puis collez et enregistrez la clé. Ce bouton ne fonctionne qu’avec un tunnel VPN S2S unique. Si vous avez de plusieurs tunnels VPN S2S, utilisez l’API *d’obtention de la clé partagée de la passerelle de réseau virtuel* ou l’applet de commande PowerShell.

![Gérer la clé](./media/vpn-gateway-configure-vpn-gateway-mp/IC717029.png)

### <a name="step-2--configure-your-vpn-device"></a>Étape 2.  Configuration de votre périphérique VPN
Les connexions site à site vers un réseau local nécessitent un périphérique VPN. Nous ne fournissons pas les étapes de configuration de tous les appareils VPN, mais les informations des liens ci-dessous peuvent vous être utiles :

- Pour plus d’informations sur les appareils VPN compatibles, consultez [Appareils VPN](vpn-gateway-about-vpn-devices.md). 
- Pour des liens vers les paramètres de configuration des appareils, consultez [Appareils VPN validés](vpn-gateway-about-vpn-devices.md#devicetable). Ces liens sont fournis dans la mesure du possible. Il est toujours préférable de vérifier les dernières informations de configuration auprès du fabricant de l’appareil.
- Pour plus d’informations sur la modification des exemples de configuration des appareils, consultez la page [Modifier les exemples](vpn-gateway-about-vpn-devices.md#editing).
- En ce qui concerne les paramètres IPsec/IKE, consultez la page [Paramètres](vpn-gateway-about-vpn-devices.md#ipsec).
- Avant de configurer votre périphérique VPN, identifiez également les éventuels [Problèmes de compatibilité connus avec le matériel](vpn-gateway-about-vpn-devices.md#known) pour le périphérique VPN que vous souhaitez utiliser.

Pour configurer votre périphérique VPN, vous avez besoin des éléments suivants :

- L’adresse IP publique de votre passerelle de réseau virtuel. Vous la trouverez dans le panneau **Vue d’ensemble** de votre réseau virtuel.
- Une clé partagée. Il s’agit de la clé partagée spécifiée lors de la création de la connexion VPN de site à site. Dans nos exemples, nous utilisons une clé partagée très basique. Vous devez générer une clé plus complexe à utiliser.

Une fois le périphérique VPN configuré, vous pouvez afficher vos informations de connexion mises à jour sur la page Tableau de bord de votre réseau virtuel.

### <a name="step-3-verify-your-local-network-ranges-and-vpn-gateway-ip-address"></a>Étape 3. Vérifier vos plages de réseau local et l’adresse IP de la passerelle VPN
#### <a name="verify-your-vpn-gateway-ip-address"></a>Vérifier votre adresse IP de passerelle VPN
Pour que la passerelle se connecte correctement, l’adresse IP de votre périphérique VPN doit être configurée pour le réseau local que vous avez spécifié pour votre configuration intersite. Elle est généralement configurée lors du processus de configuration de site à site. Toutefois, si vous avez déjà utilisé ce réseau local avec un autre périphérique ou que l’adresse IP a été modifiée pour ce réseau local, modifiez les paramètres pour spécifier l’adresse IP de la passerelle.

1. Pour vérifier l’adresse IP de votre passerelle, cliquez sur **Réseaux** dans le volet gauche du portail, puis sélectionnez **Réseaux locaux** en haut de la page. Vous pouvez afficher l’adresse de passerelle VPN pour chaque réseau local que vous avez créé. Pour modifier l'adresse IP, sélectionnez le réseau virtuel et cliquez sur **Modifier** en bas de la page.
2. Sur la page **Spécifier les détails de votre réseau local** , modifiez l'adresse IP, puis cliquez sur la flèche Suivant en bas de la page.
3. Dans la page **Spécifier l’espace d’adresses** , cliquez sur la coche en bas à droite pour enregistrer vos paramètres.

#### <a name="verify-the-address-ranges-for-your-local-networks"></a>Vérifier les plages d’adresses de vos réseaux locaux
Pour que le trafic adéquat franchisse la passerelle et atteigne votre emplacement local, vous devez vérifier que chaque plage d’adresses IP est spécifiée. Chaque plage doit être répertoriée dans la configuration **Réseaux locaux** d’Azure. Selon la configuration réseau de votre emplacement local, elle peut s’avérer quelque peu volumineuse. Le trafic lié à une adresse IP qui est contenue dans les plages répertoriées est envoyé par le biais de la passerelle VPN du réseau virtuel. Les plages que vous répertoriez ne doivent pas nécessairement être des plages privées, même si vous souhaitez vérifier que votre configuration locale est en mesure de recevoir le trafic entrant.

Pour ajouter ou modifier les plages d’un réseau local, suivez les étapes suivantes :

1. Pour modifier les plages d’adresses IP d’un réseau local, cliquez sur **Réseaux** dans le volet gauche du portail, puis sélectionnez **Réseaux locaux** en haut de la page. Pour découvrir la meilleure façon d’afficher les plages que vous avez répertoriées, voir la page **Modifier** . Pour modifier vos plages, sélectionnez le réseau virtuel et cliquez sur **Modifier** en bas de la page.
2. Sur la page **Spécifier les détails de votre réseau local** , n’effectuez aucune modification. Cliquez sur la flèche Suivant en bas à droite de la page.
3. Sur la page **Spécifier l’espace d’adressage** , modifiez votre espace d’adressage de réseau. Cliquez ensuite sur la coche pour enregistrer votre configuration.

## <a name="how-to-view-gateway-traffic"></a>Affichage du trafic de la passerelle
Vous pouvez afficher votre passerelle et le trafic de la passerelle depuis la page **Tableau de bord** de votre réseau virtuel.

Sur la page **Tableau de bord** , vous pouvez afficher les informations suivantes :

* La quantité de données qui transitent par votre passerelle (données entrantes et sortantes).
* Les noms des serveurs DNS qui sont spécifiés pour votre réseau virtuel.
* La connexion entre votre passerelle et votre périphérique VPN.
* La clé partagée qui est utilisée pour configurer la connexion de votre passerelle sur votre périphérique VPN.

## <a name="how-to-change-the-vpn-routing-type-for-your-gateway"></a>Modification du type de routage VPN de votre passerelle
Étant donné que certaines configurations de connectivité ne sont disponibles que pour certains types de routage de passerelle, vous serez peut-être amené à modifier le type de routage VPN d’une passerelle VPN existante. Vous souhaiterez par exemple ajouter une connectivité de point à site à une connexion de site à site déjà existante, qui dispose d’une passerelle statique. Les connexions point à site nécessitent une passerelle dynamique. Cela signifie que pour configurer une connexion P2S, vous devez modifier votre type de routage VPN de passerelle de statique à dynamique.

Pour modifier un type de routage VPN de passerelle, vous supprimez la passerelle existante et vous créez une passerelle avec le nouveau type de routage. Vous n’avez pas besoin de supprimer le réseau virtuel entier pour modifier le type de routage de passerelle.

Avant de modifier le type de routage VPN de votre passerelle, vérifiez que votre périphérique VPN prend en charge le type de routage que vous voulez utiliser. Pour télécharger de nouveaux exemples de configuration de routage et vérifier les spécifications de périphérique VPN, consultez la rubrique [À propos des périphériques VPN pour  Virtual Network Connectivity](vpn-gateway-about-vpn-devices.md).

> [!IMPORTANT]
> Lorsque vous supprimez une passerelle VPN de réseau virtuel, l’adresse IP virtuelle attribuée à la passerelle est de nouveau disponible. Quand vous recréez la passerelle, une nouvelle adresse IP virtuelle lui est affectée.
> 
> 

1. **Suppression de la passerelle VPN existante.**
   
    Dans la page **Tableau de bord** de votre réseau virtuel, accédez au bas de la page et cliquez sur **Supprimer une passerelle**. Patientez jusqu’à ce qu’une notification vous indique que la passerelle a été supprimée. Vous pourrez créer une passerelle après avoir reçu la notification vous indiquant que votre passerelle a bien été supprimée.
2. **Création d'une passerelle VPN.**
   
    Suivez la procédure mentionnée en haut de la page pour créer une nouvelle passerelle : [Création d’une passerelle VPN](#create-a-vpn-gateway).

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez ajouter des machines virtuelles à votre réseau virtuel. Consultez [Création d’une machine virtuelle personnalisée](../virtual-machines/windows/classic/createportal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Si vous souhaitez configurer une connexion VPN de point à site, voir l’article [Configuration d’une connexion VPN de point à site](vpn-gateway-point-to-site-create.md).



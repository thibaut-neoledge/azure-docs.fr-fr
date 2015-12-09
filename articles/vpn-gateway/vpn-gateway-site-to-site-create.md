<properties
   pageTitle="Créer un réseau virtuel avec une connexion VPN site à site à l’aide du Portail Azure Classic | Microsoft Azure"
   description="Créez un réseau virtuel avec une connexion VPN de site à site pour les configurations entre locaux et hybrides à l’aide du modèle de déploiement classique."
   services="vpn-gateway"
   documentationCenter=""
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/21/2015"
   ms.author="cherylmc"/>

# Créer un réseau virtuel avec une connexion VPN site à site à l’aide du Portail Azure Classic

> [AZURE.SELECTOR]
- [Azure Classic Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)

Cet article vous guidera tout au long de la création d’un réseau virtuel et d’une connexion VPN site à site à votre réseau local. Cet article s’applique au modèle de déploiement classique.

>[AZURE.NOTE]Il est important de comprendre qu’Azure fonctionne actuellement avec deux modèles de déploiement : Resource Manager et classique. Avant de commencer votre configuration, assurez-vous que vous comprenez les modèles de déploiement et les outils. Pour plus d’informations sur les modèles de déploiement, consultez [Modèles de déploiement Azure](../azure-classic-rm.md).

Vous pouvez sélectionner l’article correspondant au modèle de déploiement et l’outil de déploiement à l’aide des onglets situés ci-dessus. Par exemple, si vous souhaitez créer une connexion de passerelle VPN site à site à l’aide du modèle Azure Resource Manager au lieu d’utiliser le modèle classique, cliquez sur l’onglet **PowerShell - Resource Manager** (ci-dessus) pour accéder à [Créer une connexion VPN de site à site à l’aide d’Azure Resource Manager et de PowerShell.](vpn-gateway-create-site-to-site-rm-powershell.md).

 
## Avant tout chose

Vérifiez que vous disposez des éléments suivants avant de commencer la configuration.

- Un appareil VPN compatible et une personne qui est en mesure de le configurer. Consultez [À propos des périphériques VPN](vpn-gateway-about-vpn-devices.md). Si vous n’êtes pas familiarisé avec la configuration de votre appareil VPN ou avec les plages d’adresses IP situées dans la configuration de votre réseau local, vous devez vous associer à une personne qui peut fournir les informations.

-  Une adresse IP publique exposée en externe pour votre périphérique VPN. Cette adresse IP ne peut pas se trouver derrière un NAT.

- Un abonnement Azure. Si vous ne possédez pas déjà un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou vous inscrire à une [évaluation gratuite](http://azure.microsoft.com/pricing/free-trial/).


## Création de votre réseau virtuel

1. Connectez-vous au **Portail Azure Classic**.

2. Dans le coin inférieur gauche de l’écran, cliquez sur **Nouveau**. Dans le volet de navigation, cliquez sur **Services réseau**, puis sur **Réseau virtuel**. Cliquez sur **Custom Create** pour démarrer l'Assistant Configuration.

3. Remplissez les informations sur les pages suivantes pour créer votre réseau virtuel.

## Détails du réseau virtuel

Entrez les informations ci-après.

- **Nom** : nommez votre réseau virtuel. Par exemple, *NordFranceVNet*. Sachant que vous utiliserez ce nom de réseau virtuel au moment de déployer vos machines virtuelles et vos instances PaaS, vous préféreriez probablement avoir un nom qui n’est pas trop compliqué.
- **Emplacement** : l’emplacement est directement associé à l’emplacement physique (région) où vous souhaitez que vos ressources (machines virtuelles) résident. Par exemple, si les machines virtuelles que vous déployez dans ce réseau virtuel doivent être situées physiquement dans le *nord de la France*, sélectionnez cet emplacement. Après avoir créé votre réseau virtuel, vous ne pourrez plus modifier la région qui lui est associée.

## Serveurs DNS et connectivité VPN
Saisissez les informations ci-dessous, puis cliquez sur la flèche Suivant située en bas à droite.

- **Serveurs DNS** : entrez le nom et l’adresse IP du serveur DNS, ou sélectionnez un serveur DNS précédemment inscrit dans le menu contextuel. Ce paramètre ne crée pas de serveur DNS. Il vous permet de spécifier les serveurs DNS que vous souhaitez utiliser pour la résolution de noms pour ce réseau virtuel.
- **Configurer une connexion VPN site à site** : activez la case à cocher **Configuration d’un VPN de site à site**.
- **Réseau local** : un réseau local représente votre emplacement physique local. Vous pouvez sélectionner un réseau local que vous avez précédemment créé, ou vous pouvez créer un nouveau réseau local. Toutefois, si vous ne sélectionnez pas d'utiliser un réseau local que vous avez précédemment créé, rendez-vous sur la page de configuration des **réseaux locaux** et vérifiez que l’exactitude de l’adresse IP du périphérique VPN (adresse IPv4 publique) pour le périphérique VPN que vous utilisez pour cette connexion.

## Connectivité de site à site
Si vous créez un nouveau réseau local, vous verrez la page **Connectivité site à site**. Si vous souhaitez utiliser un réseau local que vous avez précédemment créé, cette page n'apparaîtra pas dans l'Assistant ; vous pouvez alors passer à la section suivante.

Saisissez les informations ci-dessous puis cliquez sur la flèche Suivant.

- 	**Nom** : nom que vous souhaitez donner à votre site de réseau local.
- 	**Adresse IP du périphérique VPN** : adresse IPv4 de votre périphérique VPN local que vous utiliserez pour vous connecter à Azure. Le périphérique VPN ne peut pas se trouver derrière un traducteur d'adresses réseau.
- 	**Espace d’adressage** : comprend l’adresse IP de départ et le CIDR (nombre d’adresses). C’est ici que vous spécifiez la ou les plages d’adresses que vous voulez envoyer via la passerelle de réseau virtuel à l'emplacement de votre site local. Si une adresse IP de destination se situe dans les plages que vous spécifiez ici, elle sera routée via la passerelle de réseau virtuel.
- 	**Ajouter un espace d'adressage** : si vous avez plusieurs plages d'adresses que vous voulez envoyer via la passerelle de réseau virtuel, c’est ici que vous spécifiez chaque plage d'adresses supplémentaire. Vous pouvez ajouter ou supprimer des plages par la suite sur la page **Réseau local**.

## Espaces d’adressage du réseau virtuel
Indiquez la plage d’adresses que vous voulez utiliser pour votre réseau virtuel. Il s’agit des adresses IP dynamiques qui seront affectées aux machines virtuelles et aux autres instances de rôle que vous déployez dans ce réseau virtuel.

Il est particulièrement important de sélectionner une plage qui ne chevauche aucune des plages utilisées pour votre réseau local. Vous devrez vous coordonner avec votre administrateur réseau. Pour ce faire, votre administrateur réseau peut avoir besoin d’extraire une plage d’adresses IP de l’espace d’adressage de votre réseau local pour que vous puissiez l’utiliser pour votre réseau virtuel.

Entrez les informations suivantes, puis cliquez sur la coche située dans le coin inférieur droit pour configurer votre réseau.

- **Espace d’adressage** : inclut l’adresse IP de départ et le nombre d’adresses. Vérifiez que les espaces d’adressage que vous spécifiez ne chevauchent pas les espaces d’adressage de votre réseau local.
- **Ajouter un sous-réseau** : inclut l’adresse IP de départ et le nombre d’adresses. Des sous-réseaux supplémentaires ne sont pas requis, mais vous pouvez créer un sous-réseau distinct pour les machines virtuelles qui ont des adresses IP dédiées statiques. Vous pouvez également placer vos machines virtuelles dans un sous-réseau séparé de vos autres instances de rôle.
- **Ajouter un sous-réseau de passerelle** : cliquez sur cette option pour ajouter le sous-réseau de passerelle. Ce sous-réseau est uniquement utilisé pour la passerelle de réseau virtuel et est nécessaire pour cette configuration.

Cliquez sur la coche en bas de la page pour créer votre réseau virtuel. Une fois votre réseau virtuel créé, la mention **Créé** apparaît sous **État** sur la page **Réseaux** du Portail Azure Classic. Après avoir créé le réseau virtuel, vous pouvez ensuite configurer votre passerelle de réseau virtuel.

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## Configuration de votre passerelle de réseau virtuel

Vous devez ensuite configurer la passerelle du réseau virtuel pour créer une connexion site à site sécurisée. Consultez [Configurer une passerelle de réseau virtuel dans le Portail Azure Classic](vpn-gateway-configure-vpn-gateway-mp.md).

## Étapes suivantes

Pour plus d’informations sur les connexions entre locaux des réseaux virtuels, consultez l’article [À propos des connexions sécurisées entre locaux pour les réseaux virtuels](vpn-gateway-cross-premises-options.md).

Si vous souhaitez configurer une connexion VPN de point à site, consultez l’article [Configuration d’une connexion VPN de point à site](vpn-gateway-point-to-site-create.md).

Vous pouvez ajouter des machines virtuelles à votre réseau virtuel. Consultez l’article [Création d’une machine virtuelle personnalisée](../virtual-machines/virtual-machines-create-custom.md).

Si vous souhaitez configurer une connexion entre votre réseau virtuel classique et un réseau virtuel créé en mode Azure Resource Manager, consultez [Connexion de réseaux virtuels classiques à des réseaux virtuels Azure Resource Manager](../virtual-network/virtual-networks-arm-asm-s2s-howto.md).

<!---HONumber=AcomDC_1203_2015-->
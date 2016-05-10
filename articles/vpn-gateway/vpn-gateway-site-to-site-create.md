<properties
   pageTitle="Créez un réseau virtuel avec une connexion de passerelle VPN site à site à l’aide du Portail Azure Classic | Microsoft Azure"
   description="Créez un réseau virtuel avec une connexion passerelle VPN S2S de site à site pour les configurations entre locaux et hybrides à l’aide du modèle de déploiement classique."
   services="vpn-gateway"
   documentationCenter=""
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/02/2016"
   ms.author="cherylmc"/>

# Créer un réseau virtuel avec une connexion VPN site à site à l’aide du Portail Azure Classic

> [AZURE.SELECTOR]
- [Portail Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Portail Azure Classic](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)


Cet article vous guidera tout au long de la création d’un réseau virtuel et d’une connexion VPN site à site à votre réseau local. Les connexions site à site peuvent être utilisées pour les configurations hybrides et entre les différents locaux. Cet article s’applique au modèle de déploiement classique et s’appuie sur le Portail Azure Classic.

**À propos des modèles de déploiement Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]
 
![Diagramme de site à site](./media/vpn-gateway-site-to-site-create/site2site.png "site à site")

**Modèles de déploiement et outils pour les connexions de site à site**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]
 
Si vous souhaitez établir une connexion entre des réseaux virtuels sans créer de connexion à un emplacement local, consultez les articles [Configurer une connexion de réseau virtuel à réseau virtuel pour le modèle de déploiement classique](virtual-networks-configure-vnet-to-vnet-connection.md) ou [Configurer une connexion de réseau virtuel à réseau virtuel à l’aide d’Azure Resource Manager et de PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

 
## Avant de commencer

Vérifiez que vous disposez des éléments suivants avant de commencer la configuration.

- Un périphérique VPN compatible et une personne qui est en mesure de le configurer. Consultez [À propos des périphériques VPN](vpn-gateway-about-vpn-devices.md). Si vous n’êtes pas familiarisé avec la configuration de votre périphérique VPN ou avec les plages d’adresses IP situées dans la configuration de votre réseau local, vous devez consulter une personne qui peut fournir ces informations.

-  Une adresse IP publique exposée en externe pour votre périphérique VPN. Cette adresse IP ne peut pas se trouver derrière un NAT.

- Un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).


## Création de votre réseau virtuel

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com/).

2. Dans le coin inférieur gauche de l’écran, cliquez sur **Nouveau**. Dans le volet de navigation, cliquez sur **Services réseau**, puis sur **Réseau virtuel**. Cliquez sur **Custom Create** pour démarrer l'Assistant Configuration.

3. Remplissez les informations sur les pages suivantes pour créer votre réseau virtuel.

## Détails du réseau virtuel

Entrez les informations ci-après.

- **Nom** : nommez votre réseau virtuel. Par exemple, *NordFranceVNet*. Sachant que vous utiliserez ce nom de réseau virtuel au moment de déployer vos machines virtuelles et vos instances PaaS, vous préféreriez probablement avoir un nom qui n’est pas trop compliqué.
- **Emplacement** : l’emplacement est directement associé à l’emplacement physique (région) où vous souhaitez que vos ressources (machines virtuelles) résident. Par exemple, si les machines virtuelles que vous déployez dans ce réseau virtuel doivent être situées physiquement dans le *nord de la France*, sélectionnez cet emplacement. Après avoir créé votre réseau virtuel, vous ne pourrez plus modifier la région qui lui est associée.

## Serveurs DNS et connectivité VPN

Saisissez les informations ci-dessous, puis cliquez sur la flèche Suivant située en bas à droite.

- **Serveurs DNS** : entrez le nom et l’adresse IP du serveur DNS, ou sélectionnez un serveur DNS précédemment inscrit dans le menu contextuel. Ce paramètre ne crée pas de serveur DNS. Il vous permet de spécifier les serveurs DNS que vous souhaitez utiliser pour la résolution de noms pour ce réseau virtuel.
- **Configurer une connexion VPN site à site** : activez la case à cocher **Configuration d’un VPN de site à site**.
- **Réseau local** : un réseau local représente votre emplacement physique local. Vous pouvez sélectionner un réseau local que vous avez précédemment créé, ou vous pouvez créer un nouveau réseau local. Toutefois, si vous ne sélectionnez pas d'utiliser un réseau local que vous avez précédemment créé, rendez-vous sur la page de configuration des **réseaux locaux** et vérifiez que l’exactitude de l’adresse IP du périphérique VPN (adresse IPv4 publique) pour le périphérique VPN que vous utilisez pour cette connexion.

## Connectivité de site à site

Si vous créez un nouveau réseau local, vous verrez la page **Connectivité site à site**. Si vous souhaitez utiliser un réseau local que vous avez précédemment créé, cette page n'apparaîtra pas dans l'Assistant ; vous pouvez alors passer à la section suivante.

Saisissez les informations ci-dessous puis cliquez sur la flèche Suivant.

- 	**Nom** : nom que vous souhaitez donner à votre site de réseau local.
- 	**Adresse IP du périphérique VPN** : adresse IPv4 de votre périphérique VPN local que vous utiliserez pour vous connecter à Azure. Le périphérique VPN ne peut pas se trouver derrière un traducteur d'adresses réseau.
- 	**Espace d’adressage** : comprend l’adresse IP de départ et le CIDR (nombre d’adresses). C’est ici que vous spécifiez la ou les plages d’adresses que vous voulez envoyer via la passerelle de réseau virtuel à l’emplacement de votre site local. Si une adresse IP de destination se situe dans les plages que vous spécifiez ici, elle sera routée via la passerelle de réseau virtuel.
- 	**Ajouter un espace d’adressage** : si vous souhaitez envoyer plusieurs plages d’adresses via la passerelle de réseau virtuel, c’est ici que vous spécifiez chaque plage d’adresses supplémentaire. Vous pouvez ajouter ou supprimer des plages par la suite sur la page **Réseau local**.

## Espaces d’adressage du réseau virtuel

Indiquez la plage d’adresses que vous voulez utiliser pour votre réseau virtuel. Il s’agit des adresses IP dynamiques qui seront affectées aux machines virtuelles et aux autres instances de rôle que vous déployez dans ce réseau virtuel.

Il est particulièrement important de sélectionner une plage qui ne chevauche aucune des plages utilisées pour votre réseau local. Vous devrez vous coordonner avec votre administrateur réseau. Pour ce faire, votre administrateur réseau peut avoir besoin d’extraire une plage d’adresses IP de l’espace d’adressage de votre réseau local pour que vous puissiez l’utiliser pour votre réseau virtuel.

Entrez les informations suivantes, puis cliquez sur la coche située dans le coin inférieur droit pour configurer votre réseau.

- **Espace d’adressage** : inclut l’adresse IP de départ et le nombre d’adresses. Vérifiez que les espaces d’adressage que vous spécifiez ne chevauchent pas les espaces d’adressage de votre réseau local.
- **Ajouter un sous-réseau** : inclut l’adresse IP de départ et le nombre d’adresses. Des sous-réseaux supplémentaires ne sont pas requis, mais vous pouvez créer un sous-réseau distinct pour les machines virtuelles qui ont des adresses IP dédiées statiques. Vous pouvez également placer vos machines virtuelles dans un sous-réseau séparé de vos autres instances de rôle.
- **Ajouter un sous-réseau de passerelle** : cliquez sur cette option pour ajouter le sous-réseau de passerelle. Ce sous-réseau est uniquement utilisé pour la passerelle de réseau virtuel et est nécessaire pour cette configuration.

Cliquez sur la coche en bas de la page pour créer votre réseau virtuel. Une fois votre réseau virtuel créé, la mention **Créé** apparaît sous **État** sur la page **Réseaux** du Portail Azure Classic. Après avoir créé le réseau virtuel, vous pouvez ensuite configurer votre passerelle de réseau virtuel.

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## Configuration de votre passerelle de réseau virtuel

Vous devez ensuite configurer la passerelle du réseau virtuel pour créer une connexion site à site sécurisée. Consultez [Configurer une passerelle VPN pour le modèle de déploiement classique](vpn-gateway-configure-vpn-gateway-mp.md).

## Étapes suivantes

Une fois la connexion achevée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Pour plus d’informations, consultez la documentation relative aux [machines virtuelles](https://azure.microsoft.com/documentation/services/virtual-machines/).

<!---HONumber=AcomDC_0504_2016-->
<properties
   pageTitle="Configuration d’un réseau virtuel et d’une passerelle pour ExpressRoute | Microsoft Azure"
   description="Cet article vous guide pas à pas dans la configuration d’un réseau virtuel (VNet) pour ExpressRoute à l’aide du modèle de déploiement classique."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="03/08/2016"
   ms.author="cherylmc"/>

# Configurer un réseau virtuel pour ExpressRoute dans le portail Classic

Cet article vous explique pas à pas comment configurer un réseau virtuel et une passerelle à utiliser avec ExpressRoute à l’aide du modèle de déploiement classique et du portail Classic.

Si vous recherchez des instructions pour le modèle de déploiement de Resource Manager, vous pouvez utiliser les articles suivants pour vous guider pas à pas pour [créer un réseau virtuel à l'aide de PowerShell](../virtual-network/virtual-networks-create-vnet-arm-ps.md) et [ajouter une passerelle VPN à un réseau virtuel Resource Manager pour une configuration ExpressRoute](expressroute-howto-add-gateway-resource-manager.md).

**À propos des modèles de déploiement Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Pour configurer un réseau virtuel et la passerelle

1. Connectez-vous au [portail Azure Classic](http://manage.windowsazure.com).

2. Dans le coin inférieur gauche de l'écran, cliquez sur **Nouveau**. Dans le volet de navigation, cliquez sur **Services réseau**, puis sur **Réseau virtuel**. Cliquez sur **Custom Create** pour démarrer l'Assistant Configuration.

3. Dans la page **Détails du réseau virtuel**, entrez les informations suivantes.

	- **Nom** : nommez votre réseau virtuel. Sachant que vous utiliserez ce nom de réseau virtuel au moment de déployer vos machines virtuelles et vos instances PaaS, vous préféreriez probablement avoir un nom qui n’est pas trop compliqué.
	- **Emplacement** : l’emplacement est directement associé à l’emplacement physique (région) où vous souhaitez que vos ressources (machines virtuelles) résident. Par exemple, si les machines virtuelles que vous déployez dans ce réseau virtuel doivent être situées physiquement dans la région Est des États-Unis, sélectionnez cet emplacement. Après avoir créé votre réseau virtuel, vous ne pourrez plus modifier la région qui lui est associée.

4. Sur la page **Serveurs DNS et connectivité VPN**, entrez les informations suivantes, puis cliquez sur la flèche Suivant située dans le coin inférieur droit.

	- **Serveurs DNS** : entrez le nom et l’adresse IP du serveur DNS, ou sélectionnez un serveur DNS précédemment inscrit dans la liste déroulante. Ce paramètre ne crée pas de serveur DNS. Il vous permet de spécifier les serveurs DNS que vous souhaitez utiliser pour la résolution de noms pour ce réseau virtuel.
	- **Configurer une connexion VPN site à site** : activez la case à cocher **Configuration d’un VPN de site à site**.
	- **Sélectionner ExpressRoute** : cochez la case **Utiliser ExpressRoute**. Cette option apparaît uniquement si vous avez sélectionné ***Configurer un réseau VPN de site à site*** à l’étape précédente.
	- **Réseau local** : un réseau local représente votre emplacement physique local. Vous pouvez sélectionner un réseau local que vous avez précédemment créé, ou vous pouvez créer un nouveau réseau local.

	Si vous sélectionnez un réseau local existant, ignorez l’étape 5.

5. Si vous créez un nouveau réseau local, vous verrez la page **Connectivité site à site**. Si vous avez sélectionné un réseau local que vous avez précédemment créé, cette page n'apparaîtra pas dans l'Assistant ; vous pouvez alors passer à la section suivante. Pour configurer votre réseau local, entrez les informations ci-dessous, puis cliquez sur la flèche Suivant.

	- **Nom** : nom que vous souhaitez donner à votre site de réseau local.
	- **Espace d’adressage** : inclut l’adresse IP de départ et le nombre d’adresses (CIDR). Vous pouvez spécifier n’importe quelle plage d’adresses à condition qu’elle ne chevauche pas la plage d’adresses de votre réseau virtuel.
	- **Ajouter un espace d’adressage** : ce paramètre ne s’applique pas à ExpressRoute. **Remarque :** Vous êtes tenu de créer un site de réseau local pour ExpressRoute. Les préfixes d’adresse spécifiés pour le site de réseau local seront ignorés. Les préfixes d’adresse proposés à Microsoft via le circuit ExpressRoute seront utilisés pour le routage.

6. Sur la page **Virtual Network Address Spaces**, entrez les informations ci-dessous, puis cliquez sur la coche située dans le coin inférieur droit pour configurer votre réseau.

	- **Espace d’adressage** : inclut l’adresse IP de départ et le nombre d’adresses. Vérifiez que les espaces d’adressage que vous spécifiez ne chevauchent pas les espaces d’adressage de votre réseau local.
	- **Ajouter un sous-réseau** : inclut l’adresse IP de départ et le nombre d’adresses. Aucun sous-réseau supplémentaire n’est requis, mais vous pouvez créer un sous-réseau distinct pour les machines virtuelles qui disposeront d’adresses IP dynamiques (DIPS). Vous pouvez également placer vos machines virtuelles dans un sous-réseau séparé de vos autres instances PaaS.
	- **Ajouter un sous-réseau de passerelle** : cliquez sur cette option pour ajouter le sous-réseau de passerelle. Ce sous-réseau est uniquement utilisé pour la passerelle de réseau virtuel et est nécessaire pour cette configuration. ***Important :*** le sous-réseau de passerelle pour ExpressRoute doit avoir une taille minimale de /28. (/27, /26, etc.)

7. Cliquez sur la coche en bas de la page pour créer votre réseau virtuel. Une fois votre réseau virtuel créé, la mention **Créé** apparaît sous **État** sur la page **Réseaux** du Portail Azure Classic.

8. Dans la page **Réseau**, cliquez sur le réseau virtuel que vous venez de créer, puis sur **Tableau de bord**.
9. En bas de la page Tableau de bord, cliquez sur **CRÉER LA PASSERELLE**, puis sur **Oui**.

10. Lorsque la création de la passerelle démarre, un message s'affiche pour vous indiquer le démarrage de la passerelle. La création de la passerelle peut durer jusqu'à 15 minutes.

11. Liez votre réseau à un circuit. Suivez les instructions de l’article [Liaison de réseaux virtuels à des circuits ExpressRoute](expressroute-howto-linkvnet-classic.md).

## Étapes suivantes

- Si vous souhaitez ajouter des machines virtuelles à votre réseau virtuel, consultez [Parcours d’apprentissage de Virtual Machines](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/).
- Pour plus d'informations sur ExpressRoute, consultez la rubrique [ExpressRoute - Aperçu technique](expressroute-introduction.md).


 

<!------HONumber=AcomDC_0309_2016-->
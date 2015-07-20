<properties
   pageTitle="Configuration d’un réseau virtuel pour ExpressRoute | Microsoft Azure"
   description="Cet article vous guide dans la configuration d’un réseau virtuel (VNet) pour ExpressRoute"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="06/25/2015"
   ms.author="cherylmc"/>

#  Configurer un réseau virtuel pour ExpressRoute

1. Connectez-vous au **portail de gestion**.
2. Dans le coin inférieur gauche de l'écran, cliquez sur **Nouveau**. Dans le volet de navigation, cliquez sur **Services réseau**, puis sur **Réseau virtuel**. Cliquez sur **Custom Create** pour démarrer l'Assistant Configuration.
3. Dans la page **Détails du réseau virtuel**, entrez les informations suivantes. Pour plus d’informations sur les paramètres de la page de détails, consultez la page [Détails du réseau virtuel](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNetDetails).

	- **Nom** : nommez votre réseau virtuel. Sachant que vous utiliserez ce nom de réseau virtuel au moment de déployer vos machines virtuelles et vos instances PaaS, vous préféreriez probablement avoir un nom qui n’est pas trop compliqué.
	- **Emplacement** : l’emplacement est directement associé à l’emplacement physique (région) où vous souhaitez que vos ressources (machines virtuelles) résident. Par exemple, si les machines virtuelles que vous déployez dans ce réseau virtuel doivent être situées physiquement dans la région Est des États-Unis, sélectionnez cet emplacement. Après avoir créé votre réseau virtuel, vous ne pourrez plus modifier la région qui lui est associée.

4. Sur la page **Serveurs DNS et connectivité VPN**, entrez les informations suivantes, puis cliquez sur la flèche Suivant située dans le coin inférieur droit. Pour plus d’informations sur les paramètres de cette page, consultez la [page Serveurs DNS et connectivité VPN](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETDNS).

	- **Serveurs DNS** : entrez le nom et l’adresse IP du serveur DNS, ou sélectionnez un serveur DNS précédemment inscrit dans la liste déroulante. Ce paramètre ne crée pas de serveur DNS. Il vous permet de spécifier les serveurs DNS que vous souhaitez utiliser pour la résolution de noms pour ce réseau virtuel.
	- **Configurer une connexion VPN site à site** : activez la case à cocher **Configuration d’un VPN de site à site**.
	- **Sélectionner ExpressRoute** : cochez la case **Utiliser ExpressRoute**. Cette option apparaît uniquement si vous avez sélectionné ***Configurer un réseau VPN de site à site*** à l’étape précédente.
	- **Réseau local** : un réseau local représente votre emplacement physique local. Vous pouvez sélectionner un réseau local que vous avez précédemment créé, ou vous pouvez créer un nouveau réseau local.

	Si vous sélectionnez un réseau local existant, ignorez l’étape 5.

5. Si vous créez un nouveau réseau local, vous verrez la page **Connectivité site à site**. Si vous avez sélectionné un réseau local que vous avez précédemment créé, cette page n'apparaîtra pas dans l'Assistant ; vous pouvez alors passer à la section suivante. Pour configurer votre réseau local, entrez les informations ci-dessous, puis cliquez sur la flèche Suivant. Pour plus d’informations sur les paramètres de cette page, consultez la [page Connectivité de site à site](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETSITE).

	- **Nom** : nom que vous souhaitez donner à votre site de réseau local.
	- **Espace d’adressage** : inclut l’adresse IP de départ et le nombre d’adresses (CIDR). Vous pouvez spécifier n’importe quelle plage d’adresses à condition qu’elle ne chevauche pas la plage d’adresses de votre réseau virtuel.
	- **Ajouter un espace d’adressage** : ce paramètre ne s’applique pas à ExpressRoute.
	- 
**Remarque :** Vous êtes tenu de créer un site de réseau local pour ExpressRoute. Les préfixes d’adresse spécifiés pour le site de réseau local seront ignorés. Les préfixes d’adresse proposés à Microsoft via le circuit ExpressRoute seront utilisés pour le routage.

6. Sur la page **Virtual Network Address Spaces**, entrez les informations ci-dessous, puis cliquez sur la coche située dans le coin inférieur droit pour configurer votre réseau. L’espace d’adressage de réseau virtuel est régi par de nombreuses règles. Pour plus d’informations, consultez la rubrique section [Page Espace d’adresses du réseau virtuel](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNET_ADDRESS).

	- **Espace d’adressage** : inclut l’adresse IP de départ et le nombre d’adresses. Vérifiez que les espaces d’adressage que vous spécifiez ne chevauchent pas les espaces d’adressage de votre réseau local.
	- **Ajouter un sous-réseau** : inclut l’adresse IP de départ et le nombre d’adresses. Aucun sous-réseau supplémentaire n’est requis, mais vous pouvez créer un sous-réseau distinct pour les machines virtuelles qui disposeront d’adresses IP dynamiques (DIPS). Vous pouvez également placer vos machines virtuelles dans un sous-réseau séparé de vos autres instances PaaS.
	- **Ajouter un sous-réseau de passerelle** : cliquez sur cette option pour ajouter le sous-réseau de passerelle. Ce sous-réseau est uniquement utilisé pour la passerelle de réseau virtuel et est nécessaire pour cette configuration. ***Important :*** le sous-réseau de passerelle pour ExpressRoute doit avoir une taille minimale de /28.

7. Cliquez sur la coche en bas à droite de la page pour créer votre réseau virtuel. Après la création de votre réseau virtuel, le statut **Créé** apparaît sous **État** dans la page **Réseaux** du portail de gestion.

8. Dans la page **Réseau**, cliquez sur le réseau virtuel que vous venez de créer, puis sur **Tableau de bord**.
9. En bas de la page Tableau de bord, cliquez sur **CRÉER LA PASSERELLE**, puis sur **Oui**.

10. Lorsque la création de la passerelle démarre, un message s'affiche pour vous indiquer le démarrage de la passerelle. La création de la passerelle peut durer jusqu'à 15 minutes.
11. **Liez votre réseau à un circuit.** Suivez les instructions suivantes uniquement après avoir vérifié que votre circuit est passé à l'état et au statut suivants : 

	- ServiceProviderState: Provisioned
	- Status: Enabled

	Vérifiez que vous disposez d'au moins un réseau virtuel Azure avec une passerelle créée. Notez que le sous-réseau de la passerelle doit être d’au-moins /28 pour pouvoir fonctionner avec une connexion ExpressRoute et qu’il doit être opérationnel.

			PS C:> $Vnet = "MyTestVNet"
			New-AzureDedicatedCircuitLink -ServiceKey $ServiceKey -VNetName $Vnet
			Provisioned

## Étapes suivantes
Si vous souhaitez ajouter des machines virtuelles à votre réseau virtuel, consultez la rubrique [Création d’une machine virtuelle personnalisée](../virtual-machines-create-custom.md).

Pour plus d'informations sur ExpressRoute, consultez la rubrique [ExpressRoute - Aperçu technique](expressroute-introduction.md).

Si vous souhaitez en savoir plus sur les réseaux virtuels, consultez la [FAQ sur le réseau virtuel](https://msdn.microsoft.com/library/azure/dn133803.aspx).

 

<!---HONumber=July15_HO2-->
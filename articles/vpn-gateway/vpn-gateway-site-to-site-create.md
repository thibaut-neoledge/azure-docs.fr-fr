<properties
   pageTitle="Créer un réseau virtuel avec une connexion VPN de site à site en utilisant le portail de gestion | Microsoft Azure"
   description="Création d’un réseau virtuel avec une connexion VPN de site à site entre configurations entre sites et hybrides."
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
   ms.date="07/23/2015"
   ms.author="cherylmc"/>

# Créer un réseau virtuel avec une connexion VPN de site à site en utilisant le portail de gestion

> [AZURE.SELECTOR]
- [Azure Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)

Vous pouvez connecter votre emplacement local à un réseau virtuel en créant une connexion VPN de site à site. Cette procédure vous guide dans la création d'un réseau virtuel et d'une connexion VPN de site à site entre votre réseau virtuel nouvellement créé et votre emplacement local.

Azure propose actuellement deux modes de gestion : Azure Service Management et Azure Resource Manager (ARM). La configuration de site à site diffère selon le mode qui a été utilisé pour créer votre réseau virtuel. Ces instructions s’appliquent à la gestion des services Azure. Si au lieu de cela, vous voulez créer une connexion VPN de site à site Azure Resource Manager, consultez[Créer une connexion VPN de site à site à l’aide d’Azure Resource Manager et de PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md).


## Avant tout chose

- Vérifiez que le périphérique VPN que vous souhaitez utiliser satisfait les conditions requises pour créer une connexion de réseau virtuel intersite. Pour plus d'informations, consultez la page [À propos des périphériques VPN pour Virtual Network Connectivity](http://go.microsoft.com/fwlink/p/?LinkId=615099).

- Obtenez une adresse IP IPv4 externe pour votre périphérique VPN. Cette adresse IP est requise pour une configuration de site à site et est utilisée pour l'appareil VPN, qui ne peut pas se trouver derrière un NAT.

>[AZURE.IMPORTANT]Si vous n'êtes pas familiarisé avec la configuration de votre périphérique VPN ou avec les plages d'adresses IP situées sur la configuration de votre réseau local, vous devez vous coordonner avec une personne qui peut fournir les informations.

## Création de votre réseau virtuel

1. Connectez-vous au **portail de gestion**.

2. Dans le coin inférieur gauche de l'écran, cliquez sur **Nouveau**. Dans le volet de navigation, cliquez sur **Services réseau**, puis sur **Réseau virtuel**. Cliquez sur **Custom Create** pour démarrer l'Assistant Configuration.

3. Remplissez les informations sur les pages suivantes pour créer votre réseau virtuel.

## Détails du réseau virtuel

Saisissez les informations ci-dessous.

- **Nom** : nommez votre réseau virtuel. Par exemple, *NordFranceVNet*. Sachant que vous utiliserez ce nom de réseau virtuel au moment de déployer vos machines virtuelles et vos instances PaaS, vous préféreriez probablement avoir un nom qui n’est pas trop compliqué.
- **Emplacement** : l’emplacement est directement associé à l’emplacement physique (région) où vous souhaitez que vos ressources (machines virtuelles) résident. Par exemple, si les machines virtuelles que vous déployez dans ce réseau virtuel doivent être situées physiquement dans le *nord de la France*, sélectionnez cet emplacement. Après avoir créé votre réseau virtuel, vous ne pourrez plus modifier la région qui lui est associée.

## Serveurs DNS et connectivité VPN
Saisissez les informations ci-dessous, puis cliquez sur la flèche Suivant située en bas à droite.

- **Serveurs DNS** : entrez le nom et l’adresse IP du serveur DNS, ou sélectionnez un serveur DNS précédemment inscrit dans la liste déroulante. Ce paramètre ne crée pas de serveur DNS. Il vous permet de spécifier les serveurs DNS que vous souhaitez utiliser pour la résolution de noms pour ce réseau virtuel.
- **Configurer une connexion VPN site à site** : activez la case à cocher **Configuration d’un VPN de site à site**.
- **Réseau local** : un réseau local représente votre emplacement physique local. Vous pouvez sélectionner un réseau local que vous avez précédemment créé, ou vous pouvez créer un nouveau réseau local. Toutefois, si vous ne sélectionnez pas d'utiliser un réseau local que vous avez précédemment créé, rendez-vous sur la page de configuration des **réseaux locaux** et vérifiez que l’exactitude de l’adresse IP du périphérique VPN (adresse IPv4 publique) pour le périphérique VPN que vous utilisez pour cette connexion.

## Connectivité de site à site
Si vous créez un nouveau réseau local, vous verrez la page **Connectivité site à site**. Si vous souhaitez utiliser un réseau local que vous avez précédemment créé, cette page n'apparaîtra pas dans l'Assistant ; vous pouvez alors passer à la section suivante.

Saisissez les informations ci-dessous puis cliquez sur la flèche Suivant.

- 	**Nom** : nom que vous souhaitez donner à votre site de réseau local.
- 	**Adresse IP du périphérique VPN** : adresse IPv4 de votre périphérique VPN local que vous utiliserez pour vous connecter à Azure. Le périphérique VPN ne peut pas se trouver derrière un traducteur d'adresses réseau.
- 	**Espace d’adressage** : comprend l’adresse IP de départ et le CIDR (nombre d’adresses). C’est ici que vous spécifiez la ou les plages d’adresses que vous voulez envoyer via la passerelle de réseau virtuel à l'emplacement de votre site local. Si une adresse IP de destination se situe dans les plages que vous spécifiez ici, elle sera routée via la passerelle de réseau virtuel.
- 	**Ajouter un espace d'adressage** : si vous avez plusieurs plages d'adresses que vous voulez envoyer via la passerelle de réseau virtuel, c’est ici que vous spécifiez chaque plage d'adresses supplémentaire. Vous pouvez ajouter ou supprimer ultérieurement des plages sur la page Réseau local.

## Espaces d’adressage du réseau virtuel
Indiquez la plage d’adresses que vous voulez utiliser pour votre réseau virtuel. Il s’agit des adresses IP dynamiques qui seront affectées aux machines virtuelles et aux autres instances de rôle que vous déployez dans ce réseau virtuel.

Il est particulièrement important de sélectionner une plage qui ne chevauche aucune des plages utilisées pour votre réseau local. Vous devrez vous coordonner avec votre administrateur réseau. Pour ce faire, votre administrateur réseau peut avoir besoin d’extraire une plage d’adresses IP de l’espace d’adressage de votre réseau local pour que vous puissiez l’utiliser pour votre réseau virtuel.

Entrez les informations suivantes, puis cliquez sur la coche située dans le coin inférieur droit pour configurer votre réseau.

- **Espace d’adressage** : inclut l’adresse IP de départ et le nombre d’adresses. Vérifiez que les espaces d’adressage que vous spécifiez ne chevauchent pas les espaces d’adressage de votre réseau local.
- **Ajouter un sous-réseau** : inclut l’adresse IP de départ et le nombre d’adresses. Des sous-réseaux supplémentaires ne sont pas requis, mais vous pouvez créer un sous-réseau distinct pour les machines virtuelles qui ont des adresses IP dédiées statiques. Vous pouvez également placer vos machines virtuelles dans un sous-réseau séparé de vos autres instances de rôle.
- **Ajouter un sous-réseau de passerelle** : cliquez sur cette option pour ajouter le sous-réseau de passerelle. Ce sous-réseau est uniquement utilisé pour la passerelle de réseau virtuel et est nécessaire pour cette configuration.

Cliquez sur la coche en bas de la page pour créer votre réseau virtuel. Après la création de votre réseau virtuel, le statut **Créé** apparaît sous **État** dans la page **Réseaux** du portail de gestion. Après avoir créé le réseau virtuel, vous pouvez ensuite configurer votre passerelle de réseau virtuel.

## Configuration de votre passerelle de réseau virtuel

Vous devez ensuite configurer la **passerelle du réseau virtuel** pour créer une connexion site à site sécurisée. Consultez [Configuration d'une passerelle de réseau virtuel dans le portail de gestion](vpn-gateway-configure-vpn-gateway-mp.md).

## Étapes suivantes

Pour plus d’informations sur la connectivité de réseau virtuel intersite, consultez la rubrique [À propos de la connectivité intersite sécurisée de réseau virtuel](https://msdn.microsoft.com/library/azure/dn133798.aspx).

Si vous souhaitez configurer une connexion VPN de point à site, consultez la rubrique [Configurer une connexion VPN de point à site](vpn-gateway-point-to-site-create.md).

Vous pouvez ajouter des machines virtuelles à votre réseau virtuel. Pour plus d’informations, consultez la rubrique [Création d’une machine virtuelle personnalisée](../virtual-machines/virtual-machines-create-custom.md).

Si vous voulez configurer une connexion de réseau virtuel en utilisant RRAS, consultez [Configurer un VPN de site à site en utilisant le service Routage et accès à distance (RRAS) de Windows Server 2012](https://msdn.microsoft.com/library/dn636917.aspx).
 

<!---HONumber=July15_HO5-->
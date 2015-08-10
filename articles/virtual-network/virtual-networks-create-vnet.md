<properties 
   pageTitle="Création d’un réseau virtuel"
   description="En savoir plus sur la création d’un réseau virtuel"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/08/2015"
   ms.author="telmos" />

# Création d’un réseau virtuel

Quand vous créez un réseau virtuel, les services et les machines virtuelles au sein du réseau virtuel peuvent communiquer entre eux en toute sécurité sans passer par Internet. La création d’un réseau virtuel Azure est un processus relativement simple et rapide si le réseau virtuel n’est pas destiné à être connecté à d’autres réseaux virtuels ou à votre réseau local. En effet, vous n’aurez pas besoin d’acquérir et de configurer un périphérique VPN, ou de coordonner les adresses IP que vous choisissez avec d’autres réseaux virtuels ou le réseau local.

>[AZURE.WARNING]N’utilisez pas cette procédure pour créer un réseau virtuel qui sera ensuite connecté à d’autres réseaux virtuels ou votre réseau local. Si vous voulez créer une connexion hybride ou entre locaux sécurisée, consultez [À propos de la connectivité intersite sécurisée de réseau virtuel](https://msdn.microsoft.com/library/azure/dn133798.aspx). Si vous voulez créer un réseau virtuel qui se connecte à un autre réseau virtuel, consultez [Configuration d’une connexion de réseau virtuel à réseau virtuel](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

## Configuration de votre réseau virtuel

1. Connectez-vous au **portail de gestion Azure**.

1. Dans le coin inférieur gauche de l'écran, cliquez sur **Nouveau**. Dans le volet de navigation, cliquez sur **Services réseau**, puis sur **Réseau virtuel**. Cliquez sur **Custom Create** pour démarrer l'Assistant Configuration.

1. Dans la page **Détails du réseau virtuel**, entrez les informations suivantes :

	![Détails du réseau virtuel](./media/virtual-networks-create-vnet/IC736054.png)

	- **Nom** : nommez votre réseau virtuel. Par exemple, nous avons créé le nom *EastUSVNet*. Vous pouvez créer le nom que vous souhaitez. Vous utiliserez ce nom de réseau virtuel au moment de déployer vos machines virtuelles et vos services, il est donc préférable de ne pas choisir un nom trop compliqué.

	- **Emplacement** : sélectionnez l’emplacement (région) dans la liste déroulante. L’emplacement est directement associé à l’emplacement physique où vous souhaitez que vos ressources (machines virtuelles) résident quand vous les déployez sur ce réseau virtuel. Par exemple, si vous souhaitez que vos machines virtuelles soient physiquement situées dans la région *Ouest des États-Unis*, sélectionnez cette région. Une fois votre réseau virtuel créé, vous ne pouvez plus modifier la région qui lui est associée.

1. Dans la page **Serveurs DNS et connectivité VPN**, n’apportez aucune modification. Passez simplement à la page suivante en cliquant sur la flèche. Par défaut, Azure assure une résolution de noms de base pour votre réseau virtuel. Il est possible que vos besoins en matière de résolution de noms soient trop complexes au regard des capacités de traitement de la résolution de noms de base d’Azure. Dans ce cas, vous pouvez par la suite ajouter une machine virtuelle qui exécute DNS sur votre réseau virtuel. Pour plus d’informations sur la résolution de noms Azure et DNS, consultez [Résolution de noms (DNS)](https://msdn.microsoft.com/library/azure/jj156088.aspx).

1. Dans la page **Espaces d’adresses du réseau virtuel**, vous entrez l’espace d’adresses que vous voulez utiliser pour ce réseau virtuel. À moins que vous ayez besoin d’une certaine plage d’adresses IP internes pour vos machines virtuelles ou que vous vouliez créer un sous-réseau spécifique pour les machines virtuelles destinées à recevoir une adresse DIP statique, vous n’avez pas besoin d’apporter de modifications dans cette page. Si vous voulez créer plusieurs sous-réseaux, vous pouvez le faire dans cette page en cliquant sur **Ajouter un sous-réseau**.

	Informations supplémentaires :

	- Les plages de cette page contiennent les adresses IP internes dynamiques que vos machines virtuelles recevront lors de leur déploiement sur ce réseau virtuel. Ces adresses IP sont destinées à la communication au sein du réseau virtuel uniquement. Ce ne sont pas des adresses IP destinées aux points de terminaison Internet.

	- Sachant que vous n’allez pas connecter ce réseau privé virtuel à votre réseau local à l’aide d’une configuration VPN entre locaux, vous n’aurez pas à coordonner ces paramètres avec les plages d’adresses IP existantes de votre réseau local. Si vous pensez être amené par la suite à créer une configuration entre différents locaux, vous devez coordonner les espaces d’adresses maintenant avec les plages qui existent déjà sur votre site local pour éviter des problèmes de routage. La modification ultérieure des plages peut s’avérer un peu compliquée, surtout si les plages d’adresses se chevauchent.

	![Espace d’adressage](./media/virtual-networks-create-vnet/IC716778.png)

1. Cliquez sur la coche en bas à droite de la page **Espaces d’adresses du réseau virtuel** pour créer votre réseau virtuel. Après la création de votre réseau virtuel, l’état **Créé** apparaît sous **État** dans la page des **réseaux** du portail de gestion.

1. Une fois votre réseau virtuel créé, vous pouvez créer des machines virtuelles et des instances PaaS au sein du réseau virtuel. Assurez-vous de choisir **À partir de la galerie** au moment de créer une machine virtuelle, pour y sélectionner votre réseau virtuel. À noter que si vous avez déjà déployé des machines virtuelles et des instances PaaS existantes, il est tout simplement impossible de les déplacer vers votre nouveau réseau virtuel. Cela est dû au fait que les paramètres de configuration réseau dont ils ont besoin sont ajoutés pendant le déploiement.

## Ajout de machines virtuelles à votre réseau virtuel

Après avoir créé votre réseau virtuel, vous pouvez lui ajouter de nouvelles machines virtuelles. Il est important de créer d’abord le réseau virtuel, puis de déployer la machine virtuelle. Après le déploiement d’une machine virtuelle, vous ne pouvez pas la déplacer sur un réseau virtuel sans la redéployer. Si vous utilisez le portail de gestion pour créer vos machines virtuelles, l’interface permettant de déployer votre machine virtuelle sur un réseau virtuel n’est disponible que si vous sélectionnez **Nouveau/Calcul/Machine virtuelle/À partir de la galerie**. Quand vous parcourez l’Assistant pour créer votre machine virtuelle, dans la page **Configuration de la machine virtuelle**, **Région/Groupe d’affinités/Réseau virtuel** s’affiche. Dans la liste déroulante, sélectionnez le réseau virtuel déjà créé. Pour plus d’informations sur la création de machines virtuelles, consultez [Machines virtuelles Azure](../virtual-machines).

## Étapes suivantes

[Gestion des propriétés du réseau virtuel](../virtual-networks-settings)

[Gestion des serveurs DNS utilisés par un réseau virtuel](../virtual-networks-manage-dns-in-vnet)

[Utilisation d’adresses IP publiques dans un réseau virtuel](../virtual-networks-public-ip-within-vnet)

[Suppression d’un réseau virtuel](../virtual-networks-delete-vnet)
 

<!---HONumber=July15_HO5-->
<properties 
   pageTitle="Configurer une passerelle VPN dans le portail Azure Classic | Microsoft Azure"
   description="Cet article vous indique comment configurer votre passerelle VPN de réseau virtuel et comment modifier un type de routage de passerelle VPN, de statique à dynamique ou de dynamique à statique."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2016"
   ms.author="cherylmc" />

# Configurer une passerelle VPN pour le modèle de déploiement classique


Si vous voulez créer une connexion intersite sécurisée entre Azure et votre emplacement local, vous devrez configurer une passerelle VPN. Il existe différents types de passerelles VPN et le type de passerelle VPN que vous allez créer dépend à la fois de votre plan de conception de réseau et du périphérique VPN local que vous souhaitez utiliser.

Par exemple, certaines options de connectivité, telles qu’une connexion de point à site, nécessitent une passerelle de routage dynamique. Si vous souhaitez configurer votre passerelle pour prendre en charge les connexions de point à site (P2S) et une connexion de site à site (S2S), vous devrez configurer une passerelle de routage dynamique, même si le site à site peut être configuré avec un type de routage de passerelle.

De plus, vous devrez vous assurer que le périphérique que vous souhaitez utiliser pour votre connexion prendra en charge le type de VPN que vous souhaitez créer. Consultez [À propos des périphériques VPN](vpn-gateway-about-vpn-devices.md).


**À propos de cet article**

Cet article a été écrit pour le modèle de déploiement classique à l’aide du [portail Classic](https://manage.windowsazure.com) (et non du portail Azure). En présence d’un article disponible concernant le modèle de déploiement du Gestionnaire de ressources, nous ajouterons un lien ici.

**À propos des modèles de déploiement Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Présentation de la configuration

La procédure suivante vous guide lors de la configuration de votre passerelle VPN dans le portail Azure Classic. Ces étapes s’appliquent aux passerelles des réseaux virtuels créés à l’aide du mode de gestion des services. Elles sont visibles dans le portail Azure Classic. Elles ne s’appliquent pas à l’utilisation du portail en version préliminaire ou aux réseaux virtuels configurés à l’aide du mode Resource Manager. Des informations sur la création de passerelles de réseau virtuel pour les réseaux virtuels créés à l'aide du mode Resource Manager sont disponibles dans la section [Création d'un réseau virtuel avec une connexion de site à site à l'aide d'Azure Resource Manager et de PowerShell ](vpn-gateway-create-site-to-site-rm-powershell.md).


1. [Créer une passerelle VPN pour le réseau virtuel](#create-a-vpn-gateway)

1. [Collecter des informations pour la configuration de votre périphérique VPN](#gather-information-for-your-vpn-device-configuration)

1. [Configurer votre périphérique VPN](#configure-your-vpn-device)

1. [Vérifier vos plages de réseau local et l’adresse IP de la passerelle VPN](#verify-your-local-network-ranges-and-vpn-gateway-ip-address)

### Avant de commencer

Avant de configurer votre passerelle, vous devrez tout d’abord créer votre réseau virtuel. Pour connaître la procédure de création d’un réseau virtuel pour les connexions entre locaux, voir [Configuration d’un réseau virtuel avec une connexion VPN de site à site](vpn-gateway-site-to-site-create.md) ou [Configuration d’une connexion VPN de point à site à un réseau virtuel Azure](vpn-gateway-point-to-site-create.md). Suivez ensuite la procédure ci-dessous pour configurer la passerelle VPN et rassemblez les informations qui vous seront nécessaires pour configurer votre périphérique VPN.

Si vous disposez déjà d’une passerelle VPN et que vous souhaitez modifier le type de routage, consultez la section [Modification du type de routage de votre passerelle VPN](#how-to-change-your-vpn-gateway-type).

## Créer une passerelle VPN

1. Sur le [portail Azure Classic](https://manage.windowsazure.com), sur la page **Réseaux**, vérifiez que la colonne d’état de votre réseau virtuel a la valeur **Créé**.

1. Dans la colonne **Nom**, cliquez sur le nom de votre réseau virtuel.

1. Dans la page **Tableau de bord** , notez que pour ce réseau virtuel, aucune passerelle n’est encore configurée. Vous verrez cet état tout au long des étapes de configuration de votre passerelle.

![Passerelle non créée](./media/vpn-gateway-configure-vpn-gateway-mp/IC717025.png)


Cliquez ensuite sur **Créer une passerelle** en bas de la page. Vous pouvez sélectionner *Routage statique* ou *Routage dynamique*. Le type de routage que vous sélectionnez dépend de plusieurs facteurs. Par exemple, ce que votre périphérique VPN prend en charge et si vous devez prendre en charge des connexions de point à site. Pour vérifier le type de routage requis, voir la rubrique [À propos des périphériques VPN pour Virtual Network Connectivity](vpn-gateway-about-vpn-devices.md). Si vous souhaitez changer de type de routage de la passerelle VPN une fois celle-ci créée, il vous faudra la supprimer et en recréer une nouvelle. Lorsque le système vous demande de confirmer la création de la passerelle, cliquez sur **Oui**.

![Type de routage VPN de passerelle](./media/vpn-gateway-configure-vpn-gateway-mp/IC717026.png)

Lorsque la passerelle est en cours de création, notez que le graphique de la passerelle sur la page devient jaune et indique *Créer une passerelle*. La création de la passerelle peut durer jusqu’à 25 minutes. Vous devrez attendre que la passerelle soit créée avant de pouvoir utiliser d’autres paramètres de configuration.

![Création de passerelle](./media/vpn-gateway-configure-vpn-gateway-mp/IC717027.png)

Lorsque l’état de la passerelle passe en *Connexion en cours*, vous pouvez collecter les informations dont vous aurez besoin pour votre périphérique VPN.

![Connexion de passerelle](./media/vpn-gateway-configure-vpn-gateway-mp/IC717028.png)

## Collecter des informations pour la configuration de votre périphérique VPN

Une fois la passerelle créée, collectez les informations de configuration de votre périphérique VPN. Ces informations se trouvent sur la page **Tableau de bord** de votre réseau virtuel :

1. **Adresse IP de la passerelle -** l’adresse IP se trouve sur la page **Tableau de bord**. Vous ne pourrez le découvrir qu’après avoir créé votre passerelle.

1. **Clé partagée -** Cliquez sur le bouton **Gérer la clé** au bas de l’écran Cliquez sur l’icône située à côté de la clé pour la copier dans le Presse-papiers, puis collez et enregistrez la clé. Notez que ce bouton ne fonctionne qu’avec un tunnel VPN S2S unique. Si vous disposez de plusieurs tunnels VPN S2S, utilisez l’API de la clé partagée de la passerelle de réseau virtuel ou l’applet de commande PowerShell.

![Gérer la clé](./media/vpn-gateway-configure-vpn-gateway-mp/IC717029.png)


## Configuration de votre périphérique VPN

A l’issue de ces étapes, vous ou votre administrateur réseau devrez configurer le périphérique VPN pour créer la connexion. Pour plus d’informations sur les périphériques VPN, voir la rubrique [À propos des périphériques VPN pour Virtual Network Connectivity](vpn-gateway-about-vpn-devices.md).

Une fois le périphérique VPN configuré, vous pouvez afficher vos informations de connexion mises à jour sur la page Tableau de bord de votre réseau virtuel.

Vous pouvez également tester votre connexion en exécutant l’une des commandes suivantes :

| | Cisco ASA | Cisco ISR/ASR | Juniper SSG/ISG | Juniper SRX/J |
|----------------------|-----------------------|-----------------------|-----------------|------------------------------------------|
| **Vérification de toutes les associations de sécurité en mode principal** | show crypto isakmp sa | show crypto isakmp sa | get ike cookie | show security ike security-association |
| **Vérification des associations de sécurité en mode rapide** | show crypto ipsec sa | show crypto ipsec sa | get sa | show security ipsec security-association |


## Vérifier vos plages de réseau local et l’adresse IP de la passerelle VPN

### Vérifier votre adresse IP de passerelle VPN

Pour que la passerelle se connecte correctement, l’adresse IP de votre périphérique VPN doit être configurée pour le réseau local que vous avez spécifié pour votre configuration intersite. Elle est généralement configurée lors du processus de configuration de site à site. Toutefois, si vous avez déjà utilisé ce réseau local avec un autre périphérique ou que l’adresse IP a été modifiée pour ce réseau local, vous devrez modifier les paramètres pour spécifier l’adresse IP de la passerelle.

1. Pour vérifier l’adresse IP de votre passerelle, cliquez sur **Réseaux** dans le volet gauche du portail, puis sélectionnez **Réseaux locaux** en haut de la page. Vous pouvez afficher l’adresse de passerelle VPN pour chaque réseau local que vous avez créé. Pour modifier l'adresse IP, sélectionnez le réseau virtuel et cliquez sur **Modifier** en bas de la page.

1. Sur la page **Spécifier les détails de votre réseau local**, modifiez l'adresse IP, puis cliquez sur la flèche Suivant en bas de la page.

1. Dans la page **Spécifier l’espace d’adresses**, cliquez sur la coche en bas à droite pour enregistrer vos paramètres.

### Vérifier les plages d’adresses de vos réseaux locaux

Pour que le trafic transite de la passerelle à votre emplacement local, vous devrez vérifier que vous avez répertorié chaque plage d’adresses IP que vous souhaitez inclure dans votre configuration de réseau local. Selon la configuration réseau de votre emplacement local, cette tâche peut être complexe, car chaque plage doit être répertoriée dans la configuration de vos **réseaux locaux** Azure. Le trafic lié à une adresse IP qui est contenue dans les plages répertoriées est ensuite envoyé via la passerelle VPN du réseau virtuel. Les plages d’adresses IP que vous répertoriez ne doivent pas nécessairement être des plages privées, même si vous souhaitez vérifier que votre configuration locale est en mesure de recevoir le trafic entrant.

Pour ajouter ou modifier les plages d’un réseau local, suivez la procédure ci-dessous.

1. Pour modifier les plages d’adresses IP d’un réseau local, cliquez sur **Réseaux** dans le volet gauche du portail, puis sélectionnez **Réseaux locaux** en haut de la page. Pour découvrir la meilleure façon d’afficher les plages que vous avez répertoriées, voir la page **Modifier**. Pour modifier vos plages, sélectionnez le réseau virtuel et cliquez sur **Modifier** en bas de la page.

1. Sur la page **Spécifier les détails de votre réseau local**, n’effectuez aucune modification. Cliquez sur la flèche Suivant en bas à droite de la page.

1. Sur la page **Spécifier l’espace d’adressage**, modifiez votre espace d’adressage de réseau. Cliquez ensuite sur la coche pour enregistrer votre configuration.

## Affichage du trafic de la passerelle

Vous pouvez afficher votre passerelle et le trafic de la passerelle depuis la page **Tableau de bord** de votre réseau virtuel.

Sur la page **Tableau de bord**, vous pouvez afficher les informations suivantes :

- La quantité de données qui transitent par votre passerelle (données entrantes et sortantes).

- Les noms des serveurs DNS qui sont spécifiés pour votre réseau virtuel.

- La connexion entre votre passerelle et votre périphérique VPN.

- La clé partagée qui est utilisée pour configurer la connexion de votre passerelle sur votre périphérique VPN.


## Modification du type de routage VPN de votre passerelle

Étant donné que certaines configurations de connectivité ne sont disponibles que pour certains types de routage de passerelle, vous serez peut-être amené à modifier le type de routage VPN d’une passerelle VPN existante. Vous souhaiterez par exemple ajouter une connectivité de point à site à une connexion de site à site déjà existante, qui dispose d’une passerelle statique. La connexion de point à site nécessite une passerelle dynamique, ce qui signifie que, pour la configurer, vous devrez modifier le type de routage VPN de votre passerelle, pour passer d’une passerelle statique à une passerelle dynamique.

Si vous avez besoin de modifier un type de routage VPN de passerelle, vous supprimerez la passerelle existante et vous la recréerez ensuite avec le nouveau type de routage. Vous n’avez pas besoin de supprimer le réseau virtuel entier pour modifier le type de routage de passerelle.

Avant de modifier le type VPN de votre passerelle, vérifiez que votre périphérique VPN prend en charge le type de routage que vous voulez utiliser. Pour télécharger de nouveaux exemples de configuration de routage et vérifier les spécifications de périphérique VPN, consultez la rubrique [À propos des périphériques VPN pour Virtual Network Connectivity](vpn-gateway-about-vpn-devices.md).

>[AZURE.IMPORTANT] Lorsque vous supprimez une passerelle VPN de réseau virtuel, l’adresse IP virtuelle attribuée à la passerelle est de nouveau disponible. Lorsque vous recréez la passerelle, une nouvelle adresse VIP lui est attribuée.

1. **Suppression de la passerelle VPN existante.**

	Sur la page **Tableau de bord** de votre réseau virtuel, accédez au bas de la page et cliquez sur **Supprimer une passerelle**. Patientez jusqu’à ce qu’une notification vous indique que la passerelle a été supprimée. Vous pourrez ensuite créer une nouvelle passerelle après avoir reçu la notification vous indiquant que votre passerelle a bien été supprimée.

1. **Création d'une passerelle VPN.**

	Suivez la procédure mentionnée en haut de la page pour créer une nouvelle passerelle : [Création d’une passerelle VPN](#create-a-vpn-gateway).


## Étapes suivantes

Pour en savoir plus sur les connexions entre locaux de réseau virtuel, voir l’article [À propos des connexions sécurisées entre locaux pour les réseaux virtuels](vpn-gateway-cross-premises-options.md).

Vous pouvez ajouter des machines virtuelles à votre réseau virtuel. Voir [Création d’une machine virtuelle personnalisée](../virtual-machines/virtual-machines-create-custom.md).

Si vous souhaitez configurer une connexion VPN de point à site, voir l’article [Configuration d’une connexion VPN de point à site](vpn-gateway-point-to-site-create.md).

 

<!---HONumber=AcomDC_0224_2016-->
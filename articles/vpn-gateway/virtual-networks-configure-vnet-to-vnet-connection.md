<properties
   pageTitle="Configurer une connexion de réseau virtuel à réseau virtuel pour le modèle de déploiement classique | Microsoft Azure"
   description="Comment interconnecter des réseaux virtuels Azure à l’aide de PowerShell et du portail Azure Classic."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/22/2016"
   ms.author="cherylmc"/>


# Configurer une connexion de réseau virtuel à réseau virtuel pour le modèle de déploiement classique

> [AZURE.SELECTOR]
- [Portail Azure Classic](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell - Azure Resource Manager](vpn-gateway-vnet-vnet-rm-ps.md)


Cet article vous guide tout au long de la procédure de création et de connexion de plusieurs réseaux virtuels à l’aide du modèle de déploiement classique (également appelé Service Management). Les étapes suivantes utilisent le portail Azure Classic pour créer des réseaux virtuels et des passerelles, et PowerShell pour configurer la connexion de réseau virtuel à réseau virtuel. Vous ne pouvez pas configurer la connexion dans le portail.

![Schéma de connectivité de réseau virtuel à réseau virtuel](./media/virtual-networks-configure-vnet-to-vnet-connection/v2vclassic.png)


### Outils et modèles de déploiement pour les connexions de réseau virtuel à réseau virtuel


[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Une connexion de réseau virtuel à réseau virtuel peut être configurée dans les deux modèles de déploiement et à l’aide de différents outils. Pour plus d’informations, consultez le tableau suivant. Nous mettons à jour ce tableau à mesure que de nouveaux articles, de nouveaux modèles de déploiement et des outils supplémentaires sont disponibles pour cette configuration. Quand un article est disponible, ce tableau contient un lien vers celui-ci.

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## À propos des connexions de réseau virtuel à réseau virtuel

La connexion entre deux réseaux virtuels est similaire à la connexion d’un réseau virtuel à un emplacement de site local. Les deux types de connectivité font appel à une passerelle VPN pour offrir un tunnel sécurisé utilisant Ipsec/IKE.

Les réseaux virtuels que vous connectez peuvent être situés dans différents abonnements et différentes régions. Vous pouvez combiner une communication de réseau virtuel à réseau virtuel avec des configurations multisites. Vous établissez ainsi des topologies réseau qui combinent une connectivité entre différents locaux et une connectivité entre différents réseaux virtuels.


### Pourquoi connecter des réseaux virtuels ?

Vous pouvez décider de connecter des réseaux virtuels pour les raisons suivantes :

- **Géo-redondance et présence géographique dans plusieurs régions**
	- Vous pouvez configurer la géo-réplication ou la synchronisation avec une connectivité sécurisée sans passer par les points de terminaison accessibles sur Internet.
	- Avec l’Azure Load Balancer et les technologies de clustering Microsoft ou tierces, vous pouvez configurer une charge de travail hautement disponible avec la géoredondance dans plusieurs régions Azure. Vous pouvez par exemple configurer SQL Always On avec des groupes de disponibilité répartis dans différentes régions Azure.

- **Applications multiniveaux régionales avec une forte limite d’isolement**
	- Dans la même région, vous pouvez configurer des applications multiniveaux avec plusieurs réseaux virtuels interconnectés avec une isolation renforcée et une communication sécurisée entre les niveaux.

- **Communication interorganisationnelle entre plusieurs abonnements dans Azure**
	- Si vous avez plusieurs abonnements Azure, vous pouvez désormais interconnecter des charges de travail de différents abonnements en toute sécurité entre des réseaux virtuels.
	- Pour les entreprises ou prestataires de services, il est désormais possible d’activer la communication interorganisationnelle avec une technologie VPN sécurisée au sein d’Azure.

### Forum Aux Questions sur l’interconnexion de réseaux virtuels pour les réseaux virtuels classiques

- Les réseaux virtuels peuvent être situés dans des abonnements identiques ou différents.

- Les réseaux virtuels peuvent être situés dans des régions (emplacements) identiques ou différentes.

- Un service cloud ou un point de terminaison d’équilibrage de charge ne peut pas s’étendre sur différents réseaux virtuels, même si ces derniers sont interconnectés.

- L’interconnexion de plusieurs réseaux virtuels ne nécessite pas d’appareils VPN.

- La connexion de réseau virtuel à réseau virtuel prend en charge la connexion de réseaux virtuels Azure. Elle ne prend pas en charge la connexion de machines virtuelles ou de services cloud qui ne sont pas déployés dans un réseau virtuel.

- La connectivité de réseau virtuel à réseau virtuel nécessite des passerelles de routage dynamiques. Les passerelles de routage statique Azure ne sont pas prises en charge.

- Une connectivité réseau virtuelle peut être utilisée en même temps que des VPN multisites. Il peut y avoir au maximum 10 tunnels VPN par passerelle VPN de réseau virtuel se connectant à d’autres réseaux virtuels ou à des sites locaux.

- Les espaces d’adressage des réseaux virtuels et sur les sites de réseau locaux ne doivent pas se chevaucher. Le chevauchement des espaces d’adressage entraîne l’échec de la création de réseaux virtuels ou du téléchargement de fichiers de configuration netcfg.

- Les tunnels redondants entre deux réseaux virtuels ne sont pas pris en charge.

- Tous les tunnels VPN du réseau virtuel, y compris les VPN de point à site, partagent la bande passante disponible pour la passerelle VPN, ainsi que le même contrat SLA concernant le temps d’activité des passerelles VPN dans Azure.

- Le trafic de réseau virtuel à réseau virtuel circule sur la dorsale principale d’Azure.


## <a name="step1"></a>Étape 1 : planifier vos plages d’adresses IP

Il est important de choisir les plages que vous utiliserez pour configurer vos réseaux virtuels. Pour cette configuration, vous devez vous assurer que vos plages de réseau virtuel ne se chevauchent pas entre elles ou avec un réseau local auquel elles se connectent.

Le tableau suivant présente un exemple de la définition de vos réseaux virtuels. Utilisez les plages comme indication uniquement. Consignez les plages pour vos réseaux virtuels. Vous aurez besoin de ces informations pour les étapes ultérieures.

**Exemples de paramètres**

|Réseau virtuel |Espace d'adressage |Région |Se connecte au site de réseau local|
|:----------------|:---------------------------|:-----------|:-----------------------------|
|VNet1 |VNet1 (10.1.0.0/16) |Ouest des États-Unis |VNet2Local (10.2.0.0/16) |
|VNet2 |VNet2 (10.2.0.0/16) |Est du Japon |VNet1Local (10.1.0.0/16) |
  
## Étape 2 : créez VNet1

Au cours de cette étape, nous créons VNet1. Lorsque vous utilisez l’un des exemples, veillez à utiliser vos propres valeurs. Si votre réseau virtuel existe déjà, vous n’avez pas besoin d’exécuter cette étape. Mais vous devez vérifier que les plages d’adresses IP ne se chevauchent pas avec les plages de votre second réseau virtuel ou avec tout autre réseau virtuel auquel vous souhaitez vous connecter.

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com). Dans cet article, nous utilisons le portail classique, car certains paramètres de configuration requis ne sont pas encore disponibles dans le portail Azure.

2. En bas à gauche de l’écran, cliquez sur **Nouveau** > **Services réseau** > **Réseau virtuel** > **Création personnalisée** pour lancer l’Assistant Configuration. Lorsque vous naviguez dans l’Assistant, ajoutez les valeurs spécifiées à chaque page.

### Détails du réseau virtuel

Dans la page Détails du réseau virtuel, entrez les informations suivantes :

  ![Détails du réseau virtuel](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736055.png)

  - **Nom** : nommez votre réseau virtuel. Par exemple : VNet1
  - **Emplacement** : lorsque vous créez un réseau virtuel, vous l’associez à un emplacement Azure (région). Par exemple, si vous souhaitez que vos machines virtuelles déployées sur votre réseau virtuel soient physiquement situées dans la région Ouest des États-Unis, sélectionnez cet emplacement. Vous ne pouvez pas modifier l’emplacement associé à votre réseau virtuel après sa création.

### Serveurs DNS et connectivité VPN

Sur la page Serveurs DNS et connectivité VPN, entrez les informations suivantes, puis cliquez sur la flèche Suivant située dans le coin inférieur droit.

  ![Serveurs DNS et connectivité VPN](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736056.jpg)

- **Serveurs DNS** : entrez le nom et l’adresse IP du serveur DNS, ou sélectionnez un serveur DNS précédemment inscrit dans la liste déroulante. Ce paramètre n'entraîne pas la création de serveur DNS. Il vous permet de spécifier le serveur DNS que vous souhaitez utiliser pour la résolution de noms pour ce réseau virtuel. Si vous souhaitez disposer de la résolution de noms entre vos réseaux virtuels, vous devez configurer votre propre serveur DNS au lieu d’utiliser la résolution de noms fournie par Azure.
- N’activez aucune case pour la connectivité P2S ou S2S. Cliquez sur la flèche située en bas à droite pour passer à l’écran suivant.

### Espaces d’adressage du réseau virtuel

Dans la page Espaces d’adresses du réseau virtuel, indiquez la plage d’adresses que vous voulez utiliser pour votre réseau virtuel. Il s’agit des adresses IP dynamiques qui seront affectées aux machines virtuelles et aux autres instances de rôle que vous déployez dans ce réseau virtuel.

Si vous créez un réseau virtuel qui aura également une connexion à votre réseau local, il est particulièrement important de sélectionner une plage qui ne se chevauche pas avec les plages qui sont utilisées pour votre réseau local. Dans ce cas, vous devez vous coordonner avec votre administrateur réseau. Pour ce faire, votre administrateur réseau peut avoir besoin d’extraire une plage d’adresses IP de l’espace d’adressage de votre réseau local pour que vous puissiez l’utiliser pour votre réseau virtuel.

  ![Espaces d’adressage du réseau virtuel](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736057.jpg)

  - **Espace d’adressage** : inclut l’adresse IP de départ et le nombre d’adresses. Vérifiez que les espaces d’adressage que vous spécifiez ne se chevauchent pas avec les espaces d’adressage de votre réseau local. Pour cet exemple, nous allons utiliser 10.1.0.0/16 pour VNet1.
  - **Ajouter un sous-réseau** : inclut l’adresse IP de départ et le nombre d’adresses. Des sous-réseaux supplémentaires ne sont pas requis, mais vous pouvez créer un sous-réseau distinct pour les machines virtuelles qui ont des adresses IP dédiées statiques. Vous pouvez également placer vos machines virtuelles dans un sous-réseau séparé de vos autres instances de rôle.
 
**Cliquez sur la coche** en bas à droite de la page pour créer votre réseau virtuel. Une fois votre réseau virtuel créé, la mention « Créé » apparaît sous État dans la page Réseaux.

## Étape 3 : créez VNet2

Ensuite, répétez la procédure précédente pour créer un autre réseau virtuel. Dans les étapes suivantes, vous allez vous connecter les deux réseaux virtuels. Vous pouvez vous référer aux [exemples de paramètres](#step1) fournis à l’étape 1. Si votre réseau virtuel existe déjà, vous n’avez pas besoin d’exécuter cette étape. Toutefois, vous devez vérifier que les plages d’adresses IP ne se chevauchent pas avec l’un des autres réseaux virtuels ou réseaux locaux auquel vous souhaitez vous connecter.

## Étape 4 : ajoutez les sites de réseau local

Lorsque vous créez une configuration de réseau virtuel à réseau virtuel, vous devez configurer les sites de réseau local répertoriés dans la page **Réseaux locaux** du portail. Azure utilise les paramètres spécifiés dans chaque site de réseau local pour déterminer comment router le trafic entre les réseaux virtuels. Vous déterminez le nom à utiliser pour faire référence à chaque site de réseau local. Il est préférable d’utiliser un nom descriptif, car vous sélectionnerez la valeur dans une liste déroulante lors des étapes ultérieures.

Par exemple, VNet1 se connecte à un site de réseau local que vous créez, nommé « VNet2Local ». Les paramètres pour VNet2Local contiennent les préfixes d’adresse pour VNet2, ainsi qu’une adresse IP publique pour la passerelle VNet2. VNet2 se connecte à un site de réseau local nommé « VNet1Local » que vous créez, qui contient les préfixes d’adresse pour VNet1 et l’adresse IP publique de la passerelle VNet1.

### <a name="localnet"></a>Ajoutez le site de réseau local VNet1Local

1. En bas à gauche de l’écran, cliquez sur **Nouveau** > **Services réseau** > **Réseau virtuel** > **Ajouter un réseau local**.

2. Dans la page **Spécifier les détails de votre réseau local**, pour **Nom**, spécifiez le nom à utiliser pour représenter le réseau auquel vous souhaitez vous connecter. Dans cet exemple, vous pouvez utiliser « VNet1Local » pour désigner les plages d’adresses IP et la passerelle pour VNet1.

3. Sous **Adresse IP de l’appareil VPN (facultatif)**, saisissez une adresse IP publique valide. En général, vous utilisez la véritable adresse IP externe d’un périphérique VPN. Pour les configurations de réseau virtuel à réseau virtuel, vous allez utiliser l’adresse IP publique affectée à la passerelle de votre réseau virtuel. Toutefois, étant donné que vous n’avez pas encore créé la passerelle, spécifiez une adresse IP publique valide en tant qu’espace réservé. Ne laissez pas ce champ vide. Il n’est pas facultatif pour cette configuration. Lors d’une étape ultérieure, vous reviendrez à ces paramètres et les configurerez avec les adresses IP de passerelle correspondantes une fois celles-ci générées par Azure. Cliquez sur la flèche pour passer à l’écran suivant.

4. Dans la page **Spécifier l’adresse**, indiquez la plage d’adresses IP et le nombre d’adresses pour VNet1. Ces valeurs doivent correspondre exactement à la plage configurée pour VNet1. Azure utilise les plages d’adresses IP que vous spécifiez pour router le trafic destiné à VNet1. Activez la coche pour créer le réseau local.

### Ajoutez le site de réseau local VNet2Local

Suivez les étapes ci-dessus pour créer le site de réseau local « VNet2Local ». Le cas échéant, vous pouvez vous référer aux valeurs des [exemples de paramètres](#step1) indiqués à l’étape 1.

### Configurez chaque réseau virtuel de manière à ce qu’il pointe vers un réseau local

Chaque réseau virtuel doit pointer vers le réseau local vers lequel vous souhaitez acheminer le trafic.

#### Pour VNet1

1. Accédez à la page **Configurer** du réseau virtuel **VNet1**.
2. Sous « Connectivité de site à site », sélectionnez « Se connecter au réseau local », puis sélectionnez **VNet2Local** comme réseau local dans la liste déroulante.
3. Enregistrez vos paramètres.

#### Pour VNet2

1. Accédez à la page **Configurer** du réseau virtuel **VNet2**.
2. Sous « Connectivité de site à site », sélectionnez « Se connecter au réseau local », puis sélectionnez **VNet1Local** dans la liste déroulante comme réseau local.
3. Enregistrez vos paramètres.

## Étape 5 - configurez une passerelle pour chaque réseau virtuel

Configurez une passerelle de routage dynamique pour chaque réseau virtuel. Cette configuration ne prend pas en charge les passerelles de routage statique. Si vous utilisez des réseaux virtuels précédemment configurés qui ont déjà des passerelles de routage dynamique, vous n’avez pas besoin d’exécuter cette étape. Si vos passerelles sont de type Routage statique, vous devez les supprimer et les recréer en tant que passerelles de routage dynamique. Si vous supprimez une passerelle, l’adresse IP publique qui lui est affectée est libérée. Vous devez reconfigurer vos réseaux locaux et périphériques VPN avec la nouvelle adresse IP publique pour la nouvelle passerelle.

1. Dans la page **Réseaux**, vérifiez que la colonne d’état de votre réseau virtuel est définie sur **Créé**.

2. Dans la colonne **Nom**, cliquez sur le nom de votre réseau virtuel. Pour cet exemple, nous allons utiliser « VNet1 ».

3. Dans la page **Tableau de bord** , notez que pour ce réseau virtuel, aucune passerelle n’est encore configurée. Vous verrez cet état évoluer tout au long des étapes de configuration de votre passerelle.

4. En bas de la page, cliquez sur **Créer une passerelle**, puis sur **Routage dynamique**. Lorsque le système vous demande de confirmer la création de la passerelle, cliquez sur Oui.

  	![Type de passerelle](./media/virtual-networks-configure-vnet-to-vnet-connection/IC717026.png)

5. Lorsque la passerelle est en cours de création, notez que le graphique de la passerelle sur la page devient jaune et indique « Création d’une passerelle ». La création d'une passerelle dure généralement environ 30 minutes.

6. Répétez ces étapes pour VNet2. Vous n’avez pas besoin de terminer la première passerelle de réseau virtuel avant de commencer à créer la passerelle de l’autre réseau virtuel.

7. Quand l’état de la passerelle est « Connexion en cours », l’adresse IP publique de chaque passerelle est visible dans le Tableau de bord. Notez l'adresse IP qui correspond à chaque réseau virtuel, en prenant soin de ne pas les mélanger. Il s’agit des adresses IP utilisées pour modifier vos adresses IP d’espaces réservés pour le périphérique VPN pour chaque réseau local.

## Étape 6 : modifiez le réseau local

1. Dans la page **Réseaux locaux**, cliquez sur le nom du réseau local à modifier, puis cliquez sur **Modifier** en bas de la page. Pour **Adresse IP du périphérique VPN**, entrez l’adresse IP de la passerelle qui correspond au réseau virtuel. Par exemple, pour VNet1Local, indiquez l’adresse IP de passerelle affectée à VNet1. Cliquez ensuite sur la flèche en bas de la page.

2. Dans la page **Spécifier l’espace d’adresses**, cliquez sur la coche en bas à droite sans apporter de modifications.

## Étape 7 - créez la connexion VPN

Lorsque vous avez terminé les étapes précédentes, définissez les clés prépartagées IPsec/IKE et créez la connexion. Cet ensemble d’étapes utilise PowerShell et ne peut pas être configuré dans le portail. Pour plus d’informations sur l’installation des applets de commande Azure PowerShell, voir [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md). Veillez à télécharger la dernière version des applets de commande de gestion des services.

1. Ouvrez Windows PowerShell et connectez-vous.

		Add-AzureAccount

2. Sélectionnez l’abonnement dans lequel vos réseaux virtuels résident.

		Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
		Select-AzureSubscription -SubscriptionName "<Subscription Name>"

3. Créez les connexions. Dans les exemples ci-dessous, notez que la clé partagée est exactement identique. La clé partagée doit toujours correspondre.


	Connexion VNet1 à VNet2

		Set-AzureVNetGatewayKey -VNetName VNet1 -LocalNetworkSiteName VNet2Local -SharedKey A1b2C3D4

	Connexion VNet2 à VNet1

		Set-AzureVNetGatewayKey -VNetName VNet2 -LocalNetworkSiteName VNet1Local -SharedKey A1b2C3D4

4. Attendez l’initialisation des connexions. Une fois la passerelle initialisée, elle ressemble à l’illustration suivante.

	![État de la passerelle - Connectée](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736059.jpg)

	[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

## Étapes suivantes

Vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Pour plus d’informations, consultez la [documentation relative aux machines virtuelles](https://azure.microsoft.com/documentation/services/virtual-machines/).



[1]: ../hdinsight-hbase-geo-replication-configure-vnets.md
[2]: http://channel9.msdn.com/Series/Getting-started-with-Windows-Azure-HDInsight-Service/Configure-the-VPN-connectivity-between-two-Azure-virtual-networks
 

<!---HONumber=AcomDC_0824_2016-->
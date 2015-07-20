<properties 
	pageTitle="Phase 1 de la charge de travail de la batterie de serveurs SharePoint intranet : configuration d'Azure" 
	description="Au cours de cette première phase de déploiement d'une batterie de serveurs SharePoint 2013 intranet avec des groupes de disponibilité SQL Server AlwaysOn dans des services d'infrastructure Azure, vous créez le réseau virtuel Azure et d'autres éléments d'infrastructure Azure." 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="josephd"/>

# Phase 1 de la charge de travail de la batterie de serveurs SharePoint intranet : configuration d'Azure

Au cours de cette phase de déploiement d'une batterie de serveurs SharePoint 2013 intranet avec des groupes de disponibilité SQL Server AlwaysOn dans des services d'infrastructure Azure, vous créez l'infrastructure de réseau et de stockage Azure. Vous devez terminer cette opération avant de passer à la [Phase 2](virtual-machines-workload-intranet-sharepoint-phase2.md). Consultez [Déploiement de SharePoint avec des groupes de disponibilité SQL Server AlwaysOn dans Azure](virtual-machines-workload-intranet-sharepoint-overview.md) pour prendre connaissance de toutes les phases.

Azure doit être configuré avec les composants réseau de base suivants :

- un réseau virtuel intersite avec un sous-réseau ;
- trois services cloud Azure ;
- Un compte de stockage Azure pour stocker des images de disque VHD et des disques de données supplémentaires

## Avant de commencer

Avant de commencer la configuration de composants Azure, remplissez les tables suivantes. Pour vous aider dans la procédure de configuration d'Azure, imprimez cette section et écrivez les informations nécessaires ou copiez cette section dans un document et remplissez-la.

Pour les paramètres du réseau virtuel (VNet), remplissez la table V.

Élément | Élément de configuration | Description | Valeur 
--- | --- | --- | --- 
1. | Nom du réseau virtuel | Le nom à attribuer au réseau virtuel Azure (par exemple, SPFarmNet). | __________________
2. | Emplacement du réseau virtuel | Le centre de données Azure qui contiendra le réseau virtuel. | __________________
3. | Nom du réseau local | Un nom à attribuer au réseau de votre organisation. | __________________
4. | Adresse IP du périphérique VPN | L'adresse IPv4 publique de l'interface de votre périphérique VPN sur Internet. Consultez votre service informatique pour la déterminer. | __________________
5. | Espace d'adressage du réseau virtuel | L'espace d'adressage (défini dans un préfixe d'adresse privé unique) pour le réseau virtuel. Consultez votre service informatique pour le déterminer. | __________________
6. | Premier serveur DNS final | La quatrième adresse IP possible pour l'espace d'adressage du sous-réseau du réseau virtuel (voir la table S). Consultez votre service informatique pour déterminer ces adresses. | __________________
7. | Second serveur DNS final | La cinquième adresse IP possible pour l'espace d'adressage du sous-réseau du réseau virtuel (voir la table S). Consultez votre service informatique pour déterminer ces adresses. | __________________

**Table V : configuration de réseaux virtuels intersite**

Remplissez la Table S pour le sous-réseau de cette solution. Donnez au sous-réseau un nom convivial, un seul d'adressage IP unique en fonction de l'espace d'adressage du réseau virtuel et un but descriptif. L'espace d'adressage doit être au format de routage CIDR (Classless Interdomain Routing), également connu comme format de préfixe réseau. 10.24.64.0/20 est un exemple. Consultez votre service informatique afin de déterminer cet espace d'adressage à partir de l'espace d'adressage de réseau virtuel.

Élément | Nom du sous-réseau | Espace d'adressage du sous-réseau | Objectif 
--- | --- | --- | --- 
1. | _______________ | _____________________________ | _________________________

**Table S : sous-réseaux du réseau virtuel**

> [AZURE.NOTE]Cette architecture prédéfinie utilise un sous-réseau unique par souci de simplicité. Si vous voulez superposer un ensemble de filtres de trafic pour émuler l'isolation du sous-réseau, vous pouvez utiliser des [groupes de sécurité réseau](https://msdn.microsoft.com/library/azure/dn848316.aspx) Azure.

Pour les deux serveurs DNS locaux que vous souhaitez utiliser lors de la configuration initiale des contrôleurs de domaine de votre réseau virtuel, remplissez la table D. Donnez à chaque serveur DNS un nom convivial et une adresse IP unique. Ce nom convivial ne doit pas nécessairement correspondre au nom d'hôte ou au nom d'ordinateur du serveur DNS. Notez que deux entrées vides sont répertoriées, mais vous pouvez en ajouter d'autres. Consultez votre service informatique pour déterminer cette liste.

Élément | Nom convivial du serveur DNS | Adresse IP du serveur DNS 
--- | --- | ---
1. | ___________________________ | ___________________________
2. | ___________________________ | ___________________________ 

**Table D : serveurs DNS locaux**

Pour router des paquets du réseau intersite vers le réseau de votre organisation par le biais de la connexion VPN de site à site, vous devez configurer le réseau virtuel avec un réseau local qui contient une liste d'espaces d'adressage (en notation CIDR) pour tous les emplacements accessibles sur le réseau local de votre organisation. La liste des espaces d'adressage qui définissent votre réseau local ne doit pas inclure ou chevaucher l'espace d'adressage utilisé pour d'autres réseaux virtuels ou d'autres réseaux locaux. En d'autres termes, les espaces d'adressage pour les réseaux virtuels configurés et les réseaux locaux doivent être uniques.

Pour l'ensemble des espaces d'adressage du réseau local, remplissez la table L. Notez que trois entrées vides sont répertoriées, mais que vous en avez généralement besoin de plus. Consultez votre service informatique pour déterminer la liste des espaces d'adressage.

Élément | Espace d'adressage du réseau local 
--- | ---
1. | ___________________________________
2. | ___________________________________
3. | ___________________________________

**Table L : préfixes d'adresses pour le réseau local**

Pour créer le réseau virtuel avec les paramètres des tables V, S, D et L, suivez les instructions figurant dans [Créer un réseau virtuel intersite à l'aide de tables de configuration](virtual-machines-workload-deploy-vnet-config-tables.md).

> [AZURE.NOTE]Cette procédure vous guide tout au long de la création d'un réseau virtuel qui utilise une connexion VPN de site à site. Pour en savoir plus sur l'utilisation d'ExpressRoute pour votre connexion de site à site, consultez [Présentation technique d'ExpressRoute](http://msdn.microsoft.com/library/dn606309.aspx).

Après la création du réseau virtuel Azure, le portail de gestion Azure détermine les éléments suivants :

- l'adresse IPv4 publique de la passerelle VPN Azure pour votre réseau virtuel ;
- La clé Internet Protocol security (IPsec) prépartagée pour la connexion VPN de site à site.

Pour afficher ces éléments dans le portail de gestion Azure une fois le réseau virtuel créé, cliquez sur **Réseaux**, puis sur le nom du réseau virtuel, et enfin sur l'option de menu **Tableau de bord**.

Ensuite, vous devez configurer la passerelle du réseau virtuel pour créer une connexion VPN de site à site sécurisée. Vous trouverez les instructions détaillées sous [Configurer une passerelle de réseau virtuel dans le Portail de gestion](http://msdn.microsoft.com/library/jj156210.aspx).

Créez ensuite la connexion VPN de site à site entre le nouveau réseau virtuel et un périphérique VPN local. Pour en savoir plus sur les instructions, consultez [Configurer une passerelle de réseau virtuel dans le Portail de gestion](http://msdn.microsoft.com/library/jj156210.aspx).

Ensuite, assurez-vous que l'espace d'adressage du réseau virtuel est accessible à partir de votre réseau local. Pour cela, il vous suffit généralement d'ajouter un itinéraire correspondant à l'espace d'adressage du réseau virtuel à votre périphérique VPN, puis de transmettre cet itinéraire au reste de l'infrastructure de routage du réseau de votre organisation. Consultez votre service informatique pour déterminer la procédure à suivre.

Ensuite, suivez les instructions de la page [Installation et configuration d'Azure PowerShell](../install-configure-powershell.md) pour installer Azure PowerShell sur votre ordinateur local. Ouvrez une invite de commandes Azure PowerShell.

Sélectionnez tout d'abord l'abonnement Azure approprié à l'aide des commandes suivantes. Remplacez tous les éléments entre guillemets, y compris les caractères < and >, par les noms appropriés.

	$subscr="<Subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

Le nom de l'abonnement apparaît dans la propriété **SubscriptionName** du résultat de la commande **Get-AzureSubscription**.

Créez ensuite les trois services cloud nécessaires pour cette batterie de serveurs SharePoint. Remplissez la table C.

Élément | Objectif | Nom du service cloud 
--- | --- | ---
1. | Contrôleurs de domaine | ___________________________
2. | Serveurs SQL | ___________________________
3. | Serveurs SharePoint | ___________________________

**Table C : noms de service Cloud**

Vous devez choisir un nom unique pour chaque service cloud. *Le nom de service cloud ne peut contenir que des lettres, des chiffres et des traits d'union. Le premier et le dernier caractère du champ doivent être une lettre ou un chiffre.*

Par exemple, vous pouvez nommer le premier service cloud DCs-*UniqueSequence*, où *UniqueSequence* est l'abréviation de votre organisation. Par exemple, si le nom de votre organisation est Tailspin Toys, vous pouvez nommer le service cloud DCs-Tailspin.

Vous pouvez tester l'unicité du nom avec la commande Azure PowerShell suivante sur votre ordinateur local.

	Test-AzureName -Service <Proposed cloud service name>

Si cette commande renvoie « False », le nom proposé est unique. Ensuite, créez le service cloud avec les éléments suivants.

	New-AzureService -Service <Unique cloud service name> -Location "<Table V – Item 2 – Value column>"

Enregistrez le nom réel de chaque service cloud nouvellement créé dans la table C.

Créez ensuite un compte de stockage pour la batterie de serveurs SharePoint. *Vous devez choisir un nom unique qui contient uniquement des chiffres et des lettres en minuscules.* Vous pouvez tester l'unicité du nom avec la commande Azure PowerShell suivante.

	Test-AzureName -Storage <Proposed storage account name>

Si cette commande renvoie « False », le nom proposé est unique. Ensuite, créez le compte de stockage et définissez l'abonnement à utiliser avec ces commandes.

	$staccount="<Unique storage account name>"
	New-AzureStorageAccount -StorageAccountName $staccount -Location "<Table V – Item 2 – Value column>"
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Définissez enfin les noms des quatre groupes à haute disponibilité. Remplissez la table A.

Élément | Objectif | Nom du groupe à haute disponibilité 
--- | --- | --- 
1. | Contrôleurs de domaine | ___________________________
2. | Serveurs SQL | ___________________________
3. | Serveurs d'applications SharePoint | ___________________________
4. | Serveurs web frontaux SharePoint | ___________________________

**Nom du groupe à haute disponibilité**

Vous aurez besoin de ces noms lors de la création des machines virtuelles au cours des phases 2, 3 et 4.

Vous trouverez ci-dessous la configuration résultant de l'exécution de cette phase.

![](./media/virtual-machines-workload-intranet-sharepoint-phase1/workload-spsqlao_01.png)

## Étape suivante

Pour poursuivre la configuration de cette charge de travail, passez à la [Phase 2 : configuration de contrôleurs de domaine](virtual-machines-workload-intranet-sharepoint-phase2.md).

## Ressources supplémentaires

[Déploiement de SharePoint avec des groupes de disponibilité SQL Server AlwaysOn dans Azure](virtual-machines-workload-intranet-sharepoint-overview.md)

[Batteries de serveurs SharePoint hébergés dans des services d’infrastructure Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Infographie SharePoint avec SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788)

[Architectures Microsoft Azure pour SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Instructions d’implémentation des services d’infrastructure Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)
 

<!---HONumber=July15_HO2-->
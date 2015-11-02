<properties
	pageTitle="Configuration de points de terminaison sur une machine virtuelle dans Azure"
	description="Découvrez comment configurer des points de terminaison dans le portail pour permettre la communication avec une machine virtuelle dans Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/28/2015"
	ms.author="cynthn"/>

#Comment configurer des points de terminaison sur une machine virtuelle

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager

Toutes les machines virtuelles créées dans Azure et appartenant au même service cloud ou réseau virtuel peuvent communiquer automatiquement entre elles à l'aide d'un canal réseau privé. Toutefois, les ordinateurs sur Internet ou d'autres réseaux virtuels requièrent des points de terminaison pour diriger le trafic réseau entrant vers une machine virtuelle.

Quand vous créez une machine virtuelle dans le portail Azure, les points de terminaison pour le Bureau à distance, l’accès distant Windows PowerShell et Secure Shell (SSH) sont créés automatiquement. Vous pouvez configurer des points de terminaison supplémentaires lors de la création de la machine virtuelle, ou ultérieurement si nécessaire.

[AZURE.INCLUDE [service-management-pointer-to-resource-manager](../../includes/service-management-pointer-to-resource-manager.md)]

- [À propos des groupes de sécurité réseau](virtual-networks-nsg.md)

Chaque point de terminaison possède un port public et un port privé :

- Le port public est utilisé par l'équilibreur de charge Azure pour écouter le trafic entrant dans la machine virtuelle à partir d'Internet.
- Le port privé est utilisé par la machine virtuelle pour écouter le trafic entrant, généralement destiné à une application ou à un service en cours d'exécution sur la machine virtuelle.

Les valeurs par défaut pour le protocole IP et les ports TCP ou UDP pour des protocoles réseau bien connus sont fournies quand vous créez des points de terminaison avec le portail. Pour les points de terminaison personnalisés, vous devrez spécifier le protocole IP correct (TCP ou UDP) et les ports publics et privés. Pour distribuer le trafic entrant au hasard entre plusieurs machines virtuelles, vous devrez créer un jeu d'équilibrage de la charge composé de plusieurs points de terminaison.

Après avoir créé un point de terminaison, vous pouvez utiliser une liste de contrôle d’accès (ACL) pour définir des règles permettant d’autoriser ou de refuser le trafic entrant vers le port public du point de terminaison en fonction de son adresse IP source. Toutefois, si la machine virtuelle se trouve dans un réseau virtuel Azure, vous devez utiliser à la place les groupes de sécurité réseau. Pour plus d’informations, consultez [À propos des groupes de sécurité réseau](virtual-networks-nsg.md).

> [AZURE.NOTE]\: la configuration du pare-feu pour les machines virtuelles Azure est effectuée automatiquement pour les ports associés au Bureau à distance et à Secure Shell (SSH) ainsi que pour l'accès distant Windows PowerShell dans la plupart des cas. Pour les ports spécifiés pour tous les autres points de terminaison, aucune configuration n'est effectuée automatiquement pour le pare-feu de la machine virtuelle. Lorsque vous créez un point de terminaison pour la machine virtuelle, vous devez vous assurer que le pare-feu de la machine autorise également le trafic du protocole et le port privé correspondant à la configuration du point de terminaison.

##Création d’un point de terminaison

1.	Si ce n’est pas déjà fait, connectez-vous au [portail](http://manage.windowsazure.com/).
2.	Cliquez sur **Machines virtuelles**, puis sur le nom de la machine virtuelle à configurer.
3.	Cliquez sur **Endpoints**. La page **Points de terminaison** répertorie tous les points de terminaison actuels de la machine virtuelle.

	![Points de terminaison](./media/virtual-machines-set-up-endpoints/endpointswindows.png)

4.	Dans la barre des tâches, cliquez sur **Ajouter**.
5.	Sur la page **Ajouter un point de terminaison** à la machine virtuelle, choisissez le type de point de terminaison.

	- Si vous créez un point de terminaison qui ne fait pas partie d’un jeu d’équilibrage de la charge, ou qui est le premier point de terminaison d’un nouveau jeu d’équilibrage de la charge, choisissez **Ajouter un point de terminaison autonome**, puis cliquez sur la flèche gauche.
	- Sinon, choisissez **Ajouter un point de terminaison à un jeu d’équilibrage de la charge existant**, sélectionnez le nom du jeu d’équilibrage de la charge, puis cliquez sur la flèche gauche. Dans la page **Spécifier les détails du point de terminaison**, tapez un nom pour le point de terminaison, puis cochez la case pour créer le point de terminaison.

6.	Sur la page **Spécifier les détails du point de terminaison**, tapez un nom pour le point de terminaison dans **Nom**. Vous pouvez également choisir un nom de protocole réseau dans la liste, ce qui permet de renseigner les valeurs initiales pour les **Protocole**, **Port public** et **Port privé**.
7.	Pour un point de terminaison personnalisé dans **Protocole**, choisissez **TCP** ou **UDP**.
8.	Pour les ports personnalisés, dans **Port public**, tapez le numéro de port pour le trafic entrant depuis Internet. Dans **Port privé**, tapez le numéro de port que la machine virtuelle écoute. Ces derniers peuvent être différents. Assurez-vous que le pare-feu sur la machine virtuelle a été configuré pour autoriser le trafic correspondant au protocole (à l'étape 7) et au port privé.
9.	Si ce point de terminaison est le premier dans un jeu d’équilibrage de la charge, cliquez sur **Créer un jeu d’équilibrage de la charge**, puis sur la flèche droite. Sur la page **Configurer le jeu d’équilibrage de la charge**, spécifiez un nom de jeu d’équilibrage de la charge, un protocole et un port de sonde ainsi que l’intervalle de sondage et le nombre de sondes envoyé. L'équilibreur de charge Azure envoie les sondes aux machines virtuelles dans un jeu d'équilibrage de la charge pour surveiller leur disponibilité. L'équilibreur de charge Azure ne transmet pas le trafic vers des machines virtuelles qui ne répondent pas à la sonde. Cliquez sur la flèche droite.
10.	Cliquez sur la coche pour créer le point de terminaison.

Le nouveau point de terminaison est répertorié dans la page **Points de terminaison**.

![Création du point de terminaison réussie](./media/virtual-machines-set-up-endpoints/endpointwindowsnew.png)

Pour configurer ce paramètre à l’aide d’une applet de commande Azure PowerShell, consultez l’article [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).

##Gestion de l’ACL sur un point de terminaison

Pour définir l'ensemble des ordinateurs qui peuvent envoyer du trafic, l'ACL sur un point de terminaison peut restreindre le trafic basé sur l'adresse IP source. Suivez cette procédure pour ajouter, modifier ou supprimer l’ACL sur un point de terminaison.

> [AZURE.NOTE]si le point de terminaison fait partie d’un jeu d’équilibrage de charge, chaque modification faite sur l’ACL pour un point de terminaison est appliquée à tous les points de terminaison du jeu.

Si la machine virtuelle se trouve dans un réseau virtuel Azure, nous vous recommandons d’utiliser les groupes de sécurité réseau à la place des ACL. Pour plus d’informations, consultez [À propos des groupes de sécurité réseau](virtual-networks-nsg.md).

1.	Si ce n’est pas déjà fait, connectez-vous au [portail](http://manage.windowsazure.com/).
2.	Cliquez sur **Machines virtuelles**, puis sur le nom de la machine virtuelle à configurer.
3.	Cliquez sur **Endpoints**. Sélectionnez le point de terminaison approprié dans la liste.

    ![Liste ACL](./media/virtual-machines-set-up-endpoints/EndpointsShowsDefaultEndpointsForVM.png)

5.	Dans la barre des tâches, cliquez sur **Gérer l’ACL** pour ouvrir la boîte de dialogue **Spécifier les détails de l’ACL**.

    ![Spécifier les détails de l’ACL](./media/virtual-machines-set-up-endpoints/EndpointACLdetails.png)

6.	Utilisez les lignes de la liste pour ajouter, supprimer ou modifier les règles d'une ACL et modifier leur ordre. La valeur du **Sous-réseau distant** est une plage d’adresses IP pour le trafic entrant depuis Internet que l’équilibreur de charge Azure utilise pour autoriser ou refuser le trafic en fonction de son adresse IP source. Veillez à spécifier la plage d'adresses IP au format CIDR, également connu sous le nom de format de préfixe adresse. 131.107.0.0/16 est un exemple.

Vous pouvez utiliser des règles pour autoriser uniquement le trafic provenant de certains ordinateurs correspondant à vos ordinateurs sur Internet ou refuser le trafic provenant de plages d'adresses spécifiques et connues.

Les règles sont évaluées dans l’ordre, en commençant par la première règle et en terminant par la dernière. Cela signifie que les règles doivent être répertoriées de la moins restrictive à la plus restrictive. Pour obtenir des exemples et plus d’informations, consultez [Qu’est-ce qu’une liste de contrôle d’accès réseau ?](../virtual-network/virtual-networks-acl/).

Pour effectuer cette configuration avec une applet de commande Azure PowerShell, consultez [Gestion des listes de contrôle d’accès (ACL) pour les points de terminaison à l’aide de PowerShell](../virtual-network/virtual-networks-acl-powershell.md).

## Ressources supplémentaires

[Équilibrage de charge pour les services d’infrastructure Azure](virtual-machines-load-balance.md)

<!---HONumber=Oct15_HO4-->
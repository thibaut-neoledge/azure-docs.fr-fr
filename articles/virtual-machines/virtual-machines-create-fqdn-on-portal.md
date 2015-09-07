<properties
   pageTitle="Créer un nom de domaine complet pour une machine virtuelle dans le portail Azure | Microsoft Azure"
	description="Apprenez à créer un nom de domaine complet pour une machine virtuelle Resource Manager dans le portail Azure en version préliminaire."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-management"/>

<tags
   ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/21/2015"
	ms.author="dkshir"/>

# Créer un nom de domaine complet dans le portail Azure en version préliminaire

Lorsque vous créez une machine virtuelle dans le [portail Azure en version préliminaire](https://portal.azure.com) à l’aide du modèle de déploiement **Resource Manager**, le portail crée une ressource d’IP publique pour la machine virtuelle. Vous pouvez utiliser cette adresse IP pour accéder à distance à la machine virtuelle. Toutefois, le portail ne crée pas de [nom de domaine complet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), par défaut. Dans la mesure où il est plus facile de retenir un nom de domaine complet qu’une adresse IP, cet article vous explique comment en ajouter un à votre machine virtuelle.

Cet article suppose que vous vous êtes connecté à votre abonnement dans le portail et créé une machine virtuelle avec les images disponibles, à l’aide de **Resource Manager**. Une fois que votre machine virtuelle commence à s’exécuter, suivez ces étapes.

1.  Affichez les paramètres de machine virtuelle sur le portail, puis cliquez sur l’adresse IP publique.

    ![localiser la ressource d’adresse IP](media/virtual-machines-create-fqdn-on-portal/locatePublicIP.PNG)

2.  **Dissociez** l’adresse IP publique de la machine virtuelle. Notez qu’aucun nom de domaine ne s’affiche à ce stade. Une fois que vous avez cliqué sur le bouton **Oui**, la dissociation prend quelques secondes.

    ![dissocier la ressource d’adresse IP](media/virtual-machines-create-fqdn-on-portal/dissociateIP.PNG)

    Nous allons associer cette adresse IP publique à la machine virtuelle après l’exécution des étapes suivantes. Si l’adresse IP publique est une adresse _IP publique dynamique_, vous perdez l’adresse IPV4, tandis qu’une nouvelle adresse est affectée après la configuration du nom de domaine complet.

3.  Une fois que le bouton **Dissocier** est grisé, cliquez sur la section **Tous les paramètres** de l’adresse IP publique, puis ouvrez l’onglet **Configuration**. Entrez l’étiquette de nom DNS souhaitée. **Enregistrez** cette configuration.

    ![entrer l’étiquette du nom dns](media/virtual-machines-create-fqdn-on-portal/dnsNameLabel.PNG)

4.  Revenez dans le panneau de la machine virtuelle dans le portail, puis cliquez sur **Tous les paramètres** pour la machine virtuelle. Ouvrez l’onglet **Interfaces réseau**, puis cliquez sur la ressource d’interface réseau associée à cette machine virtuelle. Cette action ouvrira le panneau **Interface réseau** dans le portail.

    ![ouvrir l’interface réseau](media/virtual-machines-create-fqdn-on-portal/openNetworkInterface.PNG)

5.  Notez que le champ **Adresse IP publique** associé à l’interface réseau est vide. Cliquez sur la section **Tous les paramètres** associée à cette interface réseau, puis ouvrez l’onglet **Adresses IP**. Sur le panneau **Adresses IP**, cliquez sur **Activé** pour le champ **Adresse IP publique**. Sélectionnez l’onglet **Adresse IP Configurer les paramètres requis**, puis sélectionnez l’adresse IP par défaut que vous avez dissociée plus haut. Cliquez sur **Save**. L’ajout de la ressource IP peut prendre quelques instants.

    ![configurer la ressource d’adresse IP](media/virtual-machines-create-fqdn-on-portal/configureIP.PNG)

6.  Fermez tous les autres panneaux, puis revenez au panneau **Machine virtuelle**. Dans les paramètres, cliquez sur la ressource d’adresse IP publique. Notez que le panneau d’adresse IP publique représente désormais le nom de domaine complet souhaité en tant que **nom DNS**.

    ![le nom de domaine complet est créé](media/virtual-machines-create-fqdn-on-portal/fqdnCreated.PNG)

    Vous pouvez maintenant vous connecter à distance à la machine virtuelle à l’aide de ce nom DNS. Par exemple, utilisez `SSH adminuser@testdnslabel.eastus.cloudapp.azure.com` lorsque vous vous connectez à une machine virtuelle Linux qui présente le nom de domaine complet `testdnslabel.eastus.cloudapp.azure.com` et le nom d’utilisateur `adminuser`.

<!---HONumber=August15_HO9-->
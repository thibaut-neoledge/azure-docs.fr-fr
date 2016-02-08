<properties
   pageTitle="Créer un nom de domaine complet pour une machine virtuelle dans le portail Azure | Microsoft Azure"
   description="Apprenez à créer un nom de domaine complet pour une machine virtuelle Resource Manager dans le portail Azure."
   services="virtual-machines"
   documentationCenter=""
   authors="dsk-2015"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/19/2016"
   ms.author="dkshir"/>

# Créer un nom de domaine complet dans le portail Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique


Lorsque vous créez une machine virtuelle dans le [portail Azure](https://portal.azure.com) à l’aide du modèle de déploiement du **Gestionnaire des ressources**, le portail crée une ressource d’adresse IP publique pour la machine virtuelle. Vous pouvez utiliser cette adresse IP pour accéder à distance à la machine virtuelle. Bien que le portail ne crée pas de [nom de domaine complet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), par défaut, il est très facile d’en générer un une fois la machine virtuelle créée. Cet article explique les étapes pour créer un nom DNS ou un nom de domaine complet.

Cet article suppose que vous vous êtes connecté à votre abonnement dans le portail et créé une machine virtuelle avec les images disponibles, à l’aide de **Gestionnaire des ressources**. Une fois que votre machine virtuelle commence à s’exécuter, suivez ces étapes.

1.  Affichez les paramètres de machine virtuelle sur le portail, puis cliquez sur l’adresse IP publique.

    ![localiser la ressource d’adresse IP](media/virtual-machines-create-fqdn-on-portal/locatePublicIP.PNG)

2.  Notez que le nom DNS de l’adresse IP publique est vide. Cliquez sur **Tous les paramètres** pour accéder au panneau de l’adresse IP publique.

    ![paramètres ip](media/virtual-machines-create-fqdn-on-portal/settingsIP.PNG)

3.  Ouvrez l’onglet **Configuration** dans les paramètres de l’adresse IP publique. Entrez l’étiquette souhaitée pour le nom DNS, puis **enregistrez** cette configuration.

    ![entrer l’étiquette du nom dns](media/virtual-machines-create-fqdn-on-portal/dnsNameLabel.PNG)

    La ressource d’adresse IP publique affiche désormais ce nouveau nom DNS sur son panneau.

4.  Fermez les panneaux de l’adresse IP publique, puis revenez au panneau de la machine virtuelle dans le portail. Vérifiez que le nom DNS/nom de domaine complet apparaît en regard de l’adresse IP pour la ressource d’adresse IP publique.

    ![le nom de domaine complet est créé](media/virtual-machines-create-fqdn-on-portal/fqdnCreated.PNG)


    Vous pouvez maintenant vous connecter à distance à la machine virtuelle à l’aide de ce nom DNS. Par exemple, utilisez `SSH adminuser@testdnslabel.centralus.cloudapp.azure.com` lorsque vous vous connectez à une machine virtuelle Linux qui présente le nom de domaine complet `testdnslabel.centralus.cloudapp.azure.com` et le nom d’utilisateur `adminuser`.

<!---HONumber=AcomDC_0128_2016-->
---
title: "Créer un réseau virtuel Azure (classique) - Portail Classic | Microsoft Docs"
description: "Découvrez comment créer un réseau virtuel (classique) avec un fichier de configuration réseau dans le portail Azure Classic."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 69894a0b-8050-451e-8a25-c513e1bd271e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/30/2017
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: d365f7137527d60eb509b4f431295de2218ea706
ms.openlocfilehash: 5ff91cccb711d61ed120e4a4e820d6a5dfc5e4a6


---

# <a name="create-a-virtual-network-classic-with-a-netcfg-file-using-the-azure-classic-portal"></a>Créer un réseau virtuel (classique) avec un fichier de configuration réseau dans le portail Azure Classic
[!INCLUDE [virtual-networks-create-vnet-selectors-classic-include](../../includes/virtual-networks-create-vnet-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Cet article explique comment créer un réseau virtuel avec un fichier de configuration réseau via le modèle de déploiement classique dans le portail Azure Classic. Vous pouvez également [créer un réseau virtuel via le modèle de déploiement classique sans utiliser de fichier de configuration réseau](virtual-networks-create-vnet-classic-pportal.md) ou [créer un réseau virtuel via le modèle de déploiement Azure Resource Manager](virtual-networks-create-vnet-arm-pportal.md) dans le portail Azure.

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="how-to-create-a-vnet-with-a-network-config-file-in-the-microsoft-azure-classic-portal"></a>Comment créer un réseau virtuel avec un fichier de configuration réseau dans le portail Microsoft Azure Classic
Azure utilise un fichier xml pour définir tous les réseaux virtuels disponibles pour un abonnement. Vous pouvez télécharger ce fichier et le modifier pour créer des réseaux virtuels via le modèle de déploiement classique, ou pour modifier ou supprimer des réseaux virtuels existants. Cet article explique comment télécharger ce fichier, appelé fichier de configuration réseau (ou netcfg), comment y ajouter un réseau virtuel et comment charger le fichier pour créer le réseau virtuel. Pour plus d’informations sur le fichier de configuration réseau, consultez [Schéma de configuration du réseau virtuel Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

Pour créer un réseau virtuel avec un fichier de configuration réseau dans le portail Azure Classic, procédez comme suit :

1. Dans un navigateur, accédez à http://manage.windowsazure.com et, si nécessaire, connectez-vous avec votre compte Azure.
2. Faites défiler la liste des services, cliquez sur **RÉSEAUX**, puis sur **EXPORTER**, comme illustré dans l’image suivante :

    ![Réseaux virtuels Azure](./media/virtual-networks-create-vnet-classic-portal/networks.png)
3. Dans la boîte de dialogue **Exporter la configuration de réseau**, sélectionnez l’abonnement dont vous voulez exporter la configuration de réseau virtuel, puis cliquez sur le bouton avec la coche en bas à droite.
4. Suivez les instructions de votre navigateur pour enregistrer le fichier **NetworkConfig.xml** . Assurez-vous de noter où vous enregistrez le fichier.
5. Ouvrez le fichier enregistré à l’étape 4 avec une application d’éditeur XML ou de texte, puis recherchez l’élément `<VirtualNetworkSites>` dans l’élément `<VirtualNetworkConfiguration>`. Si vous avez des réseaux virtuels existants, chacun est répertorié dans son propre élément `<VirtualNetworkSite>`. Si le fichier ne contient pas d’élément `<VirtualNetworkSites>` dans l’élément `<VirtualNetworkConfiguration>`, créez-en un.
6. Si votre fichier NetworkConfig existant ne contient aucun réseau virtuel, votre fichier NetworkConfig.xml doit ressembler à l’exemple suivant une fois que vous y avez ajouté le réseau virtuel décrit dans ce scénario :

    ```xml
    <NetworkConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="TestVNet" Location="Central US">
            <AddressSpace>
              <AddressPrefix>192.168.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="FrontEnd">
                <AddressPrefix>192.168.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>192.168.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>
    ```
7. Enregistrez le fichier de configuration réseau.
8. Dans le portail Azure Classic, cliquez sur **NOUVEAU**, **SERVICES RÉSEAU**, **RÉSEAU VIRTUEL**, puis cliquez sur **IMPORTER LA CONFIGURATION** comme illustré dans l’image suivante :

    ![IMPORTER LA CONFIGURATION](./media/virtual-networks-create-vnet-classic-portal/import.png)
10. Dans la boîte de dialogue **Importer le fichier de configuration réseau**, cliquez sur **RECHERCHER LE FICHIER...**, accédez au dossier où vous avez enregistré votre fichier à l’étape 7, sélectionnez le fichier, puis cliquez sur **Ouvrir**, comme illustré dans l’image suivante :

    ![Page d’importation du fichier de configuration réseau](./media/virtual-networks-create-vnet-classic-portal/vnet-create-portal-netcfg-figure4.png)

    En bas à droite, cliquez sur la flèche pour passer à l’étape suivante.

9. Dans la boîte de dialogue **Création de votre réseau**, notez la présence du nouveau réseau virtuel, comme illustré dans l’image suivante :

    ![Page de création de votre réseau](./media/virtual-networks-create-vnet-classic-portal/vnet-create-portal-netcfg-figure5.png)
10. Pour créer le réseau virtuel, cliquez sur le bouton avec la coche dans le coin inférieur droit de la boîte de l’image précédente. Après quelques secondes, le réseau virtuel apparaît dans la liste des réseaux virtuels disponibles, comme illustré dans l’image suivante :

    ![Nouveau réseau virtuel](./media/virtual-networks-create-vnet-classic-portal/vnet-create-portal-netcfg-figure6.png)



<!--HONumber=Jan17_HO5-->



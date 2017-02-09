---
title: "Guide pratique pour définir une adresse IP privée statique en mode classique à partir du portail Azure | Microsoft Docs"
description: "Présentation des adresses IP privées statiques et de leur gestion en mode classique à l&quot;aide du portail Azure"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a9a18f67a5c124f5f6d00a852f44ab5d071a7715


---
# <a name="how-to-set-a-static-private-ip-address-classic-in-the-azure-portal"></a>Définition d'une adresse IP privée statique (classique) dans le portail Azure
[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Cet article traite du modèle de déploiement classique. Vous pouvez également [gérer une adresse IP privée statique dans le modèle de déploiement Resource Manager](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Les étapes de l’exemple ci-dessous supposent qu’un environnement simple a déjà été créé. Si vous souhaitez exécuter les étapes telles qu’elles sont présentées dans ce document, commencez par créer l’environnement de test décrit dans [Créer un réseau virtuel](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Comment spécifier une adresse IP privée statique lors de la création d’une machine virtuelle
Pour créer une machine virtuelle nommée *DNS01* dans le sous-réseau *FrontEnd* d’un réseau virtuel nommé *TestVNet* avec l’adresse IP privée statique *192.168.1.101*, procédez comme suit :

1. Dans un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous avec votre compte Azure.
2. Cliquez sur **Nouveau** > **Compute** > **Windows Server 2012 R2 Datacenter** (notez que la liste **Sélectionner un modèle de déploiement** contient déjà **Classique**), puis cliquez sur **Créer**.
   
    ![Création d'une machine virtuelle dans le portail Azure](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. Dans le panneau **Créer une machine virtuelle** , entrez le nom de la machine virtuelle à créer (*DNS01* dans notre scénario), le compte d’administrateur local et un mot de passe.
   
    ![Création d'une machine virtuelle dans le portail Azure](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Cliquez sur **Configuration facultative** > **Réseau** > **Réseau virtuel**, puis sur **TestVNet**. Si l’option **TestVNet** n’est pas disponible, assurez-vous que vous utilisez l’emplacement *Centre des États-Unis* et avez créé l’environnement de test décrit au début de cet article.
   
    ![Création d'une machine virtuelle dans le portail Azure](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. Dans le panneau **Réseau**, vérifiez que le sous-réseau actuellement sélectionné est *FrontEnd*, puis cliquez sur **Adresses IP**. Sous **Affectation d’adresses IP**, cliquez sur **Statique**, puis entrez *192.168.1.101* pour **Adresse IP**, comme illustré ci-dessous.
   
    ![Création d'une machine virtuelle dans le portail Azure](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Cliquez successivement sur **OK** dans le panneau **Adresses IP**, sur **OK** dans le panneau **Réseau**, puis sur **OK** dans le panneau **Configuration facultative**.
7. Dans le panneau **Créer une machine virtuelle**, cliquez sur **Créer**. Notez la vignette ci-dessous affichée dans votre tableau de bord.
   
    ![Création d'une machine virtuelle dans le portail Azure](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Récupération d’informations d’adresse IP privée statique pour une machine virtuelle
Pour afficher les informations d’adresse IP privée statique de la machine virtuelle créée lors des étapes ci-dessus, exécutez les étapes ci-dessous.

1. Dans le portail Azure, cliquez sur **Parcourir tout** > **Machines virtuelles (Classic)** > **DNS01** > **Tous les paramètres** > **Adresses IP**, puis examinez l’affectation d’adresses IP et l’adresse IP, telles qu’illustrées ci-dessous.
   
    ![Création d'une machine virtuelle dans le portail Azure](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Suppression d’une adresse IP privée statique d’une machine virtuelle
Pour supprimer l’adresse IP privée statique de la machine virtuelle créée ci-dessus, procédez comme suit.

1. Dans le panneau **Adresses IP** illustré ci-dessus, cliquez sur **Dynamique** à droite de **Affectation d’adresses IP**, puis cliquez sur **Enregistrer** et sur **Oui**.
   
    ![Création d'une machine virtuelle dans le portail Azure](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Ajout d’une adresse IP privée statique à une machine virtuelle existante
Pour ajouter une adresse IP privée statique à la machine virtuelle créée lors des étapes ci-dessus, exécutez les étapes ci-dessous :

1. Dans le panneau **Adresses IP** illustré ci-dessus, à droite de **Affectations d’adresses IP**, cliquez sur **Statique**.
2. Tapez *192.168.1.101* pour **Adresse IP**, puis cliquez sur **Enregistrer** et sur **Oui**.

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur les [adresses IP publiques réservées](virtual-networks-reserved-public-ip.md) .
* En savoir plus sur les [adresses IP publiques de niveau d’instance](virtual-networks-instance-level-public-ip.md) .
* Consulter les [API REST d’adresse IP réservée](https://msdn.microsoft.com/library/azure/dn722420.aspx).




<!--HONumber=Nov16_HO3-->



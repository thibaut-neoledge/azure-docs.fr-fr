---
title: "Configuration d’un réseau virtuel dans Azure DevTest Labs | Microsoft Docs"
description: "Découvrez comment configurer un réseau virtuel et un sous-réseau existants, puis les utiliser dans une machine virtuelle avec Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 6cda99c2-b87e-4047-90a0-5df10d8e9e14
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: tarcher
ms.openlocfilehash: 19dbb1625f46f8864413dc538a96b2413bc6eea0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Configuration d’un réseau virtuel dans Azure DevTest Labs
Comme expliqué dans l’article [Ajouter une machine virtuelle avec des artefacts à un laboratoire](devtest-lab-add-vm-with-artifacts.md), quand vous créez une machine virtuelle dans un laboratoire, vous pouvez spécifier un réseau virtuel configuré. Voici l’un des scénarios de cette opération : vous devez être en mesure d'accéder aux ressources de votre réseau d'entreprise à partir de vos machines virtuelles à l'aide du réseau virtuel configuré avec ExpressRoute ou un VPN de site à site. Les sections suivantes montrent comment ajouter votre réseau virtuel existant aux paramètres de réseau virtuel d’un labo afin qu'il puisse être sélectionné lors de la création de machines virtuelles.

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Configurer un réseau virtuel pour un laboratoire en utilisant le portail Azure
Les étapes suivantes vous montrent comment ajouter un réseau virtuel (et sous-réseau) existant à un laboratoire pour qu’il puisse être utilisé lors de la création d’une machine virtuelle dans le même laboratoire. 

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Sélectionnez **Autres services**, puis **DevTest Labs** dans la liste.
3. Sélectionnez le laboratoire souhaité dans la liste des laboratoires. 
4. Dans le panneau du laboratoire, sélectionnez **Configuration et stratégies**.
5. Dans le panneau **Configuration et stratégies**, sélectionnez **Réseaux virtuels**.
6. Sur le panneau **Réseaux virtuels** , vous verrez une liste de réseaux virtuels configurés pour le labo actuel ainsi que le réseau virtuel par défaut créé pour votre labo. 
7. Sélectionnez **Ajouter**.
   
    ![Ajout d’un réseau virtuel existant à votre labo](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
8. Sur le panneau **Réseau virtuel**, sélectionnez **[Sélectionner un réseau virtuel]**.
   
    ![Sélection d’un réseau virtuel existant](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
9. Sur le panneau **Choisissez un réseau virtuel** , sélectionnez le réseau virtuel souhaité. Le panneau affiche tous les réseaux virtuels sous la même région de l'abonnement que le labo.  
10. Après avoir sélectionné un réseau virtuel, vous êtes redirigé vers le **Réseau virtuel**. Cliquez sur le sous-réseau dans la liste située en bas du panneau.

    ![Liste des sous-réseaux](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    Le panneau Lab Subnet (Sous-réseau de laboratoire) s’affiche.

    ![Panneau Lab Subnet (Sous-réseau de laboratoire)](./media/devtest-lab-configure-vnet/lab-subnet.png)

11. Spécifiez un **nom de sous-réseau de laboratoire**.
12. Pour permettre l’utilisation d’un sous-réseau dans le laboratoire de création de machines virtuelles, sélectionnez **Utiliser dans la création de machines virtuelles**.
13. Pour activer une [adresse IP publique partagée](devtest-lab-shared-ip.md), sélectionnez **Enable shared public IP** (Activer l’adresse IP publique partagée).
14. Pour autoriser les adresses IP publiques dans un sous-réseau, sélectionnez **Allow public IP creation** (Autoriser la création d’adresses IP publiques).
15. Dans le champ **Nombre maximal de machines virtuelles par utilisateur**, spécifiez le nombre maximal de machines virtuelles par utilisateur pour chaque sous-réseau. Si vous souhaitez un nombre illimité de machines virtuelles, laissez ce champ vide.
16. Cliquez sur **OK** pour fermer le panneau Lab Subnet (Sous-réseau de laboratoire).
17. Sélectionnez **Enregistrer** pour fermer le panneau Réseau virtuel.
18. Maintenant que le réseau virtuel est configuré, il peut être sélectionné lors de la création d'une machine virtuelle. 
    Pour voir comment créer une machine virtuelle et spécifier un réseau virtuel, consultez l’article [Ajouter une machine virtuelle avec des artefacts à un laboratoire](devtest-lab-add-vm-with-artifacts.md). 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez ajouté le réseau virtuel souhaité à votre laboratoire, l’étape suivante consiste à [ajouter une machine virtuelle à votre laboratoire](devtest-lab-add-vm-with-artifacts.md).


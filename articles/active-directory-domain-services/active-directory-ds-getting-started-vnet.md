---
title: "Azure Active Directory Domain Services : créer ou sélectionner un réseau virtuel | Microsoft Docs"
description: "Activer Azure Active Directory Domain Services à l’aide du portail Azure Classic"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 13ab1608-e3d8-40de-9f7b-9b5b42199af4
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/28/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 457519b00b65b0157effe2d4aba033a1c99852e8
ms.contentlocale: fr-fr
ms.lasthandoff: 06/30/2017


---
<a id="create-or-select-a-virtual-network-for-azure-active-directory-domain-services" class="xliff"></a>

# Créer ou sélectionner un réseau virtuel pour Azure Active Directory Domain Services
<a id="before-you-begin" class="xliff"></a>

## Avant de commencer
Reportez-vous à [Considérations relatives à la mise en réseau pour Azure Active Directory Domain Services](active-directory-ds-networking.md).

<a id="task-2-create-an-azure-virtual-network" class="xliff"></a>

## Tâche 2 : créer un réseau virtuel Azure
La tâche de configuration suivante consiste à créer un réseau virtuel Azure et un sous-réseau à l’intérieur de celui-ci. Vous activez Azure Active Directory Domain Services dans ce sous-réseau de votre réseau virtuel. Si vous souhaitez utiliser un réseau virtuel existant, vous pouvez ignorer cette étape.

> [!NOTE]
> Assurez-vous que le réseau virtuel Azure que vous créez ou que vous choisissez d’utiliser avec Azure Active Directory Domain Services appartient à une région Azure qui est prise en charge par Azure Active Directory Domain Services. Pour connaître les régions Azure dans lesquelles Azure Active Directory Domain Services est disponible, consultez la page [Services Azure par région](https://azure.microsoft.com/regions/#services/) .
>
>Notez le nom du réseau virtuel, car vous en aurez besoin au cours d’une étape de configuration ultérieure pour sélectionner le réseau virtuel adéquat au moment de l’activation d’Azure Active Directory Domain Services.


Pour créer un réseau virtuel Azure dans lequel vous souhaitez activer Azure Active Directory Domain Services, suivez les instructions de configuration suivantes :

1. Connectez-vous au [Portail Azure Classic](https://manage.windowsazure.com).
2. Dans le volet gauche, sélectionnez **Réseaux**.

    ![Nœud Réseaux](./media/active-directory-domain-services-getting-started/networks-node.png)  
    La fenêtre **Réseaux virtuels** s’ouvre.
3. Dans le volet des tâches en bas de la fenêtre, cliquez sur **Nouveau**.

    ![Fenêtre Réseaux virtuels](./media/active-directory-domain-services-getting-started/virtual-networks.png)
4. Cliquez sur **Services réseau**, puis sélectionnez **Réseau virtuel**.

    ![Réseau virtuel : création rapide](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)
5. Pour créer un réseau virtuel, cliquez sur **Création rapide**.

6. Spécifiez un **nom** pour votre réseau virtuel, et procédez comme suit :
    * Vous pouvez choisir de configurer **l’Espace d’adressage** ou le **Nombre maximal de machines virtuelles** pour ce réseau.
    * Vous pouvez laisser le paramètre du **serveur DNS** sur **Aucun** pour l’instant. Vous pourrez mettre à jour ce paramètre après avoir activé Azure Active Directory Domain Services.
7. Dans la liste déroulante **Emplacement**, sélectionnez une région Azure prise en charge.  
    Pour connaître les régions Azure dans lesquelles Azure Active Directory Domain Services est disponible, consultez la page [Services Azure par région](https://azure.microsoft.com/regions/#services/) .
8. Pour créer votre réseau virtuel, cliquez sur **Créer un réseau virtuel** .

    ![Créer un réseau virtuel pour Azure Active Directory Domain Services](./media/active-directory-domain-services-getting-started/create-vnet.png)
9. Une fois que vous avez créé un réseau virtuel, sélectionnez son nom, puis cliquez sur l’onglet **Configurer**.

    ![Créer un sous-réseau](./media/active-directory-domain-services-getting-started/create-vnet-properties.png)
10. Sous **Espaces d’adressage du réseau virtuel**, cliquez sur **Ajouter un sous-réseau**, puis spécifiez un sous-réseau avec le nom **AaddsSubnet**.

    ![Créer un sous-réseau pour Azure Active Directory Domain Services](./media/active-directory-domain-services-getting-started/create-vnet-add-subnet.png)

11. Pour créer le sous-réseau, cliquez sur **Enregistrer**.


<a id="next-step" class="xliff"></a>

## Étape suivante
[Tâche 3 : Activer Azure Active Directory Domain Services](active-directory-ds-getting-started-enableaadds.md)


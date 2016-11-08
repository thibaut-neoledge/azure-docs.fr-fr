---
title: Gérer les groupes de sécurité réseau à l’aide du portail en version préliminaire dans Resource Manager | Microsoft Docs
description: Découvrez comment gérer des groupes de sécurité réseau existants à l’aide du portail en version préliminaire dans Resource Manager
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2016
ms.author: jdial

---
# Gérer des groupes de sécurité réseau à l’aide du portail en version préliminaire
> [!div class="op_single_selector"]
> * [Portail](virtual-network-manage-nsg-arm-portal.md)
> * [PowerShell](virtual-network-manage-nsg-arm-ps.md)
> * [Interface de ligne de commande Azure](virtual-network-manage-nsg-arm-cli.md)
> 
> 

[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

le modèle de déploiement classique.

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## Récupérer des informations
Vous pouvez afficher vos groupes de sécurité réseau existants, récupérer des règles pour un groupe de sécurité réseau existant et découvrir quelles sont les ressources associées à un groupe de sécurité réseau.

### Afficher les groupes de sécurité réseau existants
Pour afficher tous les groupes de sécurité réseau existants d’un abonnement, procédez comme suit.

1. Dans un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous avec votre compte Azure.
2. Cliquez sur **Parcourir >** > **Groupes de sécurité réseau**.

![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure1.png)

1. Consultez la liste des groupes de sécurité réseau dans le panneau **Groupes de sécurité réseau**.

![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure2.png)

Pour afficher la liste des groupes de sécurité réseau dans le groupe de ressources **RG-NSG**, procédez comme suit.

1. Cliquez sur **Groupes de ressources >** > **RG-NSG** > **...**.

![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure3.png)

1. Dans la liste des ressources, recherchez des éléments qui affichent l’icône du groupe de sécurité réseau, comme indiqué dans le panneau **Ressources** ci-dessous.

![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure4.png)

### Répertorier toutes les règles pour un groupe de sécurité réseau
Pour afficher les règles d’un groupe de sécurité réseau nommé **NSG-FrontEnd**, procédez comme suit.

1. Dans le panneau **Groupes de sécurité réseau** ou le panneau **Ressources** ci-dessus, cliquez sur **NSG-FrontEnd**.
2. Sous l’onglet **Paramètres**, cliquez sur **Règles de sécurité de trafic entrant**.

![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure5.png)

1. Le panneau **Règles de sécurité de trafic entrant** s’affiche comme illustré ci-dessous.

![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure6.png)

1. Sous l’onglet **Paramètres**, cliquez sur **Règles de sécurité de trafic sortant** pour afficher les règles de trafic sortant.

> [!NOTE]
> Pour afficher les règles par défaut, cliquez sur l’icône **Règles par défaut** en haut du panneau qui affiche les règles.
> 
> 

### Afficher les associations de groupes de sécurité réseau
Pour afficher les ressources associées au groupe de sécurité réseau **NSG-FrontEnd**, procédez comme suit.

1. Dans le panneau **Groupes de sécurité réseau** ou le panneau **Ressources** ci-dessus, cliquez sur **NSG-FrontEnd**.
2. Sous l’onglet **Paramètres**, cliquez sur **Sous-réseaux** pour afficher les sous-réseaux associés au groupe de sécurité réseau.

![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure7.png)

1. Sous l’onglet **Paramètres**, cliquez sur **Interfaces réseau** pour afficher les cartes réseau associées au groupe de sécurité réseau.

## Gérer les règles
Vous pouvez ajouter des règles à un groupe de sécurité réseau existant, modifier des règles existantes et supprimer des règles.

### Ajouter une règle
Pour ajouter une règle autorisant le trafic **entrant** vers le port **443** à partir de n’importe quelle machine vers le groupe de sécurité réseau **NSG-FrontEnd**, procédez comme suit.

1. Dans le panneau **Groupes de sécurité réseau** ou le panneau **Ressources** ci-dessus, cliquez sur **NSG-FrontEnd**.
2. Sous l’onglet **Paramètres**, cliquez sur **Règles de sécurité de trafic entrant**.
3. Dans le panneau **Règles de sécurité de trafic entrant**, cliquez sur **Ajouter**. Ensuite, dans le panneau **Ajouter une règle de sécurité de trafic entrant**, remplissez les valeurs comme indiqué ci-dessous, puis cliquez sur **OK**.

![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure8.png)

1. Après quelques secondes, notez la nouvelle règle dans le panneau **Règles de sécurité de trafic entrant**.

![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure9.png)

### Modifier une règle
Pour modifier la règle créée précédemment qui permet d’autoriser le trafic entrant d’**Internet** uniquement, procédez comme suit.

1. Dans le panneau **Groupes de sécurité réseau** ou le panneau **Ressources** ci-dessus, cliquez sur **NSG-FrontEnd**.
2. Sous l’onglet **Paramètres**, cliquez sur la règle créée précédemment.
3. Dans le panneau **allow-https**, modifiez la propriété **Source** comme indiqué ci-dessous, puis cliquez sur **Enregistrer**.

![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure10.png)

### Supprimer une règle
Pour supprimer la règle créée précédemment, procédez comme suit.

1. Dans le panneau **Groupes de sécurité réseau** ou le panneau **Ressources** ci-dessus, cliquez sur **NSG-FrontEnd**.
2. Sous l’onglet **Paramètres**, cliquez sur la règle créée précédemment.
3. Dans le panneau **allow-https**, cliquez sur **Supprimer**, puis sur **Oui**.

![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure11.png)

## Gérer les associations
Vous pouvez associer un groupe de sécurité réseau à des cartes réseau et des sous-réseaux. Vous pouvez également dissocier un groupe de sécurité réseau de n’importe quelle ressource à laquelle il est associé.

### Associer un groupe de sécurité réseau à une carte réseau
Pour associer le groupe de sécurité réseau **NSG-FrontEnd** à la carte réseau **TestNICWeb1**, procédez comme suit.

1. Dans le panneau **Groupes de sécurité réseau** ou le panneau **Ressources** ci-dessus, cliquez sur **NSG-FrontEnd**.
2. Sous l’onglet **Paramètres**, cliquez sur **Interfaces réseau** > **Associer** > **TestNICWeb1**.

![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure12.png)

### Dissocier un groupe de sécurité réseau d’une carte réseau
Pour dissocier le groupe de sécurité réseau **NSG-FrontEnd** de la carte réseau **TestNICWeb1**, procédez comme suit.

1. Dans le portail Azure, cliquez sur **Groupes de ressources >** > **RG-NSG** > **...** > **TestNICWeb1**.
2. Dans le panneau **TestNICWeb1**, cliquez sur **Modifier la sécurité...** > **Aucune**.

![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure13.png)

> [!NOTE]
> Vous pouvez également utiliser ce panneau pour associer la carte réseau à n’importe quel groupe de sécurité réseau existant.
> 
> 

### Dissocier un groupe de sécurité réseau d’un sous-réseau
Pour dissocier le groupe de sécurité réseau **NSG-FrontEnd** du sous-réseau **FrontEnd**, procédez comme suit.

1. Dans le portail Azure, cliquez sur **Groupes de ressources >** > **RG-NSG** > **...** > **TestVNet**.
2. Dans le panneau **Paramètres**, cliquez sur **Sous-réseaux** > **FrontEnd** > **Groupe de sécurité réseau** > **Aucun**.

![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure14.png)

1. Dans le panneau **FrontEnd**, cliquez sur **Enregistrer**.

![Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure15.png)

### Association d’un groupe de sécurité réseau à un sous-réseau
Pour réassocier le groupe de sécurité réseau **NSG-FrontEnd** au sous-réseau **FrontEnd**, procédez comme suit.

1. Dans le portail Azure, cliquez sur **Groupes de ressources >** > **RG-NSG** > **...** > **TestVNet**.
2. Dans le panneau **Paramètres**, cliquez sur **Sous-réseaux** > **FrontEnd** > **Groupe de sécurité réseau** > **NSG-FrontEnd**.
3. Dans le panneau **FrontEnd**, cliquez sur **Enregistrer**.

> [!NOTE]
> Vous pouvez également associer un groupe de sécurité réseau à un sous-réseau à partir du panneau **Paramètres** du groupe de sécurité réseau.
> 
> 

## Suppression d'un groupe de sécurité réseau
Vous ne pouvez supprimer un groupe de sécurité réseau que s’il n’est associé à aucune ressource. Pour supprimer un groupe de sécurité réseau, procédez comme suit.

1. Dans le portail Azure, cliquez sur **Groupes de ressources >** > **RG-NSG** > **...** > **NSG-FrontEnd**.
2. Dans le panneau **Paramètres**, cliquez sur **Interfaces réseau**.
3. Si des cartes réseau sont répertoriées, cliquez sur la carte réseau et suivez l’étape 2 de [Dissocier un groupe de sécurité réseau d’une carte réseau](#Dissociate-an-NSG-from-a-NIC).
4. Répétez l’étape 3 pour chaque carte réseau.
5. Dans le panneau **Paramètres**, cliquez sur **Sous-réseaux**.
6. Si des sous-réseaux sont répertoriés, cliquez sur le sous-réseau et suivez les étapes 2 et 3 de [Dissocier un groupe de sécurité réseau d’un sous-réseau](#Dissociate-an-NSG-from-a-subnet).
7. Faites défiler à gauche vers le panneau **NSG-FrontEnd**, puis cliquez sur **Supprimer** > **Oui**.

[Portail Azure - Groupes de sécurité réseau](./media/virtual-network-manage-nsg-arm-portal/figure16.png)

## Étapes suivantes
* [Activez la journalisation](virtual-network-nsg-manage-log.md) des groupes de sécurité réseau.

<!---HONumber=AcomDC_0914_2016-->
<properties 
   pageTitle="Définition d'une adresse IP privée statique en mode ARM à l'aide du portail Azure | Microsoft Azure"
   description="Présentation des adresses IP privées (adresses IP dynamiques) et de leur gestion en mode ARM à l'aide du portail Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="jdial" />

# Définition d'une adresse IP privée statique dans le portail Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] Cet article traite du modèle de déploiement de Resource Manager. Vous pouvez également [gérer une adresse IP privée statique dans le modèle de déploiement classique](virtual-networks-static-private-ip-classic-pportal.md).

[AZURE.INCLUDE [réseaux-virtuels-statique-scénario-ip-inclut](../../includes/virtual-networks-static-ip-scenario-include.md)]

Les étapes de l’exemple ci-dessous supposent qu’un environnement simple a déjà été créé. Si vous souhaitez exécuter les étapes telles qu’elles sont présentées dans ce document, commencez par créer l’environnement de test décrit dans [Créer un réseau virtuel](virtual-networks-create-vnet-arm-pportal.md).

## Création d’une machine virtuelle pour tester des adresses IP privées statiques

Vous ne pouvez pas définir une adresse IP privée statique lors de la création d'une machine virtuelle dans le mode de déploiement Resource Manager à l'aide du portail Azure. Vous devez d’abord créer la machine virtuelle, puis définir son adresse IP privée de façon à ce qu’elle soit statique.

Pour créer une machine virtuelle nommée *DNS01* dans le sous-réseau *frontal* d’un réseau virtuel nommé *TestVNet*, procédez comme suit.

1. Dans un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous avec votre compte Azure.
2. Cliquez sur **Nouveau** > **Ordinateur** > **Windows Server 2012 R2 Datacenter** (notez que la liste **Sélectionner un modèle de déploiement** contient déjà **Resource Manager**), puis cliquez sur **Créer**, comme illustré dans la figure ci-dessous.

	![Création d'une machine virtuelle dans le portail Azure](./media/virtual-networks-static-ip-arm-pportal/figure01.png)

3. Dans le panneau **Informations de base**, entrez le nom de la machine virtuelle à créer (*DNS01* dans notre scénario), le compte d’administrateur local et un mot de passe, comme illustré dans la figure ci-dessous.

	![Panneau Informations de base](./media/virtual-networks-static-ip-arm-pportal/figure02.png)

4. Assurez-vous que l’**Emplacement** sélectionné est *Centre des États-Unis*. Sous **Groupe de ressources**, cliquez sur **Sélectionner un groupe existant**, cliquez de nouveau sur **Groupe de ressources**, cliquez sur *TestRG*, puis sur **OK**.

	![Panneau Informations de base](./media/virtual-networks-static-ip-arm-pportal/figure03.png)

5. Dans le panneau **Choisir une taille**, sélectionnez **A1 Standard**, puis cliquez sur **Sélectionner**.

	![Panneau Choisir une taille](./media/virtual-networks-static-ip-arm-pportal/figure04.png)

6. Dans le panneau **Paramètres** lame, assurez-vous que les propriétés suivantes sont définies sont définies avec les valeurs ci-dessous, puis cliquez sur **OK**.

	-**Compte de stockage**: *vnetstorage*
	- **Réseau**: *TestVNet*
	- **Sous-réseau**: *FrontEnd*

	![Panneau Choisir une taille](./media/virtual-networks-static-ip-arm-pportal/figure05.png)

7. Dans le panneau **Résumé**, cliquez sur **OK**. Notez la vignette ci-dessous affichée dans votre tableau de bord.

	![Création d'une machine virtuelle dans le portail Azure](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

## Récupération d’informations d’adresse IP privée statique pour une machine virtuelle

Pour afficher les informations d’adresse IP privée statique de la machine virtuelle créée lors des étapes ci-dessus, exécutez les étapes ci-dessous.

1. Dans le portail Azure, cliquez sur **PARCOURIR TOUT** > **Machines virtuelles** > **DNS01** > **Tous les paramètres** > **Interfaces réseau**, puis cliquez sur la seule interface réseau répertoriée.

	![Déploiement d’une vignette de machine virtuelle](./media/virtual-networks-static-ip-arm-pportal/figure07.png)

2. Dans le panneau **Interface réseau**, cliquez sur **Tous les paramètres** > **Adresses IP**, puis notez les valeurs **Affectation** et **Adresse IP**.

	![Déploiement d’une vignette de machine virtuelle](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## Ajout d’une adresse IP privée statique à une machine virtuelle existante
Pour ajouter une adresse IP privée statique à la machine virtuelle créée lors des étapes ci-dessus, exécutez les étapes ci-dessous :

1. Dans le panneau **Adresses IP** illustré ci-dessus, sous **Affectation**, cliquez sur **Statique**.
2. Tapez *192.168.1.101* dans **Adresse IP**, puis cliquez sur **Enregistrer**.

	![Création d'une machine virtuelle dans le portail Azure](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

>[AZURE.NOTE] Si, après avoir cliqué sur **Enregistrer**, vous remarquez que l’affectation est toujours définie sur **Dynamique**, cela signifie que l’adresse IP que vous avez tapée est déjà en cours d’utilisation. Essayez une autre adresse IP.

## Suppression d’une adresse IP privée statique d’une machine virtuelle
Pour supprimer l’adresse IP privée statique de la machine virtuelle créée ci-dessus, procédez comme suit.
	
1. Dans le panneau **Adresses IP** illustré ci-dessus, sous **Affectation**, cliquez sur **Dynamique**, puis sur **Enregistrer**.

## Étapes suivantes

- En savoir plus sur les [adresses IP publiques réservées](virtual-networks-reserved-public-ip.md).
- En savoir plus sur les [adresses IP publiques de niveau d’instance](virtual-networks-instance-level-public-ip.md).
- Consulter les [API REST d’adresse IP réservée](https://msdn.microsoft.com/library/azure/dn722420.aspx).

<!---HONumber=AcomDC_0810_2016-->
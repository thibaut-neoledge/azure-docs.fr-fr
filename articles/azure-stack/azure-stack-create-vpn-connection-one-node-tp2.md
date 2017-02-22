---
title: "Créer une connexion VPN de site à site entre deux réseaux virtuels dans des environnements Azure Stack POC différents | Microsoft Docs"
description: "Procédure pas à pas qui permet à un administrateur de cloud de créer une connexion VPN de site à site entre deux environnements POC à un nœud dans TP2."
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 3f1b4e02-dbab-46a3-8e11-a777722120ec
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/08/2017
ms.author: scottnap
translationtype: Human Translation
ms.sourcegitcommit: 5104c7996de9dc0597e65be31c28a9aaa1243a90
ms.openlocfilehash: d324290caf5b5a085a2daf67e541c295dffda732


---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-poc-environments"></a>Créer une connexion VPN de site à site entre deux réseaux virtuels dans des environnements Azure Stack POC différents
## <a name="overview"></a>Vue d’ensemble
Cet article vous montre comment créer une connexion VPN de site à site entre deux réseaux virtuels dans deux environnements Azure Stack Proof-of-Concept (POC) distincts. Pendant la configuration des connexions, vous allez découvrir le fonctionnent les passerelles VPN d’Azure Stack.

> [!NOTE]
> Ce document s’applique plus particulièrement à Azure Stack TP2 POC.
> 
> 

### <a name="connection-diagram"></a>Diagramme de connexions
Le diagramme suivant illustre à quoi la configuration doit ressembler lorsque vous avez terminé :

![](media/azure-stack-create-vpn-connection-one-node-tp2/image1.png)

### <a name="before-you-begin"></a>Avant de commencer
Pour effectuer cette configuration, vérifiez que vous disposez des éléments suivants avant de commencer :

* Deux serveurs conformes aux exigences matérielles Azure Stack POC définies par les [Conditions préalables au déploiement Azure Stack](azure-stack-deploy.md) et aux autres conditions préalables définies par ce document.
* Le package de déploiement Azure Stack Technical Preview 2.

## <a name="deploy-the-poc-environments"></a>Déployer les environnements POC
Vous allez déployer les deux environnements Azure Stack POC pour effectuer cette configuration.

* Pour chaque POC que vous déployez, vous pouvez suivre les instructions de déploiement détaillées dans l’article [Déployer Azure Stack POC](azure-stack-run-powershell-script.md).
  Chaque environnement POC dans ce document est appelé *POC1* et *POC2*.

## <a name="configure-quotas-for-compute-network-and-storage"></a>Configurer les quotas de calcul, de réseau et de stockage
Vous devez tout d’abord configurer des *Quotas* de calcul, de réseau et de stockage. Ces services peuvent être associés à un *Plan*, puis à une *Offre* à laquelle les locataires peuvent s’abonner.

> [!NOTE]
> Vous devez effectuer ces étapes pour chaque environnement Azure Stack POC.
> 
> 

La création de quotas pour des services a changé par rapport à TP1. Vous trouverez les étapes de création de quotas dans TP2 à l’adresse <http://aka.ms/mas-create-quotas>. Vous pouvez accepter les valeurs par défaut pour tous les paramètres de quota pour cet exercice.

## <a name="create-a-plan-and-offer"></a>Créer un plan et une offre
Les [plans](azure-stack-key-features.md) regroupent un ou plusieurs services. En tant que fournisseur, vous pouvez créer des plans à proposer à vos locataires. En retour, les clients s’abonnent à vos offres pour utiliser les plans et les services inclus.

> [!NOTE]
> Vous devez effectuer ces étapes pour chaque environnement Azure Stack POC.
> 
> 

1. Créez tout d’abord un plan. Pour cela, vous pouvez suivre les étapes décrites dans l’article en ligne [Créer un plan](azure-stack-create-plan.md).
2. Créez une offre en suivant les étapes décrites dans [Créer une offre dans Azure Stack](azure-stack-create-offer.md).
3. Connectez-vous au portail en tant qu’un administrateur de locataires et [abonnez-vous à l’offre que vous avez créée](azure-stack-subscribe-plan-provision-vm.md).

## <a name="create-the-network-resources-in-poc-1"></a>Créer les ressources réseau dans POC 1
Vous allez maintenant créer les ressources dont vous avez besoin pour effectuer votre configuration. Les étapes suivantes illustrent ce que vous allez faire. Ces instructions indiquent comment créer des ressources à l’aide du portail, mais vous pouvez également le faire à l’aide de PowerShell.

![](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="log-in-as-a-tenant"></a>Se connecter en tant que locataire
Un administrateur de service peut se connecter en tant que locataire pour tester les plans, les offres et les abonnements que ses locataires peuvent utiliser. Si vous n’en possédez pas déjà, commencez par [Créer un compte de locataire](azure-stack-add-new-user-aad.md) avant de vous connecter.

### <a name="create-the-virtual-network--vm-subnet"></a>Créer le réseau virtuel et le sous-réseau de machine virtuelle
1. Connectez-vous en utilisant un compte de locataire.
2. Dans le portail Azure, cliquez sur **Nouveau**.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image3.png)
3. Sélectionnez **Mise en réseau** dans le menu Marketplace.
4. Cliquez sur l’élément **Réseau virtuel** dans le menu.
5. Cliquez sur **Créer** en bas du panneau description de la ressource. Entrez les valeurs suivantes dans les champs appropriés en fonction de ce tableau.
   
   | **Champ** | **Valeur** |
   | --- | --- |
   | Nom |vnet-01 |
   | Espace d’adressage |10.0.10.0/23 |
   | Nom du sous-réseau |subnet-01 |
   | Plage d’adresses de sous-réseau |10.0.10.0/24 |
6. Vous devez voir l’abonnement que vous avez créé précédemment rempli dans le champ **Abonnement**.
7. Pour le groupe de ressources, vous pouvez créer un nouveau groupe de ressources ou, si vous avez déjà un, sélectionner **Utiliser existant**.
8. Vérifiez l’emplacement par défaut.
9. Cliquez sur **Créer**.

### <a name="create-the-gateway-subnet"></a>Créer le sous-réseau de passerelle
1. Ouvrez la ressource de réseau virtuel que vous venez de créer (Vnet-01) à partir du tableau de bord.
2. Dans le panneau Paramètres, sélectionnez **Sous-réseaux**.
3. Cliquez sur **Sous-réseau de passerelle** pour ajouter un sous-réseau de passerelle au réseau virtuel.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)
4. Par défaut, le nom du sous-réseau est défini sur **GatewaySubnet**.
   Les sous-réseaux de passerelle sont des éléments spéciaux et doivent porter ce nom pour fonctionner correctement.
5. Dans le champ **Plage d’adresses**, entrez **10.0.11.0/24**.
6. Cliquez sur **Créer** pour créer le sous-réseau de passerelle.

### <a name="create-the-virtual-network-gateway"></a>Créer la passerelle de réseau virtuel
1. Dans le portail Azure, cliquez sur **Nouveau**.
   
2. Sélectionnez **Mise en réseau** dans le menu Marketplace.
3. Sélectionnez **Passerelle de réseau virtuel** dans la liste des ressources réseau.
4. Consultez la description et cliquez sur **Créer**.
5. Dans le champ **nom**, entrez **GW1**.
6. Cliquez sur l’élément **Réseau virtuel** pour choisir un réseau virtuel.
   Sélectionnez **Vnet-01** dans la liste.
7. Cliquez sur l’élément de menu **Adresse IP publique**. Lorsque le panneau **Choisir une adresse IP publique** s’affiche, cliquez sur **Créer**.
8. Dans le champ **Nom**, entrez **GW1-PiP**, puis cliquez sur **OK**.
9. Par défaut, le **type de passerelle** doit être défini sur **VPN**. Conservez ce paramètre.
10. Par défaut, le **type de VPN** doit être défini sur **Basé sur un itinéraire**.
    Conservez ce paramètre.
11. Vérifiez que l’**abonnement** et l’**emplacement** sont corrects. Vous pouvez épingler la ressource au tableau de bord si vous le souhaitez. Cliquez sur **Create**.

### <a name="create-the-local-network-gateway"></a>Créer la passerelle de réseau local
L’implémentation d’une *passerelle de réseau local* dans ce déploiement d’évaluation d’Azure Stack est un peu différente dans un déploiement Azure réel.

Le concept de passerelle de réseau local est présent comme dans Azure. Toutefois, dans un déploiement Azure, une passerelle de réseau local représente un appareil physique local (sur le locataire) que vous utilisez pour vous connecter à une passerelle de réseau virtuel dans Azure. Mais dans ce déploiement d’évaluation d’Azure Stack, les deux extrémités de la connexion sont des passerelles de réseau virtuel !

Une approche plus générique consiste à penser que la ressource de passerelle de réseau local représente toujours la passerelle distante à l’autre extrémité de la connexion. En raison de la méthode de conception du POC, vous devez fournir l’adresse IP de la carte réseau externe sur la machine virtuelle NAT de l’autre POC en tant qu’adresse IP publique de la passerelle de réseau local. Vous allez ensuite créer des mappages NAT sur la machine virtuelle NAT pour vous assurer que les deux extrémités sont correctement connectées.


### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Obtenir l’adresse IP de la carte externe de la machine virtuelle NAT
1. Connectez-vous à l’ordinateur physique Azure Stack pour POC2.
2. Appuyez sur [touche Windows] + R pour ouvrir le menu **Exécuter**, entrez **mstsc**, puis appuyez sur Entrée.
3. Dans le champ **Ordinateur**, entrez le nom **MAS-BGPNAT01**, puis cliquez sur  **Connecter**.
4. Cliquez sur le menu Démarrer, cliquez avec le bouton droit sur **Windows PowerShell**, puis sélectionnez **Exécuter en tant qu’administrateur**.
5. Entrez **ipconfig/all**.
6. Recherchez la carte Ethernet connectée à votre réseau local, puis notez l’adresse IPv4 liée à cette carte. Dans notre exemple d’environnement, l’adresse est **10.16.167.195**, mais la vôtre peut être légèrement différente.
7. Enregistrez cette adresse. Vous l’utiliserez comme adresse IP publique de la ressource de passerelle de réseau local que vous créez dans POC1.

### <a name="create-the-local-network-gateway-resource"></a>Créer la ressource de passerelle de réseau local
1. Connectez-vous à l’ordinateur physique Azure Stack pour POC1.
2. Dans le champ **Ordinateur**, entrez le nom **MAS-CON01**, puis cliquez sur **Connecter**.
3. Dans le portail Azure, cliquez sur **Nouveau**.
   
4. Sélectionnez **Mise en réseau** dans le menu Marketplace.
5. Sélectionnez **Passerelle de réseau local** dans la liste des ressources.
6. Dans le champ **Nom**, entrez **POC2-GW**.
7. Vous ne connaissez pas encore l’adresse IP de l’autre passerelle, mais ceci n’est pas un problème puisque vous pouvez revenir et la modifier ultérieurement. Pour l’instant, entrez **10.16.167.195** dans le champ **Adresse IP**.
8. Dans le champ **Espace d’adressage**, entrez l’espace d’adressage du réseau virtuel que vous allez créer dans POC2. Il sera **10.0.20.0/23**, entrez donc cette valeur.
9. Vérifiez que vos **Abonnement**, **Groupe de ressources** et **Emplacement** sont corrects, puis cliquez sur **créer**.

### <a name="create-the-connection"></a>Créer la connexion
1. Dans le portail Azure, cliquez sur **Nouveau**.
   
2. Sélectionnez **Mise en réseau** dans le menu Marketplace.
3. Sélectionnez **Connexion** dans la liste des ressources.
4. Dans le panneau de paramètres **De base**, choisissez **Site à site (IPSec)** comme **Type de connexion**.
5. Sélectionnez l’**Abonnement**, le **Groupe de ressources** et l’**Emplacement**, puis cliquez sur **OK**.
6. Dans le panneau **Paramètres**, choisissez la **Passerelle de réseau virtuel** (**GW1**) que vous avez créée précédemment.
7. Choisissez la **Passerelle de réseau local** (**POC2-GW**) que vous créée précédemment.
8. Dans le champ **Nom de la connexion**, entrez **POC1-POC2**.
9. Dans le champ **Clé partagée (PSK)**, entrez **12345**, puis cliquez sur **OK**.

### <a name="create-a-vm"></a>Créer une machine virtuelle
Pour valider les données transitant via la connexion VPN, vous avez besoin de machines virtuelles pour envoyer et recevoir des données dans chaque POC. Créez une machine virtuelle dans POC1 et placez-la sur votre sous-réseau de machine virtuelle dans votre réseau virtuel.

1. Dans le portail Azure, cliquez sur **Nouveau**.
   
2. Sélectionnez **Machines virtuelles** dans le menu Marketplace.
3. Dans la liste des images de machine virtuelle, sélectionnez l’image **Windows Server 2012 R2 Datacenter**.
4. Dans le panneau **De base**, dans le champ **Nom**, entrez **VM01**.
5. Entrez un nom d’utilisateur et un mot de passe valides. Vous utiliserez ce compte pour vous connecter à la machine virtuelle une fois qu’elle aura été créée.
6. Indiquez un **Abonnement**, un **Groupe de ressources** et un **Emplacement**, puis cliquez sur **OK**.
7. Dans le panneau **Taille**, choisissez une taille de machine virtuelle pour cette instance, puis cliquez sur **Sélectionner**.
8. Dans le panneau **Paramètres**, vous pouvez accepter les valeurs par défaut ; vérifiez simplement que le réseau virtuel sélectionné est **VNET-01** et que le sous-réseau est défini sur **10.0.10.0/24**. Cliquez sur **OK**.
9. Vérifiez les paramètres dans le panneau **Résumé**, puis cliquez sur **OK**.

## <a name="create-the-network-resources-in-poc-2"></a>Créer les ressources réseau dans POC 2
### <a name="log-in-as-a-tenant"></a>Se connecter en tant que locataire
Un administrateur de service peut se connecter en tant que locataire pour tester les plans, les offres et les abonnements que ses locataires peuvent utiliser. Si vous n’en possédez pas déjà, commencez par [Créer un compte de locataire](azure-stack-add-new-user-aad.md) avant de vous connecter.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Créer le réseau virtuel et le sous-réseau de machine virtuelle
1. Connectez-vous en utilisant un compte de locataire.
2. Dans le portail Azure, cliquez sur **Nouveau**.
   
3. Sélectionnez **Mise en réseau** dans le menu Marketplace.
4. Cliquez sur **Réseau virtuel** dans le menu.
5. Cliquez sur **Créer** en bas du panneau description de la ressource. Entrez les valeurs suivantes pour les champs appropriés, répertoriés dans le tableau ci-dessous.
   
   | **Champ** | **Valeur** |
   | --- | --- |
   | Nom |vnet-02 |
   | Espace d’adressage |10.0.20.0/23 |
   | Nom du sous-réseau |subnet-02 |
   | Plage d’adresses de sous-réseau |10.0.20.0/24 |
6. Vous devez voir l’abonnement que vous avez créé précédemment rempli dans le champ **Abonnement**.
7. Pour le groupe de ressources, vous pouvez créer un nouveau groupe de ressources ou, si vous avez déjà un, sélectionner **Utiliser existant**.
8. Vérifiez l’**emplacement** par défaut. Si vous le souhaitez, vous pouvez épingler le réseau virtuel au tableau de bord pour y accéder facilement.
9. Cliquez sur **Create**.

### <a name="create-the-gateway-subnet"></a>Créer le sous-réseau de passerelle
1. Ouvrez la ressource de réseau virtuel que avez créée (**Vnet-02**) à partir du tableau de bord.
2. Dans le panneau **Paramètres**, sélectionnez **Sous-réseaux**.
3. Cliquez sur **Sous-réseau de passerelle** pour ajouter un sous-réseau de passerelle au réseau virtuel.
   
4. Par défaut, le nom du sous-réseau est défini sur **GatewaySubnet**.
   Les sous-réseaux de passerelle sont des éléments spéciaux et doivent porter ce nom pour fonctionner correctement.
5. Dans le champ **Plage d’adresses**, entrez **10.0.20.0/24**.
6. Cliquez sur **Créer** pour créer le sous-réseau de passerelle.

### <a name="create-the-virtual-network-gateway"></a>Créer la passerelle de réseau virtuel
1. Dans le portail Azure, cliquez sur **Nouveau**.
   
2. Sélectionnez **Mise en réseau** dans le menu Marketplace.
3. Sélectionnez **Passerelle de réseau virtuel** dans la liste des ressources réseau.
4. Consultez la description et cliquez sur **Créer**.
5. Dans le champ **Nom**, entrez **GW2**.
6. Cliquez sur **Réseau virtuel** pour choisir un réseau virtuel.
   Sélectionnez **Vnet-02** dans la liste.
7. Cliquez sur **Adresse IP publique**. Lorsque le panneau **Choisir une adresse IP publique** s’affiche, cliquez sur **Créer**.
8. Dans le champ **Nom**, entrez **GW2-PiP**, puis cliquez sur **OK**.
9. Par défaut, le **type de passerelle** doit être défini sur **VPN**. Conservez ce paramètre.
10. Par défaut, le **type de VPN** doit être défini sur **Basé sur un itinéraire**.
    Conservez ce paramètre.
11. Vérifiez que l’**abonnement** et l’**emplacement** sont corrects. Vous pouvez épingler la ressource au tableau de bord si vous le souhaitez. Cliquez sur **Créer**.

### <a name="create-the-local-network-gateway"></a>Créer la passerelle de réseau local
#### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Obtenir l’adresse IP de la carte externe de la machine virtuelle NAT
1. Connectez-vous à l’ordinateur physique Azure Stack pour POC1.
2. Maintenez les touches [touche Windows] + R enfoncées pour ouvrir le menu **Exécuter**, entrez **mstsc**, puis appuyez sur Entrée.
3. Dans le champ **Ordinateur**, entrez le nom **MAS-BGPNAT01**, puis cliquez sur  **Connecter**.
4. Cliquez sur le menu Démarrer, cliquez avec le bouton droit sur **Windows PowerShell**, puis sélectionnez **Exécuter en tant qu’administrateur**.
5. Entrez **ipconfig/all**.
6. Recherchez la carte Ethernet connectée à votre réseau local, puis notez l’adresse IPv4 liée à cette carte. Dans l’exemple d’environnement, l’adresse est **10.16.169.131**, mais la vôtre sera différente.
7. Enregistrez cette adresse. Vous l’utiliserez ultérieurement comme adresse IP publique de la ressource de passerelle de réseau local que vous créez dans POC1.

#### <a name="create-the-local-network-gateway-resource"></a>Créer la ressource de passerelle de réseau local
1. Connectez-vous à l’ordinateur physique Azure Stack pour POC2.
2. Dans le champ **Ordinateur**, entrez le nom **MAS-CON01**, puis cliquez sur **Connecter**.
3. Dans le portail Azure, cliquez sur **Nouveau**.
   
4. Sélectionnez **Mise en réseau** dans le menu Marketplace.
5. Sélectionnez **Passerelle de réseau local** dans la liste des ressources.
6. Dans le champ **Nom**, entrez **POC1-GW**.
7. Vous avez maintenant besoin de l’adresse IP publique que vous avez enregistrée pour la passerelle de réseau virtuel dans POC1. Entrez **10.16.169.131** dans le champ **Adresse IP**.
8. Dans le champ **Espace d’adressage**, entrez l’espace d’adressage de **Vnet-01** de POC1 : **10.0.0.0/16**.
9. Vérifiez que vos **Abonnement**, **Groupe de ressources** et **Emplacement** sont corrects, puis cliquez sur **créer**.

## <a name="create-the-connection"></a>Créer la connexion
1. Dans le portail Azure, cliquez sur **Nouveau**.
   
2. Sélectionnez **Mise en réseau** dans le menu Marketplace.
3. Sélectionnez **Connexion** dans la liste des ressources.
4. Dans le panneau de paramètres **De base**, choisissez **Site à site (IPSec)** comme **Type de connexion**.
5. Sélectionnez l’**Abonnement**, le **Groupe de ressources** et l’**Emplacement**, puis cliquez sur **OK**.
6. Dans le panneau **Paramètres**, choisissez la **Passerelle de réseau virtuel** (**GW1**) que vous avez créée précédemment.
7. Choisissez la **Passerelle de réseau local** (**POC1-GW**) que vous créée précédemment.
8. Dans le champ **Nom de la connexion**, entrez **POC2-POC1**.
9. Dans le champ **Clé partagée (PSK)**, entrez **12345**. Si vous choisissez une autre valeur, n’oubliez pas qu’elle DOIT correspondre à la valeur de clé partagée que vous avez créée sur POC1. Cliquez sur **OK**.

## <a name="create-a-vm"></a>Créer une machine virtuelle
Créez une machine virtuelle dans POC1 et placez-la sur votre sous-réseau de machine virtuelle dans votre réseau virtuel.

1. Dans le portail Azure, cliquez sur **Nouveau**.
   
2. Sélectionnez **Machines virtuelles** dans le menu Marketplace.
3. Dans la liste des images de machine virtuelle, sélectionnez l’image **Windows Server 2012 R2 Datacenter**.
4. Dans le panneau **De base**, dans le champ **Nom**, entrez **VM02**.
5. Entrez un nom d’utilisateur et un mot de passe valides. Vous utiliserez ce compte pour vous connecter à la machine virtuelle une fois qu’elle aura été créée.
6. Indiquez un **Abonnement**, un **Groupe de ressources** et un **Emplacement**, puis cliquez sur **OK**.
7. Dans le panneau **Taille**, choisissez une taille de machine virtuelle pour cette instance, puis cliquez sur **Sélectionner**.
8. Dans le panneau Paramètres, vous pouvez accepter les valeurs par défaut ; vérifiez simplement que le réseau virtuel sélectionné est **VNET-02** et que le sous-réseau est défini sur **20.0.0.0/24**. Cliquez sur **OK**.
9. Vérifiez les paramètres dans le panneau **Résumé**, puis cliquez sur **OK**.

## <a name="configure-the-nat-vm-in-each-poc-for-gateway-traversal"></a>Configurer la machine virtuelle NAT dans chaque POC pour la traversée de passerelle
Étant donné que le POC a été conçu pour se définir lui-même et être isolé du réseau sur lequel l’hôte physique est déployé, le réseau VIP « Externe » auquel les passerelles sont connectées n’est pas réellement externe, mais est masqué derrière un routeur effectuant la traduction d’adresses réseau (NAT). En réalité, le routeur est une machine virtuelle Windows Server (**MAS-BGPNAT01**) exécutant le rôle RRAS (Routing and Remote Access Services) dans l’infrastructure POC. Vous devez configurer la traduction d’adresses réseau (NAT) sur la machine virtuelle MAS-BGPNAT01 pour permettre à la connexion VPN de site à site de se connecter aux deux extrémités. Pour cela, vous devez créer un mappage NAT statique qui mappe l’interface externe sur la machine virtuelle BGPNAT avec l’adresse IP virtuelle du pool de passerelles Edge pour les ports requis pour une connexion VPN.

> [!NOTE]
> Cette configuration est requise pour les environnements POC uniquement.
> 
> 

### <a name="configure-nat"></a>Configurer NAT
Vous devez suivre ces étapes dans les DEUX environnements POC.

1. Connectez-vous à l’ordinateur physique Azure Stack pour POC1.
2. Maintenez les touches [touche Windows] + R enfoncées pour ouvrir le menu **Exécuter**, entrez **mstsc**, puis appuyez sur **Entrée**.
3. Dans le champ **Ordinateur**, entrez le nom **MAS-BGPNAT01**, puis cliquez sur **Connecter**.
4. Cliquez sur le menu Démarrer, cliquez avec le bouton droit sur **Windows PowerShell**, puis sélectionnez **Exécuter en tant qu’administrateur**.
5. Entrez **ipconfig/all**.
6. Recherchez la carte Ethernet connectée à votre réseau local, puis notez l’adresse IPv4 liée à cette carte. Dans l’exemple d’environnement, l’adresse est **10.16.169.131** (dans le cercle rouge ci-dessous), mais la vôtre sera différente.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image16.png)
7. Entrez la commande PowerShell suivante pour désigner l’adresse NAT externe pour les ports de l’authentification IKE. N’oubliez pas de remplacer l’adresse IP par une adresse correspondant à votre environnement.
   
       Add-NetNatExternalAddress -NatName BGPNAT -IPAddress 10.16.169.131 -PortStart 499 -PortEnd 501
8. Vous créez ensuite un mappage NAT statique pour mapper l’adresse externe avec l’adresse IP publique de passerelle afin de mapper le port ISAKMP 500 pour la PHASE 1 du tunnel IPSEC.
   
        Add-NetNatStaticMapping -NatName BGPNAT -Protocol UDP -ExternalIPAddress 10.16.169.131 -InternalIPAddress 192.168.102.1 -ExternalPort 500 -InternalPort 500
> [!NOTE] 
> Le paramètre `-InternalAddress` dans cet exemple correspond à l’adresse IP publique de la passerelle de réseau virtuel que vous avez créée précédemment.  Pour trouver cette adresse IP, examinez les propriétés du panneau Passerelle de réseau virtuel et recherchez la valeur de l’adresse IP publique.       

9. Enfin, vous devez configurer une traversée NAT qui utilise le port 4500 pour établir le tunnel IPEC complet sur les appareils NAT.
   
        Add-NetNatStaticMapping -NatName BGPNAT -Protocol UDP -ExternalIPAddress 10.16.169.131 -InternalIPAddress 192.168.102.1 -ExternalPort 4500 -InternalPort 4500
> [!NOTE] 
> Le paramètre `-InternalAddress` dans cet exemple correspond à l’adresse IP publique de la passerelle de réseau virtuel que vous avez créée précédemment.  Pour trouver cette adresse IP, examinez les propriétés du panneau Passerelle de réseau virtuel et recherchez la valeur de l’adresse IP publique.       

10. Répétez les étapes 1 à 9 dans POC2.

## <a name="test-the-connection"></a>Tester la connexion
Maintenant que la connexion de site à site a été établie, vous devez vérifier qu’un trafic transite par l’intermédiaire de celle-ci. Cette tâche est simple car elle implique simplement de se connecter à l’un des machines virtuelles que vous avez créées dans un environnement POC et à exécuter une commande ping sur la machine virtuelle que vous avez créée dans l’autre environnement. Pour vérifier que le trafic passe par la connexion de site à site, exécutez une commande ping sur l’adresse IP directe (DIP) de la machine virtuelle du sous-réseau distant, et non sur l’adresse IP virtuelle. Pour cela, vous devez rechercher et noter l’adresse à l’autre extrémité de la connexion.

### <a name="log-in-to-the-tenant-vm-in-poc1"></a>Se connecter à la machine virtuelle de locataire dans POC1
1. Connectez-vous à la machine physique Azure Stack pour POC1 et connectez-vous au portail à l’aide d’un compte locataire.
2. Cliquez sur **Machines virtuelles** dans la barre de navigation de gauche.
3. Recherchez **VM01** que vous avez créé précédemment dans la liste des machines virtuelles et cliquez dessus.
4. Dans le panneau de la machine virtuelle, cliquez sur **Connecter**.
   
     ![](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. Ouvrez une invite de commandes sur la machine virtuelle et entrez **ipconfig/all**.
6. Recherchez l’**adresse IPv4** dans la sortie et notez-la. Il s’agit de l’adresse sur laquelle vous exécuterez une commande ping à partir de POC2. Dans l’exemple d’environnement, l’adresse est **10.0.10.4**, mais peut être différente dans votre environnement. Elle doit cependant se trouver dans le sous-réseau **10.0.10.0/24** qui a été créé précédemment.

### <a name="log-in-to-the-tenant-vm-in-poc2"></a>Se connecter à la machine virtuelle de locataire dans POC2
1. Connectez-vous à la machine physique Azure Stack pour POC2 et connectez-vous au portail à l’aide d’un compte locataire.
2. Cliquez sur **Machines virtuelles** dans la barre de navigation de gauche.
3. Recherchez **VM02** que vous avez créé précédemment dans la liste des machines virtuelles et cliquez dessus.
4. Dans le panneau de la machine virtuelle, cliquez sur **Connecter**.
   
5. Ouvrez une invite de commandes sur la machine virtuelle et entrez **ipconfig/all**.
6. Vous devez voir une adresse IPv4 qui se trouve dans 10.0.20.0/24. Dans l’exemple d’environnement, l’adresse est 10.0.20.4, mais la vôtre peut être différente.
7. À partir de la machine virtuelle dans POC2, vous souhaitez maintenant exécuter une commande ping sur la machine virtuelle dans POC1, via le tunnel. Pour cela, vous exécutez une commande ping sur le DIP que vous avez enregistré à partir de VM01.
   Dans l’exemple d’environnement, l’adresse est 10.0.10.4, mais exécutez une commande ping sur l’adresse que vous avez notée dans votre laboratoire. Un résultat similaire à celui illustré ci-dessous apparaît :
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
8. Une réponse de la machine virtuelle distante indique que le test a réussi ! Vous pouvez fermer la fenêtre de connexion de la machine virtuelle. Vous pouvez également essayer d’autres transferts de données comme une copie de fichier pour tester votre connexion.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Affichage des statistiques de transfert de données via la connexion de passerelle
Si vous souhaitez connaître la quantité de données qui transite par votre connexion de site à site, ces informations sont disponibles dans le panneau de connexion. Ce test est également un bon moyen pour vérifier que la commande ping que vous venez d’envoyer est effectivement passée par la connexion VPN.

1. Toujours connecté à la machine virtuelle de locataire dans POC2, connectez-vous au **portail Microsoft Azure Stack POC** à l’aide de votre compte locataire.
2. Cliquez sur l’élément de menu **Parcourir** et sélectionnez **Connexions**.
3. Cliquez sur la connexion **POC2-POC1** dans la liste.
4. Dans le panneau Connexion, vous pouvez visualiser les statistiques de **données entrantes** et de **données sortantes**. Dans la capture d’écran suivante, vous voyez des nombres plus importants que ceux créés par une simple commande ping. Ceci est dû aux transferts de fichiers supplémentaires. Vous devriez voir des valeurs non nulles.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)




<!--HONumber=Feb17_HO2-->



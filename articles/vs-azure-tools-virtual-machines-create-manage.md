<properties
   pageTitle="Créer et gérer des machines virtuelles Azure dans Visual Studio | Microsoft Azure"
   description="Créer et gérer des machines virtuelles Azure dans Visual Studio"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="virtual-machines"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/05/2016"
   ms.author="tarcher" />

# Créer et gérer des machines virtuelles Azure dans Visual Studio

Vous pouvez créer des machines virtuelles dans Azure à l’aide de l’Explorateur de serveurs de Visual Studio.

## Créer une machine virtuelle Azure dans l’Explorateur de serveurs

Vous pouvez créer une machine virtuelle depuis le [Portail de gestion Azure](http://go.microsoft.com/fwlink/?LinkID=253103), mais également à l’aide de commandes dans l’Explorateur de serveurs. Les machines virtuelles peuvent être utilisées, par exemple, pour fournir un serveur frontal derrière un point de terminaison public avec équilibrage de charge.

### Pour créer une machine virtuelle

1. Dans l’Explorateur de serveurs, développez le nœud **Azure**, puis cliquez sur **Machines virtuelles**.

1. Dans le menu contextuel, cliquez sur **Créer une machine virtuelle**.

    L’Assistant **Création d’une machine virtuelle** s’affiche alors.

    ![Commande Créer une machine virtuelle](./media/vs-azure-tools-virtual-machines-create-manage/IC718342.png)

1. Dans la page **Choisir un abonnement**, sélectionnez l’abonnement à utiliser lors de la création de la machine virtuelle, puis cliquez sur **Suivant**.

    Si vous n’êtes pas connecté à Azure, cliquez sur **Connexion** pour vous connecter. Ensuite, sélectionnez votre abonnement Azure dans la liste déroulante s’il n’est pas déjà sélectionné.

1. Dans la page **Sélectionner une image de machine virtuelle**, sélectionnez un type d’image dans la liste déroulante **Type d’image**, puis sélectionnez une image de machine virtuelle dans la liste déroulante **Nom de l’image**. Quand vous avez terminé, cliquez sur **Suivant**.

    ![Page Sélectionner une image de machine virtuelle](./media/vs-azure-tools-virtual-machines-create-manage/IC744137.png)

    Vous pouvez choisir les types d’images suivants.

    - **Images publiques** : images de machines virtuelles des systèmes d’exploitation et des logiciels serveurs, tels que Windows Server et SQL Server.

    - **Images MSDN** : images de machines virtuelles des logiciels disponibles pour les abonnés MSDN, tels que Visual Studio et Microsoft Dynamics.

    - **Images privées** : images de machines virtuelles spécialisées et généralisées que vous avez créées.

    Pour en savoir plus sur les machines virtuelles spécialisées et généralisées, consultez [Image de machine virtuelle](https://azure.microsoft.com/blog/2014/04/14/vm-image-blog-post/). Pour plus d’informations sur l’utilisation d’une machine virtuelle comme modèle pour créer rapidement de nouvelles machines virtuelles préconfigurées, consultez [Capture d’une machine virtuelle Windows à utiliser comme modèle](https://azure.microsoft.com/documentation/articles/virtual-machines-capture-image-windows-server/).

    Vous pouvez cliquer sur le nom d’une image de machine virtuelle sur le côté droit de la page pour afficher les informations associées à cette image.

    >[AZURE.NOTE] Vous ne pouvez pas ajouter d’images de machines virtuelles aux listes **Images publiques** et **Images MSDN**, car elles sont en lecture seule. Toutes les machines virtuelles que vous créez sont ajoutées à la liste **Images privées**.

    >Si vous êtes un abonné MSDN avec un abonnement de niveau Visual Studio, vous pouvez créer une machine virtuelle Azure préconfigurée contenant Visual Studio, ainsi que plusieurs autres images. Pour plus d’informations, consultez [Créer une machine virtuelle dans Visual Studio à l’aide de la galerie d’images Visual Studio 2013 pour les abonnés MSDN](http://visualstudio2013msdngalleryimage.azurewebsites.net) et [Abonnements MSDN](https://www.visualstudio.com/products/msdn-subscriptions-vs).|

1. Dans la page **Paramètres de base de la machine virtuelle**, entrez un nom de machine virtuelle, puis ajoutez les informations associées à celle-ci, telles que sa taille, le nom d’utilisateur et le mot de passe. Quand vous avez terminé, cliquez sur **Suivant**.

    Vous devrez utiliser les nouveaux nom et mot de passe pour vous connecter à la machine à l’aide du Bureau à distance. Il est donc conseillé de noter ces informations pour ne pas les oublier. Après avoir créé une machine virtuelle Azure dans Visual Studio, vous pouvez modifier sa taille, ainsi que d’autres paramètres depuis le [Portail de gestion Azure](http://go.microsoft.com/fwlink/?LinkID=253103).

        >[AZURE.NOTE] If you choose larger sizes for the virtual machine, extra charges may apply. See [Virtual Machines Pricing Details](https://azure.microsoft.com/pricing/details/virtual-machines/) for more information.

1. Les machines virtuelles créées dans Visual Studio nécessitent un service cloud. Dans la page **Paramètres du service cloud**, sélectionnez un service cloud pour la machine virtuelle ou cliquez sur **<Créer…>** dans la liste déroulante si vous ne disposez pas d’un service cloud ou pour en utiliser un autre. Un compte de stockage est également requis. Choisissez donc un compte de stockage (ou créez-en un) dans la liste déroulante **Compte de stockage**. Pour plus d’informations, consultez [Introduction à Microsoft Azure Storage](./storage/storage-introduction/).

1. Si vous voulez spécifier un réseau virtuel (ce qui est facultatif), sélectionnez-le dans les listes déroulantes Réseau virtuel et Sous-réseau.

    Les machines virtuelles qui sont membres d’un groupe à haute disponibilité sont déployées sur des domaines d’erreur différents. Pour plus d’informations, consultez [Azure Virtual Network](https://azure.microsoft.com/services/virtual-network/).

1. Si vous voulez que votre machine virtuelle soit membre d’un groupe à haute disponibilité (également facultatif), cochez la case **Spécifier un groupe à haute disponibilité**, puis choisissez un groupe à haute disponibilité dans la liste déroulante. Quand vous avez terminé, cliquez sur le bouton **Suivant**.

    L’ajout de votre machine virtuelle à un groupe à haute disponibilité permet de vous assurer de la disponibilité de votre application lors de défaillances réseau, de défaillances matérielles d’un disque dur local et de temps d’arrêt planifiés. Vous devez utiliser le [Portail de gestion Azure](http://go.microsoft.com/fwlink/?LinkID=253103) pour créer des réseaux virtuels, des sous-réseaux et des groupes à haute disponibilité. Pour plus d’informations, consultez [Gestion de la disponibilité des machines virtuelles](https://azure.microsoft.com/documentation/articles/manage-availability-virtual-machines/).

1. Dans la page **Points de terminaison**, spécifiez les points de terminaison publics que vous voulez mettre à disposition des utilisateurs de votre machine virtuelle. Par exemple, vous pouvez choisir d’activer le protocole HTTP (port 80) en plus du Bureau à distance et des points de terminaison PowerShell, qui sont activés par défaut. Pour ajouter un point de terminaison, sélectionnez-en un dans la liste déroulante **Nom du port**, puis cliquez sur le bouton **Ajouter**. Pour supprimer un point de terminaison, cliquez sur la croix rouge (**X**) à côté de son nom dans la liste des points de terminaison.

    ![Page Points de terminaison de l’Assistant Machines virtuelles](./media/vs-azure-tools-virtual-machines-create-manage/IC718351.png)

    Les points de terminaison disponibles varient selon le service cloud que vous avez sélectionné pour votre machine virtuelle. Pour plus d’informations, consultez [Points de terminaison de service Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/).

    >[AZURE.NOTE] Une fois les points de terminaison publics activés, les services de votre machine virtuelle sont disponibles sur Internet. Veillez à installer et à configurer correctement les points de terminaison et les services de votre machine virtuelle, tels que les listes de contrôle d’accès des points de terminaison. Pour plus d’informations, consultez [Comment configurer des points de terminaison sur une machine virtuelle](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/).

1. Après avoir configuré les paramètres de la machine virtuelle, cliquez sur le bouton **Créer** pour créer la machine virtuelle.

    Étant donné que la machine virtuelle est créée par Azure, le **journal des activités Azure** indique la progression de l’opération de création des machines virtuelles.

    ![Journal des activités de la machine virtuelle – En cours](./media/vs-azure-tools-virtual-machines-create-manage/IC744138.png)

    Pour afficher uniquement les informations relatives à la machine virtuelle, cliquez sur l’onglet **Machines virtuelles** du **journal des activités Azure**.

    ![Journal des activités de la machine virtuelle – Terminé](./media/vs-azure-tools-virtual-machines-create-manage/IC744139.png)

    Si l’opération se termine avec succès, la nouvelle machine virtuelle s’affiche sous le nœud **Machines virtuelles** dans l’Explorateur de serveurs. Vous pouvez vous connecter à celui-ci en cliquant sur le raccourci **Connexion à l’aide de Bureau à distance**.

    ![Machine virtuelle affichée dans l’Explorateur de serveurs](./media/vs-azure-tools-virtual-machines-create-manage/IC744140.png)

## Gérer vos machines virtuelles

Dans la page de configuration des machines virtuelles, en plus des fonctions d’arrêt, de connexion, d’actualisation et d’ajout de points de contrôle à la machine virtuelle sélectionnée, vous pouvez également utiliser les fonctions d’affichage et de modification des paramètres de la machine virtuelle. Vous pouvez :

- modifier la taille de la machine virtuelle ;

- sélectionner le groupe à haute disponibilité à utiliser avec la machine virtuelle ;

- ajouter, supprimer et modifier les paramètres des points de terminaison publics ;

- ajouter, supprimer et configurer les extensions des machines virtuelles ;

- afficher les informations sur les disques associés aux machines virtuelles.

### Afficher ou modifier les paramètres des machines virtuelles

1. Dans l’Explorateur de serveurs, choisissez votre machine virtuelle sous le nœud **Machines virtuelles Azure**.

1. Dans le menu contextuel, choisissez **Configurer** pour afficher la page de configuration des machines virtuelles.

    ![Page de configuration des machines virtuelles Azure](./media/vs-azure-tools-virtual-machines-create-manage/IC744141.png)

1. Affichez les informations relatives à la machine virtuelle ou modifiez-les.

### Enregistrer ou restaurer l’état de votre machine virtuelle

Étant donné que vous configurez votre machine virtuelle et y installez des logiciels, il est conseillé d’enregistrer régulièrement votre progression en créant des points de contrôle de machine virtuelle. Un point de contrôle est un instantané ou une image de l’état actuel de votre machine virtuelle. Si quelque chose ne fonctionne pas sur la machine virtuelle, ou si vous voulez la reconfigurer, vous pouvez gagner du temps en la restaurant à un état précédent, plutôt que de recommencer de zéro.

### Pour créer un point de contrôle de machine virtuelle

1. Dans l’Explorateur de serveurs, choisissez votre machine virtuelle sous le nœud **Machines virtuelles Azure**.

1. Dans le menu contextuel, choisissez **Configurer** pour afficher la page de configuration des machines virtuelles.

1. Dans la page de configuration, cliquez sur le bouton **Capturer l’image**.

    ![Bouton Capturer de la page de configuration Azure](./media/vs-azure-tools-virtual-machines-create-manage/IC744142.png)

    La boîte de dialogue **Capture la machine virtuelle** s’affiche.

    ![Boîte de dialogue Capturer la machine virtuelle](./media/vs-azure-tools-virtual-machines-create-manage/IC744143.png)

1. Fournissez une étiquette et une description pour l’image. Une étiquette et une description par défaut sont fournies, mais vous pouvez les remplacer par les vôtres si vous le voulez.

1. Si vous avez déjà exécuté Sysprep sur cette machine virtuelle, cochez la case **J’ai exécuté Sysprep sur la machine virtuelle**.

    Sysprep est un outil qui, entre autres choses, supprime les données spécifiques aux systèmes de la version de Windows de la machine virtuelle, pour en faire un modèle que les autres peuvent utiliser. Pour plus d’informations, consultez [Capture d’une machine virtuelle Windows pour l’utiliser comme modèle](https://azure.microsoft.com/documentation/articles/virtual-machines-capture-image-windows-server/).

1. Après avoir configuré les paramètres de capture, cliquez sur le bouton **Capturer** pour créer le point de contrôle.

    Pendant qu’Azure crée le point de contrôle, le **journal des activités Azure** affiche la progression de l’opération.

    ![Capture d’un point de contrôle de machine virtuelle](./media/vs-azure-tools-virtual-machines-create-manage/IC744144.png)

    Le **journal des activités Azure** indiquera quand l’opération de point de contrôle sera terminée.

    ![Création de point de contrôle terminée](./media/vs-azure-tools-virtual-machines-create-manage/IC744145.png)

## Pour gérer les points de contrôle de machine virtuelle

### Pour restaurer une machine virtuelle à un état précédemment enregistré

- Suivez les étapes décrites dans [Pas-à-pas : restauration cloud de machines virtuelles Microsoft Azure à l’aide de PowerShell – Deuxième partie](http://blogs.technet.com/b/keithmayer/archive/2014/02/04/step-by-step-perform-cloud-restores-of-windows-azure-virtual-machines-using-powershell-part-2.aspx).

### Pour supprimer un point de contrôle

1. Accédez au [portail de gestion Azure](http://go.microsoft.com/fwlink/?LinkID=253103).

1. Dans la page de configuration des machines virtuelles, cliquez sur l’onglet **Images** situé en haut de la page.

1. Cliquez sur le point de contrôle que vous voulez supprimer, puis cliquez sur le bouton **Supprimer** situé au bas de la page.

## Arrêter une machine virtuelle

1. Dans l’Explorateur de serveurs, cliquez sur la machine virtuelle que vous voulez arrêter sous le nœud **Machines virtuelles Azure**.

1. Dans le menu contextuel, choisissez la commande **Arrêter**, ou choisissez **Configurer** pour afficher la page de configuration des machines virtuelles, puis cliquez sur le bouton **Arrêter**.

## Étapes suivantes

Pour en savoir plus sur la création de machines virtuelles, consultez [Créer une machine virtuelle exécutant Linux](virtual-machines-linux-tutorial.md) et [Créer une machine virtuelle exécutant Windows dans le portail Azure en version préliminaire](virtual-machines-windows-tutorial.md).

<!---HONumber=AcomDC_0128_2016-->
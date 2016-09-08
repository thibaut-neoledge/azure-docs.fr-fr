<properties
	pageTitle="Créer votre première machine virtuelle Windows | Microsoft Azure"
	description="Découvrez comment créer votre première machine virtuelle Windows à l’aide du Portail Azure."
	keywords="machine virtuelle Windows,créer une machine virtuelle,ordinateur virtuel,configuration d’une machine virtuelle"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>
<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="08/29/2016"
	ms.author="cynthn"/>

# Créer votre première machine virtuelle Windows dans le portail Azure

Ce didacticiel vous montre combien il est facile de créer une machine virtuelle Windows en quelques minutes à l’aide du Portail Azure.

Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/) en quelques minutes.

Voici une [vidéo de procédure pas à pas](https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Create-A-Virtual-Machine-Running-Windows-In-The-Azure-Preview-Portal) de ce didacticiel.


## Choisir l’image de machine virtuelle à partir de Marketplace

Nous allons utiliser une image Windows Server 2012 R2 Datacenter comme exemple, mais il s’agit simplement d’un des nombreux types d’images proposés par Azure. Les images disponibles dépendent de votre abonnement. Par exemple, les images de bureau sont disponibles pour les [abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le menu Hub, cliquez sur **Nouveau** > **Machines virtuelles** > **Windows Server 2012 R2 Datacenter**.

	![Capture d’écran affichant les images de machines virtuelles Azure disponibles dans le portail](./media/virtual-machines-windows-hero-tutorial/marketplace-new.png)


3. Dans le panneau **Windows Server 2012 R2 Datacenter**, sous **Sélectionner un modèle de déploiement**, vérifiez que **Resource Manager** est sélectionné. Cliquez sur **Create**.

	![Capture d’écran affichant le modèle de déploiement à sélectionner pour la machine virtuelle](./media/virtual-machines-windows-hero-tutorial/deployment-model.png)

## Créer la machine virtuelle Windows

Après avoir sélectionné l’image, vous pouvez utiliser les paramètres par défaut d’Azure pour la majeure partie de la configuration et créer rapidement la machine virtuelle.

1. Dans le panneau **De base**, entrez un **nom** pour la machine virtuelle. Le nom doit comporter 1 à 15 caractères et il ne peut pas contenir de caractères spéciaux.

2. Entrez un **Nom d’utilisateur** et un **Mot de passe** fort qui serviront à créer un compte local sur la machine virtuelle. Le compte local est utilisé pour ouvrir une session de machine virtuelle et la gérer.

	Le mot de passe doit compter 8 à 123 caractères et répondre à trois des quatre conditions suivantes : un caractère minuscule, un caractère majuscule, un chiffre et un caractère spécial. En savoir plus sur les [conditions requises pour les noms d’utilisateur et les mots de passe](virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm).


3. Sélectionnez un [groupe de ressources](../resource-group-overview.md#resource-groups) existant ou tapez le nom d’un nouveau groupe. Tapez un **emplacement** de centre de données Azure, comme **États-Unis de l’Ouest**.

4. Lorsque vous avez terminé, cliquez sur **OK** pour passer à la section suivante.

	![Capture d’écran affichant les paramètres dans le panneau **De base** pour la configuration d’une machine virtuelle Azure](./media/virtual-machines-windows-hero-tutorial/basics-blade.png)

	
5. Choisissez une [taille](virtual-machines-windows-sizes.md) de machine virtuelle, puis cliquez sur **Sélectionner** pour continuer.

	![Capture d’écran du panneau Taille affichant les tailles de machine virtuelle Azure que vous pouvez sélectionner](./media/virtual-machines-windows-hero-tutorial/size-blade.png)

6. Dans le panneau **Paramètres**, vous pouvez modifier les options de stockage et de mise en réseau. Pour ce didacticiel, acceptez les paramètres par défaut. Si vous avez sélectionné une taille de machine virtuelle qui le prend en charge, vous pouvez essayer Premium Storage en sélectionnant **Premium (SSD)** sous **Type de disque**. Une fois les modifications terminées, cliquez sur **OK**.

	![Capture d’écran du panneau Paramètres où vous pouvez configurer les fonctionnalités facultatives d’une machine virtuelle Azure](./media/virtual-machines-windows-hero-tutorial/settings-blade.png)

7. Cliquez sur **Résumé** pour passer en revue vos options. Lorsque le message **Validation réussie** apparaît, cliquez sur **OK**.

	![Capture d’écran de la page Synthèse affichant les options de configuration choisies pour la machine virtuelle Azure](./media/virtual-machines-windows-hero-tutorial/summary-blade.png)

8. Pendant qu’Azure crée la machine virtuelle, vous pouvez suivre la progression de cette opération dans le menu Hub sous **Machines virtuelles**.


## Se connecter à la machine virtuelle et ouvrir une session

1.	Dans le menu Hub, cliquez sur **Machines virtuelles**.

2.	Sélectionnez la machine virtuelle dans la liste.

3. Dans le panneau de la machine virtuelle, cliquez sur **Se connecter**. Cette opération crée et télécharge un fichier .rdp (Remote Desktop Protocol) qui s’apparente à un raccourci de connexion à votre ordinateur. Si vous le souhaitez, vous pouvez enregistrer le fichier sur votre bureau pour y accéder facilement. **Ouvrez** ce fichier pour vous connecter à votre machine virtuelle.

	![Capture d'écran du portail Azure montrant comment se connecter à votre machine virtuelle.](./media/virtual-machines-windows-hero-tutorial/connect.png)

4. Un message vous avertit que le fichier .rdp provient d’un éditeur inconnu. C’est normal. Dans la fenêtre Bureau à distance, cliquez sur **Connecter** pour continuer.

	![Capture d’écran d’avertissement relatif à un éditeur inconnu.](./media/virtual-machines-windows-hero-tutorial/rdp-warn.png)

5. Dans la fenêtre Sécurité de Windows, tapez le nom d’utilisateur et le mot de passe du compte local que vous avez créé lorsque vous avez créé la machine virtuelle. Le nom d’utilisateur est entré en tant que *vmname*&#92;*username*. Cliquez ensuite sur **OK**.

	![Capture d’écran de saisie du nom de la machine virtuelle, du nom d’utilisateur et du mot de passe.](./media/virtual-machines-windows-hero-tutorial/credentials.png)
 	
6.	Vous serez averti que le certificat ne peut pas être vérifié. C’est normal. Cliquez sur **Oui** pour vérifier l’identité de la machine virtuelle et terminer la connexion.

	![Capture d'écran montrant un message relatif à la vérification de l'identité de la machine virtuelle.](./media/virtual-machines-windows-hero-tutorial/cert-warning.png)


En cas de problème de connexion, consultez [Résolution des problèmes de connexion Bureau à distance avec une machine virtuelle Azure Windows](virtual-machines-windows-troubleshoot-rdp-connection.md).

Vous pouvez désormais utiliser la machine virtuelle tout comme vous le feriez avec un serveur.

## Installation d’IIS sur votre machine virtuelle

Maintenant que vous êtes connecté à la machine virtuelle, nous allons installer un rôle de serveur afin d’optimiser votre expérience.

1. Si ce n’est pas déjà fait, ouvrez le **Gestionnaire de serveur**. Cliquez sur le menu **Démarrer**, puis sur **Gestionnaire de serveur**.
2. Dans le volet gauche du **Gestionnaire de serveur**, sélectionnez **Serveur local**.
3. Dans le menu, sélectionnez **Gérer** > **Ajouter des rôles et des fonctionnalités**.
4. Dans l’assistant Ajout de rôles et de fonctionnalités, sur la page **Type d’installation** choisissez **Installation basée sur un rôle ou une fonctionnalité**, puis cliquez sur **Suivant**.

	![Capture d’écran montrant l’onglet Assistant Ajout de rôles et de fonctionnalités pour le type d’installation.](./media/virtual-machines-windows-hero-tutorial/role-wizard.png)

5. Sélectionnez la machine virtuelle dans le pool de serveurs, puis cliquez sur **Suivant**.
6. Sur la page **Rôles de serveur**, cliquez sur **Serveur Web (IIS)**.

	![Capture d’écran montrant l’onglet Assistant Ajout de rôles et de fonctionnalités pour les rôles de serveur.](./media/virtual-machines-windows-hero-tutorial/add-iis.png)

7. Dans la fenêtre contextuelle concernant l’ajout des fonctionnalités requises pour IIS, assurez-vous que l’option **Inclure les outils de gestion** est sélectionnée, puis cliquez sur **Ajouter des fonctionnalités**. Lorsque la fenêtre contextuelle se ferme, cliquez sur **Suivant** dans l’assistant.

	![Capture d’écran montrant la fenêtre contextuelle permettant de confirmer l’ajout du rôle IIS.](./media/virtual-machines-windows-hero-tutorial/confirm-add-feature.png)

8. Sur la page des fonctionnalités, cliquez sur **Suivant**.
9. Sur la page **Rôle Web Server (IIS)**, cliquez sur **Suivant**.
10. Sur la page **Services de rôle**, cliquez sur **Suivant**.
11. Sur la page **Confirmation**, cliquez sur **Installer**.
12. Une fois l’installation terminée, cliquez sur **Fermer** dans l’assistant.



## Ouverture du port 80 

Pour que votre machine virtuelle accepte le trafic entrant sur le port 80, vous devez ajouter une règle de trafic entrant pour le groupe de sécurité réseau.

1. Ouvrez le [portail Azure](https://portal.azure.com).
2. Sous **Machines virtuelles**, sélectionnez la machine virtuelle que vous avez créée.
3. Dans les paramètres des machines virtuelles, choisissez **Interfaces réseau**, puis sélectionnez l’interface réseau existante.

	![Capture d’écran montrant le paramètre de la machine virtuelle pour les interfaces réseau.](./media/virtual-machines-windows-hero-tutorial/network-interface.png)

4. Dans les **bases** de l’interface réseau, cliquez sur le **groupe de sécurité réseau**.

	![Capture d’écran montrant la section Bases pour l’interface réseau.](./media/virtual-machines-windows-hero-tutorial/select-nsg.png)

5. Dans le panneau **Bases** du groupe de sécurité réseau, une règle de trafic entrant doit exister par défaut pour **default-allow-rdp**. Elle vous permet de vous connecter à la machine virtuelle. Pour autoriser le trafic IIS, vous allez ajouter une autre règle de trafic entrant. Cliquez sur **Règle de sécurité de trafic entrant**.

	![Capture d’écran montrant la section Bases pour le groupe de sécurité réseau.](./media/virtual-machines-windows-hero-tutorial/inbound.png)

6. Dans **Règles de sécurité de trafic entrant**, cliquez sur **Ajouter**.

	![Capture d’écran affichant le bouton d’ajout d’une règle de sécurité.](./media/virtual-machines-windows-hero-tutorial/add-rule.png)

7. Dans **Règles de sécurité de trafic entrant**, cliquez sur **Ajouter**. Entrez **80** dans la plage de ports et vérifiez que l’option **Autoriser** est sélectionnée. Une fois ces opérations effectuées, cliquez sur **OK**.

	![Capture d’écran affichant le bouton d’ajout d’une règle de sécurité.](./media/virtual-machines-windows-hero-tutorial/port-80.png)
 
Pour plus d’informations sur les groupes de sécurité réseau et sur les règles de trafic entrant et sortant, voir [Autoriser l’accès externe à votre machine virtuelle à l’aide du portail Azure](virtual-machines-windows-nsg-quickstart-portal.md).
 
## Connexion au site Web IIS par défaut

1. Dans le portail Azure, cliquez sur **Machines virtuelles**, puis sélectionnez votre machine virtuelle.
2. Dans le panneau **Bases**, copiez votre **adresse IP publique**.

	![Capture d’écran indiquant où trouver l’adresse IP publique de votre machine virtuelle.](./media/virtual-machines-windows-hero-tutorial/ipaddress.png)

2. Ouvrez un navigateur et, dans la barre d’adresse, tapez l’adresse IP publique comme suit : http://<adresseIPpublique>, puis appuyez sur **Entrée** pour accéder à cette adresse.
3. Votre navigateur doit accéder à la page Web IIS par défaut, qui doit ressembler à ceci :

	![Capture d’écran montrant à quoi ressemble la page IIS par défaut dans un navigateur.](./media/virtual-machines-windows-hero-tutorial/iis-default.png)


## Arrêtez la machine virtuelle.

Il est judicieux d’arrêter la machine virtuelle pour éviter les frais lorsque vous ne l’utilisez pas. Cliquez simplement sur le bouton **Arrêter**, puis sur **Oui**.

![Capture d’écran affichant le bouton d’arrêt d’une machine virtuelle.](./media/virtual-machines-windows-hero-tutorial/stop-vm.png)
	
Cliquez simplement sur le bouton **Démarrer** pour redémarrer la machine virtuelle lorsque vous êtes prêt à l’utiliser à nouveau.


## Étapes suivantes

* Vous pouvez également tester [l’association d’un disque de données](virtual-machines-windows-attach-disk-portal.md) à votre machine virtuelle. Les disques de données fournissent plus de stockage pour votre machine virtuelle.

* Vous pouvez également [créer une machine virtuelle Windows à l’aide de Powershell](virtual-machines-windows-ps-create.md) ou [créer une machine virtuelle Linux](virtual-machines-linux-quick-create-cli.md) à l’aide de l’interface de ligne de commande Azure.

* Si vous souhaitez automatiser les déploiements, consultez [Création d’une machine virtuelle Windows avec un modèle du Gestionnaire de ressources](virtual-machines-windows-ps-template.md).

<!---HONumber=AcomDC_0831_2016-->
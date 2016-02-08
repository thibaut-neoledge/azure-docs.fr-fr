<properties
	pageTitle="Approvisionnement d’une machine virtuelle SQL Server dans Azure Resource Manager (GUI) | Microsoft Azure"
	description="Créez une machine virtuelle SQL Server en mode Azure Resource Manager. Ce didacticiel utilise l'interface utilisateur et des outils, plutôt que des scripts."
	services="virtual-machines"
	documentationCenter="na"
	authors="MikeRayMSFT"
    editor=""
	manager="jeffreyg"
	tags="azure-service-management" />


<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="12/22/2015"
	ms.author="mikeray" />

# Approvisionnement d’une machine virtuelle SQL Server dans Azure Resource Manager

> [AZURE.SELECTOR]
- [Classic portal](virtual-machines-provision-sql-server.md)
- [PowerShell](virtual-machines-sql-server-create-vm-with-powershell.md)
- [Azure Resource Manager portal](virtual-machines-sql-server-provision-resource-manager.md)

<br/>

>[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique

Ce didacticiel de bout en bout vous montre comment approvisionner une machine virtuelle Azure dans le portail à l’aide du modèle Azure Resource Manager et comment configurer SQL Server à partir d’un modèle dans la galerie Azure.

La galerie de machines virtuelles Azure inclut différentes images contenant Microsoft SQL Server. Vous pouvez sélectionner l’une des images de machine virtuelle à partir de la galerie et approvisionner la machine virtuelle en quelques clics pour votre environnement Azure.

Ce didacticiel présente les procédures suivantes :

- [Se connecter au portail et approvisionner une image de machine virtuelle SQL à partir de la galerie avec le modèle de déploiement d’Azure Resource Manager](#Provision)

- [Configurer les paramètres de la machine virtuelle et de SQL Server](#ConfigureVM)

- [Ouvrir la machine virtuelle via le Bureau à distance](#Open)

- [Connectez-vous à l’instance de SQL Server à l’aide de SQL Server Management Studio sur un autre ordinateur](#Connect)

- [Étapes suivantes](#Next)

Ce didacticiel part du principe que vous disposez déjà d’un compte Azure. Si vous n'avez pas de compte Azure, visitez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a id="Provision">Approvisionner une image de machine virtuelle SQL à partir de la galerie avec le modèle de déploiement d’Azure Resource Manager

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec votre compte.
1. Dans le portail Azure, cliquez sur **+Nouveau**. Le portail ouvre le panneau **Nouveau**. Les modèles de machine virtuelle SQL Server sont dans le groupe **Compute** du Marketplace.

1. Dans le panneau **Nouveau**, cliquez sur **Compute**.
1. Pour voir tous les types de ressources du panneau **Compute**, cliquez sur **Afficher tout**. <br/> ![Panneau de calcul Azure](./media/virtual-machines-sql-server-provision-resource-manager/azure-compute-blade.png) <br/>
1. Sous **Serveurs de bases de données**, cliquez sur **SQL Server** pour voir tous les modèles disponibles pour SQL Server. Vous devrez peut-être faire défiler jusqu’à localiser **Serveurs de base de données**.
1. 	Chaque modèle identifie une version de SQL Server et un système d’exploitation. Sélectionnez l’une de ces images à partir de la liste pour afficher un panneau contenant les informations la concernant.
1.	Le panneau d’informations fournit une description de cette image de machine virtuelle, et vous permet de sélectionner un modèle de déploiement. Sous **Sélectionner un modèle de déploiement**, sélectionnez **Resource Manager** et cliquez sur **Créer**. <br/> ![Panneau de calcul Azure](./media/virtual-machines-sql-server-provision-resource-manager/azure-compute-sql-deployment-model.png) <br/>

## <a id="ConfigureVM"> Configurer la machine virtuelle
Dans le portail Azure, il existe cinq panneaux de configuration d’une machine virtuelle SQL Server.

1.	Configurer les paramètres de base
1.	Choisir la taille de machine virtuelle
1.	Configurer les paramètres des machines virtuelles
1.	Configurer SQL Server
1.	Passer en revue le résumé

## 1\. Configurer les paramètres de base
Dans le panneau **Créer une machine virtuelle**, sous **De base**, fournissez les informations suivantes :

* Un **nom** de machine virtuelle unique.
* Un nom d’utilisateur unique pour le compte administrateur local de la machine dans la zone **Nom d’utilisateur**. Ce compte sera également membre du rôle serveur fixe sysadmin SQL Server.
* Dans la zone **Mot de passe**, entrez un mot de passe fort.
* Si vous avez plusieurs abonnements, vérifiez que l’abonnement est correct pour la machine virtuelle que vous allez générer.
* Dans la zone **Groupe de ressources**, saisissez un nom pour le groupe de ressources. Sinon, pour utiliser un groupe de ressources existant, cliquez sur **Sélectionner un élément existant**. Un groupe de ressources est une collection de services connexes dans Azure. Pour plus d’informations sur les groupes de ressources, consultez [Présentation d’Azure Resource Manager](../resource-group-overview.md). Vérifiez que l’**emplacement** est adapté à vos besoins.
* Cliquez sur **OK** pour enregistrer les paramètres. <br/>

>![Concepts de base de SQL ARM](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-basic.png) <br/>

## 2\. Choisir la taille de machine virtuelle
Dans le panneau **Créer une machine virtuelle**, sous **Taille**, choisissez une taille de machine virtuelle. Le portail Azure affiche les tailles recommandées. Pour trouver plus d’informations sur les tailles de machines virtuelles, consultez [Tailles des machines virtuelles](virtual-machines-size-specs.md). Les tailles sont basées sur le modèle que vous avez sélectionné. La taille estime le coût mensuel pour exécuter la machine virtuelle. Sélectionnez une taille de machine virtuelle pour votre serveur. Pour des considérations sur les tailles de machines virtuelles SQL Server, consultez [Meilleures pratiques relatives aux performances de SQL Server sur les machines virtuelles Azure](virtual-machines-sql-server-performance-best-practices.md).

## 3\. Configurer les paramètres des machines virtuelles
Dans le panneau **Créer une machine virtuelle**, sous **Paramètres**, configurez le stockage Azure, la mise en réseau et la surveillance de la machine virtuelle.

- Sous **Stockage**, spécifiez un type de disque. L’option Premium Storage est recommandée pour les charges de travail de production.

>[AZURE.NOTE] Elle est activée par défaut. Elle redimensionne automatiquement votre ordinateur à une taille qui prend en charge Premium Storage. Si vous désactivez Premium Storage, votre sélection de taille de machine précédente est utilisée.

- Sous **Compte de stockage**, vous pouvez accepter le nom de compte de stockage automatiquement approvisionné ou vous pouvez cliquer sur **Compte de stockage** pour choisir un compte existant et configurer le type de compte de stockage. Par défaut, Azure crée un compte de stockage avec un stockage localement redondant.

- Sous **Réseau**, vous pouvez accepter les valeurs renseignées automatiquement pour les fonctionnalités ou cliquer sur chacune de ces fonctionnalités pour configurer les options **Réseau virtuel**, **Sous-réseau**, **Adresse IP publique**, et **Groupe de sécurité réseau**. Par défaut, Azure configure automatiquement ces valeurs.

- Azure permet l’**analyse** par défaut avec le même compte de stockage désigné pour la machine virtuelle. Vous pouvez modifier ces paramètres ici.

- Dans **Groupe à haute disponibilité**, spécifiez un groupe à haute disponibilité. Dans le cadre de ce didacticiel, vous pouvez sélectionner **aucun**. Si vous envisagez de configurer des groupes de disponibilité SQL AlwaysOn, configurez la disponibilité pour éviter de recréer la machine virtuelle. Pour plus d’informations, voir [Gestion de la disponibilité des machines virtuelles](virtual-machines-manage-availability.md).

## 4\. Configurer SQL Server
Dans le panneau **Créer une machine virtuelle**, sous **Configurer SQL Server**, configurez des paramètres et optimisations spécifiques pour SQL Server. Les paramètres que vous pouvez configurer pour SQL Server sont les suivants :

- Connectivité
- Authentification
- Optimisation du stockage
- Application de correctifs
- Sauvegardes
- Intégration du coffre de clés

### Connectivité
Sous **Connectivité SQL**, spécifiez **Public (Internet)** pour autoriser les connexions à SQL Server à partir de machines ou de services sur Internet. Avec cette option sélectionnée, Azure configure automatiquement le pare-feu et le groupe de sécurité réseau pour autoriser le trafic sur le port 1433. <br/>![Connectivité de SQL ARM](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-connectivity-alt.png) <br/>

Pour vous connecter à SQL Server via Internet, vous devez également activer l’authentification SQL Server.

>[AZURE.NOTE]Pour la sécurité, limitez le port source à l’aide du groupe de sécurité réseau. Pour plus d’informations, consultez [Présentation du groupe de sécurité réseau](virtual-networks-nsg.md).

Si vous préférez ne pas activer les connexions au moteur de base de données via Internet automatiquement, choisissez l’une des options suivantes : - **Local (à l’intérieur de la machine virtuelle uniquement)** pour autoriser les connexions à SQL Server uniquement à partir de la machine virtuelle. - **Privé (dans le réseau virtuel)** pour autoriser les connexions à SQL Server à partir de machines ou de services dans le même réseau virtuel.


Par défaut, le **port** est 1433. Vous pouvez spécifier un numéro de port différent. Pour plus d’informations, consultez [Se connecter à une machine virtuelle SQL Server (Resource Manager) | Microsoft Azure](virtual-machines-sql-server-connectivity-resource-manager.md).



### Authentification
Si vous avez besoin de l’authentification SQL Server, cliquez sur **Activer** sous **Authentification SQL**.

<br/>![Authentification de SQL ARM](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-authentication.png) <br/>


Si vous activez l’authentification SQL Server, spécifiez un **nom de connexion** et un **mot de passe**. Ce nom d’utilisateur sera une connexion d’authentification SQL Server et un membre du rôle serveur fixe sysadmin. Consultez [Choisir un mode d’authentification](http://msdn.microsoft.com/library/ms144284.aspx) pour plus d’informations sur les modes d’authentification. Par défaut, SQL Server n’active pas l’authentification SQL Server. Dans ce scénario, les administrateurs locaux sur la machine virtuelle peuvent se connecter à l’instance SQL Server.

>[AZURE.NOTE] Si vous envisagez d’accéder à SQL Server via Internet (par exemple, avec l’option Connectivité publique), vous devez activer l’authentification SQL ici. L’accès public à SQL Server requiert l’utilisation de l’authentification SQL.

### Optimisation du stockage
Cliquez sur **Configuration du stockage** afin de spécifier les exigences de stockage. Vous pouvez spécifier des exigences comme les opérations d’entrée/sortie par seconde (E/S par seconde), le débit en Mbit/s et la taille totale de stockage. Configurez ces éléments en utilisant les échelles mobiles. Le portail calcule automatiquement le nombre de disques en fonction de ces exigences.

Par défaut, Azure optimise le stockage pour 5 000 E/S par seconde, 200 Mbit/s et 1 To d’espace de stockage. Vous pouvez modifier ces paramètres de stockage en fonction de la charge de travail. Sous **Stockage optimisé pour**, sélectionnez l’une des options suivantes :

- **Général** est le paramètre par défaut et prend en charge la plupart des charges de travail.
- Le traitement **transactionnel** optimise le stockage pour les charges de travail OLTP de base de données traditionnelles.
- L’**entreposage de données** optimise le stockage pour les charges de travail d’analyse et de création de rapports.

L’illustration suivante présente le panneau de configuration de stockage. <br/>![Stockage de SQL ARM](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-storage.png) <br/>

>[AZURE.NOTE] Les limites de configuration de stockage dépendent de la taille de la machine virtuelle. Pour plus d’informations, consultez [Tailles des machines virtuelles](virtual-machines-size-specs.md).

### Application de correctifs
La **mise à jour corrective automatisée SQL** est activée par défaut. La mise à jour corrective automatisée permet à Azure de corriger automatiquement SQL Server et le système d’exploitation. Spécifiez un jour de la semaine, une heure et une durée pour la fenêtre de maintenance. Azure effectue la mise à jour corrective dans la fenêtre de maintenance. La planification de la fenêtre de maintenance utilise les paramètres régionaux de la machine virtuelle pour l’heure. Si vous ne souhaitez pas qu’Azure corrige automatiquement SQL Server et le système d’exploitation, cliquez sur **Désactiver**.

<br/>![Mise à jour corrective de SQL ARM](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-patching.png) <br/>

Pour plus d’informations, consultez [Mise à jour corrective automatisée pour SQL Server dans les machines virtuelles Azure](virtual-machines-sql-server-automated-patching.md).

### Sauvegardes
Activez les sauvegardes automatiques de base de données pour toutes les bases de données sous **Sauvegarde automatisée SQL**. Lorsque vous activez la sauvegarde SQL automatisée, vous pouvez configurer les éléments suivants :

- Période de rétention de sauvegarde en jours
- Compte de stockage à utiliser pour les sauvegardes
- Chiffrement ou non de la sauvegarde Pour chiffrer la sauvegarde, cliquez sur **Activer**. Si les sauvegardes automatisées sont chiffrées, spécifiez un mot de passe. Azure crée un certificat pour chiffrer les sauvegardes et utilise le mot de passe pour protéger ce certificat.

<br/>![Sauvegarde de SQL ARM](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-autobackup.png) <br/>

 Pour plus d’informations, voir [Sauvegarde automatisée pour SQL Server dans les machines virtuelles Azure](virtual-machines-sql-server-automated-backup.md).

### Intégration du coffre de clés
Pour stocker des secrets de sécurité dans Azure pour le chiffrement, cliquez sur **Intégration du coffre de clés Azure**, puis sur **Activer**.

<br/>![Intégration du coffre de clés Azure SQL ARM](./media/virtual-machines-sql-server-provision-resource-manager/azure-sql-arm-akv.png) <br/>

Le tableau suivant répertorie les paramètres requis pour configurer l’intégration du coffre de clés Azure.

|PARAMÈTRE|DESCRIPTION|EXEMPLE|
|----------|----------|-------|
|**URL du coffre de clés** | L’emplacement du coffre de clés.|https://contosokeyvault.vault.azure.net/ |
|**Nom principal du coffre de clés Azure** |Nom du principal du service Azure Active Directory Également appelé ID client. |fde2b411-33d5-4e11-af04eb07b669ccf2|
| **Secret du principal du coffre de clés Azure**|L’intégration du coffre de clés Azure crée des informations d’identification dans SQL Server, permettant ainsi à la machine virtuelle d’accéder au coffre de clés. Choisissez un nom pour cette identification. | 9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=|
|**Nom des informations d’identification**|Choisissez un nom pour désigner ces informations d’identification.| mycred1|

Pour plus d’informations, consultez [Configurer Azure Key Vault Integration (Intégration du coffre de clés Azure) pour des machines virtuelles SQL Server sur Azure](virtual-machines-sql-server-azure-key-vault-integration.md).

## 5\. Passer en revue le résumé
Passez en revue le résumé et cliquez sur **OK** pour créer SQL Server, le groupe de ressources et les ressources spécifiées pour cette machine virtuelle. Vous pouvez surveiller le déploiement à partir du portail Azure. Le bouton **Notifications** en haut de l’écran affiche l’état de base du déploiement.

##<a id="Open"> Ouvrir la machine virtuelle à l’aide du Bureau à distance et achever la configuration
Procédez comme suit pour utiliser le Bureau à distance pour ouvrir la machine virtuelle :

1.	Une fois que la machine virtuelle Azure est créée, une icône pour la machine virtuelle apparaît dans le tableau de bord Azure. Cliquez sur l’icône pour afficher des informations sur la machine virtuelle.
1.	En haut du panneau de la machine virtuelle, cliquez sur **Connecter**. Le navigateur télécharge un fichier .rdp pour la machine virtuelle. Ouvrez le fichier .rdp.
1.	La Connexion Bureau à distance vous informe que le serveur de publication de cette connexion à distance ne peut être identifié et vous demande si vous souhaitez tout de même vous connecter. Cliquez sur **Connecter**.
1.	Dans la boîte de dialogue **Sécurité de Windows**, cliquez sur **Utiliser un autre compte**. Pour **Nom d’utilisateur**, saisissez le <machine name><nom d’utilisateur> que vous avez spécifié lorsque vous avez configuré la machine virtuelle.

Une fois que vous vous connectez à la machine virtuelle SQL Server, vous pouvez lancer SQL Server Management Studio et vous connecter avec l’authentification Windows à l’aide de vos informations d’identification d’administrateur local. Cela vous permet également de modifier les paramètres du pare-feu ou les paramètres de configuration de SQL Server post-approvisionnement si nécessaire.

##<a id="Connect"> Se connecter à SQL Server via Internet

Si vous souhaitez vous connecter à votre moteur de base de données SQL Server à partir d’Internet, plusieurs étapes sont requises, telles que la configuration du pare-feu, l’activation de l’authentification SQL Server et la configuration de votre groupe de sécurité réseau. Vous devez disposer d’une règle de groupe de sécurité réseau pour autoriser le trafic TCP sur le port 1433.

Si vous utilisez le portail pour approvisionner une image de machine virtuelle SQL Server avec Resource Manager, ces étapes ont été effectuées pour vous lorsque vous avez sélectionné **Public** pour l’option de connectivité SQL et activé l’authentification SQL Server. Toutefois, il reste quelques étapes à effectuer pour accéder à votre instance SQL Server sur Internet.

>[AZURE.NOTE] Si vous n’avez pas sélectionné Public lors de l’approvisionnement, des étapes supplémentaires sont nécessaires pour accéder à votre instance SQL Server via Internet. Pour plus d’informations, consultez [Se connecter à une machine virtuelle SQL Server (Resource Manager) | Microsoft Azure](virtual-machines-sql-server-connectivity-resource-manager.md).

Les étapes suivantes ne sont pas requises si vous devez uniquement accéder à votre machine virtuelle localement ou à partir du même réseau virtuel.

> [AZURE.INCLUDE [Se connecter à SQL Server dans une machine virtuelle (Resource Manager)](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

##<a id="Next">Étapes suivantes
Pour en savoir plus sur l’utilisation de SQL Server dans Azure, consultez [SQL Server sur Azure Virtual Machines](../articles/virtual-machines/virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=AcomDC_0128_2016-->
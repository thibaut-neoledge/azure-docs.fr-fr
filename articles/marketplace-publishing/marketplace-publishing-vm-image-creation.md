<properties
   pageTitle="Création d’une image de machine virtuelle pour Azure Marketplace | Microsoft Azure"
   description="Instructions détaillées sur la façon de créer une image de machine virtuelle pour Azure Marketplace et en permettre la commercialisation auprès des autres utilisateurs."
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="10/09/2015"
   ms.author="hascipio; v-divte"/>

# Guide à la création d’une image de machine virtuelle pour Azure Marketplace

Cet article, **étape 2**, vous guide dans la préparation des disques durs virtuels (VHD) que vous allez déployer dans Azure Marketplace. Vos disques durs virtuels constituent le fondement de votre référence SKU. Le processus varie selon que vous fournissez une référence SKU Linux ou Windows. Cet article aborde ces deux scénarios. Ce processus peut être exécuté parallèlement à la procédure de [création de compte et d’enregistrement][link-acct-creation].

## 1\. Définir les offres et les références SKU

Dans cette section, vous apprenez à définir les offres et leurs références SKU associées.

Une offre constitue le « parent » des références SKU associées. Vous pouvez proposer plusieurs offres. Il vous revient de définir les modalités de structuration de vos offres. Lorsqu’une offre est déployée dans un environnement intermédiaire, les références SKU associées le sont également. Choisissez avec soin vos identificateurs SKU, car ceux-ci apparaissent dans l’URL.

- Azure.com – http://azure.microsoft.com/marketplace/partners/{PartnerNamespace}/{OfferIdentifier}-{SKUidentifier}

- Version préliminaire du portail Azure : https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{SKUIDdentifier}

Une référence SKU désigne le nom commercial d’une image de machine virtuelle. Une image de machine virtuelle contient un disque de système d’exploitation et aucun ou plusieurs disques de données. Il s’agit en fait du profil de stockage complet d’une machine virtuelle. Chaque disque a un disque dur virtuel est nécessaire par disque. Même les disques de données vides ont besoin de la création d’un disque dur virtuel.

Quel que soit le système d’exploitation que vous utilisez, ajoutez uniquement le nombre minimal de disques de données requis par la référence SKU. Les clients ne suppriment pas les disques qui font partie d’une image lors du déploiement, mais peuvent toujours ajouter des disques pendant ou après le déploiement le cas échéant.

### 1\.1 Ajouter une offre

1. Connectez-vous au [Portail de publication][link-pubportal] à l’aide de votre compte de vendeur.
2. Accédez à l’onglet **Machines virtuelles** du Portail de publication. Dans le champ de saisie qui s’affiche, entrez le nom de votre offre. Le nom de l’offre correspond généralement au nom du produit ou service que vous prévoyez de vendre dans Azure Marketplace.
3. Sélectionnez **Créer**.

### 1\.2 Définir une référence SKU
Une fois que vous avez ajouté une offre, vous devez définir/identifier vos références SKU. Vous pouvez proposer plusieurs offres, chaque offre étant elle-même associée à plusieurs références SKU. Lorsqu’une offre est déployée dans un environnement intermédiaire, les références SKU associées le sont également.

1. **Ajoutez une référence SKU.** La référence SKU requiert un identificateur qui est utilisé dans l’URL. L’identificateur doit être unique dans votre profil de publication. Il n’y a pas de risque de conflit avec les identificateurs d’autres éditeurs.

> [AZURE.NOTE]Les identificateurs de l’offre et de la référence SKU s’afficheront dans l’URL de l’offre sur Marketplace.

2. **Ajoutez une description de votre référence SKU.** Des descriptions résumées sont visibles pour les clients, donc, vous devez les rendre facilement lisibles. Ces informations ne doivent pas être verrouillées jusqu’à la phase « Envoi vers staging »/ intermédiaire.
3. Si vous utilisez des références SKU basées sur Windows, suivez les liens suggérés pour acquérir les versions approuvées de Windows Server.

## 2\. Créer un disque dur virtuel compatible avec Azure (Linux)
Cette section se concerne sur les meilleures pratiques pour la création d’une image de machine virtuelle Linux pour Azure Marketplace. Pour consulter la procédure détaillée, consultez la documentation suivante : [Création et téléchargement d’un disque dur virtuel contenant le système d’exploitation Linux][link-azure-vm-1]

> [AZURE.TIP]La plupart des étapes suivantes (installation de l’agent, paramètres de démarrage du noyau) sont déjà prises en charge pour les images Linux disponibles à partir de la galerie d’images de Microsoft Azure. Par conséquent, vous pouvez gagner du temps en travaillant à partir de l’une de ces images sans avoir à configurer une image Linux ne connaissant pas Azure.

### 2\.1 Choisir la taille du disque dur virtuel
Les références SKU publiées (images de machine virtuelle) doivent être compatibles avec toutes les tailles de machine virtuelle prenant en charge le nombre de disques pour la référence SKU. Vous pouvez fournir des instructions en matière de taille. Celles-ci seront toutefois traitées comme des recommandations et leur application ne sera pas obligatoire.

1. Disque dur virtuel du système d’exploitation Linux : le disque dur virtuel de système d’exploitation Linux de votre image de machine virtuelle doit être créé en tant que disque dur virtuel au format fixe de 30 Go à 50 Go. La taille ne peut pas être inférieure à 30 Go. Si la taille physique est inférieure à celle du disque dur virtuel, le disque dur virtuel doit être fragmenté. Les disques durs virtuels Linux dont la taille est supérieure à 50 Go seront évalués individuellement. Si vous possédez déjà un disque dur virtuel dans un autre format, vous pouvez utiliser l’[applet de commande PowerShell Convert-VHD pour modifier le format][link-technet-1].
2. Disque dur virtuel de type disque de données : les disques de données peuvent avoir une taille maximale de 1 To. Ce type de disque dur virtuel doit être créé comme disque dur virtuel au format fixe. Ils doivent également être fragmentés. Lorsque vous définissez la taille du disque, rappelez-vous que les clients ne peuvent pas redimensionner les disques durs virtuels dans une image.

### 2\.2 Vérifier que le dernier agent Linux Azure est installé
Lorsque vous préparez le disque dur virtuel du système d’exploitation, vérifiez que c’est l’[agent Linux Azure][link-azure-vm-2] le plus récent qui est installé Utilisation de packages RPM ou Deb. Le package est souvent nommé walinuxagent ou WALinuxAgent, mais vérifiez auprès de votre distribution pour en avoir la certitude. L’agent fournit des fonctions clés pour le déploiement d’une infrastructure Linux IaaS dans Azure, telles que des fonctionnalités de déploiement de machines virtuelles et de mise en réseau.

Bien que l’agent puisse être configuré de différentes façons, nous vous recommandons d’utiliser une configuration d’agent générique afin d’optimiser la compatibilité. Il est possible d’installer l’agent manuellement, mais il est fortement recommandé d’utiliser les packages préconfigurés avec votre distribution si c’est possible.

Si vous choisissez d’installer l’agent manuellement à partir du [référentiel GitHub][link-github-waagent], commencez par copier le fichier « waagent » dans /usr/sbin, puis exécutez les commandes suivantes sous le répertoire racine :

    # chmod 755 /usr/sbin/waagent
    # /usr/sbin/waagent -install

Le fichier de configuration de l’agent sera placé dans /etc/waagent.conf.

### 2\.3 Vérifier que les bibliothèques requises sont incluses
Outre l’agent Linux Azure, les bibliothèques suivantes doivent être incluses :

1. Les [services d’intégration Linux][link-intsvc] version 3.0 ou ultérieure doivent être activés sur votre noyau. Voir [Conditions requises pour le noyau Linux](../virtual-machines/virtual-machines-linux-create-upload-vhd-generic/#linux-kernel-requirements)
2. [Kernel Patch](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c) pour la stabilité Azure I/O (probablement inutile pour les noyaux récents. Doit faire l’objet d’une vérification)
3. [Python][link-python] 2.6 ou version ultérieure
4. Python pyasn1, s’il n’est pas déjà installé
5. [OpenSSL][link-openssl] (version 1.0 ou ultérieure recommandée)

### 2\.4 Configurer les partitions de disque
Nous vous conseillons de ne pas utiliser le gestionnaire de volume logique. Créez une partition à racine unique pour le disque système d’exploitation. N’utilisez pas de partition swap sur le système d’exploitation ou le disque de données. Nous vous recommandons de supprimer une partition SWAP, même si celle-ci n’est pas montée dans /etc/fstab. Le cas échéant, une partition SWAP peut être créée sur le disque de ressources local (/dev/sdb) par l’agent Linux.

### 2\.5 Ajouter les paramètres requis pour la ligne de démarrage du noyau
Les paramètres suivants doivent également être ajoutés à la ligne de démarrage du noyau :

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

Ainsi, le support Azure peut fournir aux clients une sortie console série si nécessaire. Cela permet également un délai d’expiration approprié pour le montage du disque de système d’exploitation à partir du stockage cloud. Même si votre référence SKU empêche les clients finaux d’accéder directement par SSH à la machine virtuelle, la sortie série de la console doit être activée.

### 2\.6 Inclure un serveur SSH par défaut
Nous recommandons vivement d’activer SSH pour le client. Si le serveur SSH est activé, ajoutez l’option de maintien de la connexion au fichier de configuration du serveur sshd avec l’option suivante : **ClientAliveInterval 180**.. Il est certes recommandé d’utiliser la valeur 180. Cependant la plage autorisée est comprise entre 30 et 235. Toutes les applications ne souhaitent pas donner aux clients un accès SSH direct à la machine virtuelle. Si le protocole SSH est bloqué de façon explicite, il n’est pas utile de définir l’option **ClientAliveInterval**.

### 2\.7 Respecter les spécifications réseau
Les spécifications réseau suivantes sont requises pour une image de machine virtuelle Linux compatible avec Azure.

- Dans de nombreux cas, il est préférable de désactiver NetworkManager, à l’exception des systèmes CentOS 7.x (et leurs dérivés) pour lesquels il convient de laisser NetworkManager activé.
- La configuration réseau doit être contrôlée via les scripts **ifup**et **ifdown**. L’agent Linux peut utiliser ces commandes pour redémarrer la mise en réseau lors du déploiement.
- Vous ne devez pas utiliser de configuration réseau personnalisée. Le fichier resolv.conf doit être supprimé à la fin de la procédure. Cette opération est généralement effectuée dans le cadre de la désaffectation (voir [Guide d’utilisation de l’agent Linux Azure](../virtual-machines/virtual-machines-linux-agent-user-guide/)). Vous pouvez également effectuer cette étape manuellement en utilisant la commande suivante :

        rm /etc/resolv.conf

- Le périphérique réseau doit être monté au démarrage et utiliser le protocole DHCP.
- Le protocole IPv6 n’est pas pris en charge sur Azure. Si cette propriété est activée, il ne fonctionnera pas.

### 2\.8 S’assurer que les meilleures pratiques de sécurité sont en place
Les références SKU dans Azure Marketplace doivent impérativement suivre les pratiques recommandées en matière de sécurité, Elles comprennent notamment les éléments suivants :

- Installez tous les correctifs de sécurité pour votre distribution.
- Suivez les recommandations relatives à la sécurité de la distribution.
- Évitez de créer des comptes par défaut (lesquels ne changent pas) entre les instances de configuration.
- Effacez les entrées de l’historique de bash.
- Incluez le logiciel iptables (pare-feu), mais n’activez aucune règle. Ceci permet d’offrir une expérience par défaut cohérente aux clients. Les clients qui souhaitent utiliser un pare-feu de machine virtuelle pour une configuration supplémentaire peuvent définir les règles d’iptables de façon à satisfaire leurs besoins spécifiques.

### 2\.9 Généraliser l’image
Toutes les images dans Azure Marketplace doivent être réutilisables de manière générique, ce qui implique de supprimer certaines de leurs caractéristiques de configuration. Pour effectuer ceci dans Linux, vous devez désaffecter le disque dur virtuel de système d’exploitation.

La commande Linux suivante permet d’effectuer une désaffectation :

        # waagent -deprovision

Cette commande automatique :

- supprime la configuration du serveur de noms dans /etc/resolv.conf,
- supprime les baux du client DHCP mis en cache,
- Réinitialise le nom d’hôte sur localhost.localdomain.

Il est recommandé de définir le fichier de configuration (/etc/waagent.conf) de façon à ce que les actions suivantes soient également effectuées :

- Définissez Provisioning.RegenerateSshHostKeyPair sur « y » dans le fichier de configuration pour supprimer toutes les clés d’hôte SSH.
- Définissez Provisioning.DeleteRootPassword sur « y » dans le fichier de configuration pour supprimer le mot de passe racine de /etc/shadow. Pour plus d’informations sur le contenu du fichier de configuration, consultez la section « CONFIGURATION » du fichier README disponible sur la page de référentiel de l’agent Github (rendez-vous sur le site [https://github.com/Azure/WALinuxAgent](https://github.com/Azure/WALinuxAgent) et faites défiler la page vers le bas).  

Vous avez à présent terminé la phase de généralisation de la machine virtuelle Linux. Arrêtez la machine virtuelle à partir du portail Azure, à l’aide de la ligne de commande ou depuis la machine virtuelle. Une fois la machine virtuelle désactivée, passez à l’étape 3.4.

## 3\. Créer un disque dur virtuel compatible avec Azure (Windows)
Cette section décrit la procédure de création d’une référence SKU basée sur Windows Server pour Azure Marketplace.

### 3\.1 Vérifier que vous utilisez les disques durs virtuels de base corrects
Le disque dur virtuel de système d’exploitation pour votre image de machine virtuelle doit être basé sur une image de base approuvée par Azure contenant Windows Server ou SQL Server.

Pour commencer, créez une machine virtuelle à partir d’une des images suivantes, situées dans le [portail Microsoft Azure][link-azure-portal] :

- Windows Server ([2012 R2 Datacenter][link-datactr-2012-r2], [2012 Datacenter][link-datactr-2012], [2008 R2 SP1][link-datactr-2008-r2])
- SQL Server 2014 ([Enterprise][link-sql-2014-ent], [Standard][link-sql-2014-std], [Web][link-sql-2014-web])
- SQL Server 2012 SP2 ([Enterprise][link-sql-2012-ent], [Standard][link-sql-2012-std], [Web][link-sql-2012-web])

Ces liens sont également disponibles dans le Portail de publication sous la page des références SKU.

> [AZURE.TIP]Si vous utilisez le portail Azure actuel ou PowerShell, les images Windows Server publiées à compter du 8 septembre 2014 sont approuvées.


### 3\.2 Créer votre machine virtuelle Windows
À partir du Portail Microsoft Azure, vous pouvez créer votre machine virtuelle sur la base d’une image de base approuvée en suivant quelques étapes simples. Voici une vue d’ensemble du processus.

1. À partir de la page des images de base, sélectionnez **Créer une machine virtuelle** pour accéder au nouveau [Portail Microsoft Azure][link-azure-portal].

    ![dessin][img-acom-1]

2. Connectez-vous au portail en utilisant le compte Microsoft et mot de passe de l’abonnement Azure que vous voulez utiliser.
3. Suivez les invites pour créer une machine virtuelle à l’aide de l’image de base que vous avez sélectionnée. Vous devez fournir un nom d’hôte (nom de l’ordinateur), un nom d’utilisateur (administrateur inscrit) et un mot de passe correspondant à la machine virtuelle.

    ![dessin][img-portal-vm-create]

4. Sélectionnez la taille de la machine virtuelle à déployer :

    a. Si vous envisagez de développer le disque dur virtuel localement, la taille n’a pas d’importance. Vous pouvez utiliser une des machines virtuelles plus petites.

    b. Si vous envisagez de développer l’image dans Azure, vous pouvez utiliser les tailles de machine virtuelle recommandées pour l’image sélectionnée.

    c. Pour plus d’informations sur la tarification, consultez le sélecteur de **niveaux de facturation recommandés** inclus dans le portail. Celui-ci indique les trois tailles recommandées fournies par l’éditeur (dans ce cas, l’éditeur est Microsoft).

    ![dessin][img-portal-vm-size]

5. Définissez les propriétés :

    a. Pour accélérer le déploiement, vous pouvez conserver les valeurs par défaut des propriétés qui figurent sous **Configuration facultative** et **Groupe de ressources**.

    b. Sous **Compte de stockage**, vous pouvez éventuellement sélectionner le compte de stockage du disque dur virtuel.

    c. Sous **Groupe de ressources**, vous pouvez éventuellement sélectionner le groupe logique dans lequel placer la machine virtuelle.
6. Sélectionnez l’**emplacement** de déploiement :

    a. Si vous envisagez de développer le disque dur virtuel localement, l’emplacement n’a pas d’importance, car vous téléchargerez l’image sur Azure ultérieurement.

    b. Si vous envisagez de développer l’image dans Azure, vous pouvez utiliser dès le départ une des régions Microsoft Azure basées aux États-Unis. Ceci permet d’accélérer le processus de copie du disque dur virtuel effectué automatiquement par Microsoft lorsque vous soumettez votre image à des fins de certification.

    ![dessin][img-portal-vm-location]

7. Cliquez sur **Create**. La machine virtuelle commence à se déployer. L’opération ne dure que quelques minutes. Vous pouvez ensuite commencer à créer l’image pour votre référence SKU.

### 3\.3 Développer votre disque dur virtuel dans le cloud
Il est vivement recommandé de développer votre disque dur virtuel dans le cloud à l’aide du protocole RDP (Remote Desktop Protocol). Vous pouvez vous connecter au bureau à distance à l’aide des nom d’utilisateur et mot de passe spécifiés pendant l’approvisionnement.

> [AZURE.IMPORTANT]Si vous développez votre disque dur virtuel sur site (ce qui n’est pas recommandé), consultez la page [Création d’une image de machine virtuelle sur site](marketplace-publishing-vm-image-creation-on-premise.md). Il est inutile de télécharger votre disque dur virtuel si vous développez dans le cloud.


**Se connecter via RDP à l’aide du [Portail Microsoft Azure][link-azure-portal]**

1. Sélectionnez **Parcourir tout** > **Machines virtuelles**.
2. Le panneau Machines virtuelles s’ouvre. Assurez-vous que la machine virtuelle à laquelle vous voulez vous connecter est en cours d’exécution, puis sélectionnez-la dans la liste des machines virtuelles déployées.
3. Un panneau s’ouvre, qui décrit la machine virtuelle sélectionnée. Dans la partie supérieure, cliquez sur **Se connecter**.
4. Vous êtes invité à entrer les nom d’utilisateur et mot de passe que vous avez spécifiés lors du déploiement.

**Se connecter via RDP à l’aide de PowerShell**

Pour télécharger un fichier de bureau à distance sur une machine locale, utilisez l’[applet de commande Get-AzureRemoteDesktopFile][link-technet-2]. Pour utiliser cette applet de commande, vous devez connaître les noms du service et de la machine virtuelle. Si vous avez créé la machine virtuelle à partir du [Portail Microsoft Azure][link-azure-portal], ces informations sont disponibles sous Propriétés de la machine virtuelle :

1. Dans le Portail Microsoft Azure, sélectionnez **Parcourir** > **Machines virtuelles**.
2. Le panneau Machines virtuelles s’ouvre. Sélectionnez la machine virtuelle que vous avez déployée.
3. Un panneau s’ouvre, qui décrit la machine virtuelle sélectionnée.
4. Cliquez sur **Propriétés**.
5. La première portion du nom de domaine est le nom du service. Le nom d’hôte est celui de la machine virtuelle.

    ![dessin][img-portal-vm-rdp]

6. L’applet de commande qui permet de télécharger le fichier RDP pour la machine virtuelle créée sur le Bureau local de l’administrateur se présente comme suit.

        Get‐AzureRemoteDesktopFile ‐ServiceName “baseimagevm‐6820cq00” ‐Name “BaseImageVM” –LocalPath “C:\Users\Administrator\Desktop\BaseImageVM.rdp”

Des informations supplémentaires sont disponibles sur RDP dans l’article [Se connecter à une machine virtuelle Azure avec RDP ou SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx) sur MSDN.

**Configurer une machine virtuelle et créer votre référence SKU**

Une fois le disque dur virtuel de système d’exploitation téléchargé, utilisez Hyper-V et configurez une machine virtuelle pour commencer à créer votre référence SKU. L’article TechNet [Installer le rôle Hyper-V et configurer un ordinateur virtuel](http://technet.microsoft.com/library/hh846766.aspx) décrit la procédure détaillée.

### 3\.4 Choisir la taille du disque dur virtuel
Le disque dur virtuel de système d’exploitation Windows de votre image de machine virtuelle doit être créé en tant que disque dur virtuel au format fixe de 128 Go.

Si la taille physique est inférieure à 128 Go, le disque dur virtuel doit être fragmenté. Les images Windows et SQL Server de base fournies satisfont déjà les exigences requises alors, ne modifiez pas le format ou la taille du disque dur virtuel obtenu.

Les disques de données peuvent avoir une taille maximale de 1 To. Lorsque vous choisissez la taille du disque, rappelez-vous que les clients ne peuvent pas redimensionner les disques durs virtuels dans une image lors du déploiement. Ce type de disque dur virtuel doit être créé comme disque dur virtuel au format fixe. Ils doivent également être fragmentés. Les disques de données peuvent être vides ou contenir des données.


### 3\.5 Installer les derniers correctifs Windows
Les images de base contiennent les derniers correctifs jusqu’à leur date de publication. Avant de publier le disque dur virtuel de système d’exploitation que vous avez créé, vérifiez que Windows Update a été exécuté et que les dernières mises à jour de sécurité critiques et importantes ont été installées.

### 3\.6 Effectuer les autres tâches de configuration et de planification utiles
Si d’autres étapes de configuration sont requises, vous pouvez utiliser une tâche planifiée exécutée au démarrage pour apporter des modifications finales à la machine virtuelle après que celle-ci a été déployée :

- Il est recommandé de supprimer automatiquement la tâche suite à une exécution réussie.
- Aucune configuration ne doit utiliser de lecteurs autres que C ou D, car ceux-ci sont les deux seuls qui lecteurs qui existent à coup sûr. Le lecteur C est le disque du système d’exploitation et le lecteur D est le disque local temporaire.

### 3\.7 Généraliser l’image
Toutes les images dans Azure Marketplace doivent être réutilisables de façon générale. En d’autres termes, le disque dur virtuel de système d’exploitation doit être généralisé :

- Pour Windows, l’image doit être préparée avec « sysprepped » et les opérations de configuration effectuées doivent toutes prendre en charge la commande **sysprep**.
- Vous pouvez exécuter la commande qui suit à partir du répertoire % windir%\\System32\\Sysprep.

        sysprep.exe /generalize /oobe /sshutdown

  Des instructions sur la préparation du système d’exploitation avec Sysprep sont fournies à l’étape de l’article MSDN intitulée [Création et téléchargement d’un disque dur virtuel Windows Server dans Azure](../virtual-machines/virtual-machines-create-upload-vhd-windows-server/).

## 4\. Déployer une machine virtuelle à partir de vos disques durs virtuels
Une fois les disques durs virtuels (disque dur virtuel de système d’exploitation généralisé, et zéro ou plusieurs disques durs virtuels de disque de données) téléchargés sur un compte de stockage Azure, vous pouvez les inscrire en tant qu’image de machine virtuelle d’utilisateur. Vous pouvez tester cette image. Veuillez noter que, comme votre disque dur virtuel de système d’exploitation est généralisé, vous ne pouvez pas déployer directement la machine virtuelle en fournissant l’URL du disque dur virtuel.

Pour en savoir plus sur les images de machine virtuelle, consultez les articles de blog suivants :

- [VM Image](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [VM Image PowerShell How To](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)
- [À propos d’images de machines virtuelles dans Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)

### 4\.1 Créer une image de machine virtuelle d’utilisateur
Pour créer une image de machine virtuelle d’utilisateur à partir de votre référence SKU et commencer à déployer plusieurs machines virtuelles, vous devez utiliser l’[API REST Create VM Image](http://msdn.microsoft.com/library/azure/dn775054.aspx) pour inscrire les disques durs virtuels en tant qu’image de machine virtuelle.

Vous pouvez utiliser l’applet de commande **Invoke-WebRequest** pour créer une image de machine virtuelle à partir de PowerShell. Le script PowerShell qui suit montre comment créer une image de machine virtuelle avec un disque de système d’exploitation et un disque de données. Notez qu’un abonnement et la session PowerShell doivent déjà être configurés.

        # Image Parameters to Specify
        $ImageName=’ENTER-YOUR-OWN-IMAGE-NAME-HERE’
        $Label='ENTER-YOUR-LABEL-HERE'
        $Description='DESCRIBE YOUR IMAGE HERE’
        $osCaching='ReadWrite'
        $os = 'Windows'
        $state = 'Generalized'
        $osMediaLink = 'https://mystorageaccount.blob.core.windows.net/vhds/myosvhd.vhd'
        $dataCaching='None'
        $lun='1'
        $dataMediaLink='http://mystorageaccount.blob.core.windows.net/vhds/mydatavhd.vhd'
        # Subscription-Related Properties
        $SrvMngtEndPoint='https://management.core.windows.net'
        $subscription = Get-AzureSubscription -Current -ExtendedDetails
        $certificate = $subscription.Certificate
        $SubId = $subscription.SubscriptionId
        $body =  
        "<VMImage xmlns=`"http://schemas.microsoft.com/windowsazure`" xmlns:i=`"http://www.w3.org/2001/XMLSchema-instance`">" + Name>" + $ImageName + "</Name>" +
        "<Label>" + $Label + "</Label>" +
        "<Description>" + $Description + "</Description>" + "<OSDiskConfiguration>" +
        "<HostCaching>" + $osCaching + "</HostCaching>" +
        "<OSState>" + $state + "</OSState>" +
        "<OS>" + $os + "</OS>" +
        "<MediaLink>" + $osMediaLink + "</MediaLink>" +
        "</OSDiskConfiguration>" +
        "<DataDiskConfigurations>" +
        "<DataDiskConfiguration>" +
        "<HostCaching>" + $dataCaching + "</HostCaching>" +
        "<Lun>" + $lun + "</Lun>" +
        "<MediaLink>" + $dataMediaLink + "</MediaLink>" +
        "</DataDiskConfiguration>" +
        "</DataDiskConfigurations>" +
        "</VMImage>"
        $uri = $SrvMngtEndPoint + "/" + $SubId + "/" + "services/vmimages" $headers = @{"x-ms-version"="2014-06-01"}
        $response = Invoke-WebRequest -Uri $uri -ContentType "application/xml" -Body
        $body -Certificate $certificate -Headers $headers -Method POST
        if ($response.StatusCode -ge 200 -and $response.StatusCode -lt 300)
        {
        echo "Accepted"
        } else {
        echo "Not Accepted" }
        $opId = $response.Headers.'x-ms-request-id'
        $uri2 = $SrvMngtEndPoint + "/" + $SubId + "/" + "operations" + "/" + $opId $response2 = Invoke-WebRequest -Uri $uri2 -ContentType "application/xml" -
        Certificate $certificate -Headers $headers -Method GET
        $response2.RawContent


En exécutant ce script, vous créez une image de machine virtuelle d’utilisateur avec le nom que vous avez fourni au paramètre ImageName (myVMImage). Celle-ci est constituée d’un disque de système d’exploitation et d’un disque de données.

Cette API est une opération asynchrone et renvoie la réponse un code « 202 - Accepté ». Pour déterminer si l’image de machine virtuelle a été créée, vous devez interroger l’état de l’opération. La valeur x-ms-request-id dans la réponse indique l’ID de l’opération. Cet ID doit être défini dans $opId ci-dessous.

        $opId = #Fill In With Operation ID
        $uri2 = $SrvMngtEndPoint + "/" + $SubId + "/" + "operations" + "/" + "opId"
        $response2 = Invoke‐WebRequest ‐Uri $uri2 ‐ContentType "application/xml" ‐Certificate $certificate ‐Headers $headers ‐Method GET

Pour créer une image de machine virtuelle à partir d’un disque dur virtuel de système d’exploitation et un autre disque de données vide (le disque dur virtuel pour ce disque n’est pas créé) en utilisant l’API Create VM Image, utilisez le script suivant.

        # Image Parameters to Specify
        $ImageName=’myVMImage’
        $Label='IMAGE_LABEL'
        $Description='My VM Image to Test’
        $osCaching='ReadWrite'
        $os = 'Windows'
        $state = 'Generalized'
        $osMediaLink = 'http://mystorageaccount.blob.core.windows.net/containername/myOSvhd.vhd'
        $dataCaching='None'
        $lun='1'
        $emptyDiskSize= 32
        # Subscription-Related Properties
        $SrvMngtEndPoint='https://management.core.windows.net'
        $subscription = Get‐AzureSubscription –Current ‐ExtendedDetails
        $certificate = $subscription.Certificate
        $SubId = $subscription.SubscriptionId
        $body =
        "<VMImage xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema‐instance">" +
        "<Name>" + $ImageName + "</Name>" +
        "<Label>" + $Label + "</Label>" +
        "<Description>" + $Description + "</Description>" +
        "<OSDiskConfiguration>" +
        "<HostCaching>" + $osCaching + "</HostCaching>" +
        "<OSState>" + $state + "</OSState>" +
        "<OS>" + $os + "</OS>" +
        "<MediaLink>" + $osMediaLink + "</MediaLink>" +
        "</OSDiskConfiguration>" +
        "<DataDiskConfigurations>" +
        "<DataDiskConfiguration>" +
        "<HostCaching>" + $dataCaching + "</HostCaching>" +
        "<Lun>" + $lun + "</Lun>" +
        "<MediaLink>" + $dataMediaLink + "</MediaLink>" +
        "<LogicalDiskSizeInGB>" + $emptyDiskSize + "</LogicalDiskSizeInGB>" +
        "</DataDiskConfiguration>" +
        "</DataDiskConfigurations>" +
        "</VMImage>"
        $uri = $SrvMngtEndPoint + "/" + $SubId + "/" + "services/vmimages"
        $headers = @{"x‐ms‐version"="2014‐06‐01"}
        $response = Invoke‐WebRequest ‐Uri $uri ‐ContentType "application/xml" ‐Body $body ‐Certificate $certificate ‐Headers $headers ‐Method POST
        if ($response.StatusCode ‐ge 200 ‐and $response.StatusCode ‐lt 300)
        {
        echo "Accepted"
        }
        else
        {
        echo "Not Accepted"
        }

En exécutant ce script, vous créez une image de machine virtuelle d’utilisateur avec le nom que vous avez fourni au paramètre ImageName (myVMImage). Celle-ci est constituée d’un disque de système d’exploitation et d’un disque de données.

Cette API est une opération asynchrone et renvoie la réponse un code « 202 - Accepté ». Pour déterminer si l’image de machine virtuelle a été créée, vous devez interroger l’état de l’opération. La valeur x-ms-request-id dans la réponse indique l’ID de l’opération. Cet ID doit être défini dans $opId ci-dessous.

        $opId = #Fill In With Operation ID
        $uri2 = $SrvMngtEndPoint + "/" + $SubId + "/" + "operations" + "/" + "$opId"
        $response2 = Invoke-WebRequest -Uri $uri2 -ContentType "application/xml" Certificate $certificate -Headers $headers -Method GET

Pour créer une image de machine virtuelle à partir d’un disque dur virtuel de système d’exploitation et un autre disque de données vide (le disque dur virtuel pour ce disque n’est pas créé) en utilisant l’API Create VM Image, utilisez le script suivant.

        # Image Parameters to Specify
        $ImageName=’myVMImage’
        $Label='IMAGE_LABEL'
        $Description='My VM Image to Test’
        $osCaching='ReadWrite'
        $os = 'Windows'
        $state = 'Generalized'
        $osMediaLink =
        'http://mystorageaccount.blob.core.windows.net/containername/myOSvhd.vhd'
        $dataCaching='None'
        $lun='1'
        $emptyDiskSize= 32
        # Subscription-Related Properties
        $SrvMngtEndPoint='https://management.core.windows.net'
        $subscription = Get-AzureSubscription –Current -ExtendedDetails
        $certificate = $subscription.Certificate
        $SubId = $subscription.SubscriptionId
        $body =  
        "<VMImage xmlns=`"http://schemas.microsoft.com/windowsazure`" xmlns:i=`"http://www.w3.org/2001/XMLSchema-instance`">" +
        "<Name>" + $ImageName + "</Name>" +
        "<Label>" + $Label + "</Label>" +
        "<Description>" + $Description + "</Description>" + "<OSDiskConfiguration>" + "<HostCaching>" + $osCaching + "</HostCaching>" +
        "<OSState>" + $state + "</OSState>" +
        "<OS>" + $os + "</OS>" +
        "<MediaLink>" + $osMediaLink + "</MediaLink>" +
        "</OSDiskConfiguration>" +
        "<DataDiskConfigurations>" +
        "<DataDiskConfiguration>" +
        "<HostCaching>" + $dataCaching + "</HostCaching>" +
        "<Lun>" + $lun + "</Lun>" +
        "<MediaLink>" + $dataMediaLink + "</MediaLink>" +
        "<LogicalDiskSizeInGB>" + $emptyDiskSize + "</LogicalDiskSizeInGB>" + "</DataDiskConfiguration>" +
        "</DataDiskConfigurations>" +
        "</VMImage>"
        $uri = $SrvMngtEndPoint + "/" + $SubId + "/" + "services/vmimages"
        $headers = @{"x-ms-version"="2014-06-01"}
        $response = Invoke-WebRequest -Uri $uri -ContentType "application/xml" -Body $body Certificate $certificate -Headers $headers -Method POST
        if ($response.StatusCode -ge 200 -and $response.StatusCode -lt 300)
        { echo "Accepted"
        } else
        { echo "Not Accepted"
        }

En exécutant ce script, vous créez une image de machine virtuelle d’utilisateur avec le nom que vous avez fourni au paramètre ImageName (myVMImage). Il est constitué d’un disque dur de système d’exploitation basé sur le disque dur virtuel que vous avez transmis et d’un disque de données vide de 32 Go.

### 4\.2 Déployer une machine virtuelle à partir d’une image de machine virtuelle d’utilisateur
Pour déployer une machine virtuelle à partir d’une image de machine virtuelle d’utilisateur, vous pouvez utiliser le [Portail Azure](https://manage.windowsazure.com) ou PowerShell actuel.

**Déployer une machine virtuelle à partir du portail Azure actuel**

1. Cliquez sur **Nouveau** > **Calculer** > **Machine virtuelle** > **À partir de la galerie**.

    ![dessin][img-manage-vm-new]

2. Accédez à **Mes images**, puis sélectionnez l’image de machine virtuelle à partir de laquelle déployer une machine virtuelle :
  1. Sélectionnez celle-ci avec soin, car l’affichage **Mes images** répertorie les images de système d’exploitation et les images de machine virtuelle.
  2. L’examen du nombre de disques peut vous aider à déterminer le type d’image que vous déployez, car la majorité des images de machine virtuelle inclut plusieurs disques. Il est toutefois toujours possible d’avoir une image de machine virtuelle avec un seul disque de système d’exploitation dont le **nombre de disques** est défini sur 1.

    ![dessin][img-manage-vm-select]

3. Suivez les étapes de l’Assistant de création de machine virtuelle en spécifiant le nom de la machine virtuelle, sa taille, son emplacement, ainsi que les nom d’utilisateur et mot de passe.

**Déployer une machine virtuelle à partir de vos disques durs virtuels**

Pour déployer une grande machine virtuelle, vous pouvez utiliser les applets de commande suivantes à partir de l’image de machine virtuelle que vous venez de créer.

    $img = Get‐AzureVMImage ‐ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New‐AzureVMConfig ‐Name "VMImageVM" ‐InstanceSize "Large" ‐ImageName $img.ImageName | Add‐AzureProvisioningConfig ‐Windows ‐AdminUsername $user ‐Password $pass
    New‐AzureVM ‐ServiceName "VMImageCloudService" ‐VMs $myVM ‐Location "West US" ‐WaitForBoot

## 5\. Obtenir une certification pour votre image de machine virtuelle
L’étape suivante de la préparation de votre image de machine virtuelle pour Azure Marketplace consiste à certifier celle-ci.

Dans le cadre de ce processus, vous allez exécuter un outil de certification spécial, télécharger les résultats de la vérification dans le conteneur Azure dans lequel se trouvent vos disques durs virtuels, ajouter une offre, définir votre référence SKU et soumettre votre image de machine virtuelle pour certification.

### 5\.1 Téléchargez exécutez l’outil « Certification Test Tool for Azure Certified »
L’outil de certification s’exécute sur une machine virtuelle, configurée à partir de votre image de machine virtuelle d’utilisateur, afin de garantir que l’image de machine virtuelle est compatible avec Microsoft Azure. Celui-ci vérifie que les recommandations et exigences relatives à la préparation de votre disque dur virtuel sont respectées. L’outil génère un rapport de compatibilité, qui doit être téléchargé sur le portail de publication lors de la demande de certification.

L’outil de certification peut être utilisé avec les machines virtuelles Windows et Linux. Il se connecte aux machines virtuelles Windows via PowerShell, et se connecte aux machines virtuelles Linux via SSH.Net :

1. Commencez par télécharger l’outil de certification sur le [site de téléchargement Microsoft][link-msft-download]
2. Ouvrez l’outil de certification, puis cliquez sur le bouton **Start New Test** (Commencer un nouveau test).
3. Dans l’écran **Test Information** (Informations sur le test), entrez un nom pour la série de tests.
4. Indiquez si votre machine virtuelle est exécutée sous Linux ou Windows. Selon la valeur choisie, sélectionnez les options suivantes.

### **Connexion de l’outil de certification Tool à une image de machine virtuelle Linux**

1. Sélectionnez le mode d’authentification SSH : mot de passe ou fichier de clé.
2. Si vous utilisez l’authentification basée sur le mot de passe, entrez le nom du système de nom de domaine (DNS), le nom d’utilisateur et le mot de passe.
3. Si vous utilisez l’authentification par fichier de clé, saisissez le nom DNS, le nom d’utilisateur et l’emplacement de la clé privée.

  ![Authentification de l’image de machine virtuelle Linux par mot de passe][img-cert-vm-pswd-lnx]

  ![Authentification de l’image de machine virtuelle Linux par fichier de clé][img-cert-vm-key-lnx]

### **Connecter l’outil de certification à une image de machine virtuelle sous Windows**

1. Saisissez le nom de domaine (DNS) complet de la machine virtuelle (par exemple, MyVMName.Cloudapp.net).
2. Saisissez le nom d’utilisateur et le mot de passe.

  ![Authentification de l’image de machine virtuelle Windows par mot de passe][img-cert-vm-pswd-win]

Après avoir sélectionné les options correctes pour votre image de machine virtuelle Linux ou Windows, cliquez sur **Test Connection** (Tester la connexion) pour vérifier que SSH.Net ou PowerShell a une connexion valide pour les tests. Une fois la connexion établie, sélectionnez **Suivant** pour démarrer le test.

Une fois le test terminé, vous recevrez les résultats (Succès/Échec/Avertissement) pour chaque élément du test.

![Scénarios de test pour une image de machine virtuelle Linux][img-cert-vm-test-lnx]

![Scénarios de test pour une image de machine virtuelle Windows][img-cert-vm-test-win]

Si l’un des tests échoue, votre image ne sera pas certifiée. Dans de cas, passez en revue la configuration requise et apportez éventuellement les modifications nécessaires.

Après le test automatisé, vous devez fournir des informations supplémentaires sur votre image de machine virtuelle via un écran de questionnaire. Répondez aux questions, puis sélectionnez **Suivant**.

![Questionnaire de l’outil de certification][img-cert-vm-questionnaire]

![Questionnaire de l’outil de certification][img-cert-vm-questionnaire-2]

Après avoir répondu au questionnaire, vous pouvez fournir des informations supplémentaires, telles que les informations d’accès SSH pour l’image de machine virtuelle Linux, et expliquer les raisons de n’importe quel échec d’évaluation. Vous pouvez télécharger les résultats des tests et les fichiers journaux pour les scénarios de test exécutés, ainsi que vos réponses au questionnaire. Enregistrez les résultats dans le même conteneur que vos disques durs virtuels.

![Enregistrer les résultats du test de certification][img-cert-vm-results]

### 5\.2 Obtenir l’URI de signature d’accès partagé pour vos images de machine virtuelle

Dans le cadre du processus de publication, vous devrez spécifier les URI (identificateur de ressource uniforme) qui permettent d’accéder aux disques durs virtuels que vous avez créés pour votre référence SKU. Microsoft doit accéder à ces disques durs virtuels pendant le processus de certification. Par conséquent, vous devez créer un URI de signature d’accès partagé pour chaque disque dur virtuel. Cet URI doit être saisi dans l’onglet **Images** du Portail de publication.

L’URI de signature d’accès partagé créé doit respecter les exigences suivantes :

- Lors de la génération des URI de signature d’accès partagé pour vos disques durs virtuels, les autorisations de liste et de lecture seule sont suffisantes. Ne fournissez pas d’accès en écriture ou en suppression.
- La durée d’accès doit être d’au minimum sept jours ouvrés à compter de la date de création de l’URI de signature d’accès partagé.
- Pour éviter les erreurs immédiates liées aux variations d’horloges, spécifiez un délai de 15 minutes avant l’heure actuelle.

Pour créer un URI de signature d’accès partagé, vous pouvez suivre les instructions fournies dans [Signatures d’accès partagé, partie 1 : présentation du modèle SAP][link-azure-1] et [Signatures d’accès partagé, partie 2 : création et utilisation d’une signature d’accès partagé avec le service blob Azure][link-azure-2].

Au lieu de générer une clé d’accès partagé à l’aide d’un code, vous pouvez également utiliser des outils de stockage tels que [Azure Storage Explorer][link-azure-codeplex].

**Utiliser Azure Storage Explorer pour générer une clé d’accès partagé**

1. Téléchargez [Azure Storage Explorer][link-azure-codeplex] 6 ou version ultérieure à partir de CodePlex.
2. Après l’installation, ouvrez l’application.
3. Cliquez sur **Ajouter un compte**.

    ![dessin][img-azstg-add]

4. Spécifiez le nom du compte de stockage, la clé du compte de stockage et le domaine des points de terminaison de stockage. **Ne cochez pas** la case « Use HTTPS » (Utiliser HTTPS)

    ![dessin][img-azstg-setup-1]

5. Azure Storage Explorer est maintenant connecté à votre compte de stockage spécifique. Il affichera immédiatement tous les conteneurs figurant dans le compte de stockage. Sélectionnez le conteneur dans lequel vous avez copié le fichier de disque dur virtuel du système d’exploitation (et les disques de données, s’ils s’appliquent à votre scénario).

    ![dessin][img-azstg-setup-2]

6. Après avoir sélectionné le conteneur d’objets Blobs, Azure Storage Explorer démarrera en affichant les fichiers associés au conteneur. Sélectionnez le fichier image (.vhd) qui doit être soumis.

    ![dessin][img-azstg-setup-3]

7. Après avoir sélectionné le fichier .vhd dans le conteneur, cliquez sur l’onglet **Security** (Sécurité).

    ![dessin][img-azstg-setup-4]

8. Dans la boîte de dialogue **sécurité du conteneur Blob**, conservez les valeurs par défaut de l’onglet **Niveau d’accès**, puis cliquez sur l’onglet **Signatures d’accès partagé**.

    ![dessin][img-azstg-setup-5]

9. Suivez les étapes ci-dessous pour générer une signature d’accès partagé URI de l’image .vhd :

    ![dessin][img-azstg-setup-6]

    a. **Accès autorisé à partir de** : afin de préserver l’heure UTC, sélectionnez le jour précédant la date actuelle. Par exemple, si la date actuelle est le 6 octobre 2014, sélectionnez 5/10/2014.

    b. **Accès autorisé à** : sélectionnez une date située au moins 7 à 8 jours après l’**accès autorisé à partir de** date.

    c. **Actions autorisées** : sélectionnez les autorisations **Liste** et **en lecture**.

    d. Si vous avez correctement sélectionné votre fichier .vhd, ce dernier apparaît dans **Blob name to access** (Nom d’objet Blob auquel accéder) avec l’extension .vhd.

    e. Cliquez sur **Generate Signature** (Générer la signature).

    f. Dans l’**URI de signature d’accès partagé ainsi généré de ce conteneur**, vérifiez les points suivants comme indiqué ci-dessus :

    - 	Assurez-vous que l’URL ne commence pas par « https ».
    - 	Assurez-vous que votre nom de fichier d’image et le « .vhd » sont dans l’URI.
    - 	À la fin de la signature, assurez-vous que « =rl » s’affiche. Cela montre qu’un accès en lecture seule et de liste a été fourni avec succès.

    g. Pour vous assurer que l’URI de signature d’accès partagé fonctionne, cliquez sur **Test in Browser** (Test dans le navigateur). Le téléchargement doit normalement démarrer.
10. Copiez l’URI de signature d’accès partagé. Cet URI doit être copié dans le Portail de publication.
11. Répétez ces étapes pour chaque disque dur virtuel de la référence SKU.

### 5\.3 Fournir des informations sur l’image de machine virtuelle et demander une certification dans le Portail de publication
Une fois que vous avez créé votre offre et votre référence SKU, vous devez renseigner les détails de l’image associés à la référence en question.

1. Accédez au [Portail de publication][link-pubportal] et connectez-vous à votre compte de vendeur.
2. Sélectionnez l’onglet **VM Images** (Images de machine virtuelle)
3. L’identificateur figurant en haut de la page est en fait l’identificateur de l’offre et non l’identificateur de la référence SKU.
4. Renseignez les propriétés sous la section **SKU** (Références SKU).

    ![dessin][img-pubportal-vm-skus]

5. Sous **Famille de système d’exploitation**, sélectionnez le type de système d’exploitation associé au disque dur virtuel du système d’exploitation.
6. Sous **Système d’exploitation**, décrivez le système d’exploitation. Envisagez un format du type Famille de systèmes d’exploitation, Type, Version et Mises à jour, Par exemple, « Windows Server Datacenter 2014 R2 ».
7. Sélectionnez trois tailles de machine virtuelle recommandées. Ces recommandations sont présentées à l’utilisateur dans le panneau Niveau de tarification du Portail de gestion Azure s’il décide d’acheter et de déployer votre image.

  >[AZURE.NOTE]Il s’agit uniquement de recommandations. Le client peut sélectionner n’importe quelle taille de machine virtuelle prenant en charge les disques spécifiés dans votre image.

8. Indiquez la version. Le champ version encapsule une version sémantique pour identifier le produit et ses mises à jour :
  -	Les versions doivent être au format X.Y.Z, où X, Y et Z sont des entiers.
  -	Les images contenues dans des références SKU différentes peuvent avoir différentes versions majeures et mineures.
  -	Les versions au sein d’une même référence SKU ne doivent impliquer que des modifications incrémentielles, qui augmentent la version du correctif (Z de X.Y.Z).
9. Dans la zone **URL de disque dur virtuel de système d’exploitation**, saisissez la signature d’accès partagé créée pour le disque dur virtuel du système d’exploitation.
10. Si des disques de données sont associés à cette référence SKU, sélectionnez le numéro d’unité logique que vous voulez utiliser pour le montage de ce disque de données lors du déploiement.
11. Dans la zone **URL de disque dur virtuel LUN X** entrez la signature d’accès partagé créée pour les données du premier disque dur virtuel.
12.	Cliquez sur **Charger les résultats des tests**.
13.	Cliquez sur **Request Certification** (Demander la certification).
14.	Répétez les étapes 11, 12 et 13 pour chaque disque dur virtuel de disque de données supplémentaire.

    ![dessin][img-pubportal-vm-skus-2]

## Étape suivante
Après avoir demandé la certification de vos références SKU d’image de machine virtuelle, vous pouvez aller plus loin en consultant le [guide de création de contenu marketing sur Azure Marketplace][link-pushstaging]. Dans cette étape du processus de publication, vous devez fournir le contenu marketing, la tarification et d’autres informations nécessaires avant l’**étape 3 : test de votre machine virtuelle en mode intermédiaire**, où vous testez divers scénarios de cas d’utilisation avant de déployer l’offre sur Azure Marketplace pour une visibilité publique et l’achat.

## Voir aussi
- [Mise en route : publication d’une offre dans Azure Marketplace](marketplace-publishing-getting-started.md)

[img-acom-1]: media/marketplace-publishing-vm-image-creation/vm-image-acom-datacenter.png
[img-portal-vm-size]: media/marketplace-publishing-vm-image-creation/vm-image-portal-size.png
[img-portal-vm-create]: media/marketplace-publishing-vm-image-creation/vm-image-portal-create-vm.png
[img-portal-vm-location]: media/marketplace-publishing-vm-image-creation/vm-image-portal-location.png
[img-portal-vm-rdp]: media/marketplace-publishing-vm-image-creation/vm-image-portal-rdp.png
[img-azstg-add]: media/marketplace-publishing-vm-image-creation/vm-image-storage-add.png
[img-azstg-setup-1]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup.png
[img-azstg-setup-2]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-2.png
[img-azstg-setup-3]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-3.png
[img-azstg-setup-4]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-4.png
[img-azstg-setup-5]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-5.png
[img-azstg-setup-6]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-6.png
[img-manage-vm-new]: media/marketplace-publishing-vm-image-creation/vm-image-manage-new.png
[img-manage-vm-select]: media/marketplace-publishing-vm-image-creation/vm-image-manage-select.png
[img-cert-vm-key-lnx]: media/marketplace-publishing-vm-image-creation/vm-image-certification-keyfile-linux.png
[img-cert-vm-pswd-lnx]: media/marketplace-publishing-vm-image-creation/vm-image-certification-password-linux.png
[img-cert-vm-pswd-win]: media/marketplace-publishing-vm-image-creation/vm-image-certification-password-win.png
[img-cert-vm-test-lnx]: media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-linux.png
[img-cert-vm-test-win]: media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-win.png
[img-cert-vm-results]: media/marketplace-publishing-vm-image-creation/vm-image-certification-results.png
[img-cert-vm-questionnaire]: media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire.png
[img-cert-vm-questionnaire-2]: media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire-2.png
[img-pubportal-vm-skus]: media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus.png
[img-pubportal-vm-skus-2]: media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-2.png

[link-pushstaging]: marketplace-publishing-push-to-staging.md
[link-github-waagent]: https://github.com/Azure/WALinuxAgent
[link-azure-codeplex]: https://azurestorageexplorer.codeplex.com/
[link-azure-2]: ../storage/storage-dotnet-shared-access-signature-part-2/
[link-azure-1]: ../storage/storage-dotnet-shared-access-signature-part-1/
[link-msft-download]: http://www.microsoft.com/download/details.aspx?id=44299
[link-technet-3]: https://technet.microsoft.com/library/hh846766.aspx
[link-technet-2]: https://msdn.microsoft.com/library/dn495261.aspx
[link-azure-portal]: https://portal.azure.com
[link-pubportal]: https://publish.windowsazure.com
[link-sql-2014-ent]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014enterprisewindowsserver2012r2/
[link-sql-2014-std]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014standardwindowsserver2012r2/
[link-sql-2014-web]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014webwindowsserver2012r2/
[link-sql-2012-ent]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012/
[link-sql-2012-std]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012/
[link-sql-2012-web]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012/
[link-datactr-2012-r2]: http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012r2datacenter/
[link-datactr-2012]: http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012datacenter/
[link-datactr-2008-r2]: http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2008r2sp1/
[link-acct-creation]: marketplace-publishing-accounts-creation-registration.md
[link-azure-vm-1]: ../virtual-machines/virtual-machines-linux-create-upload-vhd/
[link-technet-1]: https://technet.microsoft.com/library/hh848454.aspx
[link-azure-vm-2]: ../virtual-machines/virtual-machines-linux-agent-user-guide/
[link-openssl]: https://www.openssl.org/
[link-intsvc]: http://www.microsoft.com/download/details.aspx?id=41554
[link-python]: https://www.python.org/

<!---HONumber=Nov15_HO4-->
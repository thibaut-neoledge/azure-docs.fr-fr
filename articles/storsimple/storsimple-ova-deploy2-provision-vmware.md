<properties
   pageTitle="Déploiement de StorSimple Virtual Array - Configuration dans VMware"
   description="Ce deuxième didacticiel de déploiement de StorSimple Virtual Array implique la configuration d'un appareil virtuel dans VMware."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/12/2016"
   ms.author="alkohli"/>


# Déploiement de StorSimple Virtual Array - Configuration d’un tableau virtuel dans VMware

![](./media/storsimple-ova-deploy2-provision-vmware/vmware4.png)

## Vue d'ensemble 
Ce didacticiel sur la configuration s'applique aux Azure StorSimple Virtual Arrays (également appelés appareils virtuel StorSimple locaux ou appareils virtuels StorSimple) exécutant la version de mise à la disposition générale (mars 2016). Ce didacticiel explique comment configurer et vous connecter à StorSimple Virtual Array sur un système hôte exécutant VMware ESXi 5.5 et versions ultérieures. Cet article concerne le déploiement de StorSimple Virtual Array dans le portail Azure Classic, ainsi que dans Microsoft Azure Government Cloud.

Vous aurez besoin de privilèges d'administrateur pour configurer un appareil virtuel et vous y connecter. La configuration initiale peut prendre environ 10 minutes.


## Configuration des composants requis

Vous trouverez ici les conditions requises pour configurer un appareil virtuel sur un ordinateur hôte exécutant VMware ESXi 5.5 et versions ultérieures.

### Pour le service StorSimple Manager

Avant de commencer, assurez-vous que :

-   Vous avez terminé toutes les étapes de la rubrique [Préparation du portail pour StorSimple Virtual Array](storsimple-ova-deploy1-portal-prep.md).

-   Vous avez téléchargé l'image de l’appareil virtuel pour Vmware à partir du portail Azure. Pour plus d'informations, consultez l’[étape 3 : Téléchargement de l'image de l’appareil virtuel](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image).

### Pour l’appareil virtuel StorSimple 

Avant de déployer un appareil virtuel, assurez-vous que :

-   Vous avez accès à un système hôte exécutant Hyper-V (2008 R2 ou version ultérieure) permettant de configurer un appareil.

-   Le système hôte est en mesure de dédier les ressources suivantes pour configurer votre appareil virtuel :

	-   Un minimum de 4 cœurs.

	-   Au moins 8 Go de RAM.

	-   Une interface réseau.

	-   Un disque virtuel de 500 Go pour les données système.

### Pour le réseau dans le centre de données 

Avant de commencer, assurez-vous que :

-   Vous avez passé en revue la configuration réseau requise pour déployer un appareil virtuel StorSimple et configuré le réseau du centre de données conformément à la configuration requise. Pour plus d’informations, consultez la [Configuration système requise pour StorSimple Virtual Array](storsimple-ova-system-requirements.md).

## Configuration étape par étape 

Pour configurer et vous connecter à un appareil virtuel, vous devez effectuer les opérations suivantes :

1.  Assurez-vous que le système hôte dispose de suffisamment de ressources pour répondre aux exigences minimales de l’appareil virtuel.

2.  Configurez un appareil virtuel dans votre hyperviseur.

3.  Démarrez l’appareil virtuel et obtenez l'adresse IP.

## Étape 1 : Vérifier que le système hôte répond aux exigences minimales de l’appareil virtuel

Pour créer un appareil virtuel, vous avez besoin des éléments suivants :

-   Un accès à un système hôte exécutant VMware ESXi Server 5.5 et versions ultérieures.

-   Un client VMware vSphere sur votre système pour gérer l'hôte ESXi.

	-   Un minimum de 4 cœurs.

	-   Au moins 8 Go de RAM.

	-   Une interface réseau connectée au réseau et capable d’acheminer le trafic vers Internet. La bande passante Internet minimale doit être de 5 Mbits/s pour une utilisation optimale de l'appareil.

	-   Un disque virtuel de 500 Go pour les données.

## Étape 2 : Configuration d'un appareil virtuel dans l'hyperviseur

Procédez comme suit pour configurer un appareil virtuel dans votre hyperviseur.

1.  Copiez l'image de l’appareil virtuel sur votre système. Il s'agit de l'image que vous avez téléchargée via le portail Azure Classic. 
	1.  Vérifiez qu’il s’agit de l’image la plus récente que vous avez téléchargée. Si vous avez téléchargé l’image précédemment, téléchargez-la à nouveau pour être certain d’avoir l’image la plus récente. La dernière image comprend deux fichiers (au lieu d’un).
	2.  Prenez note de l'emplacement où vous avez copié l'image car vous l’utiliserez plus loin dans la procédure.

2.  Connectez-vous au serveur ESXi à l'aide du client vSphere. Vous devez disposer de privilèges d'administrateur pour créer une machine virtuelle.

	![](./media/storsimple-ova-deploy2-provision-vmware/image1.png)

1.  Dans le client vSphere, dans la section inventaire du volet gauche, sélectionnez le serveur ESXi.

	![](./media/storsimple-ova-deploy2-provision-vmware/image2.png)

1.  Vous allez d'abord télécharger le VMDK sur le serveur ESXi. Accédez à l’onglet **Configuration** dans le panneau droit. Sous **Matériel**, sélectionnez **Stockage**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image3.png)

1.  Dans le panneau droit, sous **Magasins de données**, sélectionnez le magasin de données dans lequel vous voulez charger le fichier VMDK. Le magasin de données doit avoir suffisamment d'espace libre pour les disques du système d'exploitation et des données.

	![](./media/storsimple-ova-deploy2-provision-vmware/image4.png)

1.  Cliquez avec le bouton droit et sélectionnez **Parcourir les magasins de données**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image5.png)

1.  Une fenêtre **Navigateur de magasins de données** s’ouvre.

	![](./media/storsimple-ova-deploy2-provision-vmware/image6.png)

1.  Dans la barre d’outils, cliquez sur l’icône ![](./media/storsimple-ova-deploy2-provision-vmware/image7.png) pour créer un dossier. Spécifiez et notez le nom du dossier. Vous utiliserez ce nom de dossier plus tard lors de la création d'une machine virtuelle (recommandée). Cliquez sur **OK**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image8.png)

1.  Le nouveau dossier s’affiche dans le panneau gauche du **Navigateur de magasins de données**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image9.png)

1.  Cliquez sur l’icône de chargement ![](./media/storsimple-ova-deploy2-provision-vmware/image10.png) et sélectionnez **Charger un fichier**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image11.png)

1.  Vous devez maintenant rechercher et sélectionner les fichiers VMDK que vous avez téléchargés. Deux fichiers sont disponibles. Sélectionnez un fichier à télécharger.

	![](./media/storsimple-ova-deploy2-provision-vmware/image12m.png)

1.  Cliquez sur **Ouvrir**. Ceci démarrera le téléchargement du fichier VMDK vers le magasin de données spécifié. Le téléchargement du fichier peut prendre plusieurs minutes.


1.  Une fois le téléchargement terminé, le fichier apparaît dans le magasin de données, dans le dossier que vous avez créé.

	![](./media/storsimple-ova-deploy2-provision-vmware/image14.png)

	Vous devez maintenant télécharger le deuxième fichier VMDK dans le même magasin de données.

1.  Revenez à la fenêtre du client vSphere. Sélectionnez le serveur ESXi, cliquez avec le bouton droit et choisissez **Nouvelle machine virtuelle**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image15.png)

1.  Une fenêtre **Créer une machine virtuelle** s'affiche. Sur la page **Configuration**, sélectionnez l’option **Personnalisé**. Cliquez sur **Suivant**.
![](./media/storsimple-ova-deploy2-provision-vmware/image16.png)

2.  Sur la page **Nom et emplacement**, spécifiez le nom de votre machine virtuelle. Ce nom doit correspondre au nom du dossier (recommandé) que vous avez spécifié à l'étape 8.

	![](./media/storsimple-ova-deploy2-provision-vmware/image17.png)

1.  Sur la page **Stockage**, sélectionnez le magasin de données que vous souhaitez utiliser pour configurer votre machine virtuelle.

	![](./media/storsimple-ova-deploy2-provision-vmware/image18.png)

1.  Sur la page **Version de la machine virtuelle**, sélectionnez **Version de la machine virtuelle : 8**. Notez que les versions 8 à 11 sont toutes prises en charge.

	![](./media/storsimple-ova-deploy2-provision-vmware/image19.png)

1.  Sur la page **Système d'exploitation invité**, sélectionnez le **système d'exploitation invité** **Windows**. Pour **Version**, dans la liste déroulante, sélectionnez **Microsoft Windows Server 2012 (64 bits)**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image20.png)

1.  Sur la page **Processeurs**, ajustez le **nombre de sockets virtuels** et le **nombre de cœurs par socket virtuel** afin que le **nombre total de cœurs** soit égal à 4 (ou plus). Cliquez sur **Next**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image21.png)

1.  Sur la page **Mémoire**, spécifiez 8 Go (ou plus) de RAM. Cliquez sur **Next**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image22.png)

1.  Sur la page **Réseau**, spécifiez le nombre d'interfaces réseau. La configuration minimale nécessite une interface réseau.

	![](./media/storsimple-ova-deploy2-provision-vmware/image23.png)

1.  Sur la page **Contrôleur SCSI**, acceptez la valeur par défaut **Contrôleur LSI Logic SAS**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image24.png)

1.  Sur la page **Sélectionner un disque**, choisissez **Utiliser un disque virtuel existant**. Cliquez sur **Next**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image25.png)

1.  Sur la page **Sélectionner un disque existant** sous **Chemin d'accès du fichier de disque**, cliquez sur **Parcourir**. Cette opération ouvre une boîte de dialogue **Parcourir les magasins de données**. Naviguez jusqu'à l'emplacement où vous avez téléchargé le fichier VMDK. Le magasin de données inclut désormais un seul fichier, car les deux fichiers que vous avez téléchargés au départ ont été fusionnés. Sélectionnez le fichier et cliquez sur **OK**. Cliquez sur **Next**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image26.png)

1.  Sur la page **Options avancées**, acceptez la valeur par défaut et cliquez sur **Suivant**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image27.png)

1.  Sur la page **Prêt à finaliser**, passez en revue tous les paramètres associés à la nouvelle machine virtuelle. cochez la case **Modifier les paramètres de la machine virtuelle avant de finaliser**. Cliquez sur **Continuer**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image28.png)

1.  Sur la page **Propriétés des machines virtuelles**, dans l’onglet **Matériel**, localisez le matériel du périphérique. Sélectionnez **Nouveau disque dur**. Cliquez sur **Add**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image29.png)

1.  Cette opération affiche la fenêtre **Ajout de matériel**. Sur la page **Type d'appareil**, sous **Choisir le type d’appareil à ajouter**, sélectionnez **Disque**, puis cliquez sur **Suivant**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image30.png)

1.  Sur la page **Sélectionner un disque**, choisissez **Créer un disque virtuel**. Cliquez sur **Next**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image31.png)

1.  Sur la page **Créer un disque**, définissez la **taille du disque** sur 500 Go (ou plus). Sous **Configuration du disque**, sélectionnez **Allocation dynamique**. Cliquez sur **Next**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image32.png)

1.  Sur la page **Options avancées**, acceptez la valeur par défaut.

	![](./media/storsimple-ova-deploy2-provision-vmware/image33.png)

1.  Sur la page **Prêt à finaliser**, passez en revue les options de disque. Cliquez sur **Terminer**.

	![](./media/storsimple-ova-deploy2-provision-vmware/image34.png)

1.  Vous revenez maintenant à la page des propriétés de la machine virtuelle. Un nouveau disque dur est ajouté à votre machine virtuelle. Cliquez sur **Terminer**.
  
	![](./media/storsimple-ova-deploy2-provision-vmware/image35.png)

2.  Sélectionnez votre machine virtuelle dans le volet droit, puis accédez à l’onglet **Résumé**. Passez en revue les paramètres de votre machine virtuelle.

	![](./media/storsimple-ova-deploy2-provision-vmware/image36.png)

Votre machine virtuelle est désormais configurée. L'étape suivante consiste à mettre cette machine sous tension et à obtenir l'adresse IP.

## Étape 3 : Démarrer l’appareil virtuel et obtenir l'adresse IP

Procédez comme suit pour démarrer votre appareil virtuel et vous y connecter.

#### Pour démarrer l’appareil virtuel

1.  Démarrez l’appareil virtuel. Dans le volet gauche du Gestionnaire de Configuration vSphere, sélectionnez votre appareil et cliquez avec le bouton droit pour afficher le menu contextuel. Sélectionnez **Alimentation** puis **Mise sous tension**. Cette opération met votre machine virtuelle sous tension. Vous pouvez afficher l'état de l’opération dans le volet inférieur **Tâches récentes** du client vSphere.

	![](./media/storsimple-ova-deploy2-provision-vmware/image37.png)

1.  Les étapes de configuration peuvent prendre plusieurs minutes. Une fois l'appareil en cours d'exécution, accédez à l’onglet **Console**. Appuyez sur Ctrl+Alt+Suppr pour vous connecter à l'appareil. Vous pouvez également pointer le curseur sur la fenêtre de la console puis appuyer sur Ctrl+Alt+Inser. L'utilisateur par défaut est *StorSimpleAdmin* et le mot de passe par défaut est *Password1*.

	![](./media/storsimple-ova-deploy2-provision-vmware/image38.png)

1.  Pour des raisons de sécurité, le mot de passe administrateur expire à la première connexion. Vous serez invité à modifier ce mot de passe.

	![](./media/storsimple-ova-deploy2-provision-vmware/image39.png)

1.  Entrez un mot de passe contenant au moins 8 caractères. Le mot de passe doit contenir 3 caractères sur 4 en majuscules, minuscules, chiffres et caractères spéciaux. Entrez de nouveau le mot de passe pour le confirmer. Un message vous avertit que le mot de passe a été modifié.

	![](./media/storsimple-ova-deploy2-provision-vmware/image40.png)

1.  Une fois le mot de passe modifié, l’appareil virtuel peut se réamorcer. Attendez la fin de la tâche. La console Windows PowerShell de l'appareil peut s’afficher avec une barre de progression.

	![](./media/storsimple-ova-deploy2-provision-vmware/image41.png)

1.  Les étapes 6 à 8 s'appliquent uniquement lors de l'amorçage dans un environnement non DHCP. Si vous êtes dans un environnement DHCP, ignorez ces étapes et passez à l'étape 9. Si vous avez démarré votre appareil dans un environnement non DHCP, vous verrez l'écran suivant.

	![](./media/storsimple-ova-deploy2-provision-vmware/image42m.png)

	Vous devez maintenant configurer le réseau.

1.  Utilisez la commande `Get-HcsIpAddress` pour répertorier les interfaces réseau activées sur votre appareil virtuel. Si votre appareil possède une seule interface réseau activée, le nom par défaut affecté à cette interface est `Ethernet`.

	![](./media/storsimple-ova-deploy2-provision-vmware/image43m.png)

1.  Utilisez l’applet de commande `Set-HcsIpAddress` pour configurer le réseau. Voici un exemple :


    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

	![](./media/storsimple-ova-deploy2-provision-vmware/image44.png)

1.  Une fois l'installation initiale terminée et l’appareil démarré, vous verrez le texte de la bannière de l’appareil. Notez l'adresse IP et l'URL affichées dans le texte de la bannière pour gérer l'appareil. Vous utiliserez cette adresse IP pour vous connecter à l'interface utilisateur web de votre appareil virtuel et pour finaliser la configuration et l’inscription locales.

	![](./media/storsimple-ova-deploy2-provision-vmware/image45.png)


1. (Facultatif) Effectuez cette étape uniquement si vous déployez votre appareil dans Government Cloud. Vous allez maintenant activer le mode FIPS (Federal Information Processing Standard) sur votre appareil. La norme FIPS 140 définit les algorithmes de chiffrement qui sont approuvés pour une utilisation sur les systèmes informatiques du gouvernement fédéral américain dans le but de protéger les données sensibles.
	1. Pour activer le mode FIPS, exécutez l’applet de commande suivante :
		
		`Enter-HcsFIPSMode`

	2. Après avoir activé le mode FIPS, redémarrez votre appareil pour que les validations de chiffrement prennent effet.

		> [AZURE.NOTE] Vous pouvez activer ou désactiver le mode FIPS sur votre appareil. L’alternance entre le mode FIPS et le mode non FIPS n’est pas prise en charge.


Si votre périphérique ne répond pas à la configuration minimale requise, une erreur apparaît dans le texte de bannière (voir ci-dessous). Vous devez modifier la configuration de l'appareil afin qu'il dispose des ressources nécessaires à la configuration minimale. Vous pouvez ensuite redémarrer et vous connecter à l'appareil. Reportez-vous à la configuration minimale requise à l’[Étape 1 : Vérifier que le système hôte répond aux exigences minimales de l’appareil virtuel](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-ova-deploy2-provision-vmware/image46.png)

Si vous rencontrez une autre erreur lors de la configuration initiale à l’aide de l’interface utilisateur web locale, reportez-vous aux flux de travail suivants dans la rubrique concernant la [gestion de StorSimple Virtual Array à l’aide de l’interface utilisateur web locale](storsimple-ova-web-ui-admin.md).

-   Exécutez les tests de diagnostic pour [dépanner la configuration de l’interface utilisateur web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).

-   [Générez un package de journaux et affichez les fichiers journaux](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## Étapes suivantes

-   [Configurer StorSimple Virtual Array comme un serveur de fichiers](storsimple-ova-deploy3-fs-setup.md)

-   [Configurer StorSimple Virtual Array comme un serveur iSCSI](storsimple-ova-deploy3-iscsi-setup.md)

<!---HONumber=AcomDC_0413_2016-->
<properties
   pageTitle="Déploiement de StorSimple Virtual Array - Configuration dans Hyper-V"
   description="Ce deuxième didacticiel de déploiement de StorSimple Virtual Array implique la configuration d'un appareil virtuel dans Hyper-V."
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
   ms.date="01/20/2016"
   ms.author="alkohli"/>

# Déploiement de StorSimple Virtual Array - Configuration d’un tableau virtuel dans Hyper-V (version préliminaire)

![](./media/storsimple-ova-deploy2-provision-hyperv/hyperv4.png)

## Vue d'ensemble 

Ce didacticiel sur la configuration s'applique à Microsoft Azure StorSimple Virtual Array (également appelé appareil virtuel StorSimple local ou appareil virtuel StorSimple) exécutant la version 1.1.1.0 (version préliminaire publique) uniquement. Ce didacticiel explique comment configurer StorSimple Virtual Array sur un système hôte exécutant Hyper-V 2008 R2, Hyper-V 2012 ou Hyper-V 2012 R2.

Vous aurez besoin de privilèges d'administrateur pour configurer un appareil virtuel. La configuration initiale peut prendre environ 10 minutes.

> [AZURE.IMPORTANT]
> 
> Cette version d'évaluation publique est uniquement destinée à l'évaluation. L'installation de cette version préliminaire dans un environnement de production n'est pas prise en charge.

## Configuration des composants requis

Vous trouverez ici les conditions requises pour configurer un appareil virtuel sur un ordinateur hôte Hyper-V 2008 R2, Hyper-V 2012 ou Hyper-V 2012 R2.

### Pour le service StorSimple Manager

Avant de commencer, assurez-vous que :

-   Vous avez terminé toutes les étapes de la rubrique [Préparation du portail pour StorSimple Virtual Array](storsimple-ova-deploy1-portal-prep.md).

-   Vous avez téléchargé l'image de l’appareil virtuel pour Hyper-V à partir du portail Azure. Pour plus d'informations, consultez l’[étape 3 : Téléchargement de l'image de l’appareil virtuel](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image).

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

-   Vous avez passé en revue la configuration réseau requise pour déployer un appareil virtuel StorSimple et configuré le réseau du centre de données conformément à la configuration requise. Pour plus d’informations, consultez [Configuration réseau requise pour StorSimple Virtual Array](storsimple-ova-system-requirements.md#networking-requirements).

## Configuration étape par étape 

Pour configurer et vous connecter à un appareil virtuel, vous devez effectuer les opérations suivantes :

1.  Assurez-vous que le système hôte dispose de suffisamment de ressources pour répondre aux exigences minimales de l’appareil virtuel.

2.  Configurez un appareil virtuel dans votre hyperviseur.

3.  Démarrez l’appareil virtuel et obtenez l'adresse IP.

Chacune de ces étapes est expliquée dans les sections suivantes.

## Étape 1 : Vérifier que le système hôte répond aux exigences minimales de l’appareil virtuel

Pour créer un appareil virtuel, vous avez besoin des éléments suivants :

-   Hyper-V 2008 R2 SP1, Hyper-V 2012 ou Hyper-V 2012 R2 exécuté sur un système hôte Windows Server 2008 R2 SP1, Windows Server 2012 ou Windows Server 2012 R2.

-   Microsoft Hyper-V Manager sur un client Microsoft Windows connecté à l'hôte.

Vous devez vous assurer que le matériel sous-jacent (système hôte) sur lequel vous créez l’appareil virtuel est capable de dédier les ressources suivantes à votre appareil virtuel :

- Un minimum de 4 cœurs.
- Au moins 8 Go de RAM.
- Une interface réseau.
- Un disque virtuel de 500 Go pour les données système.

## Étape 2 : Configuration d'un appareil virtuel dans l'hyperviseur

Procédez comme suit pour configurer un appareil dans votre hyperviseur.

#### Pour configurer un appareil virtuel

1.  Sur votre hôte Windows Server, copiez l'image de l’appareil virtuel sur le disque local. Il s'agit de l'image que vous avez téléchargée via le portail Azure. Prenez note de l'emplacement où vous avez copié l'image car vous l’utiliserez plus loin dans la procédure.

2.  Ouvrir le **gestionnaire de serveur**. Dans le coin supérieur droit, cliquez sur **Outils** puis sélectionnez **Gestionnaire Hyper-V**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image1.png)

	Si vous utilisez Hyper-V 2008 R2, ouvrez le Gestionnaire Hyper-V. Dans le Gestionnaire de serveur, cliquez sur **Rôles > Hyper-V > Gestionnaire Hyper-V**.

1.  Dans le **Gestionnaire Hyper-V**, dans le volet Étendue, cliquez sur le nœud de votre système pour ouvrir le menu contextuel. Sélectionnez **Nouveau** puis **Machine virtuelle**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image2.png)

1.  Sur la page **Avant de commencer**, cliquez sur **Suivant**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image3.png)

1.  Sur la page **Spécifier le nom et l'emplacement** page, indiquez le **nom** de votre appareil virtuel. Cliquez sur **Next**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image4.png)

1.  Dans la page **Spécifier la génération**, sélectionnez **Génération 1**. Cliquez sur **Next**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image5.png)

	Cet écran ne s'affichera pas si vous exécutez Hyper-V 2008 R2.

1.  Sur la page **Affecter la mémoire** :

    a. Spécifiez une **mémoire de démarrage** de 8192 Mo ou plus. La mémoire minimale requise pour un appareil virtuel StorSimple est de 8 Go ou plus. Ne cochez pas l'option permettant d’**utiliser la mémoire dynamique pour cette machine virtuelle**.

    b. Cliquez sur **Next**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image6.png)

1.  Sur la page de **configuration du réseau** :

    a. Dans la liste déroulante **Connexion**, sélectionnez un commutateur virtuel. Vous devez sélectionner un commutateur virtuel connecté à Internet.

    b. Cliquez sur **Next**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image7.png)

1.  Sur la page de **connexion d’un disque dur virtuel** :

    a. Sélectionnez l'option permettant d’**utiliser un disque dur virtuel existant**. Pointez vers le disque dur virtuel téléchargé sur votre système hôte.

    b. Cliquez sur **Next**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image8.png)

1.  Examinez le **résumé** fourni. Cliquez sur **Terminer** pour créer la machine virtuelle.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image9.png)

1.  Pour répondre à la configuration minimale requise, vous avez besoin de 4 cœurs. Pour ajouter 4 processeurs virtuels, sélectionnez votre système hôte dans la fenêtre **Gestionnaire Hyper-V** puis, dans le volet droit sous la liste des **machines virtuelles**, repérez la machine virtuelle que vous venez de créer. Sélectionnez et cliquez avec le bouton droit sur le nom de la machine, puis sélectionnez **Paramètres**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image10.png)

1.  Sur la page **Paramètres**, dans le volet gauche, cliquez sur **Processeur**. Dans le volet droit, définissez le **nombre de processeurs virtuels** sur 4 (ou plus). Cliquez sur **Apply**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image11.png)

1.  Pour répondre à la configuration minimale requise, vous devez également ajouter un disque de données virtuel de 500 Go. Sur la page **Paramètres** :

    1.  Dans le volet gauche, sélectionnez **Contrôleur SCSI**. 
    2.  Dans le volet droit, sélectionnez **Disque dur** et cliquez sur **Ajouter**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image12.png)

1.  Sur la page **Disque dur**, sélectionnez l’option **Disque dur virtuel** puis cliquez sur **Nouveau**. Ceci démarrera l’**Assistant Nouveau disque dur virtuel**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image13.png)

1.  Sur la page **Avant de commencer**, cliquez sur **Suivant**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image14.png)

1.  Sur la page de **sélection du format de disque**, acceptez le format **VHDX** par défaut. Cliquez sur **Next**. Cet écran ne s'affichera pas si vous exécutez Hyper-V 2008 R2.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image15.png)

1.  Sur la page de **sélection du type de disque**, définissez le type de disque dur virtuel sur **Dynamique** (recommandé). Si vous choisissez un disque de **taille fixe**, il fonctionnera également mais vous devrez peut-être patienter plus longtemps. Nous vous recommandons de ne pas utiliser l’option de **différenciation**. Cliquez sur **Next**. Notez que l’option **Dynamique** est sélectionnée par défaut dans Hyper-V 2012 et Hyper-V 2012 R2. Dans Hyper-V 2008 R2, la valeur par défaut est **Taille fixe**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image16.png)

1.  Sur la page **Spécifier le nom et l'emplacement**, indiquez le **nom** et l’**emplacement** (vous pouvez naviguer vers cet emplacement) du disque de données. Cliquez sur **Next**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image17.png)

1.  Sur la page **Configurer un disque**, sélectionnez l'option **Créer un disque dur virtuel vierge** et spécifiez une taille de **500 Go** (ou plus). Cliquez sur **Next**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image18.png)

1.  Sur la page **Résumé**, passez en revue les détails de votre disque de données virtuel et, si vous satisfait, cliquez sur **Terminer** pour créer le disque. L'Assistant se fermera et un disque dur virtuel sera ajouté à votre ordinateur.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image19.png)

1.  Vous revenez à la page **Paramètres**. Cliquez sur **OK** pour fermer la page **Paramètres** et revenir à la fenêtre du Gestionnaire Hyper-V.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image20.png)

## Étape 3 : Démarrer l’appareil virtuel et obtenir l'adresse IP

Procédez comme suit pour démarrer votre appareil virtuel et vous y connecter.

#### Pour démarrer l’appareil virtuel

1.  Démarrez l’appareil virtuel.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image21.png)

1.  Une fois l'appareil en cours d'exécution, sélectionnez-le, cliquez avec le bouton droit et sélectionnez **Connexion**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image22.png)

1.  Vous devrez peut-être patienter 5 à 10 minutes pour que l’appareil soit prêt. Un message d'état s'affiche sur la console pour indiquer la progression. Lorsque l'appareil est prêt, sélectionnez **Action**. Appuyez sur `Ctrl + Alt + Delete` pour vous connecter à l'appareil virtuel. L'utilisateur par défaut est *StorSimpleAdmin* et le mot de passe par défaut est *Password1*.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image23.png)

1.  Pour des raisons de sécurité, le mot de passe administrateur expire à la première connexion. Vous serez invité à modifier ce mot de passe.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image24.png)

	Entrez un mot de passe contenant au moins 8 caractères. Le mot de passe doit contenir au moins 3 des 4 caractères suivants : majuscules, minuscules, caractères numériques et caractères spéciaux. Entrez de nouveau le mot de passe pour le confirmer. Un message vous avertit que le mot de passe a été modifié.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image25.png)

1.  Une fois le mot de passe modifié, l’appareil virtuel peut redémarrer. Attendez le démarrage de l’appareil.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image26.png)

 	La console Windows PowerShell de l'appareil s’affichera avec une barre de progression.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image27.png)

1.  Les étapes 6 à 8 s'appliquent uniquement lors de l'amorçage dans un environnement non DHCP. Si vous êtes dans un environnement DHCP, ignorez ces étapes et passez à l'étape 9. Si vous avez démarré votre appareil dans un environnement non DHCP, vous verrez l'écran suivant.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image28.png)

 	Vous devez maintenant configurer le réseau.

1.  Utilisez la commande `Get-HcsIpAddress` pour répertorier les interfaces réseau activées sur votre appareil virtuel. Si votre appareil possède une seule interface réseau activée, le nom par défaut affecté à cette interface est `Ethernet`.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image29.png)

1.  Utilisez l’applet de commande `Set-HcsIpAddress` pour configurer le réseau. Voici un exemple :

 	`Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

 	![](./media/storsimple-ova-deploy2-provision-hyperv/image30.png)

1.  Une fois l'installation initiale terminée et l’appareil démarré, vous verrez le texte de la bannière de l’appareil. Notez l'adresse IP et l'URL affichées dans le texte de la bannière pour gérer l'appareil. Vous utiliserez cette adresse IP pour vous connecter à l'interface utilisateur web de votre appareil virtuel et pour finaliser la configuration et l’inscription locales.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image31.png)

	Si votre périphérique ne répond pas à la configuration minimale requise, une erreur apparaît dans le texte de bannière (voir ci-dessous). Vous devez modifier la configuration de l'appareil afin qu'il dispose des ressources nécessaires à la configuration minimale. Vous pouvez ensuite redémarrer et vous connecter à l'appareil. Reportez-vous à la configuration minimale requise à l’[étape 1 : Vérifier que le système hôte répond aux exigences minimales de l’appareil virtuel](#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements).

	![](./media/storsimple-ova-deploy2-provision-hyperv/image32.png)

Si vous rencontrez une autre erreur lors de la configuration initiale à l'aide de l'interface utilisateur web locale, reportez-vous aux flux de travail suivant dans la rubrique [Gestion de votre StorSimple Virtual Array à l'aide de l'interface utilisateur web locale](storsimple-ova-web-ui-admin.md).

-   Exécutez les tests de diagnostic pour [dépanner la configuration de l'interface utilisateur web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).

-   [Générez un package de journaux et affichez les fichiers journaux](storsimple-ova-web-ui-admin.md#generate-a-log-package).

![icône de vidéo](./media/storsimple-ova-deploy2-provision-hyperv/video_icon.png) **Vidéo disponible**

Regardez la vidéo pour voir comment configurer StorSimple Virtual Array dans Hyper-V.

> [AZURE.VIDEO create-a-storsimple-virtual-array]

## Étapes suivantes

-   [Configurer StorSimple Virtual Array comme un serveur de fichiers](storsimple-ova-deploy3-fs-setup.md)

-   [Configurer StorSimple Virtual Array comme un serveur iSCSI](storsimple-ova-deploy3-iscsi-setup.md)

<!---HONumber=AcomDC_0128_2016-->
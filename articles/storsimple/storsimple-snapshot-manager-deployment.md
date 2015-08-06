<properties 
   pageTitle="Déploiement du Gestionnaire d’instantanés StorSimple | Microsoft Azure"
   description="Explique comment télécharger et installer le Gestionnaire d’instantanés StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="07/09/2015"
   ms.author="v-sharos" />

# Déploiement du Gestionnaire d’instantanés StorSimple

## Vue d'ensemble

Le Gestionnaire d’instantanés StorSimple est un composant logiciel enfichable MMC (Microsoft Management Console) qui simplifie la protection des données et la gestion des sauvegardes dans un environnement Microsoft Azure StorSimple. Avec le Gestionnaire d’instantanés StorSimple, vous pouvez gérer le stockage Microsoft Azure StorSimple local et dans le cloud comme s’il s’agissait d’un système de stockage entièrement intégré, ce qui simplifie les processus de sauvegarde et de restauration et contribue à réduire les coûts.

Ce didacticiel décrit la configuration requise, ainsi que les procédures d’installation, de suppression et de mise à niveau du Gestionnaire d’instantanés StorSimple.

## Installation du Gestionnaire d’instantanés StorSimple

Le Gestionnaire d’instantanés StorSimple peut être installé sur les ordinateurs exécutant le système d’exploitation Windows Server® 2008 R2 SP1, Windows Server 2012 ou Windows Server 2012 R2.

>[AZURE.NOTE]Sur les serveurs exécutant Windows 2008 R2, vous devez aussi installer Windows Server 2008 SP1 et Windows Management Framework 3.0.

Avant d’installer ou de mettre à niveau le composant logiciel enfichable Gestionnaire d’instantanés StorSimple pour la console MMC (Microsoft Management Console), assurez-vous que le serveur hôte et l’appareil Microsoft Azure StorSimple sont configurés correctement.

## Configuration préalable requise

Les étapes suivantes fournissent une vue d’ensemble des tâches de configuration requises avant d’installer le Gestionnaire d’instantanés StorSimple. Pour obtenir des informations complètes sur la configuration et l’installation de Microsoft Azure StorSimple, y compris la configuration système requise et des instructions pas à pas, consultez la page [Déploiement de votre appareil StorSimple local](storsimple-deployment-walkthrough.md).

>[AZURE.IMPORTANT]Avant de commencer, lisez la [liste de contrôle préalable à l’installation](storsimple-deployment-walkthrough.md#pre-installation-checklist)
> et les [conditions préalables au déploiement](storsimple-deployment-walkthrough.md#deployment-prerequisites) dans l’article [Déploiement de votre appareil StorSimple local](storsimple-deployment-walkthrough.md).
<br>
 
### Avant d’installer le Gestionnaire d’instantanés StorSimple

1. Déballez, montez et connectez l’appareil Microsoft Azure StorSimple en suivant les instructions de l’article [Installation de votre appareil StorSimple 8100](storsimple-8100-hardware-installation.md) ou [Installation de votre appareil StorSimple 8600](storsimple-8600-hardware-installation.md).

2. Assurez-vous que votre ordinateur hôte exécute l’un des systèmes d’exploitation suivants :

    - Windows Server 2008 R2 (sur les serveurs exécutant Windows 2008 R2, vous devez aussi installer Windows Server 2008 SP1 et Windows Management Framework 3.0)
    - Windows Server 2012
    - Windows Server 2012 R2
 
    > [AZURE.NOTE]Pour un appareil virtuel StorSimple, l’hôte doit être une machine virtuelle Microsoft Azure.

3. Assurez-vous que la configuration requise de Microsoft Azure StorSimple est entièrement respectée. Pour plus d’informations, consultez la page [Conditions préalables au déploiement](storsimple-deployment-walkthrough.md#deployment-prerequisites).

4. Connectez l’appareil à l’hôte et procédez à la configuration initiale. Pour plus d’informations, consultez la page [Étapes du déploiement](storsimple-deployment-walkthrough.md#deployment-steps).

5. Créez des volumes sur l’appareil, affectez-les à l’hôte et vérifiez que l’hôte peut les monter et les utiliser. Le Gestionnaire d’instantanés StorSimple prend en charge les types de volumes suivants :

    - Volumes de base
    - Volumes simples
    - Volumes dynamiques
    - Volumes dynamiques en miroir (RAID 1)
    - Volumes partagés de cluster
 
    Pour plus d’informations sur la création de volumes sur l’appareil StorSimple ou l’appareil virtuel StorSimple, consultez la section « Étape 6 : création d’un volume » de l’article [Déploiement de votre appareil StorSimple local](storsimple-deployment-walkthrough.md).

## Installation d’un nouveau Gestionnaire d’instantanés StorSimple

Avant d’installer le Gestionnaire d’instantanés StorSimple, assurez-vous que les volumes que vous avez créés sur l’appareil StorSimple ou l’appareil virtuel StorSimple sont montés, initialisés et formatés comme indiqué dans la section [Configuration préalable requise](#configure-prerequisites).

>[AZURE.IMPORTANT]
>
>- Pour un appareil virtuel StorSimple, l’hôte doit être une machine virtuelle Microsoft Azure. 
>
>- L’hôte doit exécuter Windows 2008 R2, Windows Server 2012 ou Windows Server 2012 R2. Si votre serveur exécute Windows Server 2008 R2, vous devez aussi installer Windows Server 2008 SP1 et Windows Management Framework 3.0.
>
>- Vous devez configurer une connexion iSCSI de l’hôte vers l’appareil StorSimple avant de pouvoir connecter l’appareil au Gestionnaire d’instantanés StorSimple.

Pour effectuer une nouvelle installation du Gestionnaire d’instantanés StorSimple, suivez les étapes ci-dessous. Si vous installez une mise à niveau, accédez à la section [Mise à niveau ou réinstallation du Gestionnaire d’instantanés StorSimple](#upgrade-or-reinstall-storsimple-snapshot-manager).

- Étape 1 : installation du Gestionnaire d’instantanés StorSimple 
- Étape 2 : connexion du Gestionnaire d’instantanés StorSimple à un appareil 
- Étape 3 : vérification de la connexion à l’appareil 

###Étape 1 : installation du Gestionnaire d’instantanés StorSimple

Pour installer le Gestionnaire d’instantanés StorSimple, procédez comme suit.

#### Pour installer le Gestionnaire d’instantanés StorSimple

1. Téléchargez le logiciel Gestionnaire d’instantanés StorSimple (accédez à [Gestionnaire d’instantanés StorSimple](https://www.microsoft.com/download/details.aspx?id=44220) dans le Centre de téléchargement Microsoft) et enregistrez-le localement sur l’hôte.

2. Dans l’Explorateur de fichiers, cliquez avec le bouton droit sur le dossier compressé, puis cliquez sur **Extraire tout**.

3. Dans la fenêtre **Extraire les dossiers compressés**, dans la zone **Sélectionner une destination et extraire les fichiers**, tapez ou recherchez le chemin d’accès où extraire le fichier.

      >[AZURE.IMPORTANT]Vous devez installer le Gestionnaire d’instantanés StorSimple sur le lecteur C:.
 
4. Cochez la case **Afficher les dossiers extraits une fois l’opération terminée**, puis cliquez sur **Extraire**.

    ![Boîte de dialogue Extraire les fichiers](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png)

4. Une fois l’extraction terminée, le dossier de destination s’ouvre. Double-cliquez sur l’icône d’installation de l’application présente dans le dossier de destination.

5. Quand le message **Installation/désinstallation réussie** s’affiche, cliquez sur **Fermer**. L’icône du Gestionnaire d’instantanés StorSimple doit être affichée sur votre bureau.

    ![Icône du Bureau](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png)

### Étape 2 : connexion du Gestionnaire d’instantanés StorSimple à un appareil

Pour connecter le Gestionnaire d’instantanés StorSimple à un appareil StorSimple, procédez comme suit.

#### Pour connecter le Gestionnaire d’instantanés StorSimple à un appareil

1. Cliquez sur l’icône du Gestionnaire d’instantanés StorSimple sur votre bureau. La fenêtre du Gestionnaire d’instantanés StorSimple s’affiche. Cette fenêtre contient un volet **Étendue**, un volet **Résultats** et un volet **Actions**. 

    ![Interface utilisateur du Gestionnaire d’instantanés StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)

    - Le volet **Étendue** (volet gauche) contient une liste de nœuds organisée en arborescence. Vous pouvez développer certains nœuds pour sélectionner une vue ou des données spécifiques relatives à ce nœud. Cliquez sur l’icône en forme de flèche en regard d’un nœud pour le développer ou le réduire. Cliquez avec le bouton droit sur un élément du volet **Étendue** pour afficher la liste des actions disponibles pour cet élément. 

    - Le volet **Résultats** (volet central) contient des informations d’état détaillées sur le nœud, la vue ou les données que vous avez sélectionnés dans le volet **Étendue**.

    - Le volet **Actions** répertorie les opérations que vous pouvez effectuer sur le nœud, la vue ou les données que vous avez sélectionnés dans le volet **Étendue**.

    Pour obtenir une description complète de l’interface utilisateur du Gestionnaire d’instantanés StorSimple, consultez l’article [Interface utilisateur du Gestionnaire d’instantanés StorSimple](storsimple-use-snapshot-manager.md).

2. Dans le volet **Étendue**, cliquez avec le bouton droit sur le nœud **Appareils**, puis cliquez sur **Configurer un appareil**. La boîte de dialogue **Configurer un appareil** s’affiche.

    ![Configurer un appareil](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png)

3. Dans la zone de liste **Appareil**, sélectionnez l’adresse IP de l’appareil ou de l’appareil virtuel Microsoft Azure StorSimple. Dans la zone de texte **Mot de passe**, tapez le mot de passe du Gestionnaire d’instantanés StorSimple que vous avez créé pour l’appareil dans le portail de gestion Microsoft Azure. Cliquez sur **OK**.

4. Le Gestionnaire d’instantanés StorSimple recherche l’appareil que vous avez identifié. Si l’appareil est disponible, le Gestionnaire d’instantanés StorSimple ajoute une connexion. Vous pouvez [vérifier la connexion à l’appareil](#to-verify-the-connection) pour confirmer que la connexion a bien été ajoutée.

    Si, pour une raison quelconque, l’appareil est indisponible, le Gestionnaire d’instantanés StorSimple retourne un message d’erreur. Cliquez sur **OK** pour fermer le message d’erreur, puis sur **Annuler** pour fermer la boîte de dialogue **Configurer un appareil**.

5. Au moment de se connecter à un appareil, le Gestionnaire d’instantanés StorSimple importe chaque groupe de volumes configuré pour cet appareil, à condition que le groupe de volumes soit associé à des sauvegardes. Les groupes de volumes qui ne sont associés à aucune sauvegarde ne sont pas importés. De plus, les stratégies de sauvegarde créées pour un groupe de volumes ne sont pas importées. Pour afficher les groupes importés, cliquez avec le bouton droit sur le nœud **Groupes de volumes** situé le plus haut dans le volet **Étendue**, puis cliquez sur **Activer/Désactiver les groupes importés**.

### Étape 3 : vérification de la connexion à l’appareil

Pour vérifier que le Gestionnaire d’instantanés StorSimple est connecté à l’appareil StorSimple, procédez comme suit.

#### Pour vérifier la connexion

1. Dans le volet **Étendue**, cliquez sur le nœud **Appareils**.

    ![État de l’appareil dans le Gestionnaire d’instantanés StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png)

2. Vérifiez le volet **Résultats** :

   - Si un indicateur vert est affiché sur l’icône de l’appareil et **Disponible** est indiqué dans la colonne **État**, cela signifie que l’appareil est connecté. 

   - Si un indicateur rouge est affiché sur l’icône de l’appareil et Non disponible est indiqué dans la colonne **État**, cela signifie que l’appareil n’est pas connecté.

   - Si **Actualisation** s’affiche dans la colonne **État**, cela signifie que le Gestionnaire d’instantanés StorSimple récupère les groupes de volumes et les sauvegardes associées pour un appareil connecté.

## Mise à niveau ou réinstallation du Gestionnaire d’instantanés StorSimple

Vous devez désinstaller complètement le Gestionnaire d’instantanés StorSimple avant de mettre à niveau ou de réinstaller le logiciel.

Avant de réinstaller le Gestionnaire d’instantanés StorSimple, sauvegardez la base de données existante du Gestionnaire d’instantanés StorSimple sur l’ordinateur hôte. Les informations de configuration et les stratégies de sauvegarde sont ainsi enregistrées pour faciliter la restauration de ces données à partir de la sauvegarde.

Pour mettre à niveau ou réinstaller le Gestionnaire d’instantanés StorSimple, suivez ces étapes :

- Étape 1 : désinstallation du Gestionnaire d’instantanés StorSimple 
- Étape 2 : sauvegarde de la base de données du Gestionnaire d’instantanés StorSimple 
- Étape 3 : réinstallation du Gestionnaire d’instantanés StorSimple et restauration de la base de données 

### Étape 1 : désinstallation du Gestionnaire d’instantanés StorSimple

Pour désinstaller le Gestionnaire d’instantanés StorSimple, procédez comme suit.

#### Pour désinstaller le Gestionnaire d’instantanés StorSimple

1. Sur l’ordinateur hôte, ouvrez le **Panneau de configuration**, cliquez sur **Programmes**, puis sur **Programmes et fonctionnalités**.

2. Dans le volet gauche, cliquez sur **Désinstaller ou modifier un programme**.

3. Cliquez avec le bouton droit sur **Gestionnaire d’instantanés StorSimple**, puis cliquez sur**Désinstaller**.

4. Le programme d’installation du Gestionnaire d’instantanés StorSimple démarre. Cliquez sur **Modifier l’installation**, puis sur **Désinstaller**.

    >[AZURE.NOTE]Si des processus de la console MMC sont exécutés en arrière-plan, tels que le Gestionnaire d’instantanés StorSimple ou l’outil Gestion des disques, la désinstallation échoue et vous recevez un message vous invitant à fermer toutes les instances de la console MMC avant de désinstaller le programme. Sélectionnez **Fermer automatiquement les applications et tenter de les redémarrer une fois l’installation terminée**, puis cliquez sur **OK**.
 
5. Une fois le processus de désinstallation terminé, le message **Installation/désinstallation réussie** s’affiche. Cliquez sur **Fermer**.

### Étape 2 : sauvegarde de la base de données du Gestionnaire d’instantanés StorSimple

Pour créer et enregistrer une copie de la base de données du Gestionnaire d’instantanés StorSimple, procédez comme suit.

#### Pour sauvegarder la base de données

1. Arrêtez le service de gestion Microsoft StorSimple :

   1. Démarrez le Gestionnaire de serveur.

   2. Dans le tableau de bord du Gestionnaire de serveur, dans le menu **Outils**, sélectionnez **Services**.

   3. Dans la page **Services**, sélectionnez **Service de gestion Microsoft StorSimple**.

   4. Dans le volet droit, sous **Service de gestion Microsoft StorSimple**, cliquez sur **Arrêter le service**.

        ![Stop the StorSimple Manager service](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)

2. Accédez à C:\ProgramData\Microsoft\StorSimple\BACatalog.

    >[AZURE.NOTE]ProgramData est un dossier masqué.

3. Recherchez le fichier XML de catalogue, copiez-le et stockez sa copie dans un emplacement sûr ou dans le cloud.

    ![Fichier de catalogue de sauvegarde StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)

4. Redémarrez le service de gestion Microsoft StorSimple :

    1. Dans le tableau de bord du Gestionnaire de serveur, dans le menu **Outils**, sélectionnez **Services**.

    2. Dans la page **Services**, sélectionnez **Service de gestion Microsoft StorSimple**.

    3. Dans le volet droit, sous **Service de gestion Microsoft StorSimple**, cliquez sur **Redémarrer le service**.

### Étape 3 : réinstallation du Gestionnaire d’instantanés StorSimple et restauration de la base de données

Pour réinstaller le Gestionnaire d’instantanés StorSimple, suivez les étapes de la section [Installation d’un nouveau gestionnaire d’instantanés StorSimple](#install-a-new-storsimple-snapshot-manager). Appliquez ensuite la procédure suivante pour restaurer la base de données du Gestionnaire d’instantanés StorSimple.

#### Pour restaurer la base de données

1. Arrêtez le service de gestion Microsoft StorSimple :

    1. Démarrez le Gestionnaire de serveur.

    2. Dans le tableau de bord du Gestionnaire de serveur, dans le menu **Outils**, sélectionnez **Services**.

    3. Dans la page **Services**, sélectionnez **Service de gestion Microsoft StorSimple**.

    4. Dans le volet droit, sous **Service de gestion Microsoft StorSimple**, cliquez sur **Arrêter le service**.

2. Accédez à C:\ProgramData\Microsoft\StorSimple\BACatalog.

     >[AZURE.NOTE]ProgramData est un dossier masqué.

3. Supprimez le fichier XML de catalogue et remplacez-le par la version que vous avez enregistrée précédemment.

4. Redémarrez le service de gestion Microsoft StorSimple :

    1. Dans le tableau de bord du Gestionnaire de serveur, dans le menu **Outils**, sélectionnez **Services**.

    2. Dans la page **Services**, sélectionnez **Service de gestion Microsoft StorSimple**.

    3. Dans le volet droit, sous **Service de gestion Microsoft StorSimple**, cliquez sur **Redémarrer le service**.

## Étapes suivantes

Pour en savoir plus sur le Gestionnaire d’instantanés StorSimple, accédez à la page [Qu’est-ce que le Gestionnaire d’instantanés StorSimple ?](storsimple-what-is-snapshot-manager.md)

Pour en savoir plus sur l’interface utilisateur du Gestionnaire d’instantanés StorSimple, accédez à la page [Interface utilisateur du Gestionnaire d’instantanés StorSimple](storsimple-use-snapshot-manager.md).

<!----HONumber=July15_HO4-->
<properties 
   pageTitle="Configuration d’un serveur iSCSI StorSimple Virtual Array | Microsoft Azure"
   description="Explique comment effectuer la configuration initiale, inscrire votre serveur iSCSI StorSimple, puis finaliser la configuration de l’appareil."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="07/18/2016"
   ms.author="alkohli" />


# Déploiement de StorSimple Virtual Array - Configuration de votre appareil virtuel en tant que serveur iSCSI

![flux du processus d'installation iscsi](./media/storsimple-ova-deploy3-iscsi-setup/iscsi4.png)

## Vue d'ensemble

Ce didacticiel de déploiement s'applique à Microsoft Azure StorSimple Virtual Array (également appelé appareil virtuel StorSimple local ou appareil virtuel StorSimple) exécutant la version de mise à la disposition générale (mars 2016). Ce didacticiel explique comment effectuer la configuration initiale, inscrire votre serveur iSCSI StorSimple, finaliser l'installation de l’appareil, puis créer, monter, initialiser et formater des volumes sur le serveur iSCSI de votre appareil virtuel StorSimple. Les informations de configuration StorSimple de cet article s'appliquent uniquement aux tableaux virtuels StorSimple.

Les procédures décrites ici prennent de 30 minutes à 1 heure environ. Les informations publiées dans cet article s'appliquent uniquement aux tableaux virtuels StorSimple.

## Configuration requise

Avant de configurer votre appareil virtuel StorSimple, assurez-vous que :

- Vous avez configuré un périphérique virtuel et vous êtes connecté à celui-ci, comme décrit dans [Déploiement de StorSimple Virtual Array - Configuration d’un tableau virtuel dans Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) ou [Déploiement de StorSimple Virtual Array - Configuration d’un tableau virtuel dans VMware](storsimple-ova-deploy2-provision-vmware.md).

- Vous disposez de la clé d'inscription du service StorSimple Manager que vous avez créé pour gérer les appareils virtuels StorSimple. Pour plus d'informations, consultez l’**étape 2 : Obtention de la clé d'inscription** dans [Déploiement de StorSimple Virtual Array - Préparation du portail](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key).

- S'il s'agit du deuxième appareil virtuel (ou ultérieur) que vous inscrivez auprès d’un service StorSimple Manager existant, vous devez disposer de la clé de chiffrement des données de service. Cette clé a été générée lorsque le premier appareil a été correctement inscrit auprès de ce service. Si vous avez perdu cette clé, consultez la rubrique **Obtenir la clé de chiffrement des données de service** dans [Utiliser l’interface utilisateur web pour gérer votre StorSimple Virtual Array](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## Configuration étape par étape 

Utilisez la procédure détaillée ci-dessous pour configurer et déployer votre appareil virtuel StorSimple :

-  [Étape 1 : Finalisation de la configuration de l'interface utilisateur web locale et inscription de votre appareil](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
-  [Étape 2 : Finalisation de la configuration requise pour l’appareil](#step-2-complete-the-required-device-setup)
-  [Étape 3 : Ajout d’un volume](#step-3-add-a-volume)
-  [Étape 4 : Montage, initialisation et formatage d’un volume](#step-4-mount-initialize-and-format-a-volume)

## Étape 1 : Finalisation de la configuration de l'interface utilisateur web locale et inscription de votre appareil 

#### Pour finaliser l'installation et l’inscription de l'appareil

1. Ouvrez une fenêtre de navigateur et connectez-vous à l'interface utilisateur web en tapant :

    `https://<ip-address of network interface>`

    Utilisez l'URL de connexion notée à l'étape précédente. Une erreur vous indique qu'il existe un problème avec le certificat de sécurité du site web. Cliquez sur **Poursuivre sur cette page web**.

    ![erreur de certificat de sécurité](./media/storsimple-ova-deploy3-iscsi-setup/image3.png)

2. Connectez-vous à l'interface utilisateur web de votre appareil virtuel en tant que **StorSimpleAdmin**. Entrez le mot de passe administrateur que vous avez modifié à l'étape 3 : Démarrage de l’appareil virtuel dans [Déploiement de StorSimple Virtual Array - Configuration d’un appareil virtuel dans Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) ou [Déploiement de StorSimple Virtual Array - Configuration d’un appareil virtuel dans VMware](storsimple-ova-deploy2-provision-vmware.md).

    ![page de connexion](./media/storsimple-ova-deploy3-iscsi-setup/image4.png)

3. Vous accéderez à la page d’**accueil**. Cette page décrit les différents paramètres requis pour configurer et inscrire l'appareil virtuel auprès du service StorSimple Manager. Notez que les **paramètres réseau**, **les paramètres du proxy web** et les **paramètres horaires** sont facultatifs. Les seuls paramètres requis sont les **paramètres de l’appareil** et les **paramètres du cloud**.

    ![page d'accueil](./media/storsimple-ova-deploy3-iscsi-setup/image5.png)

4. Dans la page **Paramètres réseau**, sous **Interfaces réseau**, DATA 0 sera automatiquement configuré pour vous. Chaque interface réseau est définie par défaut pour obtenir automatiquement une adresse IP (DHCP). Par conséquent, une adresse IP, un sous-réseau et une passerelle seront automatiquement attribués (pour IPv4 et IPv6).

    Si vous envisagez de déployer votre appareil en tant que serveur iSCSI (pour configurer un stockage en bloc), nous vous recommandons de désactiver l’option permettant d’**obtenir automatiquement l’adresse IP** et de configurer des adresses IP statiques.

    ![page des paramètres réseau](./media/storsimple-ova-deploy3-iscsi-setup/image6.png)

    Si vous avez ajouté plusieurs interfaces réseau lors de la configuration de l'appareil, vous pouvez les configurer ici. Notez que vous pouvez configurer votre interface réseau en IPv4 uniquement ou en IPv4 et IPv6. Les configurations en IPv6 uniquement ne sont pas prises en charge.

5. Des serveurs DNS sont requis car ils sont utilisés lorsque votre appareil tente de communiquer avec vos fournisseurs de services de stockage cloud ou pour résoudre votre appareil par nom s’il est configuré comme un serveur de fichiers. Dans la page **Paramètres réseau**, sous **Serveurs DNS** :

    1. Un serveur DNS principal et un serveur secondaire seront définis automatiquement. Si vous choisissez de configurer des adresses IP statiques, vous pouvez spécifier des serveurs DNS. Pour une haute disponibilité, nous vous recommandons de configurer un serveur DNS principal et un serveur secondaire.

    2. Cliquez sur **Apply**. Cette opération appliquera et validera les paramètres réseau.

6. Sur la page **Paramètres de l’appareil** :

    1. Attribuez un **nom** unique à votre appareil. Ce nom peut contenir 1 à 15 caractères ainsi que des lettres, des chiffres et des traits d'union.

    2. Cliquez sur l’icône **Serveur iSCSI** ![icône du serveur iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/image7.png) pour le **type** d’appareil que vous créez. Un serveur iSCSI vous permettra de configurer un stockage de bloc.

    3. Indiquez si vous souhaitez associer cet appareil à un domaine. Si votre appareil est un serveur iSCSI, l’association à un domaine est facultative. Si vous décidez de ne pas associer votre serveur iSCSI à un domaine, cliquez sur **Appliquer**, attendez que les paramètres soient appliqués, puis passez à l'étape suivante.

        Si vous souhaitez associer l'appareil à un domaine. Entrez un **nom de domaine**, puis cliquez sur **Appliquer**.

        > [AZURE.NOTE] Si vous joignez votre serveur iSCSI à un domaine, assurez-vous que votre tableau virtuel est dans sa propre unité organisationnelle (UO) pour Microsoft Azure Active Directory et qu’aucun objet de stratégie de groupe (GPO) ne lui est appliqué.

    5. Une boîte de dialogue s’affiche. Entrez vos informations d'identification de domaine au format spécifié. Cliquez sur l’icône en forme de coche ![icône en forme de coche](./media/storsimple-ova-deploy3-iscsi-setup/image15.png). Les informations d'identification de domaine seront vérifiées. Un message d'erreur apparaît si les informations d'identification sont incorrectes.

        ![credentials](./media/storsimple-ova-deploy3-iscsi-setup/image8.png)

    6. Cliquez sur **Apply**. Cette opération appliquera et validera les paramètres de l’appareil.
 
7. (Facultatif) Configurez votre serveur proxy web. Bien que la configuration du proxy web soit facultative, si vous en utilisez un, vous pouvez uniquement le configurer ici.

    ![configuration du proxy web](./media/storsimple-ova-deploy3-iscsi-setup/image9.png)

    Dans la page **Proxy web** :

    1. Indiquez **l’URL du proxy web** au format suivant : *adressehttp://host-IP* ou *Nom de domaine complet:Numéro de port*. Notez que les URL HTTPS ne sont pas prises en charge.

    2. Définissez **Authentification** sur le paramètre **De base** ou **Aucune**.

    3. Si vous utilisez une authentification, vous devez également fournir un **nom d’utilisateur** et un **mot de passe**.

    4. Cliquez sur **Apply**. Cette opération validera et appliquera les paramètres de proxy web configurés.
 
8. (Facultatif) Configurez les paramètres d'heure de votre appareil, notamment le fuseau horaire et les serveurs NTP principal et secondaire. Les serveurs NTP sont requis. En effet, votre appareil doit synchroniser les heures pour pouvoir s’authentifier auprès de vos fournisseurs de services cloud.

    ![paramètres d’heure](./media/storsimple-ova-deploy3-iscsi-setup/image10.png)

    Dans la page **Paramètres d’heure** :

    1. Dans la liste déroulante, définissez le **fuseau horaire** en fonction de l’emplacement géographique de l’appareil déployé. Le fuseau horaire par défaut de votre appareil est PST. Votre appareil utilise ce fuseau horaire pour toutes les opérations planifiées.

    2. Spécifiez un **serveur NTP principal** pour votre appareil ou acceptez la valeur par défaut time.windows.com. Vérifiez que votre réseau autorise le trafic NTP à passer de votre centre de données à Internet.

    3. (Facultatif) Spécifiez un **serveur NTP secondaire** pour votre appareil.

    4. Cliquez sur **Apply**. Cette opération validera et appliquera les paramètres d’heure configurés.

9. Configurez les paramètres de cloud de votre appareil. Dans cette étape, vous allez finaliser la configuration de l’appareil local puis inscrire l'appareil auprès du service StorSimple Manager.

    1. Entrez la **clé d’inscription de service** que vous avez obtenue à l’**étape2 : obtention de la clé d’inscription** dans [Déploiement de StorSimple Virtual Array - Préparation du portail](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key).

    2. S’il ne s’agit pas du premier appareil que vous inscrivez auprès de ce service, vous devrez fournir la **clé de chiffrement de données du service**. Cette clé et la clé d’enregistrement de service sont requises pour l’inscription d’appareils supplémentaires auprès du service StorSimple Manager. Pour plus d’informations, reportez-vous à la section permettant d’obtenir la [clé de chiffrement de données du service](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) sur votre interface utilisateur web locale.

    3. Cliquez sur **S'inscrire**. Cette opération redémarra l’appareil. Vous devrez peut-être attendre 2 à 3 minutes avant que l'appareil soit inscrit. Une fois que l’appareil a redémarré, vous êtes redirigé vers la page de connexion.

       ![enregistrer un appareil](./media/storsimple-ova-deploy3-iscsi-setup/image11.png)

10. Retournez dans le portail Azure Classic. Sur la page **Appareils**, vérifiez que l’appareil s’est bien connecté au service en vérifiant son état. L’état de l’appareil doit être **Actif**.

    ![Page Appareils](./media/storsimple-ova-deploy3-iscsi-setup/image12.png)

## Étape 2 : Finalisation de la configuration requise pour l’appareil

Pour finaliser la configuration minimale de votre appareil StorSimple, vous devez :

- Sélectionner un compte de stockage à associer à votre appareil.

- Choisir les paramètres de chiffrement pour les données envoyées vers le cloud.

Pour finaliser la configuration minimale requise, procédez comme suit dans le portail Azure Classic.

#### Pour finaliser l'installation minimale de l’appareil

1. Dans la page **Appareils**, sélectionnez l’appareil que vous venez de créer. Cet appareil apparaît en tant que **Actif**. Cliquez sur la flèche en regard du nom de l’appareil, puis sur **Démarrage rapide**.

    ![Page Appareils](./media/storsimple-ova-deploy3-iscsi-setup/image13.png)

2. Cliquez sur **Terminer la configuration de l’appareil** pour démarrer l’Assistant Configurer l’appareil.

    ![Assistant Configurer l’appareil](./media/storsimple-ova-deploy3-iscsi-setup/image14.png)

3. Dans l’Assistant Configurer l’appareil, sur la page **Paramètres de base**, procédez comme suit :

   1. Spécifiez un compte de stockage à utiliser avec votre appareil. Dans cet abonnement, vous pouvez sélectionner un compte de stockage existant dans la liste déroulante, ou spécifier **Ajouter** pour choisir un compte d’un autre abonnement.

   2. Définissez les paramètres de chiffrement pour toutes les données au repos qui seront envoyées vers le cloud. (StorSimple utilise le chiffrement AES-256). Pour chiffrer vos données, cochez la case **Activer le chiffrement de stockage cloud**. Entrez une clé de chiffrement de stockage cloud contenant 32 caractères. Entrez à nouveau la clé pour la confirmer.

   3. Cliquez sur l’icône en forme de coche ![icône en forme de coche](./media/storsimple-ova-deploy3-iscsi-setup/image15.png).

    ![paramètres de base](./media/storsimple-ova-deploy3-iscsi-setup/image16.png)

    Les paramètres seront maintenant mis à jour. Une fois les paramètres mis à jour, le bouton de finalisation de l'installation de l’appareil n’est pas disponible. Vous revenez à la page **Démarrage rapide** de l'appareil.

>[AZURE.NOTE]Vous pouvez modifier tous les autres paramètres de l’appareil à tout moment en accédant à la page **Configurer**.

## Étape 3 : Ajout d’un volume

Pour créer un volume, procédez comme suit dans le portail Azure Classic.

#### Pour créer un volume

1. Sur la page **Démarrage rapide** de l’appareil, cliquez sur **Ajouter un volume**. L’assistant Ajouter un volume se lance.

2. Dans l’Assistant Ajouter un volume, sous **Paramètres de base**, procédez comme suit :

    1. Indiquez un nom unique pour votre volume. Le nom doit être une chaîne contenant entre 3 et 127 caractères.

    2. Fournissez une description du volume. La description permet d'identifier les propriétaires du volume.

    3. Sélectionnez un type d'utilisation du volume. Le type d’utilisation peut être **Volume hiérarchisé** ou **Volume attaché localement**. (**Volume hiérarchisé** est la valeur par défaut.) Pour les charges de travail qui nécessitent des garanties locales, une faible latence et les meilleures performances possibles, sélectionnez **Volume** **attaché localement**. Pour toutes les autres données, sélectionnez **Volume** **hiérarchisé**.

        La configuration d’un volume épinglé localement est complète, et garantit que les données principales sur le volume sont conservées sur l’appareil et ne débordent pas sur le cloud. Si vous créez un volume épinglé localement, l’appareil recherche de l’espace disponible sur les couches locales pour configurer un volume de la taille demandée. La création d’un volume épinglé localement peut impliquer le débordement des données existantes de l’appareil vers le cloud, et le temps nécessaire pour créer le volume peut être long. La durée totale dépend de la taille du volume configuré, de la bande passante réseau disponible et des données sur votre appareil.

        D’autre part, la configuration d’un volume à plusieurs niveaux est légère, et sa création peut être très rapide. Lorsque vous créez un volume à plusieurs niveaux, environ 10 % de l'espace sont configurés au niveau local et 90 % dans le cloud. Par exemple, si vous avez configuré un volume de 1 To, 100 Go résident dans l'espace local et 900 Go sont utilisés dans le cloud lorsque les données sont stockées en niveaux. Cela implique que si vous n'avez plus d'espace local sur l’appareil, vous ne pouvez pas configurer un partage à plusieurs niveaux (car les 10 % ne seront pas disponibles).

    4. Indiquez la capacité allouée pour votre volume. Notez que la capacité spécifiée doit être inférieure à la capacité disponible. Si vous créez un volume à plusieurs niveaux, la taille doit être comprise entre 500 Go et 5 To. Pour un volume épinglé localement, spécifiez une taille de volume comprise entre 50 Go et 500 Go. Utilisez la capacité disponible comme guide pour configurer un volume. Si la capacité locale disponible est de 0 Go, vous ne pourrez pas configurer de volumes locaux ou à plusieurs niveaux.

        ![paramètres de base](./media/storsimple-ova-deploy3-iscsi-setup/image17.png)

    5. Cliquez sur l’icône en forme de flèche ![Icône en forme de flèche](./media/storsimple-ova-deploy3-iscsi-setup/image18.png) pour passer à la page suivante.

3. Dans la page **Paramètres supplémentaires**, ajoutez un nouvel enregistrement de contrôle d'accès (ACR) :

    1. Saisissez un **Nom** pour votre ACR.

    2. Sous **Nom de l’initiateur iSCSI**, indiquez le nom qualifié iSCSI de votre hôte Windows. Si vous ne possédez pas le nom IQN, accédez à [l’Annexe A : Obtention du nom qualifié d’un hôte Windows Server](#appendix-a-get-the-iqn-of-a-windows-server-host).

    3. Nous vous recommandons d’activer la sauvegarde par défaut en cochant la case **Activer une sauvegarde par défaut pour ce volume**. La sauvegarde par défaut crée une stratégie qui s’exécute à 22h30 chaque jour (heure de l’appareil) et crée un instantané de cloud de ce volume.

        ![paramètres supplémentaires](./media/storsimple-ova-deploy3-iscsi-setup/image19.png)

    4. Cliquez sur l’icône en forme de coche ![icône en forme de coche](./media/storsimple-ova-deploy3-iscsi-setup/image15.png). Cette opération démarre la tâche de création du volume. Vous recevrez un message de progression similaire à celui ci-dessous.

        ![message de progression](./media/storsimple-ova-deploy3-iscsi-setup/image20.png)

        Un volume est créé avec les paramètres spécifiés. Par défaut, la surveillance et la sauvegarde seront activées pour le volume.

    5. Pour vérifier que le volume a été créé, accédez à la page **Volumes**. Le volume devrait y apparaître.

        ![](./media/storsimple-ova-deploy3-iscsi-setup/image21.png)

## Étape 4 : Montage, initialisation et formatage d’un volume

Procédez comme suit pour monter, initialiser et formater vos volumes StorSimple sur un hôte Windows Server.

#### Pour monter, initialiser et formater un volume

1. Démarrez l’initiateur Microsoft iSCSI.

2. Dans la fenêtre **Propriétés de l’initiateur iSCSI**, sous l’onglet **Découverte**, cliquez sur **Découvrir un portail**.

    ![découvrir le portail](./media/storsimple-ova-deploy3-iscsi-setup/image22.png)

3. Dans la boîte de dialogue **Détecter un portail cible**, indiquez l’adresse IP de votre interface réseau compatible iSCSI, puis cliquez sur **OK**.

    ![Adresse IP](./media/storsimple-ova-deploy3-iscsi-setup/image23.png)

4. Dans la fenêtre **Propriétés de l’initiateur iSCSI**, sous l’onglet **Cibles**, recherchez les **cibles découvertes**. (Chaque volume sera une cible découverte). L’appareil doit apparaître comme **inactif**.

    ![cibles découvertes](./media/storsimple-ova-deploy3-iscsi-setup/image24.png)

5. Sélectionnez un appareil cible, puis cliquez sur **Connecter**. Une fois l’appareil connecté, son état doit indiquer **Connecté**. Pour plus d’informations sur l’utilisation de l’initiateur Microsoft iSCSI, consultez la rubrique [Installation et configuration de l’initiateur Microsoft iSCSI][1].

    ![sélectionner un appareil cible](./media/storsimple-ova-deploy3-iscsi-setup/image25.png)

6. Sur l’hôte Windows, appuyez sur la touche de logo Windows + X, puis cliquez sur **Exécuter**.

7. Dans la boîte de dialogue **Exécuter**, saisissez **Diskmgmt.msc**. Cliquez sur **OK**. La boîte de dialogue **Gestion des disques** s’affiche. Le volet de droite affiche les volumes de votre ordinateur hôte.

8. Dans la fenêtre **Gestion des disques**, les volumes montés sont affichés comme indiqué dans l’illustration suivante. Cliquez avec le bouton droit sur le volume détecté (cliquez sur le nom du disque), puis cliquez sur **En ligne**.

    ![gestion des disques](./media/storsimple-ova-deploy3-iscsi-setup/image26.png)

9. Cliquez avec le bouton droit et sélectionnez **Initialiser le disque**.

    ![initialiser le disque 1](./media/storsimple-ova-deploy3-iscsi-setup/image27.png)

10. Dans la boîte de dialogue, sélectionnez le(s) disque(s) à initialiser, puis cliquez sur **OK**.

    ![initialiser le disque 2](./media/storsimple-ova-deploy3-iscsi-setup/image28.png)

11. L'Assistant Nouveau volume simple se lance. Sélectionnez une taille de disque, puis cliquez sur **Suivant**.

    ![assistant nouveau volume 1](./media/storsimple-ova-deploy3-iscsi-setup/image29.png)

12. Affectez une lettre de lecteur au volume, puis cliquez sur **Suivant**.

    ![assistant nouveau volume 2](./media/storsimple-ova-deploy3-iscsi-setup/image30.png)

13. Entrez les paramètres pour formater le volume. **Sur Windows Server, seul le système NTFS est pris en charge.** Définissez la valeur AUS sur 64 Ko. Nommez votre volume. Il est recommandé de choisir un nom de volume identique à celui que vous avez fourni sur votre appareil virtuel StorSimple. Cliquez sur **Next**.

    ![assistant nouveau volume 3](./media/storsimple-ova-deploy3-iscsi-setup/image31.png)

14. Vérifiez les valeurs de votre volume, puis cliquez sur **Terminer**.

    ![assistant nouveau volume 4](./media/storsimple-ova-deploy3-iscsi-setup/image32.png)

    Les volumes s’affichent en tant que **En ligne** dans la page **Gestion des disques**.

    ![volumes en ligne](./media/storsimple-ova-deploy3-iscsi-setup/image33.png)

## Étapes suivantes

Découvrez comment utiliser l’interface utilisateur web locale pour [gérer votre StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

## Annexe A : Obtention du nom qualifié d’un hôte Windows Server

Procédez comme suit pour obtenir le nom qualifié iSCSI (IQN) d'un hôte Windows exécutant Windows Server 2012.

#### Pour obtenir le nom qualifié d’un hôte Windows

1. Démarrez l’initiateur Microsoft iSCSI sur l’hôte Windows.

2. Dans la fenêtre **Propriétés de l’initiateur iSCSI**, sous l’onglet **Configuration**, sélectionnez et copiez la chaîne affichée dans le champ **Nom de l’initiateur**.

    ![Propriétés de l’initiateur iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/image34.png)

2. Enregistrez cette chaîne.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx

<!---HONumber=AcomDC_0720_2016-->
<properties title="How to create a hybrid deployment of RemoteApp" pageTitle="How to create a hybrid deployment of RemoteApp" description="Learn how to create a deployment of RemoteApp that connects to your internal network." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="elizapo"></tags>

# Création d'un déploiement hybride de RemoteApp

Il existe deux types de déploiement RemoteApp :

-   Cloud : réside complètement dans Azure et est créé à l'aide de l'option **Création rapide** du portail de gestion Azure.
-   Hybride : comprend un réseau virtuel pour un accès local et est créé à l'aide de l'option **Créer avec VPN** du portail de gestion.

Ce didacticiel vous familiarise avec la procédure de création d'un déploiement hybride. Il se compose de sept étapes :

1.  Création d'une image de modèle RemoteApp
2.  Création d'un service RemoteApp
3.  Liaison avec un réseau virtuel
4.  Association d'une image de modèle
5.  Configuration d'une synchronisation d'annuaires. RemoteApp en a besoin pour synchroniser les utilisateurs, groupes, contacts et mots de passe de votre annuaire Active Directory local avec votre client Azure Active Directory.
6.  Publication de programmes RemoteApp
7.  Configuration de l'accès utilisateur

**Avant de commencer**

Avant de créer le service, vous devez effectuer les étapes suivantes :

-   Installer les [mises à jour requises][] pour améliorer les performances d'Azure RemoteApp.
-   Vous inscrire pour bénéficier de la [version préliminaire de RemoteApp][].
-   Créer un compte d'utilisateur dans Active Directory à utiliser comme compte de service RemoteApp. Limiter les autorisations pour ce compte, de telle sorte qu'il puisse uniquement joindre des ordinateurs au domaine.
-   Collecter des informations sur votre réseau local : informations sur l'adresse IP et détails du périphérique VPN.
-   Installer le module [Azure PowerShell][].
-   Collecter des informations sur les utilisateurs et groupes auxquels vous souhaitez accorder l'accès. Il peut s'agir d'informations sur le compte Microsoft ou sur le compte professionnel Active Directory pour les utilisateurs ou les groupes.

## **Étape 1 : Création d'une image de modèle**

Azure RemoteApp utilise une image de modèle Windows Server 2012 R2 pour héberger tous les programmes que vous souhaitez partager avec vos utilisateurs. Pour créer une image de modèle RemoteApp personnalisée, vous pouvez commencer par une image existante ou en créer une. Vous trouverez, ci-dessous, les exigences relatives à l'image qui peut être téléchargée en vue d'être utilisée avec Azure RemoteApp :

-   Elle doit se trouver dans un fichier VHD (pour l'instant, les fichiers VHDX ne sont pas pris en charge).
-   Le disque dur virtuel (VHD) peut être de taille fixe ou dynamique. L'utilisation d'un fichier VHD de taille dynamique est recommandée, car le téléchargement sur Azure prend moins de temps qu'un fichier VHD de taille fixe.
-   Le disque doit être initialisé à l'aide du type de partitionnement MBR (Enregistrement de démarrage principal). Le style de partition GPT (GUID Partition Table) n'est pas pris en charge.
-   Le disque dur virtuel doit contenir une seule installation de Windows Server 2012 R2. Il peut contenir plusieurs volumes, mais un seul contenant une installation de Windows.
-   Le rôle RDSH (Hôte de session Bureau à distance) et la fonctionnalité Expérience utilisateur doivent être installés.
-   Le système de fichiers EFS (Encrypting File System) doit être désactivé.
-   L'image doit être préparée avec SYSPREP en utilisant les paramètres **/oobe /generalize /shutdown** (n'utilisez PAS le paramètre **/mode:vm**).

Pour créer une image de modèle à partir de zéro, procédez comme suit :

1.  Recherchez une image ISO ou DVD Windows Server 2012 R2.
2.  Créez un disque dur virtuel.
3.  Installez Windows Server 2012 R2.
4.  Installez le rôle RDSH (Hôte de session Bureau à distance) et la fonctionnalité Expérience utilisateur.
5.  Installez les fonctionnalités supplémentaires dont vos applications ont besoin.
6.  Installez et configurez vos applications.
7.  Exécutez les éventuelles opérations de configuration Windows supplémentaires requises par vos applications.
8.  Désactivez le système de fichiers EFS.
9.  Préparez l'image avec SYSPREP.

La procédure détaillée de création d'une image se présente comme suit :

1.  Recherchez une image ISO ou DVD Windows Server 2012 R2.
2.  Créez un fichier VHD à l'aide de la fonction Gestion des disques.

    1.  Lancez la fonction Gestion des disques (diskmgmt.msc).
    2.  Créez un disque dur virtuel de taille dynamique d'une taille minimale de 40 Go. (Estimez la quantité d'espace requise pour Windows, vos applications et vos personnalisations. L'espace requis pour Windows Server avec le rôle RDSH et la fonctionnalité Expérience utilisateur est d'environ 10 Go.)

        1.  Cliquez sur **Action \> Créer un disque dur virtuel**.
        2.  Indiquez l'emplacement, la taille et le format du disque dur virtuel. Sélectionnez la case d'option **Taille dynamique**, puis cliquez sur **OK**.

            Cette opération va prendre plusieurs secondes. Une fois le disque dur virtuel créé, un nouveau disque sans lettre de lecteur et avec l'état « Non initialisé » doit normalement apparaître dans la console Gestion des disques.

        -   Cliquez sur le disque (et non sur l'état non alloué) avec le bouton droit, puis sélectionnez **Initialiser le disque**. Sélectionnez **MBR** (Enregistrement de démarrage principal) comme style de partition, puis cliquez sur **OK**.
        -   Créez un volume : cliquez avec le bouton droit sur l'espace non alloué, puis cliquez sur **Nouveau volume simple**. Vous pouvez accepter les paramètres par défaut de l'Assistant, mais veillez à attribuer une lettre de lecteur afin d'éviter les éventuels problèmes lors du téléchargement de l'image de modèle.
        -   Cliquez avec le bouton droit sur le disque, puis cliquez sur **Détacher un disque dur virtuel**.

3.  Installation de Windows Server 2012 R2 :

    1.  Créez une machine virtuelle. Utilisez l'Assistant Nouvel ordinateur virtuel de Hyper-V Manager ou Client Hyper-V.

        1.  Sur la page Connecter un disque dur virtuel, sélectionnez **Utiliser un disque dur virtuel existant**, puis accédez au disque dur virtuel créé au cours de l'étape précédente.
        2.  Sur la page Options d'installation, sélectionnez **Installer un système d'exploitation à partir d'un CD/DVD-ROM de démarrage**, puis l'emplacement de votre support d'installation Windows Server 2012 R2.
        3.  Sélectionnez les autres options de l'Assistant nécessaires pour installer Windows et vos applications. Terminez l'Assistant.

    2.  Une fois l'Assistant terminé, modifiez les paramètres de la machine virtuelle et effectuez les autres modifications nécessaires pour installer Windows et vos programmes, comme le nombre de processeurs virtuels, puis cliquez sur **OK**.
    3.  Connectez-vous à la machine virtuelle et installez Windows Server 2012 R2.

4.  Installez le rôle RDSH (Hôte de session Bureau à distance) et la fonctionnalité Expérience utilisateur :

    1.  Lancez le Gestionnaire de serveur.
    2.  Cliquez sur **Ajouter des rôles et des fonctionnalités** sur l'écran de bienvenue ou dans le menu **Gérer**.
    3.  Cliquez sur **Suivant** sur la page Avant de commencer.
    4.  Sélectionnez **Installation basée sur un rôle ou une fonctionnalité**, puis cliquez sur **Suivant**.
    5.  Sélectionnez l'ordinateur local dans la liste, puis cliquez sur **Suivant**.
    6.  Sélectionnez **Services Bureau à distance**, puis cliquez sur **Suivant**.
    7.  Développez l'entrée **Interfaces utilisateur et infrastructure** et sélectionnez **Expérience utilisateur**.
    8.  Sélectionnez **Ajouter des fonctionnalités**, puis cliquez sur **Suivant**.
    9.  Sur la page Services Bureau à distance, cliquez sur **Suivant**.
    10. Cliquez sur **Hôte de session Bureau à distance**.
    11. Sélectionnez **Ajouter des fonctionnalités**, puis cliquez sur **Suivant**.
    12. Sur la page Confirmer les sélections pour l'installation, sélectionnez **Redémarrer automatiquement le serveur de destination, si nécessaire**, puis cliquez sur **Oui** dans l'avertissement de redémarrage.
    13. Cliquez sur **Installer**. L'ordinateur redémarre.

5.  Installez les fonctionnalités supplémentaires requises par vos applications, telles que .NET Framework 3.5. Pour installer les fonctionnalités, exécutez l'Assistant Ajout de rôles et de fonctionnalités.
6.  Installez et configurez les programmes et applications que vous souhaitez publier via RemoteApp.

    **Important :** Microsoft vous conseille d'installer le rôle RDSH avant les applications pour être sûr de détecter tout problème au niveau de la compatibilité des applications avant le téléchargement de l'image sur RemoteApp.

7.  Exécutez les éventuelles opérations de configuration Windows supplémentaires requises par vos applications.
8.  Désactivez le système de fichiers EFS. Exécutez la commande suivante dans une fenêtre de commande avec élévation de privilèges :

        Fsutil behavior set disableencryption 1

    Une autre solution consiste à définir ou à ajouter la valeur DWORD suivante dans le Registre :

        HKLM\System\CurrentControlSet\Control\FileSystem\NtfsDisableEncryption = 1

9.  Si vous créez votre image dans une **machine virtuelle Azure**, vous devez supprimer ou renommer le fichier **\\Windows\\Panther\\Unattend.xml**, dans la mesure où il empêchera l'exécution du script de téléchargement utilisé ultérieurement.
10. Préparez l'image avec SYSPREP. Exécutez la commande suivante dans une invite de commandes avec élévation de privilèges :

    **C:\\Windows\\System32\\sysprep\\sysprep.exe /generalize /oobe /shutdown**

    **Remarque :** n'utilisez pas le paramètre **/mode:vm** de la commande SYSPREP, bien qu'il s'agisse d'une machine virtuelle.

## **Étape 2 : Création d'un service RemoteApp**

1.  Accédez à la page RemoteApp du [portail de gestion Azure][].
2.  Cliquez sur **Nouveau \> Créer avec VPN**.
3.  Entrez le nom de votre service, puis cliquez sur **Créer un service RemoteApp**.

Après avoir créé votre service RemoteApp, accédez à la page **Démarrage rapide** de RemoteApp pour passer aux étapes de configuration.

## **Étape 3 : Liaison avec un réseau virtuel**

Un réseau virtuel permet aux utilisateurs d'accéder aux données de votre réseau local via des ressources distantes de RemoteApp. La configuration de votre liaison au réseau virtuel se décompose en quatre étapes :

1.  Sur la page Démarrage rapide, cliquez sur **Lier à un réseau virtuel RemoteApp**.
2.  Indiquez si vous souhaitez créer un réseau virtuel ou utiliser un réseau existant. Pour les besoins de ce didacticiel, nous allons créer un réseau.
3.  Indiquez les informations suivantes pour votre nouveau réseau :

    -   Nom
    -   Espace d'adressage du réseau virtuel
    -   Espace d'adressage local
    -   Adresse IP du serveur DNS
    -   Adresse IP du réseau privé virtuel (VPN)

    Pour plus d'informations, consultez la page [Configurer un VPN de site à site dans le portail de gestion][].

4.  De retour sur la page Démarrage rapide, cliquez sur **get script** afin de télécharger un script permettant de configurer votre périphérique VPN pour qu'il se connecte au réseau virtuel que vous venez de créer. Vous aurez besoin des informations suivantes sur le périphérique VPN :

    -   Fournisseur
    -   Plateforme
    -   Système d'exploitation

    Enregistrez le script et exécutez-le sur le périphérique VPN.

    **Remarque :** une fois ce script exécuté, le réseau virtuel passe à l'état « Prêt ». Cette opération peut prendre entre 5 et 7 minutes. Vous ne pourrez pas utiliser le réseau tant qu'il ne sera pas prêt.

5.  Enfin, de retour sur la page Démarrage rapide, cliquez sur **joindre le domaine local**. Ajoutez le compte de service RemoteApp à votre domaine Active Directory local. Vous aurez besoin du nom de domaine, de l'unité d'organisation, ainsi que du nom d'utilisateur et du mot de passe du compte de service.

## **Étape 4 : Liaison avec une image de modèle RemoteApp**

Une image de modèle RemoteApp contient les programmes que vous souhaitez partager avec les utilisateurs. Vous pouvez soit télécharger la nouvelle image de modèle que vous avez créée à l'étape 1, soit associer une image existante (une image déjà téléchargée sur Azure).

Si vous optez pour le téléchargement de la nouvelle image, vous devez entrer un nom et choisir son emplacement. Plusieurs cmdlets PowerShell sont affichées sur la page suivante de l'Assistant. Copiez-les et exécutez-les à partir d'une invite de commandes Azure PowerShell avec élévation de privilèges afin de télécharger l'image spécifiée.

En cas d'association d'une image de modèle existante, il vous suffit de spécifier le nom de l'image, son emplacement et l'abonnement Azure associé.

## **Étape 5 : Configuration de la synchronisation d'annuaires Active Directory**

RemoteApp nécessite une synchronisation d'annuaires entre Azure Active Directory et votre annuaire Active Directory local afin de synchroniser les utilisateurs, groupes, contacts et mots de passe avec votre client Azure Active Directory. Pour plus d'informations sur la planification, consultez la rubrique [Programme de synchronisation d'annuaires][].

## **Étape 6 : Publication de programmes RemoteApp**

Un programme RemoteApp est l'application ou le programme mis à la disposition de vos utilisateurs. Il se situe dans l'image de modèle que vous avez téléchargée pour le service. Lorsqu'un utilisateur accède à un programme RemoteApp, celui-ci semble s'exécuter dans son environnement local. En réalité, il s'exécute dans Azure.

Avant que vos utilisateurs puissent accéder à des programmes RemoteApp, vous devez les publier sur le flux de l'utilisateur final ; il s'agit d'une liste des programmes disponibles auxquels vos utilisateurs peuvent accéder via le portail Azure.

Vous pouvez publier plusieurs programmes sur votre service RemoteApp. Sur la page des programmes RemoteApp, cliquez sur **Publier** afin d'ajouter un programme. Vous pouvez publier le programme à partir du menu Démarrer de l'image de modèle ou en indiquant le chemin sur l'image de modèle du programme. Si vous choisissez la première option, sélectionnez le programme à publier. Si vous optez pour la deuxième solution, indiquez le nom du programme, ainsi que le chemin d'accès au répertoire d'installation sur l'image de modèle.

## **Étape 7 : Configuration de l'accès utilisateur**

Maintenant que vous avez créé votre service RemoteApp, vous devez ajouter les utilisateurs et groupes qui seront autorisés à utiliser vos ressources distantes. Ceux-ci doivent se trouver dans le client Active Directory associé à l'abonnement que vous avez utilisé pour créer ce service RemoteApp.

1.  Sur la page Démarrage rapide, cliquez sur **Configurer l'accès utilisateur**.
2.  Entrez le nom du groupe ou du compte professionnel (à partir d'Active Directory) ou le compte Microsoft auquel vous souhaitez accorder l'accès.

    Dans le cas des utilisateurs, veillez à utiliser le format <user@domain.com>. Dans le cas des groupes, entrez le nom.

3.  Une fois les utilisateurs ou groupes validés, cliquez sur **Enregistrer**.

## Étapes suivantes

Félicitations ! Vous avez créé et déployé correctement votre déploiement hybride RemoteApp. L'étape suivante consistera à faire en sorte que vos utilisateurs téléchargent et installent le client Bureau à distance. L'URL du client est disponible sur la page Démarrage rapide de RemoteApp. Les utilisateurs devront ensuite se connecter à Azure et accéder aux programmes RemoteApp que vous avez publiés.

  [mises à jour requises]: http://support.microsoft.com/kb/2977219
  [version préliminaire de RemoteApp]: http://azure.microsoft.com/en-us/services/remoteapp/
  [Azure PowerShell]: http://azure.microsoft.com/en-us/documentation/articles/install-configure-powershell/
  [portail de gestion Azure]: http://manage.windowsazure.com
  [Configurer un VPN de site à site dans le portail de gestion]: http://msdn.microsoft.com/library/azure/dn133795.aspx
  [Programme de synchronisation d'annuaires]: http://msdn.microsoft.com//library/azure/hh967642.aspx

<properties linkid="manage-services-how-to-configure-a-cloud-service" urlDisplayName="How to configure" pageTitle="How to configure a cloud service - Azure" metaKeywords="Configuring cloud services" description="Learn how to configure cloud services in Azure. Learn to update the cloud service configuration and configure remote access to role instances." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Configure Cloud Services" authors="davidmu" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="davidmu"></tags>

# <span id="configurecloudservice"></span></a>Configuration de services cloud

[WACOM.INCLUDE [disclaimer][]]

Vous pouvez configurer les paramètres utilisateur les plus communément utilisés pour un service cloud dans le portail de gestion Azure. Ou, si vous voulez mettre à jour directement vos fichiers de configuration, téléchargez un fichier de configuration de service pour le mettre à jour, puis chargez-le et mettez à jour le service cloud avec les modifications de la configuration. Dans les deux cas, les mises à jour de la configuration sont transmises à toutes les instances de rôle.

Vous pouvez également activer une connexion Bureau à distance à l'un ou l'ensemble des rôles en cours d'exécution dans votre service cloud. Le Bureau à distance vous permet d'accéder au bureau de votre application lorsqu'elle est en cours d'exécution, et de dépanner et diagnostiquer les problèmes. Vous pouvez activer une connexion Bureau à distance à votre rôle, même si vous n'avez pas configuré le fichier de définition de service (.csdef) pour le Bureau à distance pendant le développement de l'application. Il n'est pas nécessaire de redéployer votre application pour activer une connexion Bureau à distance.

Azure ne peut garantir que 99,95 % de disponibilité du service pendant les mises à jour de la configuration si vous avez au moins deux instances de rôle (machines virtuelles) pour chaque rôle. Cela permet à une machine virtuelle de traiter les demandes du client pendant que l'autre est mise à jour. Pour plus d'informations, consultez la page [Contrats de niveau de service][].

## Sommaire

-   [Mise à jour de la configuration d'un service cloud][]
-   [Configuration de l'accès à distance aux instances de rôle][]

## <span id="update"></span></a> Mise à jour de la configuration d'un service cloud

1.  Dans le [portail de gestion Azure][], cliquez sur **Cloud Services**. Cliquez ensuite sur le nom du service cloud pour ouvrir le tableau de bord.

2.  Cliquez sur **Configurer**.

    Sur la page **Configure**, vous pouvez configurer la surveillance, mettre à jour les paramètres de rôle et choisir le système d'exploitation invité et la famille pour les instances de rôle (machines virtuelles).

    ![Page de configuration][]

3.  Dans les paramètres de surveillance, définissez le niveau de surveillance sur Détaillé ou Minimal et configurez les chaînes de connexion des diagnostics requises pour la surveillance détaillée. Pour obtenir des instructions, consultez la rubrique [Surveillance des services cloud][].

4.  Pour les rôles de service (regroupés par rôle), vous pouvez mettre à jour les paramètres suivants :

    > -   **Paramètres** Modifiez les valeurs de divers paramètres de configuration spécifiés dans les éléments *ConfigurationSettings* du fichier de configuration de service (.cscfg).

    > -   **Certificats** Changez l'empreinte numérique de certificat qui est utilisée dans le chiffrement SSL pour un rôle. Pour changer un certificat, vous devez d'abord télécharger le nouveau certificat (sur la page **Certificats**). Mettez ensuite à jour l'empreinte numérique dans la chaîne de certificat affichée dans les paramètres de rôle.

5.  Dans les **paramètres du système d'exploitation**, vous pouvez changer la famille ou la version du système d'exploitation pour les instances de rôle (machines virtuelles) ou choisir **Automatique** pour reprendre les mises à jour automatiques de la version actuelle du système d'exploitation. Les paramètres du système d'exploitation s'appliquent aux rôles Web et de travail, mais n'ont aucune incidence sur les rôles de machine virtuelle qui ont été ajoutés aux services hébergés dans le précédent portail de gestion Azure.

    Lorsque vous déployez un nouveau service cloud, vous pouvez choisir le système d'exploitation Windows Server 2008 R2, Windows Server 2008 avec Service Pack 2 (SP2) ou Windows Server 2012. Pendant le déploiement, la version la plus récente du système d'exploitation est installée sur toutes les instances de rôle et les systèmes d'exploitation sont mis à jour automatiquement par défaut.

    Si vous avez besoin que votre service cloud s'exécute sur un système d'exploitation différent à cause de problèmes de compatibilité dans votre code, vous pouvez choisir une famille et une version de systèmes d'exploitation. Lorsque vous choisissez une version particulière du système d'exploitation, les mises à jour automatiques des systèmes d'exploitation sont suspendues pour le service cloud. Vous devez alors veiller à ce que les systèmes d'exploitation reçoivent les mises à jour.

    Si vous résolvez tous les problèmes de compatibilité rencontrés par vos applications avec la version la plus récente du système d'exploitation, vous pouvez reprendre les mises à jour automatiques des systèmes d'exploitation en définissant la version du système d'exploitation sur **Automatique**.

    ![Paramètres du système d'exploitation][]

6.  Pour enregistrer vos paramètres de configuration et les transmettre aux instances de rôle, cliquez sur **Enregistrer**. Cliquez sur **Ignorer** pour annuler les modifications. Les commandes **Enregistrer** et **Ignorer** sont ajoutées à la barre de commandes lorsque vous avez modifié un paramètre.

### Mise à jour manuelle d'un fichier de configuration de service cloud

1.  Téléchargez un fichier de configuration de service cloud (.cscfg) avec la configuration actuelle. Sur la page **Configurer** du service cloud, cliquez sur **Télécharger**. Cliquez ensuite sur **Enregistrer** ou **Enregistrer sous** pour enregistrer le fichier.

2.  Après la mise à jour du fichier de configuration de service, téléchargez et appliquez les mises à jour de la configuration :

    a. Sur la page **Configurer**, cliquez sur **Upload**.

    La page **Télécharger un nouveau fichier de configuration** s'ouvre.

    ![Télécharger une configuration][]

    b. Dans **Fichier de configuration**, cliquez sur **Parcourir** pour sélectionner le fichier .cscfg mis à jour.

    c. Si votre service cloud contient des rôles qui ont une seule instance, activez la case à cocher **Apply configuration even if one or more roles contain a single instance** afin de permettre l'exécution des mises à jour de la configuration pour les rôles.

    Si vous n'avez pas défini au moins deux instances de chaque rôle, Azure n'est pas en mesure de garantir au moins 99,95 % de disponibilité pour votre service cloud pendant les mises à jour de la configuration du service. Pour plus d'informations, consultez la page [Contrats de niveau de service][1].

    d. Cliquez sur OK (coche).

## <span id="remoteaccess"></span></a> Configuration de l'accès à distance aux instances de rôle

Le Bureau à distance vous permet d'accéder au bureau d'un rôle en cours d'exécution dans Azure. Vous pouvez utiliser une connexion Bureau à distance pour dépanner et diagnostiquer les problèmes rencontrés par votre application lorsqu'elle est en cours d'exécution. Vous pouvez activer une connexion Bureau à distance dans votre rôle pendant la conception de l'application ou après son déploiement dans Azure (pendant que le rôle est en cours d'exécution). L'activation d'une connexion Bureau à distance dans un rôle en cours d'exécution par l'intermédiaire du portail de gestion ne vous oblige pas à redéployer votre application. Pour authentifier la connexion Bureau à distance, vous pouvez utiliser un certificat préalablement téléchargé ou en créer un.

Sur la page **Configurer** de votre service cloud, vous pouvez activer le Bureau à distance, ou changer le compte Administrateur local ou le mot de passe utilisés pour la connexion aux machines virtuelles, le certificat employé dans l'authentification ou la date d'expiration.

<div class="dev-callout"> 
<b>Remarque</b> 
<p>Si votre service cloud est constitu&eacute; de plusieurs machines virtuelles bas&eacute;es sur Windows Server connect&eacute;es, vous n'&ecirc;tes pas oblig&eacute; de configurer l'acc&egrave;s &agrave; distance, car ces machines virtuelles sont configur&eacute;es automatiquement pour le Bureau &agrave; distance.</p> 
</div>

### Configuration de l'accès à distance dans le fichier de définition de service

Ajoutez des éléments **Import** au fichier de définition de service (.csdef) pour importer les modules RemoteAccess et RemoteForwarder dans le modèle de service. Lorsque ces modules sont présents, Azure ajoute les paramètres de configuration du Bureau à distance au fichier de configuration de service. Pour terminer la configuration du Bureau à distance, vous devez importer un certificat dans Azure et spécifier le certificat dans le fichier de configuration de service. Pour plus d'informations, consultez la page [Configurer une connexion Bureau à distance pour un rôle dans Azure][].

### Activation ou modification de l'accès à distance pour les instances de rôle dans le portail de gestion

1.  Connectez-vous au [portail de gestion][portail de gestion Azure] et cliquez sur **Cloud Services**. Cliquez ensuite sur le nom du service cloud pour ouvrir le tableau de bord.

2.  Ouvrez la page **Configurer** du service cloud et cliquez sur **Remote**.

    La page **Configurer le Bureau à distance** affiche (le cas échéant) les paramètres qui ont été ajoutés au fichier de configuration de service lors du déploiement du service cloud, comme indiqué ci-après.

    ![Services cloud à distance][]

> [WACOM.NOTE]
> **Avertissement :** toutes les instances de rôle sont redémarrées lorsque vous activez pour la première fois le Bureau à distance et cliquez sur OK (coche). Pour éviter un redémarrage, le certificat utilisé pour chiffrer le mot de passe doit être installé sur le rôle. Si aucun certificat n'est installé, l'option suivante est affichée : ![CloudServices\_CreateNewCertDropDown][]

    To prevent a restart, install a certificate and then return to this dialog (see [Using Remote Desktop with Azure Roles][] for more information). If you choose an existing certificate, then a configuration update will be sent to all the instances in the role.

1.  Dans **Rôles**, sélectionnez le rôle de service que vous voulez mettre à jour ou sélectionnez **Tous** pour tous les rôles.

2.  Effectuez les modifications suivantes :

-   Pour activer le Bureau à distance, activez la case à cocher **Enable Remote Desktop**. Pour désactiver le Bureau à distance, désactivez la case à cocher.

-   Créez un compte pour utiliser les connexions Bureau à distance aux instances de rôle.

-   Mettez à jour le mot de passe du compte existant.

-   Sélectionnez le certificat téléchargé à utiliser pour l'authentification (téléchargez le certificat en utilisant la commande **Upload** sur la page **Certificats**) ou créez un certificat.

-   Changez la date d'expiration de la configuration du Bureau à distance.

1.  Lorsque les mises à jour de la configuration sont terminées, cliquez sur OK (coche).

2.  Pour vous connecter à une instance de rôle :

    a. Cliquez sur **Instances** pour ouvrir la page du même nom.

    b. Sélectionnez une instance de rôle pour laquelle Bureau à distance est configuré.

    c. Cliquez sur **Connect** et suivez les instructions pour ouvrir le bureau de la machine virtuelle.

    d. Cliquez sur **Ouvrir**, puis sur **Connect** pour démarrer la connexion Bureau à distance.

### Désactivation de l'accès à distance pour les instances de rôle dans le portail de gestion

1.  Connectez-vous au [portail de gestion][portail de gestion Azure] et cliquez sur **Cloud Services**. Cliquez ensuite sur le nom du service cloud pour ouvrir le tableau de bord.

2.  Ouvrez la page **Configurer** du service cloud et cliquez sur **Remote**.

3.  Dans **Rôles**, sélectionnez le rôle de service que vous voulez mettre à jour ou sélectionnez **Tous** pour tous les rôles.

4.  Désactivez la case à cocher **Activer le Bureau à distance**.

5.  Cliquez sur OK (coche).

  [disclaimer]: ../includes/disclaimer.md
  [Contrats de niveau de service]: https://www.windowsazure.com/fr-fr/support/legal/sla/
  [Mise à jour de la configuration d'un service cloud]: #update
  [Configuration de l'accès à distance aux instances de rôle]: #remoteaccess
  [portail de gestion Azure]: http://manage.windowsazure.com/
  [Page de configuration]: ./media/cloud-services-how-to-configure/CloudServices_ConfigurePage1.png
  [Surveillance des services cloud]: ../how-to-monitor-a-cloud-service/
  [Paramètres du système d'exploitation]: ./media/cloud-services-how-to-configure/CloudServices_ConfigurePage_OSSettings.png
  [Télécharger une configuration]: ./media/cloud-services-how-to-configure/CloudServices_UploadConfigFile.png
  [1]: http://www.windowsazure.com/fr-fr/support/legal/sla/
  [Configurer une connexion Bureau à distance pour un rôle dans Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh124107.aspx
  [Services cloud à distance]: ./media/cloud-services-how-to-configure/CloudServices_Remote.png
  [CloudServices\_CreateNewCertDropDown]: ./media/cloud-services-how-to-configure/CloudServices_CreateNewCertDropDown.png

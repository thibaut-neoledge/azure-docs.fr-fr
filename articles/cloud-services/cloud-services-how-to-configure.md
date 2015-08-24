<properties 
	pageTitle="Configuration d'un service cloud | Microsoft Azure" 
	description="Découvrez comment configurer des services cloud dans Azure. Apprenez à mettre à jour la configuration d'un service cloud et à configurer l'accès distant aux instances de rôle." 
	services="cloud-services" 
	documentationCenter="" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015"
	ms.author="adegeo"/>




# Configuration des services cloud

> [AZURE.SELECTOR]
- [Azure Portal](cloud-services-how-to-configure.md)
- [Azure Preview Portal](cloud-services-how-to-configure-portal.md)

Vous pouvez configurer les paramètres les plus couramment utilisés pour un service cloud dans le portail de gestion Azure. Ou, si vous voulez mettre à jour directement vos fichiers de configuration, téléchargez un fichier de configuration de service pour le mettre à jour, puis chargez-le et mettez à jour le service cloud avec les modifications de la configuration. Dans les deux cas, les mises à jour de la configuration sont transmises à toutes les instances de rôle.

Vous pouvez également activer une connexion Bureau à distance à l'un ou l'ensemble des rôles en cours d'exécution dans votre service cloud. Le Bureau à distance vous permet d'accéder au bureau de votre application lorsqu'elle est en cours d'exécution, et de dépanner et diagnostiquer les problèmes. Vous pouvez activer une connexion Bureau à distance à votre rôle, même si vous n'avez pas configuré le fichier de définition de service (.csdef) pour le Bureau à distance pendant le développement de l'application. Il n'est pas nécessaire de redéployer votre application pour activer une connexion Bureau à distance.

Azure ne peut garantir que 99,95 % de disponibilité du service pendant les mises à jour de la configuration si vous avez au moins deux instances de rôle pour chaque rôle. Cela permet à une machine virtuelle de traiter les demandes du client pendant que l'autre est mise à jour. Pour plus d'informations, consultez la page [Contrats de niveau de service](http://azure.microsoft.com/support/legal/sla/).

## Modification d'un service cloud

1. Dans le [portail Azure](http://manage.windowsazure.com/), cliquez sur **Cloud Services**, cliquez sur le nom du service cloud, puis sur **Configurer**.

    ![Page de configuration](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage1.png)
    
    Sur la page **Configurer**, vous pouvez configurer la surveillance, mettre à jour les paramètres de rôle et choisir le système d'exploitation invité et la famille pour les instances de rôle.

2. Dans les paramètres de **surveillance**, définissez le niveau de surveillance sur Détaillé ou Minimal et configurez les chaînes de connexion des diagnostics requises pour la surveillance détaillée.

3. Pour les rôles de service (regroupés par rôle), vous pouvez mettre à jour les paramètres suivants :
    
    >**Paramètres**Modifiez les valeurs de divers paramètres de configuration spécifiés dans les éléments *ConfigurationSettings* du fichier de configuration de service (.cscfg).
    >
    >**Certificats** Changez l'empreinte numérique de certificat qui est utilisée dans le chiffrement SSL pour un rôle. Pour changer un certificat, vous devez d'abord télécharger le nouveau certificat (sur la page **Certificats**). Mettez ensuite à jour l'empreinte numérique dans la chaîne de certificat affichée dans les paramètres de rôle.

4. Dans les paramètres du **système d'exploitation**, vous pouvez changer la famille ou la version du système d'exploitation pour les instances de rôle (machines virtuelles) ou choisir **Automatique** pour reprendre les mises à jour automatiques de la version actuelle du système d'exploitation. Les paramètres du système d'exploitation s'appliquent aux rôles web et de travail, mais n'affectent pas les machines virtuelles.

    Pendant le déploiement, la version la plus récente du système d'exploitation est installée sur toutes les instances de rôle et les systèmes d'exploitation sont mis à jour automatiquement par défaut.
    
    Si vous avez besoin que votre service cloud s'exécute sur un système d'exploitation différent à cause de problèmes de compatibilité dans votre code, vous pouvez choisir une famille et une version de systèmes d'exploitation. Lorsque vous choisissez une version particulière du système d'exploitation, les mises à jour automatiques des systèmes d'exploitation sont suspendues pour le service cloud. Vous devez alors veiller à ce que les systèmes d'exploitation reçoivent les mises à jour.
    
    Si vous résolvez tous les problèmes de compatibilité rencontrés par vos applications avec la version la plus récente du système d'exploitation, vous pouvez reprendre les mises à jour automatiques des systèmes d'exploitation en définissant la version du système d'exploitation sur **Automatique**.
    
    ![Paramètres du système d'exploitation](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage_OSSettings.png)

5. Pour enregistrer vos paramètres de configuration et les transmettre aux instances de rôle, cliquez sur **Enregistrer**. Cliquez sur **Ignorer** pour annuler les modifications. Les commandes **Enregistrer** et **Ignorer** sont ajoutées à la barre de commandes lorsque vous avez modifié un paramètre.

## Mise à jour d'un fichier de configuration de service cloud

1. Téléchargez un fichier de configuration de service cloud (.cscfg) avec la configuration actuelle. Sur la page **Configurer** du service cloud, cliquez sur **Télécharger**. Cliquez ensuite sur **Enregistrer** ou **Enregistrer sous** pour enregistrer le fichier.

2. Après la mise à jour du fichier de configuration de service, téléchargez et appliquez les mises à jour de la configuration :

    1. Sur la page **Configurer**, cliquez sur **Télécharger**.
    
        ![Télécharger une configuration](./media/cloud-services-how-to-configure/CloudServices_UploadConfigFile.png)
    
    2. Dans **Fichier de configuration**, cliquez sur **Parcourir** pour sélectionner le fichier .cscfg mis à jour.
    
    3. Si votre service cloud contient des rôles qui ont une seule instance, cochez la case **Appliquer la configuration même si un ou plusieurs rôles contiennent une seule instance** afin de permettre l’exécution des mises à jour de la configuration pour les rôles.
    
        Si vous n'avez pas défini au moins deux instances de chaque rôle, Azure n'est pas en mesure de garantir au moins 99,95 % de disponibilité pour votre service cloud pendant les mises à jour de la configuration du service. Pour plus d'informations, consultez la page [Contrats de niveau de service](http://azure.microsoft.com/support/legal/sla/).
    
    4. Cliquez sur **OK** (coche).


## Configuration de l'accès à distance aux instances de rôle

Le Bureau à distance vous permet d'accéder au bureau d'un rôle en cours d'exécution dans Azure. Vous pouvez utiliser une connexion Bureau à distance pour dépanner et diagnostiquer les problèmes rencontrés par votre application lorsqu'elle est en cours d'exécution. Vous pouvez activer une connexion Bureau à distance dans votre rôle pendant la conception de l'application ou après son déploiement dans Azure (pendant que le rôle est en cours d'exécution). L'activation d'une connexion Bureau à distance dans un rôle en cours d'exécution par l'intermédiaire du portail de gestion ne vous oblige pas à redéployer votre application. Pour authentifier la connexion Bureau à distance, vous pouvez utiliser un certificat préalablement téléchargé ou en créer un.

Sur la page **Configurer** de votre service cloud, vous pouvez activer le Bureau à distance, ou changer le compte Administrateur local ou le mot de passe utilisés pour la connexion aux machines virtuelles, le certificat employé dans l'authentification ou la date d'expiration.

### Configuration de l'accès à distance dans le fichier de définition de service

Ajoutez des éléments **Import** au fichier de définition de service (.csdef) pour importer les modules RemoteAccess et RemoteForwarder dans le modèle de service. Lorsque ces modules sont présents, Azure ajoute les paramètres de configuration du Bureau à distance au fichier de configuration de service. Pour terminer la configuration du Bureau à distance, vous devez importer un certificat dans Azure et spécifier le certificat dans le fichier de configuration de service. Pour plus d'informations, consultez la page [Configurer une connexion Bureau à distance pour un rôle dans Azure][].

### Activation ou modification de l'accès à distance pour les instances de rôle dans le portail de gestion

1. Cliquez sur **Cloud Services**, cliquez sur le nom du service cloud, puis cliquez sur **Configurer**.

2. Cliquez sur **Distant**.
    
    ![Services cloud à distance](./media/cloud-services-how-to-configure/CloudServices_Remote.png)
    
    **Avertissement :** toutes les instances de rôle sont redémarrées quand vous activez pour la première fois le Bureau à distance et cliquez sur OK (coche). Pour éviter un redémarrage, le certificat utilisé pour chiffrer le mot de passe doit être installé sur le rôle.
    
    Pour éviter un redémarrage, installez un certificat, puis revenez dans cette boîte de dialogue (pour plus d'informations, consultez la page [Utilisation du Bureau à distance avec des rôles Azure][]). Si vous choisissez un certificat existant, une mise à jour de la configuration sera envoyée à toutes les instances dans le rôle.

3. Dans **Rôles**, sélectionnez le rôle que vous voulez mettre à jour ou sélectionnez **Tous** pour tous les rôles.

4. Effectuez les modifications suivantes :
    
    - Pour activer le Bureau à distance, activez la case à cocher **Enable Remote Desktop**. Pour désactiver le Bureau à distance, désactivez la case à cocher.
    
    - Créez un compte pour utiliser les connexions Bureau à distance aux instances de rôle.
    
    - Mettez à jour le mot de passe du compte existant.
    
    - Sélectionnez le certificat téléchargé à utiliser pour l'authentification (téléchargez le certificat en utilisant la commande **Upload** sur la page **Certificats**) ou créez un certificat.
    
    - Changez la date d'expiration de la configuration du Bureau à distance.

5. Lorsque les mises à jour de la configuration sont terminées, cliquez sur **OK** (coche).

6. Pour vous connecter à une instance de rôle :
    
    1. Cliquez sur **Instances** pour ouvrir la page **Instances**.
    
    2. Sélectionnez une instance de rôle pour laquelle la fonctionnalité Bureau à distance est configurée.
    
    3. Cliquez sur **Connecter** et suivez les instructions pour ouvrir le Bureau.
    
    4. Cliquez sur **Ouvrir**, puis sur **Connecter** pour démarrer la connexion Bureau à distance.

### Désactivation de l'accès à distance pour les instances de rôle dans le portail de gestion

1. Cliquez sur **Cloud Services**, cliquez sur le nom du service cloud, puis cliquez sur **Configurer**.

2. Cliquez sur **Distant**.

3. Dans **Rôles**, sélectionnez le rôle que vous voulez mettre à jour ou sélectionnez **Tous** pour tous les rôles.

4. Désactivez la case à cocher **Activer le Bureau à distance**.

5. Cliquez sur **OK** (coche).

[Configurer une connexion Bureau à distance pour un rôle dans Azure]: https://msdn.microsoft.com/library/azure/hh124107.aspx

[Utilisation du Bureau à distance avec des rôles Azure]: https://msdn.microsoft.com/library/azure/gg443832.aspx
			
 

<!---HONumber=August15_HO7-->
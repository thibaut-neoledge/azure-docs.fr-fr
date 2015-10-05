<properties 
	pageTitle="Configuration d’un service cloud (portail en version préliminaire) | Microsoft Azure" 
	description="Découvrez comment configurer des services cloud dans Azure. Apprenez à mettre à jour la configuration d'un service cloud et à configurer l'accès distant aux instances de rôle. Ces exemples utilisent le portail Azure en version préliminaire." 
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
	ms.date="09/22/2015"
	ms.author="adegeo"/>




# Configuration des services cloud

> [AZURE.SELECTOR]
- [Azure Portal](cloud-services-how-to-configure.md)
- [Azure Preview Portal](cloud-services-how-to-configure-portal.md)

Vous pouvez configurer les paramètres les plus couramment utilisés pour un service cloud dans le portail de gestion Azure. Ou, si vous voulez mettre à jour directement vos fichiers de configuration, téléchargez un fichier de configuration de service pour le mettre à jour, puis chargez-le et mettez à jour le service cloud avec les modifications de la configuration. Dans les deux cas, les mises à jour de la configuration sont transmises à toutes les instances de rôle.

Vous pouvez également activer une connexion Bureau à distance à l'un ou l'ensemble des rôles en cours d'exécution dans votre service cloud. Le Bureau à distance vous permet d'accéder au bureau de votre application lorsqu'elle est en cours d'exécution, et de dépanner et diagnostiquer les problèmes. Vous pouvez activer une connexion Bureau à distance à votre rôle, même si vous n'avez pas configuré le fichier de définition de service (.csdef) pour le Bureau à distance pendant le développement de l'application. Il n'est pas nécessaire de redéployer votre application pour activer une connexion Bureau à distance.

Azure ne peut garantir que 99,95 % de disponibilité du service pendant les mises à jour de la configuration si vous avez au moins deux instances de rôle pour chaque rôle. Cela permet à une machine virtuelle de traiter les demandes du client pendant que l'autre est mise à jour. Pour plus d'informations, consultez la page [Contrats de niveau de service](http://azure.microsoft.com/support/legal/sla/).

## Modification d'un service cloud

1. Dans le [portail Azure en version préliminaire](http://portal.azure.com/), accédez à votre service cloud.

2. Cliquez sur l’icône **Paramètres** ou le lien **Essentials/All settings** pour ouvrir le panneau **Paramètres**.

    ![Page Paramètres](./media/cloud-services-how-to-configure-portal/cloud-service.png)
    
    À partir de là, vous pouvez afficher les **Propriétés**, modifier la **Configuration** et gérer les **Certificats** et les **Utilisateurs** qui ont accès à ce service cloud.

2. Dans la section **Analyse**, vous pouvez cliquer sur n'importe quelle vignette pour configurer des alertes.

    ![Surveillance du service cloud](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)
    
3. Dans la section **Rôles et instances**, vous pouvez cliquer sur n'importe quel rôle de service cloud pour gérer l'instance.

    ![Instance de service cloud](./media/cloud-services-how-to-configure-portal/cs-instance.png)
    
    Vous pouvez vous connecter à distance pour redémarrer ou réinitialiser le service cloud depuis ici.
    
    ![Boutons d’instance de service cloud](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

>[AZURE.NOTE]Le système d'exploitation utilisé pour le service cloud ne peut pas être modifié à l'aide du **portail Azure en version préliminaire**, vous pouvez modifier ce paramètre uniquement via la [version non-préliminaire du portail](http://manage.windowsazure.com/). Cela est détaillé [ici](cloud-services-how-to-configure.md#update-a-cloud-service-configuration-file).

## Mise à jour d'un fichier de configuration de service cloud

1. Tout d'abord, téléchargez le fichier de configuration de service cloud existant (.cscfg).

    1. Dans le [portail Azure en version préliminaire](http://portal.azure.com/), accédez à votre service cloud.

    2. Cliquez sur l’icône **Paramètres** ou le lien **Essentials/All settings** pour ouvrir le panneau **Paramètres**.

        ![Page Paramètres](./media/cloud-services-how-to-configure-portal/cloud-service.png)
    
    3. Cliquez sur l’élément **Configuration**.

        ![Panneau de configuration](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
    
    4. Cliquez sur le bouton **Download**.

        ![Télécharger](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)

2. Après la mise à jour du fichier de configuration de service, téléchargez et appliquez les mises à jour de la configuration :

    1. Suivez les 3 premières étapes ci-dessus pour ouvrir le panneau **Configuration** du service cloud.
    
    2. Cliquez sur le bouton **Upload**.

        ![Télécharger](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
    
    3. Sélectionnez le fichier .cscfg et cliquez sur **OK**.

## Configuration de l'accès à distance aux instances de rôle

L’accès à distance ne peut pas être configuré à l'aide du **portail Azure en version préliminaire**, vous pouvez modifier ce paramètre uniquement via la [version non-préliminaire du portail](http://manage.windowsazure.com/). Cette opération est décrite [ici](cloud-services-role-enable-remote-desktop.md).
			
## Étapes suivantes

* Découvrez comment [déployer un service cloud](cloud-services-how-to-create-deploy-portal.md).
* Configurez un [nom de domaine personnalisé](cloud-services-custom-domain-name-portal.md).
* [Gérez votre service cloud](cloud-services-how-to-manage-portal.md).
* Configurez des [certificats SSL](cloud-services-configure-ssl-certificate-portal.md).

<!---HONumber=Sept15_HO4-->
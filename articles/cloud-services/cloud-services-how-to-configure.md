<properties 
	pageTitle="Configuration d’un service cloud (portail Classic) | Microsoft Azure" 
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
	ms.date="04/22/2016"
	ms.author="adegeo"/>




# Configuration des services cloud

> [AZURE.SELECTOR]
- [Portail Azure](cloud-services-how-to-configure-portal.md)
- [Portail Azure Classic](cloud-services-how-to-configure.md)

Vous pouvez configurer les paramètres les plus couramment utilisés pour un service cloud dans le portail Azure Classic. Ou, si vous voulez mettre à jour directement vos fichiers de configuration, téléchargez un fichier de configuration de service pour le mettre à jour, puis chargez-le et mettez à jour le service cloud avec les modifications de la configuration. Dans les deux cas, les mises à jour de la configuration sont transmises à toutes les instances de rôle.

Le portail Azure Classic vous permet également d'[activer la connexion Bureau à distance pour un rôle dans Azure Cloud Services](cloud-services-role-enable-remote-desktop.md).

Azure ne peut garantir que 99,95 % de disponibilité du service pendant les mises à jour de la configuration si vous avez au moins deux instances de rôle pour chaque rôle. Cela permet à une machine virtuelle de traiter les demandes du client pendant que l'autre est mise à jour. Pour plus d'informations, consultez la page [Contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/).

## Modification d'un service cloud

1. Dans le [portail Azure Classic](http://manage.windowsazure.com/), cliquez sur **Cloud Services**, cliquez sur le nom du service cloud, puis sur **Configurer**.

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
    
        Si vous n'avez pas défini au moins deux instances de chaque rôle, Azure n'est pas en mesure de garantir au moins 99,95 % de disponibilité pour votre service cloud pendant les mises à jour de la configuration du service. Pour plus d'informations, consultez la page [Contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/).
    
    4. Cliquez sur **OK** (coche).


## Étapes suivantes

* Découvrez comment [déployer un service cloud](cloud-services-how-to-create-deploy.md).
* Configurez un [nom de domaine personnalisé](cloud-services-custom-domain-name.md).
* [Gérez votre service cloud](cloud-services-how-to-manage.md).
* [Activer une connexion Bureau à distance pour un rôle dans Azure Cloud Services](cloud-services-role-enable-remote-desktop.md)
* Configurez des [certificats SSL](cloud-services-configure-ssl-certificate.md).

<!---HONumber=AcomDC_0511_2016-->
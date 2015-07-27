<properties 
	pageTitle="Gestion d'un service cloud - Azure" 
	description="Découvrez comment gérer des services cloud dans le portail Azure en version préliminaire." 
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
	ms.date="07/01/2015"
	ms.author="adegeo"/>


# Gestion des services cloud

> [AZURE.SELECTOR]
- [Azure Portal](cloud-services-how-to-manage.md)
- [Azure Preview Portal](cloud-services-how-to-manage-portal.md)

Dans la zone **Cloud Services** du portail Azure en version préliminaire, vous pouvez mettre à jour un rôle de service ou un déploiement, promouvoir un déploiement intermédiaire en déploiement de production, lier des ressources à votre service cloud afin de voir les dépendances de ressources et d'étendre les ressources en même temps, mais aussi supprimer un service cloud ou un déploiement.


## Mise à jour d’un rôle ou d’un déploiement de service cloud

Si vous devez mettre à jour le code de l'application pour votre service cloud, utilisez **Update** dans le panneau de service cloud. Vous pouvez mettre à jour un ou plusieurs rôles. Vous devrez charger un nouveau package de service et un nouveau fichier de configuration de service.

1. Dans le [portail Azure en version préliminaire][], sélectionnez le service cloud que vous souhaitez mettre à jour. Le panneau d'instance de service cloud s'ouvre.

2. Dans le panneau, cliquez sur le bouton **Update**.

    ![Bouton Update](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Mettez à jour le déploiement avec un nouveau fichier de package de service (.cspkg) et un fichier de configuration de service (.cscfg).

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. Mettez **éventuellement** à jour l'étiquette de déploiement et le compte de stockage.

5. Si la mise à jour change le nombre de rôles ou la taille d'un des rôles, activez la case à cocher **Autoriser la mise à jour si la taille de rôle ou le nombre de rôles change** afin de permettre à la mise à jour de continuer.

	>[AZURE.WARNING]Notez que si vous modifiez la taille d'un rôle (c'est-à-dire la taille de la machine virtuelle qui héberge une instance de rôle) ou le nombre de rôles, l'image de chaque instance de rôle (machine virtuelle) doit être recréée et toutes les données locales sont perdues.

6. Si un des rôles de service ne comporte qu'une seule instance, activez la case à cocher **Update even if one or more role contain a single instance** afin de permettre à la mise à niveau de continuer.

	Azure ne peut garantir 99,95 % de disponibilité du service pendant la mise à jour du service cloud que si chaque rôle dispose d'au moins deux instances de rôle (machines virtuelles). Cela permet à une machine virtuelle de traiter les demandes du client pendant que l'autre est mise à jour.

8. Cliquez sur **OK** pour démarrer la mise à jour du service.



## Inversion de déploiements pour faire passer un déploiement intermédiaire en production

Utilisez **Swap** pour faire passer le déploiement intermédiaire d'un service cloud en production. Lorsque vous décidez de déployer une nouvelle version d'un service cloud, vous pouvez créer un déploiement intermédiaire et tester la nouvelle version dans l'environnement intermédiaire de votre service pendant que vos clients utilisent la version actuelle en production. Une fois que vous êtes prêt à faire passer la nouvelle version en production, vous pouvez utiliser **Swap** pour inverser les URL qui permettent d'accéder aux deux déploiements.

Vous pouvez inverser les déploiements à partir de la page **Cloud Services** ou du tableau de bord.

1. Dans le [portail Azure en version préliminaire][], sélectionnez le service cloud que vous souhaitez mettre à jour. Le panneau d'instance de service cloud s'ouvre.

2. Dans le panneau, cliquez sur le bouton **Swap**.

    ![Inverser les services cloud](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. L'invite de confirmation suivante s'affiche.

	![Inverser les services cloud](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Une fois les informations de déploiement vérifiées, cliquez sur **OK** pour inverser les déploiements.

	L'inversion des déploiements se fait rapidement, car la seule chose qui change est l'adresse IP virtuelle (VIP) des déploiements.

	Afin de réduire les coûts liés au calcul, vous pouvez supprimer le déploiement de l'environnement intermédiaire une fois que vous êtes certain que le nouveau déploiement de production se comporte comme vous le souhaitez.

## Liaison d’une ressource à un service cloud

Le portail Azure Preview ne lie pas les ressources comme le fait le portail Azure actuel. Vous devez déployer des ressources supplémentaires vers le même groupe de ressources que celui utilisé par le service cloud.

## Suppression de déploiements et d’un service cloud

Avant de pouvoir supprimer un service cloud, vous devez supprimer tous les déploiements existants.

Afin de réduire les coûts liés au calcul, vous pouvez supprimer le déploiement intermédiaire une fois que vous êtes certain que votre déploiement de production se comporte comme vous le souhaitez. Les coûts de calcul vous seront facturés pour les instances de rôle, même si le service cloud ne s'exécute pas.

Utiliser la procédure suivante pour supprimer un déploiement ou un service cloud.

1. Dans le [portail Azure en version préliminaire][], sélectionnez le service cloud que vous souhaitez supprimer. Le panneau d'instance de service cloud s'ouvre.

2. Dans le panneau, cliquez sur le bouton **Delete**.

    ![Inverser les services cloud](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Vous pouvez supprimer tout le service cloud en cochant **Cloud service and its deployments** ou choisir le **déploiement de production** ou le **déploiement intermédiaire**.

    ![Inverser les services cloud](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Cliquez sur le bouton **Delete** en bas de la page.

5. Pour supprimer le service cloud, cliquez sur **Delete cloud service**. Ensuite, à l'invite de confirmation, cliquez sur **Yes**.

> [AZURE.NOTE]Si la surveillance détaillée est configurée pour votre service cloud, Azure ne supprime pas les données de surveillance de votre compte de stockage lorsque vous supprimez le service cloud. Vous devez supprimer manuellement les données. Pour plus d'informations sur les tables de mesures, consultez [cet](cloud-services-how-to-monitor.md) article.

[portail Azure en version préliminaire]: https://portal.azure.com

<!---HONumber=July15_HO3-->
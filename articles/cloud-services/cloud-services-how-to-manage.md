<properties 
	pageTitle="Gestion d'un service cloud - Azure" 
	description="Découvrez comment gérer des services cloud dans le portail Azure." 
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

Dans la zone **Cloud Services** du portail Azure, vous pouvez mettre à jour un rôle de service ou un déploiement, promouvoir un déploiement intermédiaire en déploiement de production, lier des ressources à votre service cloud afin de voir les dépendances de ressources et d'étendre les ressources en même temps, mais aussi supprimer un service cloud ou un déploiement.


## Mise à jour d’un rôle ou d’un déploiement de service cloud

Si vous devez mettre à jour le code de l'application pour votre service cloud, utilisez **Update** dans le tableau de bord, la page **Cloud Services** ou la page **Instances**. Vous pouvez mettre à jour un ou plusieurs rôles. Vous devrez charger un nouveau package de service et un nouveau fichier de configuration de service.

1. Dans le [portail Azure](https://manage.windowsazure.com/), dans le tableau de bord, dans la page **Cloud Services** ou dans la page **Instances**, cliquez sur **Update**.

	![UpdateDeployment](./media/cloud-services-how-to-manage/CloudServices_UpdateDeployment.png)

2. Dans **Étiquette du déploiement**, entrez un nom pour identifier le déploiement (par exemple, mycloudservicev4). L'étiquette du déploiement se trouve sous **Démarrage rapide** sur le tableau de bord.

3. Dans **Package**, utilisez **Parcourir** pour charger le fichier de package du service (.cspkg).

4. Dans **Configuration**, utilisez **Parcourir** pour charger le fichier de configuration du service (.cscfg).

5. Dans **Role**, sélectionnez **All** si vous souhaitez mettre à niveau tous les rôles dans le service cloud. Pour effectuer la mise à jour d'un seul rôle, sélectionnez le rôle à mettre à jour. Même si vous sélectionnez un rôle en particulier à mettre à jour, les mises à jour du fichier de configuration de service sont appliquées à tous les rôles.

6. Si la mise à jour change le nombre de rôles ou la taille d'un des rôles, activez la case à cocher **Autoriser la mise à jour si la taille de rôle ou le nombre de rôles change** afin de permettre à la mise à jour de continuer.

	Notez que si vous modifiez la taille d'un rôle (c'est-à-dire la taille de la machine virtuelle qui héberge une instance de rôle) ou le nombre de rôles, l'image de chaque instance de rôle (machine virtuelle) doit être recréée et toutes les données locales sont perdues.

7. Si un des rôles de service ne comporte qu'une seule instance, activez la case à cocher **Update even if one or more role contain a single instance** afin de permettre à la mise à niveau de continuer.

	Azure ne peut garantir 99,95 % de disponibilité du service pendant la mise à jour du service cloud que si chaque rôle dispose d'au moins deux instances de rôle (machines virtuelles). Cela permet à une machine virtuelle de traiter les demandes du client pendant que l'autre est mise à jour.

8. Cliquez sur **OK** (coche) pour commencer la mise à jour du service.



## Inversion de déploiements pour faire passer un déploiement intermédiaire en production

Utilisez **Swap** pour faire passer le déploiement intermédiaire d'un service cloud en production. Lorsque vous décidez de déployer une nouvelle version d'un service cloud, vous pouvez créer un déploiement intermédiaire et tester la nouvelle version dans l'environnement intermédiaire de votre service pendant que vos clients utilisent la version actuelle en production. Une fois que vous êtes prêt à faire passer la nouvelle version en production, vous pouvez utiliser **Swap** pour inverser les URL qui permettent d'accéder aux deux déploiements.

Vous pouvez inverser les déploiements à partir de la page **Cloud Services** ou du tableau de bord.

1. Dans le [portail de gestion](https://manage.windowsazure.com/), cliquez sur **Cloud Services**.

2. Dans la liste des services cloud, cliquez sur le service cloud pour le sélectionner.

3. Cliquez sur **Swap**.

	L'invite de confirmation suivante s'affiche.

	![Inverser les services cloud](./media/cloud-services-how-to-manage/CloudServices_Swap.png)

4. Une fois les informations de déploiement vérifiées, cliquez sur **Yes** pour inverser les déploiements.

	L'inversion des déploiements se fait rapidement, car la seule chose qui change est l'adresse IP virtuelle (VIP) des déploiements.

	Afin de réduire les coûts liés au calcul, vous pouvez supprimer le déploiement de l'environnement intermédiaire une fois que vous êtes certain que le nouveau déploiement de production se comporte comme vous le souhaitez.

## Liaison d’une ressource à un service cloud

Pour voir les dépendances de votre service par rapport à d'autres ressources, vous pouvez lier une instance de base de données SQL ou un compte de stockage Azure au service cloud. Vous pouvez lier des ressources et annuler la liaison vers des ressources dans la page **Ressources liées**, puis surveiller leur utilisation dans le tableau de bord du service cloud. Si la surveillance du compte de stockage lié est activée, vous pouvez surveiller le nombre total de demandes dans le tableau de bord du service cloud.

Utilisez **Link** pour créer une liaison entre une instance de base de données SQL ou un compte de stockage, nouveau ou existant, et votre service cloud. Vous pouvez ensuite étendre la base de données en même temps que le service cloud qui utilise cette base de données sur la page **Scale**. (Le compte de stockage s'étend automatiquement avec l'augmentation de l'utilisation.) Pour plus d'informations, consultez la page [Extension d'un service cloud et des ressources liées](cloud-services-how-to-scale.md).

Vous pouvez également surveiller, gérer et étendre la base de données dans le nœud **Databases** du portail de gestion.

Dans ce sens, la liaison d'une ressource ne connecte pas votre application à la ressource. Si vous créez une nouvelle base de données à l'aide de **Link**, vous devez ajouter les chaînes de connexion au code de votre application, puis mettre à niveau le service cloud. Vous aurez également besoin d'ajouter des chaînes de connexion si votre application utilise des ressources dans un compte de stockage lié.

La procédure qui suit décrit comment lier une nouvelle instance de base de données SQL, déployée sur un nouveau serveur de base de données SQL, à un service cloud.

### Liaison d'une instance de base de données SQL à un service cloud

1. Dans le [portail de gestion](http://manage.windowsazure.com/), cliquez sur **Cloud Services**. Cliquez ensuite sur le nom du service cloud pour ouvrir le tableau de bord.

2. Cliquez sur **Linked Resources**.

	La page **Linked Resources** s'affiche.

	![LinkedResourcesPage](./media/cloud-services-how-to-manage/CloudServices_LinkedResourcesPage.png)

3. Cliquez sur **Link a Resource** ou sur **Link**.

	L'Assistant **Link Resource** démarre.

	![Link Page1](./media/cloud-services-how-to-manage/CloudServices_LinkedResources_LinkPage1.png)

4. Cliquez sur **Create a new resource** ou **Link an existing resource**.

5. Choisissez le type de ressource à lier. Dans le [portail de gestion](http://manage.windowsazure.com/), cliquez sur **SQL Database**. (L'aperçu du portail de gestion ne prend pas en charge la liaison d'un compte de stockage à un service cloud.)

6. Afin de terminer la configuration de la base de données, suivez les instructions de l'aide dans la zone **SQL Databases** du portail de gestion.

	Vous pouvez suivre l'état de l'opération de liaison dans la zone de messages.

	![Progression de la liaison](./media/cloud-services-how-to-manage/CloudServices_LinkedResources_LinkProgress.png)

	Une fois la liaison terminée, vous pouvez surveiller l'état de la ressource liée dans le tableau de bord du service cloud. Pour plus d'informations sur l'extension d'une base de données SQL liée, consultez la page [Extension d'un service cloud et des ressources liées](cloud-services-how-to-scale.md).

### Pour annuler la liaison d'une ressource liée

1. Dans le [portail de gestion](http://manage.windowsazure.com/), cliquez sur **Cloud Services**. Cliquez ensuite sur le nom du service cloud pour ouvrir le tableau de bord.

2. Cliquez sur **Linked Resources**, puis sélectionnez la ressource.

3. Cliquez sur **Unlink**. Cliquez ensuite sur **Yes** dans l'invite de confirmation.

	L'annulation de la liaison d'une base de données SQL n'a pas d'effet sur la base de données ou sur les connexions de l'application à la base de données. Vous pouvez gérer la base de données dans la zone **SQL Databases** du portail de gestion.



## Suppression de déploiements et d’un service cloud

Avant de pouvoir supprimer un service cloud, vous devez supprimer tous les déploiements existants.

Afin de réduire les coûts liés au calcul, vous pouvez supprimer le déploiement intermédiaire une fois que vous êtes certain que votre déploiement de production se comporte comme vous le souhaitez. Les coûts de calcul vous seront facturés pour les instances de rôle, même si le service cloud ne s'exécute pas.

Utiliser la procédure suivante pour supprimer un déploiement ou un service cloud.

1. Dans le [portail de gestion](http://manage.windowsazure.com/), cliquez sur **Cloud Services**.

2. Sélectionnez le service cloud, puis cliquez sur **Delete**. (Pour sélectionner un service cloud sans ouvrir le tableau de bord, cliquez n'importe où, sauf sur le nom dans l'entrée du service cloud.)

	Si vous avez un déploiement intermédiaire ou un déploiement de production, un menu similaire au menu suivant s'affiche en bas de la fenêtre. Avant de supprimer le service cloud, vous devez supprimer tous les déploiements existants.

	![Menu Delete](./media/cloud-services-how-to-manage/CloudServices_DeleteMenu.png)


3. Pour supprimer un déploiement, cliquez sur **Delete production deployment** ou sur **Delete staging deployment**. Ensuite, à l'invite de confirmation, cliquez sur **Yes**.

4. Si vous prévoyez de supprimer le service cloud, répétez l'étape 3 si besoin pour supprimer votre autre déploiement.

5. Pour supprimer le service cloud, cliquez sur **Delete cloud service**. Ensuite, à l'invite de confirmation, cliquez sur **Yes**.

> [AZURE.NOTE]
Si la surveillance détaillée est configurée pour votre service cloud, Azure ne supprime pas les données de surveillance de votre compte de stockage lorsque vous supprimez le service cloud. Vous devez supprimer manuellement les données. Pour savoir où trouver les tables de mesures, consultez la rubrique « Accès aux données de la surveillance détaillée en dehors du portail de gestion » sur la page <a href="http://azure.microsoft.com/documentation/articles/cloud-services-how-to-monitor/">Surveillance des services cloud</a>.

 

<!----HONumber=July15_HO5-->
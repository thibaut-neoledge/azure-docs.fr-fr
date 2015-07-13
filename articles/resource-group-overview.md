<properties
   pageTitle="Présentation d’Azure Resource Manager"
   description="Explique comment utiliser Azure Resource Manager pour les tâches de déploiement, de gestion et de contrôle d’accès des ressources sur Azure."
   services="multiple"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/22/2015"
   ms.author="tomfitz"/>

# Présentation d’Azure Resource Manager

Les applications sont généralement constituées de nombreux composants, par exemple application web, base de données, serveur de base de données, stockage et services tiers. Vous ne voyez pas ces composants comme des entités distinctes, mais plutôt comme des parties associées et interdépendantes d’une seule et même entité. Vous avez alors besoin de regrouper le déploiement, la gestion et la surveillance de ces différentes parties. Azure Resource Manager vous permet de manipuler les ressources de votre application sous la forme d’un groupe. Vous pouvez déployer, mettre à jour ou supprimer toutes les ressources de votre application dans le cadre d’une opération unique et coordonnée. Vous utilisez un modèle de déploiement pouvant fonctionner avec différents environnements (environnements de test, intermédiaire et de production). Vous pouvez clarifier la facturation pour votre organisation en visualisant les coûts cumulés pour l’ensemble du groupe.

Azure Resource Manager intègre en mode natif un contrôle d’accès à la plate-forme de gestion pour vous permettre de spécifier les actions qu’un utilisateur de votre organisation est autorisé à effectuer sur un groupe de ressources.

> [AZURE.NOTE]Cette rubrique décrit les ressources, les groupes et les modèles en utilisant le portail en version préliminaire pour illustrer ces concepts. Toutefois, vous pouvez également créer, gérer et supprimer des ressources Azure à l’aide de l’[interface de ligne de commande Azure pour Mac, Linux et Windows](virtual-machines/xplat-cli-azure-resource-manager.md), ainsi qu’avec [PowerShell](powershell-azure-resource-manager.md).

## Groupes de ressources

Un groupe de ressources est un conteneur réunissant les ressources associées d’une application. Le groupe de ressources peut inclure toutes les ressources d’une application, ou uniquement celles qui sont regroupées de façon logique. Pour déterminer comment allouer des ressources aux groupes de ressources, choisissez l’approche la plus pertinente pour votre organisation.

Lorsque vous définissez votre groupe de ressources, vous devez prendre en compte certains facteurs importants :

1. Toutes les ressources de votre groupe doivent partager le même cycle de vie. Les opérations de déploiement, de mise à jour et de suppression porteront sur toutes les ressources du groupe. Si l’une des ressources, comme un serveur de base de données, doit exister dans un autre cycle de déploiement, elle doit appartenir à un autre groupe de ressources.
2. Chaque ressource ne peut exister que dans un seul groupe de ressources.
3. Vous pouvez à tout moment ajouter ou supprimer une ressource au niveau d’un groupe de ressources.
4. Un groupe de ressources peut contenir des ressources figurant dans différentes régions.
5. Un groupe de ressources peut être utilisé pour définir l’étendue du contrôle d’accès des actions administratives.

Dans le portail Azure en version préliminaire, toutes les nouvelles ressources sont créées dans un groupe de ressources. Même si vous ne créez qu’une seule ressource comme un site web, vous devez décider s’il convient de l’ajouter à un groupe existant ou de créer un autre groupe pour cette ressource.

L’image ci-après illustre un groupe de ressources avec un site web, une base de données et Application Insights.

![résumé d’un groupe de ressources](./media/resource-group-overview/resourcegroupsummary.png)

Un groupe de ressources peut également être lié à une ressource d’un autre groupe de ressources. Une ressource est considérée comme liée lorsqu’il existe une dépendance de déploiement entre les ressources de différents groupes de ressources. Par exemple, si une application web d’un groupe de ressources se connecte à une base de données d’un autre groupe de ressources, ces ressources sont liées.

![ressources liées](./media/resource-group-overview/linkedresource.png)

Le portail en version préliminaire vous permet de visualiser les coûts, de surveiller les événements et de gérer les alertes avec facilité. L’image ci-après illustre la facturation consolidée relative à un groupe.

![facturation](./media/resource-group-overview/billing.png)

## Déploiement de modèle

Vous pouvez utiliser Azure Resource Manager pour créer un modèle simple (au format JSON) définissant le déploiement et la configuration de votre application. Ce modèle est connu sous le nom de modèle Azure et permet de définir le déploiement de façon déclarative. En utilisant un modèle, vous pouvez déployer votre application à plusieurs reprises tout au long du cycle de vie de l’application et avoir ainsi l’assurance que vos ressources présentent un état cohérent lors de leur déploiement.

Dans ce modèle, vous définissez l’infrastructure de votre application, le mode de configuration de cette infrastructure et la méthode de publication du code de votre application dans cette infrastructure. Vous n’avez pas à vous soucier de l’ordre du déploiement, car Azure Resource Manager analyse les dépendances pour vérifier que les ressources sont créées dans l’ordre approprié.

Vous pouvez également utiliser le modèle pour les mises à jour de l’infrastructure. Par exemple, vous pouvez ajouter une nouvelle ressource à votre application et ajouter des règles de configuration pour les ressources qui sont déjà déployées. Si le modèle spécifie la création d’une ressource, mais que cette ressource existe déjà, Azure Resource Manager effectue une mise à jour au lieu de créer un autre actif. Azure Resource Manager met à jour l’actif existant vers l’état qu’il présenterait s’il s’agissait d’une nouvelle ressource.

Vous pouvez spécifier des paramètres dans votre modèle pour assurer la personnalisation et la flexibilité du déploiement. Par exemple, vous pouvez transmettre des valeurs de paramètre qui adaptent le déploiement à votre environnement de test. La spécification de paramètres vous permet d’utiliser le même modèle pour effectuer le déploiement sur tous les environnements de votre application.

Vous pouvez exécuter l’intégralité de la procédure de déploiement et de configuration par le biais du modèle, de sorte qu’il ne vous reste plus aucune étape à exécuter manuellement. Azure Resource Manager fournit des extensions pour les cas qui nécessitent des opérations supplémentaires, comme l’installation d’un logiciel spécifique non inclus dans la configuration. Si vous utilisez déjà un service de gestion de configuration, comme DSC, Chef ou Puppet, vous pouvez continuer à travailler avec ce service en utilisant des extensions.

Lorsque vous créez une solution à partir de Marketplace, cette solution inclut automatiquement un modèle de déploiement. Vous n’êtes pas contraint de créer votre modèle à partir de zéro, car vous pouvez partir du modèle de votre solution et le personnaliser en fonction de vos besoins spécifiques.

Pour finir, le modèle devient partie intégrante du code source de votre application. Vous pouvez l’archiver dans votre référentiel de code source et le mettre à jour à mesure que votre application évolue. Le modèle est modifiable par le biais de Visual Studio.

Pour plus d’informations sur la définition du modèle, voir [Création de modèles Azure Resource Manager](./resource-group-authoring-templates.md).

Pour plus d’informations sur l’utilisation d’un modèle pour le déploiement, voir [Déployer une application avec un modèle Azure Resource Manager](azure-portal/resource-group-template-deploy.md) et [Déployer une application complexe de manière prévisible dans Microsoft Azure](app-service-web/app-service-deploy-complex-application-predictably.md).

## Balises

Azure Resource Manager fournit une fonctionnalité de balisage vous permettant de catégoriser les ressources en fonction de vos exigences de gestion ou de facturation. Vous pouvez utiliser des balises lorsque vous disposez d’un ensemble complexe de groupes de ressources et de ressources et que vous souhaitez visualiser ces actifs de la façon qui vous semble la plus logique. Par exemple, vous pouvez baliser des ressources qui jouent un rôle similaire dans votre organisation ou qui appartiennent au même département.

Pour commencer à utiliser des balises, cliquez sur l’icône Balises du portail en version préliminaire.

![tags](./media/resource-group-overview/tags.png)

Les ressources ne doivent pas nécessairement appartenir au même groupe de ressources pour partager une balise. Vous pouvez créer votre propre taxonomie de balise pour vous assurer que tous les utilisateurs de votre organisation utiliseront des balises communes plutôt que d’appliquer par inadvertance des balises légèrement différentes (telles que « dépt » au lieu de « département »).

Pour plus d’informations sur les balises, voir [Organisation des ressources Azure à l’aide de balises](./resource-group-using-tags.md).

## Contrôle d’accès

Azure Resource Manager vous permet de déterminer les utilisateurs qui sont autorisés à exécuter des actions spécifiques pour votre organisation. Il intègre en mode natif à la plate-forme de gestion le protocole OAuth et le contrôle d’accès en fonction du rôle (RBAC), et applique ce contrôle d’accès à tous les services de votre groupe de ressources. Vous pouvez ajouter des utilisateurs à des rôles prédéfinis de plateforme ou propres aux ressources et appliquer ces rôles à un abonnement, un groupe de ressources ou une ressource pour limiter l’accès. Par exemple, vous pouvez tirer parti du rôle prédéfini Collaborateur de base de données SQL qui permet aux utilisateurs de gérer les bases de données, mais non les serveurs de base de données ni les stratégies de sécurité. Vous pouvez attribuer le rôle Collaborateur de base de données SQL aux utilisateurs de votre organisation qui ont besoin de ce type d’accès, puis appliquer ce rôle à l’abonnement, au groupe de ressources ou à la ressource.

Pour définir le contrôle d’accès, cliquez sur le bouton d’accès du portail en version préliminaire.

![contrôle d’accès des utilisateurs](./media/resource-group-overview/access.png)

Azure Resource Manager enregistre automatiquement les actions des utilisateurs à des fins d’audit.

Vous pouvez également verrouiller explicitement les ressources essentielles afin d’empêcher les utilisateurs de les supprimer ou de les modifier.

Pour plus d’informations sur le contrôle d’accès en fonction du rôle, voir [Contrôle d’accès en fonction du rôle dans la version préliminaire du portail Azure](./role-based-access-control-configure.md).

Pour découvrir des exemples de définition de stratégies d’accès, voir [Gestion et audit d’accès aux ressources](azure-portal/resource-group-rbac.md).

## Couche de gestion cohérente

Azure Resource Manager fournit des opérations entièrement compatibles par le biais d’Azure PowerShell, de la ligne de commande Azure pour Mac, Linux et Windows, du portail Azure ou de l’API REST. Vous pouvez utiliser l’interface qui vous convient le mieux et passer rapidement d’une interface à l’autre sans risque de confusion. Le portail affiche même des notifications concernant les actions effectuées en dehors du portail.

Pour plus d’informations sur PowerShell, voir [Utilisation d’Azure PowerShell avec Azure Resource Manager](./powershell-azure-resource-manager.md) et [Cmdlets Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn757692.aspx).

Pour plus d’informations sur l’interface de ligne de commande Azure, voir [Utilisation de l’interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Management](./virtual-machines/xplat-cli-azure-resource-manager.md).

Pour plus d’informations sur l’API REST, voir [Référence sur l’API REST du gestionnaire des ressources Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx).

## Étapes suivantes
Prise en main

- [Utilisation d’Azure PowerShell avec Azure Resource Manager](./powershell-azure-resource-manager.md)
- [Utilisation de l’interface de ligne de commande Azure avec Azure Resource Manager](./virtual-machines/xplat-cli-azure-resource-manager.md)
- [Utilisation du portail Azure en version préliminaire pour gérer les ressources Azure](azure-portal/resource-group-portal.md)

Création et déploiement d’applications

- [Création de modèles Azure Resource Manager](./resource-group-authoring-templates.md)
- [Déploiement d’une application à l’aide d’un modèle Azure Resource Manager](azure-portal/resource-group-template-deploy.md)
- [Résolution des problèmes liés aux déploiements de groupes de ressources dans Azure](virtual-machines/resource-group-deploy-debug.md)
- [Déployer une application complexe de manière prévisible dans Microsoft Azure](app-service-web/app-service-deploy-complex-application-predictably.md)
- [Déploiement de ressources Azure à l’aide de bibliothèques .NET et d’un modèle](virtual-machines/arm-template-deployment.md)
- [Fonctions des modèles de gestionnaire des ressources Azure](./resource-group-template-functions.md)
- [Opérations de modèle avancées](./resource-group-advanced-template.md)

Organisation des ressources

- [Organisation des ressources Azure à l’aide de balises](./resource-group-using-tags.md)

Gestion et audit de l’accès

- [Gestion et audit d’accès aux ressources](azure-portal/resource-group-rbac.md)
- [Contrôle d’accès en fonction du rôle dans la version préliminaire du portail Azure](./role-based-access-control-configure.md)
- [Authentification d’un principal du service à l’aide d’Azure Resource Manager](./resource-group-authenticate-service-principal.md)
- [Création d’un nouveau principal du service Azure à l’aide du portail Azure](./resource-group-create-service-principal-portal.md)

<!---HONumber=July15_HO1-->
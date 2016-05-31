<properties
   pageTitle="Présentation d’Azure Resource Manager | Microsoft Azure"
   description="Explique comment utiliser Azure Resource Manager pour les tâches de déploiement, de gestion et de contrôle d’accès des ressources sur Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/20/2016"
   ms.author="tomfitz"/>

# Présentation d’Azure Resource Manager

L’infrastructure de votre application est généralement constituée de plusieurs composants (peut-être une machine virtuelle, un compte de stockage et un réseau virtuel ou une application web, une base de données, un serveur de base de données et 3 services de tiers. Vous ne voyez pas ces composants comme des entités distinctes, mais plutôt comme des parties associées et interdépendantes d’une seule et même entité. Vous avez alors besoin de regrouper le déploiement, la gestion et la surveillance de ces différentes parties. Azure Resource Manager vous permet de travailler avec les ressources de solution sous forme de groupe. Vous pouvez déployer, mettre à jour ou supprimer toutes les ressources de votre solution dans le cadre d’une opération unique et coordonnée. Vous utilisez un modèle de déploiement pouvant fonctionner avec différents environnements (environnements de test, intermédiaire et de production). Le gestionnaire de ressources assure la sécurité, les fonctions d’audit et de balisage pour vous aider à gérer vos ressources après le déploiement.

## Avantages de l’utilisation de Resource Manager

Resource Manager offre plusieurs avantages :

- Vous pouvez déployer, gérer et surveiller toutes les ressources de votre solution comme un groupe, plutôt que de gérer ces ressources individuellement.
- Vous pouvez déployer votre solution à plusieurs reprises tout au long du cycle de vie de développement et avoir ainsi l’assurance que vos ressources présentent un état cohérent lors de leur déploiement.
- Vous pouvez utiliser des modèles déclaratifs pour définir votre déploiement.
- Vous pouvez définir les dépendances entre les ressources afin de les déployer dans le bon ordre.
- Vous pouvez appliquer le contrôle d’accès à tous les services dans votre groupe de ressources, car le contrôle d’accès en fonction du rôle (RBAC) est intégré en mode natif à la plateforme de gestion.
- Vous pouvez appliquer des balises aux ressources pour organiser logiquement toutes les ressources dans votre abonnement.
- Vous pouvez clarifier la facturation pour votre organisation en visualisant les coûts cumulés pour l’ensemble du groupe ou pour un groupe de ressources partageant la même balise.  

Resource Manager propose une nouvelle façon de déployer et de gérer vos solutions. Si vous avez utilisé un modèle de déploiement antérieur et souhaitez obtenir des informations sur les modifications, consultez [Présentation du déploiement de Resource Manager et du déploiement classique](resource-manager-deployment-model.md).

## Assistance

Les suggestions suivantes vous aideront à tirer le meilleur parti de Resource Manager lorsque vous travaillez avec vos solutions.

1. Définissez et déployez votre infrastructure via la syntaxe déclarative dans les modèles du Resource Manager, et non via des commandes impératives.
2. Définissez toutes les étapes de déploiement et de configuration dans le modèle. Aucune étape manuelle ne devrait intervenir dans la configuration de votre solution.
3. Exécutez des commandes impératives pour gérer vos ressources, par exemple démarrer ou arrêter une application ou une machine.
4. Organisez des ressources avec le même cycle de vie dans un groupe de ressources. Utilisez des balises pour toute organisation des ressources.

## Groupes de ressources

Un groupe de ressources est un conteneur réunissant les ressources associées d’une application. Le groupe de ressources peut inclure toutes les ressources d’une application, ou uniquement celles qui sont regroupées de façon logique. Pour déterminer comment allouer des ressources aux groupes de ressources, choisissez l’approche la plus pertinente pour votre organisation.

Lorsque vous définissez votre groupe de ressources, vous devez prendre en compte certains facteurs importants :

1. Toutes les ressources de votre groupe doivent partager le même cycle de vie. Les opérations de déploiement, de mise à jour et de suppression porteront sur toutes les ressources du groupe. Si l’une des ressources, comme un serveur de base de données, doit exister dans un autre cycle de déploiement, elle doit appartenir à un autre groupe de ressources.
2. Chaque ressource ne peut exister que dans un seul groupe de ressources.
3. Vous pouvez à tout moment ajouter ou supprimer une ressource au niveau d’un groupe de ressources.
4. Vous pouvez déplacer une ressource d’un groupe de ressources vers un autre groupe. Pour plus d’informations, consultez la page [Déplacement de ressources vers un nouveau groupe de ressources ou un abonnement](resource-group-move-resources.md).
4. Un groupe de ressources peut contenir des ressources figurant dans différentes régions.
5. Un groupe de ressources peut être utilisé pour définir l’étendue du contrôle d’accès des actions administratives.
6. Une ressource peut être liée à la ressource d’un autre groupe de ressources lorsqu’elles interagissent entre elles, mais elles ne partagent pas le même cycle de vie (par exemple, plusieurs applications se connectant à une base de données). Pour en savoir plus, consultez [Associer des ressources avec Azure Resource Manager](resource-group-link-resources.md).

## Fournisseurs de ressources

Un fournisseur de ressources est un service qui fournit les ressources que vous pouvez déployer et gérer via Resource Manager. Chaque fournisseur de ressources offre des opérations d’API REST pour travailler avec les ressources. Par exemple, si vous voulez déployer un coffre de clés Azure pour stocker des clés et des secrets, vous allez travailler avec le fournisseur de ressources **Microsoft.KeyVault** . Ce fournisseur de ressources fournit un type de ressource appelé **vaults** pour la création du coffre de clés et un type de ressource appelé **vaults/secrets** pour la création d’un secret dans le coffre de clés. Pour en savoir plus sur un fournisseur de ressources, consultez ses opérations d’API REST, telles que les [opérations d’API REST de coffre de clés](https://msdn.microsoft.com/library/azure/dn903609.aspx).

Pour déployer et gérer votre infrastructure, vous devez connaître des détails sur les fournisseurs de ressources, comme les types de ressources qu’il offre, les numéros de version des opérations d’API REST, les opérations qu’il prend en charge et le schéma à utiliser quand vous définissez les valeurs du type de ressource à créer. Pour en savoir plus sur les fournisseurs de ressources pris en charge, consultez [Fournisseurs, régions, versions d’API et schémas de Resource Manager](resource-manager-supported-services.md).

## Déploiement de modèle

Avec Resource Manager, vous pouvez créer un modèle simple (au format JSON) définissant le déploiement et la configuration de votre application. Ce modèle est connu sous le nom de modèle Resource Manager et permet de définir le déploiement de façon déclarative. En utilisant un modèle, vous pouvez déployer votre application à plusieurs reprises tout au long du cycle de vie de l’application et avoir ainsi l’assurance que vos ressources présentent un état cohérent lors de leur déploiement.

Dans ce modèle, vous définissez l’infrastructure de votre application, le mode de configuration de cette infrastructure et la méthode de publication du code de votre application dans cette infrastructure. Vous n’avez pas à vous soucier de l’ordre du déploiement, car Azure Resource Manager analyse les dépendances pour vérifier que les ressources sont créées dans l’ordre approprié. Pour plus d’informations, consultez [Définition de dépendances dans des modèles Azure Resource Manager](resource-group-define-dependencies.md).

Lorsque vous créez une solution à partir de Marketplace, cette solution inclut automatiquement un modèle de déploiement. Vous n’êtes pas contraint de créer votre modèle à partir de zéro, car vous pouvez partir du modèle de votre solution et le personnaliser en fonction de vos besoins spécifiques. Vous pouvez récupérer un modèle pour un groupe de ressources existant en exportant l'état actuel du groupe de ressources vers un modèle, ou en affichant le modèle qui avait été utilisé pour un déploiement particulier. L’affichage du modèle exporté est un moyen utile pour en découvrir plus sur sa syntaxe. Pour en savoir plus sur l’utilisation des modèles exportés, voir [Exporter un modèle Azure Resource Manager à partir de ressources existantes](resource-manager-export-template.md).

Il est inutile de définir toute votre infrastructure dans un seul modèle. Il peut être judicieux de diviser les exigences de votre déploiement dans un ensemble de modèles ciblés destinés à un usage particulier. Vous pouvez facilement réutiliser ces modèles pour différentes solutions. Pour déployer une solution particulière, créez un modèle de référence qui relie tous les modèles requis. Pour plus d’informations, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).

Vous pouvez également utiliser le modèle pour les mises à jour de l’infrastructure. Par exemple, vous pouvez ajouter une nouvelle ressource à votre application et ajouter des règles de configuration pour les ressources qui sont déjà déployées. Si le modèle spécifie la création d’une ressource, mais que cette ressource existe déjà, Azure Resource Manager effectue une mise à jour au lieu de créer un autre actif. Azure Resource Manager met à jour l’actif existant vers l’état qu’il présenterait s’il s’agissait d’une nouvelle ressource. Vous pouvez également faire en sorte que Resource manager supprime les ressources non spécifiées dans le modèle. Pour connaître les différentes options lors du déploiement, consultez [Déployer une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).

Vous pouvez spécifier des paramètres dans votre modèle pour assurer la personnalisation et la flexibilité du déploiement. Par exemple, vous pouvez transmettre des valeurs de paramètre qui adaptent le déploiement à votre environnement de test. La spécification de paramètres vous permet d’utiliser le même modèle pour effectuer le déploiement sur tous les environnements de votre application.

Resource Manager fournit des extensions pour les cas qui nécessitent des opérations supplémentaires, comme l’installation d’un logiciel spécifique non inclus dans la configuration. Si vous utilisez déjà un service de gestion de configuration, comme DSC, Chef ou Puppet, vous pouvez continuer à travailler avec ce service en utilisant des extensions.

Pour finir, le modèle devient partie intégrante du code source de votre application. Vous pouvez l’archiver dans votre référentiel de code source et le mettre à jour à mesure que votre application évolue. Le modèle est modifiable par le biais de Visual Studio.

Pour plus d’informations sur la définition du modèle, voir [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).

Pour suivre la procédure pas à pas de création d’un modèle, consultez [Guide de création d’un modèle Resource Manager](resource-manager-template-walkthrough.md).

Pour obtenir des instructions sur le déploiement de votre solution dans différents environnements, consultez [Environnements de développement et de test dans Microsoft Azure](solution-dev-test-environments.md).

## Balises

Resource Manager fournit une fonctionnalité de balisage vous permettant de catégoriser les ressources en fonction de vos exigences de gestion ou de facturation. Vous pouvez utiliser des balises lorsque vous disposez d’un ensemble complexe de groupes de ressources et de ressources et que vous souhaitez visualiser ces actifs de la façon qui vous semble la plus logique. Par exemple, vous pouvez baliser des ressources qui jouent un rôle similaire dans votre organisation ou qui appartiennent au même département. Sans balises, les utilisateurs de votre organisation peuvent créer plusieurs ressources qui peuvent s’avérer difficiles à identifier et à gérer plus tard. Par exemple, si vous souhaitez supprimer toutes les ressources d’un projet particulier, mais qu’elles n’ont pas été marquées pour celui-ci, vous devrez les rechercher manuellement. Le balisage peut constituer un moyen important de réduire les coûts inutiles dans votre abonnement.

Les ressources ne doivent pas nécessairement appartenir au même groupe de ressources pour partager une balise. Vous pouvez créer votre propre taxonomie de balise pour vous assurer que tous les utilisateurs de votre organisation utiliseront des balises communes plutôt que d’appliquer par inadvertance des balises légèrement différentes (telles que « dépt » au lieu de « département »).

Pour plus d’informations sur les balises, voir [Organisation des ressources Azure à l’aide de balises](resource-group-using-tags.md). Vous pouvez créer une [stratégie personnalisée](#manage-resources-with-customized-policies) qui requiert l’ajout de balises aux ressources pendant le déploiement.

## Contrôle d’accès

Resource Manager vous permet de déterminer les utilisateurs qui sont autorisés à exécuter des actions spécifiques pour votre organisation. Il intègre en mode natif à la plate-forme de gestion le protocole OAuth et le contrôle d’accès en fonction du rôle (RBAC), et applique ce contrôle d’accès à tous les services de votre groupe de ressources. Vous pouvez ajouter des utilisateurs à des rôles prédéfinis de plateforme ou propres aux ressources et appliquer ces rôles à un abonnement, un groupe de ressources ou une ressource pour limiter l’accès. Par exemple, vous pouvez tirer parti du rôle prédéfini Collaborateur de base de données SQL qui permet aux utilisateurs de gérer les bases de données, mais non les serveurs de base de données ni les stratégies de sécurité. Vous pouvez attribuer le rôle Collaborateur de base de données SQL aux utilisateurs de votre organisation qui ont besoin de ce type d’accès, puis appliquer ce rôle à l’abonnement, au groupe de ressources ou à la ressource.

Resource Manager enregistre automatiquement les actions des utilisateurs à des fins d’audit. Pour plus d’informations sur l’utilisation des journaux d’audit, consultez [Opérations d’audit avec Resource Manager](resource-group-audit.md).

Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez [Contrôle d’accès en fonction du rôle d’Azure](./active-directory/role-based-access-control-configure.md). La rubrique [RBAC : rôles intégrés](./active-directory/role-based-access-built-in-roles.md) contient une liste des rôles intégrés et des actions autorisées. Les rôles intégrés incluent les rôles généraux tels que ceux de Propriétaire, Lecteur et Collaborateur, ainsi que les rôles spécifiques pour le service, par exemple, collaborateur de machine virtuelle, collaborateur de réseau virtuel et Gestionnaire de sécurité SQL (pour ne nommer que quelques-uns des rôles disponibles).

Vous pouvez également verrouiller explicitement les ressources essentielles afin d’empêcher les utilisateurs de les supprimer ou de les modifier. Pour plus d’informations, consultez [Verrouiller des ressources avec Azure Resource Manager](resource-group-lock-resources.md).

Pour connaître les meilleures pratiques, consultez [Questions de sécurité relatives à Azure Resource Manager](best-practices-resource-manager-security.md)

## Gérer les ressources à l’aide de stratégies personnalisées

Resource Manager vous permet de créer des stratégies personnalisées pour gérer vos ressources. Les types de stratégie que vous créez peuvent comporter des scénarios très variés : application d’une convention de dénomination à des ressources, limitation des types et des instances de ressource pouvant être déployés, limitation des régions pouvant héberger un type de ressource ou demande d’une valeur de balise sur des ressources pour trier la facturation par service. Vous créez des stratégies pour vous aider à réduire les coûts et à assurer la cohérence de votre abonnement. Pour plus d’informations, consultez [Utiliser le service Policy pour gérer les ressources et contrôler l’accès](resource-manager-policy.md).

## Couche de gestion cohérente

Resource Manager fournit des opérations entièrement compatibles par le biais d’Azure PowerShell, de la ligne de commande Azure pour Mac, Linux et Windows, du portail Azure ou de l’API REST. Vous pouvez utiliser l’interface qui vous convient le mieux et passer rapidement d’une interface à l’autre sans risque de confusion. Le portail affiche même des notifications concernant les actions effectuées en dehors du portail.

Pour plus d’informations sur PowerShell, voir [Utilisation d’Azure PowerShell avec Azure Resource Manager](powershell-azure-resource-manager.md) et [Cmdlets Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn757692.aspx).

Pour plus d’informations sur l’interface de ligne de commande Azure, voir [Utilisation de l’interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Management](xplat-cli-azure-resource-manager.md).

Pour plus d’informations sur l’API REST, voir [Référence sur l’API REST du gestionnaire des ressources Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx). Pour afficher les opérations REST pour vos ressources déployées, consultez la rubrique [Affichage et modification des ressources à l’aide de l’Explorateur de ressources Azure](resource-manager-resource-explorer.md).

Pour plus d’informations sur l’utilisation du portail, consultez [Utilisation du portail Azure pour gérer vos ressources Azure](./azure-portal/resource-group-portal.md).

Azure Resource Manager prend en charge le partage des ressources cross-origin (CORS). CORS permet d’appeler l’API REST Resource Manager ou une API REST du service Azure à partir d’une application web qui se trouve dans un domaine différent. Sans la prise en charge de CORS, le navigateur web empêcherait une application d’un domaine d’accéder aux ressources d’un autre domaine. Resource Manager active CORS pour toutes les demandes associées à des informations d’authentification valides.

## Étapes suivantes

- Pour en savoir plus sur la création de modèles, consultez [Création de modèles](resource-group-authoring-templates.md)
- Pour déployer le modèle créé, consultez [Déploiement de modèles](resource-group-template-deploy.md).
- Pour comprendre les fonctions que vous pouvez utiliser dans un modèle, consultez [Fonctions de modèle](resource-group-template-functions.md).
- Pour obtenir des instructions sur la conception de vos modèles, consultez [Meilleures pratiques relatives à la conception des modèles Azure Resource Manager](best-practices-resource-manager-design-templates.md).

Voici une vidéo de cette présentation :

[AZURE.VIDEO azure-resource-manager-overview]

<!---HONumber=AcomDC_0525_2016-->
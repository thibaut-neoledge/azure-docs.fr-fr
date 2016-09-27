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
   ms.date="09/16/2016"
   ms.author="tomfitz"/>

# Présentation d’Azure Resource Manager

L’infrastructure de votre application est généralement constituée de plusieurs composants (peut-être une machine virtuelle, un compte de stockage et un réseau virtuel ou une application web, une base de données, un serveur de base de données et 3 services de tiers). Vous ne voyez pas ces composants comme des entités distinctes, mais plutôt comme des parties associées et interdépendantes d’une seule et même entité. Vous avez alors besoin de regrouper le déploiement, la gestion et la surveillance de ces différentes parties. Azure Resource Manager vous permet de travailler avec les ressources de solution sous forme de groupe. Vous pouvez déployer, mettre à jour ou supprimer toutes les ressources de votre solution dans le cadre d’une opération unique et coordonnée. Vous utilisez un modèle de déploiement pouvant fonctionner avec différents environnements (environnements de test, intermédiaire et de production). Le gestionnaire de ressources assure la sécurité, les fonctions d’audit et de balisage pour vous aider à gérer vos ressources après le déploiement.

## Terminologie

Si vous êtes un nouvel utilisateur d’Azure Resource Manager, vous pouvez ne pas connaître certains termes.

- **ressource** : élément gérable disponible dans Azure. Les ressources telles que les machines virtuelles, les comptes de stockage, les applications web, les bases de données et les réseaux virtuels sont courantes, mais il en existe beaucoup d’autres.
- **groupe de ressources** : conteneur réunissant les ressources associées d’une solution Azure. Le groupe de ressources peut inclure toutes les ressources de la solution, ou uniquement celles que vous souhaitez gérer en tant que groupe. Pour déterminer comment allouer des ressources aux groupes de ressources, choisissez l’approche la plus pertinente pour votre organisation. Voir [Groupes de ressources](#resource-groups).
- **fournisseur de ressources** : un service qui fournit les ressources que vous pouvez déployer et gérer via Resource Manager. Chaque fournisseur de ressources propose des opérations pour travailler avec les ressources déployées. Parmi les fournisseurs de ressources courants figurent Microsoft.Compute, qui fournit la ressource de machine virtuelle ; Microsoft.Storage, qui fournit la ressource du compte de stockage ; et Microsoft.Web, qui fournit des ressources liées aux applications web. Voir [Fournisseurs de ressources](#resource-providers).
- **Modèle Resource Manager** : fichier JSON (JavaScript Objet Notation) qui définit une ou plusieurs ressources à déployer vers un groupe de ressources. Il définit également les dépendances entre les ressources déployées. Le modèle peut être utilisé pour déployer les ressources de manière cohérente et répétée. Voir [Déploiement de modèle](#template-deployment).
- **syntaxe déclarative** : syntaxe qui vous permet de déclarer « Voici ce que je souhaite créer » sans avoir à écrire la séquence de commandes de programmation pour le créer. Le modèle Resource Manager est un exemple de syntaxe déclarative. Dans le fichier, vous définissez les propriétés afin de déployer l’infrastructure vers Azure.

## Avantages de l’utilisation de Resource Manager

Resource Manager offre plusieurs avantages :

- Vous pouvez déployer, gérer et surveiller toutes les ressources de votre solution comme un groupe, plutôt que de les gérer individuellement.
- Vous pouvez déployer votre solution à plusieurs reprises tout au long du cycle de vie de développement et avoir ainsi l’assurance que vos ressources présentent un état cohérent lors de leur déploiement.
- Vous pouvez gérer votre infrastructure à l’aide de modèles déclaratifs plutôt que de scripts.
- Vous pouvez définir les dépendances entre les ressources afin de les déployer dans le bon ordre.
- Vous pouvez appliquer le contrôle d’accès à tous les services dans votre groupe de ressources, car le contrôle d’accès en fonction du rôle (RBAC) est intégré en mode natif à la plateforme de gestion.
- Vous pouvez appliquer des balises aux ressources pour organiser logiquement toutes les ressources de votre abonnement.
- Vous pouvez clarifier la facturation de votre organisation en affichant les coûts d’un groupe de ressources partageant la même balise.

Resource Manager propose une nouvelle façon de déployer et de gérer vos solutions. Si vous avez utilisé un modèle de déploiement antérieur et souhaitez obtenir des informations sur les modifications, consultez [Présentation du déploiement de Resource Manager et du déploiement classique](resource-manager-deployment-model.md).

## Assistance

Les suggestions suivantes vous aideront à tirer le meilleur parti de Resource Manager lorsque vous travaillez avec vos solutions.

1. Définissez et déployez votre infrastructure via la syntaxe déclarative dans les modèles du Resource Manager, et non via des commandes impératives.
2. Définissez toutes les étapes de déploiement et de configuration dans le modèle. Aucune étape manuelle ne devrait intervenir dans la configuration de votre solution.
3. Exécutez des commandes impératives pour gérer vos ressources, par exemple démarrer ou arrêter une application ou une machine.
4. Organisez des ressources avec le même cycle de vie dans un groupe de ressources. Utilisez des balises pour toute organisation des ressources.

Pour plus de recommandations, voir [Bonnes pratiques relatives à la création de modèles Azure Resource Manager](resource-manager-template-best-practices.md).

## Groupes de ressources

Lorsque vous définissez votre groupe de ressources, vous devez prendre en compte certains facteurs importants :

1. Toutes les ressources de votre groupe doivent partager le même cycle de vie. Les opérations de déploiement, de mise à jour et de suppression porteront sur toutes les ressources du groupe. Si l’une des ressources, comme un serveur de base de données, doit exister dans un autre cycle de déploiement, elle doit appartenir à un autre groupe de ressources.
2. Chaque ressource ne peut exister que dans un seul groupe de ressources.
3. Vous pouvez à tout moment ajouter ou supprimer une ressource au niveau d’un groupe de ressources.
4. Vous pouvez déplacer une ressource d’un groupe de ressources vers un autre groupe. Pour plus d’informations, consultez la page [Déplacement de ressources vers un nouveau groupe de ressources ou un abonnement](resource-group-move-resources.md).
4. Un groupe de ressources peut contenir des ressources figurant dans différentes régions.
5. Un groupe de ressources peut être utilisé pour définir l’étendue du contrôle d’accès des actions administratives.
6. Une ressource peut interagir avec celles d’autres groupes de ressources. Cette interaction est courante lorsque les deux ressources sont liées, mais ne partagent pas le même cycle de vie (par exemple, applications Web connectées à une base de données).

Lorsque vous créez un groupe de ressources, vous devez indiquer un emplacement pour ce groupe. Vous vous demandez peut-être « Pourquoi un groupe de ressources a-t-il besoin un emplacement ? Et, si les ressources peuvent avoir des emplacements différents de celui du groupe de ressources, pourquoi l’emplacement du groupe de ressources a-t-il une importance ? ». Le groupe de ressources stocke des métadonnées sur les ressources. Par conséquent, lorsque vous spécifiez un emplacement pour le groupe de ressources, vous indiquez où stocker ces métadonnées. Pour des raisons de conformité, vous devrez peut-être vous assurer que vos données sont stockées dans une région particulière.

## Fournisseurs de ressources

Chaque fournisseur de ressources propose un ensemble de ressources et d’opérations pour travailler avec les domaines techniques. Par exemple, si vous voulez stocker des clés et des secrets, vous travaillez avec le fournisseur de ressources **Microsoft.KeyVault** . Ce fournisseur de ressources fournit un type de ressource appelé **vaults** pour la création du coffre de clés et un type de ressource appelé **vaults/secrets** pour la création d’un secret dans le coffre de clés. Il fournit également des opérations par le biais [d’opérations d’API REST de Coffre de clés](https://msdn.microsoft.com/library/azure/dn903609.aspx). Vous pouvez appeler l’API REST directement ou utiliser les [applets de commande PowerShell pour le coffre de clés](https://msdn.microsoft.com/library/dn868052.aspx) et les [interfaces de ligne de commande Azure pour le coffre de clés](./key-vault/key-vault-manage-with-cli.md) pour gérer le coffre de clés. Vous pouvez également utiliser plusieurs langages de programmation pour travailler avec la plupart des ressources. Pour plus d’informations, voir [Kits de développement logiciel (SDK) et exemples](#sdks-and-samples).

Pour déployer et gérer votre infrastructure, vous devez en savoir plus sur le fournisseur de ressources. Vous devez connaître ses types de ressources, les numéros de version pour les opérations de l’API REST, les opérations prises en charge et le schéma à utiliser pour créer des ressources. Pour en savoir plus sur les fournisseurs de ressources pris en charge, consultez [Fournisseurs, régions, versions d’API et schémas de Resource Manager](resource-manager-supported-services.md).

## Déploiement de modèle

Avec Resource Manager, vous pouvez créer un modèle (au format JSON) définissant le déploiement et la configuration de votre application. En utilisant un modèle, vous pouvez déployer votre application à plusieurs reprises tout au long du cycle de vie de l’application et avoir ainsi l’assurance que vos ressources présentent un état cohérent lors de leur déploiement. Azure Resource Manager analyse les dépendances pour vérifier que les ressources sont créées dans l’ordre approprié. Pour plus d’informations, consultez [Définition de dépendances dans des modèles Azure Resource Manager](resource-group-define-dependencies.md).

Lorsque vous créez une solution à partir du portail, cette solution inclut automatiquement un modèle de déploiement. Vous n’êtes pas contraint de créer votre modèle à partir de zéro, car vous pouvez partir du modèle de votre solution et le personnaliser en fonction de vos besoins spécifiques. Vous pouvez récupérer un modèle pour un groupe de ressources existant en exportant l’état actuel du groupe de ressources ou en affichant le modèle utilisé pour un déploiement particulier. L’affichage du modèle exporté est un moyen utile pour en découvrir plus sur sa syntaxe. Pour en savoir plus sur l’utilisation des modèles exportés, voir [Exporter un modèle Azure Resource Manager à partir de ressources existantes](resource-manager-export-template.md).

Il est inutile de définir toute votre infrastructure dans un seul modèle. Il peut être judicieux de diviser les exigences de votre déploiement dans un ensemble de modèles ciblés destinés à un usage particulier. Vous pouvez facilement réutiliser ces modèles pour différentes solutions. Pour déployer une solution particulière, créez un modèle de référence qui relie tous les modèles requis. Pour plus d’informations, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).

Vous pouvez également utiliser le modèle pour les mises à jour de l’infrastructure. Par exemple, vous pouvez ajouter une ressource à votre solution et ajouter des règles de configuration pour les ressources qui sont déjà déployées. Si le modèle spécifie de créer une ressource, mais que cette ressource existe déjà, Azure Resource Manager effectue une mise à jour au lieu de créer une autre ressource. Azure Resource Manager met à jour l’actif existant vers l’état qu’il présenterait s’il s’agissait d’une nouvelle ressource. Vous pouvez également faire en sorte que Resource manager supprime les ressources non spécifiées dans le modèle. Pour connaître les différentes options lors du déploiement, consultez [Déployer une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).

Vous pouvez spécifier des paramètres dans votre modèle pour assurer la personnalisation et la flexibilité du déploiement. Par exemple, vous pouvez transmettre des valeurs de paramètre qui adaptent le déploiement à votre environnement de test. La spécification des paramètres vous permet d’utiliser le même modèle pour déployer votre solution dans différents environnements.

Resource Manager fournit des extensions pour les cas qui nécessitent des opérations supplémentaires, comme l’installation d’un logiciel spécifique non inclus dans la configuration. Si vous utilisez déjà un service de gestion de configuration, comme DSC, Chef ou Puppet, vous pouvez continuer à travailler avec ce service en utilisant des extensions.

Pour finir, le modèle devient partie intégrante du code source de votre application. Vous pouvez l’archiver dans votre référentiel de code source et le mettre à jour à mesure que votre application évolue. Le modèle est modifiable par le biais de Visual Studio.

Pour plus d’informations sur la définition du modèle, voir [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).

Pour suivre la procédure pas à pas de création d’un modèle, consultez [Guide de création d’un modèle Resource Manager](resource-manager-template-walkthrough.md).

Pour obtenir des instructions sur le déploiement de votre solution dans différents environnements, consultez [Environnements de développement et de test dans Microsoft Azure](solution-dev-test-environments.md).

## Balises

Resource Manager fournit une fonctionnalité de balisage vous permettant de catégoriser les ressources en fonction de vos exigences de gestion ou de facturation. Utilisez des balises lorsque vous disposez d’un ensemble complexe de groupes de ressources et de ressources et que vous souhaitez les visualiser de la façon qui vous convient le mieux. Par exemple, vous pouvez baliser des ressources qui jouent un rôle similaire dans votre organisation ou qui appartiennent au même département. Sans balises, les utilisateurs de votre organisation peuvent créer plusieurs ressources qui peuvent s’avérer difficiles à identifier et à gérer ultérieurement. Par exemple, vous pouvez souhaiter supprimer toutes les ressources d’un projet particulier. Si ces ressources ne sont pas balisées pour le projet, vous devez les rechercher manuellement. Le balisage constitue un levier important pour réduire les coûts inutiles dans votre abonnement.

Les ressources ne doivent pas nécessairement appartenir au même groupe de ressources pour partager une balise. Vous pouvez créer votre propre taxonomie de balise pour vous assurer que tous les utilisateurs de votre organisation utiliseront des balises communes plutôt que d’appliquer par inadvertance des balises légèrement différentes (telles que « dépt » au lieu de « département »).

Pour plus d’informations sur les balises, voir [Organisation des ressources Azure à l’aide de balises](resource-group-using-tags.md). Vous pouvez créer une [stratégie personnalisée](#manage-resources-with-customized-policies) qui requiert l’ajout de balises aux ressources pendant le déploiement.

## Contrôle d’accès

Resource Manager vous permet de déterminer les utilisateurs qui sont autorisés à exécuter des actions spécifiques pour votre organisation. Il intègre en mode natif à la plate-forme de gestion le protocole OAuth et le contrôle d’accès en fonction du rôle (RBAC), et applique ce contrôle d’accès à tous les services de votre groupe de ressources. Vous pouvez ajouter des utilisateurs à des rôles prédéfinis de plateforme ou de ressource, puis appliquer ces rôles à un abonnement, un groupe de ressources ou une ressource pour limiter l’accès. Par exemple, vous pouvez tirer parti du rôle prédéfini Collaborateur de base de données SQL qui permet aux utilisateurs de gérer les bases de données, mais non les serveurs de base de données ni les stratégies de sécurité. Vous pouvez attribuer le rôle Collaborateur de base de données SQL aux utilisateurs de votre organisation qui ont besoin de ce type d’accès, puis appliquer ce rôle à l’abonnement, au groupe de ressources ou à la ressource.

Resource Manager enregistre automatiquement les actions des utilisateurs à des fins d’audit. Pour plus d’informations sur l’utilisation des journaux d’audit, consultez [Opérations d’audit avec Resource Manager](resource-group-audit.md).

Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez [Contrôle d’accès en fonction du rôle d’Azure](./active-directory/role-based-access-control-configure.md). La rubrique [RBAC : rôles intégrés](./active-directory/role-based-access-built-in-roles.md) contient une liste des rôles intégrés et des actions autorisées. Les rôles intégrés incluent les rôles généraux tels que ceux de Propriétaire, Lecteur et Collaborateur, ainsi que les rôles spécifiques pour le service, par exemple, collaborateur de machine virtuelle, collaborateur de réseau virtuel et Gestionnaire de sécurité SQL (pour ne nommer que quelques-uns des rôles disponibles).

Vous pouvez également verrouiller explicitement les ressources essentielles afin d’empêcher les utilisateurs de les supprimer ou de les modifier. Pour plus d’informations, consultez [Verrouiller des ressources avec Azure Resource Manager](resource-group-lock-resources.md).

Pour connaître les meilleures pratiques, consultez [Questions de sécurité relatives à Azure Resource Manager](best-practices-resource-manager-security.md)

## Gérer les ressources à l’aide de stratégies personnalisées

Resource Manager vous permet de créer des stratégies personnalisées pour gérer vos ressources. Les types de stratégies que vous créez peuvent inclure divers scénarios. Vous pouvez appliquer une convention de dénomination des ressources, limiter les types et les instances de ressources qui peuvent être déployées ou limiter les régions qui peuvent héberger un type de ressource. Vous pouvez demander une valeur de balise sur les ressources pour organiser la facturation par service. Vous créez des stratégies pour vous aider à réduire les coûts et à assurer la cohérence de votre abonnement. Pour plus d’informations, consultez [Utiliser le service Policy pour gérer les ressources et contrôler l’accès](resource-manager-policy.md).

## Couche de gestion cohérente

Resource Manager fournit des opérations compatibles par le biais d’Azure PowerShell, de la ligne de commande Azure pour Mac, Linux et Windows, du portail Azure ou de l’API REST. Vous pouvez utiliser l’interface qui vous convient le mieux et passer rapidement d’une interface à l’autre sans risque de confusion.

Pour plus d’informations sur PowerShell, voir [Utilisation d’Azure PowerShell avec Azure Resource Manager](powershell-azure-resource-manager.md) et [Cmdlets Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn757692.aspx).

Pour plus d’informations sur l’interface de ligne de commande Azure, voir [Utilisation de l’interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Management](xplat-cli-azure-resource-manager.md).

Pour plus d’informations sur l’API REST, voir [Référence sur l’API REST du gestionnaire des ressources Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx). Pour afficher les opérations REST pour vos ressources déployées, consultez la rubrique [Affichage et modification des ressources à l’aide de l’Explorateur de ressources Azure](resource-manager-resource-explorer.md).

Pour plus d’informations sur l’utilisation du portail, consultez [Déployer des ressources avec des modèles Resource Manager et le portail Azure](resource-group-template-deploy-portal.md).

Azure Resource Manager prend en charge le partage des ressources cross-origin (CORS). CORS permet d’appeler l’API REST Resource Manager ou une API REST du service Azure à partir d’une application web qui se trouve dans un domaine différent. Sans la prise en charge de CORS, le navigateur web empêcherait une application d’un domaine d’accéder aux ressources d’un autre domaine. Resource Manager active CORS pour toutes les demandes associées à des informations d’authentification valides.

## Kits de développement logiciel (SDK)

Des kits de développement logiciel (SDK) Azure sont disponibles en plusieurs langues sur plusieurs plates-formes. Chacune de ces langues est disponible via le gestionnaire de package d’écosystème correspondant et GitHub.

Le code dans chaque kit de développement logiciel (SDK) est généré à partir de spécifications de l’API Azure RESTful. Disponibles en open source, ces spécifications sont basées sur la spécification Swagger 2.0. Le code des kits de développement logiciel (SDK) est généré par le biais d’un projet open source appelé AutoRest. AutoRest transforme ces spécifications d’API RESTful en bibliothèques clientes dans plusieurs langues. Si vous souhaitez améliorer des aspects du code généré dans les Kits de développement logiciel (SDK), l’ensemble des outils permettant de créer les Kits de développement logiciel (SDK) sont ouverts, disponibles gratuitement et basés sur un format de spécification d’API largement répandu.

Voici nos référentiels de Kit de développement logiciel (SDK) open source. N’hésitez pas à nous faire part de vos commentaires, des problèmes rencontrés et de vos demandes d’extraction.

[.NET](https://github.com/Azure/azure-sdk-for-net) | [Java](https://github.com/Azure/azure-sdk-for-java) | [Node.js](https://github.com/Azure/azure-sdk-for-node) | [PHP](https://github.com/Azure/azure-sdk-for-php) | [Python](https://github.com/Azure/azure-sdk-for-python) | [Ruby](https://github.com/Azure/azure-sdk-ruby)

> [AZURE.NOTE] Si le Kit de développement logiciel (SDK) ne fournit pas la fonctionnalité requise, vous pouvez également appeler l’[API REST Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx) directement.

## Exemples

### .NET

- [Manage Azure resources and resource groups with .NET (Gérer des ressources et des groupes de ressources Azure avec .NET)](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)
- [Deploy an SSH Enabled VM with a Template (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle)](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)

### Java

- [Manage Azure resources (Gérer des ressources Azure)](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource/)
- [Manage Azure resource groups (Gérer des groupes de ressources Azure)](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group/)
- [Deploy an SSH Enabled VM with a Template (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle)](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)

### Node.js

- [Manage Azure resources and resource groups with .NET (Gérer des ressources et des groupes de ressources Azure avec .NET)](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)
- [Deploy an SSH Enabled VM with a Template (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle)](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)

### Python

- [Manage Azure resources and resource groups with .NET (Gérer des ressources et des groupes de ressources Azure avec .NET)](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)
- [Deploy an SSH Enabled VM with a Template (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle)](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)

### Ruby

- [Manage Azure resources and resource groups with .NET (Gérer des ressources et des groupes de ressources Azure avec .NET)](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)
- [Deploy an SSH Enabled VM with a Template (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle)](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)


Outre ces modèles, vous pouvez parcourir les modèles de la galerie.

[.NET](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=dotnet) | [Java](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=java) | [Node.js](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=nodejs) | [Python](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=python) | [Ruby](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=ruby)

## Étapes suivantes

- Pour une présentation simple de l’utilisation des modèles, consultez [Exporter un modèle Azure Resource Manager à partir de ressources existantes](resource-manager-export-template.md).
- Pour une procédure plus détaillée de création d’un modèle, consultez [Guide de création d’un modèle Resource Manager](resource-manager-template-walkthrough.md).
- Pour comprendre les fonctions que vous pouvez utiliser dans un modèle, consultez [Fonctions de modèle](resource-group-template-functions.md).
- Pour plus d’informations sur l’utilisation de Visual Studio avec Resource Manager, consultez [Création et déploiement de groupes de ressources Azure à l’aide de Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
- Pour plus d’informations sur l’utilisation de VS Code avec Resource Manager, consultez [Utiliser des modèles Azure Resource Manager dans Visual Studio Code](resource-manager-vs-code.md).

Voici une vidéo de cette présentation :

[AZURE.VIDEO azure-resource-manager-overview]

<!---HONumber=AcomDC_0921_2016-->
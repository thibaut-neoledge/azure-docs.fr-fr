<properties
   pageTitle="Conditions techniques préalables à la création d’un modèle de solution pour Azure Marketplace | Microsoft Azure"
   description="Identifier la configuration requise pour la création d’un modèle de solution à déployer et à vendre sur Microsoft Azure Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace-publishing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/09/2015"
   ms.author="hascipio; v-divte" />

# Conditions techniques préalables à la création d’un modèle de solution pour Azure Marketplace
Avant de commencer, lisez attentivement cette procédure, de manière à comprendre à quel moment effectuer une étape et pourquoi chaque étape doit être exécutée. Autant que possible, vous devez garder les informations sur votre société et d’autres données à portée de main, télécharger les outils nécessaires et/ou créer les composants techniques requis avant de lancer le processus de création d’une offre. Cet article devrait vous aider à mieux comprendre ce processus.

## Définition de la topologie et évaluation de la plateforme
À ce stade, peut-être connaissez-vous déjà l’artefact de calcul Microsoft Azure ; peut-être vous posez-vous la question suivante : que dois-je créer ? Des images de machine virtuelle, des extensions ou des modèles ARM ?

Avant d’essayer de mapper votre application aux artefacts Microsoft Azure, il peut s’avérer utile de comprendre le fonctionnement des différentes topologies qui s’appliquent à votre solution, et que vous recommanderiez probablement à un client envisageant de déployer votre application sur site. La plupart des entreprises exploitent généralement une procédure impliquant quatre étapes semblables à celles qui suivent : évaluation, preuve de concept, pré-production et production. En général, vous les accompagnez à chaque étape, en évaluant leurs besoins et en recommandant la mise à l’échelle et la disposition qui correspondent exactement à leurs scénarios. Les clients sont tous différents, mais nous pensons qu’il est possible, dans certains cas, de recommander des topologies et tailles communes, selon l’ampleur du déploiement du client. Ainsi, avant de commencer le développement d’une solution, parcourez les dispositions les plus pertinentes et recherchez celles qui seront le plus à même de répondre aux besoins du plus grand nombre de clients, sans qu’aucune personnalisation majeure ne soit requise. Encore une fois, nous le savons bien : il existe toujours des clients ayant des attentes très précises. Cependant, dans le cas présent, nous cherchons à satisfaire la majorité d’entre eux.

Pour chaque type de déploiement (évaluation, preuve de concept, pré-production et production), identifiez la disposition et la topologie exactes que requiert votre application. Quels sont les différents niveaux ? Quelles sont les ressources nécessaires pour chaque niveau ? Quelle doit être leur taille ? Quels sont les blocs de construction requis pour la création de ces ressources ? Des déploiements de production, voire de développement ou de test, peuvent présenter des tailles très différentes, selon le client et ses besoins. Choisissez l’apparence d’une topologie de petite taille, ou de taille moyenne, grande ou très grande pour votre charge de travail et déterminez le niveau de disponibilité, d’évolutivité et de performances requis pour assurer la réussite de votre solution.

Une fois que vous avez choisi la disposition de topologie et la taille correspondante (peut-être l’avez-vous déjà fait, suite à l’expérience acquise lors de déploiements sur site avec vos clients), il est temps de savoir ce que Microsoft Azure peut vous offrir.

Évaluez vos exigences en matière de topologie par rapport aux fonctions proposées par Azure Resource Manager (ARM) et la fonction Compute associée. La plupart des clients commencent par des déploiements de petite taille, mais **nous vous recommandons vivement d’évaluer les exigences d’une topologie de grande taille en termes de performances, de mise à l’échelle et de capacité sur la fonction Compute de Microsoft ARM, même si vous implémentez une topologie d’une autre taille, au départ.** L’objectif principal est de garantir aux clients qui adoptent vos solutions de petite taille la possibilité d’évoluer correctement vers des déploiements de plus grande envergure. Toute limitation en termes de mise à l’échelle, de performances ou de capacité doit être signalée à vos homologues Microsoft, afin de nous aider à améliorer les aspects dont vous tirez parti, en tant que partenaire Marketplace, ainsi que vos clients.

> [AZURE.TIP]**Procédure recommandée :** évaluez et générez un modèle correspondant à la taille maximale attendue pour le déploiement de votre application, afin de proposer différentes options d’évolution à vos clients finaux.

## Blocs de construction de calcul Microsoft Azure
Commençons par identifier les artefacts de calcul mis à votre disposition, en déterminant de quelle manière les prendre en compte dans la conception de votre application.

### 1\. Utilisation des machines virtuelles existantes
Les blocs de construction d’une machine virtuelle sont des images de machine virtuelle. Ce type d’image correspond au profil de stockage complet d’une machine virtuelle, qui contient un disque VHD pour le système d’exploitation, ainsi que zéro disque de données ou plus. Vous pouvez créer une image de machine virtuelle pour créer un package incluant votre application logicielle avec les bits du système d’exploitation, en plus de toutes les données supplémentaires requises. Ainsi, sur Microsoft Azure Marketplace, vous trouverez quelques images de machine virtuelle pour SQL Server, qui créent un package incluant le système d’exploitation Windows avec l’application SQL Server. Ce document n’aborde pas en détail la création et l’intégration d’images de machine virtuelle dans Microsoft Azure Marketplace. Cependant, il est important de comprendre en quoi consiste ce type d’image, car cela représentera probablement le bloc de construction de votre solution. Voir [Création d’une image de machine virtuelle pour Azure Marketplace](marketplace-publishing-vm-image-creation.md).

### 2\. Extension de machine virtuelle
Il peut arriver que vous ne recherchiez pas d’image de machine virtuelle, car vous ne voulez pas être propriétaire du système d’exploitation, ni le gérer, à titre de fournisseur d’applications. Actuellement, sur Microsoft Azure Marketplace, certaines sociétés publient des systèmes d’exploitation Windows, Linux et Unix régulièrement. Vous pouvez accéder à des images pour les systèmes basés sur CentOS, Windows Server, Ubuntu, CoreOS, Oracle, Linux, etc. Au lieu de publier votre propre image de machine virtuelle, en créant un package incluant le système d’exploitation et l’application, vous pouvez envisager de créer un gestionnaire d’extensions de machine virtuelle ou d’en utiliser un. Une extension de machine virtuelle est un mécanisme logiciel permettant de simplifier la configuration et la gestion des machines virtuelles. Il s’appuie sur l’agent invité (processus léger et sécurisé) d’une machine virtuelle Microsoft Azure. Ainsi, l’extension Script personnalisé inclut un script personnalisé (comme son nom l’indique) et l’exécute dans la machine virtuelle. Ainsi, tout type de configuration autorisé par un script générique est accepté.

Dans un scénario impliquant une image classique, vous installez l’ensemble des bits sur l’image dès le départ, puis vous effectuez la configuration requise avant, voire après le démarrage des machines virtuelles (via une tâche de démarrage). Cette image peut être hautement personnalisée et, de ce fait, difficile à réutiliser. Dans certains cas, c’est ce qui semble le plus logique. Dans d’autres, lorsque vous utilisez des applications légères ou des configurations plus complexes, qui doivent être effectuées après le provisionnement des machines virtuelles, le modèle d’extension peut être utile. Parfois, les clients déploient une combinaison d’extensions et d’images de machine virtuelle, afin de disposer de solutions pré-installées, mais également pour permettre une configuration complexe des éléments après le démarrage.

Les images et extensions sont très utiles pour configurer une machine virtuelle unique, comme vous le savez sûrement. Toutefois, une seule machine virtuelle ne suffit pas pour offrir aux clients la haute disponibilité dans le cloud, ni pour leur permettre de répondre aux besoins d’un trafic dense. De ce fait, pour toute charge de travail de production, nous vous recommandons vivement de prévoir une solution à plusieurs machines virtuelles, hautement disponible, qui utilise des groupes à haute disponibilité et l’équilibrage de charge.

>[AZURE.TIP]**Procédure recommandée :** toutes les charges de production sur Azure Marketplace doivent être déployées via plusieurs machines virtuelles, qui gèrent le trafic au sein d’un groupe à haute disponibilité (avec trois domaines d’erreur) et un dispositif d’équilibrage de charge.

Pour prendre en charge les nombreuses topologies de déploiement que votre application peut gérer et recommander, Microsoft Azure Marketplace propose Azure Resource Manager (ARM) et ses modèles. ARM déploie et administre le cycle de vie d’un ensemble de ressources, par le biais d’une langue de modèle déclarative. Ce modèle correspond simplement à un fichier JSON paramétrable, qui exprime le jeu de ressources et la relation associée à utiliser lors de déploiements. Chaque ressource est placée dans un groupe de ressources, qui est un simple conteneur. Le Gestionnaire des ressources offre aux clients finaux un ensemble de fonctionnalités supplémentaires très utiles, y compris l’audit centralisé des opérations, le balisage des ressources jusqu’à la facturation client, le contrôle des accès basé sur les ressources (pour permettre une sécurité granulaire) et la prise en charge de 3 domaines d’erreur de calcul (pour une disponibilité optimale). De plus, toutes ses opérations sont idempotentes.

> [AZURE.IMPORTANT]**Obligatoire :** au vu des avantages proposés aux clients finaux, tout le contenu Marketplace doit être déployé via des modèles ARM.

Grâce à ARM et à ses modèles, vous pouvez exprimer des déploiements de ressources Microsoft Azure plus complexes (machines virtuelles, comptes de stockage et réseaux virtuels), qui s’appuient sur du contenu Marketplace comme des images et extensions de machine virtuelle.

## Développement de blocs de construction
Lorsque vous avez identifié vos topologies d’application et choisi celles que vous voulez développer et intégrer sur Azure Marketplace lors de la première étape, il est temps d’identifier et de développer les blocs de construction des machines virtuelles. Comme nous l’avons vu, vous devrez opter pour la création de packages incluant le contenu de la machine virtuelle en tant qu’image de machine virtuelle publiée vous appartenant, ou pour la valorisation d’une image de machine virtuelle existante sur Microsoft Azure Marketplace, en la configurant via un gestionnaire d’extensions publié ou votre propre gestionnaire. Il y a plusieurs solutions possibles. Cela dépend du degré de contrôle dont vous souhaitez disposer sur le système d’exploitation sous-jacent, ainsi que de vos besoins concernant l’installation, la configuration et la gestion de votre application. Le site MSDN et les blogs Microsoft Azure fournissent un certain nombre d’informations utiles sur les images et les extensions. La section **Voir aussi** propose divers articles qui peuvent vous aider.

Remarque : tout type d’image ou de gestionnaire d’extensions utilisé doit être publié au préalable sur Azure Marketplace.

## Développement de modèles ARM
Si vous ne connaissez pas Azure Resource Manager et ses modèles, il peut être utile de consulter la documentation relative à ARM et à la fonction Compute associée. Les conseils et exemples fournis ci-dessous reposent sur un niveau de compréhension de base concernant le langage de création de modèles Microsoft ARM et l’utilisation de la fonction Compute avec ARM. Cette section est essentiellement consacrée aux meilleures pratiques et aux exigences, afin de vous aider à développer des modèles efficaces pour Microsoft Azure Marketplace expressément. Cela inclut les exigences permettant d’offrir aux clients une expérience unifiée, conviviale et pertinente, sur le portail Ibiza comme par programme.

Votre solution sera probablement exprimée avec un jeu de modèles. Ces modèles sont des éléments logiques de votre topologie. La répartition de l’expression de la topologie en plusieurs fichiers s’explique par de nombreuses raisons, qu’il s’agisse d’optimiser les facilités de développement ou de gestion, de garantir la réutilisabilité de certains sous-ensembles ou de permettre un test et un développement modulaires des solutions de modèles. Suite à sa collaboration avec de nombreux partenaires, l’équipe Microsoft Azure a mis au point une répartition de la topologie, qui consiste à diviser cette dernière comme suit : un modèle appelé « Ressource partagée » et prévu pour des ressources communes (réseaux virtuels, comptes de stockage, etc.) ; zéro ou plusieurs modèles pour les ressources facultatives, appelés « Ressources facultatives » ; un ou plusieurs modèles pour les ressources spécifiques de la topologie, appelés « Ressource de configuration connue ».

Sur Azure Marketplace, une solution de modèle complète comprend les éléments suivants :

1. **Un modèle principal (mainTemplate.json)** : point d’entrée pour le déploiement global, exprimé dans le fichier de création de modèles JSON de Microsoft ARM. Il s’agit du premier fichier évalué pour le modèle de solution, utilisé pour lier tous les autres modèles. Ce fichier doit également contenir l’ensemble des entrées souhaitées (paramètres) de l’utilisateur final.
2. **Zéro ou plusieurs modèles liés** : il s’agit du reste des modèles liés au modèle principal pour exprimer la topologie complète de la solution.
3. **Zéro ou plusieurs fichiers de script** : il s’agit de tout fichier de script pertinent devant être utilisé en tant qu’entrée pour les extensions à utiliser afin de configurer une machine virtuelle.
4. **Un fichier de création de définition d’interface utilisateur (uiDefinition.json ; createUiDefinition.json)** : il s’agit du fichier qui mappe les paramètres du modèle ARM aux éléments de l’expérience utilisateur. Ce fichier octroie aux partenaires un contrôle sur l’interface utilisateur ayant fait l’objet d’un rendu pour les clients finaux sur le portail Ibiza. Toutes les entrées requises de la part de l’utilisateur final sont exprimées dans ce fichier. Chaque entrée vous permet de contrôler tous les aspects de l’interface utilisateur associés à chaque entrée (widget, valeurs par défaut, règles de validation, messages d’erreur, etc.). Lorsque vous abordez le développement de chacun de ces fichiers dans le but d’exprimer votre topologie complète, tenez compte des critères suivants afin de garantir la création du modèle optimal pour votre offre Azure Marketplace.

## Configuration requise et recommandations supplémentaires

### Ensemble minimal d’entrées utilisateur standardisé
Certains paramètres vous permettent d’exprimer les valeurs qui doivent être fournies par l’utilisateur lors du déploiement dans le modèle. Les entrées utilisateur requises peuvent varier en fonction de l’application, voire du niveau de la topologie. La liste de paramètres doit offrir un bon équilibre entre un nombre de questions obligatoires réduit et des boutons mis à disposition appropriés pour permettre à l’utilisateur de contrôler son déploiement. La liste complète d’entrées utilisateur ne fait l’objet d’aucune exigence. Cependant, nous voulons nous assurer que toutes les solutions de modèles Azure Marketplace présentent un jeu commun, qu’un grand nombre de clients souhaite contrôler. Vérifiez que vous avez défini des paramètres pour les valeurs suivantes :

1. **Emplacement** : région Microsoft Azure au sein de laquelle les ressources sont déployées.
2.	**Nom de réseau virtuel** : dans le cas de déploiements créant un réseau virtuel, nom à utiliser pour créer cette ressource. Dans le cas de déploiements utilisant un réseau virtuel existant, nom du réseau dans lequel effectuer le déploiement.
3.	**Compte de stockage** : dans le cas de déploiements créant un compte de stockage, nom du compte à créer. Dans le cas de déploiements utilisant un compte de stockage existant, nom du compte à utiliser.
4.	**Nom DNS** : nom de domaine de la machine virtuelle ou de la fonction d’équilibrage de charge à laquelle l’utilisateur final peut se connecter.
5.	**Nom d’utilisateur** : nom d’utilisateur de la ou des machines virtuelles, voire de la ou des applications. Le système peut demander à l’utilisateur final plusieurs noms d’utilisateur ; ce dernier doit en saisir au moins un.
6.	**Mot de passe** : mot de passe d’accès à la ou aux machines virtuelles, voire à la ou aux applications. Le système peut demander à l’utilisateur final plusieurs mots de passe, pour différentes machines virtuelles ou applications ; ce dernier doit en saisir au moins un.
7.	**Clé publique SSH (uniquement pour les machines virtuelles Linux)** : clé SSH pour la ou les machines virtuelles. Le système peut demander à l’utilisateur final plusieurs clés, pour différentes machines virtuelles ; ce dernier doit en saisir au moins une.

## Étapes suivantes
À présent, vous avez passé en revue les conditions préalables et effectué les tâches nécessaires. Vous pouvez donc procéder à la création de votre offre de modèle de solution, comme indiqué dans le document suivant : [Guide de création d’un modèle de solution](marketplace-publishing-solution-template-creation.md).

## Voir aussi
- [Mise en route : publication d’une offre sur Azure Marketplace](marketplace-publishing-getting-started.md)
- [Meilleures pratiques en matière de création de modèles de solution](marketplace-publishing-solution-template-best-practices.md)

[link-acct]: marketplace-publishing-accounts-creation-registration.md

<!---HONumber=Oct15_HO3-->
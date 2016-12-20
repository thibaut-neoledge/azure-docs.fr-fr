---
title: Historique des versions des diagnostics Azure
description: "Explication des modifications apportées aux différentes versions des diagnostics Azure, fournies avec les différentes versions des Kits de développement logiciel (SDK) Microsoft Azure."
services: multiple
documentationcenter: .net
author: rboucher
manager: jwhit
editor: 
ms.assetid: 0ae6738e-d3bf-4774-86bf-6e30a9d16c79
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/12/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 3b693bcb17fb306d25833ad93416cdec383642bb


---
# <a name="microsoft-azure-diagnostics-version-history"></a>Historique des versions des diagnostics Microsoft Azure
Vous découvrez tout juste les diagnostics Azure ? Consultez [Vue d’ensemble des diagnostics Azure](azure-diagnostics.md).

Chaque version du Kit de développement logiciel (SDK) Azure est généralement fournie avec une nouvelle version des diagnostics Azure. Le tableau ci-dessous décrit les versions du SDK Azure et des diagnostics Azure associées à la version du SDK.

| Version du SDK Azure | Version des diagnostics Azure | Modèle |
| --- | --- | --- |
| 1.x |1.0 |plug-in |
| 2.0 à 2.4 |1.0 |" |
| 2.5 |1.2 |extension |
| 2.6 |1.3 |" |
| 2.7 |1.4 |" |
| 2.8 |1.5 |" |

La version la plus récente est la version 1.5, fournie avec Azure SDK 2.8. La version de l'extension des diagnostics Azure fournie avec le SDK est uniquement utilisée pour les scénarios d'émulateur local. Toute application déployée utilise automatiquement la dernière version lors de l'exécution dans Azure, quelle que soit la version du SDK avec lequel l'application est générée. Toutefois, sauf si vous installez la dernière version du SDK Azure, vous n'avez pas forcément tous les outils qui vous aident à utiliser les nouvelles fonctionnalités.

Différentes fonctionnalités et modifications sont décrites ci-après.

## <a name="azure-sdk-28"></a>Azure SDK 2.8
Azure SDK 2.8 a ajouté la possibilité d’envoyer des données de diagnostic à [Application Insights](application-insights/app-insights-cloudservices.md), facilitant ainsi le diagnostic des problèmes sur votre application, ainsi qu’au niveau du système et de l’infrastructure.

## <a name="azure-26-diagnostics-changes"></a>Modifications apportées aux diagnostics Azure 2.6
Pour les projets Cloud Service d’Azure SDK 2.6 dans Visual Studio, les modifications suivantes ont été apportées. Ces modifications s’appliquent également aux versions ultérieures du Kit de développement logiciel (SDK) Azure.

* L’émulateur local prend désormais en charge les diagnostics. Cela signifie que vous pouvez collecter les données de diagnostic et vérifier que votre application crée les traces appropriées lorsque vous développez et testez dans Visual Studio. La chaîne de connexion `UseDevelopmentStorage=true` permet la collecte des données de diagnostic lorsque vous exécutez votre projet de service cloud dans Visual Studio à l’aide de l’émulateur de stockage Azure. Toutes les données de diagnostic sont collectées dans le compte de stockage (stockage de développement).
* La chaîne de connexion de compte de stockage des diagnostics (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) est à nouveau stockée dans le fichier de configuration de service (.cscfg). Dans le Kit de développement logiciel (SDK) Azure 2.5, le compte de stockage des diagnostics a été spécifié dans le fichier diagnostics.wadcfgx.

Il existe des différences notables entre la façon dont la chaîne de connexion fonctionnait dans le Kit de développement logiciel (SDK) Azure 2.4 et les versions antérieures, et celle dont elle fonctionne dans le Kit de développement logiciel (SDK) Azure 2.6 et les versions ultérieures.

* Dans le Kit de développement logiciel (SDK) Azure 2.4 et les versions antérieures, la chaîne de connexion était utilisée comme un runtime par le plug-in des diagnostics pour obtenir les informations du compte de stockage relatives au transfert de journaux de diagnostic.
* Dans le Kit de développement logiciel (SDK) Azure 2.6 et les versions ultérieures, la chaîne de connexion des diagnostics est utilisée par Visual Studio pour configurer l’extension de diagnostics avec les informations de compte de stockage appropriées lors de la publication. La chaîne de connexion permet de définir des comptes de stockage différents pour différentes configurations de service que Visual Studio utilise lors de la publication. Toutefois, étant donné que le plug-in des diagnostics n’est plus disponible (après le Kit de développement logiciel (SDK) Azure 2.5), le fichier .cscfg par lui-même ne peut pas activer l’extension des diagnostics. Vous devez activer l’extension séparément par le biais d’outils tels que Visual Studio ou PowerShell.
* Pour simplifier le processus de configuration de l’extension des diagnostics avec PowerShell, la sortie du package à partir de Visual Studio contient également les fichiers XML de configuration publique pour l’extension des diagnostics pour chaque rôle. Visual Studio utilise la chaîne de connexion des diagnostics pour renseigner les informations de compte de stockage présentes dans la configuration publique. Les fichiers de configuration publique sont créés dans le dossier Extensions et suivent le modèle PaaSDiagnostics.<RoleName>.PubConfig.xml. Tout déploiement basé sur PowerShell peut utiliser ce modèle pour mapper chaque configuration à un rôle.
* La chaîne de connexion dans le fichier .cscfg est également utilisée par le portail Azure pour accéder aux données de diagnostic afin de pouvoir les intégrer sous l’onglet **Analyse** . La chaîne de connexion est nécessaire pour configurer le service afin qu’il affiche les données d’analyse détaillées dans le portail.

### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Migration de projets vers le Kit de développement logiciel (SDK) Azure 2.6 et les versions ultérieures
Lors d’une migration du Kit de développement logiciel (SDK) Azure 2.5 vers le Kit de développement logiciel (SDK) Azure 2.6 ou les versions ultérieures, si vous avez un compte de stockage des diagnostics spécifié dans le fichier .wadcfgx, il y reste. Pour tirer parti de la souplesse que représente l’utilisation de différents comptes de stockage pour différentes configurations de stockage, vous devez ajouter manuellement la chaîne de connexion à votre projet. Si vous migrez un projet à partir d'Azure SDK 2.4 ou d’une version antérieure vers Azure SDK 2.6, les chaînes de connexion de diagnostic sont conservées. Toutefois, notez les modifications apportées à la manière dont les chaînes de connexion sont traitées dans le Kit de développement logiciel (SDK) Azure 2.6, comme spécifié dans la section précédente.

### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Détermination du compte de stockage des diagnostics par Visual Studio
* Si une chaîne de connexion des diagnostics est spécifiée dans le fichier .cscfg, Visual Studio l’utilise pour configurer l’extension des diagnostics lors de la publication et lors de la génération des fichiers xml de configuration publique durant l’empaquetage.
* Si aucune chaîne de connexion des diagnostics n’est spécifiée dans le fichier .cscfg, Visual Studio utilise à nouveau le compte de stockage spécifié dans le fichier .wadcfgx pour configurer l’extension des diagnostics lors de la publication et de la génération des fichiers xml de configuration publique durant l’empaquetage.
* La chaîne de connexion des diagnostics dans le fichier .cscfg est prioritaire sur le compte de stockage dans le fichier .wadcfgx. Si une chaîne de connexion des diagnostics est spécifiée dans le fichier .cscfg, Visual Studio l’utilise et ignore le compte de stockage dans .wadcfgx.

### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>Quel est le rôle de la case à cocher « Mettre à jour les chaînes de connexion de stockage de développement…»  ?
La case à cocher **Mettre à jour les chaînes de connexion de stockage de développement pour les diagnostics et la mise en cache avec les informations d’identification du compte de stockage Microsoft Azure lors de la publication vers Microsoft Azure** offre un moyen pratique pour mettre à jour toute chaîne de connexion de compte de stockage de développement avec le compte de stockage Azure spécifié lors de la publication.

Par exemple, supposons que vous activiez cette case à cocher et que la chaîne de connexion des diagnostics spécifie `UseDevelopmentStorage=true`. Lorsque vous publiez le projet sur Azure, Visual Studio met automatiquement à jour la chaîne de connexion des diagnostics avec le compte de stockage que vous avez spécifié dans l’Assistant Publication. Toutefois, si un compte de stockage réel a été spécifié comme chaîne de connexion des diagnostics, ce compte est utilisé à la place.

## <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Différences entre les fonctionnalités de diagnostics du Kit de développement logiciel (SDK) Azure 2.4 et les versions antérieures et du Kit de développement logiciel (SDK) Azure 2.5 et les versions ultérieures
Si vous mettez à niveau votre projet du Kit de développement logiciel (SDK) Azure 2.4 vers le Kit de développement logiciel (SDK) Azure 2.5 ou les versions ultérieures, vous devez garder à l’esprit les différences existant entre les fonctionnalités de diagnostics suivantes.

* **Les API de configuration sont déconseillées** : la configuration par programmation des diagnostics est disponible dans le Kit de développement logiciel (SDK) Azure 2.4 ou les versions antérieures, mais déconseillée dans le Kit de développement logiciel (SDK) Azure 2.5 et les versions ultérieures. Si votre configuration des diagnostics est actuellement définie dans un code, vous devez reconfigurer ces paramètres à partir de rien dans le projet migré, afin que les diagnostics continuent à fonctionner. Le nom du fichier de configuration des diagnostics est diagnostics.wadcfg dans le Kit de développement logiciel (SDK) Azure 2.4 et diagnostics.wadcfgx dans le Kit de développement logiciel (SDK) Azure 2.5 et les versions ultérieures.
* **Les diagnostics pour les applications du service cloud peuvent uniquement être configurés au niveau du rôle, pas au niveau de l’instance.**
* **Chaque fois que vous déployez votre application, la configuration des diagnostics est mise à jour** : cela peut occasionner des problèmes de parité si vous modifiez votre configuration des diagnostics à partir de l’Explorateur de serveurs, puis redéployez votre application.
* **Dans le Kit de développement logiciel (SDK) Azure 2.5 et les versions ultérieures, les vidages sur incident sont configurés dans le fichier de configuration des diagnostics, pas dans le code** : si vous avez des vidages sur incident configurés dans le code, vous devez transférer la configuration manuellement du code vers le fichier de configuration, car les vidages sur incident ne sont pas transférés durant la migration vers le Kit de développement logiciel (SDK) Azure 2.6.




<!--HONumber=Nov16_HO3-->



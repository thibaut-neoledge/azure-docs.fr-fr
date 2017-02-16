---
title: "Explorateur de stockage Microsoft Azure 0.8.7 (version préliminaire)| Microsoft Docs"
description: "Notes de publication pour l’explorateur de stockage Microsoft Azure 0.8.7 (version préliminaire)"
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: 
ms.assetid: 
ms.service: storage
ms.devlang: multiple
ms.topic: release-notes
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/18/2017
ms.author: cawa
translationtype: Human Translation
ms.sourcegitcommit: bf47274a97342253dfd4ebfa9f1029a7c18b81eb
ms.openlocfilehash: fc3620ca19d4824bc8ffb3bbe89ef47c5127b9d1


---
# <a name="microsoft-azure-storage-explorer-087-preview"></a>Explorateur de stockage Microsoft Azure 0.8.7 (version préliminaire)
## <a name="overview"></a>Vue d'ensemble
Cet article contient les notes de publication pour la version préliminaire de l’explorateur de stockage Azure 0.8.7.

[L’explorateur de stockage Microsoft Azure](./vs-azure-tools-storage-manage-with-storage-explorer.md) (version préliminaire) est une application autonome qui vous permet d’utiliser facilement les données Stockage Azure sur Windows, macOS et Linux.

## <a name="azure-storage-explorer-087-preview"></a>Explorateur de stockage Azure 0.8.7 (version préliminaire)
### <a name="download-azure-storage-explorer-087-preview"></a>Télécharger l’explorateur de stockage Azure 0.8.7 (version préliminaire)
- [Version préliminaire de l’explorateur de stockage Azure 0.8.7 pour Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Version préliminaire de l’explorateur de stockage Azure 0.8.7 pour Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Version préliminaire de l’explorateur de stockage Azure 0.8.7 pour Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new-updates"></a>Nouvelles mises à jour
* Vous pouvez choisir la façon dont vous souhaitez résoudre les conflits au début d’une session de mise à jour, de téléchargement ou de copie dans la fenêtre **Activités**.
* Pointez sur un onglet pour afficher le chemin d’accès complet de la ressource de stockage.
* Lorsque vous cliquez sur un onglet, il se synchronise avec son emplacement dans le volet de navigation de gauche.

### <a name="fixes"></a>Correctifs
* Problème résolu : l’explorateur de stockage est désormais une application de confiance sur macOS.
* Problème résolu : Ubuntu 14.04 est de nouveau pris en charge.
* Problème résolu : l’interface utilisateur Ajouter un compte clignote parfois lors du chargement des abonnements.
* Problème résolu : certaines ressources de stockage n’étaient pas répertoriées dans le volet de navigation de gauche.
* Problème résolu : le volet d’action affichait parfois des actions vides.
* Problème résolu : la taille de la fenêtre de la dernière session fermée est désormais conservée.
* Problème résolu : vous pouvez ouvrir plusieurs onglets pour la même ressource à l’aide du menu contextuel.

### <a name="known-issues"></a>Problèmes connus
* L’Accès rapide fonctionne uniquement avec les éléments basés sur un abonnement. Les ressources locales ou attachées par le biais d’une clé ou d’un jeton SAP ne sont pas prises en charge dans cette version.
* L’Accès rapide peut prendre quelques secondes pour accéder à la ressource cible, selon le nombre de ressources dont vous disposez.
* Des erreurs peuvent survenir si vous chargez plus de trois groupes de blobs ou fichiers simultanément.
* Les recherches peuvent porter sur 50 000 nœuds environ. Au-delà, elles affecteront les performances ou pourront entraîner des exceptions non prises en charge.
* Lorsque vous utilisez l’explorateur de stockage sur macOS pour la première fois, il se peut que plusieurs invites vous demandent l’autorisation de l’utilisateur pour accéder au trousseau. Nous vous suggérons de sélectionner **Toujours autoriser**, de sorte que l’invite cesse de s’afficher.

## <a name="previous-releases"></a>Versions précédentes
### <a name="features"></a>Caractéristiques
#### <a name="main-features"></a>Caractéristiques principales
* Versions macOS, Linux et Windows
* Connectez-vous pour afficher vos comptes de stockage groupés par abonnement :
    * Utilisez votre compte professionnel ou Microsoft, 2FA, etc.
    * Configurez et gérez les paramètres de proxy
    * Supprimez des comptes en vous déconnectant
* Connectez-vous aux comptes de stockage avec :
    * Nom et clé du compte
    * Points de terminaison personnalisés (y compris Azure China)
    * URI SAP pour les comptes de stockage
* Prise en charge d’Azure Resource Manager et du stockage classique
* Générez des clés SAP pour les blobs, conteneurs d’objets blob, files d’attente, tables ou partages de fichiers
* Connectez-vous à des conteneurs d’objets blob, files d’attente, tables ou partages de fichiers avec une clé de signatures d’accès partagé (SAP)
* Gérez les stratégies d’accès stockées pour les conteneurs d’objets blob, files d’attente, tables ou partages de fichiers
* Stockage de développement local avec l’Émulateur de stockage (Windows uniquement)
* Créez et supprimez des conteneurs d’objets blob, files d’attente ou tables
* Affichez les conteneurs d’objets blob $logs et les tables $metrics
* Recherchez des blobs, files d’attente, tables ou partages de fichiers spécifiques
* Liens directs vers des conteneurs ou comptes de stockage, files d’attente, tables ou partages de fichiers pour partager vos ressources et y accéder facilement
* Renommez des conteneurs d’objets blob, tables et partages de fichiers
* Possibilité de gérer et configurer des règles CORS
* Copiez facilement des clés primaires et secondaires pour les comptes de stockage
* Vérifications MD5 pour contrôler l’intégrité et la cohérence des données lors du chargement et du téléchargement
* Recherchez vos conteneurs d’objets blob, tables, files d’attente, partages de fichiers ou comptes de stockage à partir de la zone de recherche
* Vous pouvez désormais épingler les services les plus fréquemment utilisés à l’Accès rapide pour faciliter la navigation
* Vous pouvez désormais ouvrir plusieurs éditeurs dans différents onglets. Cliquez pour ouvrir un onglet temporaire, et double-cliquez pour ouvrir un onglet permanent. Vous pouvez également cliquer sur l’onglet temporaire pour le rendre permanent
* Améliorations notables en termes de stabilité et de performances pour les chargements et téléchargements, en particulier pour les fichiers volumineux sur des machines rapides
* Nous réintroduisons la recherche étendue avancée et avons ajouté le concept d’extension. Entrez le chemin d’accès à un nœud par le biais de l’icône de survol, cliquez avec le bouton, puis sélectionnez « Rechercher à partir d’ici », ou procédez manuellement pour définir l’étendue de ce nœud. Une fois l’étendue définie, vous pouvez ajouter un terme de recherche à la fin du chemin d’accès pour effectuer une recherche approfondie à partir de ce nœud
* Nous avons ajouté différents thèmes : Clair (par défaut), Foncé, Contraste noir élevé et Contraste blanc élevé.
* Sélectionnez Édition -> Thèmes pour modifier vos préférences de thème
* Sous Linux, un système d’exploitation 64 bits est désormais obligatoire
* Nous avons mis à jour notre logo.
#### <a name="blobs"></a>Objets blob
* Affichez les blobs et parcourez les répertoires
* Chargez, téléchargez, supprimez et copiez des blobs et des dossiers
* Ouvrez et affichez les blobs de texte et d’image du contenu
* Affichez et modifiez les propriétés et métadonnées des blobs
* Recherchez les blobs par préfixe
* Créez et annulez les baux des blobs et conteneurs d’objets blob
* Glissez-déplacez les fichiers à charger
* Renommez les blobs et dossiers
* Des dossiers « virtuels » vides peuvent maintenant être créés dans des conteneurs d’objets blob
* Vous pouvez modifier les propriétés des blobs et des fichiers
#### <a name="tables"></a>Tables
* Affichez et interrogez des entités avec OData, ou utilisez le Générateur de requêtes pour créer des requêtes complexes
* Ajoutez, modifiez ou supprimez des entités
* Importez et exportez le contenu des tables au format CSV (avec exportation des résultats des requêtes)
* Personnalisez l’ordre des colonnes
* Possibilité d’enregistrer les requêtes
#### <a name="queues"></a>files d’attente
* Affichez un aperçu des 32 derniers messages
* Ajoutez, enlevez de la file d’attente et affichez les messages
* Effacez la file d’attente
* Nous vous offrons désormais la possibilité de décider si vous souhaitez encoder/décoder les messages de votre file d’attente
#### <a name="file-shares"></a>Partages de fichiers
* Affichez les fichiers et parcourez les répertoires
* Chargez, téléchargez, supprimez et copiez des fichiers et des répertoires
* Affichez les propriétés de fichier
* Renommez les fichiers et répertoires

### <a name="bug-fixes"></a>Résolution des bogues
* Problème résolu : blocage de l’écran
* Sécurité améliorée

### <a name="known-issues"></a>Problèmes connus
* Les recherches peuvent porter sur 50 000 nœuds environ. Au-delà, elles affecteront les performances et les installations macOS nécessiteront peut-être des autorisations élevées
* Le panneau des paramètres de compte peut indiquer que vous devez entrer à nouveau vos informations d’identification pour filtrer les abonnements
* Les captures instantanées ne sont pas conservées lorsque les blobs sont renommés (individuellement ou dans un conteneur d’objets blob renommé). Cependant, lors d’un changement de nom, toutes les autres propriétés et métadonnées des blobs, fichiers et entités sont conservées.
* Actuellement, Azure Stack ne prend pas en charge les fichiers. Par conséquent, toute tentative de développement du nœud **Fichiers** entraînera une erreur.
* L’installation de Linux 14.04 requiert la mise à jour ou la mise à niveau de la version gcc. Les étapes suivantes montrent comment effectuer une mise à niveau :

```
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt-get update
sudo apt-get upgrade
sudo apt-get dist-upgrade
```

### <a name="previous-versions"></a>Versions antérieures
#### <a name="october-2016-release-version-085"></a>Version d’octobre 2016 (version 0.8.5)
* [Téléchargement pour Windows](https://go.microsoft.com/fwlink/?LinkId=809306)
* [Téléchargement pour Mac](https://go.microsoft.com/fwlink/?LinkId=809307)
* [Téléchargement pour Linux](https://go.microsoft.com/fwlink/?LinkId=809308)



<!--HONumber=Jan17_HO3-->



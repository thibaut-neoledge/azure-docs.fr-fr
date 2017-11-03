---
title: "Rechercher des données non structurées dans le stockage cloud Azure"
description: "Recherche de données non structurées à l’aide de Recherche Azure."
author: roygara
manager: timlt
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: rogara
ms.custom: mvc
ms.openlocfilehash: 930b735eb03aea6ce701b694ca527049b4c3f24d
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2017
---
# <a name="search-unstructured-data-in-cloud-storage"></a>Rechercher des données non structurées dans le stockage cloud

Dans ce didacticiel, vous apprenez à rechercher des données non structurées à l’aide de [Recherche Azure](../../search/search-what-is-azure-search.md) en utilisant des données stockées dans des objets blob Azure. Les données non structurées sont des données qui ne sont pas organisées de manière prédéfinie ou qui n’ont pas de modèle de données. Un fichier .txt en est un exemple.

Ce didacticiel vous explique comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un groupe de ressources
> * Créez un compte de stockage.
> * Créez un conteneur.
> * Télécharger des données vers votre conteneur
> * Créer un service de recherche via le portail
> * Utiliser le service de recherche pour rechercher votre conteneur

## <a name="download-the-sample"></a>Téléchargez l’exemple

Un exemple de jeu de données a été préparé pour vous. **Téléchargez le fichier [clinical-trials.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials.zip)** et décompressez-le dans son propre dossier.

L’exemple comprend des fichiers texte obtenus auprès de [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). Vous pouvez les utiliser comme exemples de fichiers texte pour effectuer des recherches en utilisant Azure.

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](http://portal.azure.com).

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

Le compte de stockage fournit un emplacement unique pour stocker vos objets de données Stockage Azure et y accéder.

Actuellement, il existe deux types de comptes de stockage, **Blob** et **Usage général**. Dans ce didacticiel, vous créez un compte de stockage **Usage général**.

Si vous ne connaissez pas le processus de création d’un compte de stockage à usage général, voici comment en créer un :

1. Dans le menu de gauche, sélectionnez **Comptes de stockage**, puis sélectionnez **Ajouter**.

2. Entrez un nom unique pour votre compte de stockage. 

3. Sélectionnez **Gestionnaire des ressources** pour votre **modèle de déploiement** et sélectionnez **Usage général** dans la liste déroulante **Type de compte**.

4. Sélectionnez **Stockage localement redondant (LRS)** dans la liste déroulante **Réplication**.

5. Sous **Groupe de ressources**, sélectionnez **Créer** et entrez un nom unique.

6. Sélectionnez un abonnement approprié.

7. Choisissez un emplacement et sélectionnez **Créer.**

  ![Recherche non structurée](media/storage-unstructured-search/storagepanes2.png)

## <a name="create-a-container"></a>Créez un conteneur.

Les conteneurs s’apparentent à des dossiers et servent à stocker les objets Blob.

Dans ce didacticiel, vous utilisez un seul conteneur pour stocker les fichiers texte obtenus auprès de clinicaltrials.gov.

1. Accédez à votre compte de stockage dans le portail Azure.

2. Sélectionnez **Parcourir les objets blob** sous **Service BLOB.**

3. Ajoutez un nouveau conteneur.

4. Nommez ce conteneur « données » et sélectionnez **Conteneur** pour le niveau d’accès public.

5. Sélectionnez **OK** pour créer le conteneur. 

  ![Recherche non structurée](media/storage-unstructured-search/storageactinfo.png)

## <a name="upload-the-example-data"></a>Charger l’exemple de données

Maintenant que vous avez un conteneur, vous pouvez y charger votre exemple de données.

1. Sélectionnez votre conteneur, puis **Charger**.

2. Sélectionnez l’icône de dossier bleue située en regard du champ Fichiers et accédez au dossier local où vous avez extrait l’exemple de données.

3. Sélectionnez tous les fichiers extraits, puis **Ouvrir**.

4. Sélectionnez **Charger** pour lancer le processus de chargement.

  ![Recherche non structurée](media/storage-unstructured-search/upload.png)

Le processus de chargement peut durer quelques minutes.

Une fois qu’il est terminé, accédez à votre conteneur de données pour vérifier que les fichiers texte sont chargés.

  ![Recherche non structurée](media/storage-unstructured-search/clinicalfolder.png)

## <a name="create-a-search-service"></a>Créer un service de recherche

Recherche Azure est une solution cloud de recherche en tant que service, qui offre aux développeurs des API et des outils permettant d’ajouter une expérience de recherche riche concernant vos données dans les applications web, mobiles et d’entreprise.

Si vous ne connaissez pas le processus de création d’un service de recherche, voici comment en créer un :

1. Accédez à votre compte de stockage dans le portail Azure.

2. Faites défiler vers le bas et cliquez sur **Ajouter Recherche Azure** sous **SERVICE BLOB**.

3. Sous **Importer des données**, sélectionnez **Choisir votre service**.

4. Sélectionnez **Cliquez ici pour créer un service de recherche**.

5. Dans **Nouveau service de recherche**, entrez un nom unique pour votre service de recherche dans le champ **URL**.

6. Sous **Groupe de ressources**, sélectionnez **Utiliser l’existant** et choisissez le groupe de ressources que vous avez créé précédemment.

7. Pour **Niveau tarifaire**, sélectionnez le niveau **Gratuit** et cliquez sur **Sélectionner**.

8. Sélectionnez **Créer** pour créer le service de recherche.

  ![Recherche non structurée](media/storage-unstructured-search/createsearch2.png)

## <a name="connect-your-search-service-to-your-container"></a>Connecter votre service de recherche à votre conteneur

Maintenant que vous avez un service de recherche, vous pouvez l’attacher à votre stockage d’objets blob. Cette section vous guide tout au long des processus de choix d’une source de données, de création d’un index et de création d’un indexeur.

1. Accédez à votre compte de stockage.

2. Sélectionnez **Ajouter Recherche Azure** sous **SERVICE BLOB.**

3. Sélectionnez **Service de recherche** dans **Importer des données**, puis cliquez sur le service de recherche que vous avez créé dans la section précédente. La fenêtre **Nouvelle source de données** s’ouvre.

### <a name="new-data-source"></a>Nouvelle source de données

  Une source de données spécifie les données à indexer et comment accéder à ces données. Le même service de recherche peut utiliser une source de données plusieurs fois.

1. Entrez un nom pour la source de données. Sous **Données à extraire**, sélectionnez **Contenu et métadonnées**. La source de données spécifie les parties des objets blob à indexer.
    
    a. Dans vos propres futurs scénarios, vous pourrez également sélectionner **Métadonnées de stockage uniquement**. Vous effectueriez cette sélection si vous souhaitiez limiter les données indexées aux propriétés des objets blob standard ou aux propriétés définies par l’utilisateur.
    
    b. Vous pouvez également choisir **Toutes les métadonnées** pour obtenir à la fois les propriétés des objets blob standard et *toutes* les métadonnées propres au type de contenu. 

2. Étant donné que les objets blob que vous utilisez sont des fichiers texte, affectez à **Mode d’analyse** la valeur **Texte**.
    
    a. Dans vos propres futurs scénarios, vous pourrez éventuellement sélectionner [Autres modes d’analyse](../../search/search-howto-indexing-azure-blob-storage.md) selon le contenu de vos objets blob.

  ![Recherche non structurée](media/storage-unstructured-search/datasources.png)

3. Sélectionnez **Conteneur de stockage** pour répertorier les comptes de stockage disponibles.

4. Sélectionnez votre compte de stockage, puis sélectionnez le conteneur que vous avez créé précédemment.

5. Cliquez sur **Sélectionner** pour revenir à **Nouvelle source de données** et sélectionnez **OK** pour continuer.

  ![Recherche non structurée](media/storage-unstructured-search/datacontainer.png)

### <a name="configure-the-index"></a>Configurer l’index

  Un index est une collection de champs issus de votre source de données dans lesquels vous pouvez effectuer des recherches. L’index indique à votre service de recherche comment effectuer des recherches dans vos données.

1. Dans **Importer des données**, sélectionnez **Personnaliser l’index cible**.
 
2. Entrez un nom pour votre index dans le champ **Nom de l’index**.

3. Cochez la case de l’attribut **Récupérable** sous **metadata_storage_name**.

  ![Recherche non structurée](media/storage-unstructured-search/valuestoselect.png)

4. Cliquez sur **OK**, ce qui permet d’afficher **Créer un indexeur**.

Les paramètres de l’index et les attributs que vous donnez à ces paramètres sont importants. Les paramètres spécifient *quelles* données stocker, tandis que les attributs indiquent *comment* stocker ces données.

La colonne **NOM DU CHAMP** contient les paramètres. Le tableau suivant fournit la liste des attributs disponibles et leurs descriptions.

### <a name="field-attributes"></a>Attributs de champ
| Attribut | Description |
| --- | --- |
| *Clé* |Chaîne fournissant un ID unique à chaque document utilisé pour rechercher des documents. Chaque index doit avoir une clé. Un seul champ peut être la clé, et son type doit être défini sur Edm.String. |
| *Affichable dans les résultats d’une recherche* |Définit si un champ peut être retourné dans un résultat de recherche. |
| *Filtrable* |Permet d’utiliser le champ dans des requêtes de filtre. |
| *Triable* |Permet à une requête de trier les résultats de recherche à l’aide de ce champ. |
| *À choix multiple* |Permet d’utiliser un champ pour le filtrage autonome dans une structure de navigation par facettes par un utilisateur. En général, les champs contenant des valeurs répétitives que vous pouvez utiliser pour regrouper plusieurs documents (par exemple, plusieurs documents appartenant à une seule marque ou catégorie de service) sont les mieux adaptés en tant que facettes. |
| *Possibilité de recherche* |Indique que le champ peut faire l’objet d’une recherche en texte intégral. |


### <a name="create-an-indexer"></a>Créer un indexeur
    
  Un indexeur connecte une source de données à un index de recherche et permet de planifier la réindexation de vos données.

1. Entrez un nom dans le champ **Nom**, puis sélectionnez **OK**.

  ![Recherche non structurée](media/storage-unstructured-search/exindexer.png)

2. Vous revenez à **Importer des données**, sélectionnez **OK** pour terminer le processus de connexion.

Vous avez maintenant connecté votre objet blob à votre service de recherche. Le portail a besoin de quelques minutes pour montrer que l’index est rempli. Toutefois, le service de recherche démarre l’indexation immédiatement afin que vous puissiez effectuer des recherches tout de suite.

## <a name="search-your-text-files"></a>Rechercher dans vos fichiers texte

Pour rechercher dans vos fichiers, ouvrez l’Explorateur de recherche dans l’index de votre service de recherche nouvellement créé.

Les étapes suivantes vous indiquent où trouver l’Explorateur de recherche en vous fournissant quelques exemples de requêtes :

1. Accédez à toutes les ressources et recherchez votre service de recherche nouvellement créé.

  ![Recherche non structurée](media/storage-unstructured-search/exampleurl.png)

3. Sélectionnez votre index pour l’ouvrir. 

  ![Recherche non structurée](media/storage-unstructured-search/overview.png)

4. Sélectionnez **Explorateur de recherche** pour ouvrir l’Explorateur de recherche, où vous pouvez effectuer des requêtes actives sur vos données.

  ![Recherche non structurée](media/storage-unstructured-search/indexespane.png)

5. Sélectionnez **Rechercher** alors que le champ de chaîne de requête est vide. Une requête vide retourne *toutes* les données de vos objets blob.

  ![Recherche non structurée](media/storage-unstructured-search/emptySearch.png)

### <a name="full-text-search"></a>Recherche en texte intégral 

Entrez « Myopia » dans le champ **Chaîne de requête**, puis sélectionnez **Rechercher**. Lancement d’une recherche du contenu des fichiers et retour d’un sous-ensemble de ces fichiers, contenant le mot « Myopia ».

  ![Recherche non structurée](media/storage-unstructured-search/secondMyopia.png) 

### <a name="system-properties-search"></a>Recherche de propriétés système

Vous pouvez aussi créer des requêtes qui effectuent des recherches par propriétés système à l’aide du paramètre `$select`. Entrez `$select=metadata_storage_name` dans la chaîne de requête et appuyez sur Entrée, ce qui retourne uniquement ce champ précis.
    
La chaîne de requête modifie directement l’URL, donc les espaces ne sont pas autorisés. Pour rechercher dans plusieurs champs, utilisez une virgule, comme ici : `$select=metadata_storage_name,metadata_storage_path`
    
Vous pouvez uniquement utiliser le paramètre `$select` avec des champs qui ont été marqués comme récupérables lors de la définition de votre index.

  ![Recherche non structurée](media/storage-unstructured-search/metadatasearchunstructured.png) 

Vous avez maintenant terminé ce didacticiel et vous avez un ensemble de données non structurées dans lequel vous pouvez effectuer des recherches.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à rechercher des données non structurées en utilisant Recherche Azure, à savoir comment effectuer les tâches suivantes :

> [!div class="checklist"]
> * Créer un groupe de ressources
> * Créez un compte de stockage.
> * Créez un conteneur.
> * Charger des données dans votre conteneur
> * Créer un service de recherche
> * Utilisation du service de recherche pour effectuer une recherche dans votre conteneur

Suivez ce lien pour en savoir plus sur l’indexation des documents avec Recherche Azure.

> [!div class="nextstepaction"]
> [Indexation de documents dans Stockage Blob Azure avec Recherche Azure](../../search/search-howto-indexing-azure-blob-storage.md)
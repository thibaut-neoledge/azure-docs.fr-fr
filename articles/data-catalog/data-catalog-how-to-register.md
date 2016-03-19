<properties
   pageTitle="Inscription de sources de données"
   description="Article de procédure relatif à l'inscription de sources de données avec Azure Data Catalog, y compris les champs de métadonnées extraits et les sources de données prises en charge lors de l'aperçu."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="03/03/2016"
   ms.author="maroche"/>


# Inscription de sources de données

## Introduction
**Microsoft Azure Data Catalog** est un service cloud entièrement géré qui sert de système d'inscription et de détection des sources de données d'entreprise. En d'autres termes, **Microsoft Azure Data Catalog** vise essentiellement à aider les utilisateurs à détecter, comprendre et utiliser des sources de données et permet aux organisations de mieux exploiter leurs données. Et la première étape pour qu’une source de données puisse être détectée via **Microsoft Azure Data Catalog**, c’est de l’inscrire.
## Inscription des sources de données
L'inscription représente le fait d’extraire des métadonnées depuis une source de données et de les copier dans le service **Azure Data Catalog**. Les données restent à leur emplacement d’origine, toujours sous le contrôle des administrateurs et des stratégies du système actuel.

Pour inscrire une source de données, il vous suffit de lancer l’outil d’inscription des sources de données d’**Azure Data Catalog** à partir du portail **Azure Data Catalog**. Connectez-vous avec votre compte professionnel ou étudiant (identique aux identifiants de connexion d’Azure Active Directory) puis sélectionnez la source de données que vous souhaitez inscrire. Consultez le didacticiel [Prise en main d’Azure Data Catalog](data-catalog-get-started.md) pour plus d'informations détaillées.

Une fois la source de données inscrite, le catalogue effectue le suivi de son emplacement et indexe ses métadonnées. Ainsi, les utilisateurs peuvent rechercher, parcourir et détecter la source de données et utiliser son emplacement pour se connecter à l'aide de l'application ou de l'outil de leur choix.

## Sources prises en charge
Reportez-vous au [DSR Data Catalog](data-catalog-dsr.md) pour obtenir la liste des sources de données actuellement prises en charge. <br/>


## Métadonnée structurelle
Lorsque vous inscrivez une source de données, l’outil d’inscription extrait des informations sur la structure des objets que vous sélectionnez. Il s'agit des métadonnées structurelles.

Pour tous les objets, ces métadonnées structurelles incluent l’emplacement de l’objet concerné ; les utilisateurs qui découvrent les données peuvent donc utiliser ces informations pour se connecter à l’objet dans les outils clients de leur choix. Parmi les autres métadonnées structurelles, on peut citer le nom et le type d’objet, le nom d’attribut/colonne et le type de données.

## Métadonnée descriptive
En plus des métadonnées structurelles de base extraites de la source de données, l'outil d'inscription des sources de données extrait des métadonnées descriptives. Pour SQL Server Analysis Services et SQL Server Reporting Services, ces métadonnées sont extraites des propriétés de description exposées par ces services. Pour SQL Server, ce sont les valeurs fournies à l'aide de la propriété étendue ms\_description qui sont extraites. Pour la base de données Oracle, l'outil d’inscription des sources de données extrait la colonne COMMENTS de la vue ALL\_TAB\_COMMENTS.

En plus des métadonnées descriptives extraites de la source de données, les utilisateurs peuvent saisir des métadonnées descriptives à l’aide de l’outil d’inscription des sources de données. Les utilisateurs peuvent ajouter des balises et peuvent identifier des experts pour les objets en cours d'inscription. Toutes ces métadonnées descriptives sont copiées vers le service **Azure Data Catalog** ainsi que les métadonnées structurelles.

## Avec les aperçus instantanés

Par défaut, seules les métadonnées sont extraites des sources de données et copiées dans le service **Azure Data Catalog**. Toutefois, pour comprendre plus facilement une source de données, il suffit généralement de voir un exemple des données qu’elle contient.

L’outil d’inscription des sources de données d’**Azure Data Catalog** permet aux utilisateurs d’inclure un aperçu instantané des données dans chaque table et chaque vue où elle est inscrite. Si l’utilisateur choisit d’inclure les versions préliminaires lors de l’inscription, l’outil d’inscription inclut jusqu’à 20 enregistrements de chaque table et vue. Cet instantané est ensuite copié dans le catalogue avec les métadonnées structurelles et descriptives.


> [AZURE.NOTE]  Les tableaux volumineux comportant un grand nombre de colonnes peuvent contenir moins de 20 enregistrements inclus dans leur aperçu.


## Inclusion de profils de données

Tout comme l’inclusion d’aperçus peut fournir un contexte précieux aux utilisateurs recherchant des sources de données dans **Azure Data Catalog**, l’inclusion d’un profil de données peut aussi faciliter la compréhension des sources de données découvertes.

L’outil d’inscription des sources de données d’**Azure Data Catalog** permet aux utilisateurs d’inclure un profil de données pour chaque table et chaque vue qui est inscrite. Si l’utilisateur choisit d’inclure un profil de données pendant l’inscription, l’outil d’inscription inclut des statistiques agrégées sur les données de chaque table et vue, à savoir :

* le nombre de lignes et la taille des données dans l’objet ;
* la date de la dernière mise à jour des données et le schéma de l’objet ;
* le nombre d’enregistrements Null et de valeurs distinctes pour les colonnes ;
* les valeurs minimale, maximale, moyenne et d’écart type pour les colonnes.

Ces statistiques sont ensuite copiées dans le catalogue avec les métadonnées structurelles et descriptives.

> [AZURE.NOTE]  Les colonnes de texte et de date n’incluent pas de statistiques de valeurs moyennes ou d’écart type dans leur profil de données.

## Mise à jour des inscriptions

L’inscription d’une source de données rend celle-ci détectable dans **Azure Data Catalog** à l’aide des métadonnées et de l’aperçu facultatif extraits lors de l’inscription. Si la source de données doit être mise à jour dans le catalogue (par exemple, si le schéma d'un objet a changé, ou si les tables initialement exclues doivent être incluses, ou si un utilisateur souhaite mettre à jour les données incluses dans les aperçus), l'outil d'inscription des sources de données peut être réexécuté.

La réinscription d’une source de données déjà inscrite aboutit à une opération de fusion de type « upsert » : les objets existants sont actualisés, tandis que les nouveaux objets sont créés de toute pièce. Toutes les métadonnées fournies par les utilisateurs via le portail **Azure Data Catalog** sont conservées.

## Résumé
L’inscription d’une source de données auprès d’**Azure Data Catalog** facilite la découverte et la compréhension, et consiste à copier les métadonnées descriptives et structurelles de la source de données dans le service Catalog. Une fois qu’une source de données a été inscrite, elle peut ensuite être annotée, gérée et découverte à l’aide du portail **Azure Data Catalog**.

## Voir aussi
- Didacticiel [Prise en main d’Azure Data Catalog](data-catalog-get-started.md) pour des informations détaillées sur l’inscription de sources de données.

<!---HONumber=AcomDC_0309_2016-->
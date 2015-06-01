<properties
	pageTitle="Importez vos données d'apprentissage dans Azure Machine Learning Studio | Azure"
	description="Comment importer vos données d'apprentissage Azure Machine Learning Studio depuis différentes sources de données"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/21/2015"
	ms.author="garye" />


#Importez vos données d'apprentissage dans Azure Machine Learning Studio

Lorsque vous développez une solution d'analyse prédictive dans Azure Machine Learning Studio, vous effectuez l'apprentissage de votre modèle à l'aide de données représentatives de votre espace de problème. Un certain nombre d'exemples de jeux de données sont disponibles dans ML Studio et vous pouvez les utiliser à cet effet (voir [Utilisation d'exemples de jeux de données dans Azure Machine Learning Studio](machine-learning-use-sample-datasets.md)). Mais vous pouvez également importer vos propres données dans ML Studio à utiliser dans vos expériences.

[AZURE.INCLUDE [machine-learning-free-trial](../includes/machine-learning-free-trial.md)]

Pour utiliser vos propres données dans ML Studio, vous pouvez télécharger un fichier de données à l'avance depuis votre disque dur local pour créer un module de jeu de données dans votre espace de travail. Vous pouvez également accéder aux données à partir d'une des nombreuses sources en ligne pendant que votre expérience s'exécute à l'aide du module [Reader][reader] :

- Stockage d'objets blob Azure, table ou base de données SQL
- Hadoop avec HiveQL
- Une URL web avec HTTP
- Un fournisseur de flux de données

ML Studio est conçu pour travailler avec des données tabulaires ou rectangulaires, telles que les données texte délimitées ou structurées à partir d'une base de données, bien que dans certains cas les données non rectangulaires puissent être utilisées.

Il est préférable que vos données soient relativement nettoyées. Autrement dit, vous devez régler les problèmes tels que des chaînes sans guillemet avant de télécharger les données dans votre expérience. Toutefois, des modules sont disponibles dans ML Studio et vous permettent d'effectuer certaines manipulations de données dans votre expérience. En fonction des algorithmes d'apprentissage automatique que vous allez utiliser, vous devrez décider comment gérer les problèmes structurels des données tels que des valeurs manquantes et des données fragmentées. Certains modules existent pour vous aider à régler ces problèmes. Rechercher dans la section **Transformation des données** de la palette des modules ceux qui exécutent ces fonctions.

À tout moment dans votre expérience, vous pouvez voir ou télécharger les données qui sont générées par un module en cliquant avec le bouton droit sur le port de sortie. En fonction du module, différentes options de téléchargement sont disponibles. Vous pouvez également afficher les données dans votre navigateur web de ML Studio.


## Formats de données

Vous pouvez importer un certain nombre de types de données dans votre expérience, selon le mécanisme que vous utilisez pour importer les données et l'emplacement d'où elles proviennent :

- Texte brut (.txt)
- Comma-separated values (CSV) avec un en-tête (.csv) ou sans (. nh.csv)
- Tab-separated values (TSV) avec un en-tête (.tsv) ou sans (. nh.tsv)
- Table Hive
- Base de données SQL
- Valeurs OData
- Données SVMLight (.svmlight) (voir la [définition SVMLight](http://svmlight.joachims.org/) pour les informations relatives au format)
- Données Attribute Relation File Format (ARFF) (.arff) (voir la [définition ARFF](http://weka.wikispaces.com/ARFF) pour les informations relatives au format)
- Fichier zip (.zip)
- Fichier d'espace de travail ou d'objet R (.RData)

Si vous importez des données dans un format tel que ARFF qui inclut des métadonnées, ML Studio utilise ces métadonnées pour définir le titre et le type de données de chaque colonne. Si vous importez des données dans des formats tels que TSV ou CSV qui n'incluent pas ces métadonnées, ML Studio déduit le type de données pour chaque colonne en échantillonnant les données. Si les données aussi n'ont pas de titre de colonne, ML Studio fournit des noms par défaut. Vous pouvez spécifier de manière explicite ou modifier les titres et les types de données pour les colonnes à l'aide de l'[Éditeur de métadonnées][metadata-editor].
 
Voici les types de données reconnus par ML Studio :

- String
- Integer
- Double
- Boolean
- DateTime
- TimeSpan

ML Studio utilise un type de données interne appelé *Table de données* pour passer des données entre les modules. Vous pouvez convertir de manière explicite vos données dans un format de table de données à l'aide du module [Convertir en jeu de données][convert-to-dataset]. Tout module qui accepte d'autres formats que la table de données convertira silencieusement les données de la table de données avant de les passer au module suivant. Au besoin, vous pouvez convertir à nouveau le format de la table de données au format CSV, TSV, ARFF ou SVMLight à l'aide d'autres modules de conversion. Recherchez dans la section **Conversion des formats de données** de la palette des modules ceux qui exécutent ces fonctions.


## Importation d'un fichier de données local

Vous pouvez télécharger des données à partir d'un disque dur local en procédant comme suit :

1. Cliquez sur **+NOUVEAU** en bas de la fenêtre de ML Studio.
2. Sélectionnez **JEU DE DONNÉES** et **DEPUIS UN FICHIER LOCAL**.
3. Dans la boîte de dialogue **Télécharger un nouveau jeu de données**, recherchez le fichier que vous souhaitez télécharger
4. Saisissez un nom, identifiez le type de données puis saisissez éventuellement une description. Une description est recommandée : elle vous permet d'enregistrer des caractéristiques relatives aux données que vous souhaitez mémoriser pour une utilisation future.
5. La case à cocher **Il s'agit de la nouvelle version d'un jeu de donnée existant** vous permet de mettre à jour un jeu de données existant avec de nouvelles données. Cliquez simplement sur cette case à cocher, puis saisissez le nom d'un jeu de données existant.

Pendant le téléchargement, vous verrez un message indiquant que votre fichier est en cours de téléchargement. Le temps de téléchargement dépend de la taille de vos données et de la vitesse de votre connexion au service. Si vous savez que le fichier prendra du temps, vous pouvez faire autre chose dans ML Studio en attendant. Cependant, la fermeture du navigateur entraîne l'échec du téléchargement.

Une fois que vos données sont téléchargées, elles sont stockées dans un module de jeu de données et sont disponibles pour n'importe quelle expérience dans votre espace de travail. Vous trouverez le jeu de données ainsi que tous les exemples de jeu de données préchargés dans la liste des **Jeux de données enregistrés** dans la palette des modules lorsque vous éditez une expérience.


## Accès aux données en ligne avec le module Reader

Pendant que votre expérience s'exécute, vous pouvez également accéder aux données à partir de plusieurs sources en ligne à l'aide du module [Reader][reader]. Étant donné que ces données sont accessibles pendant que votre expérience s'exécute, elles ne sont disponibles que dans une seule expérience (par opposition aux modules de jeu de données qui sont disponibles pour n'importe quelle expérience dans votre espace de travail).

Après avoir ajouté le module [Reader][reader] à votre expérience, sélectionnez la **Source de données** puis fournissez les informations d'accès à l'aide des paramètres du module. Par exemple, si vous sélectionnez **URL web via HTTP**, vous fournissez l'URL source et le format de données. Si vous accédez à vos données depuis le stockage Azure ou HDInsight (à l'aide d'une requête Hive), vous fournissez les informations de compte appropriées et l'emplacement des données.

> [AZURE.NOTE]Cet article fournit des informations générales sur le module [Reader][reader]. Pour plus d'informations sur les types de données auxquelles vous pouvez accéder, les formats, les paramètres et les réponses aux questions courantes, consultez la rubrique de référence du module pour le module [Reader][reader].


### Obtention de données depuis Azure

Vous pouvez importer des données à partir de trois sources Azure :

- **Stockage d'objets blob Azure** : si vous utilisez le format ARFF pour le stockage, les colonnes sont mappées à l'aide des métadonnées du titre. Si vous utilisez les formats TSV ou CSV, les mappages sont déduits par échantillonnage des données de la colonne. 
- **Stockage de table Azure** : le module [Reader][reader] analyse vos données pour identifier les types de données de colonne. Si vos données sont relativement homogènes et prévisibles, vous pouvez limiter le nombre de lignes qui sont analysées.
- **Base de données SQL Azure** : le module [Reader][reader] utilise l'API cliente SQL Azure Transact pour importer des données à l'aide d'une requête de base de données que vous fournissez.

Pour le stockage d'objets blob et de tables, fournissez une URI de signature d'accès partagé (URI SAS) ou les informations de compte de stockage Azure pour fournir un accès aux données. Pour une base de données SQL Azure, vous fournissez votre base de données et les informations de votre compte, ainsi qu'une requête de base de données qui identifie les données que vous souhaitez importer.

### Obtention de données depuis le web

Vous pouvez utiliser le module [Reader][reader] pour lire des données à partir d'un site web ou d'un site FTP. Vous devez fournir :

- L'adresse URL HTTP complète d'un fichier
- Le format de données du fichier (CSV, TSV, ARFF ou SvmLight)
- Pour les fichiers CSV ou TSV, indiquez si la première ligne du fichier est un en-tête

### Obtention de données depuis Hadoop

Vous pouvez utiliser le module [Reader][reader] pour lire les données depuis un stockage distribué à l'aide du langage de requête HiveQL. Vous devez spécifier la requête de base de données Hive et fournir des informations d'accès utilisateur sur le serveur HCatalog. Vous devez également spécifier si les données sont stockées dans un système de fichiers distribué de Hadoop (HDFS) ou dans Azure et, le cas échéant, les informations du compte Azure

### Obtention de données depuis un fournisseur de flux de données

En spécifiant une URL OData, vous pouvez lire directement à partir d'un fournisseur de flux de données. Vous devez fournir l'URL source et le type de contenu de données.


## Enregistrement des données de votre expérience


Il se peut que vous ayez parfois besoin d'obtenir un résultat intermédiaire à partir d'une expérience et de l'utiliser dans le cadre d'une autre expérience. Pour ce faire :

1. Cliquez avec le bouton droit sur la sortie du module que vous souhaitez enregistrer en tant que jeu de données.

2. Cliquez sur **Enregistrer comme jeu de données**.

3. Lorsque vous y êtes invité, saisissez un nom et une description qui vous permet d'identifier facilement le jeu de données.

4. Cliquez sur la coche **OK**.

Lorsque l'enregistrement est terminé, le jeu de données sera disponible pour être utilisé dans n'importe quelle expérience dans votre espace de travail. Vous pouvez le trouver dans la liste **Jeux de données enregistrés** dans la palette des modules.


<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!--HONumber=54-->
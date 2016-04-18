<properties
   pageTitle="Forum Aux Questions Azure Data Catalog | Microsoft Azure"
   description="Forum Aux Questions sur Azure Data Catalog, y compris sur les fonctionnalités de détection de source de données, d’annotation et de gestion."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="03/31/2016"
   ms.author="maroche"/>

# Forum Aux Questions Azure Data Catalog

Cet article fournit des réponses aux questions fréquemment posées sur le service Microsoft **Azure Data Catalog**.

## Q : Qu’est ce qu’Azure Data Catalog ?

R : Microsoft Azure Data Catalog est un service entièrement géré hébergé dans le cloud Microsoft Azure qui fait office de système d'enregistrement et de détection des sources de données d'entreprise. Azure Data Catalog fournit des fonctionnalités qui permettent à tous les utilisateurs (analystes, scientifiques, développeurs) d’enregistrer, de découvrir, de comprendre et d’utiliser des sources de données.

## Q: Quels sont les problèmes des clients qu’Azure Data Catalog résout ?

Azure Data Catalog résout les problèmes de découverte des source de données et des « données cachées » en permettant aux utilisateurs de découvrir et de comprendre les sources de données d'entreprise.

## Q: Qui sont les publics cibles d’Azure Data Catalog ?

Azure Data Catalog fournit des fonctionnalités pour les utilisateurs, techniciens ou non, notamment :

- les développeurs de données, les professionnels du décisionnel et de l'analyse : chargés de produire des données et du contenu d’analyse que d’autres utiliseront ;
-	les gestionnaires de données : ceux qui disposent de connaissances sur les données, ce qu’elles signifient, comment elles sont censées être utilisées et dans quel but ;
- les consommateurs de données : ceux qui doivent être en mesure de découvrir, de comprendre et de se connecter facilement aux données nécessaires pour effectuer leur travail à l'aide de l'outil de leur choix ;
- l’équipe informatique centrale : ceux qui doivent rendre détectables des centaines de sources de données pour les utilisateurs professionnels et qui ont besoin de superviser l'utilisation de données et les personnes utilisant ces données.

## Q : Quelle est la disponibilité d’Azure Data Catalog selon les régions ?

Les services Azure Data Catalog sont actuellement disponibles dans les centres de données suivants :

- Ouest des États-Unis
- Est des États-Unis
- Europe de l'Ouest
- Europe du Nord
- Est de l’Australie
- Asie du Sud-Est

## Q : Quelles sont les limites du nombre de ressources de données dans Azure Data Catalog ?

L’édition gratuite d’Azure Data Catalog est limitée à 5 000 ressources de données inscrites.

L'édition Standard d’Azure Data Catalog prend en charge jusqu'à 100 000 ressources de données inscrites.

## Q : Quels sont les types de sources et de ressources de données pris en charge ?

Reportez-vous au [DSR Data Catalog](data-catalog-dsr.md) pour obtenir la liste des sources de données actuellement prises en charge.

## Q : Comment demander la prise en charge d’une autre source de données ?

Les demandes de fonctionnalités et d’autres commentaires peuvent être envoyés dans le [forum Azure Data Catalog](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## Q : Comment effectuer mes premiers pas avec Azure Data Factory ?

Le mieux est de commencer par suivre les instructions du didacticiel [Prise en main de Data Catalog](../data-catalog-get-started/). Cet article présente de bout en bout les fonctionnalités intégrées au service.

## Q : Comment inscrire mes données ?

Pour inscrire vos données dans Azure Data Catalog, lancez l'outil d'inscription d’Azure Data Catalog à partir de la zone « Publier » du portail Azure Data Catalog. Dans l'application de publication Azure Data Catalog, connectez-vous à l’aide des mêmes informations d'identification que celles que vous utilisez pour accéder au portail Azure Data Catalog, puis sélectionnez la source de données et les ressources spécifiques que vous souhaitez inscrire.

## Q : Quelles propriétés sont extraites pour les ressources de données qui sont inscrites ?

Les propriétés spécifiques diffèrent selon la source de données, mais en général le service de publication Azure Data Catalog extrait les informations suivantes :

- Nom de la ressource
- Type de ressource
- Description de la ressource
- Noms de l’attribut/de la colonne
- Types de données de l'attribut/de la colonne
- Description de l’attribut/de la colonne

> [AZURE.IMPORTANT] L’inscription de ressources de données auprès d’Azure Data Catalog n’a pas pour effet de déplacer ou de copier vos données dans le cloud. L’inscription de ressources à partir d'une source de données copie les métadonnées des ressources vers Azure, mais les données restent dans l'emplacement de la source de données existante. La seule exception à cette règle est quand un utilisateur décide de charger des enregistrements ou un profil de données de la version préliminaire lors de l’inscription de ressources. Lors de l’inclusion d’une version préliminaire, jusqu’à 20 enregistrements sont copiés à partir de chaque ressource et stockés sous forme d’instantané dans Azure Data Catalog. Lorsque vous incluez un profil de données, les informations d’agrégation (telles que la taille des tables, les valeurs Null en pourcentage par colonne et les valeurs minimales, maximales et moyennes pour les colonnes) sont calculées et incluses dans les métadonnées stockées dans le catalogue.

<br/>

> [AZURE.NOTE] Pour les sources de données telles que SQL Server Analysis Services qui ont une propriété **Description** de première classe, l’application de publication Azure Data Catalog extrait la valeur de cette propriété. Pour les bases de données relationnelles SQL Server, qui ne possèdent pas de propriété **Description** de première classe, l’application de publication Azure Data Catalog extrait la valeur de la propriété étendue ms\_description pour les objets et les colonnes. Pour plus d’informations, consultez la page TechNet [Utilisation de propriétés étendues sur les objets de base de données](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx).

## Q : Combien de temps faut-il pour que les ressources nouvellement inscrites apparaissent dans Azure Data Catalog ?

Après avoir inscrit des ressources auprès d’Azure Data Catalog, il peut s’écouler un délai de 5 à 10 secondes avant qu’elles apparaissent dans le portail Azure Data Catalog.

## Q : Comment annoter et enrichir les métadonnées pour mes ressources de données inscrites ?

La méthode la plus simple pour fournir des métadonnées pour des ressources inscrites consiste à sélectionner la ressource dans le portail Azure Data Catalog, puis à entrer les valeurs des métadonnées dans le volet des propriétés ou du schéma de l’objet sélectionné.

Vous pouvez également fournir des métadonnées, telles que des experts et des balises, pendant le processus d'inscription. Les valeurs fournies dans le service de publication Azure Data Catalog s’appliquent à toutes les ressources inscrites à ce moment-là. Pour afficher les objets récemment inscrits dans le portail pour des annotations supplémentaires, sélectionnez le bouton **Afficher le portail** dans le dernier écran de l’application de publication Azure Data Catalog.

## Q : Comment supprimer mes objets de données inscrits ?

Vous pouvez supprimer un objet d’Azure Data Catalog en sélectionnant l’objet dans le portail, puis en cliquant sur le bouton **Supprimer**. Les métadonnées de l’objet sont supprimées d’Azure Data Catalog, mais la source de données sous-jacente réelle n’en est pas affectée.

## Q : Qu'est-ce qu’un expert ?

Un expert est une personne qui a un point de vue éclairé sur un objet de données. Un objet peut avoir plusieurs experts. Il n’est pas nécessaire que l’expert soit le « propriétaire » d'un objet ; l'expert est simplement une personne qui sait comment les données peuvent et doivent être utilisées.

## Q : Comment partager des informations avec l'équipe Azure Data Catalog si je rencontre des problèmes ?

Utilisez le forum Azure Data Catalog pour signaler des problèmes, partager des informations et poser des questions. Le forum se trouve à l’adresse http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409

##Q : Azure Data Catalog fonctionne-t-il avec cette autre source de données qui m'intéresse ?
Nous travaillons activement à l’ajout de sources de données supplémentaires à Azure Data Catalog. S'il existe une source de données que vous souhaitez voir prise en charge, veuillez la suggérer (ou faites part de votre accord si elle a déjà été suggérée) dans le forum [Azure Data Catalog](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## Q : Comment Azure Data Catalog est-il lié au catalogue de données dans Power BI pour Office 365 ?

Vous pouvez considérer Azure Data Catalog comme une évolution de Data Catalog. Le service Azure Data Catalog offre des fonctionnalités de découverte et de publication de sources de données similaires, mais il est axé sur des scénarios plus larges et ne dépend pas d’Office 365. Peu après la mise à disposition générale d’Azure Data Catalog, les deux catalogues fusionneront pour ne former qu’un seul service.

## Q : Quelles sont les autorisations dont a besoin un utilisateur pour inscrire des ressources auprès d’Azure Data Catalog ?

L’utilisateur exécutant l’outil de référencement pour Azure Data Catalog a besoin d’autorisations sur la source de données qui lui permettront de lire les métadonnées de la source. Si l'utilisateur choisit d’inclure également un aperçu, il doit aussi disposer des autorisations qui lui permettent de lire les données à partir des objets en cours d'inscription.

## Q : Azure Data Catalog sera-t-il également disponible pour un déploiement local ?

Azure Data Catalog est un service cloud qui peut fonctionner avec des sources de données cloud et locales, offrant ainsi une solution de détection de sources de données hybrides. Aucune version du service Azure Data Catalog s’exécutant localement n’est actuellement prévue.

##Q : Est-il possible d’extraire davantage de métadonnées/des métadonnées plus riches à partir de sources de données que nous inscrivons ?

Nous travaillons activement au développement des fonctionnalités d’Azure Data Catalog. S’il existe des métadonnées supplémentaires que vous aimeriez voir extraites à partir de la source de données pendant l’inscription, suggérez-les (ou votez en leur faveur si elles ont déjà été suggérées) dans le [forum Azure Data Catalog](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Par la suite, nous autoriserons des tiers à ajouter de nouveaux types de sources de données via une API d'extensibilité.

## Q : Comment restreindre la visibilité des ressources de données inscrites, afin que seules certaines personnes puissent les découvrir ?

R : Sélectionnez les ressources de données dans Azure Data Catalog, puis cliquez sur le bouton « Appropriation ». Les propriétaires de ressources de données dans Azure Data Catalog peuvent modifier les paramètres de visibilité pour autoriser tous les utilisateurs du catalogue à découvrir les ressources lui appartenant, ou pour limiter la visibilité à des utilisateurs spécifiques.

## Q : comment mettre à jour l'inscription d’une ressource de données pour que les modifications apportées dans la source de données soient reflétées dans le catalogue ?

R : Pour mettre à jour les métadonnées pour les ressources de données qui sont déjà inscrites dans le catalogue, réinscrivez simplement la source de données qui contient les ressources. Les modifications apportées à la source de données, telles que l’ajout ou la suppression de colonnes de tables ou de vues, seront actualisées dans le catalogue, mais les annotations fournies par les utilisateurs seront conservées.

## Q : Je n’ai pas trouvé de réponse à ma question ici. Que dois-je faire ?

Rendez-vous sur le [forum Azure Data Catalog](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Les questions qui y sont posées se retrouveront ici.

<!---HONumber=AcomDC_0406_2016-->
<properties
   pageTitle="Concepts de développeur Azure Data Catalog"
   description="Présentation des concepts clés du modèle conceptuel d’Azure Data Catalog, tels qu’ils sont exposés via l’API REST Catalog."
   services="data-catalog"
   documentationCenter=""
   authors="dvana"
   manager="mblythe"
   editor=""
   tags=""/>
<tags 
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="02/03/2016"
   ms.author="derrickv"/>

# Concepts de développeur Azure Data Catalog

**Microsoft Azure Data Catalog** est un service cloud entièrement géré qui fournit des fonctionnalités de détection de source de données et de métadonnées de source de données de crowdsourcing. Les développeurs peuvent utiliser le service via son API REST. Il est important de comprendre les concepts du service pour l’intégrer efficacement à **Azure Data Catalog**.

## Concepts clés

Le modèle conceptuel **Azure Data Catalog** repose sur quatre concepts clés : le **catalogue**, les **utilisateurs**, les **ressources** et les **annotations**.

![concept][1]

*Figure 1 : modèle conceptuel simplifié d’Azure Data Catalog*

### Catalogue

Un **catalogue** est le conteneur de niveau supérieur pour toutes les métadonnées stockées par une organisation. Chaque compte Azure ne peut avoir qu’un **catalogue**. Les catalogues sont liés à un abonnement Azure, mais seul un **catalogue** peut être créé pour un compte Azure donné, même si un compte peut avoir plusieurs abonnements.

Un catalogue contient des **utilisateurs** et des **ressources**.

### Utilisateurs

Les utilisateurs sont des principaux de sécurité qui ont des autorisations pour effectuer des actions (recherche dans le catalogue, ajout, modification ou suppression d’éléments, etc.) dans le catalogue.

Un utilisateur peut avoir plusieurs rôles différents. Pour plus d'informations sur les rôles, consultez la section Rôles et autorisations.

Des utilisateurs individuels et des groupes de sécurité peuvent être ajoutés.

Azure Data Catalog utilise Azure Active Directory pour la gestion des identités et des accès. Chaque utilisateur de catalogue doit être un membre de l’Active Directory du compte.

### Éléments multimédias

Un **catalogue** contient des ressources de données . Les **ressources** représentent l’unité de granularité gérée par le catalogue.

La granularité d'une ressource varie selon la source de données. Pour SQL Server ou une base de données Oracle, une ressource peut être une table ou un affichage. Pour SQL Server Analysis Services, une ressource peut être une mesure, une dimension ou un indicateur de performance clé (KPI). Pour SQL Server Reporting Services, une ressource est un rapport.

Une **ressource** est l’élément que vous ajoutez ou supprimez d’un catalogue. C’est l'unité de résultat que vous récupérez lors d’une **recherche**.

Une **ressource** est constituée de son nom, de son emplacement et de son type ainsi que d’annotations apportant une description supplémentaire.

### Annotations

Les annotations sont des éléments qui représentent des métadonnées sur les ressources.

La description, les balises, le schéma, la documentation, etc. sont des exemples d’annotations. Une liste complète des types de ressources et des types d’annotations est disponible dans la section de modèle d’objet de ressource.

## Annotations de crowdsourcing et perspective de l'utilisateur (multiplicité d'opinions)

Un aspect clé d’Azure Data Catalog est la manière dont il prend en charge le crowdsourcing des métadonnées dans le système. Par opposition à une approche wiki, où il n’y a qu’un seul avis et le dernier à écrire gagne, le modèle Azure Data Catalog autorise plusieurs avis parallèles dans le système.

Cette approche reflète la réalité des données d'entreprise où les utilisateurs peuvent avoir différentes perspectives sur une ressource donnée :

-	un administrateur de base de données peut fournir plus d'informations sur les contrats de niveau de service ou la fenêtre de traitement disponible pour les opérations ETL en bloc ;
-	un gestionnaire de données peut fournir des informations sur les processus d'entreprise auxquels s'applique la ressource ou les classifications appliquées par l'entreprise ;
-	un analyste financier peut fournir des informations sur l’utilisation des données lors des tâches de création de rapports de fin de période.

Pour prendre en charge cet exemple, chaque utilisateur (l'administrateur de base de données, le gestionnaire de données et l'analyste) peut ajouter une description à une table enregistrée dans le catalogue. Toutes les descriptions sont conservées dans le système et affichées dans le portail Azure Data Catalog.

Ce modèle est appliqué à la plupart des éléments dans le modèle d’objet. Voilà pourquoi les types d’objets dans la charge utile JSON sont souvent des tableaux, pour des propriétés où vous pouvez attendre un singleton.

Par exemple, un tableau d'objets de description se trouve sous la racine de ressources. La propriété du tableau s’appelle « descriptions ». Un objet de description possède trois propriétés : description, balises et friendlyName. Selon ce modèle, chaque utilisateur qui saisit une ou plusieurs de ces propriétés obtient un objet de description créé avec les valeurs fournies.

L'expérience utilisateur permet alors l’affichage de la combinaison. Il existe trois modèles d'affichage différents.

-	Le plus simple est « Tout afficher ». Dans ce modèle, tous les objets sont affichés dans une sorte d’affichage de liste. Voilà ce que fait l’expérience utilisateur du portail Azure Data Catalog comme description.
-	Un autre modèle est « Fusionner ». Dans ce modèle, toutes les valeurs des différents utilisateurs sont fusionnées et les doublons supprimés. Les propriétés des balises et des experts sont des exemples de ce modèle dans l’expérience utilisateur du portail Azure Data Catalog.
-	Un troisième modèle est la « règle de Thomas ». Dans ce modèle, seule la dernière valeur saisie est indiquée. Le nom convivial est un exemple de ce modèle.

## Modèle d'objet de ressource

Comme décrit dans la section Concepts clés, le modèle d’objet **Azure Data Catalog** comprend des éléments pouvant être des ressources ou des annotations. Les éléments ont des propriétés, qui peuvent être facultatives ou obligatoires. Certaines propriétés s'appliquent à tous les éléments. Certaines propriétés s'appliquent à toutes les ressources. Certaines propriétés s'appliquent uniquement à des types de ressources spécifiques.

### Propriétés communes

Ces propriétés s'appliquent à tous les types de ressources racines et tous les types d'annotations.

> [AZURE.NOTE] Les propriétés dont les noms commencent par deux traits de soulignement sont des types système.

<table><tr><td><b>Nom de la propriété</b></td><td><b>Type de données</b></td><td><b>Commentaires</b></td></tr><tr><td>modifiedTime</td><td>DateTime</td><td>L’heure de dernière modification de la racine. Cela est défini par le client. (Le serveur ne gère pas cette valeur).</td></tr><tr><td>__id</td><td>String</td><td>ID de l'élément (lecture seule). Cet ID garantit l'unicité de la ressource dans un catalogue.</td></tr><tr><td>__type</td><td>String</td><td>Le type de ressource (lecture seule)</td></tr><tr><td>__creatorId</td><td>Chaîne</td><td>Une chaîne utilisée par le créateur de la ressource pour son identification unique. </td></tr></table>

### Propriétés de racine communes

Ces propriétés s'appliquent à tous les types de ressources racines.

<table><tr><td><b>Nom de la propriété</b></td><td><b>Type de données</b></td><td><b>Commentaires</b></td></tr><tr><td>name</td><td>Chaîne</td><td>Un nom dérivé des informations d’emplacement de source de données</td></tr><tr><td>dsl</td><td>Emplacement de la source de données</td><td>Décrit la source de données de manière unique et est un des identificateurs de la ressource. (Voir la section identité double). La structure du dsl varie selon le type de source.</td></tr><tr><td>dataSource</td><td>DataSourceInfo</td><td>Plus de détails sur le type de ressource.</td></tr><tr><td>lastRegisteredBy</td><td>SecurityPrincipal</td><td>Décrit l'utilisateur qui a enregistré cette ressource le plus récemment. Contient l’ID unique de l'utilisateur (l'UPN) ainsi qu'un nom complet (nom et prénom).</td></tr><tr><td>lastRegisteredTime</td><td>dateTime</td><td>La dernière fois que cette ressource a été enregistrée dans le catalogue.</td></tr><tr><td>containerId</td><td>String</td><td>ID de la ressource de conteneur pour la source de données. Cette propriété n'est pas prise en charge pour le type Conteneur.</td></tr></table>

### Types de ressources racines

Les types de ressources racines sont des types qui représentent les différents types de ressources de données pouvant être enregistrés dans le catalogue.

<table><tr><td><b>Type de ressource</b></td><td><b>Propriétés supplémentaires</b></td><td><b>Type de données</b></td><td><b>Commentaires</b></td></tr><tr><td>Table</td><td></td><td></td><td>Une table représente des données tabulaires. Cela inclut une table SQL, un affichage SQL, une table tabulaire Analysis&#160;Services, une dimension multidimensionnelle Analysis&#160;Services, une table Oracle, etc.   </td></tr><tr><td>Measure</td><td></td><td></td><td>Ce type représente une mesure Analysis&#160;Services.</td></tr><tr><td></td><td>Measure</td><td>des colonnes</td><td>Métadonnées décrivant la mesure</td></tr><tr><td></td><td>isCalculated </td><td>Boolean</td><td>Indique si la mesure est calculée ou non.</td></tr><tr><td></td><td>measureGroup</td><td>Chaîne</td><td>Conteneur physique de mesure</td></tr><tr><td></td><td>goalExpression</td><td>Chaîne</td><td>Une expression numérique MDX ou un calcul qui retourne la valeur cible de l'indicateur de performance clé.</td></tr><tr><td></td><td>valueExpression</td><td>Chaîne</td><td>Une expression numérique MDX qui retourne la valeur réelle de l'indicateur de performance clé.</td></tr><tr><td></td><td>statusExpression</td><td>Chaîne</td><td>Une expression MDX qui représente l'état de l'indicateur de performance clé à un point spécifié dans le temps.</td></tr><tr><td></td><td>trendExpression</td><td>String</td><td>Une expression MDX qui évalue la valeur de l’indicateur de performance clé au fil du temps. La tendance peut être n'importe quel critère de temps utile dans un contexte d’entreprise spécifique.</td></tr><tr><td></td><td>measureGroup</td><td>Chaîne</td><td>conteneur physique de mesure</td></tr><tr><td>Rapport</td><td></td><td></td><td>Ce type représente un rapport SQL&#160;Server Reporting&#160;Services </td></tr><tr><td></td><td>CreatedBy</td><td>Chaîne</td><td></td></tr><tr><td></td><td>CreatedDate</td><td>String</td><td></td></tr><tr><td>Conteneur</td><td></td><td></td><td>Ce type représente un conteneur d'autres ressources telles qu'une base de données SQL, un conteneur d'objets Blob Azure ou un modèle Analysis Services.</td></tr></table>

### Types d'annotation

Les types d'annotation représentent des types de métadonnées qui peuvent être attribuées à d'autres types dans le catalogue.

<table><tr><td><b>Type d'annotation</b></td><td><b>Propriétés supplémentaires</b></td><td><b>Type de données</b></td><td><b>Commentaires</b></td></tr><tr><td>Description</td><td></td><td></td><td>Chaque utilisateur du système peut ajouter ses propres balises et descriptions. Seul cet utilisateur peut modifier l'objet Description. (Les propriétaires des ressources et les administrateurs peuvent supprimer l'objet de description mais pas le modifier). Le système les conserve séparément. Il y a donc un tableau des descriptions de chaque ressource (un pour chaque utilisateur qui a contribué à la ressource, en plus d’un éventuel tableau qui contient des informations dérivées de la source de données).</td></tr><tr><td></td><td>friendlyName</td><td>string</td><td>Un nom convivial qui peut être utilisé au lieu du nom dérivé de la source de données. Cela est utile pour l'affichage et la recherche.</td></tr><tr><td></td><td>tags</td><td>string[]</td><td>Un tableau de balises pour la ressource</td></tr><tr><td></td><td>description</td><td>string</td><td>une brève description de la ressource (2 ou 3&#160;lignes)</td></tr><tr><td>Schéma</td><td></td><td></td><td>Le schéma décrit la structure des données. Il répertorie les noms, les types d'attribut (colonne, attribut, champ, etc.), ainsi que d’autres métadonnées. Ces informations sont issues de la source de données. Il existe généralement un élément de schéma sur une ressource.</td></tr><tr><td></td><td>colonnes</td><td>Column[]</td><td>Un tableau d'objets de colonne. Ils décrivent la colonne avec des informations issues de la source de données.</td></tr><tr><td>SchemaDescription</td><td></td><td></td><td>Cet élément contient une description et un ensemble de balises pour chaque attribut défini dans le schéma. Chaque utilisateur du système peut ajouter ses propres balises et descriptions. Seul cet utilisateur peut modifier l’objet Description. (Les propriétaires des ressources et les administrateurs peuvent supprimer l’objet SchemaDescription mais pas le modifier). Le système les conserve séparément. Il y a donc un tableau d’objets SchemaDescription pour chaque ressource (un pour chaque utilisateur qui a contribué aux attributs, en plus d’un éventuel tableau qui contient des informations dérivées de la source de données). Le SchemaAttributes est faiblement lié au schéma, il peut donc être désynchronisé. Cela signifie que SchemaDescription peut décrire des colonnes qui n’existent plus dans le schéma ou qu’il ne parvient pas à faire référencer une nouvelle colonne récemment ajoutée. Il revient à l'enregistreur de le synchroniser. La source de données peut également avoir des informations de description. Il s'agit d'un objet schemaDescription supplémentaire, créé lors de l'exécution de l'outil.</td></tr><tr><td></td><td>columnDescriptions</td><td>ColumnDescription[]</td><td>Un tableau de ColumnDescriptions qui décrit les colonnes dans le schéma. </td></tr><tr><td>Expert</td><td></td><td></td><td>Cet élément contient une liste d'utilisateurs considérés comme des experts dans le jeu de données. Les avis d'experts (c'est-à-dire des descriptions) sont propagés vers le haut de l'expérience utilisateur lors de l'affichage des descriptions. Chaque utilisateur peut spécifier sa propre liste d'experts. Seul cet utilisateur peut modifier l’objet Experts. (Les propriétaires des ressources et les administrateurs peuvent supprimer l'objet Experts mais pas le modifier).</td></tr><tr><td></td><td>experts</td><td>string[]</td><td>Tableau d'adresses de messagerie.</td></tr><tr><td>VERSION PRÉLIMINAIRE</td><td></td><td></td><td>L'aperçu contient un instantané des 20&#160;premières lignes de données de la ressource. L’aperçu n’est utile que pour certains types de ressources (c’est à dire qu’il est pertinent pour Table, mais pas pour Measure).</td></tr><tr><td></td><td>preview</td><td>object[]</td><td>Tableau d'objets qui représentent une colonne. Chaque objet possède un mappage de propriété vers une colonne avec une valeur pour cette colonne pour la ligne.</td></tr>
<tr><td>AccessInstruction</td><td></td><td></td><td>Contient des informations sur comment demander l’accès à la source de données. Ces informations correspondent à ce qui est affiché dans le champ «&#160;Demande d’accès&#160;» dans le portail de Catalog.</td></tr>
<tr><td></td><td>mimeType</td><td>string</td><td>Le type mime du contenu.</td></tr>
<tr><td></td><td>Contenu</td><td>string</td><td>Les instructions pour accéder à cette ressource de données. Il peut s'agir d'une URL, une adresse de messagerie ou un ensemble d'instructions.</td></tr>

<tr><td>TableDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>numberOfRows</td></td><td>int</td><td>Le nombre de lignes dans le jeu de données</td></tr>
<tr><td></td><td>size</td><td>long</td><td>La taille en octets du jeu de données.  </td></tr>
<tr><td></td><td>schemaModifiedTime</td><td>string</td><td>L'heure de dernière modification du schéma</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>string</td><td>L'heure de dernière modification du jeu de données (données ajoutées, modifiées ou supprimées)</td></tr>

<tr><td>ColumnsDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>colonnes</td></td><td>ColumnDataProfile[]</td><td>Le nombre de lignes dans le jeu de données</td></tr>

<tr><td>Documentation</td><td></td><td></td><td>Une seule documentation peut être associée à une ressource donnée.</td></tr>
<tr><td></td><td>mimeType</td><td>string</td><td>Le type mime du contenu.</td></tr>
<tr><td></td><td>Contenu</td><td>string</td><td>Le contenu de la documentation.</td></tr>


</table>

### Types courants

Les types courants peuvent être utilisés comme les types de propriétés, mais ne sont pas des éléments.

<table><tr><td><b>Type courant</b></td><td><b>Propriétés</b></td><td><b>Type de données</b></td><td><b>Commentaires</b></td></tr><tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr><tr><td></td><td>sourceType</td><td>string</td><td>Décrit le type de source de données, par exemple, SQL&#160;Server, base de données Oracle, etc.  </td></tr><tr><td></td><td>objectType</td><td>string</td><td>Décrit le type d'objet dans la source de données, par exemple, Table, affichage SQL&#160;Server.</td></tr><tr><td></td><td>formatType</td><td>string</td><td>Décrit la structure des données. Les valeurs actuelles sont structurées ou non structurées.</td></tr><tr><td>SecurityPrincipal</td><td></td><td></td><td></td></tr><tr><td></td><td>upn</td><td>string</td><td>Adresse de messagerie unique de l'utilisateur.</td></tr><tr><td></td><td>firstName</td><td>string</td><td>Prénom de l'utilisateur (à des fins d'affichage).</td></tr><tr><td></td><td>lastName</td><td>string</td><td>Nom de l'utilisateur (à des fins d'affichage).</td></tr><tr><td>des colonnes</td><td></td><td></td><td></td></tr><tr><td></td><td>name</td><td>string</td><td>Nom de la colonne ou de l'attribut.</td></tr><tr><td></td><td>type</td><td>string</td><td>type de données de la colonne ou de l'attribut. Les types autorisés dépendent du sourceType de données de la ressource. Seul un sous-ensemble des types est pris en charge.</td></tr><tr><td></td><td>maxLength</td><td>int</td><td>La longueur maximale autorisée pour la colonne ou l'attribut. Dérivé de la source de données. Applicable uniquement à certains types de sources.</td></tr><tr><td></td><td>Precision</td><td>byte</td><td>La précision de la colonne ou de l'attribut. Dérivé de la source de données. Applicable uniquement à certains types de sources.</td></tr><tr><td></td><td>isNullable</td><td>Boolean</td><td>Si la colonne est autorisée à avoir une valeur null ou non. Dérivé de la source de données. Applicable uniquement à certains types de sources.</td></tr><tr><td></td><td>expression</td><td>string</td><td>Si la valeur est une colonne calculée, ce champ contient l'expression qui exprime la valeur. Dérivé de la source de données. Applicable uniquement à certains types de sources.</td></tr><tr><td></td><td>defaultValue</td><td>objet</td><td>La valeur par défaut insérée en l’absence de spécification dans l'instruction insert pour l'objet. Dérivé de la source de données. Applicable uniquement à certains types de sources.</td>

</tr><tr><td>ColumnDescription</td><td></td><td></td><td></td></tr>
<tr><td></td><td>tags</td><td>string[]</td><td>Un tableau de balises qui décrivent la colonne.</td></tr>
<tr><td></td><td>description</td><td>string</td><td>Une description qui décrit la colonne.</td></tr><tr><td></td><td>columnName</td><td>string</td><td>Le nom de la colonne que désigne cette information.</td></tr>

</tr><tr><td>ColumnDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName </td><td>string</td><td>Nom de la colonne</td></tr>
<tr><td></td><td>type </td><td>string</td><td>Type de la colonne</td></tr>
<tr><td></td><td>min </td><td>string</td><td>Valeur minimale dans le jeu de données</td></tr>
<tr><td></td><td>max </td><td>string</td><td>Valeur maximale dans le jeu de données</td></tr>
<tr><td></td><td>avg </td><td>double</td><td>Valeur moyenne dans le jeu de données</td></tr>
<tr><td></td><td>stdev </td><td>double</td><td>Écart type pour le jeu de données</td></tr>
<tr><td></td><td>nullCount </td><td>int</td><td>Nombre de valeurs null dans le jeu de données</td></tr>
<tr><td></td><td>distinctCount  </td><td>int</td><td>Nombre de valeurs distinctes dans le jeu de données</td></tr>



</table>

## Rôles et autorisations

Microsoft Azure Data Catalog offre des fonctionnalités d'autorisation pour les opérations CRUD sur les ressources et les annotations.

## Concepts clés

Azure Data Catalog utilise deux mécanismes d'autorisation :

- Autorisation par rôle
- Autorisation basée sur les autorisations

### contrôleur

Il existe 3 rôles : **administrateur**, **propriétaire** et **collaborateur**. Chaque rôle a une portée et des droits résumés dans le tableau suivant.

<table><tr><td><b>Rôle</b></td><td><b>Portée</b></td><td><b>Droits</b></td></tr><tr><td>Administrateur</td><td>Catalogue (c'est-à-dire toutes les ressources/annotations dans le catalogue)</td><td>Lecture Suppression ViewRoles ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Propriétaire</td><td>Chaque ressource (également appelé l'élément racine)</td><td>Lecture Suppression ViewRoles ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Collaborateur</td><td>Chaque ressource et annotation</td><td>Lecture Mise à jour Suppression ViewRoles Remarque&#160;: tous les droits sont révoqués si le droit de lecture sur l'élément est révoqué pour le collaborateur</td></tr></table>

> [AZURE.NOTE] Les droits de **Lecture**, **Mise à jour**, **Suppression**, **ViewRoles** sont applicables à tout élément (ressource ou annotation), tandis que **TakeOwnership**, **ChangeOwnership**, **ChangeVisibility** et **ViewPermissions** sont uniquement applicables à la ressource racine.
>
>Le droit de **Suppression** s’applique à un élément et à tout sous-élément ou élément unique inférieur. Par exemple, la suppression d'une ressource supprime également toutes les annotations pour cette ressource.

### Autorisations

L'autorisation est une liste d'entrées de contrôle d'accès. Chaque entrée de contrôle d'accès affecte l'ensemble de droits à un principal de sécurité. Les autorisations ne peuvent être spécifiées que sur une ressource (c’est à dire, l'élément racine) et s'appliquent à la ressource et aux sous-éléments.

Pour la version préliminaire d’**Azure Data Catalog**, seul le droit de **lecture** est pris en charge dans la liste des autorisations pour activer le scénario de limitation de la visibilité d’une ressource.

Par défaut, tout utilisateur authentifié a un droit de **lecture** sur tout élément dans le catalogue, sauf si la visibilité est limitée au jeu de principaux dans les autorisations.

## API REST

Les demandes d’éléments d’affichage **PUT** et **POST** peuvent être utilisées pour contrôler les rôles et les autorisations : en plus de la charge utile de l’élément, deux propriétés système peuvent être spécifiées : **\_\_rôles** et **\_\_autorisations**.

> [AZURE.NOTE]
>
> **__Les **\_\_autorisations** sont uniquement applicables à un élément racine.
>
> Le rôle **Propriétaire** est uniquement applicable à un élément racine.
>
> Par défaut, lorsqu'un élément est créé dans le catalogue, son **collaborateur** est défini sur l'utilisateur actuellement authentifié. Si l’élément doit pouvoir être mis à jour par tout le monde, le **collaborateur** doit être défini sur le principal de sécurité spécial <Everyone> dans la propriété de **\_\_rôles** lors de la première publication de l’élément (reportez-vous à l’exemple ci-dessous). Le **collaborateur** ne peut pas être modifié et reste identique pendant la durée de vie d’un élément (cela signifie que même l’**administrateur** ou le **propriétaire** n’a pas le droit de modifier le **collaborateur**). La seule valeur prise en charge pour l'affectation explicite de **collaborateur** est <Everyone> : cela signifie que le **collaborateur** peut uniquement être un utilisateur qui a créé un élément ou <Everyone>.

###Exemples
**Définissez collaborateur comme <Everyone> lors de la publication d’un élément.** Le principal de sécurité spécial <Everyone> a l’objectId « 00000000-0000-0000-0000-000000000201 ». **Corps** https://123154bb...6aad6370ee14.datacatalog.azure.com/default/views/tables/?api-version=2015-07.1.0-Preview **POST**

	{
	    "__roles": [
	        {
	            "role": "Contributor",
	            "members": [
	                {
	                    "objectId": "00000000-0000-0000-0000-000000000201"
	                }
	            ]
	        }
	    ],
	    … other table properties
	}

**Attribution de propriétaires et restriction de la visibilité pour un élément racine existant** **PUT** https://123154bb...6aad6370ee14.datacatalog.azure.com/default/views/tables/042297b0...1be45ecd462a?api-version=2015-07.1.0-Preview

	{
	    "__roles": [
	        {
	            "role": "Owner",
	            "members": [
	                {
	                    "objectId": "c4159539-846a-45af-bdfb-58efd3772b43",
	                    "upn": "user1@contoso.com"
	                },
	                {
	                    "objectId": "fdabd95b-7c56-47d6-a6ba-a7c5f264533f",
	                    "upn": "user2@contoso.com"
	                }
	            ]
	        }
	    ],
	    "__permissions": [
	        {
	            "principal": {
	                "objectId": "27b9a0eb-bb71-4297-9f1f-c462dab7192a",
	                "upn": "user3@contoso.com"
	            },
	            "rights": [
	                {
	                    "right": "Read"
	                }
	            ]
	        },
	        {
	            "principal": {
	                "objectId": "4c8bc8ce-225c-4fcf-b09a-047030baab31",
	                "upn": "user4@contoso.com"
	            },
	            "rights": [
	                {
	                    "right": "Read"
	                }
	            ]
	        }
	    ]
	}

> [AZURE.NOTE] Dans PUT, il n’est pas nécessaire de spécifier une charge utile d'élément dans le corps : PUT peut être utilisé pour mettre à jour uniquement les rôles et/ou les autorisations.

<!--Image references-->
[1]: ./media/data-catalog-developer-concepts/concept2.png

<!---HONumber=AcomDC_0224_2016-->
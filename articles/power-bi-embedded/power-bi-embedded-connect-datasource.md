<properties
   pageTitle="Microsoft Power BI Embedded - Connexion à une source de données"
   description=""
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="03/29/2016"
   ms.author="derrickv"/>

# Se connecter à une source de données

Avec **Power BI Embedded**, vous pouvez incorporer des rapports dans votre propore application. Lorsque vous incorporez un rapport Power BI dans votre application, le rapport se connecte aux données sous-jacentes par **importation** d’une copie des données ou par **connexion directe** à la source de données via **DirectQuery**.

Voici les différences entre **Import** et **DirectQuery**.

|Importer | DirectQuery
|---|---
|Les tables, colonnes *et données* sont importées ou copiées dans le jeu de données du rapport. Pour voir les modifications apportées aux données sous-jacentes, vous devez actualiser ou réimporter un jeu de données à jour et complet.|Seules les *tables et colonnes* sont importées ou copiées dans le jeu de données du rapport. Vous visualisez toujours les données les plus récentes.

La section suivante décrit les avantages et les limitations de l'utilisation de **DirectQuery**.

## Avantages de l'utilisation de DirectQuery

Il existe deux grands avantages à utiliser **DirectQuery** :

   -	**DirectQuery** vous permet de créer des visualisations sur des jeux de données très volumineux, là où il serait impossible d’importer d’abord toutes les données.
   -	La modification des données sous-jacentes peut nécessiter une actualisation des données, et pour certains rapports, l’affichage des données en cours peut nécessiter des transferts de données volumineux, empêchant la réimportation des données. En revanche, les rapports **DirectQuery** utilisent toujours les données actuelles.

## Limitations de DirectQuery

   Il existe quelques limitations à l’utilisation de **DirectQuery** :

   -	Toutes les tables doivent provenir d'une même base de données.
   -	Si la requête est trop complexe, une erreur se produit. Pour corriger l'erreur, vous devez refactoriser la requête afin de la simplifier. Si la requête doit être complexe, vous devez importer les données au lieu d'utiliser **DirectQuery**.
   -	Le filtrage de la relation s’effectue dans un seul sens, et non dans les deux sens.
   -	Vous ne pouvez pas modifier le type de données d'une colonne.
   -	Par défaut, les limitations s’appliquent aux expressions DAX autorisées dans les mesures. Voir [DirectQuery et mesures](#measures).

<a name="measures"/>
## DirectQuery et mesures

Pour garantir que les requêtes envoyées à la source de données sous-jacente sont suffisamment performantes, des limitations s’appliquent aux mesures. Lorsque vous utilisez **Power BI Desktop**, les utilisateurs avancées peuvent contourner cette limitation en choisissant **File > Options and settings > Options**. Dans la boîte de dialogue **Options**, choisissez **DirectQuery**, puis sélectionnez l'option **Allow unrestricted measures in DirectQuery mode**. Lorsque cette option est sélectionnée, toute expression DAX valide pour une mesure peut être utilisée. Cependant, les utilisateurs doivent être conscients que certaines expressions qui fonctionnent très bien lorsque les données sont importées peuvent entraîner des requêtes très lentes vers la source principale en mode **DirectQuery**. Pour en savoir plus sur l'utilisation de **Power BI Desktop**, consultez la section [Prise en main de Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/).

## Voir aussi
- [Prise en main de la version préliminaire de Microsoft Power BI Embedded Preview](power-bi-embedded-get-started.md)
- [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)
- [Prise en main de Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)

<!---HONumber=AcomDC_0406_2016-->
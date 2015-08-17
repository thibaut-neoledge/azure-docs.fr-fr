<properties
   pageTitle="Code SQL dynamique dans SQL Data Warehouse | Microsoft Azure"
   description="Conseils relatifs à l’utilisation de code SQL dynamique dans Azure SQL Data Warehouse pour le développement de solutions."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Code SQL dynamique dans SQL Data Warehouse
Lorsque vous développez le code d’une application pour SQL Data Warehouse, vous pouvez avoir besoin d’utiliser un code SQL dynamique de façon à offrir des solutions flexibles, génériques et modulaires. Toutefois, SQL Data Warehouse ne prend pas en charge les données de type objet blob pour l’instant. Cette restriction peut limiter la taille de vos chaînes, car les types d’objet blob comprennent les types varchar(max) et nvarchar(max). Vous pouvez découvrir que vous avez utilisé ces types dans le code de votre application lors de la création de très grandes chaînes de code SQL dynamique à exécuter.

Si tel est le cas, vous devez découper le code en blocs et utiliser l’instruction EXEC à la place.

En voici un exemple simplifié :

```
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Si la chaîne n’est pas particulièrement longue, vous pouvez utiliser [sp\_executesql][] comme d’habitude.


## Étapes suivantes
Pour obtenir des conseils supplémentaires en matière de développement, voir la [vue d’ensemble sur le développement][].

<!--Image references-->

<!--Article references-->
[vue d’ensemble sur le développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp\_executesql]: https://msdn.microsoft.com/fr-fr/library/ms188001.aspx

<!--Other Web references-->

<!---HONumber=August15_HO6-->
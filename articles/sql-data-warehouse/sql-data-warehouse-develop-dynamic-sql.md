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
   ms.date="01/07/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Code SQL dynamique dans SQL Data Warehouse
Quand vous développez le code d’une application pour SQL Data Warehouse, vous pouvez avoir besoin d’utiliser un code SQL dynamique de façon à offrir des solutions flexibles, génériques et modulaires. SQL Data Warehouse ne prend pas en charge les données de type objet blob pour l’instant. Cette restriction peut limiter la taille de vos chaînes, car les types d’objet blob comprennent les types varchar(max) et nvarchar(max). Si vous avez utilisé ces types dans votre code d’application pendant la création de chaînes de très grande taille, vous devez segmenter le code et utiliser plutôt l’instruction EXEC.

Voici un exemple simple :

```
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Si la chaîne est courte, vous pouvez utiliser [sp\_executesql][] comme d’habitude.

> [AZURE.NOTE]Les instructions exécutées en tant qu'instructions SQL dynamiques sont toujours soumises à l'ensemble des règles de validation TSQL.

## Étapes suivantes
Pour obtenir des conseils supplémentaires en matière de développement, voir la [vue d’ensemble sur le développement][].

<!--Image references-->

<!--Article references-->
[vue d’ensemble sur le développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp\_executesql]: https://msdn.microsoft.com/library/ms188001.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0114_2016-->
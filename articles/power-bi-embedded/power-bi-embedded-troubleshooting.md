<properties
   pageTitle="Résolution des problèmes de Microsoft Power BI Embedded Preview "
   description="Résolution des problèmes de Microsoft Power BI Embedded Preview "
   services="power-bi-embedded"
   documentationCenter=""
   authors="minewiskan"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="06/29/2016"
   ms.author="owend"/>

# Résolution des problèmes de Microsoft Power BI Embedded Preview
Cet article apporte des réponses concernant la résolution des problèmes de **Power BI Embedded**.

<a name="connection-string"/>
## Définition des chaînes de connexion SQL Server
Pour définir une chaîne de connexion SQL Server, vous devez suivre un format spécifique. Voici un exemple de chaîne de connexion pour SQL Server.

```
"Persist Security Info=False;Integrated Security=true;Initial Catalog=Northwind;server=(local)"
```

Pour en savoir plus sur les chaînes de connexion SQL Server, consultez les articles suivants :

-	[Chaînes de connexion SQL Server](https://msdn.microsoft.com/library/jj653752.aspx)
-	[SqlConnection.ConnectionString](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.connectionstring.aspx)

<a name="credentials"/>
## Définition des informations d'identification
Dans le cas où vous possédez des informations d'identification pour un environnement intermédiaire ou de développement, en particulier un nom d'utilisateur et un mot de passe, vous devrez peut-être mettre à jour les informations d'identification qui correspondent à une solution en production.

## Voir aussi
- [Prise en main de l’exemple](power-bi-embedded-get-started-sample.md)
- [Présentation de Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md)

<!---HONumber=AcomDC_0713_2016-->
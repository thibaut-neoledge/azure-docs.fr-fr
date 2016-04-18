<properties
   pageTitle="API. NET Power BI Embedded"
   description="API. NET Power BI Embedded"
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

# API. NET Power BI Embedded

La version préliminaire de **Microsoft Power BI Embedded** se concentre principalement sur l'exposition de la majorité des fonctionnalités existantes de l'API Power BI dans le cadre d'un service Azure vous permettant de développer vos applications. Vous pourrez en outre configurer par programmation, développer et déployer les ressources nécessaires et le contenu Power BI.

Vous pouvez utiliser l’**API Power BI** pour créer et gérer des espaces de travail de contenus Power BI. Avec l'API, vous pouvez :

  - Importer un fichier Power BI Desktop (PBIX) dans un espace de travail à l'aide d’une authentification par clé.
  - Modifier les chaînes de connexion d'un jeu de données.
  - Obtenir des **rapports** à intégrer à votre propre application.
  - Définir les informations d'identification d'un jeu de données afin de communiquer avec la source de données source adéquate.
  - Pour en savoir plus sur l'**API Power BI**, consultez [Prise en main de la version préliminaire de Microsoft Power BI Embedded](power-bi-embedded-get-started.md).

Consultez la [référence Power BI sur MSDN](https://msdn.microsoft.com/library/mt669800.aspx).

Voici quelques-unes des classes et méthodes utilisées dans l'**exemple Power BI Embedded** :

## Espace de noms Microsoft.PowerBI.Api.Beta

### Classe ReportsExtensions
|Nom|Description
|---|---
|[GetReports(IReports, String, String)](https://msdn.microsoft.com/library/microsoft.powerbi.api.beta.reportsextensions.getreports.aspx)|Obtient une liste des rapports disponibles dans l'espace de travail spécifié
|[GetReportsAsync(IReports, String, String, CancellationToken)](https://msdn.microsoft.com/library/microsoft.powerbi.api.beta.reportsextensions.getreportsasync.aspx)|Obtient une liste des rapports disponibles dans l'espace de travail spécifié

## Espace de noms Microsoft.PowerBI.Security

### Méthodes PowerBIToken
|Nom| Description
|---|---
|[CreateDevToken(String, Guid)](https://msdn.microsoft.com/library/mt670215.aspx)|Crée un jeton développeur avec un délai d'expiration par défaut permettant d'accéder aux services de la plateforme Power BI
|[CreateProvisionToken(String)](https://msdn.microsoft.com/library/mt670218.aspx)|Crée un jeton d'approvisionnement avec un délai d'expiration par défaut permettant de gérer les espaces de travail dans une collection d'espaces de travail

## Voir aussi

- [Présentation de Microsoft Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md)
- [Prise en main de la version préliminaire de Microsoft Power BI Embedded Preview](power-bi-embedded-get-started.md)

<!---HONumber=AcomDC_0406_2016-->
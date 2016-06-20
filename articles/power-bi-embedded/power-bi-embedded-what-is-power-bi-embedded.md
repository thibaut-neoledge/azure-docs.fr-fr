<properties
   pageTitle="Présentation de Microsoft Power BI Embedded"
   description="Power BI Embedded vous permet d’intégrer des rapports Power BI dans vos applications web ou mobiles. Vous n’avez donc pas besoin de créer des solutions personnalisées pour visualiser les données de vos utilisateurs"
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
   ms.date="06/01/2016"
   ms.author="jocaplan"/>


# Présentation de Microsoft Power BI Embedded

**Microsoft Power BI Embedded** vous permet d’intégrer des rapports Power BI dans vos applications web ou mobiles. Vous n’avez donc pas besoin de créer des solutions personnalisées pour visualiser les données de vos utilisateurs.

![](media\powerbi-embedded-whats-is\what-is.png)

**Microsoft Power BI Embedded** est un service Azure qui permet aux éditeurs de logiciels indépendants (ISV) de proposer des expériences de données Power BI au sein de leurs applications. En tant qu’éditeur de logiciels indépendant, vous avez créé des applications. Ces applications ont leurs propres utilisateurs et un ensemble spécifique de fonctionnalités. Ces applications peuvent également contenir des éléments de données intégrés, tels que des graphiques et des rapports, qui peuvent désormais être fournis par **Microsoft Power BI Embedded**. Les utilisateurs n’ont pas besoin d’avoir un compte Power BI pour utiliser votre application. Ils peuvent continuer à se connecter à votre application comme avant, afficher et interagir avec la création de rapports et le format mosaïque de Power BI sans nécessiter de licence supplémentaire.

## Gestion des licences pour Microsoft Power BI Embedded

Dans le modèle d’utilisation **Microsoft Power BI Embedded**, la gestion des licences Power BI n’est pas la responsabilité de l’utilisateur final. Au lieu de cela, les **rendus** sont achetés par le développeur de l’application qui consomme les effets visuels et ils sont facturés à l’abonnement propriétaire de ces ressources.

## Modèle conceptuel de Microsoft Power BI Embedded

![](media\powerbi-embedded-whats-is\model.png)

Comme pour tout autre service dans Azure, les ressources de **Microsoft Power BI Embedded** sont approvisionnées via les [API Azure ARM](https://msdn.microsoft.com/library/mt712306.aspx). Dans ce cas, la ressource approvisionnée constitue une **collection d’espaces de travail Power BI**.

## Collection d’espaces de travail

Une **collection d’espaces de travail** est le conteneur Azure de niveau supérieur pour les ressources qui contiennent 0 ou plusieurs **espaces de travail**. Une **collection** d’**espaces de travail** dispose de toutes les propriétés Azure standard, ainsi que des éléments suivants :

-	**Clés d’accès** : clés utilisées lors de l’appel sécurisé aux API de Power BI (fonction décrite dans une section ultérieure).
-	**Utilisateurs** : les utilisateurs Azure Active Directory (AAD) qui ont des droits d’administrateur pour gérer la collection d’espaces de travail Power BI via le portail Azure ou l’API ARM.
-	**Région** : dans le cadre de l’approvisionnement d’une **collection d’espaces de travail**, vous pouvez sélectionner une région applicable à l’approvisionnement. Pour plus d’informations, consultez l’article [Régions Azure](https://azure.microsoft.com/regions/).

## Espace de travail

Un **espace de travail** est un conteneur de contenu Power BI, qui peut inclure des jeux de données, des rapports et des tableaux de bord. Lors de sa création initiale, un **espace de travail** est vide. Dans la version préliminaire, vous créez tout le contenu à l’aide de Power BI Desktop et vous le téléchargez vers l’un de vos espaces de travail à l’aide des [API REST de Power BI](http://docs.powerbi.apiary.io/reference).

## Utilisation des collections d’espaces de travail et des espaces de travail
Les **collections d’espaces de travail** et les **espaces de travail** sont des conteneurs de contenu qui sont utilisés et organisés de la façon la plus adaptée à la conception de l’application que vous créez. Vous pouvez en organiser le contenu de plusieurs façons. Vous pouvez choisir de placer tout le contenu dans un seul espace de travail, puis d’utiliser des jetons d’application pour subdiviser le contenu entre vos clients. Vous pouvez également choisir de placer tous vos clients dans des espaces de travail distincts afin de les séparer. Ou, vous pouvez choisir d’organiser les utilisateurs par région, et non par client. Cette conception flexible vous permet de choisir la meilleure façon d’organiser le contenu.
## Sources de données dans la version préliminaire

Nous activerons un ensemble limité de sources de données pour la version préliminaire, comme suit :

### Requête directe

Nous prendrons en charge des connexions de requête directe sur des sources cloud pour la version préliminaire. Cela signifie que vous pourrez vous connecter à leurs sources de données pour afficher les données les plus récentes. Ces sources de données doivent être accessibles à partir du cloud et doivent utiliser l’authentification de base. Parmi les sources de données les plus adaptées, on trouve :

-	SQL Azure
-	SQL Azure DW
-	HD Insight Spark

## Jeux de données mis en cache

Il est possible d’utiliser des jeux de données mis en cache dans la version préliminaire. Cependant, vous ne pouvez pas actualiser les données mises en cache une fois qu’elle ont été chargées dans **Microsoft Power BI Embedded**.

## Authentification et autorisation avec des jetons d’application

**Microsoft Power BI Embedded** s’appuie sur votre application pour l’autorisation et l’authentification requises des utilisateurs. Vos utilisateurs finaux ne doivent pas nécessairement être des clients Azure Active Directory (Azure AD). Au lieu de cela, votre application donnera l’autorisation d’afficher un rapport Power BI dans **Microsoft Power BI Embedded** à l’aide des **jetons d’authentification de l’application (jetons d’application)**. Ces **jetons d’application** sont créés en fonction des besoins lorsque votre application veut afficher un rapport. Consultez la page [Jetons d’application](power-bi-embedded-get-started-sample.md#key-flow).

![](media\powerbi-embedded-whats-is\app-tokens.png)

### Jetons d’authentification de l’application

Les **jetons d’authentification de l’application (jetons d’application)** servent à l’authentification dans **Microsoft Power BI Embedded**. Il existe trois types de **jetons d’application** :

1.	Jetons d’approvisionnement : utilisés lors de l’approvisionnement d’un nouvel **espace de travail** dans une **collection d’espaces de travail**
2.	Jetons de développement : utilisés lors des appels directs aux **API REST de Power BI**
3.	Jetons d’incorporation : utilisés lors d’appels servant à afficher un rapport dans l’iframe incorporé

Ces jetons sont utilisés pour les différentes phases de vos interactions avec **Microsoft Power BI Embedded**. Les jetons sont conçus de sorte que vous pouvez déléguer des autorisations de votre application à Power BI.

### Génération de jetons d’application

Les kits de développement logiciels (SDK) fournis dans la version préliminaire vous permettent de générer des jetons. Tout d’abord, appelez l’une des méthodes Create\_\_\_Token(). Ensuite, appelez la méthode Generate() avec la clé d’accès récupérée à partir de la **collection d’espaces de travail**. Les méthodes Create de base pour les jetons sont définies dans la classe Microsoft.PowerBI.Security.PowerBIToken et sont les suivantes :

-	[CreateProvisionToken](https://msdn.microsoft.com/library/mt670218.aspx)
-	[CreateDevToken](https://msdn.microsoft.com/library/mt670215.aspx)
-	[CreateReportEmbedToken](https://msdn.microsoft.com/library/mt710366.aspx)

Pour obtenir un exemple d’utilisation de [CreateProvisionToken](https://msdn.microsoft.com/library/mt670218.aspx) et [CreateDevToken](https://msdn.microsoft.com/library/mt670215.aspx), consultez [Prise en main de l’exemple de code Microsoft Power BI Embedded](power-bi-embedded-get-started-sample.md).


## Voir aussi
- [Scénarios Microsoft Power BI Embedded courants](power-bi-embedded-scenarios.md)
- [Prise en main de la version préliminaire de Microsoft Power BI Embedded Preview](power-bi-embedded-get-started.md)
- [Jetons d’application](power-bi-embedded-get-started-sample.md#key-flow)
- [API REST de Power BI](http://docs.powerbi.apiary.io/reference)
- [Régions Azure](https://azure.microsoft.com/regions/)

<!---HONumber=AcomDC_0608_2016-->
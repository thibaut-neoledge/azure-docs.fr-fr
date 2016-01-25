<properties
   pageTitle="Conditions techniques préalables à la création d’un service de données pour Azure Marketplace | Microsoft Azure"
   description="Identifier la configuration requise pour la création d'un service de données à déployer et à vendre sur Azure Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/04/2016"
   ms.author="hascipio; avikova" />

# Conditions techniques préalables à la création d’une offre de service de données pour Azure Marketplace
Avant de commencer, lisez attentivement cette procédure, de manière à comprendre à quel moment effectuer une étape et pourquoi chaque étape doit être exécutée. Autant que possible, préparez les informations et autres données de votre entreprise, téléchargez les outils nécessaires et/ou créez les composants techniques avant de lancer le processus de création de l’offre.

Vous devez préparer les éléments suivants avant de commencer le processus :

## Prendre une décision sur la technologie qui sera utilisée pour publier votre offre de service de données

Un éditeur peut choisir entre plusieurs technologies lors de la publication du service de données dans Azure Marketplace. Les principales technologies prises en charge décrites ci-dessous. Quelle que soit la technologie utilisée pour publier le service de données, l’utilisateur final consomme les données via le **flux OData** exposé par le service Azure Marketplace. Vous trouverez des informations détaillées sur le service OData à l’adresse [http://www.odata.org/](http://www.odata.org/)

## Base de données SQL Azure

Il incombe à l’éditeur de préparer le jeu de données dans SQL Azure. Vous devrez vous abonner à Azure, provisionner la taille appropriée de la base de données et charger vos données dans la base de données SQL Azure. L’éditeur doit s’assurer que ses données sont toujours actualisées. Vous trouverez plus d'informations sur la souscription d’un abonnement aux services Azure à l’adresse [https://azure.microsoft.com/services/sql-database/](https://azure.microsoft.com/services/sql-database/)


Lorsque vous déplacez des données vers SQL Azure, Azure Marketplace peut exposer des tables et des vues. L’éditeur peut spécifier quelles tables/vues et colonnes sont exposées à l'utilisateur final. Le fournisseur de contenu peut également préciser les colonnes qui peuvent être interrogées par l'utilisateur final et celles qui sont uniquement renvoyées dans la charge utile. Cela offre un niveau élevé de flexibilité pour choisir les données de la base de données qui doivent être exposées. Les colonnes qui peuvent être interrogées doivent être soutenues par un ou plusieurs index de base de données.

## Service web REST

Protocole pris en charge : **HTTPS uniquement**

Les services REST existants peuvent être exposés via Azure Marketplace. Étant donné que le jeu de données est toujours exposé à l'utilisateur final comme un flux OData, le service Azure Marketplace doit pouvoir mapper le service à un service OData. Pour cela, les points de terminaison REST doivent exposer tous les paramètres en tant que paramètres HTTP.

La charge utile doit pouvoir être mappée à une réponse ATOM. Par conséquent, la réponse des services doit être au format XML et ne peut contenir qu’un élément récurrent avec des valeurs de charge utile (jeu d'enregistrements, par exemple). Le service Azure Marketplace mappera le nœud récurrent au nœud d'entrée dans ATOM et les valeurs de charge utile aux nœuds de propriétés dans le nœud d'entrée.

Les informations d'autorisation (par exemple, la clé API, l'authentification par jeton, etc.) doivent être fournies comme un paramètre HTTP ou dans l'en-tête HTTP (paire clé-valeur) : l'authentification de base est également prise en charge. Une clé valide doit être fournie et toutes les requêtes via Azure Marketplace sont effectuées via cette clé. La surveillance et la facturation de l’utilisateur interviennent au niveau de la couche Azure Marketplace.

Les erreurs retournées par le service doivent être mappées à des codes d'état HTTP. Au cas où le service retourne un fichier XML contenant l'erreur, cette erreur sera mappée par le service Azure Marketplace à des codes d'état HTTP.

## Services web SOAP

Protocole : **HTTPS uniquement**

Les conditions requises sont les mêmes que dans le service REST. La seule différence réside dans le fait que les paramètres peuvent également être fournis dans un corps XML transmis au service de l'éditeur avec chaque demande effectuée sur Azure Marketplace. Cela signifie que les paramètres HTTP fournis par l’utilisateur au niveau frontal sont traduits en éléments XML dans un document XML transmis avec la requête au web service du fournisseur de contenu.

## Services web OData

Protocole : **HTTPS uniquement**

Les données peuvent être exposées comme un service OData sur Azure Marketplace. Le système fera passer le service et remplacera la racine du service Azure Marketplace par le service racine Azure Marketplace pour s’assurer que tous les appels ultérieurs transitent par Azure Marketplace.

Les services OData ne doivent pas seulement être transmis à une base de données sur le serveur principal. OData prend en charge tout type de logique de stockage ou métier pour gérer le service.


## Étapes suivantes
Maintenant que vous avez passé en revue les conditions préalables et effectué les tâches nécessaires, vous pouvez procéder à la création de votre offre de service de données, comme indiqué dans le [Guide de publication d’un service de données](marketplace-publishing-data-service-creation.md).

Ou, si vous souhaitez passer en revue le processus global et les articles respectifs de chacune des phases de publication, consultez l'article [Mise en route : publier une offre sur Azure Marketplace](marketplace-publishing-getting-started.md).

[link-acct]: marketplace-publishing-accounts-creation-registration.md

<!---HONumber=AcomDC_0114_2016-->
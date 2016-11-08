---
title: Extraction de données publiques dans des hubs d’événements Azure | Microsoft Docs
description: Vue d’ensemble de l’importation de hubs d’événements à partir de l’exemple web
services: event-hubs
documentationcenter: na
author: spyrossak
manager: timlt
editor: ''

ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2016
ms.author: spyros;sethm

---
# Extraction de données publiques dans des hubs d’événements Azure
Dans les scénarios Internet des objets (IoT) ordinaires, vous disposez d’appareils que vous pouvez programmer pour transmettre des données à Azure : soit à un hub d’événements Azure, soit à un hub IoT. Ces deux sortes de hubs sont des points d’entrée dans Azure pour le stockage, l’analyse et la visualisation qui s’accompagnent de nombreux outils disponibles sur Microsoft Azure. Toutefois, ils exigent tous les deux que vous leur transmettiez des données au format JSON et sécurisées d’une manière particulière. Ainsi, la question suivante se pose. Que faire si vous voulez importer des données à partir de sources publiques ou privées, lesquelles données sont exposées en tant que service web ou flux quelconque, alors que vous n’avez pas la possibilité de modifier la façon dont les données sont publiées ? Prenez la météo, le trafic routier ou les cotations boursières : vous ne pouvez pas demander à Météo France, Bison Futé ou Euronext de configurer une notification Push vers votre hub d’événements. Pour résoudre ce problème, nous avons écrit et publié un exemple de petit cloud open source que vous pouvez modifier et déployer. Ce petit cloud vous permet d’extraire des données d’une telle source et de les transmettre à votre hub d’événements. De là, vous pouvez faire ce que vous voulez avec, sous réserve, bien sûr, de respecter les termes du contrat de licence du producteur. Cette application se trouve [ici](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/).

Notez que le code de cet exemple indique uniquement comment extraire des données de flux web ordinaires et comment écrire dans un hub d’événements Azure. Il ne s’agit PAS d’une application de production et aucun essai n’a été effectué pour la rendre utilisable dans un tel environnement : il s’agit strictement d’un exemple que seuls des développeurs peuvent manipuler. De plus, l’existence de cet exemple n’équivaut PAS à une recommandation invitant à préférer une **extraction** des données dans Azure à une **transmission push**. Vous avez tout intérêt à vérifier les facteurs de sécurité, performance, fonctionnalité et coût avant de choisir une architecture de bout en bout.

## Structure d’application
L’application est écrite en C# et la [description de l’exemple](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/) contient toutes les informations dont vous avez besoin pour modifier, générer et publier l’application. Les sections suivantes fournissent une vue d'ensemble détaillée de l'application.

Nous commençons en partant du principe que vous avez accès à un flux de données. Par exemple, vous pouvez avoir besoin d’extraire des données sur le trafic routier auprès de Bison Futé ou des données météorologiques auprès de Météo France, soit pour afficher des rapports personnalisés, soit pour combiner ces données avec d’autres dans votre application. Vous devez également avoir configuré un hub d’événements Azure et connaître la chaîne de connexion nécessaire pour y accéder.

Quand la solution GenericWebToEH démarre, elle lit un fichier de configuration (App.config) pour obtenir plusieurs choses :

1. L’URL, ou la liste d’URL, du site qui publie les données. Idéalement, il s’agit d’un site qui publie des données au format JSON, comme ceux référencés par le Département des transports de l’État de Washington (WSDOT) [ici](http://www.wsdot.wa.gov/Traffic/api/).
2. Les informations d’identification pour l’URL, si nécessaire. De nombreuses sources publiques n’ont pas besoin d’informations d’identification. Vous pouvez aussi placer les informations d’identification dans la chaîne d’URL. D’autres exigent que vous les fournissiez séparément. (Notez que vous pouvez uniquement spécifier un seul jeu d’informations d’identification dans cette application, si bien qu’elle fonctionne uniquement si vous spécifiez une seule URL, et non pas une liste d’URL.)
3. La chaîne de connexion et le nom de l’Event Hub dans cet espace de noms Event Hubs, auquel vous transmettez les données. Ces informations sont disponibles dans le portail Azure.
4. L’intervalle de veille, en millisecondes, entre les interrogations du site de données publiques. La définition de cet intervalle exige de la réflexion. Si la fréquence des interrogations est trop faible, vous risquez de rater des données ; en revanche, si elle est trop élevée, vous risquez d’obtenir beaucoup de données répétitives, voire pire, vous retrouver bloqué car considéré comme un robot mal intentionné. Tenez compte de la fréquence de mise à jour de la source de données : les données météorologiques ou de trafic routier sont peut-être actualisées toutes les 15 minutes, alors que les cotations boursières le sont toutes les quelques secondes, selon leur provenance.
5. Un indicateur qui détermine si les données arrivent au format JSON ou XML dans l’application. Étant donné que vous avez besoin de transmettre les données à un hub d’événements, l’application comporte un module de conversion du format XML en JSON avant l’envoi.

Après avoir lu le fichier de configuration, l’application entre dans une boucle : accès au site web public, conversion des données si nécessaire, écriture des données dans votre hub d’événements, puis attente pendant l’intervalle de veille avant de tout recommencer. Plus précisément :

* Lecture du site web public. Pour recevoir des données prêtes à l’envoi, l’instance de la classe RawXMLWithHeaderToJsonReader est utilisée à partir d’Azure/GenericWebToEH/ApiReaders/RawXMLWithHeaderToJsonReader.cs. Le flux source est lu dans la méthode GetData(), puis fractionné en éléments plus petits (c’est-à-dire en enregistrements) à l’aide de GetXmlFromOriginalText. Cette méthode lit le langage XML, ainsi que le tableau JSON ou JSON bien formé. Ensuite, le traitement démarre à l’aide de la configuration MergeToXML d’App.config (default=empty).
* Les données de réception et d’envoi sont implémentées en tant que boucle dans la méthode Process() dans Program.cs. Après avoir reçu les résultats de sortie de GetData(), la méthode empile des valeurs séparées dans le hub d’événements.

## Étapes suivantes
Pour déployer la solution, clonez ou téléchargez l’application [GenericWebToEH](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/), modifiez le fichier App.config, générez-le, puis publiez-le. Une fois que vous avez publié l’application, vous pouvez la voir s’exécuter dans le portail Azure Classic sous Cloud Services. Vous pouvez aussi modifier certains paramètres de configuration (notamment la cible Event Hub et l’intervalle de veille) sous l’onglet **Configurer**.

Consultez d’autres exemples de hubs d’événements dans la [galerie d’exemples Azure](https://azure.microsoft.com/documentation/samples/?service=event-hubs) et sur [MSDN](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5).

<!---HONumber=AcomDC_0831_2016-->
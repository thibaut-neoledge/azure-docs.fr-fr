<properties 
	pageTitle="Utilisation de connecteurs" 
	description="Utilisation de connecteurs" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java" 
	authors="prkumar" 
	manager="dwrede" 
	editor=""/>

<tags
	ms.service="app-service-logic"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="integration" 
	ms.date="03/20/2015"
	ms.author="prkumar"/>


#Utilisation de connecteurs#

Les connecteurs sont des applications API qui vous permettent de vous connecter à des services qui s'exécutent dans le cloud ou localement pour obtenir ou placer des données ou effectuer des actions. Vous pouvez les utiliser dans Azure App Services à partir d'applications web, mobile et logiques.

Azure App Services fournit par défaut plusieurs connecteurs pour vous aider à vous connecter aux services de votre choix tout en concentrant vos efforts sur la logique métier de votre application. De plus, vous pouvez étendre facilement la plateforme en écrivant vos propres applications API pour vous connecter à vos applications héritées.

Les connecteurs BizTalk offrent des fonctionnalités telles que :

1. la gestion de la sécurité OAuth pour la connexion à des services SaaS populaires (Facebook, Twitter, Office 365, SalesForce) ;
2. la connectivité à vos ressources locales via un Service Bus Relay ;
3. la connectivité pour des protocoles communs pour recevoir et envoyer des données.

##Connecteurs et applications logiques##
Les applications logiques sont composées de déclencheurs et d'actions. Certains connecteurs peuvent servir de déclencheurs pour déterminer si de nouvelles données sont disponibles et pour déclencher une application logique avec ces données comme entrée. Vous pouvez également utiliser certains connecteurs comme action au milieu d'une application logique pour rechercher des données, écrire des données ou effectuer d'autres actions prises en charge par le connecteur.

###Connecteurs en tant que déclencheurs###
Plusieurs connecteurs fournissent des déclencheurs pour les applications logiques. Ces déclencheurs sont de deux types :

1. Déclencheurs d'interrogation : ces déclencheurs interrogent le service de votre choix selon une fréquence spécifiée pour vérifier l'existence de nouvelles données. Quand de nouvelles données sont disponibles, une nouvelle instance de votre application logique s'exécute avec les données comme entrée. Le déclencheur peut effectuer des tâches supplémentaires comme le nettoyage des données qui ont été lues et transmises à l'application logique, pour empêcher que les mêmes données soient consommées plusieurs fois. Fichier, SQL et Azure Storage sont des exemples de ce type de connecteur.
2. Déclencheurs Push : ces déclencheurs écoutent les données sur un point de terminaison ou attendent qu'un événement se produise pour déclencher une nouvelle instance d'une application logique. L'Écouteur HTTP et Twitter sont des exemples de ce type de connecteur.

###Connecteurs en tant qu'actions###
Vous pouvez aussi utiliser des connecteurs comme actions dans le cadre de votre application logique. Cela peut être utile pour rechercher des données dans l'application logique à utiliser lors de l'exécution, par exemple si vous devez rechercher des informations supplémentaires sur un client dans une base de données SQL lors du traitement de l'une de ses commandes dans une application logique. Il se peut également que vous deviez écrire, mettre à jour ou supprimer des données dans une destination, auquel cas vous pouvez utiliser les actions fournies par les connecteurs.

##Utilisation des connecteurs##
Les articles suivants fournissent des exemples d'utilisation de certains connecteurs.

* [Utilisation du connecteur HTTP]
* [Utilisation du connecteur FTP]
* [Utilisation du connecteur Box]
* [Utilisation du connecteur Azure Storage Blob]
* [Utilisation du connecteur Facebook]
* [Utilisation du connecteur Twitter ]
* [Utilisation du connecteur Salesforce]
* [Utilisation du connecteur Office 365]
* [Utilisation du connecteur Sharepoint]
* [Utilisation du connecteur SAP]

Pour plus d'informations, consultez la référence d'API de connecteur [http://aka.ms/appservicesconnectorreference](http://aka.ms/appservicesconnectorreference).


<!-- Links -->

[Utilisation du connecteur Box]: app-service-logic-connector-box.md
[Utilisation du connecteur Facebook]: app-service-logic-connector-facebook.md
[Utilisation du connecteur Salesforce]: app-service-logic-connector-salesforce.md
[Utilisation du connecteur Twitter ]: app-service-logic-connector-twitter.md
[Utilisation du connecteur SAP]: app-service-logic-connector-sap.md
[Utilisation du connecteur FTP]: app-service-logic-connector-ftp.md
[Utilisation du connecteur HTTP]: app-service-logic-connector-http.md
[Utilisation du connecteur Azure Storage Blob]: app-service-logic-connector-azurestorageblob.md
[Utilisation du connecteur Office 365]: app-service-logic-connector-office365.md
[Utilisation du connecteur Sharepoint]: app-service-logic-connector-sharepoint.md


<!--HONumber=49-->
<properties
	pageTitle="Liste des tâches d'administration et de développement dans BizTalk Services | azure.microsoft.com/ Azure" 
	description="Documentation de travail et de planification pour le déploiement d'Azure BizTalk Services."
	services="biztalk-services"
	documentationCenter=""
	authors="msftman"
	manager="erikre"
	editor=""/>

<tags
	ms.service="biztalk-services"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/15/2016"
	ms.author="deonhe"/>

# Liste des tâches d'administration et de développement dans BizTalk Services  

## Mise en route
Lorsque vous utilisez azure.microsoft.com/ Azure BizTalk Services, il existe plusieurs composants locaux et cloud à prendre en compte. Pour commencer, prenez en compte le flux de processus suivant :

|Étape|Qui est responsable|Task|Liens connexes|
|----|----|----|----|
1\.|Administrateur|Créer l’abonnement azure.microsoft.com/ Azure à l’aide d’un compte azure.microsoft.com/ ou d’un compte professionnel|[Portail Azure Classic](http://go.azure.microsoft.com/.com/fwlink/p/?LinkID=213885)|
|2\.|Administrateur|Créer ou approvisionner un BizTalk Service.|[Créer un BizTalk Service à l'aide du portail Azure Classic](http://go.azure.microsoft.com/.com/fwlink/p/?LinkID=302280)|
|3\.|Administrateur|Enregistrer votre déploiement ou le déploiement de votre entreprise de BizTalk Services|[Enregistrement et mise à niveau d'un déploiement de service BizTalk sur le portail BizTalk Services](https://msdn.azure.microsoft.com/.com/library/azure/hh689837.aspx)|
|4\.|Administrateur|S’applique si l’application utilise BizTalk Adapter Service pour se connecter à un système métier (LOB) local ou utilise une file d’attente ou une destination de rubrique. Créer l'espace de noms Azure Service Bus Donner les valeurs espace de noms, nom de l'émetteur Service Bus et clé de l'émetteur Service Bus au développeur.|[Procédure : Créer ou modifier un espace de noms du service Service Bus](../service-bus/service-bus-dotnet-get-started-with-queues.md) et [Obtenir les valeurs nom et clé de l'émetteur](biztalk-issuer-name-issuer-key.md)|
|5\.|Développeur|Installer le Kit de développement logiciel (SDK) et créer le projet BizTalk Service dans Visual Studio.|[Installer le Kit de développement logiciel (SDK) Azure BizTalk Services](https://msdn.azure.microsoft.com/.com/library/azure/hh689760.aspx) et [Créer des points de terminaison de messagerie enrichis sur Azure](https://msdn.azure.microsoft.com/.com/library/azure/hh689766.aspx)|
|6\.|Développeur|Déployer votre projet BizTalk Service sur votre BizTalk Service hébergé sur Azure.|[Déploiement et actualisation du projet BizTalk Services](https://msdn.azure.microsoft.com/.com/library/azure/hh689881.aspx)|
|7\.|Administrateur|S'applique si vous utilisez EDI. Vous pouvez ajouter des Partenaires et créer des Accords sur le portail azure.microsoft.com/ Azure BizTalk Services. Lorsque vous créez un accord, vous pouvez ajouter le pont et/ou les transformations créés par le développeur dans les paramètres de l'accord.|[Configuration d'EDI, AS2 et EDIFACT sur le portail BizTalk Services](https://msdn.azure.microsoft.com/.com/library/azure/hh689853.aspx)|
|8\.|Administrateur|À l'aide du portail Azure Classic, surveiller l'intégrité de votre BizTalk Service, y compris les mesures de performances.|[Onglets Tableau de bord, Surveiller et Mettre à l'échelle dans BizTalk Services](http://go.azure.microsoft.com/.com/fwlink/p/?LinkID=302281)|
|9\.|Administrateur|À l'aide du portail azure.microsoft.com/ Azure BizTalk Services, gérer les artefacts utilisés par les Services BizTalk et suivre les messages lorsqu’ils sont traités par les fichiers de pont.|[Utilisation du portail BizTalk Services](https://msdn.azure.microsoft.com/.com/library/azure/dn874043.aspx)|
|10\.|Administrateur|Créer un plan de sauvegarde pour sauvegarder BizTalk Service.|[Continuité des activités et récupération d'urgence dans BizTalk Services](https://msdn.azure.microsoft.com/.com/library/azure/dn509557.aspx) |  
## Étapes suivantes
[Didacticiels et exemples](https://msdn.azure.microsoft.com/.com/library/azure/hh689895.aspx)

[Créer le projet dans Visual Studio](https://msdn.azure.microsoft.com/.com/library/azure/hh689811.aspx)

[Installer le Kit de développement logiciel (SDK) Azure BizTalk Services](https://msdn.azure.microsoft.com/.com/library/azure/hh689760.aspx)

## Concepts
[Créer le projet dans Visual Studio](https://msdn.azure.microsoft.com/.com/library/azure/hh689811.aspx) [Messagerie EDI, AS2 et EDIFACT (entreprise-entreprise)](https://msdn.azure.microsoft.com/.com/library/azure/hh689898.aspx)
## Autres ressources  
[Ajouter des points de terminaison de messagerie source, destination et pont](https://msdn.azure.microsoft.com/.com/library/azure/hh689877.aspx) [Découvrir et créer des tables et des transformations de messages](https://msdn.azure.microsoft.com/.com/library/azure/hh689905.aspx) [Utilisation de BizTalk Adapter Service (BAS)](https://msdn.azure.microsoft.com/.com/library/azure/hh689889.aspx) [Azure BizTalk Services](http://go.azure.microsoft.com/.com/fwlink/p/?LinkID=303664)

<!---HONumber=AcomDC_0817_2016-->
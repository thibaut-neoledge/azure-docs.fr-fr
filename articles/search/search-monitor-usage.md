<properties 
   pageTitle="Surveillance de l’utilisation et des statistiques dans un service Azure Search" 
   description="Effectuez le suivi de la consommation des ressources et de la taille des index pour Azure Search" 
   services="search" 
   documentationCenter="" 
   authors="HeidiSteen" 
   manager="mblythe" 
   editor=""
   tags="azure-portal"/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required" 
   ms.date="07/08/2015"
   ms.author="heidist"/>

# Surveillance de l’utilisation et des statistiques dans un service Azure Search

Le suivi de la croissance des index et de la taille des documents peut vous aider à ajuster la capacité de manière proactive avant d’atteindre la limite supérieure définie pour votre service.

Les chiffres et les statistiques sont facilement accessibles sur le [portail Azure](https://portal.azure.com) pour la surveillance de l’utilisation des ressources, mais vous pouvez aussi obtenir ces informations par programme en concevant un outil d’administration de service personnalisé. Cet article présente les étapes relatives à ces deux techniques.

##Affichage des chiffres et des mesures sur le portail 

1. Connectez-vous au [portail Azure](https://portal.azure.com). 

2. Cliquez sur le tableau de bord des services de votre service Azure Search. Les mosaïques associées au service se trouvent sur la page d’accueil. Vous pouvez aussi accéder au service à partir de l’option de navigation de la barre de lancement. Consultez [Création d’un service](search-create-service-portal.md) pour obtenir les instructions détaillées.

La partie Utilisation comprend un indicateur affichant les ressources disponibles en cours d’utilisation.

  ![][1]

N’oubliez pas que chaque service partagé offre au maximum un réplica et une partition. De plus, chaque service peut prendre en charge 10 000 documents au total ou 50 Mo de données maximum, quelle que soit la première limite atteinte.

##Obtention de statistiques d’index avec l’API REST

L’API REST Azure Search et le Kit de développement .NET fournissent un accès par programme aux mesures de service. Si vous utilisez des [indexeurs](https://msdn.microsoft.com/library/azure/dn946891.aspx) pour charger un index à partir de la base de données SQL Azure ou DocumentDB, une API supplémentaire vous permet d’obtenir les chiffres requis.

  + [Obtention de statistiques d’index](https://msdn.microsoft.com/library/azure/dn798942.aspx)
  + [Nombre de documents](https://msdn.microsoft.com/library/azure/dn798924.aspx)
  + [Obtention de l’état d’indexeur](https://msdn.microsoft.com/library/azure/dn946884.aspx)

## Étapes suivantes

Consultez [Limites et contraintes](https://msdn.microsoft.com/library/azure/dn798934.aspx) afin de déterminer la combinaison de partitions et de réplicas requise lorsque la capacité existante est insuffisante.

Consultez [Gestion de votre service Search sur Microsoft Azure](search-manage.md) pour en savoir plus sur l’administration des services.

<!--Image references-->
[1]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG




 

<!---HONumber=August15_HO6-->
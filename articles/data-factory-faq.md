<properties 
	pageTitle="Azure Data Factory - Forum aux Questions" 
	description="Forum aux Questions sur Azure Data Factory." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/10/2015" 
	ms.author="spelluru"/>

# Azure Data Factory - Forum aux Questions

**Q : qu'est-ce qu'Azure Data Factory ?**

Data Factory est un service entièrement géré permettant aux développeurs de composer des services de stockage, de déplacement et de traitement des données dans des pipelines de données à haute disponibilité et à tolérance de pannes. Data Factory fonctionne avec du stockage de données local ou dans le cloud. Un pipeline est un ensemble d'entrées de données, d'activités de traitement et de sorties de données qui est défini avec des scripts simples JSON et activé via des commandes PowerShell. Une fois activé, Data Factory gère et planifie les pipelines à exécuter sur HDInsight (Hadoop) avec des options pour la gestion de cluster automatique, pour l'utilisateur. Data Factory fournit également une expérience visuelle pour la surveillance et la gestion via le portail Azure en version préliminaire afin de surveiller tous les pipelines avec des informations opérationnelles et de contrôle d'intégrité de service étoffées dans un tableau de bord.
 
**Q : quel défi le client peut-il relever avec Data Factory ?**

Azure Data Factory permet d'exploiter avec agilité les services divers de stockage, de traitement et de déplacement des données dans un stockage relationnel classique comportant des données non structurées, avec des fonctionnalités de contrôle et de surveillance d'un service entièrement géré.

**Q : quel est le public ciblé par Data Factory ?**


- Développeurs de données : ils sont responsables de la création de services d'intégration entre Hadoop et d'autres systèmes :
	- ils doivent suivre le rythme et intégrer des données dans un paysage en évolution et croissance constantes ;
	- ils doivent écrire du code personnalisé pour la production d'informations, qui s'avère coûteux et difficile à mettre à jour, n'offre pas une haute disponibilité ou une tolérance aux pannes ;

- Professionnels de l'informatique : ils cherchent à intégrer des données plus variées dans leur infrastructure informatique :
	- ils doivent consulter toutes les données d'une organisation pour obtenir un aperçu éclairé de l'activité ;
	- ils doivent gérer les ressources de calcul et de stockage pour équilibrer les coûts et la mise à l'échelle au niveau local et du cloud ;
	- ils doivent ajouter rapidement des sources variées et effectuer un traitement pour répondre aux nouveaux besoins des entreprises, tout en conservant la visibilité sur toutes les ressources de calcul et de stockage.

**Q : où puis-je trouver des informations de tarification pour Azure Data Factory ?**

Consultez la [page de tarification de Data Factory][adf-pricing-details] pour plus d'informations sur la tarification d'Azure Data Factory.  

**Q : comment prendre en main Azure Data Factory ?**

- Pour une vue d'ensemble d'Azure Data Factory, consultez [Présentation d'Azure Data Factory][adf-introduction].
- Pour accéder à un didacticiel rapide, consultez [Prise en main d'Azure Data Factory][adfgetstarted].
- Pour des informations complètes, consultez la [documentation Azure Data Factory][adf-documentation-landingpage].
 
**Q : quelles sont les sources de données et les activités prises en charge ?**

- **Sources de données prises en charge :** Azure Storage (objets blob et tables), SQL Server, Azure SQL Database.
- **Activités prises en charge **: activité de copie (du niveau local vers le cloud et du cloud vers le niveau local), activité HDInsight (transformations Pig et Hive) et les activités personnalisées C#.
  
**Q : comment les clients accèdent-ils à Data Factory ?**

Les clients peuvent accéder à Data Factory via le [portail Azure en version préliminaire][azure-preview-portal].

**Q : dans quelle région sera disponible Data Factory ?**

En version publique, Data Factory sera uniquement disponible dans l'Ouest des États-Unis.  Les services de calcul et de stockage utilisés par les fabriques de données peuvent être situés dans d'autres régions.
 
**Q : quelles sont les limites du nombre de fabriques de données/pipelines/activités/jeux de données ?** 


- Nombre de fabriques de données dans un abonnement : 50
- Nombre de pipelines dans une fabrique de données : 100
- Nombre d'activités dans un pipeline : 10
- Nombre de jeux de données dans une fabrique de données : 100

**Q : quelles sont les régions prises en charge par l'activité de copie ?**

L'activité de copie prend en charge la copie des données dans les régions suivantes : Est des États-Unis 2, Ouest des États-Unis, Europe du Nord, Europe de l'ouest et Asie du Sud-est.

La copie de données est également prise en charge dans les autres régions, en utilisant l'une des cinq régions ci-dessus pour le routage des données.  L'opération de copie est mesurée en fonction de la région via laquelle les données sont acheminées.

Région de destination de la copie | Région utilisée pour le routage
-------------------------- | -----------------------
Est des États-Unis | Est des États-Unis 2
Centre des États-Unis | Centre des États-Unis 2
Centre nord des États-Unis | Est des États-Unis 2
Centre sud des États-Unis | Ouest des États-Unis
Asie orientale | Asie du Sud-Est
Japon oriental | Ouest des États-Unis
Japon occidental | Ouest des États-Unis
Sud du Brésil | Est des États-Unis 2

**Q : quand vous utilisez un cluster HDInsight, quelles sont les régions prises en charge par HDInsight ?**

Consultez la section Disponibilité géographique dans l'article suivant, ou la page de [tarification relative à HDInsight][hdinsight-supported-regions].

**Q : quelle est la région utilisée par un cluster HDInsight à la demande ?**

Le cluster HDInsight à la demande est créé dans la même région que le stockage que vous avez spécifié pour être utilisé avec le cluster.    

## Voir aussi
[Présentation d'Azure Data Factory][adf-introduction]
[Prise en main d'Azure Data Factory][adfgetstarted]
[Guide de résolution des problèmes liés à Data Factory][adf-troubleshoot]


[adfgetstarted]: ../data-factory-get-started
[adf-introduction]: ../data-factory-introduction
[adf-troubleshoot]: ../data-factory-troubleshoot
[adf-documentation-landingpage]: http://go.microsoft.com/fwlink/?LinkId=516909
[azure-preview-portal]: http://portal.azure.com

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/

<!--HONumber=35.2-->

<!--HONumber=46--> 

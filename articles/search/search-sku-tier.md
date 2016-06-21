<properties
	pageTitle="Choisir une référence (SKU) ou un niveau pour Azure Search | Microsoft Azure"
	description="Azure Search peut être configuré sur ces niveaux de référence (SKU) : Gratuit, De base et Standard, où Standard est disponible dans différentes configurations de ressources et différents niveaux de capacité."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="paulettm"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="06/05/2016"
	ms.author="heidist"/>

# Choisir une référence (SKU) ou un niveau pour Azure Search

Avant de pouvoir [créer un service de recherche](search-create-service-portal.md) dans Azure Search, vous devez connaître la référence (SKU) ou le niveau sur lequel configurer le service. Les références (SKU) incluent les niveaux suivants : Gratuit, De base ou Standard, où Standard est disponible dans plusieurs configurations de ressources et de capacités.

La capacité et les coûts d’exécution du service vont de pair. Les informations contenues dans cet article peuvent vous aider à décider quelle référence (SKU) offre le juste équilibre, mais pour que cela soit utile, vous aurez besoin de connaître au moins le nombre et la taille des index que vous envisagez de créer, ainsi que le volume de requêtes. Une fois les estimations connues, les étapes suivantes doivent simplifier le processus :

- **Étape 1** Consulter les descriptions de la référence (SKU) ci-dessous pour connaître votre option.
- **Étape 2** Répondre à une série de questions pour affiner votre choix.
- **Étape 3** Valider votre décision en passant en revue des limites strictes sur le stockage et la tarification. 

## Descriptions de la référence (SKU)

La table suivante fournit une description et une comparaison simple de chaque référence (SKU) en termes de partitions et de réplicas, ainsi que de requêtes par seconde (RPS), le cas échéant. Pour une introduction aux réplicas et aux partitions, voir [Limites de service d’Azure Search](search-limits-quotas-capacity.md).

Niveau|Capacité|Conçu pour
----|-----------|-----------
Gratuit|50 Mo ou 3 index et 10 000 documents|Un service partagé, sans frais, utilisé pour l’évaluation, l’investigation ou de petites charges de travail. Dans la mesure où ils sont partagés avec d’autres abonnés, le débit des requêtes et l’indexation varient en fonction des autres utilisateurs ayant recours au service.
De base|3 réplicas et 1 partition|Charges de travail de production de petite taille sur du matériel dédié. Hautement disponible.
Standard 1 (S1)|Les partitions sont de 25 Go chacune. <br/><br/>Le RPS sur les réplicas est d’environ 15 requêtes par seconde.|Prend en charge les combinaisons flexibles de partitions (12) et de réplicas (12), utilisées pour des charges de travail de taille moyenne sur du matériel dédié. Vous pouvez allouer des partitions et des réplicas dans des combinaisons prises en charge par un nombre maximal de 36 unités de recherche facturables.
Standard 2 (S2)|Les partitions sont de 100 Go chacune. <br/><br/>Le RPS sur les réplicas est d’environ 60 requêtes par seconde.|Exécute les grandes charges de travail de production à l’aide des mêmes configurations que S1, mais avec des partitions et des réplicas plus importants.
Version préliminaire Standard 3 (S3)|Les partitions sont de 200 Go chacune. <br/><br/>Le RPS sur les réplicas est d’environ 60 requêtes par seconde.|Exécute les charges de production proportionnellement plus importantes sur les systèmes haut de gamme, dans des configurations allant jusqu’à 12 partitions ou 12 réplicas. 
Version préliminaire Standard 3 Haute densité (S3 HD)|Une seule partition à 200 Go. <br/><br/>Le RPS sur les réplicas est supérieur à 60 requêtes par seconde.|Destiné aux clients ayant un grand nombre d’index de plus petite taille.

## Cheminement vers le choix d’une référence (SKU)

Le tableau suivant est un sous-ensemble des [Limites de service dans Azure Search](search-limits-quotas-capacity.md). Il répertorie les facteurs susceptibles d’avoir un impact sur une décision de référence (SKU), y compris les limites de capacité de référence (SKU) de la table précédente, avec des limites supplémentaires liées aux types de contenu (à savoir les index et les documents). Vous pouvez vous référer à ce graphique

Ressource|Gratuit|De base|S1|S2|S3 <br/>(version préliminaire) |S3 HD <br/>(version préliminaire) 
---|---|---|---|----|---|----
Contrat de niveau de service (SLA)|Non <sup>1</sup> |Oui |Oui |Oui |Non <sup>1</sup> |Non <sup>1</sup> 
Partitions maximales|N/A |1 |12 |12 |12|1
Index autorisés par référence (SKU)|3|5|50|200|200|1 000
Limites de documents|10 000 au total|1 million par service|15 millions par partition |60 millions par partition|120 millions par partition |1 million par index
Taille de partition|50 Mo au total|2 Go par service|25 Go par partition |100 Go par partition (jusqu’à 1,2 To par service)|200 Go par partition (jusqu’à 2,4 To par service)|200 Go (pour 1 partition)
Réplicas maximales|N/A |3 |12 |12 |12|12
Requêtes par seconde|N/A|~3 par réplica|~15 par réplica|~60 par réplica|Moins de 60 par réplica|Moins de 60 par réplica

<sup>1</sup> Les références (SKU) gratuites et les versions préliminaires ne sont pas fournies avec les contrats de niveau de service. Des contrats de niveau de service sont mis en œuvre dès qu’une référence (SKU) est généralement disponible.

> [AZURE.NOTE] Les valeurs maximales de réplica et de partition font l’objet d’une configuration maximale de facturation de 36 unités, qui impose une limite effective inférieure à ce que la valeur maximale indique au premier abord. Par exemple, pour utiliser le nombre maximal de 12 réplicas, vous pouvez avoir au maximum 3 partitions (3 * 12 = 36 unités). De même, pour utiliser le nombre maximal de partitions, vous devez réduire les réplicas à 3. Pour accéder à un graphique sur les combinaisons autorisées, voir [Mettre à l’échelle les niveaux de ressources pour interroger et indexer les charges de travail dans Azure Search](search-capacity-planning.md).

### Questions les plus courantes lors du choix d’une référence (SKU)

Les questions suivantes peuvent vous aider à mieux choisir la référence (SKU) adaptée à votre charge de travail.

1. Avez-vous des exigences en matière de **contrat de niveau de service (SLA)** ? Affinez votre choix de référence (SKU) sur De base ou Standard sans version préliminaire.
2. **De combien d’index** avez-vous besoin ? L’un des principaux éléments à prendre en compte lorsque vous choisissez une référence (SKU) est le nombre d’index pris en charge par chaque référence (SKU). La prise en charge de l’index est sensiblement différente dans les niveaux de tarification inférieurs. Les exigences en matière de nombre d’index sont déterminantes dans le choix d’une référence (SKU).
3. **Combien de documents** seront chargés dans chaque index ? Le nombre et la taille des documents déterminent la taille finale de l’index. En supposant que vous puissiez estimer la taille prévue de l’index, vous pouvez comparer ce nombre avec la taille de partition par référence (SKU), étendue par le nombre de partitions nécessaires pour stocker un index de cette taille. 
4. **Quelle est la charge de requête attendue** ? Une fois que les besoins de stockage sont compris, penchez-vous sur les charges de travail de requête. Les références (SKU) S2 et S3 offrent un débit presque équivalent, mais les exigences du contrat SLA écartent toute référence (SKU) de version préliminaire. 

La plupart des clients peuvent choisir ou écarter une référence (SKU) spécifique en fonction de leurs réponses à ces quatre questions. Si vous ne savez pas encore quelle référence (SKU) adopter, contactez le Support Azure pour plus d’informations.

## Décision de validation : la référence (SKU) offre-t-elle suffisamment de stockage et de RPS ?

La dernière étape consiste à revoir le [page de tarification](https://azure.microsoft.com/pricing/details/search/) et les [sections par service et par index dans les limites du service](search-limits-quotas-capacity.md) pour comparer vos estimations aux limites d’abonnement et de service.

Si le prix ou les exigences de stockage sont trop élevés, vous souhaiterez peut-être refactoriser les charges de travail entre plusieurs services plus petits (par exemple). À un niveau plus granulaire, vous pourriez recréer des index plus petits, ou utiliser des filtres pour effectuer des requêtes plus efficaces.

> [AZURE.NOTE] Les besoins de stockage peuvent être augmentés de façon excessive si les documents contiennent des données superflues. Dans l’idéal, les documents sont constitués uniquement de métadonnées ou de données interrogeables. Les données binaires qui peuvent encombrer un document doivent être stockées séparément (dans une table ou un objet blob de stockage Azure) avec un champ dans l’index pour conserver une référence URL aux données externes. La taille maximale d’un document individuel est de 16 Mo (ou moins si vous chargez en bloc plusieurs documents en une seule demande). Pour plus d’informations, voir [Limites de service d’Azure Search](search-limits-quotas-capacity.md) .

## Étape suivante

Une fois que vous avez déterminé la référence (SKU) qui convient le mieux, poursuivez avec ces étapes :
- [Créer un service de recherche dans le portail](search-create-service-portal.md)
- [Modifier l’allocation des partitions et des réplicas à l’échelle de votre service](search-capacity-planning.md)

<!---HONumber=AcomDC_0608_2016-->
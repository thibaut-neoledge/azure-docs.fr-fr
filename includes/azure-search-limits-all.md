Ressource|Gratuit|De base (version préliminaire) <sup>6</sup>|S1|S2 <sup>7</sup>
---|---|---|---|----
Nombre maximal de services de recherche<sup>8</sup>|1 par abonnement|12 par abonnement|12 par abonnement|1 par abonnement
Taille de stockage maximale <sup>1</sup>|50 Mo ou 10 000 documents|2 Go par service|25 Go par partition ou 300 Go de documents par service|100 Go par partition ou 1,2 To par service
Nombre maximal de documents hébergés|10 000 au total|1 million par service|15 millions par partition (jusqu’à 180 millions de documents par service)|60 millions par partition (jusqu’à 720 millions de documents par service)
Nombre maximal d’index|3|5|50|200
Nombre maximal d’indexeurs|3|5|50|200
Nombre maximal de sources de données d’indexeur|3|5|50|200
Index : nombre maximal de champs par index|1 000|100 <sup>5</sup>|1 000|1 000
Index : nombre maximal de profils de score par index|16|16|16|16
Index : nombre maximal de fonctions par profil|8|8|8|8
Indexeurs : quantité maximale de charge d’indexation par appel|10 000 documents|Limité uniquement par le nombre maximal de documents|Limité uniquement par le nombre maximal de documents|Limité uniquement par le nombre maximal de documents
Indexeurs : durée maximale d’exécution|3 minutes|24 heures|24 heures|24 heures
Indexeur d’objets blob : taille maximale des objets blob, en Mo|16|16|128|256
Indexeur d’objets blob : nombre maximal de caractères du contenu extrait d’un objet blob|32 000|64 000|4 millions|4 millions
Nombre de requêtes par seconde (QPS) <sup>2</sup>|N/A|~3 par réplica|~15 par réplica|~60 par réplica
Montée en charge : nombre maximal d’unités de recherche (SU) <sup>3</sup>|N/A|Jusqu’à 3 unités (3 réplicas et 1 partition)|36 unités|36 unités
Tarification <sup>4</sup>|N/A|75 $ par SU et par mois|250 $ par SU et par mois|1 000 $ par SU et par mois

<sup>1</sup> La taille de stockage est un volume fixe ou le nombre de documents par service, en fonction de la première occurrence.

<sup>2</sup> Le nombre de requêtes par seconde est une approximation basée sur une méthode heuristique. Il est calculé à l’aide de charges de travail client simulées et réelles pour obtenir des valeurs estimées. Le débit exact du nombre de requêtes par seconde varie en fonction de vos données et de la nature de la requête.

<sup>3</sup> Les unités de recherche désignent l’unité facturable pour un réplica ou une partition. Vous avez besoin des deux pour les opérations de stockage, d’indexation et de requête. Consultez la [planification de la capacité](../articles/search/search-capacity-planning.md) pour obtenir des combinaisons valides de réplicas et des partitions qui vous permettront de rester dans la limite maximale de 3 ou 36 unités (pour les versions De base et Standard, respectivement).

<sup>4</sup> Le prix indiqué vaut pour le marché américain et illustre les coûts relatifs sur différents niveaux. Différents marchés ont différents prix. Reportez-vous à la [page de tarification](https://azure.microsoft.com/pricing/details/search/) pour connaître les frais dans d’autres devises. Les frais sont indiqués par unité de recherche (SU). Au niveau S1, une configuration de 3 unités de recherche (par exemple, 3 réplicas et 1 partition) coûterait 750 $ par mois en moyenne. Si vous descendez en puissance et réduisez le nombre d’unités de recherche au cours du moins, la facture et adaptée en conséquence et vous ne payez que ce que vous utilisez.

<sup>5</sup> Le niveau de base est le seul niveau de tarification soumis à une limite inférieure de 100 champs par index.

<sup>6</sup> Le [niveau de base](http://aka.ms/azuresearchbasic) est disponible à un prix inférieur de 50 % du prix complet pendant la période de version préliminaire.

<sup>7</sup> S2 nécessite un ticket de service. Il ne peut pas être configuré dans le portail. Veuillez contacter le Support technique ou envoyer un courrier électronique à azuresearch_contact@microsoft.com pour obtenir une assistance.

<sup>8</sup> Vous pouvez augmenter le nombre maximal de services par abonnement pour tous les niveaux sauf le niveau gratuit. Veuillez contacter le Support technique ou envoyer un courrier électronique à azuresearch_contact@microsoft.com pour obtenir une assistance.

<!---HONumber=AcomDC_0525_2016-->
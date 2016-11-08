Le stockage est limité par l’espace disque ou le *nombre maximum* d’index ou de documents inconditionnel, selon la limite atteinte en premier.

| Ressource | Gratuit | De base | S1 | S2 | S3 <br/>(version préliminaire) | S3 HD <br/>(version préliminaire) |
| --- | --- | --- | --- | --- | --- | --- |
| Contrat de Niveau de Service (SLA) |Non <sup>1</sup> |Oui |Oui |Oui |Non <sup>1</sup> |Non <sup>1</sup> |
| Stockage par service |50 Mo |2 Go |300 Go |1,2 To |2,4 To |200 Go |
| Partitions par service |N/A |1 |12 |12 |12 |1 |
| Taille de partition |N/A |2 Go |25 Go |100 Go |200 Go |200 Go |
| Réplicas |N/A |1 |12 |12 |12 |12 |
| Nombre maximal d’index |3 |5 |50 |200 |200 |1 000 |
| Nombre maximal de documents |10 000 |1 million |180 millions par service, 15 millions par partition |720 millions de documents par service, 60 millions par partition |1,4 milliards de documents par service, 120 millions par partition |200 millions par service, 1 million par index |
| Nombre de requêtes par seconde (QPS) estimé |N/A |~3 par réplica |~15 par réplica |~60 par réplica |Moins de 60 par réplica |Moins de 60 par réplica |

<sup>1</sup> Les références gratuites et les versions préliminaires ne sont pas fournies avec les contrats de niveau de service. Des contrats de niveau de service sont mis en œuvre dès qu’une référence (SKU) est généralement disponible.

<!---HONumber=AcomDC_0608_2016-->
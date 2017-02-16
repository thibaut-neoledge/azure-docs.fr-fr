<!--
Used in:
sql-database-performance-guidance.md  
sql-database-resource-limits.md
sql-database-service-tiers.md  
-->

### <a name="basic-service-tier"></a>Niveau de service De base
| **Niveau de service** | **De base** |
| --- | :---: |
| DTU max | 5 |
| Taille de base de données maximale* |2 Go|
| Stockage In-Memory OLTP maximal |N/A |
| Nombre maximal d’ouvriers simultanés |30 |
| Nombre maximal de connexions simultanées |30 |
| Nombre maximal de sessions simultanées |300 |
|||

### <a name="standard-service-tier"></a>Niveau de service Standard
| **Niveau de service** | **S0** | **S1** | **S2** | **S3** |
| --- |---:| ---:|---:|---:|---:|
| DTU max | 10 | 20 | 50 | 100 |
| Taille de base de données maximale* | 250 Go| 250 Go | 250 Go | 250 Go |
| Stockage In-Memory OLTP maximal | N/A | N/A | N/A | N/A |
| Nombre maximal d’ouvriers simultanés | 60 | 90 | 120 | 200 |
| Nombre maximal de connexions simultanées | 60 | 90 | 120 | 200 |
| Nombre maximal de sessions simultanées |600 | 900 | 1 200 | 2 400 |
||||||

### <a name="premium-service-tier"></a>Niveau de service Premium 
| **Niveau de service** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** | 
| --- |---:|---:|---:|---:|---:|---:|
| DTU max | 125 | 250 | 500 | 1 000 | 1 750 | 4000 |
| Taille de base de données maximale* | 500 Go | 500 Go | 500 Go | 500 Go | 1 To | 1 To |
| Stockage In-Memory OLTP maximal | 1 Go | 2 Go | 4 Go | 8 Go | 14 Go | 32 Go |
| Nombre maximal d’ouvriers simultanés | 200 | 400 | 800 | 1 600 | 2 400 | 6400 |
| Nombre maximal de connexions simultanées | 200 | 400| 800| 1 600| 2 400| 6 400 |
| Nombre maximal de sessions simultanées | 30000| 30000| 30000| 30000| 30000| 30000 |
|||||||

\* La taille de base de données maximale fait référence à la taille maximale des fichiers de données et n’inclut pas l’espace utilisé par les fichiers journaux.

<!--HONumber=Jan17_HO2-->



<!--
Used in:
sql-database-elastic-pool.md   
sql-database-resource-limits.md
sql-database-service-tiers.md  
-->
 
### <a name="basic-elastic-pool-limits"></a>Limites du pool élastique de base

| Taille du pool (nombre d’eDTU)  | **50** | **100** | **200** | **300** | **400** | **800** | **1 200** | **1 600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Espace de stockage de données maximal par pool* | 5 GO | 10 Go | 20 Go | 29 Go | 39 Go | 78 Go | 117 Go | 156 Go |
| Stockage OLTP en mémoire maximal par pool | N/A  | N/A | N/A | N/A | N/A | N/A | N/A | N/A |
| Nombre maximal de bases de données par pool | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Nombre maximal d’ouvriers simultanés (demandes) par pool | 100 | 200 | 400 | 600 | 800 | 1 600 | 2 400 | 3200 |
| Nombre maximal de connexions simultanées par pool | 100 | 200 | 400 | 600 | 800 | 1 600 | 2 400 | 3200 |
| Nombre maximal de sessions simultanées par pool | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Nombre minimal d’eDTU par base de données | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Nombre maximal d’eDTU par base de données | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Espace de stockage de données maximal par base de données | 2 Go | 2 Go | 2 Go | 2 Go | 2 Go | 2 Go | 2 Go | 2 Go | 
||||||||

### <a name="standard-elastic-pool-limits"></a>Limites du pool élastique standard

| Taille du pool (nombre d’eDTU)  | **50** | **100** | **200**** | **300**** | **400**** | **800****| 
|:---|---:|---:|---:| ---: | ---: | ---: | 
| Espace de stockage de données maximal par pool* | 50 Go| 100 Go| 200 Go | 300 Go| 400 Go | 800 Go | 
| Stockage OLTP en mémoire maximal par pool | N/A  | N/A | N/A | N/A | N/A | N/A | 
| Nombre maximal de bases de données par pool | 100 | 200 | 500 | 500 | 500 | 500 | 
| Nombre maximal d’ouvriers simultanés (demandes) par pool | 100 | 200 | 400 | 600 |  800 | 1 600 |
| Nombre maximal de connexions simultanées par pool | 100 | 200 | 400 | 600 |  800 | 1 600 |
| Nombre maximal de sessions simultanées par pool | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Nombre minimal d’eDTU par base de données** | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Nombre maximal d’eDTU par base de données** | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 | 
| Espace de stockage de données maximal par base de données | 50 Go | 100 Go | 200 Go | 250 Go | 250 Go | 250 Go |
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>Limites du pool élastique standard (suite) 

| Taille du pool (nombre d’eDTU)  |  **1200**** | **1600**** | **2000**** | **2500**** | **3000**** |
|:---|---:|---:|---:| ---: | ---: |
| Espace de stockage de données maximal par pool* | 1,2 To | 1,6 To | 2 To | 2,4 To | 2,9 To | 
| Stockage OLTP en mémoire maximal par pool | N/A  | N/A | N/A | N/A | N/A | 
| Nombre maximal de bases de données par pool | 500 | 500 | 500 | 500 | 500 | 
| Nombre maximal d’ouvriers simultanés (demandes) par pool |  2 400 | 3200 | 4000 | 5 000 | 6000 |
| Nombre maximal de connexions simultanées par pool |  2 400 | 3200 | 4000 | 5 000 | 6000 |
| Nombre maximal de sessions simultanées par pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Nombre minimal d’eDTU par base de données** | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Nombre maximal d’eDTU par base de données** | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 | 
| Espace de stockage de données maximal par base de données | 250 Go | 250 Go | 250 Go | 250 Go | 250 Go | 
||||||||

### <a name="premium-elastic-pool-limits"></a>Limites du pool élastique Premium

| Taille du pool (nombre d’eDTU)  | **125** | **250** | **500** | **1 000** | **1500*****| 
|:---|---:|---:|---:| ---: | ---: | 
| Espace de stockage de données maximal par pool* | 250 Go | 500 Go | 750 Go | 1 To | 1,5 To | 
| Stockage OLTP en mémoire maximal par pool | 1 Go| 2 Go| 4 Go| 10 Go| 12 Go| 
| Nombre maximal de bases de données par pool | 50 | 100 | 100 | 100 | 100 |  
| Nombre maximal d’ouvriers simultanés par pool (demandes) | 200 | 400 | 800 | 1 600 |  2 400 | 
| Nombre maximal de connexions simultanées par pool | 200 | 400 | 800 | 1 600 |  2 400 |
| Nombre maximal de sessions simultanées par pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Nombre minimal d’eDTU par base de données | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000 | 
| Nombre maximal d’eDTU par base de données | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000 |
| Espace de stockage de données maximal par base de données | 250 Go | 500 Go | 500 Go | 500 Go | 500 Go | 
||||||||

### <a name="premium-elastic-pool-limits-continued"></a>Limites du pool élastique Premium (suite) 

| Taille du pool (nombre d’eDTU) | **2000***** | **2500***** | **3000***** | **3500***** | **4000*****|
|:---|---:|---:|---:| ---: | ---: | 
| Espace de stockage de données maximal par pool* | 2 To | 2,5 To | 3 To | 3,5 To | 4 To |
| Stockage OLTP en mémoire maximal par pool | 16 Go | 20 Go | 24 Go | 28 Go | 32 Go |
| Nombre maximal de bases de données par pool | 100 | 100 | 100 | 100 | 100 | 
| Nombre maximal d’ouvriers simultanés (demandes) par pool |  3200 | 4000 | 4 800 | 5 600 | 6 400 |
| Nombre maximal de connexions simultanées par pool |  3200 | 4000 | 4 800 | 5 600 | 6 400 |
| Nombre maximal de sessions simultanées par pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Nombre minimal d’eDTU par base de données | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 |  0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Nombre maximal d’eDTU par base de données | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Espace de stockage de données maximal par base de données | 500 Go | 500 Go | 500 Go | 500 Go | 500 Go | 
||||||||

### <a name="premium-rs-elastic-pool-limits"></a>Limites du pool élastique Premium RS

| Taille du pool (nombre d’eDTU)  | **125** | **250** | **500** | **1 000** |
|:---|---:|---:|---:| ---: | ---: | 
| Espace de stockage de données maximal par pool* | 250 Go| 500 Go | 750 Go | 750 Go |
| Stockage OLTP en mémoire maximal par pool | 1 Go | 2 Go | 4 Go | 10 Go |
| Nombre maximal de bases de données par pool | 50 | 100 | 100 | 100 |
| Nombre maximal d’ouvriers simultanés (demandes) par pool | 200 | 400 | 800 | 1 600 |
| Nombre maximal de connexions simultanées par pool | 200 | 400 | 800 | 1 600 |
| Nombre maximal de sessions simultanées par pool | 30000 | 30000 | 30000 | 30000 |
| Nombre minimal d’eDTU par base de données | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 |
| Nombre maximal d’eDTU par base de données | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 
| Espace de stockage de données maximal par base de données | 250 Go | 500 Go | 500 Go | 500 Go | 
||||||||

> [!IMPORTANT]
>\* Les bases de données regroupées se partagent l’espace de stockage du pool. Par conséquent, le stockage de données dans un pool élastique est limité au stockage de pool minimal restant ou au stockage maximal par base de données. 
>
>
>\*\* Le nombre minimal/maximal d’eDTU par base de données commençant à 200 eDTU et plus est en préversion publique.
>
>\*\*\* Le stockage de données max. par défaut par pool pour les pools Premium avec 500 eDTU ou plus est de 750 Go. Pour obtenir la taille maximale de stockage de données par pool Premium pour 1000 eDTU ou plus, vous devez explicitement la sélectionner à l’aide du portail Azure, de [PowerShell](../articles/sql-database/sql-database-elastic-pool.md#manage-sql-database-elastic-pools-using-powershell), de l’[interface de ligne de commande Azure](../articles/sql-database/sql-database-elastic-pool.md#manage-sql-database-elastic-pools-using-the-azure-cli) ou de [l’API REST](../articles/sql-database/sql-database-elastic-pool.md#manage-sql-database-elastic-pools-using-the-rest-api). Les pools Premium avec plus de 1 To de stockage sont actuellement en préversion publique dans les régions suivantes : Est des États-Unis 2, États-Unis de l’Ouest, Gouvernement des États-Unis - Virginie, Europe de l’Ouest, Centre de l’Allemagne, Asie du Sud-Est, Japon de l’Est, Est de l’Australie, Canada Centre et Canada Est. Le stockage maximal par pool pour toutes les autres régions est actuellement limité à 750 Go.
>

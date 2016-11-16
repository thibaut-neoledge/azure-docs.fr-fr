
### <a name="basic-elastic-pool-limits"></a>Limites du pool élastique de base
|  |  |
| --- |:---:|
| eDTU max. par pool |&nbsp;100 &nbsp;&nbsp;&nbsp; 200 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp;&nbsp; 1200 |
| Espace de stockage maximal par pool (Go)* |&nbsp;&nbsp;&nbsp;&nbsp;10 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;20 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;39 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;73 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;117 |
| Nombre maximal de bases de données par pool |&nbsp;&nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 |
| Stockage In-Memory OLTP maximal par pool (Go) |N/A |
| Nombre maximal d’ouvriers simultanés par pool |&nbsp;&nbsp;&nbsp;200 &nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp;&nbsp;2400 |
| Nombre maximal de connexions simultanées par pool |&nbsp;&nbsp;&nbsp;200 &nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp;&nbsp;2400 |
| Nombre maximal de sessions simultanées par pool |4800 &nbsp;9600 &nbsp; 19200 &nbsp; 28800 &nbsp; 28800 |
| Nombre maximal d’eDTU par base de données* |5 |
| Nombre minimal d’eDTU par base de données* |0,5 |
| Espace de stockage maximal par base de données (Go)** |2 |
| Restauration dans le temps |N’importe quel point dans les 7 derniers jours |
| Récupération d'urgence |Géo-réplication active |
|  | |

* Le nombre maximal et minimal d’eDTU par base de données peut être défini sur les valeurs répertoriées de votre choix dans la mesure où la taille du pool en DTU sélectionnée est au moins aussi grande que le nombre maximal d’eDTU par base de données 

** Les bases de données élastiques se partagent l’espace de stockage du pool. Par conséquent, le stockage de base de données est limité au stockage de pool minimal restant ou au stockage maximal par base de données

### <a name="standard-elastic-pool-limits"></a>Limites du pool élastique standard
|  |  |
| --- |:---:|
| eDTU max. par pool |&nbsp;100 &nbsp;&nbsp;&nbsp; 200 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp;&nbsp; 1200 |
| Espace de stockage maximal par pool (Go)* |&nbsp;100 &nbsp;&nbsp;&nbsp; 200 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp;&nbsp; 1200 |
| Nombre maximal de bases de données par pool |&nbsp;200 &nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 |
| Stockage In-Memory OLTP maximal par pool (Go) |N/A |
| Nombre maximal d’ouvriers simultanés par pool |&nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp; 2400 |
| Nombre maximal de connexions simultanées par pool |&nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp; 2400 |
| Nombre maximal de sessions simultanées par pool |4800 &nbsp; 9600 &nbsp;19200 &nbsp;28800 &nbsp;&nbsp; 28800 |
| Nombre maximal d’eDTU par base de données* |10, 20, 50, 100 |
| Nombre minimal d’eDTU par base de données* |0, 10, 20, 50, 100 |
| Espace de stockage maximal par base de données (Go)** |250 |
| Restauration dans le temps |N’importe quel point dans les 35 derniers jours |
| Récupération d'urgence |Géo-réplication active |
|  | |

* Le nombre maximal et minimal d’eDTU par base de données peut être défini sur les valeurs répertoriées de votre choix dans la mesure où la taille du pool en DTU sélectionnée est au moins aussi grande que le nombre maximal d’eDTU par base de données 

** Les bases de données élastiques se partagent l’espace de stockage du pool. Par conséquent, le stockage de base de données est limité au stockage de pool minimal restant ou au stockage maximal par base de données

### <a name="premium-elastic-pool-limits"></a>Limites du pool élastique Premium
|  |  |
| --- |:---:|
| eDTU max. par pool |125 &nbsp;&nbsp;&nbsp; 250 &nbsp;&nbsp;&nbsp; 500 &nbsp;&nbsp;&nbsp; 1000 &nbsp;&nbsp;&nbsp; &nbsp;1500 |
| Espace de stockage maximal par pool (Go)* |250 &nbsp;&nbsp;&nbsp; 500 &nbsp;&nbsp;&nbsp; 750 &nbsp;&nbsp;&nbsp;&nbsp; 750 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 750 |
| Nombre maximal de bases de données par pool |50 |
| Stockage In-Memory OLTP maximal par pool (Go) |N/A |
| Nombre maximal d’ouvriers simultanés par pool |&nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp; 2400 |
| Nombre maximal de connexions simultanées par pool |&nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp; 2400 |
| Nombre maximal de sessions simultanées par pool |4800 &nbsp; 9600 &nbsp;19200 &nbsp;28800 &nbsp;&nbsp; 28800 |
| Nombre maximal d’eDTU par base de données* |125, 250, 500, 1000 |
| Nombre minimal d’eDTU par base de données* |0, 125, 250, 500, 1000 |
| Espace de stockage maximal par base de données (Go)** |500 |
| Restauration dans le temps |N’importe quel point dans les 35 derniers jours |
| Récupération d'urgence |Géo-réplication active |
|  | |

* Le nombre maximal et minimal d’eDTU par base de données peut être défini sur les valeurs répertoriées de votre choix dans la mesure où la taille du pool en DTU sélectionnée est au moins aussi grande que le nombre maximal d’eDTU par base de données 

** Les bases de données élastiques se partagent l’espace de stockage du pool. Par conséquent, le stockage de base de données est limité au stockage de pool minimal restant ou au stockage maximal par base de données



<!--HONumber=Nov16_HO2-->



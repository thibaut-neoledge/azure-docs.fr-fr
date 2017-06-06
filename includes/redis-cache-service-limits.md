| Ressource | Limite |
| --- | --- |
| Taille du cache |530 Go |
| Bases de données |64 |
| Nombre maximal de clients connectés |40 000 |
| Réplicas du cache Redis (pour la haute disponibilité) |1 |
| Partitions dans un cache premium avec le clustering |10 |

Les limites et les tailles des solutions de cache Redis Azure varient en fonction du niveau de tarification. Pour connaître les niveaux de tarification et les tailles associées, consultez la section [Tarification du cache Redis Azure](https://azure.microsoft.com/pricing/details/cache/).

Pour plus d’informations sur les limites de configuration du cache Redis Azure, consultez la section [Configuration du serveur Redis par défaut](../articles/redis-cache/cache-configure.md#default-redis-server-configuration).

Étant donné que la configuration et la gestion des instances de cache Redis Azure sont gérées par Microsoft, toutes les commandes Redis ne sont pas prises en charge dans le cache. Pour plus d’informations, consultez [Commandes Redis non prises en charge dans le Cache Redis Azure](../articles/redis-cache/cache-configure.md#redis-commands-not-supported-in-azure-redis-cache).


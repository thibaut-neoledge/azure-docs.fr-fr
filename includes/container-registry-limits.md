| Ressource | De base | Standard | Premium |
|---|---|---|---|---|
| Storage | 10 Go | 100 Go| 500 Go |
| ReadOps par minute<sup>1, 2</sup> | 1000 | 300 000 | 10 000 000 |
| WriteOps par minute<sup>1, 3</sup> | 100 | 500 | 2000 |
| Bande passante de téléchargement en Mbits/s<sup>1</sup> | 30 | 60 | 100 |
| Bande passante de chargement en Mbits/s<sup>1</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 100 |
| Géoréplication | N/A | N/A | [Prise en charge *(préversion)*](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication) |

<sup>1</sup> Les valeurs *ReadOps*, *WriteOps* et de *bande passante* sont des estimations minimales. ACR s’efforce d’améliorer les performances en fonction de l’utilisation requise.

<sup>2</sup> [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) se traduit par plusieurs opérations de lecture en fonction du nombre de couches dans l’image, plus la récupération du manifeste.

<sup>3</sup> [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) se traduit par plusieurs opérations d’écriture, en fonction du nombre de couches qui doivent être envoyées. Un `docker push` inclut des *ReadOps* pour récupérer un manifeste pour une image existante.
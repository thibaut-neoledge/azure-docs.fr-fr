Data Factory est un service mutualisé qui possède, par défaut, les limites suivantes pour garantir la protection des abonnements clients contre les autres charges de travail. La plupart des limites de votre abonnement peuvent être facilement repoussées jusqu’à la limite maximale en contactant le support. 

| **Ressource** | **Limite par défaut** | **Limite maximale** |
| --- | --- | --- |
| fabriques de données d’un abonnement Azure |50 |[Contacter le support technique](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| pipelines dans une fabrique de données |2 500 |[Contacter le support technique](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| jeux de données dans une fabrique de données |5 000 |[Contacter le support technique](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| tranches simultanées par jeu de données |10 |10 |
| octets par objet pour les objets pipeline <sup>1</sup> |200 Ko |2 000 Ko |
| octets par objet pour les objets jeu de données et service lié <sup>1</sup> |100 Ko |2 000 Ko |
| champs par objet |100 |[Contacter le support technique](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| octets par nom de champ ou identificateur |2 Ko |[Contacter le support technique](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| octets par champ |30 Ko |[Contacter le support technique](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Cœurs de cluster HDInsight à la demande d’un abonnement<sup>2</sup> |48 |[Contacter le support technique](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Nombre de nouvelles tentatives pour les exécutions d’activités de pipeline |1 000 |MaxInt (32 bits) |

<sup>1</sup> Les objets Pipeline, DataSet et LinkedService correspondent à un regroupement logique de votre charge de travail. Les limites de ces objets ne sont pas liées à la quantité de données que vous pouvez déplacer ou traiter à l’aide du service Azure Data Factory. Data Factory est conçu pour permettre une mise à l’échelle de plusieurs pétaoctets de données.

<sup>2</sup> Les cœurs HDInsight à la demande sont alloués en dehors de l’abonnement qui contient la fabrique de données. Par conséquent, la limite indiquée ci-dessus correspond à la limite appliquée par Data Factory pour les cœurs HDInsight à la demande et est différente de la limite associée à votre abonnement Azure.

| **Ressource** | **Limite inférieure par défaut** | **Limite minimale** |
| --- | --- | --- |
| Intervalle de planification |15 minutes |15 minutes |
| Intervalle entre les nouvelles tentatives |1 seconde |1 seconde |
| Délai d’expiration des nouvelles tentatives |1 seconde |1 seconde |

### <a name="web-service-call-limits"></a>Limites d’appels du service web
Azure Resource Manager fixe des limites aux appels d’API. Vous pouvez effectuer des appels d’API à une fréquence comprise dans les [limites d’API d’Azure Resource Manager](../articles/azure-subscription-service-limits.md#resource-group-limits). 

<!--HONumber=Oct16_HO2-->



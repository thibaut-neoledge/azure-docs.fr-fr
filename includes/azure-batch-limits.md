| **Ressource** | **Limite par défaut** | **Limite maximale** |
| --- | --- | --- |
| Comptes Batch par région et par abonnement |3 |50 |
| Cœurs par compte Batch (mode de service de Batch)<sup>1</sup> |20 |N/A<sup>2</sup> |
| Travaux et planifications de travaux actifs<sup>3</sup> par compte Batch |20 |5000<sup>4</sup> |
| Pools par compte Batch |20 |2 500 |

<sup>1</sup> Les quotas de cœurs affichés concernent uniquement les comptes qui possèdent le mode d’allocation de pool du **service Batch**. Pour les comptes utilisant le mode défini dans **l’abonnement utilisateur**, les quotas de cœurs sont basés sur le quota de VM cœurs au niveau régional ou par famille de VM spécifiée dans votre abonnement.

<sup>2</sup> Vous pouvez augmenter le nombre de cœurs par compte Batch, mais le nombre maximal n’est pas spécifié. Contactez l'assistance clientèle Azure pour discuter des possibilités d'augmentation.

<sup>3</sup> Les travaux et planifications de travaux terminés ne sont pas limités.

<sup>4</sup> Contactez le service clientèle Azure si vous voulez dépasser cette limite.

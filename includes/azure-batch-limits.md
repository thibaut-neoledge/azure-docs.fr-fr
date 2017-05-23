| **Ressource** | **Limite par défaut** | **Limite maximale** |
| --- | --- | --- |
| Comptes Batch par région et par abonnement | 3 |50 |
| Cœurs dédiés par compte Batch (mode de service de Batch)<sup>1</sup> | 20 | N/A<sup>2</sup> |
| Cœurs de faible priorité par compte Batch (mode de service de Batch)<sup>3</sup> | 50 | N/A<sup>4</sup> |
| Travaux et planifications de travaux actifs<sup>5</sup> par compte Batch | 20 | 5000<sup>6</sup> |
| Pools par compte Batch | 20 | 2 500 |

<sup>1</sup> Les quotas de cœurs dédiés concernent uniquement les comptes avec le mode d’allocation de pool défini sur **service Batch**. Pour les comptes utilisant le mode défini dans **l’abonnement utilisateur**, les quotas de cœurs sont basés sur le quota de machine virtuelle au niveau régional ou par famille de machine virtuelle spécifiée dans votre abonnement.

<sup>2</sup> Vous pouvez augmenter le nombre de cœurs dédiés par compte Batch, mais le nombre maximal n’est pas spécifié. Contactez l'assistance clientèle Azure pour discuter des possibilités d'augmentation.

<sup>3</sup> Les quotas de cœurs de faible priorité concernent uniquement les comptes avec le mode d’allocation de pool défini sur **service Batch**. Les cœurs de faible priorité ne sont pas disponibles pour les comptes avec le mode d’allocation de pool défini sur **l’abonnement utilisateur**.

<sup>4</sup> Vous pouvez augmenter le nombre de cœurs de faible priorité par compte Batch, mais le nombre maximal n’est pas spécifié. Contactez l'assistance clientèle Azure pour discuter des possibilités d'augmentation.

<sup>5</sup> Les travaux et planifications de travaux terminés ne sont pas limités.

<sup>6</sup> Contactez le service clientèle Azure si vous voulez dépasser cette limite.

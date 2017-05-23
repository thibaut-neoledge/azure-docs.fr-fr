| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| Machines virtuelles par [abonnement](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> par région |10 000 par région |
| Nombre total de cœurs de machine virtuelle par [abonnement](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> par région | Contacter le support technique |
| Nombre de cœurs de gamme de machine virtuelle (Dv2, F, etc.) par [abonnement](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> par région | Contacter le support technique |
| [Coadministrateurs](../articles/billing-add-change-azure-subscription-administrator.md) par abonnement |Illimité |Illimité |
| [Comptes de stockage](../articles/storage/storage-create-storage-account.md) par abonnement |200 |200<sup>2</sup> |
| [Groupes de ressources](../articles/azure-resource-manager/resource-group-overview.md) par abonnement |800 |800 |
| [Groupes à haute disponibilité](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) par abonnement |2 000 par région |2 000 par région |
| Lectures API Resource Manager |15 000 par heure |15 000 par heure |
| Écritures API Resource Manager |1 200 par heure |1 200 par heure |
| Taille de la demande d’API Resource Manager |4 194 304 octets |4 194 304 octets |
| Balises par abonnement |10000 |10000 |
| [Services cloud](../articles/cloud-services/cloud-services-choose-me.md) par abonnement |Non applicable<sup>3</sup> |Non applicable<sup>3</sup> |
| [Groupes d'affinités](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) par abonnement |Non applicable<sup>3</sup> |Non applicable<sup>3</sup> |

<sup>1</sup>Les limites par défaut varient selon le type de catégorie d’offre, comme Essai gratuit ou Paiement à l’utilisation, et selon la gamme (Dv2, F, G, etc.).

<sup>2</sup>Cela inclut à la fois les comptes de stockage Standard et Premium. Si vous avez besoin de plus de 200 comptes de stockage, sollicitez le [Support Azure](https://azure.microsoft.com/support/faq/)pour obtenir une assistance. L’équipe Azure Storage examinera votre cas d’entreprise et pourra approuver jusqu’à 250 comptes de stockage.

<sup>3</sup>Ces fonctionnalités ne sont plus nécessaires avec les groupes de ressources Azure et Azure Resource Manager.

> [!NOTE]
> Il est important de noter qu’une limite totale régionale et une limite régionale par gamme de taille (Dv2, F, etc.) s’appliquent séparément aux cœurs de machine virtuelle.  Par exemple, considérons un abonnement dont le nombre total limite de cœurs de machine virtuelle est de 30 pour la région Est, de 30 pour la gamme A et de 30 pour la gamme D.  Cet abonnement serait autorisé à déployer 30 machines virtuelles A1, ou 30 machines virtuelles D1, ou encore une combinaison de ces deux types de machines dans la limite de 30 cœurs au total (par exemple, 10 machines virtuelles A1 et 20 machines virtuelles D1).  
> <!-- -->
> 
> 


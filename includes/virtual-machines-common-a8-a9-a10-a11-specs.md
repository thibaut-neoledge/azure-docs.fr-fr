

## <a name="deployment-considerations"></a>Points à prendre en considération pour le déploiement
* **Abonnement Azure** : pour déployer un plus grand nombre d’instances de calcul intensif, envisagez de souscrire un abonnement de paiement à l’utilisation ou d’autres options d’achat. Si vous utilisez un [compte gratuit Azure](https://azure.microsoft.com/free/), vous pouvez seulement utiliser un nombre limité de cœurs de calcul Azure.

* **Tarification et disponibilité** : ces tailles de machines virtuelles sont proposées uniquement au niveau tarifaire Standard. Consultez [Disponibilité des produits par région] (https://azure.microsoft.com/regions/services/) pour connaître la disponibilité dans les régions Azure. 
* **Quota de cœurs** : vous devrez peut-être augmenter le quota de cœurs dans votre abonnement Azure à partir de la valeur par défaut. Votre abonnement peut également limiter le nombre de cœurs, que vous pouvez déployer dans certaines familles de taille de machine virtuelle, dont la série H. Pour demander une augmentation de quota, [ouvrez une demande de service clientèle en ligne](../articles/azure-supportability/how-to-create-azure-support-request.md) gratuitement. (Les limites par défaut peuvent varier en fonction de la catégorie de votre abonnement.)
  
  > [!NOTE]
  > Si vous avez des besoins de capacité à grande échelle, contactez le support Azure. Les quotas d’Azure sont des limites de crédit et non des garanties de capacité. Quel que soit votre quota, vous êtes facturé uniquement pour les cœurs que vous utilisez.
  > 
  > 
* **Réseau virtuel** : aucun [réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/) Azure n’est requis pour utiliser les instances qui nécessitent beaucoup de ressources système. Cependant, pour bon nombre de scénarios de déploiement, vous avez besoin d’au moins un réseau virtuel Azure cloud ou d’une connexion de site à site si vous devez accéder à des ressources locales. Si nécessaire, créez un réseau virtuel avant de déployer les instances. L’ajout de machines virtuelles nécessitant beaucoup de ressources système à un réseau virtuel dans un groupe d’affinités n’est pas pris en charge.
* **Redimensionnement** : en raison de leur matériel spécialisé, vous pouvez uniquement redimensionner les instances nécessitant beaucoup de ressources système qui appartiennent à la même famille de taille (série H ou série A nécessitant beaucoup de ressources système). Par exemple, vous pouvez redimensionner une machine virtuelle de la série H uniquement d’une seule taille en une autre de cette même série. Le redimensionnement d’une taille ne nécessitant pas beaucoup de ressources système en une taille nécessitant beaucoup de ressources système n’est pas pris en charge.  

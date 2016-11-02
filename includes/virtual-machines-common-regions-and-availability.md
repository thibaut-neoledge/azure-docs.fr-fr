# <a name="regions-and-availability-for-virtual-machines-in-azure"></a>Régions et disponibilité des machines virtuelles dans Azure
Il est important de comprendre le fonctionnement des machines virtuelles et de connaître leur emplacement dans Azure, ainsi que les différentes options disponibles pour optimiser les performances, la disponibilité et la redondance. Azure fonctionne dans plusieurs centres de données à travers le monde. Ces centres de données sont regroupés en régions géographiques, ce qui vous donne une certaine flexibilité dans le choix de l’emplacement où vous créez vos applications. Cet article fournit une vue d’ensemble des fonctionnalités d’optimisation de la disponibilité et de redondance d’Azure.

## <a name="what-are-azure-regions?"></a>Que représentent les régions Azure ?
Azure vous permet de créer des ressources, par exemple des machines virtuelles, dans des régions géographiques définies comme ’États-Unis de l’Ouest’, ’Europe du Nord’ ou ’Sud-Est asiatique’. Il existe actuellement 30 régions Azure dans le monde. Vous pouvez consulter la [liste de ces régions et leurs emplacements](https://azure.microsoft.com/regions/). Chaque région comporte plusieurs centres de données, afin de garantir la redondance et la disponibilité. Lorsque vous générez vos applications, vous bénéficiez ainsi d’une certaine flexibilité pour créer des machines virtuelles plus proches de vos utilisateurs et pour répondre à n’importe quel besoin juridique, de conformité ou fiscal.

## <a name="special-azure-regions"></a>Régions Azure spéciales
Lors de la création de vos applications, vous pouvez utiliser certaines régions Azure spéciales pour répondre à vos besoins de conformité ou juridiques. Ces régions spéciales sont les suivantes :

- **Gouvernement des États-Unis - Virginie** et **Gouvernement des États-Unis - Iowa**
    - Instance physique et logique d’Azure isolée du réseau pour les administrations et les partenaires des États-Unis, gérée par un personnel autorisé aux États-Unis. Inclut des certifications de conformité supplémentaires comme [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP) et [DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA). En savoir plus sur [Azure Government](https://azure.microsoft.com/features/gov/).
- **Inde centrale**, **Inde du Sud** et **Inde de l’Ouest**
    - Ces régions sont actuellement accessibles aux clients et partenaires du programme de licence en volume avec une inscription locale en Inde. L’accès sera proposé aux abonnements en ligne tout au long de l’année 2016.
- **Est de la Chine** et **Nord de la Chine**
    - Ces régions sont disponibles grâce à un partenariat unique conclu entre Microsoft et 21Vianet, qui stipule que Microsoft ne gère pas directement les centres de données. En savoir plus sur [Microsoft Azure en Chine](http://www.windowsazure.cn/).
- **Centre de l’Allemagne** et **Nord-Est de l’Allemagne**
    - Ces régions sont actuellement disponibles par le biais d’un modèle d’administration des données, dans lequel les données client sont conservées en Allemagne sous le contrôle de T-Systems, société Deutsche Telekom responsable de l’administration des données allemandes.

## <a name="region-pairs"></a>Paires de régions
Chaque région Azure est associée à une autre région au sein de la même région géographie (par exemple, États-Unis, Europe ou Asie). Cette approche permet la réplication des ressources, par exemple le stockage des machines virtuelles, sur l’ensemble d’une région, réduisant ainsi la probabilité de catastrophes naturelles, de troubles civils, de coupures de courant ou de pannes de réseau physique affectant les deux régions en même temps. Les paires de régions offrent les avantages supplémentaires suivants :

- En cas d’une panne Azure plus importante, une région est hiérarchisée dans chaque paire afin de réduire le temps de restauration des applications. 
- Les mises à jour Azure planifiées sont déployées vers les régions jumelées une par une pour limiter les interruptions de service et les risques de panne de l’application.
- Les données continuent de résider dans la même zone géographique que la région avec laquelle elle est jumelée (sauf le Sud du Brésil) pour répondre aux exigences en termes d’impôts et d’application de la loi.

Voici quelques exemples de paires de régions :

| Primaire        | Secondaire   |
|:---------------|:------------|
| Ouest des États-Unis        | Est des États-Unis     |
| Europe du Nord   | Europe de l'Ouest |
| Asie du Sud-Est | Est de l'Asie   |

Vous pouvez afficher la [liste des paires de régions ici](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

## <a name="feature-availability"></a>Disponibilité des fonctionnalités
Certains services ou fonctionnalités des machines virtuelles ne sont disponibles que dans certaines régions, par exemple des tailles de machines virtuelles ou des types de stockage spécifiques. De même, certains services Azure globaux ne vous obligent pas à sélectionner une région particulière, par exemple [Azure Active Directory](../articles/active-directory/active-directory-whatis.md), [Traffic Manager](../articles/traffic-manager/traffic-manager-overview.md), ou [Azure DNS](../articles/dns/dns-overview.md). Pour vous aider à concevoir votre environnement d’application, vous pouvez vérifier la [disponibilité des services Azure dans chaque région](https://azure.microsoft.com/regions/#services). 


## <a name="storage-availability"></a>Disponibilité du stockage
Il est important de bien comprendre les régions géographiques Azure lorsque vous considérez les options de réplication de stockage Azure disponibles. Lorsque vous créez un compte de stockage, vous devez sélectionner une des options de réplication suivantes :

- Stockage localement redondant (LRS)
    - Réplique trois fois vos données dans la région dans laquelle vous avez créé votre compte de stockage.
- Stockage redondant dans une zone (ZRS)
    - Réplique trois fois vos données dans deux ou trois installations au sein d’une même région ou dans deux régions distinctes.
- Stockage géo-redondant (GRS)
    - Réplique vos données vers une région secondaire à des centaines de kilomètres de la région primaire.
- Stockage géo-redondant avec accès en lecture (RA-GRS)
    - Réplique vos données dans une région secondaire, comme avec GRS, mais fournit également un accès en lecture seule aux données dans l’emplacement secondaire.

Le tableau suivant fournit une rapide vue d’ensemble des différences entre les types de réplications de stockage :

| Stratégie de réplication                                                        | LRS | ZRS | GRS | RA-GRS |
|:----------------------------------------------------------------------------|:----|:----|:----|:-------|
| Les données sont répliquées entre plusieurs installations.                              | Non  | Oui | Oui | Oui    |
| Les données peuvent être lues à partir de l’emplacement secondaire comme de l’emplacement principal. | Non  | Non  | Non  | Oui    |
| Nombre de copies de données conservées sur des nœuds distincts.                      | 3   | 3   | 6   | 6      |

Vous pouvez en savoir plus sur les [options de réplication de stockage Azure ici](../articles/storage/storage-redundancy.md).

### <a name="storage-costs"></a>Coûts de stockage
Les prix varient selon le type de stockage et la disponibilité que vous sélectionnez. 

- Le stockage Premium s’appuie sur des disques SSD et est facturé en fonction de la capacité du disque.
- Le stockage standard s’appuie sur des disques rotatifs ordinaires et est facturé en fonction de la capacité en cours d’utilisation et la disponibilité de stockage souhaitée.
    - Pour RA-GRS, les frais de transfert de données de géo-réplication supplémentaires correspondent au coût de la bande passante nécessaire à la réplication de ces données vers une autre région Azure.

Consultez [Tarification Azure Storage](https://azure.microsoft.com/pricing/details/storage/) pour connaître les informations de tarification des différents types de stockage et options de disponibilité.


## <a name="azure-images"></a>Images Microsoft Azure
Dans Azure, les machines virtuelles sont créées à partir d’une image. En général, les images utilisées sont créées à partir de l’environnement [Azure Marketplace](https://azure.microsoft.com/marketplace/), dans lequel les partenaires peuvent fournir un système d’exploitation complet préconfiguré ou des images de l’application.

Lorsque vous créez une machine virtuelle à partir d’une image dans Azure Marketplace, vous utilisez en fait des modèles. Les modèles Azure Resource Manager sont des fichiers JavaScript Objet Notation (JSON) déclaratifs qui peuvent être utilisés pour créer des environnements d’application complexes comprenant des machines virtuelles, un stockage, un réseau virtuel, etc. Vous pouvez accéder à des informations supplémentaires sur l’utilisation de [modèles Azure Resource Manager](../articles/resource-group-overview.md), notamment sur la [création de vos propres modèles](../articles/resource-group-authoring-templates.md).

Vous pouvez également créer vos propres images personnalisées et les charger à l’aide de la [CLI Azure](../articles/virtual-machines/virtual-machines-linux-upload-vhd.md) ou d’[Azure PowerShell](../articles/virtual-machines/virtual-machines-windows-upload-image.md) pour créer rapidement des machines virtuelles personnalisées selon les besoins spécifiques de votre build.

## <a name="availability-sets"></a>Groupes à haute disponibilité
Un groupe à haute disponibilité est un regroupement logique de machines virtuelles qui permet à Azure de comprendre comment votre application est conçue, afin de garantir la redondance et la disponibilité. Il est recommandé de créer au moins deux machines virtuelles dans un groupe à haute disponibilité, de manière à fournir une application hautement disponible et à répondre aux exigences du [niveau de 99,95 % inscrit dans les contrats de niveau de service Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Le groupe à haute disponibilité représente le compromis entre deux regroupements supplémentaires qui vous protègent contre les défaillances matérielles et autorisent l’application des mises à jour en toute sécurité dans des domaines d’erreur (FD) et des domaines de mise à jour (UD).

![Schéma conceptuel de la configuration du domaine de mise à jour et du domaine d’erreur](./media/virtual-machines-common-regions-and-availability/ud-fd-configuration.png)

Vous pouvez en savoir plus sur la gestion de la disponibilité des [machines virtuelles Linux](../articles/virtual-machines/virtual-machines-linux-manage-availability.md) ou des [machines virtuelles Windows](../articles/virtual-machines/virtual-machines-linux-manage-availability.md).

### <a name="fault-domains"></a>Domaines d'erreur
Un domaine d’erreur est un groupe logique de matériels sous-jacents qui partagent la même source d’alimentation et le même commutateur réseau, par exemple un rack dans un centre de données local. Lorsque vous créez des machines virtuelles au sein d’un groupe à haute disponibilité, la plateforme Azure distribue automatiquement vos machines virtuelles dans ces domaines d’erreur. Cette approche limite l’impact des défaillances du matériel physique, des pannes de réseau ou des coupures de courant susceptibles de survenir.

### <a name="update-domains"></a>Domaines de mise à jour
Un domaine de mise à jour est un groupe logique de matériels sous-jacents qui peuvent faire l’objet d’une opération de maintenance ou être redémarrés en même temps. Lorsque vous créez des machines virtuelles au sein d’un groupe à haute disponibilité, la plateforme Azure distribue automatiquement vos machines virtuelles dans ces domaines de mise à jour. Cette approche garantit qu’au moins une instance de votre application reste toujours en cours d’exécution, car la plateforme Azure fait l’objet d’une maintenance périodique. Le redémarrage des domaines de mise à jour peut ne pas suivre un ordre séquentiel au cours de la maintenance planifiée, mais un seul domaine de mise à jour peut être redémarré à la fois.


## <a name="next-steps"></a>Étapes suivantes
Vous pouvez maintenant commencer à utiliser ces fonctionnalités de gestion de la redondance et de la disponibilité pour créer votre environnement Azure. Pour en savoir plus sur les meilleures pratiques, consultez la rubrique relative aux [meilleures pratiques Azure en matière de disponibilité](../articles/best-practices-availability-checklist.md).

<!--HONumber=Oct16_HO2-->



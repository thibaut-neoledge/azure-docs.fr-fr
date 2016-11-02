<properties
    pageTitle="Glossaire Azure - dictionnaire Azure | Microsoft Azure"
    description="Utilisez le glossaire Azure pour comprendre la terminologie des technologies cloud sur la plateforme Azure Ce petit dictionnaire Azure fournit les définitions des termes les plus courants de la technologie cloud pour Azure."
    keywords="Dictionnaire Azure, terminologie cloud, glossaire Azure, définitions de terminologie, termes de cloud"
    services="na"
    documentationCenter="na"
    authors="MonicaRush"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="monicar"/>


# Glossaire Microsoft Azure : un dictionnaire de la terminologie des technologies cloud sur la plateforme Azure

Le glossaire Microsoft Azure est un petit dictionnaire de la terminologie des technologies cloud pour la plateforme Azure.

## Trouvez les définitions des services Azure et d’autres termes liés au cloud

* Pour les définitions des services Azure et leurs équivalents AWS, voir [Microsoft Azure and Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) (Microsoft Azure et Amazon Web Services).

* Pour les termes généraux liés aux technologies cloud, voir [Termes de cloud computing](https://azure.microsoft.com/overview/cloud-computing-dictionary/).

Le glossaire Azure, en plus des deux références ci-dessus, constitue une taxonomie exhaustive pour Azure et le secteur du cloud.

## Glossaire Azure


### <a name="account"></a>Compte  
Un compte scolaire, professionnel ou personnel utilisé pour accéder à et gérer un abonnement Azure. Voir aussi [Association des abonnements Azure avec Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md)


### <a name="availability-set"></a>groupe à haute disponibilité  
Une collection de machines virtuelles gérées ensemble pour permettre la redondance et la fiabilité des applications. L’utilisation d’un groupe à haute disponibilité assure qu’au moins une des machines virtuelles sera disponible pendant un événement de maintenance planifié ou non. Voir aussi [Gestion de la disponibilité des machines virtuelles Windows](./virtual-machines/virtual-machines-windows-manage-availability.md) ou [Gestion de la disponibilité des machines virtuelles Linux](./virtual-machines/virtual-machines-linux-manage-availability.md)


### <a name="classic-model"></a>Modèle de déploiement Azure Classic  
L’un des deux [modèles de déploiement](resource-manager-deployment-model.md) utilisés pour déployer des ressources dans Azure (Azure Resource Manager est le modèle plus récent). Certaines ressources Azure peuvent être déployées dans un modèle ou l’autre, tandis que d’autres ressources peuvent être déployées dans les deux modèles. Les conseils pour des ressources Azure individuelles indiquent les modèles dans lesquels une ressource peut être déployée.


### <a name="cli"></a>Interface de ligne de commande Microsoft Azure  
Une [interface de ligne de commande](xplat-cli-install.md) qui peut être utilisée pour gérer les services Azure à partir de PC Windows, OSX et Linux.


### <a name="powershell"></a>Azure PowerShell  
Une [interface de ligne de commande](powershell-install-configure.md) pour gérer les services Azure par le biais d’une ligne de commande à partir de PC Windows. Certains services ou fonctionnalités de service peuvent uniquement être gérés via PowerShell ou l’interface de ligne de commande. Les conseils pour chaque ressource Azure individuelle indiquent les modèles dans lesquels une ressource peut être déployée. Consultez également [Installation et configuration d’Azure PowerShell](powershell-install-configure.md).


### <a name="arm-model"></a>modèle de déploiement Azure Resource Manager  
l’un des deux [modèles de déploiement](resource-manager-deployment-model.md) utilisés pour déployer des ressources dans Microsoft Azure (l’autre est le modèle de déploiement classique). Certaines ressources Azure peuvent être déployées dans un modèle ou l’autre, tandis que d’autres ressources peuvent être déployées dans les deux modèles. Les conseils pour des ressources Azure individuelles indiquent les modèles dans lesquels une ressource peut être déployée.


### <a name="fault-domain"></a>Domaine d’erreur  
Une collection de machines virtuelles dans un groupe à haute disponibilité pouvant échouer en même temps. Exemple : un groupe de machines dans un rack partageant une source d’alimentation et un commutateur réseau communs. Dans Azure, les machines virtuelles d’un groupe à haute disponibilité sont automatiquement réparties entre plusieurs domaines d’erreur. Voir aussi [Gestion de la disponibilité des machines virtuelles Windows](./virtual-machines/virtual-machines-windows-manage-availability.md) ou [Gestion de la disponibilité des machines virtuelles Linux](./virtual-machines/virtual-machines-linux-manage-availability.md)


### <a name="geo"></a>Zone géographique  
Une limite définie pour la résidence des données qui couvre généralement deux ou plusieurs régions. Les limites peuvent se trouver à l’intérieur ou à l’extérieur des frontières nationales et sont soumises aux réglementations fiscales. Chaque zone géographique couvre au moins une région. L’Asie-Pacifique et le Japon en sont des exemples. Également appelé *géographie*. Voir aussi [Régions Azure](best-practices-availability-paired-regions.md)


### <a name="geo-replication"></a>géoréplication  
Le processus de réplication automatique de contenu tel que les objets blob, les tables et les files d’attente au sein d’une paire régionale. Voir aussi [Géo-réplication active pour la base de données SQL Azure](./sql-database/sql-database-geo-replication-overview.md)


### <a name="image"></a>Image  
Un fichier qui contient la configuration du système d’exploitation et des applications, et qui peut servir à créer un nombre illimité de machines virtuelles. Deux types d’images peuvent être utilisés dans Azure : l’image de machine virtuelle et l’image du système d’exploitation. Une image de machine virtuelle inclut un système d'exploitation et tous les disques attachés à une machine virtuelle lors de la création de l'image. Une image du système d’exploitation contient uniquement un système d’exploitation généralisé sans aucune configuration du disque de données. Voir aussi [Parcourir et sélectionner des images de machines virtuelles Windows dans Azure avec l’interface CLI ou PowerShell](./virtual-machines/virtual-machines-windows-cli-ps-findimage.md)


### <a name="limits"></a>Limites  
Le nombre de ressources qui peuvent être créées ou le point de référence des performances pouvant être atteint. Les limites sont généralement associées aux abonnements ainsi qu’aux services et aux offres. Voir aussi [Abonnement Azure et limites, quotas et contraintes du service](azure-subscription-service-limits.md)


### <a name="load-balancer"></a>équilibreur de charge  
Une ressource qui répartit le trafic entrant sur les ordinateurs d’un réseau. Dans Azure, un équilibreur de charge répartit le trafic sur les machines virtuelles définies dans un jeu d’équilibrage de charge. Un [équilibreur de charge](./load-balancer/load-balancer-overview.md) peut être interne ou accessible sur Internet.


### <a name="offer"></a>Offre  
La tarification, les crédits et les conditions associées applicables à un abonnement Azure. Consultez la page [Détails de l’offre Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/)


### <a name="portal"></a>Portail  
Le portail web sécurisé utilisé pour déployer et gérer les services Azure. Il existe deux portails : le [portail Azure](http://portal.azure.com/) et le [portail Classic](http://manage.windowsazure.com/). Certains services sont disponibles dans les deux portails, tandis que d’autres sont uniquement disponibles dans l’un ou l’autre. Le [graphique de la disponibilité du portail Azure](https://azure.microsoft.com/features/azure-portal/availability/) répertorie les services disponibles dans chaque portail.


### <a name="region"></a>Région  
Une zone à l’intérieur d’une zone géographique qui ne dépasse pas les frontières nationales et contient un ou plusieurs centres de données. La tarification, les services régionaux et les types d’offre sont exposés au niveau régional. Une région est généralement associée à une autre région, qui peut se trouver à plusieurs centaines de kilomètres, afin de former une paire régionale. Les paires régionales peuvent servir dans des scénarios de récupération d’urgence et de haute disponibilité. Souvent, elles sont également appelées *localisation*. Voir aussi [Régions Azure](best-practices-availability-paired-regions.md)


### <a name="resource"></a>Ressource  
Un élément qui fait partie de votre solution Azure. Chaque service Azure vous permet de déployer différents types de ressources, comme des bases de données ou des machines virtuelles. Voir aussi [Présentation d’Azure Resource Manager](resource-group-overview.md)


### <a name="resource-group"></a>groupe de ressources  
Un conteneur dans Azure Resource Manager réunissant les ressources associées d’une application. Le groupe de ressources peut inclure toutes les ressources d’une application, ou uniquement celles qui sont regroupées de façon logique. Pour déterminer comment allouer des ressources aux groupes de ressources, choisissez l’approche la plus pertinente pour votre organisation. Voir aussi [Présentation d’Azure Resource Manager](resource-group-overview.md)


### <a name="arm-template"></a>modèle Azure Resource Manager  
Un fichier JSON qui définit de façon déclarative une ou plusieurs ressources Azure ainsi que les dépendances entre les ressources déployées. Le modèle peut être utilisé pour déployer les ressources de manière cohérente et répétée. Voir aussi [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md)


### <a name="resource-provider"></a>fournisseur de ressources  
Un service qui fournit les ressources que vous pouvez déployer et gérer via Azure Resource Manager. Chaque fournisseur de ressources propose des opérations pour travailler avec les ressources déployées. Les fournisseurs de ressources sont accessibles via le portail Azure, Azure PowerShell et plusieurs Kits de développement logiciel (SDK) de programmation. Voir aussi [Présentation d’Azure Resource Manager](resource-group-overview.md)


### <a name="role"></a>Rôle  
Un moyen de contrôler l’accès qui peut être affecté aux utilisateurs ainsi qu’aux groupes et services. Les rôles rendent possibles des actions telles que la création, la gestion et la lecture sur les ressources Azure. Voir aussi [RBAC : rôles intégrés](./active-directory/role-based-access-built-in-roles.md)


### <a name="sla"></a>Contrat de Niveau de Service (SLA)  
Les contrats SLA décrivent les engagements de Microsoft en matière de temps d’activité et de connectivité. Chaque service Azure dispose d’un contrat SLA spécifique. Voir aussi [Contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/)


### <a name="storage-account"></a>compte de stockage  
Un compte de stockage vous donne accès aux services Azure Objet blob, File d’attente, Table et Fichier dans Azure Storage. Votre compte de stockage fournit les espaces de noms uniques pour vos objets de données Azure Storage. Voir aussi [À propos des comptes de stockage Azure Storage](./storage/storage-create-storage-account.md)


### <a name="subscription"></a>Abonnement  
Accord d’un client avec Microsoft qui lui permet de bénéficier de services Azure. La tarification des abonnements et les conditions associées sont régies par l’offre choisie pour l’abonnement. Voir le [Contrat d’abonnement à Microsoft Online](https://azure.microsoft.com/support/legal/subscription-agreement/). Voir aussi [Association des abonnements Azure avec Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md)


### <a name="tag"></a>Balise  
Un terme d’indexation vous permettant de catégoriser les ressources en fonction de vos exigences de gestion ou de facturation. Vous pouvez utiliser des balises lorsque vous disposez d’un ensemble complexe de groupes de ressources et de ressources et que vous souhaitez visualiser ces actifs de la façon qui vous semble la plus logique. Par exemple, vous pouvez baliser des ressources qui jouent un rôle similaire dans votre organisation ou qui appartiennent au même département. Voir aussi [Organisation des ressources Azure à l’aide de balises](resource-group-using-tags.md)


### <a name="update-domain"></a>domaine de mise à jour  
Une collection de machines virtuelles dans un groupe à haute disponibilité qui sont mises à jour en même temps. Les machines virtuelles dans un même domaine de mise à jour sont redémarrées simultanément lors de la maintenance planifiée. Azure ne redémarre jamais plus d’un domaine de mise à jour à la fois. Également appelé domaine de mise à niveau. Voir aussi [Gestion de la disponibilité des machines virtuelles Windows](./virtual-machines/virtual-machines-windows-manage-availability.md) ou [Gestion de la disponibilité des machines virtuelles Linux](./virtual-machines/virtual-machines-linux-manage-availability.md)


### <a name="vm"></a>Machine virtuelle  
L’implémentation logicielle d’un ordinateur physique qui exécute un système d’exploitation. Il est possible d’exécuter plusieurs machines virtuelles simultanément sur le même matériel. Dans Azure, les machines virtuelles sont disponibles en plusieurs tailles. Voir aussi [Documentation sur les machines virtuelles](https://azure.microsoft.com/documentation/services/virtual-machines/)


### <a name="vm-extension"></a>extension de machine virtuelle  
Une ressource qui implémente des comportements ou des fonctionnalités facilitant le fonctionnement d’autres programmes ou qui vous offre la possibilité d’interagir avec un ordinateur en cours d’exécution. Par exemple, vous pouvez utiliser l’extension VM Access pour réinitialiser ou modifier les valeurs d’accès à distance sur une machine virtuelle Azure. Voir aussi [À propos des extensions et des fonctionnalités des machines virtuelles (Windows)](./virtual-machines/virtual-machines-windows-extensions-features.md) ou [À propos des extensions et des fonctionnalités des machines virtuelles (Linux)](./virtual-machines/virtual-machines-linux-extensions-features.md)


### <a name="vnet"></a>Réseau virtuel  
Un réseau qui assure la connectivité entre vos ressources Azure, isolé de tous les autres locataires Azure. Il peut être connecté à d’autres réseaux virtuels Azure par le biais d’une [passerelle VPN Azure](./vpn-gateway/vpn-gateway-about-vpngateways.md) et à votre réseau local en utilisant [plusieurs options](./vpn-gateway/vpn-gateway-cross-premises-options.md). Vous pouvez contrôler complètement les blocs d’adresses IP, les paramètres DNS, les stratégies de sécurité et les tables de routage de ce réseau. Voir aussi [Présentation du réseau virtuel](./virtual-network/virtual-networks-overview.md)

###**Voir aussi**  
- [Prise en main d’Azure](https://azure.microsoft.com/get-started/)
- [Centre de ressources du cloud](https://azure.microsoft.com/resources/)
- [Azure pour vos applications métier](https://azure.microsoft.com/overview/business-apps-on-azure/)
- [Azure dans votre centre de données](https://azure.microsoft.com/overview/business-apps-on-azure/)

<!---HONumber=AcomDC_0803_2016-->
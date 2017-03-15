

## <a name="memory-preserving-updates"></a>Mises à jour de préservation de la mémoire
Pour une classe de mises à jour dans Microsoft Azure, les clients ne constatent aucun impact sur leurs machines virtuelles en cours d’exécution. La plupart de ces mises à jour sont des composants ou des services qui peuvent être mis à jour sans interférer avec l'instance en cours d'exécution. Certaines de ces mises à jour sont des mises à jour d’infrastructure de la plateforme sur le système d’exploitation hôte qui peuvent être appliquées sans requérir un redémarrage complet de la machine virtuelle.

Ces mises à jour sont réalisées avec la technologie qui permet la migration dynamique sur place (ou mise à jour de « conservation de la mémoire »). Lors de la mise à jour, la machine virtuelle est mise en pause, ce qui permet de préserver la mémoire RAM, pendant que le système d’exploitation hôte sous-jacent reçoit les correctifs et mises à jour nécessaires. La machine virtuelle redémarre après 30 secondes d’arrêt. Une fois redémarrée, l’horloge de la machine virtuelle est automatiquement synchronisée.

Les mises à jour ne peuvent pas toutes êtes déployées à l’aide de ce mécanisme, mais étant donné la courte interruption, ce type de déploiement des mises à jour réduit considérablement l’impact sur les machines virtuelles.

Les mises à jour multi-instance (pour les machines virtuelles d’un groupe à haute disponibilité) se voient appliquer un seul domaine de mise à jour à la fois.  

## <a name="virtual-machine-configurations"></a>Configurations de machine virtuelle
Deux types de configuration de machine virtuelle sont disponibles : multi-instance et d’instance unique. Dans une configuration multi-instance, les machines virtuelles similaires sont placées dans un groupe à haute disponibilité.

La configuration multi-instance fournit la redondance entre les machines physiques, la puissance et le réseau. Elle est recommandée pour garantir la disponibilité de votre application. Toutes les machines virtuelles du groupe à haute disponibilité doivent servir le même objectif à votre application.

Pour plus d’informations sur la configuration des machines virtuelles pour une haute disponibilité, voir [Gestion de la disponibilité des machines virtuelles Windows](../articles/virtual-machines/virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [Gestion de la disponibilité des machines virtuelles Linux](../articles/virtual-machines/virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

En revanche, une configuration à instance unique est utilisée pour les machines virtuelles autonomes qui ne sont pas placées dans un groupe à haute disponibilité. Ces machines virtuelles ne répondent pas aux conditions du contrat de niveau de service (SLA) qui requiert le déploiement de deux machines virtuelles au minimum dans le même groupe à haute disponibilité.

Pour plus d’informations sur les contrats de niveau de service, voir les sections « Services cloud » et « Machines virtuelles » des [Contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/).

## <a name="multi-instance-configuration-updates"></a>Mises à jour de configuration multi-instance
Pendant les maintenances planifiées, la plateforme Azure met tout d’abord à jour l’ensemble des machines virtuelles qui sont hébergées dans une configuration multi-instance. Un redémarrage de ces machines virtuelles intervient alors, avec environ 15 minutes d’interruption de service.

Dans une mise à jour de configuration multi-instance, nous partons du principe que chaque machine virtuelle remplit une fonction similaire à celle des autres dans le groupe à haute disponibilité. Dans cette configuration, les machines virtuelles sont mises à jour de façon à préserver la disponibilité tout au long du processus.

Chaque machine virtuelle d’un groupe à haute disponibilité se voit attribuer un domaine de mise à jour et un domaine d’erreur par la plateforme Azure sous-jacente. Chaque domaine de mise à jour est un groupe de machines virtuelles qui sera redémarré dans la même fenêtre de temps. Chaque domaine d’erreur est un groupe de machines virtuelles qui partagent une source d’alimentation et un commutateur réseau communs.


Pour plus d’informations sur les domaines de mise à jour et les domaines d’erreur, consultez [Configurer plusieurs machines virtuelles dans un groupe à haute disponibilité pour la redondance](../articles/virtual-machines/virtual-machines-windows-manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).

Pour maintenir la disponibilité dans le cadre d’une mise à jour, Azure effectue la maintenance par domaine de mise à jour, en mettant à jour un domaine à la fois. La maintenance d’un domaine de mise à jour consiste à fermer chaque machine virtuelle du domaine, à appliquer la mise à jour aux machines hôtes, puis à redémarrer les machines virtuelles. Lorsque la maintenance du domaine est terminée, Azure répète le processus avec le domaine de mise à jour suivant, puis continue avec chaque domaine jusqu’à ce que tous soient mis à jour.

Les domaines de mise à jour ne sont pas nécessairement redémarrés de manière séquentielle au cours de la maintenance planifiée, mais un seul domaine de mise à jour est redémarré à la fois. Aujourd’hui, Azure fournit une notification 1 semaine à l’avance pour la maintenance planifiée des machines virtuelles dans la configuration multi-instance.

Après restauration d’une machine virtuelle, voici un exemple de ce que l’Observateur d’événements Windows peut afficher :

<!--Image reference-->
![][image2]


Utilisez la visionneuse pour déterminer quelles machines virtuelles sont configurées dans une configuration multi-instance. Aidez-vous pour cela du portail Azure, d’Azure PowerShell ou de l’interface de ligne de commande Azure. Par exemple, à l’aide du portail Azure, vous pouvez ajouter le _groupe à haute disponibilité_ à la boîte de dialogue Parcourir **machines virtuelles (classiques)**. Les machines virtuelles qui signalent le même groupe à haute disponibilité font partie d’une configuration multi-instance. Dans l’exemple suivant, la configuration multi-instance se compose de machines virtuelles SQLContoso01 et SQLContoso02.

<!--Image reference-->
  ![Écran Machines virtuelles (classiques) du portail Azure][image4]

## <a name="single-instance-configuration-updates"></a>Mises à jour de configuration d’instance unique
Une fois les mises à jour de configuration multi-instance effectuées, Azure procède aux mises à jour de configuration d’instance unique. Ces mises à jour entraînent également un redémarrage des machines virtuelles qui ne s’exécutent pas dans des groupes à haute disponibilité.

> [!NOTE]
> Si vous n’avez qu’une seule instance de machine virtuelle en cours d’exécution dans un groupe à haute disponibilité, la plateforme Azure la traite comme une mise à jour de configuration multi-instance.
>

La maintenance dans une configuration à instance unique consiste à arrêter chaque machine virtuelle en cours d’exécution sur un ordinateur hôte, à mettre à jour l’ordinateur hôte et à redémarrer les machines virtuelles. Cette maintenance nécessite environ 15 minutes d’interruption de service. Cet événement de maintenance planifiée intervient sur toutes les machines virtuelles d’une région, dans une seule fenêtre de maintenance.


Ces événements de maintenance planifiée ont un impact sur la disponibilité de votre application pour les configurations à instance unique. Azure informe une semaine à l’avance d’une maintenance planifiée des machines virtuelles dans les configurations à instance unique.

## <a name="email-notification"></a>E-mail de notification
Pour les configurations de machine virtuelle multi-instance et à instance unique uniquement, Azure envoie une alerte par e-mail pour vous informer de la prochaine maintenance planifiée (une semaine à l’avance). Cet e-mail est envoyé aux comptes de messagerie de l’administrateur et du coadministrateur de l’abonnement. Voici un exemple de ce type de courrier électronique :

<!--Image reference-->
![][image1]

## <a name="region-pairs"></a>Paires de régions

Lors de l’exécution de maintenance, Azure met à jour uniquement les instances de machine virtuelle d’une seule région de la paire. Par exemple, lors de la mise à jour des ordinateurs virtuels de l’Amérique du Nord central, Azure ne met à jour dans le même temps aucune machine virtuelle de l’Amérique du Nord méridionale. La mise à jour est planifiée à un autre moment, en activant le basculement ou l’équilibrage de charge entre les régions. En revanche, les autres régions, l’Europe du Nord par exemple, peuvent faire l’objet d’une maintenance en même temps que la région Est des États-Unis.

Consultez le tableau suivant pour les paires de régions actuelles :

| Région 1 | Région 2 |
|:--- | ---:|
| Est des États-Unis |Ouest des États-Unis |
| Est des États-Unis 2 |Centre des États-Unis |
| États-Unis - partie centrale septentrionale |Centre-Sud des États-Unis |
| Ouest-Centre des États-Unis |Ouest des États-Unis 2 |
| Est du Canada |Centre du Canada |
| Sud du Brésil |Centre-Sud des États-Unis |
| US Gov Iowa |US Gov Virginia |
| Est des États-Unis – US DoD |Centre des États-Unis – US DoD |
| Europe du Nord |Europe de l'Ouest |
| Ouest du Royaume-Uni |Sud du Royaume-Uni |
| Centre de l’Allemagne |Nord-Est de l’Allemagne |
| Asie du Sud-Est |Est de l'Asie |
| Sud-est de l’Australie |Est de l’Australie |
| Inde-Centre |Sud de l'Inde |
| Inde-Ouest |Sud de l’Inde |
| Est du Japon |Ouest du Japon |
| Centre de la Corée |Corée du Sud |
| Chine orientale |Chine du Nord |


<!--Anchors-->
[image1]: ./media/virtual-machines-common-planned-maintenance/vmplanned1.png
[image2]: ./media/virtual-machines-common-planned-maintenance/EventViewerPostReboot.png
[image3]: ./media/virtual-machines-planned-maintenance/RegionPairs.PNG
[image4]: ./media/virtual-machines-common-planned-maintenance/availabilitysetexample.png


<!--Link references-->
[Virtual Machines Manage Availability]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md

[Understand planned versus unplanned maintenance]: ../articles/virtual-machines/virtual-machines-windows-manage-availability.md#Understand-planned-versus-unplanned-maintenance/

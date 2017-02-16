## <a name="understand-planned-vs-unplanned-maintenance"></a>Différence entre maintenance planifiée et non planifiée
Il existe deux types d’événements de plateforme Microsoft Azure susceptibles d’avoir un effet sur la disponibilité de vos machines virtuelles : la maintenance planifiée et la maintenance non planifiée.

* **Les événements de maintenance planifiés** sont des mises à jour périodiques effectuées par Microsoft sur la plateforme sous-jacente Azure pour améliorer la fiabilité, les performances et la sécurité de l'infrastructure hébergeant vos machines virtuelles. La plupart de ces mises à jour se déroulent sans aucune incidence sur les machines virtuelles ou les services cloud. Toutefois, il arrive que ces mises à jour requièrent un redémarrage de votre machine virtuelle afin que les mises à jour de l'infrastructure de la plateforme soient appliquées.
* **Les événements de maintenance non planifiés** ont lieu lorsque l'infrastructure physique ou matérielle sous-jacente de votre machine virtuelle a connu une défaillance. Cela comprend les défaillances du réseau local, du disque local ou au niveau du rack. Lorsqu'une défaillance de ce type est détectée, la plateforme Azure migre automatiquement votre machine virtuelle de la machine physique défectueuse vers une machine physique saine. De tels événements sont rares, mais peuvent entraîner un redémarrage de votre machine virtuelle.

Pour réduire l'effet des interruptions de service dues à un ou plusieurs de ces événements, nous vous recommandons les meilleures pratiques suivantes pour la haute disponibilité de vos machines virtuelles :

* [Configuration de plusieurs machines virtuelles dans un groupe à haute disponibilité pour assurer la redondance]
* [Configuration de chaque couche application dans des groupes à haute disponibilité séparés]
* [Combinaison de l’équilibrage de charge et des groupes à haute disponibilité]
* [Utilisation de plusieurs comptes de stockage pour chaque groupe à haute disponibilité]

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Configuration de plusieurs machines virtuelles dans un groupe à haute disponibilité pour assurer la redondance
Pour assurer la redondance de votre application, nous vous recommandons de regrouper au moins deux machines virtuelles dans un groupe à haute disponibilité. Cette configuration assure qu'au moins une des machines virtuelles sera disponible pendant un événement de maintenance planifié ou non et répondra au niveau de 99,95 % inscrit dans les contrats de niveau de service Azure. Pour plus d’informations, consultez le [SLA pour Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> Évitez de laisser une instance unique de machine virtuelle dans un groupe à haute disponibilité. Les machines virtuelles ayant adopté cette configuration ne sont pas sous la garantie des contrats de niveau de service et connaissent des interruptions de service au cours des événements de maintenance planifiés.
> 
> 

Chaque machine virtuelle de votre groupe à haute disponibilité se voit attribuer un **domaine de mise à jour** et un **domaine d’erreur** par la plateforme Azure sous-jacente. Pour un groupe à haute disponibilité donné, cinq domaines de mise à jour non configurables par l’utilisateur sont affectés par défaut (les déploiements Resource Manager peuvent ensuite être étendus à 20 domaines de mise à jour) pour indiquer les groupes de machines virtuelles et les équipements physiques sous-jacents qui peuvent être redémarrés simultanément. Dans le cas où un seul groupe à haute disponibilité comprend plus de cinq machines virtuelles, la sixième machine est placée dans le même domaine de mise à jour que la première, la septième dans le même que la deuxième, etc. Le redémarrage des domaines de mise à jour peut ne pas suivre un ordre séquentiel au cours de la maintenance planifiée, mais un seul domaine de mise à jour peut être redémarré à la fois.

Les domaines d’erreur définissent le groupe de machines virtuelles partageant une source d’alimentation et un commutateur réseau communs. Par défaut, les machines virtuelles configurées dans votre groupe à haute disponibilité sont réparties entre trois domaines d’erreur au maximum pour les déploiements Resource Manager (deux domaines d’erreur pour les déploiements Classic). Le fait de placer vos machines virtuelles dans un groupe à haute disponibilité ne protège pas vos applications des défaillances du système d’exploitation ou propres aux applications, mais limite l’effet des défaillances des équipements physiques, des pannes du serveur et des coupures d’électricité.

<!--Image reference-->
   ![Schéma conceptuel de la configuration du domaine de mise à jour et du domaine d’erreur](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="configure-each-application-tier-into-separate-availability-sets"></a>Configuration de chaque couche application dans des groupes à haute disponibilité séparés
Si vos machines virtuelles sont presque identiques et ont la même fonction au sein de votre application, nous vous recommandons de configurer un groupe à haute disponibilité pour chaque couche de votre application.  Si vous placez deux couches différentes dans le même groupe à haute disponibilité, toutes les machines virtuelles de la même couche application peuvent être redémarrées en même temps. En configurant deux machines virtuelles ou plus dans un groupe à haute disponibilité par couche, vous vous assurez qu’au moins une machine de chaque couche reste disponible.

Par exemple, vous pouvez rassembler dans un seul groupe à haute disponibilité toutes les machines virtuelles du composant frontal de votre application exécutant IIS, Apache ou Nginx. Assurez-vous que seules les machines virtuelles frontales sont placées dans le même groupe à haute disponibilité. Assurez-vous également que seules les machines virtuelles de la couche de données sont placées dans leur propre groupe à haute disponibilité, au même titre que vos machines virtuelles répliquées SQL Server ou vos machines MySQL.

<!--Image reference-->
   ![Couches de l'application](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-sets"></a>Combinaison de l’équilibrage de charge et des groupes à haute disponibilité
Combinez [l’équilibrage de charge Azure](../articles/load-balancer/load-balancer-overview.md) avec un groupe à haute disponibilité pour une meilleure résilience de votre application. L'équilibrage de charge Azure répartit le trafic entre plusieurs machines virtuelles. L'équilibrage de charge Azure est compris pour nos machine virtuelles de niveau Standard. Certains niveaux de machines virtuelles n’intègrent pas Azure Load Balancer. Pour plus d’informations sur l’équilibrage de charge des machines virtuelles, consultez [Équilibrage de charge des machines virtuelles](../articles/virtual-machines/virtual-machines-linux-load-balance.md).

Si l’équilibrage de charge n’est pas configuré pour équilibrer le trafic entre plusieurs machines virtuelles, tout événement de maintenance planifié affecte l’unique machine virtuelle en charge du trafic, entraînant ainsi une interruption de votre couche Application. Placer plusieurs machines virtuelles de la même couche dans le même équilibrage de charge et groupe à haute disponibilité permet de toujours avoir au moins une instance disponible pour le trafic.

## <a name="use-multiple-storage-accounts-for-each-availability-set"></a>Utilisation de plusieurs comptes de stockage pour chaque groupe à haute disponibilité
Vous devez suivre les meilleures pratiques concernant les comptes de stockage utilisés par les disques durs virtuels (VHD) sur la machine virtuelle. Chaque disque (VHD) est un objet blob de pages dans un compte de stockage Azure. Il est important de s’assurer de la redondance et de l’isolation entre les comptes de stockage afin de fournir une haute disponibilité aux machines virtuelles au sein du groupe à haute disponibilité.

1. **Conservez tous les disques (système d’exploitation et données) associés à une machine virtuelle dans le même compte de stockage.**
2. **Les [limites](../articles/storage/storage-scalability-targets.md) du compte de stockage doivent être considérées** lorsque vous ajoutez plusieurs disques durs virtuels à un compte de stockage.
3. **Utilisez plusieurs comptes de stockage pour chaque groupe à haute disponibilité.** Utilisez des comptes de stockage distincts pour chaque machine virtuelle d’un groupe à haute disponibilité. Plusieurs machines virtuelles d’un même groupe à haute disponibilité NE doivent PAS partager le même compte de stockage. Il est acceptable pour les machines virtuelles de différents groupes à haute disponibilité de partager des comptes de stockage tant que les meilleures pratiques ci-dessus sont suivies.

<!-- Link references -->
[Configuration de plusieurs machines virtuelles dans un groupe à haute disponibilité pour assurer la redondance]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Configuration de chaque couche application dans des groupes à haute disponibilité séparés]: #configure-each-application-tier-into-separate-availability-sets
[Combinaison de l’équilibrage de charge et des groupes à haute disponibilité]: #combine-a-load-balancer-with-availability-sets
[Éviter les instances uniques de machines virtuelles dans les groupes à haute disponibilité]: #avoid-single-instance-virtual-machines-in-availability-sets
[Utilisation de plusieurs comptes de stockage pour chaque groupe à haute disponibilité]: #use-multiple-storage-accounts-for-each-availability-set



<!--HONumber=Nov16_HO4-->



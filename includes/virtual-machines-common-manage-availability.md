## Différence entre maintenance planifiée et non planifiée
Il existe deux types d’événements de plateforme Microsoft Azure susceptibles d’avoir un effet sur la disponibilité de vos machines virtuelles : la maintenance planifiée et la maintenance non planifiée.

- **Les événements de maintenance planifiés** sont des mises à jour périodiques effectuées par Microsoft sur la plateforme sous-jacente Azure pour améliorer la fiabilité, les performances et la sécurité de l'infrastructure hébergeant vos machines virtuelles. La majorité de ces mises à jour ont lieu sans affecter les machines virtuelles et les services cloud. Toutefois, il arrive que ces mises à jour requièrent un redémarrage de votre machine virtuelle afin que les mises à jour de l'infrastructure de la plateforme soient appliquées.

- **Les événements de maintenance non planifiés** ont lieu lorsque l'infrastructure physique ou matérielle sous-jacente de votre machine virtuelle a connu une défaillance. Cela comprend les défaillances du réseau local, du disque local ou au niveau du rack. Lorsqu'une défaillance de ce type est détectée, la plateforme Azure migre automatiquement votre machine virtuelle de la machine physique défectueuse vers une machine physique saine. De tels événements sont rares, mais peuvent entraîner un redémarrage de votre machine virtuelle.

## Meilleures pratiques de conception de votre application pour la haute disponibilité
Pour réduire l'effet des interruptions de service dues à un ou plusieurs de ces événements, nous vous recommandons les meilleures pratiques suivantes pour la haute disponibilité de vos machines virtuelles :

* [Configuration de plusieurs machines virtuelles dans un groupe à haute disponibilité pour assurer la redondance]
* [Configuration de chaque couche application dans des groupes à haute disponibilité séparés]
* [Combinaison de l'équilibrage de charge et des groupes à haute disponibilité]
* [Éviter les instances uniques de machines virtuelles dans les groupes à haute disponibilité]

### Configuration de plusieurs machines virtuelles dans un groupe à haute disponibilité pour assurer la redondance
Pour assurer la redondance de votre application, nous vous recommandons de regrouper au moins deux machines virtuelles dans un groupe à haute disponibilité. Cette configuration assure qu'au moins une des machines virtuelles sera disponible pendant un événement de maintenance planifié ou non et répondra au niveau de 99,95 % inscrit dans les contrats de niveau de service Azure. Pour plus d’informations sur les contrats de niveau de service, consultez la section « Services cloud, machines virtuelles et réseau virtuel » dans [Contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/).

Chaque machine virtuelle de votre groupe à haute disponibilité se voit attribuer un domaine de mise à jour et un domaine d'erreur par la plateforme Azure sous-jacente. Pour un groupe à haute disponibilité donné, cinq domaines de mises à jour non configurables par l'utilisateur sont attribués pour indiquer les groupes de machines virtuelles et les équipements physiques sous-jacents pouvant être redémarrés en même temps. Dans le cas où un seul groupe à haute disponibilité comprend plus de cinq machines virtuelles, la sixième machine sera placée dans le même domaine de mise à jour que la première, la septième dans le même que la deuxième, etc. L'ordre de redémarrage des domaines de mise à jour ne peut être traité de manière séquentielle au cours de la maintenance planifiée, mais les domaines de mise à jour seront redémarrés un par un.

Les domaines de défaillance définissent le groupe de machines virtuelles partageant une source d'alimentation et un commutateur réseau communs. Par défaut, les machines virtuelles configurées dans votre groupe à haute disponibilité sont réparties entre deux domaines de défaillance. Le fait de placer vos machines virtuelles dans un groupe à haute disponibilité ne protège pas vos applications des défaillances du système d’exploitation ou propres aux applications, mais limite l’effet des défaillances des équipements physiques, des pannes du serveur et des coupures d’électricité.

<!--Image reference-->
   ![Configuration UD FD](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

>[AZURE.NOTE] Pour obtenir des instructions, consultez [Comment configurer un groupe à haute disponibilité pour des machines virtuelles][].

### Configuration de chaque couche application dans des groupes à haute disponibilité séparés
Si les machines virtuelles de votre groupe à haute disponibilité sont presque identiques et ont la même fonction au sein de votre application, nous vous recommandons de configurer un groupe à haute disponibilité pour chaque couche de votre application. Si vous placez deux couches différentes dans le même groupe à haute disponibilité, toutes les machines virtuelles de la même couche application peuvent être redémarrées en même temps. En configurant deux machines virtuelles ou plus dans un groupe à haute disponibilité par couche, vous vous assurez qu'au moins une machine de chaque couche restera disponible.

Par exemple, vous pouvez rassembler toutes les machines virtuelles utilisant IIS, Apache, Nginx, etc. pour le composant frontal de votre application dans un seul groupe à haute disponibilité. Assurez-vous que seules les machines virtuelles frontales sont placées dans le même groupe à haute disponibilité. Assurez-vous également que seules les machines virtuelles de la couche de données sont placées dans leur propre groupe à haute disponibilité, au même titre que vos machines virtuelles répliquées SQL Server ou vos machines MySQL.

<!--Image reference-->
   ![Couches de l'application](./media/virtual-machines-common-manage-availability/application-tiers.png)


### Combinaison de l'équilibrage de charge et des groupes à haute disponibilité
Combinez l'équilibrage de charge Azure avec un groupe à haute disponibilité pour une meilleure résilience de votre application. L'équilibrage de charge Azure répartit le trafic entre plusieurs machines virtuelles. L'équilibrage de charge Azure est compris pour nos machine virtuelles de niveau Standard. Notez que tous les niveaux de machines virtuelles n’intègrent pas l’équilibreur de charge Azure. Pour plus d’informations sur l’équilibrage de charge des machines virtuelles, consultez la section [Équilibrage de charge des machines virtuelles](../articles/virtual-machines/virtual-machines-linux-load-balance.md).

Si l'équilibrage de charge n'est pas configuré pour équilibrer le trafic entre plusieurs machines virtuelles, tout événement de maintenance planifié aura un effet sur l'unique machine virtuelle en charge du trafic, ce qui entraînera une panne de votre couche application. Placer plusieurs machines virtuelles de la même couche dans le même équilibrage de charge et groupe à haute disponibilité permet de toujours avoir au moins une instance disponible pour le trafic.

### Éviter les instances uniques de machines virtuelles dans les groupes à haute disponibilité
Évitez de laisser une instance unique de machine virtuelle dans un groupe à haute disponibilité. Les machines virtuelles ayant adopté cette configuration ne sont pas sous la garantie des contrats de niveau de service et connaissent des interruptions de service au cours des événements de maintenance planifiés. Veuillez noter que, si vous déployez une instance unique de machine virtuelle dans un groupe à haute disponibilité, vous recevrez également, à l’avance par e-mail, une notification de maintenance planifiée des machines virtuelles à instances multiples.

<!-- Link references -->
[Configuration de plusieurs machines virtuelles dans un groupe à haute disponibilité pour assurer la redondance]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Configuration de chaque couche application dans des groupes à haute disponibilité séparés]: #configure-each-application-tier-into-separate-availability-sets
[Combinaison de l'équilibrage de charge et des groupes à haute disponibilité]: #combine-the-load-balancer-with-availability-sets
[Éviter les instances uniques de machines virtuelles dans les groupes à haute disponibilité]: #avoid-single-instance-virtual-machines-in-availability-sets
[Comment configurer un groupe à haute disponibilité pour des machines virtuelles]: ../articles/virtual-machines/virtual-machines-windows-classic-configure-availability.md

<!---HONumber=AcomDC_0330_2016-->
<properties 
	pageTitle="Gestion de la disponibilité des machines virtuelles - Azure" 
	description="Apprenez à utiliser plusieurs machines virtuelles afin de garantir la haute disponibilité de votre application Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="kenazk" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/26/2014" 
	ms.author="kenazk"/>

#Gestion de la disponibilité des machines virtuelles

## Présentation de la maintenance planifiée et non planifiée
Deux types d'événements de plateforme Azure peuvent avoir un effet sur la disponibilité de vos machines virtuelles : la maintenance planifiée et la maintenance non planifiée.

- **Les événements de maintenance planifiés** sont des mises à jour périodiques effectuées par Microsoft sur la plateforme sous-jacente Azure pour améliorer la fiabilité, les performances et la sécurité de l'infrastructure hébergeant vos machines virtuelles. La majorité de ces mises à jour ont lieu sans affecter les machines virtuelles et les services cloud. Toutefois, il arrive que ces mises à jour requièrent un redémarrage de votre machine virtuelle afin que les mises à jour de l'infrastructure de la plateforme soient appliquées. 

- **Les événements de maintenance non planifiés** ont lieu lorsque l'infrastructure physique ou matérielle sous-jacente de votre machine virtuelle a connu une défaillance. Cela comprend les défaillances du réseau local, du disque local ou au niveau du rack. Lorsqu'une défaillance de ce type est détectée, la plateforme Azure migre automatiquement votre machine virtuelle de la machine physique défectueuse vers une machine physique saine. De tels événements sont rares, mais peuvent entraîner un redémarrage de votre machine virtuelle. 

## Meilleures pratiques de conception de votre application pour la haute disponibilité
Pour réduire l'effet des interruptions de service dues à un ou plusieurs de ces événements, nous vous recommandons les meilleures pratiques suivantes pour la haute disponibilité de vos machines virtuelles :

* [Configuration de plusieurs machines virtuelles dans un groupe à haute disponibilité pour assurer la redondance] 
* [Configuration de chaque couche application dans des groupes à haute disponibilité séparés] 
* [Combinaison de l'équilibrage de charge et des groupes à haute disponibilité] 
* [Éviter les instances uniques de machines virtuelles dans les groupes à haute disponibilité] 

### Configuration de plusieurs machines virtuelles dans un groupe à haute disponibilité pour assurer la redondance 
Pour assurer la redondance de votre application, nous vous recommandons de regrouper au moins deux machines virtuelles dans un groupe à haute disponibilité. Cette configuration assure qu'au moins une des machines virtuelles sera disponible pendant un événement de maintenance planifié ou non et répondra au niveau de 99,95 % inscrit dans les contrats de niveau de service Azure. Pour plus d'informations sur les contrats de niveau de service, consultez la section " Services cloud, machines virtuelles et réseau virtuel " de la rubrique [Contrats de niveau de service](../../../support/legal/sla/). 

Chaque machine virtuelle de votre groupe à haute disponibilité se voit attribuer un domaine de mise à jour et un domaine d'erreur par la plateforme Azure sous-jacente. Pour un groupe à haute disponibilité donné, cinq domaines de mises à jour non configurables par l'utilisateur sont attribués pour indiquer les groupes de machines virtuelles et les équipements physiques sous-jacents pouvant être redémarrés en même temps. Dans le cas où un seul groupe à haute disponibilité comprend plus de cinq machines virtuelles, la sixième machine sera placée dans le même domaine de mise à jour que la première, la septième dans le même que la deuxième, etc. L'ordre de redémarrage des domaines de mise à jour ne peut être traité de manière séquentielle au cours de la maintenance planifiée, mais les domaines de mise à jour seront redémarrés un par un.

Les domaines d'erreur définissent le groupe de machines virtuelles partageant une source d'alimentation et un commutateur réseau communs. Par défaut, les machines virtuelles configurées dans votre groupe à haute disponibilité sont réparties entre deux domaines d'erreur. Si placer vos machines virtuelles dans un groupe à haute disponibilité ne protège pas vos applications des défaillances du système d'exploitation ou des défaillances propres aux applications, cela limite les effets des défaillances d'équipement physique, des pannes du serveur et des coupures d'électricité.   

<!--Image reference-->
   ![UD FD configuration](./media/virtual-machines-manage-availability/ud-fd-configuration.png)

>[AZURE.NOTE] Pour obtenir des instructions, consultez la page [Configuration d'un groupe à haute disponibilité pour des machines virtuelles] [].

### Configuration de chaque couche application dans des groupes à haute disponibilité séparés
Si les machines virtuelles de votre groupe à haute disponibilité sont presque identiques et ont la même fonction au sein de votre application, nous vous recommandons de configurer un groupe à haute disponibilité pour chaque couche de votre application.  Si vous placez deux couches différentes dans le même groupe à haute disponibilité, toutes les machines virtuelles de la même couche application pourraient redémarrer en même temps. En configurant deux machines virtuelles ou plus dans un groupe à haute disponibilité par couche, vous vous assurez qu'au moins une machine de chaque couche restera disponible.   

Par exemple, vous pouvez rassembler toutes les machines virtuelles utilisant IIS, Apache, Nginx, etc. pour le composant frontal de votre application dans un seul groupe à haute disponibilité. Assurez-vous que seules les machines virtuelles frontales sont placées dans le même groupe à haute disponibilité. Assurez-vous également que seules les machines virtuelles de la couche de données sont placées dans leur propre groupe à haute disponibilité, comme vos machines virtuelles répliquées SQL Server ou vos machines MySQL.

<!--Image reference-->
   ![Application tiers](./media/virtual-machines-manage-availability/application-tiers.png)

 
### Combinaison de l'équilibrage de charge et des groupes à haute disponibilité
Combinez l'équilibrage de charge Azure avec un groupe à haute disponibilité pour une meilleure résilience de votre application. L'équilibrage de charge Azure répartit le trafic entre plusieurs machines virtuelles. L'équilibrage de charge Azure est compris pour nos machine virtuelles de niveau Standard. Notez que tous les niveaux de machines virtuelles ne comprennent pas l'équilibrage de charge Azure. Pour plus d'informations sur l'équilibrage de charge des machines virtuelles, consultez la page [Équilibrage de charge des machines virtuelles](../load-balance-virtual-machines/). 

Si l'équilibrage de charge n'est pas configuré pour équilibrer le trafic entre plusieurs machines virtuelles, tout événement de maintenance planifié aura un effet sur l'unique machine virtuelle en charge du trafic, ce qui entraînera une panne de votre couche application. Placer plusieurs machines virtuelles de la même couche dans le même équilibrage de charge et groupe à haute disponibilité permet de toujours avoir au moins une instance disponible pour le trafic. 

### Éviter les instances uniques de machines virtuelles dans les groupes à haute disponibilité
Évitez de laisser une instance unique de machine virtuelle dans un groupe à haute disponibilité. Les machines virtuelles se trouvant dans cette configuration ne tombent pas sous la garantie des contrats de niveau de service et connaîtront des interruptions de service au cours des événements de maintenance planifiés.  De plus, si vous déployez une instance unique de machine virtuelle dans un groupe à haute disponibilité, vous ne recevrez pas d'avertissement ou de notification de maintenance de plateforme. Dans cette configuration, votre instance unique de machine virtuelle sera redémarrée sans avertissement pendant la maintenance de la plateforme.

[Configuration de plusieurs machines virtuelles dans un groupe à haute disponibilité pour assurer la redondance]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy 
[Configuration de chaque couche application dans des groupes à haute disponibilité séparés]: #configure-each-application-tier-into-separate-availability-sets 
[Combinaison de l'équilibrage de charge et des groupes à haute disponibilité]: #combine-the-load-balancer-with-availability-sets 
[Éviter les instances uniques de machines virtuelles dans les groupes à haute disponibilité]: #avoid-single-instance-virtual-machines-in-availability-sets 

 
<!-- Link references -->
[Configuration d'un groupe à haute disponibilité pour des machines virtuelles]: ../virtual-machines-how-to-configure-availability



<!--HONumber=45--> 

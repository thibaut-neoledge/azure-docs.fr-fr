<properties 
	pageTitle="Maintenance planifiée des machines virtuelles Azure" 
	description="Présentation de la maintenance planifiée Azure et de ses effets sur vos machines virtuelles s'exécutant dans Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="kenazk" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/26/2014" 
	ms.author="kenazk"/>


# Maintenance planifiée des machines virtuelles Azure

## Pourquoi Azure exécute une maintenance planifiée
<p> Microsoft Azure exécute régulièrement des mises à jour afin d'améliorer la fiabilité, les performances et la sécurité de l'infrastructure hôte qui supporte les machines virtuelles. Nombre de ces mises à jour sont exécutées sans impact sur les machines virtuelles ou services cloud. Cependant, certaines d'entre elles requièrent un redémarrage de votre machine virtuelle pour appliquer les mises à jour requises sur l'infrastructure. La machine virtuelle est éteinte lorsqu'on applique le correctif sur l'infrastructure, puis les machines virtuelles sont redémarrées. Notez qu'il existe deux sortes de maintenance qui peuvent avoir un impact sur la disponibilité de votre machine virtuelle : maintenance planifiée et non planifiée. Cette page décrit comment Microsoft Azure exécute une maintenance planifiée. Pour plus d'informations sur la maintenance non planifiée, consultez la page [Compréhension de la maintenance planifiée et non planifiée].

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

* [Configurations de machine virtuelle]  
* [Mise à jour multi-instance]
* [Mise à jour d'instance unique]
* [Notification par courrier électronique]


## Configurations de machine virtuelle
Deux types de configuration de machine virtuelle sont disponibles : multi-instance et d'instance unique.  Les machines virtuelles multi-instance sont configurées en plaçant des machines virtuelles identiques dans un groupe à haute disponibilité. La configuration multi-instance offre une redondance. Elle est recommandée pour garantir la disponibilité de votre application. Toutes les machines virtuelles du groupe à haute disponibilité doivent être quasiment identiques et servir aux mêmes fins que votre application. Pour plus d'informations sur la configuration des machines virtuelles pour une haute disponibilité, consultez la page " <a href="http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability/">Gestion de la disponibilité des machines virtuelles</a> ". 

Contrairement aux machines virtuelles multi-instance, les machines virtuelles à instance unique sont des machines virtuelles autonomes qui ne sont pas placées dans un groupe à haute disponibilité. Seules, les machines virtuelles à instance unique ne répondent pas aux conditions du contrat de niveau de service (SLA), qui requiert le déploiement de deux machines virtuelles au minimum sous le même groupe à haute disponibilité. Pour plus d'informations sur les contrats de niveau de service, consultez la section " Services cloud, machines virtuelles et réseau virtuel "de la rubrique [Contrats de niveau de service](http://azure.microsoft.com/support/legal/sla/).


## Mise à jour multi-instance
Lors de la maintenance planifiée, la plateforme Azure met d'abord à jour l'ensemble de machines hôtes hébergeant l'ensemble de machines virtuelles dans une configuration multi-instance, provoquant le redémarrage de ces machines virtuelles. Dans une configuration multi-instance, les machines virtuelles sont mises à jour de façon à préserver la disponibilité tout au long du processus, en considérant que chaque machine sert à une fonction similaire à celle des autres machines du groupe. Chaque machine virtuelle de votre groupe à haute disponibilité se voit attribuer un domaine de mise à jour et un domaine d'erreur par la plateforme Azure sous-jacente. Chaque domaine de mise à jour est un groupe de machines virtuelles qui sera redémarré dans la même fenêtre de temps. Chaque domaine d'erreur est un groupe de machines virtuelles qui partagent une source d'alimentation et un commutateur réseau communs. 

Pour plus d'informations sur les domaines de mise à jour et d'erreur, consultez " <a href="http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability/#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy">Configuration de plusieurs machines virtuelles dans un groupe à haute disponibilité pour la redondance</a> ".

Microsoft Azure garantit qu'aucun événement de maintenance planifiée n'entraîne la déconnexion de machines virtuelles de deux domaines de mise à jour simultanément. La maintenance est exécutée en arrêtant chaque machine virtuelle, en appliquant la mise à jour aux machines hôtes, en redémarrant la machine virtuelle et en passant au domaine de mise à jour suivant. L'événement de maintenance planifiée se termine une fois tous les domaines de mise à jour mis à jour. L'ordre de redémarrage des domaines de mise à jour peut ne pas être séquentiel lors de la maintenance planifiée, mais un seul domaine de mise à jour est redémarré à la fois. Aujourd'hui, aucune notification à l'avance de la maintenance planifiée n'est fournie pour les machines virtuelles dans la configuration multi-instance.

Après la restauration de la machine virtuelle, voici un exemple de ce que votre observateur d'événements Windows peut afficher :

<!--Image reference-->
![][image2]

## Mise à jour d'instance unique
Une fois les mises à jour multi-instance terminées, Azure exécute la mise à jour sur l'ensemble des machines hôtes hébergeant les machines virtuelles à instance unique. Cette mise à jour entraîne également le redémarrage des machines virtuelles qui ne sont pas exécutées dans des groupes à haute disponibilité. Notez que même si vous n'avez qu'une seule instance fonctionnant dans un groupe à haute disponibilité, la plateforme Azure la considérera comme une multi-instance. Dans une configuration à instance unique, les machines virtuelles sont mises à jour en les éteignant, en appliquant la mise à jour à la machine hôte et en redémarrant la machine virtuelle. Ces mises à jour sont exécutées sur toutes les machines virtuelles d'une région dans une seule fenêtre de maintenance. Cet événement de maintenance planifiée aura un impact sur la disponibilité de votre application pour ce type de configuration de machine virtuelle. 
 
### Notification par courrier électronique
Pour la configuration de machines virtuelles à instance unique uniquement, Azure envoie des courriers électroniques au moins une semaine à l'avance afin de vous avertir de la maintenance planifiée. Ces courriers électroniques sont envoyés sur le compte électronique principal indiqué lors de l'inscription. Vous en trouverez un exemple ci-dessous :

<!--Image reference-->
![][image1]

<!--Anchors-->
[Pourquoi Azure exécute une maintenance planifiée]: #why-azure-performs-planned-maintenance
[Configurations de machine virtuelle]: #virtual-machine-configurations
[Mise à jour multi-instance]: #multi-instance-update
[Mise à jour d'instance unique]: #single-instance-update
[Notification par courrier électronique]: #email-notification
[image1]: ./media/virtual-machines-planned-maintenance/vmplanned1.png
[image2]: ./media/virtual-machines-planned-maintenance/EventViewerPostReboot.png


<!--Link references-->
[Les machines virtuelles gèrent la disponibilité]: ../virtual-machines-windows-tutorial/
[Compréhension de la maintenance planifiée et non planifiée]: ../virtual-machines-manage-availability/#Understand-planned-versus-unplanned-maintenance/ 




<!--HONumber=42-->

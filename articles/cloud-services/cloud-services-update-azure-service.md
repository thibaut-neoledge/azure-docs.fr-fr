<properties
pageTitle="Mise à jour d’un service cloud | Microsoft Azure"
description="Découvrez comment mettre à jour des services cloud dans Azure. Découvrez comment mettre à jour un service cloud se poursuit pour garantir la disponibilité."
services="cloud-services"
documentationCenter=""
authors="kenazk"
manager="timlt"
editor=""/>
<tags
ms.service="cloud-services"
ms.workload="tbd"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="10/26/2015"
ms.author="kenazk"/>

# Mettre à jour un service cloud

## Vue d'ensemble
À 10 000 pieds, la mise à jour d’un Service Cloud, et notamment de ses rôles et du système d’exploitation invité, est un processus qui se déroule en trois étapes. Tout d’abord, les fichiers binaires et de configuration du nouveau Service Cloud et de la version de système d’exploitation doivent être téléchargés. Dans un deuxième temps, Azure réserve des ressources de calcul et réseau au Service Cloud selon les spécifications de la nouvelle version de Service Cloud. Enfin, Azure effectue une mise à niveau propagée pour effectuer une mise à jour incrémentielle du client vers la nouvelle version ou le nouveau système d’exploitation invité tout en préservant votre disponibilité. Cet article discute des détails de cette dernière étape, la mise à jour propagée.

## Mettre à jour un Service Azure

Azure organise vos instances de rôle en regroupements logiques appelés domaines de mise à niveau (UD). Les domaines de mise à niveau (UD) sont des ensembles logiques d’instances de rôle qui sont mis à jour en tant que groupe. Azure met à jour un domaine de mise à niveau de Service Cloud à la fois, ce qui permet aux instances présentes sur les autres domaines de mise à niveau de maintenir le trafic.

Le nombre de domaines de mise à niveau par défaut est de 5. Vous pouvez spécifier un nombre différent de domaines de mise à niveau en incluant l’attribut upgradeDomainCount dans le fichier de définition du service (.csdef). Pour plus d’informations sur l’attribut upgradeDomainCount, consultez [Schéma WebRole](https://msdn.microsoft.com/library/azure/gg557553.aspx) ou [Schéma WorkerRole](https://msdn.microsoft.com/library/azure/gg557552.aspx).

Lorsque vous effectuez la mise à jour sur place d’un ou de plusieurs rôles dans votre service, Azure met à jour les ensembles d’instances de rôle en fonction du domaine de mise à niveau auquel ils appartiennent. Azure met à jour toutes les instances dans un domaine de mise à niveau donné (les arrête, les met à jour, les remet en ligne) puis passe au domaine suivant. En arrêtant uniquement les instances en cours d’exécution dans le domaine de mise à niveau en cours, Azure garantit que l’opération aura un impact minimal sur le service en cours d’exécution. Pour plus d’informations, consultez [Déroulement de la mise à niveau](https://msdn.microsoft.com/library/azure/Hh472157.aspx#proceed) plus loin dans cet article.

> [AZURE.NOTE]Bien que les termes **mise à jour** et **mise à niveau** aient une signification légèrement différente dans le contexte Azure, ils peuvent être utilisés indifféremment pour les processus et les descriptions des fonctionnalités du présent document.

Votre service doit définir au moins deux instances d’un rôle pour que le rôle soit mis à jour sur place, sans interruption de service. Si le service se compose d’une seule instance de rôle, votre service sera indisponible jusqu’à la fin de la mise à jour sur place.

Cette rubrique décrit les informations suivantes sur les mises à jour Azure :

-   [Modifications de service autorisées pendant une mise à jour](#AllowedChanges)
-   [Restauration d’une mise à jour](#RollbackofanUpdate)
-   [Lancement de plusieurs opérations de mutation sur un déploiement en cours](#multiplemutatingoperations)
-   [Distribution des rôles entre domaines de mise à niveau](#distributiondfroles)
-   [Déroulement d’une mise à niveau](#howanupgradeproceeds)

## Modifications de service autorisées pendant une mise à jour
Le tableau suivant présente les modifications de service autorisées au cours d’une mise à jour :

|Modifications autorisées de l’hébergement, des services et des rôles|Mise à jour sur place|Intermédiaire (échange d’adresses IP virtuelles)|Supprimer et redéployer|
|---|---|---|---|
|Version de système d’exploitation|Oui|Oui|Oui
|Niveau de confiance .NET|Oui|Oui|Oui|
|Taille de machine virtuelle|Oui*|Oui|Oui|
|Paramètres de stockage locaux|Augmentation uniquement*|Oui|Oui|
|Ajouter et supprimer les rôles dans un service|Oui|Oui|Oui|
|Nombre d’instances d’un rôle particulier|Oui|Oui|Oui|
|Nombre ou type de points de terminaison pour un service|Oui*|Non|Oui|
|Noms et valeurs de paramètres de configuration|Oui|Oui|Oui|
|Valeurs (et non noms) des paramètres de configuration|Oui|Oui|Oui|
|Ajouter de nouveau certificats|Oui|Oui|Oui|
|Modifier les certificats existants|Oui|Oui|Oui|
|Déployer un nouveau code|Oui|Oui|Oui|
*Nécessite le kit de développement logiciel Azure 1.5 ou versions ultérieures.

> [AZURE.WARNING]La modification de la taille de machine virtuelle détruira les données locales.


Les éléments suivants ne sont pas pris en charge pendant une mise à jour :

-   Modification du nom d’un rôle. Supprimez, puis ajoutez le rôle avec le nouveau nom.
-   Modification du nombre de domaines de mise à niveau.
-   Réduction de la taille des ressources locales.

Si vous exécutez d’autres mises à jour de votre définition de service, comme la réduction des ressources de taille locale, vous devez exécuter une mise à jour de l’échange d’adresses IP virtuelles. Pour plus d’informations, consultez [Déploiement d’échange](https://msdn.microsoft.com/library/azure/ee460814.aspx).

## Déroulement d’une mise à niveau
Vous pouvez décider si vous souhaitez mettre à jour tous les rôles de votre service ou un seul rôle de ce service. Dans les deux cas, toutes les instances de chaque rôle mis à niveau qui appartiennent au premier domaine de mise à niveau sont arrêtées, mises à niveau et remises en ligne. Une fois de retour en ligne, les instances du deuxième domaine de mise à niveau sont arrêtées, mises à niveau et remises en ligne. Un service cloud peut avoir au plus une mise à niveau active à la fois. La mise à niveau s’effectue toujours sur la dernière version du service cloud.

Le diagramme suivant montre comment la mise à niveau se poursuit si vous mettez à niveau tous les rôles dans le service :

![Mettre à niveau le service](media/cloud-services-update-azure-service/IC345879.png "Mettre à niveau le service")

Le diagramme suivant montre comment la mise à jour se déroule en cas de mise à jour d’un seul rôle :

![Mettre à niveau le rôle](media/cloud-services-update-azure-service/IC345880.png "Mettre à niveau le rôle")

> [AZURE.NOTE]Lors de la mise à niveau d’un service d’instance unique vers plusieurs instances, votre service est réduit pendant que la mise à niveau est exécutée, en fonction de la façon dont Azure met à niveau les services. Le contrat de niveau de service garantissant la disponibilité du service ne s’applique qu’aux services déployés avec plusieurs instances. La liste suivante décrit la façon dont les données de chaque lecteur sont affectées à chaque scénario de mise à niveau de service Azure :
>
>Redémarrage de machine virtuelle :
>
-   C: Préservés
-   D: Préservés
-   E: Préservés
>
>Redémarrage de portail :
>
-   C: Préservés
-   D: Préservés
-   E: Détruits
>
>Réinitialiser le portail :
>
-   C: Préservés
-   D: Détruits
-   E: Détruits

>Mise à niveau sur place :
>
-   C: Préservés
-   D: Préservés
-   E: Détruits
>
>Migration de nœud
>
-   C: Détruits
-   D: Détruits
-   E: Détruits

>Notez que, dans la liste ci-dessus, le lecteur E: représente le lecteur racine du rôle et qu’il ne doit pas être codées en dur. Utilisez plutôt la variable d’environnement %RoleRoot% pour représenter le lecteur.

>Pour réduire les temps d’arrêt en cas de mise à niveau d’un service à instance unique, déployez un nouveau service à instances multiples sur le serveur intermédiaire et effectuez un échange d’adresses IP virtuelles.

Pendant une mise à jour automatique, le contrôleur de structure Azure évalue de façon périodique l’état du service cloud pour déterminer quand il est temps de passer à un autre domaine de mise à niveau. Cette évaluation de l’état est effectuée sur une base par rôle et considère uniquement les instances de la dernière version (c’est-à-dire les instances de domaines de mise à niveau qui ont déjà été examinées). Il vérifie qu’un nombre minimum d’instances de rôle, pour chaque rôle, a atteint un état terminal satisfaisant.

### Délai de démarrage de l’instance de rôle 
Le contrôleur de structure attend 30 minutes pour que chaque instance de rôle atteigne un état démarré. Si la durée d'expiration est écoulée, le contrôleur de structure continuera à remonter jusqu’à l'instance de rôle suivante.

## Restauration d’une mise à jour
Azure offre une flexibilité dans la gestion des services pendant la mise à jour en vous permettant de lancer des opérations supplémentaires sur un service, une fois la demande de mise à jour initiale acceptée par le contrôleur d’architecture Azure. Une restauration ne peut être effectuée que lorsqu’une mise à jour (modification de configuration) ou une mise à niveau se trouve dans l’état **en cours** du déploiement. Une mise à jour ou mise à niveau est considérée comme en cours tant qu’il existe au moins une instance du service qui n’a pas encore été mise à jour vers la nouvelle version. Pour vérifier si une restauration est autorisée, contrôlez si la valeur de l’indicateur RollbackAllowed retournée par les opérations d’[obtention du déploiement](https://msdn.microsoft.com/library/azure/ee460804.aspx) et d’[obtention des propriétés de service cloud](https://msdn.microsoft.com/library/azure/ee460806.aspx), est bien définie sur true.

> [AZURE.NOTE]Il convient d’appeler la restauration uniquement sur une mise à jour ou une mise à niveau **sur place**, parce que les mises à niveau d’échange d’adresse virtuelle impliquent le remplacement d’une instance complète s’exécutant sur votre service avec une autre.

Le rétablissement d’une mise à jour en cours a les effets suivants sur le déploiement :

-   Toutes les instances de rôle n’ayant pas encore été mises à jour ou à niveau vers la nouvelle version ne sont ni mises à jour ni mises à niveau, car les instances s’exécutent déjà sur la version cible du service.
-   Toutes les instances de rôle déjà mises à jour ou à niveau vers la nouvelle version du fichier de package de service (*.cspkg) ou le fichier de configuration (*.cscfg) (ou les deux fichiers) sont rétablis vers la version précédant la mise à niveau de ces fichiers.

Cette fonction est assurée par les fonctionnalités suivantes :

-   La [restauration de mise à jour ou de mise à niveau](https://msdn.microsoft.com/library/azure/hh403977.aspx), qui peut être appelée sur une mise à jour de configuration (déclenchée par un appel [Modifier la Configuration de déploiement](https://msdn.microsoft.com/library/azure/ee460809.aspx)) ou une mise à niveau (déclenchée par l’appel de [mise à niveau d’un déploiement](https://msdn.microsoft.com/library/azure/ee460793.aspx)) tant qu’il reste au moins une instance du service qui n’a pas encore été mise à jour vers la nouvelle version.
-   L’élément Verrouillé et l’élément RollbackAllowed, qui sont retournés comme parties intégrantes du corps de réponse des opérations [Obtention d’un déploiement](https://msdn.microsoft.com/library/azure/ee460804.aspx) et [Obtention des propriétés de Service Cloud](https://msdn.microsoft.com/library/azure/ee460806.aspx) :
    1.  L’élément Verrouillé permet de détecter si une opération de mutation peut être appelée sur un déploiement donné.
    2.  L’élément RollbackAllowed vous permet de détecter lorsque l’opération de [restauration de mise à jour ou de mise à niveau](https://msdn.microsoft.com/library/azure/hh403977.aspx) peut être appelée sur un déploiement donné.

    Pour effectuer une restauration, il est inutile de vérifier les éléments Verrouillés et RollbackAllowed. Il suffit de vérifier que RollbackAllowed est défini sur true. Ces éléments sont retournés uniquement si ces méthodes sont appelées avec l’en-tête de demande défini sur « x-ms-version : 2011-10-01 » ou une version ultérieure. Pour plus d’informations sur les en-têtes de contrôle de version, consultez [Contrôle de version de gestion de service](https://msdn.microsoft.com/library/azure/gg592580.aspx).

Dans certaines situations, la restauration d’une mise à jour ou d’une mise à niveau n’est pas prise en charge, notamment les suivantes :

-   Réduction des ressources locale : si la mise à jour augmente les ressources locales d’un rôle, la plateforme Azure n’autorise pas la restauration. Pour plus d’informations sur la configuration des ressources locales pour un rôle, consultez [Configurer les ressources de stockage local](https://msdn.microsoft.com/library/azure/ee758708.aspx).
-   Limitations de quota : si la mise à jour correspond à une opération de réduction, vous pouvez ne plus avoir suffisamment de capacité de calcul pour effectuer l’opération de restauration. Chaque abonnement Azure a un quota associé qui spécifie le nombre maximal de cœurs qui peuvent être utilisés par les services hébergés appartenant à cet abonnement. Si l’exécution de la restauration d’une mise à jour donnée met votre abonnement au dessus du quota, la restauration ne sera pas activée.
-   Condition de concurrence : si la mise à jour initiale est terminée, la restauration est impossible.

Exemple de la situation où le déploiement d’une mise à jour peut être utile si vous utilisez l’opération de [mise à niveau de déploiement](https://msdn.microsoft.com/library/azure/ee460793.aspx) en mode manuel pour contrôler la fréquence à laquelle la mise à niveau majeure d’un service hébergé Azure est exécutée.

Lors du déploiement de la mise à niveau, vous appelez [Mettre à niveau un déploiement](https://msdn.microsoft.com/library/azure/ee460793.aspx) en mode manuel et commencez à parcourir les domaines de mise à niveau. Si à un moment donné, lorsque vous surveillez la mise à niveau, vous notez que des instances de rôle dans les premiers domaines de mise à niveau ne répondent plus, vous pouvez appeler une opération de [restauration de mise à jour ou de mise à niveau](https://msdn.microsoft.com/library/azure/hh403977.aspx) sur le déploiement, ce qui laissera les instances non mises à jour intactes, et restaurera les instances ayant été mises à niveau vers le package et la configuration de service à leur niveau antérieur.

## Lancement de plusieurs opérations de mutation sur un déploiement en cours
Dans certains cas, vous pouvez souhaiter initier plusieurs opérations de mutation simultanées sur un déploiement en cours. Par exemple, vous pouvez effectuer une mise à jour du service et, pendant la propagation de la mise à jour sur votre service, vous pouvez apporter des modifications, par exemple, restaurer la mise à jour, en appliquer une autre, ou encore supprimer le déploiement. Cela peut s’avérer nécessaire si une mise à niveau de service contient un code bogué qui entraîne des échecs répétés d’une instance de rôle mise à niveau. Dans ce cas, le contrôleur de structure Azure ne sera pas en mesure de faire avancer l’application de cette mise à jour, parce qu’un nombre insuffisant d’instances du domaine mis à niveau sont dans un bon état. Cet état est appelé *Déploiement bloqué*. Vous pouvez débloquer le déploiement en restaurant la mise à jour ou en appliquant une nouvelle mise à jour par dessus celle qui a échoué.

Une fois que le contrôleur de structure Azure a reçu la demande initiale de mise à jour ou de mise à niveau, vous pouvez démarrer des opérations de mutation suivantes. Autrement dit, il est inutile d’attendre la fin de l’opération initiale avant de commencer une autre opération de mutation.

Le lancement d’une deuxième opération de mise à jour pendant que la première mise à jour est en cours permet d’effectuer une opération similaire à l’opération de restauration. Si la deuxième mise à jour est en mode automatique, le premier domaine de mise à niveau sera immédiatement, mis à niveau, ce qui pourra éventuellement provoquer la mise hors ligne de plusieurs domaines de mise à niveau hors ligne au même moment dans le temps.

Les opérations de mutation sont les suivantes : [Modification de la configuration de déploiement](https://msdn.microsoft.com/library/azure/ee460809.aspx), [Mise à niveau d’un déploiement](https://msdn.microsoft.com/library/azure/ee460793.aspx), [État de la mise à jour de déploiement](https://msdn.microsoft.com/library/azure/ee460808.aspx), [Suppression de déploiement](https://msdn.microsoft.com/library/azure/ee460815.aspx) et [Restauration de mise à jour ou de mise à niveau](https://msdn.microsoft.com/library/azure/hh403977.aspx).

Deux opérations, [Obtenir le déploiement](https://msdn.microsoft.com/library/azure/ee460804.aspx) et [Obtenir les propriétés de service cloud](https://msdn.microsoft.com/library/azure/ee460806.aspx), retournent l’indicateur Verrouillé qui peut être examiné pour déterminer si une opération de mutation peut être appelée sur un déploiement donné.

Pour appeler la version de ces méthodes qui renvoie un indicateur Verrouillé, vous devez définir un en-tête de requête « x-ms-version: 2011-10-01 » ou ultérieure. Pour plus d’informations sur les en-têtes de contrôle de version, consultez [Contrôle de version de gestion de service](https://msdn.microsoft.com/library/azure/gg592580.aspx).

## Distribution des rôles entre domaines de mise à niveau
Azure répartit les instances d’un rôle de manière égale sur un certain nombre de domaines de mise à niveau, qui peuvent être configurés dans le cadre du fichier de définition de service (.csdef). Le nombre maximal de domaines de mise à niveau est de 20, et le nombre par défaut est 5. Pour plus d’informations sur la façon de modifier le fichier de définition de service, consultez [Schéma de définition du service Azure (fichier .csdef)](https://msdn.microsoft.com/library/azure/ee758711.aspx).

Par exemple, si votre rôle comporte dix instances, par défaut, chaque domaine de mise à niveau contient deux instances. Si votre rôle comporte 14 instances, alors quatre des domaines de mise à niveau contiennent trois instances et un cinquième domaine en contient deux.

Les domaines de mise à niveau sont identifiés avec un index de base zéro : l’ID du premier domaine de mise à niveau est égal à 0, et le deuxième domaine de mise à niveau possède un ID de 1 et ainsi de suite.

Le diagramme suivant montre comment un service contenant deux rôles qui sont distribués lorsque le service définit deux domaines de mise à niveau. Le service exécute huit instances de rôle web et neuf instances de rôle de travail.

![Distribution des domaines de mise à niveau](media/cloud-services-update-azure-service/IC345533.png "Distribution des domaines de mise à niveau")

> [AZURE.NOTE]Notez qu’Azure contrôle la façon dont les instances sont affectées entre d’un domaine de mise à niveau à l’autre. Il est impossible de spécifier quelles sont les instances affectées, et à quel domaine.

## Étapes suivantes
[Gestion de Cloud Services](cloud-services-how-to-manage.md)<br> [Surveiller Cloud Services](cloud-services-how-to-monitor.md)<br> [Configurer Cloud Services](cloud-services-how-to-cofigure.md)<br>

<!---HONumber=Nov15_HO4-->
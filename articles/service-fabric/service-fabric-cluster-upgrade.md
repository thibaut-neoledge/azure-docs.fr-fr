<properties
   pageTitle="Mise à niveau d'un cluster Service Fabric | Microsoft Azure"
   description="Mettez à niveau le code et/ou la configuration Service Fabric qui exécute un cluster Service Fabric, y compris la mise à niveau de certificat, l'ajout de ports d'application, les correctifs de système d'exploitation, etc. À quoi vous attendre lors de l'exécution des mises à niveau ?"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="chackdan"/>

# Mettre à niveau un cluster Service Fabric

Un cluster Azure Service Fabric est une ressource que vous possédez mais qui est en partie gérée par Microsoft. Cet article décrit ce qui est géré automatiquement et ce que vous pouvez configurer vous-même.

## Configuration du cluster qui est gérée automatiquement

Microsoft tient à jour le code de l'infrastructure et la configuration exécutés dans un cluster. Nous effectuons des mises à niveau automatiques surveillées du logiciel en fonction des besoins. Ces mises à niveau peuvent concerner le code, la configuration ou les deux. Pour garantir que votre application ne subit aucun impact ou un impact minimal en raison de ces mises à niveau, nous effectuons les mises à niveau en trois phases, comme suit.

### Phase 1 : La mise à niveau est effectuée à l'aide de toutes les stratégies d'intégrité du cluster

Pendant cette phase, les mises à niveau traitent un domaine de mise à niveau à la fois et les applications qui étaient en cours d'exécution dans le cluster continuent à fonctionner sans interruption. Les stratégies d'intégrité du cluster (une combinaison de l'intégrité du nœud et l'intégrité de toutes les applications en cours d'exécution dans le cluster) sont respectées pendant la durée de la mise à niveau.

Si les stratégies d'intégrité du cluster ne sont pas respectées, la mise à niveau est annulée et un message électronique est envoyé au propriétaire de l'abonnement. Le message électronique contient les informations suivantes :

- Une notification indiquant que nous avons dû annuler une mise à niveau du cluster.
- Des suggestions d'actions correctives, le cas échéant.
- Le nombre de jours (n) avant l'exécution de la Phase 2.

Nous essayons d'exécuter la même mise à niveau plusieurs fois dans le cas où les mises à niveau ont échoué pour des raisons d'infrastructure. Après le délai de n jours à partir de la date d'envoi du message électronique, nous passons à la Phase 2.

Si les stratégies d'intégrité du cluster sont respectées, la mise à niveau est considérée comme réussie et marquée comme terminée. Cela peut se produire pendant la première exécution ou l'une des exécutions de la mise à niveau suivantes dans cette phase. Aucun message électronique de confirmation d'une exécution réussie n'est envoyé. Ceci afin d'éviter de vous envoyer trop de messages électroniques. La réception d'un message devant être considérée comme une exception. Nous pensons que la plupart des mises à niveau du cluster s'exécuteront sans affecter la disponibilité de votre application.

### Phase 2 : La mise à niveau est effectuée à l'aide des stratégies d'intégrité par défaut uniquement

Les stratégies d'intégrité sont définies de telle sorte que le nombre d'applications intègres au début de la mise à niveau reste identique pendant la durée de la mise à niveau. Pendant la Phase 2, comme durant la Phase 1, les mises à niveau traitent un domaine de mise à niveau à la fois et les applications qui étaient en cours d'exécution dans le cluster continuent à fonctionner sans interruption. Les stratégies d'intégrité du cluster (une combinaison de l'intégrité du nœud et l'intégrité de toutes les applications en cours d'exécution dans le cluster) sont respectées pendant la durée de la mise à niveau.

Si les stratégies d'intégrité du cluster en vigueur ne sont pas respectées, la mise à niveau est annulée et un message électronique est envoyé au propriétaire de l'abonnement. Le message électronique contient les informations suivantes :

- Une notification indiquant que nous avons dû annuler une mise à niveau du cluster.
- Des suggestions d'actions correctives, le cas échéant.
- Le nombre de jours (n) avant l'exécution de la Phase 3.

Nous essayons d'exécuter la même mise à niveau plusieurs fois dans le cas où les mises à niveau ont échoué pour des raisons d'infrastructure. Un message électronique de rappel est envoyé deux jours avant que le délai de n jours ne soit écoulé. Après le délai de n jours à partir de la date d'envoi du message électronique, nous passons à la Phase 3. Les messages électroniques que nous vous envoyons lors de la Phase 2 doivent être pris au sérieux et des actions correctives doivent être prises.

Si les stratégies d'intégrité du cluster sont respectées, la mise à niveau est considérée comme réussie et marquée comme terminée. Cela peut se produire pendant la première exécution ou l'une des exécutions de la mise à niveau suivantes dans cette phase. Aucun message électronique de confirmation d'une exécution réussie n'est envoyé.

### Phase 3 : La mise à niveau est effectuée à l'aide de stratégies d'intégrité agressives

Ces stratégies d'intégrité visent à compléter la mise à niveau plutôt que protéger l'intégrité des applications. Très peu de mises à niveau du cluster se terminent par cette phase. Si votre cluster atteint cette phase, il est très probable que votre application devienne défectueuse et/ou ne soit plus disponible.

Comme pour les deux autres phases, les mises à niveau de la Phase 3 traitent un domaine de mise à niveau à la fois.

Si les stratégies d'intégrité de cluster en vigueur ne sont pas respectées, la mise à niveau est annulée. Nous essayons d'exécuter la même mise à niveau plusieurs fois dans le cas où les mises à niveau ont échoué pour des raisons d'infrastructure. Après cela, le cluster est épinglé et il ne bénéficiera plus des mises à niveau et/ou du support.

Un message électronique contenant ces informations est envoyé au propriétaire de l'abonnement, ainsi que les actions correctives. Nous pensons qu'aucun cluster ne sera confronté à un échec de la Phase 3.

Si les stratégies d'intégrité du cluster sont respectées, la mise à niveau est considérée comme réussie et marquée comme terminée. Cela peut se produire pendant la première exécution ou l'une des exécutions de la mise à niveau suivantes dans cette phase. Aucun message électronique de confirmation d'une exécution réussie n'est envoyé.

## Configurations de cluster que vous contrôlez

Voici les configurations que vous pouvez modifier sur un cluster en direct.

### Certificats

Vous pouvez facilement mettre à jour les certificats principaux ou secondaires à partir du portail (affiché ci-dessous) ou par le biais de l'émission d'une commande PUT sur la ressource servicefabric.cluster.

![Capture d'écran qui affiche les empreintes numériques des certificats dans le portail Azure.][CertificateUpgrade]

>[AZURE.NOTE] Avant d'identifier un certificat à utiliser pour les ressources de cluster, vous devez effectuer les étapes suivantes ; sinon, les nouveaux certificats ne seront pas utilisés : 1. Charger le nouveau certificat dans le coffre de clés. Pour obtenir des instructions, consultez [Sécurité Service Fabric](service-fabric-cluster-security.md). Commencez à l'étape 2 dans ce document. 2. Mettez à jour toutes les machines virtuelles qui composent le cluster pour déployer le certificat sur celles-ci. Pour ce faire, consultez le [blog de l'équipe Azure Key Vault](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx).

### Ports d'application

Vous pouvez modifier les ports d'application en modifiant les propriétés de ressource d'équilibrage de charge associées au type de nœud. Vous pouvez utiliser le portail ou vous pouvez utiliser Resource Manager PowerShell directement.

Pour ouvrir un nouveau port sur toutes les machines virtuelles dans un type de nœud, vous devez effectuer les opérations suivantes :

1. Ajouter une nouvelle sonde à l'équilibrage de charge approprié.

    Si vous avez déployé votre cluster à l'aide du portail, l'équilibrage de charge est nommé « loadBalancer-0 », « loadBalancer-1 », etc., pour chaque type de nœud. Étant donné que les noms de l'équilibrage de charge sont uniques dans un groupe de ressources seulement, il est préférable de les rechercher dans un groupe de ressources spécifique.

    ![Capture d'écran qui illustre l'ajout d'une sonde à un équilibrage de charge dans le portail.][AddingProbes]

2. Ajouter une nouvelle règle pour l'équilibrage de charge.

    Pour le même équilibrage de charge, ajoutez une nouvelle règle à l'aide de la sonde créée à l'étape précédente.

    ![Capture d'écran qui illustre l'ajout d'une nouvelle règle à un équilibrage de charge dans le portail.][AddingLBRules]


### Propriétés de positionnement

Pour chaque type de nœud, vous pouvez ajouter des propriétés de positionnement personnalisées que vous souhaitez utiliser dans vos applications. NodeType est une propriété par défaut que vous pouvez utiliser sans l'ajouter explicitement.

>[AZURE.NOTE] Pour plus d'informations sur l'utilisation des propriétés de positionnement, consultez [Vue d'ensemble des contraintes de placement](service-fabric-placement-constraint.md).

### Métriques de capacité

Pour chaque type de nœud, vous pouvez ajouter des mesures de capacité personnalisées que vous souhaitez utiliser dans vos applications pour créer un rapport sur la charge. Pour plus d'informations sur l'utilisation de mesures de capacité pour créer un rapport sur la charge, consultez la [Présentation du rapport de charge dynamique](service-fabric-resource-balancer-dynamic-load-reporting.md).

### Correctifs de système d'exploitation sur les machines virtuelles qui composent le cluster

Ceci est publié sous la forme d'une fonctionnalité automatique. Mais aujourd'hui, vous êtes chargé de corriger vos machines virtuelles. Vous devez effectuer cette opération sur une seule machine virtuelle à la fois, afin de ne pas en arrêter plusieurs à la fois.

### Mises à niveau du système d'exploitation sur les machines virtuelles qui composent le cluster

Si vous devez mettre à niveau l'image du système d'exploitation sur les machines virtuelles du cluster, alors vous devez procéder sur une machine virtuelle à la fois et vous êtes responsable de cette mise à niveau. Il n'existe pour le moment aucune automatisation.

## Étapes suivantes

- Découvrez comment [mettre votre cluster à l’échelle](service-fabric-cluster-scale-up-down.md).
- Découvrez les [mises à niveau de l’application](service-fabric-application-upgrade.md).

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes.png
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png

<!---HONumber=AcomDC_0218_2016-->
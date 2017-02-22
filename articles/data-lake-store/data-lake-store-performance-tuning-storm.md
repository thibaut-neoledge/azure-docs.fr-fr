---
title: "Recommandations en matière d’optimisation des performances d’Azure Data Lake Store Storm | Microsoft Docs"
description: "Recommandations en matière d’optimisation des performances d’Azure Data Lake Store Storm"
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/19/2016
ms.author: stewu
translationtype: Human Translation
ms.sourcegitcommit: ebf876f946eceddce9c8c990d8b28fcb969bec23
ms.openlocfilehash: 112226028c053cc91f9fb2bc0e5978f7cb2343ed


---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-store"></a>Recommandations en matière d’optimisation des performances pour Storm sur HDInsight et Azure Data Lake Store

Il existe plusieurs facteurs à prendre en compte lorsque vous réglez les performances d’une topologie Storm.  Il est important de comprendre les caractéristiques du travail effectué par les spouts et les bolts (si le travail est intensif en E/S ou en mémoire).

## <a name="prerequisites"></a>Composants requis 

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/). 
* **Un compte Azure Data Lake Store**. Pour savoir comment en créer un, consultez [Prise en main d'Azure Data Lake Store](data-lake-store-get-started-portal.md) 
* **Cluster Azure HDInsight** ayant accès à un compte Data Lake Store. Voir [Créer un cluster HDInsight avec Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Veillez à activer le Bureau à distance pour le cluster. 
* **Exécution d’un cluster Storm sur Azure Data Lake Store**.  Pour plus d’informations, consultez [Storm sur HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-overview) 
* **Instructions d’optimisation des performances sur ADLS**.  Pour les concepts généraux sur les performances, consultez le [Guide de réglage des performances pour Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-performance-tuning-guidance)  

**Ajustement du parallélisme de la topologie**

Vous pouvez améliorer les performances en augmentant l’accès simultané aux E/S vers et à partir du d’Azure Data Lake Store.  
Une topologie Storm possède un ensemble de configurations qui déterminent le parallélisme :
* Nombre de processus worker : les workers sont répartis uniformément sur les machines virtuelles.
* Nombre d’instances d’exécuteur de spout
* Nombre d’instances d’exécuteur de bolt
* Nombre de tâches de spout
* Nombre de tâches de bolt

Par exemple, sur un cluster avec 4 machines virtuelles et 4 processus worker, 32 exécuteurs de spout et 32 tâches spout, 256 exécuteurs bolt et 512 tâches bolt :

Chaque superviseur, un nœud de travail, aura un seul processus worker de JVM qui gèrera 4 threads spout et 64 threads bolt. Au sein de chaque thread, les tâches sont exécutées séquentiellement. Avec la configuration ci-dessus, chaque thread spout aura une tâche et chaque thread bolt aura 2 tâches.

Dans Storm, voici les différents composants impliqués et leur impact sur le niveau de parallélisme, que vous obtenez :
* Le nœud principal (appelé Nimbus dans Storm) est utilisé pour envoyer et gérer les travaux. Ces nœuds n’ont aucun impact sur le degré de parallélisme.
* Les nœuds superviseurs : dans Azure HDInsight, ils correspondent aux nœuds de travail Azure VM.
* Les tâches worker sont des processus Storm s’exécutant sur des machines virtuelles. Chaque tâche worker correspond à une instance de JVM Java. Storm distribue le nombre de processus worker que vous spécifiez de manière aussi égale que possible sur les nœuds de travail.
* Instances d’exécuteurs spout et bolt : chaque instance d’exécuteur correspond à un thread s’exécutant dans les processus workers (JVM)
* Tâches Storm : il s’agit des tâches logiques exécutées par chacun de ces threads. Cela ne modifie pas le niveau de parallélisme. Vous devez donc évaluer si vous avez besoin de plusieurs tâches par exécuteur ou non.

## <a name="guidance"></a>Assistance

Lorsque vous travaillez avec Azure Data Lake, vous obtenez de meilleures performances si vous procédez comme suit :
* Fusionnez vos petits éléments dans des tailles supérieures (idéalement 4 Mo)
* Effectuez autant de requêtes simultanées que vous le pouvez. Étant donné que chaque thread bolt effectue des lectures bloquantes, il est préférable d’avoir un nombre de threads dans la plage de 8 à 12 par cœur, afin que la carte réseau et le processeur soient bien utilisés.  Une machine virtuelle plus grande permettra d’avoir plus de demandes simultanées.  

## <a name="example"></a>Exemple

Supposons que vous disposez d’un cluster de 8 nœuds de travail avec une machine virtuelle Azure D13v2.  Une machine virtuelle D13v2 a 8 cœurs, donc sur les 8 nœuds de travail, vous avez un total de 64 cœurs.

Supposons que nous utilisons 8 threads bolt par cœur. Avec 64 cœurs, nous voulons 512 instances (c'est-à-dire les threads) d’exécuteur bolt au total. Dans ce cas, supposons que nous commençons avec une machine virtuelle Java (JVM) par machine virtuelle et utilisons principalement la simultanéité des threads dans la JVM pour obtenir la simultanéité. Cela signifie que nous avons besoins de 8 tâches worker (une par machine virtuelle Azure) et 512 exécuteurs bolt. Avec cette configuration, Storm va tenter de distribuer les workers uniformément entre les nœuds de travail (également appelés nœuds de superviseur), en donnant à chaque nœud de travail une machine virtuelle Java. À présent dans l’exemple, Storm va tenter de distribuer les exécuteurs uniformément entre les superviseurs, en donnant à chaque superviseur (c'est-à-dire chaque JVM) 8 threads chacun.

## <a name="further-tuning"></a>Paramétrage supplémentaire
Une fois que vous avez la topologie de base, vous pouvez envisager de modifier les paramètres :
* **Nombre de JVM par nœud de travail :** si vous avez une structure de données volumineuse (par exemple, une table de recherche) que vous hébergez en mémoire, chaque JVM demande une copie distincte, alors que d’avoir moins de JVM vous permet d’utiliser la structure sur plusieurs threads. Pour les E/S du bolt, le nombre de JVM ne fait pas beaucoup de différence pour le nombre de threads ajoutés sur ces JVM. Par souci de simplicité, nous recommandons une JVM par worker, mais selon ce que votre bolt fait ou le traitement d’application dont vous avez besoin, vous devez évaluer s’il s’agit d’une valeur à modifier.
* **Nombre d’exécuteurs spout :** étant donné que l’exemple présenté ici utilise les bolts pour écrire dans Azure Data Lake, le nombre de spouts ne concerne pas directement les performances bolt. Toutefois, selon la quantité de traitement ou d’E/S se produisant dans le spout, vous devez régler les spouts pour de meilleures performances. Dans tous les cas, vous devez vous assurer que vous avez suffisamment spouts pour pouvoir occuper les bolts, c’est-à-dire que les débits de sortie des spouts doivent correspondre aux débits des bolts. La configuration réelle dépend du spout, un sujet qui dépasse la portée de ce document.
* **Nombre de tâches :** chaque bolt s’exécute en tant que thread unique. Les tâches supplémentaires par bolt n’apportent pas de simultanéité supplémentaire. Le seul moment où elles sont utiles est si votre processus d’accusé de réception de tuple représente une grande partie du temps d’exécution de votre bolt. Nous vous recommandons de grouper de nombreux tuples dans un ensemble plus grand avant d’envoyer un accusé de réception à partir du bolt. Ainsi, dans que la plupart des cas, l’ajout de tâches n’offre aucun avantage supplémentaire.
* **Groupage local ou aléatoire :** lorsque ce paramètre est activé, les tuples sont envoyés vers des bolts dans le même processus de travail. Cela réduit les communications entre processus et les appels réseau. Cela est recommandé pour la plupart des topologies.

En tant qu’approche initiale, nous vous conseillons de commencer avec un scénario de base, et de faire des tests avec vos propres données pour régler les paramètres mentionnés ci-dessus afin d’obtenir des performances optimales.

## <a name="tuning-the-spout"></a>Réglage du spout

**Délai d’expiration de tuple**

topology.message.timeout.secs : ce paramètre détermine la durée d’attente de fin et d’accusé de réception d’un message avant de le considérer comme ayant échoué.

**Mémoire maximale par le processus worker**

Worker.childopts : ce paramètre permet de spécifier des paramètres de ligne de commande supplémentaires pour les workers Java. Le paramètre le plus couramment utilisé ici est XmX, qui détermine la quantité maximale de mémoire allouée au segment de mémoire d’une machine virtuelle Java.

**Nombre max de spouts en attente**

Topology.max.spout.Pending : cette configuration détermine le nombre de tuples qui peuvent être évalués (pas encore acceptés sur tous les nœuds de la topologie) par thread spout à un moment donné.

Un bon calcul à faire est l’estimation de la taille de chacun de vos tuples. Ensuite, calculez la quantité de mémoire par thread spout. La mémoire totale allouée à un thread divisée par cette valeur devrait vous donner la limite supérieure pour le paramètre de nombre maximal de spouts en attente.

## <a name="tuning-the-bolt"></a>Réglage du bolt
Lorsque vous écrivez sur ADLS, il est recommandé de définir une stratégie de synchronisation de taille (tampon du côté client) sur 4 Mio.  Un vidage ou une hsync() est ensuite effectué uniquement lorsque la taille du tampon atteint la valeur précédente.  Le pilote ADLS sur la machine virtuelle de worker effectue automatiquement cette mise en tampon, à moins que l’utilisateur exécute explicitement une hsync().

Le bolt Storm par défaut d’ADLS a un paramètre de stratégie de synchronisation de taille (fileBufferSize) qui peut être utilisé pour régler ce paramètre.

Dans les topologies intensives en E/S, il est recommandé que chaque thread bolt écrive dans son propre fichier et définisse une stratégie de rotation de fichiers (fileRotationSize).  Lorsque le fichier atteint une certaine taille, le flux est automatiquement vidé, et un nouveau fichier est utilisé pour l’écriture.  La taille de fichier recommandée pour la rotation est de 1 Go.

**Quand accuser réception :** dans Storm, un spout conserve un tuple jusqu'à ce qu’il soit admis explicitement par le bolt.  Si un tuple a été lu par le bolt, sans accusé de réception, cela signifie qu’il n’est pas garanti que le bolt est resté dans le serveur principal Azure Data Lake Store.  Une fois qu’un tuple est admis, la persistance du spout peut être garantie par le bolt. Il peut donc supprimer les données source, quelle que soit la source utilisée pour la lecture.  

**Pour de meilleures performances sur ADLS :** nous recommandons 4 Mo de mémoire tampon bolt, avec une écriture sur le serveur principal ADLS en tant qu’écriture unique de 4 Mo. Une fois les données correctement écrites dans le magasin (en appelant hflush()), bolt peut accuser réception des données auprès du spout. C’est ce que l’exemple de bolt fourni ici effectue. Il est également acceptable de contenir de grands nombres de tuples avant d’appeler hflush() et de confirmer réception des tuples. Toutefois, cela augmente le nombre de tuples en cours de vérification que le spout doit contenir, et par conséquent la quantité de mémoire requise par machine virtuelle Java.

Les applications peuvent avoir l’obligation d’accuser réception plus fréquemment (à des tailles de données inférieures à 4 Mo) pour d’autres raisons non liées aux performances. Toutefois, cela peut affecter le débit d’E/S sur le serveur principal de stockage. Le client doit donc considérer sérieusement ce compromis sur les performances d’E/S du bolt.

Si le taux de tuples entrants n’est pas suffisamment élevé pour que le remplissage de la mémoire tampon de 4 Mo s’effectue rapidement, vous devez envisager de résoudre le problème de plusieurs façons :
* Réduire le nombre de bolts, pour qu’il y ait moins de tampons de 4 Mo à remplir
* Utiliser une stratégie basée sur le temps ou le décompte, avec un hflush() déclenché tous les x vidages ou toutes les y millisecondes, et accusé de réception des tuples alors accumulés.

Notez que dans ce cas le débit est plus faible, mais avec un rythme d’événements faible, le débit maximal n’est de toute façon pas l’objectif principal.  Les corrections ci-dessus vous permettent de réduire le temps de bout en bout nécessaire à un tuple pour circuler vers le magasin, ce qui peut être important si vous souhaitez avoir un pipeline en temps réel, même en cas de rythme d’événements faible.  Notez également que si votre débit entrant de tuples est faible, vous devrez également ajuster le paramètre topology.message.timeout_secs, pour que les tuples n’expirent pas pendant qu’ils sont mis en mémoire tampon ou traités.

## <a name="interpreting-storm-ui"></a>Interprétation de l’interface utilisateur de Storm  
Lorsque votre topologie est en cours d’exécution, vous pouvez la surveiller dans l’interface utilisateur de Storm. Lorsque vous observez l’interface utilisateur, voici les principaux paramètres à examiner :

* **Latence totale de l’exécution du processus** : il s’agit de la durée moyenne nécessaire à un tuple pour être émis par un spout, traité par un bolt et être validé.

* **Latence totale du processus bolt** : il s’agit du temps moyen passé par le tuple sur le bolt jusqu'à réception d’un accusé de réception.

* **Latence totale d’exécution de bolt** : il s’agit du temps moyen passé par le bolt dans la méthode execute.

* **Nombre d’échecs** : fait référence au nombre de tuples qui n’ont pas pu être entièrement traités avant expiration.

* **Capacité** : il s’agit d’une mesure du niveau d’activité de votre système. Si ce nombre a la valeur 1, vos bolts fonctionnent aussi rapidement que possible. S’il est inférieur à 1, augmentez votre parallélisme. S’il est supérieur à&1;, réduisez le parallélisme.

## <a name="common-troubleshooting-scenarios"></a>Scénarios courants de résolution des problèmes
* **Grand nombre de tuples expirés** : examinez chaque nœud de la topologie pour déterminer où se trouve le goulet d’étranglement. La raison la plus courante est que les bolts ne suivent pas le rythme des spouts. La conséquence est que des tuples bouchent les mémoires tampon internes en attendant d’être traités. Envisagez d’augmenter la valeur de délai d’attente ou de diminuer le nombre maximal de spouts en attente.

* **Latence totale d’exécution de traitement élevée, mais faible latence de traitement des bolts** : dans ce cas, les tuples ne sont pas émis assez rapidement. Vérifiez qu’il existe un nombre suffisant de validateurs. Une autre possibilité est qu’ils restent en file d’attente trop longtemps avant que les bolts lancent leur traitement. Réduisez le nombre maximal de spouts en attente.

* **Latence d’exécution de bolt élevée** : cela signifie que la méthode execute() de votre bolt prend trop de temps. Optimisez le code ou examinez le comportement de vidage/les tailles d’écriture.

## <a name="limitation"></a>Limitations à prendre en compte 
Limitation par ADLS : si vous atteignez les limites de la bande passante fournie par ADLS, vous commencerez à voir des échecs de tâche. Vous pouvez identifier le problème en consultant les erreurs de limitation dans les journaux des tâches.  Vous pouvez réduire le parallélisme en augmentant la taille de conteneur.  Si vous avez besoin de davantage de simultanéité pour votre travail, veuillez nous contacter.   
Pour vérifier si une limitation est appliquée, vous devez activer la journalisation du débogage côté client. Voici comment procéder :

1. Modifiez les éléments suivants dans Ambari > Storm > Config > Avancé storm-worker-log4j.  Modifiez &lt;root level="info"&gt; sur &lt;root level=”debug”&gt;.  Redémarrez tous les nœuds/le service pour que la configuration prenne effet.
2. Surveillez les journaux de topologie Storm sur les nœuds de travail (sous /var/log/storm/worker-artifacts/&lt;NomTopologie&gt;/&lt;port&gt;/worker.log) pour les exceptions de limitation d’ADLS.

## <a name="additional-tuning"></a>Paramétrage supplémentaire
Vous trouverez des réglages de performance supplémentaires pour Storm dans ce [blog](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/).

## <a name="examples-to-run"></a>Exemples à exécuter
Essayez cet exemple disponible sur [github](https://github.com/hdinsight/storm-performance-automation).



<!--HONumber=Jan17_HO2-->



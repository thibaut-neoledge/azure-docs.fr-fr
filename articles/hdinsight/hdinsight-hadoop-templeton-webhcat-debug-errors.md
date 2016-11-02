<properties
 pageTitle="Présentation et résolution des erreurs WebHCat sur HDInsight"
 description="Découvrez quelles sont les erreurs courantes renvoyées par WebHCat sur HDInsight et comment les résoudre."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
 tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="09/27/2016"
 ms.author="larryfr"/>


#<a name="understand-and-resolve-errors-received-from-webhcat-(templeton,)-on-hdinsight"></a>Compréhension et résolution des erreurs reçues à partir de WebHCat (Templeton) sur HDInsight

Lorsque vous utilisez WebHCat (anciennement Templeton) pour travailler avec HDInsight, vous pouvez recevoir des erreurs. Ce document fournit des conseils sur les erreurs courantes, en expliquant pourquoi elles se produisent et que faire pour les résoudre.

##<a name="what-is-webhcat?"></a>Présentation de WebHCat

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) est une API REST pour [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), une couche de gestion du stockage et des tables pour Hadoop. WebHCat est activé par défaut sur les clusters HDInsight et est utilisé par différents outils pour envoyer des tâches, obtenir le statut d’une tâche, etc. sans se connecter au cluster.

##<a name="modifying-configuration"></a>Modification de la configuration

> [AZURE.IMPORTANT] Plusieurs des erreurs répertoriées dans ce document se produisent car une limite maximale configurée a été dépassée. Lorsque l’étape de résolution mentionne que vous pouvez modifier une valeur, vous devez utiliser l’une des méthodes suivantes pour effectuer cette modification :

* Pour les clusters **Windows** : utilisez une action de script pour configurer la valeur lors de la création du cluster. Pour en savoir plus, consultez la rubrique [Développement d’actions de script avec HDInsight](hdinsight-hadoop-script-actions.md).

* Pour les clusters **Linux** : utilisez Ambari (API REST ou web) pour modifier la valeur. Pour en savoir plus, consultez la rubrique [Gestion des clusters HDInsight à l’aide d’Ambari](hdinsight-hadoop-manage-ambari.md)

###<a name="default-configuration"></a>Configuration par défaut

Voici les valeurs de configuration par défaut qui peuvent affecter les performances de WebHCat ou provoquer des erreurs si elles sont dépassées :

| Paramètre | Résultat | Valeur par défaut |
| ------- | ------------ | ------------- |
| [yarn.scheduler.capacity.maximum-applications][maximum-applications] | Nombre maximal de tâches pouvant être actives simultanément (en attente ou en cours d’exécution) | 10 000 |
| [templeton.exec.max-procs][max-procs] | Nombre maximal de demandes pouvant être traitées simultanément | 20 |
| [mapreduce.jobhistory.max-age-ms][max-age-ms] | Nombre de jours pendant lesquels l’historique des tâches est conservé. | 7 jours |

##<a name="too-many-requests"></a>Trop de demandes

**Code d’état HTTP**: 429

| Cause : | Résolution : |
| ----- | ---------- |
| Vous avez dépassé le nombre maximal de demandes simultanées prises en charge par WebHCat par minute (20 par défaut) | Réduisez votre charge de travail pour vérifier que vous n’avez pas dépassé le nombre maximal de demandes simultanées ou pour augmenter la limite de demandes simultanées en modifiant `templeton.exec.max-procs`. Pour en savoir plus, consultez la section [Modification de la configuration](#modifying-configuration) |

##<a name="server-unavailable"></a>Serveur non disponible

**Code d’état HTTP**: 503

| Cause : | Résolution : |
| ---------------- | ------------------- |
| Cette erreur se produit généralement lors du basculement entre le HeadNode principal et secondaire du cluster. | Veuillez patienter deux minutes, puis recommencez l’opération. |

##<a name="bad-request-content:-could-not-find-job"></a>Contenu de demande erroné : impossible de trouver la tâche

**Code d’état HTTP**: 400

| Cause : | Résolution : |
| ---------------- | ------------------- |
| Les informations détaillées de la tâche ont été nettoyées par la tâche de nettoyage de l’historique | La période de conservation par défaut de l’historique des tâches est de 7 jours. Cette durée peut être changée en modifiant le paramètre `mapreduce.jobhistory.max-age-ms`. Pour en savoir plus, consultez la section [Modification de la configuration](#modifying-configuration) |
| La tâche a été arrêtée en raison d’un basculement | Veuillez patienter deux minutes avant de renvoyer la tâche |
| L’ID de cette tâche n’est pas valide | Veuillez vérifier si l’ID de cette tâche est correct |

##<a name="bad-gateway"></a>Passerelle incorrecte

**Code d’état HTTP**: 502

| Cause : | Résolution : |
| ---------------- | ------------------- |
| Le nettoyage de la mémoire interne coïncide avec le processus de WebHCat | Veuillez attendre que le nettoyage de la mémoire se termine ou redémarrez le service WebHCat |
| Le délai d’attente d’une réponse du service ResourceManager a expiré. Cela peut se produire lorsque le nombre d’applications actives dépasse le nombre maximal configuré (10 000 par défaut) | Veuillez attendre que les tâches en cours d’exécution se terminent ou augmentez la limite de tâches simultanées en modifiant `yarn.scheduler.capacity.maximum-applications`. Pour en savoir plus, consultez la section [Modification de la configuration](#modifying-configuration)  |
| Lorsque vous tentez de récupérer toutes les tâches via l’appel [GET /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) lorsque `Fields` est défini sur `*` | Ne récupérez pas *tous* les détails des tâches, utilisez plutôt `jobid` pour récupérer uniquement les détails des tâches dont l’ID est supérieur à un ID de tâche particulier. Ou n’utilisez pas `Fields` |
| Le service WebHCat est arrêté pendant le basculement du HeadNode | Veuillez patienter deux minutes, puis recommencez l’opération |
| Plus de 500 tâches en attente ont été envoyées via WebHCat | Veuillez patienter le temps que les tâches en attente se terminent avant d’envoyer d’autres tâches |

[maximum-applications]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://hive.apache.org/javadocs/hcat-r0.5.0/configuration.html
[max-age-ms]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
 



<!--HONumber=Oct16_HO2-->



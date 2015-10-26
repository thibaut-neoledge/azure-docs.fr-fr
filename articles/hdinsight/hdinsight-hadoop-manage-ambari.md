<properties
   pageTitle="Gérer les clusters HDInsight à l'aide d’Ambari | Microsoft Azure"
   description="Découvrez comment utiliser Ambari pour gérer et surveiller des clusters HDInsight Linux."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/23/2015"
   ms.author="larryfr"/>

#Gestion des clusters HDInsight à l'aide d’Ambari (version préliminaire)

Découvrez comment utiliser Ambari pour gérer et surveiller des clusters Azure HDInsight Linux.

> [AZURE.NOTE]La plupart des informations mentionnées dans cet article s'appliquent uniquement aux clusters HDInsight Linux. Pour les clusters HDInsight Windows, seule la surveillance via l'API REST d'Ambari est disponible. Consultez la page [Surveillance d'Hadoop Windows sur HDInsight à l'aide de l'API Ambari](hdinsight-monitor-use-ambari-api.md).

##<a id="whatis"></a>Présentation d'Ambari

<a href="http://ambari.apache.org" target="_blank">Apache Ambari</a> simplifie la gestion de Hadoop en fournissant une interface utilisateur web conviviale qui peut être utilisée pour approvisionner, gérer et surveiller les clusters Hadoop. Les développeurs peuvent intégrer ces fonctionnalités dans leurs applications à l'aide des <a href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md" target="_blank">API REST Ambari</a>.

Ambari est fourni par défaut avec les clusters HDInsight Linux. Les clusters HDInsight Windows fournissent des fonctionnalités de surveillance via l'API REST d'Ambari.

##Proxy SSH

> [AZURE.NOTE]Bien qu’Ambari pour votre cluster soit accessible directement via Internet, certains liens de l’interface utilisateur web d’Ambari (telle que le JobTracker) ne sont pas exposés sur Internet. Par conséquent, un message d’erreur tel que « Serveur introuvable » s’affiche quand vous tentez d’accéder à ces fonctionnalités, sauf si vous utilisez un tunnel Secure Shell (SSH) pour le trafic web proxy vers le nœud principal du cluster.

Pour des informations sur la création d'un tunnel SSH pour utiliser Ambari, consultez [Utilisation de SSH Tunneling pour accéder à l'interface Web Ambari, ResourceManager, JobHistory, NameNode, Oozie et d'autres interfaces Web](hdinsight-linux-ambari-ssh-tunnel.md).

##Interface utilisateur Web d'Ambari

L'interface utilisateur web d'Ambari est disponible, sur chaque cluster HDInsight Linux que vous créez, à l'adresse **https://&lt;clustername>.azurehdinsight.net**.

Vous êtes invité à vous authentifier à la page à deux reprises : la première fois pour vous authentifier auprès du cluster HDInsight, la seconde, pour vous authentifier auprès d'Ambari.

* **Authentification du cluster** : utilisez le nom d'utilisateur et le mot de passe de l'administrateur du cluster (le nom par défaut est **admin**)

* **Authentification d'Ambari** : la valeur par défaut du nom d'utilisateur et du mot de passe est **admin**.

	> [AZURE.NOTE]Si vous avez modifié le mot de passe de l’utilisateur **admin**, vous devez entrer le nouveau mot de passe.

Lorsque la page s'ouvre, vérifiez la barre située en haut de l'écran. Elle contient les informations et les commandes suivantes :

![ambari-nav](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

* **Logo d’Ambari** : ouvre le tableau de bord, qui peut être utilisé pour surveiller le cluster.

* **Nom du cluster # ops** : affiche le nombre d'opérations Ambari en cours. En sélectionnant le nom du cluster ou **ops #**, une liste des opérations effectuées en arrière-plan s’affiche.

* **# alertes** : avertissements ou alertes critiques, le cas échéant, pour le cluster. Sa sélection fait apparaître une liste d'alertes.

* **Tableau de bord** : affiche le tableau de bord.

* **Services** : informations et paramètres de configuration des services sur le cluster

* **Hôtes** : informations et paramètres de configuration des nœuds sur le cluster

* **Alertes** : journal contenant informations, avertissements et alertes critiques.

* **Administrateur** : pile logicielle/services installés sur le cluster, informations sur le compte de service et sécurité Kerberos.

* **Bouton Administrateur** : gestion d'Ambari, paramètres utilisateur et déconnexion.

##Analyse

###Alertes

Ambari offre de nombreuses alertes, qui auront comme attribut, l'un des états suivants :

* **OK**

* **Avertissement**

* **CRITIQUE**

* **INCONNU**

Les alertes autres que **OK** font apparaître l’entrée **# alerts** en haut de la page pour afficher le nombre d'alertes. La sélection de cette entrée fera apparaître les alertes et leur état.

Les alertes sont organisées en plusieurs groupes par défaut, qui peuvent être consultés depuis la page **Alertes**.

![page d’alertes](./media/hdinsight-hadoop-manage-ambari/alerts.png)

Vous pouvez gérer les groupes à l'aide du menu **Actions**, en sélectionnant **Gérer les groupes d'alerte**. Cela vous permet de modifier les groupes existants ou d'en créer de nouveaux.

![gérer des groupes d'alertes, boîte de dialogue](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

Vous pouvez également créer des notifications d’alerte depuis le menu **Actions**. Cela vous permet de créer des déclencheurs qui envoient des notifications par **E-MAIL** ou **SNMP** lorsque des combinaisons spécifiques alerte/gravité se produisent. Par exemple, vous pouvez envoyer une alerte lorsque l'une des alertes du groupe **YARN par défaut** est définie comme **Critique**.

![créer une alerte, boîte de dialogue](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

###Cluster

L'onglet **Mesures** du tableau de bord contient une série de widgets qui permettent de surveiller facilement l'état de votre cluster d'un seul coup d'œil. Plusieurs widgets, tels que **Utilisation du processeur**, fournissent des informations supplémentaires lorsque vous cliquez dessus.

![tableau de bord avec des mesures](./media/hdinsight-hadoop-manage-ambari/metrics.png)

L'onglet **Cartes thermiques** affiche les paramètres sous forme de cartes thermiques colorées, allant du vert au rouge.

![tableau de bord avec des cartes thermiques](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

Pour obtenir des informations détaillées sur les nœuds au sein du cluster, sélectionnez **Hôtes**, puis sélectionnez le nœud spécifique qui vous intéresse.

![détails de l'hôte](./media/hdinsight-hadoop-manage-ambari/host-details.png)

###Services

La barre latérale du tableau de bord, intitulée **Services**, fournit un aperçu rapide de l'état des services exécutés sur le cluster. Différentes icônes sont utilisées pour indiquer l'état ou les actions qui devraient être effectuées, telles qu'un symbole jaune de recyclage qui vous indique si un service doit être recyclé.

![barre latérale des services](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

La sélection d'un service fait apparaître des informations détaillées sur le service

![informations de résumé du service](./media/hdinsight-hadoop-manage-ambari/service-details.png)

####Liens rapides

Certains services affichent un lien **Liens rapides** en haut de la page. Ils peuvent être utilisés pour accéder à des interfaces utilisateur web spécifiques des services, tels que :

* **Historique des travaux** : historique des travaux MapReduce.

* **Gestionnaire des ressources** : interface utilisateur du gestionnaire des ressources YARN.

* **NameNode** : interface utilisateur NameNode HDFS.

* **Interface utilisateur web Oozie** : interface utilisateur Oozie.

La sélection de l'un de ces liens ouvrira un nouvel onglet dans votre navigateur, qui affichera la page sélectionnée.

> [AZURE.NOTE]La sélection d'un lien **Liens rapides** pour un service quelconque entraîne l'apparition d'une erreur vous indiquant que le serveur est introuvable, à moins que vous n’utilisiez un tunnel SSL pour le trafic proxy web vers le cluster. Cela est lié au fait que les applications Web utilisées pour afficher ces informations ne sont pas exposées sur Internet.
>
> Pour des informations sur l'utilisation d'un tunnel SSH avec HDInsight, consultez [Utilisation de SSH Tunneling pour accéder à l'interface Web Ambari, ResourceManager, JobHistory, NameNode, Oozie et d'autres interfaces Web](hdinsight-linux-ambari-ssh-tunnel.md)

##gestion

###Utilisateurs d'Ambari, groupes et autorisations

La gestion des utilisateurs, des groupes et des autorisations n'est pas disponible dans la version préliminaire de HDInsight Linux.

###Hôtes

La page **Hôtes** répertorie tous les hôtes du cluster. Pour gérer des hôtes, procédez comme suit :

![page d'hôtes](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [AZURE.NOTE]L'ajout, la désactivation ou la remise en service d'un hôte ne doivent pas être utilisés avec des clusters HDInsight.

1. Sélectionnez le ou les hôtes que vous souhaitez gérer.

2. Utilisez le menu **Actions** pour sélectionner l'action que vous souhaitez effectuer :

	* **Démarrer tous les composants** : démarre tous les composants sur l'hôte.

	* **Arrêter tous les composants** : arrête tous les composants sur l'hôte.

	* **Redémarrer tous les composants** : redémarre tous les composants sur l’hôte.

	* **Activer le mode Maintenance** : supprime les alertes de l'hôte. Cette option doit être activée si vous effectuez des actions qui généreront des alertes, tel le redémarrage d'un service sur lequel s'appuient des services en cours d'exécution.

	* **Désactiver le mode Maintenance** : rétablit les alertes normales de l'hôte.

	* **Arrêter** : arrête DataNode ou NodeManagers sur l'hôte.

	* **Démarrer** : démarre DataNode ou NodeManagers sur l'hôte.

	* **Redémarrer** : arrête et redémarre DataNode ou NodeManagers sur l'hôte.

	* **Désactiver** : supprime un hôte du cluster.

		> [AZURE.NOTE]N'utilisez pas cette action sur les clusters HDInsight.

	* **Réactiver** : ajoute un hôte préalablement désactivé au cluster.

		> [AZURE.NOTE]N'utilisez pas cette action sur les clusters HDInsight.

###<a id="service"></a>Services

Depuis la page **Tableau de bord** ou **Services**, utilisez le bouton **Actions** situé en bas de la liste des services pour arrêter et redémarrer tous les services.

![actions de service](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

> [AZURE.WARNING]Bien que __Ajouter un service__ soit répertorié dans ce menu, il ne doit pas être utilisé pour ajouter des services au cluster HDInsight. Les nouveaux services doivent être ajoutés à l'aide d'une action de script lors de l’approvisionnement du cluster. Pour plus d'informations sur l'utilisation des actions de script, consultez l'article [Personnaliser des clusters HDInsight à l'aide d'actions de script](hdinsight-hadoop-customize-cluster-linux.md).


Bien que le bouton **Actions** permette de redémarrer tous les services, vous souhaitez souvent démarrer, arrêter ou redémarrer un service spécifique. Pour effectuer des actions sur un service individuel, procédez comme suit :

1. Depuis la page **Tableau de bord** ou **Services**, sélectionnez un service.

2. En haut de l'onglet **Résumé**, utilisez le bouton **Actions de service** et sélectionnez l'action que vous souhaitez effectuer. Ceci redémarrera le service sur tous les nœuds.

	![action de service](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

	> [AZURE.NOTE]Le redémarrage de certains services lorsque le cluster fonctionne peut générer des alertes. Pour éviter cela, vous pouvez utiliser le bouton **Actions de service** afin d'activer le **Mode Maintenance** avant de lancer le redémarrage.

3. Lorsqu'une action est sélectionnée, l'entrée **# op** située en haut de la page s’incrémente pour indiquer qu'une opération s'exécute en arrière-plan. Si leur affichage est configuré, une liste des opérations exécutées en arrière-plan apparaît.

	> [AZURE.NOTE]Si vous avez activé **Mode Maintenance** pour le service, n'oubliez pas de le désactiver à l'aide du bouton **Actions de service** une fois l'opération terminée.

Pour configurer un service, procédez comme suit :

1. Depuis la page **Tableau de bord** ou **Services**, sélectionnez un service.

2. Sélectionnez l'onglet **Configurations**. La configuration actuelle apparaîtra. Une liste des configurations précédentes est également affichée.

	![configurations](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. Utilisez les champs affichés pour modifier la configuration, puis sélectionnez **Enregistrer**. Ou sélectionnez une configuration antérieure, puis sélectionnez **Définir comme actuelle** pour rétablir des paramètres utilisés précédemment.

##API REST

Ambari Web utilise une API REST sous-jacente, que vous pouvez exploiter pour créer vos propres outils de gestion et de surveillance. Bien que l'API soit relativement simple d'utilisation, certaines particularités d’Azure méritent d’être connues :

* **Authentification** : le nom d'utilisateur (**admin**, par défaut) et le mot de passe de l'administrateur du cluster doivent être utilisés pour l’authentification au service.

* **Sécurité** : Ambari utilise l'authentification de base. De ce fait, vous devez toujours utiliser HTTP (HTTPS) sécurisé pour communiquer avec l'API.

* **Adresses IP** : les adresses renvoyées aux hôtes au sein d'un cluster ne sont pas accessibles en dehors du cluster, sauf si le cluster est membre d'un réseau virtuel Azure. L'adresse IP est alors accessible aux autres membres du réseau virtuel, mais pas depuis l'extérieur du réseau.

* **Certaines fonctionnalités ne sont pas activées** : certaines fonctionnalités d'Ambari, telles que l'ajout ou la suppression d'hôtes du cluster, ou l'ajout de nouveaux services, sont désactivées, puisqu'il est géré par le service cloud HDInsight. D'autres fonctionnalités ne peuvent pas être pleinement mises en œuvre dans la version préliminaire de HDInsight Linux.

Pour obtenir une référence complète de l'API REST, consultez la page [Référence V1 de l'API d'Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

<!---HONumber=Oct15_HO3-->
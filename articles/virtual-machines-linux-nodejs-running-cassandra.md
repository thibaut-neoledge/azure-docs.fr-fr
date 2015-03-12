<properties 
	pageTitle="Exécution de Cassandra avec Linux sur Azure" 
	description="Explique comment exécuter un cluster Cassandra sur Linux dans des machines virtuelles Azure." 
	services="virtual-machines" 
	documentationCenter="nodejs" 
	authors="hanuk" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/01/2014" 
	ms.author="hanuk"/>





<h1><a id = ""></a>Exécution de Cassandra avec Linux sur Azure et accès au cluster depuis Node.js </h1>
**Auteur :** Hanu Kommalapati

## Sommaire##

- [Vue d'ensemble] []
- [Déploiement dans une seule région] []
- [Test de cluster Cassandra à une seule région] []
- [Déploiement dans plusieurs régions] []
- [Test de cluster Cassandra à plusieurs régions] []
- [Test de cluster Cassandra depuis Node.js] []
- [Conclusion] []

##<a id="overview"> </a>Vue d'ensemble ##
Microsoft Azure est une plateforme de cloud ouverte qui exécute des logiciels Microsoft et non-Microsoft tels que des systèmes d'exploitation, serveurs d'applications, intergiciels de messagerie, ainsi que des bases de données SQL et NoSQL à partir de modèles commerciaux et open source. La création de services résilients sur des clouds publics, y compris Azure, nécessite une planification soignée et une architecture délibérée pour les serveurs d'applications et les niveaux de stockage. L'architecture de stockage distribuée de Cassandra aide naturellement à créer des systèmes hautement disponibles qui offrent une tolérance de panne en cas de défaillance de cluster. Cassandra est une base de données NoSQL à l'échelle du cloud maintenue par Apache Software Foundation à l'adresse cassandra.apache.org ; Cassandra est écrit en Java et s'exécute donc sur Windows, ainsi que sur les plateformes Linux. 

L'objectif de cet article est d'illustrer le déploiement de Cassandra sur Ubuntu en tant que cluster à centre de données unique et multiple exploitant des machines virtuelles Microsoft Azure et des réseaux virtuels. Le déploiement de cluster pour les charges de travail optimisées pour la production est hors de portée de cet article car il nécessite une configuration de nœud à plusieurs disques, une conception de topologie en anneau et une modélisation des données appropriées pour prendre en charge la réplication, une cohérence des données et des exigences de débit et de haute disponibilité. 

Cet article adopte une approche fondamentale pour montrer les opérations nécessaires à la création d'un cluster Cassandra comparé à Docker, Chef ou Puppet, qui peuvent faciliter le déploiement de l'infrastructure.  

##<a id="depmodels"></a>Les modèles de déploiement ##
La mise en réseau Microsoft Azure permet de déployer des clusters privés isolés dont l'accès peut être limité afin de bénéficier d'une sécurité réseau affinée.  Cet article ayant pour but d'illustrer le déploiement de Cassandra à un niveau fondamental, nous ne nous concentrerons pas sur le niveau de cohérence et la conception du stockage optimale pour le débit. Voici la liste des exigences de mise en réseau pour notre cluster hypothétique :

- Les systèmes externes ne peuvent pas accéder à la base de données Cassandra depuis Azure ou en dehors d'Azure
- Le cluster Cassandra doit se trouver derrière un équilibreur de charge pour le trafic Thrift
- Déployez les nœuds Cassandra en deux groupes dans chaque centre de données pour améliorer la disponibilité du cluster 
- Verrouillez le cluster pour que seule la batterie de serveurs d'applications ait accès à la base de données directement
- Aucun point de terminaison de réseau public autre que SSH
- Chaque nœud Cassandra a besoin d'une adresse IP interne fixe

Cassandra peut être déployé dans une seule région Azure ou dans plusieurs régions, selon la nature distribuée de la charge de travail. Le modèle de déploiement dans plusieurs régions peut être exploité pour répondre aux besoins d'utilisateurs finaux plus proches d'un lieu particulier via la même infrastructure Cassandra. La réplication de nœud intégrée de Cassandra assure la synchronisation des écritures à multiples maîtres provenant de plusieurs centres de données et présente une vue cohérente des données aux applications. Le déploiement dans plusieurs régions peut également contribuer à l'atténuation des risques liés aux ruptures de service Azure plus étendues. La topologie de réplication et de cohérence paramétrable de Cassandra permet de répondre à divers besoins des applications en matière d'objectif de point de récupération. 


###<a id="oneregion"> </a>Déploiement dans une seule région ###
Nous allons commencer avec un déploiement dans une seule région, puis nous utiliserons les enseignements que nous aurons tirer afin de créer un modèle à plusieurs régions. Nous utiliserons la mise en réseau virtuel Azure pour créer des sous-réseaux isolés, afin que les exigences de sécurité réseau mentionnées ci-dessus puissent être satisfaites.  Le processus décrit lors de la création du déploiement dans une seule région utilise Ubuntu 14.04 LTS et Cassandra 2.08 ; toutefois, vous pouvez aisément adapter le processus à d'autres variantes de Linux. Voici certaines des caractéristiques du déploiement dans une seule région.  

**Haute disponibilité :** les nœuds Cassandra illustrés dans la Figure 1 sont déployés dans deux groupes à haute disponibilité afin d'être répartis entre plusieurs domaines d'erreur à des fins de haute disponibilité. Les machines virtuelles annotées avec chaque groupe à haute disponibilité sont mappées à deux domaines d'erreur. Microsoft Azure utilise le concept de domaine d'erreur pour gérer les temps d'arrêt non planifiés (par exemple, les défaillances matérielles ou logicielles), alors que le concept de domaine de mise à niveau (correctifs/mises à niveau du système d'exploitation hôte ou invité, mises à niveau des applications) permet de gérer les temps d'arrêt planifiés. Consultez [Récupération d'urgence et haute disponibilité des applications Azure](http://msdn.microsoft.com/library/dn251004.aspx) pour plus d'informations sur le rôle des domaines d'erreur et de mise à niveau dans l'obtention d'une haute disponibilité.

![Single region deployment](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux1.png)

Figure 1 : déploiement dans une seule région

Notez qu'à la date de rédaction de cet article, Azure n'autorise pas le mappage explicite d'un groupe de machines virtuelles à un domaine d'erreur spécifique ; par conséquent, même avec le modèle de déploiement illustré à la Figure 1, il est statistiquement probable que toutes les machines virtuelles puissent être mappées à deux domaines d'erreur au lieu de quatre. 

**Équilibrage de la charge du trafic Thrift :** les bibliothèques clientes Thrift sur le serveur web se connectent au cluster via un équilibreur de charge interne. Cela nécessite d'ajouter l'équilibrage de la charge interne au sous-réseau de " données " (voir Figure 1) dans le contexte de l'hébergement, par le service cloud, du cluster Cassandra. Une fois l'équilibrage de la charge interne défini, chaque nœud nécessite que le point de terminaison à charge équilibrée soit ajouté avec les annotations d'un jeu d'équilibrage de la charge avec le nom d'équilibrage de charge défini précédemment. Consultez [Équilibrage de la charge interne Azure](http://msdn.microsoft.com/library/azure/dn690121.aspx) pour plus de détails.

**Valeurs initiales de cluster :** il est important de sélectionner les nœuds les plus disponibles pour les valeurs initiales, car les nouveaux nœuds communiqueront avec les nœuds de départ pour découvrir la topologie du cluster. Un nœud de chaque groupe à haute disponibilité est désigné comme nœud de départ afin d'éviter tout point de défaillance unique.

**Facteur de réplication et niveau de cohérence :** la haute disponibilité et la durabilité des données intégrées à Cassandra sont caractérisées par le facteur de réplication (RF : nombre de copies de chaque ligne stockée sur le cluster) et le niveau de cohérence (nombre de réplicas à lire/écrire avant de retourner le résultat à l'appelant). Le facteur de réplication est spécifié lors de la création de KEYSPACE (semblable à une base de données relationnelle) alors que le niveau de cohérence est spécifié lors de l'émission de la requête CRUD. Le facteur de réplication est spécifié lors de la création de KEYSPACE, tandis que le niveau de cohérence est spécifié lors de l'exécution de la requête. Consultez la rubrique [Configuration pour la cohérence](http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) dans la documentation de Cassandra pour plus de détails sur la cohérence et pour connaître la formule de calcul du quorum.

Cassandra prend en charge deux types de modèles d'intégrité des données : la cohérence et la cohérence finale ; le facteur de réplication et le niveau de cohérence déterminent ensemble si les données seront cohérentes dès qu'une opération d'écriture sera terminée ou si elles seront finalement cohérentes. Par exemple, si vous spécifiez QUORUM comme niveau de cohérence, les données seront toujours cohérentes, alors que tout niveau de cohérence en dessous du nombre de réplicas qu'il faut écrire pour atteindre le QUORUM (par exemple, UN) entraîne la cohérence finale des données. 

Le cluster à huit nœuds illustré ci-dessus, avec un facteur de réplication de 3 et un niveau de cohérence de lecture/écriture de QUORUM (deux nœuds sont lus ou écrits à des fins de cohérence), peut survivre à la perte théorique d'au plus un nœud par groupe de réplication avant que l'application ne commence à remarquer la défaillance. Cela suppose que tous les espaces clés ont des demandes de lecture/écriture bien équilibrées.  Voici les paramètres que nous utiliserons pour le cluster déployé : 

Configuration de cluster Cassandra à une seule région :

<table>
<tr>

<th>Paramètre de cluster</th><th>Valeur</th><th>Remarques</th></tr>
<tr><td>Nombre de nœuds (N) </td><td>8</td><td>Nombre total de nœuds dans le cluster</td></tr>
<tr><td>Facteur de réplication (RF)</td><td>	3 </td><td>	Nombre de réplicas d'une ligne donnée </td></tr>
<tr><td>Niveau de cohérence (écriture)</td><td>	QUORUM[(RF/2) +1) = 2][Le résultat de la formule est arrondi vers le bas.] </td><td> Écrit au plus deux réplicas avant que la réponse soit envoyée à l'appelant ; le troisième réplica est écrit de façon finalement cohérente. </td></tr>
<tr><td>Niveau de cohérence (lecture)	</td><td>QUORUM[(RF/2) +1 = 2][Le résultat de la formule est arrondi vers le bas.]</td><td>	Lit deux réplicas avant d'envoyer la réponse à l'appelant.</td></tr>
<tr><td>Stratégie de réplication </td><td>	NetworkTopologyStrategy [voir [Réplication des données](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) dans la documentation de Cassandra pour plus d'informations]</td><td>	Comprend la topologie de déploiement et place des réplicas sur les nœuds afin que tous les réplicas ne finissent pas sur le même rack</td></tr>
<tr><td>Snitch	</td><td>GossipingPropertyFileSnitch [voir [Snitches](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) dans la documentation de Cassandra pour plus d'informations]</td><td>	NetworkTopologyStrategy utilise un concept de snitch pour comprendre la topologie. GossipingPropertyFileSnitch procure un meilleur contrôle lors du mappage de chaque nœud au centre de données et au rack. Le cluster utilise ensuite gossip pour propager ces informations. Cela est beaucoup plus simple pour le paramétrage d'adresses IP dynamiques que PropertyFileSnitch </td></tr>
</TABLE>

**Considérations relatives à Azure pour le cluster Cassandra :** les fonctionnalité de machines virtuelles Microsoft Azure utilisent le stockage d'objets blob Azure pour la persistance des disques ; le stockage Azure enregistre trois réplicas de chaque disque pour une durabilité élevée. Cela signifie que chaque ligne de données insérée dans une table Cassandra est déjà stockée dans trois réplicas ; ainsi, la cohérence des données est déjà prise en charge même si le facteur de réplication (RF) est 1. Le principal problème posé par un facteur de réplication de 1 est que l'application subira une interruption de service même en cas de défaillance d'un seul nœud Cassandra. Toutefois, si un nœud est arrêté suite aux problèmes reconnus par Azure Fabric Controller (matériel, défaillances de logiciels système), ce dernier approvisionne un nouveau nœud à la place à avec les mêmes lecteurs de stockage. L'approvisionnement d'un nouveau nœud pour remplacer l'ancien peut prendre quelques minutes. De même, pour les activités de maintenance planifiée telles que les modifications du système d'exploitation invité, les mises à niveau de Cassandra et les modifications d'applications, Azure Fabric Controller effectue des mises à niveau cumulatives des nœuds dans le cluster.  Les mises à niveau cumulatives peuvent également mettre hors connexion plusieurs nœuds à la fois et, par conséquent, le cluster peut subir une interruption de courte durée pour quelques partitions. Toutefois, les données ne seront pas perdues, grâce à la redondance de stockage intégrée à Azure.

Les systèmes déployés sur Azure ne nécessitant pas de haute disponibilité (par exemple environ 99,9 %, ce qui équivaut à 8,76 heures par an, consultez [Haute disponibilité](http://en.wikipedia.org/wiki/High_availability) pour plus d'informations) peuvent être exécutés avec RF=1 et un Niveau de cohérence=UN.  Pour les applications ayant des exigences de haute disponibilité, RF=3 et Niveau de cohérence=QUORUM tolère un temps d'arrêt de l'un des nœuds de l'un des réplicas. RF=1 dans les déploiements traditionnels (par exemple locaux) ne peut pas être utilisé en raison de la perte de données résultant de problèmes tels que les défaillances de disques.   

## Déploiement dans plusieurs régions ##
Le modèle de cohérence et de réplication compatible avec les centres de données Cassandra décrit ci-dessus simplifie le déploiement par défaut dans plusieurs régions sans la nécessité de faire appel à des outils externes. Cela est assez différent des bases de données relationnelles traditionnelles, où la configuration de la mise en miroir de base de données pour les écritures multimaîtres peut être très complexe. Cassandra dans une configuration à plusieurs régions peut aider à implémenter les scénarios d'utilisation suivants : 

**Déploiement basé sur la proximité :** les applications mutualisées, avec un mappage clair entre les utilisateurs clients et les régions, peuvent tirer parti des faibles latences du cluster à plusieurs régions. Par exemple, des systèmes de gestion de formation pour des établissements d'enseignement peuvent déployer un cluster distribué dans les régions Est et Ouest des États-Unis pour servir les campus respectifs pour les transactions et l'analyse. Les données peuvent être localement cohérentes au moment des lectures et des écritures et peuvent être finalement cohérentes entre les deux régions. Il existe d'autres exemples, comme la distribution multimédia ou le commerce électronique et tout ce qui répond aux demandes des bases d'utilisateurs concentrées géographiquement constitue un bon cas d'utilisation pour ce modèle de déploiement.

**Haute disponibilité :** la redondance est un facteur clé dans l'obtention de la haute disponibilité des logiciels et du matériel ; pour plus d'informations, consultez Création de systèmes de cloud fiables sur Microsoft Azure. Sur Microsoft Azure, la seule méthode fiable pour assurer la redondance consiste à déployer un cluster dans plusieurs régions. Vous pouvez déployer les applications en mode actif-actif ou actif-passif et si l'une des régions est défaillante, Azure Traffic Manager peut rediriger le trafic vers la région active. Avec le déploiement dans une seule région, si la disponibilité est de 99,9 %, un déploiement dans deux régions peut atteindre une disponibilité de 99,9999 % calculée par la formule suivante : (1-(1-0.999) * (1-0.999))*100) ; pour plus d'informations, consultez le document ci-dessus.

**Récupération d'urgence :** conçu correctement, un cluster Cassandra à plusieurs régions peut résister aux pannes catastrophiques d'un centre de données. Si une région est défaillante, l'application déployée dans d'autres régions peut répondre aux demandes des utilisateurs. Comme toute autre implémentation de continuité des activités métier, l'application doit pouvoir tolérer certaines pertes de données dues aux données contenues dans le pipeline asynchrone. Toutefois, Cassandra accélère la récupération par rapport aux processus de récupération de bases de données traditionnels. La Figure 2 montre le modèle de déploiement dans plusieurs régions classique avec huit nœuds dans chaque région. Les deux régions sont des images miroirs l'une de l'autre ; les conceptions réelles varient selon le type de charge de travail (par exemple, transactionnelle ou analytique), l'objectif de point de récupération, l'objectif de temps de récupération, la cohérence des données et les exigences de disponibilité.

![Multi region deployment](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux2.png)

Figure 2 : déploiement de Cassandra dans plusieurs régions

### Intégration réseau ###
Les ensembles de machines virtuelles déployés sur des réseaux privés situés dans deux régions communiquent entre eux à l'aide d'un tunnel VPN. Le tunnel VPN connecte deux passerelles logicielles approvisionnées pendant le processus de déploiement réseau. Les deux régions ont une architecture réseau similaire en termes de sous-réseaux " web " et de " données " ; la mise en réseau Azure permet de créer autant de sous-réseaux que nécessaire et d'appliquer des listes de contrôle d'accès selon les besoins de sécurité réseau. Lors de la conception de la topologie de cluster, la latence de communication entre les centres de données et l'impact économique du trafic réseau doivent être pris en considération. 

### Cohérence des données pour le déploiement dans plusieurs centres de données ###
Les déploiements distribués doivent être conscients de l'impact de la topologie de cluster sur le débit et la haute disponibilité. Le facteur de récupération et le niveau de cohérence doivent être sélectionnées de sorte que le quorum ne dépende pas de la disponibilité de tous les centres de données. 
Pour un système qui a besoin d'une cohérence élevée, un LOCAL_QUORUM pour le niveau de cohérence (pour les écritures et lectures) permet de s'assurer que les lectures et écritures locales sont satisfaites à partir des nœuds locaux tandis que les données sont répliquées de manière asynchrone vers les centres de données distants.  Le Tableau 2 récapitule les détails de configuration pour le cluster à plusieurs régions décrit plus loin. 

**Configuration de cluster Cassandra à deux régions**

<table>
<tr><th>Paramètre de cluster </th><th>Valeur	</th><th>Remarques </th></tr>
<tr><td>Nombre de nœuds (N) </td><td>	8 + 8	</td><td> Nombre total de nœuds dans le cluster </td></tr>
<tr><td>Facteur de réplication (RF)</td><td>	3 	</td><td>Nombre de réplicas d'une ligne donnée </td></tr>
<tr><td>Niveau de cohérence (écriture)	</td><td>LOCAL_QUORUM [(sum(RF)/2) +1) = 4] [Le résultat de la formule est arrondi vers le bas]	</td><td>Deux nœuds seront écrits dans le premier centre de données de façon synchrone ; les deux nœuds supplémentaires nécessaires pour le quorum seront écrits de façon asynchrone dans le deuxième centre de données. </td></tr>
<tr><td>Niveau de cohérence (lecture)</td><td>	LOCAL_QUORUM [((RF/2) +1) = 2] [Le résultat de la formule est arrondi vers le bas]	</td><td>Les demandes de lecture sont satisfaites à partir d'une seule région ; deux nœuds sont lus avant que la réponse soit envoyée au client.  </td></tr>
<tr><td>Stratégie de réplication </td><td>	NetworkTopologyStrategy[voir [Réplication des données](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) dans la documentation de Cassandra pour plus d'informations] </td><td>	Comprend la topologie de déploiement et place des réplicas sur les nœuds afin que tous les réplicas ne finissent pas sur le même rack  </td></tr>
<tr><td>Snitch</td><td> GossipingPropertyFileSnitch [voir [Snitches](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) dans la documentation de Cassandra pour plus d'informations] </td><td>NetworkTopologyStrategy utilise un concept de snitch pour comprendre la topologie. GossipingPropertyFileSnitch procure un meilleur contrôle lors du mappage de chaque nœud au centre de données et au rack. Le cluster utilise ensuite gossip pour propager ces informations. Cela est beaucoup plus simple pour le paramétrage d'adresses IP dynamiques que PropertyFileSnitch </td></tr> 
</table> 

##LA CONFIGURATION LOGICIELLE##
Les versions logicielles suivantes sont utilisées lors du déploiement :

<table>
<tr><th>Logiciel</th><th>Source</th><th>Version</th></tr>
<tr><td>JRE	</td><td>[JRE 8](http://www.oracle.com/technetwork/java/javase/downloads/server-jre8-downloads-2133154.html) </td><td>8U5</td></tr>
<tr><td>JNA	</td><td>[JNA](https://github.com/twall/jna) </td><td> 3.2.7</td></tr>
<tr><td>Cassandra</td><td>[Apache Cassandra 2.0.8](http://www.apache.org/dist/cassandra/2.0.8/apache-cassandra-2.0.8-bin.tar.gz)</td><td> 2.0.8</td></tr>
<tr><td>Ubuntu	</td><td>[Portail Microsoft Azure](http://azure.microsoft.com) </td><td>14.04 LTS</td></tr>
</table>

Étant donné que le téléchargement de JRE nécessite l'acceptation manuelle de la licence Oracle, pour simplifier le déploiement, téléchargez tous les logiciels nécessaires sur le bureau en vue de leur chargement ultérieur dans l'image du modèle Ubuntu que nous créerons préalablement au déploiement du cluster. 

Téléchargez les logiciels ci-dessus dans un répertoire de téléchargements connu (par exemple, %TEMP%/downloads sur Windows ou ~/downloads sur Linux ou Mac) sur le bureau local. 

### CRÉATION D'UNE MACHINE VIRTUELLE UBUNTU ###
Lors de cette étape du processus, nous allons créer une image Ubuntu avec les logiciels prérequis pour que l'image puisse être réutilisée pour l'approvisionnement de plusieurs nœuds Cassandra.  
####ÉTAPE 1 : génération de la paire de clés SSH####
Au moment du déploiement, Azure requiert une clé publique X509 encodée PEM ou DER. Générez une paire de clés publiques/privées en suivant les instructions de la rubrique Utilisation de SSH avec Linux sur Azure. Si vous prévoyez d'utiliser putty.exe comme client SSH sur Windows ou Linux, vous devez convertir la clé privée RSA codée PEM au format PPK en utilisant puttygen.exe. Les instructions se trouvent dans la page web ci-dessus. 

####ÉTAPE 2 : création du modèle de machine virtuelle Ubuntu####
Pour créer le modèle de machine virtuelle, connectez-vous au portail azure.microsoft.com et procédez comme suit : Cliquez sur Nouveau, Calculer, Machine virtuelle, À partir de la galerie, Ubuntu, Ubuntu Server 14.04 LTS, puis cliquez sur la flèche droite. Un didacticiel décrivant la création d'une machine virtuelle Linux est disponible à la rubrique Création d'une machine virtuelle exécutant Linux.

Entrez les informations suivantes dans l'écran " Configuration de la machine virtuelle " n°1 : 

<table>
<tr><th>Nom du champ              </td><td>       Valeur du champ               </td><td>         Remarques                </td><tr>
<tr><td>Date de sortie de la version    </td><td> Sélectionnez une date dans la liste déroulante</td><td></td><tr>
<tr><td>Nom de la machine virtuelle    </td><td> cass-template	               </td><td> Nom d'hôte de la machine virtuelle </td><tr>
<tr><td>Niveau	                 </td><td> Standard	                       </td><td> Conservez la valeur par défaut              </td><tr>
<tr><td>Taille	                 </td><td> A1                              </td><td>Sélectionnez la machine virtuelle en fonction des besoins en E/S ; pour cela, conservez la valeur par défaut </td><tr>
<tr><td> Nouveau nom d'utilisateur	         </td><td> localadmin	                   </td><td> Dans Ubuntu 12.xx et versions ultérieures, " admin " est un nom d'utilisateur réservé.</td><tr>
<tr><td> Authentification	     </td><td> Clic sur la case à cocher                 </td><td>Déterminez si vous souhaitez utiliser une clé SSH pour la sécurisation. </td><tr>
<tr><td> Certificat	         </td><td> Nom de fichier du certificat de clé publique </td><td> Utilisez la clé publique générée précédemment</td><tr>
<tr><td> Nouveau mot de passe	</td><td> Mot de passe fort </td><td> </td><tr>
<tr><td> Confirmer le mot de passe	</td><td> Mot de passe fort </td><td></td><tr>
</table>

Entrez les informations suivantes dans l'écran " Configuration de la machine virtuelle " n°2 : 

<table>
<tr><th>Nom du champ             </th><th> Valeur du champ	                   </th><th> Remarques                                 </th></tr>
<tr><td> Service de cloud computing	</td><td> Créer un nouveau service de cloud computing	</td><td>Le service cloud est un conteneur qui calcule des ressources telles que des machines virtuelles</td></tr>
<tr><td> Nom du cloud Service DNS	</td><td>ubuntu-template.cloudapp.net	</td><td>Donnez un nom d'équilibrage de charge non spécifique à la machine.</td></tr>
<tr><td> Région/Groupe d'affinités/Réseau virtuel </td><td>	Ouest des États-Unis	</td><td> Sélectionnez une région à partir de laquelle vos applications Web auront accès au cluster Cassandra.</td></tr>
<tr><td>Compte de stockage </td><td>	Utiliser la valeur par défaut	</td><td>Utilisez le compte de stockage par défaut ou un compte de stockage créé au préalable dans une région particulière</td></tr>
<tr><td>Groupe à haute disponibilité </td><td>	Aucun </td><td>	Laissez cette valeur vide</td></tr>
<tr><td>Points de terminaison	</td><td>Utiliser la valeur par défaut </td><td>	Utilisez la configuration SSH par défaut </td></tr>
</table>

Cliquez sur la flèche droite, conservez les valeurs par défaut dans l'écran n°3 et cliquez sur le bouton " Rechercher " pour terminer le processus d'approvisionnement de machine virtuelle. Après quelques minutes, la machine virtuelle avec le nom " ubuntu-template " doit être à l'état " en cours d'exécution ". 

###INSTALLATION DU LOGICIEL NÉCESSAIRE###
####ÉTAPE 1 : téléchargement de tarballs ####
À l'aide de scp ou pscp, copiez les logiciels téléchargés précédemment dans le répertoire ~/downloads en utilisant le format de commande suivant : 

#####pscp server-jre-8u5-linux-x64.tar.gz localadmin@hk-cas-template.cloudapp.net:/home/localadmin/downloads/server-jre-8u5-linux-x64.tar.gz #####

Répétez la commande ci-dessus pour JRE ainsi que pour les bits Cassandra. 

####ÉTAPE 2 : préparation de la structure de répertoires et extraction des archives####
Connectez-vous à la machine virtuelle, créez la structure de répertoires et extrayez les logiciels en tant que super utilisateur à l'aide de l'interpréteur de commandes de script ci-dessous :

	#!/bin/bash
	CASS_INSTALL_DIR="/opt/cassandra"
	JRE_INSTALL_DIR="/opt/java"
	CASS_DATA_DIR="/var/lib/cassandra"
	CASS_LOG_DIR="/var/log/cassandra"
	DOWNLOADS_DIR="~/downloads"
	JRE_TARBALL="server-jre-8u5-linux-x64.tar.gz"
	CASS_TARBALL="apache-cassandra-2.0.8-bin.tar.gz"
	SVC_USER="localadmin"
	
	RESET_ERROR=1
	MKDIR_ERROR=2
	
	reset_installation ()
	{
	   rm -rf $CASS_INSTALL_DIR 2> /dev/null
	   rm -rf $JRE_INSTALL_DIR 2> /dev/null
	   rm -rf $CASS_DATA_DIR 2> /dev/null
	   rm -rf $CASS_LOG_DIR 2> /dev/null
	}
	make_dir ()
	{
	   if [ -z "$1" ]
	   then
	      echo "make_dir: invalid directory name"
	      exit $MKDIR_ERROR
	   fi
	   
	   if [ -d "$1" ]
	   then
	      echo "make_dir: directory already exists"
	      exit $MKDIR_ERROR
	   fi
	
	   mkdir $1 2>/dev/null
	   if [ $? != 0 ]
	   then
	      echo "directory creation failed"
	      exit $MKDIR_ERROR
	   fi
	}
	
	unzip()
	{
	   if [ $# == 2 ]
	   then
	      tar xzf $1 -C $2
	   else
	      echo "archive error"
	   fi
	   
	}
	
	if [ -n "$1" ]
	then
	   SVC_USER=$1
	fi
	
	reset_installation 
	make_dir $CASS_INSTALL_DIR
	make_dir $JRE_INSTALL_DIR
	make_dir $CASS_DATA_DIR
	make_dir $CASS_LOG_DIR
	
	#unzip JRE and Cassandra 
	unzip $HOME/downloads/$JRE_TARBALL $JRE_INSTALL_DIR
	unzip $HOME/downloads/$CASS_TARBALL $CASS_INSTALL_DIR
	
	#Change the ownership to the service credentials
	
	chown -R $SVC_USER:$GROUP $CASS_DATA_DIR
	chown -R $SVC_USER:$GROUP $CASS_LOG_DIR
	echo "edit /etc/profile to add JRE to the PATH"
	echo "installation is complete"


Si vous collez ce script dans la fenêtre vim, veillez à supprimer le retour chariot ('\r") à l'aide de la commande suivante :

	tr -d '\r' <infile.sh >outfile.sh

####Étape 3 : modification de etc/profile####
Ajoutez le code suivant à la fin : 

	JAVA_HOME=/opt/java/jdk1.8.0_05 
	CASS_HOME= /opt/cassandra/apache-cassandra-2.0.8
	PATH=$PATH:$HOME/bin:$JAVA_HOME/bin:$CASS_HOME/bin
	export JAVA_HOME
	export CASS_HOME
	export PATH

####Étape 4 : installation de JNA pour les systèmes de production####
Utilisez la séquence de commandes suivante : 
La commande suivante installe jna-3.2.7.jar et jna-platform-3.2.7.jar dans le répertoire /usr/share.java
sudo apt-get install libjna-java 

Créez des liens symboliques dans le répertoire $CASS_HOME/lib pour que le script de démarrage Cassandra puisse trouver ces fichiers JAR :

	ln -s /usr/share/java/jna-3.2.7.jar $CASS_HOME/lib/jna.jar

	ln -s /usr/share/java/jna-platrom-3.2.7.jar $CASS_HOME/lib/jna-platform.jar

####Étape 5 : configuration de cassandra.yaml####
Modifiez cassandra.yaml sur chaque machine virtuelle afin de refléter la configuration requise par toutes les machines virtuelles [nous procéderons à quelques ajustements lors de l'approvisionnement réel] : 

<table>
<tr><th>Nom du champ   </th><th> Valeur  </th><th>	Remarques </th></tr>
<tr><td>nom_cluster </td><td>	" CustomerService "	</td><td> Utilisez le nom qui reflète votre déploiement</td></tr> 
<tr><td>listen_address	</td><td>[Laissez cette valeur vide]	</td><td> Supprimez " localhost " </td></tr>
<tr><td>rpc_addres   </td><td>[Laissez cette valeur vide]	</td><td> Supprimez " localhost " </td></tr>
<tr><td>valeurs initiales	</td><td>"10.1.2.4, 10.1.2.6, 10.1.2.8"	</td><td>Liste de toutes les adresses IP qui sont désignées comme valeurs initiales.</td></tr>
<tr><td>endpoint_snitch </td><td> org.apache.cassandra.locator.GossipingPropertyFileSnitch </td><td> Ceci est utilisé par NetworkTopologyStrateg pour la déduction du centre de données et du rack de la machine virtuelle</td></tr>
</table>

####Étape 6 : capture de l'image de machine virtuelle####
Ouvrez une session sur la machine virtuelle à l'aide du nom d'hôte (hk-AC-template.cloudapp.net) et de la clé privée SSH créée précédemment. Consultez Utilisation de SSH avec Linux sur Azure pour plus d'informations sur la façon de se connecter à l'aide de la commande ssh ou putty.exe. 

Exécutez la séquence d'actions suivante pour capturer l'image :
#####1 : annulation du déploiement#####
Utilisez la commande " sudo waagent -deprovision+user " pour supprimer des informations spécifiques à l'instance de machine virtuelle. Pour plus de détails sur le processus de capture d'image, consultez [Capture d'une machine virtuelle Linux à utiliser comme modèle](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/). 

#####2 : arrêt de la machine virtuelle#####
Assurez-vous que la machine virtuelle est sélectionnée et cliquez sur le lien Arrêter dans la barre de commandes inférieure.

#####3 : capture de l'image#####
Assurez-vous que la machine virtuelle est sélectionnée et cliquez sur le lien Capturer dans la barre de commandes inférieure. Dans l'écran suivant, donnez un nom à l'image (par exemple, hk-cas-2-08-ub-14-04-2014071), une description appropriée, puis cliquez sur la coche pour terminer le processus de capture.

Cela prendra quelques secondes et l'image devrait être disponible dans la section Mes images de la galerie d'images. La machine virtuelle source sera automatiquement supprimée une fois l'image capturée correctement. 

##Processus de déploiement dans une seule région##
**Étape 1 : création du réseau virtuel**
Connectez-vous au portail de gestion et créez un réseau virtuel avec les attributs indiqués dans le tableau. Pour connaître les étapes détaillées du processus, consultez [Configuration d'un réseau virtuel de cloud uniquement dans le portail de gestion](http://msdn.microsoft.com/library/azure/dn631643.aspx).      

<table>
<tr><th>Nom d'attribut de machine virtuelle</th><th>Valeur</th><th>Remarques</th></tr>
<tr><td>Nom</td><td>vnet-cass-west-us</td><td></td></tr>	
<tr><td>Région</td><td>Ouest des États-Unis</td><td></td></tr>	
<tr><td>Serveurs DNS	</td><td>Aucun</td><td>Ignorez cet attribut, car nous n'utilisons pas de serveur DNS</td></tr>
<tr><td>Configurer un réseau VPN de point à site</td><td>Aucun</td><td> Ignorez cet attribut</td></tr>
<tr><td>Configurer un réseau VPN de site à site</td><td>Aucun</td><td> Ignorez cet attribut</td></tr>
<tr><td>Espace d'adressage</td><td>10.1.0.0/16</td><td></td></tr>	
<tr><td>Adresse IP de départ</td><td>10.1.0.0</td><td></td></tr>	
<tr><td>CIDR </td><td>/16 (65531)</td><td></td></tr>
</table>

Ajoutez les sous-réseaux suivants : 

<table>
<tr><th>Nom</th><th>Adresse IP de départ</th><th>CIDR</th><th>Remarques</th></tr>
<tr><td>web</td><td>10.1.1.0</td><td>/24 (251)</td><td>Sous-réseau pour la batterie de serveurs</td></tr>
<tr><td>données</td><td>10.1.2.0</td><td>/24 (251)</td><td>Sous-réseau pour les nœuds de base de données</td></tr>
</table>

Les sous-réseaux de données et web peuvent être protégés par l'intermédiaire de groupes de sécurité réseau, mais cet aspect est hors de portée de cet article.  

**Étape 2 : approvisionnement des machines virtuelles** 
À l'aide de l'image créée précédemment, nous allons créer les machines virtuelles suivantes sur le serveur cloud " hk-c-svc-west " et les lier aux sous-réseaux respectifs comme indiqué ci-dessous : 

<table>
<tr><th>Nom de la machine    </th><th>Sous-réseau	</th><th>Adresse IP	</th><th>Groupe à haute disponibilité</th><th>Contrôleur de domaine ou rack</th><th>Initial ?</th></tr>
<tr><td>hk-c1-west-us	</td><td>données	</td><td>10.1.2.4	</td><td>hk-c-aset-1	</td><td>dc =WESTUS rack =rack1 </td><td>Oui</td></tr>
<tr><td>hk-c2-west-us	</td><td>données	</td><td>10.1.2.5	</td><td>hk-c-aset-1	</td><td>dc =WESTUS rack =rack1	</td><td>Non </td></tr>
<tr><td>hk-c3-west-us	</td><td>données	</td><td>10.1.2.6	</td><td>hk-c-aset-1	</td><td>dc =WESTUS rack =rack2	</td><td>Oui</td></tr>
<tr><td>hk-c4-west-us	</td><td>données	</td><td>10.1.2.7	</td><td>hk-c-aset-1	</td><td>dc =WESTUS rack =rack2	</td><td>Non </td></tr>
<tr><td>hk-c5-west-us	</td><td>données	</td><td>10.1.2.8	</td><td>hk-c-aset-2	</td><td>dc =WESTUS rack =rack3	</td><td>Oui</td></tr>
<tr><td>hk-c6-west-us	</td><td>données	</td><td>10.1.2.9	</td><td>hk-c-aset-2	</td><td>dc =WESTUS rack =rack3	</td><td>Non </td></tr>
<tr><td>hk-c7-west-us	</td><td>données	</td><td>10.1.2.10	</td><td>hk-c-aset-2	</td><td>dc =WESTUS rack =rack4	</td><td>Oui</td></tr>
<tr><td>hk-c8-west-us	</td><td>données	</td><td>10.1.2.11	</td><td>hk-c-aset-2	</td><td>dc =WESTUS rack =rack4	</td><td>Non </td></tr>
<tr><td>hk-w1-west-us	</td><td>web	</td><td>10.1.1.4	</td><td>hk-w-aset-1	</td><td>                       </td><td>N/A</td></tr>
<tr><td>hk-w2-west-us	</td><td>web	</td><td>10.1.1.5	</td><td>hk-w-aset-1	</td><td>                       </td><td>N/A</td></tr>
</table>

La création de la liste de machines virtuelles ci-dessus nécessite la procédure suivante :

1.  Création d'un service cloud vide dans une région particulière
2.	Création d'une machine virtuelle à partir de l'image capturée précédemment et association avec le réseau virtuel créé précédemment. Répétez cette procédure pour toutes les machines virtuelles
3.	Ajout d'un équilibreur de charge interne au service cloud et association au sous-réseau de " données "
4.	Pour chaque machine virtuelle créée précédemment, ajout d'un point de terminaison à équilibrage de la charge pour le trafic Thrift via un jeu à équilibrage de la charge connecté à l'équilibreur de charge interne créé précédemment

La procédure ci-dessus peut être exécutée à l'aide du portail de gestion Azure ; utilisez un ordinateur Windows (utilisez une machine virtuelle sur Azure si vous n'avez pas accès à un ordinateur Windows), utilisez le script PowerShell suivant pour approvisionner les huit machines virtuelles automatiquement.

**List1 : script PowerShell pour l'approvisionnement des machines virtuelles**
		
		#Tested with Azure Powershell - November 2014	
		#This powershell script deployes a number of VMs from an existing image inside an Azure region
		#Import your Azure subscription into the current Powershell session before proceeding
		#The process: 1. create Azure Storage account, 2. create virtual network, 3.create the VM template, 2. crate a list of VMs from the template
		
		#fundamental variables - change these to reflect your subscription
		$country="us"; $region="west"; $vnetName = "your_vnet_name";$storageAccount="your_storage_account"
		$numVMs=8;$prefix = "hk-cass";$ilbIP="your_ilb_ip"
		$subscriptionName = "Azure_subscription_name"; 
		$vmSize="ExtraSmall"; $imageName="your_linux_image_name"
		$ilbName="ThriftInternalLB"; $thriftEndPoint="ThriftEndPoint"
		
		#generated variables
		$serviceName = "$prefix-svc-$region-$country"; $azureRegion = "$region $country"
		
		$vmNames = @()
		for ($i=0; $i -lt $numVMs; $i++)
		{
		   $vmNames+=("$prefix-vm"+($i+1) + "-$region-$country" );
		}
		
		#select an Azure subscription already imported into Powershell session
		Select-AzureSubscription -SubscriptionName $subscriptionName -Current
		Set-AzureSubscription -SubscriptionName $subscriptionName -CurrentStorageAccountName $storageAccount
		
		#create an empty cloud service
		New-AzureService -ServiceName $serviceName -Label "hkcass$region" -Location $azureRegion
		Write-Host "Created $serviceName"
		
		$VMList= @()   # stores the list of azure vm configuration objects
		#create the list of VMs
		foreach($vmName in $vmNames)
		{
		   $VMList += New-AzureVMConfig -Name $vmName -InstanceSize ExtraSmall -ImageName $imageName |
		   Add-AzureProvisioningConfig -Linux -LinuxUser "localadmin" -Password "Local123" |
		   Set-AzureSubnet "data"
		}
		
		New-AzureVM -ServiceName $serviceName -VNetName $vnetName -VMs $VMList
		
		#Create internal load balancer
		Add-AzureInternalLoadBalancer -ServiceName $serviceName -InternalLoadBalancerName $ilbName -SubnetName "data" -StaticVNetIPAddress "$ilbIP"
		Write-Host "Created $ilbName"
		#Add add the thrift endpoint to the internal load balancer for all the VMs
		foreach($vmName in $vmNames)
		{
		    Get-AzureVM -ServiceName $serviceName -Name $vmName |
		        Add-AzureEndpoint -Name $thriftEndPoint -LBSetName "ThriftLBSet" -Protocol tcp -LocalPort 9160 -PublicPort 9160 -ProbePort 9160 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ilbName | 
		        Update-AzureVM 
		
		    Write-Host "created $vmName"     
		}

**Étape 3 : configuration de Cassandra sur chaque machine virtuelle**

Connectez-vous à la machine virtuelle et effectuez les tâches suivantes : 

* Modifiez $CASS_HOME/conf/cassandra-rackdc.properties pour spécifier les propriétés du rack et du centre de données :
      
       dc =EASTUS, rack =rack1

* Modifiez cassandra.yaml pour configurer les nœuds de départ comme indiqué ci-dessous :
     
       Valeurs initiales : "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10"

**Étape 4 : démarrage des machines virtuelles et test du cluster**

Connectez-vous à l'un des nœuds (par exemple, hk-c1-west-us) et exécutez la commande suivante pour connaître l'état du cluster : 
       
       nodetool -h 10.1.2.4 -p 7199 status 

Vous devez obtenir un affichage semblable à celui ci-dessous pour un cluster à huit nœuds : 

<table>
<tr><th>Statut</th></th>Adresse	</th><th>Charge	</th><th>Jetons	</th><th>Appartenance </th><th>ID de l'hôte	</th><th>Rack</th></tr>
<tr><th>UN	</td><td>10.1.2.4 	</td><td>87.81 KB	</td><td>256	</td><td>38,0%	</td><td>GUID (supprimé)</td><td>rack1</td></tr>
<tr><th>UN	</td><td>10.1.2.5 	</td><td>41.08 KB	</td><td>256	</td><td>68,9%	</td><td>GUID (supprimé)</td><td>rack1</td></tr>
<tr><th>UN	</td><td>10.1.2.6 	</td><td>55.29 KB	</td><td>256	</td><td>68,8%	</td><td>GUID (supprimé)</td><td>rack2</td></tr>
<tr><th>UN	</td><td>10.1.2.7 	</td><td>55.29 KB	</td><td>256	</td><td>68,8%	</td><td>GUID (supprimé)</td><td>rack2</td></tr>
<tr><th>UN	</td><td>10.1.2.8 	</td><td>55.29 KB	</td><td>256	</td><td>68,8%	</td><td>GUID (supprimé)</td><td>rack3</td></tr>
<tr><th>UN	</td><td>10.1.2.9 	</td><td>55.29 KB	</td><td>256	</td><td>68,8%	</td><td>GUID (supprimé)</td><td>rack3</td></tr>
<tr><th>UN	</td><td>10.1.2.10 	</td><td>55.29 KB	</td><td>256	</td><td>68.8%	</td><td>GUID (supprimé)</td><td>rack4</td></tr>
<tr><th>UN	</td><td>10.1.2.11 	</td><td>55.29 KB	</td><td>256	</td><td>68.8%	</td><td>GUID (supprimé)</td><td>rack4</td></tr>
</table>

##<a id="testone"> </a>Test du cluster à une seule région##
Pour tester le cluster, procédez comme suit :

1.    À l'aide de la commande Powershell Get-AzureInternalLoadbalancer, obtenez l'adresse IP de l'équilibreur de charge interne (par exemple 10.1.2.101). La syntaxe de la commande est la suivante : Get-AzureLoadbalancer -ServiceName "hk-c-svc-west-us" [affiche les détails de l'équilibreur de charge interne, ainsi que son adresse IP]
2.	Connectez-vous à la machine virtuelle de la batterie de serveurs web (par exemple, hk-w1-west-us) à l'aide de Putty ou ssh
3.	Exécutez $CASS_HOME/bin/cqlsh 10.1.2.101 9160 
4.	Utilisez les commandes CQL suivantes pour vérifier si le cluster fonctionne :

		CREATE KEYSPACE customers_ks WITH REPLICATION = { 'class' : 'SimpleStrategy', 'replication_factor' : 3 };	
		USE customers_ks;
		CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
		
		SELECT * FROM Customers;

Vous devez obtenir un affichage semblable à celui-ci :

<table>
  <tr><th> customer_id </th><th> firstname </th><th> lastname </th></tr>
  <tr><td> 1 </td><td> John </td><td> Doe </td></tr>
  <tr><td> 2 </td><td> Jane </td><td> Doe </td></tr>
</table>

Notez que le keyspace créé à l'étape 4 utilise SimpleStrategy avec un replication_factor égal à 3. SimpleStrategy est recommandé pour les déploiements de centre de données unique, tandis que NetworkTopologyStrategy est recommandé pour les déploiements de plusieurs centres de données. Un replication_factor égal à 3 procure une tolérance des échecs de nœuds. 

##<a id="tworegion"> </a>Processus de déploiement de plusieurs régions##
Nous allons tirer parti du déploiement à une seule région que nous venons d'effectuer et répéter la même procédure pour installer la deuxième région. La principale différence entre le déploiement dans une seule région et dans plusieurs régions concerne la configuration du tunnel VPN pour la communication inter-région ; nous allons commencer par l'installation du réseau, puis approvisionner les machines virtuelles et configurer Cassandra. 

###Étape 1 : création du réseau virtuel dans la seconde région###
Connectez-vous au portail de gestion et créez un réseau virtuel avec les attributs indiqués dans le tableau. Pour connaître les étapes détaillées du processus, consultez [Configuration d'un réseau virtuel de cloud uniquement dans le portail de gestion](http://msdn.microsoft.com/library/azure/dn631643.aspx).      

<table>
<tr><th>Nom de l'attribut    </th><th>Valeur	</th><th>Remarques</th></tr>
<tr><td>Nom	</td><td>vnet-cass-east-us</td><td></td></tr>	
<tr><td>Région	</td><td>Est des États-Unis</td><td></td></tr>	
<tr><td>Serveurs DNS		</td><td></td><td>Ignorez cet attribut, car nous n'utilisons pas de serveur DNS</td></tr>
<tr><td>Configurer un réseau VPN de point à site</td><td></td><td>		Ignorez cet attribut</td></tr>
<tr><td>Configurer un réseau VPN de site à site</td><td></td><td>		Ignorez cet attribut</td></tr>
<tr><td>Espace d'adressage	</td><td>10.2.0.0/16</td><td></td></tr>	
<tr><td>Adresse IP de départ	</td><td>10.2.0.0	</td><td></td></tr>
<tr><td>CIDR	</td><td>/16 (65531)</td><td></td></tr>
</table>	

Ajoutez les sous-réseaux suivants : 
<table>
<tr><th>Nom    </th><th>Adresse IP de départ	</th><th>CIDR	</th><th>Remarques</th></tr>
<tr><td>web	</td><td>10.2.1.0	</td><td>/24 (251)	</td><td>Sous-réseau pour la batterie de serveurs</td></tr>
<tr><td>données	</td><td>10.2.2.0	</td><td>/24 (251)	</td><td>Sous-réseau pour les nœuds de base de données</td></tr>
</table>


###Étape 2 : création de réseaux locaux###
Un réseau local dans la mise en réseau virtuel Azure est un espace d'adressage de proxy qui mappe à un site distant, y compris un cloud privé ou une autre région Azure. Cet espace d'adressage de proxy est lié à une passerelle distante pour le routage réseau vers les destinations réseau appropriées. Pour obtenir des instructions sur l'établissement de connexions de réseau virtuel à réseau virtuel, consultez [Configurer une connexion de réseau virtuel à réseau virtuel](http://msdn.microsoft.com/library/azure/dn690122.aspx). 

Créez deux réseaux locaux avec les détails suivants :

<table>
<tr><th>Nom de réseau          </th><th>Adresse de la passerelle VPN	</th><th>Espace d'adressage	</th><th>Remarques</th></tr>
<tr><td>hk-lnet-map-to-east-us</td><td>	23.1.1.1	</td><td>10.2.0.0/16	</td><td>Lors de la création du réseau local, affectez un espace réservé pour l'adresse de passerelle. L'adresse de passerelle réelle est remplie une fois la passerelle créée. Assurez-vous que l'espace d'adressage correspond exactement au réseau virtuel distant approprié ; dans le cas présent, il s'agit du réseau virtuel créé dans la région Est des États-Unis.</td></tr>
<tr><td>hk-lnet-map-to-west-us	</td><td>23.2.2.2	</td><td>10.1.0.0/16	</td><td>Lors de la création du réseau local, affectez un espace réservé pour l'adresse de passerelle. L'adresse de passerelle réelle est remplie une fois la passerelle créée. Assurez-vous que l'espace d'adressage correspond exactement au réseau virtuel distant approprié ; dans le cas présent, il s'agit du réseau virtuel créé dans la région Ouest des États-Unis.</td></tr>
</table>


###Étape 3 : Mappage du réseau " Local " aux réseaux virtuels respectifs###
À partir du portail de gestion de service, sélectionnez chaque réseau virtuel, cliquez sur " Configurer ", cochez la case " Se connecter au réseau local " et sélectionnez les réseaux locaux avec les détails suivants : 

<table>
<tr><th>Réseau virtuel </th><th>Réseau local</th></tr>
<tr><td>hk-vnet-west-us	</td><td>hk-lnet-map-to-east-us</td></tr>
<tr><td>hk-vnet-east-us	</td><td>hk-lnet-map-to-west-us</td></tr>
</table>

###Étape 4 : création des passerelles sur VNET1 et VNET2###
À partir du tableau de bord des deux réseaux virtuels, cliquez sur Créer une passerelle pour déclencher le processus d'approvisionnement de la passerelle VPN. Après quelques minutes, le tableau de bord de chaque réseau virtuel doit afficher l'adresse de passerelle réelle. 
###Étape 5 : mise à jour des réseaux " Local " avec les adresses de " Passerelle " respectives###
Modifiez les deux réseaux locaux pour remplacer l'espace réservé d'adresse IP de passerelle par l'adresse IP réelle des passerelles que vous venez d'approvisionner. Utilisez le mappage suivant : 

<table>
<tr><th>Réseau local    </th><th>Passerelle de réseau virtuel</th></tr>
<tr><td>hk-lnet-map-to-east-us </td><td>Passerelle de hk-vnet-west-us</td></tr>
<tr><td>hk-lnet-map-to-west-us </td><td>Passerelle de hk-vnet-east-us</td></tr>
</table>

###Étape 6 : mise à jour de la clé partagée###
Utilisez le script Powershell suivant pour mettre à jour la clé IPSec de chaque passerelle VPN [utilisez la même clé pour les deux passerelles] : 
Set-AzureVNetGatewayKey -VNetName hk-vnet-east-us -LocalNetworkSiteName hk-lnet-map-to-west-us -SharedKey D9E76BKK
Set-AzureVNetGatewayKey -VNetName hk-vnet-west-us -LocalNetworkSiteName hk-lnet-map-to-east-us -SharedKey D9E76BKK 

###Étape 6 : établissement de la connexion de réseau virtuel à réseau virtuel###
À partir du portail de gestion de service Azure, utilisez le menu " Tableau de bord " des deux réseaux virtuels pour établir la connexion de passerelle à passerelle. Utilisez les éléments de menu " Se connecter " dans la barre d'outils inférieure. Après quelques minutes, le tableau de bord doit afficher les informations de connexion sous forme graphique.

###Étape 7 : création des machines virtuelles dans la région n°2 ###
Créez l'image Ubuntu en suivant les mêmes étapes que celles décrites dans la procédure de déploiement de la région n°1 ou copiez le fichier de disque dur virtuel d'image dans le compte de stockage Azure situé dans la région n°2 et créez l'image. Utilisez cette image et créez la liste suivante de machines virtuelles dans un nouveau service cloud hk-c-svc-east-us : 

<table>
<tr></th>Nom de la machine </th><th>Sous-réseau</th><th>Adresse IP</th><th>Groupe à haute disponibilité</th><th>Contrôleur de domaine ou rack</th><th>Initial ?</th></tr>
<tr><td>hk-c1-east-us	</td><td>données	</td><td>10.2.2.4	</td><td>hk-c-aset-1	</td><td>dc =EASTUS rack =rack1	</td><td>Oui</td></tr>
<tr><td>hk-c2-east-us	</td><td>données	</td><td>10.2.2.5	</td><td>hk-c-aset-1	</td><td>dc =EASTUS rack =rack1	</td><td>Non </td></tr>
<tr><td>hk-c3-east-us	</td><td>données	</td><td>10.2.2.6	</td><td>hk-c-aset-1	</td><td>dc =EASTUS rack =rack2	</td><td>Oui</td></tr>
<tr><td>hk-c5-east-us	</td><td>données	</td><td>10.2.2.8	</td><td>hk-c-aset-2	</td><td>dc =EASTUS rack =rack3	</td><td>Oui</td></tr>
<tr><td>hk-c6-east-us	</td><td>données	</td><td>10.2.2.9	</td><td>hk-c-aset-2	</td><td>dc =EASTUS rack =rack3	</td><td>Non </td></tr>
<tr><td>hk-c7-east-us  </td><td>données	</td><td>10.2.2.10	</td><td>hk-c-aset-2	</td><td>dc =EASTUS rack =rack4	</td><td>Oui</td></tr>
<tr><td>hk-c8-east-us	</td><td>données	</td><td>10.2.2.11	</td><td>hk-c-aset-2	</td><td>dc =EASTUS rack =rack4	</td><td>Non </td></tr>
<tr><td>hk-w1-east-us	</td><td>web	</td><td>10.2.1.4	</td><td>hk-w-aset-1	</td><td>	N/A</td><td>N/A</td></tr>
<tr><td>hk-w2-east-us	</td><td>web	</td><td>10.2.1.5	</td><td>hk-w-aset-1	</td><td>	N/A</td><td>N/A</td></tr>
</table>

Suivez les mêmes instructions que pour la région n°1, mais utilisez l'espace d'adressage 10.2.xxx.xxx. 
###Étape 8 : configuration de Cassandra sur chaque machine virtuelle###
Connectez-vous à la machine virtuelle et effectuez les tâches suivantes : 

1. Modifiez $CASS_HOME/conf/cassandra-rackdc.properties pour spécifier les propriétés du rack et du centre de données au format suivant :
    dc =EASTUS
    rack =rack1
2. Modifiez cassandra.yaml pour configurer les nœuds de départ : 
    Valeurs initiales : "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10,10.2.2.4,10.2.2.6,10.2.2.8,10.2.2.10"
###Étape 9 : démarrage de Cassandra###
Connectez-vous à chaque machine virtuelle et démarrez Cassandra en arrière-plan en exécutant la commande suivante :
$CASS_HOME/bin/cassandra

##<a id="testtwo"> </a>Test du cluster à plusieurs régions##
À ce stade, Cassandra a été déployé sur 16 nœuds, avec huit nœuds dans chaque région Azure. Ces nœuds sont dans le même cluster suite à la configuration des nœuds de départ et du nom de cluster identique. Pour tester le cluster, procédez comme suit :
###Étape 1 : obtention de l'adresse IP de l'équilibreur de charge interne pour les deux régions à l'aide de PowerShell### 
- Get-AzureInternalLoadbalancer -ServiceName "hk-c-svc-west-us"
- Get-AzureInternalLoadbalancer -ServiceName "hk-c-svc-east-us"  

    Notez les adresses IP affichées (par exemple, west - 10.1.2.101, east - 10.2.2.101).

###Étape 2 : exécution de la commande suivante dans la région Ouest après la connexion à hk-w1-west-us###
1.    Exécutez $CASS_HOME/bin/cqlsh 10.1.2.101 9160 
2.	Exécutez les commandes CQL suivantes :

		CREATE KEYSPACE customers_ks
		WITH REPLICATION = { 'class' : 'NetworkToplogyStrategy', 'WESTUS' : 3, 'EASTUS' : 3};
		USE customers_ks;
		CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
		SELECT * FROM Customers;

Vous devez obtenir un affichage semblable à celui-ci :

<table>
<tr><th>customer_id </th><th>firstname</th><th>Lastname</th></tr>
<tr><td>1</td><td>John</td><td>Doe</td></tr>
<tr><td>2</td><td>Jane</td><td>Doe</td></tr>
</table>

###Étape 3 : exécution de la commande suivante dans la région Est après la connexion à hk-w1-east-us:###
1.    Exécutez $CASS_HOME/bin/cqlsh 10.2.2.101 9160 
2.	Exécutez les commandes CQL suivantes :

		USE customers_ks;
		CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
		SELECT * FROM Customers;

Vous devez obtenir le même affichage que pour la région Ouest :

<table>
<tr><th>customer_id </th><th>firstname</th><th>Lastname</th></tr>
<tr><td>1</td><td>John</td><td>Doe</td></tr>
<tr><td>2</td><td>Jane</td><td>Doe</td></tr>
</table>

Exécutez quelques insertions de plus et vérifiez qu'elles sont répliquées vers la partie west-us du cluster. 

##<a id="testnode"> </a>Test de cluster Cassandra depuis Node.js##
À l'aide de l'une des machines virtuelles Linux créées au niveau " web " précédemment, nous allons exécuter un script Node.js simple pour lire les données insérées précédemment 

**Étape 1 : installation de Node.js et du client Cassandra**

1. Installez Node.js et npm
2. Installez le package de nœud " cassandra-client " à l'aide de npm
3. Exécutez le script suivant à l'invite de shell qui affiche la chaîne json des données récupérées : 

		var pooledCon = require('cassandra-client').PooledConnection;
		var ksName = "custsupport_ks";
		var cfName = "customers_cf";
		var hostList = ['internal_loadbalancer_ip:9160'];
		var ksConOptions = { hosts: hostList,
		                     keyspace: ksName, use_bigints: false };
		
		function createKeyspace(callback){
		   var cql = 'CREATE KEYSPACE ' + ksName + ' WITH strategy_class=SimpleStrategy AND strategy_options:replication_factor=1';
		   var sysConOptions = { hosts: hostList,  
		                         keyspace: 'system', use_bigints: false };
		   var con = new pooledCon(sysConOptions);
		   con.execute(cql,[],function(err) {
		   if (err) {
		     console.log("Failed to create Keyspace: " + ksName);
		     console.log(err);
		   }
		   else {
		     console.log("Created Keyspace: " + ksName);
		     callback(ksConOptions, populateCustomerData);
		   }
		   });
		   con.shutdown();
		} 
		
		function createColumnFamily(ksConOptions, callback){
		  var params = ['customers_cf','custid','varint','custname',
		                'text','custaddress','text'];
		  var cql = 'CREATE COLUMNFAMILY ? (? ? PRIMARY KEY,? ?, ? ?)';
		var con =  new pooledCon(ksConOptions);
		  con.execute(cql,params,function(err) {
		      if (err) {
		         console.log("Failed to create column family: " + params[0]);
		         console.log(err);
		      }
		      else {
		         console.log("Created column family: " + params[0]);
		         callback();
		      }
		  });
		  con.shutdown();
		} 
		
		//populate Data
		function populateCustomerData() {
		   var params = ['John','Infinity Dr, TX', 1];
		   updateCustomer(ksConOptions,params);
		
		   params = ['Tom','Fermat Ln, WA', 2];
		   updateCustomer(ksConOptions,params);
		}
		
		//update will also insert the record if none exists
		function updateCustomer(ksConOptions,params)
		{
		  var cql = 'UPDATE customers_cf SET custname=?,custaddress=? where custid=?';
		  var con = new pooledCon(ksConOptions);
		  con.execute(cql,params,function(err) {
		      if (err) console.log(err);
		      else console.log("Inserted customer : " + params[0]);
		  });
		  con.shutdown();
		}
		
		//read the two rows inserted above
		function readCustomer(ksConOptions)
		{
		  var cql = 'SELECT * FROM customers_cf WHERE custid IN (1,2)';
		  var con = new pooledCon(ksConOptions);
		  con.execute(cql,[],function(err,rows) {
		      if (err) 
		         console.log(err);
		      else 
		         for (var i=0; i<rows.length; i++)
		            console.log(JSON.stringify(rows[i]));
		    });
		   con.shutdown();
		}
		
		//exectue the code
		createKeyspace(createColumnFamily);
		readCustomer(ksConOptions)


##<a id="conclusion"> </a>Conclusion##
Microsoft Azure est une plateforme flexible qui autorise l'exécution de logiciels Microsoft et open source comme illustré dans cet exercice. Les clusters Cassandra hautement disponibles peuvent être déployés dans un seul centre de données grâce à la répartition des nœuds de cluster sur plusieurs domaines d'erreur. Les clusters Cassandra peuvent également être déployés dans plusieurs régions Azure géographiquement distantes afin de protéger les systèmes contre les sinistres. Ensemble, Azure et Cassandra permettent de créer des services cloud hautement évolutifs, hautement disponibles et récupérables après sinistre.  

[Vue d'ensemble]: #overview
[Déploiement dans une seule région]: #oneregion
[Test de cluster Cassandra à une seule région]: #testone
[Déploiement dans plusieurs régions]: #tworegion
[Test de cluster Cassandra à plusieurs régions]: #testtwo
[Test de cluster Cassandra depuis Node.js]: #testnode
[Conclusion]: #conclusion

##Références##
- [http://cassandra.apache.org](http://cassandra.apache.org)
- [http://www.datastax.com](http://www.datastax.com) 
- [http://www.nodejs.org](http://www.nodejs.org) 



<!--HONumber=42-->

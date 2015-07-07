<properties
	pageTitle="Planification de capacité pour un réplica Hyper-V"
	description="Cet article explique comment utiliser l’outil Capacity Planner pour Azure Site Recovery et inclut des ressources pour la planification de capacités pour la réplication Hyper-V."
	services="site-recovery"
	documentationCenter="na"
	authors="csilauraa"
	manager="jwhit"
	editor="tysonn" />
<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="06/01/2015"
	ms.author="lauraa" />

# Planification de capacité pour un réplica Hyper-V

Azure Site Recovery utilise Réplica Hyper-V pour la réplication entre deux sites VMM locaux ou entre un serveur VMM local et le stockage Azure. Cet article contient des instructions détaillées concernant l’utilisation de Capacity Planner pour l’outil Azure Site Recovery (ASR) - Réplica Hyper-V. L’outil Capacity Planner pour ASR - Réplica Hyper-V aide un administrateur à concevoir le serveur, le stockage et l’infrastructure réseau requis pour déployer correctement Réplica Hyper-V et pour valider la connectivité réseau entre deux sites.

## Conditions requises pour le système
- Système d’exploitation : Windows Server® 2012 ou Windows Server® 2012 R2
- Mémoire : 20 Mo (minimum)
- Processeur : surcharge de 5 pour cent (minimum)
- Espace disque : 5 Mo (minimum)

## Étapes du didacticiel
- Étape 1 : préparer le site principal
- Étape 2 : préparer le site de récupération s’il est local
- Étape 3 : exécuter l’outil de planification de la capacité
- Étape 4 : interpréter les résultats

## Étape 1 : préparer le site principal
1. Répertoriez toutes les machines virtuelles Hyper-V qui devront être activées pour la réplication, ainsi que les clusters/hôtes Hyper-V principaux correspondants.
2. Regroupez les clusters et les hôtes Hyper-V principaux dans les groupes suivants :

  - Serveurs autonomes Windows Server® 2012
  - Clusters Windows Server® 2012
  - Serveurs autonomes Windows Server® 2012 R2
  - Clusters Windows Server® 2012 R2

3. Vous devrez exécuter l’outil de planification de la capacité une fois pour chaque groupe de serveurs autonomes et une fois pour chaque cluster.
4. Activez l’accès à distance à WMI sur tous les clusters et hôtes principaux. Vérifiez que l’ensemble de règles de pare-feu et que les autorisations d’utilisateur sont correctement définis.

        netsh firewall set service RemoteAdmin enable

5. Activez l’analyse des performances sur les hôtes principaux.

  - Ouvrez le **Pare-feu Windows** avec le composant logiciel enfichable **Fonctions de sécurité avancées**, puis activez les règles de trafic entrant suivantes :
    - Accès réseau COM+ (DCOM-IN)
    - Toutes les règles du groupe Gestion à distance du journal des événements

## Étape 2 : préparer le site de récupération
Si vous utilisez Azure comme site de récupération, ou si votre site de récupération local n’est pas encore prêt, vous pouvez ignorer cette section. Cependant, si vous l’ignorez, vous ne pourrez pas mesurer la bande passante disponible entre les deux sites.

1. Choisissez une méthode d’authentification.

	a. Kerberos : à utiliser lorsque les hôtes Hyper-V principaux et de récupération sont dans le même domaine ou dans des domaines mutuellement approuvés.

	b. Certificat : à utiliser lorsque les hôtes Hyper-V principaux et de récupération sont dans des domaines différents. Les certificats peuvent être créés à l’aide de makecert. Pour en savoir plus à propos des étapes nécessaires au déploiement des certificats à l’aide de cette technique, consultez le billet de blog [Authentification par certificat dans Réplica Hyper-V - makecert](http://blogs.technet.com/b/virtualization/archive/2013/04/13/hyper-v-replica-certificate-based-authentication-makecert.aspx).

2. Identifiez un cluster/hôte Hyper-V de récupération **unique** à partir du site de récupération.

	a. Ce cluster/cet hôte de récupération sera utilisé pour répliquer une machine virtuelle factice et pour estimer la bande passante disponible entre les sites principaux et secondaires.

	b. **Recommandé** : utilisez un hôte Hyper-V de récupération unique pour l’exécution des tests.

### Préparation d’un hôte Hyper-V unique en tant que serveur de récupération
1. Dans le Gestionnaire Hyper-V, cliquez sur **Paramètres Hyper-V** dans le volet **Actions**.
2. Dans la boîte de dialogue **Paramètres Hyper-V**, cliquez sur **Configuration de la réplication**.
3. Dans le volet Détails, sélectionnez **Activer cet ordinateur en tant que serveur de réplication**.
4. Dans la section **Authentification et ports**, sélectionnez la méthode d’authentification que vous avez choisie précédemment. Quelle que soit la méthode d’authentification choisie, spécifiez le port à utiliser (les ports par défaut sont 80 pour Kerberos via HTTP et 443 pour l’authentification basée sur certificat via HTTPS).
5. Si vous utilisez l’authentification basée sur les certificats, cliquez sur **Sélectionner le certificat**, puis fournissez les informations de certificat.
6. Dans la section **Autorisation et stockage**, indiquez que vous autorisez **n’importe quel** serveur authentifié (principal) à envoyer des données de réplication vers ce serveur de réplication.
7. Cliquez sur **OK** ou sur **Appliquer**.

![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)

8. Autorisez le fonctionnement de l’écouteur https lorsque la commande suivante est exécutée : netsh http show servicestate.
9. Ouvrez les ports du pare-feu :

        Port 443 (certificae-based authentication):
          Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"


        Port 80 (Kerberos):
          Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"

### Préparation d’un cluster Hyper-V unique en tant que cible de récupération
Si vous avez déjà préparé un hôte Hyper-V autonome pour un serveur de récupération, vous pouvez ignorer cette section.

1. Configurez le service Broker de réplication Hyper-V.

	a. Dans **Gestionnaire de serveur**, ouvrez **Gestionnaire du cluster de basculement**.

	b. Dans le volet gauche, connectez-vous au cluster et, lorsque le nom du cluster est en surbrillance, cliquez sur **Configurer un rôle** dans le volet **Actions**. Cette opération ouvre l’**Assistant Haute disponibilité**.

	c. Dans l’écran **Sélectionner un rôle**, sélectionnez **Service Broker de réplication Hyper-V**.

	d. Terminez la procédure de l’Assistant en fournissant un **Nom NetBIOS** et une **adresse IP** à utiliser comme point de connexion au cluster (appelé point d’accès client). Le **Service Broker de réplication Hyper-V** est configuré, ce qui crée un nom de point d’accès client. Notez le nom de point d’accès client, car vous allez l’utiliser ultérieurement pour configurer le réplica.

	e. Vérifiez que le rôle Service Broker de réplication Hyper-V est correctement mis en ligne et peut basculer entre tous les nœuds du cluster. Pour ce faire, cliquez avec le bouton droit sur le rôle, pointez le curseur sur **Déplacer**, puis cliquez sur **Sélectionner un nœud**. Sélectionner un nœud > **OK**.

	f. Si vous utilisez l’authentification basée sur les certificats, assurez-vous que chaque nœud du cluster et que le point d’accès client du service Broker de réplication Hyper-V disposent tous deux d’un certificat installé.

2. Configurez les paramètres de réplication :

	a. Dans **Gestionnaire de serveur**, ouvrez **Gestionnaire du cluster de basculement**.
	
	b. Dans le volet gauche, connectez-vous au cluster et, lorsque le nom du cluster est en surbrillance, cliquez sur **Rôles** dans la catégorie **Naviguer** du volet **Détails**.
	
	c. Cliquez avec le bouton droit sur le rôle, puis choisissez les **Paramètres de réplication**.
	
	d. Dans le volet **Détails**, sélectionnez **Activer ce cluster en tant que serveur de réplication**.

	e. Dans la section **Authentification et ports**, sélectionnez la méthode d’authentification que vous avez choisie précédemment. Quelle que soit la méthode d’authentification choisie, spécifiez le port à utiliser (les ports par défaut sont 80 pour Kerberos via HTTP et 443 pour l’authentification basée sur certificat via HTTPS).

	f. Si vous utilisez l’authentification basée sur les certificats, cliquez sur **Sélectionner le certificat**, puis fournissez les informations de certificat requises.

	g. Dans la section **Autorisation et stockage**, indiquez si vous autorisez **n’importe quel** serveur authentifié (principal) à envoyer des données de réplication vers ce serveur de réplication ou si vous limitez l’autorisation aux données provenant de serveurs principaux spécifiques. Vous pouvez utiliser des caractères génériques pour limiter l’autorisation aux serveurs d’un domaine spécifique sans avoir à tous les spécifier individuellement (par exemple, *.contoso.com).

	h. Ouvrez les ports de pare-feu sur tous les hôtes de récupération Hyper-V : Port 443 (Certificate auth): Get-ClusterNode | ForEach-Object {Invoke-command -computername \$_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"}}


          Port 80 (Kerberos auth):
              Get-ClusterNode | ForEach-Object {Invoke-command -computername \$_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}


## Étape 3 : exécuter l’outil de planification de la capacité
1. Téléchargez l’outil Capacity Planner.
2. Exécutez l’outil à partir de l’un des serveurs principaux (ou de l’un des nœuds du cluster principal). Cliquez avec le bouton droit sur le fichier .exe, puis sélectionnez **Exécuter en tant qu’administrateur**.
3. Si vous choisissez cette option, acceptez les **Conditions de la licence**, puis cliquez sur **Suivant**.
4. Sélectionnez une **Durée pour la collecte des mesures**. Il est vivement recommandé d’exécuter l’outil pendant les heures de production afin de s’assurer que les données les plus représentatives sont collectées. La durée suggérée pour la collecte des mesures est de 30 minutes. Si vous essayez uniquement de valider la connectivité réseau, vous pouvez choisir une durée d’une minute.

![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)

5. Renseignez les **Détails du site principal**, comme indiqué, puis cliquez sur **Suivant**.

	Pour un hôte autonome, entrez le nom du serveur ou le nom de domaine complet.

	Si votre hôte principal fait partie d’un cluster, vous pouvez entrer le nom de domaine complet de l’un des éléments suivants :

	a. Point d’accès client du service Broker de réplication Hyper-V

	b. Nom du cluster

	c. N’importe quel nœud du cluster

  ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)


6. Entrez les **Détails du site de réplication** (réplication site local vers site local uniquement).

  Si vous souhaitez activer la réplication vers Azure, ou si vous n’avez pas préparé de cluster ou d’hôte Hyper-V en tant que serveur de récupération (comme expliqué à l’étape 2), vous devez ignorer le test impliquant le site de réplication.

  Spécifiez les informations du **Site de réplication**, puis cliquez sur **Suivant**.

i. Pour un hôte autonome, entrez le nom du serveur ou le nom de domaine complet.

ii. Si votre hôte principal fait partie d’un cluster, vous pouvez entrer le nom de domaine complet de l’un des éléments suivants :

a. Point d’accès client du service Broker de réplication Hyper-V

b. Nom du cluster

c. N’importe quel nœud du cluster

   ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)

7. Ignorez les tests impliquant le **Site de réplication étendu**. Ils ne sont pas pris en charge par le logiciel ASR.
8. Sélectionnez les machines virtuelles à analyser. L’outil se connecte aux serveurs autonomes ou du cluster spécifiés dans les **Détails du site principal** et énumère les machines virtuelles en cours d’exécution. Sélectionnez les machines virtuelles et les disques virtuels pour lesquels vous souhaitez que des mesures soient collectées.

Les machines virtuelles suivantes ne seront pas répertoriées ou affichées :

- Machines virtuelles ayant déjà été activées pour la réplication
- Machines virtuelles qui ne sont pas en cours d’exécution

9. Entrez les **Informations réseau** (ces informations s’appliquent uniquement en cas de réplication d’un site local vers un site local et lorsque les détails du site de réplication sont fournis).

Spécifiez les informations réseau demandées, puis cliquez sur **Suivant**.

- Estimation de la bande passante WAN
- Le certificat à utiliser pour l’authentification (facultatif) : si vous envisagez d’utiliser l’authentification basée sur les certificats, vous devez fournir les certificats requis sur cette page.

   ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)

10. Dans la série d’écrans suivants, cliquez sur **Suivant** pour démarrer l’outil.

![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)

11. Lorsque l’outil a terminé, cliquez sur **Afficher le rapport** pour vérifier le résultat.

    Emplacement par défaut du rapport :

    %lecteur_système%\Users\Public\Documents\Capacity Planner

    Emplacement des journaux :

    %lecteur_système%\Users\Public\Documents\CapacityPlanner

## Étape 4 : interpréter les résultats
Vous pouvez ignorer les mesures qui ne sont pas répertoriées dans l’un des deux scénarios suivants, car elles ne sont pas pertinentes pour ce scénario.

### Réplication d’un site local vers un autre site local
  - Impact de la réplication sur la mémoire et le système de calcul de l’hôte principal
  - Impact de la réplication sur l’espace du disque de stockage et les opérations d’E/S par seconde des hôtes principaux et de récupération
  - Bande passante totale requise pour la réplication delta (Mbits/s)
  - Bande passante réseau observée entre l’hôte principal et l’hôte de restauration (Mbits/s)
  - Suggestion quant au nombre idéal de transferts parallèles actifs entre les deux hôtes/clusters

### Réplication d’un site local vers Azure
  - Impact de la réplication sur la mémoire et le système de calcul de l’hôte principal
  - Impact de la réplication sur l’espace du disque de stockage et les opérations d’E/S par seconde de l’hôte principal
  - Bande passante totale requise pour la réplication delta (Mbits/s)

Vous trouverez des instructions plus détaillées sur la page de téléchargement de [Capacity Planner pour Réplica Hyper-V](http://go.microsoft.com/?linkid=9876170).

## Autres ressources
Les ressources suivantes peuvent vous aider à planifier la capacité pour la réplication Hyper-V :

- [Mise à jour : Capacity Planner pour Réplica Hyper-V](http://go.microsoft.com/fwlink/?LinkId=510891) : lisez ce billet de blog pour obtenir une vue d’ensemble de ce nouvel outil.

- [Capacity Planner pour Réplica Hyper-V](http://go.microsoft.com/fwlink/?LinkId=510892) : téléchargez la dernière version de cet outil.

- [Ateliers pratiques guidés](http://go.microsoft.com/fwlink/?LinkId=510893) : assimilez une excellente procédure pas à pas de planification de la capacité avec l’outil du blog TechNet de Keith Mayer.

- [Test des performances et de l’évolutivité - local vers local](https://msdn.microsoft.com/library/azure/dn760892.aspx) : lisez les résultats du test de réplication pour un déploiement entre deux sites locaux.


## Étapes suivantes

Pour commencer à déployer ASR, effectuez les opérations suivantes :

- [Configuration de la protection entre un site local VMM et Azure](site-recovery-vmm-to-azure.md)
- [Configuration de la protection entre un site local Hyper-V et Azure](site-recovery-hyper-v-site-to-azure)
- [Configuration de la protection entre des sites VMM locaux](site-recovery-vmm-to-vmm)
- [Configuration de la protection entre des sites locaux VMM avec SAN](site-recovery-vmm-san)
- [Configuration de la protection avec un seul serveur VMM](site-recovery-single-vmm)
 

<!---HONumber=62-->
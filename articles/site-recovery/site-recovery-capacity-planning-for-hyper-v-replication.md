<properties
	pageTitle="Exécution de l'outil de planification de la capacité Hyper-V pour Site Recovery | Microsoft Azure"
	description="Cet article contient des instructions sur l’utilisation de l’outil de planification de la capacité Hyper-V pour Azure Site Recovery."
	services="site-recovery"
	documentationCenter="na"
	authors="rayne-wiselman"
	manager="jwhit"
	editor="" />
<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="07/12/2016"
	ms.author="raynew" />

# Exécution de l'outil de planification de la capacité Hyper-V pour Site Recovery

Dans le cadre de votre déploiement Azure Site Recovery, vous devrez déterminer les conditions requises de la réplication et vos besoins en bande passante. L’outil de planification de la capacité Hyper-V pour Site Recovery vous aide à déterminer vos besoins en réplication et bande passante pour la réplication des machines virtuelles Hyper-V.


Cet article décrit comment exécuter l'outil de planification de la capacité Hyper-V. Cet outil devrait être utilisé avec les autres outils de planification de la capacité et les informations décrites dans la rubrique consacrée à la [planification de la capacité pour Site Recovery](site-recovery-capacity-planner.md).


## Avant de commencer

Exécutez l’outil sur un serveur ou un nœud de cluster Hyper-V sur votre site principal. Pour exécuter l’outil, voici ce dont ont besoin les serveurs hôtes Hyper-V :

- Système d’exploitation : Windows Server® 2012 ou Windows Server® 2012 R2
- Mémoire : 20 Mo (minimum)
- Processeur : surcharge de 5 pour cent (minimum)
- Espace disque : 5 Mo (minimum)

Avant d’exécuter l’outil, vous devez préparer le site principal. Si vous effectuez une réplication entre deux sites locaux et que vous voulez vérifier la bande passante, vous devez aussi préparer un serveur réplica.


## Étape 1 : préparer le site principal
1. Sur le site principal, dressez la liste de toutes les machines virtuelles Hyper-V que vous voulez répliquer et des hôtes/clusters Hyper-V sur lesquels elles se trouvent. L’outil peut s’exécuter chaque fois pour plusieurs hôtes autonomes ou pour un même cluster, mais pas les deux à la fois. De même, sachant qu’il doit s’exécuter séparément pour chaque système d’exploitation, vous devez regrouper et noter vos serveurs Hyper-V comme suit :

  - Serveurs autonomes Windows Server® 2012
  - Clusters Windows Server® 2012
  - Serveurs autonomes Windows Server® 2012 R2
  - Clusters Windows Server® 2012 R2

3. Activez l’accès à distance à WMI sur tous les hôtes et clusters Hyper-V. Exécutez cette commande sur chaque serveur/cluster pour faire en sorte que les règles de pare-feu et les autorisations utilisateur soient définies :

        netsh firewall set service RemoteAdmin enable

5. Activez l’analyse des performances sur les serveurs et les clusters comme suit :

  - Ouvrez le Pare-feu Windows avec le composant logiciel enfichable **Sécurité avancée** , puis activez les règles de trafic entrant suivantes : **Accès réseau COM + (DCOM-In)** et toutes les règles du **groupe Gestion à distance des journaux des événements**.

## Étape 2 : préparer un serveur réplica (réplication de local en local)

Cette étape n’est pas nécessaire si vous répliquez dans Azure.

Nous vous recommandons de configurer un seul hôte Hyper-V en tant que serveur de récupération de façon à y répliquer une machine virtuelle factice afin de vérifier la bande passante. Vous pouvez passer cette étape, mais dans ce cas, vous ne pourrez pas mesurer la bande passante.

1. Si vous voulez utiliser un nœud de cluster comme réplica, configurez le service Broker de réplication Hyper-V :

	- Dans **Gestionnaire de serveur**, ouvrez **Gestionnaire du cluster de basculement**.
	- Connectez-vous au cluster, mettez en surbrillance le nom du cluster, puis cliquez sur **Actions** > **Configurer un rôle** pour ouvrir l’Assistant Haute disponibilité.
	- Dans **Sélectionner un rôle** cliquez sur **Service Broker de réplication Hyper-V**. Dans l’Assistant, indiquez le **Nom NetBIOS** et l’**Adresse IP** à utiliser comme point de connexion au cluster (appelé point d’accès client). Le **Service Broker de réplication Hyper-V** est alors configuré, ce qui crée un nom de point d’accès client que vous devez noter.
	- Vérifiez que le rôle Service Broker de réplication Hyper-V est correctement mis en ligne et peut basculer entre tous les nœuds du cluster. Pour ce faire, cliquez avec le bouton droit sur le rôle, pointez le curseur sur **Déplacer**, puis cliquez sur **Sélectionner un nœud**. Sélectionner un nœud > **OK**.
	- Si vous utilisez l’authentification basée sur les certificats, vérifiez que le certificat est installé sur chaque nœud du cluster et sur le point d’accès client.
2.  Activez un serveur réplica :

	- Pour un cluster, ouvrez le Gestionnaire du cluster de basculement, connectez-vous au cluster, puis cliquez sur **Rôles** > sélectionner un rôle > **Paramètres de réplication** > **Activer ce cluster en tant que serveur de réplication**. Notez que si vous utilisez un cluster en tant que réplica, le rôle Service Broker de réplication Hyper-V doit aussi être présent sur le cluster du site principal.
	- Pour un serveur autonome, ouvrez le Gestionnaire Hyper-V. Dans le volet **Actions**, cliquez sur **Paramètres Hyper-V** pour le serveur à activer puis, dans **Configuration de la réplication**, cliquez sur **Activer cet ordinateur en tant que serveur de réplication**.
3. Configurez l’authentification :

	- Dans **Authentification et ports**, sélectionnez le mode d’authentification du serveur principal et les ports d’authentification. Si vous utilisez un certificat, cliquez sur **Sélectionner un certificat** pour en sélectionner un. Utilisez Kerberos si les hôtes Hyper-V principal et de récupération sont dans le même domaine ou dans des domaines approuvés. Utilisez des certificats pour des domaines différents ou un déploiement de groupe de travail.
	- Dans la section **Autorisation et stockage**, indiquez que vous autorisez **n’importe quel** serveur authentifié (principal) à envoyer des données de réplication vers ce serveur réplica. Cliquez sur **OK** ou sur **Appliquer**.

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)

	- Exécutez **netsh http show servicestate** pour vérifier que l’écouteur s’exécute pour le protocole/port que vous avez spécifié :
4. Configurez des pare-feu. Pendant l’installation de Hyper-V, des règles de pare-feu sont créées pour autoriser le trafic sur les ports par défaut (HTTPS sur 443, Kerberos sur 80). Activez ces règles comme suit :

		- Certificate authentication on cluster (443): **Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"}}**
		- Kerberos authentication on cluster (80): **Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}**
		- Certificate authentication on standalone server: **Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"**
		- Kerberos authentication on standalone server: **Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"**

## Étape 3 : exécuter l’outil de planification de la capacité

Après avoir préparé votre site principal et configuré un serveur de récupération, vous pouvez exécuter l’outil.

1. [Téléchargez](https://www.microsoft.com/download/details.aspx?id=39057) l’outil à partir du Centre de téléchargement Microsoft.
2. Exécutez l’outil à partir de l’un des serveurs principaux (ou de l’un des nœuds du cluster principal). Cliquez avec le bouton droit sur le fichier .exe, puis sélectionnez **Exécuter en tant qu’administrateur**.
3. Dans **Before you begin**, indiquez la durée de collecte souhaitée des données. Nous vous recommandons d’exécuter l’outil pendant les heures de production pour vous assurer que les données sont représentatives. Si vous espérez seulement valider la connectivité réseau, vous pouvez effectuer une collecte d’une minute seulement.

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)

4. Dans **Primary site details**, spécifiez le nom du serveur ou le nom de domaine complet (FQDN) d’un hôte autonome ou, pour un cluster, spécifiez le FQDN du point d’accès client, le nom du cluster ou n’importe quel nœud du cluster, puis cliquez sur **Next**. L’outil détecte automatiquement le nom du serveur sur lequel il s’exécute. L’outil choisit les machines virtuelles qui peuvent être surveillées pour les serveurs spécifiés.

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)

5. Dans **Replica Site Details**, si vous répliquez dans Azure ou sur un centre de données secondaire et que vous n’avez pas configuré de serveur réplica, sélectionnez **Skip tests involving replica site**. Si vous répliquez sur un centre de données secondaire et que vous avez configuré un réplica, entrez le FQDN du serveur autonome ou le point d’accès client du cluster dans **Server name (or) Hyper-V Replica Broker CAP**.

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)

6. Dans **Extended Replica Details**, activez **Skip the tests involving Extended Replica site**. Ces tests ne sont pas pris en charge par Site Recovery.
7. Dans **Choose VMs to Replicate**, l’outil se connecte au serveur ou au cluster et affiche les machines virtuelles et les disques qui s’exécutent sur le serveur principal, conformément aux paramètres que vous avez spécifiés dans la page **Primary Site Details**. Notez que les machines virtuelles déjà activées pour la réplication ou qui ne sont pas en cours d’exécution ne s’affichent pas. Sélectionnez les machines virtuelles pour lesquelles vous voulez collecter les métriques. Si vous sélectionnez les disques durs virtuels, les données sont aussi collectées automatiquement pour les machines virtuelles.
9. Si vous avez configuré un serveur réplica ou un cluster, dans **Network information**, spécifiez la quantité approximative de bande passante WAN qui, selon vous, sera utilisée entre les sites principal et de réplication, puis sélectionnez les certificats si vous avez configuré une authentification par certificat.

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)

10. Dans **Summary**, vérifiez les paramètres, puis cliquez sur **Next** pour commencer la collecte des métriques. La progression et l’état de l’outil s’affichent dans la page **Calculate Capacity**. Quand l’outil a terminé, cliquez sur **View Report** pour examiner le résultat. Par défaut, les rapports et les journaux sont stockés dans **%systemdrive%\\Utilisateurs\\Public\\Documents\\Capacity Planner**.

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)


## Étape 4 : interpréter les résultats
Les principales métriques sont indiquées ci-dessous. Vous pouvez ignorer celles qui n’y sont pas mentionnées. Elles n’ont aucune utilité pour Site Recovery.

### Réplication d’un site local vers un autre
  - Impact de la réplication sur la mémoire et le système de calcul de l’hôte principal
  - Impact de la réplication sur l’espace du disque de stockage et les opérations d’E/S par seconde des hôtes principaux et de récupération
  - Bande passante totale requise pour la réplication delta (Mbits/s)
  - Bande passante réseau observée entre l’hôte principal et l’hôte de restauration (Mbits/s)
  - Suggestion quant au nombre idéal de transferts parallèles actifs entre les deux hôtes/clusters

### Réplication d’un site local vers Azure
  - Impact de la réplication sur la mémoire et le système de calcul de l’hôte principal
  - Impact de la réplication sur l’espace du disque de stockage et les opérations d’E/S par seconde de l’hôte principal
  - Bande passante totale requise pour la réplication delta (Mbits/s)

## Autres ressources

- Pour plus d’informations sur l’outil, lisez le document qui accompagne le téléchargement de l’outil.
- Consultez une procédure pas à pas de l’outil sur le [blog TechNet](http://blogs.technet.com/b/keithmayer/archive/2014/02/27/guided-hands-on-lab-capacity-planner-for-windows-server-2012-hyper-v-replica.aspx) de Keith Mayer.
- [Obtenez les résultats](site-recovery-performance-and-scaling-testing-on-premises-to-on-premises.md) de nos tests de performances pour la réplication Hyper-V d’un site local vers un autre.



## Étapes suivantes

Une fois que vous avez terminé la planification de la capacité, vous pouvez commencer à déployer Site Recovery :

- [Répliquer des machines virtuelles Hyper-V dans des clouds VMM vers Azure.](site-recovery-vmm-to-azure.md)
- [Répliquer des machines virtuelles Hyper-V (sans VMM) dans Azure](site-recovery-hyper-v-site-to-azure.md)
- [Répliquer des machines virtuelles Hyper-V entre des sites VMM](site-recovery-vmm-to-vmm.md)
- [Répliquer des machines virtuelles Hyper-V entre des sites VMM avec SAN](site-recovery-vmm-san.md)
- [Répliquer des machines virtuelles Hyper-V sur un seul serveur VMM](site-recovery-single-vmm.md)

<!---HONumber=AcomDC_0720_2016-->
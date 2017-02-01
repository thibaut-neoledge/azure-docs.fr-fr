---
title: "Haute disponibilité et récupération d’urgence de SAP HANA sur Azure (grandes instances) | Microsoft Docs"
description: "Établissez la haute disponibilité et planifiez la récupération d’urgence de SAP HANA sur Azure (grandes instances)."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: 047eeddecc33b637a4f0dec3aa591c42ada83706
ms.openlocfilehash: 9e988659dac046067c53765fc1ddc90ee152e27f


---
# <a name="high-availability-and-disaster-recovery-of-sap-hana-on-azure-large-instances"></a>Haute disponibilité et récupération d’urgence de SAP HANA sur Azure (grandes instances)

La haute disponibilité et la récupération d’urgence constituent des aspects fondamentaux de l’exécution de vos serveurs SAP HANA sur Azure (grandes instances) stratégiques. Il est important que vous collaboriez avec SAP, votre intégrateur de systèmes et/ou Microsoft pour concevoir et implémenter efficacement la stratégie de haute disponibilité/récupération d’urgence adaptée à votre situation. Vous devez également prendre en compte plusieurs considérations essentielles propres à votre environnement, telles que l’objectif de point de récupération (RPO) et l’objectif de délai de récupération (RTO).

## <a name="high-availability"></a>Haute disponibilité

Microsoft prend en charge les méthodes de haute disponibilité &quot;prêtes à l’emploi&quot; de SAP HANA, qui sont décrites ci-dessous :

- **Réplication du stockage :** capacité du système de stockage à répliquer lui-même toutes les données à un autre emplacement (dans le même centre de données ou séparément de ce dernier). SAP HANA opère indépendamment de cette méthode.
- **Réplication du système :** réplication de toutes les données de SAP HANA sur un autre système SAP HANA. La réplication des données à intervalles réguliers contribue à minimiser le RTO. SAP HANA prend en charge les modes asynchrone, synchrone en mémoire et synchrone (uniquement recommandé pour les systèmes SAP HANA situés dans le même centre de données ou à moins de 100 km de distance).
- **Basculement automatique de l’hôte :** un ou plusieurs nœuds SAP HANA de secours sont configurés en mode &quot;montée en puissance parallèle&quot;, et SAP HANA &quot;bascule&quot; automatiquement vers un autre nœud lorsque le nœud principal n’est plus disponible.

Pour plus d’informations sur la haute disponibilité de la plateforme SAP HANA, consultez les informations SAP suivantes :

- [SAP HANA High-Availability Whitepaper (Livre blanc sur la haute disponibilité de SAP HANA)](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [SAP HANA Administration Guide (Guide d’administration de SAP HANA)](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [SAP Academy Video on SAP HANA System Replication (Vidéo SAP Academy sur la réplication du système SAP HANA) ](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP Support Note #1999880 – FAQ on SAP HANA System Replication (Note de support SAP n°1999880 – FAQ sur la réplication du système SAP HANA)](https://bcs.wdf.sap.corp/sap/support/notes/1999880)
- [SAP Support Note #2165547 – SAP HANA Backup and Restore within SAP HANA System Replication Environment (Note de support SAP n°2165547 – Sauvegarde et restauration SAP HANA dans l’environnement de réplication du système SAP HANA)](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP Support Note #1984882 – Using SAP HANA System Replication for Hardware Exchange with Minimum/Zero Downtime (Note de support SAP n°1984882 – Utilisation de la réplication du système SAP HANA pour l’échange de matériel avec un temps d’arrêt minime ou nul)](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="disaster-recovery"></a>Récupération d’urgence

SAP HANA sur Azure (grandes instances) est offert dans deux régions Azure d’une région géopolitique. Une connectivité réseau directe a été mise en place entre les deux tampons de grande instance de ces deux régions pour la réplication des données dans le cadre d’une récupération d’urgence. La réplication des données repose sur l’infrastructure de stockage. La réplication des données n’est pas effectuée par défaut ; elle se produit pour les configurations client qui ont demandé une récupération d’urgence.

Toutefois, pour tirer parti de la récupération d’urgence, vous devez commencer par concevoir la connectivité réseau des deux régions Azure en conséquence. Cela signifie que vous avez besoin d’un circuit ExpressRoute assurant la connexion entre l’emplacement local et votre région Azure principale, et d’un autre circuit pour la connexion entre l’emplacement local et votre région de récupération d’urgence. Cette mesure s’applique à une situation dans laquelle une région Azure complète, incluant un emplacement Microsoft Enterprise Edge (MSEE), rencontre un problème.

Votre seconde mesure consiste, dans l’idéal, à connecter à ces deux circuits ExpressRoute tous les réseaux virtuels Azure qui sont connectés au serveur SAP HANA sur Azure (grandes instances) dans l’une des régions. Cette mesure remédie aux situations dans lesquelles un seul des emplacements MSEE qui connecte votre emplacement local à Azure devient hors service.

La figure ci-après illustre la configuration optimale pour la récupération d’urgence :

![Configuration optimale pour la récupération d’urgence](./media/sap-hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)

Une configuration de récupération d’urgence optimale du réseau consiste à utiliser deux circuits ExpressRoute entre l’emplacement local et les deux régions Azure. L’un des circuits aboutit à la région n°1, exécutant une instance de production. Le second circuit ExpressRoute aboutit à la région n°2, exécutant certaines instances HANA hors production (ceci est important dans le cas où la totalité d’une région Azure, comprenant le MSEE et le tampon de grande instance, est déconnectée).

En guise de seconde mesure, tous les réseaux virtuels sont connectés aux différents circuits ExpressRoute assurant la connexion au serveur SAP HANA sur Azure (grandes instances). Vous pouvez contourner l’emplacement d’un MSEE défaillant, ou bien réduire le RPO associé à la récupération d’urgence, comme vous le verrez par la suite.

Une configuration de récupération d’urgence présente les exigences suivantes :

- Les références SKU SAP HANA sur Azure (grandes instances) doivent présenter la même taille que vos références SKU de production commandées et déployées dans la région de récupération d’urgence. Ces instances peuvent servir à exécuter des instances HANA de test, bac à sable (sandbox) ou d’assurance qualité.
- En outre, vous devez commander un profil de récupération d’urgence pour chacune des SKU SAP HANA sur Azure (grandes instances) que vous souhaitez récupérer sur le site de récupération d’urgence, si nécessaire. Cette nécessité entraîne l’allocation de volumes de stockage, qui constituent la cible de la réplication de stockage entre votre région de production et la région de récupération d’urgence.

Une fois tous ces éléments configurés, le démarrage de la réplication du stockage vous incombe. Dans l’infrastructure de stockage utilisée pour SAP HANA sur Azure (grandes instances), la réplication du stockage repose sur les captures instantanées de stockage. Pour démarrer la réplication de récupération d’urgence, vous devez exécuter :

- Une capture instantanée de votre numéro d’unité logique de démarrage, comme décrit précédemment
- Une capture instantanée de vos volumes associés à HANA, comme décrit précédemment

Après l’exécution de ces captures instantanées, un réplica initial des volumes est amorcé sur les volumes associés à votre profil de récupération d’urgence dans la région de récupération d’urgence.

Par la suite, la dernière capture instantanée de stockage est utilisée toutes les heures pour la réplication des données différentielles qui sont apparues sur les volumes de stockage dans l’intervalle.

Le RPO obtenu avec cette configuration est compris entre 60 et 90 minutes. Pour améliorer le RPO dans le cadre d’une récupération d’urgence, vous devez copier les sauvegardes des journaux de transactions HANA de SAP HANA sur Azure (grandes instances) vers l’autre région Azure. Pour effectuer cette opération, procédez comme suit :

- Sauvegardez le journal des transactions HANA aussi souvent que possible dans /hana/log/backup.
- Ensuite, copiez les sauvegardes des journaux de transactions terminées sur une machine virtuelle Azure, qui se trouve dans un réseau virtuel connecté au serveur SAP HANA sur Azure (grandes instances).
- À partir de cette machine virtuelle, copiez les sauvegardes sur une machine virtuelle située dans un réseau virtuel dans la région de récupération d’urgence.
- Conservez les sauvegardes des journaux de transactions dans cette région sur la machine virtuelle.

En cas de sinistre, une fois que le profil de récupération d’urgence a été déployé sur un serveur réel, copiez les sauvegardes des journaux de transactions à partir de la machine virtuelle vers le serveur SAP HANA sur Azure (grandes instances), qui constitue désormais le serveur principal dans la région de récupération d’urgence, puis restaurez ces sauvegardes. Cette opération est possible, car l’état de HANA sur les disques de récupération d’urgence est celui d’une capture instantanée HANA. Il s’agit du point de décalage pour les restaurations ultérieures des sauvegardes des journaux de transactions.

## <a name="backup-and-restore"></a>Sauvegarde et restauration

L’un des aspects les plus importants de l’utilisation de bases de données consiste à s’assurer que ces bases peuvent être protégées de différents types d’événements graves. Ces événements peuvent avoir une multitude de causes, depuis les catastrophes naturelles jusqu’aux simples erreurs des utilisateurs.

La sauvegarde d’une base de données, combinée à une possibilité de restauration à un moment donné quelconque (par exemple avant la suppression de données critiques), permet de récupérer la base de données dans un état aussi proche que possible de celui qu’elle présentait au moment de l’interruption.

Pour optimiser les résultats, vous devez effectuer deux types de sauvegardes :

- Sauvegardes de base de données
- Sauvegardes des journaux de transactions

Pour plus de sûreté, vous pouvez compléter les sauvegardes de base de données complètes exécutées au niveau application par des sauvegardes effectuées à l’aide des captures instantanées de stockage. L’exécution de sauvegardes des journaux se révèle également importante pour la restauration de la base de données (et pour la suppression des transactions déjà validées dans les journaux).

Vous disposez de deux options de sauvegarde et de restauration pour SAP HANA sur Azure (grandes instances) :

- Utilisation de vos propres méthodes de sauvegarde. Après avoir calculé et garanti l’espace disque requis, effectuez des sauvegardes complètes de la base de données et des journaux à l’aide des méthodes de sauvegarde de disque (sur ces disques). Au fil du temps, les sauvegardes sont copiées vers le service Stockage Azure (après la configuration d’un serveur de fichiers reposant sur Azure avec un stockage standard quasiment illimité), ou utilisent Azure Backup Vault ou Azure Cold Storage. Une autre possibilité consiste à recourir à un outil de protection des données tiers, tel que Commvault, pour stocker les sauvegardes une fois ces dernières copiées dans le stockage Azure standard. L’utilisation de vos propres méthodes de sauvegarde peut également se révéler nécessaire pour les données qui doivent être stockées sur de plus longues périodes à des fins de conformité et d’audit.
- Utilisation des fonctionnalités de sauvegarde et de restauration fournies par l’infrastructure sous-jacente de SAP HANA sur Azure (grandes instances). Cette option répond à la nécessité d’effectuer des sauvegardes, et rend les sauvegardes manuelles quasiment obsolètes (sauf dans les cas où des sauvegardes de données sont requises pour des besoins de conformité). Le reste de cette section traite des fonctionnalités de sauvegarde et de restauration offertes avec HANA grandes instances.

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Utilisation des captures instantanées de stockage de SAP HANA sur Azure (grandes instances)

L’infrastructure de stockage qui sous-tend SAP HANA sur Azure (grandes instances) met en œuvre la notion de capture instantanée des volumes de stockage. La prise en charge de la sauvegarde et de la restauration d’un volume spécifique est régie par les règles suivantes :

- Au lieu d’exécuter des sauvegardes de base de données, le système procède à de fréquentes captures instantanées des volumes de stockage.
- La capture instantanée de stockage initie une capture instantanée SAP HANA avant d’exécuter la capture instantanée de stockage proprement dite. Cette capture instantanée HANA constituera le point à partir duquel configurer les restaurations de journaux après la récupération de la capture instantanée de stockage.
- Une fois la capture instantanée de stockage exécutée avec succès, la capture instantanée SAP HANA sera supprimée.
- Les sauvegardes de journaux sont effectuées fréquemment et stockées dans le volume de sauvegarde des journaux ou dans Azure.
- Si la base de données doit être restaurée à un point dans le temps spécifique, une demande est envoyée au support technique de Microsoft Azure (interruption de production) ou à l’équipe de gestion des services SAP HANA sur Azure afin de solliciter une restauration à partir d’une capture instantanée de stockage donnée (par exemple, une restauration planifiée d’un système bac à sable à son état d’origine).
- La capture instantanée SAP HANA incluse dans la capture instantanée de stockage constitue un point de décalage pour l’application des sauvegardes de journaux qui ont été exécutées et stockées après la création de la capture instantanée de stockage.
- Ces sauvegardes de journaux sont effectuées pour restaurer la base de données à un point dans le temps spécifique.

La spécification du nom de la sauvegarde entraînera la capture instantanée des volumes suivants :

- hana/data
- hana/log
- hana/log\_backup (monté en tant que sauvegarde dans hana/log)
- hana/shared

### <a name="storage-snapshot-considerations"></a>Considérations relatives aux captures instantanées de stockage

>[!NOTE] 
>Les captures instantanées de stockage ne sont _pas_ fournies gratuitement, car elles nécessitent l’allocation d’un espace de stockage supplémentaire.

Mécanismes spécifiques des captures instantanées de stockage de SAP HANA sur Azure (grandes instances) :

- Une capture instantanée de stockage spécifique (au moment précis de sa création) consomme très peu de stockage.
- Étant donné que les données changent et que le contenu des fichiers de données SAP HANA évolue sur le volume de stockage, la capture instantanée doit stocker le contenu des blocs d’origine.
- La taille de la capture instantanée de stockage augmente. Plus la durée d’existence de la capture instantanée est longue, plus la capture instantanée de stockage devient volumineuse.
- Plus le volume de base de données SAP HANA fait l’objet de modifications pendant la durée de vie d’une capture instantanée de stockage, plus la capture instantanée de stockage consomme d’espace.

SAP HANA sur Azure (grandes instances) est fourni avec des tailles de volume fixes pour le volume de données et de journaux SAP HANA. L’exécution de captures instantanées de ces volumes consommera une partie de l’espace des volumes. Il vous incombe donc de planifier les captures instantanées de stockage (au sein du processus SAP HANA sur Azure (grandes instances)).

Les sections ci-après fournissent diverses informations concernant l’exécution de ces captures instantanées, y compris des recommandations générales :

- Bien que le matériel puisse prendre en charge 255 captures instantanées par volume, il est vivement recommandé de rester bien en deçà de ce nombre.
- L’espace libre doit être analysé et déterminé avant toute exécution de captures instantanées de stockage.
- Réduisez le nombre de captures instantanées de stockage en fonction de l’espace libre. Il peut être nécessaire de réduire le nombre de captures instantanées conservées ou d’étendre les volumes (vous pouvez commander du stockage supplémentaire par unités de 1 To).
- Lorsque vous exécutez des tâches telles que le déplacement de données dans SAP HANA avec des outils de migration système (en utilisant R3Load ou en restaurant des bases de données SAP HANA à partir de sauvegardes), il est fortement déconseillé d’effectuer la moindre capture instantanée de stockage. (Une migration système étant généralement exécutée sur un nouveau système SAP HANA, aucune capture instantanée n’est donc nécessaire dans ce cas.)
- Dans le cadre de réorganisations plus vastes des tables SAP HANA, les captures instantanées de stockage doivent être évitées dans la mesure du possible.
- En outre, les captures instantanées de stockage constituent une condition préalable à l’utilisation des fonctionnalités de récupération d’urgence de SAP HANA sur Azure (grandes instances).

### <a name="setting-up-storage-snapshots"></a>Configuration des captures instantanées de stockage

1. Assurez-vous que Perl est installé dans le système d’exploitation Linux sur le serveur HANA grandes instances.

2. Modifiez /etc/ssh/ssh\_config en ajoutant la ligne _MACs hmac-sha1_.

3. Créez un compte d’utilisateur de sauvegarde SAP HANA sur le nœud principal de chaque instance SAP HANA que vous exécutez (le cas échéant).

4. Le client SAP HANA HDB doit être installé sur tous les serveurs HANA grandes instances.

5. Sur le premier serveur HANA grandes instances de chaque région, vous devez créer une clé publique pour accéder à l’infrastructure de stockage sous-jacente qui contrôle la création de captures instantanées.

6. Copiez le script azure\_hana\_backup.pl à partir du dossier /scripts à l’emplacement de l’outil **hdbsql** de l’installation SAP HANA.

7. Copiez le fichier HANABackupDetails.txt à partir du dossier /scripts au même emplacement que le script Perl.

8. Modifiez le fichier HANABackupDetails.txt selon vos besoins pour les spécifications client appropriées.

### <a name="detailed-storage-snapshot-steps"></a>Procédure détaillée de configuration des captures instantanées de stockage

**Étape 1 : Installer SAP HANA HDBClient**

Le système d’exploitation Linux installé dans SAP HANA sur Azure (grandes instances) inclut les dossiers et scripts nécessaires pour l’exécution de captures instantanées de stockage SAP HANA à des fins de sauvegarde et de récupération d’urgence. Toutefois, il vous incombe d’installer SAP HANA HDBclient lorsque vous installez SAP HANA.  (Microsoft n’installe ni HDBclient, ni SAP HANA.)

**Étape 2 : Modifier /etc/ssh/ssh\_config**

Modifiez /etc/ssh/ssh\_config en ajoutant la ligne _MACs hmac-sha1_ comme indiqué ci-dessous.
Modifiez la section ci-dessous pour y ajouter **MACs hmac-sha1** :
```
#   RhostsRSAAuthentication no
#   RSAAuthentication yes
#   PasswordAuthentication yes
#   HostbasedAuthentication no
#   GSSAPIAuthentication no
#   GSSAPIDelegateCredentials no
#   GSSAPIKeyExchange no
#   GSSAPITrustDNS no
#   BatchMode no
#   CheckHostIP yes
#   AddressFamily any
#   ConnectTimeout 0
#   StrictHostKeyChecking ask
#   IdentityFile ~/.ssh/identity
#   IdentityFile ~/.ssh/id_rsa
#   IdentityFile ~/.ssh/id_dsa
#   Port 22
Protocol 2
#   Cipher 3des
#   Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-cbc
#   MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd160
MACs hmac-sha1
#   EscapeChar ~
#   Tunnel no
#   TunnelDevice any:any
#   PermitLocalCommand no
#   VisualHostKey no
#   ProxyCommand ssh -q -W %h:%p gateway.example.com
```

**Étape 3 : Créer une clé publique**

Sur le premier serveur SAP HANA sur Azure (grandes instances) de chaque région Azure, vous devez créer une clé publique servant à accéder à l’infrastructure de stockage pour permettre la création de captures instantanées. Cette clé publique garantit qu’aucun mot de passe n’est requis pour la connexion au stockage et que le système ne conserve aucune information d’identification de mot de passe. Dans le système d’exploitation Linux du serveur HANA grandes instances, exécutez la commande ci-après pour générer la clé publique :
```
  ssh-keygen –t dsa –b 1024
```
Le nouvel emplacement est _/root/.ssh/id\_dsa.pub. _N’entrez pas_ de phrase secrète ; dans le cas contraire, vous devrez saisir cette phrase secrète à chaque connexion avant de continuer. À la place, appuyez deux fois sur **Entrée** pour supprimer l’exigence de saisie d’une phrase secrète au moment de la connexion.

Assurez-vous que la clé publique a été corrigée comme attendu en remplaçant les dossiers par /root/.ssh/, puis en exécutant la commande **ls**. Si la clé est présente, la clé publique est copiée par l’exécution de la commande ci-après, puis la totalité de la chaîne est copiée :

![Clé publique copiée par l’exécution de cette commande](./media/sap-hana-overview-high-availability-disaster-recovery/image2-public-key.png)

À ce stade, contactez l’équipe de gestion des services SAP HANA sur Azure et fournissez-lui la clé. L’équipe de gestion utilisera la clé publique pour l’inscrire dans l’infrastructure de stockage sous-jacente.

**Étape 4 : Créer un compte d’utilisateur SAP HANA**

Créez un compte d’utilisateur SAP HANA dans SAP HANA Studio à des fins de sauvegarde. Ce compte doit disposer des privilèges BACKUP ADMIN (ADMINISTRATION DES SAUVEGARDES) et CATALOG READ (LECTURE DU CATALOGUE). Dans cet exemple, le compte d’utilisateur SCADMIN est créé.

![Création d’un utilisateur dans HANA Studio](./media/sap-hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

**Étape 5 : Autoriser le compte d’utilisateur SAP HANA**

Autorisez le compte d’utilisateur SAP HANA (à être utilisé par les scripts sans nécessiter d’autorisation chaque fois que le script est exécuté). La commande SAP HANA **hdbuserstore** permet la création d’une clé utilisateur SAP HANA stockée sur un ou plusieurs nœuds SAP HANA et autorise l’utilisateur à accéder à SAP HANA sans avoir à gérer les mots de passe à partir du processus d’utilisation de scripts décrit ci-dessous.

>[!IMPORTANT]
>Vous devez exécuter la commande ci-après en tant qu’utilisateur _root_. Dans le cas contraire, le script ne fonctionnera pas correctement.

Entrez la commande **hdbuserstore** comme suit :

![Saisie de la commande hdbuserstore ](./media/sap-hana-overview-high-availability-disaster-recovery/image4-hdbuserstore-command.png)

Dans l’exemple ci-après, impliquant l’utilisateur SCADMIN01 et le nom d’hôte lhanad01, la commande est la suivante :
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
Choisissez de gérer la totalité des scripts à partir d’un seul serveur pour les instances HANA de montée en puissance parallèle. Dans cet exemple, la clé SAP HANA de SCADMIN01 doit être modifiée pour chaque hôte de façon à refléter l’hôte qui est associé à la clé (autrement dit, le compte de sauvegarde SAP HANA est modifié avec le nombre d’instances de la base de données HANA **lhanad**). La clé doit disposer de privilèges administratifs sur l’hôte auquel elle est affectée, et l’utilisateur de sauvegarde pour la montée en puissance parallèle doit disposer de droits d’accès à toutes les instances SAP HANA.
```
hdbuserstore set SCADMIN01 lhanad:30015 SCADMIN <password>
hdbuserstore set SCADMIN02 lhanad:30115 SCADMIN <password>
hdbuserstore set SCADMIN03 lhanad:30215 SCADMIN <password>
```

**Étape 6 : Copier des éléments à partir du dossier /scripts**

Copiez les éléments ci-après à partir du dossier /scripts, figurant sur l’image Gold de l’installation, dans le répertoire de travail de **hdbsql**. Pour les installations HANA actuelles, il s’agit du répertoire /hana/shared/D01/exe/linuxx86\_64/hdb.
```
azure\_hana\_backup.pl
testHANAConnection.pl
testStorageSnapshotConnection.pl
removeTestStorageSnapshot.pl
HANABackupCustomerDetails.txt
```
Copiez les éléments ci-après s’ils exécutent une montée en puissance parallèle ou un traitement analytique en ligne (OLAP) :
```
azure\_hana\_backup\_bw.pl
testHANAConnectionBW.pl
testStorageSnapshotConnectionBW.pl
removeTestStorageSnapshotBW.pl
HANABackupCustomerDetailsBW.txt
```
Le fichier HANABackupCustomerDetails.txt est modifiable comme suit pour un déploiement avec montée en puissance. Il s’agit essentiellement du fichier de contrôle et de configuration pour le script qui exécutera les captures instantanées de stockage. Notez que vous devez avoir reçu les informations _Storage Backup Name (Nom de sauvegarde du stockage)_ et _Storage IP Address (Adresse IP de stockage)_ de la part de l’équipe de gestion des services SAP HANA sur Azure au moment où vos instances ont été déployées. Vous _ne pouvez pas_ modifier la séquence, l’ordre ou l’espacement des variables ; dans le cas contraire, le script ne s’exécutera pas correctement.

Dans le cas d’un déploiement avec montée en puissance, le fichier de configuration ressemblerait à ceci :
```
#Provided by Microsoft Service Management
Storage Backup Name: lhanad01backup
Storage IP Address: 10.250.20.21
#Created by customer using hdbuserstore
HANA Backup Name: SCADMIND01
```
Pour une configuration de montée en puissance parallèle, le fichier HANABackupCustomerDetailsBW.txt ressemblerait à ceci :
```
#Provided by Microsoft Service Management
Storage Backup Name: lhanad01backup
Storage IP Address: 10.250.20.21
#Node IP addresses, instance numbers, and HANA backup name
#provided by customer.  HANA backup name created using
#hdbuserstore utility.
Node 1 IP Address: 10.254.15.21
Node 1 HANA instance number: 01
Node 1 HANA Backup Name: SCADMIN01
Node 2 IP Address: 10.254.15.22
Node 2 HANA instance number: 02
Node 2 HANA Backup Name: SCADMIN02
Node 3 IP Address: 10.254.15.23
Node 3 HANA instance number: 03
Node 3 HANA Backup Name: SCADMIN03
Node 4 IP Address: 10.254.15.24
Node 4 HANA instance number: 04
Node 4 HANA Backup Name: SCADMIN04
Node 5 IP Address: 10.254.15.25
Node 5 HANA instance number: 05
Node 5 HANA Backup Name: SCADMIN05
Node 6 IP Address: 10.254.15.26
Node 6 HANA instance number: 06
Node 6 HANA Backup Name: SCADMIN06
Node 7 IP Address: 10.254.15.27
Node 7 HANA instance number: 07
Node 7 HANA Backup Name: SCADMIN07
Node 8 IP Address: 10.254.15.28
Node 8 HANA instance number: 08
Node 8 HANA Backup Name: SCADMIN08
```
Il est important de noter que seuls les détails du nœud 1 sont actuellement utilisés dans le script de captures instantanées de stockage HANA réel. Toutefois, nous vous recommandons de tester l’accès à tous les nœuds HANA ou à partir de ces derniers. De cette façon, si le nœud de sauvegarde principal doit changer, vous vous serez déjà assuré qu’un autre nœud pourra prendre sa place en modifiant les détails du nœud 1.

Pour vérifier l’exactitude des configurations dans le fichier de configuration ou l’adéquation de la connectivité aux instances HANA, vous devez exécuter le premier script (indépendant de la charge de travail SAP) indiqué ci-dessous pour une configuration de montée en puissance, et le second script de la liste pour une configuration de montée en puissance parallèle.
```
testHANAConnection.pl
```
Pour les configurations de montée en puissance parallèle :
```
testHANAConnectionBW.pl
```
Assurez-vous que l’instance HANA principale a accès à tous les serveurs HANA requis. Il n’existe aucun paramètre pour le script, mais le fichier HANABackupCustomerDetails/HANABackupCustomerDetailsBW approprié doit être complété pour que le script s’exécute correctement. Bien qu’il soit impossible pour le script de vérifier l’absence d’erreurs pour chaque instance spécifique, puisque seuls les codes d’erreur de commande d’environnement sont renvoyés, le script fournit quelques commentaires utiles pour vous permettre de procéder à cette vérification.

Pour exécuter le script :
```
 ./testHANAConnection.pl
```
Si le script parvient à obtenir le statut de l’instance HANA, il renvoie un message indiquant que la connexion HANA a réussi.

En outre, il existe un second type de script qui vous permet de vérifier la capacité du serveur de l’instance HANA principale à se connecter au stockage. Vous devez exécuter le script ci-après avant le script azure\_hana\_backup(\_bw).pl. Si un volume ne comporte aucune capture instantanée, il est impossible de faire la différence entre un volume ne contenant aucune capture instantanée et un échec d’obtention des détails de capture instantanée par le protocole ssh.  C’est la raison pour laquelle le script ci-après exécute deux étapes :

- Il vérifie que la console de stockage est accessible.
- Il crée une capture instantanée de test, ou fictive, pour chaque volume par instance HANA.

L’instance HANA est donc incluse en tant qu’argument. Là encore, vous ne pouvez pas vérifier l’absence d’erreurs, car le script se rapporte à la connexion du stockage. Toutefois, le script vous fournit quelques indications utiles si son exécution échoue.

Le script est exécuté sous la forme :
```
 ./testStorageSnapshotConnection.pl <hana instance>
```
Ou
```
./testStorageSnapshotConnectionBW.pl <hana instance>
```
Le script affiche également un message indiquant que vous pouvez vous connecter correctement à votre client de stockage déployé, qui est organisé autour des numéros d’unité logique utilisés par les instances de serveur dont vous êtes propriétaire.

Avant d’exécuter les premières sauvegardes basées sur les captures instantanées de stockage, vous devez exécuter ces scripts pour vous assurer que la configuration est correcte.

Après avoir exécuté ces scripts, vous devez supprimer les captures instantanées en exécutant la commande ci-après :
```
./removeTestStorageSnapshot.pl <hana instance>
```
Ou
```
./removeTestStorageSnapshot.pl <hana instance>
```

**Étape 7 : Effectuer des captures instantanées à la demande**

Effectuez des captures instantanées à la demande (et planifiez des captures instantanées régulières à l’aide de cron) comme décrit ci-dessous, en exécutant le script suivant pour les configurations de montée en puissance :
```
./azure_hana_backup.pl lhanad01 customer 20
```
Ou exécutez le script qui suit pour les configurations de montée en puissance parallèle :
```
./azure_hana_backup_bw.pl lhanad01 customer 20
```
Le script de montée en puissance parallèle effectue certaines vérifications supplémentaires pour contrôler que tous les serveurs HANA sont accessibles, et que toutes les instances HANA renvoient le statut d’instance approprié, avant de procéder à la création de captures instantanées SAP HANA ou de stockage.

Les arguments requis sont les suivants :

- Instance HANA nécessitant une sauvegarde
- Préfixe de capture instantanée pour la capture instantanée de stockage
- Nombre de captures instantanées à conserver pour le préfixe spécifique
```
./azure_hana_backup.pl lhanad01 customer 20
```
L’exécution du script crée la capture instantanée de stockage à l’aide de trois phases distinctes :

- Exécution d’une capture instantanée HANA
- Exécution d’une capture instantanée de stockage
- Suppression de la capture instantanée HANA

Exécutez le script en l’appelant à partir du dossier exécutable HDB dans lequel il a été copié. Il sauvegarde au moins les volumes ci-après, mais également tous les volumes dont le nom comporte le nom de l’instance SAP HANA explicite.
```
hana_data_<hana instance>_prod_t020_vol
hana_log_<hana instance>_prod_t020_vol
hana_log_backup_<hana instance>_prod_t020_vol
hana_shared_<hana instance>_prod_t020_vol
```
La période de rétention est strictement administrée avec le nombre de captures instantanées soumis sous la forme d’un paramètre lors de l’exécution du script (par exemple 20, comme dans l’exemple ci-dessus). Par conséquent, la durée est tributaire de la période d’exécution et du nombre de captures instantanées dans l’appel du script. Si le nombre de captures instantanées conservées dépasse le nombre spécifié en tant que paramètre dans l’appel du script, la plus ancienne capture instantanée de stockage portant cette étiquette (dans notre exemple ci-dessus, _custom_) sera supprimée avant l’exécution d’une nouvelle capture instantanée. Cela signifie que le nombre que vous indiquez comme dernier paramètre de l’appel correspond au nombre que vous pouvez utiliser pour contrôler le nombre de captures instantanées.

>[!NOTE] 
>Dès que vous modifiez l’étiquette, le comptage redémarre.

Vous devez inclure le nom d’instance HANA fourni en tant qu’argument par l’équipe de gestion des services SAP HANA sur Azure, si cette équipe effectue des captures instantanées d’environnements à plusieurs nœuds. Dans les environnements à nœud unique, l’indication du nom de l’unité SAP HANA sur Azure (grandes instances) suffit, mais la spécification du nom de l’instance HANA reste recommandée.

En outre, vous pouvez sauvegarder des volumes\numéros d’unité logique de démarrage à l’aide du même script. Vous devez sauvegarder votre volume de démarrage au moins une fois lorsque vous exécutez HANA pour la première fois, bien que nous recommandions une planification de sauvegarde hebdomadaire ou nocturne pour le démarrage dans cron. Au lieu d’ajouter un nom d’instance SAP HANA, insérez simplement _boot_ en tant qu’argument dans le script, comme ci-dessous :
```
./azure_hana_backup boot customer 20
```
La même stratégie de rétention est également associée au volume de démarrage. Comme indiqué ci-dessus, les captures instantanées à la demande ne doivent être utilisées que pour des cas spéciaux (par exemple, au cours d’une mise à niveau par le biais d’un package d’extension (EHP) SAP ou lorsque vous devez créer une capture instantanée de stockage distincte).

Nous vous encourageons vivement à effectuer des captures instantanées de stockage planifiées à l’aide de cron, et d’utiliser le même script pour la totalité des sauvegardes et des besoins en matière de récupération d’urgence (en modifiant les entrées du script en fonction des différentes heures de sauvegarde demandées). Ces captures sont toutes planifiées différemment dans cron selon leur durée d’exécution : toutes les heures, toutes les 12 heures, une fois par jour ou une fois par semaine (selon les circonstances). La planification cron est destinée à créer des captures instantanées de stockage qui correspondent à l’étiquetage de rétention précédemment décrit pour la sauvegarde hors site à long terme. Le script inclut des commandes demandant la sauvegarde de tous les volumes de production en fonction de la fréquence requise (les données et les fichiers journaux sont sauvegardés toutes les heures, tandis que le volume de démarrage n’est sauvegardé qu’une fois par jour).

Les entrées du script cron ci-après s’exécutent toutes les heures à la dixième minute, toutes les 12 heures à la dixième minute et une fois par jour à la dixième minute. Les travaux cron sont créés de façon à n’effectuer qu’une seule capture instantanée de stockage SAP HANA au cours d’une heure donnée, afin que les sauvegardes effectuées une fois par heure et une fois par jour ne se produisent pas à la même heure (12h10). L’équipe de gestion des services SAP HANA sur Azure vous indiquera l’heure d’exécution recommandée pour vos sauvegardes de façon à optimiser votre processus de création et de réplication de captures instantanées.

La planification cron par défaut dans /etc/crontab est la suivante :
```
10 1-11,13-23 * * * ./azure_hana_backup.pl lhanad01 hourly 66
10 12 * * *  ./azure_hana_backup.pl lhanad01 12hour 14
```
Dans les instructions cron ci-dessus, les volumes HANA (sans le volume de démarrage) font l’objet d’une capture instantanée avec l’étiquette « hourly » (toutes les heures). 66 de ces captures instantanées sont conservées. En outre, 14 captures instantanées avec l’étiquette « 12hour » (toutes les 12 heures) sont conservées, ce qui vous offre la possibilité d’effectuer des captures instantanées une fois par heure pendant trois jours, ainsi que des captures instantanées toutes les 12 heures pendant quatre autres jours, de façon à bénéficier de captures instantanées portant sur une semaine complète. (Les captures instantanées effectuées une fois par heure ne sont pas planifiées pour s’exécuter à chacune des 12 heures. Le système ne crée qu’une seule capture instantanée à la 12e heure, au lieu de produire une capture instantanée une fois par heure et une capture instantanée toutes les 12 heures).

La planification dans cron peut se révéler complexe, car un seul script doit être exécuté à une heure donnée, à moins que le script ne soit décalé de plusieurs minutes. Si vous souhaitez effectuer des sauvegardes quotidiennes pour une rétention à long terme, soit une capture instantanée quotidienne est conservée avec une capture instantanée effectuée toutes les 12 heures (avec une valeur de rétention de sept pour chacune d’elles), soit la capture instantanée effectuée une fois par heure est décalée pour survenir dix minutes plus tard, mais une seule capture instantanée quotidienne est conservée dans le volume de production.
```
10 1-11,13-23 * * * ./azure_hana_backup.pl lhanad01 hourly 66
10 12 * * *  ./azure_hana_backup.pl lhanad01 12hour 7 
10 0 * * * ./azure_hana_backup.pl lhanad01 daily 7
```
Les fréquences répertoriées ici sont de simples exemples. Pour déterminer votre nombre optimal de captures instantanées, utilisez les critères suivants :

- Exigences en matière de RTO pour la récupération jusqu’à une date et heure
- Utilisation de l’espace
- Exigences en matière de RPO et de RTO pour les récupérations d’urgence potentielles
- Exécution de sauvegardes de base de données complètes HANA pour les disques. Chaque fois qu’une sauvegarde de base de données complète sera effectuée pour les disques, ou pour l’interface _backint_, l’exécution de captures instantanées de stockage échouera. Si vous prévoyez d’exécuter des sauvegardes de base de données complètes reposant sur des captures instantanées de stockage, vous devrez vous assurer que l’exécution des captures instantanées de stockage est désactivée pendant cette période.

>[!IMPORTANT]
> L’utilisation de captures instantanées de stockage pour les sauvegardes SAP HANA est uniquement valide lorsqu’elle est effectuée en association avec des sauvegardes de journaux SAP HANA. Ces sauvegardes de journaux doivent pouvoir couvrir les périodes qui s’écoulent entre les captures instantanées de stockage. Si vous vous êtes engagé envers les utilisateurs à assurer une récupération jusqu’à une date et heure remontant à 30 jours, vous devrez disposer des éléments suivants :

- Possibilité d’accès à une capture instantanée de stockage datant de 30 jours.
- Sauvegardes de journaux contiguës sur les 30 derniers jours

Dans la plage des sauvegardes de journaux, créez également une capture instantanée du volume de sauvegarde des journaux. Toutefois, vous devez vous assurer que des sauvegardes de journaux régulières sont effectuées de manière à :

- Disposer des sauvegardes de journaux contiguës nécessaires pour l’exécution de récupérations jusqu’à une date et heure
- Éviter que le volume de journaux SAP HANA manque d’espace

L’une des dernières étapes consiste à planifier les sauvegardes de journaux SAP HANA dans SAP HANA Studio. La destination cible des sauvegardes de journaux SAP HANA est le volume hana/log\_backups spécialement créé avec le point de montage de /hana/log/backups.

![Planifier les sauvegardes de journaux SAP HANA dans SAP HANA Studio](./media/sap-hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Vous pouvez choisir d’effectuer des sauvegardes plus fréquemment qu’une fois toutes les 15 minutes. Certains utilisateurs effectuent même des sauvegardes de journaux toutes les minutes. En revanche, le choix d’un intervalle de _plus_ de 15 minutes est déconseillé.

La dernière étape consiste à créer une sauvegarde basée sur un fichier (après l’installation initiale de SAP HANA) afin de créer une entrée de sauvegarde unique devant figurer dans le catalogue de sauvegarde. Dans le cas contraire, SAP HANA ne lancera pas les sauvegardes de journaux précédemment définies.

![Créer une sauvegarde basée sur un fichier pour créer une entrée de sauvegarde unique](./media/sap-hana-overview-high-availability-disaster-recovery/image6-make-backup.png)

### <a name="monitoring-number-and-size-of-snapshots-on-disk-volume"></a>Surveillance du nombre et de la taille des captures instantanées sur le volume de disque

Sur un volume de stockage spécifique, vous pouvez surveiller le nombre de captures instantanées et l’espace de stockage consommé par ces captures. La commande **ls** n’affiche pas le répertoire des captures instantanées ni les fichiers. En revanche, la commande **du** du système d’exploitation Linux affiche ces informations à l’aide des commandes suivantes :
```
- du –sh .snapshot will provide a total of all snapshots within the snapshot directory.
- du –sh --max-depth=1 will list all snapshots saved in the .snapshot folder and the size of each snapshot.
- du –hc will just provide the total size used by all snapshots.
```
Utilisez ces commandes pour vous assurer que les captures instantanées créées et stockées ne consomment pas la totalité du stockage sur les volumes.

### <a name="reducing-the-number-of-snapshots-on-a-server"></a>Réduction du nombre de captures instantanées sur un serveur

Comme expliqué précédemment, vous pouvez réduire le nombre stocké de captures instantanées portant une certaine étiquette. Les deux derniers paramètres de la commande permettant de lancer une capture instantanée correspondent à une étiquette et au nombre de captures instantanées à conserver.
```
./azure_hana_backup.pl lhanad01 customer 20
```
Dans l’exemple ci-dessus, la capture instantanée présente l’étiquette _customer_, et le nombre de captures instantanées dotées de cette étiquette qui doivent être conservées est de _20_. Afin de faire face au problème de la consommation d’espace disque, vous pouvez vouloir réduire le nombre de captures instantanées stockées. Un moyen simple de réduire la quantité de captures instantanées consiste à exécuter le script en définissant le dernier paramètre sur la valeur 5 :
```
./azure_hana_backup.pl lhanad01 customer 5
```
L’exécution du script défini ci-dessus abaisse le nombre total de captures instantanées à 5, dont la nouvelle capture instantanée de stockage. Notez les points suivants :

- La diminution du nombre de captures instantanées n’est possible que si la dernière capture instantanée créée avant l’exécution du script avec le nombre réduit date de plus d’une heure.
- En outre, le script ne pourra pas supprimer les captures instantanées qui datent de moins d’une heure.

Ces restrictions sont associées à la fonctionnalité de récupération d’urgence facultative offerte.

Supposons que vous ne souhaitiez plus conserver un ensemble de captures instantanées portant ce préfixe. Dans ce cas, vous pouvez exécuter le script en définissant _0_ comme valeur de rétention. Le script supprimera alors toutes les captures instantanées correspondant à ce préfixe, puis son exécution prendra fin. Toutefois, cette opération aura une incidence sur les possibilités de récupération d’urgence.

### <a name="recovering-to-the-latest-hana-snapshot"></a>Récupération jusqu’à la dernière capture instantanée HANA

Si vous devez faire face à une interruption de la production, le processus de récupération à partir d’une capture instantanée de stockage peut être lancé sous la forme d’un incident client auprès de l’équipe de gestion des services SAP HANA sur Azure. Dans certains cas, ce type de scénario constitue un problème de la plus haute urgence si des données sont supprimées d’un système de production et que le seul moyen de les récupérer consiste à effectuer une restauration de la base de données de production.

En revanche, une récupération jusqu’à une date et heure peut ne pas avoir de caractère d’urgence et être planifiée plusieurs jours à l’avance. Vous pouvez planifier cette opération avec l’équipe de gestion des services SAP HANA sur Azure au lieu de signaler un problème hautement prioritaire. Par exemple, vous pourriez envisager de tester une mise à niveau du logiciel SAP en appliquant un nouveau package d’extension, puis vouloir ensuite revenir à une capture instantanée correspondant à l’état du système avant sa mise à niveau par le package d’extension.

Avant d’émettre la demande, vous devez effectuer certaines tâches de préparation, décrites ci-dessous. L’équipe de gestion des services SAP HANA sur Azure sera alors en mesure de traiter la demande et de fournir les volumes restaurés. Il vous incombera ensuite de restaurer la base de données HANA à l’aide des captures instantanées.

>[!NOTE]  
>Les captures d’écran ci-après peuvent varier selon la version de SAP HANA que vous utilisez.

1. Déterminez la capture instantanée à restaurer. Sauf indication contraire, seul le volume hana/data est restauré.

2. Arrêtez l’instance HANA.
![Arrêter l’instance HANA](./media/sap-hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

3. Démontez les volumes de données sur chaque nœud de base de données HANA. La restauration de la capture instantanée échouera si les volumes de données ne sont pas démontés.
![Démonter les volumes de données sur chaque nœud de base de données HANA](./media/sap-hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

4. Ouvrez une demande de support Azure pour demander la restauration d’une capture instantanée spécifique.

    **Lors de la restauration de la capture instantanée de stockage,** l’équipe de gestion des services SAP HANA sur Azure qui restaure les captures instantanées pour les volumes peut vous demander de participer à une téléconférence pour s’assurer qu’aucune donnée n’est perdue.

    **Après la restauration de la capture instantanée de stockage,** l’équipe de gestion des services SAP HANA sur Azure vous informe lorsque la capture instantanée de stockage a été restaurée. 

5. Une fois le processus de restauration terminé, remontez tous les volumes de données.
![Remonter tous les volumes de données](./media/sap-hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

6. Sélectionnez les options de récupération dans SAP HANA Studio, si ces options ne s’affichent pas automatiquement lors de la reconnexion à la base de données HANA par le biais de HANA Studio. L’exemple ci-après illustre une restauration à partir de la dernière capture instantanée HANA. Une capture instantanée de stockage incorpore une capture instantanée HANA. Si vous effectuez la restauration à partir de la dernière capture instantanée de stockage, il doit s’agir de la capture instantanée HANA la plus récente. (Si vous effectuez une restauration à partir de captures instantanées de stockage antérieures, vous devez localiser la capture instantanée HANA correspondante en vous basant sur l’heure de création de la capture instantanée de stockage.) ![Sélectionner les options de récupération dans SAP HANA Studio](./media/sap-hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

7. Sélectionnez le type de récupération en activant l’option **Recover the database to a specific data backup or storage snapshot (Récupérer la base de données jusqu’à une sauvegarde de données ou une capture instantanée de stockage spécifiques)**.
![Sélectionner le type de récupération en activant l’option de récupération de la base de données jusqu’à une sauvegarde de données ou une capture instantanée de stockage spécifiques](./media/sap-hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

8. Sélectionnez l’option **Specify backup without catalog (Spécifier une sauvegarde sans catalogue)**.
![Sélectionner l’option de spécification d’une sauvegarde sans catalogue](./media/sap-hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

9. Sélectionnez **Destination Type (Type de destination) : Snapshot (Capture instantanée)**.
![Choisir une destination de type capture instantanée](./media/sap-hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

10. Cliquez sur **Finish (Terminer)** pour démarrer le processus de récupération.
![Achever la procédure pour lancer le processus de récupération](./media/sap-hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

11. La base de données HANA est restaurée et récupérée à partir de la capture instantanée HANA figurant dans la capture instantanée de stockage.
![Base de données HANA restaurée et récupérée à partir de la capture instantanée HANA figurant dans la capture instantanée de stockage](./media/sap-hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recovering-to-most-recent-state"></a>Récupération jusqu’à l’état le plus récent

Ce processus effectue une restauration à partir d’une capture instantanée HANA incluse dans la capture instantanée de stockage, puis restaure les sauvegardes des journaux de transactions à partir du dernier état que présentait la base de données avant la restauration de la capture instantanée de stockage.

>[!Important] 
>Avant de poursuivre, assurez-vous que vous disposez d’une chaîne complète et contiguë de sauvegardes de journaux des transactions. Sans ces sauvegardes, vous ne serez pas en mesure de revenir à l’état actuel de la base de données.

Suivez les étapes 1 à 6 de la procédure de récupération jusqu’à la dernière capture instantanée HANA décrite ci-dessus.

1. Sélectionnez l’option **Recover the database to its most recent state (Récupérer la base de données jusqu’à son état le plus récent)**.
![Sélectionner l’option de récupération de la base de données jusqu’à son état le plus récent](./media/sap-hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

2. Spécifiez l’emplacement des dernières sauvegardes de journaux HANA. L’emplacement spécifié doit contenir toutes les sauvegardes des journaux de transactions HANA entre la capture instantanée HANA et l’état le plus récent.
![Spécifier l’emplacement des dernières sauvegardes de journaux HANA](./media/sap-hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

3. Choisissez une sauvegarde comme base pour la récupération de la base de données. Dans notre exemple, il s’agit de la capture instantanée HANA qui était incluse dans la capture instantanée de stockage. (Dans la capture d’écran suivante, une seule de ces captures instantanées a été effectuée.) ![Choisir une sauvegarde comme base pour la récupération de la base de données](./media/sap-hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

4. Désélectionnez l’option **Use Delta Backups (Utiliser les sauvegardes différentielles)** si ces sauvegardes n’existent pas entre le moment de la capture instantanée HANA et l’état le plus récent.
![Désélectionner l’option d’utilisation des sauvegardes différentielles si ces sauvegardes n’existent pas entre le moment de la capture instantanée HANA et l’état le plus récent](./media/sap-hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

5. Cliquez sur **Finish (Terminer)** sur l’écran récapitulatif pour démarrer la procédure de restauration.
![Achever la procédure sur l’écran récapitulatif pour lancer la restauration](./media/sap-hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

Pour effectuer une récupération jusqu’à une date et heure situées entre la capture instantanée HANA (incluse dans la capture instantanée de stockage) et une autre date et heures postérieures à cette capture instantanée, procédez comme suit :

- Vérifiez que vous disposez de toutes les sauvegardes des journaux de transactions entre la capture instantanée HANA et l’heure souhaitée pour la récupération.
- Sélectionnez l’option **Recover the database to the following point in time (Récupérer la base de données jusqu’à la date et heure suivantes)** (reportez-vous à l’étape 2 de la procédure _Récupération jusqu’à l’état le plus récent_).
- Les étapes restantes sont les mêmes que celles de la procédure ci-dessus.

## <a name="monitoring-snapshot-execution"></a>Surveillance de l’exécution des captures instantanées

Vous devez surveiller l’exécution des captures instantanées de stockage. Le script qui exécute une capture instantanée de stockage écrit la sortie dans un fichier, puis enregistre ce dernier au même emplacement que les scripts Perl. Un fichier distinct est créé pour chaque capture instantanée. La sortie de ce fichier indique clairement les différentes phases exécutées par le script de captures instantanées :

- Recherche des volumes qui doivent créer une capture instantanée
- Recherche des captures instantanées créées à partir de ces volumes
- Suppression des captures instantanées existantes en fonction du nombre de captures instantanées que vous avez spécifié
- Création d’une capture instantanée HANA
- Création de la capture instantanée de stockage sur les volumes
- Suppression de la capture instantanée HANA
- Changement du nom de la capture instantanée la plus récente par l’attribution de la valeur **.0**.

La partie la plus importante du script est la suivante :
```
**********************Creating HANA snapshot**********************
Creating the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data create snapshot"" ...
HANA snapshot created successfully.
**********************Creating Storage snapshot**********************
Taking snapshot hourly.recent for hana_data_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_backup_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_shared_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for sapmnt_lhanad01_t020_vol ...
Snapshot created successfully.
**********************Deleting HANA snapshot**********************
Deleting the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data drop snapshot"" ...
HANA snapshot deletion successfully.
```
Dans cet exemple, vous pouvez voir la façon dont le script enregistre la création de la capture instantanée HANA. Dans le cas d’une montée en puissance parallèle, ce processus est initialisé sur le nœud principal. Le nœud principal initie la création synchrone des captures instantanées sur chacun des nœuds de travail.

Puis la capture instantanée de stockage est effectuée. Une fois que l’exécution des captures instantanées de stockage a réussi, la capture instantanée HANA est supprimée.



<!--HONumber=Dec16_HO2-->



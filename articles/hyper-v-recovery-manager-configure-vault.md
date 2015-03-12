<properties 
	pageTitle="Didacticiel : Configuration de la protection entre des sites locaux VMM" 
	description="Azure Site Recovery coordinates the replication, failover and recovery of Hyper-V virtual machines between on-premises VMM sites." 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="raynew"/>


# Prise en main d'Azure Site Recovery :  protection entre deux sites VMM locaux à l'aide de la réplication Hyper-V


<div class="dev-callout"> 

<p>Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité de votre entreprise et de sa charge de travail en coordonnant la réplication, le basculement et la récupération de machines virtuelles dans divers scénarios de déploiement.<p>

<P>Ce didacticiel explique comment déployer Azure Site Recovery pour orchestrer et automatiser la protection des charges de travail s'exécutant sur un site VMM local vers un autre site VMM local à l'aide de la réplication Hyper-V.  Il utilise, dans la mesure du possible, le chemin de déploiement le plus rapide et les paramètres par défaut.</P>

<UL>
<LI>Vous trouverez des instructions complètes sur le déploiement dans les guides <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Planification</a> et <a href="http://go.microsoft.com/fwlink/?LinkId=321295">Déploiement</a>.</LI>
<LI>D'autres scénarios de déploiement Azure Site Recovery sont fournis dans la page <a href="http://go.microsoft.com/fwlink/?LinkId=518690">Vue d'ensemble d'Azure Site Recovery</a>.</LI>
<LI>Si vous rencontrez des problèmes au cours de ce didacticiel, consultez l'article Wiki <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery : erreurs courantes et résolutions</a> ou publiez vos questions sur le <a href="http://go.microsoft.com/fwlink/?LinkId=313628">forum Azure Recovery Services</a>.</LI>
</UL>

</div>


<h2><a id="before"></a>Configuration requise</h2> 
<div class="dev-callout"> 
<P>Avant de commencer le didacticiel, assurez-vous que tout est en place.</P>

<UL>
<LI><b>Compte Azure</b> : vous devez disposer d'un compte Azure. Si vous n'en avez pas, consultez la page <a href="http://aka.ms/try-azure">Version d'évaluation gratuite d'Azure</a>. Pour plus d'informations sur la tarification, consultez la page <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Tarification d'Azure Site Recovery Manager</a>.</LI>
<LI><b>Serveur VMM</b> : vous devez disposer d'au moins un serveur VMM s'exécutant sous System Center 2012 SP1 ou System Center 2012 R2.</LI>
<LI><b>Clouds VMM</b> : vous devez disposer d'au moins un cloud sur le serveur VMM source à protéger et d'un cloud sur le serveur VMM de destination. Si vous disposez d'un seul serveur VMM, vous devez configurer deux clouds. Le cloud principal à protéger doit comporter les éléments suivants :<UL>
	<LI>un ou plusieurs groupes hôtes VMM ;</LI>
	<LI>un ou plusieurs serveurs hôtes Hyper-V ou clusters dans chaque groupe hôte.</LI>
	<li>une ou plusieurs machines virtuelles situées sur le serveur Hyper-V source dans le cloud.</li>
		</UL></LI>
<LI>**Réseaux** : vous pouvez éventuellement configurer le mappage réseau pour vous assurer, d'une part, que les machines virtuelles de réplication sont placées de manière optimale sur les serveurs hôtes Hyper-V après le basculement et, d'autre part, que ces machines virtuelles peuvent se connecter aux réseaux VM appropriés. Quand le mappage réseau est activé, une machine virtuelle à l'emplacement principal est connectée à un réseau et son réplica à l'emplacement cible est connecté à son réseau mappé. Si vous ne configurez pas le mappage réseau, les machines virtuelles ne seront pas connectées aux réseaux VM après le basculement. Ce didacticiel décrit la procédure pas à pas la plus élémentaire et ne couvre pas le mappage réseau. Pour en savoir plus, consultez les sections suivantes :</LI>
	<UL>
	<LI><a href="http://go.microsoft.com/fwlink/?LinkId=522289">Mappage réseau</a> dans le guide Planification.</LI>
	<LI><a href="http://go.microsoft.com/fwlink/?LinkId=522293">Activer le mappage réseau</a> dans le guide de déploiement.</LI>
	</UL></LI>

</UL>



<h2><a id="tutorial"></a>Étapes du didacticiel</h2> 

Après avoir vérifié la configuration requise, procédez comme suit :
<UL>
<LI><a href="#vault">Étape 1 : Création d'un coffre</a> : créez un coffre Azure Site Recovery.</LI>
<LI><a href="#download">Étape 2 : Installation de l'application Fournisseur sur chaque serveur VMM</a> : générez une clé d'inscription dans le coffre, puis téléchargez le fichier d'installation du fournisseur. Exécutez le programme d'installation sur chaque serveur VMM pour installer le fournisseur et inscrire le serveur VMM dans le coffre.</LI>
<LI><a href="#clouds">Étape 3 : Configuration de la protection des clouds</a> : configurez les paramètres de protection des clouds VMM. Ces paramètres de protection sont appliqués à toutes les machines virtuelles dans le cloud que vous activez pour bénéficier de la protection Azure Site Recovery.</LI>
<LI><a href="#storagemapping">Étape 4 : Configuration du mappage de stockage</a> : si vous souhaitez spécifier l'emplacement de stockage des données de réplication, vous pouvez configurer le mappage de stockage. Les classifications de stockage sur le serveur VMM source sont mappées aux classifications de stockage sur le serveur cible.</LI>
<LI><a href="#enablevirtual">Étape 5 : Activation de la protection pour les machines virtuelles</a> : activez la protection des machines virtuelles situées dans les clouds VMM protégés.</LI>
<LI><a href="#recovery plans">Étape 6 : Configuration et exécution de plans de récupération</a> : créez un plan de récupération et exécutez un test de basculement pour vérifier que le plan fonctionne.</LI>

</UL>




<a name="vault"></a> <h2>Étape 1 : Création d'un coffre</h2>

1. Connectez-vous au [Portail de gestion](https://manage.windowsazure.com).


2. Développez <b>Data Services</b>, <b>Recovery Services</b>, puis cliquez sur <b>Coffre Site Recovery</b>.


3. Cliquez sur <b>Créer</b>, puis sur <b>Création rapide</b>.
	
4. Dans <b>Nom</b>, entrez un nom convivial permettant d'identifier le coffre.

5. Dans <b>Région</b>, sélectionnez la région géographique du coffre. Pour découvrir les régions prises en charge, référez-vous à la disponibilité géographique de la page <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Tarification d'Azure Site Recovery</a>.

6. Cliquez sur <b>Créer un coffre</b>. 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png)

<P>Vérifiez la barre d'état pour vous assurer que le coffre a été créé correctement. Le coffre apparaît comme <b>Actif</b> dans la page Recovery Services principale.</P>

<a name="upload"></a> <h2>Étape 2 : Configuration du coffre</h2>


1. Dans la page <b>Recovery Services</b>, cliquez sur le coffre pour ouvrir la page Démarrage rapide. Démarrage rapide peut également être ouvert à tout moment en utilisant l'icône.

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png)

2. Dans la liste déroulante, sélectionnez **Entre deux sites Hyper-V locaux**.
3. Dans **Préparer les serveurs VMM**, cliquez sur le fichier **Générer le fichier de clé d'inscription**. Une fois générée, la clé est valide pendant 5 jours. Copiez le fichier sur le serveur VMM. Vous en aurez besoin lors de la configuration du fournisseur.

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SR_E2ERegisterKey.png)
	



<a name="download"></a> <h2>Étape 3 : Installation du fournisseur Azure Site Recovery</h2>
	

1. Dans la page <b>Démarrage rapide</b>, dans **Préparer les serveurs VMM**, cliquez sur <b>Télécharger le fournisseur Microsoft Azure Site Recovery pour une installation sur les serveurs VMM</b> pour obtenir la dernière version du fichier d'installation du fournisseur.

2. Exécutez ce fichier sur les serveurs VMM source et cible.

3. Dans **Vérification de la configuration requise**, indiquez d'arrêter le service VMM pour lancer l'installation du fournisseur. Le service s'arrête et redémarre automatiquement une fois l'installation terminée. 

	![Prerequisites](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png)

4. Dans **Microsoft Update**, vous pouvez opter pour l'installation de mises à jour. En activant ce paramètre, les mises à jour du fournisseur seront installées en fonction de votre stratégie Microsoft Update.

	![Microsoft Updates](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png)

Une fois le fournisseur installé, poursuivez l'installation afin d'enregistrer le serveur dans le coffre.

5. Dans la page Connexion Internet, indiquez la façon dont le fournisseur s'exécutant sur le serveur VMM se connecte à Internet. Sélectionnez <b>Utiliser les paramètres du proxy système par défaut</b> pour utiliser les paramètres de connexion Internet par défaut configurés sur le serveur.

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png)

6. Dans **Clé d'inscription**, indiquez que la clé a été téléchargée depuis Azure Site Recovery et copiée sur le serveur VMM.
7. Dans **Nom du coffre**, vérifiez le nom du coffre dans lequel le serveur sera inscrit.
8. Dans **Nom du serveur**, entrez un nom convivial permettant d'identifier le serveur VMM dans le coffre.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png)


9. Dans **Synchronisation initiale des métadonnées du cloud**, indiquez si vous souhaitez synchroniser les métadonnées de tous les clouds sur le serveur VMM avec le coffre. Cette action se produit une seule fois sur chaque serveur. Si vous ne souhaitez pas synchroniser tous les clouds, vous pouvez désactiver ce paramètre et synchroniser individuellement chaque cloud via les propriétés du cloud de la console VMM.


7. Dans **Chiffrement des données**, vous pouvez générer un certificat utilisé pour chiffrer des données protégées dans Azure. 
Cette option ne s'applique pas au scénario décrit dans ce didacticiel.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png)

8. Cliquez sur <b>Inscrire</b> pour terminer le processus. Une fois l'enregistrement terminé, les métadonnées du serveur VMM sont extraites par Azure Site Recovery. Le serveur apparaît sous l'onglet <b>Ressources</b> de la page **Serveurs** du coffre.




<h2><a id="clouds"></a>Étape 4 : Configuration des paramètres de protection de cloud</h2>

Une fois les serveurs VMM inscrits, vous pouvez configurer les paramètres de protection de cloud. Vous avez activé l'option **Synchroniser les données du cloud avec le coffre** lors de l'installation du fournisseur. Ainsi, tous les clouds du serveur VMM apparaissent sous l'onglet <b>Éléments protégés</b> du coffre.

![Published Cloud](./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png)

1. Dans la page Démarrage rapide, cliquez sur **Configurer la protection pour les clouds VMM**.
2. Sous l'onglet **Éléments protégés**, sélectionnez le cloud à configurer, puis accédez à l'onglet **Configuration**. Notez les points suivants :
3. Dans <b>Cible</b>, sélectionnez <b>VMM</b>.
4. Dans <b>Emplacement cible</b>, sélectionnez le serveur VMM local qui gère le cloud à utiliser pour la récupération.
4. Dans <b>Cloud cible</b>, sélectionnez le cloud cible à utiliser pour le basculement des machines virtuelles du cloud source. Notez les points suivants :
	- Nous vous recommandons de choisir un cloud cible qui remplisse les conditions requises pour la récupération des machines virtuelles que vous prévoyez de protéger.
	- Un cloud ne peut appartenir qu'à une seule paire de clouds, que ce soit en qualité de cloud principal ou de cloud cible.

6. Dans <b>Copier la fréquence</b>, laissez le paramètre par défaut. Cette valeur indique la fréquence de synchronisation des données entre les emplacements source et cible. Elle n'est utile que lorsque l'hôte Hyper-V s'exécute sur Windows Server 2012 R2. Les autres serveurs utilisent un paramétrage par défaut de cinq minutes.
7. Dans <b>Points de récupération supplémentaires</b>, conservez le paramètre par défaut. Cette valeur indique si vous souhaitez créer des points de récupération supplémentaires. La valeur par défaut de zéro indique que seul le dernier point principal de récupération d'une machine virtuelle est stocké sur un serveur hôte de réplication.
8. Dans <b>Fréquence des instantanés cohérents au niveau applicatif</b>, laissez le paramètre par défaut. Cette valeur indique la fréquence de création de captures instantanées. Ces dernières utilisent le service VSS (Volume Shadow Copy Service) pour s'assurer que les applications sont dans un état cohérent lors de la prise des captures instantanées.  Si vous voulez définir cette valeur pour la procédure pas à pas du didacticiel, assurez-vous qu'elle est inférieure au nombre de points de récupération supplémentaires que vous configurez.
	![Configure protection settings](./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2E.png)
9. Dans <b>Compression du transfert de données</b>, indiquez si les données répliquées transférées doivent être compressées. 
10. Dans <b>Authentification</b>, indiquez le mode d'authentification du trafic entre les serveurs hôtes Hyper-V principaux et de récupération. À moins de disposer d'un environnement Kerberos configuré et opérationnel, nous vous recommandons de choisir HTTPS pour les besoins de cette procédure pas à pas. Azure Site Recovery configurera automatiquement des certificats pour l'authentification HTTPS. Aucune configuration manuelle n'est nécessaire. Notez que ce paramètre n'est utile que pour les serveurs hôtes Hyper-V s'exécutant sur Windows Server 2012 R2.
11. Dans <b>Port</b>, laissez le paramètre par défaut. Cette valeur définit le numéro de port sur lequel les ordinateurs hôtes Hyper-V source et cible écoutent le trafic de réplication.
12. Dans <b>Méthode de réplication</b>, indiquez comment sera traitée la réplication de données initiale entre les emplacements source et cible avant que la réplication normale ne démarre. 
	- <b>Sur le réseau</b> :la copie de données sur le réseau peut demander du temps et des ressources. Nous vous recommandons d'utiliser cette option si le cloud contient des machines virtuelles dotées de disques durs virtuels relativement petits et si le serveur VMM principal est connecté au serveur VMM secondaire sur une connexion à large bande passante. Vous pouvez opter pour un démarrage immédiat de la copie ou sélectionner une heure. Si vous utilisez la réplication réseau, nous vous recommandons de la planifier sur des heures creuses.
	- <b>Hors connexion</b> : cette méthode précise que la réplication initiale s'exécute à l'aide d'un support externe. Elle est indiquée si vous voulez éviter une dégradation des performances réseau ou pour les emplacements éloignés géographiquement. Pour utiliser cette méthode, il convient de spécifier l'emplacement d'exportation sur le cloud source et l'emplacement d'importation sur le cloud cible. Lorsque vous activez la protection pour une machine virtuelle, le disque dur virtuel est copié vers l'emplacement d'exportation spécifié. Vous l'envoyez vers le site cible et le copiez vers l'emplacement d'importation. Le système copie les informations importées vers les machines virtuelles de réplication. Pour connaître les détails de la configuration requise pour la réplication hors ligne, consultez l'<a href="http://go.microsoft.com/fwlink/?LinkId=323469">Étape 3 : Configuration des paramètres de protection des clouds VMM</a> du guide de déploiement.
13. Sélectionnez **Supprimer une machine virtuelle de réplica** pour indiquer que la machine virtuelle de réplication doit être supprimée si vous cessez de protéger la machine virtuelle en sélectionnant l'option **Désactiver la protection pour la machine virtuelle** sous l'onglet Machines virtuelles des propriétés du cloud. Si vous activez ce paramètre et que vous désactivez la protection, la machine virtuelle est supprimée d'Azure Site Recovery, les paramètres Site Recovery de la machine virtuelle sont supprimés de la console VMM et le réplica est supprimé.
	![Configure protection settings](./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2ERep.png)

<p>Suite à l'enregistrement des paramètres, une tâche est créée que vous pouvez surveiller sous l'onglet <b>Tâches</b>. Tous les serveurs hôte Hyper-V du cloud VMM source sont configurés pour la réplication. Les paramètres du cloud peuvent être modifiés sous l'onglet <b>Configurer</b>. Pour modifier l'emplacement cible ou le cloud cible, vous devez supprimer la configuration du cloud, puis reconfigurer ce dernier.</p>

<h2><a id="storagemapping"></a>Étape 5 : Configuration du mappage de stockage</h2>

<p>Ce tutoriel décrit le chemin le plus simple pour déployer Azure Site Recovery dans un environnement de test. Pour configurer le mappage de stockage via ce didacticiel, suivez la procédure de <a href="http://go.microsoft.com/fwlink/?LinkId=402535">configuration du mappage de stockage</a> présentée dans le guide de déploiement.</p>


<h2><a id="enablevirtual"></a>Étape 6 : Activation de la protection d'une machine virtuelle</h2>
<p>Dès lors que les serveurs, les clouds et les réseaux ont été configurés correctement, vous pouvez activer la protection pour les machines virtuelles du cloud.</p>
<OL>
<li>Dans l'onglet <b>Machines virtuelles</b> du cloud dans lequel se trouve la machine virtuelle, cliquez sur <b>Activer la protection</b>, puis sélectionnez <b>Ajouter des machines virtuelles</b>. </li>
<li>Dans la liste des machines virtuelles du cloud, sélectionnez celle que vous voulez protéger.</li> 
</OL>

![Enable virtual machine protection](./media/hyper-v-recovery-manager-configure-vault/SR_EnableProtectionVM.png)


<P>Vous pouvez suivre la progression de l'action d'activation de la protection, y compris la réplication initiale, sous l'onglet **Tâches**. Lorsque la tâche de finalisation de la protection s'exécute, la machine virtuelle est prête à être basculée. Une fois la protection activée et les machines virtuelles répliquées, celles-ci sont affichées dans Azure.</p>



![Virtual machine protection job](./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png)


<h2><a id="recoveryplans"></a>Étape 7 : Test du déploiement</h2>

Pour vous assurer que les machines virtuelles et les données basculent comme prévu, testez votre déploiement. Pour ce faire, créez un plan de récupération en sélectionnant des groupes de réplication. Ensuite, exécutez un test de basculement sur le plan.

1. Sous l'onglet **Plans de récupération**, cliquez sur **Créer un plan de récupération**.
2. Spécifiez un nom pour le plan de récupération et les serveurs VMM source et cible. Le basculement et la récupération doivent être activés sur les machines virtuelles du serveur source. Sélectionnez **Hyper-V** pour afficher uniquement les clouds configurés pour la réplication Hyper-V.

	![Create recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRE2E_RP1.png)

3. Dans **Sélectionner les machines virtuelles**, sélectionnez les groupes de réplication. Toutes les machines virtuelles associées au groupe de réplication sont sélectionnées et ajoutées au plan de récupération. Ces machines virtuelles sont ajoutées au groupe par défaut du plan de récupération, à savoir le groupe 1. Vous pouvez ajouter d'autres groupes si nécessaire. Notez qu'après la réplication, les machines virtuelles démarrent dans l'ordre des groupes du plan de récupération.

	![Add virtual machines](./media/hyper-v-recovery-manager-configure-vault/SRE2E_RP2.png)	

4. Une fois un plan de récupération créé, celui-ci apparaît dans la liste sous l'onglet **Plans de récupération**. 
5. Sous l'onglet **Plans de récupération**, sélectionnez le plan et cliquez sur **Test de basculement**.
6. Dans la page **Confirmer le test de basculement**, sélectionnez **Aucun**. Si cette option est activée, les machines virtuelles de réplication basculées ne sont connectées à aucun réseau. Le test vérifie que la machine virtuelle bascule comme prévu, mais il ne vérifie pas l'environnement de votre réseau de réplication. Si vous souhaitez exécuter un test de basculement plus complet, consultez la page <a href="http://go.microsoft.com/fwlink/?LinkId=522291">Tester un déploiement d'un serveur local vers un serveur local sur MSDN</a>.

	![Select test network](./media/hyper-v-recovery-manager-configure-vault/SRSAN_TestFailover1.png)


7. La machine virtuelle de test est créée sur le même hôte que celui sur lequel la machine virtuelle de réplication existe. Elle n'est pas ajoutée au cloud où se trouve la machine virtuelle de réplication.
8. Après la réplication, la machine virtuelle de réplication possède une adresse IP différente de celle de la machine virtuelle principale. Si vous émettez des adresses à partir de DHCP, DNS est mis à jour automatiquement. Si vous n'utilisez pas DHCP et que vous souhaitez vous assurer que les adresses sont identiques, vous devez exécuter quelques scripts.
9. Exécutez cet exemple de script pour récupérer l'adresse IP.
    **$vm = Get-SCVirtualMachine -Name <VM_NAME>
	$na = $vm[0].VirtualNetworkAdapters>
	$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
	$ip.address**  
11. Exécutez cet exemple de script pour mettre à jour DNS, en spécifiant l'adresse IP que vous avez récupérée à l'aide de l'exemple de script précédent.

	**[string]$Zone,
	[string]$name,
	[string]$IP
	)
	$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
	$newrecord = $record.clone()
	$newrecord.RecordData[0].IPv4Address  =  $IP
	Set-DnsServerResourceRecord -zonename com -OldInputObject $record -NewInputObject $Newrecord**


<h3><a id="runtest"></a>Suivi de l'activité</h3>
<p>Vous pouvez utiliser l'onglet <b>Tâches</b> et le <b>Tableau de bord</b> pour afficher et surveiller les tâches principales effectuées par le coffre Azure Site Recovery, telles que la configuration de la protection d'un cloud, l'activation et la désactivation de la protection d'une machine virtuelle, l'exécution d'un basculement (planifié, non planifié ou test) et la validation d'un basculement non planifié.</p>

<p>Sous l'onglet <b>Tâches</b>, vous pouvez afficher les tâches, accéder aux détails de l'une d'elles ainsi qu'aux erreurs, exécuter des requêtes pour récupérer les tâches qui correspondent à des critères spécifiques, exporter des tâches vers Excel et redémarrer celles qui ont échoué.</p>

<p>Dans le <b>Tableau de bord</b>, vous pouvez télécharger la dernière version des fichiers d'installation du fournisseur et de l'agent, obtenir des informations sur la configuration du coffre, découvrir le nombre de machines virtuelles dont la protection est assurée par le coffre, afficher les tâches récentes, gérer le certificat du coffre et resynchroniser les machines virtuelles.</p>

<p>Pour plus d'informations sur les interactions avec les tâches et le tableau de bord, consultez le <a href="http://go.microsoft.com/fwlink/?LinkId=398534">Guide sur les opérations et la surveillance</a>.</p>
	
<h2><a id="next"></a>Étapes suivantes</h2>
<UL>
<LI>Pour planifier et déployer Azure Site Recovery dans un environnement de production complet, consultez le <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Guide de planification d'Azure Site Recovery</a> et le <a href="http://go.microsoft.com/fwlink/?LinkId=321295">Guide de déploiement d'Azure Site Recovery</a>.</LI>
<LI>Pour toute question, visitez le <a href="http://go.microsoft.com/fwlink/?LinkId=313628">forum Azure Recovery Services</a>.</LI> 
</UL>

<!--HONumber=35.2-->

<!--HONumber=46--> 

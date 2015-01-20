<properties urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Prise en main d'Azure Site Recovery : protection entre un site local et Azure à l'aide de la réplication Hyper-V" metaKeywords="Azure Site Recovery, VMM, clouds, récupération d'urgence" description="Azure Site Recovery coordonne la réplication, le basculement et la récupération de machines virtuelles Hyper-V situées dans des clouds VMM locaux sur Azure." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Getting Started with Azure Site Recovery: On-Premises VMM Site to Azure protection with Hyper-V Replication
" editor="jimbe" manager="johndaw" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/19/2014" ms.author="raynew" />


# Prise en main d'Azure Site Recovery : protection entre un site VMM local et Azure à l'aide de la réplication Hyper-V



<div class="dev-callout"> 

<p>Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité de votre entreprise et de sa charge de travail en coordonnant la réplication, le basculement et la récupération de machines virtuelles dans divers scénarios de déploiement.<p>

<P>Ce didacticiel explique comment déployer Azure Site Recovery pour orchestrer la protection entre un site VMM local et Azure à l'aide de la réplication Hyper-V.  Il utilise, dans la mesure du possible, le chemin de déploiement le plus rapide et les paramètres par défaut.</P>

<UL>
<LI>Pour plus d'informations sur un déploiement complet, consultez les guides <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Planification</a> et <a href="http://go.microsoft.com/fwlink/?LinkId=402679">Déploiement</a>.</LI>
<LI>D'autres scénarios de déploiement Azure Site Recovery sont fournis dans la page <a href="http://go.microsoft.com/fwlink/?LinkId=518690">Vue d'ensemble d'Azure Site Recovery</a>.</LI>
<LI>Si vous rencontrez des problèmes au cours de ce didacticiel, consultez l'article Wiki <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery : erreurs courantes et résolutions</a> ou publiez vos questions sur le <a href="http://go.microsoft.com/fwlink/?LinkId=313628">forum Azure Recovery Services</a>.</LI>
</UL>
</div>


<h2><a id="before"></a>Configuration requise</h2> 
<div class="dev-callout"> 
<P>Avant de commencer le didacticiel, assurez-vous que tout est en place.</P>

<UL>
<LI><b>Compte Azure</b> : vous devez disposer d'un compte Azure. Si vous n'en avez pas, consultez la page <a href="http://aka.ms/try-azure">Version d'évaluation gratuite d'Azure</a>. Pour plus d'informations sur la tarification de l'abonnement, consultez la page <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Tarification d'Azure Site Recovery Manager</a>.</LI>

<LI><b>Compte de stockage Azure</b> : vous avez besoin d'un compte de stockage Azure pour stocker les données répliquées dans Azure. La géo-réplication doit être activée pour ce compte. Ce dernier doit se trouver dans la même région que le service Azure Site Recovery et être associé au même abonnement. Pour en savoir plus sur la configuration du stockage Azure, consultez la page <a href="http://go.microsoft.com/fwlink/?LinkId=398704">Introduction à Microsoft Azure Storage</a>.</LI><LI><b>Serveur VMM :</b> serveur VMM exécuté sur System Center 2012 R2.</LI>
<LI><b>Clouds VMM</b> : un cloud au moins sur le serveur VMM. Le cloud doit contenir les éléments suivants :
	<UL>
	<LI>un ou plusieurs groupes hôtes VMM ;</LI>
	<LI>un ou plusieurs serveurs hôtes Hyper-V ou clusters dans chaque groupe hôte.</LI>
	<li>une ou plusieurs machines virtuelles situées sur le serveur Hyper-V source dans le cloud. Les machines virtuelles doivent être de la génération 1.</li>
		</UL></LI>	
<LI><b>Machine virtuelle</b> : vous avez besoin de machines virtuelles qui satisfont aux exigences liées à Azure. Consultez la page <a href="http://go.microsoft.com/fwlink/?LinkId=402602">Configuration requise et prise en charge</a> dans le guide de planification.</LI>
<LI>Pour une liste complète des conditions de prise en charge des machines virtuelles pour le basculement vers Azure, lisez  </LI>
</UL>


<h2><a id="tutorial"></a>Étapes du didacticiel</h2> 

Après avoir vérifié la configuration requise, procédez comme suit :
<UL>

<LI><a href="#vault">Étape 1 : Création d'un coffre</a> : créez un coffre Azure Site Recovery.</LI>
<LI><a href="#download">Étape 2 : Installation de l'application Fournisseur sur le serveur VMM</a> : générez une clé d'inscription dans le coffre, puis téléchargez le fichier d'installation du fournisseur. Exécutez le programme d'installation sur le serveur VMM pour installer le fournisseur et inscrire le serveur VMM dans le coffre.</LI>
<LI><a href="#storage">Étape 3 : Ajout d'un compte de stockage Azure</a> : si vous n'avez pas de compte, créez-en un. </LI>
<LI><a href="#agent">Étape 4 : Installation de l'application Agent</a> : installez l'agent Microsoft Azure Recovery Services sur chaque hôte Hyper-V situé dans les clouds VMM à protéger.</LI>
<LI><a href="#clouds">Étape 5 : Configuration de la protection des clouds</a> : configurez les paramètres de protection des clouds VMM.</LI>
<LI><a href="#NetworkMapping">Étape 6 : Configuration du mappage réseau</a> : vous pouvez éventuellement configurer le mappage réseau de manière à mapper les réseaux de machines virtuelles sources vers les réseaux Azure cibles.</LI>
<LI><a href="#virtualmachines">Étape 7 : Activation de la protection pour les machines virtuelles</a> : activez la protection des machines virtuelles situées dans les clouds VMM protégés.</LI>
<LI><a href="#test">Étape 8 : Test du déploiement</a> : vous pouvez exécuter un test de basculement pour une seule machine virtuelle, ou créer un plan de récupération et exécuter un test de basculement pour ce dernier pour vérifier qu'il fonctionne.</LI>
</UL>



<a name="vault"></a> <h2>Étape 1 : Création d'un coffre</h2>

1. Connectez-vous au [Portail de gestion](https://manage.windowsazure.com).


2. Développez <b>Data Services</b>, <b>Recovery Services</b>, puis cliquez sur <b>Coffre Site Recovery</b>.

3. Cliquez sur <b>Créer</b>, puis sur <b>Création rapide</b>.
	

4. Dans <b>Nom</b>, entrez un nom convivial permettant d'identifier le coffre.

5. Dans <b>Région</b>, sélectionnez la région géographique du coffre. Les régions géographiques disponibles sont les suivantes : Asie orientale, Europe de l'Ouest, Ouest des États-Unis, Est des États-Unis, Europe du Nord et Asie du Sud-Est.
6. Cliquez sur <b>Créer un coffre</b>. 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png)

<P>Vérifiez la barre d'état pour vous assurer que le coffre a été créé correctement. Le coffre apparaît comme <b>Actif</b> dans la page Recovery Services principale.</P>





 <a name="download"></a> <h2>Étape 2 : Génération d'une clé d'enregistrement et installation du fournisseur Azure Site Recovery</h2>
 

1. Dans la page <b>Recovery Services</b>, cliquez sur le coffre pour ouvrir la page Démarrage rapide. Démarrage rapide peut également être ouvert à tout moment en utilisant l'icône.

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png)

2. Dans la liste déroulante, sélectionnez **Entre un site Hyper-V local et Microsoft Azure**.
3. Dans **Préparer les serveurs VMM**, cliquez sur le fichier **Générer le fichier de clé d'inscription**. Une fois générée, la clé est valide pendant 5 jours. Copiez le fichier sur le serveur VMM. Vous en aurez besoin lors de la configuration du fournisseur.

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SR_E2ARegisterKey.png)

4. Dans la page <b>Démarrage rapide</b>, dans **Préparer les serveurs VMM**, cliquez sur <b>Télécharger le fournisseur Microsoft Azure Site Recovery pour une installation sur les serveurs VMM</b> pour obtenir la dernière version du fichier d'installation du fournisseur.

2. Exécutez ce fichier sur le serveur VMM source.


3. Dans **Vérification de la configuration requise**, indiquez d'arrêter le service VMM pour lancer l'installation du fournisseur. Le service s'arrête et redémarre automatiquement une fois l'installation terminée.

	![Prerequisites](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png)

4. Dans **Microsoft Update**, vous pouvez opter pour l'installation de mises à jour. En activant ce paramètre, les mises à jour du fournisseur seront installées en fonction de votre stratégie Microsoft Update.

	![Microsoft Updates](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png)

Une fois le fournisseur installé, poursuivez l'installation afin d'enregistrer le serveur dans le coffre.

5. Dans la page **Connexion Internet**, indiquez la façon dont le fournisseur s'exécutant sur le serveur VMM se connecte à Internet. Sélectionnez <b>Utiliser les paramètres proxy par défaut du système</b> pour utiliser les paramètres de connexion Internet par défaut configurés sur le serveur.

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png)

6. Dans **Clé d'inscription**, indiquez que la clé a été téléchargée depuis Azure Site Recovery et copiée sur le serveur VMM.
7. Dans **Nom du coffre**, vérifiez le nom du coffre dans lequel le serveur sera inscrit.
8. Dans **Nom du serveur**, entrez un nom convivial permettant d'identifier le serveur VMM dans le coffre.

	
	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png)

8. Dans **Synchronisation initiale des métadonnées du cloud**, indiquez si vous souhaitez synchroniser les métadonnées de tous les clouds sur le serveur VMM avec le coffre. Cette action se produit une seule fois sur chaque serveur. Si vous ne souhaitez pas synchroniser tous les clouds, vous pouvez désactiver ce paramètre et synchroniser individuellement chaque cloud via les propriétés du cloud de la console VMM.


9. Dans **Chiffrement des données**, spécifiez un emplacement dans lequel enregistrer un certificat SSL généré automatiquement pour le chiffrement de données. Ce certificat est utilisé si vous activez le chiffrement de données pour un cloud protégé par Azure dans le portail Azure Site Recovery. Conservez ce certificat en sécurité. Quand vous exécutez un basculement vers Azure, vous le sélectionner pour déchiffrer les données chiffrées. 
Cette option ne vous concerne pas si vous effectuez une réplication d'un site local vers un autre.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png)

8. Cliquez sur <b>Inscrire</b> pour terminer le processus. Une fois l'enregistrement terminé, les métadonnées du serveur VMM sont extraites par Azure Site Recovery. Le serveur figure dans l'édition de l'onglet <b>Ressources</b> de la page **Serveurs** du coffre.

<h2><a id="storage"></a>Étape 3 : Création d'un compte de stockage Azure</h2>
Si vous ne possédez pas de compte Azure Storage, cliquez sur **Ajouter un compte Azure Storage**. La géo-réplication doit être activée pour ce compte. Ce dernier doit se trouver dans la même région que le service Azure Site Recovery et être associé au même abonnement.

<P>Utilisez ce didacticiel pour configurer une validation technique rapide d'Azure Site Recovery dans un déploiement local sur Azure. Il utilise les chemins les plus rapides et les paramètres par défaut lorsque cela est possible. Grâce à lui, vous pourrez créer un coffre Azure Site Recovery, installer le fournisseur Azure Site Recovery dans le serveur VMM source, installer l'agent Azure Recovery Services sur des serveurs hôtes Hyper-V dans les clouds VMM, configurer les paramètres de protection des clouds, activer la protection des machines virtuelles et tester votre déploiement.</P>

![Storage account](./media/hyper-v-recovery-manager-configure-vault/SR_E2AStorageAgent.png)

<h2><a id="agent"></a>Étape 4 : Installation de l'agent Azure Recovery Services sur les hôtes Hyper-V</h2>

Installez l'agent Azure Recovery Services sur chaque serveur hôte Hyper-V situé dans les clouds VMM que vous souhaitez protéger.

1. Sur la page Démarrage rapide, cliquez sur <b>Télécharger l'agent des services Azure Site Recovery et l'installer sur les hôtes</b> afin d'obtenir la dernière version du fichier d'installation de l'agent.

	![Install Recovery Services Agent](./media/hyper-v-recovery-manager-configure-vault/SR_E2AInstallHyperVAgent.png)

2. Exécutez le fichier d'installation sur chaque serveur hôte Hyper-V situé dans les clouds VMM que vous souhaitez protéger.
3. Sur la page **Vérification de la configuration requise**, cliquez sur <b>Suivant</b>. Tous les éléments manquants de la configuration requise sont automatiquement installés.

	![Prerequisites Recovery Services Agent](./media/hyper-v-recovery-manager-configure-vault/SR_AgentPrereqs.png)

4. Dans la page **Paramètres d'installation**, indiquez où vous voulez installer l'agent et sélectionnez l'emplacement du cache dans lequel les métadonnées de sauvegarde seront installées. Cliquez ensuite sur <b>Installer</b>.


<h2><a id="clouds"></a>Étape 5 : Configuration des paramètres de protection de cloud</h2>

Une fois les serveurs VMM inscrits, vous pouvez configurer les paramètres de protection de cloud. Vous avez activé l'option **Synchroniser les données du cloud avec le coffre** lors de l'installation du fournisseur. Ainsi, tous les clouds du serveur VMM apparaissent sous l'onglet <b>Éléments protégés</b> du coffre.

![Published Cloud](./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png)


1. Dans la page Démarrage rapide, cliquez sur **Configurer la protection pour les clouds VMM**.
2. Sous l'onglet **Éléments protégés**, cliquez sur le cloud à configurer, puis accédez à l'onglet **Configuration**.
3. Dans la zone <b>Cible</b>, sélectionnez <b>Microsoft Azure</b>.
4. Dans la zone <b>Compte de stockage</b>, sélectionnez le stockage Azure dans lequel vous voulez stocker les machines virtuelles.
5. En regard de <b>Chiffrer les données stockées</b>, cliquez sur <b>Désactivé</b>. Ce paramètre indique que les données doivent être répliquées avec chiffrement entre le site local et Azure.
6. Dans <b>Copier la fréquence</b>, laissez le paramètre par défaut. Cette valeur indique la fréquence de synchronisation des données entre les emplacements source et cible.
7. Dans la zone <b>Conserver les points de récupération pour</b>, conservez le paramètre par défaut. Avec une valeur par défaut de zéro, seul le dernier point de récupération d'une machine virtuelle principale est stocké sur un serveur hôte de réplication.
8. Dans <b>Fréquence des instantanés cohérents au niveau applicatif</b>, laissez le paramètre par défaut. Cette valeur indique la fréquence de création de captures instantanées. Ces dernières utilisent le service VSS (Volume Shadow Copy Service) pour s'assurer que les applications sont dans un état cohérent lors de la prise des captures instantanées.  Si vous définissez une valeur, assurez-vous qu'elle est inférieure au nombre de points de récupération supplémentaires que vous configurez.
9. Dans la zone <b>Heure de démarrage de la réplication</b>, indiquez l'heure à laquelle la réplication initiale des données dans Azure doit démarrer. Le fuseau horaire du serveur hôte Hyper-V est utilisé à cet effet. Nous vous recommandons de planifier la réplication initiale sur des heures creuses. 

	![Cloud replication settings](./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2A.png)

Suite à l'enregistrement des paramètres, une tâche est créée que vous pouvez surveiller sous l'onglet <b>Tâches</b>. Tous les serveurs hôte Hyper-V du cloud VMM source sont configurés pour la réplication.

Après l'enregistrement, les paramètres du cloud peuvent être modifiés sous l'onglet <b>Configurer</b>. Pour modifier l'emplacement ou le stockage cible, vous devez supprimer la configuration du cloud, puis reconfigurer ce dernier. Notez que si vous modifiez le compte de stockage, la modification n'est appliquée que pour les machines virtuelles qui sont activées pour la protection après la modification du compte. Les machines virtuelles existantes ne sont pas migrées vers le nouveau compte de stockage.</p>

<h2><a id="networkmapping"></a>Étape 6 : Configuration du mappage réseau</h2>

<p>Ce tutoriel décrit le chemin le plus simple pour déployer Azure Site Recovery dans un environnement de test. Pour configurer le mappage réseau via ce didacticiel, consultez la rubrique <a href="http://go.microsoft.com/fwlink/?LinkId=324817">Préparation du mappage réseau</a> du guide de planification. Pour configurer le mappage, suivez la procédure de <a href="http://go.microsoft.com/fwlink/?LinkId=402533">configuration du mappage réseau</a> présentée dans le guide de déploiement.</p>




<h2><a id="virtualmachines"></a>Étape 7 : activation de la protection pour les machines virtuelles</h2>

Dès lors que les serveurs, les clouds et les réseaux ont été configurés correctement, vous pouvez activer la protection pour les machines virtuelles du cloud. Notez les points suivants :

- Les machines virtuelles doivent répondre aux exigences liées à Azure. Pour en savoir plus sur ces dernières, consultez la page <a href="http://go.microsoft.com/fwlink/?LinkId=402602">Configuration requise et prise en charge</a> dans le guide de planification.
- Pour activer la protection, vous devez définir les propriétés du système d'exploitation et du disque du système d'exploitation pour la machine virtuelle. Lorsque vous créez une machine virtuelle dans VMM à l'aide d'un modèle de machine virtuelle, vous pouvez définir la propriété. Vous pouvez également définir ces propriétés pour des machines virtuelles existantes sous les onglets **Général** et **Configuration matérielle** des propriétés de la machine virtuelle. Si vous ne définissez pas ces propriétés dans VMM, vous pourrez les configurer dans le portail Azure Site Recovery.

![Create virtual machine](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ANew.png)

![Modify virtual machine properties](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2AExisting.png)


1. Pour activer la protection, sous l'onglet <b>Machines virtuelles</b> dans le cloud où se trouve la machine virtuelle, cliquez sur <b>Activer la protection</b>, puis sélectionnez <b>Ajouter des machines virtuelles</b>.
2. Dans la liste des machines virtuelles du cloud, sélectionnez celle que vous voulez protéger. 

	![Enable virtual machine protection](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ASelectVM.png)

3. Vérifiez les propriétés de la machine virtuelle et modifiez-les si besoin est.

	![Verify virtual machines](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVME2AProps.png)

Vous pouvez suivre la progression de l'action d'activation de la protection, y compris la réplication initiale, sous l'onglet **Tâches**. Lorsque la tâche de finalisation de la protection s'exécute, la machine virtuelle est prête à être basculée. Une fois la protection activée et les machines virtuelles répliquées, celles-ci sont affichées dans Azure.


![Virtual machine protection job](./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png)


<h2><a id="test"></a>Étape 8 : Test du déploiement</h2>
Pour tester votre déploiement, vous pouvez exécuter un test de basculement pour une seule machine virtuelle, ou créer un plan de récupération comportant plusieurs machines virtuelles et exécuter sur lui un test de basculement.  Il simule votre mécanisme de basculement et de récupération dans un réseau isolé. Notez les points suivants :
<UL>
<li>Si vous voulez vous connecter à la machine virtuelle dans Azure avec le Bureau à distance après le basculement, activez Connexion Bureau à distance sur la machine virtuelle avant d'exécuter le test de basculement.</li>
<li>Après le basculement, vous utiliserez une adresse IP publique pour vous connecter à la machine virtuelle dans Azure avec le Bureau à distance. Dans ce cas, assurez-vous qu'aucune de vos stratégies de domaine ne vous empêche de vous connecter à une machine virtuelle avec une adresse publique.</li>
</UL>

1. Sous l'onglet **Plans de récupération**, ajoutez un nouveau plan. Spécifiez un nom, **VMM** dans **Type de Source**, puis le serveur VMM source dans **Source**. La cible est Azure.

	![Create recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRAzure_RP1.png)

2. Dans la page **Confirmer le test de basculement**, sélectionnez **Aucun**. Un test de basculement avec ce paramètre vérifie que la machine virtuelle a été répliquée correctement dans Azure, mais il ne vérifie pas la configuration de votre réseau de réplication. Si vous souhaitez exécuter le test avec un réseau Azure spécifié, consultez la page href="http://go.microsoft.com/fwlink/?LinkId=522292">Tester un déploiement d'un serveur local vers Azure</a>.

	![No network](./media/hyper-v-recovery-manager-configure-vault/SRAzure_TestFailoverNoNetwork.png)


3. Quand le basculement se trouve en phase **Fin du test de basculement**, cliquez sur **Terminer le test** pour terminer le test de basculement. Sous l'onglet **Tâche**, vous pouvez suivre la progression et l'état du basculement et effectuer toutes les actions nécessaires.
4. À la fin du basculement, procédez comme suit :
	- Vérifiez que les machines virtuelles démarrent correctement.
	- Cliquez sur **Notes** pour consigner et enregistrer les éventuelles observations associées au test de basculement.
	- Cliquez sur **Le test de basculement est terminé. Nettoyer l'environnement de test.** pour mettre automatiquement hors tension la machine virtuelle de test et supprimer le réseau Azure test.
5. Après le basculement, vous pouvez voir le réplica de test de la machine virtuelle dans le portail Azure. Si vous êtes autorisé à accéder aux machines virtuelles à partir de votre réseau local, vous pouvez initier une connexion Bureau à distance à la machine virtuelle.


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

<properties 
	pageTitle="Didacticiel : configurer la protection entre un site local Hyper-V et Azure" 
	description="Azure Site Recovery coordonne la réplication, le basculement et la récupération de machines virtuelles entre un site local Hyper-V et Azure" 
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

# Didacticiel : configurer la protection entre un site local Hyper-V et Azure


<h2><a id="overview" name="overview" href="#overview"></a>Vue d'ensemble</h2>
<p>Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité de votre entreprise et de sa charge de travail en coordonnant la réplication, le basculement et la récupération de machines virtuelles dans divers scénarios de déploiement.<p>

Ce didacticiel explique comment déployer Azure Site Recovery pour protéger les charges de travail s'exécutant sur un serveur Hyper-V avec Windows Server 2012 R2 dans un site local. Les machines virtuelles sur le serveur Hyper-V sont répliquées dans Azure à l'aide de la réplication Hyper-V. Ce déploiement est particulièrement utile si vous possédez des serveurs Hyper-V dans votre entreprise ou dans votre filiale et que System Center VMM n'est pas déployé.


<LI>Ce didacticiel utilise le chemin de déploiement le plus rapide avec les paramètres minimums et les paramètres par défaut.
Vous trouverez des instructions complètes sur le déploiement dans les guides <a href="http://go.microsoft.com/fwlink/?LinkId=522087">Planification</a> et <a href=" http://go.microsoft.com/fwlink/?LinkId=522088">Déploiement</a>.</LI>
<LI>Vous pouvez découvrir d'autres scénarios de déploiement d'Azure Site Recovery dans <a href="http://go.microsoft.com/fwlink/?LinkId=518690">Vue d'ensemble d'Azure Site Recovery</a>.</LI>
<LI>Si vous rencontrez des problèmes durant ce didacticiel, consultez l'article Wiki <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery : erreurs courantes et résolutions</a> ou publiez vos questions sur le <a href="http://go.microsoft.com/fwlink/?LinkId=313628">forum Azure Recovery Services</a>.</LI>
</UL>

</div>

<h2><a id="before" name="before" href="#before"></a>Conditions préalables</h2>
<div class="dev-callout"> 
<P>Avant de commencer, assurez-vous que tout est en place.</P>

<UL>
<LI><b>Compte Azure</b> : vous devez disposer d'un compte Azure. Si vous n'en avez pas, consultez la page <a href="http://aka.ms/try-azure">Version d'évaluation gratuite d'Azure</a>. Pour plus d'informations sur la tarification, consultez la page <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Tarification d'Azure Site Recovery Manager</a>.</LI>
<LI><b>Hyper-V</b> : dans le site local source, vous devez disposer d'au moins un serveur exécutant Windows Server 2012 R2 avec le rôle Hyper-V. Le serveur Hyper-V doit contenir une ou plusieurs machines virtuelles. Les serveurs Hyper-V doivent être connectés à Internet, directement ou via un proxy.</LI>
<LI><b>Machines virtuelles</b> : les machines virtuelles à protéger doivent répondre à la configuration requise d'Azure. Consultez la page <a href="http://go.microsoft.com/fwlink/?LinkId=522287">Prise en charge des machines virtuelles</a>.</LI>

</UL>
<h2><a id="tutorial" name="tutorial" href="#tutorial"></a>Étapes du didacticiel</h2>
Après avoir vérifié la configuration requise, procédez comme suit :
<UL>
<LI><a href="#vault">Étape 1 : créer un coffre</a>. Créez un coffre Azure Site Recovery.</LI>
<LI><a href="#site">Étape 2 : créer un site Hyper-V</a>. Créez un site Hyper-V qui servira de conteneur logique pour tous les serveurs Hyper-V contenant des machines virtuelles à protéger.</LI>
<LI><a href="#download">Étape 3 : préparer les serveurs Hyper-V</a>. Générez une clé d'inscription et téléchargez le fichier d'installation du fournisseur. Exécutez le fichier sur chaque serveur Hyper-V dans le site et sélectionnez la clé pour inscrire le serveur dans le coffre.</LI>
<LI><a href="#resources">Étape 4 : préparer les ressources</a>. Créez un compte Azure Storage pour stocker les machines virtuelles répliquées.</LI>
<LI><a href="#protectiongroup">Étape 5 : créer et configurer des groupes de protection</a>. Créez un groupe de protection et appliquez-lui des paramètres de protection. Les paramètres de protection sont appliqués à chaque machine virtuelle que vous ajoutez au groupe.</LI>
<LI><a href="#enablevirtual">Étape 6 : activer la protection des machines virtuelles</a>. Activez la protection des machines virtuelles en les ajoutant à un groupe de protection.</LI>
<LI><a href="#recovery plans">Étape 7 : tester le déploiement</a>. Exécutez un test de basculement sur une machine virtuelle.</LI>

</UL>




<a name="vault"></a> <h3>Étape 1 : créer un coffre</h3>

1. Connectez-vous au [Portail de gestion](https://manage.windowsazure.com).


2. Développez <b>Services de données</b>, <b>Services de récupération</b>, puis cliquez sur <b>Coffre Site Recovery</b>.


3. Cliquez sur <b>Créer</b>, puis sur <b>Création rapide</b>.
	
4. Dans <b>Nom</b>, entrez un nom convivial permettant d'identifier le coffre.

5. Dans <b>Région</b>, sélectionnez la région géographique du coffre. Pour vérifier les régions prises en charge, consultez les disponibilités géographiques dans la page <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Tarification d'Azure Site Recovery</a>.

6. Cliquez sur <b>Créer un coffre</b>. 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png)

<P>Vérifiez la barre d'état pour vous assurer que le coffre a été créé correctement. Le coffre apparaît comme <b>Actif</b> dans la page Recovery Services principale.</P>

<a name="site"></a> <h3>Étape 2 : créer un site Hyper-V</h3>

1. Dans la page Recovery Services, cliquez sur le coffre pour ouvrir la page Démarrage rapide. Vous pouvez aussi ouvrir cette page à tout moment au moyen de l'icône.

	![Hyper-V site scenario](./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png)

2. Dans la liste déroulante, sélectionnez **Entre un site Hyper-V local et Microsoft Azure**.

	![Hyper-V site scenario](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_SelectScenario.png)

3. Dans **Créer un site Hyper-V**, cliquez sur **Créer un site Hyper-V**. Spécifiez un nom de site et enregistrez-le.

	![Hyper-V site](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_CreateSite2.png)

<a name="download"></a> <h3>Étape 3 : préparer les serveurs Hyper-V</h3>
	

1. Dans **Préparer les serveurs Hyper-V**, cliquez sur **Télécharger une clé d'inscription**.
2. Dans la page **Télécharger une clé d'inscription**, cliquez sur **Télécharger** en regard du site. Téléchargez la clé dans un lieu sûr facilement accessible par le serveur Hyper-V. Une fois générée, la clé est valide pendant 5 jours.

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_DownloadKey2.png)

4. Cliquez sur <b>Télécharger le fournisseur</b> pour obtenir la dernière version.
5. Exécutez le fichier sur chaque serveur Hyper-V à inscrire dans le coffre. Le fichier installe deux composants :
	- **Fournisseur Azure Site Recovery** : gère la communication et l'orchestration entre le serveur Hyper-V et le portail Azure Site Recovery. 
	- **Agent Azure Recovery Services** : gère le transport de données entre les machines virtuelles en cours d'exécution sur le serveur Hyper-V source et Azure Storage. 
6. Dans **Microsoft Update**, vous pouvez opter pour l'installation de mises à jour. Une fois ce paramètre activé, les mises à jour du fournisseur et de l'agent sont installées en fonction de votre stratégie Microsoft Update.

	![Microsoft Updates](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider1.png)

7. Dans **Installation**, indiquez où vous souhaitez installer le fournisseur et l'agent sur le serveur Hyper-V.

	![Install location](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider2.png)

8. Une fois l'installation terminée, poursuivez la procédure pour inscrire le serveur dans le coffre.

	![Installation complete](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider3.png)


9. Dans la page **Connexion Internet**, indiquez la façon dont le fournisseur se connecte à Azure Site Recovery. Sélectionnez <b>Utiliser les paramètres proxy par défaut du système</b> pour utiliser les paramètres de connexion Internet par défaut configurés sur le serveur. 

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider4.png)

9. Dans la page **Paramètres du coffre**, cliquez sur **Parcourir** pour sélectionner le fichier de clé. Spécifiez l'abonnement Azure Site Recovery, le nom du coffre et le site Hyper-V auquel appartient le serveur Hyper-V.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_SelectKey.png)


11. Le serveur est alors inscrit dans le coffre.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider6.png)

11. Une fois l'inscription terminée, les métadonnées du serveur Hyper-V sont récupérées par Azure Site Recovery et le serveur s'affiche sous l'onglet **Sites Hyper-V** dans la page **Serveurs** du coffre.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider7.png)


<h3><a id="resources"></a>Étape 4 : préparer les ressources</h3>


1. Dans la page Démarrage rapide, dans **Préparer les ressources**, sélectionnez **Créer un compte de stockage** pour créer un compte Azure Storage si vous n'en avez pas. La géo-réplication doit être activée pour ce compte. Ce dernier doit se trouver dans la même région que le coffre Azure Site Recovery et être associé au même abonnement.

	![Create storage account](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_CreateResources1.png)



<h3><a id="protectiongroup"></a>Étape 5 : créer et configurer des groupes de protection</h3>

<p>Les groupes de protection rassemblent les machines virtuelles qui ont les mêmes paramètres de protection. Vous appliquez des paramètres de protection à un groupe de protection, et ces paramètres sont appliqués à toutes les machines virtuelles que vous ajoutez au groupe.</p>
1. Dans **Créer et configurer des groupes de protection**, cliquez sur **Créer un groupe de protection**. Si la configuration requise n'est pas respectée, un message est généré et vous pouvez cliquer sur **Afficher les détails** pour obtenir plus d'informations.

2. Sous l'onglet **Groupes de protection**, ajoutez un groupe de protection. Spécifiez un nom, le site Hyper-V source, la cible **Azure**, le nom de votre abonnement à Azure Site Recovery et le compte Azure Storage.

	![Protection group](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_ProtectionGroupCreate.png)


2. Dans **Paramètres de réplication**, conservez les paramètres par défaut.

	![Protection group](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_ProtectionGroup2.png)


<h3><a id="enablevirtual"></a>Étape 6 : activer la protection des machines virtuelles</h3>
<p>. Activez la protection des machines virtuelles en les ajoutant à un groupe de protection.</p>

1. Sous l'onglet <b>Machines</b> du groupe de protection, cliquez sur <b>Ajouter des machines virtuelles aux groupes de protection pour activer la protection</b>.
2. Dans la page **Activer la protection pour les machines virtuelles**, sélectionnez les machines virtuelles à protéger. 

	![Enable virtual machine protection](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_AddVM1.png)

Les tâches d'activation de la protection démarrent. Vous pouvez suivre la progression des tâches sous l'onglet **Tâches**. Lorsque la tâche de finalisation de la protection s'exécute, la machine virtuelle est prête à être basculée. Une fois la protection activée, les machines virtuelles s'affichent dans Azure au terme de la réplication initiale.

Dans Azure Site Recovery, vous pouvez examiner les machines virtuelles protégées dans **Éléments protégés** > **Groupes de Protection** > *protectiongroup_name* > **Machines virtuelles**. 


<h3><a id="recoveryplans"></a>Étape 7 : tester le déploiement</h3>

Testez votre déploiement pour simuler votre mécanisme de basculement et de récupération dans un réseau isolé, sans affecter votre environnement de production. Pour ce faire, exécutez un test de basculement sur une machine virtuelle protégée.

1. **Éléments protégés** > **Groupes de protection** > *protectiongroup_name* > **Machines virtuelles**, sélectionnez la machine virtuelle à basculer, puis cliquez sur **Test de basculement**.
2. Dans la page **Confirmer le test de basculement**, sélectionnez **Aucun**. 

	![Recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_TestFailoverNoNetwork.png)

3. Vous pouvez suivre la progression et l'état du basculement sous l'onglet **Tâche**.
4. Quand le basculement se trouve en phase **Terminer le test**, terminez la vérification comme suit :
	- Après le basculement, examinez la machine virtuelle de réplication dans le portail Azure. Vérifiez que la machine virtuelle démarre correctement.
	- Si vous êtes autorisé à accéder aux machines virtuelles à partir de votre réseau local, vous pouvez initier une connexion Bureau à distance à la machine virtuelle.
	- Cliquez sur **Terminer le test** pour le terminer.
	- Cliquez sur **Notes** pour consigner et enregistrer les éventuelles observations associées au test de basculement.
	- Cliquez sur **Le test de basculement est terminé. Nettoyer l'environnement de test.** pour mettre automatiquement hors tension et supprimer la machine virtuelle de test.

<h2><a id="runtest" name="runtest" href="#runtest"></a>Suivi de l'activité</h2>
<p>Vous pouvez utiliser l'onglet <b>Tâches</b> et le <b>Tableau de bord</b> pour afficher et surveiller les tâches principales effectuées par le coffre Azure Site Recovery, telles que la configuration de la protection d'un cloud, l'activation et la désactivation de la protection d'une machine virtuelle, l'exécution d'un basculement (planifié, non planifié ou test) et la validation d'un basculement non planifié.</p>

<p>Sous l'onglet <b>Tâches</b>, vous pouvez afficher les tâches, accéder aux détails de l'une d'elles ainsi qu'aux erreurs, exécuter des requêtes pour récupérer les tâches qui correspondent à des critères spécifiques, exporter des tâches vers Excel et redémarrer celles qui ont échoué.</p>

<p>Dans le <b>Tableau de bord</b>, vous pouvez télécharger la dernière version des fichiers d'installation du fournisseur et de l'agent, obtenir des informations sur la configuration du coffre, découvrir le nombre de machines virtuelles dont la protection est assurée par le coffre, afficher les tâches récentes, gérer le certificat du coffre et resynchroniser les machines virtuelles.</p>

<p>Pour plus d'informations sur les interactions avec les tâches et le tableau de bord, consultez le <a href="http://go.microsoft.com/fwlink/?LinkId=398534">Guide sur les opérations et la surveillance</a>.</p>

<h2><a id="next" name="next" href="#next"></a>Étapes suivantes</h2>
<UL>
<LI>Pour planifier et déployer Azure Site Recovery dans un environnement de production complet, consultez le <a href="http://go.microsoft.com/fwlink/?LinkId=522087">Guide de planification d'Azure Site Recovery</a> et le <a href=" http://go.microsoft.com/fwlink/?LinkId=522088">Guide de déploiement d'Azure Site Recovery</a>.</LI>
<LI>Pour toute question, visitez le <a href="http://go.microsoft.com/fwlink/?LinkId=313628">forum Azure Recovery Services</a>.</LI> 
</UL>

<!--HONumber=49-->
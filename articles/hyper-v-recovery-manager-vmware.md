<properties 
	pageTitle="Didacticiel : Configuration de la protection entre des sites locaux VMWare" 
	description="InMage dans Azure Site Recovery gère la réplication, le basculement et la protection entre des sites locaux VMWare." 
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

# Didacticiel : Configuration de la protection entre des sites locaux VMWare


<h2><a id="overview" name="overview" href="#overview"></a>Vue d'ensemble</h2>

<p>InMage dans Azure Site Recovery assure une réplication en temps réel entre des sites VMWare locaux. InMage est inclus dans les abonnements au service Azure Site Recovery.</p>




<h2><a id="before" name="before" href="#before"></a>Conditions préalables</h2>
<div class="dev-callout"> 

<UL>
<LI><b>Compte Azure</b> : vous devez disposer d'un compte Azure. Si vous n'en avez pas, consultez la page <a href="http://aka.ms/try-azure">Version d'évaluation gratuite d'Azure</a>. Pour plus d'informations sur la tarification de l'abonnement, consultez la page <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Tarification d'Azure Site Recovery Manager</a>.</LI>
</UL>


<h2><a id="tutorial" name="tutorial" href="#tutorial"></a>Étapes du didacticiel</h2>
<a name="vault"></a> <h3>Étape 1 : créer un coffre et télécharger InMage</h3>

1. Connectez-vous au [Portail de gestion](https://manage.windowsazure.com).


2. Développez <b>Services de données</b>, <b>Services de récupération</b>, puis cliquez sur <b>Coffre Site Recovery</b>.


3. Cliquez sur <b>Créer</b>, puis sur <b>Création rapide</b>.
	
4. Dans <b>Nom</b>, entrez un nom convivial permettant d'identifier le coffre.

5. Dans <b>Région</b>, sélectionnez la région géographique du coffre. Pour vérifier les régions prises en charge, consultez les disponibilités géographiques dans la page <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Tarification d'Azure Site Recovery</a>.

6. Cliquez sur <b>Créer un coffre</b>. 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SRSAN_HvVault.png)

<P>Vérifiez la barre d'état pour vous assurer que le coffre a été créé correctement. Le coffre apparaît comme <b>Actif</b> dans la page Recovery Services principale.</P>

<a name="upload"></a> <h3>Étape 2 : configurer le coffre</h3>


1. Dans la page <b>Recovery Services</b>, cliquez sur le coffre pour ouvrir la page Démarrage rapide. Vous pouvez aussi ouvrir cette page à tout moment au moyen de l'icône.

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SRSAN_QuickStartIcon.png)

2. Dans la liste déroulante, sélectionnez **Entre deux sites VMware locaux**.
3. Téléchargez InMage Scout.
	
	![VMWare](./media/hyper-v-recovery-manager-configure-vault/SRVMWare_SelectScenario.png)

4. Configurez la réplication entre deux sites VMWare en vous reportant à documentation InMage que vous avez téléchargée avec le produit.


<h2><a id="next" name="next" href="#next"></a>Étapes suivantes</h2>
<UL>

<LI>Pour toute question, visitez le <a href="http://go.microsoft.com/fwlink/?LinkId=313628">forum Azure Recovery Services</a>.</LI> 
</UL>

<!--HONumber=49-->
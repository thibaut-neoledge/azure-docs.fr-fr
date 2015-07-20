<properties 
	pageTitle="Déployer entre des sites VMWare locaux" 
	description="InMage Scout dans Azure Site Recovery gère la réplication, le basculement et la récupération entre des sites VMWare locaux." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/29/2015" 
	ms.author="raynew"/>


# Déployer entre des sites VMWare locaux


##Vue d'ensemble

InMage Scout dans Azure Site Recovery assure une réplication en temps réel entre des sites VMWare locaux. InMage Scout est inclus dans les abonnements au service Azure Site Recovery.


## Composants requis

- **Compte Azure** : vous devez avoir un compte [Microsoft Azure](http://azure.microsoft.com/). Vous pouvez commencer avec une [version d'évaluation gratuite](pricing/free-trial/).


##Étape 1 : Créer un coffre et télécharger InMage Scout

1. Connectez-vous au [portail de gestion](https://portal.azure.com).
2. Développez **Services de données** > **Services de récupération**, puis cliquez sur **Coffre Site Recovery**.
3. Cliquez sur **Créer un nouveau** > **Création rapide**.
4. Dans **Nom**, entrez un nom convivial permettant d'identifier le coffre.
5. Dans **Région**, sélectionnez la région géographique du coffre. Pour vérifier les régions prises en charge, consultez la section Disponibilité géographique dans la page [Détails sur la tarification d'Azure Site Recovery](pricing/details/site-recovery/).

<P>Vérifiez la barre d'état pour vous assurer que le coffre a été créé correctement. Le coffre apparaît comme <b>Actif</b> dans la page Recovery Services principale.</P>

##Étape 2 : Configurer le coffre

1. Cliquez sur **Create vault**.
2. Sur la page **Recovery Services**, cliquez sur le coffre pour ouvrir la page Démarrage rapide.
3. Dans la liste déroulante, sélectionnez **Entre deux sites VMWare locaux**.
4. Téléchargez InMage Scout.
5. Configurez la réplication entre deux sites VMWare en vous reportant à documentation InMage Scout que vous avez téléchargée avec le produit.


##Étapes suivantes

Posez vos questions sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).<

<!---HONumber=July15_HO2-->
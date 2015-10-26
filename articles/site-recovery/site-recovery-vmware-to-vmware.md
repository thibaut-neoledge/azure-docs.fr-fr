<properties 
	pageTitle="Configuration de la protection entre des sites locaux VMware" 
	description="Utilisez cet article pour configurer la protection entre deux sites VMware à l’aide d’Azure Site Recovery." 
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
	ms.date="10/07/2015" 
	ms.author="raynew"/>


# Configuration de la protection entre des sites locaux VMware


## Vue d'ensemble

InMage Scout dans Azure Site Recovery assure une réplication en temps réel entre des sites VMware locaux. InMage Scout est inclus dans les abonnements au service Azure Site Recovery.


## Composants requis

- **Compte Azure** : vous devez avoir un compte [Microsoft Azure](http://azure.microsoft.com/). Vous pouvez commencer avec une [version d'évaluation gratuite](pricing/free-trial/).


## Étape 1 : Créer un coffre

1. Connectez-vous au [portail de gestion](https://portal.azure.com).
2. Développez **Services de données** > **Services de récupération**, puis cliquez sur **Coffre Site Recovery**.
3. Cliquez sur **Créer un nouveau** > **Création rapide**.
4. Dans **Nom**, entrez un nom convivial permettant d'identifier le coffre.
5. Dans **Région**, sélectionnez la région géographique du coffre. Pour vérifier les régions prises en charge, consultez la section Disponibilité géographique dans la page [Détails sur la tarification d'Azure Site Recovery](pricing/details/site-recovery/).

Vérifiez la barre d'état pour vous assurer que le coffre a été créé correctement. Le coffre apparaît comme **Actif** sur la page principale Recovery Services.

## Étape 2 : Configurer le coffre et télécharger les composants InMage Scout

1. Cliquez sur **Create vault**.
2. Sur la page **Recovery Services**, cliquez sur le coffre pour ouvrir la page Démarrage rapide.
3. Dans la liste déroulante, sélectionnez **Entre deux sites VMware locaux**.
4. Téléchargez InMage Scout. Les fichiers d'installation pour tous les composants requis figurent dans le fichier zip téléchargé.


## Étape 3 : Installer les mises à jour de composants

Consultez les dernières [mises à jour](#updates). Vous installerez les fichiers de mises à jour dans l'ordre suivant :

1. Serveur RX, s'il existe
2. Serveurs de configuration
3. Serveurs de traitement
3. Serveurs cibles maîtres.
4. Serveurs vContinuum.

Effectuez l’installation suivante :

1. Téléchargez le fichier zip [update](http://download.microsoft.com/download/9/F/D/9FDC6001-1DD0-4C10-BDDD-8A9EBFC57FDF/ASR Scout 8.0.1 Update1.zip). Ce fichier zip contient les fichiers suivants :

	-  RX\_8.0.1.0\_GA\_Update\_1\_3279231\_23Jun15.tar.gz
	-  CX\_Windows\_8.0.1.0\_GA\_Update\_1\_3259146\_23Jun15.exe
	-  UA\_Windows\_8.0.1.0\_GA\_Update\_1\_3259401\_23Jun15.exe
	-  UA\_RHEL6-64\_8.0.1.0\_GA\_Update\_1\_3259401\_23Jun15.tar.gz
	-  vCon\_Windows\_8.0.1.0\_GA\_Update\_1\_3259523\_23Jun15.exe
2. Extrayez les fichiers zip.
2. **Serveur RX** : copiez **RX\_8.0.1.0\_GA\_Update\_1\_3279231\_23Jun15.tar.gz** sur le serveur RX et extrayez-le. Dans le dossier extrait, exécutez **/Install**.
2. **Serveur de configuration/serveur de traitement** : copiez **CX\_Windows\_8.0.1.0\_GA\_Update\_1\_3259146\_23Jun15.exe** sur le serveur de configuration et le serveur de traitement. Double-cliquez pour l'exécuter.
3. **Serveur cible maître Windows** : pour mettre à jour l’agent unifié, copiez **UA\_Windows\_8.0.1.0\_GA\_Update\_1\_3259401\_23Jun15.exe** sur le serveur cible maître. Double-cliquez dessus pour l'exécuter. Notez que l'agent unifié pour Windows ne s’applique pas au serveur source. Il doit être installé uniquement sur le serveur cible maître Windows.
4. **Serveur cible maître Linux** : pour mettre à jour l’agent unifié, copiez **UA\_RHEL6-64\_8.0.1.0\_GA\_Update\_1\_3259401\_23Jun15.tar.gz** sur le serveur cible maître et extrayez-le. Dans le dossier extrait, exécutez **/Install**.
5. **Serveur vContinuum** : copiez **vCon\_Windows\_8.0.1.0\_GA\_Update\_1\_3259523\_23Jun15.exe** sur le serveur vContinuum. Assurez-vous que vous avez fermé l'Assistant vContinuum. Double-cliquez sur le fichier pour l'exécuter.

## Étape 4 : Configurer la réplication
5. Configurez la réplication entre les sites VMware sources et cibles.
6. Pour obtenir des instructions, consultez la documentation InMage Scout téléchargée avec le produit. Vous pouvez également accéder à la documentation comme suit :

	- [Notes de publication](http://download.microsoft.com/download/4/5/0/45008861-4994-4708-BFCD-867736D5621A/InMage_Scout_Standard_Release_Notes.pdf)
	- [Matrice de compatibilité](http://download.microsoft.com/download/C/D/A/CDA1221B-74E4-4CCF-8F77-F785E71423C0/InMage_Scout_Standard_Compatibility_Matrix.pdf)
	- [Guide d’utilisation](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf)
	- [Guide d’utilisation de RX](http://download.microsoft.com/download/A/7/7/A77504C5-D49F-4799-BBC4-4E92158AFBA4/InMage_ScoutCloud_RX_User_Guide_8.0.1.pdf)
	- [Guide d'installation rapide](http://download.microsoft.com/download/6/8/5/685E761C-8493-42EB-854F-FE24B5A6D74B/InMage_Scout_Standard_Quick_Install_Guide.pdf)


## Mises à jour

### ASR Scout 8.0.1 Update 1

Cette dernière mise à jour comprend des correctifs de bogues et de nouvelles fonctionnalités :

- 31 jours de protection gratuite par instance de serveur. Cela vous permet de tester les fonctionnalités ou de procéder à une validation technique.
	- Toutes les opérations sur le serveur, y compris le basculement et la restauration, sont gratuites pendant les 31 premiers jours à partir de l'heure à laquelle un serveur a été protégé à l'aide d'ASR Scout.
	- À compter du 32e jour, chaque serveur protégé sera facturé au tarif d'instance standard pour la protection Azure Site Recovery sur un site appartenant au client.
	- À tout moment, le nombre de serveurs protégés actuellement facturés est disponible sur le tableau de bord du coffre Azure Site Recovery.
- Ajout de la prise en charge de vCLI 5.5 Update 2.
- Ajout de la prise en charge des systèmes d'exploitation Linux sur le serveur source :
	- RHEL 6 Update 6
	- RHEL 5 Update 11
	- CentOS 6 Update 6
	- CentOS 5 Update 11
- Correctifs de bogues pour résoudre les problèmes suivants :
	- L’inscription du coffre échoue pour le serveur de configuration ou le serveur RX.
	- Les volumes de cluster n'apparaissent pas comme prévu lorsque les machines virtuelles en cluster sont à nouveau protégées lors la reprise.
	- La restauration automatique échoue lorsque le serveur cible maître est hébergé sur un autre serveur ESXi que les machines virtuelles de production locales.
	- Les autorisations du fichier de configuration sont modifiées lors de la mise à niveau vers la version 8.0.1, ce qui affecte la protection et les opérations.
	- Le seuil de resynchronisation n'est pas appliqué comme prévu, entraînant un comportement de réplication incohérent.
	- Les paramètres RPO n'apparaissent pas correctement dans l'interface du serveur de configuration. Une valeur de données non compressée apparaît par erreur comme valeur compressée.
	-  L'opération Remove n’effectue pas la suppression prévue dans l'Assistant vContinuum et la réplication n'est pas supprimée de l'interface du serveur de configuration.
	-  Dans l'Assistant vContinuum, le disque est automatiquement désactivé lorsque vous cliquez sur **Details** dans la vue de disque au cours de la protection des machines virtuelles MSCS.
	- Pendant le scénario P2V, les services HP requis comme CIMnotify et CqMgHost ne passent pas à l’état Manual dans la machine virtuelle de restauration, ce qui entraîne un démarrage supplémentaire.
	- La machine virtuelle Linux protégée échoue le serveur cible maître contient plus de 26 disques.
	
## Étapes suivantes

Posez vos questions sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).<

<!---HONumber=Oct15_HO3-->
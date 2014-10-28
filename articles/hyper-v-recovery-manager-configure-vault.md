<properties linkid="configure-hyper-v-recovery-vault" urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="Azure Site Recovery coordinates the replication, failover and recovery of Hyper-V virtual machines located in on-premises VMM clouds to another on-premises site." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection" editor="jimbe" manager="cfreeman" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew"></tags>

# Prise en main d'Azure Site Recovery : protection locale en local

 <div class="dev-callout">

Utilisez Azure Site Recovery pour orchestrer la protection de machines virtuelles sur des serveurs hôtes Hyper-V locaux situées dans les clouds VMM. Plusieurs possibilités de configuration sont possibles :

-   **Protection locale en local** : réplique de machines virtuelles locales sur un autre site local. Vous configurez et activez ainsi les paramètres de protection dans des coffres Azure Site Recovery. Réplique de données de machines virtuelles d'un serveur Hyper-V local à un autre. Découvrez ce scénario sur la page [Prise en main d'Azure Site Recovery : protection locale en local][].
-   **Protection locale sur Azure** : réplique de machines virtuelles locales sur Azure. Vous configurez et activez ainsi les paramètres de protection dans des coffres Azure Site Recovery. Répliques de données de machines virtuelles d'un serveur Hyper-V local à un stockage Azure. Découvrez ce scénario sur la page [Prise en main d'Azure Site Recovery : protection locale sur Azure][]

## <span id="about"></span></a>À propos de ce didacticiel

Utilisez ce didacticiel pour configurer une validation technique rapide d'Azure Site Recovery dans un déploiement local sur Azure. Il utilise les chemins les plus rapides et les paramètres par défaut lorsque cela est possible. Grâce à lui, vous pourrez créer un coffre Azur Site Recovery, installer le fournisseur Azure Site Recovery dans le serveur VMM source, configurer les paramètres de protection des clouds, activer la protection des machines virtuelles et tester votre déploiement.

Pour découvrir comment réaliser un déploiement complet, consultez les pages suivantes :

-   [Plan de déploiement Azure Site Recovery][] : décrit les étapes de planification à réaliser avant d'entreprendre un déploiement complet.
-   [Déploiement d'Azure Site Recovery : protection locale en local][] : fournit les instructions étape par étape pour réaliser un déploiement complet.

Si vous rencontrez des problèmes au cours de ce didacticiel, consultez l'article Wiki [Azure Site Recovery : erreurs courantes et résolutions][], ou publiez vos questions sur le [forum Azure Recovery Services][].

 </div>

## <span id="before"></span></a>Avant de commencer

 <div class="dev-callout">

Avant de commencer ce didacticiel, vérifiez la configuration requise.

### <span id="HVRMPrereq"></span></a>Conditions préalables

-   **Compte Azure** : vous devez disposer d'un compte Azure. Si vous n'en avez pas, consultez la page [Version d'évaluation gratuite d'Azure][]. Pour plus d'informations sur la tarification, consultez la page [Détails des prix d'Azure Site Recovery Manager][].
-   **Serveur VMM** : vous devez disposer d'au moins un serveur VMM s'exécutant sous System Center 2012 SP1 ou System Center 2012 R2.
-   **Clouds VMM** : vous devez disposer d'au moins un cloud sur le serveur VMM source à protéger et d'un cloud sur le serveur VMM de destination. Si vous disposez d'un seul serveur VMM, vous devez configurer deux clouds. Le cloud principal à protéger doit comporter les éléments suivants :
    -   un ou plusieurs groupes hôtes VMM ;
    -   un ou plusieurs serveurs hôtes Hyper-V ou clusters dans chaque groupe hôte.
    -   une ou plusieurs machines virtuelles situées sur le serveur Hyper-V source dans le cloud.

## <span id="tutorial"></span></a>Étapes du didacticiel

Après avoir vérifié la configuration requise, procédez comme suit :

-   [Étape 1 : création d'un coffre][] : créez un coffre Azure Site Recovery.
-   [Étape 2 : installation de l'application fournisseur][] : générez une clé d'enregistrement et exécutez l'application fournisseur Microsoft Azure Site Recovery sur le serveur VMM. Cela permet d'installer le fournisseur et d'enregistrer le serveur VMM dans le coffre.
-   [Étape 3 : configuration de la protection des clouds][] : configurez les paramètres de protection des clouds VMM.
-   <a href="#networkmapping">Étape 5 : configuration du mappage réseau : vous pouvez éventuellement configurer le mappage réseau pour mapper les réseaux de machines virtuelles sources vers les réseaux de machines virtuelles cibles.
-   [Étape 6 : configuration du mappage de stockage][] : vous pouvez éventuellement configurer le mappage de stockage pour mapper des classifications de stockage sur le serveur de machines virtuelles source vers des classifications de stockage sur le serveur cible.
-   [Étape 7 : activation de la protection pour les machines virtuelles][] : activez la protection des machines virtuelles situées dans les clouds VMM protégés
-   [Étape 8 : configuration et exécution de plans de récupération][] : créez un plan de récupération et exécutez un test de basculement pour le plan.

<a name="vault"></a>

## Étape 1 : création d'un coffre

1.  Connectez-vous au [portail de gestion][].

2.  Développez **Data Services**, puis **Recovery Services** et cliquez sur **Site Recovery Vault**.

3.  Cliquez sur **Create New**, puis sur **Quick Create**.

4.  Dans **Name**, entrez un nom convivial pour identifier le coffre.

5.  Dans **Region**, sélectionnez la région géographique du coffre. Pour découvrir les régions prises en charge, référez-vous à la disponibilité géographique de la page [Détails des prix d'Azure Site Recovery][]

6.  Cliquez sur **Create vault**.

    ![Nouveau coffre][]

Vérifiez la barre d'état pour vous assurer que le coffre a été créé correctement. Le coffre apparaît comme **Actif** sur la page principale Recovery Services.

<a name="upload"></a>

## Étape 2 : configuration du coffre

1.  Sur la page **Recovery Services**, cliquez sur le coffre pour ouvrir la page Démarrage rapide. Démarrage rapide peut également être ouvert à tout moment en utilisant l'icône.

    ![Icône Quick Start][]

2.  Dans la liste déroulante, sélectionnez **Between two on-premises Hyper-V sites**.
3.  Dans **Prepare VMM Servers**, cliquez sur le fichier **Generate registration key**. Une fois générée, la clé est valide pendant 5 jours. Copiez le fichier sur le serveur VMM. Vous en aurez besoin lors de la configuration du fournisseur.

    ![Clé d'enregistrement][]

<a name="download"></a>

## Étape 3 : installation du fournisseur Azure Site Recovery

1.  Sur la page **Quick Start**, dans **Prepare VMM servers**, cliquez sur **Download Microsoft Azure Site Recovery Provider for installation on VMM servers** pour obtenir la dernière version du fichier d'installation du fournisseur.

2.  Exécutez ce fichier sur les serveurs VMM source et cible.

3.  Dans **Pre-requirements Check**, choisissez d'arrêter le service VMM pour lancer l'installation du fournisseur. Le service s'arrête et redémarre automatiquement une fois l'installation terminée.

    ![Configuration requise][]

4.  Dans **Microsoft Update**, vous pouvez opter pour l'installation de mises à jour. En activant ce paramètre, les mises à jour du fournisseur seront installées en fonction de votre stratégie Microsoft Update.

    ![Mises à jour Microsoft][]

Une fois le fournisseur installé, poursuivez l'installation afin d'enregistrer le serveur dans le coffre.

1.  Sur la page de connexion à Internet, indiquez la façon dont le fournisseur s'exécutant sur le serveur VMM se connecte à Internet, puis sélectionnez **Use default system proxy settings** pour utiliser les paramètres de connexion à Internet par défaut configurés sur le serveur.

    ![Paramètres Internet][]

2.  Dans **Registration Key**, indiquez que vous téléchargez depuis Azure Site Recovery et que vous copiez sur le serveur VMM.
3.  Dans **Vault name**, vérifiez le nom du coffre dans lequel le serveur est enregistré.
4.  Dans **Server name**, entrez un nom convivial pour identifier le serveur VMM dans le coffre.

    ![Enregistrement du serveur][]

5.  Dans la synchronisation **Initial cloud metadata**, indiquez si vous souhaitez synchroniser les métadonnées de tous les clouds sur le serveur VMM à l'aide du coffre. Cette action se produit une seule fois sur chaque serveur. Si vous ne souhaitez pas synchroniser tous les clouds, vous pouvez désactiver ce paramètre et synchroniser individuellement chaque cloud via les propriétés du cloud de la console VMM.

6.  Dans **Data Encryption**, vous générez un certificat qui est utilisé pour chiffrer les données protégées dans Azure.
    Cette option ne s'applique pas si vous répliquez d'un site local à un autre.

    ![Enregistrement du serveur][1]

7.  Cliquez sur **Register** pour terminer le processus. Une fois l'enregistrement terminé, les métadonnées du serveur VMM sont extraites par Azure Site Recovery. Le serveur figure dans l'édition de l'onglet **Resources** de la page **Servers** du coffre.

## <span id="clouds"></span></a>Étape 4 : configuration des paramètres de protection de cloud

Une fois les serveurs VMM inscrits, vous pouvez configurer les paramètres de protection de cloud. L'option **Synchronize cloud data with the vault** s'active lors de l'installation du fournisseur de manière à ce que tous les clouds du serveur VMM apparaîtrent dans l'onglet **Protected Items** du coffre.

![Cloud publié][]

1.  Sur la page Démarrage rapide, cliquez sur **Set up protection for VMM clouds**.
2.  Sous l'onglet **Protected Items**, sélectionnez le cloud que vous souhaitez configurer, puis accédez à l'onglet **Configuration**. Notez les points suivants :
3.  Dans **Target**, sélectionnez **VMM**.
4.  Dans **Target Location**, spécifiez le serveur VMM local qui gère le cloud à utiliser pour la récupération.
5.  Dans **Target Cloud**, spécifiez le cloud cible à utiliser pour le basculement des machines virtuelles du cloud source. Notez les points suivants :

    -   Nous vous recommandons de choisir un cloud cible qui remplisse les conditions requises pour la récupération des machines virtuelles que vous prévoyez de protéger.
    -   Un cloud ne peut appartenir qu'à une seule paire de clouds, que ce soit en qualité de cloud principal ou de cloud cible.

6.  Dans **Copy frequency**, laissez le paramètre par défaut. Cette valeur indique la fréquence de synchronisation des données entre les emplacements source et cible. Elle n'est utile que lorsque l'hôte Hyper-V s'exécute sur Windows Server 2012 R2. Les autres serveurs utilisent un paramétrage par défaut de cinq minutes.
7.  Dans **Additional recovery points**, conservez le paramètre par défaut. Cette valeur indique si vous souhaitez créer des points de récupération supplémentaires. La valeur par défaut de zéro indique que seul le dernier point principal de récupération d'une machine virtuelle est stocké sur un serveur hôte de réplication.
8.  Dans **Frequency of application-consistent snapshots**, laissez le paramètre par défaut. Cette valeur indique la fréquence de création de captures instantanées. Ces dernières utilisent le service VSS (Volume Shadow Copy Service) pour s'assurer que les applications sont dans un état cohérent lors de la prise des captures instantanées. Si vous voulez définir cette valeur pour la procédure pas à pas du didacticiel, assurez-vous qu'elle est inférieure au nombre de points de récupération supplémentaires que vous configurez.
    ![Configure protection settings][]
9.  Dans **Data transfer compressed**, indiquez si les données répliquées transférées doivent être compressées.
10. Dans **Authentication**, indiquez le mode d'authentification du trafic entre les serveurs hôtes Hyper-V principaux et de récupération. À moins de disposer d'un environnement Kerberos configuré et opérationnel, nous vous recommandons de choisir HTTPS pour les besoins de cette procédure pas à pas. Azure Site Recovery configurera automatiquement des certificats pour l'authentification HTTPS. Aucune configuration manuelle n'est nécessaire. Notez que ce paramètre n'est utile que pour les serveurs hôtes Hyper-V s'exécutant sur Windows Server 2012 R2.
11. Dans **Port**, laissez le paramètre par défaut. Cette valeur définit le numéro de port sur lequel les ordinateurs hôtes Hyper-V source et cible écoutent le trafic de réplication.
12. Dans **Replication method**, indiquez comment sera traitée la réplication de données initiale entre les emplacements source et cible avant que la réplication normale ne démarre.

    -   **Over network** : la copie de données sur le réseau peut demander du temps et des ressources. Nous vous recommandons d'utiliser cette option si le cloud contient des machines virtuelles dotées de disques durs virtuels relativement petits et si le serveur VMM principal est connecté au serveur VMM secondaire sur une connexion à large bande passante. Vous pouvez opter pour un démarrage immédiat de la copie ou sélectionner une heure. Si vous utilisez la réplication réseau, nous vous recommandons de la planifier sur des heures creuses.
    -   **Offline** : cette méthode précise que la réplication initiale s'exécute à l'aide d'un support externe. Elle est indiquée si vous voulez éviter une dégradation des performances réseau ou pour les emplacements éloignés géographiquement. Pour utiliser cette méthode, il convient de spécifier l'emplacement d'exportation sur le cloud source et l'emplacement d'importation sur le cloud cible. Lorsque vous activez la protection pour une machine virtuelle, le disque dur virtuel est copié vers l'emplacement d'exportation spécifié. Vous l'envoyez vers le site cible et le copiez vers l'emplacement d'importation. Le système copie les informations importées vers les machines virtuelles de réplication. Pour connaître les détails de la configuration requise pour la réplication hors ligne, consultez l'[Étape 3 : configuration des paramètres de protection des clouds VMM][] du guide de déploiement.

13. Sélectionnez **Delete Replica Virtual Machine** pour indiquer que la machine virtuelle de réplication doit être supprimé si vous cessez de protéger la machine virtuelle en sélectionnant l'option **Delete protection for the virtual machine** dans l'onglet Virtual Machines des propriétés du cloud. En désactivant ce paramètre lorsque vous retirez la protection, la machine virtuelle est supprimée d'Azure Site Recovery, les paramètres Site Recovery de la machine virtuelle sont supprimés de la console VMM, et la réplique est supprimée.
    ![Configure protection settings][2]

Suite à l'enregistrement des paramètres, une tâche est créée et peut être surveillée sous l'onglet **Jobs**. Tous les serveurs hôte Hyper-V du cloud VMM source sont configurés pour la réplication. Les paramètres de cloud peuvent être modifiés sous l'onglet **Configure**. Pour modifier l'emplacement cible ou le cloud cible, vous devez supprimer la configuration du cloud, puis reconfigurer ce dernier.

## <span id="networkmapping"></span></a>Étape 5 : configuration du mappage réseau

Ce tutoriel décrit le chemin le plus simple pour déployer Azure Site Recovery dans un environnement de test. Pour configurer le mappage réseau via ce didacticiel, consultez la rubrique [Préparation du mappage réseau][] du guide de planification. Pour configurer le mappage, suivez la procédure de [configuration du mappage réseau][] présentée dans le guide de déploiement.

## <span id="storagemapping"></span></a>Étape 6 : configuration du mappage de stockage

Ce tutoriel décrit le chemin le plus simple pour déployer Azure Site Recovery dans un environnement de test. Pour configurer le mappage de stockage via ce didacticiel, suivez la procédure de [configuration du mappage de stockage][] présentée dans le guide de déploiement.

## <span id="enablevirtual"></span></a>Étape 7 : activation de la protection d'une machine virtuelle

Dès lors que les serveurs, les clouds et les réseaux ont été configurés correctement, vous pouvez activer la protection pour les machines virtuelles du cloud.

1.  Dans l'onglet **Virtual Machines** du cloud dans lequel se trouve la machine virtuelle, cliquez sur **Enable protection**, puis sélectionnez **Add virtual machines**.
2.  Dans la liste des machines virtuelles du cloud, sélectionnez celle que vous voulez protéger.

![Activation de la protection d'une machine virtuelle][]

Vous pouvez consulter la progression de l'action d'activation de la protection, ainsi que la réplication initiale, sous l'onglet \*\*Jobs\*\*. Lorsque la tâche de finalisation de la protection s'exécute, la machine virtuelle est prête à être basculée. Une fois la protection activée et les machines virtuelles répliquées, celles-ci sont affichées dans Azure.

![Tâche de protection de la machine virtuelle][]

## <span id="recoveryplans"></span></a>Étape 8 : test du déploiement

Pour tester votre déploiement, vous pouvez exécuter un test de basculement pour une seule machine virtuelle, ou créer un plan de récupération comportant plusieurs machines virtuelles et exécuter sur lui un test de basculement. Il simule votre mécanisme de basculement et de récupération dans un réseau isolé.

-   Pour créer un plan de récupération, consultez la page [Création et personnalisation de plans de récupération : protection locale sur Azure][].
-   Pour exécuter un test de basculement, consultez la page [Test de déploiement local en local][].

### <span id="runtest"></span></a>Suivi de l'activité

Vous pouvez utiliser l'onglet **Jobs** et **Dashboard** pour afficher et surveiller les tâches principales effectuées par le coffre Azure Site Recovery, telles que la configuration de la protection d'un cloud, l'activation et la désactivation de la protection d'une machine virtuelle, l'exécution d'un basculement (planifié, non planifié ou test) et la validation d'un basculement non planifié.

Depuis l'onglet **Jobs**, vous pouvez afficher les tâches, accéder aux détails de l'une d'entre elles ainsi qu'aux erreurs précédemment rencontrées lors de l'exécution de celle-ci, exécuter des requêtes de tâches pour récupérer les tâches qui correspondent à des critères spécifiques, exporter des tâches sur Excel et redémarrer celles qui ont échoué.

Depuis **Dashboard**, vous pouvez télécharger la dernière version des fichiers d'installation fournisseur et agent, obtenir des informations sur la configuration du coffre, découvrir le nombre de machines virtuelles dont la protection est assurée par le coffre, afficher les tâches récentes, gérer le certificat de coffre et resynchroniser des machines virtuelles.

Pour plus d'informations sur les interactions avec les tâches et le tableau de bord, consultez le [Guide sur les opérations et la surveillance][].

## <span id="next"></span></a>Étapes suivantes

-   Pour planifier et déployer Azure Site Recovery dans un environnement de production complet, consultez le [Guide de planification d'Azure Site Recovery][Plan de déploiement Azure Site Recovery] et le [Guide de déploiement d'Azure Site Recovery][Déploiement d'Azure Site Recovery : protection locale en local].
-   Pour toute question, visitez le [forum Azure Recovery Services][].

 </div>

  [Prise en main d'Azure Site Recovery : protection locale en local]: http://go.microsoft.com/fwlink/?LinkId=398765
  [Prise en main d'Azure Site Recovery : protection locale sur Azure]: http://go.microsoft.com/fwlink/?LinkId=398764
  [Plan de déploiement Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=321294
  [Déploiement d'Azure Site Recovery : protection locale en local]: http://go.microsoft.com/fwlink/?LinkId=321295
  [Azure Site Recovery : erreurs courantes et résolutions]: http://go.microsoft.com/fwlink/?LinkId=389879
  [forum Azure Recovery Services]: http://go.microsoft.com/fwlink/?LinkId=313628
  [Version d'évaluation gratuite d'Azure]: http://aka.ms/try-azure
  [Détails des prix d'Azure Site Recovery Manager]: http://go.microsoft.com/fwlink/?LinkId=378268
  [Étape 1 : création d'un coffre]: #vault
  [Étape 2 : installation de l'application fournisseur]: #download
  [Étape 3 : configuration de la protection des clouds]: #clouds
  [Étape 6 : configuration du mappage de stockage]: #storagemapping
  [Étape 7 : activation de la protection pour les machines virtuelles]: #enablevirtual
  [Étape 8 : configuration et exécution de plans de récupération]: #recovery%20plans
  [portail de gestion]: https://manage.windowsazure.com
  [Détails des prix d'Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=389880
  [Nouveau coffre]: ./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png
  [Icône Quick Start]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png
  [Clé d'enregistrement]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2ERegisterKey.png
  [Configuration requise]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png
  [Mises à jour Microsoft]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png
  [Paramètres Internet]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png
  [Enregistrement du serveur]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png
  [1]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png
  [Cloud publié]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png
  [Configure protection settings]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2E.png
  [Étape 3 : configuration des paramètres de protection des clouds VMM]: http://go.microsoft.com/fwlink/?LinkId=323469
  [2]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2ERep.png
  [Préparation du mappage réseau]: http://go.microsoft.com/fwlink/?LinkId=324817
  [configuration du mappage réseau]: http://go.microsoft.com/fwlink/?LinkId=402534
  [configuration du mappage de stockage]: http://go.microsoft.com/fwlink/?LinkId=402535
  [Activation de la protection d'une machine virtuelle]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableProtectionVM.png
  [Tâche de protection de la machine virtuelle]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
  [Création et personnalisation de plans de récupération : protection locale sur Azure]: http://go.microsoft.com/fwlink/?LinkId=511492
  [Test de déploiement local en local]: http://go.microsoft.com/fwlink/?LinkId=511493
  [Guide sur les opérations et la surveillance]: http://go.microsoft.com/fwlink/?LinkId=398534

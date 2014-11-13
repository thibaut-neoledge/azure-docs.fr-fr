<properties urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Prise en main d'Azure&nbsp;Site&nbsp;Recovery&nbsp;: protection d'un serveur local vers Azure" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="Azure Site Recovery coordonne la r&eacute;plication, le basculement et la r&eacute;cup&eacute;ration de machines virtuelles Hyper-V situ&eacute;es dans des clouds VMM locaux sur Azure." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Prise en main d'Azure&nbsp;Site&nbsp;Recovery&nbsp;: protection d'un serveur local vers Azure" editor="jimbe" manager="johndaw" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew" />

# Prise en main d'Azure Site Recovery : protection d'un serveur local vers Azure

<div class="dev-callout">

Utilisez Azure Site Recovery pour orchestrer la protection de machines virtuelles sur des serveurs hôtes Hyper-V locaux situées dans les clouds VMM. Plusieurs possibilités de configuration sont possibles :

-   **Protection locale sur Azure** : réplique de machines virtuelles locales sur Azure. Vous pouvez configurer et activer les paramètres de protection dans des coffres Azure Site Recovery. Répliques de données de machines virtuelles d'un serveur Hyper-V local à un stockage Azure.
-   **Protection locale en local** : réplique de machines virtuelles locales sur un autre site local. Vous pouvez configurer et activer les paramètres de protection dans des coffres Azure Site Recovery. Réplique de données de machines virtuelles d'un serveur Hyper-V local à un autre. Découvrez ce scénario sur la page [Prise en main d'Azure Site Recovery : protection locale en local][Prise en main d'Azure Site Recovery : protection locale en local].

## <span id="about"></span></a>À propos de ce didacticiel

Utilisez ce didacticiel pour configurer une validation technique rapide d'Azure Site Recovery dans un déploiement local sur Azure. Il utilise les chemins les plus rapides et les paramètres par défaut lorsque cela est possible. Grâce à lui, vous pourrez créer un coffre Azure Site Recovery, installer le fournisseur Azure Site Recovery dans le serveur VMM source, installer l'agent Azure Recovery Services sur des serveurs hôtes Hyper-V dans les clouds VMM, configurer les paramètres de protection des clouds, activer la protection des machines virtuelles et tester votre déploiement.

Pour découvrir comment réaliser un déploiement complet, consultez les pages suivantes :

-   [Plan de déploiement Azure Site Recovery][Plan de déploiement Azure Site Recovery] : décrit les étapes de planification requises avant d'entreprendre un déploiement complet.
-   [Déploiement d'Azure Site Recovery : protection d'un serveur local vers Azure][Déploiement d'Azure Site Recovery : protection d'un serveur local vers Azure] : fournit des instructions pas à pas pour un déploiement complet.

Si vous rencontrez des problèmes au cours de ce didacticiel, consultez l'article Wiki [Azure Site Recovery : erreurs courantes et résolutions][Azure Site Recovery : erreurs courantes et résolutions], ou publiez vos questions sur le [forum Azure Recovery Services][forum Azure Recovery Services].

</div>

## <span id="before"></span></a>Avant de commencer

<div class="dev-callout">

Avant de commencer ce didacticiel, vérifiez la configuration requise.

### <span id="HVRMPrereq"></span></a>Conditions préalables

-   **Compte Azure** : vous devez disposer d'un compte Azure. Si vous n'en avez pas, consultez la page [Version d'évaluation gratuite d'Azure][Version d'évaluation gratuite d'Azure]. Pour plus d'informations sur la tarification, consultez la page [Détails des prix d'Azure Site Recovery Manager][Détails des prix d'Azure Site Recovery Manager].
-   **Compte de stockage Azure** : vous avez besoin d'un compte de stockage Azure pour stocker les données répliquées dans Azure. La géo-réplication doit être activée pour ce compte. Ce dernier doit se trouver dans la même région que le service Azure Site Recovery et être associé au même abonnement. Pour en savoir plus sur la configuration du stockage Azure, consultez la page [Introduction à Microsoft Azure Storage][Introduction à Microsoft Azure Storage].
-   **Serveur VMM** : serveur VMM exécuté sur System Center 2012 R2.
-   **Clouds VMM** : un cloud au moins sur le serveur VMM. Le cloud doit contenir les éléments suivants :
    -   un ou plusieurs groupes hôtes VMM ;
    -   un ou plusieurs serveurs hôtes Hyper-V ou clusters dans chaque groupe hôte.
    -   une ou plusieurs machines virtuelles situées sur le serveur Hyper-V source dans le cloud. Les machines virtuelles doivent être de la génération 1.
-   **Machine virtuelle** : vous avez besoin de machines virtuelles qui satisfont aux exigences liées à Azure. Consultez la page [Configuration requise et prise en charge][Configuration requise et prise en charge] dans le guide de planification.
-   Pour une liste complète des conditions de prise en charge des machines virtuelles pour le basculement vers Azure, lisez

## <span id="tutorial"></span></a>Étapes du didacticiel

Après avoir vérifié la configuration requise, procédez comme suit :

-   [Étape 1 : Création d'un coffre][Étape 1 : Création d'un coffre] : créez un coffre Azure Site Recovery.
-   [Étape 2 : Installation de l'application fournisseur][Étape 2 : Installation de l'application fournisseur] : générez une clé d'enregistrement et exécutez l'application fournisseur Microsoft Azure Site Recovery sur le serveur VMM. Cela permet d'installer le fournisseur et d'enregistrer le serveur VMM dans le coffre.
-   [Étape 3 : Ajout d'un compte de stockage Azure][Étape 3 : Ajout d'un compte de stockage Azure] : si vous n'avez pas de compte, créez-en un.
-   [Étape 4 : Installation de l'application de l'agent][Étape 4 : Installation de l'application de l'agent] : installez l'agent Microsoft Azure Recovery Services sur chaque hôte Hyper-V.
-   [Étape 5 : Configuration de la protection des clouds][Étape 5 : Configuration de la protection des clouds] : configurez les paramètres de protection des clouds VMM.
-   [Étape 6 : Configuration du mappage réseau][Étape 6 : Configuration du mappage réseau] : vous pouvez éventuellement configurer le mappage réseau de manière à mapper les réseaux de machines virtuelles sources vers les réseaux Azure cibles.
-   [Étape 7 : Activation de la protection pour les machines virtuelles][Étape 7 : Activation de la protection pour les machines virtuelles] : activez la protection pour les machines virtuelles situées dans des clouds VMM protégés.
-   [Étape 8 : Test du déploiement][Étape 8 : Test du déploiement] : vous pouvez exécuter un test de basculement pour une seule machine virtuelle, ou créer un plan de récupération et exécuter un test de basculement pour ce dernier.

<a name="vault"></a>

## Étape 1 : Création d'un coffre

</p>
1.  Connectez-vous au [portail de gestion][portail de gestion].

2.  Développez **Data Services**, puis **Recovery Services** et cliquez sur **Site Recovery Vault**.

3.  Cliquez sur **Create New**, puis sur **Quick Create**.

4.  Dans **Name**, entrez un nom convivial pour identifier le coffre.

5.  Dans **Region**, sélectionnez la région géographique du coffre. Les régions géographiques disponibles sont les suivantes : Asie orientale, Europe de l'Ouest, Ouest des États-Unis, Est des États-Unis, Europe du Nord et Asie du Sud-Est.
6.  Cliquez sur **Create vault**.

    ![Nouveau coffre][Nouveau coffre]

Vérifiez la barre d'état pour vous assurer que le coffre a été créé correctement. Le coffre apparaît comme **Actif** sur la page principale Recovery Services.

<a name="download"></a>

## Étape 2 : Génération d'une clé d'enregistrement et installation du fournisseur Azure Site Recovery

</p>
1.  Sur la page **Recovery Services**, cliquez sur le coffre pour ouvrir la page Démarrage rapide. Démarrage rapide peut également être ouvert à tout moment en utilisant l'icône.

    ![Icône Quick Start][Icône Quick Start]

2.  Dans la liste déroulante, sélectionnez **Entre un site Hyper-V local et Microsoft Azure**.
3.  Dans **Prepare VMM Servers**, cliquez sur le fichier **Generate registration key**. Une fois générée, la clé est valide pendant 5 jours. Copiez le fichier sur le serveur VMM. Vous en aurez besoin lors de la configuration du fournisseur.

    ![Clé d'enregistrement][Clé d'enregistrement]

4.  Sur la page **Quick Start**, dans **Prepare VMM servers**, cliquez sur **Download Microsoft Azure Site Recovery Provider for installation on VMM servers** pour obtenir la dernière version du fichier d'installation du fournisseur.

5.  Exécutez ce fichier sur le serveur VMM source.

6.  Dans **Pre-requirements Check**, choisissez d'arrêter le service VMM pour lancer l'installation du fournisseur. Le service s'arrête et redémarre automatiquement une fois l'installation terminée.

    ![Configuration requise][Configuration requise]

7.  Dans **Microsoft Update**, vous pouvez opter pour l'installation de mises à jour. En activant ce paramètre, les mises à jour du fournisseur seront installées en fonction de votre stratégie Microsoft Update.

    ![Mises à jour Microsoft][Mises à jour Microsoft]

Une fois le fournisseur installé, poursuivez l'installation afin d'enregistrer le serveur dans le coffre.

1.  Sur la page **Connexion Internet**, indiquez la façon dont le fournisseur exécuté sur le serveur VMM se connecte à Internet. Sélectionnez **Utiliser les paramètres proxy par défaut du système** pour utiliser les paramètres de connexion Internet par défaut configurés sur le serveur.

    ![Paramètres Internet][Paramètres Internet]

2.  Dans **Registration Key**, indiquez que vous téléchargez depuis Azure Site Recovery et que vous copiez sur le serveur VMM.
3.  Dans **Vault name**, vérifiez le nom du coffre dans lequel le serveur est enregistré.
4.  Dans **Server name**, entrez un nom convivial pour identifier le serveur VMM dans le coffre.

    ![Enregistrement du serveur][Enregistrement du serveur]

5.  Dans la synchronisation **Initial cloud metadata**, indiquez si vous souhaitez synchroniser les métadonnées de tous les clouds sur le serveur VMM à l'aide du coffre. Cette action se produit une seule fois sur chaque serveur. Si vous ne souhaitez pas synchroniser tous les clouds, vous pouvez désactiver ce paramètre et synchroniser individuellement chaque cloud via les propriétés du cloud de la console VMM.

6.  Dans **Chiffrement des données**, vous spécifiez un emplacement pour enregistrer un certificat SSL généré automatiquement pour le chiffrement de données. Ce certificat est utilisé si vous activez le chiffrement de données pour un cloud protégé par Azure dans le portail Azure Site Recovery. Conservez ce certificat en sécurité. Lorsque vous exécuterez un basculement vers Azure, vous le sélectionnerez pour déchiffrer les données chiffrées.
    Cette option n'est pas pertinente si vous effectuez une réplication d'un site local vers un autre.

    ![Enregistrement du serveur][1]

7.  Cliquez sur **Register** pour terminer le processus. Une fois l'enregistrement terminé, les métadonnées du serveur VMM sont extraites par Azure Site Recovery. Le serveur figure dans l'édition de l'onglet **Resources** de la page **Servers** du coffre.

## <span id="storage"></span></a>Étape 3 : Création d'un compte de stockage Azure

Si vous ne possédez pas de compte de stockage Azure, cliquez sur **Ajouter un compte de stockage Azure**. La géo-réplication doit être activée pour ce compte. Ce dernier doit se trouver dans la même région que le service Azure Site Recovery et être associé au même abonnement.

Utilisez ce didacticiel pour configurer une validation technique rapide d'Azure Site Recovery dans un déploiement local sur Azure. Il utilise les chemins les plus rapides et les paramètres par défaut lorsque cela est possible. Grâce à lui, vous pourrez créer un coffre Azure Site Recovery, installer le fournisseur Azure Site Recovery dans le serveur VMM source, installer l'agent Azure Recovery Services sur des serveurs hôtes Hyper-V dans les clouds VMM, configurer les paramètres de protection des clouds, activer la protection des machines virtuelles et tester votre déploiement.

![Compte de stockage][Compte de stockage]

## <span id="agent"></span></a>Étape 4 : Installation de l'agent Azure Recovery Services sur les hôtes Hyper-V

Installez l'agent Azure Recovery Services sur chaque serveur hôte Hyper-V situé dans les clouds VMM que vous souhaitez protéger.

1.  Sur la page Démarrage rapide, cliquez sur **Télécharger l'agent des services Azure Site Recovery et l'installer sur les hôtes** afin d'obtenir la dernière version du fichier d'installation de l'agent.

    ![Install Recovery Services Agent][Install Recovery Services Agent]

2.  Exécutez le fichier d'installation sur chaque serveur hôte Hyper-V situé dans les clouds VMM que vous souhaitez protéger.
3.  Sur la page **Vérification de la configuration requise**, cliquez sur **Suivant**. Tous les éléments manquants de la configuration requise sont automatiquement installés.

    ![Prerequisites Recovery Services Agent][Prerequisites Recovery Services Agent]

4.  Sur la page **Paramètres d'installation**, indiquez où vous voulez installer l'agent et sélectionnez l'emplacement du cache dans lequel les métadonnées de sauvegarde seront installées. Cliquez ensuite sur **Installer**.

## <span id="clouds"></span></a>Étape 5 : Configuration des paramètres de protection de cloud

Une fois les serveurs VMM inscrits, vous pouvez configurer les paramètres de protection de cloud. L'option **Synchroniser les données du cloud avec le coffre** s'active lors de l'installation du fournisseur de manière à ce que tous les clouds du serveur VMM apparaissent dans l'onglet **Éléments protégés** du coffre.

![Cloud publié][Cloud publié]

1.  Sur la page Démarrage rapide, cliquez sur **Set up protection for VMM clouds**.

2.  Sous l'onglet **Éléments protégés**, cliquez sur le cloud que vous souhaitez configurer, puis accédez à l'onglet **Configuration**.
3.  Dans la zone **Cible**, sélectionnez **Microsoft Azure**.
4.  Dans la zone **Compte de stockage**, sélectionnez le stockage Azure dans lequel vous voulez stocker les machines virtuelles.
5.  En regard de **Chiffrer les données stockées**, cliquez sur **Désactivé**. Ce paramètre indique que les données doivent être répliquées avec chiffrement entre le site local et Azure.
6.  Dans **Copy frequency**, laissez le paramètre par défaut. Cette valeur indique la fréquence de synchronisation des données entre les emplacements source et cible.
7.  Dans la zone **Conserver les points de récupération pour**, conservez le paramètre par défaut. Avec une valeur par défaut de zéro, seul le dernier point de récupération d'une machine virtuelle principale est stocké sur un serveur hôte de réplication.
8.  Dans **Frequency of application-consistent snapshots**, laissez le paramètre par défaut. Cette valeur indique la fréquence de création de captures instantanées. Ces dernières utilisent le service VSS (Volume Shadow Copy Service) pour s'assurer que les applications sont dans un état cohérent lors de la prise des captures instantanées. Si vous définissez une valeur, assurez-vous qu'elle est inférieure au nombre de points de récupération supplémentaires que vous configurez.
9.  Dans la zone **Heure de démarrage de la réplication**, indiquez l'heure à laquelle la réplication initiale des données dans Azure doit démarrer. Le fuseau horaire du serveur hôte Hyper-V est utilisé à cet effet. Nous vous recommandons de planifier la réplication initiale sur des heures creuses.

    ![Cloud replication settings][Cloud replication settings]

Suite à l'enregistrement des paramètres, une tâche est créée et peut être surveillée sous l'onglet **Jobs**. Tous les serveurs hôte Hyper-V du cloud VMM source sont configurés pour la réplication. Les paramètres du cloud peuvent être modifiés sous l'onglet **Configurer**. Pour modifier l'emplacement ou le stockage cible, vous devez supprimer la configuration du cloud, puis reconfigurer ce dernier. Notez que si vous modifiez le compte de stockage, la modification n'est appliquée que pour les machines virtuelles qui sont activées pour la protection après la modification du compte. Les machines virtuelles existantes ne sont pas migrées vers le nouveau compte de stockage.

## <span id="networkmapping"></span></a>Étape 6 : Configuration du mappage réseau

Ce tutoriel décrit le chemin le plus simple pour déployer Azure Site Recovery dans un environnement de test. Pour configurer le mappage réseau via ce didacticiel, consultez la rubrique [Préparation du mappage réseau][Préparation du mappage réseau] du guide de planification. Pour configurer le mappage, suivez la procédure de [configuration du mappage réseau][configuration du mappage réseau] présentée dans le guide de déploiement.

## <span id="virtualmachines"></span></a>Étape 7 : activation de la protection pour les machines virtuelles

Dès lors que les serveurs, les clouds et les réseaux ont été configurés correctement, vous pouvez activer la protection pour les machines virtuelles du cloud. Notez les points suivants :

-   Les machines virtuelles doivent répondre aux exigences liées à Azure. Pour en savoir plus sur ces dernières, consultez la page [Configuration requise et prise en charge][Configuration requise et prise en charge] dans le guide de planification.
-   Pour activer la protection, vous devez définir les propriétés du système d'exploitation et du disque du système d'exploitation pour la machine virtuelle. Lorsque vous créez une machine virtuelle dans VMM à l'aide d'un modèle de machine virtuelle, vous pouvez définir la propriété. Vous pouvez également définir ces propriétés pour une machine virtuelle existante sous les onglets \*\*Général\*\* et \*\*Configuration matérielle\*\* des propriétés de la machine virtuelle. Si vous ne définissez pas ces propriétés dans VMM, vous pourrez les configurer dans le portail Azure Site Recovery.

![Create virtual machine][Create virtual machine]

![Modify virtual machine properties][Modify virtual machine properties]

1.  Pour activer la protection, sous l'onglet **Machines virtuelles** dans le cloud où se trouve la machine virtuelle, cliquez sur **Activer la protection**, puis sélectionnez **Ajouter des machines virtuelles**.
2.  Dans la liste des machines virtuelles du cloud, sélectionnez celle que vous voulez protéger.

    ![Activation de la protection d'une machine virtuelle][Activation de la protection d'une machine virtuelle]

3.  Vérifiez les propriétés de la machine virtuelle et modifiez-les si besoin est.

    ![Verify virtual machines][Verify virtual machines]

Vous pouvez consulter la progression de l'action d'activation de la protection, ainsi que la réplication initiale, sous l'onglet \*\*Jobs\*\*. Lorsque la tâche de finalisation de la protection s'exécute, la machine virtuelle est prête à être basculée. Une fois la protection activée et les machines virtuelles répliquées, celles-ci sont affichées dans Azure.

![Tâche de protection de la machine virtuelle][Tâche de protection de la machine virtuelle]

## <span id="test"></span></a>Étape 8 : Test du déploiement

Pour tester votre déploiement, vous pouvez exécuter un test de basculement pour une seule machine virtuelle, ou créer un plan de récupération comportant plusieurs machines virtuelles et exécuter sur lui un test de basculement. Il simule votre mécanisme de basculement et de récupération dans un réseau isolé. Notez les points suivants :

-   Si vous voulez vous connecter à la machine virtuelle dans Azure avec le Bureau à distance après le basculement, activez Connexion Bureau à distance sur la machine virtuelle avant d'exécuter le test de basculement.
-   Après le basculement, vous utiliserez une adresse IP publique pour vous connecter à la machine virtuelle dans Azure avec le Bureau à distance. Dans ce cas, assurez-vous qu'aucune de vos stratégies de domaine ne vous empêche de vous connecter à une machine virtuelle avec une adresse publique.

-   Pour créer un plan de récupération, consultez la page [Création et personnalisation de plans de récupération : protection locale sur Azure][Création et personnalisation de plans de récupération : protection locale sur Azure].
-   Pour des instructions sur l'exécution d'un test de basculement, consultez la page [Test de déploiement d'un serveur local vers Azure][Test de déploiement d'un serveur local vers Azure].

</p>
### <span id="runtest"></span></a>Suivi de l'activité

Vous pouvez utiliser l'onglet **Jobs** et **Dashboard** pour afficher et surveiller les tâches principales effectuées par le coffre Azure Site Recovery, telles que la configuration de la protection d'un cloud, l'activation et la désactivation de la protection d'une machine virtuelle, l'exécution d'un basculement (planifié, non planifié ou test) et la validation d'un basculement non planifié.

Depuis l'onglet **Jobs**, vous pouvez afficher les tâches, accéder aux détails de l'une d'entre elles ainsi qu'aux erreurs précédemment rencontrées lors de l'exécution de celle-ci, exécuter des requêtes de tâches pour récupérer les tâches qui correspondent à des critères spécifiques, exporter des tâches sur Excel et redémarrer celles qui ont échoué.

Depuis **Dashboard**, vous pouvez télécharger la dernière version des fichiers d'installation fournisseur et agent, obtenir des informations sur la configuration du coffre, découvrir le nombre de machines virtuelles dont la protection est assurée par le coffre, afficher les tâches récentes, gérer le certificat de coffre et resynchroniser des machines virtuelles.

Pour plus d'informations sur les interactions avec les tâches et le tableau de bord, consultez le [Guide sur les opérations et la surveillance][Guide sur les opérations et la surveillance].

## <span id="next"></span></a>Étapes suivantes

-   Pour planifier et déployer Azure Site Recovery dans un environnement de production complet, consultez le [Guide de planification d'Azure Site Recovery][Plan de déploiement Azure Site Recovery] et le [Guide de déploiement d'Azure Site Recovery][Guide de déploiement d'Azure Site Recovery].
-   Pour toute question, visitez le [forum Azure Recovery Services][forum Azure Recovery Services].

</div>

  [Prise en main d'Azure Site Recovery : protection locale en local]: http://go.microsoft.com/fwlink/?LinkId=398765
  [Plan de déploiement Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=321294
  [Déploiement d'Azure Site Recovery : protection d'un serveur local vers Azure]: http://go.microsoft.com/fwlink/?LinkId=402679
  [Azure Site Recovery : erreurs courantes et résolutions]: http://go.microsoft.com/fwlink/?LinkId=389879
  [forum Azure Recovery Services]: http://go.microsoft.com/fwlink/?LinkId=313628
  [Version d'évaluation gratuite d'Azure]: http://aka.ms/try-azure
  [Détails des prix d'Azure Site Recovery Manager]: http://go.microsoft.com/fwlink/?LinkId=378268
  [Introduction à Microsoft Azure Storage]: http://go.microsoft.com/fwlink/?LinkId=398704
  [Configuration requise et prise en charge]: http://go.microsoft.com/fwlink/?LinkId=402602
  [Étape 1 : Création d'un coffre]: #vault
  [Étape 2 : Installation de l'application fournisseur]: #download
  [Étape 3 : Ajout d'un compte de stockage Azure]: #storage
  [Étape 4 : Installation de l'application de l'agent]: #agent
  [Étape 5 : Configuration de la protection des clouds]: #clouds
  [Étape 6 : Configuration du mappage réseau]: #NetworkMapping
  [Étape 7 : Activation de la protection pour les machines virtuelles]: #virtualmachines
  [Étape 8 : Test du déploiement]: #test
  [portail de gestion]: https://manage.windowsazure.com
  [Nouveau coffre]: ./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png
  [Icône Quick Start]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png
  [Clé d'enregistrement]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2ARegisterKey.png
  [Configuration requise]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png
  [Mises à jour Microsoft]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png
  [Paramètres Internet]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png
  [Enregistrement du serveur]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png
  [1]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png
  [Compte de stockage]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2AStorageAgent.png
  [Install Recovery Services Agent]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2AInstallHyperVAgent.png
  [Prerequisites Recovery Services Agent]: ./media/hyper-v-recovery-manager-configure-vault/SR_AgentPrereqs.png
  [Cloud publié]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png
  [Cloud replication settings]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2A.png
  [Préparation du mappage réseau]: http://go.microsoft.com/fwlink/?LinkId=324817
  [configuration du mappage réseau]: http://go.microsoft.com/fwlink/?LinkId=402533
  [Create virtual machine]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ANew.png
  [Modify virtual machine properties]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2AExisting.png
  [Activation de la protection d'une machine virtuelle]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ASelectVM.png
  [Verify virtual machines]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableVME2AProps.png
  [Tâche de protection de la machine virtuelle]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
  [Création et personnalisation de plans de récupération : protection locale sur Azure]: http://go.microsoft.com/fwlink/?LinkId=511492
  [Test de déploiement d'un serveur local vers Azure]: http://go.microsoft.com/fwlink/?LinkId=511494
  [Guide sur les opérations et la surveillance]: http://go.microsoft.com/fwlink/?LinkId=398534
  [Guide de déploiement d'Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=321295

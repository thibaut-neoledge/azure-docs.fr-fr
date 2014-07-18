<properties  linkid="configure-hyper-v-recovery-vault" urlDisplayName="configure-hyper-v-recovery-vault" pageTitle="Configure Hyper-V Recovery Manager to protect virtual machines in VMM clouds" metaKeywords="hyper-v recovery, VMM, clouds, disaster recovery" description="Azure Hyper-V Recovery Manager can help you protect applications and services by coordinating the replication and recovery of virtual machines located in System Center 2012 VMM private clouds." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Configure Azure Hyper-V Recovery Manager" editor="jimbe" manager="cfreeman" authors="" />

# Configuration d'Azure Hyper-V Recovery Manager

 
<div  class="dev-callout"> 

<p>Azure Hyper-V Recovery Manager assure la coordination et la gestion de la protection des machines virtuelles Hyper-V dans les clouds privés résidant sur des serveurs VMM (Virtual Machine Manager) sous System Center 2012 Service Pack 1 (SP1) ou System Center 2012 R2. Hyper-V Recovery Manager orchestre le basculement de ces machines virtuelles d'un serveur hôte Hyper-V local vers un autre. Dans Azure, des coffres Hyper-V Recovery Manager permettent de stocker votre configuration de protection.</p>

<h2><a  id="about"></a>À propos de ce didacticiel</h2>
<p>Ce didacticiel propose une procédure pas-à-pas rapide pour déployer Hyper-V Recovery Manager. Pour plus d'informations, lisez les documents suivants :</p>
<ul>
<li><a  href="http://go.microsoft.com/fwlink/?LinkId=321294">Guide de planification pour Hyper-V Recovery Manager</a> : fournit des informations détaillées sur les étapes de planification à réaliser avant d'entreprendre un déploiement complet.</li>
<li><a  href="http://go.microsoft.com/fwlink/?LinkId=321295">Guide de déploiement pour Hyper-V Recovery Manager</a> : fournit des instructions pas à pas pour réaliser un déploiement complet.</li>
<li>Si vous rencontrez des problèmes au cours de ce didacticiel, consultez la page <a  href="http://go.microsoft.com/fwlink/?LinkId=389879">Hyper-V Recovery Manager : erreurs courantes et résolutions</a> ou publiez vos questions sur le <a  href="http://go.microsoft.com/fwlink/?LinkId=313628">forum Azure Recovery Services</a></li>
</ul>
</div>

 <h2><a  id="before" ></a>Avant de commencer</h2>


 
<div  class="dev-callout"> 
<p>Avant de commencer ce didacticiel, vérifiez la configuration requise.</p>

<h3><a  id="HVRMPrereq"></a>Configuration requise pour Hyper-V Recovery Manager</h3>

<ul>
<li>**Compte Azure** : vous avez besoin d'un compte Azure avec la fonctionnalité Azure Recovery Services activée. Si vous ne possédez pas de compte ou si la fonctionnalité n'est pas inscrite, consultez les pages <a  href="http://aka.ms/try-azure">Version d'évaluation gratuite d'Azure</a> et <a  href="http://go.microsoft.com/fwlink/?LinkId=378268">Détails des prix du Gestionnaire de récupération Hyper-V</a>.</li>
<li>**Certificat (.cer)** : pour inscrire des serveurs VMM dans un coffre Hyper-V Recovery Manager, vous devez télécharger un certificat de gestion (.cer) contenant la clé publique d'accès au coffre. Notez les points suivants :<ul>
	<li>Pour les besoins du didacticiel, vous pouvez utiliser un certificat auto-signé que vous créez à l'aide de l'outil Makecert.exe. Pour un déploiement complet, vous pouvez utiliser un certificat SSL valide qui respecte la configuration requise des certificats décrite dans la section <a  href=" http://go.microsoft.com/fwlink/?LinkId=386485">Configuration requise et prise en charge</a> du guide de planification.</li>
	<li>Chaque coffre est à tout moment associé à un seul certificat .cer. Vous pouvez télécharger un nouveau certificat pour en remplacer un, si nécessaire.</li>
</ul></li>
<li>**Certificat (fichier .pfx)** : le certificat .cer doit être exporté sous forme de fichier .pfx (contenant la clé privée). Vous devez importer ce fichier sur chaque serveur VMM contenant les machines virtuelles que vous voulez protéger. Ensuite, lorsque vous installez l'agent fournisseur Hyper-V Recovery Manager sur chaque serveur VMM au cours du déploiement, sélectionnez le fichier .pfx pour inscrire les serveurs VMM auprès du coffre.</li>

</ul>

<h3><a  id="VMMPrereq"></a>Configuration requise pour VMM</h3>

<ul>
<li>Au moins un serveur VMM s'exécutant sous System Center 2012 SP1 ou System Center 2012 R2.</li>
<li>Clouds privés VMM. Si vous disposez d'un seul serveur VMM, vous devez configurer deux clouds. Si vous utilisez plusieurs serveurs VMM, vous avez besoin d'au moins un cloud sur le serveur VMM source à protéger et d'un cloud sur le serveur VMM de destination que vous allez utiliser pour la récupération. Le cloud principal à protéger doit comporter les éléments suivants :<ul>
	<li>un ou plusieurs groupes hôtes VMM ;</li>
	<li>un ou plusieurs serveurs hôtes Hyper-V dans chaque groupe hôte.</li>
		</ul></li>
<li>Si vous voulez que des machines virtuelles soient connectées à un réseau de machines virtuelles après un basculement, configurez un mappage réseau dans Hyper-V Recovery Manager. Avant de démarrer le déploiement, vérifiez les points suivants :<ul>
	<li>La machine virtuelle du cloud du serveur VMM source est connectée à un réseau de machines virtuelles. Ce réseau doit être lié à un réseau logique lui-même associé au cloud.</li>
	<li>Le cloud cible sur le serveur VMM de destination a un réseau de machines virtuelles correspondant. Ce réseau doit être lié à un réseau logique correspondant lui-même associé au cloud cible.</li>
	</ul></li>
	
	<p>Pour en savoir plus sur le mappage réseau, lisez la section <a  href="http://go.microsoft.com/fwlink/?LinkId=324817">Préparation du mappage réseau</a> du guide de planification.</p>
</ul>


<h2><a  id="tutorial"></a>Étapes du didacticiel</h2> 

Après avoir vérifié la configuration requise, procédez comme suit :
<ul>
<li><a  href="#createcert">Obtenez et configurez des certificats</a> : obtenez un certificat .cer, exportez-le sous forme de fichier .pfx que vous allez importer dans les serveurs VMM.</li>
<li><a  href="#vault">Étape 1 : création d'un coffre</a> : créez un coffre Hyper-V Recovery Manager dans Azure.</li>
<li><a  href="#upload">Étape 2 : téléchargement du certificat</a> : téléchargez le certificat de gestion vers le coffre.</li>
<li><a  href="#download">Étape 3 : téléchargement et installation du fournisseur</a> : téléchargez et installez le fournisseur Hyper-V Recovery Manager sur les serveurs VMM que vous souhaitez protéger. Inscrivez ensuite les serveurs VMM auprès du coffre.</li>
<li><a  href="#clouds">Étape 4 : configuration de la protection des clouds</a> : configurez les paramètres de protection des clouds VMM.</li>
<li><a  href="#networks">Étape 5 : configuration du mappage réseau</a> : mappez les réseaux de machines virtuelles des serveurs VMM sources vers les serveurs VMM cibles.</li>
<li><a  href="#virtualmachines">Étape 6 : activation de la protection pour les machines virtuelles</a> : activez la protection pour les machines virtuelles Hyper-V situées dans les clouds VMM.</li>
<li><a  href="#recovery plans">Étape 7 : configuration et exécution de plans de récupération</a> : créez et personnalisez des plans de récupération qui permettent de regrouper les machines virtuelles à des fins de basculement. Exécutez ensuite le plan de récupération.</li>
<li><a  href="#jobs">Étape 8 : surveillance</a> : surveillez les paramètres, le statut et la progression via les onglets **Jobs** et **Dashboard**.</li>
</ul>



<a name="createcert"></a> <h2>Obtention et configuration de certificats</h2>

Pour obtenir et configurer des certificats, procédez comment suit :
<ol>
<li><a  href="#obtaincert">Obtention d'un certificat pour la procédure pas à pas</a> : obtenez un certificat à l'aide de l'outil MakeCert ou utilisez un certificat existant qui respecte la <a  href="http://go.microsoft.com/fwlink/?LinkId=321294">configuration requise des certificats</a>.</li>
<li><a  href="#exportcert">Exportation du certificat au format .pfx</a> : sur le serveur sur lequel réside ou a été créé le certificat, exportez le fichier .cer sous forme de fichier .pfx (avec la clé privée). </li>
<li><a  href="#importcert">Importation du certificat .pfx dans les serveurs VMM</a> : après avoir exporté le fichier .pfx, importez-le dans le magasin de certificats personnels sur chaque serveur VMM que vous souhaitez inscrire auprès du coffre.</li>
</ol>


<h3><a  id="obtaincert"></a>Obtention d'un certificat auto-signé (.cer)</h3>
<p>Pour créer un certificat .cer x.509 qui respecte entièrement la configuration requise des certificats, utilisez l'outil MakeCert comme suit :</p>
<ol>
<li>
Sur l'ordinateur sur lequel vous souhaitez exécuter MakeCert, téléchargez la dernière version du <a  href="http://go.microsoft.com/fwlink/?LinkId=378269">Kit de développement logiciel (SDK) Windows</a>. Notez que la commande makecert.exe fait partie du Kit de développement logiciel (SDK) Windows de base. Vous n'avez donc pas besoin de télécharger et d'installer le SDK dans son intégralité.</li>
<li>Sur la page Spécifier un emplacement, sélectionnez **Installer le Kit de développement logiciel Windows (Kit SDK Windows) pour Windows 8.1 sur cet ordinateur**.</li>
<li>Sur la page Rejoindre notre Programme d'amélioration de l'expérience utilisateur, sélectionnez le paramètre qui vous convient le mieux.</li>
<li>Sur la page Contrat de licence, cliquez sur **Accepter** pour accepter conditions.</li>
<li>Sur la page Sélectionnez les composants du programme que vous voulez installer, effacez toutes les options à l'exception de **Kit de développement logiciel Windows (Kit SDK Windows)**.</li>
<li>À la fin de l'installation, vérifiez que makecert.exe figure bien dans le dossier C:\ProgramFiles (x86)\Windows Kits\<i>VersionWindows</i>\bin\x64.</li>
<li>Ouvrez une invite de commandes (cmd.exe) avec des privilèges d'administrateur et accédez au dossier makecert.exe.</li> 
<li>>Exécutez la commande suivante pour créer votre certificat auto-signé. Remplacez CertificateName par le nom que vous voulez attribuer au certificat, puis indiquez la date d'expiration effective de votre certificat après -e :</li>
<code>makecert.exe -r -pe -n CN=CertificateName -ss my -sr localmachine -eku 1.3.6.1.5.5.7.3.2 -len 2048 -e 01/01/2016 CertificateName.cer</code>
</ol>
<p>Le certificat est créé et stocké dans le même dossier. Vous pouvez éventuellement le déplacer vers un emplacement plus accessible en vue de l'exporter à la prochaine étape.</p>

<h3><a  id="exportcert"></a>Exportation du certificat au format .pfx</h3>
<p>Pour exporter le fichier .cer au format .pfx, procédez comme suit :</p>
<ol>
<li>Dans l'écran d'accueil, tapez **mmc.exe** pour démarrer Microsoft Management Console (MMC).</li>
<li>Dans le menu **Fichier**, cliquez sur **Ajouter/Supprimer un composant logiciel enfichable**. La boîte de dialogue **Ajouter ou supprimer des composants logiciels enfichables** s'affiche.</li>
<li>Dans **Composants logiciels enfichables disponibles**, cliquez sur **Certificats**, puis sur **Ajouter**.</li>
<li>Sélectionnez **Compte d'ordinateur**, puis cliquez sur **Suivant**.</li>
<li>Sélectionnez **Ordinateur local**, puis cliquez sur **Terminer**.</li>
<li>Dans l'arborescence de la console MMC, développez **Certificats**, puis **Personnel**.</li>
<li>Dans le volet d'informations, cliquez sur le certificat que vous souhaitez gérer.</li>
<li>Dans le menu **Action**, pointez sur **Toutes les tâches**, puis cliquez sur **Exporter**. L'Assistant Exportation du certificat s'affiche. Cliquez sur **Suivant**.</li>
<li>Sur la page **Exporter la clé privée**, cliquez sur **Oui** pour exporter la clé privée. Cliquez sur **Suivant**. Notez que cette opération n'est nécessaire que si vous souhaitez exporter la clé privée vers d'autres serveurs après l'installation.</li>
<li>Sur la page Format de fichier d'exportation, sélectionnez **Échange d'informations personnelles - PKCS #12 (.PFX)**. Cliquez sur **Suivant**.</li>
<li>Sur la page **Mot de passe**, tapez et confirmez le mot de passe utilisé pour chiffrer la clé privée. Cliquez sur **Suivant**.</li>
<li>Suivez les pages de l'Assistant pour exporter le certificat au format .pfx.</li>
</ol>


<h3><a  id="importcert"></a>Importation du certificat .pfx sur les serveurs VMM</h3>
<p>Après avoir exporté le certificat, copiez-le sur chaque serveur VMM à inscrire dans le coffre, puis importez-le comme suit. Notez que si vous avez exécuté MakeCert.exe sur un serveur VMM, vous n'avez pas besoin d'importer le certificat sur ce serveur.</p>
 
<ol>
<li>Copiez les fichiers de certificat de clé privée (.pfx) à un emplacement sur le serveur local.</li>
<li>Dans le composant logiciel enfichable MMC Certificats, sélectionnez **Compte d'ordinateur**, puis cliquez sur Suivant.</li>
<li>Sélectionnez Ordinateur local, puis cliquez sur **Terminer**. Dans la boîte de dialogue **Ajouter/Supprimer un composant logiciel enfichable**, cliquez sur **OK**. </li>
<li>Dans la console MMC, développez **Certificats**, cliquez avec le bouton droit sur **Personnel**, pointez sur Toutes les tâches, puis cliquez sur **Importer** pour démarrer l'Assistant Importation du certificat.</li>
<li>Sur la page d'accueil de l'Assistant Importation du certificat, cliquez sur **Suivant**.</li>
<li>Sur la page Fichier à importer, cliquez sur **Parcourir** et recherchez le dossier dans lequel se trouve le fichier de certificat .pfx qui contient le certificat à importer. Sélectionnez le fichier approprié, puis cliquez sur **Ouvrir**.</li>
<li>Sur la page Mot de passe, dans la zone **Mot de passe**, tapez le mot de passe du fichier de clé privée que vous avez spécifié dans la procédure précédente, puis cliquez sur **Suivant**.</li>
<li>Sur la page Magasin de certificats, sélectionnez **Placer tous les certificats dans le magasin suivant**, cliquez sur **Parcourir**, sélectionnez le magasin **Personnel**, cliquez sur **OK**, puis sur Suivant.</li>
<li>Sur la page Fin de l'Assistant Importation de certificat, cliquez sur **Terminer**.</li>
</ol>

Après avoir effectué ces étapes, vous pouvez choisir le certificat .cer à télécharger vers le coffre et sélectionner le certificat .pfx au moment d'inscrire un server VMM lors de l'installation du fournisseur.
</div>

 <a name="vault"></a> <h2>Étape 1 : création d'un coffre</h2>

1.  Connectez-vous au [portail de gestion][1].

2.  Cliquez sur **Data Services**, puis sur **Recovery Services**.
    
    ![Programme d'évaluation](./media/hyper-v-recovery-manager-configure-vault/RS_PreviewProgram.png)

3.  Cliquez sur **Recovery Services**, sur **Create New**, pointez sur **Hyper-V Recovery Manager Vault**, puis cliquez sur **Quick Create**.
    
    ![Nouveau coffre](./media/hyper-v-recovery-manager-configure-vault/RS_hvvault.png)

4.  Dans **Name**, entrez un nom convivial pour identifier le coffre.

5.  Dans **Region**, sélectionnez la région géographique du coffre. Vous avez le choix entre les régions géographiques suivantes : Asie-Pacifique Sud-Est, Europe du Nord et Est des États-Unis.****

6.  Dans **Subscription**, tapez les détails de l'abonnement.

7.  Cliquez sur **Create vault**.

Vérifiez le statut du coffre dans les notifications situées au bas du portail. Un message confirme que le coffre a été correctement créé. Il apparaît comme étant **En ligne** sur la page Recovery Services.


<a name="upload"></a> <h2>Étape 2 : téléchargement d'un certificat (.cer)</h2>

1.  Sur la page **Recovery Services**, ouvrez le coffre approprié. 2.  Cliquez sur l'icône Quick Start pour ouvrir la page du même nom.
    
    ![Icône Quick Start](./media/hyper-v-recovery-manager-configure-vault/RS_QuickStartIcon.png)

3.  Cliquez sur **Manage Certificate**.
    
    ![Quick Start](./media/hyper-v-recovery-manager-configure-vault/RS_QuickStart.png)

4.  Dans la boîte de dialogue **Manage Certificate**, cliquez sur **Browse For File** pour rechercher le fichier .cer à télécharger vers le coffre.
    
    ![Gérer le certificat](./media/hyper-v-recovery-manager-configure-vault/RS_ManageCert.png)

<a name="download"></a> <h2>Étape 3 : téléchargement et installation du fournisseur</h2> 
Installez le fournisseur Hyper-V Recovery Manager sur chaque serveur VMM à inscrire dans le coffre. La dernière version du fichier d'installation du fournisseur est stockée dans le Centre de téléchargement Azure. Lorsque vous exécutez le fichier sur un serveur VMM, le fournisseur est installé et le serveur VMM est inscrit auprès du coffre.

1.  Sur la page **Quick Start**, cliquez sur **Download Provider** pour obtenir le fichier .exe d'installation du fournisseur. Exécutez ce fichier sur le serveur VMM pour lancer l'installation du fournisseur.
    
    ![Télécharger l'agent](./media/hyper-v-recovery-manager-configure-vault/RS_installwiz.png)

2.  Suivez les étapes pour terminer l'installation du fournisseur.
    
    ![Installation terminée](./media/hyper-v-recovery-manager-configure-vault/RS_SetupComplete.png)

3.  Une fois le fournisseur installé, suivez les étapes de l'Assistant pour inscrire le serveur VMM auprès du coffre.

4.  Sur la page Internet Connection, indiquez la façon dont le fournisseur s'exécutant sur le serveur VMM se connecte à Internet. Le fournisseur peut utiliser les paramètres de connexion à Internet par défaut du serveur. Vous pouvez également cliquer sur **Use a proxy server for Internet requests** pour utiliser des paramètres personnalisés.
    
    ![Paramètres Internet](./media/hyper-v-recovery-manager-configure-vault/RS_ProviderProxy.png)
    
    Si vous souhaitez utiliser des paramètres personnalisés dans le cadre de cette procédure pas à pas, lisez les informations fournies à l'[Étape 2 : installation du fournisseur et inscription des serveurs VMM][2] du guide de déploiement.

5.  Sur la page Certificate Registration, sélectionnez le fichier .pfx correspondant au fichier .cer que vous avez téléchargé vers le coffre.
    
    ![Inscription de certificat](./media/hyper-v-recovery-manager-configure-vault/RS_CertReg1.png)
    
    ![Coffre de certificats](./media/hyper-v-recovery-manager-configure-vault/RS_CertReg2.png)

6.  Sur la page VMM Server, attribuez un nom convivial au serveur VMM. Ce nom sert à identifier le serveur dans la console Hyper-V Recovery Manager.

7.  Sélectionnez **Synchronize cloud data with the vault** pour synchroniser les données sur tous les clouds privés situés sur le serveur VMM avec le coffre Hyper-V Recovery Manager. Cette action se produit une seule fois sur chaque serveur. Si vous ne souhaitez pas synchroniser tous les clouds, vous pouvez les publier individuellement pour les synchroniser avant de configurer les paramètres de protection de cloud.

8.  Cliquez sur **Register** pour terminer le processus.
    
    ![Paramètres Internet](./media/hyper-v-recovery-manager-configure-vault/RS_PublishCloudSetup.png)

À ce stade, les métadonnées du serveur VMM sont extraites par Hyper-V Recovery Manager afin d'orchestrer le basculement et la récupération. Dès lors qu'un serveur est correctement inscrit, son nom convivial s'affiche sous l'onglet **Resources** de la page Servers du coffre.


<h2><a  id="clouds" ></a>Étape 4 : configuration des paramètres de protection de cloud</h2>


Une fois les serveurs VMM inscrits, vous pouvez configurer les paramètres de protection de cloud. Si vous n'avez pas activé l'option **Synchronize cloud data with the vault** lors de l'installation du fournisseur sur le serveur VMM, vous devez publier le cloud dans Hyper-V Recovery Manager à partir de la console VMM. Une fois que les clouds sont détectés par Hyper-V Recovery Manager, vous pouvez configurer les paramètres de protection.

<h3><a  id="publishclouds" ></a>Publication d'un cloud</h3>


1.  Dans la console VMM, ouvrez l'espace de travail **VMs and Services**.
2.  Dans le volet **VMs and Services**, ouvrez le cloud que vous souhaitez publier.
3.  Sur la page **General** des propriétés du cloud, pour publier le cloud, sélectionnez **Send configuration data about this cloud to the Azure Hyper-V Recovery Manager**. Une fois le cloud publié, il s'affiche dans le coffre.
    
    ![Clouds](./media/hyper-v-recovery-manager-configure-vault/RS_PublishCloud.png)
    
    ![Cloud publié](./media/hyper-v-recovery-manager-configure-vault/RS_Clouds.png)

<h3><a  id="configureclouds" ></a>Configuration des clouds</h3>


Pour configurer les clouds afin de les protéger, procédez comme suit :

1.  Sur la page Quick Start, cliquez sur **Configure Protection Settings**.
2.  Sous l'onglet **Protected Items**, sélectionnez le cloud que vous souhaitez configurer, puis accédez à l'onglet **Configuration**.
    ![Configuration de cloud](./media/hyper-v-recovery-manager-configure-vault/RS_CloudConfig.png)
3.  Dans **Target Location**, spécifiez le serveur VMM qui gère le cloud à utiliser pour la récupération.
4.  Dans **Target Cloud**, spécifiez le cloud cible à utiliser pour le basculement des machines virtuelles du cloud source.
5.  Dans **Copy Frequency**, laissez le paramètre par défaut. Cette valeur indique la fréquence de synchronisation des données entre les emplacements source et cible. Elle n'est utile que lorsque l'hôte Hyper-V s'exécute sur Windows Server 2012 R2. Les autres serveurs utilisent un paramétrage par défaut de cinq minutes.
6.  Dans **Additional Recovery Points**, conservez le paramètre par défaut. Cette valeur indique si vous souhaitez créer des points de récupération supplémentaires. Avec une valeur par défaut de zéro, seul le dernier point de récupération d'une machine virtuelle principale est stocké sur un serveur hôte de réplication.
7.  Dans **Application-Consistent Snapshot Frequency**, laissez le paramètre par défaut. Cette valeur indique la fréquence de création de captures instantanées. Ces dernières utilisent le service VSS (Volume Shadow Copy Service) pour s'assurer que les applications sont dans un état cohérent lors de la prise des captures instantanées. Si vous voulez définir cette valeur pour la procédure pas à pas du didacticiel, assurez-vous qu'elle est inférieure au nombre de points de récupération supplémentaires que vous configurez.
8.  Dans **Data Transfer Compression**, indiquez si les données répliquées transférées doivent être compressées.
9.  Dans **Authentication**, indiquez le mode d'authentification du trafic entre les serveurs hôtes Hyper-V principaux et de récupération. À moins de disposer d'un environnement Kerberos configuré et opérationnel, nous vous recommandons de choisir HTTPS pour les besoins de cette procédure pas à pas. Dans ce cas, les serveurs hôtes s'authentifient mutuellement à l'aide d'un certificat de serveur et le trafic est chiffré. Hyper-V Recovery Manager configure automatiquement les certificats à utiliser pour l'authentification HTTPS. Aucune configuration manuelle n'est nécessaire. Notez que ce paramètre n'est utile que pour les serveurs hôtes Hyper-V s'exécutant sur Windows Server 2012 R2.
10. Dans **Port**, laissez le paramètre par défaut. Cette valeur définit le numéro de port sur lequel les ordinateurs hôtes Hyper-V source et cible écoutent le trafic de réplication.
11. Dans **Initial Replication Settings**, indiquez comment sera traitée la réplication de données initiale entre les emplacements source et cible avant que la réplication normale ne démarre.
    * **Over the network** : la copie de données sur le réseau peut demander du temps et des ressources. Nous vous recommandons   d'utiliser cette option si le cloud contient des machines virtuelles dotées de disques durs virtuels relativement petits et si le serveur VMM principal est connecté au serveur VMM secondaire sur une connexion à large bande passante. Vous pouvez opter pour un démarrage immédiat de la copie ou sélectionner une heure. Si vous utilisez la réplication réseau, nous vous recommandons de la planifier sur des heures creuses.
    * **Offline** : cette méthode précise que la réplication initiale s'exécute à l'aide d'un support externe. Elle est indiquée si vous voulez éviter une dégradation des performances réseau ou pour les emplacements éloignés géographiquement. Pour utiliser cette méthode, il convient de spécifier l'emplacement d'exportation sur le cloud source et l'emplacement d'importation sur le cloud cible. Lorsque vous activez la protection pour une machine virtuelle, le disque dur virtuel est copié vers l'emplacement d'exportation spécifié. Vous l'envoyez vers le site cible et le copiez vers l'emplacement d'importation. Le système copie les informations importées vers les machines virtuelles de réplication. Pour connaître les détails de la configuration requise pour la réplication hors ligne, consultez l'[Étape 3 : configuration des paramètres de protection des clouds VMM][3] du guide de déploiement.

<h4><a  id="cloudsettingd" ></a>Paramètres après la configuration de la protection</h4>


Dès lors qu'un cloud est configuré, tous les clusters et serveurs hôtes configurés dans les clouds source et cible sont configurés pour la réplication. Voici, plus précisément, les éléments qui sont configurés :

* Règles de pare-feu utilisées par la fonctionnalité Réplica Hyper-V de sorte que les ports soient ouverts pour le trafic de réplication.
* Installation des certificats nécessaires à la réplication.
* Configuration des paramètres de la fonctionnalité Réplica Hyper-V.

Les paramètres de cloud peuvent être modifiés sous l'onglet **Configure**. Notez les points suivants :

* Nous vous recommandons de choisir un cloud cible qui remplisse les conditions requises pour la récupération des machines virtuelles que vous prévoyez de protéger.
* Un cloud ne peut appartenir qu'à une seule paire de clouds, que ce soit en qualité de cloud principal ou de cloud cible.
* Suite à l'enregistrement de la configuration du cloud, une tâche est créée et peut être surveillée sous l'onglet **Jobs**. Pour modifier l'emplacement cible ou le cloud cible après l'enregistrement de la configuration, vous devez supprimer la configuration du cloud, puis reconfigurer ce dernier.

<h2><a  id="networks" ></a>Étape 5 : configuration du mappage réseau</h2>


L'onglet **Networks** vous permet de configurer le mappage entre les réseaux de machines virtuelles des serveurs VMM source et cible. Le mappage réseau est l'assurance qu'après un basculement, les machines virtuelles de réplication sont connectées aux réseaux appropriés et qu'elles sont placées sur des hôtes capables d'accéder aux réseaux de machines virtuelles. Vérifiez la configuration requise des réseaux VMM dans la section [Configuration requise et prise en charge]( http://go.microsoft.com/fwlink/?LinkId=386485) du guide de planification. Nous vous recommandons de mapper les réseaux comme suit :

* Mappez les réseaux utilisés par chaque cloud configuré pour la protection.
* Exécutez le mappage réseau avant d'activer les machines virtuelles pour la protection, car le mappage est utilisé lors du placement des machines virtuelles de réplication. Le mappage fonctionnera correctement si vous le configurez après avoir activé la protection, mais vous serez peut-être amené à migrer certaines machines virtuelles de réplication.

Pour mapper des réseaux, procédez comme suit :

1.  Sur la page Quick Start, cliquez sur **Configure Network Mapping**. 
2.  Dans **Source Location**, sélectionnez le serveur VMM source. 
3.  Dans **Target Location**, sélectionnez le serveur VMM cible. Si vous déployez Hyper-V Recovery Manager avec un seul serveur VMM, la source et la cible sont identiques. La liste des réseaux de machines virtuelles sources et cibles associés s'affiche à l'écran. Les réseaux non mappés n'affichent aucune valeur.
4.  Sélectionnez une entrée non mappée dans la liste source et cible, puis cliquez sur **Map**. Le service détecte les réseaux de machines virtuelles sur le serveur cible et les affiche.
    ![Gérer un certificat](./media/hyper-v-recovery-manager-configure-vault/RS_networks.png)

5.  Sur la page Select a Target Network, sélectionnez le réseau de machines virtuelles cible que vous souhaitez utiliser sur le serveur VMM cible. 
	![Réseau cible](./media/hyper-v-recovery-manager-configure-vault/RS_TargetNetwork.png)

6.  Cliquez sur les icônes d'information en regard du nom des réseaux source et cible pour afficher les sous-réseaux et le type de chaque réseau.
    
    Lorsque vous sélectionnez un réseau cible, les clouds protégés qui utilisent le réseau source s'affichent à l'écran, tout comme la disponibilité des réseaux de machines virtuelles cibles associés aux clouds. Nous vous recommandons de sélectionner un réseau cible qui soit accessible à tous les clouds utilisés pour la protection.

7.  Cliquez sur la coche pour terminer le processus de mappage. Une tâche de suivi du processus de mappage démarre. Elle est consultable sous l'onglet **Jobs**.
    
    Les machines virtuelles de réplication existantes correspondant aux machines virtuelles connectées au réseau de machines virtuelles source sont alors connectées au réseau de machines virtuelles cible. De la même manière, les nouvelles machines virtuelles de réplication créées après l'activation de la réplication sur les machines
    virtuelles connectées au réseau de machines virtuelles source sont connectées au réseau de machines virtuelles cible.

<h3><a  id="modifynetworks" ></a>Modification des mappages réseau</h3>


Les mappages réseau peuvent être modifiés ou supprimés sous l'onglet **Networks**. Notez les points suivants :

*  Si vous annulez un mappage sélectionné, celui-ci est supprimé et les machines virtuelles de réplication sont déconnectées du réseau auquel elles étaient connectées au moment du mappage.
*  Lorsque vous modifiez un mappage sélectionné, les modifications sont mises à jour et les machines virtuelles de réplication sont connectées aux nouveaux paramètres réseau fournis.


<h2><a  id="virtualmachines" ></a>Étape 6 : activation de la protection pour les machines virtuelles</h2>


Dès lors que les serveurs, les clouds et les réseaux ont été configurés correctement, vous pouvez activer la protection pour les machines virtuelles du cloud. L'activation de la protection s'effectue dans la console VMM en cliquant avec le bouton droit sur chaque machine virtuelle à protéger et en sélectionnant **Enable Recovery**.


Une fois la protection activée, les machines virtuelles en question figurent dans la liste des machines virtuelles du cloud. Vous pouvez consulter la progression de l'action d'activation de la protection sous l'onglet **Jobs**.

![Machines virtuelles](./media/hyper-v-recovery-manager-configure-vault/RS_Clouds.png)

L'activation de la protection de machines virtuelles entraîne la création de trois tâches. La première à s'exécuter est la tâche d'activation de la protection. Ensuite, à l'issue de la réplication initiale, deux autres tâches de finalisation de la protection s'exécutent. La machine virtuelle n'est prête pour le basculement qu'une fois ces trois tâches effectuées.


<h2><a  id="recoveryplans" ></a>Étape 7 : configuration et exécution de plans de récupération</h2>


Un plan de récupération rassemble des groupes de machines virtuelles pour permettre un traitement unitaire et définit l'ordre de basculement des différents groupes. En plus de définir des groupes de machines virtuelles, vous pouvez personnaliser les plans de récupération pour qu'ils exécutent des scripts automatiques ou qu'ils attendent la confirmation d'actions manuelles. Pour créer un plan de récupération, procédez comme suit :

1.  Sur la page Quick Start, cliquez sur **Create Recovery Plan**. 
2.  Sur la page Specify the Recovery Page Name and Target, indiquez un nom et les serveurs VMM source et cible. Si vous déployez Hyper-V Recovery Manager sur un seul serveur VMM, les serveurs source et cible sont identiques. Le serveur VMM source doit contenir des machines virtuelles pour lesquelles la protection est activée.
    
    ![Créer un plan de récupération](./media/hyper-v-recovery-manager-configure-vault/RS_RecoveryPlan1.png)

3.  Sur la page Select Virtual Machines, sélectionnez les machines virtuelles à ajouter au plan de récupération. Celles-ci sont ajoutées au groupe par défaut (Groupe 1) du plan de récupération.
4.  Cliquez sur la coche pour créer le plan de récupération. Les plans de récupération que vous créez peuvent être supprimés sous l'onglet **Recovery Plans**.
    
    ![Machines virtuelles du plan de récupération](./media/hyper-v-recovery-manager-configure-vault/RS_RecoveryPlan2.png)

Après avoir créé un plan de récupération, vous pouvez effectuer les actions suivantes :

* Personnalisez le plan. Vous pouvez ajouter de nouveaux groupes à un plan de récupération ou ajouter des machines virtuelles à un groupe. Vous pouvez définir des actions personnalisées sous forme de scripts et d'actions manuelles qui s'exécutent avant et après des groupes spécifiés. Les noms de groupes sont incrémentiels. Ainsi, après le plan de récupération par défaut Groupe 1, vous créez le Groupe 2, le Groupe 3, et ainsi de suite. Le basculement des machines virtuelles s'opère selon l'ordre des groupes. Pour plus d'informations sur la personnalisation des plans de récupération, consultez la section [Étape 6 : création et personnalisation de plans de récupération][4] du guide de déploiement. * Exécutez un test de basculement.

<h3><a  id="run" ></a>Exécution des plans de récupération</h3>


Les plans de récupération peuvent être exécutés dans le cadre d'un test proactif, d'un basculement planifié ou à l'occasion d'un basculement non planifié. Cette procédure pas à pas explique comment exécuter un test de basculement. Pour plus d'informations sur les autres types de basculement, lisez le [Guide sur les opérations et la surveillance](hhttp://go.microsoft.com/fwlink/?LinkId=378272).

<h3><a  id="protect" ></a>Exécution du plan</h3>


Vous pouvez exécuter un test de basculement pour vérifier votre plan de récupération dans un environnement de test. Un test de basculement s'avère utile pour vérifier que votre plan de récupération et votre stratégie de basculement de machines virtuelles fonctionnement comme prévu. Il simule votre mécanisme de basculement et de récupération dans un réseau isolé.

<h5><a  id="networkrecommendations" ></a>Avant de commencer</h5>


Lors du déclenchement d'un test, il vous est demandé d'indiquer comment les machines virtuelles de test doivent être connectées aux réseaux à l'issue du basculement.

*  Si vous voulez utiliser un réseau existant, nous vous recommandons de créer à cette fin un réseau logique distinct qui n'est pas utilisé en production.
*  Si vous optez pour la création automatique d'un réseau de machines virtuelles de test, les réseaux temporaires et les machines virtuelles de test sont nettoyés automatiquement à l'issue du test de basculement.
*  Si vous utilisez un réseau local virtuel (VLAN) basé sur un réseau logique, assurez-vous que les sites réseau que vous ajoutez au réseau logique sont isolés.
*  Si vous utilisez un réseau logique basé sur la virtualisation de réseau Windows, Hyper-V Recovery Manager crée automatiquement des réseaux de machines virtuelles isolés.


<h5><a  id="runtest" ></a>Exécution d'un test de basculement</h5>


Pour exécuter un test de basculement pour un plan de récupération, procédez comme suit :

1.   Sous l'onglet **Recovery Plans**, sélectionnez le plan de récupération approprié.
2.   Pour lancer le basculement, cliquez sur le bouton **Test Failover**.
3.   Sur la page Confirm Test Failover, indiquez comment les machines virtuelles doivent être connectées aux réseaux après le test de basculement, à savoir :
*  **None** : sélectionnez ce paramètre pour indiquer que les réseaux de machines virtuelles ne doivent pas être utilisés dans le test de basculement. Utilisez cette option si vous souhaitez tester des machines virtuelles individuelles plutôt que votre configuration réseau. Elle donne également un aperçu rapide du fonctionnement des
  tests de basculement. Les machines virtuelles de test ne sont pas connectées aux réseaux à l'issue du basculement.
*  **Use existing** : utilisez cette option si vous avez déjà créé et isolé un réseau de machines virtuelles à utiliser pour le test de basculement. À l'issue du basculement, toutes les machines virtuelles de test utilisées dans le basculement de test sont connectées au réseau spécifié dans **VM Network**.
*  **Create automatically** : sélectionnez ce paramètre pour indiquer que Hyper-V Recovery Manager doit créer automatiquement un réseau de machines virtuelles en fonction du paramétrage que vous spécifiez dans Logical Network et les sites réseau associés. Utilisez cette option si le plan de récupération fait appel à plusieurs réseaux
  de machines virtuelles. Dans le cas des réseaux de virtualisation de réseau Windows, cette option peut être utilisée pour créer automatiquement des réseaux de machines virtuelles à partir des mêmes paramètres (sous-réseaux et pools d'adresses IP) que ceux du réseau de la machine virtuelle de réplication. Ces réseaux de machines virtuelles sont nettoyés automatiquement à l'issue du test de basculement.



À la fin du test de basculement, effectuez les opérations suivantes :


1.   Vérifiez que les machines virtuelles démarrent correctement. 
2.   Après avoir effectué cette vérification, terminez le test de basculement afin de nettoyer l'environnement isolé. Si vous avez opté pour la création automatique de réseaux de machines virtuelles, le nettoyage supprime toutes les machines virtuelles de test, ainsi que les réseaux de test.
3.   Cliquez sur **Notes** pour consigner et enregistrer les éventuelles observations associées au test de basculement.
4.   Lorsque vous lancez un test de basculement pour un plan de récupération, le processus de basculement s'affiche sur la page d'informations du plan de récupération. Vous pouvez examiner les différentes étapes du test de basculement et leur statut respectif et consulter ou créer des notes sur le test de basculement. Par ailleurs, il est possible de consulter le détail des tâches de basculement sous l'onglet **Jobs**. Vous y trouverez les différentes tâches associées au basculement, leur statut, le moment où elles ont démarré, ainsi que leur durée. Vous pouvez exporter une tâche de la liste de basculement vers une feuille de calcul Excel.


Notez les points suivants :

* La page Confirm Test Failover présente les détails du serveur VMM sur lequel les machines virtuelles seront créées.
* Vous pouvez suivre la progression des tâches du test de basculement sous l'onglet **Jobs**.

<h2><a  id="jobsdashboard" ></a>Étape 8 : surveillance</h2>


<h3><a  id="jobsdashboard" ></a>Utilisation de l'onglet Jobs et du tableau de bord</h3>


<h4><a  id="jobs" ></a>Tâches</h4>


L'onglet **Jobs** présente les principales tâches effectuées par le coffre Hyper-V Recovery Manager, à savoir, la configuration de la protection d'un cloud ; l'activation et la désactivation de la protection d'une machine virtuelle ; l'exécution d'un basculement (planifié, non planifié ou de test) ; la validation d'un basculement non planifié ; et la configuration d'une réplication inverse.

Sous l'onglet **Jobs**, vous pouvez effectuer les tâches suivantes :

* **Exécuter une requête de tâches** : vous pouvez exécuter une requête afin d'extraire les tâches qui correspondent à des critères déterminés. Vous pouvez filtrer les tâches à l'aide des paramètres suivants :

* Recherchez les tâches qui ont été exécutées sur un serveur VMM déterminé.
* Recherchez les tâches qui ont été exécutées sur un cloud, une machine virtuelle, un réseau, un plan de récupération de cloud ou l'ensemble de ces éléments.
* Identifiez les tâches qui se sont déroulées au cours d'une date ou d'une période déterminé(e).

Notez qu'une requête peut renvoyer jusqu'à 200 tâches. Pour rester en dessous de ce maximum, nous vous recommandons d'affiner les paramètres de requête.

* **Obtenir les détails d'une tâche** : vous pouvez cliquer sur une tâche de la liste **Jobs** pour obtenir des détails supplémentaires. Ces détails incluent le nom de la tâche, son statut, le moment où elle a démarré et sa durée. Sous l'onglet d'information sur les tâches, vous pouvez exporter les détails d'une tâche vers une feuille de calcul Excel ou essayer de redémarrer une tâche ayant échoué, ignorée ou annulée.
* **Exporter des tâches** : vous pouvez exporter les résultats d'une requête de tâches vers une feuille de calcul Excel.
* **Redémarrer** : vous pouvez redémarrer les tâches qui ont échoué pour essayer de les réexécuter.
* **Détails des erreurs** : pour les tâches qui ont échoué, vous pouvez cliquer sur **Error Details** pour obtenir la liste des erreurs rencontrées par ces tâches. Les causes possibles de chaque erreur sont indiquées et s'accompagnent de recommandations. La description des erreurs peut être copiée dans le Presse-papiers à des fins de dépannage.

> L'onglet **Jobs** fournit les informations suivantes :

> *  **Name** : nom de la tâche.
*  **Item** : nom du cloud, du plan de récupération, du serveur VMM
  ou de la machine virtuelle sur lequel la tâche s'est exécutée.
*  **Status** : statut de la tâche (terminée, annulée, ayant échoué
  ou autre).
*  **Type** : type de la tâche.
*  **Started At** : moment auquel la tâche a démarré.
*  **Task Time** : durée de la tâche.


![Onglet Jobs](./media/hyper-v-recovery-manager-configure-vault/RS_Jobs.png)

<h4><a  id="protect" ></a>Exécution d'une requête de tâches</h4>


Vous pouvez exécuter des requêtes pour extraire les tâches qui correspondent à des critères déterminés, à savoir :

*  les tâches qui ont été exécutées sur un serveur VMM déterminé ;
*  les tâches qui ont été exécutées sur un cloud, une machine virtuelle, un réseau, un plan de récupération déterminé ou l'ensemble de ces éléments ;
*  les tâches qui se sont déroulées au cours d'une période déterminée.


Pour exécuter une requête de tâches, procédez comme suit :

1.   Ouvrez l'onglet **Jobs**. 
2.   Dans **Server**, spécifiez le serveur VMM sur lequel la tâche s'est exécutée.
3.   Dans **Type**, indiquez si la tâche a été exécutée sur un cloud, une machine virtuelle, un réseau ou un plan de récupération de cloud.
4.   Dans **Status**, spécifiez le statut de la tâche. 
5.   Dans **Duration**, indiquez la période au cours de laquelle la tâche s'est déroulée.


<h4><a  id="dashboard" ></a>Tableau de bord</h4>


L'onglet **Dashboard** offre un aperçu rapide de l'utilisation de Hyper-V Recovery Manager dans votre organisation. Il s'agit d'un emplacement centralisé qui permet d'identifier les machines virtuelles qui bénéficient de la protection de coffres Hyper-V Recovery Manager. Le tableau de bord offre les fonctionnalités suivantes :


Sous l'onglet **Dashboard**, vous pouvez effectuer les tâches suivantes :

* **Download Provider** : téléchargez le fournisseur Hyper-V Recovery Manager en vue de l'installer sur un serveur VMM.
* **Manage Certificate** : modifiez les paramètres du certificat associé au coffre.
* **Delete** : supprimez un coffre.
* **Resynchronize virtual machines** : si Hyper-V Recovery Manager détecte que des machines virtuelles principales ou de récupération n'ont pas été synchronisées comme prévu, vous pouvez afficher la liste des machines virtuelles en question et les sélectionner pour tenter de les resynchroniser à partir du tableau de bord.

L'onglet **Dashboard** fournit les informations suivantes :

*  **Usage Overview** : indique le nombre de machines virtuelles dont la protection est assurée par Hyper-V Recovery Manager. 
*  **Quick Glance** : affiche des informations de configuration essentielles pour les services de récupération et le coffre Hyper-V Recovery Manager. Dans cette section, vous pouvez déterminer si le coffre est en ligne, identifier le certificat qui lui est attribué et sa date d'expiration, et trouver des détails sur l'abonnement du
  service.
*  **Recent Jobs** : affiche les tâches qui ont abouti ou échoué au cours des dernières 24 heures ou les tâches en cours ou en attente.
*  **Issues** : le tableau de bord affiche des informations sur les problèmes de connexion des serveurs VMM, ainsi que les problèmes liés aux paramètres de configuration du cloud ou à la synchronisation de la réplication des machines virtuelles. Vous pouvez obtenir des détails supplémentaires sur un problème, afficher les tâches associées au problème ou essayer de resynchroniser les machines virtuelles.


![Tableau de
bord](./media/hyper-v-recovery-manager-configure-vault/RS_Dashboard.png)

<h2><a  id="next" ></a>Étapes suivantes</h2>


*  Pour planifier et déployer Hyper-V Recovery Manager dans un
  environnement de production complet, consultez le [Guide de
  planification du Gestionnaire de récupération Hyper-V][5] et le [Guide de déploiement du Gestionnaire de
  récupération Hyper-V][6].
*  Pour toute question, visitez le [forum Azure Recovery Services][7].




[1]: https://manage.windowsazure.com
[2]: http://go.microsoft.com/fwlink/?LinkId=378266
[3]: http://go.microsoft.com/fwlink/?LinkId=323469
[4]: http://go.microsoft.com/fwlink/?LinkId=378271
[5]: http://go.microsoft.com/fwlink/?LinkId=321294
[6]: http://go.microsoft.com/fwlink/?LinkId=321295
[7]: http://go.microsoft.com/fwlink/?LinkId=313628
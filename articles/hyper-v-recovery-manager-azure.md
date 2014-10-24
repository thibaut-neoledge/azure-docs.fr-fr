<properties linkid="configure-hyper-v-recovery-vault" urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Configure Azure Site Recovery to protect virtual machines on Hyper-V server located in VMM clouds" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="Azure Site Recovery coordinates the replication, failover and recovery of Hyper-V virtual machines located in VMM clouds from one on-premises site to another. Azure Site Recovery can also replicate, failover, and recover Hyper-V virtual machine data between VMM clouds and Microsoft Azure." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Getting Started with Azure Site Recovery: On-Premises to Azure Protection" editor="jimbe" manager="cfreeman" authors="" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# Prise en main d'Azure Site Recovery : protection d'un serveur local vers Azure

<div class="dev-callout"> 

<p>Utilisez Azure Site Recovery pour prot&eacute;ger les machines virtuelles ex&eacute;cut&eacute;es sur des h&ocirc;tes Hyper-V qui sont situ&eacute;s dans des clouds System Center Virtual Machine Manager (VMM). Vous pouvez configurer deux types de protection&nbsp;:</p>

<ul>
<li><b>Protection d'un serveur local vers Azure</b>&nbsp;: r&eacute;pliquez les machines virtuelles locales situ&eacute;es sur des serveurs h&ocirc;tes Hyper-V dans des clouds VMM vers Azure. Vous pouvez configurer et activer les param&egrave;tres de protection dans des coffres Azure Site Recovery. Les donn&eacute;es des machines virtuelles sont r&eacute;pliqu&eacute;es du serveur Hyper-V local vers un stockage Azure.</li>

<li><b>Protection d'un serveur local vers un autre</b>&nbsp;: r&eacute;pliquez les machines virtuelles situ&eacute;es sur des serveurs h&ocirc;tes Hyper-V dans des clouds VMM d'un serveur local vers un autre. Vous pouvez configurer et activer les param&egrave;tres de protection dans des coffres Azure Site Recovery. Les donn&eacute;es des machines virtuelles sont r&eacute;pliqu&eacute;es d'un serveur Hyper-V local vers un autre. Azure Site Recovery orchestre simplement le processus.
Pour en savoir plus sur ce sc&eacute;nario, consultez la page <a href="http://go.microsoft.com/fwlink/?LinkId=398765">Prise en main de Azure Site Recovery&nbsp;: protection d'un serveur local vers un autre</a>.</li>

</ul>
 
<h2><a id="about"></a>&Agrave; propos de ce didacticiel</h2>


<P>Le but de ce didacticiel est de vous aider &agrave; d&eacute;ployer Azure Site Recovery pour une validation technique rapide. Il utilise, dans la mesure du possible, la voie la plus rapide et les param&egrave;tres par d&eacute;faut. Il d&eacute;crit notamment les proc&eacute;dures suivantes&nbsp;:
<ul>
<li>Configuration d'un coffre Azure Site Recovery&nbsp;: t&eacute;l&eacute;chargez un certificat dans le coffre et installez-le sur le serveur VMM source, puis g&eacute;n&eacute;rez une cl&eacute; de coffre. </li>
<li>Configuration du serveur VMM source et des serveurs h&ocirc;tes Hyper-V&nbsp;: installez le fournisseur Azure Site Recovery sur le serveur VMM source et installez l'agent des services de r&eacute;cup&eacute;ration Azure sur les serveurs h&ocirc;tes Hyper-V.</li>
<li>Configuration des clouds VMM&nbsp;: configurez les param&egrave;tres de protection pour les clouds sur le serveur VMM source.</li>
<li>Activation des machines virtuelles&nbsp;: activez la protection pour les machines virtuelles</li>
<li>Ex&eacute;cution d'un basculement&nbsp;: cr&eacute;ez un plan de r&eacute;cup&eacute;ration et ex&eacute;cutez un test de basculement.</li>
</ul>


Pour des informations sur un d&eacute;ploiement complet, consultez les pages suivantes&nbsp;:</P>

<UL>
<LI><a href="http://go.microsoft.com/fwlink/?LinkId=321294">Planifier un d&eacute;ploiement Azure Site Recovery</a>&nbsp;: d&eacute;crit les op&eacute;rations de planification que vous devez ex&eacute;cuter avant de commencer un d&eacute;ploiement complet.</LI>
<LI><a href="http://go.microsoft.com/fwlink/?LinkId=402679">D&eacute;ployer Azure Site Recovery&nbsp;: protection d'un serveur local vers Azure</a>&nbsp;: fournit des instructions pas &agrave; pas pour un d&eacute;ploiement complet.</LI>
<LI><a href="http://go.microsoft.com/fwlink/?LinkId=378272">G&eacute;rer et surveiller Azure Site Recovery</a>&nbsp;: d&eacute;crit la marche &agrave; suivre pour ex&eacute;cuter des basculements, et pour g&eacute;rer et surveiller votre d&eacute;ploiement.</LI>
</UL>
<P>Si vous rencontrez des probl&egrave;mes au cours de ce didacticiel, consultez l'article wiki <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery&nbsp;: erreurs courantes et r&eacute;solutions</a> ou publiez vos questions sur le <a href="http://go.microsoft.com/fwlink/?LinkId=313628">forum Azure Recovery Services</a>.</P>

</div>

## <span id="before"></span></a>Avant de commencer

<div class="dev-callout"> 
<P>Avant de commencer ce didacticiel, v&eacute;rifiez la configuration requise.</P>

<h3><a id="HVRMPrereq"></a>Configuration requise pour Azure</h3>

<UL>
<LI><b>Compte Azure</b>&nbsp;: vous avez besoin d'un compte Azure. Si vous n'avez pas encore de compte Azure, consultez la page <a href="http://aka.ms/try-azure">Version d'&eacute;valuation gratuite d'Azure</a>. Pour des informations sur la tarification, consultez la page <a href="http://go.microsoft.com/fwlink/?LinkId=378268">D&eacute;tails des prix du Gestionnaire Azure Site Recovery</a>.</LI>
<LI><b>Certificat</b>&nbsp;: vous avez besoin de t&eacute;l&eacute;charger dans le coffre un certificat de gestion (.cer) avec une cl&eacute; publique. Vous allez exporter ce certificat dans un fichier .pfx (avec une cl&eacute; priv&eacute;e) et l'importer vers chaque serveur VMM que vous souhaitez inscrire aupr&egrave;s du coffre. Pour les besoins du didacticiel, vous utiliserez un certificat auto-sign&eacute;. Pour un d&eacute;ploiement complet, vous pouvez utiliser un certificat SSL valide qui respecte la <a href="http://go.microsoft.com/fwlink/?LinkId=321294">configuration requise des certificats</a> d&eacute;crite dans le guide de planification</a>. </LI>


</LI>

<LI><b>Compte de stockage Azure</b>&nbsp;: vous avez besoin d'un compte de stockage Azure pour stocker les donn&eacute;es r&eacute;pliqu&eacute;es dans Azure. La g&eacute;o-r&eacute;plication doit &ecirc;tre activ&eacute;e pour ce compte. Ce dernier doit se trouver dans la m&ecirc;me r&eacute;gion que le service Azure Site Recovery et &ecirc;tre associ&eacute; au m&ecirc;me abonnement. Pour en savoir plus sur la configuration du stockage Azure, consultez la page <a href="http://go.microsoft.com/fwlink/?LinkId=398704">Introduction &agrave; Microsoft Azure Storage</a>.</LI>
</UL>

<h3><a id="VMMPrereq"></a>Configuration requise pour VMM</h3>

<UL>
<LI><b>Serveur VMM</b>&nbsp;: serveur VMM ex&eacute;cut&eacute; sur System Center&nbsp;2012&nbsp;R2.</LI>
<LI><b>Clouds VMM</b>&nbsp;: un cloud au moins sur le serveur VMM. Le cloud doit contenir les &eacute;l&eacute;ments suivants&nbsp;:
    <UL>
    <LI>un ou plusieurs groupes h&ocirc;tes VMM&nbsp;;</LI>
    <LI>un ou plusieurs clusters ou serveurs h&ocirc;tes Hyper-V dans chaque groupe h&ocirc;te&nbsp;;</LI>
    <li>une ou plusieurs machines virtuelles situ&eacute;es sur le serveur Hyper-V source dans le cloud. Les machines virtuelles doivent &ecirc;tre de la g&eacute;n&eacute;ration&nbsp;1.</li>
        </UL></LI>  
</UL>

<h3><a id="VMPrereq"></a>Configuration requise pour les machines virtuelles</h3>

<UL>
<LI><b>G&eacute;n&eacute;ration</b>&nbsp;: Azure ne prend en charge que les machines virtuelles de la g&eacute;n&eacute;ration&nbsp;1.</LI>
<LI>Pour une liste compl&egrave;te des conditions de prise en charge des machines virtuelles pour le basculement vers Azure, consultez la page <a href="http://go.microsoft.com/fwlink/?LinkId=402602">Configuration requise et prise en charge</a> dans le guide de planification. </LI>  
</UL>

<h2><a id="tutorial"></a>&Eacute;tapes du didacticiel</h2> 

Apr&egrave;s avoir v&eacute;rifi&eacute; la configuration requise, proc&eacute;dez comme suit&nbsp;:
<UL>
<LI><a href="#createcert">&Eacute;tape&nbsp;1&nbsp;: obtention et configuration des certificats</a>&nbsp;: obtenez un certificat .cer, exportez-le en tant que fichier .pfx et importez ce fichier dans les serveurs VMM.</LI>
<LI><a href="#vault">&Eacute;tape&nbsp;2&nbsp;: cr&eacute;ation d'un coffre</a>&nbsp;: cr&eacute;ez un coffre Azure Site Recovery.</LI>
<LI><a href="#upload">&Eacute;tape&nbsp;3&nbsp;: configuration du coffre</a>&nbsp;: t&eacute;l&eacute;chargez un certificat de gestion dans le coffre et g&eacute;n&eacute;rez une cl&eacute; pour le coffre. La cl&eacute; permet de s'assurer que le fournisseur situ&eacute; sur le serveur VMM ex&eacute;cute uniquement les commandes envoy&eacute;es par Azure Site Recovery.</LI>
<LI><a href="#download">&Eacute;tape&nbsp;4&nbsp;: installation de l'application du fournisseur</a>&nbsp;: ex&eacute;cutez l'application du fournisseur Azure Site Recovery sur le serveur VMM. Le fournisseur est install&eacute; et le serveur VMM est inscrit dans le coffre.</LI>
<LI><a href="#agent">&Eacute;tape&nbsp;5&nbsp;: installation de l'application de l'agent</a>&nbsp;: installez l'agent Microsoft Azure Recovery Services sur chaque h&ocirc;te Hyper-V.</LI>
<LI><a href="#clouds">&Eacute;tape&nbsp;6&nbsp;: configuration de la protection des clouds</a>&nbsp;: configurez les param&egrave;tres de protection des clouds VMM.</LI>
<LI><a href="#NetworkMapping">&Eacute;tape&nbsp;7&nbsp;: configuration du mappage r&eacute;seau</a>&nbsp;: vous pouvez &eacute;ventuellement configurer le mappage r&eacute;seau de mani&egrave;re &agrave; mapper les r&eacute;seaux de machines virtuelles sources vers les r&eacute;seaux Azure cibles.</LI>
<LI><a href="#virtualmachines">&Eacute;tape&nbsp;8&nbsp;: activation de la protection pour les machines virtuelles</a>&nbsp;: activez la protection pour les machines virtuelles situ&eacute;es dans des clouds VMM prot&eacute;g&eacute;s.</LI>
<LI><a href="#recovery plans">&Eacute;tape&nbsp;9&nbsp;: configuration et ex&eacute;cution des plans de r&eacute;cup&eacute;ration</a>&nbsp;: cr&eacute;ez un plan de r&eacute;cup&eacute;ration et ex&eacute;cutez un test de basculement pour le plan.</LI>
</UL>



<a name="createcert"></a> <h2>&Eacute;tape&nbsp;1&nbsp;: Obtention et configuration de certificats</h2>

Pour obtenir et configurer des certificats, proc&eacute;dez comment suit&nbsp;:
<OL>
<LI><a href="#obtaincert">Obtention d'un certificat auto-sign&eacute; pour la proc&eacute;dure pas &agrave; pas</a>&nbsp;: obtenez un certificat &agrave; l'aide de l'outil MakeCert.</LI>
<LI><a href="#exportcert">Exportation du certificat au format .pfx</a>&nbsp;: sur le serveur sur lequel a &eacute;t&eacute; cr&eacute;&eacute; le certificat, exportez le fichier .cer en tant que fichier .pfx (avec la cl&eacute; priv&eacute;e). </LI>
<LI><a href="#importcert">Importation du certificat .pfx dans le serveur VMM</a>&nbsp;: apr&egrave;s l'exportation, importez le fichier .pfx dans le dossier personnel du magasin de l'ordinateur local sur le serveur VMM que vous voulez inscrire aupr&egrave;s du coffre.</LI>
</OL>


<h3><a id="obtaincert"></a>Obtention d'un certificat auto-sign&eacute; (.cer)</h3>
<P>Cr&eacute;ez un certificat .cer x.509 qui respecte enti&egrave;rement la configuration requise des certificats&nbsp;:</P>
<ol>
<LI>
Sur l'ordinateur sur lequel vous souhaitez ex&eacute;cuter MakeCert, t&eacute;l&eacute;chargez la derni&egrave;re version du <a href="http://go.microsoft.com/fwlink/?LinkId=378269">Kit de d&eacute;veloppement logiciel (SDK) Windows</a>. Il n'est pas n&eacute;cessaire d'installer l'int&eacute;gralit&eacute; du Kit de d&eacute;veloppement logiciel (SDK).</LI>
<ol>
<LI>Sur la page Sp&eacute;cifier un emplacement, s&eacute;lectionnez <b>Installer le Kit de d&eacute;veloppement logiciel Windows pour Windows&nbsp;8.1 sur cet ordinateur</b>.</LI>
<LI>Sur la page S&eacute;lectionnez les composants du programme que vous voulez installer, effacez toutes les options &agrave; l'exception de <b>Kit de d&eacute;veloppement logiciel Windows</b>.</LI>
<LI>&Agrave; la fin de l'installation, v&eacute;rifiez que makecert.exe figure bien dans le dossier C:\ProgramFiles (x86)\Windows Kits\<i>VersionWindows</i>\bin\x64.</LI>
<LI>Ouvrez une invite de commandes (cmd.exe) avec des privil&egrave;ges d'administrateur et acc&eacute;dez au dossier makecert.exe.</LI> 
<LI>Ex&eacute;cutez la commande suivante pour cr&eacute;er votre certificat auto-sign&eacute;. Remplacez CertificateName par le nom que vous voulez attribuer au certificat, puis indiquez la date d'expiration effective de votre certificat apr&egrave;s -e&nbsp;:</LI>
<code>
makecert.exe -r -pe -n CN=CertificateName -ss my -sr localmachine -eku 1.3.6.1.5.5.7.3.2 -len 2048 -e 01/01/2016 CertificateName.cer</code>
</ol>
<P>Un &eacute;tat r&eacute;ussi indique que le certificat a &eacute;t&eacute; cr&eacute;&eacute;. Il est stock&eacute; dans le m&ecirc;me dossier que makecert.exe. Vous pouvez le d&eacute;placer vers un emplacement plus accessible pour l'exportation.</P>

<h3><a id="exportcert"></a>Exportation du certificat au format .pfx</h3>
<P>Pour exporter le fichier .cer au format .pfx, proc&eacute;dez comme suit&nbsp;:</P>
<ol>
<li>Un &eacute;tat r&eacute;ussi indique que le certificat a &eacute;t&eacute; cr&eacute;&eacute;. Il est stock&eacute; dans le m&ecirc;me dossier que makecert.exe. Vous pouvez le d&eacute;placer vers un emplacement plus accessible pour l'exportation.</li>
<li>Dans le volet d'informations, cliquez sur le certificat que vous souhaitez g&eacute;rer.</li>
<li>Dans le menu <b>Action</b>, pointez sur <b>Toutes les t&acirc;ches</b>, puis cliquez sur <b>Exporter</b>. L'Assistant Exportation du certificat s'affiche. Cliquez sur <b>Suivant</b>.</li>
<li>Sur la page <b>Exporter la cl&eacute; priv&eacute;e</b>, cliquez sur <b>Oui</b> pour exporter la cl&eacute; priv&eacute;e. Cliquez sur <b>Suivant</b>. Notez que cette op&eacute;ration n'est n&eacute;cessaire que si vous souhaitez exporter la cl&eacute; priv&eacute;e vers d'autres serveurs apr&egrave;s l'installation.</li>
<li>Sur la page Format de fichier d'exportation, s&eacute;lectionnez <b>&Eacute;change d'informations personnelles - PKCS #12 (.PFX)</b>. Cliquez sur <b>Suivant</b>.</li>
<li>Sur la page <b>Mot de passe</b>, tapez et confirmez le mot de passe utilis&eacute; pour chiffrer la cl&eacute; priv&eacute;e. Cliquez sur <b>Suivant</b>.</li>
<li>Suivez les pages de l'Assistant pour exporter le certificat au format .pfx.</li>
</ol>

<h3><a id="importcert"></a>Importation du certificat .pfx sur les serveurs VMM</h3>
<p>Apr&egrave;s l'exportation, copiez le fichier .pfx sur le serveur VMM, puis importez-le. Notez que si vous avez ex&eacute;cut&eacute; MakeCert.exe sur un serveur VMM, vous n'avez pas besoin d'importer le certificat sur ce serveur.</p>
 
<ol>
<li>Copiez les fichiers de certificat de cl&eacute; priv&eacute;e (.pfx) &agrave; un emplacement sur le serveur local.</li>
<li>Dans le composant logiciel enfichable MMC Certificats, s&eacute;lectionnez <b>Compte d'ordinateur</b>, puis cliquez sur <b>Suivant</b>.</li>
<li>S&eacute;lectionnez <b>Ordinateur local</b>, puis cliquez sur <b>Terminer</b>.</li>
<li>Dans la console MMC, d&eacute;veloppez <b>Certificats</b>, cliquez avec le bouton droit sur <b>Personnel</b>, pointez sur <b>Toutes les t&acirc;ches</b>, puis cliquez sur <b>Importer</b> pour d&eacute;marrer l'Assistant Importation du certificat.</li>
<li>Sur la page Fichier &agrave; importer, cliquez sur <b>Parcourir</b> et recherchez le dossier dans lequel se trouve le fichier de certificat .pfx qui contient le certificat &agrave; importer. S&eacute;lectionnez le fichier appropri&eacute;, puis cliquez sur <b>Ouvrir</b>.</li>
<li>Sur la page Mot de passe, dans la zone <b>Mot de passe</b>, tapez le mot de passe du fichier de cl&eacute; priv&eacute;e .pfx et cliquez sur <b>Suivant</b>.</li>
<li>Sur la page Magasin de certificats, s&eacute;lectionnez <b>Placer tous les certificats dans le magasin suivant</b>, cliquez sur <b>Parcourir</b>, s&eacute;lectionnez le magasin <b>Personnel</b>, cliquez sur <b>OK</b>, puis cliquez sur <b>Suivant</b>. Terminez l'Assistant.</li>
</ol>

Apr&egrave;s avoir effectu&eacute; ces &eacute;tapes, vous pouvez choisir le certificat .cer &agrave; t&eacute;l&eacute;charger lors de la configuration du coffre et s&eacute;lectionner le certificat .pfx au moment d'inscrire un server VMM pendant l'installation du fournisseur.
</div>

<a name="vault"></a>

## Étape 2 : création d'un coffre


1.  Connectez-vous au [portail de gestion][portail de gestion].

2.  Développez **Services de données** et **Services de récupération**, puis cliquez sur **Coffre de récupération de site**.

3.  Cliquez sur **Créer un nouveau**, puis sur **Création rapide**.

4.  Dans **Name**, entrez un nom convivial pour identifier le coffre.

5.  Dans **Region**, sélectionnez la région géographique du coffre. Les régions géographiques disponibles sont les suivantes : Asie orientale, Europe de l'Ouest, Ouest des États-Unis, Est des États-Unis, Europe du Nord et Asie du Sud-Est.
6.  Cliquez sur **Create vault**.

    ![Nouveau coffre][Nouveau coffre]

Vérifiez la barre d'état pour vous assurer que le coffre a été créé correctement. Le coffre est répertorié comme **Actif** sur la page principale des services de récupération.

<a name="upload"></a>

## Étape 3 : configuration du coffre


1.  Sur la page **Services de récupération**, cliquez sur le coffre afin d'ouvrir la page Démarrage rapide. Vous pouvez également ouvrir cette page à tout moment à l'aide de l'icône correspondante.

    ![Icône Quick Start][Icône Quick Start]

2.  Dans la liste déroulante **Installer la récupération**, sélectionnez **Entre un site local et Microsoft Azure**.
3.  Pour télécharger le certificat (.cer) dans le coffre, cliquez sur **Gérer le certificat**.

    ![Quick Start][Quick Start]

4.  Dans la boîte de dialogue **Gérer le certificat**, cliquez sur **Rechercher le fichier** et sélectionnez le fichier .cer.

    ![Gérer le certificat][Gérer le certificat]

5.  Pour générer une clé pour le coffre, cliquez sur **Obtenir la clé du coffre**. La clé est générée automatiquement. Si vous régénérez une clé, elle remplace la clé précédente. Notez que vous aurez besoin de cette clé ultérieurement lors de l'installation du fournisseur Azure Site Recovery sur le serveur VMM.

    ![Gérer le certificat][1]

<a name="download"></a>

## Étape 4 : installation du fournisseur Azure Site Recovery


1.  Sur la page **Démarrage rapide**, cliquez sur **Télécharger le fournisseur** pour obtenir la dernière version du fichier d'installation du fournisseur.

    ![Download Provider File][Download Provider File]

2.  Exécutez ce fichier sur le serveur VMM source.

    ![Télécharger l'agent][Télécharger l'agent]

3.  Une fois le fournisseur installé, poursuivez l'installation pour inscrire le serveur dans le coffre.
    ![Setup Complete][Setup Complete]
4.  Sur la page Connexion Internet, indiquez la façon dont le fournisseur exécuté sur le serveur VMM se connecte à Internet. Cliquez sur **Suivant** pour utiliser les paramètres de connexion Internet par défaut configurés sur le serveur.
    ![Internet Settings][Internet Settings]
5.  Sur la page Inscription du coffre, procédez comme suit :

    -   Sélectionnez la clé privée (.pfx) que vous avez importée sur le serveur VMM.
    -   Sélectionnez le coffre dans lequel vous voulez inscrire le serveur.
    -   Spécifiez la clé du coffre. Il s'agit de la clé de coffre que vous avez générée précédemment. Coupez et collez la valeur de la clé à partir de la page Démarrage rapide.

    </p>
    ![Inscription de certificat][Inscription de certificat]

6.  Sur la page Chiffrement des données, vous pouvez indiquer si vous voulez autoriser le chiffrement des données pendant la réplication pour un cloud particulier. Si vous sélectionnez cette option, un certificat SSL est généré automatiquement. Lors de l'exécution d'un basculement, vous devez sélectionner ce certificat. Après avoir activé ce paramètre, vous pouvez activer ou désactiver le chiffrement des données pour un cloud dans le portail Azure Site Recovery. Pour les besoins de ce didacticiel, conservez le paramètre par défaut et cliquez sur **Suivant**.

    ![Coffre de certificats][Coffre de certificats]

7.  Sur la page Serveur VMM, procédez comme suit :

    -   Indiquez un nom convivial pour le serveur VMM. Ce nom sert à identifier le serveur dans la console Azure Site Recovery.
    -   Sélectionnez **Synchroniser les métadonnées des clouds avec le coffre** pour synchroniser les informations sur les clouds VMM avec le coffre Azure Site Recovery. Cette action se produit une seule fois sur chaque serveur. Si vous ne souhaitez pas synchroniser tous les clouds, vous pouvez les publier individuellement pour les synchroniser avant de configurer les paramètres de protection de cloud.

8.  Cliquez sur **Register** pour terminer le processus.

    ![PublishCloud][PublishCloud]

Dès lors qu'un serveur est correctement inscrit, son nom convivial s'affiche sous l'onglet **Ressources** de la page Serveurs du coffre.

## <span id="storage"></span></a>Étape 5 : installation de l'agent Azure Recovery Services

Installez l'agent Azure Recovery Services sur chaque serveur hôte Hyper-V situé dans les clouds VMM que vous souhaitez protéger.

1.  Sur la page Démarrage rapide, cliquez sur **Télécharger l'agent des services Azure Site Recovery et l'installer sur les hôtes** afin d'obtenir la dernière version du fichier d'installation de l'agent.

    ![Install Recovery Services Agent][Install Recovery Services Agent]

2.  Exécutez le fichier d'installation sur chaque serveur hôte Hyper-V situé dans les clouds VMM que vous souhaitez protéger.
3.  Sur la page Vérification de la configuration requise, cliquez sur **Suivant**. Tous les éléments manquants de la configuration requise sont automatiquement installés.

![Prerequisites Recovery Services Agent][Prerequisites Recovery Services Agent]

1.  Sur la page Paramètres d'installation, indiquez où vous voulez installer l'agent et sélectionnez l'emplacement du cache dans lequel les métadonnées de sauvegarde seront installées. Cliquez ensuite sur **Installer**.

Sur la page Connexion Internet, indiquez la façon dont le fournisseur exécuté sur le serveur VMM se connecte à Internet. Cliquez sur **Suivant** pour utiliser les paramètres de connexion Internet par défaut configurés sur le serveur.![Recovery Services Agent location][Recovery Services Agent location]

## <span id="clouds"></span></a>Étape 6 : configuration des paramètres de protection de cloud

Une fois les serveurs VMM inscrits, vous pouvez configurer les paramètres de protection de cloud. Comme vous avez activé l'option **Synchroniser les données des clouds avec le coffre** lors de l'installation du fournisseur, tous les clouds sur le serveur VMM apparaîtront sous l'onglet **Éléments protégés** dans le coffre.

![Cloud publié][Cloud publié]

Configurez les paramètres de protection comme suit :

1.  Sur la page Démarrage rapide, cliquez sur **Configurer la protection pour les clouds VMM**.

![Enable cloud protection][Enable cloud protection]

1.  Sous l'onglet **Éléments protégés**, cliquez sur le cloud que vous souhaitez configurer, puis accédez à l'onglet **Configuration**.
2.  Dans la zone **Cible**, sélectionnez **Microsoft Azure**.
3.  Dans la zone **Compte de stockage**, sélectionnez le stockage Azure dans lequel vous voulez stocker les machines virtuelles.
4.  En regard de **Chiffrer les données stockées**, cliquez sur **Désactivé**. Ce paramètre indique que les données doivent être répliquées avec chiffrement entre le site local et Azure.
5.  Dans la zone **Fréquence de copie**, conservez le paramètre par défaut. Cette valeur indique la fréquence de synchronisation des données entre les emplacements source et cible.
6.  Dans la zone **Conserver les points de récupération pour**, conservez le paramètre par défaut. Avec une valeur par défaut de zéro, seul le dernier point de récupération d'une machine virtuelle principale est stocké sur un serveur hôte de réplication.
7.  Dans la zone **Fréquence des instantanés cohérents au niveau applicatif**, conservez le paramètre par défaut. Cette valeur indique la fréquence de création de captures instantanées. Ces dernières utilisent le service VSS (Volume Shadow Copy Service) pour s'assurer que les applications sont dans un état cohérent lors de la prise des captures instantanées. Si vous définissez une valeur, assurez-vous qu'elle est inférieure au nombre de points de récupération supplémentaires que vous configurez.

    ![Configuration de cloud][Configuration de cloud]

8.  Dans la zone **Heure de démarrage de la réplication**, indiquez l'heure à laquelle la réplication initiale des données dans Azure doit démarrer. Le fuseau horaire du serveur hôte Hyper-V est utilisé à cet effet. Nous vous recommandons de planifier la réplication initiale sur des heures creuses.

    ![Cloud replication settings][Cloud replication settings]

Suite à l'enregistrement des paramètres, une tâche est créée et peut être surveillée sous l'onglet **Tâches**. Tous les serveurs hôtes Hyper-V dans le cloud VMM source seront configurés pour la réplication. Les paramètres du cloud peuvent être modifiés sous l'onglet **Configurer**. Pour modifier l'emplacement ou le stockage cible, vous devez supprimer la configuration du cloud, puis reconfigurer ce dernier. Notez que si vous modifiez le compte de stockage, la modification n'est appliquée que pour les machines virtuelles qui sont activées pour la protection après la modification du compte. Les machines virtuelles existantes ne sont pas migrées vers le nouveau compte de stockage.

## <span id="networkmapping"></span></a>Étape 7 : configuration du mappage réseau

Vous pouvez éventuellement activer le mappage réseau pour mapper les réseaux de machines virtuelles sources vers les réseaux virtuels Azure cibles. Si vous ne créez pas de mappages réseau, seules les machines virtuelles dont le basculement s'effectue dans le même plan de récupération peuvent se connecter les unes aux autres dans Azure. Si vous créez un mappage réseau, toutes les machines virtuelles dont le basculement s'effectue dans le même réseau peuvent se connecter les unes aux autres, indépendamment du plan de récupération auquel elles appartiennent. Par ailleurs, si une passerelle réseau est configurée sur le réseau Azure cible, les machines virtuelles peuvent se connecter aux machines virtuelles locales. Si vous souhaitez configurer le mappage réseau dans le cadre de ce didacticiel, consultez la page [Configuration du mappage réseau][Configuration du mappage réseau] dans le guide de déploiement.

## <span id="virtualmachines"></span></a>Étape 8 : activation de la protection pour les machines virtuelles

Dès lors que les serveurs, les clouds et les réseaux ont été configurés correctement, vous pouvez activer la protection pour les machines virtuelles du cloud.

Avant d'activer la protection pour une machine virtuelle, vérifiez et mettez à jour ses paramètres si besoin est. Par exemple, le système d'exploitation invité de la machine virtuelle doit être Windows Server 2008 ou une version ultérieure, ou Linux. La machine virtuelle doit être de la génération 1 uniquement. Pour une liste complète des exigences liées à Azure Site Recovery, consultez la page [Configuration requise et prise en charge][Configuration requise et prise en charge] dans le guide de planification.

Dans la console VMM, vérifiez et mettez à jour les paramètres. Les paramètres du système d'exploitation pour la machine virtuelle peuvent être modifiés sur la page Général des propriétés de la machine virtuelle et les paramètres du disque du système d'exploitation sur la page Configuration matérielle.

![Modify virtual machine properties][Modify virtual machine properties]

![Modify virtual machine properties][2]

1.  Pour activer la protection, sous l'onglet **Machines virtuelles** dans le cloud où se trouve la machine virtuelle, cliquez sur **Activer la protection**, puis sélectionnez **Ajouter des machines virtuelles**.
2.  Dans la liste des machines virtuelles dans le cloud, sélectionnez celle que vous souhaitez protéger.

![Enable virtual machine protection][Enable virtual machine protection]

Une fois la protection activée, deux tâches sont créées. La première à s'exécuter est la tâche d'activation de la protection. Ensuite, à l'issue de la réplication initiale, la tâche de finalisation de la protection est exécutée. La machine virtuelle n'est prête pour le basculement qu'une fois ces tâches terminées correctement. Vous pouvez surveiller la progression sous l'onglet **Tâches**.

![Virtual machine protection job][Virtual machine protection job]

## <span id="recoveryplans"></span></a>Étape 9 : configuration et exécution d'un plan de récupération

Un plan de récupération rassemble des machines virtuelles dans des groupes de telle sorte que leur basculement puisse se faire comme s'il s'agissait d'une seule et même unité Pour créer un plan de récupération, procédez comme suit :

1.  Sous l'onglet **Plans de récupération**, cliquez sur **Créer**.
2.  Sur la page Spécifier le nom de la page de récupération et la cible, sélectionnez le serveur VMM source et Azure comme cible.

    ![Créer un plan de récupération][Créer un plan de récupération]

3.  Sur la page Sélectionner les machines virtuelles, sélectionnez les machines virtuelles à ajouter au plan de récupération. Seules les machines virtuelles pour lesquelles la protection est activée sont affichées. Celles-ci sont ajoutées au groupe par défaut (Groupe 1) du plan de récupération.
4.  Cliquez sur la coche pour créer le plan de récupération.

    ![Machines virtuelles du plan de récupération][Machines virtuelles du plan de récupération]

### <span id="run"></span></a>Test de basculement

Les plans de récupération peuvent être exécutés dans le cadre d'un test proactif, d'un basculement planifié ou à l'occasion d'un basculement non planifié. Cette procédure pas à pas explique comment effectuer un test de basculement de VMM vers Azure pour vérifier que votre stratégie de basculement fonctionne comme prévu. Il simule votre mécanisme de basculement et de récupération dans un réseau isolé. Notez les points suivants :

-   Si vous voulez vous connecter à la machine virtuelle dans Azure avec le Bureau à distance après le basculement, activez Connexion Bureau à distance sur la machine virtuelle avant d'exécuter le test de basculement.
-   Après le basculement, vous utiliserez une adresse IP publique pour vous connecter à la machine virtuelle dans Azure avec le Bureau à distance. Dans ce cas, assurez-vous qu'aucune de vos stratégies de domaine ne vous empêche de vous connecter à une machine virtuelle avec une adresse publique.

</p>
#### 

<p>
<span id="runtest"></span></a>Exécution du basculement

</h5>
Pour exécuter un test de basculement pour un plan de récupération, procédez comme suit :

</p>
1.  Avant d'exécuter le plan de récupération, vous pouvez valider les paramètres des machines virtuelles dans le plan. À cet effet, sur la page des propriétés du cloud, cliquez sur la machine virtuelle. Sur la page Propriétés de la source et de la cible pour le basculement, vérifiez les paramètres. Vérifiez notamment que la taille suggérée pour la machine virtuelle cible dans Azure ainsi que les paramètres réseau sont corrects. Pour une liste complète de la configuration requise pour les machines virtuelles, consultez la page [Configuration requise et prise en charge][3].

![Virtual Machine Properties][Virtual Machine Properties]

1.  Sous l'onglet **Plans de récupération**, sélectionnez le plan de récupération requis.
2.  Pour lancer le basculement, cliquez sur le bouton **Test de basculement**.
3.  Sur la page Confirmer le test de basculement, sélectionnez le réseau Azure auquel vos machines virtuelles seront connectées après le basculement. Vous pouvez également sélectionner **Aucun réseau**. Lorsque ce paramètre est sélectionné, les machines virtuelles ne sont pas connectées à un réseau après le basculement.

![Test Failover][Test Failover]

Vous pouvez suivre la progression des tâches du test de basculement sous l'onglet **Jobs**. À la fin du test de basculement, effectuez les opérations suivantes :

1.  Vérifiez que les machines virtuelles démarrent correctement dans Azure.
2.  Cliquez sur **Notes** pour consigner et enregistrer les éventuelles observations associées au test de basculement.
3.  En plus des informations sous l'onglet **Tâches**, lorsque vous exécutez un test de basculement pour un plan de récupération, le processus s'affiche sur la page d'informations du plan de récupération. Vous pouvez afficher les étapes et l'état du basculement, et afficher ou créer des notes pour le test de basculement.

#### 

<p>
<span id="runtest"></span></a>Surveillance de l'activité

</h5>

Vous pouvez utiliser l'onglet **Tâches** et le **Tableau de bord** pour afficher et surveiller les principales tâches exécutées par le coffre Azure Site Recovery, notamment la configuration de la protection pour un cloud, l'activation et la désactivation de la protection pour une machine virtuelle, l'exécution d'un basculement (planifié, non planifié ou test) et la validation d'un basculement non planifié.

</p>
À partir de l'onglet **Tâches**, vous pouvez afficher les tâches, explorer au niveau du détail les tâches et les erreurs, exécuter des requêtes de tâches pour extraire les tâches qui correspondent à des critères déterminés, exporter des tâches vers Excel et redémarrer des tâches ayant échoué.

À partir du **Tableau de bord**, vous pouvez télécharger les dernières versions des fichiers d'installation du fournisseur et de l'agent, obtenir des informations de configuration pour le coffre, afficher le nombre de machines virtuelles dont la protection est gérée par le coffre, afficher les tâches récentes, gérer le certificat du coffre et resynchroniser les machines virtuelles.

Pour plus d'informations sur les interactions avec les tâches et le tableau de bord, consultez le [Guide sur les opérations et la surveillance][Guide sur les opérations et la surveillance].

## <span id="next"></span></a>Étapes suivantes

-   Pour planifier et déployer Azure Site Recovery dans un environnement de production complet, consultez le [Guide de planification Azure Site Recovery][Planifier un déploiement Azure Site Recovery] et le [Guide de déploiement Azure Site Recovery][Guide de déploiement Azure Site Recovery].
-   Pour toute question, visitez le [forum Azure Recovery Services][forum Azure Recovery Services].

  [Prise en main de Azure Site Recovery : protection d'un serveur local vers un autre]: http://go.microsoft.com/fwlink/?LinkId=398765
  [Planifier un déploiement Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=321294
  [Déployer Azure Site Recovery : protection d'un serveur local vers Azure]: http://go.microsoft.com/fwlink/?LinkId=402679
  [Gérer et surveiller Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=378272
  [Azure Site Recovery : erreurs courantes et résolutions]: http://go.microsoft.com/fwlink/?LinkId=389879
  [forum Azure Recovery Services]: http://go.microsoft.com/fwlink/?LinkId=313628
  [Version d'évaluation gratuite d'Azure]: http://aka.ms/try-azure
  [Détails des prix du Gestionnaire Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=378268
  [Introduction à Microsoft Azure Storage]: http://go.microsoft.com/fwlink/?LinkId=398704
  [Configuration requise et prise en charge]: http://go.microsoft.com/fwlink/?LinkId=402602
  [Étape 1 : obtention et configuration des certificats]: #createcert
  [Étape 2 : création d'un coffre]: #vault
  [Étape 3 : configuration du coffre]: #upload
  [Étape 4 : installation de l'application du fournisseur]: #download
  [Étape 5 : installation de l'application de l'agent]: #agent
  [Étape 6 : configuration de la protection des clouds]: #clouds
  [Étape 7 : configuration du mappage réseau]: #NetworkMapping
  [Étape 8 : activation de la protection pour les machines virtuelles]: #virtualmachines
  [Étape 9 : configuration et exécution des plans de récupération]: #recovery%20plans
  [Obtention d'un certificat auto-signé pour la procédure pas à pas]: #obtaincert
  [Exportation du certificat au format .pfx]: #exportcert
  [Importation du certificat .pfx dans le serveur VMM]: #importcert
  [Kit de développement logiciel (SDK) Windows]: http://go.microsoft.com/fwlink/?LinkId=378269
  [portail de gestion]: https://manage.windowsazure.com
  [Nouveau coffre]: ./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png
  [Icône Quick Start]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png
  [Quick Start]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStart.png
  [Gérer le certificat]: ./media/hyper-v-recovery-manager-configure-vault/SR_ManageCert.png
  [1]: ./media/hyper-v-recovery-manager-configure-vault/SR_VaultKey.png
  [Download Provider File]: ./media/hyper-v-recovery-manager-configure-vault/SR_DownloadProviderFile.png
  [Télécharger l'agent]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderInstall1.png
  [Setup Complete]: ./media/hyper-v-recovery-manager-configure-vault/SR_InstallWiz.png
  [Internet Settings]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png
  [Inscription de certificat]: ./media/hyper-v-recovery-manager-configure-vault/SR_CertReg1.png
  [Coffre de certificats]: ./media/hyper-v-recovery-manager-configure-vault/SR_CertReg2.png
  [PublishCloud]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMMServerName.png
  [Install Recovery Services Agent]: ./media/hyper-v-recovery-manager-configure-vault/SR_HyperVAgent.png
  [Prerequisites Recovery Services Agent]: ./media/hyper-v-recovery-manager-configure-vault/SR_AgentPrereqs.png
  [Recovery Services Agent location]: ./media/hyper-v-recovery-manager-configure-vault/SR_AgentInstallSettings.png
  [Cloud publié]: ./media/hyper-v-recovery-manager-configure-vault/SR_Clouds.png
  [Enable cloud protection]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableCloudProtection.png
  [Configuration de cloud]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudConfigureE2A1.png
  [Cloud replication settings]: ./media/hyper-v-recovery-manager-configure-vault/SR_PublishCloudSetup2.png
  [Configuration du mappage réseau]: http://go.microsoft.com/fwlink/?LinkId=402533
  [Modify virtual machine properties]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMMVMPropsGeneral.png
  [2]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMMVMPropsHW.png
  [Enable virtual machine protection]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableProtectionVM.png
  [Virtual machine protection job]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
  [Créer un plan de récupération]: ./media/hyper-v-recovery-manager-configure-vault/SR_RecoveryPlan1.png
  [Machines virtuelles du plan de récupération]: ./media/hyper-v-recovery-manager-configure-vault/SR_RecoveryPlan2.png
  [3]: http://go.microsoft.com/fwlink/?LinkId=402676
  [Virtual Machine Properties]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMPropertiesE2A.png
  [Test Failover]: ./media/hyper-v-recovery-manager-configure-vault/SR_TestFailover.png
  [Guide sur les opérations et la surveillance]: http://go.microsoft.com/fwlink/?LinkId=398534
  [Guide de déploiement Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=321295

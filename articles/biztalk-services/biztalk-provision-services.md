<properties
	pageTitle="Créer des services Azure BizTalk Services dans le portail Azure | Microsoft Azure"
	description="Apprenez à approvisionner ou à créer des services Azure BizTalk Services dans le portail Azure ; MABS, WABS"
	services="biztalk-services"
	documentationCenter=""
	authors="MandiOhlinger"
	manager="dwrede"
	editor="cgronlun"/>

<tags
	ms.service="biztalk-services"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="12/01/2015"
	ms.author="mandia"/>



# Créer des services BizTalk Services à l’aide du portail Azure

Cet article décrit la procédure de création de services Azure BizTalk Services dans le portail Azure.

> [AZURE.TIP]Pour vous connecter au portail Azure, vous devez disposer d’un compte Azure et d’un abonnement Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Consultez [Version d'évaluation gratuite d'Azure](http://go.microsoft.com/fwlink/p/?LinkID=239738).

## Création d'un service BizTalk
Selon l'édition que vous choisissez, tous les paramètres du service BizTalk peuvent ne pas être disponibles.

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. En bas du volet de navigation, sélectionnez **NEW** : ![Sélectionner le bouton New][NEWButton]

3. Sélectionnez **APP SERVICES** > **SERVICE BIZTALK** > **CRÉATION PERSONNALISÉE** : ![Sélectionner BizTalk Services, puis Custom Create][NewBizTalkService]

4. Entrez les paramètres du service BizTalk :

	<table border="1">
<tr>
<td><strong>Nom du service BizTalk</strong></td>
<td>Vous pouvez entrer n'importe quel nom, mais soyez spécifique. Voici quelques exemples&#160;:<br/><br/>
<em>masociété</em>.biztalk.windows.net<br/>
<em>masociétémonapplication</em>.biztalk.windows.net<br/>
<em>monapplication</em>.biztalk.windows.net<br/><br/>«&#160;.biztalk.windows.net&#160;» est automatiquement ajouté au nom que vous entrez. L'URL obtenue est utilisée pour accéder à votre service BizTalk. Par exemple <strong>https://<em>monapplication</em>.biztalk.windows.net</strong>.
</td>
</tr>
<tr>
<td><strong>Édition</strong></td>
<td>Si vous êtes en phase de test ou de développement, choisissez <strong>Développeur</strong>. Si vous êtes à l’étape de production, utilisez le <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302279">tableau comparatif des éditions de BizTalk Services</a> pour déterminer si l’édition <strong>Premium</strong>, <strong>Standard</strong> ou <strong>De base</strong> est le bon choix pour votre scénario d’entreprise.
</td>
</tr>
<tr>
<td><strong>Région</strong></td>
<td>Sélectionnez la région géographique dans laquelle héberger votre service BizTalk.</td>
</tr>
<tr>
<td><strong>URL du domaine</strong></td>
<td><strong>Facultatif</strong>. Par défaut, l'URL du domaine est <em>YourBizTalkServiceName</em>.biztalk.windows.net. Un domaine personnalisé peut également être entré. Par exemple, si votre domaine est <em>contoso</em>, vous pouvez entrer&#160;: <br/><br/>
<em>MaSociété</em>.contoso.com<br/>
<em>MaSociétéMonApplication</em>.contoso.com<br/>
<em>MonApplication</em>.contoso.com<br/>
<em>NomServiceBizTalk</em>.contoso.com<br/>
</td>
</tr>
</table>
Sélectionnez la flèche SUIVANT.

5. Entrez les paramètres de stockage et de base de données :

	<table border="1">
<tr>
<td><strong>Compte de stockage de surveillance/archivage</strong></td>
<td>Sélectionnez un compte de stockage existant ou créez un nouveau compte de stockage. <br/><br/>Si vous créez un compte de stockage, entrez le <strong>Nom du compte de stockage</strong>.</td>
</tr>
<tr>
<td><strong>Base de données de suivi</strong></td>
<td>Si vous utilisez une base de données SQL Azure existante, celle-ci ne peut pas être utilisée par un autre service BizTalk. Vous devez disposer de l'identifiant et du mot de passe entrés lors de la création de ce serveur de base de données SQL Azure.<br/><br/><strong>CONSEIL</strong>&#160;: créez la base de données de suivi et le compte de stockage de surveillance/d’archivage dans la même région que le service BizTalk.</td>
</tr>
</table>
Sélectionnez la flèche SUIVANT.

6. Entrez les paramètres de base de données :

	<table border="1">
<tr>
<td><strong>Name</strong></td>
<td>Disponible lorsque l'option <strong>Create a new SQL Database instance</strong> est sélectionnée dans l'écran précédent.
<br/><br/>
Entrez le nom de la base de données SQL que votre service BizTalk doit utiliser.</td>
</tr>
<tr>
<td><strong>Serveur</strong></td>
<td>Disponible lorsque l'option <strong>Create a new SQL Database instance</strong> est sélectionnée dans l'écran précédent.
<br/><br/>
Sélectionnez un serveur de base de données SQL existant ou créez un serveur de base de données SQL.</td>
</tr>
<tr>
<td><strong>Nom de connexion du serveur</strong></td>
<td>Entrez le nom d'utilisateur de connexion.</td>
</tr>
<tr>
<td><strong>Mot de passe de connexion du serveur</strong></td>
<td>Entrez le mot de passe de connexion.</td>
</tr>
<tr>
<td><strong>Région</strong></td>
<td>Disponible lorsque l'option <strong>Create a new SQL Database instance</strong> est sélectionnée. Sélectionnez la région géographique dans laquelle héberger votre base de données SQL.</td>
</tr>
</table>

Cliquez sur la coche pour terminer l'Assistant. L’icône de progression s’affiche : ![Icône d'avancement affichée à la fin de l'opération][ProgressComplete]

Une fois que vous avez terminé, le service Azure BizTalk est créé et prêt pour vos applications. Les paramètres par défaut sont suffisants. Si vous souhaitez modifier les paramètres par défaut, cliquez sur **BIZTALK SERVICES** dans le volet de navigation gauche, puis sélectionnez votre service BizTalk. Des paramètres supplémentaires sont affichés sous les onglets [Tableau de bord, Surveiller et Mettre à l'échelle](biztalk-dashboard-monitor-scale-tabs.md) situés en haut.

Selon l'état du service BizTalk, certaines opérations ne peuvent pas être effectuées. Pour en obtenir la liste, consultez [BizTalk Services : Tableau comparatif des états](biztalk-service-state-chart.md).


## Étapes postérieures à l’approvisionnement

-  [Installer le certificat sur un ordinateur local](#InstallCert)
-  [Ajouter un certificat prêt pour la production](#AddCert)
-  [Obtenir l'espace de noms Access Control](#ACS)

#### <a name="InstallCert"></a>Installer le certificat sur un ordinateur local
Dans le cadre de l'approvisionnement du service BizTalk, un certificat auto-signé est créé et associé à votre abonnement au service BizTalk. Vous devez télécharger ce certificat et l'installer sur les ordinateurs à partir desquels vous déployez des applications de service BizTalk ou envoyez des messages à un point de terminaison de service BizTalk.

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Cliquez sur **BIZTALK SERVICES** dans le volet de navigation gauche, puis sélectionnez votre abonnement au service BizTalk.
3. Sélectionnez l'onglet **Tableau de bord**.
4. Sélectionnez **Télécharger le certificat SSL**. ![Modifier le certificat SSL][QuickGlance]
5. Double-cliquez sur le certificat et suivez les étapes de l'Assistant pour installer le certificat. Veillez à installer le certificat dans le magasin **Autorités de certification racines de confiance**.

#### <a name="AddCert"></a>Ajouter un certificat prêt pour la production
Le certificat auto-signé qui est créé automatiquement lors de la création de BizTalk Services est conçu pour être utilisé uniquement dans des environnements de développement. Dans les scénarios de production, remplacez-le par un certificat prêt pour la production.

1. Sous l'onglet **Tableau de bord**, sélectionnez **Mettre à jour le certificat SSL**.
2. Accédez à votre certificat SSL privé (*NomCertificat*.pfx) qui inclut le nom de votre service BizTalk, puis entrez le mot de passe et cliquez sur la coche.

#### <a name="ACS"></a>Obtenir l'espace de noms Access Control

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Cliquez sur **BIZTALK SERVICES** dans le volet de navigation gauche, puis sélectionnez votre service BizTalk.
3. Dans la barre des tâches, sélectionnez **Informations de connexion** : ![Sélectionner les informations de connexion][ACSConnectInfo]

4. Copiez les valeurs Access Control.

Lorsque vous déployez un projet BizTalk Services à partir de Visual Studio, vous entrez cet espace de noms Access Control. L’espace de noms Access Control est automatiquement créé pour votre service BizTalk.

Les valeurs Access Control values peuvent être utilisées avec n'importe quelle application. Lorsqu'Azure BizTalk Services est créé, cet espace de noms Access Control contrôle l'authentification avec votre déploiement de service BizTalk. Si vous souhaitez modifier l'abonnement ou gérer l'espace de noms, sélectionnez **ACTIVE DIRECTORY** dans le volet de navigation gauche, puis votre espace de noms. La barre des tâches affiche la liste des options.

Cliquez sur **Manage** pour ouvrir le portail de gestion Access Control. Dans le portail de gestion Access Control, le service BizTalk utilise les **identités de service** : ![Identités de service ACS dans le portail de gestion de contrôle d'accès][ACSServiceIdentities]

L'identité de service Access Control est un ensemble d'informations d'identification qui permet aux applications ou aux clients de s'authentifier directement auprès du contrôle d'accès et de recevoir un jeton.

> [AZURE.IMPORTANT]Le service BizTalk utilise **Propriétaire** comme identité de service par défaut et la valeur **Mot de passe**. Si vous utilisez la valeur de clé symétrique plutôt que la valeur de mot de passe, l’erreur ci-après peut survenir.<br/><br/>*Impossible de se connecter au compte de service de gestion de contrôle d’accès avec les informations d’identification spécifiées.*

La rubrique [Gestion de votre espace de noms ACS](https://msdn.microsoft.com/library/azure/hh674478.aspx) répertorie quelques instructions et recommandations.

## Explication des exigences

Ces exigences ne s’appliquent pas à l’édition gratuite. <table border="1"> <tr bgcolor="FAF9F9"> <td><strong>Ce dont vous avez besoin</strong></td> <td><strong>Pourquoi vous en avez besoin</strong></td> </tr> <tr> <td>Abonnement Azure</td> <td>L’abonnement détermine qui peut se connecter au portail Azure. Le titulaire du compte crée l'abonnement dans <a HREF="https://account.windowsazure.com/Subscriptions">Abonnements Azure</a>.  
<br/><br/> Le compte Azure peut avoir plusieurs abonnements et peut être géré par toute personne y étant autorisée. Par exemple, le titulaire de votre compte Azure crée un abonnement nommé <em>BizTalkServiceSubscription</em> et donne accès à cet abonnement aux administrateurs BizTalk de votre entreprise (par exemple, ContosoBTSAdmins@live.com). Dans ce scénario, les administrateurs BizTalk se connectent au portail Azure et disposent des droits d’administrateur complets pour tous les services hébergés dans l’abonnement, y compris Azure BizTalk Services. Comme ils ne sont pas les titulaires du compte Azure, ils n'ont aucun accès aux informations de facturation. <br/><br/> <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=267577"> L’article relatif à la gestion des abonnements et des comptes de stockage dans le portail Azure</a> fournit des informations complémentaires. </td> </tr> <tr> <td>Base de données Azure</td> <td>Elle stocke les tables, les vues et les procédures stockées utilisées par le service BizTalk, y compris les données de suivi.  
<br/><br/> Lorsque vous créez un service BizTalk, vous pouvez utiliser un serveur SQL Azure existant, une base de données SQL Azure existante ou créer automatiquement un serveur ou une base de données.  
<br/><br/> La mise à l'échelle de la base de données SQL est automatiquement configurée. En général, la mise à l'échelle par défaut est suffisante pour un service BizTalk. La modification de la mise à l'échelle a des conséquences sur la tarification. Consultez <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=234930"> Comptes et facturation dans la Base de données SQL Azure</a> <br/><br/> <strong>Remarques</strong> <br/> <ul> <li> : lorsque vous créez une base de données et un serveur SQL Azure, les services Azure sont automatiquement activés. Le service BizTalk nécessite l'activation des services Azure.</li> <li>Si vous créez une base de données SQL Azure sur un serveur SQL Azure existant, les règles de pare-feu du serveur ne sont pas modifiées. Il est donc possible que d'autres services Azure ne soient pas autorisés à accéder aux bases de données du serveur.</li> </ul> </td> </tr> <tr> <td>L'espace de noms Access Control d’Azure</td> <td> s'authentifie auprès d’Azure BizTalk Services. Lorsque vous déployez un projet BizTalk Services à partir de Visual Studio, vous entrez cet espace de noms Access Control. Lorsque vous créez un service BizTalk, l'espace de noms Access Control est automatiquement créé.</td> </tr>

<tr>
<td>un compte Azure Storage&#160;;</td>
<td>Donne accès aux tables, objets blob et files d'attente utilisés par votre service BizTalk pour enregistrer ce qui suit&#160;:

<ul>
<li>Fichiers journaux qui surveillent le service BizTalk. La sortie de la surveillance s’affiche également dans l’onglet **Surveillance** du portail Azure.</li>
<li>Lors de la création d'un contrat&#160;X12 ou&#160;AS2 entre des partenaires, vous pouvez activer la fonctionnalité d'archivage pour stocker les propriétés des messages. Ces données sont enregistrées dans le compte de stockage.</li>
</ul>
<br/>
Lorsque vous créez un service BizTalk, vous pouvez utiliser un compte de stockage existant ou en créer un automatiquement.
<br/><br/>
Les paramètres de stockage par défaut sont suffisants pour un service BizTalk.
<br/><br/>
Lorsque vous créez un compte de stockage, une clé primaire et une clé secondaire sont automatiquement créées. Ces clés contrôlent l’accès à votre compte de stockage. Le service BizTalk utilise automatiquement la clé primaire.
<br/><br/>
Pour plus d’informations, consultez <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285671">Stockage</a>.
</td>
</tr>

<tr>
<td>Certificat privé SSL</td>
<td>
Quand un service Azure BizTalk est créé, une URL HTTPS qui inclut le nom de votre service BizTalk est également créée. Cette URL est automatiquement configurée pour utiliser un certificat auto-signé à des fins de développement uniquement. Pour la production, vous avez besoin d'un certificat SSL privé.
<br/><br/>
<strong>Informations importantes sur le certificat SSL</strong>

<ul>
<li>La date d'expiration du certificat doit être comprise dans les 5&#160;prochaines années.</li>
<li>Tous les certificats privés exigent un mot de passe. Retenez ce mot de passe. Il est également recommandé de communiquer ce mot de passe à vos administrateurs.</li>
<li>Les certificats auto-signés sont utilisés dans des environnements de test/développement. Lorsque vous utilisez des certificats auto-signés, importez le certificat dans votre magasin de certificats personnels et dans le magasin de certificats Autorités de certification racines de confiance.</li>
</ul>
<br/>Lorsque vous envoyez la demande de certificat de production à votre autorité de certification, indiquez les propriétés de certificat suivantes&#160;:
<br/>

<ul>
<li><strong>Utilisation avancée de la clé</strong>&#160;: au minimum, Azure BizTalk Services exige l'authentification du serveur.</li>
<li><strong>Nom commun</strong>&#160;: entrez le nom de domaine complet (FQDN) de l'URL de votre service Azure BizTalk. Voir la section <a HREF="#BizTalk">Création d’un service BizTalk</a> au début de cet article.</li>
</ul>
<br/>
Un certificat nouveau ou différent peut être ajouté après la création du service BizTalk.
</td>
</tr>
</table>



## les connexions hybrides

Quand vous créez un service Azure BizTalk, l'onglet **Connexions hybrides** est disponible :

![Onglet Connexions hybrides][HybridConnectionTab]

Les connexions hybrides permettent de connecter un site web Azure ou un service mobile Azure à toute ressource locale utilisant un port TCP statique, par exemple SQL Server, MySQL, les API web HTTP, Mobile Services et la plupart des services web personnalisés. Les connexions hybrides et le service d'adaptateur BizTalk sont différents. Le service d'adaptateur BizTalk permet de connecter Azure BizTalk Services à un système métier local.

 Pour en savoir plus, en particulier sur la création et la gestion des connexions hybrides, consultez [Connexions hybrides](integration-hybrid-connection-overview.md).


## Étapes suivantes

Après avoir créé un service BizTalk, passez en revue les différents [onglets de BizTalk Services : Tableau de bord, Surveiller et Mettre à l’échelle](biztalk-dashboard-monitor-scale-tabs.md). Votre service BizTalk est prêt pour vos applications. Pour commencer à créer des applications, consultez la page [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## Voir aussi
- [Tableau comparatif des éditions de BizTalk Services](biztalk-editions-feature-chart.md)<br/>
- [Tableau comparatif des états du service BizTalk](biztalk-service-state-chart.md)<br/>
- [Sauvegarde et restauration de BizTalk Services](biztalk-backup-restore.md)<br/>
- [Limitation BizTalk Services](biztalk-throttling-thresholds.md)<br/>
- [Nom et clé de l'émetteur dans BizTalk Services](biztalk-issuer-name-issuer-key.md)<br/>
- [Utilisation du Kit de développement logiciel (SDK) Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
- [Connexions hybrides](integration-hybrid-connection-overview.md)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
[HybridConnectionTab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png

<!---HONumber=AcomDC_1203_2015-->
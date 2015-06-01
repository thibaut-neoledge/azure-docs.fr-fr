<properties 
	pageTitle="Création de services BizTalk dans le portail de gestion | Azure" 
	description="Apprenez à configurer ou à créer un service BizTalk Services dans le portail de gestion Azure ; MABS, WABS" 
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
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="mandia"/>

# Créer un service BizTalk à l'aide du portail de gestion Azure

Cette rubrique répertorie les étapes à suivre pour créer un service Azure BizTalk dans le portail de gestion Azure. Plus précisément :

-   [Création d'un service BizTalk][Création d'un service BizTalk]
-   [Étapes postérieures à l'approvisionnement][Étapes postérieures à l'approvisionnement]
-   [Explication des exigences][Explication des exigences]
-   [Connexions hybrides (nouveauté)](#HC)

<div class="dev-callout"> 
<b>Conseil</b> 
<p>Pour vous connecter au portail de gestion Azure, vous devez disposer d'un compte et d'un abonnement Azure. Si vous ne poss&eacute;dez pas de compte, vous pouvez cr&eacute;er un compte d'&eacute;valuation gratuit en quelques minutes. Consultez <a href="http://go.microsoft.com/fwlink/p/?LinkID=239738">Version d'&eacute;valuation gratuite d'Azure</a>.</p> 
</div>

## <a name="BizTalk"></a>Création d'un service BizTalk

Selon l'édition que vous choisissez, tous les paramètres du service BizTalk peuvent ne pas être disponibles.

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].
2.  Dans le volet de navigation inférieur, sélectionnez **NOUVEAU** :
<br/>
    ![Sélectionnez le bouton Nouveau][Sélectionnez le bouton Nouveau]

3.  Sélectionnez **APP SERVICES** > **BIZTALK SERVICE** > **CRÉATION PERSONNALISÉE** :
<br/>
    ![Sélectionnez BizTalk Service, puis Création personnalisée][Sélectionnez BizTalk Service, puis Création personnalisée]

4.  Entrez les paramètres du service BizTalk.

	<table border="1">
	<tr>
	<td><strong>BizTalk Service Name</strong></td>
	<td>Vous pouvez entrer n'importe quel nom, mais soyez spécifique. Voici quelques exemples :<br/><br/>
	<em>masociété</em>.biztalk.windows.net<br/>
	<em>masociétémonapplication</em>.biztalk.windows.net<br/>
	<em>monapplication</em>.biztalk.windows.net<br/><br/>
« .biztalk.windows.net » est automatiquement ajouté au nom que vous entrez. L'URL obtenue est utilisée pour accéder à votre service BizTalk. Par exemple <strong>https://<em>monapplication</em>.biztalk.windows.net</strong>.
	</td>
	</tr>
	<tr>
	<td><strong>Édition</strong></td>
	<td>Si vous êtes en phase de test ou de développement, choisissez <strong>Développeur</strong>. Si vous êtes en phase de production, reportez-vous au document <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302279">BizTalk Services : Tableau comparatif des éditions</a> pour déterminer l'édition (<strong>Premium</strong>, <strong>Standard</strong> ou <strong>De base</strong>) la mieux adaptée à votre scénario d'entreprise.	 
	</td>
	</tr>
	<tr>
	<td><strong>Région</strong></td>
	<td>Sélectionnez la région géographique dans laquelle héberger votre service BizTalk.</td>
	</tr>
	<tr>
	<td><strong>Domain URL</strong></td>
	<td><strong>Facultatif</strong>. Par défaut, l'URL du domaine est <em>YourBizTalkServiceName</em>.biztalk.windows.net. Un domaine personnalisé peut également être entré. Par exemple, si votre domaine est <em>contoso</em>, vous pouvez entrer : <br/><br/>
	<em>MaSociété</em>.contoso.com<br/>
	<em>MaSociétéMonApplication</em>.contoso.com<br/>
	<em>MonApplication</em>.contoso.com<br/>
	<em>NomServiceBizTalk</em>.contoso.com<br/>
	</td>
	</tr>
	</table>
Sélectionnez la flèche SUIVANT.

5.  Entrez les paramètres de stockage et de base de données :

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>Monitoring/Archiving Storage Account</strong></td>
    <td align="left">Sélectionnez un compte de stockage existant ou sélectionnez Créer un nouveau compte de stockage.<br /><br /> Si vous créez un compte de stockage, entrez le <strong>Nom du compte de stockage</strong>.</td>
    </tr>
    <tr class="even">
    <td align="left"><strong>Base de données des suivis</strong></td>
    <td align="left">Si vous utilisez une base de données SQL Azure existante, celle-ci ne peut pas être utilisée par un autre service BizTalk. Vous devez disposer de l'identifiant et du mot de passe entrés lors de la création de ce serveur de base de données SQL Azure.<br /><br />
    <div class="dev-callout"> 
<b>Conseil</b> 
<p>Cr&eacute;ez la base de donn&eacute;es des suivis et le compte de stockage de surveillance ou d'archive dans la m&ecirc;me r&eacute;gion que le service BizTalk.</p> 
</div></td>
    </tr>
    </tbody>
    </table>

    Sélectionnez la flèche SUIVANT.

6.  Entrez les paramètres de base de données :

	<table border="1">
	<tr>
	<td><strong>Nom</strong></td>
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
	<td><strong>Server Login Name</strong></td>
	<td>Entrez le nom d'utilisateur de connexion.</td>
	</tr>
	<tr>
	<td><strong>Server Login Password</strong></td>
	<td>Entrez le mot de passe de connexion.</td>
	</tr>
	<tr>
	<td><strong>Région</strong></td>
	<td>Disponible lorsque l'option <strong>Create a new SQL Database instance</strong> est sélectionnée. Sélectionnez la région géographique dans laquelle héberger votre base de données SQL.</td>
	</tr>
	</table>

Cliquez sur la coche pour terminer l'Assistant. L'icône d'avancement affiche :
<br/>
![Icône d'avancement affichée à la fin de l'opération][Icône d'avancement affichée à la fin de l'opération]

Une fois que vous avez terminé, le service Azure BizTalk est créé et prêt pour vos applications. Les paramètres par défaut sont suffisants. Si vous souhaitez modifier les paramètres par défaut, sélectionnez **BIZTALK SERVICES** dans le volet de navigation gauche, puis sélectionnez votre service BizTalk. Des paramètres supplémentaires sont affichés sous les onglets [Tableau de bord, Surveiller et Mettre à l'échelle][Tableau de bord, Surveiller et Mettre à l'échelle] situés en haut.

Selon l'état du service BizTalk, certaines opérations ne peuvent pas être effectuées. Pour en obtenir la liste, consultez [BizTalk Services : Tableau comparatif des états][BizTalk Services : Tableau comparatif des états].

## <a name="PostProv"></a>Étapes postérieures à l'approvisionnement

-   [Installer le certificat sur un ordinateur local][Installer le certificat sur un ordinateur local]
-   [Ajouter un certificat prêt pour la production][Ajouter un certificat prêt pour la production]
-   [Obtenir l'espace de noms Access Control][Obtenir l'espace de noms Access Control]

#### <a name="InstallCert"></a>Installer le certificat sur un ordinateur local

Dans le cadre de l'approvisionnement du service BizTalk, un certificat auto-signé est créé et associé à votre abonnement au service BizTalk. Vous devez télécharger ce certificat et l'installer sur les ordinateurs à partir desquels vous déployez des applications de service BizTalk ou envoyez des messages à un point de terminaison de service BizTalk.

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].
2.  Cliquez sur **BIZTALK SERVICES** dans le volet de navigation gauche, puis sélectionnez votre abonnement au service BizTalk.
3.  Cliquez sur l'onglet **Tableau de bord**.
4.  Cliquez sur **Télécharger le certificat SSL**.
<br/>
![Modifier le certificat SSL][Modifier le certificat SSL]
5.  Double-cliquez sur le certificat et suivez les étapes de l'Assistant pour installer le certificat. Veillez à installer le certificat dans le magasin **Autorités de certification racines de confiance**.

#### <a name="AddCert"></a>Ajouter un certificat prêt pour la production

Le certificat auto-signé qui est créé automatiquement lors de l'approvisionnement de BizTalk Services est conçu pour être uniquement utilisé dans des environnements de développement. Dans les scénarios de production, vous devez le remplacer par un certificat prêt pour la production.

1.  Sous l'onglet **Tableau de bord**, cliquez sur **Mettre à jour le certificat SSL**.
2.  Accédez à votre certificat SSL privé (*NomCertificat*.pfx) qui inclut le nom de votre service BizTalk, puis entrez le mot de passe et cliquez sur la coche.

#### <a name="ACS"></a>Obtenir l'espace de noms Access Control

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].
2.  Sélectionnez **BIZTALK SERVICES** dans le volet de navigation gauche, puis votre service BizTalk.
3.  Dans la barre des tâches, sélectionnez **Informations de connexion** :
<br/>
![Sélectionnez Informations de connexion][Sélectionnez Informations de connexion]

4.  Copiez les valeurs Access Control.

Lorsque vous déployez un projet BizTalk Services à partir de Visual Studio, vous entrez cet espace de noms Access Control. L'espace de noms Access Control est automatiquement créé pour votre service BizTalk.

Les valeurs Access Control values peuvent être utilisées avec n'importe quelle application. Lorsqu'Azure BizTalk Services est créé, cet espace de noms Access Control contrôle l'authentification avec votre déploiement de service BizTalk. Si vous souhaitez modifier l'abonnement ou gérer l'espace de noms, sélectionnez **ACTIVE DIRECTORY** dans le volet de navigation gauche, puis votre espace de noms. La barre des tâches affiche la liste des options.

Cliquez sur **Manage** pour ouvrir le portail de gestion Access Control. Dans ce dernier, le service BizTalk utilise **Identités de service** :
<br/>
![Identités de service ACS dans le portail de gestion Access Control][Identités de service ACS dans le portail de gestion Access Control]

L'identité de service Access Control est un ensemble d'informations d'identification qui permet aux applications ou aux clients de s'authentifier directement auprès du contrôle d'accès et de recevoir un jeton.

**Important**<br/>
 Le service BizTalk utilise **Propriétaire** comme identité de service par défaut et la valeur **Mot de passe**. Si vous utilisez la valeur Clé symétrique plutôt que la valeur Mot de passe, l'erreur suivante peut se produire :

*Impossible de se connecter au compte de service de gestion Access Control avec les informations d'identification spécifiées.*

La rubrique [Gestion de votre espace de noms ACS][Gestion de votre espace de noms ACS] répertorie quelques instructions et recommandations.

## <a name="Requirements"></a>Explication des exigences

Ces exigences ne s'appliquent pas à l'édition gratuite.

<table border="1">

<tr bgcolor="FAF9F9">

<td>
<strong>Ce dont vous avez besoin</strong>

</td>

<td>
<strong>Raison</strong>

</td>

</tr>

<tr>

<td>
Abonnement Azure

</td>

<td>
L'abonnement détermine qui peut se connecter au portail de gestion Azure. Le titulaire du compte crée l'abonnement dans <a HREF="https://account.windowsazure.com/Subscriptions"> Abonnements Azure</a>.
<br/><br/>
Le compte Azure peut avoir plusieurs abonnements et peut être géré par toute personne y étant autorisée. Par exemple, le titulaire de votre compte Azure crée un abonnement nommé *AbonnementServiceBizTalk* et donne aux administrateurs BizTalk de votre société (par exemple, <ContosoBTSAdmins@live.com>) accès à cet abonnement. Dans ce scénario, les administrateurs BizTalk se connectent au portail de gestion Azure et disposent des droits d'administrateur complets pour tous les services hébergés dans l'abonnement, y compris Azure BizTalk Services. Comme ils ne sont pas les titulaires du compte Azure, ils n'ont aucun accès aux informations de facturation.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=267577">Gestion des abonnements et des comptes de stockage dans le portail de gestion Azure</a> fournit plus d'informations.

</td>

</tr>

<tr>

<td>
Base de données SQL Azure

</td>

<td>
Stocke les tables, vues et procédures stockées utilisées par le service BizTalk, y compris les données de suivi.
<br/><br/>
Lorsque vous créez un service BizTalk, vous pouvez utiliser un serveur SQL Azure existant, une base de données SQL Azure existante ou créer automatiquement un serveur ou une base de données.
<br/><br/>
La mise à l'échelle de la base de données SQL est automatiquement configurée. En général, la mise à l'échelle par défaut est suffisante pour un service BizTalk. La modification de la mise à l'échelle a des conséquences sur la tarification. Consultez <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=234930"> Comptes et facturation dans la base de données SQL Azure</a>
<br/><br/>
<strong>Remarques</strong>
<br/>
<ul>
<li>Lorsque vous créez un serveur et une base de données SQL Azure, les services Azure sont automatiquement activés. Le service BizTalk nécessite l'activation des services Azure.</li>
<li>Si vous créez une base de données SQL Azure sur un serveur SQL Azure existant, les règles de pare-feu du serveur ne sont pas modifiées. Il est donc possible que d'autres services Azure ne soient pas autorisés à accéder aux bases de données du serveur.</li>
</ul>
</td>

</tr>

<tr>

<td>
Espace de noms Access Control Azure

</td>

<td>
Permet de s'authentifier auprès d'Azure BizTalk Services. Lorsque vous déployez un projet BizTalk Services à partir de Visual Studio, vous entrez cet espace de noms Access Control. Lorsque vous créez un service BizTalk, l'espace de noms Access Control est automatiquement créé.

</td>

</tr>
</p>
<tr>
<td>
Compte Azure Storage

</td>
<td>
Donne accès aux tables, objets blob et files d'attente utilisés par votre service BizTalk pour enregistrer ce qui suit :

<ul>
<li>Fichiers journaux qui surveillent le service BizTalk. La sortie de la surveillance s'affiche également dans l'onglet Surveillance du portail de gestion Azure.</li>
<li>Lors de la création d'un contrat X12 ou AS2 entre des partenaires, vous pouvez activer la fonctionnalité d'archivage pour stocker les propriétés des messages. Ces données sont enregistrées dans le compte de stockage.</li>
</ul>
<br/>
 Lorsque vous créez un service BizTalk, vous pouvez utiliser un compte de stockage existant ou en créer un automatiquement.
<br/><br/>
 Les paramètres de stockage par défaut sont suffisants pour un service BizTalk.
<br/><br/>
 Lorsque vous créez un compte de stockage, une clé primaire et une clé secondaire sont automatiquement créées. Ces clés contrôlent l'accès à votre compte de stockage. Le service BizTalk utilise automatiquement la clé primaire.
<br/><br/>
 Pour plus d’informations, consultez <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285671"> Stockage</a>.

</td>
</tr>
<tr>
<td>
Certificat privé SSL

</td>
<td>
Quand un service Azure BizTalk est créé, une URL HTTPS qui inclut le nom de votre service BizTalk est également créée. Cette URL est automatiquement configurée pour utiliser un certificat auto-signé à des fins de développement uniquement. Pour la production, vous avez besoin d'un certificat SSL privé.
<br><br/>
<strong>Informations importantes sur le certificat SSL</strong>

<ul>
<li>La date d'expiration du certificat doit être comprise dans les 5 prochaines années.</li>
<li>Tous les certificats privés exigent un mot de passe. Retenez ce mot de passe. Il est également recommandé de communiquer ce mot de passe à vos administrateurs.</li>
<li>Les certificats auto-signés sont utilisés dans des environnements de test/développement. Lorsque vous utilisez des certificats auto-signés, importez le certificat dans votre magasin de certificats personnels et dans le magasin de certificats Autorités de certification racines de confiance.</li>
</ul>
<br/>
Lorsque vous envoyez la demande de certificat de production à votre autorité de certification, indiquez les propriétés de certificat suivantes :

<ul>
<li><strong>Utilisation avancée de la clé</strong> : Au minimum, Azure BizTalk Services exige l'authentification du serveur.</li>
<li><strong>Nom commun</strong> : Entrez le nom de domaine complet de l'URL de votre service Azure BizTalk. Consultez <a HREF="#BizTalk">Créer un service BizTalk</a> dans cette rubrique.</li>
</ul>
<br/>
Un certificat nouveau ou différent peut être ajouté après la création du service BizTalk.
</td>
</tr>
</table>
## <a name="HC"></a>Connexions hybrides

Quand vous créez un service Azure BizTalk, l'onglet **Connexions hybrides** est disponible :

![Onglet Connexions hybrides][Onglet Connexions hybrides]

Les connexions hybrides permettent de connecter un site web Azure ou un service mobile Azure à toute ressource locale utilisant un port TCP statique, par exemple SQL Server, MySQL, les API web HTTP, Mobile Services et la plupart des services web personnalisés. Les connexions hybrides et le service d'adaptateur BizTalk sont différents. Le service d'adaptateur BizTalk permet de connecter Azure BizTalk Services à un système métier local.

Pour en savoir plus, en particulier sur la création et la gestion des connexions hybrides, consultez [Connexions hybrides][Connexions hybrides].

## Suivant

Après avoir créé un service BizTalk, passez en revue les différents onglets dans la page [BizTalk Services : Onglets Tableau de bord, Surveiller et Mettre à l'échelle][Tableau de bord, Surveiller et Mettre à l'échelle]. Votre service BizTalk est prêt pour vos applications. Pour commencer à créer des applications, consultez la page [Azure BizTalk Services][Azure BizTalk Services].

## Voir aussi

-   [BizTalk Services : Tableau comparatif des éditions][BizTalk Services : Tableau comparatif des éditions]
-   [BizTalk Services : Tableau comparatif des états][BizTalk Services : Tableau comparatif des états]
-   [BizTalk Services : Sauvegarde et restauration][BizTalk Services : Sauvegarde et restauration]
-   [BizTalk Services : Limitation][BizTalk Services : Limitation]
-   [BizTalk Services : Nom et clé de l'émetteur][BizTalk Services : Nom et clé de l'émetteur]
-   [Utilisation du Kit de développement logiciel (SDK) Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)
-   [Connexions hybrides][Connexions hybrides]

  [Création d'un service BizTalk]: #BizTalk
  [Étapes postérieures à l'approvisionnement]: #PostProv
  [Explication des exigences]: #Requirements
  [portail de gestion Azure]: http://go.microsoft.com/fwlink/p/?LinkID=213885
  [Sélectionnez le bouton Nouveau]: ./media/biztalk-provision-services/WABS_New.png
  [Sélectionnez BizTalk Service, puis Création personnalisée]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
  [BizTalk Services : Tableau comparatif des éditions]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [Icône d'avancement affichée à la fin de l'opération]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
  [Tableau de bord, Surveiller et Mettre à l'échelle]: http://go.microsoft.com/fwlink/p/?LinkID=302281
  [BizTalk Services : Tableau comparatif des états]: http://go.microsoft.com/fwlink/p/?LinkID=329870
  [Installer le certificat sur un ordinateur local]: #InstallCert
  [Ajouter un certificat prêt pour la production]: #AddCert
  [Obtenir l'espace de noms Access Control]: #ACS
  [Modifier le certificat SSL]: ./media/biztalk-provision-services/WABS_QuickGlance.png
  [Sélectionnez Informations de connexion]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
  [Identités de service ACS dans le portail de gestion Access Control]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
  [Gestion de votre espace de noms ACS]: http://go.microsoft.com/fwlink/p/?LinkID=285670
  [Onglet Connexions hybrides]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png
  [Connexions hybrides]: http://go.microsoft.com/fwlink/p/?LinkID=397274
  [Azure BizTalk Services]: http://go.microsoft.com/fwlink/p/?LinkID=235197
  [BizTalk Services : Sauvegarde et restauration]: http://go.microsoft.com/fwlink/p/?LinkID=329873
  [BizTalk Services : Limitation]: http://go.microsoft.com/fwlink/p/?LinkID=302282
  [BizTalk Services : Nom et clé de l'émetteur]: http://go.microsoft.com/fwlink/p/?LinkID=303941

<!--HONumber=46--> 

# Recommandations en matière de sécurité Azure

## Résumé

Lors du développement d'applications pour Azure, les identités et
les accès doivent être vos principales préoccupations en matière de sécurité.
Cette rubrique explique ces questions de sécurité quant aux identités et aux accès dans le cloud, et comment vous
pouvez protéger au mieux vos applications cloud.

## Vue d'ensemble

La sécurité d'une application est subordonnée à sa surface d'exposition. En effet, les risques
de sécurité d'une application sont proportionnels à sa surface d'exposition. Pour
exemple, une application qui s'exécute en tant que processus de traitement par lots automatisé
est moins exposée du point de vue de la sécurité qu'un site web accessible au public.

Quand vous passez dans le cloud, vous gagnez une certaine tranquillité d'esprit
pour ce qui est de l'infrastructure et de la mise en réseau, car ces aspects sont gérés dans des centres de données
selon des pratiques, des outils et des technologies de sécurité de premier plan. En
revanche, votre application se trouve intrinsèquement plus exposée dans le cloud, avec une surface
d'exposition supérieure qui peut être exploitée par des personnes malveillantes. Cela s'explique
par le fait que bon nombre de technologies et de services cloud sont exposés en tant que points de terminaison,
par opposition aux composants en mémoire. Le stockage Azure, Service Bus,
la base de données SQL (anciennement SQL Azure) et bien d'autres services sont accessibles
sur le réseau via leurs points de terminaison.

Dans les applications cloud, les développeurs ont une plus grande responsabilité quant à la
conception, au développement et à la maintenance de leurs
applications cloud selon des normes de sécurité élevées pour garder à distance les personnes malveillantes.
Comme vous pouvez le constater dans le schéma ci-dessous (extrait du [PDF relatif aux notes de sécurité
Azure](http://blogs.msdn.com/b/jmeier/archive/2010/08/03/now-available-azure-security-notes-pdf.aspx) disponible sur le blog de J.D. Meier), notez dans quelle mesure la partie infrastructure est gérée par le
fournisseur de services cloud (dans notre cas Azure), ce
qui permet aux développeurs d'applications de se consacrer davantage à la sécurité :

![Securing the application][01]

Le point positif, c'est que l'ensemble des pratiques, principes et techniques
de développement de la sécurité que vous connaissez déjà s'appliquent aussi au développement
d'applications cloud. Voici les principaux éléments dont il faut
tenir compte :

-   Toutes les entrées sont vérifiées selon des critères de type, de longueur, de portée et de chaîne
    pour éviter les attaques par injection, et toutes les données que votre application renvoie en écho sont
    parfaitement assainies.
-   Les données sensibles doivent être protégées aussi bien en stockage
    qu'en transit sur le réseau de façon à limiter les risques de divulgation d'informations et de falsification de données.
-   L'audit et la journalisation doivent être correctement implémentés afin
    de limiter les risques de non-répudiation.
-   L'authentification et l'autorisation doivent être implémentées en utilisant
    des mécanismes éprouvés de la plateforme afin d'empêcher les risques
    d'usurpation d'identité et d'élévation de privilèges.

Pour une liste complète des menaces, attaques, vulnérabilités et
autres contre-mesures, reportez-vous à [Aide-mémoire : infrastructure de
sécurité des applications web](http://msdn.microsoft.com/fr-fr/library/ff649461.aspx) et à l'[Index du guide de sécurité pour les applications](http://msdn.microsoft.com/fr-fr/library/ff650760.aspx) de Microsoft patterns & practices.

Dans le cloud, les mécanismes d'authentification et de contrôle d'accès sont très différents
de ceux des applications locales. De plus,
les solutions d'authentification et de contrôle d'accès disponibles pour les
applications cloud sont bien plus nombreuses, ce qui peut semer la confusion et avoir des répercussions négatives sur la qualité
des implémentations. La confusion s'accroît dès lors qu'il s'agit de définir ce qu'est
une application cloud. Par exemple, un problème se pose quand il s'agit de déployer
une application sur le cloud, alors que son mécanisme d'authentification est fourni par Active Directory. Il en va
de même quand il s'agit de déployer une application en local avec des mécanismes d'authentification
basés sur le cloud (par exemple avec le contrôle d'accès
Azure Active Directory, précédemment appelé Service de contrôle d'accès
ou ACS).

## Menaces, failles et attaques

Une menace est un mauvais résultat potentiel que vous voulez éviter, comme la
divulgation d'informations sensibles ou un service qui devient indisponible.
Il est pratique courante dans le monde anglo-saxon de classer les menaces par catégories à l'aide de l'acronyme " STRIDE " :

-   **S**poofing (usurpation d'identité)
-   **T**ampering with data (altération des données)
-   **R**epudiation of actions (répudiation des actions)
-   **I**nformation disclosure (divulgation d'informations)
-   **D**enial of service (déni de service)
-   **E**levation of privileges (élévation des privilèges)

Les vulnérabilités sont des bogues que nous, développeurs, introduisons dans
le code d'une application, qui la rendent exploitable par des personnes malveillantes. Par exemple, l'envoi de données
sensibles sous forme de texte en clair présente un risque réel de divulgation
d'informations par attaque de type " reniflage du trafic ".

Les attaques visent à exploiter ces vulnérabilités et à nuire à
une application. Par exemple, une exécution de script de site à site (XSS, Cross Site Scripting) est une attaque
qui exploite une sortie non assainie. Autre exemple d'attaque :
l'écoute électronique sur réseau, qui consiste à intercepter des informations d'identification envoyées en clair. Ces attaques
peuvent entraîner une usurpation d'identité. Pour simplifier,
considérez les menaces, les vulnérabilités et les attaques comme de vrais dangers.
Considérons les diagrammes suivants comme une représentation synthétique des dangers
liés à une application web déployée sur Azure (extraits du
document PDF [Azure Security Notes](http://blogs.msdn.com/b/jmeier/archive/2010/08/03/now-available-azure-security-notes-pdf.aspx)de J.D. Meier) :

![Threats Vulnerabilities and Attacks][02]

En tant que développeur, c'est vous qui pouvez contrôler les failles. Moins vous
en introduisez, moins vous laissez de chances aux personnes malveillantes
de les exploiter.

Les vulnérabilités liées aux identités et aux accès vous exposent
à toutes les menaces du modèle STRIDE. Par exemple, un mécanisme d'authentification
mal implémenté peut entraîner une usurpation d'identité et,
par voie de conséquence, la divulgation d'informations, une altération des données, des opérations avec privilèges élevés, voire
un arrêt complet du service. Penchez-vous sur les questions suivantes,
qui peuvent mettre en lumière des vulnérabilités potentielles au niveau
de l'implémentation des mécanismes d'identité et d'accès dans votre application cloud :

-   	Envoyez-vous des informations d'identification en clair sur le réseau à destination de vos
    services Azure ?
-   Stockez-vous les informations d'identification des services Azure en clair ?
-   Vos informations d'identification des services Azure suivent-elles
    des stratégies de mots de passe fort ?
-   Laissez-vous Azure vérifier les informations d'identification
    ou utilisez-vous des mécanismes de vérification personnalisés ?
-   Limitez-vous les sessions d'authentification des services Azure
    ou la durée de vie des jetons à une plage de temps raisonnable ?
-   Vérifiez-vous les autorisations pour chaque point d'entrée Azure de
    votre application cloud distribuée ?
-   En cas de défaillance, votre mécanisme d'autorisation est-il suffisamment
    sûr pour éviter d'exposer des informations sensibles ou d'autoriser un accès illimité ?

## Contre-mesures

La meilleure contre-mesure face à une attaque consiste
non pas à implémenter vos propres mécanismes d'identité et d'accès, mais à utiliser ceux
offerts par la plateforme. Envisagez d'utiliser les technologies d'identité et d'accès suivantes :

**Windows Identity Foundation (WIF).** WIF est une bibliothèque runtime .NET
intégrée au .NET Framework 4.5 (également disponible sous la forme d'un téléchargement
distinct pour .NET 3.5/4.0). WIF assure l'essentiel du traitement des protocoles,
tels que WS-Federation et WS-Trust, et des jetons, tels que SAML
(Security Assertion Markup Language) : vous n'avez donc pas besoin
d'écrire du code complexe lié à la sécurité dans votre application. L'exemple suivant
suivantes fournissent des informations détaillées sur WIF :

-   [Exemples Windows Identity Foundation 4.5](http://code.msdn.microsoft.com/site/search?f%5B0%5D.Type=SearchText&f%5B0%5D.Value=wif&f%5B1%5D.Type=Topic&f%5B1%5D.Value=claims-based%20authentication) dans la galerie de code MSDN.
-   [Outils Windows Identity Foundation 4.5 pour Visual Studio 11 Bêta](http://visualstudiogallery.msdn.microsoft.com/e21bf653-dfe1-4d81-b3d3-795cb104066e) dans
    la galerie de code MSDN.
-   [Windows Identity Foundation runtime (.Net 3.5/4.0)](http://www.microsoft.com/fr-fr/download/details.aspx?id=17331) sur MSDN.
-   [Exemples Windows Identity Foundation 3.5/4.0 et modèles
    Visual Studio 2008/2010](http://www.microsoft.com/fr-fr/download/details.aspx?displaylang=en&id=4451) sur MSDN.

**Contrôle d'accès Azure AD (appelé précédemment ACS)**. 
C'est un service cloud qui intègre le service d'émission de jeton de sécurité
(STS, Security Token Service) et autorise la fédération avec différents fournisseurs d'identité (IdP)
Il peut s'agir d'un service d'annuaire d'entreprise Active Directory ou de
fournisseurs d'identité Internet (par exemple Windows Live ID/Compte Microsoft, Facebook, Google et Yahoo!), ou
encore de fournisseurs d'identité Open ID 2.0. 	Les ressources suivantes fournissent des
informations détaillées sur le contrôle d'accès Azure AD :

-   [Access Control Service 2.0](http://msdn.microsoft.com/library/gg429786.aspx) 
-   [Scénarios et solutions utilisant ACS](http://msdn.microsoft.com/fr-fr/library/gg185920.aspx)
-   [Rubrique de procédure du service de contrôle d'accès (ACS)](http://msdn.microsoft.com/fr-fr/library/windowsazure/gg185939.aspx)
-   [Guide sur l'identité et le contrôle d'accès basés sur les revendications](http://msdn.microsoft.com/fr-fr/library/ff423674.aspx)
-   [Kit de formation développeur dans le domaine de l'identité](http://www.microsoft.com/fr-fr/download/details.aspx?id=14347)
-   [Cours de formation développeur dans le domaine de l'identité (MSDN)](http://msdn.microsoft.com/fr-fr/IdentityTrainingCourse)

**Active Directory Federation Services (AD FS).**Active Directory
Federation Services (AD FS) 2.0 assurent la prise en charge des solutions d'identité
basées sur les revendications qui font intervenir les technologies
Windows Server et Active Directory. Les services AD FS 2.0 prennent en charge les protocoles WS-Trust,
WS-Federation et SAML. 	Les ressources suivantes fournissent des informations détaillées sur les
services AD FS :

-   [Plan du contenu AD FS 2.0](http://social.technet.microsoft.com/wiki/contents/articles/2735.ad-fs-2-0-content-map.aspx)
-   [Conception SSO de Web][Conception SSO de Web]
-   [Federated Web SSO Design][Conception SSO de Web fédéré]

**Signatures d'accès partagé Azure.** Les signatures d'accès partagé
vous permettent de paramétrer l'accès à une ressource d'objet blob ou de conteneur. 
ressources suivantes fournissent des informations détaillées sur les
signatures d'accès partagé :

-   [Gestion de l'accès aux objets blob et aux conteneurs](http://msdn.microsoft.com/fr-fr/library/ee393343.aspx)
-   [Nouvelle fonctionnalité de stockage : les signatures d'accès partagé](http://blog.smarx.com/posts/new-storage-feature-signed-access-signatures)
-   [Signatures d'accès partagé : une facilité d'utilisation accrue](http://blog.smarx.com/posts/shared-access-signatures-are-easy-these-days)

## Plan des scénarios

Cette section décrit brièvement les principaux scénarios abordés dans cette rubrique.
Utilisez-la comme un outil pour déterminer la bonne solution de gestion des identités convenant à votre
l'application.

-   **Application ASP.NET Web Forms avec authentification fédérée.** Dans
    ce scénario, vous contrôlez l'accès à votre application de formulaires web ASP.NET à l'aide de
    l'identité Internet, comme Live ID / Compte Microsoft, ou de
    l'identité d'entreprise, gérée dans Windows Server Active Directory.
-   **Service WCF (SOAP) avec authentification fédérée.**Dans ce
    scénario, vous contrôlez l'accès à votre service WCF (SOAP)
    via des identités de service gérées par le contrôle d'accès Azure AD.
-   **Service WCF (SOAP) avec authentification fédérée, identités dans
    Active Directory.** Dans ce scénario, vous contrôlez l'accès à votre
    service web WCF (SOAP) via des identités gérées par le service d'annuaire d'entreprise
    Windows Server Active Directory.
-   **Service WCF (REST) avec authentification fédérée.**Dans ce
    scénario, vous contrôlez l'accès à votre service WCF (REST)
    via des identités de service gérées par le contrôle d'accès Azure AD.
-   **Service WCF (REST) avec Live ID/Compte Microsoft, Facebook,
    Google, Yahoo!, Open ID.** Dans ce scénario, vous contrôlez l'accès
    à votre service WCF (REST) via une identité Internet comme
    Live ID/Compte Microsoft.
-   **Application Web ASP.NET vers le service WCF REST avec jeton SWT partagé.** Dans
    ce scénario, vous disposez d'une application distribuée avec une application web
    ASP.NET frontale et un service REST en aval, et vous voulez faire transiter le contexte de
    l'utilisateur final via des niveaux physiques.
-   **Autorisation du contrôle d'accès en fonction du rôle (RBAC) dans les applications
    et services prenant en charge les revendications.** Dans ce scénario, vous voulez implémenter
    une logique d'autorisation dans votre application en fonction des rôles.
-   **Autorisation basée sur les revendications dans les applications et services
    prenant en charge les revendications.** Dans ce scénario, vous voulez implémenter une logique d'autorisation
    dans votre application selon des règles d'autorisation complexes.
-   **Scénarios d'identité et d'accès pour le service de stockage Azure.**Dans
    ce scénario, vous devez partager de façon sécurisée l'accès aux objets blob et aux
    conteneurs de stockage Azure.
-   **Scénarios d'identité et d'accès pour la base de données SQL Azure.**La base de données
    SQL prend en charge seulement l'authentification SQL Server. L'authentification
    Windows (sécurité intégrée) n'est pas prise en charge. Les utilisateurs
    doivent fournir leurs informations d'identification (nom d'utilisateur et mot de passe) chaque fois qu'ils se connectent à
    une base de données SQL.
-   **Scénarios d'identité et d'accès pour Azure Service Bus.**Dans ce scénario,
    vous devez accéder de façon sécurisée aux files d'attente d'Azure Service Bus.
-   **Scénarios d'identité et d'accès pour le cache en mémoire.**Dans ce scénario,
    vous devez accéder de façon sécurisée aux données gérées par le cache en mémoire.
-   **Scénarios d'identité et d'accès pour Azure Marketplace.**Dans ce scénario,
    vous devez accéder de façon sécurisée à des jeux de données
    Azure Marketplace.

## Scénarios d'identité et d'accès Azure

Cette section décrit des scénarios d'identité et d'accès courants pour
différentes architectures d'application. Servez-vous du plan des scénarios
pour vous orienter rapidement.

### Application ASP.NET Web Forms avec authentification fédérée

Dans ce scénario d'architecture d'application, votre application web peut être
déployée sur Azure ou en local. Les utilisateurs de
l'application doivent s'authentifier via le service d'annuaire d'entreprise Active Directory
ou des fournisseurs d'identité Internet.

Pour une solution à ces scénarios, utilisez le contrôle d'accès Azure AD et Windows
Identity Foundation.

![Azure Active Directory Access control][03]

Pour implémenter ce scénario, consultez les ressources suivantes :

-   [ création de ma première application ASP.NET prenant en charge les revendications à l'aide d'ACS](http://msdn.microsoft.com/fr-fr/library/gg429779.aspx)
-   [ hébergement de pages de connexion dans votre application Web ASP.NET](http://msdn.microsoft.com/fr-fr/library/gg185926.aspx)
-   [ implémentation de l'autorisation de revendications dans une application ASP.NET prenant en charge les revendications à l'aide de WIF et ACS](http://msdn.microsoft.com/fr-fr/library/gg185907.aspx)    
-   [ Implémentation du contrôle d'accès basé sur les rôles (RBAC)
    dans une application ASP.NET prenant en charge les revendications à l'aide de WIF et ACS](http://msdn.microsoft.com/fr-fr/library/gg185914.aspx)
-   [ Configuration de l'approbation entre ACS et les applications web ASP.NET
    à l'aide de certificats X.509](http://msdn.microsoft.com/fr-fr/library/gg185947.aspx)
-   [Exemple de code : formulaires ASP.NET simples](http://msdn.microsoft.com/fr-fr/library/gg185938.aspx)

### Service WCF (SOAP) avec identité de service

Dans ce scénario d'architecture d'application, votre service WCF (SOAP) peut être
déployée sur Azure ou en local. Une application web
ou même un autre service web accède à ce service comme un
service en aval. Vous devez contrôler l'accès à ce service en utilisant une identité spécifique à
l'application. De fait, le type de compte de pool d'applications que vous utilisiez
dans IIS présente des caractéristiques similaires. Même si la technologie est différente
les approches sont comparables en ce sens que l'accès au service s'effectue via un compte dont l'étendue est l'application et non pas un
compte d'utilisateur final.

Utilisez la fonctionnalité Identité de service du contrôle d'accès Azure AD.
Ceci est similaire au compte de pool d'applications IIS que vous utilisiez lors du
déploiement de vos applications sur Windows Server et IIS. Configurez le contrôle d'accès
Azure AD de sorte qu'il émette des jetons SAML qui seront gérés par WIF
au niveau du service WCF (SOAP).

![WCF (SOAP) Service][04]


Pour implémenter ce scénario, consultez les ressources suivantes :

-   [ 	Ajout d'identités de service avec certificat X.509, mot de passe,
    ou clé symétrique](http://msdn.microsoft.com/fr-fr/library/gg185924.aspx)
-   [ Authentification à l'aide d'un certificat client auprès d'un service WCF
    protégé par ACS](http://msdn.microsoft.com/fr-fr/library/hh289316.aspx)
-   [ Authentification à l'aide d'un nom d'utilisateur et d'un mot de passe client
    protégé par ACS](http://msdn.microsoft.com/fr-fr/library/gg185954.aspx)
-   [Exemple de code : authentification par certificat WCF](http://msdn.microsoft.com/fr-fr/library/gg185952.aspx)
-   [Exemple de code : authentification du nom d'utilisateur WCF](http://msdn.microsoft.com/fr-fr/library/gg185927.aspx)

### Service WCF (SOAP) avec authentification, identités fédérées dans Active Directory

Dans ce scénario d'architecture d'application, votre service WCF (SOAP) peut être
déployée sur Azure ou en local. Vous devez contrôler l'accès à ce
service à l'aide d'une identité gérée par un service d'annuaire d'entreprise
Windows Server Active Directory (AD).

Utilisez le contrôle d'accès Azure AD configuré pour la fédération
avec les services AD FS de Windows Server. Dans ce cas, il n'est pas nécessaire de configurer l'identité
de service avec le contrôle d'accès Azure AD. L'agent qui doit accéder au service
WCF (SOAP) fournit les informations d'identification aux services AD FS, qui émettent
un jeton à l'issue de l'authentification. Le jeton est
ensuite envoyé au contrôle d'accès Azure AD, qui le réémet à destination
de l'agent. L'agent utilise le jeton pour envoyer la demande au service WCF
(SOAP).

![WCF (SOAP) Service With AD][05]

Pour implémenter ce scénario, consultez les ressources suivantes :

-   [ 	Ajout d'identités de service avec certificat X.509, mot de passe,
    ou clé symétrique](http://msdn.microsoft.com/fr-fr/library/gg185924.aspx)
-   [ configuration d'AD FS 2.0 en tant que fournisseur d'identité](http://msdn.microsoft.com/fr-fr/library/gg185961.aspx)
-   [ Utilisation du service de gestion pour configurer AD FS 2.0
    en tant que fournisseur d'identité d'entreprise](http://msdn.microsoft.com/fr-fr/library/gg185905.aspx)
-   [Exemple de code : authentification fédérée WCF avec AD FS 2.0
](http://msdn.microsoft.com/fr-fr/library/hh127796.aspx)

### Service WCF (SOAP) avec les identités de service

Dans ce scénario, votre service WCF (REST) peut être déployé sur 
Azure ou en local. Une application web ou un autre service web accède à ce service
comme un service en aval. Vous devez contrôler l'accès à ce service
à l'aide d'une identité spécifique à l'application. De fait, le type de compte de
pool d'applications que vous utilisiez dans IIS présente des caractéristiques similaires.
Même si la technologie est différente, les approches sont comparables dans le sens où l'accès au service s'effectue via
un compte dont la portée est l'application et non pas compte d'utilisateur final.

Utilisez la fonctionnalité Identité de service du contrôle d'accès Azure AD.
Configurez le contrôle d'accès Azure AD pour émettre des jetons SWT
(Simple Web Token). Pour gérer le jeton SWT du côté du service REST, vous pouvez
soit implémenter un gestionnaire de jetons personnalisé et le rattacher
au pipeline WIF, soit l'analyser " manuellement " sans utiliser
l'infrastructure.

Examinez le schéma suivant (WIF est facultatif) :

![REST Service][06]

Pour implémenter ce scénario, consultez les ressources suivantes :

-   [ Configuration de l'approbation entre ACS et le service WCF à l'aide
    de clés symétriques](http://msdn.microsoft.com/fr-fr/library/gg185958.aspx)
-   [ Authentification auprès d'un service WCF REST déployé sur
    Azure à l'aide d'ACS](http://msdn.microsoft.com/fr-fr/library/hh289317.aspx)
-   [Exemple de code : service Web ASP.NET](http://msdn.microsoft.com/fr-fr/library/gg983271.aspx)
-   [Exemple de code : application Windows Phone 7](http://msdn.microsoft.com/fr-fr/library/gg983271.aspx)
-   [Service WCF REST avec un jeton SWT émis par le service de
    contrôle d'accès Azure (ACS)](http://code.msdn.microsoft.com/REST-WCF-With-SWT-Token-123d93c0)

### Service WCF (REST) avec Live ID/Compte Microsoft, Facebook, Google, Yahoo!, Open ID

Dans ce scénario, votre service WCF (REST) peut être déployé dans 
Azure ou en local. Vous devez contrôler l'accès à ce service en utilisant
une identité Internet publique (par exemple, Live ID/Compte Microsoft ou Facebook).

Utilisez le contrôle d'accès Azure AD pour émettre des jetons SWT. Pour gérer le jeton SWT du côté
du service REST, vous pouvez soit implémenter un gestionnaire de jetons
personnalisé et le rattacher au pipeline WIF, soit l'analyser " manuellement "
sans utiliser l'infrastructure WIF.

Il est important de noter que pour implémenter ce scénario, l'application
doit utiliser un contrôle de navigateur web pour recueillir les informations d'identification de
l'utilisateur final. Autrement dit, ceci exclut les scénarios où l'accès au service REST
s'effectue à partir d'une application web ASP.NET. Cela convient seulement aux
scénarios où l'accès au service REST s'effectue via l'application cliente
de l'utilisateur, comme une application Windows Phone 7 ou un client pour ordinateur de
bureau enrichi. La principale raison expliquant l'intervention du contrôle de navigateur web
est que les identités Internet ne prennent pas en charge en mode natif les scénarios
de profil actif (scénario de services web). Les identités Internet prennent essentiellement en charge
les scénarios de profil passif (applications web) qui reposent sur les redirections de navigateur : c'est dans ce contexte que
l'utilité du contrôle de navigateur web prend tout son sens.

Examinez le schéma suivant (l'infrastructure WIF étant facultative, elle n'est pas représentée ici) :

![WIF is optional][07]

Pour implémenter ce scénario, consultez les ressources suivantes :

-   [ authentification auprès d'un service WCF REST déployé dans Azure à l'aide d'ACS](http://msdn.microsoft.com/fr-fr/library/hh289317.aspx)
-   [ configuration de Google en tant que fournisseur d'identité](http://msdn.microsoft.com/fr-fr/library/gg185976.aspx)
-   [ configuration de Facebook en tant que fournisseur d'identité](http://msdn.microsoft.com/fr-fr/library/gg185919.aspx)
-   [ configuration de Yahoo! en tant que fournisseur d'identité](http://msdn.microsoft.com/fr-fr/library/gg185977.aspx)
-  [Exemple de code : application Windows Phone 7](http://msdn.microsoft.com/fr-fr/library/gg983271.aspx)
-   [Service WCF REST avec un jeton SWT émis par le service de
    contrôle d'accès Azure (ACS)](http://code.msdn.microsoft.com/REST-WCF-With-SWT-Token-123d93c0)


### Application Web ASP.NET vers le service WCF REST avec jeton SWT partagé

Dans ce scénario, vous disposez d'une application distribuée avec une application web
ASP.NET frontale et un service REST en aval, et vous voulez conserver le contexte
de l'utilisateur final à travers les niveaux physiques. Ceci est parfois nécessaire lorsqu'il s'agit
d'implémenter une logique d'autorisation ou une journalisation basées sur l'identité
de l'utilisateur final dans le service REST en aval.

Configurez le contrôle d'accès Azure AD de sorte qu'il émette un jeton SWT. Le jeton SWT
est émis à destination de l'application Web ASP.NET frontale,
puis il est partagé avec le service REST en aval. Dans ce cas, seule une partie de confiance
est configurée dans le contrôle d'accès Azure AD. Toutefois, il convient de
faire plusieurs mises en garde :

-   Comme WIF ne fournit pas de gestionnaire de jetons SWT prêt à l'emploi,
    vous devez implémenter un gestionnaire de jetons personnalisé à utiliser avec l'application web
    ASP.NET. Au lieu de l'implémenter vous-même, appuyez-vous plutôt
    sur le gros du travail effectué par WIF pour prendre en charge le protocole
    WS-Federation qui repose sur les redirections de navigateur.
-   Lors de l'implémentation d'un gestionnaire de jetons SWT, vérifiez que le jeton
    de démarrage fait l'objet d'un adressage, de façon à pouvoir le mémoriser.
    À défaut, vous ne pourrez pas le partager et l'envoyer au service REST
    en aval.
-   Vous n'êtes pas tenu d'utiliser WIF dans un service REST
    À la place, vous pouvez analyser le jeton " manuellement ", car dans ce cas, il n'est pas nécessaire
    de gérer les redirections.

![ASP.NET Web Application][08]

Pour implémenter ce scénario, consultez les ressources suivantes :

-   [ configuration de Google en tant que fournisseur d'identité](http://msdn.microsoft.com/fr-fr/library/gg185976.aspx)
-   [ configuration de Facebook en tant que fournisseur d'identité](http://msdn.microsoft.com/fr-fr/library/gg185919.aspx)
-   [ configuration de Yahoo! en tant que fournisseur d'identité](http://msdn.microsoft.com/fr-fr/library/gg185977.aspx)
-   [Délégation d'application web ASP.NET vers le service WCF REST à l'aide d'un jeton
    Jeton](http://code.msdn.microsoft.com/ASPNET-Web-App-To-REST-WCF-b2b95f82)

### Contrôle d'accès en fonction du rôle dans les applications et services prenant en charge les revendications

Dans ce scénario, vous devez implémenter l'autorisation dans votre application ou service
web en fonction des rôles utilisateurs : seuls les utilisateurs qui ont le rôle
nécessaire obtiennent l'accès, et l'accès est refusé à ceux qui n'ont pas ce rôle. Pour simplifier,
votre application doit être en mesure de répondre à cette question simple : l'utilisateur a-t-il
le rôle X ?

Il existe plusieurs façons de résoudre ce scénario. Vous pouvez utiliser le contrôle d'accès Azure AD,
le gestionnaire d'authentification par revendication WIF, le mappage samlSecurityTokenRequirement
ou le gestionnaire de rôles personnalisé.

WIF est utilisé dans tous les cas. WIF prend en charge la méthode
IPrincipal.IsInRole("MonRôle"). Dans la plupart des cas, il est essentiel de s'assurer que le jeton
contient la revendication du type de rôle avec l'URI de
http://schemas.microsoft.com/ws/2008/06/identity/claims/role
pour permettre à WIF de vérifier l'appartenance au rôle lors de l'appel
de la méthode IsInRole.

**Contrôle d'accès Azure AD**. Cette implémentation fait appel
au moteur de règles de transformation des revendications du contrôle d'accès Azure AD. En utilisant
les règles du moteur de règles de transformation des revendications, vous pouvez transformer une
revendication entrante en revendication du type de rôle,
si bien que quand le jeton parvient à l'application ou à un service, WIF peut analyser cette revendication de rôle pour s'assurer
que l'appel de la méthode IsInRole aboutit.

![][09]

**Gestionnaire d'authentification par revendication WIF**. Dans cette implémentation,
utilisez le gestionnaire d'authentification par revendication (ClaimsAuthenticationManager) comme point d'extensibilité de WIF. Cette approche
consiste à transformer des revendications entrantes arbitraires en un type de
revendication de rôle au niveau de l'application. La complexité de la transformation est limitée
seulement par le code que vous écrivez.

![][10]

**Mappage samlSecurityTokenRequirement**. Dans cette implémentation, vous utilisez
la configuration samlSecurityTokenRequirement de web.config pour indiquer à WIF
les types de revendications qui se comportent comme s'ils étaient des types de revendications de rôle. Par exemple, 
si le jeton véhicule une revendication de type groupe, vous pouvez le mapper au type
de revendication de rôle. Avec cette approche, vous ne pouvez effectuer que des mappages simples.

![][11]

**Gestionnaire de rôles personnalisé.** Cette implémentation vous permet d'implémenter un gestionnaire
de rôles personnalisé. WIF est utilisé pour inspecter les revendications entrantes
lors de l'implémentation de méthodes d'interface RoleManager personnalisées telles que GetAllRoles().

![][12]

Pour implémenter ce scénario, consultez les ressources suivantes :

-   [ Implémentation du contrôle d'accès basé sur les rôles (RBAC)
    dans une application ASP.NET prenant en charge les revendications à l'aide de WIF et ACS](http://msdn.microsoft.com/fr-fr/library/gg185914.aspx)
-   [ implémentation de la logique de transformation des jetons à l'aide de règles](http://msdn.microsoft.com/fr-fr/library/gg185955.aspx)
-   [Autorisation avec RoleManager pour les applications web ASP.NET prenant en charge les
    revendications (WIF)](http://blogs.msdn.com/b/alikl/archive/2010/11/18/authorization-with-rolemanager-for-claims-aware-wif-asp-net-web-applications.aspx)
-   Exemple de code : Utilisation de revendications dans IsInRole dans le [Kit de développement logiciel (SDK) Windows Identity
    Foundation](http://www.microsoft.com/downloads/details.aspx?FamilyID=c148b2df-c7af-46bb-9162-2c9422208504)

### Autorisation basée sur les revendications dans les applications et services prenant en charge les revendications

Dans ce scénario, vous devez implémenter une logique d'autorisation
complexe dans votre application ou service web. Or, la méthode IsInRole()
ne répond pas à vos besoins en matière d'autorisation. Si votre approche en matière
d'autorisation repose sur les rôles, envisagez une implémentation du contrôle
d'accès basé sur les rôles, qui est décrit dans la section précédente.

Utilisez ClaimsAuthorizationManager comme point d'extensibilité WIF.
ClaimsAuthorizationManager permet les appels de vérification d'accès externe.
Le code de votre application apparaît ainsi plus propre et d'une maintenance plus facile que quand les vérifications d'accès sont
implémentées dans le code de l'application.

![][13]

Pour implémenter ce scénario, consultez les ressources suivantes :

-   [ implémentation de la logique de transformation des jetons à l'aide de règles](http://msdn.microsoft.com/fr-fr/library/gg185955.aspx)
-   [ Implémentation de l'autorisation de revendications dans une application ASP.NET
    prenant en charge les revendications à l'aide de WIF et ACS](http://msdn.microsoft.com/fr-fr/library/gg185907.aspx)
-   Exemple de code : Autorisation basée sur les revendications dans le [Kit de développement logiciel (SDK) Windows
    Identity Foundation](http://www.microsoft.com/downloads/details.aspx?FamilyID=c148b2df-c7af-46bb-9162-2c9422208504)


## Scénarios d'identité et d'accès pour le service de stockage Azure

Dans ce scénario, vous devez partager de façon sécurisée
conteneurs de stockage Azure.

Utilisez des signatures d'accès partagé. Pour accéder à votre compte de service de stockage
à partir de votre propre application, utilisez le hachage partagé
accessible via le portail Azure lors de la configuration et de la gestion de vos
comptes de service de stockage. Si vous voulez permettre à quelqu'un d'autre
d'accéder aux objets blob et aux conteneurs de votre compte de service de stockage, utilisez des URL de signature
d'accès partagé.

Veillez à gérer les informations de manière sécurisée pour éviter de les exposer ;
de même, accordez une attention particulière à la durée de vie des
signatures d'accès partagé.

![][14]

Pour résoudre ce scénario, consultez les ressources suivantes :

-   [Gestion de l'accès aux objets blob et aux conteneurs](http://msdn.microsoft.com/fr-fr/library/ee393343.aspx)
-   [Nouvelle fonctionnalité de stockage : les signatures d'accès partagé](http://blog.smarx.com/posts/new-storage-feature-signed-access-signatures)
-   [Signatures d'accès partagé : une facilité d'utilisation accrue](http://blog.smarx.com/posts/shared-access-signatures-are-easy-these-days)


## Scénarios d'identité et d'accès pour la base de données SQL Azure

La base de données SQL prend en charge uniquement l'authentification SQL Server. L'authentification
    Windows (sécurité intégrée) n'est pas prise en charge. Les utilisateurs
doivent fournir leurs informations d'identification (nom d'utilisateur et mot de passe) chaque fois qu'ils se connectent à
une base de données SQL. Pour éviter toute divulgation d'informations,
montrez-vous particulièrement vigilant lors de la gestion de votre nom d'utilisateur et de votre mot de passe.

![][15]

Pour résoudre ce scénario, consultez les ressources suivantes :

-   [Instructions de sécurité et limitations (Base de données SQL)](http://msdn.microsoft.com/fr-fr/library/windowsazure/ff394108.aspx#authentication)
-   [ Connexion à la base de données SQL à l'aide de sqlcmd](http://msdn.microsoft.com/fr-fr/library/windowsazure/ee336280.aspx)
-   [ Connexion à la base de données SQL à l'aide d'ADO.NET](http://msdn.microsoft.com/fr-fr/library/windowsazure/ee336243.aspx)
-   [ Connexion à la base de données SQL à l'aide d'ASP.NET](http://msdn.microsoft.com/fr-fr/library/windowsazure/ee621781.aspx)
-   [ Connexion à la base de données SQL au moyen de WCF Data Services](http://msdn.microsoft.com/fr-fr/library/windowsazure/ee621789.aspx)
-  [ Connexion à la base de données SQL à l'aide de PHP](http://msdn.microsoft.com/fr-fr/library/windowsazure/ff394110.aspx)
-   [ Connexion à la base de données SQL à l'aide de JDBC](http://msdn.microsoft.com/fr-fr/library/windowsazure/gg715284.aspx)
-   [ Connexion à la base de données SQL à l'aide d'ADO.NET Entity Framework](http://msdn.microsoft.com/fr-fr/library/windowsazure/ff951633.aspx)

## Scénarios d'identité et d'accès pour Azure Service Bus

Service Bus et le contrôle d'accès Azure AD ont une relation
spéciale en ce sens que chaque espace de noms du service Service Bus
est couplé à un espace de noms correspondant du service de contrôle d'accès du même nom, suivi du
suffixe " -sb ". La raison de cette relation spéciale se trouve dans la façon
dont les services Service Bus et Contrôle d'accès gèrent leur relation de confiance mutuelle
et les questions secrètes de chiffrement associées. Pour plus d'informations,
consultez les ressources répertoriées ci-dessous.

![][16]

Pour résoudre ce scénario, consultez les ressources suivantes :

-   [Sécurisation de Service Bus avec ACS](http://channel9.msdn.com/posts/Securing-Service-Bus-with-ACS) (vidéo)
-   [Sécurisation de Service Bus avec ACS](https://skydrive.live.com/view.aspx?cid=123CCD2A7AB10107&resid=123CCD2A7AB10107%211849) (diapositives)
-   [Authentification et autorisation du Service Bus avec le service de
    contrôle d'accès](http://msdn.microsoft.com/fr-fr/library/hh403962.aspx)

## Scénarios d'identité et d'accès pour le cache en mémoire

Le cache en mémoire (antérieurement appelé Cache Azure)
s'appuie sur le contrôle d'accès Azure AD pour l'authentification. Il utilise des clés partagées
accessibles via le portail de gestion. Utilisez les clés de votre code
ou des fichiers de configuration lors de l'accès au cache. Veillez à stocker les clés en
lieu sûr pour éviter toute divulgation d'informations.

![][17]


Pour résoudre ce scénario, consultez les ressources suivantes :

-   [ Configurer un client de cache par programmation pour la
    Mise en cache](http://msdn.microsoft.com/fr-fr/library/windowsazure/gg618003.aspx)
-   [ Configurer un client de cache à l'aide du fichier de configuration d'application
    pour la mise en cache Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/gg278346.aspx)
-   [Exemples de Service Bus et de mise en cache Azure](http://msdn.microsoft.com/fr-fr/library/ee706741.aspx) (section Exemples
    de mise en cache)

## Scénarios d'identité et d'accès pour Azure Marketplace

Chaque accès à un jeu de données Azure Marketplace, qu'il soit gratuit ou payant,
passe obligatoirement par l'authentification de l'utilisateur. Quand vous
créez une application, vous devez inclure le processus d'authentification
dans votre code. Examinez les scénarios courants suivants :

### J'accède à mon jeu de données

Dans ce scénario, vous créez une application qui utilise des jeux de données
de votre abonnement Marketplace. Vous êtes l'utilisateur de l'application.
L'application peut être déployée dans Azure, en local ou dans Marketplace.

Utilisez la clé partagée accessible via votre abonnement
Marketplace. Vous obtenez la clé partagée sur le portail Marketplace.

![][18]

Pour résoudre ce scénario, consultez les ressources suivantes :

-   [Utilisation de l'authentification HTTP de base dans votre application Marketplace](http://msdn.microsoft.com/fr-fr/library/gg193417.aspx)

### Des utilisateurs accèdent à mes jeux de données

Dans ce scénario, vous créez une application permettant aux utilisateurs
d'accéder à vos jeux de données. L'application peut être déployée sur Azure,
en local ou sur Marketplace.

Pour résoudre ce scénario, utilisez la délégation OAuth. Les utilisateurs seront invités
à fournir leurs informations d'identification (Live ID/Compte Microsoft),
puis à effectuer le processus de consentement.

![][19]

Pour résoudre ce scénario, consultez les ressources suivantes :

-   [Exemple de client Web OAuth](http://go.microsoft.com/fwlink/?LinkId=219162)
-   [Exemple de client riche OAuth](http://go.microsoft.com/fwlink/?LinkId=219163)

### L'application accède à l'API Marketplace

Dans ce scénario, vous créez une application qui accède à
l'API Marketplace. Cette API requiert une authentification pour que
les appels à celle-ci aboutissent. L'application est déployée sur
Azure Marketplace.

![][20]

Pour plus d'informations sur l'implémentation de l'authentification,
consultez le kit de publication Marketplace.

Pour résoudre ce scénario, consultez les ressources suivantes :

-   [Télécharger le kit de publication d'application](http://go.microsoft.com/fwlink/?LinkId=221323)
-   [Présentation d'Azure Marketplace pour les applications](https://datamarket.azure.com/)

## Dispositifs de sécurité

Cette section décrit les dispositifs de sécurité pour Windows Identity Foundation
et le contrôle d'accès Azure AD. Utilisez-la comme liste de contrôle de sécurité
de base pour ces technologies lors de la conception et du déploiement de votre application.

### Windows Identity Foundation

Les dispositifs de sécurité suivants s'appliquent à WIF. Les informations ci-dessous sont extraitesdes articles [Considérations sur la conception WIF](http://msdn.microsoft.com/fr-fr/library/ee517298.aspx) et [Sécurité WIF (Windows Identity Foundation) pour les applications web ASP.NET - Menaces et contre-mesures](http://blogs.msdn.com/b/alikl/archive/2010/12/02/windows-identity-foundation-wif-security-for-asp-net-web-applications-threats-amp-countermeasures.aspx)
.

-   **IssuerNameRegistry**. Spécifie les services d'émission de jeton de sécurité
    (STS) approuvés. Assurez-vous que seuls sont répertoriés des services STS approuvés.
-   **cookieHandler requireSsl="true"**. Spécifie si les cookies de session
    sont transférés via le protocole SSL.
-   **wsFederation's requireHttps="true"**. Spécifie si la communication
    du protocole de fédération avec le fournisseur d'identité
    est effectuée via le protocole SSL.
-   **tokenReplayDetection enabled="true"**. Spécifie si la fonctionnalité de
    détection de relecture de jeton est activée. Sachez que cette fonctionnalité
    crée une affinité de serveur, car elle gère des copies locales des jetons utilisés.
-   **audienceUris**. Indique l'audience ciblée du jeton. Si votre
    application reçoit un jeton qui ne lui était pas destiné,
    WIF le rejette.
-   **requestValidation** et **httpRuntime requestValidationType**.
    Active/désactive la fonctionnalité de validation ASP.NET. Reportez-vous aux recommandations
    décrites dans l'article [Windows Identity Foundation (WIF) : une valeur potentiellement dangereuse
    de Request.Form a été détectée à partir du client](http://social.technet.microsoft.com/wiki/contents/articles/1725.windows-identity-foundation-wif-a-potentially-dangerous-request-form-value-was-detected-from-the-client-wresult-t-requestsecurityto.aspx)

### Contrôle d'accès Azure AD

Envisagez les dispositifs de sécurité suivants lors du déploiement du contrôle d'accès
Azure AD. Les informations ci-dessous sont extraites des articles [Consignes de sécurité
relatives à ACS](http://msdn.microsoft.com/fr-fr/library/gg185962.aspx) et [Instructions relatives à la gestion des certificats et des clés](http://msdn.microsoft.com/fr-fr/library/hh204521.aspx).

-   **Expiration des jetons STS**. Utilisez le portail de gestion du contrôle d'accès
    Azure AD pour définir un délai d'expiration des jetons agressif.
-   **Validation des données lorsque la fonctionnalité URL d'erreur est utilisée**. La fonctionnalité URL
    d'erreur du contrôle d'accès Azure AD requiert un accès anonyme à la page de
    l'application où sont envoyés les messages d'erreur. Partez du principe que toutes les données
    qui parviennent à cette page sont dangereuses et issues d'une source non approuvée.
-   **Chiffrement des jetons pour les scénarios très sensibles**. Pour limiter les risques
    de divulgation des informations disponibles dans les jetons,
    envisagez de chiffrer ces derniers.
-   **Chiffrement des cookies à l'aide de RSA lors du déploiement sur Azure**.
    WIF chiffre les cookies en utilisant DPAPI par défaut. Cela crée une affinité de
    serveur et peut provoquer des exceptions dans le cadre d'un déploiement
    dans des environnements de batterie de serveurs web et Azure. Utilisez plutôt RSA 
    dans ce cas.
-   **Certificats de signature de jeton**. Renouvelez périodiquement les certificats de
    signature de jeton pour éviter un déni de service. Le contrôle d'accès Azure AD signe tous
    les jetons de sécurité qu'il émet. Des certificats X.509 sont
    utilisés pour la signature quand vous créez une application qui utilise des
    jetons SAML émis par ACS. Quand des certificats de signature expirent,
    vous recevez une erreur quand vous tentez de demander un jeton.
-   **Clés de signature de jeton**. Renouvelez périodiquement les clés de signature
    de jeton pour éviter un déni de service. Le contrôle d'accès Azure AD signe tous les
    jetons de sécurité qu'il émet. Des clés de signature symétrique de 256 bits
    sont utilisées quand vous créez une application qui utilise des jetons
    SWT émis par ACS. Quand des clés de signature expirent, vous recevez une erreur
    quand vous tentez de demander un jeton.
-   **Certificats de chiffrement de jeton**. Renouvelez périodiquement les certificats de chiffrement
    de jeton pour éviter un déni de service. Le chiffrement
    de jeton est requis si l'application par partie de confiance est un service web
    utilisant des jetons preuves de possession via
    le protocole WS-Trust ; autrement, le chiffrement de jeton est facultatif. Quand des certificats
    de chiffrement expirent, vous recevez une erreur quand vous tentez de
    demander un jeton.
-   **Certificats de déchiffrement de jeton**. Renouvelez périodiquement les certificats de déchiffrement
    de jeton pour éviter un déni de service. Le contrôle d'accès
    Azure AD peut accepter des jetons chiffrés provenant de fournisseurs
    d'identité WS-Federation (par exemple AD FS 2.0). Un certificat X.509
    hébergé dans le contrôle d'accès Azure AD est utilisé pour le chiffrement.
    Quand des certificats de chiffrement expirent, vous recevez des erreurs quand vous tentez
    de demander un jeton.
-   **Informations d'identification de l'identité de service**. Renouvelez périodiquement les informations d'identification
    signature de jeton pour éviter un déni de service. Les identités de service
    utilisent des informations d'identification configurées globalement pour votre espace
    de noms Contrôle d'accès Azure AD,
    ce qui permet aux applications ou aux clients de s'authentifier directement auprès du contrôle d'accès Azure AD et
    de recevoir un jeton. L'identité de service du contrôle d'accès
    Azure AD peut être associée à trois types d'informations d'identification : une clé symétrique,
    un mot de passe et un certificat X.509. Vous commencerez à recevoir
    des exceptions dès que les informations d'identification seront expirées.
-   **Informations d'identification du compte de service de gestion du contrôle
    d'accès Azure AD**. Renouvelez périodiquement les informations d'identification du service de
    gestion pour éviter un déni de service. Le service de gestion du contrôle
    d'accès Azure AD est un composant clé qui vous permet de gérer et de configurer
    par programmation les paramètres de votre espace de noms 
    Contrôle d'accès Azure AD. Le compte de service de gestion
    peut être associé à trois types d'informations d'identification. Il s'agit
    de la clé symétrique, du mot de passe et d'un certificat X.509. Vous
    commencerez à recevoir des exceptions dès que les informations d'identification seront expirées.
-   **Certificats de signature et de chiffrement du fournisseur
    d'identité WS-Federation**. Interrogez le fournisseur d'identité WS-Federation
    pour tester la validité des certificats et ainsi éviter un déni de service. Un certificat de fournisseur d'identité WS-Federation
    est disponible via ses métadonnées.
    Lors de la configuration d'un fournisseur d'identité WS-Federation, comme AD FS, le
    certificat de signature WS-Federation est configuré à travers
    des métadonnées WS-Federation disponibles via une URL ou sous forme de fichier. Après avoir
    configuré le fournisseur d'identité WS-Federation, utilisez le
    service de gestion du contrôle d'accès Azure AD pour l'interroger quant à la validité
    de ses certificats. Vous commencerez à recevoir des exceptions dès
    l'expiration du certificat.

## Hébergement partagé à l'aide de sites web Azure

Tous les scénarios et solutions décrits dans cette rubrique sont
valides dans la mesure où l'application est hébergée sur des sites web Azure.

## Azure Virtual Machines

Tous les scénarios et solutions décrits dans cette rubrique sont
valides dans la mesure où l'application est hébergée sur des machines virtuelles Azure.

## Ressources

-   [Kit de formation développeur dans le domaine de l'identité](http://go.microsoft.com/fwlink/?LinkId=214555)
-   [Cours de formation développeur dans le domaine de l'identité (MSDN)](http://go.microsoft.com/fwlink/?LinkId=214561)
-   [Guide sur l'identité et le contrôle d'accès basés sur les revendications](http://go.microsoft.com/fwlink/?LinkId=214562)
-   [Service de contrôle d'accès](http://msdn.microsoft.com/fr-fr/library/windowsazure/gg429786.aspx)
-   [Rubrique de procédure du service de contrôle d'accès (ACS)](http://msdn.microsoft.com/fr-fr/library/windowsazure/gg185939.aspx)
-   [Sécurisation d'une application Web ASP.NET de rôle Web Azure à l'aide du service de contrôle d'accès v2.0](http://social.technet.microsoft.com/wiki/contents/articles/2590.aspx)
-   [Vidéos sur le service de contrôle d'accès (ACS) Azure AD](http://social.technet.microsoft.com/wiki/contents/articles/2777.aspx)
-   [Cycle de vie de développement de la sécurité (SDL) Microsoft](http://www.microsoft.com/security/sdl/default.aspx)
-   [Outil SDL de modélisation des menaces 3.1.8](http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=2955)
-   [Blogs sur la sécurité et la confidentialité](http://www.microsoft.com/about/twc/en/us/blogs.aspx)
-   [Security Response Center](http://www.microsoft.com/security/msrc/default.aspx)
-   [Rapport de renseignement sur la sécurité (SIR)](http://www.microsoft.com/security/sir/)
-   [Cycle de vie de développement de la sécurité (SDL)](http://www.microsoft.com/security/sdl/default.aspx)
-   [Centre de développement de la sécurité (MSDN)](http://msdn.microsoft.com/security/)


[01]:./media/SecurityRX/01_SecuringTheApplication.gif
[02]:./media/SecurityRX/02_ThreatsVulnerabilitiesandAttacks.gif
[03]:./media/SecurityRX/03_WindowsAzureADAccesscontrol.gif
[04]:./media/SecurityRX/04_WCF(SOAP)Service.gif
[05]:./media/SecurityRX/05_AzureADAccessControl.gif
[06]:./media/SecurityRX/06_RESTService.gif
[07]:./media/SecurityRX/07_WIFisOptional.gif
[08]:./media/SecurityRX/08_ASPNETWebApptoREST.gif
[09]:./media/SecurityRX/09_RBAC.gif
[10]:./media/SecurityRX/10_WIFClaimsAuthenticationManager.gif
[11]:./media/SecurityRX/11_SecurityTokenRequriementmapping.gif
[12]:./media/SecurityRX/12_CustomRoleManager.gif
[13]:./media/SecurityRX/13_ClaimsAuthorizationManager.gif
[14]:./media/SecurityRX/14_WindowsAzurestorage.gif
[15]:./media/SecurityRX/15_SQLAzureIdentityandAccessScenarios.gif
[16]:./media/SecurityRX/16_WindowsAzureServiceBusIdentity.gif
[17]:./media/SecurityRX/17_WindowsAzureCacheIdentity.gif
[18]:./media/SecurityRX/18_IAccessMyDataset.gif
[19]:./media/SecurityRX/19_UsersAccessMyDatasets.gif
[20]:./media/SecurityRX/20_ApplicationAccessMarketplaceAPI.gif

[Conception SSO de Web]: http://technet.microsoft.com/fr-fr/library/dd807033(WS.10).aspx
[Conception SSO de Web fédéré]: http://technet.microsoft.com/fr-fr/library/dd807050(WS.10).aspx

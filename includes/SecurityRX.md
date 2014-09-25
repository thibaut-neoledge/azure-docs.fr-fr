
# Recommandations en matière de sécurité Azure

## Résumé

Lorsqu'il s'agit de développer des applications pour Azure, l'identité et l'accès représentent en matière de sécurité les principaux enjeux qu'il ne faut pas perdre de vue. Cette rubrique explique les principaux enjeux de sécurité liés à l'identité et à l'accès dans le cloud, ainsi que les techniques permettant d'optimiser la protection des applications cloud.

## Vue d'ensemble

La sécurité d'une application est subordonnée à sa surface d'exposition. En effet, les risques de sécurité d'une application sont proportionnels à sa surface d'exposition. Par exemple, une application qui s'exécute en tant que processus de traitement par lots automatisé est moins exposée du point de vue de la sécurité qu'un site Web accessible au public.

Lorsque vous passez dans le cloud, vous gagnez une certaine tranquillité d'esprit pour ce qui est de l'infrastructure et de la mise en réseau, car ces aspects sont gérés dans des centres de données selon des pratiques, des outils et des technologies de sécurité de premier ordre. En revanche, votre application se trouve intrinsèquement plus exposée dans le cloud, avec une surface d'exposition supérieure qui peut être exploitée par des personnes malveillantes. Cela s'explique par le fait que bon nombre de technologies et de services cloud sont exposés en tant que points de terminaison par opposition aux composants en mémoire. Le stockage Azure, Service Bus, la base de données SQL (anciennement SQL Azure) et bien d'autres services sont accessibles sur le réseau via leurs points de terminaison.

Dans ce contexte, les développeurs d'applications cloud sont plus que jamais contraints de concevoir et de développer des applications conformes à des normes de sécurité élevées et de veiller à ce qu'elles le restent de façon à tenir les personnes malveillantes à distance. Comme vous pouvez le constater dans le schéma ci-dessous (extrait du [PDF relatif aux notes de sécurité Azure][1], disponible sur le blog de
J.D. Meier), la partie infrastructure est gérée par le fournisseur de services cloud (dans notre cas, Azure), ce qui permet aux développeurs d'applications de davantage se consacrer à la sécurité :

![Sécurisation de
l’application](./media/SecurityRX/01_SecuringTheApplication.gif)

Le point positif, c'est que l'ensemble des pratiques, principes et techniques de développement de la sécurité que vous connaissez déjà s'appliquent toujours au développement d'applications cloud. Voici les principaux éléments dont il faut tenir compte :

* Toutes les entrées sont vérifiées selon des critères de type, de
  longueur, de portée et de chaîne pour éviter les attaques par
  injection, et toutes les données que votre application renvoie en écho
  sont parfaitement assainies.
* Les données sensibles doivent être protégées aussi bien au repos qu'en
  transit sur le réseau de façon à limiter les risques de divulgation
  d'informations et de falsification de données.
* L'audit et la journalisation doivent être correctement implémentés
  afin de limiter les risques de non-répudiation.
* L'authentification et l'autorisation doivent être implémentées en
  employant les mécanismes éprouvés de la plateforme afin d'empêcher les
  risques d'usurpation d'identité et d'élévation de privilèges.

Pour obtenir la liste complète des menaces, attaques, failles et autres contre-mesures, reportez-vous à l'[aide-mémoire : Infrastructure de sécurité des applications Web][2] et à l'[Index du guide de sécurité pour les applications][3] de l'équipe Microsoft patterns & practices.

Dans le cloud, les mécanismes d'authentification et de contrôle d'accès sont très différents de ceux des applications locales. De plus, les solutions d'authentification et de contrôle d'accès disponibles sur le marché des applications cloud sont bien plus nombreuses, ce qui peut semer la confusion et avoir des répercussions négatives sur la qualité des implémentations. La confusion s'accroît dès lors qu'il s'agit de définir ce qu'est une application cloud. Par exemple, un problème se pose lorsqu'il s'agit de déployer une application sur le cloud, alors que son mécanisme d'authentification est fourni par Active Directory. Il en va de même lorsqu'il s'agit de déployer une application en local avec des mécanismes d'authentification basés sur le cloud (par exemple, avec le contrôle d'accès Azure Active Directory, appelé précédemment Service de contrôle d'accès ou ACS).

## Menaces, failles et attaques

Une menace peut avoir de graves conséquences qu'il est essentiel d'éviter : divulgation d'informations sensibles, indisponibilité d'un service, etc. Il est pratique courante dans le monde anglo-saxon de classer les menaces par catégories à l'aide de l'acronyme
<< STRIDE >> :

* **S**poofing (usurpation d'identité)
* **T**ampering with data (altération des données)
* **R**epudiation of actions (répudiation des actions)
* **I**nformation disclosure (divulgation d'informations)
* **D**enial of service (déni de service)
* **E**levation of privileges (élévation des privilèges)

Les failles sont des bogues que nous, développeurs, introduisons dans le code d'une application, qui la rendent exploitable par des personnes malveillantes. Par exemple, l'envoi de données sensibles sous forme de texte en clair présente un risque réel de divulgation d'informations par attaque de type << reniflage du trafic >>.

Les attaques visent à exploiter ces failles et à nuire à une application. Par exemple, une exécution de script de site à site (XSS, Cross Site Scripting) est une attaque qui exploite une sortie non assainie. Autre exemple d'attaque : l'écoute électronique sur réseau, qui consiste à intercepter des informations d'identification envoyées en clair. Ces attaques peuvent entraîner une usurpation d'entité. Pour faire simple, les menaces, les failles et les attaques constituent des dangers. Les schémas suivants représentent une vue d'ensemble des dangers liés au déploiement d'une application Web dans Azure (extraits du [PDF relatif aux notes de sécurité Azure][1], disponible sur le blog de J.D. Meier) :

![Menaces, failles et
attaques](./media/SecurityRX/02_ThreatsVulnerabilitiesandAttacks.gif)

En tant que développeur, c'est vous qui pouvez contrôler les failles. Moins vous en introduisez, moins vous laissez de chances aux personnes malveillantes de les exploiter.

Les failles liées à l'identité et à l'accès vous exposent à toutes les menaces du modèle STRIDE. Par exemple, un mécanisme d'authentification mal implémenté peut entraîner une usurpation d'identité et, par voie de conséquence, une divulgation d'informations, une altération de données, des opérations de privilèges élevés, voire même un arrêt complet du service. Penchez-vous sur les questions suivantes, qui peuvent mettre en lumière des failles potentielles au niveau de l'implémentation des mécanismes d'identité et d'accès dans votre application cloud :

* Envoyez-vous des informations d'identification en clair sur le réseau
  à destination de vos services Windows Azure ?
* Stockez-vous les informations d'identification des services Azure en
  clair ?
* Vos informations d'identification des services Azure suivent-elles les
  stratégies de mots de passe fort ?
* Laissez-vous Azure vérifier les informations d'identification ou
  utilisez-vous des mécanismes de vérification personnalisés ?
* Limitez-vous les sessions d'authentification des services Azure ou la
  durée de vie des jetons à une fenêtre de temps raisonnable ?
* Vérifiez-vous les autorisations pour chaque point d'entrée Azure de
  votre application cloud distribuée ?
* En cas de défaillance, votre mécanisme d'autorisation est-il
  suffisamment sûr pour éviter d'exposer des informations sensibles ou
  d'autoriser un accès illimité ?

## Contre-mesures

La meilleure riposte face à une attaque consiste non pas à implémenter vos propres mécanismes d'identité et d'accès, mais à utiliser ceux offerts par la plateforme. Envisagez d'utiliser les technologies d'identité et d'accès suivantes :

**Windows Identity Foundation (WIF).** WIF est une bibliothèque runtime .NET intégrée à .NET Framework 4.5 (également disponible sous forme de téléchargement pour .NET 3.5/4.0). WIF assure l'essentiel du traitement des protocoles, tels que WS-Federation et WS-Trust, et des jetons, tels que SAML (Security Assertion Markup Language). Tant et si bien que vous n'avez pas besoin d'écrire du code complexe lié à la sécurité dans votre application. Les ressources suivantes fournissent des informations détaillées sur WIF :

* [Exemples Windows Identity Foundation 4.5][4] dans la galerie de code
  MSDN.
* [Outils Windows Identity Foundation 4.5 pour Visual Studio 11 Beta][5]
  dans la galerie de code MSDN.
* [Runtime Windows Identity Foundation (.Net 3.5/4.0)][6] sur MSDN.
* [Exemples Windows Identity Foundation 3.5/4.0 et modèles Visual
  Studio 2008/2010][7] sur MSDN.

**Contrôle d'accès Azure AD (appelé précédemment ACS)**. Le contrôle d'accès Windows Azure AD est un service cloud qui intègre le service d'émission de jeton de sécurité (STS, Security Token Service) et autorise la fédération avec différents fournisseurs d'identité (IdP). Il peut s'agir d'un service d'annuaire d'entreprise Active Directory ou de fournisseurs d'identité Internet (par exemple, Windows Live ID/Compte Microsoft, Facebook, Google et Yahoo!) ou encore de fournisseurs d'identité Open ID 2.0. Les ressources suivantes fournissent des informations détaillées sur le contrôle d'accès Azure AD :

* [Service de contrôle d'accès 2.0][8]
* [Scénarios et solutions utilisant ACS][9]
* [Rubrique de procédure du service de contrôle d'accès (ACS)][10]
* [Guide sur l'identité et le contrôle d'accès basés sur les
  revendications][11]
* [Kit de formation développeur dans le domaine de l'identité][12]
* [Cours de formation développeur dans le domaine de l'identité
  (MSDN)][13]

**Services AD FS (Active Directory Federation Services).**Les services AD FS 2.0 (Active Directory Federation Services) assurent la prise en charge des solutions d'identité basée sur les revendications qui font intervenir les technologies Windows Server?? et Active Directory. Les services AD FS 2.0 prennent en charge les protocoles WS-Trust, WS-Federation et SAML. Les ressources suivantes fournissent des informations détaillées sur les services AD FS :

* [Plan du contenu AD FS 2.0][14]
* [Conception SSO de Web][15]
* [Conception SSO de Web fédéré][16]

**Signatures d'accès partagé Azure.** Les signatures d'accès partagé permettent de paramétrer l'accès à une ressource d'objet blob ou de conteneur. Les ressources suivantes fournissent des informations détaillées sur les signatures d'accès partagé :

* [Gestion de l'accès aux objets blob et aux conteneurs][17]
* [Nouvelle fonctionnalité de stockage : les signatures d'accès
  partagé][18]
* [Signatures d'accès partagé : une facilité d'utilisation accrue][19]

## Plan des scénarios

Cette section décrit brièvement les principaux scénarios abordés dans cette rubrique. Servez-vous-en de plan pour trouver la solution d'identité qui convient le mieux à votre application.

* **Application ASP.NET Web Forms avec authentification fédérée.** Dans
  ce scénario, vous contrôlez l'accès à votre application ASP.NET Web
  Forms via une identité Internet (par exemple, Live ID/Compte
  Microsoft) ou une identité d'entreprise gérée dans Windows Server
  Active Directory.
* **Service WCF (SOAP) avec authentification fédérée.** Dans ce
  scénario, vous contrôlez l'accès à votre service WCF (SOAP) via des
  identités de service gérées par le contrôle d'accès Azure AD.
* **Service WCF (SOAP) avec authentification, identités fédérées dans
  Active Directory.** Dans ce scénario, vous contrôlez l'accès à votre
  service Web WCF (SOAP) via des identités gérées par le service
  d'annuaire d'entreprise Windows Server Active Directory.
* **Service WCF (REST) avec authentification fédérée.** Dans ce
  scénario, vous contrôlez l'accès à votre service WCF (REST) via des
  identités de service gérées par le contrôle d'accès Azure AD.
* **Service WCF (REST) avec Live ID/Compte Microsoft, Facebook, Google,
  Yahoo!, Open ID.** Dans ce scénario, vous contrôlez l'accès à votre
  service WCF (REST) via une identité Internet (par exemple, Live
  ID/Compte Microsoft).
* **Application Web ASP.NET vers le service WCF REST avec jeton SWT
  partagé.** Dans ce scénario, vous disposez d'une application
  distribuée avec une application Web ASP.NET frontale et un service
  REST en aval et vous souhaitez faire transiter le contexte de
  l'utilisateur final via des niveaux physiques.
* **Autorisation du contrôle d'accès en fonction du rôle dans les
  applications et services prenant en charge les revendications.** Ce
  scénario consiste à implémenter une logique d'autorisation dans votre
  application en fonction des rôles.
* **Autorisation basée sur les revendications dans les applications et
  services prenant en charge les revendications.** Ce scénario consiste
  à implémenter une logique d'autorisation dans votre application selon
  des règles d'autorisation complexes.
* **Scénarios d'identité et d'accès pour le service de stockage Azure.**
  Dans ce scénario, vous devez partager de façon sécurisée l'accès aux
  objets blob et conteneurs de stockage Azure.
* **Scénarios d'identité et d'accès pour la base de données SQL Azure.**
  La base de données SQL prend en charge uniquement l'authentification
  SQL Server. L'authentification Windows (sécurité intégrée) n'est pas
  prise en charge. Les utilisateurs doivent fournir leurs informations
  d'identification (nom d'utilisateur et mot de passe) chaque fois
  qu'ils se connectent à la base de données SQL.
* **Scénarios d'identité et d'accès pour Azure Service Bus.** Dans ce
  scénario, vous devez accéder en toute sécurité aux files d'attente
  Azure Service Bus.
* **Scénarios d'identité et d'accès pour le cache en mémoire.** Dans ce
  scénario, vous devez accéder en toute sécurité aux données gérées par
  le cache en mémoire.
* **Scénarios d'identité et d'accès pour Azure Marketplace.** Dans ce
  scénario, vous devez accéder en toute sécurité à des jeux de données
  Azure Marketplace.

## Scénarios d'identité et d'accès Azure

Cette section décrit des scénarios d'identité et d'accès courants pour différentes architectures d'application. Servez-vous du plan des scénarios pour vous orienter rapidement.

### Application ASP.NET Web Forms avec authentification fédérée

Dans ce scénario d'architecture d'application, votre application Web peut être déployée dans Azure ou en local. Les utilisateurs de l'application sont tenus de s'authentifier via le service d'annuaire d'entreprise Active Directory ou des fournisseurs d'identité Internet.

Pour résoudre ces scénarios, utilisez le contrôle d'accès Azure AD et Windows Identity Foundation.

![Contrôle d’accès Azure Active
Directory](./media/SecurityRX/03_WindowsAzureADAccesscontrol.gif)

Pour implémenter ce scénario, consultez les ressources suivantes :

* [Procédure : création de ma première application ASP.NET prenant en
  charge les revendications à l'aide d'ACS][20]
* [Procédure : hébergement de pages de connexion dans votre application
  Web ASP.NET][21]
* [Procédure : implémentation de l'autorisation de revendications dans
  une application ASP.NET prenant en charge les revendications à l'aide
  de WIF et ACS][22]
* [Procédure : implémentation du contrôle d'accès basé sur les rôles
  (RBAC) dans une application ASP.NET prenant en charge les
  revendications à l'aide de WIF et ACS][23]
* [Procédure : configuration de l'approbation entre ACS et les
  applications Web ASP.NET à l'aide de certificats X.509][24]
* [Exemple de code : formulaires ASP.NET simples][25]

### Service WCF (SOAP) avec identité de service

Dans ce scénario d'architecture d'application, votre service WCF (SOAP) peut être déployé dans Azure ou en local. Une application Web, voire un autre service Web, accède à ce service comme un service en aval. Vous devez contrôler l'accès à ce service en utilisant une identité propre à l'application. De fait, le type de compte de pool d'applications que vous utilisiez dans IIS présente des caractéristiques similaires. Même si la technologie est différente, les approches sont comparables dans le sens où l'accès au service s'effectue via un compte d'étendue d'application et non un compte d'utilisateur final.

Utilisez la fonctionnalité Identité de service du contrôle d'accès Azure AD. Le compte de pool d'applications que vous utilisiez lorsque vous déployiez vos applications dans Windows Server et IIS présentait les mêmes caractéristiques. Configurez le contrôle d'accès Azure AD de sorte qu'il émette des jetons SAML qui seront gérés par WIF au niveau du service WCF (SOAP).

![Service WCF (SOAP)][81]

Pour implémenter ce scénario, consultez les ressources suivantes :

* [Procédure : ajout d'identités de service avec certificat X.509, mot
  de passe ou clé symétrique][26]
* [Procédure : authentification à l'aide d'un certificat client, auprès
  d'un service WCF protégé par ACS][27]
* [Procédure : authentification à l'aide d'un nom d'utilisateur et d'un
  mot de passe client auprès d'un service WCF protégé par ACS][28]
* [Exemple de code : authentification par certificat WCF][29]
* [Exemple de code : authentification du nom d'utilisateur WCF][30]

### Service WCF (SOAP) avec authentification, identités fédérées dans Active Directory

Dans ce scénario d'architecture d'application, votre service WCF (SOAP) peut être déployé dans Azure ou en local. Vous devez contrôler l'accès à ce service à l'aide d'une identité gérée par un service d'annuaire d'entreprise Windows Server Active Directory (AD).

Utilisez le contrôle d'accès Azure AD configuré pour la fédération avec les services AD FS de Windows Server. Dans ce cas, vous n'avez pas besoin de configurer l'identité de service avec le contrôle d'accès Azure AD. L'agent qui doit accéder au service WCF (SOAP) fournit les informations d'identification aux services AD FS, qui émettent un jeton à l'issue de l'authentification. Le jeton est ensuite envoyé au contrôle
d'accès Azure AD, qui le réémet à destination de l'agent. L'agent utilise le jeton pour envoyer la demande au service WCF (SOAP).

![Service WCF (SOAP) avec
AD](./media/SecurityRX/05_AzureADAccessControl.gif)

Pour implémenter ce scénario, consultez les ressources suivantes :

* [Procédure : ajout d'identités de service avec certificat X.509, mot
  de passe ou clé symétrique][26]
* [Procédure : configuration d'AD FS 2.0 en tant que fournisseur
  d'identité][31]
* [Procédure : utilisation du service de gestion pour configurer
  AD FS 2.0 en tant que fournisseur d'identité d'entreprise][32]
* [Exemple de code : authentification fédérée WCF avec AD FS 2.0][33]

### Service WCF (SOAP) avec les identités de service

Dans ce scénario, votre service WCF (REST) peut être déployé dans Windows Azure ou en local. Une application Web ou un autre service Web accède à ce service comme un service en aval. Vous devez contrôler l'accès à ce service à l'aide d'une identité propre à l'application. De fait, le type de compte de pool d'applications que vous utilisiez dans IIS présente des caractéristiques similaires. Même si la technologie est différente, les approches sont comparables dans le sens où l'accès au service s'effectue via un compte d'étendue d'application et non un compte d'utilisateur final.

Utilisez la fonctionnalité Identité de service du contrôle d'accès Azure AD. Configurez le contrôle d'accès Azure AD pour qu'il émette des jetons Web simples (SWT, Simple Web Token). Pour gérer le jeton SWT du côté du service REST, vous pouvez soit implémenter un gestionnaire de jetons personnalisé et le rattacher au pipeline WIF, soit l'analyser << manuellement >> sans utiliser l'infrastructure WIF.

Examinez le schéma suivant (WIF est facultatif) :

![Service REST](./media/SecurityRX/06_RESTService.gif)

Pour implémenter ce scénario, consultez les ressources suivantes :

* [Procédure : configuration de l'approbation entre ACS et le service
  WCF à l'aide de clés symétriques][34]
* [Procédure : authentification auprès d'un service WCF REST déployé
  dans Azure à l'aide d'ACS][35]
* [Exemple de code : service Web ASP.NET][36]
* [Exemple de code : application Windows Phone 7][36]
* [Service WCF REST avec un jeton SWT émis par les services de contrôle
  d'accès Azure (ACS)][37]

### Service WCF (REST) avec Live ID/Compte Microsoft, Facebook, Google, Yahoo!, Open ID

Dans ce scénario, votre service WCF (REST) peut être déployé dans Windows Azure ou en local. Vous devez contrôler l'accès à ce service en utilisant une identité Internet publique (par exemple, Live ID/Compte Microsoft ou Facebook).

Utilisez le contrôle d'accès Azure AD pour émettre des jetons SWT. Pour gérer le jeton SWT du côté du service REST, vous pouvez soit implémenter un gestionnaire de jetons personnalisé et le rattacher au pipeline WIF, soit l'analyser << manuellement >> sans utiliser l'infrastructure WIF.

Il est important de noter que pour implémenter ce scénario, l'application doit utiliser un contrôle de navigateur Web pour recueillir les informations d'identification de l'utilisateur final. Autrement dit, cela exclut les scénarios où l'accès au service REST s'effectue à partir d'une application Web ASP.NET. Seuls sont admis les scénarios où l'accès au service REST s'effectue via l'application cliente de l'utilisateur comme une application Windows Phone 7 ou un client pour ordinateur de bureau enrichi. La principale raison expliquant l'intervention du contrôle de navigateur Web est que les identités Internet ne prennent pas en charge en mode natif les scénarios de profil actif (scénario de services Web). Les identités Internet prennent essentiellement en charge les scénarios de profil passif (applications Web) qui reposent sur les redirections de navigateur : c'est dans ce contexte que l'utilité du contrôle de navigateur Web prend tout son sens.

Examinez le schéma suivant (l'infrastructure WIF étant facultative, elle n'est pas représentée ici) :

![WIF est facultatif](./media/SecurityRX/07_WIFisOptional.gif)

Pour implémenter ce scénario, consultez les ressources suivantes :

* [Procédure : authentification auprès d'un service WCF REST déployé
  dans Azure à l'aide d'ACS][35]
* [Procédure : configuration de Google en tant que fournisseur
  d'identité][38]
* [Procédure : configuration de Facebook en tant que fournisseur
  d'identité][39]
* [Procédure : configuration de Yahoo! en tant que fournisseur
  d'identité][40]
* [Exemple de code : application Windows Phone 7][36]
* [Service WCF REST avec un jeton SWT émis par les services de contrôle
  d'accès Azure (ACS)][37]

### Application Web ASP.NET vers le service WCF REST avec jeton SWT partagé

Dans ce scénario, vous disposez d'une application distribuée avec une application Web ASP.NET frontale et un service REST en aval et vous souhaitez conserver le contexte de l'utilisateur final dans les niveaux physiques. Cela est parfois nécessaire lorsqu'il s'agit d'implémenter une logique d'autorisation ou une journalisation basées sur l'identité de l'utilisateur final dans le service REST en aval.

Configurez le contrôle d'accès Azure AD de sorte qu'il émette un jeton SWT. Le jeton SWT est émis à destination de l'application Web ASP.NET frontale puis partagé avec le service REST en aval. Dans ce cas, seule une partie de confiance est configurée dans le contrôle d'accès Azure AD. Toutefois, il existe plusieurs mises en garde :

* Sachant que WIF ne fournit pas de gestionnaire de jetons SWT prêt à
  l'emploi, vous devez implémenter un gestionnaire de jetons
  personnalisé à utiliser avec l'application Web ASP.NET. Au lieu de
  l'implémenter vous-même, appuyez-vous plutôt sur le gros du travail
  effectué par WIF pour prendre en charge le protocole WS-Federation qui
  repose sur les redirections de navigateur.
* Lors de l'implémentation d'un gestionnaire de jetons SWT personnalisé,
  veillez à ce que le jeton de démarrage soit pris en compte pour faire
  en sorte qu'il soit conservé. À défaut, vous ne pourrez pas le
  partager et l'envoyer au service REST en aval.
* Vous n'êtes pas tenu d'utiliser WIF dans un service REST. À la place,
  vous pouvez analyser le jeton << manuellement >>, car il n'est pas
  nécessaire de gérer les redirections en pareil cas.

![Application Web ASP.NET](./media/SecurityRX/08_ASPNETWebApptoREST.gif)

Pour implémenter ce scénario, consultez les ressources suivantes :

* [Procédure : configuration de Google en tant que fournisseur
  d'identité][38]
* [Procédure : configuration de Facebook en tant que fournisseur
  d'identité][39]
* [Procédure : configuration de Yahoo! en tant que fournisseur
  d'identité][40]
* [Application Web ASP.NET vers le délégation du service WCF REST avec
  un jeton SWT partagé][41]

### Contrôle d'accès en fonction du rôle dans les applications et services prenant en charge les revendications

Dans ce scénario, vous devez implémenter l'autorisation dans votre application ou service Web en fonction des rôles d'utilisateur : seuls les utilisateurs qui possèdent le rôle nécessaire obtiennent l'accès. Pour simplifier, votre application doit être en mesure de répondre à cette question simple : l'utilisateur a-t-il le rôle X ?

Il existe plusieurs façons de résoudre ce scénario. Vous pouvez utiliser le contrôle d'accès Azure AD, le gestionnaire d'authentification par revendication WIF, le mappage samlSecurityTokenRequirement ou le gestionnaire de rôles personnalisé.

WIF est utilisé dans tous les cas. WIF prend en charge la méthode IPrincipal.IsInRole("MyRole"). Dans la plupart des cas, il est essentiel de s'assurer que le jeton contient la revendication du type de rôle avec l'URI http://schemas.microsoft.com/ws/2008/06/identity/claims/role pour permettre à WIF de vérifier l'appartenance au rôle lors de l'appel de la méthode IsInRole.

**Contrôle d'accès Azure AD**. Cette implémentation fait appel au moteur de règles de transformation des revendications du contrôle d'accès Windows Azure AD. En utilisant les règles du moteur de règles de transformation des revendications, vous pouvez transformer une revendication entrante en revendication du type de rôle, si bien que lorsque le jeton parvient à l'application ou à un service, WIF peut analyser cette revendication de rôle pour s'assurer que l'appel de la méthode IsInRole aboutit.

![](./media/SecurityRX/09_RBAC.gif)

**Gestionnaire d'authentification par revendication WIF**. Dans cette implémentation, le gestionnaire d'authentification par revendication (ClaimsAuthenticationManager) est utilisé comme point d'extensibilité de WIF. Cette approche consiste à transformer des revendications entrantes arbitraires en un type de revendication de rôle au niveau de l'application. La complexité de la transformation se limite seulement au code que vous écrivez.

![](./media/SecurityRX/10_WIFClaimsAuthenticationManager.gif)

**Mappage samlSecurityTokenRequirement**. Dans cette implémentation, vous utilisez la configuration samlSecurityTokenRequirement de web.config pour indiquer à WIF les types de revendications qui se comportent comme des types de revendications de rôle. Par exemple, si le jeton véhicule une revendication de type de groupe, vous pouvez la mapper au type de revendication de rôle. Avec cette approche, vous ne pouvez effectuer que des mappages simples.

![](./media/SecurityRX/11_SecurityTokenRequriementmapping.gif)

**Gestionnaire de rôles personnalisé.** Cette implémentation vous permet d'implémenter un gestionnaire de rôles personnalisé. WIF est utilisé pour inspecter les revendications entrantes lors de l'implémentation de méthodes d'interface RoleManager personnalisées telles que GetAllRoles().

![](./media/SecurityRX/12_CustomRoleManager.gif)

Pour implémenter ce scénario, consultez les ressources suivantes :

* [Procédure : implémentation du contrôle d'accès basé sur les rôles
  (RBAC) dans une application ASP.NET prenant en charge les
  revendications à l'aide de WIF et ACS][23]
* [Procédure : implémentation de la logique de transformation des
  jetons à l'aide de règles][42]
* [Autorisation à l'aide de RoleManager pour les applications Web
  ASP.NET prenant en charge les revendications (WIF)][43]
* Exemple de code : utilisation de revendications dans IsInRole dans le
  [Kit de développement logiciel (SDK) Windows Identity Foundation][44]

### Autorisation basée sur les revendications dans les applications et services prenant en charge les revendications

Dans ce scénario, vous devez implémenter une logique d'autorisation complexe dans votre application ou service Web. Or, la méthode IsInRole() ne répond pas à vos besoins d'autorisation. Si votre approche d'autorisation repose sur les rôles, envisagez une implémentation du contrôle d'accès basé sur les rôles décrit dans la section précédente.

Utilisez ClaimsAuthorizationManager comme point d'extensibilité WIF. ClaimsAuthorizationManager autorise les appels de vérification d'accès externe. Le code de votre application apparaît ainsi plus concis et gérable que lorsque les vérifications d'accès sont implémentées dans le code de l'application.

![](./media/SecurityRX/13_ClaimsAuthorizationManager.gif)

Pour implémenter ce scénario, consultez les ressources suivantes :

* [Procédure : implémentation de la logique de transformation des
  jetons à l'aide de règles][42]
* [Procédure : implémentation de l'autorisation de revendications dans
  une application ASP.NET prenant en charge les revendications à l'aide
  de WIF et ACS][22]
* Exemple de code : autorisation basée sur les revendications dans le
  [Kit de développement logiciel (SDK) Windows Identity Foundation][44]

## Scénarios d'identité et d'accès pour le service de stockage Azure

Dans ce scénario, vous devez partager en toute sécurité l'accès aux objets blobs et conteneurs de stockage Azure.

Utilisez des signatures d'accès partagé. Pour accéder à votre compte de service de stockage à partir de votre propre application, utilisez le hachage partagé accessible via le portail Azure lors de la configuration et de la gestion de vos comptes de service de stockage. Si vous souhaitez permettre à quelqu'un d'autre d'accéder aux objets blob et conteneurs de votre compte de service de stockage, utilisez des URL de signature d'accès partagé.

Veillez à gérer les informations de manière sécurisée pour éviter de les exposer ; de même, accordez une attention particulière à la durée de vie des signatures d'accès partagé.

![](./media/SecurityRX/14_WindowsAzurestorage.gif)

Pour résoudre ce scénario, consultez les ressources suivantes :

* [Gestion de l'accès aux objets blob et aux conteneurs][17]
* [Nouvelle fonctionnalité de stockage : les signatures d'accès
  partagé][18]
* [Signatures d'accès partagé : une facilité d'utilisation accrue][19]

## Scénarios d'identité et d'accès pour la base de données SQL Azure

La base de données SQL prend uniquement en charge l'authentification SQL Server. L'authentification Windows (sécurité intégrée) n'est pas prise en charge. Les utilisateurs doivent fournir leurs informations d'identification (nom d'utilisateur et mot de passe) chaque fois qu'ils se connectent à la base de données SQL. Pour éviter toute divulgation d'informations, montrez-vous particulièrement vigilant lors de la gestion de vos nom d'utilisateur et mot de passe.

![](./media/SecurityRX/15_SQLAzureIdentityandAccessScenarios.gif)

Pour résoudre ce scénario, consultez les ressources suivantes :

* [Instructions de sécurité et limitations (Base de données SQL)][45]
* [Connexion à une base de données SQL à l'aide de sqlcmd][46]
* [Connexion à une base de données SQL à l'aide d'ADO.NET][47]
* [Connexion à une base de données SQL au moyen d'ASP.NET][48]
* [Connexion à une base de données SQL au moyen de WCF Data
  Services][49]
* [Connexion à une base de données SQL à l'aide de PHP][50]
* [Connexion à une base de données SQL à l'aide de JDBC][51]
* [Connexion à une base de données SQL à l'aide d'ADO.NET Entity
  Framework][52]

## Scénarios d'identité et d'accès pour Azure Service Bus

Service Bus et le contrôle d'accès Azure AD ont une relation spéciale dans le sens où chaque espace de noms du service Service Bus est couplé à un espace de noms correspondant du service Contrôle d'accès, dont le nom, identique, est suivi du suffixe << -sb >>. La raison de cette relation spéciale se trouve dans la façon dont les services Service Bus et Contrôle d'accès gèrent leur relation de confiance mutuelle et les secrets cryptographiques associés. Pour plus d'informations, consultez les ressources répertoriées ci-dessous.

![](./media/SecurityRX/16_WindowsAzureServiceBusIdentity.gif)

Pour résoudre ce scénario, consultez les ressources suivantes :

* [Sécurisation de Service Bus avec ACS][53] (vidéo)
* [Sécurisation de Service Bus avec ACS][54] (vidéo)
* [Authentification et autorisation de Service Bus avec le service de
  contrôle d'accès][55]

## Scénarios d'identité et d'accès pour le cache en mémoire

Le cache en mémoire (appelé précédemment Cache Azure) a recours au contrôle d'accès Azure AD pour les besoins d'authentification. Il utilise des clés partagées accessibles via le portail de gestion. Utilisez les clés de votre code ou des fichiers de configuration lors de l'accès au cache. Veillez à stocker les clés en lieu sûr pour éviter toute divulgation d'informations.

![](./media/SecurityRX/17_WindowsAzureCacheIdentity.gif)

Pour résoudre ce scénario, consultez les ressources suivantes :

* [Configuration d'un client de cache par programme pour la mise en
  cache Azure][56]
* [Configuration d'un client de cache à l'aide du fichier de
  configuration d'application pour la mise en cache Azure][57]
* [Exemples Service Bus et mise en cache Azure][58] (section Exemples de
  mise en cache)

## Scénarios d'identité et d'accès pour Azure Marketplace

L'autorisation de tout accès à un jeu de données Azure Marketplace, qu'il soit gratuit ou payant, passe obligatoirement par l'authentification de l'utilisateur. Lorsque vous créez une application, vous devez inclure le processus d'authentification dans votre code. Examinez les scénarios courants suivants :

### J'accède à mon jeu de données

Dans ce scénario, vous créez une application qui utilise les jeux de données de votre abonnement Marketplace. Vous êtes l'utilisateur de l'application. L'application peut être déployée dans Azure, en local ou dans Marketplace.

Utilisez la clé partagée accessible via votre abonnement Marketplace. Vous obtenez la clé partagée sur le portail Marketplace.

![](./media/SecurityRX/18_IAccessMyDataset.gif)

Pour résoudre ce scénario, consultez les ressources suivantes :

* [Utilisation de l'authentification HTTP de base dans votre application
  Marketplace][59]

### Des utilisateurs accèdent à mes jeux de données

Dans ce scénario, vous créez une application permettant aux utilisateurs d'accéder à vos jeux de données. L'application peut être déployée dans Azure, en local ou dans Marketplace.

Pour résoudre ce scénario, utilisez la délégation OAuth. Les utilisateurs seront invités à fournir leurs informations d'identification (Live ID/Compte Microsoft) et à exécuter le processus de consentement.

![](./media/SecurityRX/19_UsersAccessMyDatasets.gif)

Pour résoudre ce scénario, consultez les ressources suivantes :

* [Exemple de client Web OAuth][60]
* [Exemple de client riche OAuth][61]

### L'application accède à l'API Marketplace

Dans ce scénario, vous créez une application qui accède à l'API Marketplace. Cette API exige une authentification pour que les appels à celle-ci aboutissent. L'application est déployée dans Azure Marketplace.

![](./media/SecurityRX/20_ApplicationAccessMarketplaceAPI.gif)

Pour plus d'informations sur l'implémentation de l'authentification, consultez le kit de publication Marketplace.

Pour résoudre ce scénario, consultez les ressources suivantes :

* [Télécharger le kit de publication d'application][62]
* [Présentation d'Azure Marketplace pour les applications][63]

## Dispositifs de sécurité

Cette section décrit les dispositifs de sécurité pour Windows Identity Foundation et le contrôle d'accès Azure AD. Vous pouvez vous en servir comme liste de contrôle de sécurité de base pour ces technologies au moment de concevoir et de déployer votre application.

### Windows Identity Foundation

Les dispositifs de sécurité suivants s'appliquent à WIF. Les informations ci-dessous sont extraites des articles [Considérations en matière de conception WIF][64] et [Sécurité WIF (Windows Identity Foundation) pour les applications Web ASP.NET - Menaces et contre-mesures][65] .

* **IssuerNameRegistry**. Spécifie les services d'émission de jeton de
  sécurité (STS) approuvés. Assurez-vous que seuls sont répertoriés des
  services STS approuvés.
* **cookieHandler requireSsl="true"**. Indique si les cookies de session
  sont transférés via le protocole SSL.
* **wsFederation's requireHttps="true"**. Indique si la communication du
  protocole de fédération avec le fournisseur d'identité s'est effectuée
  via le protocole SSL.
* **tokenReplayDetection enabled="true"**. Indique si la fonctionnalité
  de détection de relecture de jeton est activée. Sachez que cette
  fonctionnalité crée une affinité de serveur, car elle gère des copies
  locales des jetons utilisés.
* **audienceUris**. Indique l'audience ciblée du jeton. Si votre
  application reçoit un jeton qui ne lui était pas destiné, WIF le
  rejette.
* **requestValidation** et **httpRuntime requestValidationType**.
  Active/désactive la fonctionnalité de validation ASP.NET.
  Reportez-vous aux recommandations décrites dans l'article [Windows
  Identity Foundation (WIF) : une valeur potentiellement dangereuse de
  Request.Form a été détectée à partir du client][66]

### Contrôle d'accès Azure AD

Envisagez les dispositifs de sécurité suivants lors du déploiement du contrôle d'accès Azure AD. Les informations ci-dessous sont extraites des articles [Consignes de sécurité relatives à ACS][67] et [Instructions relatives à la gestion des certificats et des clés][68].

* **Expiration des jetons STS**. Utilisez le portail de gestion du
  contrôle d'accès Azure AD pour définir un délai d'expiration des
  jetons agressif.
* **Validation des données lorsque la fonctionnalité URL d'erreur est
  utilisée**. La fonctionnalité URL d'erreur du contrôle d'accès Azure
  AD exige un accès anonyme à la page de l'application où sont envoyés
  les messages d'erreur. Partez du principe que toutes les données qui
  parviennent à cette page sont dangereuses et issues d'une source non
  approuvée.
* **Chiffrement des jetons pour les scénarios très sensibles**. Pour
  limiter les risques de divulgation des informations disponibles dans
  les jetons, envisagez de chiffrer ces derniers.
* **Chiffrement des cookies à l'aide de RSA dans le cadre d'un
  déploiement dans Azure**. Par défaut, WIF chiffre les cookies à l'aide
  de DPAPI. Cela crée une affinité de serveur et peut provoquer des
  exceptions dans le cadre d'un déploiement dans des environnements de
  batterie de serveurs Web et Azure. Utilisez plutôt RSA dans ce cas.
* **Certificats de signature de jeton**. Renouvelez régulièrement les
  certificats de signature de jeton pour éviter un déni de service. Le
  contrôle d'accès Azure AD signe tous les jetons de sécurité qu'il
  émet. Des certificats X.509 sont utilisés à des fins de signature
  lorsque vous créez une application qui utilise des jetons SAML émis
  par ACS. Dès lors que les certificats de signature expirent, vous
  obtenez une erreur lorsque vous tentez de demander un jeton.
* **Clés de signature de jeton**. Renouvelez régulièrement les clés de
  signature de jeton pour éviter un déni de service. Le contrôle d'accès
  Azure AD signe tous les jetons de sécurité qu'il émet. Des clés de
  signature symétrique de 256 bits sont utilisées lorsque vous créez une
  application qui utilise des jetons SWT émis par ACS. Dès lors que les
  clés de signature expirent, vous obtenez une erreur lorsque vous
  tentez de demander un jeton.
* **Certificats de chiffrement de jeton**. Renouvelez régulièrement les
  certificats de chiffrement de jeton pour éviter un déni de service. Le
  chiffrement de jeton est obligatoire si l'application par partie de
  confiance est un service Web utilisant des jetons preuves de
  possession dans le protocole WS-Trust ; autrement, le chiffrement de
  jeton est facultatif. Dès lors que les certificats de chiffrement
  expirent, vous obtenez une erreur lorsque vous tentez de demander un
  jeton.
* **Certificats de déchiffrement de jeton**. Renouvelez régulièrement
  les certificats de déchiffrement de jeton pour éviter un déni de
  service. Le contrôle d'accès Azure AD peut accepter les jetons
  chiffrés provenant de fournisseurs d'identité WS-Federation (par
  exemple, AD FS 2.0). Un certificat X.509 hébergé dans le contrôle
  d'accès Azure AD est utilisé pour le déchiffrement. Dès lors que les
  certificats de déchiffrement expirent, vous obtenez une erreur lorsque
  vous tentez de demander un jeton.
* **Informations d'identification de l'identité de service**. Renouvelez
  régulièrement les informations d'identification de l'identité de
  service pour éviter un déni de service. Les identités de service
  utilisent les informations d'identification configurées globalement
  pour votre espace de noms Contrôle d'accès Azure AD, ce qui permet aux
  applications ou aux clients de s'authentifier directement auprès du
  contrôle d'accès Azure AD et de recevoir un jeton. L'identité de
  service du contrôle d'accès Azure AD peut être associée à trois types
  d'informations d'identification, à savoir : une clé symétrique, un
  mot de passe et un certificat X.509. Vous commencerez à recevoir des
  exceptions dès que les informations d'identification auront expiré.
* **Informations d'identification du compte de service de gestion du
  contrôle d'accès Azure AD**. Renouvelez régulièrement les informations
  d'identification du service de gestion pour éviter un déni de service.
  Le service de gestion du contrôle d'accès Azure AD est un composant
  clé qui vous permet de gérer et de configurer par programme les
  paramètres de votre espace de noms Contrôle d'accès Azure AD. Le
  compte de service de gestion peut être associé à trois types
  d'informations d'identification. Il s'agit de la clé symétrique, du
  mot de passe et du certificat X.509. Vous commencerez à recevoir des
  exceptions dès que les informations d'identification auront expiré.
* **Certificats de signature et de chiffrement du fournisseur d'identité
  WS-Federation**. Interrogez le fournisseur d'identité WS-Federation
  pour tester la validité des certificats et ainsi éviter un déni de
  service. Le certificat du fournisseur d'identité WS-Federation est
  accessible par l'intermédiaire de ses métadonnées. Lors de la
  configuration du fournisseur d'identité WS-Federation, tel qu'AD FS,
  le certificat de signature WS-Federation est configuré par
  l'intermédiaire des métadonnées de WS-Federation disponibles via l'URL
  ou sous forme de fichier. Après avoir configuré le fournisseur
  d'identité WS-Federation, utilisez le service de gestion du contrôle
  d'accès Azure AD pour l'interroger au sujet de la validité de ses
  certificats. Vous commencerez à recevoir des exceptions dès que le
  certificat expirera.

## Hébergement partagé à l'aide de sites Web Azure

L'ensemble des scénarios et des solutions décrits dans cette rubrique sont valables dans la mesure où l'application est hébergée sur des sites Web Azure.

## Machines virtuelles Azure

L'ensemble des scénarios et des solutions décrits dans cette rubrique sont valables dans la mesure où l'application est hébergée sur des machines virtuelles Azure.

## Ressources

* [Kit de formation développeur dans le domaine de l'identité][69]
* [Cours de formation développeur dans le domaine de l'identité
  (MSDN)][70]
* [Guide sur l'identité et le contrôle d'accès basés sur les
  revendications][71]
* [Service de contrôle d'accès][72]
* [Rubrique de procédure du service de contrôle d'accès (ACS)][10]
* [Sécurisation d'une application Web ASP.NET de rôle Web Azure à l'aide
  du service de contrôle d'accès v2.0][73]
* [Vidéos sur le service de contrôle d'accès (ACS) Azure AD][74]
* [Cycle de vie de développement de la sécurité (SDL) Microsoft][75]
* [Outil SDL de modélisation des menaces 3.1.8][76]
* [Blogs sur la sécurité et la confidentialité][77]
* [Security Response Center][78]
* [Rapport de renseignement sur la sécurité (SIR)][79]
* [Cycle de vie de développement de la sécurité (SDL)][75]
* [Centre de développement de la sécurité (MSDN)][80]



[1]: http://blogs.msdn.com/b/jmeier/archive/2010/08/03/now-available-azure-security-notes-pdf.aspx
[2]: http://msdn.microsoft.com/en-us/library/ff649461.aspx
[3]: http://msdn.microsoft.com/en-us/library/ff650760.aspx
[4]: http://code.msdn.microsoft.com/site/search?f%5B0%5D.Type=SearchText&f%5B0%5D.Value=wif&f%5B1%5D.Type=Topic&f%5B1%5D.Value=claims-based%20authentication
[5]: http://visualstudiogallery.msdn.microsoft.com/e21bf653-dfe1-4d81-b3d3-795cb104066e
[6]: http://www.microsoft.com/en-us/download/details.aspx?id=17331
[7]: http://www.microsoft.com/en-us/download/details.aspx?displaylang=en&id=4451
[8]: http://msdn.microsoft.com/library/gg429786.aspx
[9]: http://msdn.microsoft.com/en-us/library/gg185920.aspx
[10]: http://msdn.microsoft.com/en-us/library/windowsazure/gg185939.aspx
[11]: http://msdn.microsoft.com/en-us/library/ff423674.aspx
[12]: http://www.microsoft.com/en-us/download/details.aspx?id=14347
[13]: http://msdn.microsoft.com/en-us/IdentityTrainingCourse
[14]: http://social.technet.microsoft.com/wiki/contents/articles/2735.ad-fs-2-0-content-map.aspx
[15]: http://technet.microsoft.com/en-us/library/dd807033(WS.10).aspx
[16]: http://technet.microsoft.com/en-us/library/dd807050(WS.10).aspx
[17]: http://msdn.microsoft.com/en-us/library/ee393343.aspx
[18]: http://blog.smarx.com/posts/new-storage-feature-signed-access-signatures
[19]: http://blog.smarx.com/posts/shared-access-signatures-are-easy-these-days
[20]: http://msdn.microsoft.com/en-us/library/gg429779.aspx
[21]: http://msdn.microsoft.com/en-us/library/gg185926.aspx
[22]: http://msdn.microsoft.com/en-us/library/gg185907.aspx
[23]: http://msdn.microsoft.com/en-us/library/gg185914.aspx
[24]: http://msdn.microsoft.com/en-us/library/gg185947.aspx
[25]: http://msdn.microsoft.com/en-us/library/gg185938.aspx
[26]: http://msdn.microsoft.com/en-us/library/gg185924.aspx
[27]: http://msdn.microsoft.com/en-us/library/hh289316.aspx
[28]: http://msdn.microsoft.com/en-us/library/gg185954.aspx
[29]: http://msdn.microsoft.com/en-us/library/gg185952.aspx
[30]: http://msdn.microsoft.com/en-us/library/gg185927.aspx
[31]: http://msdn.microsoft.com/en-us/library/gg185961.aspx
[32]: http://msdn.microsoft.com/en-us/library/gg185905.aspx
[33]: http://msdn.microsoft.com/en-us/library/hh127796.aspx
[34]: http://msdn.microsoft.com/en-us/library/gg185958.aspx
[35]: http://msdn.microsoft.com/en-us/library/hh289317.aspx
[36]: http://msdn.microsoft.com/en-us/library/gg983271.aspx
[37]: http://code.msdn.microsoft.com/REST-WCF-With-SWT-Token-123d93c0
[38]: http://msdn.microsoft.com/en-us/library/gg185976.aspx
[39]: http://msdn.microsoft.com/en-us/library/gg185919.aspx
[40]: http://msdn.microsoft.com/en-us/library/gg185977.aspx
[41]: http://code.msdn.microsoft.com/ASPNET-Web-App-To-REST-WCF-b2b95f82
[42]: http://msdn.microsoft.com/en-us/library/gg185955.aspx
[43]: http://blogs.msdn.com/b/alikl/archive/2010/11/18/authorization-with-rolemanager-for-claims-aware-wif-asp-net-web-applications.aspx
[44]: http://www.microsoft.com/downloads/details.aspx?FamilyID=c148b2df-c7af-46bb-9162-2c9422208504
[45]: http://msdn.microsoft.com/en-us/library/windowsazure/ff394108.aspx#authentication
[46]: http://msdn.microsoft.com/en-us/library/windowsazure/ee336280.aspx
[47]: http://msdn.microsoft.com/en-us/library/windowsazure/ee336243.aspx
[48]: http://msdn.microsoft.com/en-us/library/windowsazure/ee621781.aspx
[49]: http://msdn.microsoft.com/en-us/library/windowsazure/ee621789.aspx
[50]: http://msdn.microsoft.com/en-us/library/windowsazure/ff394110.aspx
[51]: http://msdn.microsoft.com/en-us/library/windowsazure/gg715284.aspx
[52]: http://msdn.microsoft.com/en-us/library/windowsazure/ff951633.aspx
[53]: http://channel9.msdn.com/posts/Securing-Service-Bus-with-ACS
[54]: https://skydrive.live.com/view.aspx?cid=123CCD2A7AB10107&resid=123CCD2A7AB10107%211849
[55]: http://msdn.microsoft.com/en-us/library/hh403962.aspx
[56]: http://msdn.microsoft.com/en-us/library/windowsazure/gg618003.aspx
[57]: http://msdn.microsoft.com/en-us/library/windowsazure/gg278346.aspx
[58]: http://msdn.microsoft.com/en-us/library/ee706741.aspx
[59]: http://msdn.microsoft.com/en-us/library/gg193417.aspx
[60]: http://go.microsoft.com/fwlink/?LinkId=219162
[61]: http://go.microsoft.com/fwlink/?LinkId=219163
[62]: http://go.microsoft.com/fwlink/?LinkId=221323
[63]: https://datamarket.azure.com/
[64]: http://msdn.microsoft.com/en-us/library/ee517298.aspx
[65]: http://blogs.msdn.com/b/alikl/archive/2010/12/02/windows-identity-foundation-wif-security-for-asp-net-web-applications-threats-amp-countermeasures.aspx
[66]: http://social.technet.microsoft.com/wiki/contents/articles/1725.windows-identity-foundation-wif-a-potentially-dangerous-request-form-value-was-detected-from-the-client-wresult-t-requestsecurityto.aspx
[67]: http://msdn.microsoft.com/en-us/library/gg185962.aspx
[68]: http://msdn.microsoft.com/en-us/library/hh204521.aspx
[69]: http://go.microsoft.com/fwlink/?LinkId=214555
[70]: http://go.microsoft.com/fwlink/?LinkId=214561
[71]: http://go.microsoft.com/fwlink/?LinkId=214562
[72]: http://msdn.microsoft.com/en-us/library/windowsazure/gg429786.aspx
[73]: http://social.technet.microsoft.com/wiki/contents/articles/2590.aspx
[74]: http://social.technet.microsoft.com/wiki/contents/articles/2777.aspx
[75]: http://www.microsoft.com/security/sdl/default.aspx
[76]: http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=2955
[77]: http://www.microsoft.com/about/twc/en/us/blogs.aspx
[78]: http://www.microsoft.com/security/msrc/default.aspx
[79]: http://www.microsoft.com/security/sir/
[80]: http://msdn.microsoft.com/security/
[81]:./media/SecurityRX/04_WCF(SOAP)Service.gif
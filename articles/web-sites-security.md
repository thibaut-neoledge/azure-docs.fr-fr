<properties title="Securing an Azure Website" pageTitle="Securing an Azure Website." description="Learn how to secure an Azure Website." metaKeywords="Azure web site security, azure web site https, azure web site ftps, azure web site ssl, azure web site ssl rewrite" services="web-sites" solutions="" documentationCenter="web" authors="larryfr" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr"></tags>

## Sécurisation d'une application web sur un site web Azure

Lors du développement d'une application web, il est primordial de s'engager à fournir un service sécurisé à vos clients. Dans cet article, vous découvrirez les fonctionnalités des sites web Azure qui peuvent sécuriser votre application web.

> [WACOM.NOTE] Ce document n'a pas pour but d'évoquer toutes les questions de sécurité concernant les applications web. Pour démarrer l'étude de la sécurisation des applications web, consultez la page [Open Web Application Security Project (OWASP)][Open Web Application Security Project (OWASP)], et en particulier la rubrique [top 10 project][top 10 project], qui répertorie les 10 problèmes de sécurité actuels les plus critiques des applications web, selon les membres de l'OWASP.

### Sommaire

-   [Sécurisation des communications][Sécurisation des communications]
-   [Sécurisation du développement][Sécurisation du développement]
-   [Étapes suivantes][Étapes suivantes]

## <a name="https"></a>Sécurisation des communications

Si vous utilisez le nom de domaine **.azurewebsites.net** créé pour votre site web, vous pouvez immédiatement utiliser HTTPS, car un certificat SSL est fourni pour tous les noms de domaine* **.azurewebsites.net**. Si votre site utilise un [nom de domaine personnalisé][nom de domaine personnalisé], vous pouvez charger un certificat SSL et activer HTTPS pour le domaine personnalisé.

Pour plus d'informations, consultez la page [Activation du protocole HTTPS pour un site web Azure][Activation du protocole HTTPS pour un site web Azure].

### Application du protocole HTTPS

Les sites web Azure n'appliquent *pas* HTTPS ; les visiteurs peuvent toujours accéder à votre site via HTTP, ce qui peut engendrer l'exposition d'informations sensibles. Pour activer HTTPS, utilisez le module **Réécriture d'URL**. Le module Réécriture d'URL est inclus avec les sites web Azure et vous permet de définir des règles qui sont appliquées aux requêtes entrantes avant qu'elles ne soient transmises à votre application. Il peut être utilisé pour des applications écrites dans n'importe quel langage de programmation pris en charge par les sites web Azure.

> [WACOM.NOTE] Les applications .NET MVC doivent utiliser le filtre [RequireHttps][RequireHttps] plutôt que le module Réécriture d'URL. Pour plus d'informations sur l'utilisation de RequireHttps, consultez la page [Déploiement d'une application ASP.NET MVC 5 sécurisée vers un site web Azure][Déploiement d'une application ASP.NET MVC 5 sécurisée vers un site web Azure].
>
> Pour plus d'informations sur la redirection par programme des requêtes utilisant d'autres langages et infrastructures de programmation, consultez la documentation de ces technologies.

Les règles de réécriture d'URL sont définies dans un fichier **web.config** stocké à la racine de votre application. L'exemple suivant contient une règle de réécriture d'URL basique qui force tout le trafic entrant à utiliser HTTPS.

<a name="example"></a>**Exemple de fichier Web.Config de réécriture d'URL**

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <rewrite>
          <rules>
            <rule name="Force HTTPS" enabled="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{HTTPS}" pattern="off" />
              </conditions>
              <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>

Cette règle fonctionne en renvoyant le code d'état HTTP 301 (redirection permanente) lorsque l'utilisateur demande une page utilisant HTTP. Le code 301 redirige la demande vers la même URL que celle requise par le visiteur, mais remplace la partie HTTP de la demande par HTTPS. Par exemple, <HTTP://contoso.com> serait redirigé vers <HTTPS://contoso.com>.

> [WACOM.NOTE] Si votre application est écrite en **Node.js**, **PHP**, **Python Django** ou **Java**, elle n'inclut probablement pas de fichier web.config. Toutefois, **Node.js**, **Python Django** et **Java** utilisent tous un fichier web.config lorsqu'ils sont hébergés sur des sites web Azure - Azure crée le fichier automatiquement durant le déploiement ; c'est pour cette raison que vous ne le voyez jamais. Si vous en incluez un comme élément de votre application, il écrasera celui qu'Azure génère automatiquement.

### .NET

Pour les applications .NET, modifiez le fichier web.config de votre application et ajoutez la section **\<rewrite\>** de l'[exemple][exemple] à la section **\<system.WebServer\>**.

Si votre fichier web.config inclut déjà une section **\<rewrite\>**, ajoutez la **\<rule\>** de l'[exemple][exemple] comme première entrée de la section **\<rules\>**.

### PHP

Pour les applications PHP, enregistrez simplement l'[exemple][exemple] comme fichier web.config à la racine de votre application, puis redéployez l'application vers votre site web Azure.

### Node.js, Python Django et Java

Un fichier web.config est automatiquement créé pour les applications Node.js, Python Django et Java s'ils n'en fournissent pas déjà un, mais il existe uniquement sur le serveur puisqu'il est créé pendant le déploiement. Le fichier généré automatiquement contient des paramètres qui disent à Azure comment héberger votre application.

Pour récupérer et modifier le fichier auto-généré à partir du site web, procédez comme suit :

1.  Téléchargez le fichier via FTP (consultez la page [Chargement/téléchargement de fichiers sur FTP et collecte de journaux de diagnostic][Chargement/téléchargement de fichiers sur FTP et collecte de journaux de diagnostic]).

2.  Ajoutez-le à la racine de votre application.

3.  Ajoutez les règles de réécriture à l'aide des informations suivantes.

    -   **Node.js et Python Django**

        Le fichier web.config généré pour les applications Node.js et Python Django auront déjà une section **\<rewrite\>**, contenant les entrées **\<rule\>** requises pour le bon fonctionnement du site. Pour forcer le site à utiliser HTTPS, ajoutez la **\<rule\>** de l'exemple comme première entrée de la section **\<rules\>**. Cette opération forcera HTTPS, tout en laissant les autres règles intactes.

    -   **Java**

        Le fichier web.config des applications Java utilisant Apache Tomcat ne contient pas de section **\<rewrite\>** ; vous devrez donc ajouter la section **\<rewrite\>** de l'exemple à la section **\<system.webServer\>**.

4.  Redéployez le projet (en incluant le fichier web.config mis à jour) vers Azure

Une fois que vous avez déployé un fichier web.config avec une règle de réécriture pour forcer HTTPS, il doit prendre effet immédiatement et rediriger toutes les demandes vers HTTPS.

Pour plus d'informations sur le module Réécriture d'URL d'IIS, consultez la documentation sur la [Réécriture d'URL][Réécriture d'URL].

## <a name="develop"></a>Sécurisation du développement

### Profils et paramètres de publication

Lorsque vous développez des applications, exécutez des tâches de gestion ou automatisez des tâches à l'aide d'utilitaires tels que **Visual Studio**, **Web Matrix**, **Azure PowerShell** ou l'**interface de ligne de commande interplateforme**, vous pouvez utiliser un fichier de *paramètres de publication* ou un *profil de publication*. Les deux vous authentifient auprès d'Azure et doivent être sécurisés pour empêcher tout accès sans autorisation.

-   Un fichier de **paramètres de publication** contient :

    -   L'identifiant de votre abonnement Azure.

    -   Un certificat de gestion qui vous permet de réaliser des tâches de gestion pour votre abonnement *sans avoir à fournir un nom de compte ou un mot de passe*.

-   Un fichier de **profil de publication** contient :

    -   Des informations pour publier sur votre site web Azure

Si vous faites appel à un utilitaire qui utilise des paramètres de publication ou un profil de publication, importez le fichier contenant les paramètres ou le profil de publication dans l'utilitaire, puis **supprimez** le fichier. Si vous devez garder le fichier, comme par exemple pour le partager avec d'autres personnes travaillant sur le projet, stockez-le dans un emplacement sécurisé comme un répertoire **chiffré** avec autorisations restreintes.

De plus, assurez-vous que les informations d'identification importées sont sécurisées. Par exemple, **Azure PowerShell** et l'**interface de ligne de commande interplateforme Azure** stockent les informations importées dans votre **répertoire de base** (*~* sur Linux et les systèmes OS X et dans */Utilisateurs/VotreNomUtilisateur* sur les systèmes Windows.) Pour plus de sécurité, vous pouvez **chiffrer** ces emplacements à l'aide d'outils de chiffrement disponibles pour votre système d'exploitation.

### Paramètres de configuration et chaînes de connexion

Il est d'usage courant de stocker les chaînes de connexion, les informations d'identification et autres informations sensibles dans les fichiers de configuration. Malheureusement, ces fichiers peuvent être exposés sur votre site web, ou déposés dans un référentiel public, exposant ces informations.

Les sites web Azure vous permettent de stocker des informations de configuration comme éléments d'environnement d'exécution de sites web sous forme de **paramètres d'application** et de **chaînes de connexion**. Ces valeurs sont exposées sur votre application au moment de l'exécution via des *variables d'environnement* pour la plupart des langages de programmation. Pour les applications .NET, ces valeurs sont injectées dans votre configuration .NET au moment de l'exécution.

Les **paramètres d'application** et les **chaînes de connexion** sont configurables via le portail de gestion Azure ou les utilitaires comme PowerShell ou l'interface de ligne de commande interplateforme Azure.

Pour plus d'informations sur les paramètres d'application et les chaînes de connexion, consultez la page [Configuration de sites web][Configuration de sites web].

### FTPS

Azure fournit un accès FTP sécurisé au système de fichiers pour votre site web via **FTPS**. Ceci vous permet d'accéder en toute sécurité au code d'application sur le site web ainsi qu'aux journaux de diagnostic. Le lien FTPS de votre site web se trouve dans le **Tableau de bord** du site, dans le [portail de gestion Azure][portail de gestion Azure].

Pour plus d'informations sur FTPS, consultez la page [Protocole FTP][Protocole FTP].

## Étapes suivantes

Pour plus d'informations sur la sécurité de la plateforme Azure, ou pour savoir comme signaler **un incident de sécurité ou un abus**, ou pour informer Microsoft que vous allez exécuter un **test de pénétration** de votre site, consultez la section Sécurité du [Centre de gestion de la confidentialité Microsoft Azure][Centre de gestion de la confidentialité Microsoft Azure].

Pour plus d'informations sur les fichiers **web.config** ou **applicationhost.config** des sites web Azure, consultez la page [Options de configuration déverrouillées sur les sites web Azure][Options de configuration déverrouillées sur les sites web Azure].

Pour des détails sur les informations de journalisation pour les sites web Azure, qui peuvent être utiles pour détecter les attaques, consultez la page [Activation de la journalisation des diagnostics][Activation de la journalisation des diagnostics].

  [Open Web Application Security Project (OWASP)]: https://www.owasp.org/index.php/Main_Page
  [top 10 project]: https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project
  [Sécurisation des communications]: #https
  [Sécurisation du développement]: #develop
  [Étapes suivantes]: #next
  [nom de domaine personnalisé]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-custom-domain-name/
  [Activation du protocole HTTPS pour un site web Azure]: /fr-fr/documentation/articles/web-sites-configure-ssl-certificate/
  [RequireHttps]: http://msdn.microsoft.com/fr-fr/library/system.web.mvc.requirehttpsattribute.aspx
  [Déploiement d'une application ASP.NET MVC 5 sécurisée vers un site web Azure]: /fr-fr/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
  [exemple]: #example
  [Chargement/téléchargement de fichiers sur FTP et collecte de journaux de diagnostic]: http://blogs.msdn.com/b/avkashchauhan/archive/2012/06/19/windows-azure-website-uploading-downloading-files-over-ftp-and-collecting-diagnostics-logs.aspx
  [Réécriture d'URL]: http://www.iis.net/downloads/microsoft/url-rewrite
  [Configuration de sites web]: /fr-fr/documentation/articles/web-sites-configure/
  [portail de gestion Azure]: https://manage.windowsazure.com
  [Protocole FTP]: http://en.wikipedia.org/wiki/File_Transfer_Protocol
  [Centre de gestion de la confidentialité Microsoft Azure]: /fr-fr/support/trust-center/security/
  [Options de configuration déverrouillées sur les sites web Azure]: http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/
  [Activation de la journalisation des diagnostics]: /fr-fr/documentation/articles/web-sites-enable-diagnostic-log/

<properties urlDisplayName="Create a Line-of-Business Application on Azure Websites" pageTitle="Création d'applications métier dans Sites Web Azure" metaKeywords="Web Sites" description="This guide provides a technical overview of how to use Azure Websites to create intranet, line-of-business applications. This includes authentication strategies, service bus relay, and monitoring." umbracoNaviHide="0" disqusComments="1" editor="mollybos" manager="wpickett" title="Create a Line-of-Business Application on Azure Websites" authors="jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/01/2014" ms.author="jroth" />



# Création d'applications métier dans Sites Web Azure

Ce guide fournit un aperçu technique de l'utilisation de Sites Web Azure pour créer des applications métier. Pour les besoins de ce document, nous supposerons qu'il s'agit d'applications intranet devant être sécurisées à des fins d'utilisation en interne. Les applications métier présentent deux caractéristiques spécifiques : elles requièrent une authentification, généralement via un annuaire d'entreprise, et nécessitent normalement un accès ou une intégration aux données et services locaux. Ce guide aborde la génération d'applications métier dans [Azure Web Sites][websitesoverview]. Toutefois, dans certaines situations, [Azure Cloud Services][csoverview] ou [Azure Virtual Machines][vmoverview] conviendront mieux à vos besoins. Examinez les différences entre ces options dans la rubrique [Comparaison entre Azure Web Sites, Azure Cloud Services et Azure Virtual Machines][chooseservice]. 

Les questions couvertes par ce guide sont les suivantes :

- [Présentation des avantages](#benefits)
- [Identification de la stratégie d'authentification](#authentication)
- [Création d'un site web Azure nécessitant une authentification](#createintranetsite)
- [Utilisation de Service Bus à des fins d'intégration aux ressources locales](#servicebusrelay)
- [Surveillance de l'application](#monitor)

<div class="dev-callout">
<strong>Remarque</strong>
<p>Ce guide présente les domaines et les tâches parmi les plus courants s'adaptant au développement de sites .COM publics. Cependant, Azure Web Sites offre encore d'autres fonctionnalités, que vous pouvez utiliser pour votre implémentation spécifique. Pour passer en revue ces fonctionnalités, consultez les autres guides <a href="http://www.windowsazure.com/fr-fr/manage/services/web-sites/global-web-presence-solution-overview/">Présence web globale</a> et <a href="http://www.windowsazure.com/fr-fr/manage/services/web-sites/digital-marketing-campaign-solution-overview">Campagnes de marketing numérique</a>.</p>
</div>

##<a name="benefits"></a>Présentation des avantages
Étant donné que les applications métier ciblent généralement des entreprises, il convient de se pencher sur les avantages de l'utilisation du cloud plutôt que de l'infrastructure et des ressources locales. Tout d'abord, il faut tenir compte des avantages classiques du cloud, comme la possibilité d'évolution en fonction de la charge de travail dynamique. Prenons l'exemple d'une application permettant de gérer les bilans de performances annuels. Pendant la plus grande partie de l'année, ce type d'application ne gère qu'un trafic réduit. Mais, dans le cas d'une grande entreprise, ce trafic connaît une très forte augmentation pendant la période des bilans. Azure offre des options de mise à l'échelle permettant à une entreprise d'accroître ses capacités pendant la période de fort trafic tout en réalisant des économies avec des capacités réduites pendant tout le reste de l'année. Le cloud permet également de se concentrer davantage sur le développement de l'application et de passer moins de temps sur l'acquisition et la gestion de l'infrastructure.

Outre ces avantages classiques, placer une application métier dans le cloud permet à tous les employés et partenaires d'une entreprise d'utiliser l'application depuis n'importe où. Les utilisateurs n'ont en effet pas besoin d'être connectés au réseau de l'entreprise pour pouvoir utiliser l'application, et cela évite au service informatique de devoir mettre en place des solutions de proxy inverse complexes. Plusieurs options d'authentification sont disponibles pour protéger l'accès aux applications ; elles sont présentées dans les sections suivantes de ce guide.

##<a name="authentication"></a>Identification de la stratégie d'authentification
Le choix de la stratégie d'authentification constitue, dans le cadre des applications métier, l'une des décisions les plus importantes. Plusieurs options sont alors possibles :

- Utiliser le [service Azure Active Directory][adoverview]. ce service peut être utilisé en tant qu'annuaire autonome, ou synchronisé avec un annuaire Active Directory local. Dans ce cas, les applications interagissent avec Azure Active Directory pour l'authentification des utilisateurs. Pour une présentation de cette approche, consultez [Utilisation d'Azure Active Directory][adusing].
- Utiliser Azure Virtual Machines et Azure Virtual Network pour installer Active Directory : cela permet d'étendre une installation Active Directory locale au cloud. Il est également possible d'utiliser Active Directory Federation Services (AD FS) pour fédérer les demandes d'identification vers l'annuaire Active Directory local. L'authentification pour l'application Azure utilise alors AD FS pour accéder à l'annuaire Active Directory local. Pour plus d'informations sur cette approche, consultez [Exécution de Windows Server Active Directory sur des machines virtuelles][adwithvm] et [Recommandations en matière de déploiement de Windows Server Active Directory dans Azure Virtual Machines][addeployguidelines]. 
- Utilisez un service intermédiaire, tel que [Azure Access Control Service][acs2] (ACS), afin d'utiliser plusieurs services d'identité pour authentifier les utilisateurs. cela fournit une abstraction vers l'authentification via Active Directory ou un autre fournisseur d'identité. Pour plus d'informations, consultez [Utilisation d'Azure Active Directory Access Control][acsusing].

Pour notre scénario d'application métier, la première approche avec Azure Active Directory constitue le moyen le plus rapide pour implémenter une stratégie d'authentification. C'est donc l'option à laquelle s'intéresse ce guide. Cependant, en fonction de vos besoins métier, il se peut que vous préfériez adopter l'une des deux autres solutions présentées. Par exemple, si vous n'êtes pas autorisé à synchroniser les informations d'identité avec le cloud, AD FS peut s'avérer être une meilleure option. De même, si vous devez prendre en charge d'autres fournisseurs d'identité, tels que Facebook, ACS serait plus adapté.

Avant que vous ne configuriez un nouvel annuaire Azure Active Directory, notez que certains services, tels qu'Office 365 ou Windows Intune, exploitent déjà le service Azure Active Directory. Dans ce cas, il est préférable d'associer votre abonnement existant à votre abonnement Azure. Pour plus d'informations, consultez [Qu'est-ce qu'un locataire Azure AD ?][adtenant].

Si vous ne disposez encore d'aucun service utilisant Azure Active Directory, vous pouvez créer un nouvel annuaire, via la section **Active Directory** du portail de gestion.

![BusinessApplicationsAzureAD][BusinessApplicationsAzureAD]

Une fois votre annuaire créé, vous pouvez créer et gérer des utilisateurs, des applications intégrées et des domaines.

![BusinessApplicationsADUsers][BusinessApplicationsADUsers]

Pour une procédure pas à pas complète, consultez [Ajout de l'authentification à une application web à l'aide d'Azure AD][adsso]. Si vous souhaitez utiliser ce nouvel annuaire en tant que ressource autonome, vous devez à présent développer des applications intégrées. Cependant, si vous disposez d'identités Active Directory locales, il est plus judicieux de les synchroniser avec le nouvel annuaire Azure Active Directory. Pour plus d'informations sur la façon de procéder, consultez [Intégration d'un annuaire][dirintegration].

Une fois l'annuaire créé et alimenté, vous devez créer des applications Web nécessitant une authentification afin de les intégrer à l'annuaire. Ces étapes sont présentées dans les sections suivantes.

##<a name="createintranetsite"></a>Créer un site web Azure prenant en charge l'authentification
Dans notre scénario de présence sur le web à l'international, nous avons passé en revue les différentes options de création et de déploiement d'un site web. Si vous n'êtes pas encore familiarisé avec Azure Web Sites, [consultez ces informations][scenarioglobalweb]. Une application ASP.NET sous Visual Studio constitue un choix classique pour une application Web intranet utilisant l'authentification Windows, notamment en raison de l'étroite intégration et de la prise en charge de ce scénario offertes par ASP.NET et Visual Studio.

Par exemple, quand vous créez un projet ASP.NET MVC 4 dans Visual Studio, vous pouvez créer une **application intranet** dans la boîte de dialogue de création de projet.

![BusinessApplicationsVSIntranetApp][BusinessApplicationsVSIntranetApp]

Cela permet d'apporter des modifications aux paramètres du projet en vue de prendre en charge l'authentification Windows. En particulier, l'attribut **mode** de l'élément **authentification** est défini sur **Windows** dans le fichier web.config. Vous devez le modifier manuellement si vous souhaitez créer un autre projet ASP.NET, tel qu'un projet Web Forms, ou utiliser un projet existant.

Pour un projet MVC, vous devez également accéder à la fenêtre des propriétés du projet, afin de modifier deux valeurs : Définissez l'**authentification Windows** sur **Activé** et l'**authentification anonyme** sur **Désactivé**.

![BusinessApplicationsVSProperties][BusinessApplicationsVSProperties]

Pour pouvoir utiliser l'authentification via Azure Active Directory, vous devez inscrire l'application auprès de l'annuaire, puis modifier la configuration de l'application à des fins de connexion. Il existe deux méthodes pour ce faire dans Visual Studio :

- [Outil Identité et accès pour Visual Studio](#identityandaccessforvs)
- [Outils Microsoft ASP.NET pour Azure Active Directory](#aspnettoolsforwaad)

###<a name="identityandaccessforvs"></a>Outil Identité et accès pour Visual Studio :
Vous pouvez utiliser l'[outil Identité et accès][identityandaccess] (que vous pouvez télécharger et installer). qui s'intègre au menu contextuel du projet, dans Visual Studio. Les instructions et captures d'écran suivantes concernent Visual Studio 2012. Cliquez avec le bouton droit et sélectionnez **Identité et accès**. Vous devez alors configurer trois options. Sous l'onglet **Fournisseurs**, indiquez le **chemin d'accès au document de métadonnées STS** et l'**URI ID de l'application** (pour obtenir ces valeurs, consultez la section [Inscription d'applications dans Azure Active Directory](#registerwaadapp)).

![BusinessApplicationsVSIdentityAndAccess][BusinessApplicationsVSIdentityAndAccess]

La dernière modification à apporter à la configuration concerne l'onglet**Configuration** de la boîte de dialogue **Identité et accès**. Vous devez cocher la case **Activer les cookies de la batterie de serveurs**. Pour une procédure pas à pas complète, consultez [Ajout de l'authentification à une application web à l'aide d'Azure AD][adsso].

####<a name="registerwaadapp"></a>Inscription de l'application auprès d'Azure Active Directory
Pour compléter l'onglet **Fournisseurs**, vous devez inscrire votre application auprès d'Azure Active Directory. Dans le portail de gestion Azure, dans la section **Active Directory**, sélectionnez votre annuaire, puis accédez à l'onglet **Applications**. pour ajouter votre site web Azure à l'aide de son URL. Notez que lors de cette procédure, vous devez initialement définir l'URL sur l'adresse d'hôte local fournie pour le débogage local dans Visual Studio. Vous pourrez ensuite la remplacer par la véritable URL du site web durant le déploiement.

![BusinessApplicationsADIntegratedApps][BusinessApplicationsADIntegratedApps]

Une fois le site web ajouté, le portail indique le chemin d'accès au document de métadonnées STS (l'**URL du document de métadonnées de fédération**) et l'**URI ID de l'application**. Ces valeurs sont utilisées sous l'onglet **Fournisseurs** de la boîte de dialogue **Identité et accès** de Visual Studio. 

![BusinessApplicationsADAppAdded][BusinessApplicationsADAppAdded]

###<a name="aspnettoolsforwaad"></a>Outils Microsoft ASP.NET pour Azure Active Directory
Vous pouvez également utiliser les [outils Microsoft ASP.NET pour Azure Active Directory][aspnettools]. Pour ce faire, cliquez sur l'élément **Activer l'authentification Azure** dans le menu **Projet** de Visual Studio. Une boîte de dialogue s'affiche alors, vous invitant à entrer l'adresse du domaine Azure Active Directory (et non l'URL de l'application).

![BusinessApplicationsVSEnableAuth][BusinessApplicationsVSEnableAuth]

Si vous êtes l'administrateur de ce domaine Active Directory, cochez la case **Approvisionner cette application dans Azure AD**. afin d'inscrire l'application auprès d'Active Directory. Dans le cas contraire, désactivez cette case à cocher et transmettez les informations affichées à un administrateur. Celui-ci pourra alors utiliser le portail de gestion pour créer une application intégrée à l'aide de la procédure décrite précédemment dans l'outil Identity and Access Tool. Pour plus d'informations sur l'utilisation des outils ASP.NET pour Azure Active Directory, consultez [Authentification Azure][azureauthtutorial].

Dans le cadre de la gestion de cette application métier, vous avez la possibilité d'utiliser n'importe quel système de contrôle de code source pris en charge pour le déploiement. Cependant, pour notre scénario, en raison du haut niveau d'intégration Visual Studio, Team Foundation Service (TFS) constitue le système de contrôle le plus adapté. Notez que, dans ce cas, Sites Web Azure offre une intégration à TFS. Dans le portail de gestion, accédez à l'onglet **Tableau de bord** de votre site web. Sélectionnez **Configurer le déploiement à partir du contrôle de code source**. puis suivez les instructions d'utilisation de TFS. 

![BusinessApplicationsDeploy][BusinessApplicationsDeploy]

##<a name="servicebusrelay"></a>Utilisation de Service Bus à des fins d'intégration aux ressources locales
Un grand nombre d'applications métier doivent être intégrées aux données et services locaux. Pour des raisons pratiques ou réglementaires, certaines données ne peuvent être déplacées vers le cloud. Il est donc important, quand vous devez déterminer les données à héberger dans Azure et les données à conserver localement, de passer en revue les différentes ressources dans le [Centre de gestion de la confidentialité Azure][trustcenter]. Les applications Web hybrides s'exécutent dans Azure et accèdent aux ressources conservées en local.

Avec Azure Virtual Machines ou Cloud Services, vous pouvez utiliser Virtual Network pour connecter les applications dans Azure à un réseau d'entreprise. Toutefois, Azure Web Sites ne prend pas en charge Azure Virtual Networks. Le meilleur moyen d'effectuer ce type d'intégration avec Azure Web Sites est d'utiliser le [service de relais Azure Service Bus][sbrelay]. Ce service permet d'établir une connexion sécurisée entre les applications dans le cloud et les services WCF exécutés sur un réseau d'entreprise. La communication s'effectue ainsi sans ouvrir de ports de pare-feu. 

Dans l'illustration ci-après, l'application dans le cloud et le service WCF local communiquent avec Service Bus via un espace de noms précédemment créé. Le service WCF local a accès aux données et services internes qui ne peuvent être placés dans le cloud, et enregistre un point de terminaison dans l'espace de noms. Le site web exécuté dans Azure se connecte également à ce point de terminaison au sein de Service Bus. Cette étape ne nécessite que l'envoi continu de requêtes HTTP publiques.

![BusinessApplicationsServiceBusRelay][BusinessApplicationsServiceBusRelay]

Service Bus connecte ensuite l'application dans le cloud au service WCF local. Cela permet de fournir une architecture de base pour la création d'applications hybrides utilisant à la fois les services et les ressources conservés dans Azure et en local. Pour plus d'informations, consultez [Utilisation du service de relais Service Bus][sbrelayhowto] et le [Didacticiel sur la messagerie par relais Service Bus][sbrelaytutorial]. Vous pouvez également consulter un exemple illustrant cette technique : [Pizzeria - Connexion de sites web à un réseau local via Service Bus][enterprisepizza].

##<a name="monitor"></a>Surveillance de l'application
Les applications métier bénéficient des fonctionnalités standard des sites web, telles que la mise à l'échelle et la surveillance. Pour les applications métier dont la charge varie en fonction des heures ou des jours, la fonction Mise à l'échelle automatique (version préliminaire) peut ainsi faciliter la mise à l'échelle du site, pour une exploitation efficace des ressources. Les options de surveillance incluent la surveillance des points de terminaison et des quotas. Pour plus d'informations sur ces otpions, consultez les scénarios [Présence web globale][scenarioglobalweb] et [Campagne de marketing numérique][scenariodigitalmarketing].

Les besoins en surveillance varient selon les applications métier, en fonction de leur importance. Pour les applications les plus stratégiques, pensez à investir dans une solution de surveillance tierce, telle que [New Relic][newrelic].

Les applications métier sont généralement gérées par le service informatique. En cas d'erreur ou de comportement inattendu, il leur est possible d'activer la journalisation détaillée, à des fins d'analyse des données et d'identification du problème. Dans le portail de gestion, accédez à l'onglet **Configuration** et passez en revue les options des sections **Diagnostic d'application** et **Diagnostic de site**. 

![BusinessApplicationsDiagnostics][BusinessApplicationsDiagnostics]

Vous pouvez utiliser les divers journaux des applications et de site pour identifier et résoudre les problèmes relatifs au site web. Notez que certaines options spécifient **Système de fichiers**, dans ce cas, les fichiers journaux sont enregistrés dans le système de fichiers du site. Vous pouvez y accéder via FTP, Azure PowerShell ou à l'aide des outils en ligne de commande Azure. D'autres options spécifient **Stockage**, les informations sont alors envoyées au compte de stockage Azure spécifié. Pour l'option **Journalisation du serveur web**, vous pouvez également indiquer un quota de disque pour le système de fichiers ou une stratégie de rétention pour le stockage. Cela permet de limiter la quantité de données de journalisation stockées.

![BusinessApplicationsDiagRetention][BusinessApplicationsDiagRetention]

Pour plus d'informations sur ces paramètres de journalisation, consultez [Procédure : configuration des diagnostics et téléchargement des journaux pour un site web][configurediagnostics].

Vous pouvez consulter les données brutes via FTP ou à l'aide d'utilitaires de stockage, tels qu'Azure Storage Explorer, mais vous pouvez également consulter les informations de journalisation dans Visual Studio. Pour plus d'informations sur l'utilisation de ces journaux dans des scénarios de résolution de problèmes, consultez [Résolution des problèmes liés aux sites web dans Visual Studio][troubleshootwebsites].

##<a name="summary"></a>Résumé
Azure permet d'héberger des applications intranet sécurisées dans le cloud. Azure Active Directory permet l'authentification des utilisateurs, afin de restreindre l'accès aux applications aux seuls membres autorisés. Le service de relais Service Bus permet d'établir la communication entre les applications Web et les données et services locaux. Cette approche d'application hybride simplifie le processus de publication d'une application métier dans le cloud, supprimant la nécessité de migrer toutes les données et services connexes. Une fois déployées, les applications métier bénéficient des fonctionnalités standard de mise à l'échelle et de surveillance fournies par Sites Web Azure. Pour plus d'informations, consultez les articles techniques répertoriés ci-après.

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">Domaine</th>
   <th align="left" valign="top">Ressources</th>
</tr>
<tr>
   <td valign="middle"><strong>Planification</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/fr-fr/manage/services/web-sites/choose-web-app-service">Comparaison entre Azure Web Sites, Azure Cloud Services et Azure Virtual Machines</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Création et déploiement</strong></td>
   <td valign="top">- <a href ="http://www.windowsazure.com/fr-fr/develop/net/tutorials/get-started/">Déploiement d'une application web ASP.NET sur un site web Azure</a><br/>- <a href="http://www.windowsazure.com/fr-fr/develop/net/tutorials/web-site-with-sql-database/">Déployer une application ASP.NET MVC sécurisée sur Azure</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Authentification</strong></td>
   <td valign="top">- <a href ="http://www.windowsazure.com/fr-fr/manage/windows/fundamentals/identity/">Présentation des options d'identité Azure</a><br/>- <a href="http://www.windowsazure.com/fr-fr/documentation/services/active-directory/">Service Azure Active Directory</a><br/>- <a href="http://technet.microsoft.com/fr-fr/library/jj573650.aspx">Qu'est-ce qu'un locataire Azure AD ?</a><br/>- <a href="http://msdn.microsoft.com/library/windowsazure/dn151790.aspx">Ajout de l'authentification à votre application web à l'aide d'Azure AD</a><br/>- <a href="http://www.asp.net/aspnet/overview/aspnet-and-visual-studio-2012/windows-azure-authentication">Didacticiel sur l'authentification Azure</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Relais Service Bus</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/fr-fr/develop/net/how-to-guides/service-bus-relay/">Utilisation du service de relais Service Bus</a><br/>- <a href="http://msdn.microsoft.com/fr-fr/library/windowsazure/ee706736.aspx">Didacticiel sur la messagerie par relais Service Bus.</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Surveiller</strong></td>
   <td valign="top">- <a href ="http://www.windowsazure.com/fr-fr/manage/services/web-sites/how-to-monitor-websites/">Surveillance de sites web</a><br/>- <a href="http://msdn.microsoft.com/library/windowsazure/dn306638.aspx">Procédure : réception de notifications d'alerte et gestion des règles d'alerte dans Azure</a><br/>- <a href="http://www.windowsazure.com/fr-fr/manage/services/web-sites/how-to-monitor-websites/#howtoconfigdiagnostics">Procédure : configuration des diagnostics et téléchargement des journaux pour un site web</a><br/>- <a href="http://www.windowsazure.com/fr-fr/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/">Résolution des problèmes liés aux sites web Azure dans Visual Studio</a></td>
</tr>
</table>

  [websitesoverview]:/fr-fr/documentation/services/web-sites/
  [csoverview]:/fr-fr/documentation/services/cloud-services/
  [vmoverview]:/fr-fr/documentation/services/virtual-machines/
  [chooseservice]:/fr-fr/manage/services/web-sites/choose-web-app-service
  [scenarioglobalweb]:/fr-fr/manage/services/web-sites/global-web-presence-solution-overview/
  [scenariodigitalmarketing]:/fr-fr/manage/services/web-sites/digital-marketing-campaign-solution-overview
  [adoverview]:/fr-fr/documentation/services/active-directory/
  [adusing]:/fr-fr/manage/windows/fundamentals/identity/#ad
  [adwithvm]:/fr-fr/manage/windows/fundamentals/identity/#adinvm
  [addeployguidelines]:http://msdn.microsoft.com/fr-fr/library/windowsazure/jj156090.aspx
  [acs2]:http://msdn.microsoft.com/library/windowsazure/hh147631.aspx
  [acsusing]:/fr-fr/manage/windows/fundamentals/identity/#ac
  [adtenant]:http://technet.microsoft.com/fr-fr/library/jj573650.aspx
  [adsso]:http://msdn.microsoft.com/library/windowsazure/dn151790.aspx
  [dirintegration]:http://technet.microsoft.com/fr-fr/library/jj573653.aspx
  [identityandaccess]:http://visualstudiogallery.msdn.microsoft.com/e21bf653-dfe1-4d81-b3d3-795cb104066e
  [aspnettools]:http://go.microsoft.com/fwlink/?LinkID=282306
  [azureauthtutorial]:http://www.asp.net/aspnet/overview/aspnet-and-visual-studio-2012/windows-azure-authentication
  [trustcenter]:/fr-fr/support/trust-center/
  [sbrelay]:http://msdn.microsoft.com/fr-fr/library/windowsazure/jj860549.aspx
  [sbrelayhowto]:/fr-fr/develop/net/how-to-guides/service-bus-relay/
  [sbrelaytutorial]:http://msdn.microsoft.com/fr-fr/library/windowsazure/ee706736.aspx
  [enterprisepizza]:http://code.msdn.microsoft.com/windowsazure/Enterprise-Pizza-e2d8f2fa
  [newrelic]:http://newrelic.com/azure
  [configurediagnostics]:/fr-fr/manage/services/web-sites/how-to-monitor-websites/#howtoconfigdiagnostics
  [troubleshootwebsites]:/fr-fr/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
  [BusinessApplicationsAzureAD]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AzureAD.png
  [BusinessApplicationsADUsers]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AD_Users.png
  [BusinessApplicationsVSIntranetApp]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_IntranetApp.png
  [BusinessApplicationsVSProperties]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_Properties.png
  [BusinessApplicationsVSIdentityAndAccess]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_IdentityAndAccess.png
  [BusinessApplicationsADIntegratedApps]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AD_IntegratedApps.png
  [BusinessApplicationsADAppAdded]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AD_AppAdded.png
  [BusinessApplicationsVSEnableAuth]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_EnableAuth.png
  [BusinessApplicationsDeploy]: ./media/web-sites-business-application-solution-overview/BusinessApplications_Deploy.png
  [BusinessApplicationsServiceBusRelay]: ./media/web-sites-business-application-solution-overview/BusinessApplications_ServiceBusRelay.png
  [BusinessApplicationsDiagnostics]: ./media/web-sites-business-application-solution-overview/BusinessApplications_Diagnostics.png
  [BusinessApplicationsDiagRetention]: ./media/web-sites-business-application-solution-overview/BusinessApplications_Diag_Retention.png
  
  
  
  
  
  
  
  
  
  
  
  
  
  




  




<!--HONumber=35.1-->

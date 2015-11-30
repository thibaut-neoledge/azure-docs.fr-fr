<properties 
    pageTitle="Configuration de l'authentification unique pour les applications ne faisant pas partie de la galerie d'applications Azure Active Directory | Microsoft Azure." 
    description="Découvrez comment connecter les applications à Azure Active Directory à l'aide de SAML et de l'authentification unique basée sur le mot de passe en libre-service" 
    services="active-directory" 
    authors="asmalser-msft"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="11/18/2015" 
    ms.author="asmalser" />

#Configuration de l'authentification unique pour les applications ne faisant pas partie de la galerie d'applications Azure Active Directory.

La galerie d'applications Azure Active Directory contient une liste d'applications qui prennent en charge une forme d'authentification unique avec Azure Active Directory, conformément à la description dans [cet article](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/). Une fois que vous (spécialiste informatique ou intégrateur système de votre organisation) avez trouvé l'application que vous voulez connecter, vous pouvez commencer par suivre les instructions détaillées présentées dans le portail de gestion pour activer l'authentification unique.

Les clients titulaires de licences [Azure Active Directory Premium](https://msdn.microsoft.com/library/azure/dn532272.aspx) bénéficient de ces fonctionnalités supplémentaires, qui peuvent être appelées à partir de la catégorie **Personnalisé** de la galerie d'applications Azure AD :

* Connexion libre-service de toute application prenant en charge les fournisseurs d'identité SAML 2.0
* Connexion libre-service de n'importe quelle application Web qui a une page de connexion basée sur HTML utilisant une SSO basée sur le mot de passe
* Possibilité d'ajouter des liens à n'importe quelle application dans le [Lanceur d'application Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou le [Panneau d'accès Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/#deploying-azure-ad-integrated-applications-to-users)
* Connexion libre-service des applications qui utilisent le protocole SCIM pour l'affectation d'utilisateurs ([description ici](active-directory-scim-provisioning))

Ceci peut inclure non seulement les applications SaaS que vous utilisez, mais qui n'ont pas encore été embarquées dans la galerie d'applications Azure AD, mais encore les applications Web tierces que votre organisation a déployées sur les serveurs que vous contrôlez, dans le cloud ou localement.

Remarque : Les développeurs d'applications qui cherchent à tester la compatibilité entre leur application et cette fonctionnalité peuvent le faire à l'aide d'un [essai gratuit d'Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/), mais sont encouragés à acquérir une [licence de droits d'utilisation interne](https://mspartner.microsoft.com/en/us/pages/membership/internal-use-software.aspx).

##Ajout d'une application non répertoriée ou personnalisée 

Pour configurer une application, connectez-vous au portail de gestion Azure avec votre compte d'administrateur Azure Active Directory et accédez à la section **Active Directory > [Directory] > Applications**, sélectionnez **Ajouter**, puis **Ajouter une application à partir de la galerie**.

![][1]

Dans la galerie d'applications, vous pouvez ajouter une application personnalisée en utilisant la catégorie **Personnalisé** à gauche, ou en sélectionnant le lien **Ajouter une application non répertoriée** affiché dans les résultats de recherche si votre application souhaitée n'a pas été trouvée. Après avoir entré un nom pour votre application, vous pouvez configurer les options d'authentification unique et le comportement.

**Astuce**: comme meilleure pratique, utilisez la fonction de recherche pour vérifier si l'application existe déjà dans la galerie d'applications. Si l'application est trouvée et que sa description mentionne « authentification unique », l'application est déjà prise en charge pour une authentification unique fédérée.

![][2]

L'ajout d'une application personnalisée fournit une expérience très similaire à celle disponible pour des applications pré-intégrées. Pour commencer, sélectionnez **Configurer l’authentification unique**. L'écran suivant présente les trois options suivantes de configuration de l'authentification unique, qui sont décrites dans les sections suivantes.

![][3]


##Authentification unique Azure AD

Sélectionnez cette option pour configurer l'authentification basée SAML pour l'application. Ceci nécessite que l'application prenne en charge SAML 2.0, et vous devriez collecter des informations sur la façon d'utiliser les fonctionnalités SAML de l'application avant de continuer. Après avoir sélectionné **Suivant**, on vous demandera d'entrer trois URL différentes correspondant aux points de terminaison SAML pour l'application.

![][4]

Les icônes d'info-bulle dans la boîte de dialogue fournissent des détails sur chaque URL et la manière dont elle est utilisée. Une fois ces entrées effectuées, cliquez sur **Suivant** pour passer à l'écran suivant. Cet écran fournit des informations sur ce qui doit être configuré dans l'application pour lui permettre d'accepter un jeton SAML à partir d'Azure AD.

![][5]

Les valeurs requises varient selon l'application, consultez la documentation SAML de l'application pour en savoir plus. Les URL des services**Connexion** et **Déconnexion** correspondent toutes les deux au même point de terminaison, qui est le point de terminaison de gestion des demandes SAML pour votre instance d'Azure AD. L'URL de l'émetteur est la valeur qui s'affiche comme l'« Émetteur » dans le jeton SAML émis pour l'application.

Une fois que votre application a été configurée, cliquez sur le bouton **Suivant**, puis sur **Terminer** pour fermer la boîte de dialogue.

##Affectation d'utilisateurs et de groupes à votre application SAML 

Une fois que votre application a été configurée pour utiliser Azure AD comme fournisseur d'identité SAML, elle est presque prête pour le test. Pour contrôler la sécurité, Azure AD n'émettra pas de jeton permettant de se connecter à l'application avant que l'accès n'ait été octroyé par Azure AD. Les utilisateurs peuvent bénéficier d'un accès direct ou via un groupe dont ils sont membres.

Pour affecter un utilisateur ou un groupe à votre application, cliquez sur le bouton **Affecter des utilisateurs**. Sélectionnez l'utilisateur ou le groupe que vous souhaitez affecter, puis sélectionnez le bouton **Affecter**.

![][6]

L'affectation d'un utilisateur permettra à Azure AD d'émettre un jeton pour l'utilisateur, ainsi que d'afficher une mosaïque pour cette application dans le panneau d'accès de l'utilisateur. Une mosaïque de l'application s'affichera également dans le lanceur d'application Office 365 si l'utilisateur utilise Office 365.

Vous pouvez télécharger un logo de la mosaïque pour l'application avec le bouton **Télécharger un logo** sur l'onglet **Configurer** de l'application.

###Personnalisation des revendications émises dans le jeton SAML 

Lorsqu'un utilisateur s'authentifie auprès de l'application, Azure AD émet un jeton SAML à l'application qui contient des informations (ou des revendications) sur l'utilisateur qui l'identifient de façon unique. Par défaut, ceci inclut le nom d'utilisateur, l'adresse e-mail, le prénom et nom de l'utilisateur.

Vous pouvez afficher ou modifier les revendications envoyées à l'application dans le jeton SAML sous l'onglet **Attributs**.

![][7]

Il existe deux raisons pour lesquelles vous devrez peut-être modifier les revendications émises dans le jeton SAML : •L’application a été écrite pour exiger un ensemble différent d’URI de revendication ou de valeurs de revendication •Votre application a été déployée d'une manière qui nécessite que la revendication NameIdentifier soit différente du nom d'utilisateur (c’est-à-dire le nom d'utilisateur principal) stocké dans Azure Active Directory.

Pour plus d'informations sur la manière d'ajouter et de modifier des revendications pour ces scénarios, consultez cet [article sur la personnalisation des revendications](active-directory-saml-claims-customization.md).

###Test de l’application SAML 

Après la configuration des URL SAML et du certificat dans Azure AD et dans l'application, des utilisateurs ou des groupes ont été affectés à l'application dans Azure et les revendications ont été revues et modifiées si nécessaire. L'utilisateur est alors prêt à se connecter à l'application.

Pour tester, il suffit de se connecter au panneau d'accès Azure AD sous https://myapps.microsoft.com à l'aide d'un compte d'utilisateur que vous avez affecté à l'application, puis de cliquer sur la mosaïque de l'application pour lancer le processus d'authentification unique. Vous pouvez également naviguer directement jusqu'à l'URL de connexion de l'application et vous connecter à partir de là.

Pour avoir des conseils de débogage, consultez cet [article sur le débogage d'applications à authentification unique basées sur SAML](active-directory-saml-debugging.md)

##Authentification unique basée sur un mot de passe

Sélectionnez cette option pour configurer l'[authentification unique basée sur un mot de passe](active-directory-appssoaccess-whatis.md) pour une application Web qui a une page de connexion HTML. L'authentification unique basée sur un mot de passe, également appelée archivage de mot de passe, vous permet de gérer l'accès utilisateur et les mots de passe pour les applications Web qui ne prennent pas en charge la fédération d'identité. Elle est également utile pour les scénarios où plusieurs utilisateurs doivent partager un seul compte, par exemple les comptes d'applications de médiaux sociaux de votre organisation.

Après avoir sélectionné **Suivant**, on vous demandera d'entrer l'URL de la page de connexion de l’application basée sur le Web. Notez qu'il doit s’agir de la page qui contient le nom d’utilisateur et les champs d'entrée du mot de passe. Une fois l'entrée effectuée, Azure AD démarre un processus pour analyser la page de connexion pour un nom d'utilisateur et un mot de passe entrés. Si le processus échoue, il vous guidera à travers un autre processus d'installation d'une extension de navigateur (nécessite Internet Explorer, Chrome ou Firefox) qui vous permettra de capturer manuellement les champs.

Une fois la page de connexion capturée, les utilisateurs et les groupes peuvent être affectés et les stratégies d'informations d'identification peuvent être définies comme [applications SSO avec mot de passe](active-directory-appssoaccess-whatis.md) standard.

Remarque : Vous pouvez télécharger un logo de la mosaïque pour l'application avec le bouton **Télécharger un logo** sur l'onglet **Configurer** de l'application.

##Authentification unique existante

Sélectionnez cette option pour ajouter un lien au panneau d'accès Azure AD ou au portail Office 365 de votre organisation. Vous pouvez utiliser ceci pour ajouter des liens à des applications Web personnalisées qui utilisent actuellement des services de fédération Azure Active Directory (ou un autre service de fédération) au lieu d'Azure AD pour l'authentification. Ou bien, vous pouvez ajouter des liens ciblés vers des pages SharePoint spécifiques ou d'autres pages Web dont vous souhaitez qu'elles s'affichent uniquement sur vos panneaux d'accès d'utilisateurs.

Après avoir sélectionné **Suivant**, on vous demandera d'entrer l'URL de l'application à lier. Une fois l'opération terminée, les utilisateurs et les groupes peuvent être affectés à l'application, qui s'affiche alors dans le [Lanceur d'applications Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou le [Panneau d'accès Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/#deploying-azure-ad-integrated-applications-to-users) pour ces utilisateurs.

Remarque : Vous pouvez télécharger un logo de la mosaïque pour l'application avec le bouton **Télécharger un logo** sur l'onglet **Configurer** de l'application.

<!--Image references-->
[1]: ./media/active-directory-saas-custom-apps/customapp1.png
[2]: ./media/active-directory-saas-custom-apps/customapp2.png
[3]: ./media/active-directory-saas-custom-apps/customapp3.png
[4]: ./media/active-directory-saas-custom-apps/customapp4.png
[5]: ./media/active-directory-saas-custom-apps/customapp5.png
[6]: ./media/active-directory-saas-custom-apps/customapp6.png
[7]: ./media/active-directory-saas-custom-apps/customapp7.png

<!---HONumber=Nov15_HO4-->
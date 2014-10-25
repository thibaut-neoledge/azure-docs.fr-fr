<properties umbracoNaviHide="0" pageTitle="Azure AD Integration with Google Apps" metaKeywords="Azure Google Apps Integration" description="Learn about integrating Azure AD with Google Apps." linkid="documentation-services-identity-windows-azure-ad-integration-with-google=apps" urlDisplayName="Azure AD Integration with Google Apps" headerExpose="" footerExpose="" disqusComments="0" editor="lisatoft" manager="terrylan" title="Azure AD Integration with Google Apps" authors="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# Intégration d'Azure AD à Google Apps

L'objectif de ce didacticiel est de montrer comment intégrer Azure et Google Apps. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un client de test dans Googe Apps

Si vous n'avez pas encore de client valide dans Google Apps, vous pouvez par exemple souscrire à un compte d'évaluation sur le site web Google Apps for Business.

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

Activation de l'intégration de l'application pour Google Apps
Configuration de l'authentification unique
Activation de l'accès à l'API Google Apps
Ajout de domaines personnalisés
Configuration du déploiement des utilisateurs

# Activation de l'intégration de l'application pour Google Apps

Cette section décrit l'activation de l'intégration de l'application pour Google Apps.

## Pour activer l'application de l'intégration pour Google Apps, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.
2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
4.  Cliquez sur **Ajouter** en bas de la page pour ouvrir la boîte de dialogue **Ajouter une application**.
5.  Dans la boîte de dialogue **Intégrer une application avec Azure AD**, cliquez sur **Gérer l'accès à une application**.
6.  Sur la page **Sélectionner une application à gérer**, sélectionnez **Google Apps** dans la liste des applications.
7.  Cliquez sur le bouton **Terminer** pour ajouter l'application et fermer la boîte de dialogue.

# Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s'authentifier sur Google Apps avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

## Pour configurer l'authentification unique fédérée, procédez comme suit :

1.  Dans le portail de gestion Azure, sélectionnez **Active Directory** dans le volet de navigation gauche pour ouvrir la page de dialogue d'Active directory.
2.  Dans la liste des annuaires, sélectionnez votre annuaire pour ouvrir sa page de configuration.
3.  Sélectionnez **APPLICATIONS** dans le menu du haut.
4.  Dans la liste des applications, sélectionnez **Google Apps** pour ouvrir la boîte de dialogue de configuration de Google Apps.
5.  Cliquez sur **Configurer l'authentification unique** pour ouvrir la boîte de dialogue du même nom.

    ![Google\_Tutorial\_01][]

6.  Sur la page de dialogue Sélectionner le mode d'authentification unique pour cette application, sélectionnez Les utilisateurs s'authentifient avec leur compte dans Azure AD en tant que MODE, puis cliquez sur le bouton Suivant.

    ![Google\_Tutorial\_02][]

7.  Sur la page de dialogue **Configurer l'URL de l'application**, dans la zone de texte **URL DU CLIENT GOOGLE APPS**, entrez l'URL du client Google Apps, puis cliquez sur le bouton **Suivant**.

    ![Google\_Tutorial\_03][]

8.  Sur la page de dialogue **Configurer l'authentification unique sur Google Apps**, procédez comme suit, puis cliquez sur le bouton **Terminer**.

    -   Cliquez sur **Télécharger le certificat**, puis enregistrez le certificat sous la forme **c:\\googleapps.cer**.
    -   Ouvrez la page de connexion à Google Apps, puis connectez-vous.

    ![Google\_Tutorial\_04][]

    -   Dans la **Console d'administration**, cliquez sur Sécurité.

    ![Google\_Tutorial\_05][]

    Si l'icône Sécurité est masquée, vous devez cliquer sur Autres contrôles, en bas de la page.

9.  Sur la page **Sécurité**, cliquez sur les paramètres **Avancés**.

    ![Google\_Tutorial\_06][]

10. Dans la section des paramètres **Avancés** de la page, sélectionnez **Configurer l'authentification unique**.

    ![Google\_Tutorial\_07][]

11. Sur la page Configurer l'authentification unique, procédez comme suit :

    ![Google\_Tutorial\_08][]

        + Select <strong>Enable Single Sign-on</strong>.
        + On the <strong>Configure single sign-on at Google Apps</strong> page in the Azure AD Portal, copy the <strong>SINGLE SIGN-ON URL</strong>, and then paste it into the related textbox on the <strong>Security page</strong> in the Google Apps <strong>Admin console</strong>.
        + On the <strong>Configure single sign-on at Google Apps</strong> page in the Azure AD Portal, copy the <strong>Single sign-out service URL</strong>, and then paste it into the related textbox on the <strong>Security</strong> page in the Google Apps <strong>Admin console</strong>.
        + On the <strong>Configure single sign-on at Google Apps</strong> page in the Azure AD Portal, copy the <strong>Change password URL</strong>, and then paste it into the related textbox on the <strong>Security</strong> page in the Google Apps <strong>Admin console</strong>.
        + Click the <strong>Browse</strong> button to locate the <strong>Verification certificate</strong>, and then click Upload.
        + Click <strong>Save</strong> changes.

12. Sur la page **Configurer l'authentification unique sur Google Apps** du portail Azure AD, cliquez sur le bouton Terminer.

Vous pouvez maintenant vous rendre sur le [Volet d'accès][] et tester l'authentification unique sur Google Apps.

# Activation de l'accès à l'API Google Apps

Lorsque vous intégrez Azure Active Directory à Google Apps pour l'approvisionnement des utilisateurs, vous devez activer l'accès à l'API pour votre client dans Google Apps.

## Pour activer l'accès à l'API Google Apps, procédez comme suit :

1.  Connectez-vous à votre client Google Apps.
2.  Dans la **Console d'administration**, cliquez sur **Sécurité**.

    ![Google\_Tutorial\_05][]

    </p>
    Si l'icône Sécurité est masquée, cliquez sur Autres contrôles, en bas de la console Administration.

3.  Sur la page Sécurité, cliquez sur **Référence de l'API** pour ouvrir la page de dialogue de configuration associée.
4.  Sélectionnez **Activer l'accès à l'API**.

    ![Google\_Tutorial\_09][]

    </p>

# Ajout de domaines personnalisés

Pour que vous puissiez configurer l'approvisionnement des utilisateurs avec Google Apps, les domaines Azure AD et Google Apps doivent avoir le même nom de domaine complet (FQDN). Cependant, lorsque, par exemple, vous utilisez des clients d'évaluation pour tester le scénario de ce didacticiel, les noms de domaine complets (FQDN) de vos clients ne correspondent pas. Pour résoudre ce problème, vous pouvez configurer des domaines personnalisés dans Azure AD et dans Google Apps.
Vous devez pour cela accéder au fichier de zone DNS de votre domaine public.

![Google\_Tutorial\_10][]

## Pour ajouter un domaine personnalisé dans Azure AD, procédez comme suit :

1.  Dans le portail de gestion Azure, sélectionnez **Active Directory** dans le volet de navigation gauche pour ouvrir la page de dialogue d'**Active directory**.
2.  Dans la liste des annuaires, sélectionnez votre annuaire pour ouvrir sa page de configuration.
3.  Sélectionnez **DOMAINES** dans le menu du haut.
4.  Pour ouvrir la zone de texte **AJOUTER UN NOM DE DOMAINE**, entrez votre nom de domaine, puis cliquez sur **ajouter**.
5.  Cliquez sur **Suivant** pour ouvrir la page de dialogue **Vérifier votre nom de domaine**.

    ![Google\_Tutorial\_11][]

6.  Sélectionnez un **TYPE D'ENREGISTREMENT**, puis inscrivez l'enregistrement sélectionné dans votre fichier de zone DNS.

    ![Google\_Tutorial\_12][]

7.  Utilisez la commande **nslookup** pour vérifier si l'inscription de l'enregistrement DNS est correctement effectuée.

    ![Google\_Tutorial\_13][]

## Pour ajouter un domaine personnalisé dans Google Apps, procédez comme suit :

1.  Connectez-vous à votre client Google Apps.
2.  Dans la **Console d'administration**, cliquez sur **Domaines**.

    ![Google\_Tutorial\_14][]

3.  Cliquez sur **Ajouter un domaine personnalisé**.

    ![Google\_Tutorial\_15][]

4.  Cliquez sur **Utiliser un domaine que vous possédez déjà**, puis cliquez sur **Continuer**.

    ![Google\_Tutorial\_16][]

5.  Entrez le nom de votre domaine personnalisé, puis cliquez sur **Continuer**.

    ![Google\_Tutorial\_17][]

6.  Procédez comme suit pour vérifier la propriété du domaine.

    Si vous avez déjà configuré l'authentification unique fédérée, vous devez mettre à jour l'URL du client Google Apps dans la configuration de votre authentification unique fédérée.

# Configuration de l'approvisionnement des utilisateurs

Cette section décrit comment activer l'approvisionnement des comptes d'utilisateurs Active Directory sur Google Apps.

## Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :

1.  Dans le portail de gestion Azure, sélectionnez **Active Directory** dans le volet de navigation gauche pour ouvrir la page de dialogue d'**Active directory**.
2.  Dans la liste des annuaires, sélectionnez votre annuaire pour ouvrir sa page de configuration.
3.  Sélectionnez **APPLICATIONS** dans le menu du haut.
    Dans la liste des applications, sélectionnez **Google Apps** pour ouvrir la boîte de dialogue de configuration de **Google Apps**.
4.  Cliquez sur **Configurer la synchronisation de compte** pour ouvrir la boîte de dialogue du même nom.
5.  Sur la page de dialogue **CONFIGURER LA SYNCHRONISATION DE COMPTE**, entrez le nom de domaine, le nom d'utilisateur et le mot de passe Google Apps, puis cliquez sur le bouton **Suivant**.

    ![Google\_Tutorial\_18][]

6.  Sur la page de dialogue **Confirmation**, cliquez sur le bouton **Terminer** pour fermer la page de dialogue **﻿CONFIGURER LA SYNCHRONISATION DE COMPTE**.

Vous pouvez maintenant créer un compte de test, attendre 10 minutes, puis vérifier la synchronisation du compte à Google Apps.

Consultez également la page
[Meilleures pratiques de gestion des améliorations d'accès à l'application pour l'accès à l'application Azure Active Directory][]

  [Google\_Tutorial\_01]: ./media/integration-azure-google-apps/Google_Tutorial_01.png
  [Google\_Tutorial\_02]: ./media/integration-azure-google-apps/Google_Tutorial_02.png
  [Google\_Tutorial\_03]: ./media/integration-azure-google-apps/Google_Tutorial_03.png
  [Google\_Tutorial\_04]: ./media/integration-azure-google-apps/Google_Tutorial_04.png
  [Google\_Tutorial\_05]: ./media/integration-azure-google-apps/Google_Tutorial_05.png
  [Google\_Tutorial\_06]: ./media/integration-azure-google-apps/Google_Tutorial_06.png
  [Google\_Tutorial\_07]: ./media/integration-azure-google-apps/Google_Tutorial_07.png
  [Google\_Tutorial\_08]: ./media/integration-azure-google-apps/Google_Tutorial_08.png
  [Volet d'accès]: https://login.microsoftonline.com/login.srf?wa=wsignin1.0&rpsnv=2&ct=1384289458&rver=6.1.6206.0&wp=MCMBI&wreply=https:%2F%2Faccount.activedirectory.windowsazure.com%2Flanding.aspx%3Ftarget%3D%252fapplications%252fdefault.aspx&lc=1033&id=500633
  [Google\_Tutorial\_09]: ./media/integration-azure-google-apps/Google_Tutorial_09.png
  [Google\_Tutorial\_10]: ./media/integration-azure-google-apps/Google_Tutorial_10.png
  [Google\_Tutorial\_11]: ./media/integration-azure-google-apps/Google_Tutorial_11.png
  [Google\_Tutorial\_12]: ./media/integration-azure-google-apps/Google_Tutorial_12.png
  [Google\_Tutorial\_13]: ./media/integration-azure-google-apps/Google_Tutorial_13.png
  [Google\_Tutorial\_14]: ./media/integration-azure-google-apps/Google_Tutorial_14.png
  [Google\_Tutorial\_15]: ./media/integration-azure-google-apps/Google_Tutorial_15.png
  [Google\_Tutorial\_16]: ./media/integration-azure-google-apps/Google_Tutorial_16.png
  [Google\_Tutorial\_17]: ./media/integration-azure-google-apps/Google_Tutorial_17.png
  [Google\_Tutorial\_18]: ./media/integration-azure-google-apps/Google_Tutorial_18.png
  [Meilleures pratiques de gestion des améliorations d'accès à l'application pour l'accès à l'application Azure Active Directory]: http://social.technet.microsoft.com/wiki/contents/articles/18409.best-practices-for-managing-the-application-access-enhancements-for-windows-azure-active-directory.aspx

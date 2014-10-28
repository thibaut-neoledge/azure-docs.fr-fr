<properties linkid="develop-php-how-to-guides-web-sso" urlDisplayName="Web SSO" pageTitle="Single sign-on with Azure Active Directory (PHP)" metaKeywords="Azure PHP web app, Azure single sign-on, Azure PHP Active Directory" description="Learn how to create a PHP web application that uses single sign-on with Azure Active Directory." metaCanonical="" services="active-directory" documentationCenter="PHP" title="Web Single Sign-On with PHP and Azure Active Directory" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Authentification unique web avec PHP et Azure Active Directory

## <a name="introduction"></a>Introduction

Ce didacticiel va expliquer aux développeurs PHP comment tirer parti d'Azure Active Directory pour activer l'authentification unique pour les utilisateurs des clients Office 365. Vous apprendrez à :

-   configurer l'application web dans le client d'une société ;
-   protéger l'application avec WS-Federation.

### Conditions préalables

Les éléments de développement suivants sont requis pour cette procédure pas à pas :

-   [Exemple de code PHP pour Azure Active Directory][Exemple de code PHP pour Azure Active Directory]
-   [Eclipse PDT 3.0.x All In Ones][Eclipse PDT 3.0.x All In Ones]
-   PHP 5.3.1 (via Web Platform Installer)
-   IIS (Internet Information Services) 7.5 avec SSL activé
-   Windows PowerShell
-   [Cmdlets PowerShell pour Office 365][Cmdlets PowerShell pour Office 365]

### Sommaire

-   [Introduction][Introduction]
-   [Étape 1 : création d'une application PHP][Étape 1 : création d'une application PHP]
-   [Étape 2 : configuration de l'application dans le client d'annuaire d'une société][Étape 2 : configuration de l'application dans le client d'annuaire d'une société]
-   [Étape 3 : protection de l'application en utilisant WS-Federation pour la connexion des employés][Étape 3 : protection de l'application en utilisant WS-Federation pour la connexion des employés]
-   [Résumé][Résumé]

## <a name="createapp"></a>Étape 1 : création d'une application PHP

Cette étape décrit la création d'une application PHP simple qui représentera une source protégée. L'accès à cette ressource sera accordé via une authentification fédérée gérée par le service d'émission de jeton de sécurité (STS) d'une société, qui est décrit ultérieurement dans ce didacticiel.

1.  Ouvrez une nouvelle instance d'Eclipse.
2.  Dans le menu **File**, cliquez sur **New**, puis sur **New PHP Project**.
3.  Dans la boîte de dialogue **New PHP Project**, nommez le projet *phpSample*, puis cliquez sur **Finish**.
4.  Dans le menu **PHP Explorer** de gauche, cliquez avec le bouton droit sur *phpProject*, cliquez sur **New**, puis sur **PHP File**.
5.  Dans la boîte de dialogue **New PHP File**, nommez le projet **index.php**, puis cliquez sur **Finish**.
6.  Remplacez le balisage généré par le code suivant, puis enregistrez le fichier **index.php** :

        <!DOCTYPE>
        <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
            <title>Index</title>
        </head>
        <body>
            ##Index Page
        </body>
        </html> 

7.  Dans l'invite de commandes, tapez *inetmgr*, puis appuyez sur Entrée pour ouvrir le **Gestionnaire des services Internet (IIS)**.

8.  Dans le Gestionnaire des services Internet, développez le dossier **Sites** dans le menu de gauche, cliquez avec le bouton droit sur **Site web par défaut**, puis cliquez sur **Ajouter une application...**.

9.  Dans la boîte de dialogue **Ajouter une application**, définissez la valeur **Alias** sur *phpSample* et le **Chemin physique** sur le chemin du fichier où vous avez créé le projet PHP.

10. Dans Eclipse, dans le menu **Run**, cliquez sur **Run**.

11. Dans le menu **Run PHP Web Application**, cliquez sur **OK**.

12. La page **index.php** s'ouvre sous un nouvel onglet dans Eclipse. Cette page affiche simplement le texte : *Page d'index*.

## <a name="provisionapp"></a>Étape 2 : configuration de l'application dans le client d'annuaire d'une société

Cette étape explique comment l'administrateur d'une société utilisant Azure Active Directory met en service l'application PHP dans son client et configure l'authentification unique. Une fois cette étape terminée, les employés de la société peuvent s'authentifier pour accéder à l'application web en utilisant leurs comptes Office 365.

Le processus de configuration commence par la création d'un principal du service pour l'application. Azure Active Directory utilise les principaux du service pour enregistrer et authentifier des applications dans l'annuaire.

1.  Si ce n'est pas déjà fait, téléchargez et installez les cmdlets PowerShell pour Office 365.
2.  Dans le menu **Démarrer**, exécutez la console **Module Microsoft Online Services pour Windows PowerShell**. Celle-ci fournit un environnement en ligne de commande permettant de configurer des attributs de votre client Office 365, comme la création et la modification des principaux du service.
3.  Pour importer le module **MSOnlineExtended** requis, entrez la commande suivante, puis appuyez sur Entrée :

        Import-Module MSOnlineExtended -Force

4.  Pour vous connecter à votre annuaire Office 365, vous devez fournir les informations d'identification de l'administrateur de la société. Tapez la commande suivante et appuyez sur Entrée, puis entrez vos informations d'identification lorsque vous y êtes invité :

        Connect-MsolService

5.  ﻿À présent, vous allez créer un principal du service pour l'application. Tapez la commande suivante, puis appuyez sur Entrée :

        New-MsolServicePrincipal -ServicePrincipalNames @("phpSample/localhost") -DisplayName "Federation Sample Website" -Type Symmetric -Usage Verify -StartDate "12/01/2012" -EndDate "12/01/2013" 

    Cette étape affiche des informations similaires aux suivantes :

        The following symmetric key was created as one was not supplied qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=
        DisplayName           : Federation Sample PHP Website
        ServicePrincipalNames : {phpSample/localhost}
        ObjectId              : 59cab09a-3f5d-4e86-999c-2e69f682d90d
        AppPrincipalId        : 7829c758-2bef-43df-a685-717089474505
        TrustedForDelegation  : False
        AccountEnabled        : True
        KeyType               : Symmetric
        KeyId                 : f1735cbe-aa46-421b-8a1c-03b8f9bb3565
        StartDate             : 12/01/2012 08:00:00 a.m.
        EndDate               : 12/01/2013 08:00:00 a.m.
        Usage                 : Verify 

    > [WACOM.NOTE]
    > Enregistrez ce résultat, surtout la clé symétrique générée. Cette clé est uniquement affichée pendant la création du principal du service et vous ne pourrez pas la récupérer après cela. Les autres valeurs sont requises pour l'utilisation de l'API Graph pour lire et écrire des informations dans l'annuaire.

6.  Dans la dernière étape, vous allez définir l'URL de réponse de votre application. Celle-ci reçoit les réponses après les tentatives d'authentification. Tapez les commandes suivantes, puis appuyez sur Entrée :

        $replyUrl = New-MsolServicePrincipalAddresses –Address "https://localhost/phpSample" 

        Set-MsolServicePrincipal –AppPrincipalId "7829c758-2bef-43df-a685-717089474505" –Addresses $replyUrl 

L'application web est à présent configurée dans l'annuaire et peut être utilisée pour l'authentification unique web par les employés de la société.

## <a name="protectapp"></a>Étape 3 : protection de l'application en utilisant WS-Federation pour la connexion des employés

Dans cette étape, vous allez ajouter la prise en charge de la connexion fédérée en utilisant Windows Identity Foundation (WIF) et les bibliothèques simpleSAML.php que vous avez téléchargées avec l'exemple de code lorsque vous avez passé en revue la configuration requise. Vous allez également ajouter une page de connexion et configurer l'approbation entre l'application et le client d'annuaire.

1.  Dans Eclipse, cliquez avec le bouton droit sur le projet **phpSample**, cliquez sur **New**, puis sur **File**.

2.  Dans la boîte de dialogue **New File**, nommez le fichier **federation.ini**, puis cliquez sur **Finish**.

3.  Dans le nouveau fichier **federation.ini**, entrez les informations suivantes, en définissant les valeurs à l'aide des informations que vous avez enregistrées lors de l'étape 2, lorsque vous avez créé le principal du service :

        federation.trustedissuers.issuer=https://accounts.accesscontrol.windows.net/v2/wsfederation
        federation.trustedissuers.thumbprint=qY+Drf20Zz+A4t2we3PebCopoCugO76My+JMVsqNBFc=
        federation.trustedissuers.friendlyname=Fabrikam
        federation.audienceuris=spn:7829c758-2bef-43df-a685-717089474505
        federation.realm=spn:7829c758-2bef-43df-a685-717089474505
        federation.reply=https://localhost/phpSample/index.php 

        <div class="dev-callout"><strong>Note</strong><p>The <b>audienceuris</b> and <b>realm</b> values must be prefaced by "spn:".</p></div>

4.  Dans Eclipse, cliquez avec le bouton droit sur le projet **phpSample**, cliquez sur **New**, puis sur **PHP File**.

5.  Dans la boîte de dialogue **New PHP File**, nommez le projet **secureResource.php**, puis cliquez sur **Finish**.

6.  Dans le nouveau fichier **secureResource.php**, entrez le code suivant, en remplaçant le chemin d'accès **c:\\phpLibraries** par celui de l'emplacement racine dans lequel vous avez téléchargé l'exemple de code. L'emplacement racine doit inclure le fichier **simpleSAML.php** et le dossier **federation** :

        <?php
        ini_set('include_path', ini_get('include_path').';c:\phpLibraries\;');
        require_once ('federation/FederatedLoginManager.php');
        session_start();
        $token = $_POST['wresult'];
        $loginManager = new FederatedLoginManager();
        if (!$loginManager->isAuthenticated()) {
            if (isset ($token)) {
                try {
                    $loginManager->authenticate($token);
                } catch (Exception $e) {
                    print_r($e->getMessage());
                }  
            } else {
                $returnUrl = "https://" . $_SERVER['HTTP_HOST'] . $_SERVER['PHP_SELF'];
                header('Pragma: no-cache');
                header('Cache-Control: no-cache, must-revalidate');
                header("Location: " . FederatedLoginManager :: getFederatedLoginUrl($returnUrl), true, 302);
                exit();
            }
        }
        ?> 

7.  Ouvrez la page **index.php** et mettez à jour son contenu pour la sécuriser, puis enregistrez-la :

        <?php
        require_once (dirname(__FILE__) . '/secureResource.php');
        ?>
        <!DOCTYPE html>
        <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
            <title>Index Page</title>
        </head>
        <body>
            <h2>Index Page</h2>
            <h3>Welcome <strong><?php print_r($loginManager->getPrincipal()->getName()); ?></strong>!</h3>
            <h4>Claim list:</h4>
            <ul>
                <?php
                foreach ($loginManager->getClaims() as $claim) {
                    print_r('<li>' . $claim->toString() . '</li>');
                }
                ?>  
            </ul>
        </body>
        </html> 

8.  Dans le menu **Run**, cliquez sur **Run**. Vous êtes automatiquement redirigé vers la page du fournisseur d'identité Office 365, où vous pouvez vous connecter en utilisant vos informations d'identification de client d'annuaire. Par exemple, *thomas.levesque@fabrikam.onmicrosoft.com*.

## <a name="summary"></a>Résumé

Ce didacticiel vous a montré comment créer et configurer une unique application PHP cliente utilisant les capacités d'authentification unique d'Azure Active Directory.

Un exemple illustrant l'utilisation d'Azure Active Directory et de l'authentification unique pour des sites web PHP est disponible sur la page <https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/WAAD.WebSSO.PHP>.

  [Exemple de code PHP pour Azure Active Directory]: https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/WAAD.WebSSO.PHP
  [Eclipse PDT 3.0.x All In Ones]: http://www.eclipse.org/pdt/downloads/
  [Cmdlets PowerShell pour Office 365]: http://onlinehelp.microsoft.com/fr-fr/office365-enterprises/ff652560.aspx
  [Introduction]: #introduction
  [Étape 1 : création d'une application PHP]: #createapp
  [Étape 2 : configuration de l'application dans le client d'annuaire d'une société]: #provisionapp
  [Étape 3 : protection de l'application en utilisant WS-Federation pour la connexion des employés]: #protectapp
  [Résumé]: #summary
  [\*thomas.levesque@fabrikam.onmicrosoft.com\*]: mailto:*john.doe@fabrikam.onmicrosoft.com*

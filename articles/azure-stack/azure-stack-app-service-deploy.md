---
title: "Déployer App Services : Azure Stack | Microsoft Docs"
description: "Instructions détaillées pour le déploiement d’App Service dans Azure Stack"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/3/2017
ms.author: anwestg
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: 4b4f978f008dbcd8a7424f285198535cf133d7e2
ms.contentlocale: fr-fr
ms.lasthandoff: 09/15/2017

---
# <a name="add-an-app-service-resource-provider-to-azure-stack"></a>Ajouter un fournisseur de ressources App Service à Azure Stack

Si vous souhaitez permettre à vos locataires de créer des applications web, mobiles et d’API avec leur abonnement Azure Stack, vous devez ajouter un [fournisseur de ressources Azure App Service](azure-stack-app-service-overview.md) à votre déploiement Azure Stack. Suivez les étapes décrites dans cet article.

## <a name="download-the-required-components"></a>Télécharger les composants requis

1. Téléchargez le [programme d’installation de la préversion App Service sur Azure Stack](http://aka.ms/appsvconmasrc1installer).

2. Téléchargez les [scripts d’assistance au déploiement App Service sur Azure Stack](http://aka.ms/appsvconmasrc1helper).

3. Extrayez les fichiers à partir du fichier zip des scripts d’assistance. La structure de fichiers et de dossier suivante s’affiche :

   - Create-AppServiceCerts.ps1
   - Create-IdentityApp.ps1
   - Modules
      - AzureStack.Identity.psm1
      - GraphAPI.psm1
   
## <a name="create-certificates-required-by-app-service-on-azure-stack"></a>Créer les certificats requis par App Service sur Azure Stack

Ce premier script fonctionne avec l’autorité de certification Azure Stack pour créer trois certificats dont App Service a besoin. Exécutez le script sur l’hôte Azure Stack et assurez-vous que vous exécutez PowerShell en tant que azurestack\AzureStackAdmin.

1. Dans une session PowerShell exécutée en tant que azurestack\AzureStackAdmin, exécutez le script **AppServiceCerts.ps1-créer** à partir du dossier où vous avez extrait les scripts d’assistance. Le script crée trois certificats, dans le même dossier que le script des certificats de création, qui sont requis par App Service.

2. Entrez un mot de passe pour sécuriser les fichiers .pfx et prenez-en note. Vous devrez l’entrer dans le programme d’installation App Service sur Azure Stack.

### <a name="create-appservicecertsps1-parameters"></a>Paramètres Create-AppServiceCerts.ps1

| Paramètre | Obligatoire ou facultatif | Valeur par défaut | Description |
| --- | --- | --- | --- |
| pfxPassword | Requis | Null | Mot de passe utilisé pour protéger la clé privée du certificat |
| DomainName | Requis | local.azurestack.external | Suffixe de la région et du domaine Azure Stack |
| CertificateAuthority | Requis | AzS-CA01.azurestack.local | Point de terminaison de l’autorité de certification |

## <a name="use-the-installer-to-download-and-install-app-service-on-azure-stack"></a>Utiliser le programme d’installation pour télécharger et installer App Service sur Azure Stack

Le programme d’installation appservice.exe effectue les tâches suivantes :

* Vous invite à accepter les termes du contrat de licence logicielle Microsoft et tiers.
* Collecte les informations sur le déploiement Azure Stack.
* Crée un conteneur d’objets blob dans le compte de stockage Azure Stack spécifié.
* Télécharge les fichiers nécessaires pour installer le fournisseur de ressources App Service.
* Prépare l’installation pour déployer le fournisseur de ressources App Service dans l’environnement Azure Stack.
* Charger les fichiers vers le compte de stockage App Service.
* Effectue le déploiement du fournisseur de ressources App Service.
* Crée une zone DNS et les entrées pour App Service.
* Inscrit le fournisseur de ressources App Service.
* Inscrit les éléments de la galerie App Service.

Les étapes suivantes vous guident pour l’installation.

> [!NOTE]
> Vous *devez* utiliser un compte avec élévation de privilèges (administrateur local ou de domaine) pour exécuter le programme d’installation. Si vous êtes connecté en tant que azurestack\azurestackuser, vous êtes invité à entrer des informations d’identification avec élévation de privilèges.

1. Exécutez appservice.exe en tant que azurestack\AzureStackAdmin.

2. Cliquez sur **Déployer App Service application sur votre cloud Azure Stack**.

    ![Programme d’installation d’App Service sur Azure Stack][1]

3. Consultez et acceptez les termes du contrat de licence de la préversion du logiciel Microsoft, puis cliquez sur **Suivant**.

4. Consultez et acceptez les termes du contrat de licence tiers, puis cliquez sur **Suivant**.

5. Passez en revue les informations de configuration cloud App Service, puis cliquez sur **Suivant**.

    ![Configuration cloud App Service sur Azure Stack][2]

    > [!NOTE]
    > Le programme d’installation App Service sur Azure Stack fournit les valeurs par défaut pour une installation Azure Stack à un nœud. Si vous avez personnalisé des options lors du déploiement d’Azure Stack (par exemple, le suffixe du domaine), vous devez modifier en conséquence les valeurs dans cette fenêtre. Par exemple, si vous utilisez le suffixe de domaine mycloud.com, vous devez modifier votre point de terminaison Azure Resource Manager administrateur sur adminmanagement.[region].mycloud.com.

6. Cliquez sur le bouton **Se connecter** situé en regard de la zone **Abonnements Azure Stack**.

   - Si vous utilisez Azure Active Directory (Azure AD), vous devez entrer votre compte et mot de passe d’administrateur Azure AD. Cliquez sur **Se connecter**. Vous *devez* entrer le compte Azure AD que vous avez fourni lorsque vous avez déployé Azure Stack.
   - Si vous utilisez AD FS, vous devez fournir votre compte d’administrateur, par exemple, azurestackadmin@azurestack.local. Entrez votre mot de passe, puis cliquez sur **Se connecter**.

7. Sélectionnez votre abonnement dans la zone **Abonnements Azure Stack**.

8. Dans la zone **Emplacements Azure Stack**, sélectionnez l’emplacement qui correspond à la région où vous effectuez le déploiement. Par exemple, sélectionnez **local**. Cliquez sur **Suivant**.

    ![Sélection d’abonnement App Service sur Azure Stack][3]

9. Entrez le **Nom du groupe de ressources** pour votre déploiement App Service. Par défaut, il est défini sur **APPSERVICE-LOCAL**.

10. Entrez le **Nom du compte de stockage** qu’App Service doit créer dans le cadre de l’installation. Par défaut, il est défini sur **appsvclocalstor**.

11. Entrez les détails de SQL Server pour l’instance utilisée pour héberger les bases de données du fournisseur de ressources App Service. Cliquez sur **Suivant** et le programme d’installation valide les propriétés de connexion SQL.

    ![Informations sur le groupe de ressources, le stockage et SQL Server pour App Service sur Azure Stack][4]

12. Cliquez sur le bouton **Parcourir** situé en regard de la zone **Fichier du certificat SSL App Service par défaut**. Accédez au certificat **_.appservice.local.AzureStack.external** [créé précédemment](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps). Si vous avez spécifié un emplacement et un suffixe de domaine différents lorsque vous avez créé le certificat, sélectionnez le certificat correspondant.

13. Entrez le mot de passe du certificat que vous avez défini lorsque vous avez créé le certificat.

14. Cliquez sur le bouton **Parcourir** situé en regard de la zone **Fichier du certificat SSL du fournisseur de ressources**. Accédez au certificat **api.appservice.local.AzureStack.external** [créé précédemment](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps). Si vous avez spécifié un emplacement et un suffixe de domaine différents lors de la création des certificats, sélectionnez le certificat correspondant.

15. Entrez le mot de passe du certificat que vous avez défini lorsque vous avez créé le certificat.

16. Cliquez sur le bouton **Parcourir** situé en regard de la zone **Fichier du certificat racine du fournisseur de ressources**. Accédez au certificat **AzureStackCertificationAuthority** [créé précédemment](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps).

17. Cliquez sur **Suivant**. Le programme d’installation vérifie le mot de passe du certificat fourni.

    ![Détails du certificat App Service sur Azure Stack][5]

18. Vérifiez la configuration du rôle App Service. Les valeurs par défaut sont renseignées avec les références d’instance minimale recommandée pour chaque rôle. Un résumé des exigences de base et de mémoire est fourni pour vous aider à planifier votre déploiement. Une fois vos sélections effectuées, cliquez sur **Suivant**.

    - **Contrôleur** : par défaut, une instance A1 Standard est sélectionnée. Ceci est le minimum recommandé. Le rôle Contrôleur est responsable de la gestion et de la maintenance de l’intégrité du cloud App Service.
    - **Gestion** : par défaut, une instance A2 Standard est sélectionnée. Pour le basculement, nous recommandons d’utiliser deux instances. Le rôle Gestion est responsable des points de terminaison App Service Azure Resource Manager et d’API, des extensions du portail (admin, locataire, portail Functions) et du service des données.
    - **Serveur de publication** : par défaut, une instance A1 Standard est sélectionnée. Ceci est le minimum recommandé. Le rôle Serveur de publication est responsable de la publication du contenu via FTP et déploiement web.
    - **Frontal** : par défaut, une instance A1 Standard est sélectionnée. Ceci est le minimum recommandé. Le rôle Frontal est responsable de l’acheminement des demandes aux applications App Service.
    - **Worker partagé**: par défaut, une instance A1 Standard est sélectionnée, mais vous pouvez en ajouter d’autres. En tant qu’administrateur, vous pouvez définir votre offre et choisir n’importe quel niveau de référence. Les niveaux doivent avoir au minimum un cœur. Le rôle Worker partagé est chargé d’héberger les applications web, mobiles ou d’API et les applications Azure Functions.

    ![Configuration du rôle App Service sur Azure Stack][6]

    > [!NOTE]
    > Dans les préversions techniques, le programme d’installation du fournisseur de ressources App Service déploie également une instance A1 Standard pour fonctionner en tant que serveur de fichiers simple pour prendre en charge Azure Resource Manager. Cette configuration est conservée pour le kit de développement à un seul nœud. Pour les charges de production, lors de la disponibilité générale, le programme d’installation App Service permet d’utiliser un serveur de fichiers à haute disponibilité.

19. Choisissez votre image de machine virtuelle **Windows Server 2016** de déploiement parmi celles disponibles dans le fournisseur de ressources de calcul pour le cloud App Service. Cliquez sur **Suivant**.

    ![Sélection d’image de machine virtuelle App Service sur Azure Stack][7]

20. Entrez un nom d’utilisateur et un mot de passe pour les rôles de travail configurés dans le cloud App Service. Entrez un nom d’utilisateur et un mot de passe pour tous les autres rôles App Service. Cliquez sur **Suivant**.

    ![Saisie des informations d’identification App Service sur Azure Stack][8]

21. Dans l’écran de synthèse, vérifiez les sélections effectuées. Pour apporter des modifications, passez en revue les écrans et modifiez vos sélections. Si la configuration est conforme à ce que vous souhaitez, cochez la case. Cliquez sur **Suivant** pour commencer le déploiement.

    ![Synthèse des sélections App Service sur Azure Stack][9]

22. Suivez la progression de l’installation. Le déploiement d’App Service sur Azure Stack prend environ 45 à 60 minutes avec les sélections par défaut.

    ![Progression de l’installation App Service sur Azure Stack][10]

23. Une fois le programme d’installation terminé avec succès, cliquez sur **Quitter**.

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Valider l’installation App Service sur Azure Stack

1. Dans le portail d’administration Azure Stack, accédez au groupe de ressources créé par le programme d’installation. Par défaut, ce groupe est **APPSERVICE-LOCAL**.

2. Recherchez **CN0-VM**. Pour vous connecter à la machine virtuelle, cliquez sur **Se connecter** dans le panneau **Machine virtuelle**.

3. Sur le bureau de cette machine virtuelle, cliquez deux fois sur **Console de gestion cloud web**.

4. Accédez à **Serveurs gérés**.

5. Lorsque toutes les machines affichent l’état **Prêt** pour un ou plusieurs Workers, passez à l’étape 6.

6. Fermez l’ordinateur Bureau à distance et revenez à l’ordinateur où vous avez exécuté le programme d’installation App Service.

    > [!NOTE]
    > Vous n’avez pas besoin d’attendre qu’un ou plusieurs Workers affichent l’état **Prêt** pour terminer l’installation d’App Service sur Azure Stack. Toutefois, vous devez avoir au minimum un Worker prêt à déployer une application web, mobile ou d’API, ou Azure Functions.

    ![État des serveurs gérés App Service sur Azure Stack][11]

## <a name="configure-an-azure-ad-service-principal-for-virtual-machine-scale-set-integration-on-worker-tiers-and-sso-for-the-azure-functions-portal-and-advanced-developer-tools"></a>Configurer un principal du service Azure AD pour l’intégration d’un groupe de machines virtuelles identiques sur les niveaux Worker et l’authentification unique pour le portail Azure Functions et les outils de développement avancés

>[!NOTE]
> Ces étapes s’appliquent uniquement aux environnements Azure Stack sécurisés avec Azure AD.

Les administrateurs doivent configurer l’authentification unique pour :

* Activer les outils de développement avancés dans App Service (Kudu).
* Activer l’utilisation de l’expérience du portail Azure Functions.

Procédez comme suit :

1. Ouvrez une instance PowerShell en tant que azurestack\azurestackadmin.

2. Accédez à l’emplacement où les scripts ont été téléchargés et extraits dans [l’étape des prérequis](#Download-Required-Components).

3. [Installez](azure-stack-powershell-install.md) et [configurez un environnement PowerShell Azure Stack](azure-stack-powershell-configure-admin.md).

4. Dans la même session PowerShell, exécutez le script **CreateIdentityApp.ps1**. Lorsque vous êtes invité à entrer votre ID de locataire Azure AD, entrez l’ID de locataire Azure AD que vous utilisez pour votre déploiement Azure Stack, par exemple, myazurestack.onmicrosoft.com.

5. Dans la fenêtre **Informations d’identification**, entrez votre compte administrateur et votre mot de passe pour le service Azure AD. Cliquez sur **OK**.

6. Entrez le chemin d’accès au fichier du certificat et le mot de passe du certificat pour le [certificat créé précédemment](# Create certificates to be used by App Service on Azure Stack). Le certificat par défaut créé pour cette étape est sso.appservice.local.azurestack.external.pfx.

7. Le script crée une nouvelle application dans Azure AD locataire et génère un script PowerShell nommé **UpdateConfigOnController.ps1**.

    >[!NOTE]
    > Notez **l’ID d’application** qui est retourné dans la sortie PowerShell. Vous aurez besoin de cette information pour la recherche à l’étape 12.

8. Copiez le fichier de certificat de l’application d’identité et le script généré vers **CN0-VM** à l’aide d’une session Bureau à distance.

9. Ouvrez une nouvelle fenêtre de navigateur et connectez-vous au portail Azure (portal.azure.com) en tant que **Administrateur du service Azure Active Directory**.

10. Ouvrez le fournisseur de ressources Azure AD.

11. Cliquez sur **Inscriptions des applications**.

12. Recherchez **l’ID d’application** retourné à l’étape 7. Une application App Service est répertoriée.

13. Cliquez sur **Application** dans la liste, puis ouvrez le panneau **Clés**.

14. Ajoutez une nouvelle clé avec **Description - Portail Functions** et définissez la **Date d’expiration** sur **N’expire jamais**.

15. Cliquez sur **Enregistrer**, puis copiez la clé qui a été générée.

    >[!NOTE]
    > Veillez à noter ou à copier la clé lorsqu’elle est générée. Une fois qu’elle est enregistrée, il n’est plus possible de l’afficher à nouveau et vous devez regénérer une nouvelle clé.

    ![Clés d’application App Service sur Azure Stack][12]

16. Revenez à **Inscription des applications** dans Azure AD.

17. Cliquez sur **Autorisations requises** > **Accorder des autorisations** > **Oui**.

    ![Octroi de l’authentification unique App Service sur Azure Stack][13]

18. Revenez à **CN0-VM**, puis rouvrez la **Console de gestion cloud web**.

19. Sélectionnez le nœud **Paramètres** dans le volet gauche et recherchez le paramètre **ApplicationClientSecret**.

20. Cliquez dessus avec le bouton droit et sélectionnez **Modifier**. Collez la clé générée à l’étape 15, puis cliquez sur **OK**.

    ![Clés d’application App Service sur Azure Stack][14]

21. Ouvrez une fenêtre PowerShell administrateur. Accédez au répertoire où le fichier de script et le certificat ont été copiés à l’étape 7.

22. Exécutez le fichier de script. Ce fichier de script entre les propriétés dans la configuration App Service sur Azure Stack et initie une opération de réparation sur tous les rôles Serveur frontal et Gestion.

| Paramètre | Obligatoire ou facultatif | Valeur par défaut | Description |
| --- | --- | --- | --- |
| DirectoryTenantName | Obligatoire | Null | ID de locataire Azure AD. Fournir le GUID ou une chaîne, par exemple, myazureaaddirectory.onmicrosoft.com |
| TenantAzure Resource ManagerEndpoint | Obligatoire | management.local.azurestack.external | Point de terminaison Azure Resource Manager de locataire |
| AzureStackCredential | Obligatoire | Null | Administrateur Azure AD |
| CertificateFilePath | Obligatoire | Null | Chemin d’accès au fichier de certificat d’application d’identité généré précédemment |
| CertificatePassword | Obligatoire | Null | Mot de passe utilisé pour protéger la clé privée du certificat |
| DomainName | Requis | local.azurestack.external | Suffixe de la région et du domaine Azure Stack |
| AdfsMachineName | Facultatif | Ignorer dans le cas d’un déploiement Azure AD, mais obligatoire dans le déploiement AD FS. Nom de l’ordinateur AD FS, par exemple, AzS-ADFS01.azurestack.local |

## <a name="configure-an-ad-fs-service-principal-for-virtual-machine-scale-set-integration-on-worker-tiers-and-sso-for-the-azure-functions-portal-and-advanced-developer-tools"></a>Configurer un principal du service AD FS pour l’intégration d’un groupe de machines virtuelles identiques sur les niveaux Worker et l’authentification unique pour le portail Azure Functions et les outils de développement avancés

>[!NOTE]
> Ces étapes s’appliquent uniquement aux environnements Azure Stack sécurisés avec AD FS.

Les administrateurs doivent configurer l’authentification unique pour :

* Configurer un principal du service pour l’intégration d’un groupe de machines virtuelles identiques sur les niveaux Worker.
* Activer les outils de développement avancés dans App Service (Kudu).
* Activer l’utilisation de l’expérience du portail Azure Functions. 

Procédez comme suit :

1. Ouvrez une instance PowerShell en tant que azurestack\azurestackadmin.

2. Accédez à l’emplacement où les scripts ont été téléchargés et extraits dans [l’étape des prérequis](#Download-Required-Components).

3. [Installez](azure-stack-powershell-install.md) et [configurez un environnement PowerShell Azure Stack](azure-stack-powershell-configure-admin.md).

4. Dans la même session PowerShell, exécutez le script **CreateIdentityApp.ps1**. Lorsque vous êtes invité à entrer votre ID de locataire Azure AD, entrez **ADFS**.

5. Dans la fenêtre **Informations d’identification**, entrez votre compte administrateur et votre mot de passe pour le service AD FS. Cliquez sur **OK**.

6. Entrez le chemin d’accès au fichier du certificat et le mot de passe du certificat pour le [certificat créé précédemment](# Create certificates to be used by App Service on Azure Stack). Le certificat par défaut créé pour cette étape est sso.appservice.local.azurestack.external.pfx.

7. Le script crée une nouvelle application dans Azure AD locataire et génère un script PowerShell nommé **UpdateConfigOnController.ps1**.

8. Copiez le fichier de certificat de l’application d’identité et le script généré vers **CN0-VM** à l’aide d’une session Bureau à distance.

9. Revenez à **CN0-VM**.

10. Ouvrez une fenêtre PowerShell administrateur et accédez au répertoire où le fichier de script et le certificat ont été copiés à l’étape 7.

11. Exécutez le fichier de script. Ce fichier de script entre les propriétés dans la configuration App Service sur Azure Stack et initie une opération de réparation sur tous les rôles Serveur frontal et Gestion.

| Paramètre | Obligatoire ou facultatif | Valeur par défaut | Description |
| --- | --- | --- | --- |
| DirectoryTenantName | Obligatoire | Null | Utiliser **ADFS** pour l’environnement AD FS |
| TenantAzure Resource ManagerEndpoint | Obligatoire | management.local.azurestack.external | Point de terminaison Azure Resource Manager de locataire |
| AzureStackCredential | Obligatoire | Null | Compte d’administrateur du service AD FS |
| CertificateFilePath | Obligatoire | Null | Chemin d’accès au fichier de certificat d’application d’identité généré précédemment |
| CertificatePassword | Obligatoire | Null | Mot de passe utilisé pour protéger la clé privée du certificat |
| DomainName | Requis | local.azurestack.external | Suffixe de la région et du domaine Azure Stack |
| AdfsMachineName | Facultatif | Nom de l’ordinateur AD FS, par exemple, AzS-ADFS01.azurestack.local |

## <a name="test-drive-app-service-on-azure-stack"></a>Tester App Service sur Azure Stack

Après avoir déployé et inscrit le fournisseur de ressources App Service, testez-le pour vous assurer que les locataires peuvent déployer des applications web, mobiles et d’API.

> [!NOTE]
> Vous devez créer une offre avec l’espace de noms Microsoft.Web dans le plan. Ensuite, vous devez avoir un abonnement locataire qui s’abonne à cette offre. Pour plus d’informations, consultez [Créer une offre](azure-stack-create-offer.md) et [Créer un plan](azure-stack-create-plan.md).
>
Vous *devez* disposer d’un abonnement locataire pour créer des applications qui utilisent App Service sur Azure Stack. Les seules fonctionnalités qu’un administrateur de service peut effectuer dans le portail d’administration sont liées à l’administration du fournisseur de ressources App Service. Ces fonctionnalités incluent l’ajout de capacité, la configuration de sources de déploiement, l’ajout de niveaux Worker et de références.
>
À compter de la troisième préversion technique, vous devez utiliser le portail du locataire et disposer d’un abonnement locataire pour créer des applications web, mobiles, d’API et Azure Functions. 

1. Dans le portail du locataire Azure Stack, cliquez sur **Nouveau** > **Web + Mobile** > **Application web**.

2. Dans le panneau **Application web**, tapez un nom dans la zone **Application web**.

3. Sous **Groupe de ressources**, cliquez sur **Nouveau**. Tapez un nom dans la zone **Groupe de ressources**.

4. Cliquez sur **Plan App Service/Emplacement** > **Créer**.

5. Dans le panneau **Plan App Service**, tapez un nom dans la zone **Plan App Service**.

6. Cliquez sur **Niveau tarifaire** > **Libre-Partagé** ou **Partagé-Partagé** > **Sélectionnez** > **OK** > **Créer**.

7. En moins d’une minute, une vignette pour la nouvelle application web s’affiche dans le tableau de bord. Cliquez sur la vignette.

8. Dans le panneau **Application web**, cliquez sur **Parcourir** pour afficher le site web par défaut pour cette application.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Déployer un site web WordPress, DNN ou Django (facultatif)

1. Dans le portail du locataire Azure Stack, cliquez sur **+**, accédez à la Place de marché Azure, déployez un site web Django et attendez que l’opération se termine. La plateforme web Django utilise une base de données basée sur système de fichiers. Elle ne nécessite aucun fournisseur de ressources supplémentaire comme SQL ou MySQL.

2. Si vous avez également déployé un fournisseur de ressources MySQL, vous pouvez déployer un site web WordPress à partir de la Place de marché. Lorsque vous êtes invité à entrer les paramètres de la base de données, entrez le nom d’utilisateur *User1@Server1*, avec le nom d’utilisateur et le nom de serveur de votre choix.

3. Si vous avez également déployé un fournisseur de ressources SQL Server, vous pouvez déployer un site web DNN à partir de la Place de marché. Lorsque vous êtes invité à entrer les paramètres de la base de données, sélectionnez une base de données sur l’ordinateur exécutant SQL Server connecté à votre fournisseur de ressources.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également tester d’autres [services PaaS](azure-stack-tools-paas-services.md).

- [Fournisseur de ressources SQL Server](azure-stack-sql-resource-provider-deploy.md)
- [Fournisseur de ressources MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy/app-service-exe-start.png
[2]: ./media/azure-stack-app-service-deploy/app-service-exe-default-entries-step-cloud-configuration.png
[3]: ./media/azure-stack-app-service-deploy/app-service-exe-default-entries-step-subscription-location-populated.png
[4]: ./media/azure-stack-app-service-deploy/app-service-exe-default-entries-step-resource-group-sql.png
[5]: ./media/azure-stack-app-service-deploy/app-service-exe-default-entries-step-certificates.png
[6]: ./media/azure-stack-app-service-deploy/app-service-exe-default-entries-step-role-configuration.png
[7]: ./media/azure-stack-app-service-deploy/app-service-exe-default-entries-step-vm-image-selection.png
[8]: ./media/azure-stack-app-service-deploy/app-service-exe-default-entries-step-role-credentials.png
[9]: ./media/azure-stack-app-service-deploy/app-service-exe-selection-summary.png
[10]: ./media/azure-stack-app-service-deploy/app-service-exe-installation-progress.png
[11]: ./media/azure-stack-app-service-deploy/managed-servers.png
[12]: ./media/azure-stack-app-service-deploy/app-service-sso-keys.png
[13]: ./media/azure-stack-app-service-deploy/app-service-sso-grant.png
[14]: ./media/azure-stack-app-service-deploy/app-service-application-secret.png

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525


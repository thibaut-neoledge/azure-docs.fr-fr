---
title: "Déployer App Service dans un environnement hors connexion : Azure Stack | Microsoft Docs"
description: "Instructions détaillées sur le déploiement d’App Service dans un environnement Azure Stack déconnecté sécurisé par AD FS."
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
ms.openlocfilehash: b733851d4459ead1ae437604a27ca7720e2a3a87
ms.contentlocale: fr-fr
ms.lasthandoff: 09/15/2017

---
# <a name="add-an-app-service-resource-provider-to-a-disconnected-azure-stack-environment-secured-by-ad-fs"></a>Ajouter un fournisseur de ressources App Service à un environnement Azure Stack déconnecté sécurisé par AD FS

Vous devez ajouter un [fournisseur de ressources Azure App Service](azure-stack-app-service-overview.md) à votre déploiement Azure Stack :

* Si vous exécutez Azure Stack dans un environnement isolé sécurisé par AD FS. 
* Si vous souhaitez donner à vos locataires la possibilité de créer des applications web, mobiles et d’API, et des applications Azure Functions, avec leur abonnement Azure Stack. 

Pour ce faire, suivez les étapes décrites dans cet article.

## <a name="download-the-required-components"></a>Télécharger les composants requis

1. Téléchargez le [programme d’installation de la préversion App Service sur Azure Stack](http://aka.ms/appsvconmasrc1installer).

2. Téléchargez les [scripts d’assistance au déploiement App Service sur Azure Stack](http://aka.ms/appsvconmasrc1helper).

3. Extrayez les fichiers à partir du fichier zip des scripts d’assistance. La structure de fichiers et de dossier suivante s’affiche :

   - Create-AppServiceCerts.ps1
   - Create-IdentityApp.ps1
   - Modules
      - AzureStack.Identity.psm1
      - GraphAPI.psm1

## <a name="create-an-offline-installation-package"></a>Créer un package d'installation hors connexion

Pour déployer App Service dans un environnement isolé, vous devez créer un package d’installation hors connexion à partir d’une machine connectée à Internet.

1. Exécutez le programme d’installation de la préversion App Service sur Azure Stack (AppService.exe) à partir d’une machine connectée à Internet.

2. Cliquez sur l’onglet **Avancé**, puis cliquez sur **Créer un package d’installation hors connexion**.

    ![Créer un package d’installation hors connexion App Service sur Azure Stack][1]

3. Le programme d’installation App Service crée un package d’installation hors connexion, affiche le chemin d’accès et donne la possibilité d’ouvrir le dossier.

   ![Package d’installation hors connexion App Service sur Azure Stack][2]

4. Copiez le programme d’installation de la préversion App Service sur Azure Stack (AppService.exe) et le package d’installation hors connexion sur l’ordinateur hôte Azure Stack.

## <a name="create-certificates-required-by-app-service-on-azure-stack"></a>Créer les certificats requis par App Service sur Azure Stack

Ce premier script fonctionne avec l’autorité de certification Azure Stack pour créer trois certificats dont App Service a besoin. Exécutez le script sur l’hôte Azure Stack et assurez-vous que vous exécutez PowerShell en tant que azurestack\administrator.

1. Dans une session PowerShell exécutée en tant que azurestack\administrator, exécutez le script **AppServiceCerts.ps1-créer** à partir de l’emplacement où vous avez extrait les scripts d’assistance.  Le script crée trois certificats, dans le même dossier que le script des certificats de création, qui sont requis par App Service.

2. Entrez un mot de passe pour sécuriser les fichiers .pfx et prenez-en note. Vous devez l’entrer dans le programme d’installation App Service sur Azure Stack.

### <a name="create-appservicecertsps1-parameters"></a>Paramètres Create-AppServiceCerts.ps1

| Paramètre | Obligatoire ou facultatif | Valeur par défaut | Description |
| --- | --- | --- | --- |
| pfxPassword | Requis | Null | Mot de passe utilisé pour protéger la clé privée du certificat |
| DomainName | Requis | local.azurestack.external | Suffixe de la région et du domaine Azure Stack |
| CertificateAuthority | Requis | AzS-CA01.azurestack.local | Point de terminaison de l’autorité de certification |

## <a name="complete-the-offline-installation-of-app-service-on-azure-stack"></a>Terminer l’installation hors connexion d’App Service sur Azure Stack

> [!NOTE]
> Vous *devez* utiliser un compte avec élévation de privilèges (administrateur local ou de domaine) pour exécuter le programme d’installation. Si vous êtes connecté en tant que azurestack\azurestackuser, vous êtes invité à entrer des informations d’identification avec élévation de privilèges.

1. Exécutez appservice.exe en tant que azurestack\administrator.

2. Cliquez sur l’onglet **Avancé**, puis cliquez sur **Terminer l’installation hors connexion**.

    ![Terminer l’installation hors connexion d’App Service sur Azure Stack][3]

3. Spécifiez l’emplacement du package d’installation hors connexion que vous avez créé, puis cliquez sur **Suivant**.

    ![Emplacement du package d’installation hors connexion App Service sur Azure Stack][4]

4. Consultez et acceptez les termes du contrat de licence de la préversion du logiciel Microsoft, puis cliquez sur **Suivant**.

5. Consultez et acceptez les termes du contrat de licence tiers, puis cliquez sur **Suivant**.

6. Passez en revue les informations de configuration cloud App Service, puis cliquez sur **Suivant**.

    ![Configuration cloud App Service sur Azure Stack][5]

    > [!NOTE]
    > Le programme d’installation App Service sur Azure Stack fournit les valeurs par défaut pour une installation Azure Stack à un nœud. Si vous avez personnalisé des options lors du déploiement d’Azure Stack (par exemple, le suffixe du domaine), vous devez modifier en conséquence les valeurs dans cette fenêtre. Par exemple, si vous utilisez le suffixe de domaine mycloud.com, vous devez modifier votre point de terminaison Azure Resource Manager administrateur sur adminmanagement.[region].mycloud.com.

7. Cliquez sur le bouton **Se connecter** situé en regard de la zone **Abonnements Azure Stack**. Entrez votre compte d’administrateur, par exemple, azurestackadmin@azurestack.local. Entrez votre mot de passe, puis cliquez sur **Se connecter**.

8. Sélectionnez votre abonnement dans la zone **Abonnements Azure Stack**.

9. Dans la zone **Emplacements Azure Stack**, sélectionnez l’emplacement qui correspond à la région où vous effectuez le déploiement. Par exemple, sélectionnez **local**. Cliquez sur **Suivant**.

    ![Sélection d’abonnement App Service sur Azure Stack][6]

10. Entrez le **Nom du groupe de ressources** pour votre déploiement App Service. Par défaut, il est défini sur **APPSERVICE-LOCAL**.

11. Entrez le **Nom du compte de stockage** qu’App Service doit créer dans le cadre de l’installation. Par défaut, il est défini sur **appsvclocalstor**.

12. Entrez les détails de SQL Server pour l’instance utilisée pour héberger les bases de données du fournisseur de ressources App Service. Cliquez sur **Suivant** et le programme d’installation valide les propriétés de connexion SQL.

13. Cliquez sur le bouton **Parcourir** situé en regard de la zone **Fichier du certificat SSL App Service par défaut**. Accédez au certificat **_.appservice.local.AzureStack.external** [créé précédemment](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps). Si vous avez spécifié un emplacement et un suffixe de domaine différents lorsque vous avez créé le certificat, sélectionnez le certificat correspondant.

14. Entrez le mot de passe du certificat que vous avez défini lorsque vous avez créé le certificat.

15. Cliquez sur le bouton **Parcourir** situé en regard de la zone **Fichier du certificat SSL du fournisseur de ressources**. Accédez au certificat **api.appservice.local.AzureStack.external** [créé précédemment](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps). Si vous avez spécifié un emplacement et un suffixe de domaine différents lorsque vous avez créé le certificat, sélectionnez le certificat correspondant.

16. Entrez le mot de passe du certificat que vous avez défini lorsque vous avez créé le certificat.

17. Cliquez sur le bouton **Parcourir** situé en regard de la zone **Fichier du certificat racine du fournisseur de ressources**. Accédez au certificat **AzureStackCertificationAuthority** [créé précédemment](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps).

18. Cliquez sur **Suivant**. Le programme d’installation vérifie le mot de passe du certificat fourni.

    ![Détails du certificat App Service sur Azure Stack][8]

19. Vérifiez la configuration du rôle App Service. Les valeurs par défaut sont renseignées avec les références d’instance minimale recommandée pour chaque rôle. Un résumé des exigences de base et de mémoire est fourni pour vous aider à planifier votre déploiement. Une fois vos sélections effectuées, cliquez sur **Suivant**.

    - **Contrôleur** : par défaut, une instance A1 Standard est sélectionnée. Ceci est le minimum recommandé. Le rôle Contrôleur est responsable de la gestion et de la maintenance de l’intégrité du cloud App Service.
    - **Gestion** : par défaut, une instance A2 Standard est sélectionnée. Pour le basculement, nous recommandons d’utiliser deux instances. Le rôle Gestion est responsable des points de terminaison App Service Azure Resource Manager et d’API, des extensions du portail (admin, locataire, portail Functions) et du service des données.
    - **Serveur de publication** : par défaut, une instance A1 Standard est sélectionnée. Ceci est le minimum recommandé. Le rôle Serveur de publication est responsable de la publication du contenu via FTP et déploiement web.
    - **Frontal** : par défaut, une instance A1 Standard est sélectionnée. Ceci est le minimum recommandé. Le rôle Frontal est responsable de l’acheminement des demandes aux applications App Service.
    - **Worker partagé**: par défaut, une instance A1 Standard est sélectionnée, mais vous pouvez en ajouter d’autres. En tant qu’administrateur, vous pouvez définir votre offre et choisir n’importe quel niveau de référence. Les niveaux doivent avoir au minimum un cœur. Le rôle Worker partagé est chargé d’héberger les applications web, mobiles ou d’API et les applications Azure Functions.

    ![Configuration du rôle App Service sur Azure Stack][9]

    > [!NOTE]
    > Dans les préversions techniques, le programme d’installation du fournisseur de ressources App Service déploie également une instance A1 Standard pour fonctionner en tant que serveur de fichiers simple pour prendre en charge Azure Resource Manager. Cette configuration est conservée pour un point de contact à un seul nœud. Pour les charges de production, lors de la disponibilité générale, le programme d’installation App Service permet d’utiliser un serveur de fichiers à haute disponibilité.

20. Choisissez votre image de machine virtuelle **Windows Server 2016** de déploiement parmi celles disponibles dans le fournisseur de ressources de calcul pour le cloud App Service. Cliquez sur **Suivant**. 

    ![Sélection d’image de machine virtuelle App Service sur Azure Stack][10]

21. Entrez un nom d’utilisateur et un mot de passe pour les rôles de travail configurés dans le cloud App Service. Entrez un nom d’utilisateur et un mot de passe pour tous les autres rôles App Service. Cliquez sur **Suivant**.

    ![Saisie des informations d’identification App Service sur Azure Stack][11]

22. Dans l’écran de synthèse, vérifiez les sélections effectuées. Pour apporter des modifications, passez en revue les écrans et modifiez vos sélections. Si la configuration est conforme à ce que vous souhaitez, cochez la case. Cliquez sur **Suivant** pour commencer le déploiement. 

    ![Synthèse des sélections App Service sur Azure Stack][12]

23. Suivez la progression de l’installation. Le déploiement d’App Service sur Azure Stack prend environ 45 à 60 minutes avec les sélections par défaut.

    ![Progression de l’installation App Service sur Azure Stack][13]

24. Une fois le programme d’installation terminé avec succès, cliquez sur **Quitter**.

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

4. Dans la même session PowerShell, exécutez le script **CreateIdentityApp.ps1**. Lorsque vous êtes invité à entrer votre ID de locataire Azure Active Directory (Azure AD), entrez **ADFS**.

5. Dans la fenêtre **Informations d’identification**, entrez votre compte administrateur et votre mot de passe pour le service AD FS. Cliquez sur **OK**.

6. Entrez le chemin d’accès au fichier du certificat et le mot de passe du certificat pour le [certificat créé précédemment](# Create certificates to be used by App Service on Azure Stack). Le certificat par défaut créé pour cette étape est sso.appservice.local.azurestack.external.pfx.

7. Le script crée une nouvelle application dans Azure AD locataire et génère un script PowerShell.

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


## <a name="validate-the-app-service-on-azure-stack-installation"></a>Valider l’installation App Service sur Azure Stack

1. Dans le portail d’administration Azure Stack, accédez au groupe de ressources créé par le programme d’installation. Par défaut, ce groupe est **APPSERVICE-LOCAL**.

2. Recherchez **CN0-VM**. Pour vous connecter à la machine virtuelle, cliquez sur **Se connecter** dans le panneau **Machine virtuelle**.

3. Sur le bureau de cette machine virtuelle, cliquez deux fois sur **Console de gestion cloud web**.

4. Accédez à **Serveurs gérés**.

5. Lorsque toutes les machines affichent l’état **Prêt** pour un ou plusieurs Workers, passez à l’étape 6.

6. Fermez l’ordinateur Bureau à distance et revenez à l’ordinateur où vous avez exécuté le programme d’installation App Service.

    > [!NOTE]
    > Vous n’avez pas besoin d’attendre qu’un ou plusieurs Workers affichent l’état **Prêt** pour terminer l’installation d’App Service sur Azure Stack. Toutefois, vous devez avoir au minimum un Worker prêt à déployer une application web, mobile ou d’API, ou Azure Functions.
    
    ![État des serveurs gérés App Service sur Azure Stack][14]

## <a name="test-drive-app-service-on-azure-stack"></a>Tester App Service sur Azure Stack

Après avoir déployé et inscrit le fournisseur de ressources App Service, testez-le pour vous assurer que les locataires peuvent déployer des applications web, mobiles et d’API.

> [!NOTE]
> Vous devez créer une offre avec l’espace de noms Microsoft.Web dans le plan. Ensuite, vous devez avoir un abonnement locataire qui s’abonne à cette offre. Pour plus d’informations, consultez [Créer une offre](azure-stack-create-offer.md) et [Créer un plan](azure-stack-create-plan.md).
>

Vous *devez* disposer d’un abonnement locataire pour créer des applications qui utilisent App Service sur Azure Stack. Les seules fonctionnalités qu’un administrateur de service peut effectuer dans le portail d’administration sont liées à l’administration du fournisseur de ressources App Service. Ces fonctionnalités incluent l’ajout de capacité, la configuration de sources de déploiement, l’ajout de niveaux Worker et de références.

À compter de la troisième préversion technique, vous devez utiliser le portail du locataire et disposer d’un abonnement locataire pour créer des applications web, mobiles et d’API.  

1. Dans le portail du locataire Azure Stack, cliquez sur **Nouveau** > **Web + Mobile** > **Application web**.

2. Dans le panneau **Application web**, tapez un nom dans la zone **Application web**.

3. Sous **Groupe de ressources**, cliquez sur **Nouveau**. Puis tapez un nom dans la zone **Groupe de ressources**.

4. Cliquez sur **Plan App Service/Emplacement** > **Créer**.

5. Dans le panneau **Plan App Service**, tapez un nom dans la zone **Plan App Service**.

6. Cliquez sur **Niveau tarifaire** > **Libre-Partagé** ou **Partagé-Partagé** > **Sélectionnez** > **OK** > **Créer**.

7. En moins d’une minute, une vignette pour la nouvelle application web s’affiche dans le tableau de bord. Cliquez sur la vignette.

8. Dans le panneau **Application web**, cliquez sur **Parcourir** pour afficher le site web par défaut pour cette application.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Déployer un site web WordPress, DNN ou Django (facultatif)

1. Dans le portail du locataire Azure Stack, cliquez sur **+**. Accédez à la Place de marché Azure, déployez un site web Django et attendez que l’opération se termine. La plateforme web Django utilise une base de données basée sur système de fichiers. Elle ne nécessite aucun fournisseur de ressources supplémentaire comme SQL ou MySQL.

2. Si vous avez également déployé un fournisseur de ressources MySQL, vous pouvez déployer un site web WordPress à partir de la Place de marché. Lorsque vous êtes invité à entrer les paramètres de la base de données, entrez le nom d’utilisateur *User1@Server1*, avec le nom d’utilisateur et le nom de serveur de votre choix.

3. Si vous avez également déployé un fournisseur de ressources SQL Server, vous pouvez déployer un site web DNN à partir de la Place de marché. Lorsque vous êtes invité à entrer les paramètres de la base de données, sélectionnez une base de données sur l’ordinateur exécutant SQL Server connecté à votre fournisseur de ressources.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également tester d’autres [services PaaS](azure-stack-tools-paas-services.md).

- [Fournisseur de ressources SQL Server](azure-stack-sql-resource-provider-deploy.md)
- [Fournisseur de ressources MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy-offline/app-service-offline-step1.png
[2]: ./media/azure-stack-app-service-deploy-offline/app-service-offline-step2.png
[3]: ./media/azure-stack-app-service-deploy-offline/app-service-offline-step3.png
[4]: ./media/azure-stack-app-service-deploy-offline/app-service-offline-step4.png
[5]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-default-entries-step-cloud-configuration.png
[6]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-default-entries-step-subscription-location-populated.png
[7]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-default-entries-step-resource-group-SQL.png
[8]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-default-entries-step-certificates.png
[9]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-default-entries-step-role-configuration.png
[10]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-default-entries-step-vm-image-selection.png
[11]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-default-entries-step-role-credentials.png
[12]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-selection-summary.png
[13]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-installation-progress.png
[14]: ./media/azure-stack-app-service-deploy-offline/managed-servers.png


<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525


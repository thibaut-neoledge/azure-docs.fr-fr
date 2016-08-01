<properties
   pageTitle="Créer un principal du service avec l’interface de ligne de commande (CLI) Azure | Microsoft Azure"
   description="Explique comment utiliser l’interface de ligne de commande (CLI) Azure pour créer une application et un principal du service Active Directory, et comment accorder à l’application l’accès aux ressources par le biais du contrôle d’accès en fonction du rôle. Cet article montre comment authentifier l’application avec un mot de passe ou un certificat."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="07/19/2016"
   ms.author="tomfitz"/>

# Créer un principal du service pour accéder aux ressources à l’aide de l’interface de ligne de commande (CLI) Azure

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Interface de ligne de commande Azure](resource-group-authenticate-service-principal-cli.md)
- [Portail](resource-group-create-service-principal-portal.md)

Lorsque vous exécutez une application ou un script qui a besoin d’accéder à des ressources, vous préférerez probablement éviter d’utiliser les informations d’identification d’un utilisateur. Cet utilisateur peut avoir différentes autorisations que vous souhaiteriez affecter au processus, et peut assumer des responsabilités susceptibles d’évoluer. Au lieu de cela, vous pouvez créer pour l’application une identité qui inclut des informations d’identification et des affectations de rôles. Votre application se connectera alors sous cette identité à chaque exécution. Cette rubrique vous montre comment utiliser l[’interface de ligne de commande Azure pour Mac, Linux et Windows](xplat-cli-install.md) pour configurer tout ce dont vous avez besoin pour qu’une application puisse s’exécuter sous ses propres informations d’identification et sous sa propre identité.

Dans cet article, vous allez créer deux objets : l’application Active Directory (AD) et le principal du service. L’application AD contient les informations d’identification (c’est-à-dire un ID d’application et un mot de passe ou un certificat). Le principal du service contient l’affectation de rôle. Dans l’application AD, vous pouvez créer plusieurs principaux du service. Cette rubrique se concentre sur une application à locataire unique conçue pour s’exécuter au sein d’une seule organisation. Les applications à locataire unique sont généralement utilisées pour les applications métier exécutées au sein de votre organisation. Vous pouvez également créer des applications mutualisées lorsque votre application doit s’exécuter dans de plusieurs organisations. Ces applications sont habituellement utilisées pour les applications SaaS (Software-as-a-Service). Pour configurer une application mutualisée, consultez le [Guide du développeur pour l’authentification avec l’API Azure Resource Manager](resource-manager-api-authentication.md).

Cet article décrit de nombreux concepts qu’il est essentiel de bien comprendre pour utiliser Active Directory. Pour obtenir une explication plus détaillée des applications et des principaux du service, consultez la rubrique [Objets principal du service et application](./active-directory/active-directory-application-objects.md). Pour plus d'informations sur l'authentification Active Directory, consultez la rubrique [Scénarios d'authentification pour Azure AD](./active-directory/active-directory-authentication-scenarios.md).

L’interface de ligne de commande Azure vous propose deux options pour authentifier votre application AD :

 - password
 - certificat

Si, après avoir configuré votre application AD, vous souhaitez vous connecter à Azure à partir d’une autre infrastructure de programmation (telles Python, Ruby ou Node.js), l’authentification par mot de passe peut se révéler la meilleure option. Avant de déterminer s’il est préférable d’utiliser un mot de passe ou un certificat, consultez la section [Exemples d’applications](#sample-applications) pour obtenir des exemples d’authentification dans les différentes infrastructures.

## Obtenir l’ID de locataire

Chaque fois que vous vous connectez en tant que principal de service, vous devez fournir l’ID de locataire du répertoire de votre application AD. Un locataire est une instance d’Active Directory. Étant donné que vous aurez besoin de cette valeur aussi bien pour l’authentification par mot de passe que pour l’authentification par certificat, tâchons d’obtenir dès maintenant cette valeur.

1. Connectez-vous à votre compte.

        azure config mode arm
        azure login

1. Si vous récupérez l’ID de client pour votre abonnement actuellement authentifié, il est inutile de fournir l’ID d’abonnement en tant que paramètre. Le commutateur **- r** récupère la valeur sans guillemets.

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

Passez maintenant à la section ci-dessous pour vous authentifier à l’aide d’un [mot de passe](#create-service-principal-with-password) ou d’un [certificat](#create-service-principal-with-certificate).


## Créer un principal du service avec un mot de passe

Dans cette section, vous allez suivre la procédure qui vous permettra de créer un principal du service à l’aide d’un mot de passe et de l’affecter à un rôle.

1. Créez un principal du service pour votre application. Indiquez le nom d’affichage de votre application, l’URI vers une page décrivant votre application (le lien n’est pas vérifié), les URI identifiant votre application et le mot de passe correspondant à l’identité de votre application. Cette commande crée à la fois l’application AD et le principal du service.

        azure ad sp create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p <Your_Password>
        
    Le nouveau principal du service est renvoyé. L’ID d’objet est nécessaire lorsque vous accordez des autorisations. Le nom du principal du service est nécessaire pour se connecter.
    
        info:    Executing command ad sp create
        + Creating application exampleapp
        / Creating service principal for application 7132aca4-1bdb-4238-ad81-996ff91d8db+
        data:    Object Id:               ff863613-e5e2-4a6b-af07-fff6f2de3f4e
        data:    Display Name:            exampleapp
        data:    Service Principal Names:
        data:                             7132aca4-1bdb-4238-ad81-996ff91d8db4
        data:                             https://www.contoso.org/example
        info:    ad sp create command OK

2. Accordez des autorisations au principal du service sur votre abonnement. Dans cet exemple, vous allez permettre au principal du service de lire toutes les ressources de l’abonnement. Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez [Contrôle d’accès en fonction du rôle Azure](./active-directory/role-based-access-control-configure.md). Pour affecter un rôle, vous devez disposer d’un accès `Microsoft.Authorization/*/Write`, qui est accordé via le rôle [Propriétaire](./active-directory/role-based-access-built-in-roles.md#owner) ou [Administrateur de l’accès utilisateur](./active-directory/role-based-access-built-in-roles.md#user-access-administrator).

        azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/

Et voilà ! Votre application Active Directory et votre principal du service sont maintenant configurés. La section suivante vous montre comment vous connecter avec les informations d’identification via l’interface de ligne de commande Azure ; si vous souhaitez toutefois utiliser les informations d’identification dans votre application de code, vous pouvez ignorer cette rubrique. Vous pouvez passer directement aux [exemples d’applications](#sample-applications) pour obtenir des exemples de connexion à l’aide d’un ID d’application et d’un mot de passe.

### Fournir des informations d’identification via Azure CLI

1. Pour le nom d’utilisateur, utilisez le nom du principal du service retourné lors de la création du principal du service. Si vous avez besoin de récupérer l’ID d’application, utilisez la commande suivante. Indiquez le nom de l’application Active Directory dans le paramètre **search**.

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

3. Connectez-vous en tant que principal du service.

        azure login -u 7132aca4-1bdb-4238-ad81-996ff91d8db4 --service-principal --tenant "$tenantId"

    Vous êtes invité à entrer le mot de passe. Entrez le mot de passe que vous avez indiqué lors de la création de l’application AD.

        info:    Executing command login
        Password: ********

Vous êtes maintenant authentifié en tant que principal du service pour le principal du service que vous avez créé.

## Créer un principal du service avec un certificat

Dans cette section, vous suivrez la procédure vous permettant de créer un principal du service qui utilise un certificat pour l’authentification. Pour cela, [OpenSSL](http://www.openssl.org/) doit être installé.

1. Vous pouvez créer un certificat auto-signé.

        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'

2. Combinez les clés publiques et privées.

        cat privkey.pem cert.pem > examplecert.pem

3. Ouvrez le fichier **examplecert.pem** et copiez les données de certificat. Recherchez la longue séquence de caractères entre **-----BEGIN CERTIFICATE-----** et **-----END CERTIFICATE-----**.

1. Créez un principal du service pour votre application. Indiquez l’ID d’application renvoyé à l’étape précédente.

        azure ad sp create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>
        
    Le nouveau principal du service est renvoyé. L’ID d’objet est nécessaire lorsque vous accordez des autorisations.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
2. Accordez des autorisations au principal du service sur votre abonnement. Dans cet exemple, vous allez permettre au principal du service de lire toutes les ressources de l’abonnement. Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez [Contrôle d’accès en fonction du rôle Azure](./active-directory/role-based-access-control-configure.md). Pour affecter un rôle, vous devez disposer d’un accès `Microsoft.Authorization/*/Write`, qui est accordé via le rôle [Propriétaire](./active-directory/role-based-access-built-in-roles.md#owner) ou [Administrateur de l’accès utilisateur](./active-directory/role-based-access-built-in-roles.md#user-access-administrator).

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

### Fournir un certificat via un script Azure CLI automatisé

Pour vous authentifier avec Azure CLI, vous devez fournir l’empreinte du certificat, le fichier de certificat, l’ID d’application et l’ID de client.

    azure login --service-principal --tenant 00000 -u 000000 --certificate-file C:\certificates\examplecert.pem --thumbprint 000000

Vous êtes maintenant authentifié en tant que principal du service pour l’application Active Directory que vous avez créée.

Si vous devez récupérer l’empreinte de certificat et supprimer les caractères inutiles, utilisez :

    openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
    
Vous obtenez alors une valeur d’empreinte semblable à ce qui suit :

    30996D9CE48A0B6E0CD49DBB9A48059BF9355851

Si vous avez besoin de récupérer l’ID d’application, utilisez la commande suivante :

    azure ad app show --search exampleapp --json | jq -r '.[0].appId'

Si vous avez besoin de récupérer l’ID de locataire ultérieurement, consultez la section [Obtenir l’ID de locataire](#get-tenant-id).


## Exemples d'applications

Les exemples d’applications suivants montrent comment ouvrir une session en tant que principal du service.

**.NET**

- [Deploy an SSH Enabled VM with a Template with .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/) (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle dans .NET)
- [Manage Azure resources and resource groups with .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/) (Gérer des ressources et des groupes de ressources Azure avec .NET)

**Java**

- [Getting Started with Resources - Deploy Using Azure Resource Manager Template - in Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/) (Découverte des ressources - Déployer à l’aide du modèle Azure Resource Manager dans Java)
- [Getting Started with Resources - Manage Resource Group - in Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//) (Découverte des ressources - Gérer un groupe de ressources dans Java)

**Python**

- [Deploy an SSH Enabled VM with a Template in Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/) (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle dans Python)
- [Managing Azure Resource and Resource Groups with Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/) (Gérer des ressources et des groupes de ressources Azure avec Python)

**Node.JS**

- [Deploy an SSH Enabled VM with a Template in Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/) (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle dans Node.js)
- [Manage Azure resources and resource groups with Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/) (Gérer des ressources et des groupes de ressources Azure avec Node.js)

**Ruby**

- [Deploy an SSH Enabled VM with a Template in Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/) (Déployer une machine virtuelle compatible SSH à l’aide d’un modèle dans Ruby)
- [Managing Azure Resource and Resource Groups with Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/) (Gérer des ressources et des groupes de ressources Azure avec Ruby)


## Étapes suivantes
  
- Pour obtenir des instructions détaillées sur l’intégration d’une application à Azure pour la gestion des ressources, consultez [Guide du développeur pour l’authentification avec l’API Azure Resource Manager](resource-manager-api-authentication.md).
- Pour obtenir des informations supplémentaires sur l’utilisation de certificats et de l’interface de ligne de commande Azure, consultez la page [Certificate-based auth with Azure Service Principals from Linux command line](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx) (Authentification par certificat à l’aide de principaux du service Azure à partir de la ligne de commande Linux).

<!---HONumber=AcomDC_0720_2016-->
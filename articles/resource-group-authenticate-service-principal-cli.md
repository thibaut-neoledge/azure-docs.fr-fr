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
   ms.date="05/26/2016"
   ms.author="tomfitz"/>

# Créer un principal du service pour accéder aux ressources à l’aide de l’interface de ligne de commande (CLI) Azure

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Interface de ligne de commande Azure](resource-group-authenticate-service-principal-cli.md)
- [Portail](resource-group-create-service-principal-portal.md)

Cette rubrique vous montre comment utiliser [l’interface de ligne de commande Azure pour Mac, Linux et Windows](xplat-cli-install.md) pour créer une application Active Directory (AD), telle qu’un processus, une application ou un service automatisé, qui peut accéder aux autres ressources de votre abonnement. Azure Resource Manager vous permet d’utiliser le contrôle d’accès en fonction du rôle pour octroyer les actions autorisées à l’application.

Dans cet article, vous allez créer deux objets : l’application AD et le principal du service. L’application AD réside sur le client sur lequel l’application est inscrite, et définit le processus à exécuter. Le principal du service contient l’identité de l’application AD et est utilisé pour attribuer des autorisations. Dans l’application AD, vous pouvez créer plusieurs principaux du service. Pour obtenir une explication plus détaillée des applications et des principaux du service, consultez la rubrique [Objets principal du service et application](./active-directory/active-directory-application-objects.md). Pour plus d'informations sur l'authentification Active Directory, consultez la rubrique [Scénarios d'authentification pour Azure AD](./active-directory/active-directory-authentication-scenarios.md).

2 options sont à votre disposition pour authentifier votre application :

 - mot de passe : convient lorsqu’un utilisateur souhaite se connecter de manière interactive pendant l’exécution
 - certificat : convient aux scripts sans assistance qui doivent s’authentifier sans intervention de l’utilisateur
 
## Créer une application AD à l’aide d’un mot de passe

Dans cette section, vous allez appliquer les étapes permettant de créer l’application Active Directory à l’aide d’un mot de passe.

1. Basculez en mode Azure Resource Manager et connectez-vous à votre compte.

        azure config mode arm
        azure login

2. Créez une application AD en exécutant la commande **azure ad app create**. Indiquez le nom d’affichage de votre application, l’URI vers une page décrivant votre application (le lien n’est pas vérifié), les URI identifiant votre application et le mot de passe correspondant à l’identité de votre application.

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    L’application AD est renvoyée. La propriété ApplicationId est nécessaire pour la création de principaux du service et l’acquisition de jeton d’accès.

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

### Créer un principal du service et lui assigner un rôle

1. Créez un principal du service pour votre application. Indiquez l’ID d’application renvoyé à l’étape précédente.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    Le nouveau principal du service est renvoyé. L’ID d’objet est nécessaire lorsque vous accordez des autorisations.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK

2. Accordez des autorisations au principal du service sur votre abonnement. Dans cet exemple, vous allez permettre au principal du service de lire toutes les ressources de l’abonnement. Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez [Contrôle d’accès en fonction du rôle Azure](./active-directory/role-based-access-control-configure.md).

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

### Fournir manuellement des informations d’identification via Azure CLI

Vous avez créé une application AD et un principal du service pour cette application. Vous avez également assigné un rôle au principal du service. Maintenant, vous devez ouvrir une session en tant qu’application pour effectuer des opérations. Vous pouvez renseigner manuellement les informations d’identification de l’application lors de l’exécution de scripts ou de commandes à la demande.

1. Déterminez le paramètre **TenantId** de l’abonnement qui contient le principal du service. Si vous récupérez l’ID de client pour votre abonnement actuellement authentifié, il est inutile de fournir l’ID d’abonnement en tant que paramètre. Le commutateur **- r** récupère la valeur sans guillemets.

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

2. Pour le nom d’utilisateur, utilisez le paramètre **AppId** que vous avez utilisé lors de la création du principal du service. Si vous avez besoin de récupérer l’ID d’application, utilisez la commande suivante. Indiquez le nom de l’application Active Directory dans le paramètre **search**.

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

3. Connectez-vous en tant que principal du service.

        azure login -u "$appId" --service-principal --tenant "$tenantId"

    Vous êtes invité à entrer le mot de passe. Entrez le mot de passe que vous avez indiqué lors de la création de l’application AD.

        info:    Executing command login
        Password: ********

Vous êtes maintenant authentifié en tant que principal du service pour l’application AD que vous avez créée.

## Créer une application AD à l’aide d’un certificat

Dans cette section, vous suivrez la procédure vous permettant de créer le principal du service d’une application AD qui utilise un certificat pour l’authentification. Pour cela, [OpenSSL](http://www.openssl.org/) doit être installé.

1. Vous pouvez créer un certificat auto-signé.

        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'

2. Combinez les clés publiques et privées.

        cat privkey.pem cert.pem > examplecert.pem

3. Ouvrez le fichier **examplecert.pem** et copiez les données de certificat. Recherchez la longue séquence de caractères entre **-----BEGIN CERTIFICATE-----** et **-----END CERTIFICATE-----**.

4. Créez une application Active Directory à l’aide de la commande **azure ad app create** et fournissez les données de certificat que vous avez copiées à l’étape précédente en tant que valeur de clé.

        azure ad app create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>

    L’application Active Directory est renvoyée. La propriété ApplicationId est nécessaire pour la création de principaux du service et l’acquisition de jeton d’accès.

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

### Créer un principal du service et lui assigner un rôle

1. Créez un principal du service pour votre application. Indiquez l’ID d’application renvoyé à l’étape précédente.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    Le nouveau principal du service est renvoyé. L’ID d’objet est nécessaire lorsque vous accordez des autorisations.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
2. Accordez des autorisations au principal du service sur votre abonnement. Dans cet exemple, vous allez permettre au principal du service de lire toutes les ressources de l’abonnement. Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez [Contrôle d’accès en fonction du rôle Azure](./active-directory/role-based-access-control-configure.md).

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

### Préparer les valeurs pour votre script

Dans votre script, vous allez transmettre trois valeurs nécessaires pour vous connecter en tant que principal du service. Vous aurez besoin de ce qui suit :

- ID d’application
- ID client
- Empreinte de certificat

Vous avez vu l’ID d’application et l’empreinte de certificat lors des étapes précédentes. Toutefois, si vous avez besoin de récupérer ces valeurs ultérieurement, les commandes requises sont indiquées ci-dessous, ainsi que la commande permettant d’obtenir l’ID client.

1. Pour récupérer l’empreinte de certificat et supprimer les caractères inutiles, utilisez :

        openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
    
     Vous obtenez alors une valeur d’empreinte semblable à ce qui suit :

        30996D9CE48A0B6E0CD49DBB9A48059BF9355851

2. Pour récupérer l’ID client, utilisez :

        azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId'

3. Pour récupérer l’ID d’application, utilisez :

        azure ad app show --search exampleapp --json | jq -r '.[0].appId'

### Fournir un certificat via un script Azure CLI automatisé

Vous avez créé une application Active Directory et un principal du service pour votre application. Vous avez également assigné un rôle au principal du service. Vous devez maintenant ouvrir une session en tant que principal du service pour pouvoir effectuer des opérations de principal du service.

Pour vous authentifier avec Azure CLI, vous devez fournir l’empreinte du certificat, le fichier de certificat, l’ID d’application et l’ID de client.

        azure login --service-principal --tenant {tenant-id} -u {app-id} --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}

Vous êtes maintenant authentifié en tant que principal du service pour l’application Active Directory que vous avez créée.

## Étapes suivantes
  
- Pour obtenir des exemples d’authentification .NET, consultez [Kit de développement logiciel (SDK) Azure Resource Manager pour .NET](resource-manager-net-sdk.md).
- Pour obtenir des exemples d’authentification Java, consultez [Kit de développement logiciel (SDK) Azure Resource Manager pour Java](resource-manager-java-sdk.md).
- Pour obtenir des exemples d’authentification Python, consultez [Resource Management Authentication for Python](https://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagementauthentication.html) (Authentification Resource Management pour Python).
- Pour obtenir des exemples d’authentification REST, consultez [API REST Resource Manager](resource-manager-rest-api.md).
- Pour obtenir des instructions détaillées sur l’intégration d’une application dans Azure pour la gestion des ressources, consultez [Guide du développeur pour l’authentification avec l’API Azure Resource Manager](resource-manager-api-authentication.md).
- Pour obtenir des informations supplémentaires sur l’utilisation de certificats et de l’interface de ligne de commande Azure, consultez la page [Certificate-based auth with Azure Service Principals from Linux command line](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx) (Authentification par certificat à l’aide de principaux du service Azure à partir de la ligne de commande Linux).

<!---HONumber=AcomDC_0629_2016-->
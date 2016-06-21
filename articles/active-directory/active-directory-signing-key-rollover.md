<properties
	pageTitle="Substitution de la clé de signature dans Azure AD | Microsoft Azure"
	description="Cet article décrit les meilleures pratiques de substitution de clé de signature pour Azure Active Directory"
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="priyamo"/>

# Substitution de la clé de signature dans Azure Active Directory

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

Cette rubrique explique ce que vous devez savoir sur les clés publiques utilisées dans Azure Active Directory (Azure AD) pour la signature des jetons de sécurité. Il est important de noter que ces clés sont renouvelées toutes les 6 semaines. En cas d’urgence, vous pouvez obtenir une nouvelle clé avant ce délai de 6 semaines. Toutes les applications qui utilisent Azure AD doivent être en mesure de gérer le processus de substitution de clé par le biais d’un programme. Poursuivez la lecture de ce document pour comprendre le principe de fonctionnement des clés et savoir comment mettre à jour votre application pour gérer la substitution de clé.

## Vue d’ensemble des clés de signature dans Azure AD

Azure AD utilise une technique de chiffrement de clés publiques conforme aux normes du secteur pour établir une relation de confiance avec les applications qui l’utilisent. Plus concrètement, cela signifie qu’Azure AD utilise une clé de signature se composant d’une paire clé publique-clé privée. Lorsqu’un utilisateur se connecte à une application qui utilise Azure AD pour s’authentifier, Azure AD crée un jeton de sécurité qui contient des informations sur l’utilisateur. Ce jeton est signé par Azure AD à l’aide de sa clé privée avant d’être renvoyé à l’application. Pour vérifier que le jeton est valide et provient bien d’Azure AD, l’application doit valider la signature du jeton à l’aide de la clé publique exposée par Azure AD contenue dans le document de métadonnées de fédération du client. Cette clé publique (et la clé de signature d’où elle dérive) est la même que celle qui est utilisée pour tous les clients dans Azure AD.

Pour des raisons de sécurité, la clé publique d’Azure AD est renouvelée toutes les six semaines. En cas d’urgence, la clé peut être changée bien avant ce délai. Toute application intégré à Azure AD doit être préparée à gérer un événement de substitution de clé, quelle que soit sa fréquence. Selon la date de création de votre application et la bibliothèque d’authentification que vous avez utilisée pour la développer, votre application peut ou non contenir la logique nécessaire pour gérer la substitution de clé. Si ce n’est pas le cas et que votre application tente d’utiliser une clé publique ayant expiré pour vérifier la signature d’un jeton, la demande de connexion échouera.

Une clé pouvant être substituée à tout moment, il y a toujours plusieurs clés publiques valides disponibles dans le document de métadonnées de la fédération. Votre application doit être préparée à utiliser n’importe quelle clé spécifiée dans le document, car une clé peut être substituée rapidement et une autre prise en remplacement, et ainsi de suite. Il est recommandé que votre application place ces clés en cache dans une base de données ou dans un fichier de configuration afin d’améliorer l’efficacité de la communication avec Azure Active Directory pendant le processus de connexion et de valider rapidement un jeton à l’aide d’une clé différente.

## Mise à jour de votre application avec la logique de substitution de clé

Plusieurs facteurs influent sur la manière dont votre application gèrera la substitution de clé, par exemple l’infrastructure d’identité utilisée, la version du framework ou encore le type d’application. Chaque section ci-dessous vous montre comment mettre à jour les types d’applications et les configurations les plus courantes. Vous pouvez également suivre ces étapes pour vous assurer que la logique fonctionne correctement.

### Applications web créées avec Visual Studio 2013

Si votre application a été générée à l’aide d’un modèle d’application web dans Visual Studio 2013 et que vous avez sélectionné **Comptes professionnels** dans le menu **Modifier l’authentification**, elle possède déjà la logique nécessaire pour gérer la substitution de clé. Cette logique stocke l’identifiant unique de votre organisation et les informations de clé de signature dans deux tables de base de données associées au projet. Vous trouverez la chaîne de connexion de la base de données dans le fichier Web.config du projet.

Si vous avez ajouté manuellement l’authentification à votre solution, votre application ne possède pas la logique de substitution de clé nécessaire. Vous devez écrire vous-même cette logique, ou bien suivre les étapes décrites dans la section « Récupérer manuellement la clé la plus récente et mettre à jour votre application ».

Les étapes suivantes vous aideront à vérifier le bon fonctionnement de la logique de votre application.

1. Dans Visual Studio 2013, ouvrez la solution, puis cliquez sur l’onglet **Explorateur de serveurs** dans la fenêtre de droite.
2. Développez **Connexions de données**, **DefaultConnection**, puis **Tables**. Recherchez la table **IssuingAuthorityKeys**, faites un clic droit, puis cliquez sur **Afficher les données de la table**.
3. La table **IssuingAuthorityKeys** comporte au moins une ligne correspondant à la valeur de l’empreinte numérique de la clé. Supprimez toutes les lignes de la table.
4. Cliquez avec le bouton droit sur la table **Clients**, puis cliquez sur **Afficher les données de la table**.
5. La table **Clients** comporte au moins une ligne correspondant à un identifiant unique du client de répertoire. Supprimez toutes les lignes de la table. Si vous ne supprimez pas les lignes à la fois dans la table **Clients** et dans la table **IssuingAuthorityKeys**, vous obtiendrez une erreur au moment de l’exécution.
6. Générez et exécutez l'application. Une fois connecté à votre compte, vous pouvez arrêter l’application.
7. Revenez à **l’Explorateur de serveurs** et examinez les valeurs contenues dans les tables **IssuingAuthorityKeys** et **Clients**. Vous remarquerez qu’elles ont été automatiquement complétées avec les informations appropriées extraites du document de métadonnées de fédération.

### Applications web créées avec Visual Studio 2012

Si votre application a été créée dans Visual Studio 2012, vous avez probablement utilisé l’outil Identité et accès pour configurer votre application. Il est également probable que vous utilisiez le [registre de validation de nom d’émetteur (VINR)](https://msdn.microsoft.com/library/dn205067.aspx). Le VINR est chargé de conserver les informations relatives aux fournisseurs d’identité approuvés (Azure AD) ainsi que les clés utilisées pour valider les jetons qu’ils émettent. Le VINR permet également de mettre à jour automatiquement les informations de clé stockées dans un fichier Web.config en téléchargeant le dernier document de métadonnées de fédération associé à votre annuaire, en vérifiant si la configuration est à jour avec le dernier document et en mettant à jour l’application pour utiliser la nouvelle clé si nécessaire.

Si vous avez créé votre application à l’aide des exemples de code ou de la documentation détaillée fournis par Microsoft, la logique de substitution de clé est déjà incluse dans votre projet. Vous remarquerez que le code ci-dessous existe déjà dans votre projet. Si votre application ne contient pas déjà cette logique, suivez les étapes ci-dessous pour l’ajouter et vérifier qu’elle fonctionne correctement.

1. Dans **l’Explorateur de solutions**, ajoutez une référence à l’assembly **System.IdentityModel** correspondant au projet approprié.
2. Ouvrez le fichier **Global.asax.cs** et ajoutez le code suivant en utilisant les directives :
```
using System.Configuration;
using System.IdentityModel.Tokens;
```
3. Ajoutez la méthode suivante au fichier **Global.asax.cs** :
```
protected void RefreshValidationSettings()
{
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
}
```
4. Appelez la méthode **RefreshValidationSettings()** dans la méthode **Application\_Start()** dans **Global.asax.cs** comme indiqué :
```
protected void Application_Start()
{
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
}
```

Une fois ces étapes effectuées, le fichier Web.config de votre application sera mis à jour avec les dernières informations du document de métadonnées de fédération, y compris les clés les plus récentes. Cette mise à jour sera effectuée chaque fois que votre pool d’applications sera recyclé dans IIS. Par défaut, IIS est configuré pour recycler les applications toutes les 29 heures.

Suivez les étapes ci-dessous pour vérifier que la logique de substitution de clé fonctionne correctement.

1. Vérifiez que votre application utilise bien le code ci-dessus, puis ouvrez le fichier **Web.config** et accédez au bloc **<issuerNameRegistry>**. Examinez plus attentivement les quelques lignes suivantes :
```
<issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
```
2. Dans le paramètre **<add thumbprint=””>**, modifiez la valeur de l’empreinte numérique en remplaçant l’un des caractères par un caractère différent. Enregistrez le fichier **Web.config**.

3. Générez puis exécutez l’application. Si vous pouvez terminer le processus de connexion, votre application mettra correctement à jour la clé en téléchargeant les informations requises à partir du document de métadonnées de fédération de votre répertoire. Si vous rencontrez des problèmes de connexion, vérifiez que les modifications apportées à votre application sont correctes. Pour cela, consultez la rubrique [Ajout de l’authentification à votre application web à l’aide Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect), ou téléchargez et examinez l’exemple de code suivant : [Multi-Tenant Cloud Application for Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b) (Application cloud mutualisée pour Azure Active Directory).


### Applications web créées avec Visual Studio 2008 ou 2010 et avec Windows Identity Foundation (WIF) version 1.0 pour .NET 3.5

Si vous avez créé une application sur WIF v1.0, aucun mécanisme n’est prévu pour actualiser automatiquement la configuration de votre application afin de permettre l’utilisation d’une nouvelle clé. Le moyen le plus simple pour mettre à jour la clé consiste à utiliser les outils FedUtil inclus dans le SDK WIF, qui permettent de récupérer le dernier document de métadonnées et de mettre à jour votre configuration. Ces instructions sont fournies ci-dessous. Vous pouvez également effectuer une des opérations suivantes :

- Suivez les instructions contenues dans la section « Récupérer manuellement la clé la plus récente et mettre à jour votre application » ci-dessous et écrivez la logique permettant d’effectuer les opérations par le biais d’un programme.
- Mettez à jour votre application vers .NET 4.5, qui inclut la dernière version de WIF contenue dans l’espace de noms système. Vous pouvez ensuite utiliser le [registre de validation de nom d’émetteur (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) pour mettre à jour automatiquement la configuration de l’application.


1. Vérifiez que le SDK WIF v1.0 est installé sur votre ordinateur de développement pour Visual Studio 2008 ou 2010. Dans le cas contraire, vous pouvez [le télécharger ici](https://www.microsoft.com/fr-FR/download/details.aspx?id=4451).
2. Dans Visual Studio, ouvrez la solution, puis cliquez avec le bouton droit sur le projet applicable et sélectionnez **Update federation metadata** (Mettre à jour les métadonnées de fédération). Si cette option n’est pas disponible, cela signifie que l’outil FedUtil et/ou le SDK WIF v1.0 n’a pas été installé.
3. À l’invite, sélectionnez **Mettre à jour** pour mettre à jour vos métadonnées de fédération. Si vous avez accès à l’environnement serveur sur lequel l’application est hébergée, vous pouvez éventuellement utiliser le [planificateur de mise à jour automatique des métadonnées](https://msdn.microsoft.com/library/ee517272.aspx) de FedUtil.
4. Cliquez sur **Terminer** pour terminer le processus de mise à jour.

### API web utilisant les jetons web JSON (JWT)

Si votre application appelle une API à l’aide d’un jeton JWT émis par Azure AD pour autoriser la demande, le jeton JWT est validé de la même façon qu’une demande d’authentification, c’est-à-dire en utilisant la clé publique d’Azure AD pour vérifier la signature. Les applications API web doivent être préparées à gérer la substitution de clé, car elles utilisent le même certificat X509 pour signer le jeton.

Si vous avez créé une application API web dans Visual Studio 2013 à l’aide du modèle API web et que vous avez sélectionné **Comptes professionnels** dans le menu **Modifier l’authentification**, votre application contient déjà la logique nécessaire. Si vous avez configuré l’authentification manuellement, suivez les instructions ci-dessous pour savoir comment configurer votre API web afin de mettre automatiquement à jour ses informations de clé.

L’extrait de code suivant montre comment obtenir les clés les plus récentes à partir du document de métadonnées de fédération, puis comment utiliser le [Gestionnaire de jetons JWT](https://msdn.microsoft.com/library/dn205065.aspx) pour valider le jeton. Cet extrait de code suppose que vous allez utiliser votre propre mécanisme de mise en cache pour conserver la clé afin de valider les prochains jetons d’Azure AD (dans une base de données, dans le fichier de configuration ou à un autre emplacement).

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Classic Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### Récupérer manuellement la clé la plus récente et mettre à jour votre application

Si votre plate-forme ou votre type d’application ne prend en charge aucun mécanisme permettant de mettre automatique à jour les clés, vous pouvez effectuer les opérations suivantes :

1. Dans un navigateur web, accédez à https://manage.windowsazure.com, connectez-vous à votre compte, puis cliquez sur l’icône Active Directory à partir du menu de gauche.
2. Cliquez sur le répertoire dans lequel votre application est enregistrée, puis cliquez sur le lien **Afficher les points de terminaison** sur la barre de commandes.
3. Dans la liste des points de terminaison d’authentification unique et de répertoire, copiez le lien **Document de métadonnées de fédération**.
4. Ouvrez un nouvel onglet dans votre navigateur et accédez à l’URL que vous venez de copier. Vous accédez alors au contenu du document XML des métadonnées de fédération. Pour plus d’informations sur ce document, consultez la rubrique Métadonnées de fédération.
5. Pour mettre à jour une application afin qu’elle utilise une nouvelle clé, localisez chaque **<RoleDescriptor>** bloque et copiez la valeur de l’élément **<X509Certificate>** de chaque bloc. Par exemple :
```
<RoleDescriptor xmlns:fed="http://docs.oasis-open.org/wsfed/federation/200706" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" protocolSupportEnumeration="http://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType">
      <KeyDescriptor use="signing">
            <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
                <X509Data>
                    <X509Certificate>MIIDPjC…BcXWLAIarZ</X509Certificate>
```
6. Une fois que vous avez copié la valeur de l’élément **<X509Certificate>**, collez cette valeur dans un éditeur de texte. Veillez à supprimer les espaces de fin et à enregistrer le fichier avec l’extension **.cer**.

Vous venez de créer le certificat X509 qui est utilisé en tant que clé publique pour Azure AD. En utilisant les informations du certificat (par exemple, son empreinte numérique et sa date d’expiration), vous pouvez, soit manuellement soit à l’aide d’un programme, vérifier la validité du certificat et de l’empreinte numérique actuellement utilisés par votre application.

<!---HONumber=AcomDC_0608_2016-->
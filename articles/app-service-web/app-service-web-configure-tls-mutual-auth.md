<properties 
	pageTitle="Configuration de l'authentification mutuelle TLS pour une application Web" 
	description="Découvrez comment configurer votre application Web pour utiliser l'authentification par certificat client sur TLS." 
	services="app-service" 
	documentationCenter="" 
	authors="naziml" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/15/2015" 
	ms.author="naziml"/>

# Configuration de l'authentification mutuelle TLS pour une application Web

## Vue d'ensemble ##
Vous pouvez restreindre l'accès à votre application Web Azure en activant différents types d'authentification. Une méthode consiste à authentifier à l'aide d'un certificat client lorsque la requête est exécutée sur TLS/SSL. Ce mécanisme est appelé authentification mutuelle TLS ou authentification par certificat client. Cet article décrit en détail comment configurer votre application Web pour utiliser l'authentification par certificat client.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Configuration d'une application Web pour l'authentification par certificat client ##
Pour configurer votre application Web afin qu'elle exige des certificats clients, vous devez ajouter le paramètre de site clientCertEnabled pour votre application Web et lui donner la valeur true. Ce paramètre n'est actuellement pas disponible par le biais de l'expérience de gestion dans le portail et vous devrez utiliser l'API REST pour y parvenir.

Vous pouvez utiliser l'[outil ARMClient](https://github.com/projectkudu/ARMClient) pour faciliter l’élaboration de l’appel de l’API REST. Une fois que vous êtes connecté avec l'outil, vous devez émettre la commande suivante :

    ARMClient PUT subscriptions/{Subscription Id}/resourcegroups/{Resource Group Name}/providers/Microsoft.Web/sites/{Website Name}?api-version=2015-04-01 @enableclientcert.json -verbose
    
en remplaçant tous les éléments {} par des informations de votre application Web et en créant un fichier appelé enableclientcert.json avec le contenu JSON suivant :

> { "location": "My Web App Location", "properties": { "clientCertEnabled": true } }


Veillez à modifier la valeur de « location » par l'emplacement de votre application Web ; par exemple, Nord du centre des États-Unis ou Ouest des États-Unis, etc.


## Accès au certificat client à partir de votre application Web ##
Lorsque votre application Web est configurée pour utiliser l'authentification par certificat client, celui-ci sera disponible dans votre application par le biais d'une valeur codée en base64 dans l'en-tête de la requête « X-ARR-ClientCert ». Votre application peut créer un certificat à partir de cette valeur et ensuite l'utiliser à des fins d'authentification et d'autorisation dans votre application.

## Considérations spéciales pour la validation de certificat ##
Le certificat client qui est envoyé à l'application n'est soumis à aucune validation par la plateforme Azure Web Apps. La validation de ce certificat est la responsabilité de l'application Web. Voici un exemple de code ASP.NET qui valide les propriétés du certificat pour l'authentification.

    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class cert : System.Web.UI.Page
        {
            public string certHeader = "";
            public string errorString = "";
            private X509Certificate2 certificate = null;
            public string certThumbprint = "";
            public string certSubject = "";
            public string certIssuer = "";
            public string certSignatureAlg = "";
            public string certIssueDate = "";
            public string certExpiryDate = "";
            public bool isValidCert = false;

            //
            // Read the certificate from the header into an X509Certificate2 object
            // Display properties of the certificate on the page
            //
            protected void Page_Load(object sender, EventArgs e)
            {
                NameValueCollection headers = base.Request.Headers;
                certHeader = headers["X-ARR-ClientCert"];
                if (!String.IsNullOrEmpty(certHeader))
                {
                    try
                    {
                        byte[] clientCertBytes = Convert.FromBase64String(certHeader);
                        certificate = new X509Certificate2(clientCertBytes);
                        certSubject = certificate.Subject;
                        certIssuer = certificate.Issuer;
                        certThumbprint = certificate.Thumbprint;
                        certSignatureAlg = certificate.SignatureAlgorithm.FriendlyName;
                        certIssueDate = certificate.NotBefore.ToShortDateString() + " " + certificate.NotBefore.ToShortTimeString();
                        certExpiryDate = certificate.NotAfter.ToShortDateString() + " " + certificate.NotAfter.ToShortTimeString();
                    }
                    catch (Exception ex)
                    {
                        errorString = ex.ToString();
                    }
                    finally 
                    {
                        isValidCert = IsValidClientCertificate();
                        if (!isValidCert) Response.StatusCode = 403;
                        else Response.StatusCode = 200;
                    }
                }
                else
                {
                    certHeader = "";
                }
            }

            //
            // This is a SAMPLE verification routine. Depending on your application logic and security requirements, 
            // you should modify this method
            //
            private bool IsValidClientCertificate()
            {
                // In this example we will only accept the certificate as a valid certificate if all the conditions below are met:
                // 1. The certificate is not expired and is active for the current time on server.
                // 2. The subject name of the certificate has the common name nildevecc
                // 3. The issuer name of the certificate has the common name nildevecc and organization name Microsoft Corp
                // 4. The thumbprint of the certificate is 30757A2E831977D8BD9C8496E4C99AB26CB9622B
                //
                // This example does NOT test that this certificate is chained to a Trusted Root Authority (or revoked) on the server 
                // and it allows for self signed certificates
                //

                if (certificate == null || !String.IsNullOrEmpty(errorString)) return false;
                
                // 1. Check time validity of certificate
                if (DateTime.Compare(DateTime.Now, certificate.NotBefore) < 0 && DateTime.Compare(DateTime.Now, certificate.NotAfter) > 0) return false;
                
                // 2. Check subject name of certificate
                bool foundSubject = false;
                string[] certSubjectData = certificate.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certSubjectData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundSubject = true;
                        break;
                    }
                }
                if (!foundSubject) return false;

                // 3. Check issuer name of certificate
                bool foundIssuerCN = false, foundIssuerO = false;
                string[] certIssuerData = certificate.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certIssuerData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundIssuerCN = true;
                        if (foundIssuerO) break;
                    }

                    if (String.Compare(s.Trim(), "O=Microsoft Corp") == 0)
                    {
                        foundIssuerO = true;
                        if (foundIssuerCN) break;
                    }
                }

                if (!foundIssuerCN || !foundIssuerO) return false;

                // 4. Check thumprint of certificate
                if (String.Compare(certificate.Thumbprint.Trim().ToUpper(), "30757A2E831977D8BD9C8496E4C99AB26CB9622B") != 0) return false;

                // If you also want to test if the certificate chains to a Trusted Root Authority you can uncomment the code below
                //
                //X509Chain certChain = new X509Chain();
                //certChain.Build(certificate);
                //bool isValidCertChain = true;
                //foreach (X509ChainElement chElement in certChain.ChainElements)
                //{
                //    if (!chElement.Certificate.Verify())
                //    {
                //        isValidCertChain = false;
                //        break;
                //    }
                //}
                //if (!isValidCertChain) return false;

                return true;
            }
        }
    }

<!---HONumber=Oct15_HO3-->
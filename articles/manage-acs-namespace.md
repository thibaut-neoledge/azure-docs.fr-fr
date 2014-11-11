<properties linkid="manage-services-manage-acs" urlDisplayName="Manage ACS" pageTitle="Access Control Service - Azure service management" metaKeywords="" description="Learn how to manage your Azure Access Control Service (ACS) using certificates and keys." metaCanonical="" services="active-directory" documentationCenter="" title="Managing Your ACS Namespace" authors="mbaldwin" solutions="" manager="mbaldwin" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="mbaldwin" />

# Gestion de votre espace de noms ACS

Cette rubrique décrit les tâches de gestion qu'il est recommandé d'exécuter régulièrement pour que les applications qui utilisent Azure Access Control Service (ACS) continuent de fonctionner correctement et sans interruption. Ces tâches de gestion sont les suivantes :

1.  Important : effectuez le suivi de l'expiration et exécutez la substitution pour les certificats, les clés et les mots de passe utilisés par l'espace de noms ACS, les applications par partie de confiance, les identités de service, les fournisseurs d'identité et le compte du service de gestion ACS. Pour plus d'informations, reportez-vous aux instructions relatives à la gestion des certificats et des clés.

2.  Passez en revue vos fournisseurs d'identité, identités de service, règles et administrateurs de portail, et supprimez ceux qui sont obsolètes.

Pour plus d'informations sur ACS, consultez la page [Access Control Service 2.0][Access Control Service 2.0].

## Instructions relatives à la gestion des certificats et des clés

Pour des raisons de sécurité, l'expiration des certificats et des clés utilisés dans ACS est garantie. Il est important d'effectuer le suivi des dates d'expiration pour que ces certificats et ces clés puissent être renouvelés.

Les étapes générales pour la substitution d'une signature de jeton (clé symétrique ou certificat X.509) ou d'un certificat de déchiffrement de jeton sont les suivantes :

1.  Configurez le nouveau certificat ou la nouvelle clé dans ACS en tant que clé « secondaire », à côté du certificat ou de la clé existant, qui expirera.
2.  Avertissez les partenaires utilisateurs du service qu'ils doivent mettre à jour leurs clés correspondantes avant une certaine échéance.
3.  Les partenaires doivent mettre à jour le certificat ou la clé correspondant pour leurs parties de confiance ou fournisseurs d'identité. Par exemple, importez les métadonnées de WS-Federation mises à jour pour l'espace de noms ACS qui contient le nouveau certificat de validation de la signature de jeton, ou configurez manuellement la clé symétrique dans la configuration de l'application.
4.  Une fois que toutes les applications ont été mises à jour (ou lorsque l'échéance est arrivée à son terme), marquez le nouveau certificat ou la nouvelle clé comme primaire dans la configuration d'ACS.
5.  Après une période de grâce raisonnable, supprimez l'ancien certificat ou l'ancienne clé de la configuration d'ACS.

Les étapes générales pour la substitution des certificats de chiffrement de jeton sont les suivantes :

1.  Vous (ou vos partenaires) mettez à jour le certificat ou la clé correspondant qui est utilisé pour le déchiffrement de jeton dans les applications par partie de confiance.
2.  Configurez le nouveau certificat de chiffrement dans ACS, à côté du certificat existant, qui expirera.
3.  Supprimez l'ancien certificat de chiffrement.

Les étapes générales pour la substitution des clés d'identité du service ou du service de gestion sont les suivantes :

1.  Configurez le nouveau certificat ou la nouvelle clé dans ACS, à côté du certificat ou de la clé existant, qui expirera.
2.  Vous (ou vos partenaires) mettez à jour le certificat ou la clé correspondant qui est utilisé pour les demandes de jeton dans les applications clientes.
3.  Une fois que tous les clients ont été mis à jour (ou après une période de grâce raisonnable), supprimez l'ancien certificat ou l'ancienne clé.

Lorsqu'un certificat ou une clé expire, ACS ne réussit pas à émettre des jetons, empêchant votre partie de confiance de fonctionner normalement. Les certificats et les clés arrivés à expiration sont ignorés par ACS, ce qui provoque des exceptions, comme si aucun certificat ou clé n'avait été configuré initialement. Les sections suivantes fournissent des informations sur les différents certificats et clés gérés par ACS, sur leur renouvellement et sur la procédure à suivre pour déterminer s'ils ont expiré et doivent être renouvelés.

-   Utilisez la section Certificats et clés du portail de gestion ACS pour gérer les certificats et les clés relatifs à l'espace de noms du service et aux applications par partie de confiance. Pour plus d'informations sur ces types d'informations d'identification, consultez la page [Certificats et clés][Certificats et clés].
-   Utilisez la section Identités de service du portail de gestion ACS pour gérer les informations d'identification (certificats, clés ou mots de passe) relatives aux identités de service. Pour plus d'informations sur les identités de service, consultez la page [Identités de service][Identités de service].
-   Utilisez la section Service de gestion du portail de gestion ACS pour gérer les informations d'identification (certificats, clés ou mots de passe) relatives aux comptes du service de gestion ACS. Pour plus d'informations sur le service de gestion ACS, consultez la page [Service de gestion ACS][Service de gestion ACS].

Certains types de certificats et de clés ne sont pas visibles dans le portail de gestion ACS. En particulier pour les fournisseurs d'identité WS-Federation tels que AD FS, vous devez contrôler de façon proactive la validité des certificats utilisés par les fournisseurs d'identité. Actuellement, les certificats disponibles par l'intermédiaire des métadonnées des fournisseurs d'identité WS-Federation ne sont pas visibles sur le portail de gestion ACS. Pour vérifier la validité des certificats, vous devez utiliser le service de gestion afin d'examiner les dates d'effet et d'expiration pour les propriétés StartDate et EndDate de [IdentityProviderKey][IdentityProviderKey]. Lorsque le certificat ou une clé expire et devient par conséquent non valide, ACS commence à lever des exceptions [Codes d'erreur ACS][Codes d'erreur ACS] propres au certificat ou à la clé. Pour les codes d'erreur spécifiques, consultez les sections ci-dessous.

Vous pouvez mettre à jour les certificats et les clés par programme à l'aide du [service de gestion ACS][Service de gestion ACS]. Pensez à examiner l'exemple de code KeyManagement disponible en téléchargement dans le cadre de l'[Exemple de code : service de gestion][Exemple de code : service de gestion].

## Certificats et clés disponibles

La liste suivante affiche les certificats et clés disponibles qui sont utilisés dans ACS et pour lesquels vous devez effectuer le suivi des dates d'expiration :

-   Certificats de signature de jeton
-   Clés de signature de jeton
-   Certificats de chiffrement de jeton
-   Certificats de déchiffrement de jeton
-   Informations d'identification de service
-   Informations d'identification de compte du service de gestion ACS
-   Certificats de signature et de chiffrement du fournisseur d'identité

La suite de cette rubrique couvre en détail les différents certificats et clés.

## Certificats de signature de jeton

ACS signe tous les jetons de sécurité qu'il émet. Des certificats X.509 sont utilisés à des fins de signature lorsque vous créez une application qui utilise des jetons SAML émis par ACS.

Vous pouvez gérer les certificats de signature de jeton via la section Certificats et clés du portail de gestion ACS.

**Pour gérer les certificats de signature de jeton**

1.  Ouvrez un navigateur Internet et rendez-vous sur le portail de gestion Azure ([][]<http://go.microsoft.com/fwlink/?LinkID=129428></a>).

2.  Connectez-vous au site web en utilisant un Windows Live ID. Si vous n'en possédez pas, cliquez sur Inscrivez-vous maintenant pour en créer un.

3.  Une fois connecté avec votre Windows Live ID, vous êtes redirigé vers la page du portail de gestion. Dans la partie inférieure gauche de cette page, cliquez sur **Service Bus and Access Control**.

    ![][0]

4.  Pour lancer le portail de gestion ACS, cliquez sur **Access Control** dans l'arborescence affichée sur le côté gauche, sélectionnez l'espace de noms du service ACS que vous voulez configurer, puis cliquez sur le bouton **Access Control Service** dans la barre d'outils en haut de la page.

    ![][1]

    À ce stade, votre écran a normalement l'aspect suivant :

    ![][2]

5.  Cliquez sur **Certificats et clés** dans l'arborescence affichée sur le côté gauche sous la section Service Settings.

    ![][3]

    À ce stade, votre écran a normalement l'aspect suivant :

    ![][4]

6.  Sous la section Token Signing, utilisez le bouton Ajouter pour configurer le nouveau certificat dans ACS en tant que clé « secondaire », à côté du certificat existant, qui expirera.

7.  Avertissez les partenaires utilisateurs du service qu'ils doivent mettre à jour leurs clés correspondantes avant une certaine échéance.

8.  Les partenaires doivent mettre à jour le certificat correspondant pour leurs parties de confiance ou fournisseurs d'identité. Par exemple, importez les métadonnées de WS-Federation mises à jour pour l'espace de noms ACS qui contient le nouveau certificat de validation de la signature de jeton, ou configurez manuellement la clé symétrique dans la configuration de l'application.

9.  Une fois que toutes les applications ont été mises à jour (ou lorsqu'une échéance est arrivée à son terme), marquez le nouveau certificat comme primaire dans la configuration d'ACS.

10. Après une période de grâce raisonnable, utilisez le bouton Supprimer sous la section Token Signing de la page Certificats et clés pour supprimer l'ancien certificat de la configuration d'ACS.

Pour plus d'informations, consultez la page [Certificats et clés][Certificats et clés].

Une fois que les certificats de signature ont expiré, vous recevez les erreurs suivantes lorsque vous essayez de demander un jeton :

<table><tr><td><b>Code d'erreur</b></td>
<td><b>Message</b></td>
<td><b>Action requise pour corriger le message</b></td>
</tr>
<tr>
<td>ACS50004</td>
<td>Aucun certificat de signature X.509 primaire n'est configuré. Un certificat de signature est requis pour SAML.</td>
<td>Si la partie de confiance choisie utilise SAML comme type de jeton, assurez-vous qu'un certificat X.509 valide est configuré pour la partie de confiance ou l'espace de noms du service. Le certificat doit être défini sur primaire et doit être dans sa période de validité.</td></tr>
</table> 

## Clé de signature de jeton

ACS signe tous les jetons de sécurité qu'il émet. Des clés de signature symétrique de 256 bits sont utilisées lorsque vous créez une application qui utilise des jetons SWT émis par ACS.

Vous pouvez gérer les clés de signature de jeton via la section Certificats et clés du portail de gestion ACS.

**Pour gérer une clé de signature de jeton**

1.  Ouvrez un navigateur Internet et rendez-vous sur le portail de gestion Azure ([][]<http://go.microsoft.com/fwlink/?LinkID=129428></a>).

2.  Connectez-vous au site web en utilisant un Windows Live ID. Si vous n'en possédez pas, cliquez sur Inscrivez-vous maintenant pour en créer un.

3.  Une fois connecté avec votre Windows Live ID, vous êtes redirigé vers la page du portail de gestion. Dans la partie inférieure gauche de cette page, cliquez sur **Service Bus and Access Control**.

    ![][0]

4.  Pour lancer le portail de gestion ACS, cliquez sur **Access Control** dans l'arborescence affichée sur le côté gauche, sélectionnez l'espace de noms du service ACS que vous voulez configurer, puis cliquez sur le bouton **Access Control Service** dans la barre d'outils en haut de la page.

    ![][1]

    À ce stade, votre écran a normalement l'aspect suivant :

    ![][2]

5.  Cliquez sur **Certificats et clés** dans l'arborescence affichée sur le côté gauche sous la section Service Settings.

    ![][3]

    À ce stade, votre écran a normalement l'aspect suivant :

    ![][4]

6.  Sous la section Token Signing, utilisez le bouton Ajouter pour configurer la nouvelle clé dans ACS en tant que clé « secondaire », à côté de la clé existante, qui expirera.

7.  Avertissez les partenaires utilisateurs du service qu'ils doivent mettre à jour leurs clés correspondantes avant une certaine échéance.

8.  Les partenaires doivent mettre à jour la clé correspondante pour leurs parties de confiance ou fournisseurs d'identité. Par exemple, importez les métadonnées de WS-Federation mises à jour pour l'espace de noms ACS qui contient le nouveau certificat de validation de la signature de jeton, ou configurez manuellement la clé symétrique dans la configuration de l'application.

9.  Une fois que toutes les applications ont été mises à jour (ou lorsqu'une échéance est arrivée à son terme), marquez la nouvelle clé comme primaire dans la configuration d'ACS.

10. Après une période de grâce raisonnable, utilisez le bouton Supprimer sous la section Token Signing de la page Certificats et clés pour supprimer l'ancienne clé de la configuration d'ACS.

Pour plus d'informations, consultez la page [Certificats et clés][Certificats et clés].

Une fois que les clés de signature ont expiré, vous recevez les erreurs suivantes lorsque vous essayez de demander un jeton :

<table><tr><td><b>Code d'erreur</b></td>
<td><b>Message</b></td>
<td><b>Action requise pour corriger le message</b></td>
</tr>
<tr>
<td>ACS50003</td>
<td>Aucune clé de signature symétrique primaire n'est configurée. Une clé de signature symétrique est requise pour SWT.</td>
<td>Si la partie de confiance choisie utilise SWT comme type de jeton, assurez-vous qu'une clé symétrique est configurée pour la partie de confiance ou l'espace de noms du service, que la clé est définie sur primaire et qu'elle est dans sa période de validité.</td></tr>
</table> 

## Certificats de chiffrement de jeton

Le chiffrement de jeton est obligatoire si l’application par partie de confiance est un service Web utilisant des jetons preuves de possession dans le protocole WS-Trust ; autrement, le chiffrement de jeton est facultatif.

Vous pouvez gérer les certificats de chiffrement de jeton via la section Certificats et clés du portail de gestion ACS.

**Pour gérer les certificats de chiffrement de jeton**

1.  Ouvrez un navigateur Internet et rendez-vous sur le portail de gestion Azure ([][]<http://go.microsoft.com/fwlink/?LinkID=129428></a>).

2.  Connectez-vous au site web en utilisant un Windows Live ID. Si vous n'en possédez pas, cliquez sur Inscrivez-vous maintenant pour en créer un.

3.  Une fois connecté avec votre Windows Live ID, vous êtes redirigé vers la page du portail de gestion. Dans la partie inférieure gauche de cette page, cliquez sur **Service Bus and Access Control**.

    ![][0]

4.  Pour lancer le portail de gestion ACS, cliquez sur **Access Control** dans l'arborescence affichée sur le côté gauche, sélectionnez l'espace de noms du service ACS que vous voulez configurer, puis cliquez sur le bouton **Access Control Service** dans la barre d'outils en haut de la page.

    ![][1]

    À ce stade, votre écran a normalement l'aspect suivant :

    ![][2]

5.  Cliquez sur **Certificats et clés** dans l'arborescence affichée sur le côté gauche sous la section Service Settings.

    ![][3]

    À ce stade, votre écran a normalement l'aspect suivant :

    ![][5]

6.  Vous (ou vos partenaires) mettez à jour le certificat ou la clé correspondant qui est utilisé pour le déchiffrement de jeton dans les applications par partie de confiance.
7.  Utilisez le bouton Ajouter pour configurer le nouveau certificat de chiffrement dans ACS, à côté du certificat existant, qui expirera.
8.  Utilisez le bouton Supprimer pour supprimer l'ancien certificat de chiffrement.

Pour plus d'informations, consultez la page [Certificats et clés][Certificats et clés].

Une fois que les certificats de chiffrement ont expiré, vous recevez les erreurs suivantes lorsque vous essayez de demander un jeton :

<table><tr><td><b>Code d'erreur</b></td>
<td><b>Message</b></td>
<td><b>Action requise pour corriger le message</b></td>
</tr>
<tr>
<td>ACS50005</td>
<td>Le chiffrement de jeton est requis, mais aucun certificat de chiffrement n'est configuré pour la partie de confiance.</td>
<td>Désactivez le chiffrement de jeton pour la partie de confiance choisie ou chargez le certificat X.509 à utiliser pour le chiffrement de jeton.</td></tr>
</table> 

## Certificats de déchiffrement de jeton

ACS peut accepter les jetons chiffrés provenant de fournisseurs d'identité WS-Federation (par exemple, AD FS 2.0). Un certificat X.509 hébergé dans ACS est utilisé pour le déchiffrement.

Vous pouvez gérer les certificats de déchiffrement de jeton via la section Certificats et clés du portail de gestion ACS.

**Pour gérer les certificats de déchiffrement de jeton**

1.  Ouvrez un navigateur Internet et rendez-vous sur le portail de gestion Azure ([][]<http://go.microsoft.com/fwlink/?LinkID=129428></a>).

2.  Connectez-vous au site web en utilisant un Windows Live ID. Si vous n'en possédez pas, cliquez sur Inscrivez-vous maintenant pour en créer un.

3.  Une fois connecté avec votre Windows Live ID, vous êtes redirigé vers la page du portail de gestion. Dans la partie inférieure gauche de cette page, cliquez sur **Service Bus and Access Control**.

    ![][0]

4.  Pour lancer le portail de gestion ACS, cliquez sur **Access Control** dans l'arborescence affichée sur le côté gauche, sélectionnez l'espace de noms du service ACS que vous voulez configurer, puis cliquez sur le bouton **Access Control Service** dans la barre d'outils en haut de la page.

    ![][1]

    À ce stade, votre écran a normalement l'aspect suivant :

    ![][2]

5.  Cliquez sur **Certificats et clés** dans l'arborescence affichée sur le côté gauche sous la section Service Settings.

    ![][3]

    À ce stade, votre écran a normalement l'aspect suivant :

    ![][6]

6.  Sous la section Token decryption, utilisez le bouton Ajouter pour configurer le nouveau certificat dans ACS en tant que clé « secondaire », à côté du certificat existant, qui expirera.

7.  Avertissez les partenaires utilisateurs du service qu'ils doivent mettre à jour leurs clés correspondantes avant une certaine échéance.

8.  Les partenaires doivent mettre à jour le certificat correspondant pour leurs parties de confiance ou fournisseurs d'identité. Par exemple, importez les métadonnées de WS-Federation mises à jour pour l'espace de noms ACS qui contient le nouveau certificat de validation de la signature de jeton, ou configurez manuellement la clé symétrique dans la configuration de l'application.

9.  Une fois que toutes les applications ont été mises à jour (ou lorsqu'une échéance est arrivée à son terme), marquez le nouveau certificat comme primaire dans la configuration d'ACS.

10. Après une période de grâce raisonnable, utilisez le bouton Supprimer sous la section Token Signing de la page Certificats et clés pour supprimer l'ancien certificat de la configuration d'ACS.

Pour plus d'informations, consultez la page [Certificats et clés][Certificats et clés].

Une fois que les certificats de déchiffrement ont expiré, vous recevez les erreurs suivantes lorsque vous essayez de demander un jeton :

<table><tr><td><b>Code d'erreur</b></td>
<td><b>Message</b></td>
</tr>
<tr>
<td>ACS10001</td>
<td>Une erreur s'est produite pendant le traitement de l'en-tête SOAP.</td>
</tr>
<tr><td>ACS20001</td>
<td>Une erreur s'est produite pendant le traitement d'une réponse de connexion WS-Federation.</td></tr>
</table> 

## Informations d'identification de service

Les identités de service sont des informations d'identification configurées globalement pour l'espace de noms ACS qui permettent aux applications ou aux clients de s'authentifier directement auprès d'ACS et de recevoir un jeton. Il existe trois types d'informations d'identification auxquels une identité de service ACS peut être associée : clé symétrique, mot de passe et certificat X.509.

Vous pouvez gérer les informations d'identification de service via la page Identités de service du portail de gestion ACS.

**Pour gérer les informations d'identité de service**

1.  Ouvrez un navigateur Internet et rendez-vous sur le portail de gestion Azure ([][]<http://go.microsoft.com/fwlink/?LinkID=129428></a>).

2.  Connectez-vous au site web en utilisant un Windows Live ID. Si vous n'en possédez pas, cliquez sur Inscrivez-vous maintenant pour en créer un.

3.  Une fois connecté avec votre Windows Live ID, vous êtes redirigé vers la page du portail de gestion. Dans la partie inférieure gauche de cette page, cliquez sur **Service Bus and Access Control**.

    ![][0]

4.  Pour lancer le portail de gestion ACS, cliquez sur **Access Control** dans l'arborescence affichée sur le côté gauche, sélectionnez l'espace de noms du service ACS que vous voulez configurer, puis cliquez sur le bouton **Access Control Service** dans la barre d'outils en haut de la page.

    ![][1]

    À ce stade, votre écran a normalement l'aspect suivant :

    ![][2]

5.  Cliquez sur **Identités de service** dans l'arborescence affichée sur le côté gauche sous la section Service Settings.

    ![][7]

6.  Cliquez sur l'identité de service que vous souhaitez modifier.

    ![][8]

7.  Dans la section Informations d'identification, utilisez le bouton Ajouter pour configurer le nouveau certificat ou la nouvelle clé dans ACS, à côté du certificat ou de la clé existant, qui expirera.

8.  Vous (ou vos partenaires) mettez à jour le certificat ou la clé correspondant qui est utilisé pour les demandes de jeton dans les applications clientes.

9.  Une fois que tous les clients ont été mis à jour (ou après une période de grâce raisonnable), supprimez l'ancien certificat ou l'ancienne clé avec le bouton Supprimer.

Pour plus d'informations, consultez la page [Identités de service][Identités de service].

Si les informations d'identification ont expiré, ACS lève l'exception suivante :

<table>
<colgroup>
<col width="25%" />
<col width="25%" />
<col width="25%" />
<col width="25%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><strong>Information d'identification&gt;</strong></td>
<td align="left"><strong>Code d'erreur</strong></td>
<td align="left"><strong>Message</strong></td>
<td align="left"><strong>Action requise pour corriger le message</strong></td>
</tr>
<tr class="even">
<td align="left">Clé symétrique, mot de passe</td>
<td align="left">ACS50006</td>
<td align="left">La vérification de la signature a échoué. (Il est possible que le message contienne plus de détails.)</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Certificat X.509</td>
<td align="left">ACS50016</td>
<td align="left">Le certificat X509 avec l'objet '&lt;Nom d'objet du certificat&gt;' et l'empreinte numérique '&lt;Empreinte numérique du certificat&gt;' ne correspond à aucun certificat configuré.</td>
<td align="left">Assurez-vous que le certificat demandé a été téléchargé vers ACS.</td>
</tr>
</tbody>
</table>

Pour vérifier et mettre à jour les dates d'expiration des clés symétriques ou du mot de passe, ou pour charger un nouveau certificat en tant qu'informations d'identification de service, suivez les instructions décrites dans [Ajout d'identités de service avec certificat X.509, mot de passe ou clé symétrique][Ajout d'identités de service avec certificat X.509, mot de passe ou clé symétrique] Une liste des informations d'identification de service est disponible sur la page Edit Service Identity.

## Informations d'identification du service de gestion

Le service de gestion ACS est un composant clé d'ACS qui vous permet de gérer et de configurer par programme les paramètres dans un espace de noms ACS. Le compte de service de gestion ACS peut être associé à trois types d'informations d'identification. Il s’agit de la clé symétrique, du mot de passe et du certificat X.509.

Vous pouvez gérer les informations d'identification du service de gestion via la page Service de gestion du portail de gestion ACS.

**Pour gérer les informations d'identification du service de gestion ACS**

1.  Ouvrez un navigateur Internet et rendez-vous sur le portail de gestion Azure ([][]<http://go.microsoft.com/fwlink/?LinkID=129428></a>).

2.  Connectez-vous au site web en utilisant un Windows Live ID. Si vous n'en possédez pas, cliquez sur Inscrivez-vous maintenant pour en créer un.

3.  Une fois connecté avec votre Windows Live ID, vous êtes redirigé vers la page du portail de gestion. Dans la partie inférieure gauche de cette page, cliquez sur **Service Bus and Access Control**.

    ![][0]

4.  Pour lancer le portail de gestion ACS, cliquez sur **Access Control** dans l'arborescence affichée sur le côté gauche, sélectionnez l'espace de noms du service ACS que vous voulez configurer, puis cliquez sur le bouton **Access Control Service** dans la barre d'outils en haut de la page.

    ![][1]

    À ce stade, votre écran a normalement l'aspect suivant :

    ![][2]

5.  Cliquez sur **Service de gestion** dans l'arborescence affichée sur le côté gauche sous la section Administration.

    ![][9]

6.  Cliquez sur le compte du service de gestion.

    ![][10]

7.  Dans la section Informations d'identification, utilisez le bouton Ajouter pour configurer le nouveau certificat ou la nouvelle clé dans ACS, à côté du certificat ou de la clé existant, qui expirera.

8.  Vous (ou vos partenaires) mettez à jour le certificat ou la clé correspondant qui est utilisé pour les demandes de jeton dans les applications clientes.

9.  Une fois que tous les clients ont été mis à jour (ou après une période de grâce raisonnable), supprimez l'ancien certificat ou l'ancienne clé avec le bouton Supprimer.

Pour plus d'informations, consultez la page [Service de gestion ACS][Service de gestion ACS].

Si ces informations d'identification ont expiré, ACS lève les exceptions suivantes :

<table>
<colgroup>
<col width="25%" />
<col width="25%" />
<col width="25%" />
<col width="25%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><strong>Information d'identification&gt;</strong></td>
<td align="left"><strong>Code d'erreur</strong></td>
<td align="left"><strong>Message</strong></td>
<td align="left"><strong>Action requise pour corriger le message</strong></td>
</tr>
<tr class="even">
<td align="left">Clé symétrique, mot de passe</td>
<td align="left">ACS50006</td>
<td align="left">La vérification de la signature a échoué. (Il est possible que le message contienne plus de détails.)</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Certificat X.509</td>
<td align="left">ACS50016</td>
<td align="left">Le certificat X509 avec l'objet '&lt;Nom d'objet du certificat&gt;' et l'empreinte numérique '&lt;Empreinte numérique du certificat&gt;' ne correspond à aucun certificat configuré.</td>
<td align="left">Assurez-vous que le certificat demandé a été téléchargé vers ACS.</td>
</tr>
</tbody>
</table>

Une liste des informations d'identification du compte du service de gestion ACS est disponible sur la page Edit Management Service Account du portail de gestion ACS.

## Certificat du fournisseur d'identité WS-Federation

Le certificat du fournisseur d’identité WS-Federation est accessible par l’intermédiaire de ses métadonnées. Lors de la configuration du fournisseur d'identité WS-Federation, tel qu'AD FS, le certificat de signature WS-Federation est configuré par l'intermédiaire des métadonnées de WS-Federation disponibles via l'URL ou sous forme de fichier. Pour plus d'informations, consultez les pages [Fournisseurs d'identité WS-Federation][Fournisseurs d'identité WS-Federation] et [Configuration d'AD FS 2.0 en tant que fournisseur d'identité][Configuration d'AD FS 2.0 en tant que fournisseur d'identité]. Après avoir configuré le fournisseur d'identité WS-Federation dans ACS, utilisez le service de gestion ACS pour l'interroger au sujet de la validité de ses certificats. Notez que pour chaque chargement consécutif des métadonnées via le portail de gestion ACS ou le service de gestion ACS, les clés sont remplacées.

Si le certificat a expiré, ACS lève les exceptions suivantes :

<table><tr><td><b>Code d'erreur</b></td>
<td><b>Message</b></td>
</tr>
<tr>
<td>ACS10001</td>
<td>Une erreur s'est produite pendant le traitement de l'en-tête SOAP.</td>
</tr>
<tr><td>ACS20001</td>
<td>Une erreur s'est produite pendant le traitement d'une réponse de connexion WS-Federation.</td></tr>
<tr><td>ACS50006</td><td>La vérification de la signature a échoué. (Il est possible que le message contienne plus de détails.)</td></tr>
</table> 

  [Access Control Service 2.0]: http://msdn.microsoft.com/library/azure/hh147631.aspx
  [Certificats et clés]: http://msdn.microsoft.com/fr-fr/library/gg185932.aspx
  [Identités de service]: http://msdn.microsoft.com/fr-fr/library/gg185945.aspx
  [Service de gestion ACS]: http://msdn.microsoft.com/fr-fr/library/gg185972.aspx
  [IdentityProviderKey]: http://msdn.microsoft.com/fr-fr/library/hh124084.aspx
  [Codes d'erreur ACS]: http://msdn.microsoft.com/fr-fr/library/gg185949.aspx
  [Exemple de code : service de gestion]: http://msdn.microsoft.com/fr-fr/library/gg185970.aspx
  [0]: http://go.microsoft.com/fwlink/?LinkID=129428
  [0]: ./media/manage-acs-namespace/ACS1.png
  [1]: ./media/manage-acs-namespace/ACS2.png
  [2]: ./media/manage-acs-namespace/ACS3.png
  [3]: ./media/manage-acs-namespace/ACS4.png
  [4]: ./media/manage-acs-namespace/ACS5.png
  [5]: ./media/manage-acs-namespace/ACS7.png
  [6]: ./media/manage-acs-namespace/ACS9.png
  [7]: ./media/manage-acs-namespace/ACS11.png
  [8]: ./media/manage-acs-namespace/ACS112.png
  [Ajout d'identités de service avec certificat X.509, mot de passe ou clé symétrique]: http://msdn.microsoft.com/fr-fr/library/gg185924.aspx
  [9]: ./media/manage-acs-namespace/ACS14.png
  [10]: ./media/manage-acs-namespace/ACS15.png
  [Fournisseurs d'identité WS-Federation]: http://msdn.microsoft.com/fr-fr/library/gg185933.aspx
  [Configuration d'AD FS 2.0 en tant que fournisseur d'identité]: http://msdn.microsoft.com/fr-fr/library/gg185961.aspx

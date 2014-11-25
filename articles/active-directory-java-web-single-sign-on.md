<properties linkid="develop-java-how-to-guides-web-sso" urlDisplayName="Web SSO" pageTitle="Single sign-on with Azure Active Directory (Java)" metaKeywords="Azure Java web app, Azure single sign-on, Azure Java Active Directory" description="Learn how to create a Java web application that uses single sign-on with Azure Active Directory." metaCanonical="" services="active-directory" documentationCenter="Java" title="Web Single Sign-On with Java and Azure Active Directory" authors="mbaldwin" solutions="" manager="mbaldwin" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="mbaldwin" />

# Authentification unique web avec Java et Azure Active Directory

## <a name="introduction"></a>Introduction

Ce didacticiel va expliquer aux développeurs Java comment tirer parti d'Azure Active Directory pour activer l'authentification unique pour les utilisateurs des clients Office 365. Vous apprendrez à :

-   configurer l'application web dans le client d'une société ;
-   protéger l'application avec WS-Federation.

### Conditions préalables

Ce didacticiel utilise un serveur d'applications spécifique, mais si vous êtes un développeur Java expérimenté, vous pouvez appliquer le processus décrit ci-dessous à d'autres environnements. Les éléments de développement suivants sont requis pour ce didacticiel :

-   [Exemple de code Java pour Azure Active Directory][Exemple de code Java pour Azure Active Directory]
-   [Java Runtime Environment 1.6][Java Runtime Environment 1.6]
-   [JBoss Application Server version 7.1.1.Final][JBoss Application Server version 7.1.1.Final]
-   [IDE JBoss Developer Studio][IDE JBoss Developer Studio]
-   IIS (Internet Information Services) 7.5 avec SSL activé
-   Windows PowerShell
-   [Cmdlets PowerShell pour Office 365][Cmdlets PowerShell pour Office 365]

### Sommaire

-   [Introduction][Introduction]
-   [Étape 1 : création d'une application Java][Étape 1 : création d'une application Java]
-   [Étape 2 : configuration de l'application dans le client d'annuaire d'une société][Étape 2 : configuration de l'application dans le client d'annuaire d'une société]
-   [Étape 3 : protection de l'application en utilisant WS-Federation pour la connexion des employés][Étape 3 : protection de l'application en utilisant WS-Federation pour la connexion des employés]
-   [Résumé][Résumé]

## <a name="createapp"></a>Étape 1 : création d'une application Java

Cette étape décrit la création d'une application Java simple qui représentera une source protégée. L'accès à cette ressource sera accordé via une authentification fédérée gérée par le service d'émission de jeton de sécurité (STS) d'une société, qui est décrit ultérieurement dans ce didacticiel.

1.  Ouvrez une nouvelle instance de JBoss Developer Studio.
2.  Dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet...**.
3.  Dans la boîte de dialogue **New Project**, développez le dossier **Maven**, cliquez sur **Maven Project**, puis sur **Suivant**.
4.  Dans la boîte de dialogue **New Maven Project**, cochez la case **Create a simple project (skip archetype selection)**, puis cliquez sur **Suivant**.
5.  Sur la page suivante de la boîte de dialogue **New Maven Project**, tapez *sample* dans les zones de texte **Group Id** et **Artifact Id**. Sélectionnez ensuite **war** dans le menu déroulant **Packaging** et cliquez sur **Finish**.
6.  Le projet Maven est créé. Dans le menu **Project Explorer** sur la gauche, développez le projet **sample**, cliquez avec le bouton droit sur le fichier **pom.xml**, cliquez sur **Open With**, puis sur **Text Editor**.
7.  Dans le fichier **pom.xml**, ajoutez le code XML suivant dans la section *\<project\>* :

        <repositories>
            <repository>
                <id>jboss-public-repository-group</id>
                <name>JBoss Public Maven Repository Group</name>
                <url>https://repository.jboss.org/nexus/content/groups/public-jboss/</url>
                <layout>default</layout>
                <releases>
                    <enabled>true</enabled>
                    <updatePolicy>never</updatePolicy>
                </releases>
                <snapshots>
                    <enabled>true</enabled>
                    <updatePolicy>never</updatePolicy>
                </snapshots>
            </repository>
        </repositories>
        <pluginRepositories>
            <pluginRepository>
                <id>jboss-public-repository-group</id>
                <name>JBoss Public Maven Repository Group</name>
                <url>https://repository.jboss.org/nexus/content/groups/public-jboss/</url>
                <layout>default</layout>
                <releases>
                    <enabled>true</enabled>
                    <updatePolicy>always</updatePolicy>
                </releases>
                <snapshots>
                    <enabled>true</enabled>
                    <updatePolicy>always</updatePolicy>
                </snapshots>
            </pluginRepository>
        </pluginRepositories>
        <build>
            <plugins>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>2.0.2</version>
                    <configuration>
                        <source>1.6</source>
                        <target>1.6</target>
                    </configuration>
                </plugin>
            </plugins>
        </build> 

    Une fois le code XML entré, enregistrez le fichier **pom.xml**.

8.  Cliquez avec le bouton droit sur le projet **sample** et cliquez sur **Maven**, puis sur **Update Maven Project**. Dans la boîte de dialogue **Update Maven Project**, cliquez sur **OK**. Ceci met à jour votre projet en appliquant les modifications apportées au fichier **pom.xml**.

9.  Cliquez avec le bouton droit sur le projet **sample**, cliquez sur **New**, puis sur **JSP File**.

10. Dans la boîte de dialogue **New JSP File**, remplacez le chemin d'accès du nouveau fichier par *sample/src/main/webapp*. Nommez ensuite le fichier **index.jsp**, puis cliquez sur **Finish**.

11. Le nouveau fichier **index.jsp** s'ouvre automatiquement. Remplacez le code généré automatiquement par le code suivant, puis enregistrez le fichier :

        <%@ page language="java" contentType="text/html; charset=ISO-8859-1"  pageEncoding="ISO-8859-1"%>
        <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
        <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
            <title>Index</title>
        </head>
        <body>
            <h3>Index Page</h3>
        </body>
        </html> 

12. Cliquez avec le bouton droit sur le projet **sample** et cliquez sur **Run As**, puis sur **Run on Server**.

13. Dans la boîte de dialogue **Run On Server**, vérifiez que la case à cocher **JBoss Enterprise Application Platform 6.x** est activée, puis cliquez sur **Finished**.

## <a name="provisionapp"></a>Étape 2 : configuration de l'application dans le client d'annuaire d'une société

Cette étape explique comment l'administrateur d'une société utilisant Azure Active Directory met en service l'application Java dans son client et configure l'authentification unique. Une fois cette étape terminée, les employés de la société peuvent s'authentifier pour accéder à l'application web en utilisant leurs comptes Office 365.

Le processus de configuration commence par la création d'un principal du service pour l'application. Azure Active Directory utilise les principaux du service pour enregistrer et authentifier des applications dans l'annuaire.

1.  Si ce n'est pas déjà fait, téléchargez et installez les cmdlets PowerShell pour Office 365.
2.  Dans le menu **Démarrer**, exécutez la console **Module Microsoft Online Services pour Windows PowerShell**. Celle-ci fournit un environnement en ligne de commande permettant de configurer des attributs de votre client Office 365, comme la création et la modification des principaux du service.
3.  Pour importer le module **MSOnlineExtended** requis, entrez la commande suivante, puis appuyez sur Entrée :

        Import-Module MSOnlineExtended -Force

4.  Pour vous connecter à votre annuaire Office 365, vous devez fournir les informations d'identification de l'administrateur de la société. Tapez la commande suivante et appuyez sur Entrée, puis entrez vos informations d'identification lorsque vous y êtes invité :

        Connect-MsolService

5.  ﻿À présent, vous allez créer un principal du service pour l'application. Tapez la commande suivante, puis appuyez sur Entrée :

        New-MsolServicePrincipal -ServicePrincipalNames @("javaSample/localhost") -DisplayName "Federation Sample Website" -Type Symmetric -Usage Verify -StartDate "12/01/2012" -EndDate "12/01/2013" 

    Cette étape affiche des informations similaires aux suivantes :

        The following symmetric key was created as one was not supplied qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=
        DisplayName           : Federation Sample Java Website
        ServicePrincipalNames : {javaSample/localhost}
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

        $replyUrl = New-MsolServicePrincipalAddresses -Address "https://localhost:8443/sample" 

        Set-MsolServicePrincipal -AppPrincipalId "7829c758-2bef-43df-a685-717089474505" -Addresses $replyUrl 

L'application web est à présent configurée dans l'annuaire et peut être utilisée pour l'authentification unique web par les employés de la société.

## <a name="protectapp"></a>Étape 3 : protection de l'application en utilisant WS-Federation pour la connexion des employés

Dans cette étape, vous allez ajouter la prise en charge de la connexion fédérée en utilisant Windows Identity Foundation (WIF) et la bibliothèque **waad-federation** que vous avez téléchargée avec l'exemple de code lorsque vous avez passé en revue la configuration requise. Vous allez également ajouter une page de connexion et configurer l'approbation entre l'application et le client d'annuaire.

1.  Dans JBoss Developer Studio, cliquez sur **File**, puis sur **Import**.

2.  Dans la boîte de dialogue **Import**, développez le dossier **Maven**, cliquez sur **Existing Maven Projects**, puis sur **Next**.

3.  Dans la boîte de dialogue **Import Maven Projects**, définissez le chemin d'accès **Root Directory** sur l'emplacement où vous avez téléchargé la bibliothèque **waad-federation** de l'exemple de code. Activez ensuite la case à cocher en regard du fichier **pom.xml** à partir du projet **waad-federation**, puis cliquez sur **Finish**.

4.  Développez le projet **sample**, cliquez avec le bouton droit sur le fichier **pom.xml**, cliquez sur **Open With**, puis sur **Text Editor**.

5.  Ajoutez le code XML suivant à la section *\<project\>* du fichier **pom.xml**, puis enregistrez-le :

        <dependencies>
            <dependency>
                <groupId>javax.servlet</groupId>
                <artifactId>servlet-api</artifactId>
                <version>3.0-alpha-1</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.samples.waad.federation</groupId>
                <artifactId>waad-federation</artifactId>
                <version>0.0.1-SNAPSHOT</version>
            </dependency>
        </dependencies> 

6.  Cliquez avec le bouton droit sur le projet **sample**, puis cliquez sur **Maven** et sur **Update Project**. Dans la boîte de dialogue **Update Maven Project**, cliquez sur **OK**. Ceci met à jour votre projet en appliquant les modifications apportées au fichier **pom.xml**.

7.  Cliquez avec le bouton droit sur le projet **sample**, cliquez sur **New**, puis sur **Filter**.

8.  Dans la boîte de dialogue **Create Filter**, tapez *FederationFilter* pour l'entrée **Class name**, puis cliquez sur **Finish**.

9.  Le fichier **FederationFilter.java** généré automatiquement s'ouvre. Remplacez son code par le code suivant, puis enregistrez le fichier :

        import java.io.IOException;
        import javax.servlet.Filter;
        import javax.servlet.FilterChain;
        import javax.servlet.FilterConfig;
        import javax.servlet.ServletException;
        import javax.servlet.ServletRequest;
        import javax.servlet.ServletResponse;
        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpServletResponse;
        import java.util.regex.*;
        import com.microsoft.samples.federation.FederatedLoginManager; import com.microsoft.samples.federation.URLUTF8Encoder;

        public class FederationFilter implements Filter {
            private String loginPage;
            private String allowedRegex;
            public void init(FilterConfig config) throws ServletException {
                this.loginPage = config.getInitParameter("login-page-url");
                this.allowedRegex = config.getInitParameter("allowed-regex");
            }
            public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
                    if (request instanceof HttpServletRequest) { 
                        HttpServletRequest httpRequest = (HttpServletRequest) request;
                        if (!httpRequest.getRequestURL().toString().contains(this.loginPage)) {
                            FederatedLoginManager loginManager = FederatedLoginManager.fromRequest(httpRequest);
                            boolean allowedUrl = Pattern.compile(this.allowedRegex).matcher(httpRequest.getRequestURL().toString()).find();
                            if (!allowedUrl && !loginManager.isAuthenticated()) {
                                HttpServletResponse httpResponse = (HttpServletResponse) response;
                                String encodedReturnUrl = URLUTF8Encoder.encode(httpRequest.getRequestURL().toString());
                                httpResponse.setHeader("Location", this.loginPage + "?returnUrl=" + encodedReturnUrl);
                                httpResponse.setStatus(302);
                                return;
                            }
                        }
                    }
                chain.doFilter(request, response);
            }
            public void destroy() {
            }
        } 

10. Dans l'**Explorateur de projets**, développez les dossiers **src**, **main** et **webapp**. Cliquez avec le bouton droit sur le fichier **web.xml**, cliquez sur **Open With**, puis sur **Text Editor**.

11. Dans le fichier **web.xml**, ajoutez un filtre pour gérer les pages sécurisées et non sécurisées et rediriger les utilisateurs non authentifiés vers la page de connexion (indiqué comme le paramètre de filtre **login-page-url**). Cependant, si une URL correspond au regex indiqué dans le paramètre de filtre **allowed-regex**, elle ne sera pas filtrée. Ajoutez le code XML suivant à la section *\<web-app\>*, puis enregistrez le fichier **web.xml**.

        <filter>
            <filter-name>FederationFilter</filter-name>
            <filter-class>FederationFilter</filter-class>
            <init-param>
                <param-name>login-page-url</param-name>
                <param-value>/sample/login.jsp</param-value>
            </init-param>
            <init-param>
                <param-name>allowed-regex</param-name>
                <param-value>(\/sample\/login.jsp|\/sample\/wsfed-saml|\/sample\/oauth)</param-value>
            </init-param>
        </filter>
        <filter-mapping>
            <filter-name>FederationFilter</filter-name>
            <url-pattern>/*</url-pattern>
        </filter-mapping> 

12. Pour créer une page de connexion, cliquez avec le bouton droit sur le projet **sample**, cliquez sur **New**, puis sur **JSP File**.

13. Dans la boîte de dialogue **New JSP File**, remplacez le chemin d'accès du nouveau fichier par *sample/src/main/webapp*. Nommez ensuite le fichier **login.jsp** et cliquez sur **Finish**.

14. Le nouveau fichier **login.jsp** s'ouvre automatiquement. Remplacez le code généré automatiquement par le code suivant, puis enregistrez le fichier :

        <%@ page language="java" contentType="text/html; charset=ISO-8859-1"  pageEncoding="ISO-8859-1"%>
        <%@ page import="com.microsoft.samples.federation.*"%>
        <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
        <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1"> 
            <title>Login Page</title>
        </head>
        <body>
            <h3>Login Page</h3>
            <a href="<%=FederatedLoginManager.getFederatedLoginUrl(request.getParameter("returnUrl"))%>"><%=FederatedConfiguration.getInstance().getStsFriendlyName()%></a>
        </body>
        </html> 

15. Dans l'**Explorateur de projets**, développez le dossier **/src/main** du projet **sample**. Cliquez avec le bouton droit sur le dossier **resources**, cliquez sur **New**, puis sur **Other**.

16. Dans la boîte de dialogue **New**, développez le dossier **JBoss Tools Web**, cliquez sur **Properties File**, puis sur **Next**.

17. Dans la boîte de dialogue **New File Properties**, nommez le fichier **federation**, puis cliquez sur **Finish**.

18. Dans l'**Explorateur de projets**, développez le dossier **src/main/resources** du projet **sample**. Cliquez avec le bouton droit sur le fichier **federation.properties**, cliquez sur **Open With**, puis sur **Text Editor**.

19. Ajoutez les entrées de configuration suivantes au fichier **federation.properties**, puis enregistrez-le :

        federation.trustedissuers.issuer=https://accounts.accesscontrol.windows.net/v2/wsfederation
        federation.trustedissuers.thumbprint=qY+Drf20Zz+A4t2we3PebCopoCugO76My+JMVsqNBFc=
        federation.trustedissuers.friendlyname=Fabrikam
        federation.audienceuris=spn:7829c758-2bef-43df-a685-717089474505
        federation.realm=spn:7829c758-2bef-43df-a685-717089474505
        federation.reply=https://localhost:8443/sample/wsfed-saml 

    > [WACOM.NOTE]
    > Les valeurs **audienceuris** et **realm** doivent être précédées par « spn: ».

20. ﻿À présent, vous devez créer un Servlet. Cliquez avec le bouton droit sur le projet **sample**, cliquez sur **New**, sur **Other**, puis sur **Servlet**.

21. Dans la boîte de dialogue **Create Servlet**, entrez le **nom de classe** de *FederationServlet*, puis cliquez sur **Finish**.

22. Le fichier **FederationServlet.java** s'ouvre automatiquement. Remplacez son contenu par le code suivant, puis enregistrez-le :

        import java.io.IOException;
        import javax.servlet.ServletException;
        import javax.servlet.http.HttpServlet;
        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpServletResponse;
        import com.microsoft.samples.federation.FederatedAuthenticationListener;
        import com.microsoft.samples.federation.FederatedLoginManager;
        import com.microsoft.samples.federation.FederatedPrincipal;
        import com.microsoft.samples.federation.FederationException;

        public class FederationServlet extends HttpServlet {
            private static final long serialVersionUID = 1L;

            protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                String token = request.getParameter("wresult").toString();

                if (token == null) {
                    response.sendError(400, "You were supposed to send a wresult parameter with a token");
                }
                FederatedLoginManager loginManager = FederatedLoginManager.fromRequest(request, new SampleAuthenticationListener());

                try {
                    loginManager.authenticate(token, response);
                } catch (FederationException e) {
                    response.sendError(500, "Oops! and error occurred.");
                }
            }

            private class SampleAuthenticationListener implements FederatedAuthenticationListener {
                @Override
                public void OnAuthenticationSucceed(FederatedPrincipal principal) {
                    // ***
                    // do whatever you want with the principal object that contains the token's claims
                    // ***
                }
            }
        } 

23. Dans l'**Explorateur de projets**, développez le dossier **src/main/webapp/WEB-INF**. Cliquez avec le bouton droit sur le fichier **web.xml**, cliquez sur **Open With**, puis sur **Text Editor**.

24. Dans le fichier **web.xml**, remplacez le paramètre **/FederationServlet** dans la section *\<url-pattern\>* par **/ws-saml**. Par exemple :

        <servlet>
            <description></description>
            <display-name>FederationServlet</display-name>
            <servlet-name>FederationServlet</servlet-name>
            <servlet-class>FederationServlet</servlet-class>
        </servlet>
        <servlet-mapping>
            <servlet-name>FederationServlet</servlet-name>
            <url-pattern>/wsfed-saml</url-pattern>
        </servlet-mapping> 

25. Dans l'**Explorateur de projets**, développez le dossier **src/main/webapp**. Cliquez avec le bouton droit sur le fichier **index.jsp**, cliquez sur **Open With**, puis sur **Text Editor**.

26. Remplacez le code existant du fichier **index.jsp** par le code suivant, puis enregistrez le fichier :

        <%@ page language="java" contentType="text/html; charset=ISO-8859-1" pageEncoding="ISO-8859-1"%>
        <%@ page import="com.microsoft.samples.federation.*"%>
        <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
        <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
            <title>Index Page</title>
        </head>
        <body>
            <h3>Welcome <strong><%=FederatedLoginManager.fromRequest(request).getPrincipal().getName()%></strong>!</h3>
            <h2>Claim list:</h2>
            <ul> 
                <% for (Claim claim : FederatedLoginManager.fromRequest(request).getClaims()) { %>
                <li><%= claim.toString()%></li>
                <%  } %>
            </ul>
        </body>
        </html> 

27. Dans l'**Explorateur de projets**, développez le dossier **src/main/webapp/WEB-INF**. Cliquez avec le bouton droit sur le fichier **web.xml**, cliquez sur **Open With**, puis sur **Text Editor**.

28. ﻿À présent, vous allez activer SSL pour l'application. Dans le fichier **web.xml**, insérez la section *\<security-constraint\>* suivante dans la section *\<web-app\>*, puis enregistrez le fichier :

        <security-constraint>
            <web-resource-collection>
                <web-resource-name>SSL Forwarding</web-resource-name>
                <url-pattern>/*</url-pattern>
                <http-method>POST</http-method>
                <http-method>GET</http-method>
            </web-resource-collection>
            <user-data-constraint>
                <transport-guarantee>CONFIDENTIAL</transport-guarantee>
            </user-data-constraint>
        </security-constraint> 

    > [WACOM.NOTE]
    > Avant de poursuivre, vérifiez que JBoss Server est déjà configuré pour prendre en charge SSL.

29. Vous pouvez maintenant exécuter l'application de bout en bout. Cliquez avec le bouton droit sur le projet **sample**, cliquez sur **Run As**, puis sur **Run on Server**. Acceptez les valeurs que vous avez indiquées auparavant, puis cliquez sur **Finish**.

30. Le navigateur JBoss ouvre la page de connexion. Si vous cliquez sur le lien **Awesome Computers**, vous êtes automatiquement redirigé vers la page du fournisseur d'identité Office 365, où vous pouvez vous connecter en utilisant les informations d'identification du client d'annuaire. Par exemple, *thomas.levesque@fabrikam.onmicrosoft.com*.

31. Une fois connecté, vous êtes redirigé vers la page sécurisée (**sample/index.jsp**) en tant qu'utilisateur authentifié.

## <a name="summary"></a>Résumé

Ce didacticiel vous a montré comment créer et configurer une unique application Java cliente utilisant les capacités d'authentification unique d'Azure Active Directory.

  [Exemple de code Java pour Azure Active Directory]: https://github.com/WindowsAzure/azure-sdk-for-java-samples/tree/master/WAAD.WebSSO.JAVA
  [Java Runtime Environment 1.6]: http://www.oracle.com/technetwork/java/javase/downloads/index.html
  [JBoss Application Server version 7.1.1.Final]: http://www.jboss.org/jbossas/downloads/
  [IDE JBoss Developer Studio]: https://devstudio.jboss.com/earlyaccess/
  [Cmdlets PowerShell pour Office 365]: http://onlinehelp.microsoft.com/fr-fr/office365-enterprises/ff652560.aspx
  [Introduction]: #introduction
  [Étape 1 : création d'une application Java]: #createapp
  [Étape 2 : configuration de l'application dans le client d'annuaire d'une société]: #provisionapp
  [Étape 3 : protection de l'application en utilisant WS-Federation pour la connexion des employés]: #protectapp
  [Résumé]: #summary

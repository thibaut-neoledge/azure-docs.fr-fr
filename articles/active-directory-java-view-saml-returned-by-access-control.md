<properties linkid="develop-java-how-to-guides-view-saml-returned-by-acs" urlDisplayName="View ACS SAML" pageTitle="View SAML Returned by the Access Control Service (Java)" metaKeywords="" description="Learn how to view SAML returned by the Access Control Service in Java applications hosted on Azure." metaCanonical="" services="" documentationCenter="Java" title="How to view SAML returned by the Azure Access Control Service" authors="waltpo" videoId="" scriptId="" solutions="" manager="" editor="mollybos" />

Affichage des données SAML (Security Assertion Markup Language) renvoyées par Azure Access Control Service
==========================================================================================================

Ce guide explique comment afficher les données SAML (Security Assertion Markup Language) sous-jacentes renvoyées à votre application par Azure Access Control Service (ACS). Ce guide s'appuie sur la rubrique [Authentification des utilisateurs Web auprès d'Azure Access Control Service à l'aide d'Eclipse](../active-directory-java-authenticate-users-access-control-eclipse) en fournissant le code nécessaire à l'affichage des données SAML. L'application terminée sera semblable à ce qui suit.

![Exemple de sortie SAML](./media/active-directory-java-view-saml-returned-by-access-control/SAML_Output.png)

Pour plus d'informations sur ACS, consultez la section [Étapes suivantes](#next_steps).

> [WACOM.NOTE] Le filtre ACS Azure (par Microsoft Open Technologies) est une version préliminaire de la technologie destinée à la communauté. En tant que logiciel préliminaire, il n'est pas officiellement pris en charge par Microsoft Open Technologies, Inc. ou Microsoft.

Sommaire
--------

-   [Configuration requise](#pre)
-   [Ajout de la bibliothèque JspWriter au chemin d'accès de la build et à l'assembly de déploiement](#add_library)
-   [Modification du fichier JSP pour afficher les données SAML](#modify_jsp)
-   [Exécution de l'application](#run_application)
-   [Étapes suivantes](#next_steps)

Configuration requise
---------------------

Pour réaliser les tâches présentées dans ce guide, suivez l'exemple de la rubrique [Authentification des utilisateurs Web auprès d'Azure Access Control Service à l'aide d'Eclipse](../active-directory-java-authenticate-users-access-control-eclipse) et utilisez-le comme point de départ de ce didacticiel.

Ajout de la bibliothèque JspWriter au chemin d'accès de la build et à l'assembly de déploiement
-----------------------------------------------------------------------------------------------

Ajoutez la bibliothèque contenant la classe **javax.servlet.jsp.JspWriter** au chemin d'accès de la build et à l'assembly de déploiement. Si vous utilisez Tomcat, utilisez la bibliothèque **jsp-api.jar** située dans le dossier **lib** d'Apache.

1.  Dans l'Explorateur de projets Eclipse, cliquez avec le bouton droit sur **MyACSHelloWorld**, puis cliquez sur **Build Path**, sur **Configure Build Path**, sur l'onglet **Libraries** et sur **Add External JARs**.
2.  Dans la boîte de dialogue **JAR Selection**, accédez au fichier JAR requis, sélectionnez-le, puis cliquez sur **Open**.
3.  Dans la boîte de dialogue **Properties for MyACSHelloWorld**, cliquez sur **Deployment Assembly**.
4.  Dans la boîte de dialogue **Web Deployment Assembly**, cliquez sur **Add**.
5.  Dans la boîte de dialogue **New Assembly Directive**, cliquez sur **Java Build Path Entries** puis sur **Next**.
6.  Sélectionnez la bibliothèque qui convient, puis cliquez sur **Terminer**.
7.  Cliquez sur **OK** pour fermer la boîte de dialogue **Properties for MyACSHelloWorld**.

Modification du fichier JSP pour afficher les données SAML
----------------------------------------------------------

Modifiez le fichier **index.jsp** de manière à utiliser le code suivant.

    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
        <%@ page import="javax.xml.parsers.*"
                 import="javax.xml.transform.*"
                 import="org.w3c.dom.*"
                 import="java.io.*"
                 import="javax.xml.transform.stream.*"
                 import="javax.xml.transform.dom.*"
                 import="javax.xml.xpath.*"
                 import="javax.servlet.jsp.JspWriter" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Sample ACS Filter</title>
    </head>
    <body>
        <h3>SAML information from sample ACS program</h3>
        <%!
        void displaySAMLInfo(Node node, String parent, JspWriter out)
        {
        
            try
            {
                String nodeName;
                int nChild, i;
                
                nodeName = node.getNodeName();
                out.println("<br>");
                out.println("<u>Examining <b>" + parent + nodeName + "</b></u><br>");
                   
                   // Attributs.
                   NamedNodeMap attribsMap = node.getAttributes();
                   if (null != attribsMap)
                   {
                         for (i=0; i < attribsMap.getLength(); i++)
                         {
                                Node attrib = attribsMap.item(i);
                                out.println("Attribute: <b>" + attrib.getNodeName() + "</b>: " + attrib.getNodeValue()  + "<br>");
                         }
                   }
                   
                   // Nœuds enfants.
                   NodeList list = node.getChildNodes();
                   if (null != list)
                   {
                          nChild = list.getLength();
                          if (nChild > 0)
                          {                    

                                 // S'il s'agit d'un nœud de texte, contentez-vous d'imprimer le texte.
                                 if (list.item(0).getNodeName() == "#text")
                                 {
                                     out.println("Text value: <b>" + list.item(0).getTextContent() + "</b><br>");
                                 }
                                 else
                                 {
                                     // Imprimez les noms des nœuds enfants.
                                     out.print("Contains " + nChild + " child node(s): ");   
                                     for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);
                                        
                                        out.print("<b>" + temp.getNodeName() + "</b>");
                                        if (i < nChild - 1)
                                        {
                                            // Séparez les noms.
                                            out.print(", ");
                                        }
                                        else
                                        {
                                            // Terminez la phrase.
                                            out.print(".");
                                        }
                                            
                                     }
                                     out.println("<br>");
                                     
                                     // Traitez les nœuds enfants.
                                     for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);
                                        displaySAMLInfo(temp, parent + nodeName + "\", out);
                                     }
                               }
                          }
                      }
                  }
                catch (Exception e)
                {
                    System.out.println("Exception encountered.");
                    e.printStackTrace();            
                }
            }
        %>

        <%
        try 
        {
            String data  = (String) request.getAttribute("ACSSAML");
            
            DocumentBuilder docBuilder;
            Document doc = null;
            DocumentBuilderFactory docBuilderFactory = DocumentBuilderFactory.newInstance();
            docBuilderFactory.setIgnoringElementContentWhitespace(true);
            docBuilder = docBuilderFactory.newDocumentBuilder();
            byte[] xmlDATA = data.getBytes();
            
            ByteArrayInputStream in = new ByteArrayInputStream(xmlDATA); 
            doc = docBuilder.parse(in);
            doc.getDocumentElement().normalize();
            
            // Itérez les nœuds enfants du document.
            NodeList list = doc.getChildNodes();

            for (int i=0; i < list.getLength(); i++)
            {
                displaySAMLInfo(list.item(i), "", out);
            }
        }
        catch (Exception e) 
        {
            out.println("Exception encountered.");
            e.printStackTrace();
        }
        
        %>
    </body>
    </html>

Exécution de l'application
--------------------------

1.  Exécutez votre application dans l'émulateur de l'ordinateur ou déployez-la dans Azure en suivant la procédure décrite à la rubrique [Authentification des utilisateurs Web auprès d'Azure Access Control Service à l'aide d'Eclipse](../active-directory-java-authenticate-users-access-control-eclipse).
2.  Lancez un navigateur et ouvrez votre application Web. Une fois connecté à votre application, vous verrez les données SAML ainsi que l'assertion de sécurité fournie par le fournisseur d'identité.

Étapes suivantes
----------------

Pour en savoir plus sur les fonctionnalités ACS et découvrir des scénarios plus complexes, consultez la page [Access Control Service 2.0](http://go.microsoft.com/fwlink/?LinkID=212360).


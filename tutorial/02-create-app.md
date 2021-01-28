<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="879a7-101">In diesem Abschnitt erstellen Sie eine neue React-App.</span><span class="sxs-lookup"><span data-stu-id="879a7-101">In this section you'll create a new React app.</span></span>

1. <span data-ttu-id="879a7-102">Öffnen Sie Die Befehlszeilenschnittstelle (CLI), navigieren Sie zu einem Verzeichnis, in dem Sie über die Rechte zum Erstellen von Dateien verfügen, und führen Sie die folgenden Befehle aus, um eine neue React-App zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="879a7-102">Open your command-line interface (CLI), navigate to a directory where you have rights to create files, and run the following commands to create a new React app.</span></span>

    ```Shell
    npx create-react-app@4.0.1 graph-tutorial --template typescript
    ```

1. <span data-ttu-id="879a7-103">Wechseln Sie nach Abschluss des Befehls in das Verzeichnis in der CLI, und führen Sie den folgenden Befehl aus, um einen `graph-tutorial` lokalen Webserver zu starten.</span><span class="sxs-lookup"><span data-stu-id="879a7-103">Once the command finishes, change to the `graph-tutorial` directory in your CLI and run the following command to start a local web server.</span></span>

    ```Shell
    yarn start
    ```

    > [!NOTE]
    > <span data-ttu-id="879a7-104">Wenn Sie ["Vorzung" nicht](https://yarnpkg.com/) installiert haben, können Sie stattdessen `npm start` verwenden.</span><span class="sxs-lookup"><span data-stu-id="879a7-104">If you do not have [Yarn](https://yarnpkg.com/) installed, you can use `npm start` instead.</span></span>

<span data-ttu-id="879a7-105">Ihr Standardbrowser wird mit [https://localhost:3000/](https://localhost:3000) einer standardmäßigen React-Seite geöffnet.</span><span class="sxs-lookup"><span data-stu-id="879a7-105">Your default browser opens to [https://localhost:3000/](https://localhost:3000) with a default React page.</span></span> <span data-ttu-id="879a7-106">Wenn Ihr Browser nicht geöffnet wird, öffnen Sie ihn, und überprüfen Sie, ob [https://localhost:3000/](https://localhost:3000) die neue App funktioniert.</span><span class="sxs-lookup"><span data-stu-id="879a7-106">If your browser doesn't open, open it and browse to [https://localhost:3000/](https://localhost:3000) to verify that the new app works.</span></span>

## <a name="add-node-packages"></a><span data-ttu-id="879a7-107">Hinzufügen von Knotenpaketen</span><span class="sxs-lookup"><span data-stu-id="879a7-107">Add Node packages</span></span>

<span data-ttu-id="879a7-108">Installieren Sie vor dem Wechsel einige zusätzliche Pakete, die Sie später verwenden werden:</span><span class="sxs-lookup"><span data-stu-id="879a7-108">Before moving on, install some additional packages that you will use later:</span></span>

- <span data-ttu-id="879a7-109">[react-router-dom](https://github.com/ReactTraining/react-router) für deklaratives Routing innerhalb der React-App.</span><span class="sxs-lookup"><span data-stu-id="879a7-109">[react-router-dom](https://github.com/ReactTraining/react-router) for declarative routing inside the React app.</span></span>
- <span data-ttu-id="879a7-110">[Bootstrap für](https://github.com/twbs/bootstrap) Formatierung und allgemeine Komponenten.</span><span class="sxs-lookup"><span data-stu-id="879a7-110">[bootstrap](https://github.com/twbs/bootstrap) for styling and common components.</span></span>
- <span data-ttu-id="879a7-111">[reactstrap](https://github.com/reactstrap/reactstrap) für React-Komponenten, die auf Bootstrap basieren.</span><span class="sxs-lookup"><span data-stu-id="879a7-111">[reactstrap](https://github.com/reactstrap/reactstrap) for React components based on Bootstrap.</span></span>
- <span data-ttu-id="879a7-112">[fontawesome-free](https://github.com/FortAwesome/Font-Awesome) für Symbole.</span><span class="sxs-lookup"><span data-stu-id="879a7-112">[fontawesome-free](https://github.com/FortAwesome/Font-Awesome) for icons.</span></span>
- <span data-ttu-id="879a7-113">[Moment](https://github.com/moment/moment) für die Formatierung von Datums- und Zeitangaben.</span><span class="sxs-lookup"><span data-stu-id="879a7-113">[moment](https://github.com/moment/moment) for formatting dates and times.</span></span>
- <span data-ttu-id="879a7-114">[windows-iana](https://github.com/rubenillodo/windows-iana) für die Übersetzung von Windows-Zeitzonen in das IANA-Format.</span><span class="sxs-lookup"><span data-stu-id="879a7-114">[windows-iana](https://github.com/rubenillodo/windows-iana) for translating Windows time zones to IANA format.</span></span>
- <span data-ttu-id="879a7-115">[msal-browser](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) für die Authentifizierung bei Azure Active Directory und das Abrufen von Zugriffstoken.</span><span class="sxs-lookup"><span data-stu-id="879a7-115">[msal-browser](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) for authenticating to Azure Active Directory and retrieving access tokens.</span></span>
- <span data-ttu-id="879a7-116">[microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) for making calls to Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="879a7-116">[microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) for making calls to Microsoft Graph.</span></span>

<span data-ttu-id="879a7-117">Führen Sie den folgenden Befehl in Der CLI aus.</span><span class="sxs-lookup"><span data-stu-id="879a7-117">Run the following command in your CLI.</span></span>

```Shell
yarn add react-router-dom@5.2.0 @types/react-router-dom@5.1.7 bootstrap@4.6.0 reactstrap@8.9.0 @types/reactstrap@8.7.2 @fortawesome/fontawesome-free@5.15.2
yarn add moment@2.29.1 moment-timezone@0.5.32 windows-iana@4.2.1 @azure/msal-browser@2.10.0 @microsoft/microsoft-graph-client@2.2.1 @types/microsoft-graph@1.28.0
```

## <a name="design-the-app"></a><span data-ttu-id="879a7-118">Entwerfen der App</span><span class="sxs-lookup"><span data-stu-id="879a7-118">Design the app</span></span>

<span data-ttu-id="879a7-119">Erstellen Sie zunächst eine Navigationsleiste für die App.</span><span class="sxs-lookup"><span data-stu-id="879a7-119">Start by creating a navbar for the app.</span></span>

1. <span data-ttu-id="879a7-120">Erstellen Sie eine neue Datei im `./src` Verzeichnis mit dem `NavBar.tsx` Namen, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="879a7-120">Create a new file in the `./src` directory named `NavBar.tsx` and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/NavBar.tsx" id="NavBarSnippet":::

1. <span data-ttu-id="879a7-121">Erstellen Sie eine Startseite für die App.</span><span class="sxs-lookup"><span data-stu-id="879a7-121">Create a home page for the app.</span></span> <span data-ttu-id="879a7-122">Erstellen Sie eine neue Datei im `./src` Verzeichnis mit dem `Welcome.tsx` Namen, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="879a7-122">Create a new file in the `./src` directory named `Welcome.tsx` and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/Welcome.tsx" id="WelcomeSnippet":::

1. <span data-ttu-id="879a7-123">Erstellen Sie eine Fehlermeldungsanzeige, um dem Benutzer Meldungen anzeigen zu können.</span><span class="sxs-lookup"><span data-stu-id="879a7-123">Create an error message display to display messages to the user.</span></span> <span data-ttu-id="879a7-124">Erstellen Sie eine neue Datei im `./src` Verzeichnis mit dem `ErrorMessage.tsx` Namen, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="879a7-124">Create a new file in the `./src` directory named `ErrorMessage.tsx` and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/ErrorMessage.tsx" id="ErrorMessageSnippet":::

1. <span data-ttu-id="879a7-125">Öffnen Sie die Datei `./src/index.css`, und ersetzen Sie ihren Inhalt durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="879a7-125">Open the `./src/index.css` file and replace its entire contents with the following.</span></span>

    :::code language="css" source="../demo/graph-tutorial/src/index.css":::

1. <span data-ttu-id="879a7-126">Öffnen `./src/App.tsx` Sie den gesamten Inhalt, und ersetzen Sie ihn durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="879a7-126">Open `./src/App.tsx` and replace its entire contents with the following.</span></span>

    ```typescript
    import React, { Component } from 'react';
    import { BrowserRouter as Router, Route, Redirect } from 'react-router-dom';
    import { Container } from 'reactstrap';
    import NavBar from './NavBar';
    import ErrorMessage from './ErrorMessage';
    import Welcome from './Welcome';
    import 'bootstrap/dist/css/bootstrap.css';

    class App extends Component<any> {
      render() {
        let error = null;
        if (this.props.error) {
          error = <ErrorMessage
            message={this.props.error.message}
            debug={this.props.error.debug} />;
        }

        return (
          <Router>
            <div>
              <NavBar
                isAuthenticated={this.props.isAuthenticated}
                authButtonMethod={this.props.isAuthenticated ? this.props.logout : this.props.login}
                user={this.props.user} />
              <Container>
                {error}
                <Route exact path="/"
                  render={(props) =>
                    <Welcome {...props}
                      isAuthenticated={this.props.isAuthenticated}
                      user={this.props.user}
                      authButtonMethod={this.props.login} />
                  } />
              </Container>
            </div>
          </Router>
        );
      }
    }

    export default App;
    ```

1. <span data-ttu-id="879a7-127">Speichern Sie alle Änderungen, und starten Sie die App neu.</span><span class="sxs-lookup"><span data-stu-id="879a7-127">Save all of your changes and restart the app.</span></span> <span data-ttu-id="879a7-128">Jetzt sollte die App ganz anders aussehen.</span><span class="sxs-lookup"><span data-stu-id="879a7-128">Now, the app should look very different.</span></span>

    ![Screenshot der neu gestalteten Homepage](images/create-app-01.png)

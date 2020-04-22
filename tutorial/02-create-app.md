<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="4bcb8-101">In diesem Abschnitt erstellen Sie eine neue Reaktions-app.</span><span class="sxs-lookup"><span data-stu-id="4bcb8-101">In this section you'll create a new React app.</span></span>

1. <span data-ttu-id="4bcb8-102">Öffnen Sie die Befehlszeilenschnittstelle (CLI), navigieren Sie zu einem Verzeichnis, in dem Sie über Berechtigungen zum Erstellen von Dateien verfügen, und führen Sie die folgenden Befehle aus, um eine neue Reaktions-APP zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="4bcb8-102">Open your command-line interface (CLI), navigate to a directory where you have rights to create files, and run the following commands to create a new React app.</span></span>

    ```Shell
    npx create-react-app@3.4.0 graph-tutorial --template typescript
    ```

1. <span data-ttu-id="4bcb8-103">Nachdem der Befehl abgeschlossen ist, wechseln Sie `graph-tutorial` in das Verzeichnis in der CLI, und führen Sie den folgenden Befehl aus, um einen lokalen Webserver zu starten.</span><span class="sxs-lookup"><span data-stu-id="4bcb8-103">Once the command finishes, change to the `graph-tutorial` directory in your CLI and run the following command to start a local web server.</span></span>

    ```Shell
    npm start
    ```

<span data-ttu-id="4bcb8-104">Ihr Standardbrowser wird [https://localhost:3000/](https://localhost:3000) mit einer Standard reaktionsseite geöffnet.</span><span class="sxs-lookup"><span data-stu-id="4bcb8-104">Your default browser opens to [https://localhost:3000/](https://localhost:3000) with a default React page.</span></span> <span data-ttu-id="4bcb8-105">Wenn Ihr Browser nicht geöffnet wird, öffnen Sie ihn, [https://localhost:3000/](https://localhost:3000) und navigieren Sie zu, um zu überprüfen, ob die neue APP funktioniert.</span><span class="sxs-lookup"><span data-stu-id="4bcb8-105">If your browser doesn't open, open it and browse to [https://localhost:3000/](https://localhost:3000) to verify that the new app works.</span></span>

## <a name="add-node-packages"></a><span data-ttu-id="4bcb8-106">Hinzufügen von Knoten Paketen</span><span class="sxs-lookup"><span data-stu-id="4bcb8-106">Add Node packages</span></span>

<span data-ttu-id="4bcb8-107">Bevor Sie fortfahren, installieren Sie einige zusätzliche Pakete, die Sie später verwenden werden:</span><span class="sxs-lookup"><span data-stu-id="4bcb8-107">Before moving on, install some additional packages that you will use later:</span></span>

- <span data-ttu-id="4bcb8-108">[reagiere-Router-Dom](https://github.com/ReactTraining/react-router) für das deklarative Routing innerhalb der Reaktions-app.</span><span class="sxs-lookup"><span data-stu-id="4bcb8-108">[react-router-dom](https://github.com/ReactTraining/react-router) for declarative routing inside the React app.</span></span>
- <span data-ttu-id="4bcb8-109">[Bootstrap](https://github.com/twbs/bootstrap) für Styling und allgemeine Komponenten.</span><span class="sxs-lookup"><span data-stu-id="4bcb8-109">[bootstrap](https://github.com/twbs/bootstrap) for styling and common components.</span></span>
- <span data-ttu-id="4bcb8-110">[reactstrap](https://github.com/reactstrap/reactstrap) für reaktionskomponenten basierend auf Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="4bcb8-110">[reactstrap](https://github.com/reactstrap/reactstrap) for React components based on Bootstrap.</span></span>
- <span data-ttu-id="4bcb8-111">[fontawesome-kostenlos](https://github.com/FortAwesome/Font-Awesome) für Symbole.</span><span class="sxs-lookup"><span data-stu-id="4bcb8-111">[fontawesome-free](https://github.com/FortAwesome/Font-Awesome) for icons.</span></span>
- <span data-ttu-id="4bcb8-112">[Zeitpunkt](https://github.com/moment/moment) für die Formatierung von Datums-und Uhrzeitangaben.</span><span class="sxs-lookup"><span data-stu-id="4bcb8-112">[moment](https://github.com/moment/moment) for formatting dates and times.</span></span>
- <span data-ttu-id="4bcb8-113">[msal](https://github.com/AzureAD/microsoft-authentication-library-for-js) zur Authentifizierung bei Azure Active Directory und zum Abrufen von Zugriffstoken.</span><span class="sxs-lookup"><span data-stu-id="4bcb8-113">[msal](https://github.com/AzureAD/microsoft-authentication-library-for-js) for authenticating to Azure Active Directory and retrieving access tokens.</span></span>
- <span data-ttu-id="4bcb8-114">[Microsoft-Graph-Client](https://github.com/microsoftgraph/msgraph-sdk-javascript) zum tätigen von Anrufen an Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="4bcb8-114">[microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) for making calls to Microsoft Graph.</span></span>

<span data-ttu-id="4bcb8-115">Führen Sie den folgenden Befehl in der CLI aus.</span><span class="sxs-lookup"><span data-stu-id="4bcb8-115">Run the following command in your CLI.</span></span>

```Shell
npm install react-router-dom@5.1.2 @types/react-router-dom@5.1.3 bootstrap@4.4.1 reactstrap@8.4.1 @types/reactstrap@8.4.2
npm install @fortawesome/fontawesome-free@5.12.1 moment@2.24.0 msal@1.2.1 @microsoft/microsoft-graph-client@2.0.0 @types/microsoft-graph@1.12.0
```

## <a name="design-the-app"></a><span data-ttu-id="4bcb8-116">Entwerfen der App</span><span class="sxs-lookup"><span data-stu-id="4bcb8-116">Design the app</span></span>

<span data-ttu-id="4bcb8-117">Erstellen Sie zunächst ein navbar für die app.</span><span class="sxs-lookup"><span data-stu-id="4bcb8-117">Start by creating a navbar for the app.</span></span>

1. <span data-ttu-id="4bcb8-118">Erstellen Sie eine neue Datei im `./src` Verzeichnis mit `NavBar.tsx` dem Namen, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="4bcb8-118">Create a new file in the `./src` directory named `NavBar.tsx` and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/NavBar.tsx" id="NavBarSnippet":::

1. <span data-ttu-id="4bcb8-119">Erstellen Sie eine Startseite für die app.</span><span class="sxs-lookup"><span data-stu-id="4bcb8-119">Create a home page for the app.</span></span> <span data-ttu-id="4bcb8-120">Erstellen Sie eine neue Datei im `./src` Verzeichnis mit `Welcome.tsx` dem Namen, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="4bcb8-120">Create a new file in the `./src` directory named `Welcome.tsx` and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/Welcome.tsx" id="WelcomeSnippet":::

1. <span data-ttu-id="4bcb8-121">Erstellen Sie eine Fehlermeldungen-Anzeige, um Nachrichten an den Benutzer anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="4bcb8-121">Create an error message display to display messages to the user.</span></span> <span data-ttu-id="4bcb8-122">Erstellen Sie eine neue Datei im `./src` Verzeichnis mit `ErrorMessage.tsx` dem Namen, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="4bcb8-122">Create a new file in the `./src` directory named `ErrorMessage.tsx` and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/ErrorMessage.tsx" id="ErrorMessageSnippet":::

1. <span data-ttu-id="4bcb8-123">Öffnen Sie die Datei `./src/index.css`, und ersetzen Sie ihren Inhalt durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="4bcb8-123">Open the `./src/index.css` file and replace its entire contents with the following.</span></span>

    :::code language="css" source="../demo/graph-tutorial/src/index.css":::

1. <span data-ttu-id="4bcb8-124">Öffnen `./src/App.tsx` Sie den gesamten Inhalt, und ersetzen Sie ihn durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="4bcb8-124">Open `./src/App.tsx` and replace its entire contents with the following.</span></span>

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
                user={this.props.user}/>
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

1. <span data-ttu-id="4bcb8-125">Speichern Sie alle Änderungen, und aktualisieren Sie die Seite.</span><span class="sxs-lookup"><span data-stu-id="4bcb8-125">Save all of your changes and refresh the page.</span></span> <span data-ttu-id="4bcb8-126">Nun sollte die APP sehr unterschiedlich aussehen.</span><span class="sxs-lookup"><span data-stu-id="4bcb8-126">Now, the app should look very different.</span></span>

    ![Screenshot der neu gestalteten Homepage](images/create-app-01.png)

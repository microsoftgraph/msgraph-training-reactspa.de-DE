<!-- markdownlint-disable MD002 MD041 -->

In diesem Abschnitt erstellen Sie eine neue Reaktions-app.

1. Öffnen Sie die Befehlszeilenschnittstelle (CLI), navigieren Sie zu einem Verzeichnis, in dem Sie über Berechtigungen zum Erstellen von Dateien verfügen, und führen Sie die folgenden Befehle aus, um eine neue Reaktions-APP zu erstellen.

    ```Shell
    npx create-react-app@3.4.1 graph-tutorial --template typescript
    ```

1. Nachdem der Befehl abgeschlossen ist, wechseln Sie `graph-tutorial` in das Verzeichnis in der CLI, und führen Sie den folgenden Befehl aus, um einen lokalen Webserver zu starten.

    ```Shell
    npm start
    ```

Ihr Standardbrowser wird [https://localhost:3000/](https://localhost:3000) mit einer Standard reaktionsseite geöffnet. Wenn Ihr Browser nicht geöffnet wird, öffnen Sie ihn, und navigieren Sie zu, um [https://localhost:3000/](https://localhost:3000) zu überprüfen, ob die neue APP funktioniert.

## <a name="add-node-packages"></a>Hinzufügen von Knoten Paketen

Bevor Sie fortfahren, installieren Sie einige zusätzliche Pakete, die Sie später verwenden werden:

- [reagiere-Router-Dom](https://github.com/ReactTraining/react-router) für das deklarative Routing innerhalb der Reaktions-app.
- [Bootstrap](https://github.com/twbs/bootstrap) für Styling und allgemeine Komponenten.
- [reactstrap](https://github.com/reactstrap/reactstrap) für reaktionskomponenten basierend auf Bootstrap.
- [fontawesome-kostenlos](https://github.com/FortAwesome/Font-Awesome) für Symbole.
- [Zeitpunkt](https://github.com/moment/moment) für die Formatierung von Datums-und Uhrzeitangaben.
- [Windows-IANA](https://github.com/rubenillodo/windows-iana) für die Übersetzung von Windows-Zeitzonen in das IANA-Format.
- [msal-Browser](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) zur Authentifizierung bei Azure Active Directory und zum Abrufen von Zugriffstoken.
- [Microsoft-Graph-Client](https://github.com/microsoftgraph/msgraph-sdk-javascript) zum tätigen von Anrufen an Microsoft Graph.

Führen Sie den folgenden Befehl in der CLI aus.

```Shell
npm install react-router-dom@5.2.0 @types/react-router-dom@5.1.5 bootstrap@4.5.2 reactstrap@8.5.1 @types/reactstrap@8.5.1 @fortawesome/fontawesome-free@5.14.0
npm install moment@2.27.0 moment-timezone@0.5.31 windows-iana@4.2.1 @azure/msal-browser@2.1.0 @microsoft/microsoft-graph-client@2.0.0 @types/microsoft-graph@1.18.0
```

## <a name="design-the-app"></a>Entwerfen der App

Erstellen Sie zunächst ein navbar für die app.

1. Erstellen Sie eine neue Datei im `./src` Verzeichnis mit dem Namen `NavBar.tsx` , und fügen Sie den folgenden Code hinzu.

    :::code language="typescript" source="../demo/graph-tutorial/src/NavBar.tsx" id="NavBarSnippet":::

1. Erstellen Sie eine Startseite für die app. Erstellen Sie eine neue Datei im `./src` Verzeichnis mit dem Namen `Welcome.tsx` , und fügen Sie den folgenden Code hinzu.

    :::code language="typescript" source="../demo/graph-tutorial/src/Welcome.tsx" id="WelcomeSnippet":::

1. Erstellen Sie eine Fehlermeldungen-Anzeige, um Nachrichten an den Benutzer anzuzeigen. Erstellen Sie eine neue Datei im `./src` Verzeichnis mit dem Namen `ErrorMessage.tsx` , und fügen Sie den folgenden Code hinzu.

    :::code language="typescript" source="../demo/graph-tutorial/src/ErrorMessage.tsx" id="ErrorMessageSnippet":::

1. Öffnen Sie die Datei `./src/index.css`, und ersetzen Sie ihren Inhalt durch Folgendes:

    :::code language="css" source="../demo/graph-tutorial/src/index.css":::

1. Öffnen Sie `./src/App.tsx` den gesamten Inhalt, und ersetzen Sie ihn durch Folgendes.

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

1. Speichern Sie alle Änderungen, und aktualisieren Sie die Seite. Nun sollte die APP sehr unterschiedlich aussehen.

    ![Screenshot der neu gestalteten Homepage](images/create-app-01.png)

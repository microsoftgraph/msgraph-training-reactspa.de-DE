<!-- markdownlint-disable MD002 MD041 -->

In diesem Abschnitt erstellen Sie eine neue React-App.

1. Öffnen Sie Die Befehlszeilenschnittstelle (CLI), navigieren Sie zu einem Verzeichnis, in dem Sie über die Rechte zum Erstellen von Dateien verfügen, und führen Sie die folgenden Befehle aus, um eine neue React-App zu erstellen.

    ```Shell
    npx create-react-app@4.0.1 graph-tutorial --template typescript
    ```

1. Wechseln Sie nach Abschluss des Befehls in das Verzeichnis in der CLI, und führen Sie den folgenden Befehl aus, um einen `graph-tutorial` lokalen Webserver zu starten.

    ```Shell
    yarn start
    ```

    > [!NOTE]
    > Wenn Sie ["Vorzung" nicht](https://yarnpkg.com/) installiert haben, können Sie stattdessen `npm start` verwenden.

Ihr Standardbrowser wird mit [https://localhost:3000/](https://localhost:3000) einer standardmäßigen React-Seite geöffnet. Wenn Ihr Browser nicht geöffnet wird, öffnen Sie ihn, und überprüfen Sie, ob [https://localhost:3000/](https://localhost:3000) die neue App funktioniert.

## <a name="add-node-packages"></a>Hinzufügen von Knotenpaketen

Installieren Sie vor dem Wechsel einige zusätzliche Pakete, die Sie später verwenden werden:

- [react-router-dom](https://github.com/ReactTraining/react-router) für deklaratives Routing innerhalb der React-App.
- [Bootstrap für](https://github.com/twbs/bootstrap) Formatierung und allgemeine Komponenten.
- [reactstrap](https://github.com/reactstrap/reactstrap) für React-Komponenten, die auf Bootstrap basieren.
- [fontawesome-free](https://github.com/FortAwesome/Font-Awesome) für Symbole.
- [Moment](https://github.com/moment/moment) für die Formatierung von Datums- und Zeitangaben.
- [windows-iana](https://github.com/rubenillodo/windows-iana) für die Übersetzung von Windows-Zeitzonen in das IANA-Format.
- [msal-browser](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) für die Authentifizierung bei Azure Active Directory und das Abrufen von Zugriffstoken.
- [microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) for making calls to Microsoft Graph.

Führen Sie den folgenden Befehl in Der CLI aus.

```Shell
yarn add react-router-dom@5.2.0 @types/react-router-dom@5.1.7 bootstrap@4.6.0 reactstrap@8.9.0 @types/reactstrap@8.7.2 @fortawesome/fontawesome-free@5.15.2
yarn add moment@2.29.1 moment-timezone@0.5.32 windows-iana@4.2.1 @azure/msal-browser@2.10.0 @microsoft/microsoft-graph-client@2.2.1 @types/microsoft-graph@1.28.0
```

## <a name="design-the-app"></a>Entwerfen der App

Erstellen Sie zunächst eine Navigationsleiste für die App.

1. Erstellen Sie eine neue Datei im `./src` Verzeichnis mit dem `NavBar.tsx` Namen, und fügen Sie den folgenden Code hinzu.

    :::code language="typescript" source="../demo/graph-tutorial/src/NavBar.tsx" id="NavBarSnippet":::

1. Erstellen Sie eine Startseite für die App. Erstellen Sie eine neue Datei im `./src` Verzeichnis mit dem `Welcome.tsx` Namen, und fügen Sie den folgenden Code hinzu.

    :::code language="typescript" source="../demo/graph-tutorial/src/Welcome.tsx" id="WelcomeSnippet":::

1. Erstellen Sie eine Fehlermeldungsanzeige, um dem Benutzer Meldungen anzeigen zu können. Erstellen Sie eine neue Datei im `./src` Verzeichnis mit dem `ErrorMessage.tsx` Namen, und fügen Sie den folgenden Code hinzu.

    :::code language="typescript" source="../demo/graph-tutorial/src/ErrorMessage.tsx" id="ErrorMessageSnippet":::

1. Öffnen Sie die Datei `./src/index.css`, und ersetzen Sie ihren Inhalt durch Folgendes:

    :::code language="css" source="../demo/graph-tutorial/src/index.css":::

1. Öffnen `./src/App.tsx` Sie den gesamten Inhalt, und ersetzen Sie ihn durch Folgendes.

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

1. Speichern Sie alle Änderungen, und starten Sie die App neu. Jetzt sollte die App ganz anders aussehen.

    ![Screenshot der neu gestalteten Homepage](images/create-app-01.png)

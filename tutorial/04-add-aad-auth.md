<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen. Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken zum Aufrufen von Microsoft Graph zu erhalten. In diesem Schritt werden Sie die Bibliothek der [Microsoft-Authentifizierungsbibliothek](https://github.com/AzureAD/microsoft-authentication-library-for-js) in die Anwendung integrieren.

1. Erstellen Sie eine neue Datei im `./src` Verzeichnis mit `Config.ts` dem Namen, und fügen Sie den folgenden Code hinzu.

    :::code language="typescript" source="../demo/graph-tutorial/src/Config.ts.example":::

    Ersetzen `YOUR_APP_ID_HERE` Sie durch die Anwendungs-ID aus dem Anwendungs Registrierungs Portal.

    > [!IMPORTANT]
    > Wenn Sie die Quellcodeverwaltung wie git verwenden, wäre es jetzt ein guter Zeitpunkt, die Datei `Config.ts` aus der Quellcodeverwaltung auszuschließen, um unbeabsichtigtes Auslaufen ihrer APP-ID zu vermeiden.

## <a name="implement-sign-in"></a>Implementieren der Anmeldung

In diesem Abschnitt erstellen Sie einen Authentifizierungsanbieter und implementieren die Anmeldung und Abmeldung.

1. Erstellen Sie eine neue Datei im `./src` Verzeichnis mit `AuthProvider.tsx` dem Namen, und fügen Sie den folgenden Code hinzu.

    ```typescript
    import React from 'react';
    import { UserAgentApplication } from 'msal';

    import { config } from './Config';

    export interface AuthComponentProps {
      error: any;
      isAuthenticated: boolean;
      user: any;
      login: Function;
      logout: Function;
      getAccessToken: Function;
      setError: Function;
    }

    interface AuthProviderState {
      error: any;
      isAuthenticated: boolean;
      user: any;
    }

    export default function withAuthProvider<T extends React.Component<AuthComponentProps>>
      (WrappedComponent: new(props: AuthComponentProps, context?: any) => T): React.ComponentClass {
      return class extends React.Component<any, AuthProviderState> {
        private userAgentApplication: UserAgentApplication;

        constructor(props: any) {
          super(props);
          this.state = {
            error: null,
            isAuthenticated: false,
            user: {}
          };

          // Initialize the MSAL application object
          this.userAgentApplication = new UserAgentApplication({
            auth: {
                clientId: config.appId,
                redirectUri: config.redirectUri
            },
            cache: {
                cacheLocation: "sessionStorage",
                storeAuthStateInCookie: true
            }
          });
        }

        componentDidMount() {
          // If MSAL already has an account, the user
          // is already logged in
          var account = this.userAgentApplication.getAccount();

          if (account) {
            // Enhance user object with data from Graph
            this.getUserProfile();
          }
        }

        render() {
          return <WrappedComponent
            error = { this.state.error }
            isAuthenticated = { this.state.isAuthenticated }
            user = { this.state.user }
            login = { () => this.login() }
            logout = { () => this.logout() }
            getAccessToken = { (scopes: string[]) => this.getAccessToken(scopes)}
            setError = { (message: string, debug: string) => this.setErrorMessage(message, debug)}
            {...this.props} {...this.state} />;
        }

        async login() {
          try {
            // Login via popup
            await this.userAgentApplication.loginPopup(
                {
                  scopes: config.scopes,
                  prompt: "select_account"
              });
            // After login, get the user's profile
            await this.getUserProfile();
          }
          catch(err) {
            this.setState({
              isAuthenticated: false,
              user: {},
              error: this.normalizeError(err)
            });
          }
        }

        logout() {
          this.userAgentApplication.logout();
        }

        async getAccessToken(scopes: string[]): Promise<string> {
          try {
            // Get the access token silently
            // If the cache contains a non-expired token, this function
            // will just return the cached token. Otherwise, it will
            // make a request to the Azure OAuth endpoint to get a token
            var silentResult = await this.userAgentApplication.acquireTokenSilent({
              scopes: scopes
            });

            return silentResult.accessToken;
          } catch (err) {
            // If a silent request fails, it may be because the user needs
            // to login or grant consent to one or more of the requested scopes
            if (this.isInteractionRequired(err)) {
              var interactiveResult = await this.userAgentApplication.acquireTokenPopup({
                scopes: scopes
              });

              return interactiveResult.accessToken;
            } else {
              throw err;
            }
          }
        }

        async getUserProfile() {
          try {
            var accessToken = await this.getAccessToken(config.scopes);

            if (accessToken) {
              // TEMPORARY: Display the token in the error flash
              this.setState({
                isAuthenticated: true,
                error: { message: "Access token:", debug: accessToken }
              });
            }
          }
          catch(err) {
            this.setState({
              isAuthenticated: false,
              user: {},
              error: this.normalizeError(err)
            });
          }
        }

        setErrorMessage(message: string, debug: string) {
          this.setState({
            error: {message: message, debug: debug}
          });
        }

        normalizeError(error: string | Error): any {
          var normalizedError = {};
          if (typeof(error) === 'string') {
            var errParts = error.split('|');
            normalizedError = errParts.length > 1 ?
              { message: errParts[1], debug: errParts[0] } :
              { message: error };
          } else {
            normalizedError = {
              message: error.message,
              debug: JSON.stringify(error)
            };
          }
          return normalizedError;
        }

        isInteractionRequired(error: Error): boolean {
          if (!error.message || error.message.length <= 0) {
            return false;
          }

          return (
            error.message.indexOf('consent_required') > -1 ||
            error.message.indexOf('interaction_required') > -1 ||
            error.message.indexOf('login_required') > -1
          );
        }
      }
    }
    ```

1. Öffnen `./src/App.tsx` Sie und fügen Sie `import` die folgende Anweisung am Anfang der Datei hinzu.

    ```typescript
    import withAuthProvider, { AuthComponentProps } from './AuthProvider';
    ```

1. Ersetzen Sie die `class App extends Component<any> {` -Verbindung durch Folgendes.

    ```typescript
    class App extends Component<AuthComponentProps> {
    ```

1. Ersetzen Sie die `export default App;` -Verbindung durch Folgendes.

    ```typescript
    export default withAuthProvider(App);
    ```

1. Speichern Sie Ihre Änderungen, und aktualisieren Sie den Browser. Klicken Sie auf die Schaltfläche zum Anmelden, um zu `https://login.microsoftonline.com`weitergeleitet zu werden. Melden Sie sich mit Ihrem Microsoft-Konto an, und stimmen Sie den angeforderten Berechtigungen zu. Die APP-Seite sollte aktualisiert werden, wobei das Token angezeigt wird.

### <a name="get-user-details"></a>Benutzerdetails abrufen

In diesem Abschnitt erhalten Sie Informationen zu den Benutzern von Microsoft Graph.

1. Erstellen Sie eine neue Datei im `./src` Verzeichnis mit `GraphService.ts` dem Namen, und fügen Sie den folgenden Code hinzu.

    :::code language="typescript" source="../demo/graph-tutorial/src/GraphService.ts" id="graphServiceSnippet1":::

    Dadurch wird die `getUserDetails`-Funktion implementiert, die das Microsoft Graph-SDK verwendet, um den `/me`-Endpunkt aufzurufen und das Ergebnis zurückzugeben.

1. Öffnen `./src/AuthProvider.tsx` Sie und fügen Sie `import` die folgende Anweisung am Anfang der Datei hinzu.

    ```typescript
    import { getUserDetails } from './GraphService';
    ```

1. Ersetzen Sie die vorhandene `getUserProfile`-Funktion durch den folgenden Code.

    :::code language="typescript" source="../demo/graph-tutorial/src/AuthProvider.tsx" id="getUserProfileSnippet" highlight="6-15":::

1. Speichern Sie Ihre Änderungen, und starten Sie die APP, nachdem Sie die Anmeldung wieder auf der Startseite durchlaufen haben, aber die Benutzeroberfläche sollte geändert werden, um anzugeben, dass Sie angemeldet sind.

    ![Screenshot der Startseite nach dem Anmelden](./images/add-aad-auth-01.png)

1. Klicken Sie in der oberen rechten Ecke auf den Avatar des Benutzers, um auf den **Abmelde** Link zuzugreifen. Wenn Sie auf **Abmelden** klicken, wird die Sitzung zurückgesetzt und Sie kehren zur Startseite zurück.

    ![Screenshot des Dropdown-Menüs mit dem Link „Abmelden“](./images/add-aad-auth-02.png)

## <a name="storing-and-refreshing-tokens"></a>Speichern und Aktualisieren von Token

Zu diesem Zeitpunkt verfügt Ihre Anwendung über ein Zugriffstoken, das in der `Authorization` Kopfzeile von API-aufrufen gesendet wird. Dies ist das Token, das es der App ermöglicht, im Namen des Benutzers auf Microsoft Graph zuzugreifen.

Dieses Token ist jedoch nur kurzzeitig verfügbar. Das Token läuft eine Stunde nach seiner Ausgabe ab. An dieser Stelle kommt das Aktualisierungstoken ins Spiel. Anhand des Aktualisierungstoken ist die App in der Lage, ein neues Zugriffstoken anzufordern, ohne dass der Benutzer sich erneut anmelden muss.

Da die APP die MSAL-Bibliothek verwendet, müssen Sie keine Token-Speicher-oder Aktualisierungslogik implementieren. Das `UserAgentApplication` Token wird in der Browsersitzung zwischengespeichert. Die `acquireTokenSilent` Methode überprüft zuerst das zwischengespeicherte Token, und wenn es nicht abgelaufen ist, wird es zurückgegeben. Wenn er abgelaufen ist, wird das zwischengespeicherte Aktualisierungstoken verwendet, um ein neues zu erhalten. Sie verwenden diese Methode mehr im folgenden Modul.

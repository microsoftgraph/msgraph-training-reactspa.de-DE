<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="11b3b-101">In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="11b3b-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="11b3b-102">Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken zum Aufrufen von Microsoft Graph zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="11b3b-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="11b3b-103">In diesem Schritt werden Sie die Bibliothek der [Microsoft-Authentifizierungsbibliothek](https://github.com/AzureAD/microsoft-authentication-library-for-js) in die Anwendung integrieren.</span><span class="sxs-lookup"><span data-stu-id="11b3b-103">In this step you will integrate the [Microsoft Authentication Library](https://github.com/AzureAD/microsoft-authentication-library-for-js) library into the application.</span></span>

1. <span data-ttu-id="11b3b-104">Erstellen Sie eine neue Datei im `./src` Verzeichnis mit dem Namen `Config.ts` , und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="11b3b-104">Create a new file in the `./src` directory named `Config.ts` and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/Config.example.ts":::

    <span data-ttu-id="11b3b-105">Ersetzen Sie `YOUR_APP_ID_HERE` durch die Anwendungs-ID aus dem Anwendungs Registrierungs Portal.</span><span class="sxs-lookup"><span data-stu-id="11b3b-105">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="11b3b-106">Wenn Sie die Quellcodeverwaltung wie git verwenden, wäre es jetzt ein guter Zeitpunkt, die `Config.ts` Datei aus der Quellcodeverwaltung auszuschließen, um unbeabsichtigtes Auslaufen ihrer APP-ID zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="11b3b-106">If you're using source control such as git, now would be a good time to exclude the `Config.ts` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="11b3b-107">Implementieren der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="11b3b-107">Implement sign-in</span></span>

<span data-ttu-id="11b3b-108">In diesem Abschnitt erstellen Sie einen Authentifizierungsanbieter und implementieren die Anmeldung und Abmeldung.</span><span class="sxs-lookup"><span data-stu-id="11b3b-108">In this section you'll create an authentication provider and implement sign-in and sign-out.</span></span>

1. <span data-ttu-id="11b3b-109">Erstellen Sie eine neue Datei im `./src` Verzeichnis mit dem Namen `AuthProvider.tsx` , und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="11b3b-109">Create a new file in the `./src` directory named `AuthProvider.tsx` and add the following code.</span></span>

    ```typescript
    import React from 'react';
    import { PublicClientApplication } from '@azure/msal-browser';

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
        private publicClientApplication: PublicClientApplication;

        constructor(props: any) {
          super(props);
          this.state = {
            error: null,
            isAuthenticated: false,
            user: {}
          };

          // Initialize the MSAL application object
          this.publicClientApplication = new PublicClientApplication({
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
          const accounts = this.publicClientApplication.getAllAccounts();

          if (accounts && accounts.length > 0) {
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
            await this.publicClientApplication.loginPopup(
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
          this.publicClientApplication.logout();
        }

        async getAccessToken(scopes: string[]): Promise<string> {
          try {
            const accounts = this.publicClientApplication
              .getAllAccounts();

            if (accounts.length <= 0) throw new Error('login_required');
            // Get the access token silently
            // If the cache contains a non-expired token, this function
            // will just return the cached token. Otherwise, it will
            // make a request to the Azure OAuth endpoint to get a token
            var silentResult = await this.publicClientApplication
                .acquireTokenSilent({
                  scopes: scopes,
                  account: accounts[0]
                });

            return silentResult.accessToken;
          } catch (err) {
            // If a silent request fails, it may be because the user needs
            // to login or grant consent to one or more of the requested scopes
            if (this.isInteractionRequired(err)) {
              var interactiveResult = await this.publicClientApplication
                  .acquireTokenPopup({
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
            error.message.indexOf('login_required') > -1 ||
            error.message.indexOf('no_account_in_silent_request') > -1
          );
        }
      }
    }
    ```

1. <span data-ttu-id="11b3b-110">Öffnen `./src/App.tsx` Sie und fügen Sie die folgende `import` Anweisung am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="11b3b-110">Open `./src/App.tsx` and add the following `import` statement to the top of the file.</span></span>

    ```typescript
    import withAuthProvider, { AuthComponentProps } from './AuthProvider';
    ```

1. <span data-ttu-id="11b3b-111">Ersetzen Sie die `class App extends Component<any> {` -Verbindung durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="11b3b-111">Replace the line `class App extends Component<any> {` with the following.</span></span>

    ```typescript
    class App extends Component<AuthComponentProps> {
    ```

1. <span data-ttu-id="11b3b-112">Ersetzen Sie die `export default App;` -Verbindung durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="11b3b-112">Replace the line `export default App;` with the following.</span></span>

    ```typescript
    export default withAuthProvider(App);
    ```

1. <span data-ttu-id="11b3b-113">Speichern Sie Ihre Änderungen, und aktualisieren Sie den Browser.</span><span class="sxs-lookup"><span data-stu-id="11b3b-113">Save your changes and refresh the browser.</span></span> <span data-ttu-id="11b3b-114">Klicken Sie auf die Anmeldeschaltfläche, und es sollte ein Popupfenster angezeigt werden, das geladen wird `https://login.microsoftonline.com` .</span><span class="sxs-lookup"><span data-stu-id="11b3b-114">Click the sign-in button and you should see a pop-up window that loads `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="11b3b-115">Melden Sie sich mit Ihrem Microsoft-Konto an, und stimmen Sie den angeforderten Berechtigungen zu.</span><span class="sxs-lookup"><span data-stu-id="11b3b-115">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="11b3b-116">Die APP-Seite sollte aktualisiert werden, wobei das Token angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="11b3b-116">The app page should refresh, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="11b3b-117">Benutzerdetails abrufen</span><span class="sxs-lookup"><span data-stu-id="11b3b-117">Get user details</span></span>

<span data-ttu-id="11b3b-118">In diesem Abschnitt erhalten Sie Informationen zu den Benutzern von Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="11b3b-118">In this section you will get the user's details from Microsoft Graph.</span></span>

1. <span data-ttu-id="11b3b-119">Erstellen Sie eine neue Datei im `./src` Verzeichnis `GraphService.ts` mit dem Namen, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="11b3b-119">Create a new file in the `./src` directory called `GraphService.ts` and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/GraphService.ts" id="graphServiceSnippet1":::

    <span data-ttu-id="11b3b-120">Dadurch wird die `getUserDetails`-Funktion implementiert, die das Microsoft Graph-SDK verwendet, um den `/me`-Endpunkt aufzurufen und das Ergebnis zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="11b3b-120">This implements the `getUserDetails` function, which uses the Microsoft Graph SDK to call the `/me` endpoint and return the result.</span></span>

1. <span data-ttu-id="11b3b-121">Öffnen `./src/AuthProvider.tsx` Sie und fügen Sie die folgende `import` Anweisung am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="11b3b-121">Open `./src/AuthProvider.tsx` and add the following `import` statement to the top of the file.</span></span>

    ```typescript
    import { getUserDetails } from './GraphService';
    ```

1. <span data-ttu-id="11b3b-122">Ersetzen Sie die vorhandene `getUserProfile`-Funktion durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="11b3b-122">Replace the existing `getUserProfile` function with the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/AuthProvider.tsx" id="getUserProfileSnippet" highlight="6-18":::

1. <span data-ttu-id="11b3b-123">Speichern Sie Ihre Änderungen, und starten Sie die APP, nachdem Sie die Anmeldung wieder auf der Startseite durchlaufen haben, aber die Benutzeroberfläche sollte geändert werden, um anzugeben, dass Sie angemeldet sind.</span><span class="sxs-lookup"><span data-stu-id="11b3b-123">Save your changes and start the app, after sign-in you should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![Screenshot der Startseite nach dem Anmelden](./images/add-aad-auth-01.png)

1. <span data-ttu-id="11b3b-125">Klicken Sie in der oberen rechten Ecke auf den Avatar des Benutzers, um auf den **Abmelde** Link zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="11b3b-125">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="11b3b-126">Wenn Sie auf **Abmelden** klicken, wird die Sitzung zurückgesetzt und Sie kehren zur Startseite zurück.</span><span class="sxs-lookup"><span data-stu-id="11b3b-126">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![Screenshot des Dropdown-Menüs mit dem Link „Abmelden“](./images/add-aad-auth-02.png)

## <a name="storing-and-refreshing-tokens"></a><span data-ttu-id="11b3b-128">Speichern und Aktualisieren von Token</span><span class="sxs-lookup"><span data-stu-id="11b3b-128">Storing and refreshing tokens</span></span>

<span data-ttu-id="11b3b-129">Zu diesem Zeitpunkt verfügt Ihre Anwendung über ein Zugriffstoken, das in der `Authorization` Kopfzeile von API-aufrufen gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="11b3b-129">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="11b3b-130">Dies ist das Token, das es der App ermöglicht, im Namen des Benutzers auf Microsoft Graph zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="11b3b-130">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="11b3b-131">Dieses Token ist jedoch nur kurzzeitig verfügbar.</span><span class="sxs-lookup"><span data-stu-id="11b3b-131">However, this token is short-lived.</span></span> <span data-ttu-id="11b3b-132">Das Token läuft eine Stunde nach seiner Ausgabe ab.</span><span class="sxs-lookup"><span data-stu-id="11b3b-132">The token expires an hour after it is issued.</span></span> <span data-ttu-id="11b3b-133">An dieser Stelle kommt das Aktualisierungstoken ins Spiel.</span><span class="sxs-lookup"><span data-stu-id="11b3b-133">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="11b3b-134">Anhand des Aktualisierungstoken ist die App in der Lage, ein neues Zugriffstoken anzufordern, ohne dass der Benutzer sich erneut anmelden muss.</span><span class="sxs-lookup"><span data-stu-id="11b3b-134">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="11b3b-135">Da die APP die MSAL-Bibliothek verwendet, müssen Sie keine Token-Speicher-oder Aktualisierungslogik implementieren.</span><span class="sxs-lookup"><span data-stu-id="11b3b-135">Because the app is using the MSAL library, you do not have to implement any token storage or refresh logic.</span></span> <span data-ttu-id="11b3b-136">Das `PublicClientApplication` Token wird in der Browsersitzung zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="11b3b-136">The `PublicClientApplication` caches the token in the browser session.</span></span> <span data-ttu-id="11b3b-137">Die `acquireTokenSilent` Methode überprüft zuerst das zwischengespeicherte Token, und wenn es nicht abgelaufen ist, wird es zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="11b3b-137">The `acquireTokenSilent` method first checks the cached token, and if it is not expired, it returns it.</span></span> <span data-ttu-id="11b3b-138">Wenn er abgelaufen ist, wird das zwischengespeicherte Aktualisierungstoken verwendet, um ein neues zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="11b3b-138">If it is expired, it uses the cached refresh token to obtain a new one.</span></span> <span data-ttu-id="11b3b-139">Sie verwenden diese Methode mehr im folgenden Modul.</span><span class="sxs-lookup"><span data-stu-id="11b3b-139">You'll use this method more in the following module.</span></span>

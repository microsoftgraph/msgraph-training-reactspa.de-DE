<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="713c2-101">In dieser Übung erstellen Sie mithilfe des Azure Active Directory Admin Center eine neue Azure AD-Webanwendungs Registrierung.</span><span class="sxs-lookup"><span data-stu-id="713c2-101">In this exercise, you will create a new Azure AD web application registration using the Azure Active Directory admin center.</span></span>

1. <span data-ttu-id="713c2-102">Öffnen Sie einen Browser, und navigieren Sie zum [Azure Active Directory Admin Center](https://aad.portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="713c2-102">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="713c2-103">Melden Sie sich mit einem **persönlichen Konto** (auch: Microsoft-Konto) oder einem **Geschäfts- oder Schulkonto** an.</span><span class="sxs-lookup"><span data-stu-id="713c2-103">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="713c2-104">Wählen Sie in der linken Navigationsleiste **Azure Active Directory** aus, und wählen Sie dann **App** -Registrierungen unter **Manage**aus.</span><span class="sxs-lookup"><span data-stu-id="713c2-104">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="713c2-105">Ein Screenshot der APP-Registrierungen</span><span class="sxs-lookup"><span data-stu-id="713c2-105">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

1. <span data-ttu-id="713c2-106">Wählen Sie **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="713c2-106">Select **New registration**.</span></span> <span data-ttu-id="713c2-107">Legen Sie auf der Seite **Anwendung registrieren** die Werte wie folgt fest.</span><span class="sxs-lookup"><span data-stu-id="713c2-107">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="713c2-108">Legen Sie **Name** auf `React Graph Tutorial` fest.</span><span class="sxs-lookup"><span data-stu-id="713c2-108">Set **Name** to `React Graph Tutorial`.</span></span>
    - <span data-ttu-id="713c2-109">Legen Sie **Unterstützte Kontotypen** auf **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** fest.</span><span class="sxs-lookup"><span data-stu-id="713c2-109">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="713c2-110">Legen Sie unter **Umleitungs-URI** die erste Dropdownoption auf `Web` fest, und legen Sie den Wert auf `http://localhost:3000` fest.</span><span class="sxs-lookup"><span data-stu-id="713c2-110">Under **Redirect URI**, set the first drop-down to `Web` and set the value to `http://localhost:3000`.</span></span>

    ![Screenshot der Seite "Anwendung registrieren"](./images/aad-register-an-app.png)

1. <span data-ttu-id="713c2-112">Wählen Sie **Registrieren** aus.</span><span class="sxs-lookup"><span data-stu-id="713c2-112">Choose **Register**.</span></span> <span data-ttu-id="713c2-113">Kopieren Sie auf der Seite **Diagramm-Lernprogramm reagieren** den Wert der **Anwendungs-ID (Client)** , und speichern Sie Sie, um Sie im nächsten Schritt zu benötigen.</span><span class="sxs-lookup"><span data-stu-id="713c2-113">On the **React Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Ein Screenshot der Anwendungs-ID der neuen App-Registrierung](./images/aad-application-id.png)

1. <span data-ttu-id="713c2-115">Wählen Sie unter **Verwalten** die Option **Authentifizierung** aus.</span><span class="sxs-lookup"><span data-stu-id="713c2-115">Select **Authentication** under **Manage**.</span></span> <span data-ttu-id="713c2-116">Suchen Sie den **impliziten Grant** -Abschnitt, und aktivieren Sie **Zugriffstoken** und **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="713c2-116">Locate the **Implicit grant** section and enable **Access tokens** and **ID tokens**.</span></span> <span data-ttu-id="713c2-117">Wählen Sie **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="713c2-117">Choose **Save**.</span></span>

    ![Screenshot des impliziten Grant-Abschnitts](./images/aad-implicit-grant.png)

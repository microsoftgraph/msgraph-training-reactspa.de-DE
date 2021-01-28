# <a name="getting-started-with-create-react-app"></a><span data-ttu-id="89f7d-101">Erste Schritte mit dem Erstellen einer React-App</span><span class="sxs-lookup"><span data-stu-id="89f7d-101">Getting Started with Create React App</span></span>

<span data-ttu-id="89f7d-102">Dieses Projekt wurde mit create [React App](https://github.com/facebook/create-react-app)bootstrapped .</span><span class="sxs-lookup"><span data-stu-id="89f7d-102">This project was bootstrapped with [Create React App](https://github.com/facebook/create-react-app).</span></span>

## <a name="available-scripts"></a><span data-ttu-id="89f7d-103">Verfügbare Skripts</span><span class="sxs-lookup"><span data-stu-id="89f7d-103">Available Scripts</span></span>

<span data-ttu-id="89f7d-104">Im Projektverzeichnis können Sie:</span><span class="sxs-lookup"><span data-stu-id="89f7d-104">In the project directory, you can run:</span></span>

### `yarn start`

<span data-ttu-id="89f7d-105">Führt die App im Entwicklungsmodus aus.</span><span class="sxs-lookup"><span data-stu-id="89f7d-105">Runs the app in the development mode.</span></span>\
<span data-ttu-id="89f7d-106">Öffnen [http://localhost:3000](http://localhost:3000) Sie diese, um sie im Browser anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="89f7d-106">Open [http://localhost:3000](http://localhost:3000) to view it in the browser.</span></span>

<span data-ttu-id="89f7d-107">Die Seite wird neu geladen, wenn Sie Bearbeitungen erstellen.</span><span class="sxs-lookup"><span data-stu-id="89f7d-107">The page will reload if you make edits.</span></span>\
<span data-ttu-id="89f7d-108">Außerdem werden alle Lintfehler in der Konsole angezeigt.</span><span class="sxs-lookup"><span data-stu-id="89f7d-108">You will also see any lint errors in the console.</span></span>

### `yarn test`

<span data-ttu-id="89f7d-109">Startet die Testlaufumgebung im interaktiven Watch-Modus.</span><span class="sxs-lookup"><span data-stu-id="89f7d-109">Launches the test runner in the interactive watch mode.</span></span>\
<span data-ttu-id="89f7d-110">Weitere Informationen finden Sie im Abschnitt [zum Ausführen](https://facebook.github.io/create-react-app/docs/running-tests) von Tests.</span><span class="sxs-lookup"><span data-stu-id="89f7d-110">See the section about [running tests](https://facebook.github.io/create-react-app/docs/running-tests) for more information.</span></span>

### `yarn build`

<span data-ttu-id="89f7d-111">Erstellt die App für die Produktion im `build` Ordner.</span><span class="sxs-lookup"><span data-stu-id="89f7d-111">Builds the app for production to the `build` folder.</span></span>\
<span data-ttu-id="89f7d-112">Es bündelt React ordnungsgemäß im Produktionsmodus und optimiert den Build für eine optimale Leistung.</span><span class="sxs-lookup"><span data-stu-id="89f7d-112">It correctly bundles React in production mode and optimizes the build for the best performance.</span></span>

<span data-ttu-id="89f7d-113">Der Build wird minified, und die Dateinamen enthalten die Hashes.</span><span class="sxs-lookup"><span data-stu-id="89f7d-113">The build is minified and the filenames include the hashes.</span></span>\
<span data-ttu-id="89f7d-114">Ihre App kann bereitgestellt werden!</span><span class="sxs-lookup"><span data-stu-id="89f7d-114">Your app is ready to be deployed!</span></span>

<span data-ttu-id="89f7d-115">Weitere Informationen finden Sie im Abschnitt [zur](https://facebook.github.io/create-react-app/docs/deployment) Bereitstellung.</span><span class="sxs-lookup"><span data-stu-id="89f7d-115">See the section about [deployment](https://facebook.github.io/create-react-app/docs/deployment) for more information.</span></span>

### `yarn eject`

<span data-ttu-id="89f7d-116">**Hinweis: Dies ist ein one-way-Vorgang. Sobald `eject` Sie sind, können Sie nicht mehr zurück!**</span><span class="sxs-lookup"><span data-stu-id="89f7d-116">**Note: this is a one-way operation. Once you `eject`, you can’t go back!**</span></span>

<span data-ttu-id="89f7d-117">Wenn Sie mit dem Buildtool und den Konfigurationsoptionen nicht zufrieden sind, können Sie `eject` dies jederzeit.</span><span class="sxs-lookup"><span data-stu-id="89f7d-117">If you aren’t satisfied with the build tool and configuration choices, you can `eject` at any time.</span></span> <span data-ttu-id="89f7d-118">Mit diesem Befehl wird die einzelne Buildabhängigkeit aus Ihrem Projekt entfernt.</span><span class="sxs-lookup"><span data-stu-id="89f7d-118">This command will remove the single build dependency from your project.</span></span>

<span data-ttu-id="89f7d-119">Stattdessen werden alle Konfigurationsdateien und transitiven Abhängigkeiten (Webpack, Babel, ESLint usw.) direkt in Ihr Projekt kopiert, damit Sie die vollständige Kontrolle über diese haben.</span><span class="sxs-lookup"><span data-stu-id="89f7d-119">Instead, it will copy all the configuration files and the transitive dependencies (webpack, Babel, ESLint, etc) right into your project so you have full control over them.</span></span> <span data-ttu-id="89f7d-120">Alle Befehle außer funktionieren weiterhin, verweisen jedoch auf die kopierten `eject` Skripts, damit Sie sie optimieren können.</span><span class="sxs-lookup"><span data-stu-id="89f7d-120">All of the commands except `eject` will still work, but they will point to the copied scripts so you can tweak them.</span></span> <span data-ttu-id="89f7d-121">An diesem Punkt sind Sie allein.</span><span class="sxs-lookup"><span data-stu-id="89f7d-121">At this point you’re on your own.</span></span>

<span data-ttu-id="89f7d-122">Sie müssen niemals `eject` verwenden.</span><span class="sxs-lookup"><span data-stu-id="89f7d-122">You don’t have to ever use `eject`.</span></span> <span data-ttu-id="89f7d-123">Der behandelte Featuresatz eignet sich für kleine und mittlere Bereitstellungen, und Sie sollten sich nicht verpflichtet fühlen, dieses Feature zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="89f7d-123">The curated feature set is suitable for small and middle deployments, and you shouldn’t feel obligated to use this feature.</span></span> <span data-ttu-id="89f7d-124">Wir wissen jedoch, dass dieses Tool nicht hilfreich wäre, wenn Sie es nicht anpassen könnten, wenn Sie dafür bereit sind.</span><span class="sxs-lookup"><span data-stu-id="89f7d-124">However we understand that this tool wouldn’t be useful if you couldn’t customize it when you are ready for it.</span></span>

## <a name="learn-more"></a><span data-ttu-id="89f7d-125">Weitere Informationen</span><span class="sxs-lookup"><span data-stu-id="89f7d-125">Learn More</span></span>

<span data-ttu-id="89f7d-126">Weitere Informationen finden Sie in der [Dokumentation zum Erstellen einer React-App.](https://facebook.github.io/create-react-app/docs/getting-started)</span><span class="sxs-lookup"><span data-stu-id="89f7d-126">You can learn more in the [Create React App documentation](https://facebook.github.io/create-react-app/docs/getting-started).</span></span>

<span data-ttu-id="89f7d-127">Informationen zu React finden Sie in der [React-Dokumentation.](https://reactjs.org/)</span><span class="sxs-lookup"><span data-stu-id="89f7d-127">To learn React, check out the [React documentation](https://reactjs.org/).</span></span>

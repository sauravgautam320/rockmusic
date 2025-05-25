
*   **`core`**: Contains shared code like app configurations (colors, strings, text styles, asset paths) and core services (e.g., dependency injection setup).
*   **`data`**: Manages data operations.
    *   `models`: Defines the data structures (e.g., `MusicService`).
    *   `repositories`: Abstracts data sources and provides a clean API for data access to the ViewModels. `MusicServiceRepositoryImpl` handles communication with Firebase Firestore.
*   **`presentation`**: Contains all UI-related code.
    *   `viewmodels`: Holds the presentation logic and state for the views. `HomeViewModel` fetches and prepares data for the `HomeScreen`.
    *   `views`: Represents the screens of the application.
    *   `widgets`: Contains custom, reusable UI components used across different screens or multiple times on a single screen.
*   **`assets`**: Stores static files like custom fonts and images/icons.

## Approach & Architecture

### MVVM (Model-View-ViewModel)

The application is structured using the MVVM pattern:

*   **Model**: Represents the data and business logic (e.g., `MusicServiceModel`, data fetching logic within the repository).
*   **View**: The UI part of the application (Flutter widgets in `views/` and `widgets/`). It observes the ViewModel for state changes and updates itself accordingly. It also forwards user interactions to the ViewModel.
*   **ViewModel**: Acts as a bridge between the View and the Model. It exposes data from the Model to the View in a view-specific format and handles user interactions passed from the View. `HomeViewModel` is an example.

### State Management

*   **Provider**: Used for state management. `ChangeNotifierProvider` and `Consumer` (or `context.watch`) are used to provide ViewModels to the widget tree and listen for changes to rebuild the UI. `HomeViewModel` extends `ChangeNotifier`.

### Dependency Injection

*   **GetIt**: Used as a service locator for dependency injection. This helps in decoupling components and makes testing easier by allowing mocks to be injected. Repositories and ViewModels are registered in `service_locator.dart` and resolved when needed.

### Firebase Integration

*   **Firebase Firestore**: Used as the backend database to store and retrieve the list of music services.
*   The `MusicServiceRepositoryImpl` class is responsible for all Firestore interactions (fetching the `services` collection).
*   Data (title, description, image filenames) for each service card is stored in Firestore documents.
*   The app dynamically loads this data, maps it to `MusicService` models, and displays it in the UI. This allows for easy updates to the service list without needing to rebuild the app.

### Custom Assets and Fonts

*   The UI heavily relies on custom image assets for icons, backgrounds, and decorative elements, stored in `assets/images/`.
*   Custom fonts (Syne and Lobster) are used for typography, configured in `pubspec.yaml` and applied via `ThemeData` and `AppTextStyles`.

## Setup & Running the Project

1.  **Clone the Repository:**
    ```bash
    git clone <repository_url>
    cd rockmusic
    ```

2.  **Flutter SDK:** Ensure you have the Flutter SDK installed.

3.  **Firebase Setup:**
    *   Create a Firebase project at [console.firebase.google.com](https://console.firebase.google.com/).
    *   Register your Flutter app (Android, iOS) with this Firebase project.
    *   Download the `google-services.json` (for Android) and `GoogleService-Info.plist` (for iOS) configuration files.
    *   Place `google-services.json` into the `android/app/` directory.
    *   Place `GoogleService-Info.plist` into the `ios/Runner/` directory (use Xcode to add it to the Runner project).
    *   **Important**: Run `flutterfire configure` (if you have FlutterFire CLI installed) in the project root to automatically fetch and set up `lib/firebase_options.dart`. If not, ensure this file is correctly populated with your Firebase project's web configurations.
    *   **Enable Firestore:** In your Firebase project console, go to "Firestore Database" and create a database. Start in "test mode" for initial development (you can add security rules later).

4.  **Populate Firestore Data:**
    *   In your Firestore database, create a collection named `services`.
    *   For each music service, add a new document to this collection. Each document should have the following fields:
        *   `title` (String): e.g., "Music Production"
        *   `description` (String): e.g., "Custom instrumentals & film scoring"
        *   `cardBackgroundImageName` (String): Filename of the background image for the card (e.g., `music_prod.png`). This file must exist in `assets/images/`.
        *   `iconNameForCard` (String): Filename of the small icon for the card (e.g., `music_prod_icon.png`). This file must exist in `assets/images/`.
        *   `orderIndex` (Number, optional): For controlling the display order (e.g., `1`, `2`).

5.  **Install Dependencies:**
    ```bash
    flutter pub get
    ```

6.  **Run the App:**
    ```bash
    flutter run
    ```

This should launch the app on your connected device or emulator, fetching and displaying the music services from your Firestore database.
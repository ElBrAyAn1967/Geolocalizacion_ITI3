# 🌍 Proyecto: Aplicación de Geolocalización con Android Studio

## 📋 Plan de desarrollo

### **Fase 1: Planificación**
1. **Definir requerimientos**:
   - Mostrar un mapa interactivo.
   - Calcular rutas óptimas entre dos puntos.
   - Permitir búsqueda por coordenadas o nombres de lugares.
   - Integrar inicio de sesión y registro con MongoDB.
   
2. **Diseñar la estructura**:
   - Pantallas principales:
     - **Login/Registro**
     - **Mapa principal** (con controles para ingresar destinos).
     - **Detalles de ruta**.
     - **Historial de rutas**.

3. **Planificación del backend**:
   - Construir un servidor con Node.js para manejar autenticación y almacenamiento de datos.
   - Usar MongoDB para guardar usuarios y rutas.

---

### **Fase 2: Configuración del proyecto**
1. **Crea un nuevo proyecto en Android Studio**:
   - Lenguaje: **Java**
   - SDK mínimo: **Android 6.0 (API 23)**

2. **Configura dependencias**:
   - Añade las siguientes librerías en `build.gradle`:
     ```gradle
     implementation 'com.mapbox.maps:android:10.10.0'
     implementation 'com.squareup.retrofit2:retrofit:2.9.0'
     implementation 'com.squareup.retrofit2:converter-gson:2.9.0'
     implementation 'org.mongodb:mongodb-driver-sync:4.10.0'
     ```

3. **Habilita permisos en `AndroidManifest.xml`**:
   ```xml
   <uses-permission android:name="android.permission.INTERNET" />
   <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
   ```

---

### **Fase 3: Desarrollo del frontend**

#### **1. Diseñar la UI (XML)**
- **`activity_main.xml`**: Muestra el mapa y controles para buscar rutas.
- **`activity_login.xml`**: Pantalla de login y registro.
- **`activity_routedetail.xml`**: Vista de detalles de la ruta.

#### **2. Implementar el mapa (Mapbox SDK)**
- Inicializa el mapa en `MainActivity.java`:
  ```java
  MapView mapView = findViewById(R.id.mapView);
  mapView.getMapboxMap().loadStyleUri(Style.MAPBOX_STREETS);
  ```

- Agrega funcionalidad para buscar rutas con coordenadas:
  ```java
  Point origin = Point.fromLngLat(-99.1332, 19.4326); // Ejemplo: CDMX
  Point destination = Point.fromLngLat(-98.9817, 19.3575); // Ejemplo: Puebla

  NavigationRoute.builder(this)
      .accessToken(getString(R.string.mapbox_access_token))
      .origin(origin)
      .destination(destination)
      .build()
      .getRoute(new RouteCallback() {
          @Override
          public void onResponse(@NonNull DirectionsResponse response) {
              // Mostrar la ruta en el mapa
          }
      });
  ```

---

### **Fase 4: Desarrollo del backend**

#### **1. Configuración del servidor Node.js**
- Instala dependencias:
  ```bash
  npm install express mongoose jsonwebtoken bcrypt
  ```

- Crea endpoints básicos en `server.js`:
  ```javascript
  app.post('/login', (req, res) => {
      const { email, password } = req.body;
      // Validar usuario y generar token JWT
  });

  app.post('/register', (req, res) => {
      const { email, password } = req.body;
      // Registrar usuario en MongoDB
  });

  app.post('/routes', (req, res) => {
      const { userId, origin, destination } = req.body;
      // Guardar ruta en la base de datos
  });
  ```

#### **2. Estructura de MongoDB**
- Crea colecciones para:
  - **Usuarios**: Guardar credenciales.
  - **Rutas**: Guardar rutas asociadas a usuarios.

Ejemplo de modelo:
```javascript
const RouteSchema = new mongoose.Schema({
    userId: String,
    origin: String,
    destination: String,
    createdAt: { type: Date, default: Date.now }
});
```

---

### **Fase 5: Conexión del frontend con el backend**

#### **1. Configurar Retrofit**
- Agrega una instancia básica en Android:
  ```java
  Retrofit retrofit = new Retrofit.Builder()
      .baseUrl("https://tu-servidor.com/")
      .addConverterFactory(GsonConverterFactory.create())
      .build();
  ```

- Realiza una llamada al endpoint de login:
  ```java
  Call<LoginResponse> call = retrofit.create(ApiService.class).login(new LoginRequest(email, password));
  call.enqueue(new Callback<LoginResponse>() {
      @Override
      public void onResponse(Call<LoginResponse> call, Response<LoginResponse> response) {
          if (response.isSuccessful()) {
              // Almacenar token en SharedPreferences
          }
      }

      @Override
      public void onFailure(Call<LoginResponse> call, Throwable t) {
          // Manejar error
      }
  });
  ```

---

### **Fase 6: Pruebas y optimización**
1. **Pruebas**:
   - Asegúrate de que el mapa carga correctamente.
   - Verifica el cálculo de rutas.
   - Prueba el inicio de sesión y almacenamiento de rutas.

2. **Optimización**:
   - Usa herramientas como Logcat para depurar.
   - Implementa almacenamiento en caché para minimizar el uso de datos.

---

## 🗺️ Mapa relacional del flujo de información
```plaintext
Usuarios ↔ Backend ↔ MongoDB
          ↓
       Android App ↔ Mapbox SDK ↔ OpenStreetMap

Flujo de acciones:
1. Usuario se registra o inicia sesión (MongoDB valida credenciales).
2. Usuario interactúa con el mapa (Mapbox muestra el mapa y OpenStreetMap provee datos).
3. Usuario selecciona origen y destino.
4. Backend guarda rutas en MongoDB.
5. Historial de rutas se sincroniza con la app.
```

---

## 🌟 Bonus: Funcionalidades futuras
- **Modo sin conexión**: Descarga mapas locales.
- **Notificaciones**: Alertas de cambios en la ruta o tráfico.
- **Realidad aumentada**: Navegación visual interactiva.

¡Ahora tienes un plan completo para tu app de geolocalización! 🚀

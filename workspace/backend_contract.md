# Contrato de Backend: Dashboard Semillero Digital

## 1. Información General

**Proyecto:** Dashboard Semillero Digital  
**Tecnología:** Node.js + TypeScript  
**Modos de operación:**
- Modo Google: Integración con Google Classroom API
- Modo Mock: Dataset simulado (2 cursos, ~300 alumnos, tareas variadas)

## 2. Estructura de Directorios

```
/backend/
├── src/
│   ├── config/                  # Configuración de la aplicación
│   │   ├── index.ts             # Exporta configuración unificada
│   │   ├── app.config.ts        # Configuración general
│   │   ├── google.config.ts     # Configuración para Google API
│   │   ├── auth.config.ts       # Configuración de autenticación OAuth
│   │   └── mock.config.ts       # Configuración para modo Mock
│   ├── api/                     # Capa de API REST
│   │   ├── routes/              # Definición de rutas
│   │   │   ├── index.ts         # Exporta todas las rutas
│   │   │   ├── auth.routes.ts   # Rutas de autenticación
│   │   │   ├── courses.routes.ts # Rutas para cursos
│   │   │   ├── students.routes.ts # Rutas para estudiantes
│   │   │   └── assignments.routes.ts # Rutas para tareas
│   │   ├── controllers/         # Controladores de API
│   │   │   ├── auth.controller.ts # Controlador de autenticación
│   │   │   ├── courses.controller.ts
│   │   │   ├── students.controller.ts
│   │   │   └── assignments.controller.ts
│   │   └── middlewares/         # Middlewares de API
│   │       ├── error.middleware.ts # Manejo uniforme de errores
│   │       ├── auth.middleware.ts  # Autenticación y validación de sesión
│   │       └── validation.middleware.ts # Validación de datos
│   ├── services/                # Capa de servicios
│   │   ├── interfaces/          # Interfaces para servicios
│   │   │   ├── course.interface.ts
│   │   │   ├── student.interface.ts
│   │   │   ├── assignment.interface.ts
│   │   │   └── auth.interface.ts # Interfaz para autenticación
│   │   ├── auth/                # Servicios de autenticación
│   │   │   └── auth.service.ts  # Servicio de autenticación OAuth
│   │   ├── google/              # Servicios para Google Classroom
│   │   │   ├── google.service.ts # Servicio principal
│   │   │   ├── courses.service.ts
│   │   │   ├── students.service.ts
│   │   │   └── assignments.service.ts
│   │   └── mock/               # Servicios para modo Mock
│   │       ├── mock.service.ts  # Servicio principal
│   │       ├── courses.service.ts
│   │       ├── students.service.ts
│   │       └── assignments.service.ts
│   ├── models/                  # Modelos de datos
│   │   ├── course.model.ts
│   │   ├── student.model.ts
│   │   ├── assignment.model.ts
│   │   └── user.model.ts       # Modelo de usuario autenticado
│   ├── utils/                   # Utilidades
│   │   ├── response.util.ts     # Formato uniforme de respuesta
│   │   ├── error.util.ts        # Clases de error personalizadas
│   │   ├── logger.util.ts       # Sistema de logging
│   │   └── session.util.ts      # Utilidades para manejo de sesión
│   └── app.ts                   # Punto de entrada de la aplicación
├── tests/
│   ├── unit/                    # Tests unitarios
│   │   ├── services/
│   │   │   ├── google/
│   │   │   └── mock/
│   │   └── controllers/
│   ├── integration/             # Tests de integración
│   │   ├── api/
│   │   └── services/
│   ├── e2e/                     # Tests end-to-end
│   └── mocks/                   # Datos mock para pruebas
│       ├── courses.mock.ts
│       ├── students.mock.ts
│       └── assignments.mock.ts
├── .env.example                 # Ejemplo de variables de entorno
├── .gitignore                   # Archivos ignorados por git
├── package.json                 # Dependencias y scripts
├── tsconfig.json                # Configuración de TypeScript
└── jest.config.js               # Configuración de tests
```

## 3. Componentes Principales

### 3.1. Controladores

Los controladores manejan las peticiones HTTP y delegan la lógica de negocio a los servicios.

**Estructura uniforme:**
```typescript
// Ejemplo: courses.controller.ts
import { Request, Response, NextFunction } from 'express';
import { ServiceFactory } from '../services/service.factory';
import { ResponseUtil } from '../utils/response.util';

export class CoursesController {
  async getCourses(req: Request, res: Response, next: NextFunction): Promise<void> {
    try {
      const service = ServiceFactory.getCourseService();
      const courses = await service.getCourses();
      
      ResponseUtil.success(res, courses);
    } catch (error) {
      next(error);
    }
  }
  
  // Otros métodos: getCourseById, getStudentsInCourse, etc.
}
```

### 3.2. Servicios

Los servicios implementan la lógica de negocio y se dividen en dos implementaciones:

#### Factory de Servicios
```typescript
// service.factory.ts
import { config } from '../config';
import { CourseService } from './interfaces/course.interface';
import { GoogleCourseService } from './google/courses.service';
import { MockCourseService } from './mock/courses.service';
import { OAuth2Client } from 'google-auth-library';
import { createOAuth2Client } from '../config/auth.config';

export class ServiceFactory {
  // Obtiene el cliente OAuth autenticado si hay tokens disponibles en la sesión
  static getAuthenticatedClient(session: any): OAuth2Client | null {
    if (config.MODE !== 'GOOGLE' || !session?.tokens?.access_token) {
      return null;
    }
    
    const oauth2Client = createOAuth2Client();
    oauth2Client.setCredentials({
      access_token: session.tokens.access_token,
      refresh_token: session.tokens.refresh_token,
      expiry_date: session.tokens.expiry_date
    });
    
    return oauth2Client;
  }
  
  static getCourseService(session?: any): CourseService {
    if (config.MODE === 'GOOGLE' && session) {
      const client = this.getAuthenticatedClient(session);
      return new GoogleCourseService(client);
    }
    return new MockCourseService();
  }
  
  // Otros métodos factory para estudiantes, tareas, etc.
  static getStudentService(session?: any): StudentService {
    if (config.MODE === 'GOOGLE' && session) {
      const client = this.getAuthenticatedClient(session);
      return new GoogleStudentService(client);
    }
    return new MockStudentService();
  }
  
  static getAssignmentService(session?: any): AssignmentService {
    if (config.MODE === 'GOOGLE' && session) {
      const client = this.getAuthenticatedClient(session);
      return new GoogleAssignmentService(client);
    }
    return new MockAssignmentService();
  }
}
```

#### Servicios Google
Implementan la integración con Google Classroom API.

```typescript
// google/courses.service.ts
import { CourseService } from '../interfaces/course.interface';
import { Course } from '../../models/course.model';
import { googleClient } from '../../config/google.config';

export class GoogleCourseService implements CourseService {
  async getCourses(): Promise<Course[]> {
    const response = await googleClient.courses.list({
      pageSize: 100,
      courseStates: ['ACTIVE']
    });
    
    return response.data.courses.map(course => ({
      id: course.id,
      name: course.name,
      section: course.section,
      // Mapeo de otros campos
    }));
  }
  
  // Implementación de otros métodos de la interfaz
}
```

#### Servicios Mock
Utilizan datos simulados para desarrollo y pruebas.

```typescript
// mock/courses.service.ts
import { CourseService } from '../interfaces/course.interface';
import { Course } from '../../models/course.model';
import { mockCourses } from '../../config/mock.config';

export class MockCourseService implements CourseService {
  async getCourses(): Promise<Course[]> {
    return mockCourses;
  }
  
  // Implementación de otros métodos de la interfaz
}
```

### 3.3. Ruteo de API REST

Definición de rutas usando Express Router.

```typescript
// routes/courses.routes.ts
import { Router } from 'express';
import { CoursesController } from '../controllers/courses.controller';
import { authMiddleware } from '../middlewares/auth.middleware';
import { validationMiddleware } from '../middlewares/validation.middleware';

const router = Router();
const controller = new CoursesController();

router.get('/', authMiddleware, controller.getCourses);
router.get('/:id', authMiddleware, validationMiddleware, controller.getCourseById);
router.get('/:id/students', authMiddleware, validationMiddleware, controller.getStudentsInCourse);
// Otras rutas

export default router;
```

### 3.4. Configuración

La configuración permite cambiar entre modos de operación y otras opciones.

```typescript
// config/app.config.ts
import dotenv from 'dotenv';
dotenv.config();

export const appConfig = {
  PORT: process.env.PORT || 3000,
  MODE: process.env.MODE || 'MOCK', // 'GOOGLE' o 'MOCK'
  NODE_ENV: process.env.NODE_ENV || 'development',
  LOG_LEVEL: process.env.LOG_LEVEL || 'info'
};
```

El archivo `.env.example` incluye las variables necesarias para la configuración:

```
# Puerto del servidor
PORT=8000

# Modo de operación: GOOGLE (API real) o MOCK (datos simulados)
MODE=MOCK

# Variables para autenticación OAuth con Google
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=
GOOGLE_REDIRECT_URI=http://localhost:8000/auth/google/callback

# Secreto para firmar las cookies de sesión
SESSION_SECRET=superseguro

# Nivel de logs (debug, info, warn, error)
LOG_LEVEL=info
```

Cuando `MODE=GOOGLE`, el sistema utiliza la autenticación OAuth real con Google y accede a la API de Google Classroom. Cuando `MODE=MOCK`, el sistema utiliza un dataset simulado y no requiere autenticación.

### 3.5. Manejo de Errores Uniforme

Sistema centralizado de manejo de errores.

```typescript
// utils/error.util.ts
export class AppError extends Error {
  statusCode: number;
  isOperational: boolean;
  
  constructor(message: string, statusCode: number) {
    super(message);
    this.statusCode = statusCode;
    this.isOperational = true;
    
    Error.captureStackTrace(this, this.constructor);
  }
}

export class NotFoundError extends AppError {
  constructor(resource: string) {
    super(`${resource} no encontrado`, 404);
  }
}

// Otras clases de error específicas
```

```typescript
// middlewares/error.middleware.ts
import { Request, Response, NextFunction } from 'express';
import { AppError } from '../utils/error.util';
import { logger } from '../utils/logger.util';

export const errorMiddleware = (
  err: Error,
  req: Request,
  res: Response,
  next: NextFunction
): void => {
  if (err instanceof AppError) {
    logger.warn(`${err.statusCode} - ${err.message}`);
    
    res.status(err.statusCode).json({
      data: null,
      meta: {
        success: false,
        message: err.message,
        code: err.statusCode
      }
    });
    return;
  }
  
  // Error no controlado
  logger.error(`500 - ${err.message}`);
  res.status(500).json({
    data: null,
    meta: {
      success: false,
      message: 'Error interno del servidor',
      code: 500
    }
  });
};
```

### 3.6. Formato de Respuesta Uniforme

Todas las respuestas siguen un formato estándar.

```typescript
// utils/response.util.ts
import { Response } from 'express';

export class ResponseUtil {
  static success(res: Response, data: any, message = 'Operación exitosa'): void {
    res.status(200).json({
      data,
      meta: {
        success: true,
        message,
        code: 200
      }
    });
  }
  
  static created(res: Response, data: any, message = 'Recurso creado exitosamente'): void {
    res.status(201).json({
      data,
      meta: {
        success: true,
        message,
        code: 201
      }
    });
  }
  
  // Otros métodos para diferentes tipos de respuestas
}
```

### 3.7. Autenticación OAuth con Google

El sistema implementa autenticación OAuth 2.0 con Google para acceder a la API de Google Classroom.

#### 3.7.1. Configuración OAuth

```typescript
// config/auth.config.ts
import dotenv from 'dotenv';
import { OAuth2Client } from 'google-auth-library';

dotenv.config();

export const authConfig = {
  GOOGLE_CLIENT_ID: process.env.GOOGLE_CLIENT_ID || '',
  GOOGLE_CLIENT_SECRET: process.env.GOOGLE_CLIENT_SECRET || '',
  GOOGLE_REDIRECT_URI: process.env.GOOGLE_REDIRECT_URI || 'http://localhost:8000/auth/google/callback',
  SESSION_SECRET: process.env.SESSION_SECRET || 'superseguro',
  SCOPES: [
    'https://www.googleapis.com/auth/userinfo.email',
    'https://www.googleapis.com/auth/userinfo.profile',
    'https://www.googleapis.com/auth/classroom.courses.readonly',
    'https://www.googleapis.com/auth/classroom.coursework.me.readonly',
    'https://www.googleapis.com/auth/classroom.rosters.readonly',
    'https://www.googleapis.com/auth/classroom.student-submissions.me.readonly'
  ]
};

export const createOAuth2Client = (): OAuth2Client => {
  return new OAuth2Client(
    authConfig.GOOGLE_CLIENT_ID,
    authConfig.GOOGLE_CLIENT_SECRET,
    authConfig.GOOGLE_REDIRECT_URI
  );
};
```

#### 3.7.2. Rutas de Autenticación

```typescript
// routes/auth.routes.ts
import { Router } from 'express';
import { AuthController } from '../controllers/auth.controller';

const router = Router();
const controller = new AuthController();

// Inicia el flujo de autenticación OAuth con Google
router.get('/google', controller.googleAuth);

// Callback para recibir el código de autorización de Google
router.get('/google/callback', controller.googleCallback);

// Verifica el estado de la sesión actual
router.get('/status', controller.getAuthStatus);

// Cierra la sesión actual
router.post('/logout', controller.logout);

export default router;
```

#### 3.7.3. Controlador de Autenticación

```typescript
// controllers/auth.controller.ts
import { Request, Response, NextFunction } from 'express';
import { AuthService } from '../services/auth/auth.service';
import { ResponseUtil } from '../utils/response.util';
import { AppError } from '../utils/error.util';

export class AuthController {
  private authService: AuthService;

  constructor() {
    this.authService = new AuthService();
  }

  // Inicia el flujo de autenticación OAuth con Google
  googleAuth = async (req: Request, res: Response): Promise<void> => {
    const authUrl = this.authService.getGoogleAuthUrl();
    res.redirect(authUrl);
  };

  // Maneja el callback de Google OAuth
  googleCallback = async (req: Request, res: Response, next: NextFunction): Promise<void> => {
    try {
      const { code } = req.query;
      
      if (!code || typeof code !== 'string') {
        throw new AppError('Código de autorización no válido', 400);
      }
      
      // Obtiene tokens y guarda en sesión
      const tokens = await this.authService.getTokensFromCode(code);
      req.session.tokens = tokens;
      req.session.isAuthenticated = true;
      
      // Redirecciona al dashboard después de autenticación exitosa
      res.redirect('/');
    } catch (error) {
      next(error);
    }
  };

  // Verifica el estado de autenticación actual
  getAuthStatus = (req: Request, res: Response): void => {
    const isAuthenticated = req.session.isAuthenticated || false;
    
    ResponseUtil.success(res, { isAuthenticated });
  };

  // Cierra la sesión actual
  logout = (req: Request, res: Response, next: NextFunction): void => {
    try {
      req.session.destroy((err) => {
        if (err) {
          throw new AppError('Error al cerrar sesión', 500);
        }
        
        ResponseUtil.success(res, null, 'Sesión cerrada exitosamente');
      });
    } catch (error) {
      next(error);
    }
  };
}
```

#### 3.7.4. Middleware de Autenticación

```typescript
// middlewares/auth.middleware.ts
import { Request, Response, NextFunction } from 'express';
import { ResponseUtil } from '../utils/response.util';
import { config } from '../config';

export const authMiddleware = (req: Request, res: Response, next: NextFunction): void => {
  // Si estamos en modo MOCK, permitir acceso sin autenticación
  if (config.MODE === 'MOCK') {
    return next();
  }
  
  // Verificar si el usuario está autenticado
  if (!req.session.isAuthenticated || !req.session.tokens) {
    res.status(401).json({
      data: null,
      meta: {
        success: false,
        message: 'No autenticado. Inicie sesión para acceder.',
        code: 401
      }
    });
    return;
  }
  
  // Si el token está por expirar, intentar refrescarlo
  const expiryDate = req.session.tokens.expiry_date;
  if (expiryDate && Date.now() >= expiryDate - 5 * 60 * 1000) {
    // Implementar lógica para refrescar token usando refresh_token
  }
  
  next();
};
```

#### 3.7.5. Servicio de Autenticación

```typescript
// services/auth/auth.service.ts
import { OAuth2Client } from 'google-auth-library';
import { createOAuth2Client, authConfig } from '../../config/auth.config';

export interface TokenResponse {
  access_token: string;
  refresh_token?: string;
  scope: string;
  token_type: string;
  expiry_date?: number;
}

export class AuthService {
  private oauth2Client: OAuth2Client;

  constructor() {
    this.oauth2Client = createOAuth2Client();
  }

  // Genera URL para autenticación con Google
  getGoogleAuthUrl(): string {
    return this.oauth2Client.generateAuthUrl({
      access_type: 'offline',
      scope: authConfig.SCOPES,
      prompt: 'consent'
    });
  }

  // Obtiene tokens a partir del código de autorización
  async getTokensFromCode(code: string): Promise<TokenResponse> {
    const { tokens } = await this.oauth2Client.getToken(code);
    
    return {
      access_token: tokens.access_token!,
      refresh_token: tokens.refresh_token,
      scope: tokens.scope!,
      token_type: tokens.token_type!,
      expiry_date: tokens.expiry_date
    };
  }

  // Refresca el token de acceso usando el refresh token
  async refreshAccessToken(refreshToken: string): Promise<TokenResponse> {
    this.oauth2Client.setCredentials({
      refresh_token: refreshToken
    });
    
    const { credentials } = await this.oauth2Client.refreshAccessToken();
    
    return {
      access_token: credentials.access_token!,
      refresh_token: credentials.refresh_token || refreshToken,
      scope: credentials.scope!,
      token_type: credentials.token_type!,
      expiry_date: credentials.expiry_date
    };
  }
}
```

## 4. Endpoints de API

### 4.1. Cursos

| Método | Ruta | Descripción |
|--------|------|-------------|
| GET | /api/courses | Obtener todos los cursos |
| GET | /api/courses/:id | Obtener curso por ID |
| GET | /api/courses/:id/students | Obtener estudiantes de un curso |
| GET | /api/courses/:id/assignments | Obtener tareas de un curso |

### 4.2. Estudiantes

| Método | Ruta | Descripción |
|--------|------|-------------|
| GET | /api/students | Obtener todos los estudiantes |
| GET | /api/students/:id | Obtener estudiante por ID |
| GET | /api/students/:id/courses | Obtener cursos de un estudiante |
| GET | /api/students/:id/assignments | Obtener tareas de un estudiante |

### 4.3. Tareas

| Método | Ruta | Descripción |
|--------|------|-------------|
| GET | /api/assignments | Obtener todas las tareas |
| GET | /api/assignments/:id | Obtener tarea por ID |
| GET | /api/assignments/:id/submissions | Obtener entregas de una tarea |

## 5. Dataset Mock

El modo Mock incluirá un dataset simulado con:
- 2 cursos con diferentes características
- Aproximadamente 300 estudiantes distribuidos en los cursos
- Tareas variadas con diferentes estados y fechas de entrega

```typescript
// Ejemplo parcial de mock.config.ts
export const mockCourses = [
  {
    id: 'course-001',
    name: 'Programación Básica',
    section: 'A-2023',
    description: 'Fundamentos de programación para principiantes',
    creationTime: '2023-01-15T08:00:00Z',
    updateTime: '2023-01-15T08:00:00Z',
    ownerId: 'teacher-001',
    active: true
  },
  {
    id: 'course-002',
    name: 'Desarrollo Web Avanzado',
    section: 'B-2023',
    description: 'Técnicas avanzadas de desarrollo web',
    creationTime: '2023-02-01T09:30:00Z',
    updateTime: '2023-02-01T09:30:00Z',
    ownerId: 'teacher-002',
    active: true
  }
];

// Estudiantes y tareas definidos de manera similar
```

## 5.1. Fallback con Badge "MOCK MODE"

Cuando el sistema está operando en modo Mock (`MODE=MOCK`), la interfaz de usuario debe mostrar claramente un badge o indicador visual con el texto "MOCK MODE" para que los usuarios sepan que están interactuando con datos simulados y no con datos reales de Google Classroom.

### 5.1.1. Implementación del Badge

El backend proporcionará un endpoint para verificar el modo actual:

```typescript
// controllers/system.controller.ts
import { Request, Response } from 'express';
import { config } from '../config';
import { ResponseUtil } from '../utils/response.util';

export class SystemController {
  getSystemMode(req: Request, res: Response): void {
    ResponseUtil.success(res, {
      mode: config.MODE,
      isMock: config.MODE === 'MOCK'
    });
  }
}
```

### 5.1.2. Endpoint para Verificar el Modo

```typescript
// routes/system.routes.ts
import { Router } from 'express';
import { SystemController } from '../controllers/system.controller';

const router = Router();
const controller = new SystemController();

router.get('/mode', controller.getSystemMode);

export default router;
```

### 5.1.3. Respuesta del Endpoint

La respuesta del endpoint `/api/system/mode` tendrá el siguiente formato:

```json
{
  "data": {
    "mode": "MOCK",
    "isMock": true
  },
  "meta": {
    "success": true,
    "message": "Operación exitosa",
    "code": 200
  }
}
```

La interfaz de usuario debe consultar este endpoint al iniciar y mostrar un badge prominente con el texto "MOCK MODE" cuando `isMock` sea `true`. Este badge debe ser visible en todas las páginas de la aplicación para evitar confusiones.

## 6. Plan de Pruebas

### 6.1. Tests Unitarios

- **Servicios**: Verificar que cada servicio implementa correctamente su interfaz.
  - **AuthService**: Probar generación de URL de autenticación, obtención y refresco de tokens.
  - **GoogleServices**: Verificar integración con la API de Google Classroom usando mocks.
  - **MockServices**: Comprobar que devuelven datos simulados correctamente.

- **Controladores**: Comprobar que manejan correctamente las peticiones y respuestas.
  - **AuthController**: Verificar redirecciones, manejo de callbacks y gestión de sesiones.
  - **API Controllers**: Validar que utilizan los servicios adecuados según el modo de operación.

- **Middlewares**: Validar funcionamiento correcto.
  - **authMiddleware**: Comprobar que permite acceso con sesión válida y rechaza sin sesión.
  - **errorMiddleware**: Verificar manejo uniforme de errores.

- **Utilidades**: Validar funciones auxiliares como formateo de respuestas y manejo de errores.

### 6.2. Tests de Integración

- **Flujo de autenticación**:
  - Verificar redirección a Google OAuth.
  - Simular callback con código de autorización válido e inválido.
  - Comprobar almacenamiento correcto de tokens en sesión.
  - Validar comportamiento del middleware de autenticación.

- **API con autenticación**:
  - Verificar que las rutas protegidas rechazan peticiones sin autenticación.
  - Comprobar que las rutas aceptan peticiones autenticadas.
  - Validar refresco automático de tokens expirados.

- **Servicios + Controladores**: Comprobar la integración entre capas.
  - Verificar que los controladores utilizan el servicio correcto según el modo.
  - Comprobar que los servicios de Google utilizan el cliente OAuth autenticado.

- **Modo Mock**:
  - Validar que el sistema funciona correctamente en modo Mock.
  - Comprobar que el endpoint `/api/system/mode` devuelve el estado correcto.

### 6.3. Tests End-to-End (E2E)

- **Flujo completo de autenticación**:
  - Login con Google y redirección al callback.
  - Validación de tokens y acceso a endpoints protegidos.
  - Cierre de sesión y verificación de pérdida de acceso.

- **Modo Google**:
  - Autenticación y acceso a datos reales de Google Classroom.
  - Verificación de respuestas con datos reales.
  - Manejo de errores de la API de Google.

- **Modo Mock**:
  - Verificación de badge "MOCK MODE" en la interfaz.
  - Acceso a endpoints sin autenticación.
  - Validación de respuestas con datos simulados.

- **Flujos completos de usuario** simulando peticiones reales.
  - Navegación por cursos, estudiantes y tareas.
  - Verificación de respuestas completas incluyendo formato y datos.

### 6.4. Cobertura de Tests

- Objetivo mínimo: 70% de cobertura global.
- Enfoque en componentes críticos: servicios y controladores (>80%).
- Cobertura especial en componentes de autenticación (>90%).

## 7. Criterios de Aceptación (DoD)

1. **Funcionalidad Completa**:
   - Todos los endpoints definidos funcionan correctamente en ambos modos (Google y Mock).
   - Las respuestas siguen el formato uniforme `{data, meta}`.
   - Login con Google redirige correctamente y completa el flujo OAuth.
   - Scopes mínimos definidos para Classroom (classroom.courses.readonly, classroom.coursework.me.readonly, etc.).

2. **Autenticación**:
   - Rutas `/auth/google` y `/auth/google/callback` funcionan correctamente.
   - Tokens (`access_token`, `refresh_token`) se guardan en sesión de forma segura.
   - Usuarios sin sesión activa reciben código de error `401` al intentar acceder a endpoints protegidos.
   - El middleware `authMiddleware` valida correctamente la sesión activa.

3. **Modo Mock**:
   - Dataset completo cargado y disponible para pruebas.
   - Incluye 2 cursos, ~300 estudiantes y tareas variadas.
   - Badge "MOCK MODE" visible en la interfaz cuando se opera en este modo.
   - Endpoint `/api/system/mode` devuelve correctamente el estado actual.

4. **Calidad de Código**:
   - Cobertura de tests ≥ 70%.
   - Sin errores de linting.
   - Documentación de código completa (JSDoc).

5. **Manejo de Errores**:
   - Sistema de manejo de errores uniforme implementado.
   - Todos los casos de error devuelven respuestas con formato correcto.
   - Errores de autenticación manejados adecuadamente.

6. **Configuración**:
   - Sistema configurable mediante variables de entorno.
   - Cambio entre modos (GOOGLE/MOCK) sin modificar código.
   - Variables OAuth configuradas correctamente en `.env`.

7. **Rendimiento**:
   - Tiempo de respuesta < 200ms para endpoints en modo Mock.
   - Tiempo de respuesta < 1s para endpoints en modo Google.

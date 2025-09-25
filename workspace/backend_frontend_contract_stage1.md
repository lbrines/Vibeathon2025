# Contrato MVP - Semillero Digital Dashboard (Hackaton)

## 1. Información General

**Proyecto:** Semillero Digital Dashboard  
**Versión:** MVP para Hackaton  
**Fecha:** Septiembre 2025  

## 2. Objetivo del MVP

Implementar un dashboard funcional para el seguimiento académico con dos modos de operación:

- **Modo MOCK**: Utiliza un dataset local con 2 cursos, aproximadamente 300 alumnos y tareas variadas, mostrando un badge "MOCK MODE" en la UI.
- **Modo GOOGLE**: Integración real con Google Classroom API mediante OAuth2, consumiendo datos en tiempo real.

## 3. Estructura de Directorios

```
/
├── frontend/                     # Aplicación frontend (Next.js + TypeScript)
│   ├── src/
│   │   ├── app/                  # Estructura de rutas Next.js
│   │   │   ├── page.tsx          # Dashboard principal (Home)
│   │   │   ├── courses/          # Rutas para cursos
│   │   │   ├── students/         # Rutas para estudiantes
│   │   │   └── assignments/      # Rutas para tareas
│   │   ├── components/           # Componentes reutilizables
│   │   │   ├── ui/               # Componentes de UI básicos
│   │   │   │   ├── MockModeBadge.tsx  # Badge para modo simulación
│   │   │   │   └── FilterPanel.tsx    # Panel de filtros global
│   │   │   ├── layout/           # Componentes de estructura
│   │   │   └── dashboard/        # Componentes específicos del dashboard
│   │   │       ├── StudentProgressCard.tsx  # Tarjeta de alumnos y progreso
│   │   │       ├── TeacherCoursesTable.tsx  # Tabla de profesores y cursos
│   │   │       ├── SubmissionStatusCard.tsx # Estado de entregas
│   │   │       └── RiskMetricsPanel.tsx     # Panel de métricas de riesgo
│   │   ├── hooks/                # Hooks personalizados
│   │   │   ├── useAuth.ts        # Hook para autenticación
│   │   │   └── useFilters.ts     # Hook para filtros globales
│   │   ├── services/             # Servicios para comunicación con API
│   │   │   ├── api.ts            # Cliente API base
│   │   │   ├── courses.ts        # Servicio para cursos
│   │   │   ├── students.ts       # Servicio para estudiantes
│   │   │   ├── assignments.ts    # Servicio para tareas
│   │   │   └── metrics.ts        # Servicio para métricas y KPIs
│   │   ├── types/                # Definiciones de tipos TypeScript
│   │   └── utils/                # Utilidades y helpers
│   ├── public/                   # Archivos estáticos
│   ├── .eslintrc.json           # Configuración de linting
│   ├── .gitignore               # Archivos ignorados por git
│   ├── next.config.js           # Configuración de Next.js
│   ├── package.json             # Dependencias y scripts
│   ├── tsconfig.json            # Configuración de TypeScript
│   └── README.md                # Documentación del frontend
│
├── backend/                     # Aplicación backend (Node.js + TypeScript)
│   ├── src/
│   │   ├── config/              # Configuración de la aplicación
│   │   │   ├── index.ts         # Exporta configuración unificada
│   │   │   ├── app.config.ts    # Configuración general
│   │   │   ├── google.config.ts # Configuración para Google API
│   │   │   ├── auth.config.ts   # Configuración de autenticación
│   │   │   └── mock.config.ts   # Configuración para modo Mock
│   │   ├── api/                 # Capa de API REST
│   │   │   ├── routes/          # Definición de rutas
│   │   │   │   ├── index.ts     # Exporta todas las rutas
│   │   │   │   ├── auth.routes.ts # Rutas de autenticación
│   │   │   │   ├── courses.routes.ts
│   │   │   │   ├── students.routes.ts
│   │   │   │   ├── assignments.routes.ts
│   │   │   │   └── metrics.routes.ts # Rutas para métricas y KPIs
│   │   │   ├── controllers/     # Controladores de API
│   │   │   │   ├── auth.controller.ts
│   │   │   │   ├── courses.controller.ts
│   │   │   │   ├── students.controller.ts
│   │   │   │   ├── assignments.controller.ts
│   │   │   │   └── metrics.controller.ts
│   │   │   └── middlewares/     # Middlewares de API
│   │   │       ├── auth.middleware.ts # Middleware de autenticación
│   │   │       ├── error.middleware.ts
│   │   │       └── validation.middleware.ts
│   │   ├── services/            # Capa de servicios
│   │   │   ├── interfaces/      # Interfaces para servicios
│   │   │   │   ├── course.interface.ts
│   │   │   │   ├── student.interface.ts
│   │   │   │   ├── assignment.interface.ts
│   │   │   │   └── metrics.interface.ts
│   │   │   ├── factory/         # Factory para seleccionar servicios
│   │   │   │   └── service.factory.ts
│   │   │   ├── google/          # Servicios para Google Classroom
│   │   │   │   ├── classroom.client.ts # Cliente OAuth2 para Google
│   │   │   │   ├── courses.service.ts
│   │   │   │   ├── students.service.ts
│   │   │   │   ├── assignments.service.ts
│   │   │   │   └── metrics.service.ts
│   │   │   └── mock/           # Servicios para modo Mock
│   │   │       ├── mock.service.ts
│   │   │       ├── courses.service.ts
│   │   │       ├── students.service.ts
│   │   │       ├── assignments.service.ts
│   │   │       └── metrics.service.ts
│   │   ├── models/              # Modelos de datos
│   │   │   ├── course.model.ts
│   │   │   ├── student.model.ts
│   │   │   ├── assignment.model.ts
│   │   │   ├── submission.model.ts
│   │   │   ├── metrics.model.ts
│   │   │   └── user.model.ts    # Modelo para usuarios/sesiones
│   │   ├── utils/               # Utilidades
│   │   │   ├── response.util.ts # Formato uniforme de respuesta
│   │   │   ├── error.util.ts    # Clases de error personalizadas
│   │   │   ├── kpi.util.ts      # Cálculo de KPIs
│   │   │   └── risk.util.ts     # Cálculo de riesgo académico
│   │   └── app.ts               # Punto de entrada de la aplicación
│   ├── .env.example             # Ejemplo de variables de entorno
│   ├── .gitignore              # Archivos ignorados por git
│   ├── package.json            # Dependencias y scripts
│   ├── tsconfig.json           # Configuración de TypeScript
│   └── jest.config.js          # Configuración de tests
│
├── mocks/                      # Dataset para modo Mock
│   ├── courses.json            # Datos de cursos
│   ├── students.json           # Datos de estudiantes
│   └── assignments.json        # Datos de tareas
│
├── tests/                      # Tests del proyecto
│   ├── backend/                # Tests para el backend
│   │   ├── unit/               # Tests unitarios
│   │   │   ├── services/       # Tests de servicios
│   │   │   └── controllers/    # Tests de controladores
│   │   └── integration/        # Tests de integración
│   └── frontend/               # Tests para el frontend
│       ├── unit/               # Tests unitarios de componentes
│       └── e2e/                # Tests end-to-end
│
└── README.md                   # Documentación general del proyecto
```

## 4. Especificación del Backend

### 4.1. Configuración de Modos

El backend debe soportar dos modos de operación, configurables mediante variable de entorno:

```
MODE=MOCK | GOOGLE
```

- **Modo MOCK**: Utiliza servicios que cargan datos desde archivos JSON locales y muestra un badge "MOCK MODE" en la UI.
- **Modo GOOGLE**: Utiliza servicios que se conectan a la API de Google Classroom mediante OAuth2.

La configuración se realizará a través de un archivo `.env`:

```
# Puerto del servidor
PORT=8000

# Modo de operación: GOOGLE (API real) o MOCK (datos simulados)
MODE=MOCK

# Variables para integración con Google
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=
GOOGLE_REDIRECT_URI=http://localhost:8000/auth/google/callback
SESSION_SECRET=superseguro
FRONTEND_URL=http://localhost:3000
```

### 4.2. Autenticación OAuth2

El backend implementará un flujo completo de autenticación OAuth2 con Google:

#### 4.2.1. Rutas de autenticación

| Método | Ruta | Descripción |
|--------|------|-------------|
| GET | /auth/google | Inicia el flujo de autenticación redirigiendo al consentimiento de Google |
| GET | /auth/google/callback | Maneja la respuesta de Google, intercambia el código por tokens y crea la sesión |
| GET | /auth/status | Devuelve el estado de autenticación del usuario actual |
| POST | /auth/logout | Cierra la sesión del usuario y revoca los tokens |

#### 4.2.2. Middleware de autenticación

```typescript
// auth.middleware.ts
export const authRequired = async (req: Request, res: Response, next: NextFunction) => {
  if (!req.session || !req.session.user) {
    return res.status(401).json({
      data: null,
      meta: {
        success: false,
        message: "Autenticación requerida",
        code: 401
      }
    });
  }
  
  // Inyectar cliente de Classroom con el token del usuario
  req.classroomClient = createClassroomClient(req.session.tokens);
  
  next();
};
```

#### 4.2.3. Scopes requeridos

Los siguientes scopes son necesarios para el funcionamiento del MVP:

- `classroom.courses.readonly`
- `classroom.rosters.readonly`
- `classroom.coursework.students.readonly`
- `classroom.student-submissions.students.readonly`

### 4.3. Servicios Definidos

#### 4.3.1. Factory de Servicios

```typescript
// service.factory.ts
import { config } from '../config';
import { CourseService } from './interfaces/course.interface';
import { StudentService } from './interfaces/student.interface';
import { AssignmentService } from './interfaces/assignment.interface';
import { MetricsService } from './interfaces/metrics.interface';

import { GoogleCourseService } from './google/courses.service';
import { GoogleStudentService } from './google/students.service';
import { GoogleAssignmentService } from './google/assignments.service';
import { GoogleMetricsService } from './google/metrics.service';

import { MockCourseService } from './mock/courses.service';
import { MockStudentService } from './mock/students.service';
import { MockAssignmentService } from './mock/assignments.service';
import { MockMetricsService } from './mock/metrics.service';

export class ServiceFactory {
  // Para servicios Google, se requiere el token de autenticación
  static getCourseService(tokens?: any): CourseService {
    if (config.MODE === 'GOOGLE' && tokens) {
      return new GoogleCourseService(tokens);
    }
    return new MockCourseService();
  }
  
  static getStudentService(tokens?: any): StudentService {
    if (config.MODE === 'GOOGLE' && tokens) {
      return new GoogleStudentService(tokens);
    }
    return new MockStudentService();
  }
  
  static getAssignmentService(tokens?: any): AssignmentService {
    if (config.MODE === 'GOOGLE' && tokens) {
      return new GoogleAssignmentService(tokens);
    }
    return new MockAssignmentService();
  }
  
  static getMetricsService(tokens?: any): MetricsService {
    if (config.MODE === 'GOOGLE' && tokens) {
      return new GoogleMetricsService(tokens);
    }
    return new MockMetricsService();
  }
}
```

#### 4.2.2. MockService

Implementación que carga y sirve datos desde los archivos JSON en `/mocks/`:

```typescript
// mock/mock.service.ts
import fs from 'fs';
import path from 'path';

export class MockService {
  protected loadMockData<T>(filename: string): T[] {
    const filePath = path.join(__dirname, '../../../mocks', filename);
    const data = fs.readFileSync(filePath, 'utf-8');
    return JSON.parse(data);
  }
}

// mock/courses.service.ts
import { CourseService } from '../interfaces/course.interface';
import { Course } from '../../models/course.model';
import { MockService } from './mock.service';

export class MockCourseService extends MockService implements CourseService {
  async getCourses(): Promise<Course[]> {
    return this.loadMockData<Course>('courses.json');
  }
  
  // Implementación de otros métodos
}
```

#### 4.2.3. GoogleService (stub)

Implementación placeholder para futura integración con Google Classroom API:

```typescript
// google/google.service.ts
export class GoogleService {
  // Base para futura implementación
}

// google/courses.service.ts
import { CourseService } from '../interfaces/course.interface';
import { Course } from '../../models/course.model';
import { GoogleService } from './google.service';

export class GoogleCourseService extends GoogleService implements CourseService {
  async getCourses(): Promise<Course[]> {
    // Placeholder - En esta etapa solo devuelve un array vacío
    // En futuras etapas, implementará la conexión real con Google Classroom API
    return [];
  }
  
  // Stubs para otros métodos
}
```

### 4.4. Endpoints REST

El backend expondrá los siguientes endpoints REST con formato de respuesta unificado `{ data, meta: { success, message, code } }`:

#### 4.4.1. Entidades base

**Cursos**

| Método | Ruta | Descripción | Parámetros |
|--------|------|-------------|------------|
| GET | /api/courses | Obtener todos los cursos | `cohort`, `teacherId` |
| GET | /api/courses/:id | Obtener curso por ID | - |

**Estudiantes**

| Método | Ruta | Descripción | Parámetros |
|--------|------|-------------|------------|
| GET | /api/students | Obtener todos los estudiantes | `cohort`, `risk`, `teacherId` |
| GET | /api/students/:id | Obtener estudiante por ID | - |

**Tareas**

| Método | Ruta | Descripción | Parámetros |
|--------|------|-------------|------------|
| GET | /api/assignments | Obtener todas las tareas | `courseId`, `status`, `dueFrom`, `dueTo` |
| GET | /api/assignments/:id | Obtener tarea por ID | - |
| GET | /api/assignments/:id/submissions | Obtener entregas de una tarea | `status` (entregado\|atrasado\|faltante\|reentrega) |

#### 4.4.2. KPIs y métricas

**Overview global**

| Método | Ruta | Descripción | Parámetros |
|--------|------|-------------|------------|
| GET | /api/metrics/overview | Obtener métricas generales | `cohort`, `teacherId`, `status` |

Ejemplo de respuesta:
```json
{
  "data": {
    "totalStudents": 300,
    "totalCourses": 2,
    "assignmentsByStatus": {
      "entregado": 450,
      "atrasado": 120,
      "faltante": 80,
      "reentrega": 30
    },
    "onTimeRate": 0.75,
    "lateRate": 0.20
  },
  "meta": {
    "success": true,
    "message": "Métricas obtenidas correctamente",
    "code": 200
  }
}
```

**Métricas de riesgo**

| Método | Ruta | Descripción | Parámetros |
|--------|------|-------------|------------|
| GET | /api/metrics/risk | Obtener estudiantes en riesgo | `cohort`, `threshold` |

Ejemplo de respuesta:
```json
{
  "data": {
    "highRiskStudents": [
      {
        "id": "student-001",
        "name": "Alumno 1",
        "riskScore": 85,
        "attendancePct": 65,
        "lateAssignments": 4,
        "missingAssignments": 2
      }
    ],
    "riskDistribution": {
      "high": 45,
      "medium": 120,
      "low": 135
    }
  },
  "meta": {
    "success": true,
    "message": "Métricas de riesgo obtenidas correctamente",
    "code": 200
  }
}
```

### 4.5. Cálculo de riesgo académico

El sistema implementará una heurística para calcular el riesgo académico de los estudiantes:

```typescript
// risk.util.ts
export function calculateRiskScore(student: Student): number {
  // Ponderación: 40% asistencia, 40% entregas atrasadas/faltantes, 20% calificaciones
  const attendanceScore = calculateAttendanceScore(student.attendancePct);
  const submissionScore = calculateSubmissionScore(student.lateCount, student.missingCount);
  const gradeScore = student.avgGrade ? calculateGradeScore(student.avgGrade) : 50;
  
  // Valor entre 0-100, donde mayor valor = mayor riesgo
  return (attendanceScore * 0.4) + (submissionScore * 0.4) + (gradeScore * 0.2);
}

// Criterios de clasificación
export function getRiskLevel(score: number): 'high' | 'medium' | 'low' {
  if (score >= 70) return 'high';
  if (score >= 40) return 'medium';
  return 'low';
}
```

### 4.4. Formato de Respuesta

Todas las respuestas seguirán un formato uniforme:

```json
{
  "data": {},  // Datos de la respuesta o null en caso de error
  "meta": {
    "success": true,  // true o false según el resultado
    "message": "Operación exitosa",  // Mensaje descriptivo
    "code": 200  // Código HTTP
  }
}
```

## 5. Especificación del Frontend

### 5.1. Tecnologías

- **Framework**: Next.js 15 (App Router)
- **Lenguaje**: TypeScript
- **Estilos**: Bootstrap 5 (si es requerido por el hackaton) o Tailwind CSS
- **Estado**: React Context API / React Query

### 5.2. Dashboard Principal

El dashboard principal (`/app/page.tsx`) incluirá los siguientes elementos:

1. **Badge "MOCK MODE"**: Visible solo cuando `MODE=MOCK` en el backend
2. **Filtros globales persistentes**:
   - Cohorte (selector)
   - Profesor (selector)
   - Estado de entrega (chips seleccionables)
3. **Tarjetas de KPIs**:
   - **Alumnos + progreso**: Muestra el total de alumnos y una barra de progreso con el porcentaje de entregas a tiempo
   - **Profesores + clases**: Tabla compacta con profesor, número de cursos y número de alumnos
   - **Estado de entregas**: Conteo por estado (entregado, atrasado, faltante, reentrega) con gráfico simple
4. **Tabla de estudiantes en riesgo**: Muestra los estudiantes con mayor riesgo académico

### 5.3. Componentes Principales

```typescript
// components/ui/MockModeBadge.tsx
export const MockModeBadge = () => {
  const { isMockMode } = useMockMode();
  
  if (!isMockMode) return null;
  
  return (
    <div className="badge bg-warning text-dark position-fixed top-0 end-0 m-2">
      MOCK MODE
    </div>
  );
};

// components/dashboard/StudentProgressCard.tsx
export const StudentProgressCard = () => {
  const { data, isLoading } = useQuery(['metrics', 'overview'], getMetricsOverview);
  
  if (isLoading) return <SkeletonLoader />;
  
  return (
    <Card>
      <CardHeader>Progreso de Alumnos</CardHeader>
      <CardBody>
        <div className="d-flex justify-content-between mb-2">
          <span>Total Alumnos: {data.totalStudents}</span>
          <span>{data.onTimeRate * 100}% a tiempo</span>
        </div>
        <ProgressBar 
          value={data.onTimeRate * 100} 
          variant={data.onTimeRate > 0.8 ? "success" : data.onTimeRate > 0.6 ? "warning" : "danger"}
        />
      </CardBody>
    </Card>
  );
};
```

### 5.4. Servicios API

El frontend se comunicará con el backend a través de servicios API:

```typescript
// services/api.ts
const API_BASE_URL = process.env.NEXT_PUBLIC_API_URL || 'http://localhost:8000/api';

export async function fetchApi<T>(endpoint: string, params?: Record<string, any>): Promise<T> {
  // Construir query string desde parámetros
  const queryString = params ? 
    '?' + Object.entries(params)
      .filter(([_, value]) => value !== undefined && value !== null)
      .map(([key, value]) => `${key}=${encodeURIComponent(value)}`)
      .join('&') : '';
  
  const response = await fetch(`${API_BASE_URL}${endpoint}${queryString}`);
  const result = await response.json();
  
  if (!result.meta.success) {
    throw new Error(result.meta.message);
  }
  
  return result.data;
}

// services/metrics.ts
import { fetchApi } from './api';
import { MetricsOverview, RiskMetrics } from '../types';

export async function getMetricsOverview(params?: { 
  cohort?: string; 
  teacherId?: string;
  status?: string;
}): Promise<MetricsOverview> {
  return fetchApi<MetricsOverview>('/metrics/overview', params);
}

export async function getRiskMetrics(params?: {
  cohort?: string;
  threshold?: number;
}): Promise<RiskMetrics> {
  return fetchApi<RiskMetrics>('/metrics/risk', params);
}
```

### 5.5. Hooks para estado global

```typescript
// hooks/useFilters.ts
import { createContext, useContext, useState, useEffect } from 'react';

type Filters = {
  cohort: string | null;
  teacherId: string | null;
  status: string | null;
};

const FiltersContext = createContext<{
  filters: Filters;
  setFilters: (filters: Partial<Filters>) => void;
}>({ filters: { cohort: null, teacherId: null, status: null }, setFilters: () => {} });

export const FiltersProvider = ({ children }) => {
  // Cargar filtros desde localStorage para persistencia
  const [filters, setFiltersState] = useState<Filters>(() => {
    if (typeof window === 'undefined') return { cohort: null, teacherId: null, status: null };
    
    const savedFilters = localStorage.getItem('dashboard-filters');
    return savedFilters ? JSON.parse(savedFilters) : { cohort: null, teacherId: null, status: null };
  });
  
  // Actualizar filtros y guardar en localStorage
  const setFilters = (newFilters: Partial<Filters>) => {
    const updated = { ...filters, ...newFilters };
    setFiltersState(updated);
    localStorage.setItem('dashboard-filters', JSON.stringify(updated));
  };
  
  return (
    <FiltersContext.Provider value={{ filters, setFilters }}>
      {children}
    </FiltersContext.Provider>
  );
};

export const useFilters = () => useContext(FiltersContext);
```

### 5.6. Responsive Design

El frontend será completamente responsive, adaptado a los siguientes breakpoints:

- **Móvil**: 360×640px - Diseño de tarjetas apiladas, menú colapsable
- **Tablet**: 768×1024px - Diseño de 2 columnas, tablas simplificadas
- **Desktop**: 1440×900px - Diseño completo con todas las columnas

Se implementarán las siguientes estrategias para responsividad:

- Uso de media queries o clases responsive de Bootstrap
- Tablas que se convierten en tarjetas en vista móvil
- Menús colapsables para navegación en dispositivos pequeños
- Gráficos que se adaptan al tamaño de pantalla
```

## 6. Dataset Mock

El dataset mock incluirá:

- **Cursos**: 2 cursos con diferentes características
- **Estudiantes**: Aproximadamente 300 estudiantes distribuidos entre los cursos
- **Tareas**: Variedad de tareas con diferentes estados y fechas de entrega

Ejemplo de estructura de datos:

```json
// mocks/courses.json (extracto)
[
  {
    "id": "course-001",
    "name": "Programación Básica",
    "section": "A-2023",
    "description": "Fundamentos de programación para principiantes",
    "creationTime": "2023-01-15T08:00:00Z",
    "updateTime": "2023-01-15T08:00:00Z",
    "ownerId": "teacher-001",
    "active": true
  },
  {
    "id": "course-002",
    "name": "Desarrollo Web Avanzado",
    "section": "B-2023",
    "description": "Técnicas avanzadas de desarrollo web",
    "creationTime": "2023-02-01T09:30:00Z",
    "updateTime": "2023-02-01T09:30:00Z",
    "ownerId": "teacher-002",
    "active": true
  }
]
```

## 7. TEST PLAN (TDD obligatorio)

### 7.1. Tests Unitarios

Se implementarán tests unitarios para:

- **Mapeos y utilidades**:
  - Normalización de fechas y cálculo de estados
  - Cálculo de KPIs (onTimeRate, lateRate)
  - Cálculo de riskScore y clasificación de riesgo
  - Validación de parámetros y filtros

- **Servicios**:
  - Servicios Mock (verificación de carga de datos)
  - ServiceFactory (selección correcta de implementación según MODE)

### 7.2. Tests de Integración

- **Autenticación**:
  - Verificar flujo de callback OAuth2 completo
  - Comprobar almacenamiento correcto de tokens en sesión
  - Verificar que rutas protegidas responden 401 sin sesión

- **Endpoints con filtros**:
  - Verificar que los filtros funcionan correctamente en modo MOCK
  - Verificar que los filtros funcionan correctamente en modo GOOGLE (con stubs)
  - Comprobar formato de respuesta uniforme en todos los endpoints

### 7.3. Tests E2E

- **Flujo principal**:
  - Login → Dashboard → Aplicar filtros → Verificar que los KPIs cambian
  - Navegación entre vistas (dashboard, cursos, estudiantes)

- **Modo MOCK**:
  - Verificar que el badge "MOCK MODE" se muestra correctamente
  - Comprobar que las respuestas llegan en <200ms

### 7.4. Cobertura

- **Global**: ≥70% de cobertura en todo el código
- **Services y Metrics/Risk**: ≥85% de cobertura
- **A11y smoke tests**: Verificación básica de accesibilidad (foco visible, labels, contrastes)

### 7.5. CI Gates

- **Typecheck**: Verificación estática de tipos TypeScript
- **Lint**: Verificación de reglas de linting
- **Tests**: Ejecución de tests unitarios, integración y E2E
- **Cobertura**: Verificación de umbrales de cobertura

## 8. Rendimiento

### 8.1. Criterios de rendimiento

- **Modo MOCK**:
  - P95 < 200 ms para endpoints de lista y métricas
  - Carga inicial del dashboard < 1s

- **Modo GOOGLE**:
  - P95 < 1s para endpoints de lista y métricas
  - Cache oportunista en memoria (60s) para `/metrics/*`

### 8.2. Estrategias de optimización

- **Backend**:
  - Caché en memoria para respuestas frecuentes
  - Procesamiento paralelo de datos cuando sea posible

- **Frontend**:
  - Lazy loading de componentes secundarios
  - Skeleton loaders durante la carga
  - Optimización de imágenes y assets

## 9. Entregables para Devpost (Ready-to-Ship)

### 9.1. README completo

El README incluirá:

- **Pre-requisitos**: Node.js, npm/yarn, etc.
- **Configuración**: Instrucciones para configurar `.env`
- **Instalación**: `npm install` o equivalente
- **Ejecución**: `docker compose up` o comandos equivalentes
- **URLs**: Endpoints y rutas principales
- **Usuario mock**: Credenciales para modo MOCK
- **Troubleshooting**: Soluciones a problemas comunes
- **Modos**: Instrucción clara sobre cómo alternar entre modo GOOGLE y MOCK

### 9.2. Video de demostración (1-2 minutos)

Script sugerido para el video:

1. **Login OAuth** (o badge MOCK): Mostrar el proceso de autenticación o el badge de MOCK MODE
2. **Filtros**: Demostrar el uso de filtros por cohorte, profesor y estado
3. **Dashboard**: Presentar los KPIs principales y su significado
4. **Navegación**: Mostrar la navegación rápida entre cursos, estudiantes y tareas

### 9.3. Estructura del repositorio

- **Licencia**: MIT o similar
- **Estructura clara**: Separación backend/frontend
- **Comandos NPM**:
  - `dev`: Iniciar en modo desarrollo
  - `test`: Ejecutar tests
  - `build`: Compilar para producción
  - `start`: Iniciar en modo producción

## 10. Definition of Done (DoD)

El proyecto se considerará completado cuando cumpla con TODOS los siguientes criterios:

- [x] Login Google operativo con scopes mínimos y rutas `/auth/*`
- [x] Endpoints con filtros (`cohort`, `teacherId`, `status`, fechas)
- [x] `/api/metrics/overview` y `/api/metrics/risk` funcionales (MOCK + GOOGLE)
- [x] Frontend con Dashboard + filtros + badge MOCK
- [x] README reproducible + guión de video
- [x] Tests (unit/int/e2e) en verde con coberturas acordadas
- [x] Sin errores de linting; tipo estricto
- [x] Performance cumplida (P95 objetivo por modo)

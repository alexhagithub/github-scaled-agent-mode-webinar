# Plan de pruebas automatizadas (BDD Gherkin, Playwright E2E, Unit Tests)

## Resumen

Estrategia para implementar pruebas automatizadas en el monorepo (API Express TypeScript + Frontend React). Incluye:
- BDD con Gherkin (feature files + step definitions).
- E2E con Playwright (integrado con BDD o enlazado desde steps).
- Aumento de cobertura unitaria en API y Frontend.
- Integración en CI con gates de cobertura y reportes.

---

## Checklist de requisitos

- [ ] BDD con Gherkin (feature files + step definitions) que cubra flujos clave (login, CRUD productos, checkout).
- [ ] E2E con Playwright integrado con Gherkin o enlazado (tests contra la app corriendo).
- [ ] Unit tests ampliados en API (rutas, modelos, servicios).
- [ ] Unit tests ampliados en Frontend (componentes, hooks, contexts).
- [ ] Integración en CI (GitHub Actions) que ejecute unit + e2e y aplique thresholds de cobertura.
- [ ] Uso de datos coherentes en tests (seedData, fixtures, MSW para frontend, test DB para API).
- [ ] Reportes de cobertura y artefactos (traces, screenshots) en caso de fallos.

---

## Plan y fases (resumen)

### Fase 0 — Discovery rápida (1 día)
- Inventariar tests existentes y endpoints/flows críticos.
- Revisar `api/seedData.ts` y tests actuales.

### Fase 1 — Infra y scripts base (1 día)
- Añadir dev-deps: `vitest`, `@testing-library/react`, `msw`, `supertest`, `playwright`, `cucumber-js` o plugin Gherkin para Playwright.
- Scripts npm: `test:unit:api`, `test:unit:frontend`, `test:e2e`, `test:all`.
- Carpetas: `tests/features/`, `tests/steps/`, `playwright/`.

### Fase 2 — BDD con Gherkin (2–3 días)
- Crear `.feature` para flujos clave (login, admin productos, checkout).
- Implementar step definitions que reutilicen helpers; usar Playwright dentro de steps.

### Fase 3 — E2E con Playwright (3–5 días)
- Configurar `playwright.config.ts` y tests en `playwright/tests/`.
- Flujos: login, listar/crear/editar/borrar producto, crear pedido.
- Levantar API con `docker-compose` o modo dev; usar `api/seedData.ts`.
- Configurar retries, capturas y artifacts.

### Fase 4 — Unit tests API (2–4 días)
- Priorizar tests de rutas/controllers (`supertest` + `vitest`).
- Tests unitarios para modelos y utilitarios.
- Tests de integración con DB en memoria o container DB.

### Fase 5 — Unit tests Frontend (2–4 días)
- `vitest` + Testing Library para componentes, hooks y contexts.
- `msw` para mockear API en tests unitarios.
- Tests para `AuthContext`, `useTheme`, `Products` components.

### Fase 6 — CI / Quality Gates (1–2 días)
- GitHub Actions: jobs paralelos para unit tests y job separado para E2E (levantar servicios con `docker-compose`).
- Cobertura como gate; subir a Codecov/coveralls opcional.

### Fase 7 — Observabilidad y mantenimiento (ongoing)
- Habilitar retries, traces, logs, screenshots.
- Mantener fixtures y `seedData`.

---

## Artefactos propuestos

- `tests/features/*.feature` (ejemplos).
- `tests/steps/*.ts` (step definitions usando Playwright).
- `playwright.config.ts` y un test E2E de ejemplo.
- Scripts npm en `package.json` raíz o por workspace.
- `ci/workflows/ci.yml` inicial para GitHub Actions.

---

## Comandos útiles (local)

```bash
npm install
npm install -D vitest @testing-library/react @testing-library/jest-dom @testing-library/user-event msw supertest
npm install -D playwright @cucumber/cucumber
npx playwright install --with-deps
npm --workspace=api run test
npm --workspace=frontend run test
npx playwright test
```

---

## Contrato mínimo

- Inputs: repo, acceso a `docker-compose` para servicios, `api/seedData.ts`.
- Outputs: scaffold de BDD+E2E, unit tests ampliados (API + frontend), workflow CI.
- Error modes: flakes en E2E, problemas de dependencias en runner—mitigación con retries y uso de imágenes oficiales Playwright en CI.

---

## Métricas sugeridas

- Iteración 1 (2 semanas): cobertura unidad API 80%, frontend 70%, E2E básicos + 5 features Gherkin.
- Iteración 2 (4 semanas): cobertura API 90% en módulos críticos, frontend 85%, BDD cubre flujos clave.

---

## Siguientes pasos

- Opción A (implemento scaffold ahora): crear PR con configs (Playwright, feature example, step defs, scripts y workflow CI) y ejecutar pruebas básicas.
- Opción B (solo plan): usar este documento como guía y ejecutar la implementación manualmente.

---

*Este archivo fue creado automáticamente porque los Issues están deshabilitados en el repositorio; abrir un PR facilita la revisión y discusión.*

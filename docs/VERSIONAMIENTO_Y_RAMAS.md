# Versionamiento y flujo de ramas — ConsultaMed

> **Índice:** reglas obligatorias · equipo · flujo por desarrollador · rol Dev1 · diagrama de flujo · comandos útiles.

Repositorio remoto: **https://github.com/davidluna301/Gestion_Hospital**

## Reglas obligatorias

1. **Nunca hacer commit directamente en `main`.** La rama `main` se reserva para releases o integración final acordada con el propietario del repositorio.
2. La rama de integración continua del equipo es **`develop`**.
3. Cada integrante trabaja en una **rama de característica** (`feature/...`) creada **a partir de `develop` actualizado**.
4. Tras fusionar en `develop`, **no se elimina** la rama de característica (queda como evidencia del trabajo y del flujo).

## Equipo y contacto

| Rol en el equipo | Usuario GitHub      | Correo institucional              |
|------------------|---------------------|-----------------------------------|
| Dev1 (repo)      | davidluna301        | david.lunamar@campusucc.edu.co    |
| Dev2             | Vanessaucc          | julieth.mena@campusucc.edu.co     |
| Dev3             | valeriaucc          | valeria.gongora@campusucc.edu.co  |
| Dev4             | BurbanoValentina    | valentina.burbanos@campusucc.edu.co |

## Flujo por cada desarrollador

### Antes de empezar (Dev2, Dev3, Dev4 — y Dev1 en tareas nuevas)

```bash
git checkout develop
git pull origin develop
```

### Crear rama de trabajo

Convención sugerida: `feature/dev<numero>-<tema-corto>` en minúsculas y sin espacios.

Ejemplos:

- `feature/dev1-documentacion`
- `feature/dev2-plantillas`
- `feature/dev3-logica-citas`
- `feature/dev4-admin-y-despliegue`

```bash
git checkout -b feature/dev2-plantillas
```

### Commits (buenas prácticas)

- Mensajes en **español**, en imperativo o descriptivo claro.
- Prefijos útiles: `docs:`, `feat:`, `fix:`, `style:`, `refactor:`, `chore:`.
- Commits **pequeños y frecuentes** mejor que un solo commit gigante.
- Repartir el esfuerzo: cada dev debe aportar **varios commits** por iteración para equidad y trazabilidad.

Ejemplos:

```text
docs: ampliar sección de despliegue en README
feat(consultas): validar solapamiento de citas en formulario
fix(templates): corregir enlace del menú en móvil
```

### Subir la rama y abrir integración a develop

```bash
git push -u origin feature/dev2-plantillas
```

En GitHub: **Pull Request** con base **`develop`** (no `main`). Revisión del equipo y merge.

### Después del merge

- **No borrar** `feature/dev2-plantillas` en el remoto (acuerdo del equipo para evidencia académica).
- Opcional: `git checkout develop && git pull origin develop` para seguir en línea con el equipo.

## Rol de Dev1 (propietario del repo)

- Mantener **`README.md`** coherente (objetivo, instalación, diagramas, enlaces).
- Añadir remoto si el clon es nuevo:

```bash
git remote add origin https://github.com/davidluna301/Gestion_Hospital.git
```

- Primera vez: publicar `develop` y las ramas `feature/*` según lo acordado:

```bash
git push -u origin develop
git push -u origin feature/dev1-documentacion
```

## Resumen visual del flujo

```text
main          (sin commits directos del equipo en día a día)
  ^
  |   (solo merges acordados / release)
  |
develop  <---- pull antes de cada nueva rama
  ^
  |   merge PR
  |
feature/devX-tarea
```

## Comprobar estado

```bash
git status
git log develop --oneline -15
git branch -a
```

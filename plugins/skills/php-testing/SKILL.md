---
name: php-testing
description: Convenciones y reglas de testing PHP usando PHPUnit, Codeception y Mockery.
allowed-tools:
  - Read
  - Write
  - MultiEdit
  - Bash
  - Grep
  - Glob
---

# PHP Testing Standards

Eres un desarrollador senior especializado en testing PHP con Codeception y PHPUnit.

Debes seguir estrictamente las siguientes convenciones del proyecto.

---

## Testing Stack

- Codeception
- PHPUnit
- Mockery

---

## Estructura de tests

### Unit Tests

- Mantener espejo de `src/` dentro de `tests/unit`
- Todos los unit tests deben implementarse como `Cest`

Ejemplo:

```txt
src/User/Application/CreateUser/CreateUser.php
↓
tests/unit/User/Application/CreateUser/CreateUserCest.php
````

### API Tests

* Ubicar tests API en `tests/api`

---

## Convenciones

### Naming de tests

Todos los métodos deben usar el formato:

```php
siXxxEntoncesYyy
```

Ejemplo:

```php
public function siElUsuarioEsValidoEntoncesSeCreaCorrectamente(): void
```

---

## Reglas de mocks

### Permitido

* Mockear únicamente infraestructura
* Usar Mockery

### Prohibido

* Nunca mockear Application Services

---

## Datos de prueba

Usar patrón ObjectMother.

Ubicación:

```txt
tests/_data/*Data
```

Ejemplo:

```php
$request = CreateUserRequestData::valid();
```

---

## Helpers

Reutilizar helpers compartidos en:

```txt
tests/_support
```

No crear nuevos helpers si ya existe uno reutilizable.

---

## Assertions

`UnitTester` debe contener asserts reutilizables.

---

## Ejecución de tests

### Unit

```bash
make unit
```

### API

```bash
make api
```

---

## Reglas de generación de código

Cuando generes tests:

1. Respetar exactamente la estructura de carpetas.
2. Mantener tests pequeños y enfocados.
3. Priorizar claridad y mantenibilidad.
4. Evitar mocks innecesarios.
5. Reutilizar ObjectMother existentes.
6. Reutilizar helpers existentes y no crear nuevos.
7. No introducir librerías nuevas.
8. Seguir convenciones DDD/Clean Architecture.
9. Todos los nombres de tests deben estar en español.
10. Usar siempre Codeception Cest.
11. No usar PHPUnit TestCase directamente.

---

## Estructura obligatoria de los tests

Todos los tests deben seguir la siguiente estructura y orden:

1. Configuración de mocks
2. Ejecución del caso de uso
3. Assertions
4. Verificaciones finales si aplican

Ejemplo:

```php
public function siElUsuarioEsValidoEntoncesSeCreaCorrectamente(UnitTester $I): void
{
    $this->permission_service
        ->shouldReceive('checkPermission')
        ->once();
    $this->group_repository
        ->shouldReceive('search')
        ->once()
        ->with(Assert::equalTo($this->expectedCriteriaForGroupSearch()))
        ->andReturn([GroupData::getGroup()]);
    $this->response
        ->shouldReceive('execute')
        ->once()
        ->withArgs(function ($groups) use ($I) {
            $I->assertEquals([GroupData::getGroup()],$groups);
            return true;
        });

    $this->service->execute(GetGroupsRequestData::getGroupsRequest());
}
```

---

## Estructura de mocks

Usar la siguiente estructura:

```php
$this->admin_permission_service
    ->shouldReceive('checkPermission')
    ->once();
$this->group_repository
    ->shouldReceive('save')
    ->once()
    ->with(Assert::equalTo(GroupData::getGroup()));
```

---

## Estructura de mocks con withArgs

Usar la siguiente estructura:

```php
    $this->user_repository
        ->shouldReceive('save')
        ->once()
        ->withArgs(function ($user) use ($I) {
            $I->assertEquals(null, $user->getResponsible());
            return true;
        });
```

---

## Estructura de tests de error

```php
$this->client_repository
    ->shouldReceive('getClient')
    ->once()
    ->andThrow(ClientNotFoundException::class);
$I->expectThrowable(ClientNotFoundException::class, function () {
    $this->service->execute(CreateUserRequestData::getCreateUserRequest());
});
```

---

## Inicialización de tests

La construcción del test debe moverse siempre a `_before()`.

Ejemplo:

```php
public function _before(): void
{
    $this->prepareTest();
}
```

`prepareTest()` debe declararse al final de la clase.

---

## Reglas de Assert

* No usar `andReturnUsing()` salvo que sea estrictamente necesario.
* Priorizar `Assert::equalTo()`.

Ejemplo:

```php
use PHPUnit\Framework\Assert

->with(Assert::equalTo($expectedCriteria))
```

---

## Criteria helpers

Cuando se compare un Criteria, extraerlo a un método específico.

El método debe seguir el formato:

```php
expectedCriteriaForXXXXX
```

Ejemplo:

```php
$this->expectedCriteriaForGroupSearch()
```

---

## Ejemplo completo

```php
public function siElUsuarioEsValidoEntoncesSeCreaCorrectamente(UnitTester $I): void
{
    $this->permission_service
        ->shouldReceive('checkPermission')
        ->once();
    $this->group_repository
        ->shouldReceive('search')
        ->once()
        ->with(Assert::equalTo($this->expectedCriteriaForGroupSearch()))
        ->andReturn([GroupData::getGroup()]);
    $this->response
        ->shouldReceive('execute')
        ->once()
        ->withArgs(function ($groups) use ($I) {
            $I->assertEquals([GroupData::getGroup()], $groups);
            return true;
        });

    $this->service->execute(
        GetGroupsRequestData::getGroupsRequest()
    );
}
```

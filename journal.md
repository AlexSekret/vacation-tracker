---
tags:
  - Java
  - обучение
Date and time: 2025-10-10 14:06
---

# ПЛАН РЕАЛИЗАЦИИ ПЕТ-ПРОЕКТА

## **ПОДГОТОВИТЕЛЬНЫЙ ЭТАП (НЕДЕЛЯ 1)**

 - [x] **Настрой окружение**
   - [x] Установи JDK 17+, IntelliJ IDEA, Git
   - [x] Создай новый Spring Boot проект на [start.spring.io](https://start.spring.io)
   - [x] Добавь зависимости: Spring Web, Spring Data JPA, H2 Database, Lombok
   - [x] Добавить в корень проекта файл Specification.md
   - [x] Добавить в корень проекта файл Journal.md

- [x] **Создай базовую структуру пакетов**

   ```Java
   src/main/java/com/yourcompany/dayoff/
   ├── controller/
   ├── service/
   ├── repository/
   ├── entity/
   └── dto/
   ```

 - [x] **Настрой базу данных и миграции с flyway**
   - [x] Создай структуру папок для миграций
   - [x] Создай первую миграцию V1__Create_tables.sql
   - [x] Создай миграцию V2__Insert_test_data.sql
   - [x] Настрой application.properties
   - [x] Проверь миграции
      - [x] Запусти приложение
      - [x] Убедись, что Flyway создал таблицу flyway_schema_history
      - [x] Проверь, что все таблицы созданы и тестовые данные добавлены

**Хорошие практики с Flyway:**
- Каждая миграция в отдельном файле
- Названия файлов по шаблону:`V{версия}__{описание}.sql`
- Миграции должны быть идемпотентными (можно выполнять многократно)
- Не изменяй уже выполненные миграции

## **ЭТАП 1: СУЩНОСТИ И РЕПОЗИТОРИИ (НЕДЕЛЯ 1-2)**

1. [ ] **Создай Entity классы**
   - `Employees.java` с полями из ТЗ
   - `Overtimes.java` со связью @ManyToOne к Employee
   - `DayOffs.java` со связью @ManyToOne к Employee

2. [ ] **Добавь JPA аннотации**
   - `@Entity`, `@Id`, `@GeneratedValue`
   - `@ManyToOne` и `@JoinColumn` для связей
   - Используй `@CreationTimestamp` для дат создания

3. [ ] **Создай Repository интерфейсы**
   - `EmployeeRepository extends JpaRepository<Employee, Long>`
   - `OvertimeRepository extends JpaRepository<Overtime, Long>`
   - `DayOffRepository extends JpaRepository<DayOff, Long>`
      - Напиши тесты для репозиториев
         - EmployeeRepositoryTest с @DataJpaTest
         - Протестируй findAllByIsActiveTrue()
         - Проверь сохранение и поиск сущностей
4. [ ] **Напиши кастомные запросы**
   - `findAllByIsActiveTrue()` в EmployeeRepository
   - `findByEmployeeId()` в OvertimeRepository и DayOffRepository
- [ ] Протестируй миграции Flyway
	- [ ] FlywayMigrationTest с @SpringBootTest
	- [ ] Убедись, что все миграции применяются
	- [ ] Проверь целостность данных после миграций

## **ЭТАП 2: API ДЛЯ СОТРУДНИКОВ (НЕДЕЛЯ 2-3)**

1. **Создай DTO классы**
   - `EmployeeDTO.java` - для передачи данных
   - `CreateEmployeeRequest.java` - для создания
   - `EmployeeResponse.java` - для ответа
2. **Реализуй EmployeeService**
   - `getAllActiveEmployees()`
   - `createEmployee(CreateEmployeeRequest request)`
   - `updateEmployee(Long id, CreateEmployeeRequest request)`
   - `deactivateEmployee(Long id)` (не удалять!)
3. Напиши unit-тесты для EmployeeService
	- EmployeeServiceTest с @ExtendWith(MockitoExtension.class)
	- Протестируй создание, обновление, деактивацию сотрудников
	- Используй Mockito для моков репозитория
4. **Создай EmployeeController**
    - `GET /api/employees` - список активных сотрудников
    - `POST /api/employees` - создание сотрудника
    - `PUT /api/employees/{id}` - обновление сотрудника
    - `DELETE /api/employees/{id}` - деактивация (soft delete)
    - Напиши интеграционные тесты для EmployeeController
		- EmployeeControllerIT с @SpringBootTest и @AutoConfigureTestDatabase
		- Протестируй все REST endpoints
		- Проверь валидацию и коды ответов
5. **Добавь валидацию**
    - `@NotBlank` для обязательных полей
    - `@Size` для ограничения длины строк
    - `@Valid` в контроллере

## **ЭТАП 3: ПЕРЕРАБОТКИ (НЕДЕЛЯ 3-4)**

1. **Создай DTO для переработок**
    - `OvertimeDTO.java`
    - `CreateOvertimeRequest.java`
2. **Реализуй OvertimeService**
    - `createOvertime(CreateOvertimeRequest request)`
    - `getOvertimeByEmployeeId(Long employeeId)`
    - Протестируй OvertimeService
		- OvertimeServiceTest - unit тесты
		- Проверь корректность создания переработок
		- Убедись, что баланс рассчитывается правильно
3. **Создай OvertimeController**
    - `POST /api/overtime` - добавление переработки
    - `GET /api/overtime/employee/{employeeId}` - история переработок
    - Напиши интеграционные тесты для OvertimeController
	    - OvertimeControllerIT
	    - Протестируй создание переработок и получение истории
4. **Добавь Enum для причин**
    - `OvertimeReason.java`: WORKDAY, VACATION, SICKDAY

## **ЭТАП 4: ОТГУЛЫ (НЕДЕЛЯ 4-5)**

1. **Создай DTO для отгулов**
    - `DayOffDTO.java`
    - `CreateDayOffRequest.java`

2. **Реализуй DayOffService**
    - `createDayOff(CreateDayOffRequest request)`
    - Проверка баланса перед созданием
    - Бросай исключение при недостатке часов
    - Протестируй логику отгулов
		- DayOffServiceTest - проверь валидацию баланса
		- Напиши тест, когда часов недостаточно (должен быть exception)
		- Протестируй успешное списание часов
3. **Создай DayOffController**
    - `POST /api/dayoff` - создание отгула
    - `GET /api/dayoff/employee/{employeeId}` - история отгулов
    - Интеграционные тесты DayOffController
		- DayOffControllerIT
		- Проверь сценарии с достаточным и недостаточным балансом
4. **Обработай исключения**
    - Создай `@ControllerAdvice` для обработки ошибок
    - Возвращай понятные сообщения об ошибках

## **ЭТАП 5: БАЛАНС И ОТЧЕТЫ (НЕДЕЛЯ 5-6)**

1. **Реализуй расчет баланса**
    - Создай метод `calculateBalance(Long employeeId)` в сервисе
    - Используй SQL запрос из представления или Java логику
2. **Создай EmployeeBalanceService**
    - `getAllEmployeesWithBalance()` - список с балансом
    - `getEmployeeBalance(Long employeeId)` - баланс одного
3. **Добавь эндпоинты для баланса**
    - `GET /api/balance` - общий отчет
    - `GET /api/balance/employee/{id}` - баланс сотрудника
4. Напиши комплексные тесты для баланса
	- BalanceCalculationIT - интеграционный тест
	- Создай сценарий: переработки → отгулы → проверь баланс
	- Проверь корректность расчетов в разных сценариях
5. Протестируй Edge Cases
	- Сотрудник без переработок и отгулов
	- Несколько переработок подряд
	- Отгулы у неактивного сотрудника

## **ЭТАП 6: ФРОНТЕНД И ТЕСТИРОВАНИЕ (НЕДЕЛЯ 6-7)**

1. **Создай простой UI**
    - Используй Thymeleaf для шаблонов
    - Главная страница с таблицей балансов
    - Формы для добавления сотрудников, переработок, отгулов

2. **Напиши базовые тесты**
    - `EmployeeServiceTest` с @DataJpaTest
    - Протестируй создание сотрудника и расчет баланса

3. **Добавь логирование**
    - Используй SLF4J в сервисах
    - Логируй важные операции (создание, ошибки)
4. Настрой Jacoco для покрытия кода
	- Добавь в pom.xml плагин jacoco
	- Цель: 70%+ покрытие кода
	- Сгенерируй отчет о покрытии
5. Напиши тесты для исключений
	- ExceptionHandlerTest
	- Проверь, что все ошибки обрабатываются корректно
	- Убедись в понятных сообщениях для пользователя
6. Запусти все тесты вместе
	- mvn clean test - unit тесты
	- mvn clean verify - unit + integration тесты
	- Убедись, что все тесты проходят

## **РЕКОМЕНДАЦИИ ПО КАЧЕСТВУ КОДА**

**Обязательные практики:**
- Комментируй только сложную бизнес-логику
- Используй понятные названия методов и переменных
- Разделяй ответственность между классами
- Обрабатывай все возможные исключения

**Что проверять в каждом PR:**
- Код компилируется без ошибок
- Все тесты проходят
- Нет закомментированного кода
- Соблюдены принципы REST API
- Валидация работает корректно

**Постепенное усложнение:**
1. Сначала сделай работающий прототип
2. Затем добавь валидацию и обработку ошибок
3. Потом улучши UI/UX
4. В конце добавь тесты и оптимизации

Каждую неделю проводи код-ревью и обсуждай архитектурные решения. Удачи в реализации! 🚀

## **ПРАКТИКИ ТЕСТИРОВАНИЯ ДЛЯ JUNIOR**

**Структура теста (AAA Pattern):**

```java
@Test
void shouldCreateEmployee_WhenValidRequest() {
    // Arrange - подготовка данных
    CreateEmployeeRequest request = new CreateEmployeeRequest("John", "Doe", "Developer");

    // Act - выполнение действия
    EmployeeResponse response = employeeService.createEmployee(request);
    
    // Assert - проверка результата
    assertThat(response.getFirstName()).isEqualTo("John");
}
```

**Именование тестов:**
- `should{Что}__When{Условие}`
- `shouldThrowException__WhenInsufficientBalance`


**Что мокать:**
- Repository - всегда мок
- External services - мок
- Другие сервисы внутри приложения - мок


**Интеграционные тесты:**
- Используй H2 in-memory базу
- Аннотация`@Transactional`для отката изменений
- `TestRestTemplate`для вызовов API

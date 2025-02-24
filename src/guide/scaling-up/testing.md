<script setup>
import TestingApiSwitcher from './TestingApiSwitcher.vue'
</script>

# Тестування {#testing}

## Для чого тестувати? {#why-test}

Автоматизовані тести допомагають вам і вашій команді швидко та впевнено створювати складні застосунки Vue, запобігаючи регресії та заохочуючи вас розбивати ваш застосунок на функції, модулі, класи та компоненти, які можна перевірити. Як і у випадку з будь-яким іншим застосунком, ваш новий застосунок Vue може дати збій з багатьох причин, і важливо, щоб ви могли виявити ці проблеми та виправити їх перед випуском.

У цьому посібнику ми розглянемо базову термінологію та надамо рекомендації щодо вибору інструментів для вашого застосунку Vue 3.

Є один специфічний розділ Vue, що охоплює композиційні функції. Дивіться [Тестування композиційних функцій](#testing-composables) нижче для додаткової інформації.

## Коли тестувати {#when-to-test}

Починайте тестування заздалегідь! Ми рекомендуємо вам почати писати тести якнайшвидше. Чим довше ви відкладаєте додавання тестів, тим більше буде залежностей у вашого застосунку і тим важче буде його запустити.

## Типи тестування {#testing-types}

Розробляючи стратегію тестування застосунку Vue, ви повинні використовувати такі типи тестування:

- **Модульний**: Перевіряє, чи вхідні дані для заданої функції, класу чи композиційної функції створюють очікуваний результат або побічні ефекти.
- **Компонентний**: Перевіряє, чи ваш компонент монтується, рендериться, чи можна з ним взаємодіяти та чи поводиться він належним чином. Ці тести включають більше коду, ніж модульні тести, є більш складними та потребують більше часу для виконання.
- **Наскрізний**: Перевіряє функції, які охоплюють кілька сторінок, і надсилає реальні мережеві запити вашому робочому застосунку Vue. Ці тести часто передбачають встановлення бази даних або іншої серверної частини.

Кожен тип тестування відіграє певну роль у стратегії тестування вашого застосунку, і кожен захистить вас від різних типів проблем.

## Огляд {#overview}

Ми коротко обговоримо, що собою являє кожен із них, як їх можна реалізувати для застосунків Vue, і надамо деякі загальні рекомендації.

## Модульне тестування {#unit-testing}

Модульні тести пишуть для того, щоби перевірити, що невеликі ізольовані блоки коду працюють належним чином. Модульний тест зазвичай охоплює окрему функцію, клас, композиційний метод або модуль. Модульні тести зосереджені на логічній коректності та стосуються лише невеликої частини загальної функціональності застосунку. Вони можуть імітувати великі частини середовища вашого застосунку (наприклад, початковий стан, складні класи, сторонні модулі та мережеві запити).

Загалом модульні тести виявляють проблеми з бізнес-логікою та логічною правильністю функції.

Візьмемо, наприклад, цю функцію `increment`:

```js
// helpers.js
export function increment(current, max = 10) {
  if (current < max) {
    return current + 1
  }
  return current
}
```

Оскільки вона дуже самодостатня, буде легко викликати функцію `increment` і стверджувати, що вона повертає те, що має, тому ми напишемо модульний тест.

Якщо будь-яке з цих тверджень не виконується, очевидно, що проблема міститься у функції `increment`.

```js{4-16}
// helpers.spec.js
import { increment } from './helpers'

describe('increment', () => {
  test('збільшує поточне число на 1', () => {
    expect(increment(0, 10)).toBe(1)
  })

  test('не збільшує поточне число вище максимального', () => {
    expect(increment(10, 10)).toBe(10)
  })

  test('має максимальне значення за замовчуванням 10', () => {
    expect(increment(10)).toBe(10)
  })
})
```

Як згадувалося раніше, модульне тестування зазвичай застосовується до самодостатньої бізнес-логіки, компонентів, класів, модулів або функцій, які не включають візуалізацію інтерфейсу користувача, мережеві запити чи інші проблеми середовища.

Зазвичай це звичайні модулі JavaScript / TypeScript, не пов'язані з Vue. Загалом, написання модульних тестів для бізнес-логіки в додатках Vue суттєво не відрізняється від додатків, що використовують інші фреймворки.

Є два випадки, коли ви ВИКОНУЄТЕ модульне тестування функцій Vue:

1. Композиційні функції
2. Компоненти

### Композиційні функції {#composables}

Однією з категорій функцій, специфічних для застосунків Vue, є [Композиційні функції](/guide/reusability/composables), які можуть потребувати спеціальної обробки під час тестування.
Дивіться [Тестування композиційних функцій](#testing-composables) нижче для додаткової інформації.

### Модульне тестування компонентів {#unit-testing-components}

Компоненти можуть тестуватися двома способами:

1. Біла скринька: Модульне тестування

   Тести, які є "тестами білої скриньки", проінформовані про деталі реалізації та залежність компонента. Вони зосереджені на **ізоляції** компонента, що тестується. Ці тести зазвичай залучають деякі фіктивні, якщо не всі фіктивні дочірні елементи вашого компонента, а також налаштування стану і залежностей плагіна (наприклад, Pinia).

2. Чорна скринька: Компонентне тестування

   Тести, які є "тестами чорної скриньки", не знають про деталі реалізації компонента. Ці тести імітують якнайменше, щоб перевірити інтеграцію вашого компонента та всієї системи. Зазвичай вони рендерять усі дочірні компоненти та вважаються скоріше "інтеграційним тестом". Дивіться [Рекомендації щодо компонентного тестування](#component-testing) нижче.

### Рекомендації {#recommendation}

- [Vitest](https://vitest.dev/)

  Оскільки офіційне встановлення, створене `create-vue` базується на [Vite](https://vitejs.dev/), ми рекомендуємо використовувати фреймворк модульного тестування, який може використовувати ту саму конфігурацію та трансформувати конвеєр безпосередньо з Vite. [Vitest](https://vitest.dev/) - це фреймворк модульного тестування, розроблений спеціально для цієї мети, створений та підтримується членами команди Vue / Vite. Він інтегрується з проєктами на основі Vite з мінімальними зусиллями та працює блискавично швидко.

### Інші варіанти {#other-options}

- [Peeky](https://peeky.dev/) - це ще один швидкий модуль тестування з першокласною інтеграцією Vite. Він також створений членом основної команди Vue і пропонує графічний інтерфейс тестування.

- [Jest](https://jestjs.io/) - популярний фреймворк модульного тестування, який можна налаштувати для роботи з Vite через [vite-jest](https://github.com/sodatea/vite-jest) пакет. Однак ми рекомендуємо Jest тільки в тому випадку, якщо в вас є наявний набір тестів Jest, який потрібно перенести до проєкту на основі Vite, оскільки Vitest пропонує більш повну інтеграцію та кращу продуктивність.

## Компонентне тестування {#component-testing}

У застосунках Vue компоненти є основними будівельними блоками інтерфейсу користувача. Таким чином, компоненти є природною одиницею ізоляції, коли йдеться про перевірку поведінки вашого застосунку. З точки зору деталізації, компонентне тестування знаходиться десь вище від модульного тестування та може розглядатися як форма інтеграційного тестування. Більшість вашого застосунку Vue повинна бути охоплена компонентним тестуванням, і ми рекомендуємо, щоб кожен компонент Vue мав власний файл специфікацій.

Компонентне тестування має виявляти проблеми, пов'язані з реквізитами компонента, подіями, слотами, які він надає, стилями, класами, хуками життєвого циклу, тощо.

Компонентне тестування не має імітувати дочірні компоненти, а натомість перевіряти взаємодію між вашим компонентом і його дочірніми компонентами, взаємодіючи з ними так, як це робив би користувач. Наприклад, компонентний тест має натискати на елемент так, як це зробив би користувач, замість програмної взаємодії з компонентом.

Компонентні тести мають зосереджуватися на публічних інтерфейсах компонента, а не на внутрішніх деталях реалізації. Для більшості компонентів загальнодоступний інтерфейс обмежується: випроміненими подіями, атрибутами та слотами. Під час тестування не забувайте **тестувати що робить компонент, а не як він це робить**.

**РОБІТЬ**

- Для **Візуальної** логіки: стверджуйте правильний результат рендерінгу на основі введених реквізитів і слотів.
- Для **поведінкової** логіки: затверджуйте правильні оновлення рендерінгу або випромінені події у відповідь на події введення користувачем.

  У наведеному нижче прикладі ми демонструємо компонент Stepper, який має елемент DOM з позначкою "збільшити" та який може бути натиснутий. Ми передаємо реквізит під назвою `max`, який запобігає збільшенню Stepper після `2`, тож якщо ми натиснемо кнопку 3 рази, інтерфейс все одно має показувати `2`.

  Ми нічого не знаємо про реалізацію Stepper, лише те, що "вхід" - це реквізит `max`, а "вихід" - це стан DOM, яким його побачить користувач.

<TestingApiSwitcher>

<div class="testing-library-api">

```js
const { getByText } = render(Stepper, {
  props: {
    max: 1
  }
})

getByText('0') // Неявне твердження, що "0" знаходиться в межах компонента

const button = getByText('збільшити')

// Надсилання події натискання до нашої кнопки збільшення.
await fireEvent.click(button)

getByText('1')

await fireEvent.click(button)
```

</div>

<div class="vtu-api">

```js
const valueSelector = '[data-testid=stepper-value]'
const buttonSelector = '[data-testid=increment]'

const wrapper = mount(Stepper, {
  props: {
    max: 1
  }
})

expect(wrapper.find(valueSelector).text()).toContain('0')

await wrapper.find(buttonSelector).trigger('click')

expect(wrapper.find(valueSelector).text()).toContain('1')
```

</div>

<div class="cypress-api">

```js
const valueSelector = '[data-testid=stepper-value]'
const buttonSelector = '[data-testid=increment]'

mount(Stepper, {
  props: {
    max: 1
  }
})

cy.get(valueSelector)
  .should('be.visible')
  .and('contain.text', '0')
  .get(buttonSelector)
  .click()
  .get(valueSelector)
  .should('contain.text', '1')
```

</div>

</TestingApiSwitcher>

- **НЕ РОБІТЬ**

  Не заявляйте про приватний стан примірника компонента та не тестуйте приватні методи компонента. Тестування деталей реалізації робить тести крихкими, оскільки вони, швидше за все, зламаються та вимагають оновлення, коли реалізація змінюється.

  Основне завдання компонента - це відтворення правильного рендерінгу DOM, тому тести, зосереджені на виводі DOM, забезпечують такий самий рівень гарантії правильності (якщо не більший), але водночас є більш надійними та стійкими до змін.

  Не покладайтеся виключно на тести миттєвих знімків. Затвердження рядків HTML не визначає правильності. Пишіть тести навмисно.

  Якщо метод потрібно ретельно протестувати, подумайте про те, щоб витягти його в окрему службову функцію та написати спеціальний модульний тест для нього. Якщо його неможливо витягнути чисто, його можна протестувати як частину компонента, інтеграції або наскрізного тесту, який охоплює його.

### Рекомендації {#recommendation-1}

- [Vitest](https://vitest.dev/) для компонентів або композиційних функцій, які рендеряться без відображення (наприклад, функція [`useFavicon`](https://vueuse.org/core/useFavicon/#usefavicon) у VueUse). Компоненти та DOM можуть тестуватися за допомогою [@testing-library/vue](https://testing-library.com/docs/vue-testing-library/intro).

- [Компонентне тестування Cypress](https://on.cypress.io/component) для компонентів, чия очікувана поведінка залежить від правильного рендерингу стилів, або ініціювання власних подій DOM. Може бути використаним з Бібліотекою тестування через [@testing-library/cypress](https://testing-library.com/docs/cypress-testing-library/intro).

Основні відмінності між Vitest і засобами запуску на основі браузера полягають у швидкості та контексті виконання. Якщо казати стисло, засоби запуску на основі браузера, такі як Cypress, можуть виявляти проблеми, з якими не можуть впоратися засоби виконання на основі вузлів, такі як Vitest (наприклад, проблеми зі стилями, реальні рідні події DOM, файли cookie, локальне сховище та збої мережі), але браузерні засоби виконання _на декілька порядків повільніше, ніж Vitest_, тому що вони відкривають браузер, компілюють ваші таблиці стилів, тощо. Cypress - це засіб виконання на основі браузеру, який підтримує компонентні тестування. Будь ласка, ознайомтеся з [сторінкою порівняння Vitest](https://vitest.dev/guide/comparisons.html#cypress), щоб отримати останню інформацію про порівняння Vitest та Cypress.

### Монтування бібліотек {#mounting-libraries}

Компонентне тестування часто включає монтування компонента, що тестується окремо, ініціювання змодельованих подій введення користувачем і підтвердження на рендереному виході DOM. Існують спеціальні бібліотеки службових програм, які спрощують ці завдання.

- [`@testing-library/vue`](https://github.com/testing-library/vue-testing-library) - це бібліотека тестування Vue, орієнтована на компонентне тестування, не покладаючись на деталі реалізації. Створена з урахуванням доступності, її підхід також робить рефакторинг легким. Її рушійний принцип полягає в тому, що чим більше тести схожі на спосіб використання програмного забезпечення, тим більшу впевненість вони можуть забезпечити.

- [`@vue/test-utils`](https://github.com/vuejs/test-utils) - це офіційна бібліотека низькорівневого тестування компонентів, яка була написана для надання доступу користувачам до спеціальних API Vue. Це також бібліотека нижчого рівня, на основі якої побудовано `@testing-library/vue`.

Ми рекомендуємо використовувати `@testing-library/vue` для компонентного тестування в застосунках, оскільки його фокус краще відповідає пріоритетам тестування застосунків. Використовуйте `@vue/test-utils`, лише якщо ви створюєте розширені компоненти, які потребують тестування внутрішніх компонентів Vue.

### Інші варіанти {#other-options-1}

- [Nightwatch](https://v2.nightwatchjs.org/) - це засіб тестування E2E з підтримкою тестування компонентів Vue. ([Приклад проєкту](https://github.com/nightwatchjs-community/todo-vue) з Nightwatch v2)

## E2E тестування {#e2e-testing}

У той час як модульні тести забезпечують розробникам певний ступінь впевненості, модульні та компонентні тести обмежені у своїх можливостях щодо забезпечення цілісного охоплення застосунку при розгортанні його у виробництво. В результаті наскрізні (E2E) тести охоплюють, мабуть, найважливіший аспект застосунку: що відбувається, коли користувачі фактично використовують ваші застосунки.

Наскрізні тести зосереджені на поведінці багатосторінкового застосунку, які надсилають мережеві запити до вашого виробничого застосунку Vue. Вони часто передбачають створення бази даних або іншої серверної частини і навіть можуть запускатися в живому проміжному тестовому середовищі.

Наскрізні тести часто виявляють проблеми з вашим маршрутизатором, бібліотекою керування станом, компонентами верхнього рівня (наприклад, App чи Layout), загальнодоступними ресурсами або будь-якою обробкою запитів. Як зазначено вище, вони виявляють критичні проблеми, які неможливо виявити за допомогою модульних або компонентних тестів.

Наскрізні тести не імпортують жодного коду вашої застосунку Vue, а натомість повністю покладаються на тестування вашого застосунку шляхом навігації по цілих сторінках у реальному браузері.

Наскрізні тести перевіряють багато рівнів у вашому застосунку. Вони можуть бути націлені або на ваш локальний застосунок, або навіть на живе проміжне тестове середовище. Тестування у вашому проміжному середовищі включає не лише ваш зовнішній код і статичний сервер, але й усі пов'язані серверні служби та інфраструктуру.

> Чим більше ваші тести нагадують те, як використовується ваше програмне забезпечення, тим більшу впевненість вони можуть дати вам. - [Кент С. Доддс](https://twitter.com/kentcdodds/status/977018512689455106) - Автор Бібліотеки тестування

Перевіряючи те, як дії користувача впливають на ваш застосунок, E2E тести часто є ключем до більшої впевненості в тому, чи працює застосунок належним чином чи ні.

### Вибір рішення для тестування E2E {#choosing-an-e2e-testing-solution}

У той час як наскрізне (E2E) тестування здобуло в інтернеті негативної репутації через ненадійні (нестабільні) тести та уповільнення процесів розробки, сучасні інструменти E2E зробили кроки вперед, щоби створити більш надійні, інтерактивні та корисніші тести. У наступних розділах наведено деякі вказівки щодо того, про що слід пам'ятати при виборі фреймворку E2E тестування для вашого застосунку.

#### Кросбраузерне тестування {#cross-browser-testing}

Однією з основних переваг наскрізного (E2E) тестування є його здатність тестувати ваш застосунок в декількох браузерах. Хоча може здатися бажаним мати 100% кросбраузерне покриття, важливо зазначити, що кросбраузерне тестування має меншу віддачу від ресурсів команди через додатковий час і потужність пристрою, необхідні для їх послідовного виконання. У зв’язку з цим варто пам’ятати про цей компроміс, вибираючи кількість кросбраузерного тестування, яке потребує ваш застосунок.

#### Швидші цикли зворотного зв'язку {#faster-feedback-loops}

Одна з основних проблем наскрізних (E2E) тестів та розробки полягає в тому, що запуск всього пакету займає багато часу. Як правило, це робиться лише в конвеєрах безперервної інтеграції та розгортання (CI/CD). Сучасні фреймворки E2E тестування допомогли вирішити цю проблему, додавши такі функції, як розпаралелювання, що дозволяє конвеєрам CI/CD працювати набагато швидше, ніж раніше. Крім того, під час локальної розробки, можливість вибірково запускати один тест для сторінки, над якою ви працюєте, а також забезпечення гарячого перезавантаження тестів може допомогти підвищити ефективність робочого процесу та продуктивність розробника.

#### Першокласний досвід налагодження {#first-class-debugging-experience}

Хоча розробники традиційно покладалися на журнали сканування у вікні терміналу, щоб визначити що пішло не так під час тестування, сучасні наскрізні (E2E) тестові фреймворки дозволяють розробникам використовувати інструменти, з якими вони вже знайомі, наприклад, інструменти розробника браузера.

#### Видимість в режимі без відображення {#visibility-in-headless-mode}

Коли наскрізні (E2E) тести виконуються в конвеєрах безперервної інтеграції/розгортання, вони часто виконуються в автономних браузерах (тобто видимий браузер не відкривається для перегляду користувачем). Критично важливою особливістю сучасних фреймворків E2E тестування є можливість переглядати знімки та/або відео застосунку під час тестування, що дає певне уявлення про те, чому виникають помилки. Історично склалося так, що підтримувати ці інтеграції було важко.

### Рекомендації {#recommendation-2}

- [Cypress](https://www.cypress.io/)

  Загалом, ми вважаємо, що Cypress надає найповніше рішення E2E із такими функціями, як інформативний графічний інтерфейс, чудові можливості налагодження, вбудовані твердження та заглушки, стійкість до розшаровування, паралелізація та знімки. Як згадувалося вище, він також забезпечує підтримку [Компонентного тестування](https://docs.cypress.io/guides/component-testing/introduction). Однак він підтримує лише браузери на основі Chromium і Firefox.

### Інші варіанти {#other-options-2}

- [Playwright](https://playwright.dev/) також є чудовим рішенням для E2E тестування із ширшою підтримкою браузерів (переважно WebKit). Дивіться [Чому Playwright](https://playwright.dev/docs/why-playwright) для більш детальної інформації.

- [Nightwatch v2](https://v2.nightwatchjs.org/) це рішення для E2E тестування, засноване на [Selenium WebDriver](https://www.npmjs.com/package/selenium-webdriver). Це забезпечує найширший діапазон підтримки браузерів.

## Рецепти {#recipes}

### Додавання Vitest до проєкту {#adding-vitest-to-a-project}

У проєктах на основі Vite, виконайте:

```sh
> npm install -D vitest happy-dom @testing-library/vue
```

Далі оновіть конфігурацію Vite, додавши блок параметрів `test`:

```js{6-12}
// vite.config.js
import { defineConfig } from 'vite'

export default defineConfig({
  // ...
  test: {
    // активувати jest-подібні глобальні API тестів
    globals: true,
    // симулювати DOM за допомогою happy-dom
    // (вимагає встановлення happy-dom як однорангової залежності)
    environment: 'happy-dom'
  }
})
```

:::tip Порада:
Якщо ви використовуєте TypeScript, додайте `vitest/globals` до параметру `types` у `tsconfig.json`.

```json
// tsconfig.json

{
  "compilerOptions": {
    "types": ["vitest/globals"]
  }
}
```

:::

Потім створіть у своєму проєкті файл із закінченням `*.test.js`. Ви можете розмістити всі тестові файли в тестовому каталозі в корені проєкту або в тестових каталогах поруч із вихідними файлами. Vitest автоматично шукатиме їх за умовами іменування.

```js
// MyComponent.test.js
import { render } from '@testing-library/vue'
import MyComponent from './MyComponent.vue'

test('it should work', () => {
  const { getByText } = render(MyComponent, {
    props: {
      /* ... */
    }
  })

  // assert output
  getByText('...')
})
```

Нарешті, оновіть `package.json`, додавши сценарій тесту та запустіть його:

```json{4}
{
  // ...
  "scripts": {
    "test": "vitest"
  }
}
```

```sh
> npm test
```

### Тестування композиційних функцій {#testing-composables}

> Цей розділ передбачає, що ви ознайомилися з розділом [Композиційні функції](/guide/reusability/composables).

Коли справа доходить до тестування композиційних функцій, ми можемо розділити їх на дві категорії: композиційні функції, які не залежать від екземпляра основного компонента, та композиційні функції, які залежать від нього.

Композиційна функція залежить від екземпляра основного компонента, коли вона використовує такі API:

- Хуки життєвого циклу
- Provide / Inject

Якщо композиційна функція використовує тільки API реактивності, тоді її можна перевірити, безпосередньо викликавши її та підтвердивши її повернутий стан / методи:

```js
// counter.js
import { ref } from 'vue'

export function useCounter() {
  const count = ref(0)
  const increment = () => count.value++

  return {
    count,
    increment
  }
}
```

```js
// counter.test.js
import { useCounter } from './counter.js'

test('useCounter', () => {
  const { count, increment } = useCounter()
  expect(count.value).toBe(0)

  increment()
  expect(count.value).toBe(1)
})
```

Компонент, який залежить від хуків життєвого циклу або Provide / Inject, має бути загорнутий у головний компонент для тестування. Ми можемо створити такий помічник, як наведено нижче:

```js
// test-utils.js
import { createApp } from 'vue'

export function withSetup(composable) {
  let result
  const app = createApp({
    setup() {
      result = composable()
      // заглушити попередження про відсутній шаблон
      return () => {}
    }
  })
  app.mount(document.createElement('div'))
  // повернути результат та екземпляр застосунку
  // для тестування надання (provide) / розмонтувння (unmount)
  return [result, app]
}
```

```js
import { withSetup } from './test-utils'
import { useFoo } from './foo'

test('useFoo', () => {
  const [result, app] = withSetup(() => useFoo(123))
  // імітувати provide для впровадження тестування
  app.provide(...)
  // виконати твердження
  expect(result.foo.value).toBe(1)
  // ініціювати onUnmounted хук за потреби
  app.unmount()
})
```

Для більш складних композиційних функцій також може бути простіше тестувати їх, написавши тести для компонента-огортки з використанням методів [Компонентного тестування](#component-testing).

<!--
TODO можна буде додати більше рецептів тестування налалі, наприклад
- Як налаштувати CI через GitHub дії
- Як зробити імітування у компонентних тестуваннях
-->

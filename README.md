# Для чего этот скрипт

Скрипт позволяет добавить персонализированную подпись к шаблонам писем, которые находятся в вашем Confluence. После того как вы добавите свою подпись, она автоматически прикрепляется к кнопкам с шаблонами писем и добавляется в письмо при нажатии на кнопку. <br>
Подпись сохраняется локально в браузере, используя `localStorage`, и будет доступна даже после перезагрузки страницы.<br>

До:<br>
![image](https://github.com/user-attachments/assets/a87bfbcb-fe41-49ba-a50f-7bb63f47b926)

После:<br>
![image](https://github.com/user-attachments/assets/eef9db0f-0c24-44df-b48f-4f79803e076f)


# Начало работы

* [Кнопка шаблона письма](#кнопка-шаблона-письма)
* [Шаги для использования](#шаги-для-использования)
* [Добавление и удаление подписи](#добавление-и-удаление-подписи)
* [Код скрипта](#код-скрипта)

## Кнопка шаблона письма
1.	Добавьте на страницу макрос UI Button<br>
![image](https://github.com/user-attachments/assets/69bc259e-79c5-43fb-80fe-d75cbc1ed880)
2.	В настройках кнопки установите галочку:<br>
![image](https://github.com/user-attachments/assets/2e27f0c6-016f-4e0a-ad7b-ecc53597c18e)
3.	Создайте ссылку с помощью сайта https://mailtolinkgenerator.com/ и вставьте ее в URL<br>
![image](https://github.com/user-attachments/assets/25c00d92-852e-4be5-9d1a-09da521d093b)
4.	Теперь при нажатии на кнопку будет открываться почтовый клиент, установленный по умолчанию и использоваться тот шаблон, который вы вставили в URL.<br>

## Шаги для использования:
1.	Перейдите на страницу Confluence<br>
2.	Откройте страницу для редактирования.<br>
3.	Добавьте макрос HTML на страницу:<br>
    1. Нажмите "Вставить прочий контент" > "Другие макросы" > "HTML".<br>
     ![image](https://github.com/user-attachments/assets/ccdf7533-8269-4653-995d-396a64db083d)
     ![image](https://github.com/user-attachments/assets/8829a091-cac8-4249-926d-70cbf3096e1d)
     ![image](https://github.com/user-attachments/assets/b8f6b4cb-2fd3-4467-a5fa-6232c7bf7e88)
     ![image](https://github.com/user-attachments/assets/332843ac-55b1-4fb4-8895-24f03c0b55c3)
    2. Вставьте код в поле макроса.<br>
4.	Сохраните изменения.<br>
![image](https://github.com/user-attachments/assets/749453f2-ba08-43e7-9cba-9ed2ee5ba4ae)


## Добавление и удаление подписи
Добавление подписи<br>
* В поле "Вставь сюда подпись и нажми на кнопку 'Создать подпись'" введите или вставьте текст подписи.
* Подпись может быть любой текст, который вы хотите использовать.
* После того как подпись введена, нажмите на кнопку "Создать подпись".
* Ваша подпись будет сохранена, и она появится в блоке "Текущая подпись".
* Эта подпись будет автоматически применена ко всем кнопкам на странице, где это необходимо.
* После создания подписи, текущая подпись будет отображена под надписью "Текущая подпись:". Вы сможете увидеть ваш текст подписи.

Удаление подписи:<br>
* Если вы хотите удалить подпись, нажмите на кнопку "Удалить подпись".
* Подпись будет удалена и отображение текущей подписи изменится на "Отсутствует".
* Вся информация о подписи, сохраненная в локальном хранилище, также будет удалена.

## Код скрипта:
```
<!-- Таблица для ввода подписи и кнопок -->
<table>
  <tr>
    <td>
      <textarea
        class="js-input"
        placeholder='Вставь сюда подпись и нажми на кнопку "Создать подпись"'
        id="inputSignature"
        name="inputBody"
        rows="7"
      ></textarea>
    </td>
    <td>
      <div><b>Текущая подпись:</b></div>
      <div id="currentSignature">Отсутствует</div>
    </td>
  </tr>
  <tr>
    <td>
      <div class="button-container">
        <button id="createSignatureButton" onclick="createSignature()">
          Создать подпись
        </button>
        <button id="deleteSignatureButton" onclick="clearSignature()">
          Удалить подпись
        </button>
      </div>
    </td>
  </tr>
</table>

<script>
  // Функция для создания подписи
  function createSignature() {
    const signature = encodeURIComponent(
      document.querySelector(".js-input").value
    );

    if (!signature) {
      alert("Пожалуйста, вставьте подпись в текстовое поле перед созданием.");
      return;
    }

    // Сохраняем подпись в localStorage
    localStorage.setItem("signatureKey", signature);
    alert("Подпись успешно создана.");
    location.reload();
  }

  // Функция для удаления подписи
  function clearSignature() {
    localStorage.removeItem("signatureKey");
    alert("Подпись удалена.");
    location.reload();
  }

  // Функция для применения подписи ко всем кнопкам
  function addSignatureToButtons(signature) {
    const buttons = document.querySelectorAll(".rwui_button");
    for (let i = 0; i < buttons.length; i++) {
      const button = buttons[i];
      button.href = button.href + signature;
    }
  }

  // Функция для отображения текущей подписи
  function displayCurrentSignature(signature) {
    const decodedSignature = decodeURIComponent(signature).replaceAll(
      "%0A",
      "\n"
    );
    document.querySelector("#currentSignature").textContent = decodedSignature;
  }

  // Функция для инициализации подписи при загрузке страницы
  window.addEventListener("load", () => {
    setTimeout(initializeSignature, 100);
  });

  // Функция для инициализации подписи из localStorage
  function initializeSignature() {
    const savedSignature = localStorage.getItem("signatureKey");

    if (savedSignature) {
      // Применяем подпись, если она есть
      addSignatureToButtons(savedSignature);
      displayCurrentSignature(savedSignature);
    }
  }
</script>

<style>
  /* Стили для поля ввода подписи */
  .js-input {
    width: 400px;
    height: 150px;
    font-size: 14px;
    font-family: Arial, sans-serif;
    padding: 10px;
  }

  /* Стили для контейнера кнопок, чтобы они были в одной строке */
  .button-container {
    display: flex;
    gap: 10px;
  }

  /* Стили для кнопок */
  #createSignatureButton,
  #deleteSignatureButton {
    background-color: #4caf50;
    color: white;
    font-size: 16px;
    padding: 10px;
    border: none;
    cursor: pointer;
  }

  /* Стили для отображения текущей подписи */
  #currentSignature {
    white-space: pre-line;
    font-weight: bold;
  }
</style>
```

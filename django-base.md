# IT-Compot Backend Methods.

## Содержание
- [Старт проекта](#простой-старт-проекта)
- [Маршруты](#маршруты)
  - [Статические маршруты](#статические-маршруты)
  - [Динамические маршруты](#динамические-маршруты)
  - [Именование маршрутов и перенаправления](#url-naming--redirects-именование-маршрутов-и-перенаправление)
- [Обмен данными клиент - сервер](#обмен-данными-клиент----сервер)
- [Модели](#models)
  - [Создание модели и миграции](#создание-простейшей-модели-для-товара)
  - [Fields - полям модели](#model-fields)
  - [ORM](#orm)
  - [Media Files](#Media-Files)
- [Шаблонизация](#шаблонизация)
  - [Использование статических файлов](#Использование-статических-файлов-в-шаблоне)
  - [Передача переменных внутрь шаблона](#передача-переменных-внутрь-шаблона)
  - [Формирование внутренних маршрутов](#Формирование-внутренних-маршрутов-в-шаблоне)
  - [Использование циклов и условий в шаблоне](#использование-циклов-и-условий-в-шаблоне)
  - [Include в шаблонах](#Include-в-шаблонах)
  - [Переменные в Include](#Переменные-в-Include)
  - [Расширение/Наследование шаблонов](#расширениенаследование-шаблонов)
- [Структура проекта](#Структура-проекта)

## <u>Простой старт проекта</u>

1. Создание виртуальной среды с именем venv_name.

    `python -m venv venv_name`

2. Активация виртуального окружения.

    `./venv_name/Scripts/activate`

3. Установка django.
   
   `pip install django`

4. Создание django проекта с именем project_name.
   
   `django-admin startproject project_name`

5. Переходим в директорию созданного проекта.
   
   `cd ./project_name`

6. Запуск сервера django.
   
   `python manage.py runserver`

7. Создание django приложения с именем app_name.
   
   `python manage.py startapp app_name`

8. Добавим приложение в проект.

    ```python
    # settings.py
    INSTALLED_APPS = [
        ...,
        ...,
        'app_name',
    ]
    ```

## <u>Маршруты</u>
Чтобы привязать определенную ссылку к отображению html страницы, <br> нужно добавить желаемую ссылку в urls.py
и создать функцию для рендера html странички.

### Статические маршруты
В данном примере мы соединим ссылку `http://127.0.0.1:8000/shop/products/` со страничкой `products.html`.

1. В файле views.py создаем функцию `example_view` которая будет возвращать отрендеренный шаблон `products.html`.
   ```python
      # views.py
      def example_view(request):
          # возвращаем отрендеренную html страничку products
          return render(request, 'app_name/products.html')  
   ```

2. В файле urls.py создаем новый маршрут `shop/products/` и соединяем его с функцией `example_view`.
   ```python
      # urls.py
      from django.urls import path
      from app_name.views import example_view  # импортируем из views.py функцию
   
      urlpatterns = [
          ...
          path('shop/products/', example_view),  # связываем адрес и функцию
      ]
   ```
### Динамические маршруты
В данном примере мы будем передавать через ссылку параметры<br> 
`http://127.0.0.1:8000/shop/products/1/MnogoTexta`.
1. #### Добавляем в маршрут динамическую часть `<int:id>` и `<str:text>`.<br>
   Здесь `int` и `str` это обозначение ожидаемого типа параметра.<br>
   `int` для чисел `str` для строк.
   ```python
      # urls.py
      urlpatterns = [
          ...
          path('shop/products/<int:id>/<str:text>/', example_view),
      ]
   ```
2. #### В файле views.py создаем функцию `example_view` которая будет возвращать отрендеренный шаблон `products.html`.
   ```python
      # views.py
      def example_view(request, id, text):  
          # Дополнительные параметры функции example_view после request должны 
          # называться так же как и параметры в связанной ссылке,.
          return render(request, 'app_name/products.html')
   ```
    > Теперь когда мы перейдем по ссылке `http://127.0.0.1:8000/shop/products/1/MnogoTexta`, 
   > получим в параметр `id` цифру `1`, а в параметр `text` строчку `MnogoTexta`.

### URL naming & redirects (Именование маршрутов и перенаправление)
   * **URL-имя** - это уникальное имя, присваиваемое URL-паттерну в Django. <br>
     Оно позволяет нам ссылаться на этот URL в коде и шаблонах, <br>
     используя понятные и наглядные имена вместо абсолютных URL-адресов.

   * **Redirect** (перенаправление) - позволяет перенаправить пользователя на определенную ссылку.<br>
   * #### Примеры
     * Укажем имена для двух маршрутов
         ```python
         # urls.py
         urlpatterns = [
             ...
             # Примеры со статическим и динамическими маршрутами
             path('shop/products/', example_view1, name='example1'),  
             path('shop/products/<int:id>/<str:text>/', example_view2, name='example2'),  
         ]
         ```
     * Перенаправление:
          ```python
          # views.py
          # Эти функции эквивалентны
          def some_view(request):
              return redirect('example1')
          
          
          def some_view(request):
              return redirect('shop/products/')
          ```
          Так мы перенаправим пользователя на адрес http://127.0.0.1:8000/shop/products/ <br><br>
     
     * **Шаблонизация маршрутов описана в [этом разделе](https://github.com/xlartas/it-compot-backend-methods/blob/main/django-base.md#Формирование-внутренних-маршрутов-в-шаблоне)**
     <br><br>

## <span id="Обмен-данными-клиент---сервер"><u>Обмен данными</u> `клиент <--> сервер`</span> 
>Слово **'клиент'**, можно заменить на **'браузер пользователя'** (однако это не совсем правда).

### Получение данных с клиента.
   Любой запрос на сервер, будь то загрузка страницы или отправка данных <br>
   на сервер сопровождается определенным типом запроса.<br>
   Например, при обычном переходе по ссылке отправляется GET запрос, <br>
   а в ответ мы получаем html страницу и связанные css js и т.п.<br><br>
   
   Для отправки данных мы используем форму (тег form).<br>
   Отправлять данные мы можем как через `GET`, так и через `POST`.<br>
   Атрибут `method` у тега `form` определяет какой тип запроса будет использоваться при отправке.<br>
   Атрибут `action` у тега `form` определяет на какой адрес будет отправляться запрос.<br>
   
   method: Если собираемся менять что-то в базе данных то POST, иначе GET.
   action: Cсылка, куда будут отправляться данные. Если не указывать, то на ту же страницу.

   * Создадим форму для отправки данных.
       ```html
       <!-- example.html -->
       <form method="get" action="">
           <input type="text" name="name1" placeholder="Подсказка при пустом поле.">
           <input type="text" name="name2" value="Уже установленное значение.">
           <input type="hidden" name="name3" value="Невидимое поле">
           <button type="submit">Кнопка подтверждения отправки</button>
       </form>
       ```    
       ![](imgs/form.png)

* Так как action не указан будем получать данные и рендерить страницу в одном view.<br>
  _Не забудьте связать эту функцию с маршрутом_.
  >Если не знаете как, смотрите информацию про маршруты на этой же странице.
    ```python
    # views.py
    
    def example_view(request):
        # Так мы можем посмотреть данные отправленные с GET запросом, если их нет, то результатом в консоли будет пустой список.
        print(request.GET) 
        # Проверяем Если запрос типа POST
        if request.method == 'POST':
            # Так можем распечатать содержимое POST запроса.
            print(request.POST)
            # Получаем переменные. 
            # name1, name2, name3, это значения атрибутов name в input тегах.
            print(request.POST['name1'])
            print(request.POST['name2'])
            print(request.POST['name3'])
        return render(request, 'app_name/example.html')
    ```
### Отправка данных клиенту.
    
* Изменим строку с `render` в функции выше.
   ```python
   ...
   return render(request, 'app_name/example.html', {
       'key1': 'уууу аааа',
       'ABCkey2': 'value2',
   })
   ```

* Теперь мы можем в `example.html` использовать переданную информацию по ключу.
    ```html
    <!-- example.html-->
    ...
    <span>{{ key1 }}<!-- Тут появится 'уууу аааа'--></span> 
    <p>{{ ABCkey2 }}<!-- Тут появится 'value2'--></p> 
    ...
    ```
### Виды полей ввода `input`
*   ```html
    Text Input: Однострочный текст.
    <input type="text" name="any_name">
    ```
    
*   ```html
    Password Input: Ввода пароля, текст отображается как маскированный (******).
    <input type="password" name="any_name">
    ```
*   1. [ ] Галôчка :)))
    ```html
    Checkbox Input: Галочка ().
    <input type="checkbox" name="any_name" value="yes">
    ```
    
*   ```html
    Radio Input: Этот тип позволяет пользователю выбирать одну опцию из группы. 
    (Несколько галочек, из которых можно выбрать только 1. name="у всех одинаковый")
    <input type="radio" name="equal_any_name" value="male"> Мужской
    <input type="radio" name="equal_any_name" value="female"> Женский
    ```
    
*   ```html
    File Input: Выбирать и загружать файлы с компьютера.
    <input type="file" name="any_name">
    ```
    
*   ```html
    Hidden Input: Создает скрытое поле, которое может использоваться 
    для передачи данных на сервер БЕЗ отображения на веб-странице.
    <input type="hidden" name="any_name" value="my_secret_value">
    ```
    
*   ```html
    Number Input: Этот тип позволяет пользователю вводить числа.
    <input type="number" name="any_name" min="1" max="100" step="1">
    ```
    
*   ```html
    Date Input: Этот тип используется для выбора даты.
    <input type="date" name="any_name">
    ```
    
*   ```html
    Email Input: Этот тип используется для ввода email-адреса.
    <input type="email" name="any_name">
    ```
    
*   ```html
    URL Input: Этот тип используется для ввода URL.
    <input type="url" name="website">
    ```
    
*   ```html
    Color Input: Этот тип позволяет пользователю выбирать цвет.
    <input type="color" name="favorite_color">
    ```
    
*   ```html
    Range Input: Этот тип создает ползунок для выбора значения из диапазона чисел.
    <input type="range" name="volume" min="0" max="100">
    ```
    
*   ```html
    Search Input: Этот тип используется для создания поля поиска.
    <input type="search" name="search_query">
    ```







## <u>Models</u>
### Создание простейшей модели для товара
1. Создаем класс модели Product в models.py
   ```python
   from django.db import models
   
   class Product(models.Model):
       name = models.CharField(max_length=30)
       price = models.IntegerField()
       ...
   ```
2. Создаем файлы миграций через терминал

   `python manage.py makemigrations`

3. И выполняем по ним миграцию

   `python manage.py migrate`

> Как результат получаем в базе данных созданную таблицу 
> shop_product(shop - название приложения product - название класса модели).
> При каждом последующем изменении класса модели нужно повторять создание и выполнение файлов мигарций,
> чтобы таблица в настоящей базе данных соответствовала классу модели.
 
> Так выглядит в базе данных наша таблица Product c 3-мя товарами. 
> Обрати внимание, что было добавлено новое поле id, 
> которое должно быть у каждой таблицы для обеспечения уникальности каждого объекта в таблице.

| **id** |    **name**    | **price** |
|:------:|:--------------:|:---------:|
|    1   |     Алмазик    |    999    |
|    2   | Золотой слиток |    500    |
|    3   |  Скин на Pudge |    1000   |

### Model Fields
* `CharField(max_length=30)` Поле для коротких текстовых строк, где максимальная длина строки составляет 30 символов.


* `TextField()` Поле для длинных текстовых строк без жесткого ограничения на длину.


* `IntegerField()` Поле для целых чисел.


* `PositiveIntegerField()` Поле для целых чисел.


* `FloatField()` Поле для чисел с плавающей запятой.


* `BooleanField()` Поле для логических значений True или False.


* `DateTimeField()` Поле для хранения комбинированных дат и времени.


* `EmailField()` Поле для хранения адресов электронной почты с валидацией формата.


* `ImageField(upload_to='images/')` Поле для хранения изображений с указанием пути для сохранения.


* `ForeignKey(to=Category, on_delete=models.CASCADE)` Поле для создания связи "один ко многим" с другой моделью Category. 
   on_delete=models.CASCADE означает, что при удалении связанной модели Category, все связанные объекты также будут удалены.


* `ManyToManyField(Product, on_delete=models.CASCADE)` Поле для создания связи "многие ко многим" с другой моделью Product.

### ORM
_Примеры взаимодействия с моделью Product._<br>
Не забываем импортировать модель перед использованием.

* Получить все объекты
   ```python
   Product.objects.all()
   ```
* Получить один объект по определенным параметрам
   ```python
   Product.objects.get(id=2, is_available=True)
   ```
* Отфильтровать объекты
   ```python
   Product.objects.filter(is_available=True)
   ```
* Создать новый объект
   ```python
   Product.objects.create(
      name='Имя продукта', 
      price=999.99, 
      is_available=True
   )
   ```
* Изменить поля объекта и сохранить
   ```python
   some_product = Product.objects.get(id=1)  #  получаем объект
   some_product.name = 'Новое имя'  # изменяем поле name
   some_product.price = 0.12  # изменяем поле price
   some_product.save()  # сохраняем изменения
   ```

### Media Files
`Media files` в контексте `django` - это файлы **_любого_** типа, загруженные извне(админка, пользовательский интерфейс)
```python
# models.py
class Product(models.Model):
    name = models.CharField(max_length=100)
    # ImageField поле в котором будет храниться ссылка 
    # на загруженный медиа файл (в данном случае картинка)
    # upload_to - путь к папке загрузки данных файлов.
    # Полный путь будет показан ниже
    image = models.ImageField(upload_to='products/')
```
```python
# project_name/settings.py
...
# Тут указываем адрес для доступа к файлом 
# по ссылке (MEDIA_URL) и локальный путь для хранения на диске(MEDIA_ROOT).

# MEDIA_URL - адрес по которому будут доступны media файлы.
# Вы можете проверить доступ после добавления первого объекта с медиа файлом перейдя по ссылке:
# http://127.0.0.1:8000/MEDIA_URL/images/IMAGE_NAME/
MEDIA_URL = '/media/'
# MEDIA_ROOT - локальный адрес хранения media.
# Корневая папка проекта + 'media'
# ./project_folder/media/
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
...
```
```python
# project_name/urls.py
urlpatterns = [
    ...,
    ...,
    ...,
]
#  На сервере media обслуживает серверная служба, 
#  а не django, поэтому только при локальной разработке в debug режиме  
#  мы включаем обслуживание на стороне django, добавляя адреса для media.
if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```


## <u>Шаблонизация</u>
Благодаря шаблонизатору django мы можем использовать <br> 
переменные внутри шаблона, а также циклы, условия, включения и расширения.

### Передача переменных внутрь шаблона
Когда отображаем страничку `example.html` мы используем функцию `render`, <br>
которая как раз прогоняет страничку через `шаблонизатор`. <br>
Первым параметром функции мы передаем параметр `request`,<br>
вторым путь до html файла странички в виде строки.
```python
# views.py
def example_view(request):  
    return render(request, 'app_name/example.html')
```
Чтобы передать переменную в шаблон, нужно третьим параметром в функции `render` <br> 
передать нужную переменную в словаре под каким-либо именем. <br>
Например, ниже мы передаем переменную `param` под именем `param_name`
```python
# views.py
def example_view(request):  
    param = 999
    return render(request, 'app_name/example.html', {'param_name': param})
```
Тогда внутри `example.html` мы можем отобразить эту переменную используя `{{ param_name }}`.
```html
<!-- example.html -->
<!doctype html>
<html>
<head>
    <title>Document</title>
</head>
<body>
    <!-- После функции render тут появиться 999(значение переменной param) -->
    {{ param_name }}
</body>
</html>
```

### Использование статических файлов в шаблоне
```html
<!-- example.html -->
<!-- Загружаем статические файлы -->
{% load static %}  
...
<head>
    ...
    <link rel="stylesheet" href="{% static 'app_name/css/style.css' %}">
</head>
<body>
    <img src="{% static 'app_name/img/image.png' %}" alt="">
</body>
``` 
### Формирование внутренних маршрутов в шаблоне
```python
# urls.py
urlpatterns = [
    ...
    # Примеры со статическим и динамическими маршрутами
    path('shop/products/', example_view1, name='example1'),  
    path('shop/products/<int:id>/<str:text>/', example_view2, name='example2'),  
]
```
```html
<a href="{% url 'example1' %}">Кликни на меня</a>
<!-- После рендера такого шаблона мы получим -->
<a href="/shop/products/">Кликни на меня</a>
<!-- Такие, неполные ссылки, ведут на адрес состоящий из домена и этой неполной ссылки.-->
<!-- Полный вид итоговой ссылки при переходе будет http://127.0.0.1:8000/shop/products/ -->

<!-- Имена можно использовать где угодно внутри шаблона. Вот еще пример.-->
<form action="{% url 'example1' %}"></form>

<!-- Для шаблонизации динамических ссылок нужно передавать дополнительно необходимые параметры по порядку.-->
<a href="{% url 'example2' id=123 text='My text' %}">Кликни на меня</a>
<!-- Часто требуется передавать в маршрут контекстные данные, например -->  
<a href="{% url 'example2' id=product.id text=product.title %}">Кликни на меня</a>
```


### Использование циклов и условий в шаблоне
Если мы хотим отобразить много объектов или отобразить объект <br>
только при каких-то условиях, нужно использовать циклы и условия.

Передадим в шаблон все объекты модели Product.
```python
# views.py
def example_view(request):  
    # Получаем все продукты в переменную all_products
    all_products = Product.objects.all() 
    # Рендерим наш шаблон передавая переменную all_products под именем all_products.
    return render(request, 'app_name/example.html', {'all_products': all_products})
```
Используем цикл и условие, чтобы отобразить все товары, которые имеют цену меньше 500.<br>
Запускаем цикл для перебора товаров.<br>
Каждую итерацию цикла в переменную product будет попадать<br>
новый товар из переменной all_products пока товары в all_products не закончатся.<br>
Для каждого товара мы проверяем: цена меньше 500? Если да, то отображаем div с данными товара.
```html
<!-- example.html -->
...
<body>
    {% for product in all_products %} 
        {% if product.price < 500 %}
            <div class="product">
                <span>{{ product.name }}</span>
                <span>{{ product.price }}</span>
            </div>
        {% endif %}
    {% endfor %}
</body>
```
Естественно мы можем передавать несколько переменных в шаблон, а не только одну.
```python
# views.py
def example_view(request):  
    return render(request, 'app_name/example.html', {
        'param1': 11231,
        'param2': 'Еще параметр',
        'param3': 'и еще',
        'param4': 999,
        'productId1': Product.objects.get(id=1),
    })
```

### Include в шаблонах
`Include` = `включение, вставка`<br><br>
Давайте представим, что у вас есть веб-сайт с несколькими страницами. <br>
На каждой странице есть одинаковое шапка и подвал. <br>
Вместо того чтобы копировать и вставлять одинаковый код шапки <br>
и подвала на каждую страницу, вы можете вынести этот код в <br>
отдельный файл (`header.html` и `footer.html`). <br>
Затем, когда вы создаете основные страницы сайта, вы просто <br>
"включаете" (`include`) эти маленькие файлы шаблонов в нужные места.<br>
Таким образом, если вы захотите что-то изменить в шапке, вам нужно <br>
будет сделать это только в одном файле `header.html`, а изменения <br>
автоматически отразятся на всех страницах, где это меню "включено".<br>
В коде Django это выглядит примерно так:

> Как всегда, шаблоны мы храним в папке `templates`. <br>
> Советую создать внутри отдельную папку для шаблонов, которые мы будем куда-либо вставлять.
```html
<!-- Ваш основной шаблон страницы -->
<div class="header">
    {% include 'app_name/includes/header.html' %}
</div>
<div class="content">
    <!-- Контент вашей страницы -->
</div>
<div class="footer">
    {% include 'app_name/includes/footer.html' %}
</div>
```

### Переменные в Include
В Django, тег `{% include %}` позволяет вставить один шаблон в другой. <br>
Чтобы передать переменные в этот включенный шаблон, вы можете использовать `with`.
```html
<!-- app_name/main.html -->
...
{% include "card.html" with name="John" age=30 %}
...
```
```html
<!-- app_name/includes/card.html -->

<!-- Файл для вставки(include) -->
<div class="card">
    <p>Name: {{ name }}</p>
    <p>Age: {{ age }}</p>
</div>
```
Здесь `card.html` использует переменные `name` и `age`, переданные из `main.html`.


### Расширение/Наследование шаблонов

В Django, наследование шаблонов позволяет расширять шаблоны страниц с общим содержимым и структурой.
Например, каждая страница начинается с 
```html
<!doctype html>
<html lang="en">
<head>
    <meta ...........
```
И вот как раз чтобы постоянно не писать одно и тоже, экономить место и улучшать <br>
качество кода делают базовый шаблон, в котором подобные, часто повторяющиеся вещи, <br>
прописываются 1 раз, а после этот шаблон расширяется контентом уникальным для разных страниц.<br>

```html
<!-- templates/app_name/base.html -->

<!-- Шаблон который будем заполнять контентом / расширять -->
<html>
<head>
    <title>{% block title %}Default Title{% endblock %}</title>
</head>
<body>
    {% block content %}{% endblock %}
</body>
</html>
```
```html
<!-- templates/app_name/page.html -->

<!-- За основу берем base.html и вместо блока content -->
<!-- подставляем свой параграфи или любой другой код, -->
<!-- а вместо блока title надпись 'Page Title' -->
{% extends "base.html" %}

{% block title %}Page Title{% endblock %}

{% block content %}
    <p>This is the content of the page.</p>
{% endblock %}
```

В этом примере `page.html` наследует структуру из `base.html` и переопределяет блоки `title` и `content`.
`{% extends "base.html" %}`: указывает, что **текущий** шаблон **наследуется** от `base.html`.<br>
`{% block blockname %}` ... `{% endblock %}`: в базовом шаблоне определяет `блоки`, которые <br>
можно **переопределить** в **дочерних** шаблонах.<br>
Важно заметить, что это самый простой и базовый пример использования наследования/расширения шаблонов.<br>
Цепочка расширения может состоять не только из двух шаблонов. <br>
В базовом приложении `admin` вложенность наследования около 4.







## <u>Структура проекта</u>
Структура директорий в проекте django.
Обратите внимание, что это пример структуры, а не точный её вид.<br><br>
📦PROJECT_NAME `Корневая папка проекта`<br>
 ┣---📜manage.py<br>
 ┣---📂project_name `Конфигурация проекта`<br>
 ┃    ┣---📜__init__.py<br>
 ┃    ┣---📜settings.py `Настройки проекта`<br>
 ┃    ┣---📜urls.py `Корневые маршруты`<br>
 ┃    ┣---📜wsgi.py<br>
 ┃    ┗---📜asgi.py<br>
 ┣--- 📂core `Приложение core`<br>
 ┃    ┣---📂migrations `Файлы миграций`<br>
 ┃    ┣---📂templates `Шаблоны`<br>
 ┃    ┃  ┣---📂core `Шаблоны core приложения`<br>
 ┃    ┃  ┃  ┗---📜example.html<br>
 ┃    ┣---📂static `Статические файлы`<br>
 ┃    ┃  ┣---📂core `Статические файлы core приложения`<br>
 ┃    ┃  ┃  ┣---📂css<br>
 ┃    ┃  ┃  ┣---📂img<br>
 ┃    ┃  ┃  ┃  ┗---📜image.png<br>
 ┃    ┃  ┃  ┗---📂js<br>
 ┃    ┣---📜__init__.py<br>
 ┃    ┣---📜apps.py<br>
 ┃    ┣---📜admin.py `Управление админкой`<br>
 ┃    ┣---📜models.py `Модели приложения core`<br>
 ┃    ┣---📜urls.py `Маршруты приложения core`<br>
 ┗    ┗---📜views.py `Функции приложения core для отображения страниц`<br>


-----------------------------
**© it-compot**<br>
_by Nikita Artasov_










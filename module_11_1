import requests
import os
from PIL import Image, ImageOps
from bs4 import BeautifulSoup
from selenium import webdriver
from selenium.webdriver.chrome.options import Options

'''Данный код парсит заданную ссылку на сайт с изображениями,
сохраняет первые 20 изображений(*) в формате .JPG,
затем изменяет размер изображений и добавляет в левый верхний угол Watermark
полученный результат сохраняет в папку Test.
* - в код добавлено условие с первыми 6-ю изображениями из-за особенностей сайта'''

# Настройки для Selenium
options = Options()
options.headless = True  # запуск без интерфейса
options.add_argument("--disable-gpu")  # отключить GPU

# Создать драйвер
driver = webdriver.Chrome(options=options)

# Начало работы request
# Открыть страницу
driver.get("https://ru.freepik.com/search")

# Получить HTML страницы
html = driver.page_source

# Закрыть драйвер
driver.quit()

# Парсить HTML страницы
soup = BeautifulSoup(html, 'html.parser')

# Найти все картинки на странице
images = soup.find_all('img')

print("Завершен поиск изображений")

# Папка для сохранения картинок
image_folder = "./images"

# Создать папку, если она не существует

if not os.path.exists(image_folder):
    os.makedirs(image_folder)

print("Создана папка для оригиналов")

# Скачать картинки
for i, image in enumerate(images[:26]):
    image_url = image.get('src')
    if image_url and image_url.startswith("/"):
        image_url = "https://ru.freepik.com" + image_url
    elif image_url and not image_url.startswith("http"):
        continue
    if image_url:
        if i < 6:
            filename = f"Drop_{i+1}.jpg"
        else:
            filename = f"image_{(i-6)+1}.jpg"
        response = requests.get(image_url)
        with open(f"{image_folder}/{filename}", "wb") as file:
            file.write(response.content)

print("Сохранены оригиналы картинок в формате .JPG")

# Удалить картинки с именем "Drop_1.jpg,..., Drop_6.jpg"
for i in range(1, 7):
    filename = f"Drop_{i}.jpg"
    file_path = f"{image_folder}/{filename}"
    if os.path.exists(file_path):
        os.remove(file_path)

print("Удалены лишние элементы")


test_folder = "test"
if not os.path.exists(test_folder):
    os.makedirs(test_folder)

size = (480, 640)  # размер изображения
watermark_image = Image.open("watermark.png")
watermark_image = watermark_image.resize((50, 100))  # уменьшить размер watermark
for file in os.listdir(image_folder):
    with Image.open(os.path.join(image_folder, file)) as im:
        im = ImageOps.cover(im, size)
        im.paste(watermark_image, (5, 5), mask=watermark_image)
        im.save(os.path.join(test_folder, file.replace(".jpg", ".png")))

print("Картинки скачаны и сохранены в папку test в формате PNG")
print("Тест окончен!")

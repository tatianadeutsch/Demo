#**Комментарии**


import requests
from pprint import pprint


`files_url = 'https://cloud-api.yandex.net/v1/disk/resources/files'`
`upload_url = 'https://cloud-api.yandex.net/v1/disk/resources/upload'`

`def get_token():`
    `with open('token.txt', 'r') as file:`
        `return file.readline()`


`class YaUploader:`
    `files_url = 'https://cloud-api.yandex.net/v1/disk/resources/files'`
    `upload_url = 'https://cloud-api.yandex.net/v1/disk/resources/upload'`

    def __init__(self, token: str):
        self.token = token

    @property
    def header(self):
        return self.get_headers()

    def get_headers(self):
        return {
            'Content-Type': 'application/json',
            'Authorization': 'OAuth {}'.format(self.token)
        }

    def get_upload_link(self, file_path):
        params = {'path': file_path, 'overwrite': 'true'}
        response = requests.get(self.upload_url, params=params, headers=self.header)
        return response.json()

    def upload(self, file_path):
        """Метод загружает файлы по списку file_list на яндекс диск"""
        href = self.get_upload_link(file_path).get('href')
        if not href:
            print('Не удалось получить ссылку для загрузки файла')
            return
#
        with open(file_path, 'rb') as file:
            response = requests.put(href, data=file)
            if response.status_code == 201:
                print('Файл загружен')
                return True
            else:
                print(f'Ошибка {response.status_code}')
                return False

if __name__ == '__main__':
    # Получить путь к загружаемому файлу и токен от пользователя
    path_to_file = input('Путь к файлу: ')
    token = get_token()
    uploader = YaUploader(token)
    result = uploader.upload(path_to_file)

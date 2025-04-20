[[Модель CarOwner]]
[Модель Car](Car.md (автомобили))
[Модель Discount](Discount.md)

### Импорты модуля
```
import os
import sys
import base64
from django.db import models
from decimal import Decimal
import requests
from django.utils import timezone
```
### Модель [[CarOwner]]
```python
class CarOwner(models.Model):
    owner_id = models.AutoField(primary_key=True)
    name = models.CharField(max_length=255, default='Неизвестно')
    email = models.EmailField(max_length=100, null=True, blank=True, default='noemail@example.com')
    phone_number = models.CharField(max_length=20, default='375')
    discount_card = models.CharField(max_length=6, default='0')
    аdditional_phone_number = models.CharField(max_length=20, blank=True, null=True)

    def __str__(self):
        return f"{self.name} - {self.phone_number}"
```


### Модель [Discount (скидки)](Discount.md)

``` python
class Discount(models.Model):
    owner = models.ForeignKey(CarOwner, on_delete=models.CASCADE)
    car = models.ForeignKey(Car, on_delete=models.CASCADE)
    total_spent = models.IntegerField(default=0)
    discount_rate = models.DecimalField(max_digits=5, decimal_places=2, default=Decimal('0.15'))

  
    def calculate_discount(self):
        if self.discount_rate is not None and self.total_spent is not None:
            return f"{round(self.discount_rate * self.total_spent, 2)}%"
        return "Данные не указаны"

  

    def send_discount_sms(self):

        current_discount = self.calculate_discount()

        message = f"Номер карты {self.car.discount_card}. Ваша новая скидка составляет {current_discount}."

        phone_numbers = [self.owner.phone_number]

  

        mts_sms_login = 'Fomina_19_PoWw'

        mts_sms_password = 'B5kgo0'

        mts_sms_client_id = '1921'

        mts_sms_url = f'https://api.communicator.mts.by/{mts_sms_client_id}/json2/simple'

  

        headers = {

            'Content-Type': 'application/json',

            'Authorization': 'Basic ' + base64.b64encode(f"{mts_sms_login}:{mts_sms_password}".encode()).decode()

        }

  

        start_time = timezone.now().strftime("%Y-%m-%d %H:%M:%S%z")[:-2] + ':' + timezone.now().strftime("%Y-%m-%d %H:%M:%S%z")[-2:]

  

        for phone_number in phone_numbers:

            data = {

                'phone_number': phone_number,

                'extra_id': "AD-6640-7006",

                'callback_url': "https://api.communicator.mts.by/sms/callback",

                'start_time': start_time,

                'tag': "Предложение продажи",

                'channels': ["sms"],

                'channel_options': {

                    'sms': {

                        'text': message,

                        'ttl': 300

                    }

                }

            }

  

            try:

                response = requests.post(mts_sms_url, headers=headers, json=data, timeout=60)

                print(f'HTTP статус для {phone_number}:', response.status_code)

                print(f'HTTP ответ для {phone_number}:', response.text)

                result = response.json()

                print(f'Ответ от МТС API для {phone_number}:', result)

                status = result.get('status', None)

  

                if status in ('SENT', 'QUEUED'):

                    print(f'SMS принято для {phone_number}, статус: {status}')

                elif status is None:

                    print(f'Ответ API не содержит статус для {phone_number}, полный ответ: {result}')

                else:

                    print(f'SMS отклонено для {phone_number}, статус: {status}')

            except Exception as e:

                print(f'Не удалось отправить SMS на {phone_number}: плохой или отсутствующий ответ от МТС API.')

                print(e)

  

    def __str__(self):

        return f"{self.car.discount_card} - {self.car.registration_number_car}"
```

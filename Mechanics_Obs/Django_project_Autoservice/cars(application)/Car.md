``` python
class Car(models.Model):

    owner = models.ForeignKey(CarOwner, on_delete=models.CASCADE, default=None)

    brand = models.CharField(max_length=50, default='Неизвестно')

    model = models.CharField(max_length=50, default='Неизвестно')

    year_of_car_production = models.IntegerField(default=2000)

    registration_number_car = models.CharField(max_length=20, default='Не указано')

    discount_card = models.CharField(max_length=6, default='0')

  

    def __str__(self):

        return f"{self.brand} {self.model}"
```
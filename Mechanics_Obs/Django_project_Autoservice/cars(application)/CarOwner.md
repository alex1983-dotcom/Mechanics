
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

[Remove Choice From Choices In Froms](#remove-choice-from-choices-in-froms)



___
## Remove Choice From Choices In Froms
---
> `models.py`
```python
from django.db import models

class User(models.Model):
    TYPE_CHOICES = (
        (0, 'Admin'),
        (1, 'Type1'),
        (2, 'Type2')
    )
	name = models.CharField(max_length=200)
	password = models.CharField(max_length=200)
	user_type = models.IntegerField(default=0, choices=TYPE_CHOICES)
```

> ## `forms.py`
```python
from django import forms

class UserForm(forms.ModelForm):
    class Meta:
		model = User
        fields = ['name', 'password', 'user_type']

    def __init__(self, *args, **kwargs):
		super().__init__(*args, **kwargs)

        # if you don't need (0, 'admin') on choice in form
		self.fields['user_type'].choices = [
			choice for choice in self.fields['user_type'].choices
			if choice[0] != 0
		]
```
___
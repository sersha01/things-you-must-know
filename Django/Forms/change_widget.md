# Change Widget of time and date

```python
widgets = {
	'opening_time': forms.TimeInput(attrs = {'type': 'time'}),
	'closing_time': forms.TimeInput(attrs = {'type': 'time'}),
}
```

```python
widgets = {
	'date_of_birth': forms.DateInput(attrs = {'type': 'date'}),
}
```
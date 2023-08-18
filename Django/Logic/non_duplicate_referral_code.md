# Create a referral code with x digits without any duplicates

```python
def generate_random_alphanumeric(length):
    characters = string.ascii_uppercase + string.digits
    random_string = ''.join(random.choice(characters) for _ in range(length))
    return random_string

   ...
   ...

...
user = CustomUser.objects.get(id=id)
if not user.referral_code:
    first_4_letters_name = user.name[:4].upper() if user.name else ""
    random_code_length = 10 - len(first_4_letters_name)
    while True:
        random_10_digit_alphanumeric = generate_random_alphanumeric(random_code_length)
        referral_code = first_4_letters_name + random_10_digit_alphanumeric
        if not CustomUser.objects.filter(referral_code=referral_code).exists():
            user.referral_code = referral_code
            user.save()
            break 
```